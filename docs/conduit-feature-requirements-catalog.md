# Conduit Feature Requirements for Spec-Driven Development Demos

This document collects multiple candidate feature requirements for the Conduit application. These can be used to demonstrate requirement capture, specification writing, test design, implementation planning, and AI-assisted development.

---

## Feature 1: Bookmark an article

### Business goal
Allow authenticated users to save articles for later reading.

### Functional requirements
- A logged-in user can bookmark an article by slug.
- A logged-in user can list all of their bookmarked articles.
- A user cannot bookmark the same article more than once.
- If the article does not exist, the API should return not found.
- If the user is not authenticated, the API should return unauthorized.
- Deleting an article should remove related bookmarks automatically.

### API requirements
- `POST /api/articles/{slug}/bookmark`
- `GET /api/user/bookmarks`

### Data requirements
- Add a `Bookmark` entity.
- Store `user_id` and `article_id`.
- Add a unique constraint on `user_id + article_id`.
- Add cascade delete when the related article is removed.

### Response requirements
#### Bookmark create
- Return a success response.
- Include the bookmarked article slug.

#### Bookmark list
- Return the current user's bookmarked articles.
- Include article summary fields or slugs.

### Validation rules
- Authentication is required for both endpoints.
- Duplicate bookmarks must not create duplicate rows.
- Missing article slug should return not found.
- Invalid or missing auth should return unauthorized.

### Testing requirements
#### Unit tests
- Test successful bookmark creation.
- Test duplicate bookmark handling.
- Test article-not-found behavior.
- Test listing bookmarks for a user.
- Use mocked `AsyncSession`.

#### API tests
- Test authenticated bookmark creation.
- Test unauthenticated bookmark creation.
- Test duplicate bookmark request.
- Test article-not-found case.
- Test listing user bookmarks.

### Non-goals
- No UI changes are required.
- No admin reporting or moderation flow is required.
- No public bookmark sharing is required.

---

## Feature 2: Reading list

### Business goal
Allow users to save articles into a personal read-later list.

### Functional requirements
- A logged-in user can add an article to their reading list.
- A logged-in user can remove an article from their reading list.
- A logged-in user can list all saved articles.
- Duplicate saves are not allowed.
- Unauthenticated access must be rejected.
- Removing an article from the reading list should not delete the article itself.

### API requirements
- `POST /api/articles/{slug}/reading-list`
- `DELETE /api/articles/{slug}/reading-list`
- `GET /api/user/reading-list`

### Data requirements
- Add a `ReadingListItem` entity.
- Store `user_id`, `article_id`, and `created_at`.
- Add a unique constraint on `user_id + article_id`.

### Response requirements
#### Add to reading list
- Return success response with article slug.

#### Remove from reading list
- Return success response or no-content response.

#### List reading list
- Return all saved articles for the current user.

### Validation rules
- Authentication is required for all endpoints.
- Duplicate saves must not create duplicate rows.
- Removing a missing reading list item should return not found or a clear idempotent response.

### Testing requirements
#### Unit tests
- Test add to reading list.
- Test remove from reading list.
- Test duplicate prevention.
- Test listing reading list items.

#### API tests
- Test authenticated add.
- Test authenticated remove.
- Test unauthenticated access.
- Test duplicate add.
- Test list response.

### Non-goals
- No sharing of reading lists.
- No sorting or tagging of reading list entries.

---

## Feature 3: Article reactions

### Business goal
Allow readers to react to articles with a small set of predefined reactions.

### Functional requirements
- A logged-in user can react to an article.
- Allowed reactions are `like`, `insightful`, and `celebrate`.
- A user can have only one reaction per article.
- A user can change their reaction.
- Article detail or reactions endpoint should return reaction counts by type.
- Unauthenticated users cannot create or update reactions.

### API requirements
- `POST /api/articles/{slug}/reactions`
- `GET /api/articles/{slug}/reactions`

### Data requirements
- Add an `ArticleReaction` entity.
- Store `user_id`, `article_id`, and `reaction_type`.
- Add a unique constraint on `user_id + article_id`.
- Restrict `reaction_type` to allowed values.

### Response requirements
#### Create or update reaction
- Return current reaction and article slug.

#### Get reactions
- Return aggregate counts by reaction type.
- Optionally include current user's reaction when authenticated.

### Validation rules
- Invalid reaction type should return validation error.
- Missing article should return not found.
- Duplicate create should update or reject based on chosen spec behavior.

### Testing requirements
#### Unit tests
- Test create reaction.
- Test update reaction.
- Test invalid reaction type handling.
- Test count aggregation logic.

#### API tests
- Test authenticated reaction create.
- Test reaction update.
- Test unauthenticated request.
- Test invalid reaction type.
- Test reaction counts response.

### Non-goals
- No emoji customization.
- No free-text reactions.
- No reaction notifications.

---

## Feature 4: Report an article

### Business goal
Allow authenticated users to report inappropriate or problematic articles.

### Functional requirements
- A logged-in user can report an article by slug.
- A report must include a reason.
- A user can list their own submitted reports.
- Duplicate reports for the same article by the same user should be prevented.
- Unauthenticated requests must be rejected.

### API requirements
- `POST /api/articles/{slug}/report`
- `GET /api/user/reports`

### Data requirements
- Add an `ArticleReport` entity.
- Store `user_id`, `article_id`, `reason`, and `created_at`.
- Add a unique constraint on `user_id + article_id`.

### Response requirements
#### Create report
- Return confirmation with article slug and report reason.

#### List reports
- Return reports submitted by the current user.

### Validation rules
- Reason is required.
- Empty or whitespace-only reason should be rejected.
- Missing article should return not found.
- Duplicate report should return clear conflict or validation response.

### Testing requirements
#### Unit tests
- Test report creation.
- Test blank reason validation.
- Test duplicate prevention.
- Test listing reports.

#### API tests
- Test authenticated report create.
- Test unauthenticated report create.
- Test blank reason request.
- Test missing article.
- Test list own reports.

### Non-goals
- No admin moderation dashboard.
- No escalation workflow.
- No email notifications.

---

## Feature 5: Article draft autosave

### Business goal
Allow users to save incomplete article drafts before publishing.

### Functional requirements
- A logged-in user can save a draft article.
- A logged-in user can update an existing draft.
- A logged-in user can list their drafts.
- A logged-in user can publish a draft as a full article.
- Drafts should not appear in public article listings.

### API requirements
- `POST /api/drafts`
- `PUT /api/drafts/{draft_id}`
- `GET /api/user/drafts`
- `POST /api/drafts/{draft_id}/publish`

### Data requirements
- Add a `DraftArticle` entity.
- Store `user_id`, `title`, `description`, `body`, `tags`, and timestamps.
- Draft and published article must remain distinct until publish action is completed.

### Response requirements
#### Save draft
- Return draft identifier and saved fields.

#### List drafts
- Return current user's draft articles.

#### Publish draft
- Return the created article slug.

### Validation rules
- Authentication is required for all endpoints.
- Only the owner can update or publish a draft.
- Publishing a missing draft should return not found.
- Publishing should create a real article and optionally remove or mark the draft.

### Testing requirements
#### Unit tests
- Test draft creation.
- Test draft update.
- Test owner-only access.
- Test publish flow.

#### API tests
- Test save draft.
- Test update draft.
- Test list drafts.
- Test publish draft.
- Test unauthorized access.

### Non-goals
- No collaborative editing.
- No draft version history.
- No scheduled publishing.

---

## Feature 6: Followed authors feed filter

### Business goal
Allow users to fetch articles written only by authors they follow.

### Functional requirements
- A logged-in user can fetch a feed of articles by followed authors.
- Feed should support limit and offset.
- Unauthenticated access must be rejected.
- The response should exclude articles by unfollowed authors.

### API requirements
- `GET /api/articles/feed/following`

### Data requirements
- Reuse existing follow relationship if present.
- No new entity required if follow graph already exists.
- Query should efficiently join followed authors and articles.

### Response requirements
- Return paginated article list response.
- Include limit, offset, and article metadata as per existing list format.

### Validation rules
- Authentication is required.
- Invalid limit or offset should return validation error.
- Empty follow graph should return an empty list, not an error.

### Testing requirements
#### Unit tests
- Test feed query with followed authors.
- Test empty result case.
- Test pagination behavior.
- Test exclusion of unfollowed authors.

#### API tests
- Test authenticated feed fetch.
- Test unauthenticated request.
- Test limit and offset behavior.
- Test empty feed case.

### Non-goals
- No ranking algorithm.
- No recommendation engine.
- No sponsored content.

---

## Feature 7: Article view count tracking

### Business goal
Track how many times an article is viewed.

### Functional requirements
- Each successful article fetch should increment a view count.
- Article detail response should include current view count.
- View count should not increment for missing articles.
- Implementation should avoid obvious double-counting mistakes within the same request.

### API requirements
- Reuse existing article detail endpoint.
- No separate create endpoint is required.

### Data requirements
- Add `view_count` field to article or related stats table.
- Default view count should be zero.

### Response requirements
- Existing article detail response should include `view_count`.

### Validation rules
- Missing article still returns not found.
- Counter updates should happen only on successful retrieval.

### Testing requirements
#### Unit tests
- Test increment on successful fetch.
- Test no increment on missing article.
- Test initial default value.

#### API tests
- Test article detail returns view_count.
- Test repeated fetch increases count.
- Test missing article behavior.

### Non-goals
- No unique viewer tracking.
- No analytics dashboard.
- No time-series reporting.

---

## How to use this document

- Choose one feature for tomorrow's spec-driven development demo.
- Convert the selected requirement into a formal technical spec.
- Derive unit and API tests from the spec before implementation.
- Implement the feature in layers and validate against the tests.
