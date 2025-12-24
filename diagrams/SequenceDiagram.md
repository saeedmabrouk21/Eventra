```mermaid
sequenceDiagram
    Admin->>System: Create tenant
    System-->>Admin: Tenant created confirmation

    EndUser->>System: Sign up
    System-->>EndUser: Welcome email sent

    EndUser->>System: Sign in
    System-->>EndUser: Sign in confirmation

    EndUser->>System: View profile
    System-->>EndUser: Profile data

    EndUser->>System: Update profile
    System-->>EndUser: Update confirmation

    Admin->>System: Deactivate user
    System-->>Admin: User deactivated confirmation

    Admin->>System: Change user role
    System-->>Admin: Role updated confirmation

    Admin->>System: Search audit log
    System-->>Admin: Audit log results

```