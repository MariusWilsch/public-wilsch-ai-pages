---
publish: true
---

# Script-Writer Feature
[[client-paul]]

## Why

Decouples script writing workflow from video queue processing. Allows admins to create and assign script writing jobs to team members, who can work independently and submit completed work. Separates content creation from video production pipeline.

## What

### Admin Workflow
1. **Create Script Job** - Name + description for a writing task
2. **Assign to Writers** - Select one or more team members
3. **Track Progress** - Monitor pending/in-progress/completed status
4. **Manage Assignments** - Remove users, delete jobs

### Scripter Workflow
1. **View Assignments** - See all assigned work (pending + in-progress only)
2. **Start Working** - Mark assignment as in-progress
3. **Complete Work** - Submit with optional notes
4. **Auto-hide** - Completed work disappears from view

### Roles
- **Admin/Scripter role**: Can create jobs, assign, track
- **Any authenticated user**: Can view and complete own assignments

## How

### Key Behaviors
- **Multi-assignment**: Same script can be assigned to multiple users
- **Independent status**: Each user has own assignment status
- **Cascade delete**: Deleting script removes all assignments
- **Status tracking**: pending → in_progress → completed
- **Unique constraint**: Same script can't be assigned twice to same user

### Data Model
- `scripts` table: id, name, description, created_by
- `script_assignments` table: script_id, user_id, status, notes, timestamps

### Frontend Components
- `ScriptManagement.jsx` - Admin view (create, assign, track)
- `MyAssignments.jsx` - Scripter view (view, start, complete)

## Done

### Success Criteria
- Admin can create script job and assign to users
- Writers see only their assignments
- Status progresses: pending → in_progress → completed
- Completed assignments hidden from scripter view
- Progress visible to admin with counts

### Implementation Status
- **Frontend**: Complete (`paul_queue/script-writer-frontend/`)
- **Backend**: Spec exists (`paul_queue/SCRIPT_JOBS_FEATURE.md`), implementation may be on nuca-systems only
- **Database**: Schema ready (migrations exist)

### Related
- Issue #226: Absorb Script-Writer into Main frontend
- Backend uses Supabase JWT with `user_roles` claim for auth
