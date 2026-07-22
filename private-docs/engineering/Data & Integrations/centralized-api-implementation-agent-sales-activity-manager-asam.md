# 🔗 Centralized API Implementation - Agent Sales Activity Manager (ASAM)

## Overview

This document describes the implementation of a centralized API service to ensure data consistency across the Agent Sales Activity Manager (ASAM) application. The solution addresses the issue where updating agent data doesn't reflect immediately in the dashboard and other components.

## Problem Statement

- Data updates in one part of the application weren't reflected in other parts
- Multiple components were making direct `fetch()` calls to API endpoints
- No mechanism existed to notify components when data changed
- Inconsistent API usage patterns across the application

## Solution Implementation

### 1. Centralized API Service (`src/lib/api/apiService.ts`)

Extended the existing API service to include all CRUD operations:

#### New APIs Added:

- **UserApi**: Complete CRUD operations for users

  - `getUsers()`: Fetch all users
  - `getUser(id)`: Fetch single user
  - `createUser(userData)`: Create new user
  - `updateUser(id, userData)`: Update existing user
  - `deleteUser(id)`: Delete user
  - `getTeamMembers()`: Fetch team members

- **ClientApi**: Complete CRUD operations for clients

  - `getClients()`: Fetch all clients
  - `createClient(clientData)`: Create new client
  - `updateClient(id, clientData)`: Update client
  - `deleteClient(id)`: Delete client

- **WeeklyActivityApi**: Operations for weekly activities
  - `getWeeklyActivities(date, userId?)`: Fetch weekly activities
  - `saveWeeklyActivities(data)`: Save weekly activities

### 2. Event-Based Data Refresh System

Implemented a global event system to notify components when data changes:

```typescript
// Event emitter for data refresh notifications
class DataRefreshEmitter extends EventTarget {
  emit(eventType: string, detail?: any) {
    this.dispatchEvent(new CustomEvent(eventType, { detail }));
  }
}

export const dataRefreshEmitter = new DataRefreshEmitter();
```

#### Event Types:

- `USER_UPDATED`, `USER_CREATED`, `USER_DELETED`
- `ACTIVITY_UPDATED`, `ACTIVITY_CREATED`, `ACTIVITY_DELETED`
- `TASK_UPDATED`, `TASK_CREATED`, `TASK_DELETED`
- `CLIENT_UPDATED`, `CLIENT_CREATED`, `CLIENT_DELETED`
- `DATA_REFRESH_NEEDED` (general refresh event)

### 3. Automatic Dashboard Refresh

The dashboard now listens for data change events and automatically refreshes:

```typescript
useEffect(() => {
  const handleDataRefresh = () => {
    if (dateRange) {
      fetchDashboardData();
    }
  };

  // Listen for data refresh events
  dataRefreshEmitter.addEventListener(DATA_EVENTS.DATA_REFRESH_NEEDED, handleDataRefresh);
  // ... other event listeners

  return () => {
    // Clean up event listeners
    dataRefreshEmitter.removeEventListener(DATA_EVENTS.DATA_REFRESH_NEEDED, handleDataRefresh);
    // ... cleanup other listeners
  };
}, [dateRange]);
```

### 4. Updated Components

The following components have been updated to use the centralized API service:

- **`src/app/settings/agents/[id]/page.tsx`**: Agent detail/edit page
- **`src/app/settings/agents/new/page.tsx`**: Create new agent page
- **`src/app/settings/users/hooks/useAgents.ts`**: Agents data hook
- **`src/app/dashboard/page.tsx`**: Main dashboard

## Benefits

1. **Immediate Data Synchronization**: When agent data is updated, the dashboard and other components automatically refresh
2. **Consistent API Usage**: All components use the same API methods
3. **Error Handling**: Centralized error handling and logging
4. **Type Safety**: TypeScript interfaces ensure type safety across all API calls
5. **Maintainability**: Single source of truth for API operations

## Usage Guidelines

### For New Components

When creating new components that need to fetch or modify data:

1. **Import the API service**:

```typescript
import { UserApi, ActivityApi, ClientApi } from '@/lib/api/apiService';
```

2. **Use the API methods**:

```typescript
// Fetch data
const users = await UserApi.getUsers();

// Create data
const newUser = await UserApi.createUser(userData);

// Update data
const updatedUser = await UserApi.updateUser(userId, updateData);

// Delete data
await UserApi.deleteUser(userId);
```

3. **Listen for data changes** (if needed):

```typescript
useEffect(() => {
  const handleUserUpdate = (event: CustomEvent) => {
    // Refresh your component's data
    fetchData();
  };

  dataRefreshEmitter.addEventListener(DATA_EVENTS.USER_UPDATED, handleUserUpdate);

  return () => {
    dataRefreshEmitter.removeEventListener(DATA_EVENTS.USER_UPDATED, handleUserUpdate);
  };
}, []);
```

### Best Practices

1. **Always use the centralized API service** - Never make direct `fetch()` calls
2. **Handle errors appropriately** - The API service throws errors that should be caught
3. **Clean up event listeners** - Always remove event listeners in cleanup functions
4. **Use TypeScript types** - Import and use the provided interfaces for type safety

## Future Enhancements

1. **Caching Layer**: Implement caching to reduce unnecessary API calls
2. **Optimistic Updates**: Update UI immediately while API calls are in progress
3. **Retry Logic**: Add automatic retry for failed API calls
4. **Request Deduplication**: Prevent duplicate concurrent requests
5. **WebSocket Integration**: Real-time updates across multiple browser tabs

## Migration Checklist

If you have components still using direct `fetch()` calls, follow these steps:

1. [ ] Import the appropriate API from `apiService.ts`
2. [ ] Replace `fetch()` calls with API service methods
3. [ ] Add event listeners if the component needs to react to data changes
4. [ ] Test that data updates are reflected across the application
5. [ ] Remove any local state management that duplicates API data

## Example: Complete Migration

### Before (Direct Fetch):

```typescript
const response = await fetch(`/api/users/${userId}`, {
  method: 'PATCH',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify(updateData),
});

if (!response.ok) {
  throw new Error('Failed to update user');
}

const updatedUser = await response.json();
```

### After (Centralized API):

```typescript
try {
  const updatedUser = await UserApi.updateUser(userId, updateData);
  // The dashboard and other components will automatically refresh
} catch (error) {
  console.error('Failed to update user:', error);
  // Handle error appropriately
}
```

## Troubleshooting

### Data not refreshing automatically?

1. Check that the component is listening for the correct event type
2. Ensure event listeners are properly cleaned up and re-attached
3. Verify that the API method is emitting the refresh event

### Getting TypeScript errors?

1. Import the correct types from `apiService.ts`
2. Ensure all required fields are provided
3. Check that optional fields are marked with `?` in interfaces

### API calls failing?

1. Check browser console for detailed error messages
2. Verify that the user is authenticated
3. Ensure the API endpoint exists and is properly configured
