````markdown
# Lost & Found Platform — Sequence Diagrams

This document describes the main interactions between users and the Lost & Found Platform.

The diagrams focus on the main business flows of the system.

---

## 1. User Registration & Login

```mermaid
sequenceDiagram
    actor User
    participant System
    participant Database

    User->>System: Register
    System->>Database: Check if email already exists

    alt Email already exists
        Database-->>System: Email exists
        System-->>User: Registration failed
    else Email available
        Database-->>System: Email available
        System->>Database: Create user account
        Database-->>System: User created
        System-->>User: Registration successful
    end

    User->>System: Login
    System->>Database: Validate credentials

    alt Valid credentials
        Database-->>System: Credentials valid
        System-->>User: Login successful
    else Invalid credentials
        Database-->>System: Credentials invalid
        System-->>User: Login failed
    end
````

---

## 2. Create Lost Item Report

```mermaid
sequenceDiagram
    actor User
    participant System
    participant Database
    participant Matching as Matching Service
    participant Notification as Notification Service

    User->>System: Submit Lost Item Report

    System->>System: Validate report data

    alt Invalid data
        System-->>User: Validation errors
    else Valid data
        System->>Database: Save Lost Report
        Database-->>System: Report created

        System->>Matching: Check for potential matches
        Matching-->>System: Matching results

        alt Potential match found
            System->>Notification: Notify relevant user(s)
            Notification-->>User: Match notification
        else No match found
            System-->>User: Report created successfully
        end
    end
```

---

## 3. Create Found Item Report

```mermaid
sequenceDiagram
    actor User
    participant System
    participant Database
    participant Matching as Matching Service
    participant Notification as Notification Service

    User->>System: Submit Found Item Report

    System->>System: Validate report data

    alt Invalid data
        System-->>User: Validation errors
    else Valid data
        System->>Database: Save Found Report
        Database-->>System: Report created

        System->>Matching: Check for potential matches
        Matching-->>System: Matching results

        alt Potential match found
            System->>Notification: Notify relevant user(s)
            Notification-->>User: Match notification
        else No match found
            System-->>User: Report created successfully
        end
    end
```

---

## 4. Search & Browse Reports

```mermaid
sequenceDiagram
    actor User
    participant System
    participant Database

    User->>System: Search for an item
    Note over User,System: Keyword / Category / Location / Date

    System->>Database: Search Lost & Found Reports
    Database-->>System: Matching reports

    System-->>User: Display search results

    User->>System: View report details
    System->>Database: Get report details
    Database-->>System: Report details

    System-->>User: Display report details
```

---

## 5. Matching Lost & Found Items

```mermaid
sequenceDiagram
    participant System
    participant Matching as Matching Service
    participant Database
    participant Notification as Notification Service
    actor LostUser as Lost Item Owner
    actor FoundUser as Found Item Reporter

    System->>Matching: Check new Lost/Found Report

    Matching->>Database: Get relevant reports
    Database-->>Matching: Lost/Found reports

    Matching->>Matching: Compare reports
    Note over Matching: Category<br/>Location<br/>Date<br/>Item characteristics<br/>Description

    alt Potential match found
        Matching->>Database: Create Match
        Database-->>Matching: Match created

        Matching->>Notification: Notify Lost User
        Matching->>Notification: Notify Found User

        Notification-->>LostUser: Potential match notification
        Notification-->>FoundUser: Potential match notification
    else No match found
        Matching-->>System: No potential match
    end
```

---

## 6. Submit Claim & Ownership Verification

```mermaid
sequenceDiagram
    actor LostUser as Claimant
    participant System
    participant Database
    participant Verification as Verification Service
    actor FoundUser as Found Item Reporter
    participant Notification as Notification Service

    LostUser->>System: View Potential Match
    System->>Database: Get Match details
    Database-->>System: Match details

    System-->>LostUser: Display matched item

    LostUser->>System: Submit Claim
    System->>Database: Create Claim
    Database-->>System: Claim created

    System->>Verification: Start Ownership Verification

    LostUser->>Verification: Provide ownership details
    Verification->>Verification: Verify ownership information

    alt Ownership verified
        Verification-->>System: Verification Approved

        System->>Database: Update Claim status to Approved
        System->>Notification: Notify Claimant
        System->>Notification: Notify Found User

        Notification-->>LostUser: Claim approved
        Notification-->>FoundUser: Claim approved - item can be returned

    else Ownership not verified
        Verification-->>System: Verification Rejected

        System->>Database: Update Claim status to Rejected
        System->>Notification: Notify Claimant

        Notification-->>LostUser: Claim rejected
    end
```

---

## 7. Item Handover & Claim Completion

```mermaid
sequenceDiagram
    actor FoundUser as Found Item Reporter
    actor LostUser as Item Owner
    participant System
    participant Database
    participant Notification as Notification Service

    FoundUser->>System: Confirm item is ready for handover

    System->>Database: Update handover status
    Database-->>System: Status updated

    System->>Notification: Notify Item Owner
    Notification-->>LostUser: Item ready for handover

    LostUser->>FoundUser: Complete item handover

    FoundUser->>System: Confirm handover
    System->>Database: Record handover
    Database-->>System: Handover recorded

    System->>Database: Update Item status to Returned
    System->>Database: Update Claim status to Completed

    System->>Notification: Send completion notification
    Notification-->>LostUser: Item returned successfully
    Notification-->>FoundUser: Handover completed
```

---

## 8. Admin Reviews a Report

```mermaid
sequenceDiagram
    actor Admin
    participant System
    participant Database
    participant Notification as Notification Service
    actor User

    Admin->>System: Open reported/suspicious content
    System->>Database: Get report details
    Database-->>System: Report details

    System-->>Admin: Display report details

    Admin->>System: Review report

    alt Report is valid
        Admin->>System: Approve report
        System->>Database: Update report status
        Database-->>System: Status updated
        System-->>Admin: Report approved

    else Report violates rules
        Admin->>System: Take action
        System->>Database: Update report/user status
        Database-->>System: Status updated

        System->>Notification: Notify affected user
        Notification-->>User: Action notification

        System-->>Admin: Action completed
    end
```

---

## Main Business Flow

The overall platform flow can be summarized as:

```mermaid
flowchart TD
    A[User Registration / Login] --> B[Report Lost Item]
    A --> C[Report Found Item]

    B --> D[Matching]
    C --> D

    D --> E{Potential Match?}

    E -->|No| F[Continue Searching]
    E -->|Yes| G[Notify Relevant Users]

    G --> H[View Match]
    H --> I[Submit Claim]

    I --> J[Ownership Verification]

    J --> K{Verification Result}

    K -->|Rejected| L[Claim Rejected]
    K -->|Approved| M[Claim Approved]

    M --> N[Item Handover]
    N --> O[Item Status: Returned]
    O --> P[Claim Status: Completed]
```

---

## Main Entities Involved

The main business entities involved in these flows are:

* User
* LostReport
* FoundReport
* Category
* Match
* Claim
* OwnershipVerification
* Handover
* Notification

---

## Main Actors

### User

A registered user can:

* Report lost items
* Report found items
* Search and browse reports
* View potential matches
* Submit claims
* Complete ownership verification
* Track report and claim status
* Receive notifications

### Admin

An admin can:

* Manage users
* Review lost/found reports
* Review claims
* Review reported or suspicious content
* Take appropriate actions

