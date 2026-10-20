# Feature Specification: Document Upload and Management

**Feature Branch**: `001-document-upload-management`

**Created**: 2026-10-20

**Status**: Draft

**Input**: User description: "Document Upload and Management feature as described in StakeholderDocs/document-upload-and-management-feature.md — enable Contoso employees to upload work-related documents, organize them by category and project, find them through browsing and search, share them with colleagues, and use them from projects, tasks, and the dashboard, with role-based access, activity tracking, and offline local storage for the training environment."

## Clarifications

### Session 2026-10-20

- Q: What can a Team Lead see and do with team members' documents (FR-031)? → A: View, preview, download, edit metadata, and delete documents uploaded by members of their team, except documents in the "Personal Files" category. Replacing the file and sharing stay with the uploader.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Upload and Retrieve My Documents (Priority: P1)

An employee selects one or more files from their computer, gives each a title and category (optionally a description, project, and tags), and uploads them. They see upload progress and a clear success or error result for each file. Afterwards, they open "My Documents" to see everything they uploaded, sort and filter the list, and download any of their files.

**Why this priority**: This is the core value of the feature — a single, secure place to keep work documents instead of local drives and email attachments. Every other story depends on documents existing in the system.

**Independent Test**: Sign in as a seeded Employee, upload a valid PDF with a title and category, confirm it appears in My Documents with the correct metadata, sort/filter the list, and download the file and compare it with the original. Attempt an oversized file and an unsupported type and confirm both are rejected with clear messages.

**Acceptance Scenarios**:

1. **Given** a signed-in user on the upload screen, **When** they select a supported file of 25 MB or less, enter a title, choose a category, and confirm, **Then** the system shows upload progress, stores the document, shows a success message, and the document appears in My Documents.
2. **Given** a completed upload, **When** the user views the document details, **Then** the system shows the upload date and time, the uploader's name, the file size, and the file type, all recorded automatically.
3. **Given** the upload screen, **When** the user tries to upload without a title or without a category, **Then** the upload is blocked and the missing fields are identified.
4. **Given** the upload screen, **When** the user selects a file larger than 25 MB, **Then** the system rejects it before storage with a message stating the 25 MB limit, and nothing is stored.
5. **Given** the upload screen, **When** the user selects a file whose type is not on the supported list, **Then** the system rejects it with a message listing the supported types, and nothing is stored.
6. **Given** a file that fails the malware check, **When** the user uploads it, **Then** the system rejects it with a message saying it was blocked for security reasons, and nothing is stored.
7. **Given** the user selects several files at once, **When** some are valid and some are not, **Then** valid files are stored, invalid files are rejected, and the result for each file is reported individually.
8. **Given** My Documents contains several documents, **When** the user sorts by title, upload date, category, or file size, or filters by category, project, or date range, **Then** the list updates to match.
9. **Given** a document in My Documents, **When** the user chooses to download it, **Then** they receive a file identical to the one uploaded, named after the original file name.

---

### User Story 2 - Collaborate on Project Documents (Priority: P2)

A project team member uploads a document to a project they belong to. Every member of that project, including its Project Manager, sees it in the project's document list and can download it. Project members are notified when a new document is added. The Project Manager can remove any document from their project.

**Why this priority**: Linking documents to projects provides the project visibility the business asked for and replaces uncontrolled sharing through email and shared drives.

**Independent Test**: As a project member, upload a document linked to the seeded project. As another member, confirm a notification was received and the document appears in the project's documents and can be downloaded. As a non-member, confirm the document is not visible in any list or search and that directly requesting it is denied.

**Acceptance Scenarios**:

1. **Given** a user who is a member or the Project Manager of a project, **When** they upload a document and select that project, **Then** the document is associated with the project and appears in the project's document list.
2. **Given** a user who is not a member or the Project Manager of a project, **When** they try to upload a document to that project, **Then** the system refuses and no file or record is stored.
3. **Given** a project document, **When** any member of that project views the project, **Then** they see the document and can download it.
4. **Given** a new document added to a project, **When** the upload completes, **Then** every other project member and the Project Manager receives an in-app notification naming the document and project.
5. **Given** a user who is not a member of a project, **When** they open the project's documents or request one of its documents directly, **Then** access is denied and no document content or metadata is disclosed.
6. **Given** a Project Manager viewing a document in their project uploaded by someone else, **When** they delete it and confirm, **Then** the document is permanently removed.

---

### User Story 3 - Search for Documents (Priority: P3)

A user types a search term and finds documents by title, description, tags, uploader name, or associated project. Results only include documents the user is allowed to access.

**Why this priority**: Fast retrieval is a top business goal (locating a document in under 30 seconds), but it needs documents to exist first (P1/P2).

**Independent Test**: Upload documents with distinct titles, descriptions, and tags under different users and projects. Search as several seeded users and confirm each sees only documents they may access, matched on every listed field.

**Acceptance Scenarios**:

1. **Given** accessible documents with matching titles, descriptions, tags, uploader names, or project names, **When** the user searches for a term, **Then** all matching accessible documents are returned.
2. **Given** a document the user may not access that matches the term, **When** the user searches, **Then** that document is not in the results and no hint of its existence is shown.
3. **Given** no accessible document matches, **When** the user searches, **Then** the system shows a clear "no documents found" message.

---

### User Story 4 - Maintain My Documents (Priority: P3)

The uploader corrects a document's title, description, category, or tags; replaces the file with an updated version; or permanently deletes a document after confirming. A Team Lead can also correct the details of, or delete, documents uploaded by members of their team, except "Personal Files" (FR-031).

**Why this priority**: Keeping metadata accurate supports the goal of 90% of documents being properly categorized, and deletion lets users remove obsolete or mistaken uploads.

**Independent Test**: As an uploader, edit metadata and confirm the changes appear in lists and search; replace the file and confirm downloads return the new content; delete a document and confirm it is gone from all views and cannot be downloaded. As a different, non-privileged user, confirm each of these actions is refused.

**Acceptance Scenarios**:

1. **Given** a document the user uploaded, **When** they change its title, description, category, or tags and save, **Then** the new values are shown everywhere the document appears.
2. **Given** a document the user uploaded, **When** they replace the file with a valid new file, **Then** later downloads return the new file, the file size, type, and last-updated date are refreshed, and the previous file is no longer kept.
3. **Given** a replacement file that fails validation (size, type, or malware check), **When** the user submits it, **Then** the replacement is rejected and the original file remains available unchanged.
4. **Given** a document the user may delete, **When** they choose delete, **Then** the system asks for confirmation and states that deletion is permanent; only after confirmation is the document removed from all lists, searches, shares, and task attachments, and its file is removed from storage.
5. **Given** a user who is not permitted to edit, replace, or delete a document, **When** they attempt that action by any means, **Then** the system refuses and the document is unchanged.

---

### User Story 5 - Share Documents with Colleagues (Priority: P3)

A document owner shares a document with specific colleagues or with a team. Each recipient gets an in-app notification and finds the document in a "Shared with Me" section, from which they can view and download it.

**Why this priority**: Controlled sharing replaces risky email and shared-drive distribution, but it builds on upload and access rules from earlier stories.

**Independent Test**: As an owner, share a personal document with one seeded user and with a team. Confirm each recipient is notified, sees the document in Shared with Me, and can download it, while a user who was not a recipient cannot see or open it.

**Acceptance Scenarios**:

1. **Given** a document the user uploaded, **When** they share it with one or more selected users, **Then** each recipient receives an in-app notification and the document appears in their Shared with Me section.
2. **Given** a document the user uploaded, **When** they share it with a team, **Then** every member of that team at the time of sharing (other than the owner) becomes a recipient and is notified.
3. **Given** a recipient of a shared document, **When** they open Shared with Me, **Then** they see the document's title, owner, category, and share date, and can view and download it.
4. **Given** a recipient, **When** they attempt to edit, replace, delete, or re-share the shared document, **Then** the system refuses.
5. **Given** a user who is not the document's uploader, **When** they attempt to share it, **Then** the system refuses.
6. **Given** a document already shared with a user, **When** the owner shares it with that user again, **Then** no duplicate share or duplicate notification is created.
7. **Given** a shared document, **When** the owner removes a recipient's access, **Then** the document disappears from that recipient's Shared with Me and they can no longer open it.

---

### User Story 6 - Preview Documents in the Browser (Priority: P4)

A user with access to a PDF or image document opens a preview in the application without downloading it.

**Why this priority**: Previewing saves time but is a convenience on top of download.

**Independent Test**: Upload a PDF and a PNG, open their previews as an authorized user, and confirm they display. Confirm unsupported preview types offer download only, and that an unauthorized user cannot open the preview.

**Acceptance Scenarios**:

1. **Given** an accessible PDF, JPEG, or PNG document, **When** the user chooses preview, **Then** the document is displayed in the application.
2. **Given** an accessible document of a type that cannot be previewed, **When** the user views it, **Then** preview is not offered and download is available.
3. **Given** a user without access to a document, **When** they request its preview by any means, **Then** access is denied and no content is returned.

---

### User Story 7 - Use Documents from Tasks and the Dashboard (Priority: P4)

While viewing a task, a user sees the documents attached to it, uploads a new document directly from the task, or attaches one of their existing documents. On the dashboard home page, the user sees their 5 most recently uploaded documents and a document count among the summary cards.

**Why this priority**: Integration places documents where employees already work, raising adoption, but depends on the core document capabilities.

**Independent Test**: Open a seeded task linked to a project, upload a document from the task, and confirm it is attached to the task and associated with the task's project. Open the dashboard and confirm the Recent Documents list and document count reflect the user's uploads.

**Acceptance Scenarios**:

1. **Given** a task the user may view, **When** they open the task details, **Then** they see the documents attached to that task that they are allowed to access.
2. **Given** a task linked to a project, **When** the user uploads a document from the task details, **Then** the document is attached to the task and automatically associated with the task's project.
3. **Given** a task the user may view and a document they uploaded, **When** they attach that document to the task, **Then** the document appears in the task's documents and, if the task has a project and the document had none, the document becomes associated with that project.
4. **Given** the user has uploaded documents, **When** they open the dashboard home page, **Then** a Recent Documents area lists their 5 most recently uploaded documents, newest first, and a summary card shows how many documents they have uploaded.
5. **Given** the user has uploaded no documents, **When** they open the dashboard, **Then** the Recent Documents area shows an empty-state message with a way to upload, and the count shows 0.

---

### User Story 8 - Oversight, Activity Tracking, and Reports (Priority: P5)

Team Leads oversee documents uploaded by members of their team, and Administrators review all documents for audit and compliance. The system records every upload, download, preview, metadata change, file replacement, deletion, and share action. Administrators generate reports on the most uploaded document types, the most active uploaders, and document access patterns.

**Why this priority**: Oversight and audit support compliance and the "zero security incidents" goal, but they are not required for employees to begin using documents.

**Independent Test**: Perform uploads, downloads, shares, and deletions as several seeded users. As the Administrator, run each report for a date range and confirm the figures match the actions performed. As a non-Administrator, confirm the reports cannot be opened.

**Acceptance Scenarios**:

1. **Given** any document action listed above, **When** it completes, **Then** an activity record is kept with the action, the acting user, the document, and the date and time.
2. **Given** an Administrator, **When** they open document reports for a date range, **Then** they see the most uploaded document types, the most active uploaders, and access patterns (views/downloads per document and per user) for that range.
3. **Given** a user who is not an Administrator, **When** they attempt to open document reports or activity records, **Then** access is denied.
4. **Given** an Administrator, **When** they browse or search documents, **Then** they can see, view, and download any document in the system.
5. **Given** a Team Lead, **When** they open a team documents view, **Then** they see documents uploaded by members of their team, excluding documents in the "Personal Files" category, and can view, download, edit metadata of, and delete them (see FR-031).
6. **Given** a Team Lead and a team member's document in the "Personal Files" category, **When** the Team Lead tries to find, open, download, edit, or delete it without another access grant, **Then** access is denied and no document content or metadata is disclosed.

---

### Edge Cases

- **Empty file**: A zero-byte file is rejected as invalid.
- **Exactly 25 MB**: A file of exactly 25 MB is accepted; anything larger is rejected.
- **Disguised type**: A file whose name extension is on the supported list but whose declared type does not match a supported type is rejected.
- **Unsafe or duplicate file names**: File names containing path characters, very long names, or names identical to existing uploads never affect where or how files are stored and never overwrite another document; the original name is kept only as display information.
- **Duplicate titles**: Two documents may share the same title; each remains a separate document.
- **Storage failure during upload**: If the file cannot be stored, no document record is created and the user sees an actionable error.
- **Record failure after storage**: If the file is stored but the document record cannot be saved, the stored file is removed and the user sees an actionable error; no orphaned file or record remains.
- **Replacement failure**: If a replacement cannot be completed, the original file and record remain intact and usable.
- **File missing from storage**: If a document's file is missing when downloaded or previewed, the user sees a clear "file unavailable" message, and the problem is logged for administrators.
- **Deletion cleanup failure**: If the document record is deleted but its file cannot be removed, the document is still gone for users, and the leftover file is logged for later cleanup.
- **Concurrent deletion**: If a document is deleted while another user is viewing or downloading it, the other user sees a "document no longer available" message.
- **Project membership removed**: A user removed from a project immediately loses access to that project's documents, except documents they uploaded themselves or that were shared with them directly.
- **Task without a project**: Documents uploaded from a task that has no project are attached to the task but have no project association.
- **Attaching a document from another project**: Attaching an existing document to a task whose project differs from the document's current project is refused with an explanation.
- **Sharing with self or with nobody**: Sharing with oneself is ignored; submitting a share with no recipients is blocked.
- **Notifications turned off**: Users who have disabled in-app notifications do not receive document notifications, consistent with existing notification preferences.
- **Interrupted upload**: If the user leaves or the connection drops during an upload, the partial upload is discarded and nothing is stored.
- **Large lists**: Lists with up to 500 documents remain usable and meet the load-time target.

## Requirements *(mandatory)*

### Functional Requirements

**Upload and validation**

- **FR-001**: The system MUST allow signed-in users to select and upload one or more files in a single upload action.
- **FR-002**: The system MUST accept only these file types: PDF; Word, Excel, and PowerPoint documents (current and legacy formats); plain text; JPEG and PNG images. All other types MUST be rejected with a message listing the supported types.
- **FR-003**: The system MUST reject empty files and files larger than 25 MB with a message stating the limit, before any content is permanently stored.
- **FR-004**: The system MUST validate file type and size on the server for every upload and replacement, regardless of any checks performed in the browser.
- **FR-005**: The system MUST check every uploaded or replacement file for malware before it is permanently stored, and MUST reject files that fail the check with a security message. The check MUST work offline and MUST be replaceable by a stronger scanner later without changing the user-facing behavior.
- **FR-006**: Users MUST provide a title (required, up to 255 characters) and a category (required) for each document. Users MAY provide a description (up to 2,000 characters), one associated project, and up to 10 tags (each up to 50 characters).
- **FR-007**: The category MUST be one of: Project Documents, Team Resources, Personal Files, Reports, Presentations, Other.
- **FR-008**: When several files are uploaded together, the title for each file MUST default to its file name (without extension) and remain editable per file; the category, description, project, and tags entered MUST apply to every file in the batch.
- **FR-009**: The system MUST automatically record for each document: upload date and time, uploader, original file name, file size, and file type (supporting file type descriptors up to 255 characters).
- **FR-010**: The system MUST show upload progress and report success or failure for each file individually.

**Storage and data integrity**

- **FR-011**: Uploaded files MUST be stored in a location that is not publicly served by the web application, and MUST be retrievable only through application operations that check the requester's access.
- **FR-012**: The system MUST assign each stored file a system-generated, unique storage name. User-supplied file names MUST NOT be used to determine storage locations.
- **FR-013**: The system MUST store the file before creating the document record. If the record cannot be created, the system MUST remove the stored file. If the file cannot be stored, no record MUST be created.
- **FR-014**: File replacement MUST store the new file first, then update the record, then remove the previous file. If the record update fails, the new file MUST be removed and the original kept.
- **FR-015**: Deletion MUST remove the document record and all of its shares and task attachments together, then remove the stored file. A failure to remove the file MUST be logged for cleanup without restoring the document for users.
- **FR-016**: Storage MUST be accessed through a replaceable storage capability so that the local training storage can later be swapped for another storage provider without changing business rules, screens, or stored document data.
- **FR-017**: Expected failures (validation, permission, missing file, storage errors) MUST show actionable messages to the user. Unexpected failures MUST be logged without including document contents or secrets.

**Browsing, search, and access**

- **FR-018**: Users MUST be able to view a "My Documents" list of the documents they uploaded, showing title, category, upload date, file size, and associated project.
- **FR-019**: Users MUST be able to sort My Documents by title, upload date, category, and file size, and filter by category, associated project, and upload date range.
- **FR-020**: When viewing a project, users permitted to access that project MUST see all documents associated with it.
- **FR-021**: Users MUST be able to search documents by title, description, tags, uploader name, and associated project name; results MUST include only documents the user may access.
- **FR-022**: Users MUST be able to download any document they may access; the download MUST carry the original file name.
- **FR-023**: Users MUST be able to preview PDF, JPEG, and PNG documents they may access within the application; other types MUST offer download only.

**Maintenance**

- **FR-024**: Users permitted by the access rules MUST be able to edit a document's title, description, category, and tags, and replace its file with a new file that passes all upload validation.
- **FR-025**: Users permitted by the access rules MUST be able to delete a document only after an explicit confirmation stating that deletion is permanent and cannot be undone.

**Sharing and notifications**

- **FR-026**: A document's uploader MUST be able to share it with specific users and with teams, and to remove a recipient's access. A team means the users who share the same department; sharing with a team grants access to the team's members at the time of sharing.
- **FR-027**: Each user MUST have a "Shared with Me" section listing documents shared with them, with title, owner, category, and share date.
- **FR-028**: The system MUST send an in-app notification to each new share recipient, and to every other member (including the Project Manager) of a project when a document is added to that project, honoring each user's existing in-app notification preference.

**Access rules (authorization)**

- **FR-029**: Every document operation — listing, searching, viewing details, previewing, downloading, uploading, editing, replacing, sharing, and deleting — MUST be authorized on the server using the signed-in user's identity, role, ownership, project membership, team (department) membership, document category, and share grants. Knowing a document's identifier, or hiding a control in the interface, MUST NOT grant access. Denied requests MUST disclose no document content or metadata.
- **FR-030**: Access MUST follow this matrix ("project member" includes the project's Project Manager; "task participant" means the assignee or creator of a task the document is attached to):

  | Action | Uploader | Project member (document's project) | Project Manager (document's project) | Share recipient | Task participant (task without project) | Team Lead (uploader's team) | Administrator | Any other user |
  |--------|----------|-------------------------------------|--------------------------------------|-----------------|------------------------------------------|-----------------------------|---------------|----------------|
  | See in lists/search, view details, preview, download | Yes | Yes | Yes | Yes | Yes | Yes, except Personal Files (FR-031) | Yes | No |
  | Edit metadata | Yes | No | No | No | No | Yes, except Personal Files (FR-031) | No | No |
  | Replace file | Yes | No | No | No | No | No | No | No |
  | Delete | Yes | No | Yes | No | No | Yes, except Personal Files (FR-031) | Yes | No |
  | Share / remove shares | Yes | No | No | No | No | No | No | No |

- **FR-031**: Team Leads MUST be able to see in lists and search, view details of, preview, download, edit the metadata of, and delete documents uploaded by members of their team (other users in the same department), except documents in the "Personal Files" category. Team Leads MUST NOT replace the file of, or share, a team member's document. The team relationship and category MUST be evaluated at the time of each request, so a document recategorized as "Personal Files", or whose uploader leaves the department, is no longer accessible through this rule. This rule does not remove access a Team Lead has through another grant in FR-030 (for example, as a project member or share recipient). Team Lead edits and deletions MUST be recorded in the activity log (FR-037) as actions by the Team Lead.
- **FR-032**: Any signed-in user MUST be able to upload personal documents (no project). Uploading to a project, or from a task linked to a project, MUST require being a member or the Project Manager of that project.
- **FR-033**: Unauthenticated users MUST NOT be able to reach any document screen or retrieve any document content.

**Task and dashboard integration**

- **FR-034**: Task details MUST show the documents attached to the task that the viewing user may access, and MUST let users who may view the task upload a new document or attach a document they uploaded.
- **FR-035**: Documents uploaded from, or attached to, a task linked to a project MUST be associated with that project. Attaching a document that already belongs to a different project MUST be refused.
- **FR-036**: The dashboard home page MUST show the user's 5 most recently uploaded documents, newest first, and a summary card with the number of documents the user has uploaded.

**Activity tracking and reports**

- **FR-037**: The system MUST record an activity entry for every upload, download, preview, metadata edit, file replacement, deletion, share, and share removal, capturing the action, the acting user, the document (including its title at the time), and the date and time. Activity entries MUST remain after a document is deleted.
- **FR-038**: Administrators MUST be able to generate reports for a selected date range showing the most uploaded document types, the most active uploaders, and access patterns (views and downloads per document and per user). Non-administrators MUST NOT access reports or activity entries.

**Training environment constraints**

- **FR-039**: The feature MUST work fully offline in the local training environment, without cloud accounts, paid services, or external services at runtime.
- **FR-040**: Any simulated security control (such as the training malware check) MUST be clearly documented as training-only and not a production guarantee.
- **FR-041**: Introducing document data into an existing local training database MUST follow a documented upgrade or recreate path; any destructive reset MUST require the user's explicit consent.
- **FR-042**: Uploaded user content MUST never be placed in version control.

### Key Entities

- **Document**: A work file stored in the system together with its metadata — title, optional description, category, optional associated project, tags, original file name, system-generated storage name, file size, file type, uploader, upload date and time, and last-updated date and time. Uses a whole-number identifier consistent with existing users and projects. Belongs to one uploader and at most one project.
- **Document Tag**: A short free-text label attached to a document to aid search; a document has 0–10 tags.
- **Document Share**: A grant giving one recipient user view and download access to one document, recording who shared it and when. A document is shared at most once with the same recipient.
- **Task Document Attachment**: A link between a task and a document, recording who attached it and when. A document can be attached to more than one task within the same project.
- **Document Activity Entry**: A permanent record of an action on a document — action type, acting user, document identifier and title at the time, and date and time — used for audit and reports.
- **Existing entities used**: User (role and department determine team membership and access), Project and Project Member (determine project document access), Task (task attachments), Notification (share and new-document alerts).

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A user can upload a single document — select the file, enter a title, choose a category, and confirm — with no more than 3 clicks beyond entering the required information.
- **SC-002**: In the training environment, an upload of a file of up to 25 MB completes within 30 seconds.
- **SC-003**: Document lists containing up to 500 documents display within 2 seconds, and search results return within 2 seconds.
- **SC-004**: PDF and image previews display within 3 seconds.
- **SC-005**: Every allowed and denied combination in the access matrix (FR-030, FR-031) is confirmed using the seeded training identities, with 0 cases where a user can list, find, preview, download, change, or delete a document they are not permitted to access.
- **SC-006**: 100% of invalid uploads (oversized, empty, unsupported type, or failed malware check) are rejected with a message naming the reason, and none leave a stored file or document record.
- **SC-007**: In failure tests (storage failure, record failure, replacement failure), 0 orphaned document records or files remain.
- **SC-008**: In usability testing, users locate a known document through search or filters in under 30 seconds on average.
- **SC-009**: 100% of the tracked document actions performed during acceptance testing appear in the activity records and are reflected in the Administrator reports.
- **SC-010**: After launch (business outcome), 70% of active dashboard users upload at least one document within 3 months, and at least 90% of documents in a random review sample are judged to have the correct category.

## Assumptions

- **Training scope**: ContosoDashboard is a training application. The existing mock sign-in (choosing a seeded user) is kept and remains labeled training-only; no production security guarantees are claimed.
- **Roles**: The existing roles (Employee, Team Lead, Project Manager, Administrator) are reused without new roles. A "project member" is any user assigned to the project plus its Project Manager; "their projects" for a Project Manager means projects they manage.
- **Teams**: A "team" is the group of users who share the same department, matching the existing "My Team" view. Team shares are fixed when made: people who join the department later do not automatically gain access.
- **Administrator access**: "Full access for audit and compliance" means Administrators can see, preview, download, and delete any document, and run reports. Administrators do not edit or replace another user's document or share on their behalf, to preserve authorship; editing metadata of team members' documents is limited to the uploader's Team Lead (FR-031).
- **Project Manager access**: Project Managers can view and delete any document in their projects, but editing metadata and replacing files stay with the uploader, as stated in the stakeholder input.
- **Uploader access**: The uploader keeps full control of their own documents even after leaving the associated project.
- **Malware check**: Because the feature must run offline, the training environment uses a local, simulated malware check (for example, detecting standard antivirus test files) behind a replaceable check. This is documented as a training stand-in and not real malware protection.
- **Replacement**: Replacing a file overwrites the current version. The previous file is not kept, because version history is out of scope.
- **Dashboard count**: The dashboard document count shows documents the user uploaded, matching the scope of the Recent Documents area.
- **Notifications**: Document notifications use the existing in-app notifications and respect each user's existing in-app notification preference. Email notifications are not part of this feature.
- **File type list**: "Microsoft Office documents" covers both current (.docx, .xlsx, .pptx) and legacy (.doc, .xls, .ppt) Word, Excel, and PowerPoint formats; "text files" means plain-text (.txt) files.
- **Typical files**: Most documents are under 10 MB, and the training environment has enough local disk space for uploads. Storage quotas are not enforced.
- **Database**: The current local database is created at startup and has no schema-migration mechanism. Adding document data may require recreating the disposable training database. This must be documented and must only happen with user consent.
- **Implementation guidance deferred to planning**: The stakeholder document's technical guidance (storage abstraction and naming, storage folder layout such as uploader/project/unique-name, text-based category values, upload component handling, and sign-in claim requirements) is design input for the implementation plan, not part of this specification's acceptance criteria.
- **Out of scope** (per stakeholder input): real-time collaborative editing; version history and rollback; approval workflows and document routing; integration with external systems such as SharePoint or OneDrive; mobile apps; document templates or generation; storage quotas; soft delete, trash, or recovery of deleted documents; cloud storage in the training implementation; email notifications for document events.
