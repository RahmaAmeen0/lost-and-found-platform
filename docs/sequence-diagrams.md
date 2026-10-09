# Lost & Found Platform — Sequence Diagrams

This document describes the main interactions between users and the Lost & Found Platform.

The diagrams represent the intended business workflows and system responsibilities. They serve as a reference for the future Angular frontend, .NET Web API backend, and database implementation.

## 1. User Registration

This workflow describes how a new user creates an account.

```mermaid
sequenceDiagram
    actor User
    participant Frontend as Angular Frontend
    participant API as .NET Web API
    participant DB as Database

    User->>Frontend: Submit registration form
    Frontend->>Frontend: Validate required fields
    Frontend->>API: POST /api/auth/register
    API->>API: Validate request and password rules
    API->>DB: Check whether email already exists

    alt Email already registered
        DB-->>API: Email exists
        API-->>Frontend: 409 Conflict
        Frontend-->>User: Display email already registered
    else Email available
        DB-->>API: Email available
        API->>API: Hash password securely
        API->>DB: Create user account
        DB-->>API: User created
        API-->>Frontend: 201 Created
        Frontend-->>User: Display registration success
    end
```

**Business rules:**
- Email addresses must be unique.
- Passwords must never be stored in plaintext.
- The backend must validate registration data even if the frontend performs validation.
- The API must not expose sensitive account information in its responses.

## 2. User Login

This workflow describes how an existing user authenticates.

```mermaid
sequenceDiagram
    actor User
    participant Frontend as Angular Frontend
    participant API as .NET Web API
    participant DB as Database

    User->>Frontend: Submit login form
    Frontend->>Frontend: Validate required fields
    Frontend->>API: POST /api/auth/login
    API->>DB: Find user by email
    DB-->>API: User account or no result

    alt Account does not exist or password is incorrect
        API-->>Frontend: 401 Unauthorized
        Frontend-->>User: Display generic login error
    else Account found
        API->>API: Verify password hash

        alt Password is valid
            API->>API: Create authentication response
            API-->>Frontend: 200 OK with authentication result
            Frontend-->>User: Display login success
        else Password is invalid
            API-->>Frontend: 401 Unauthorized
            Frontend-->>User: Display generic login error
        end
    end
```

**Business rules:**
- The backend must verify the submitted password against its stored hash.
- Login errors should not reveal whether an email address exists.
- The authentication mechanism and token/session handling must be defined before implementation.
- Protected endpoints must enforce authentication and authorization.

## 3. Publish a Found Item

A finder submits an item that they have found. Public information is separated from private identifying details.

```mermaid
sequenceDiagram
    actor Finder
    participant Frontend as Angular Frontend
    participant API as .NET Web API
    participant DB as Database

    Finder->>Frontend: Submit found item form
    Frontend->>API: POST /api/found-items
    API->>API: Authenticate user and validate request

    alt Request is invalid or user is unauthorized
        API-->>Frontend: 400 Bad Request or 401 Unauthorized
        Frontend-->>Finder: Display validation or access error
    else Request is valid
        API->>DB: Save public item details
        API->>DB: Save private item details with restricted access
        DB-->>API: Item created
        API-->>Frontend: 201 Created with public item data
        Frontend-->>Finder: Display published item
    end
```

**Business rules:**
- Only authorized users may create found item posts.
- Public responses must not include private identifying details.
- Private details must be protected by backend authorization.
- The public and private records must be saved consistently.
- The initial item status and any moderation requirements must be defined in the business rules.

## 4. Search Found Items

A user searches the publicly available found item posts.

```mermaid
sequenceDiagram
    actor User
    participant Frontend as Angular Frontend
    participant API as .NET Web API
    participant DB as Database

    User->>Frontend: Enter search criteria
    Frontend->>API: GET /api/found-items with filters
    API->>API: Validate and normalize filters
    API->>DB: Query eligible public found items
    DB-->>API: Matching public item records
    API-->>Frontend: 200 OK with paginated results
    Frontend-->>User: Display found item posts
```

**Business rules:**
- Search results must contain public information only.
- Results should support pagination.
- Search filters may include category, color, approximate location, and date range.
- Private identifying details must not be returned in search results.

## 5. Submit a Lost Item Report and Generate Matches

If the user cannot find a suitable item, they submit a lost item report. The backend compares the report with eligible found item posts.

```mermaid
sequenceDiagram
    actor Reporter as User
    participant Frontend as Angular Frontend
    participant API as .NET Web API
    participant Matching as Matching Service
    participant DB as Database

    Reporter->>Frontend: Submit lost item report
    Frontend->>API: POST /api/lost-items
    API->>API: Authenticate user and validate report

    alt Report is invalid or user is unauthorized
        API-->>Frontend: Validation or authorization error
        Frontend-->>Reporter: Display error
    else Report is valid
        API->>DB: Save lost item report
        DB-->>API: Report created
        API->>Matching: Find potential matches
        Matching->>DB: Retrieve eligible found items
        DB-->>Matching: Public matching attributes
        Matching->>Matching: Calculate and rank match scores
        Matching->>DB: Save match results
        DB-->>Matching: Results saved
        Matching-->>API: Ranked potential matches
        API-->>Frontend: 201 Created with report and match summary
        Frontend-->>Reporter: Display potential matches
    end
```

**Business rules:**
- A lost item report and a found item post are separate records.
- Matching identifies candidates; it does not establish ownership.
- The initial matching algorithm may use weighted rules based on category, color, approximate location, and date.
- Matching scores and thresholds must be defined separately from ownership verification.
- Matching must not disclose private details to unauthorized users.
- If no suitable matches exist, the report should remain available according to the agreed report lifecycle.

## 6. Submit and Review a Claim

A user submits a claim for a found item. The claim must be reviewed before the item can be handed over.

```mermaid
sequenceDiagram
    actor Claimant as Claimant
    participant Frontend as Angular Frontend
    participant API as .NET Web API
    participant DB as Database
    actor Reviewer as Authorized Reviewer

    Claimant->>Frontend: Select found item and submit claim
    Frontend->>API: POST /api/claims
    API->>API: Authenticate claimant and validate request
    API->>DB: Check item status and existing claims
    DB-->>API: Item and claim information

    alt Item is unavailable or claim is not allowed
        API-->>Frontend: 409 Conflict or 403 Forbidden
        Frontend-->>Claimant: Display claim cannot be submitted
    else Claim is allowed
        API->>DB: Create pending claim
        DB-->>API: Claim created
        API-->>Frontend: 201 Created
        Frontend-->>Claimant: Display pending status

        Reviewer->>Frontend: Open claim for review
        Frontend->>API: Request pending claims
        API->>DB: Retrieve authorized review data
        DB-->>API: Claim review information
        API-->>Frontend: Return permitted review data
        Frontend-->>Reviewer: Display claim details

        Reviewer->>Frontend: Approve or reject claim
        Frontend->>API: Submit review decision
        API->>API: Verify reviewer permissions
        API->>DB: Update claim status
        DB-->>API: Claim updated
        API-->>Frontend: Return updated claim status
        Frontend-->>Reviewer: Display review result
    end
```

**Business rules:**
- A claim must reference an existing found item.
- Only authorized users may review claims.
- The claimant must not be allowed to approve their own claim.
- Claim submission does not automatically approve ownership.
- Valid claim statuses and rules for multiple claims must be defined before implementation.
- Approval must depend on the required ownership verification process.

## 7. Verify Ownership

Ownership verification uses private identifying details and, when necessary, additional evidence.

```mermaid
sequenceDiagram
    actor Claimant
    participant Frontend as Angular Frontend
    participant API as .NET Web API
    participant DB as Database
    actor Reviewer as Authorized Reviewer

    Claimant->>Frontend: Submit ownership answers or evidence
    Frontend->>API: Submit verification information
    API->>API: Authenticate claimant and validate submission
    API->>DB: Retrieve restricted verification data
    DB-->>API: Authorized verification data
    API->>API: Evaluate verification requirements
    API->>DB: Record verification outcome
    DB-->>API: Outcome recorded
    API-->>Frontend: Return permitted status information
    Frontend-->>Claimant: Display verification status

    opt Manual review required
        Reviewer->>Frontend: Review verification evidence
        Frontend->>API: Submit verification decision
        API->>API: Check reviewer authorization
        API->>DB: Record review decision
        DB-->>API: Decision saved
        API-->>Frontend: Return updated status
        Frontend-->>Reviewer: Display updated verification status
    end
```

**Business rules:**
- Verification answers and evidence must not be publicly accessible.
- The system must not reveal the correct private answers through error messages.
- Verification criteria and approval thresholds must be defined explicitly.
- Manual review may be required for ambiguous or high-risk cases.
- The verification result must be recorded and associated with the relevant claim.
- Verification must be completed before handover authorization.

## 8. Complete Item Handover

After ownership is verified and the claim is approved, the handover can be recorded.

```mermaid
sequenceDiagram
    actor Claimant
    actor Finder
    participant Frontend as Angular Frontend
    participant API as .NET Web API
    participant DB as Database

    Claimant->>Frontend: Request handover details
    Frontend->>API: Request approved claim
    API->>DB: Check claim, verification, and item status
    DB-->>API: Current claim and item state

    alt Claim is not approved or item is unavailable
        API-->>Frontend: 409 Conflict or 403 Forbidden
        Frontend-->>Claimant: Display handover unavailable
    else Handover is allowed
        API-->>Frontend: Return permitted handover information
        Frontend-->>Claimant: Display handover instructions
        Finder->>Frontend: Confirm item handover
        Frontend->>API: Submit handover confirmation
        API->>API: Validate authorization and current state
        API->>DB: Record handover and update item and claim statuses
        DB-->>API: Handover recorded
        API-->>Frontend: 200 OK with updated status
        Frontend-->>Claimant: Display completion status
        Frontend-->>Finder: Display completion status
    end
```

**Business rules:**
- Handover is permitted only after the claim is approved and ownership verification is complete.
- Only authorized participants may confirm a handover.
- Item and claim status changes must be performed consistently.
- A completed handover must not be duplicated.
- The system must retain a record of the completed handover.

## Implementation Notes

- These diagrams describe intended behavior, not implemented endpoints.
- Endpoint names and HTTP status codes are proposed and may be refined in the API specification.
- Database entities and transaction boundaries will be finalized during database design.
- Authentication, authorization, input validation, and privacy enforcement belong to the backend.
- Item, claim, and verification status transitions must be documented and enforced consistently.
- Error responses should avoid exposing private item details or sensitive account information.
