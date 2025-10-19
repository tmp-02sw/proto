# Protocol Buffer Definitions

This directory contains the `.proto` files for gRPC service communication between microservices.

## Services

### ProjectService (project.proto)
**Hosted by:** `project-service` on port `50051`

**Used by:** `task-service` to:
- Validate project existence before creating tasks
- Check project status (prevent adding tasks to archived projects)
- Verify user permissions
- Get project details for task display

**Methods:**
- `GetProject` - Retrieve project details
- `ValidateProjectAccess` - Check if user can access project
- `GetProjectMembers` - Get list of project members
- `CanAddTasks` - Check if project accepts new tasks

---

### TaskService (task.proto)
**Hosted by:** `task-service` on port `50052`

**Used by:** `project-service` to:
- Get task counts and statistics per project
- List all tasks belonging to a project
- Delete tasks when project is deleted (cascade)

**Methods:**
- `GetTasksByProject` - Get all tasks for a project
- `GetTaskStatistics` - Get task stats with completion rate
- `DeleteTasksByProject` - Cascade delete tasks
- `CountTasksByProject` - Get task count

---

## Generating Code

To generate TypeScript code from these proto files:

```bash
# Install protoc compiler
# Then generate code for each service

# For task-service
npm run proto:generate

# For project-service
npm run proto:generate
```

## Port Allocation

- **project-service gRPC:** `50051`
- **task-service gRPC:** `50052`
- **project-service HTTP:** `3001`
- **task-service HTTP:** `3000`
