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

---

## Automatic Pipeline Triggers

When changes are pushed to this repository, dependent service pipelines are automatically triggered:

### Dependent Services
- ✅ **task-service** - Uses `task.proto` and `project.proto`
- ✅ **project-service** - Uses `project.proto`
- ✅ **notification-service** - Uses proto files for messaging

### How It Works

1. **Proto Repository Workflow** (`.github/workflows/trigger-dependent-services.yml`)
   - Triggers on push to `main`, `master`, or `develop` branches
   - Only triggers when `.proto` files are changed
   - Dispatches `repository_dispatch` events to dependent services

2. **Service Workflows**
   - Listen for `repository_dispatch` events with type `proto-updated`
   - Automatically rebuild and redeploy when proto files change
   - Display proto commit information in workflow summary

### Setup Requirements

**In the proto repository:**
- Add `PAT_TOKEN` secret with permissions to trigger workflows in dependent repositories

**In dependent service repositories:**
- Workflows automatically listen for `proto-updated` events
- No additional configuration needed

### Manual Trigger

To manually trigger dependent services:

```bash
# Using GitHub CLI
gh workflow run trigger-dependent-services.yml --repo ws02-project/proto
```

Or use the GitHub API to dispatch events directly to service repositories.
