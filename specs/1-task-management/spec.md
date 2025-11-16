# Feature Specification: タスク管理アプリ（Task Management）

**Feature Branch**: `1-task-management`  
**Created**: 2025-11-16  
**Status**: Draft  
**Input**: User description: "タスク管理アプリを作成したい。主要機能を以下に示す。①タスクのCRUD②サブタスク③タスクへのタグ付与およびタグの管理④各種ダッシュボード（期限切れのタスク/期限が近いタスク/すべてのタスク）"

## Clarifications

### Session 2025-11-16
- Q: サブタスクは `Task` に `parent_task_id` を持たせる形で表現しますか、それとも別エンティティにしますか？ → A: `A`（`Task.parent_task_id` を用いる）

## User Scenarios & Testing *(mandatory)*

### User Story 1 - タスクの基本操作（Priority: P1）

ユーザーはタスクを作成、参照、更新、削除（CRUD）できる。各タスクはタイトル、説明、期限、優先度、状態（未着手/進行中/完了）を持つ。

**Why this priority**: 基本的なタスク管理機能はアプリケーションの核であり、最優先で提供する必要があるため。

**Independent Test**: 新規タスクを追加 → タスク一覧に表示 → タスク編集でフィールドを更新 → タスクが更新される → タスク削除で一覧から消える。

**Acceptance Scenarios**:
1. **Given** 空のタスクリスト、 **When** ユーザーが必須フィールドでタスクを作成、 **Then** 作成したタスクが一覧に表示される。
2. **Given** 既存タスク、 **When** ユーザーがタスクを編集して保存、 **Then** 表示内容が更新される。
3. **Given** 既存タスク、 **When** ユーザーがタスクを削除、 **Then** タスクは一覧から削除される。

---

### User Story 2 - サブタスク（Priority: P1）

ユーザーはタスクに対して複数のサブタスクを追加できる。サブタスクは独立して完了状態を持ち、親タスクの進捗に反映される（例: サブタスク全完了で親を完了にするかどうかは実装ポリシーとして選択可能）。

**Why this priority**: 複雑な作業を分割して管理するための重要機能である。

**Independent Test**: 親タスクを作成 → サブタスクを追加 → サブタスクの状態を切替 → 親タスクの進捗/表示が適切に反映される。

**Acceptance Scenarios**:
1. **Given** 親タスクが存在、 **When** サブタスクを追加、 **Then** サブタスクが親タスクの下に表示される。
2. **Given** 複数のサブタスク、 **When** すべてのサブタスクが完了、 **Then** 親タスクに完了フラグ（または視覚的な完了表示）が付与される（挙動はAssumptions参照）。

---

### User Story 3 - タグとタグ管理（Priority: P2）

ユーザーはタスクに複数のタグを付与できる。タグは一覧で管理（作成・編集・削除）でき、タグでタスクをフィルタできる。

**Why this priority**: タスクの分類とフィルタリングでユーザーの生産性を向上させるため。

**Independent Test**: タグを作成 → タスクにタグを追加 → タグで一覧をフィルタ → フィルタ結果が期待通りになる。

**Acceptance Scenarios**:
1. **Given** タグが未作成、 **When** 新規タグを作成、 **Then** タグ一覧に追加される。
2. **Given** タスクにタグを追加、 **When** タグでフィルタ、 **Then** 当該タグを持つタスクのみ表示される。

---

### User Story 4 - ダッシュボード（Priority: P2）

ユーザーは以下のダッシュボードビューを参照できる: 期限切れのタスク、期限が近いタスク（例: 7日以内）、すべてのタスク。

**Why this priority**: タスクの優先度付けと管理のために視認性が重要である。

**Independent Test**: 複数タスクを作成（期限を設定）→ 各ダッシュボードを表示 → 表示に含まれるタスクが条件に合致する。

**Acceptance Scenarios**:
1. **Given** 期限が過ぎたタスクが存在、 **When** 「期限切れ」ビューを表示、 **Then** 期限切れタスクのみ表示される。
2. **Given** 期限が近いタスクが存在、 **When** 「期限が近い」ビューを表示、 **Then** 期間内のタスクのみ表示される。

---

### Edge Cases

- 期限のないタスクの扱い（一覧のどこに表示するか）。
- サブタスク大量追加時の表示・並び順。
- 同名タグの重複管理（同名を禁止するか、内部IDで区別するか）。

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: システムはユーザーがタスクを作成、参照、更新、削除（CRUD）できること。
- **FR-002**: タスクはタイトル（必須）、説明（任意）、期限（任意）、優先度（任意）、状態（列挙値）を持つこと。
- **FR-003**: システムはタスクに対して複数のサブタスクを紐づけられること。サブタスクは独立して状態を持つ。
- **FR-004**: システムはタグを管理（作成・編集・削除）でき、タスクに複数タグを付与できること。
- **FR-005**: システムはタグによるフィルタリング機能を提供すること。
- **FR-006**: システムは期限切れ、期限が近い、すべてのタスクを表示するダッシュボードを提供すること。
- **FR-007**: すべての主要機能は独立してテスト可能であること（受入基準を満たすエンドツーエンドのシナリオを持つ）。

## Key Entities *(include if feature involves data)*

- **Task**: id, title, description, due_date, priority, status, tags[], parent_task_id?（親タスクの参照）
	- 備考: 本仕様ではサブタスクは別テーブルの独立エンティティではなく、`Task.parent_task_id` による階層で表現する（Clarification: Session 2025-11-16）。
- **Tag**: id, name, color?（表示用）

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: P1 フロー（タスクCRUD + サブタスク基本操作）が手動テストで再現可能で、主要シナリオの受入テストが100%合格すること。
- **SC-002**: ダッシュボード（期限切れ/期限が近い/すべて）が手動テストで検証可能で、表示の条件一致率が100%であること。
- **SC-003**: タグによるフィルタで期待するタスクが90%以上正しく返ること（データ前提に依存するため、明確な受入テストを定義すること）。
- **SC-004**: ユーザーが主要なタスク操作を3分以内に完了できる（UI 操作性の目標、実測による検証）。

## Assumptions

- 単一テナントのタスク管理（最初は認証・多ユーザーの詳細は省略）。
- データの永続化は存在するものとするが、実装手段は本仕様では記述しない。
- 親タスクの「自動完了」ポリシーはチームで決める（デフォルトでは自動完了は行わない。要望があればオプションで提供）。

## Dependencies

- UI/UX の要件確定（表示/操作の詳細が未指定）
- 要件に応じたデータ永続化・バックエンド設計

## Notes

- 重大な未決点: マルチユーザー/共有タスク、通知やリマインダー機能は本フェーズに含めていない。必要なら別機能として追加する。

## Detailed Design

### Data Model (suggested)

Entities and example fields (types are suggested):

- Task
  - `id` (UUID)
  - `title` (string, required)
  - `description` (string, optional)
  - `due_date` (datetime, optional)
  - `priority` (enum: low/medium/high, default: medium)
  - `status` (enum: todo/in_progress/done, default: todo)
  - `parent_task_id` (UUID, nullable) — for hierarchical tasks (task/subtask relationship represented here)
  - `created_at` (datetime)
  - `updated_at` (datetime)
  - `version` (integer) — optimistic locking

- Tag
  - `id` (UUID)
  - `name` (string, unique)
  - `color` (string, optional)

- TaskTag (join table)
  - `task_id` (UUID)
  - `tag_id` (UUID)

Notes:
- Subtask は別テーブルではなく `Task.parent_task_id` により表現する（Clarification: Session 2025-11-16）。これによりタグやフィルタが共通で扱いやすくなる。
- Use a relational schema (e.g., PostgreSQL) for strong consistency and queryability. Index `due_date`, `status`, `parent_task_id` and `tag_id` for dashboards and filters.
- Use `version` or `updated_at` for optimistic concurrency control to prevent lost updates.


### API / Endpoints (REST-style, can be adapted to GraphQL)

- Tasks
  - `GET /api/tasks` — list tasks; support query params: `?status=&tag=&due_before=&due_after=&limit=&offset=&sort=`
  - `POST /api/tasks` — create task (body: title, description, due_date, priority, parent_task_id)
  - `GET /api/tasks/{id}` — retrieve
  - `PUT /api/tasks/{id}` — update (support partial updates via PATCH if desired)
  - `DELETE /api/tasks/{id}` — delete

  - Notes on subtasks: サブタスクは `Task.parent_task_id` により表現する（Clarification: Session 2025-11-16）。サブタスクを作成するには `POST /api/tasks` に `parent_task_id` を含めて作成することを推奨する。
  - 互換性のため、`POST /api/tasks/{task_id}/subtasks` をエイリアス的に実装してもよい（内部的には `POST /api/tasks` に `parent_task_id=task_id` を付与する処理とする）。

- Tags
  - `GET /api/tags`
  - `POST /api/tags` — create tag (enforce unique `name`)
  - `PUT /api/tags/{id}` — update
  - `DELETE /api/tags/{id}` — delete (consider cascade / orphan handling)

- Dashboards / Filters
  - `GET /api/dashboard/overdue` — 期限切れタスク
  - `GET /api/dashboard/due-soon?days=7` — 期限が近いタスク
  - `GET /api/dashboard/all` — すべてのタスク（ページネーション対応）

Response conventions:
- Use standard HTTP status codes (200, 201, 204, 400, 404, 409, 500).
- On validation errors return 400 with a structured body: `{ "errors": [{ "field": "title", "message": "required" }, ...] }`.

### Business Rules / Domain Decisions

- Subtask → Parent interaction
	- デフォルトポリシー: サブタスクをすべて完了しても親タスクの `status` は自動で `done` にしない（自動化はオプション）。UI上で「サブタスク完了時に親を自動完了する」切替を提供することは可能。
- Tag uniqueness
	- タグ名はグローバルでユニーク（同名のタグを禁止）。代替案としては同名許可＋内部IDで区別だが、運用上の混乱を避けるためユニークを推奨。
- Deletion semantics
	- タスク削除時: サブタスクはデフォルトで一括削除（soft-delete 推奨）。タグは削除しない（タグは多くのタスクで共有されるため）。
- Concurrency
	- クライアントは `version` または `updated_at` を持ち、更新時にチェックして競合を検出（409 Conflict）。競合解決はクライアントに任せ、必要ならマージUIを提供。

### Validation rules

- `title`: 非空、最大長 255
- `due_date`: 将来日または許容する過去日（期限切れの追跡のため過去日を許容）
- `priority`: 事前定義の列挙値のみ

### Pagination / Sorting

- Use `limit`/`offset` or keyset pagination for large lists. Default `limit=50`, `max_limit=200`.
- Default sort: `due_date ASC, priority DESC` for dashboards where applicable.

### Error & Edge Handling

- Handle absent `due_date`: show in 'no due date' bucket or at end of sorted lists.
- For large number of subtasks, paginate or collapse in UI; API supports `GET /api/tasks/{id}/subtasks?limit=&offset=`.

### Acceptance Test Specifications (concrete)

P1: タスクCRUD

1. 新規作成
	- Pre: empty list
	- Action: `POST /api/tasks` with `{ "title": "買い物", "due_date": "2025-11-20T12:00:00Z" }`
	- Expect: 201 Created, response contains `id`, `title`, `due_date`.
	- Follow-up: `GET /api/tasks` returns created task.

2. 更新
	- Pre: existing task
	- Action: `PUT /api/tasks/{id}` with updated `title` and `priority`
	- Expect: 200 OK, fields changed. `updated_at` advanced.

3. 削除
	- Action: `DELETE /api/tasks/{id}`
	- Expect: 204 No Content, subsequent `GET /api/tasks/{id}` returns 404.

P1: サブタスク

1. 追加
	- Action: `POST /api/tasks/{task_id}/subtasks` with `{ "title": "買い物: 卵" }`
	- Expect: 201 Created, subtask returned and visible via `GET /api/tasks/{task_id}/subtasks`.

2. サブタスク完了の影響
	- Given: 親に2つのサブタスク
	- When: 両方完了にする
	- Then: 親タスクは自動で `done` にならない（デフォルト）。UIで自動完了が有効な場合はそれを検証する別シナリオを用意。

P2: タグとダッシュボード

1. タグ作成とフィルタ
	- Action: `POST /api/tags` と `POST /api/tasks/{id}` にタグを指定
	- Expect: `GET /api/tasks?tag=urgent` により該当タスクのみ返る。

2. ダッシュボード
	- Create tasks with due dates: one past, one within 7 days, others later
	- `GET /api/dashboard/overdue` returns only past-due tasks
	- `GET /api/dashboard/due-soon?days=7` returns tasks within 7 days

### Non-Functional Requirements

- Performance: 平常時 p95 レイテンシ < 200ms for `GET /api/tasks` with typical page sizes (limit<=50).
- Scale: 単一インスタンスで1万件のタスクを扱えることを目安に設計。将来的にシャーディング/分割を検討。
- Availability: 計画フェーズでは高可用構成はオプション（要件に応じて設計）。
- Observability: 主要 API のカウント、レイテンシ、エラー率をメトリクスとして収集。リクエスト/エラーは構造化ログに残す。
- Security: 本フェーズは単一テナント仮定。将来的に認証（OAuth2/JWT 等）を追加する。機密情報は環境変数やシークレットストアに保存。

### Implementation Notes (modularity / loose coupling)

- アーキテクチャ: プレゼンテーション (UI)・API 層・サービス層・データ層を明確に分離することで疎結合を確保する。
- インターフェース: サービス間は明確なインターフェース（契約）を用い、直接内部実装に依存しない（依存注入を活用）。
- テスト: 単体テスト（サービス単位）と契約テスト（API 契約）を充実させ、統合テストでエンドツーエンドシナリオを検証する。

### Next steps

- 確認: 上記の詳細で問題ないか確認してください（特にサブタスク→親タスク動作、タグの一意性、削除ポリシー）。
- 承認後: `/speckit.plan` を実行して実装タスクに分解します。


