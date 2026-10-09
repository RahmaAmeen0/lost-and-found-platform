# Lost & Found Platform

A web-based Lost and Found platform that helps users report lost items, publish found items, discover potential matches, and safely reclaim their belongings.

The platform aims to simplify the process of reuniting lost items with their owners while protecting private item details to reduce fraudulent ownership claims.

## Project Goals

- Allow users to report lost items and publish found item posts.
- Enable users to browse and search publicly available found items.
- Identify potential matches between lost reports and found item posts.
- Support a claim and ownership verification workflow.
- Protect private item details from unauthorized access.
- Record the successful handover of recovered items.

## Core Features

- **User Authentication:** Registration and login.
- **Found Item Reporting:** Publish found items with public and private details.
- **Lost Item Reporting:** Submit reports describing lost belongings.
- **Item Discovery:** Browse and search found item posts.
- **Matching:** Identify and rank potential matches using predefined matching rules.
- **Claim Management:** Submit and review requests to reclaim found items.
- **Ownership Verification:** Verify claims using private item details and other appropriate evidence.
- **Handover Tracking:** Record the completion of item handovers.

## Main User Flows

1. A user finds an item and publishes a found item post.
2. A person who lost an item searches the available found item posts.
3. If no suitable item is found, the user submits a lost item report.
4. The system identifies potential matches between lost reports and found items.
5. The user submits a claim for a potential match.
6. The claim is reviewed, and ownership is verified before approval.
7. The handover is recorded, and the item's status is updated.

A potential match does not automatically prove ownership. Claims must follow the ownership verification process.

## Privacy and Security

- Public item posts must not expose private identifying details.
- Private item details must only be accessible to authorized users and system components.
- Ownership verification must use information that is not fully disclosed in public posts.
- Users must only be allowed to perform actions permitted by their roles and the item's current status.
- Authentication and authorization must be enforced by the backend.

## Technology Stack

The intended application architecture includes:

- **Frontend:** Angular
- **Backend:** .NET Web API
- **Database:** To be selected and designed during the database design phase.

The technology choices and implementation details will be finalized before development begins.

## Project Status

**Current phase:** Requirements and documentation.

The project is currently being prepared for MVP development. The business workflows, functional requirements, security rules, and database design will be clarified before implementation.

## Planned MVP

The initial release is expected to focus on:

- User registration and login.
- Creating and browsing found item posts.
- Submitting lost item reports.
- Basic rule-based matching.
- Claim submission and ownership verification.
- Recording item handovers.

Advanced matching, real-time notifications, and other nonessential features may be considered for later releases.

## Documentation

Project documentation will cover:

- Functional requirements and business rules.
- Sequence diagrams and item status lifecycles.
- System architecture.
- Database schema and data dictionary.
- API specifications.
- Privacy, security, and authorization.
- MVP scope and implementation roadmap.

## Future Development

Development will proceed in stages:

1. Review and finalize requirements.
2. Define and validate the MVP scope.
3. Design the database and data relationships.
4. Specify backend API contracts.
5. Implement the backend and Angular frontend.
6. Test the main workflows and security rules.

