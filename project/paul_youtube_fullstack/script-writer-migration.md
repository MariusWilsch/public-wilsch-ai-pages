---
publish: true
---

# Script-Writer Migration Plan
[[client-paul]]

**Heuristic:** Native Supabase = Supabase, Workaround = FastAPI

## Summary
- **Supabase (native):** 11 endpoints
- **FastAPI (debuggable):** 6 endpoints
- **Code deletion:** ~45%

## Endpoint Categorization

### Script Job Endpoints (script_job_endpoints.py)

| Endpoint | Target | Why | Native/Workaround |
|----------|--------|-----|-------------------|
| `GET /api/scripts` | **FastAPI** | Admin sees all, scripter sees only assigned - multi-table join with role logic | Workaround |
| `GET /api/scripts/<id>` | **FastAPI** | Role-based access requires assignment check | Workaround |
| `POST /api/scripts` | **Supabase** | Simple insert with RLS | Native |
| `PUT /api/scripts/<id>` | **Supabase** | Simple update with RLS | Native |
| `DELETE /api/scripts/<id>` | **Supabase** | Single table delete with cascade | Native |
| `GET /api/scripts/<id>/assignments` | **Supabase** | PostgREST embedded resource | Native |
| `POST /api/scripts/<id>/assign` | **FastAPI** | Multi-table: validate roles, check duplicates, bulk create | Workaround |
| `DELETE /api/scripts/<id>/unassign/<user_id>` | **Supabase** | Simple delete with composite key | Native |
| `GET /api/my-assignments` | **Supabase** | View with RLS | Native |
| `POST /api/my-assignments/<id>/start` | **Supabase** | Simple update with RLS | Native |
| `POST /api/my-assignments/<id>/complete` | **FastAPI** | File upload + DB coordination | Workaround |
| `PUT /api/my-assignments/<id>/notes` | **Supabase** | Simple update with RLS | Native |
| `POST /api/my-assignments/<id>/upload` | **FastAPI** | File validation + old file cleanup | Workaround |
| `GET /api/my-assignments/<id>/download` | **Supabase Storage** | Signed URL with RLS | Native |
| `GET /api/scripts/available-users` | **Supabase** | Simple filter on roles | Native |

### Script Writer Endpoints (script_writer_endpoints.py)

| Endpoint | Target | Why | Native/Workaround |
|----------|--------|-----|-------------------|
| `POST /api/script-writer/upload` | **FastAPI** | PDF/DOCX text extraction (PyPDF2, python-docx) | Workaround |
| `GET /api/script-writer/scripts` | **Supabase** | Pagination + search via PostgREST | Native |
| `GET /api/script-writer/scripts/<id>` | **Supabase** | Simple lookup with RLS | Native |
| `PUT /api/script-writer/scripts/<id>` | **Supabase** | Simple update with RLS | Native |
| `DELETE /api/script-writer/scripts/<id>` | **FastAPI** | File cleanup + DB delete coordination | Workaround |
| `GET /api/script-writer/scripts/<id>/download` | **Supabase Storage** | Signed URL download | Native |
| `GET /api/script-writer/scripts/<id>/text` | **Supabase** | Simple field retrieval | Native |

## Supabase Configuration Needed

### Storage Buckets
```sql
CREATE BUCKET assignment_uploads (PUBLIC = false);
CREATE BUCKET script_uploads (PUBLIC = false);
```

### RLS Policies
```sql
-- Scripts: admin full access, scripter sees assigned only
CREATE POLICY "scripter_view_assigned"
  ON scripts FOR SELECT
  USING (
    auth.uid() IN (
      SELECT user_id FROM script_assignments WHERE script_id = scripts.id
    ) OR 'admin' = ANY(auth.jwt()->>'user_roles')
  );

-- Assignments: user sees own, admin sees all
CREATE POLICY "own_assignments"
  ON script_assignments FOR SELECT
  USING (user_id = auth.uid() OR 'admin' = ANY(auth.jwt()->>'user_roles'));

CREATE POLICY "update_own_assignments"
  ON script_assignments FOR UPDATE
  USING (user_id = auth.uid());
```

### Views
```sql
CREATE VIEW assignment_list_view AS
SELECT sa.*, s.name AS script_name, s.description
FROM script_assignments sa
JOIN scripts s ON s.id = sa.script_id;

ALTER VIEW assignment_list_view SET (security_invoker = true);
```

## FastAPI Services Needed

### 1. ScriptAssignmentService
**Why FastAPI:** Multi-table validation (role checks, duplicate detection) would require `security_definer` functions. Hard to debug in SQL.

**Operations:**
- List scripts with role-based filtering
- Assign script to users (validate roles, prevent duplicates)
- Get script with access check

### 2. FileProcessingService
**Why FastAPI:** PDF/DOCX text extraction is compute-intensive. PyPDF2, python-docx are Python-native. Edge Functions could work but debugging is painful.

**Operations:**
- Upload script with text extraction
- Delete script + cleanup file
- Upload/replace assignment file
- Complete assignment (file + status update)

## Migration Order

### Phase 1: Read-only Supabase (low risk)
- Set up Storage buckets
- Create views for complex queries
- Migrate list/get endpoints to PostgREST
- Frontend switches to Supabase client for reads

### Phase 2: FastAPI Services (medium risk)
- Deploy FileProcessingService (text extraction)
- Deploy ScriptAssignmentService (orchestration)
- Frontend calls FastAPI for uploads/assignments

### Phase 3: Write operations (medium risk)
- Enable RLS policies for simple CRUD
- Migrate create/update/delete to PostgREST
- Frontend switches to Supabase for simple writes

### Phase 4: Cleanup (low risk)
- Remove Flask blueprint files
- Archive old code

## Risk Assessment

| Risk Level | Items |
|------------|-------|
| **Low** | Read operations, file downloads, simple updates |
| **Medium** | File upload coordination, RLS policy correctness |
| **High** | PDF/DOCX parsing reliability, storage permissions |

## Related

- [Endpoint Migration Template](endpoint-migration-template.md) - The methodology
- [Strangler Pattern Sequencing](adr-strangler-pattern-sequencing.md) - Overall migration strategy
