# Lost & Found Platform — Product Points

## 1. Product Overview

A platform that connects people who have lost items with people who have found them.

The platform allows users to report lost and found items, search and browse reports, identify potential matches, verify item ownership, and complete the item return process.

---

## 2. Users

### User

A user can:

* Create an account and log in.
* Report a lost item.
* Report a found item.
* Browse and search reported items.
* View potential matches.
* Submit a claim for an item.
* Complete ownership verification.
* Track the status of their reports and claims.
* Receive notifications.

### Admin

The admin can:

* Manage users.
* Manage lost and found reports.
* Review claims.
* Handle reported or suspicious content.

---

## 3. Authentication & Profile

* User Registration
* User Login
* User Profile
* Manage personal information

---

## 4. Lost Item Reporting

A user can create a Lost Item Report containing:

* Item Name
* Category
* Description
* Images
* Lost Date
* Lost Location
* Identifying Details

The report has a status that changes throughout its lifecycle.

---

## 5. Found Item Reporting

A user can create a Found Item Report containing:

* Item Name
* Category
* Description
* Images
* Found Date
* Found Location
* Item Details

The report also has a status that changes throughout its lifecycle.

---

## 6. Browse & Search

Users can browse and search reported items using information such as:

* Keywords
* Category
* Location
* Date

Users can view the details of a Lost or Found Report.

---

## 7. Matching

The system compares Lost Reports with Found Reports to identify potential matches.

The matching process considers information such as:

* Category
* Location
* Date
* Item characteristics
* Description

When the system identifies a potential match, it creates a Match and notifies the relevant user.

---

## 8. Notifications

Users receive notifications for important events, such as:

* A potential match has been found.
* A claim has been submitted or updated.
* Ownership verification has been completed.
* The claim has been approved or rejected.

---

## 9. Claim

When a user identifies a found item as potentially belonging to them, they can submit a Claim.

The claim is associated with the potential match and goes through a verification process.

---

## 10. Ownership Verification

The system verifies whether the claimant is the actual owner of the item.

The claimant provides identifying information that can be used to prove ownership.

Based on the verification result, the claim is either:

* Approved
* Rejected

---

## 11. Item Handover

After a claim is approved:

* The item is marked as ready to be returned.
* The handover/return process is recorded.
* The item status is updated to `Returned`.
* The related claim is marked as completed.

---

## 12. Admin Management

The Admin can:

* Manage Users
* Manage Lost Reports
* Manage Found Reports
* Review Claims
* Review reported/suspicious content
* Take appropriate actions on problematic reports or users
