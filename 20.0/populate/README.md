# populate migration guide (19.0 -> 20.0)

## What's new for users

The 20.0 release notes extract provided for this migration covers **localizations only** (accounting, payroll and point-of-sale changes per country). **Nothing in it concerns the `populate` addon**, so there is no new business screen, report or setting to announce to functional users.

`populate` is a developer/QA tool: it creates realistic, high-volume data so performance work can be benchmarked on databases that look like production, instead of small, skewed demo data. What actually changed in 20.0 is a full rewrite: the populate framework available since 18.0 — which copied existing records and produced skewed distributions — is replaced by a new `populate` addon driven by declarative **Blueprints**. Everything below describes that rewrite.

## Technical data model changes

Four new models now carry the feature (it used to be script-based, with hooks on `BaseModel` and no dedicated data model):

- **populate.blueprint** — declarative definition of what to create.
  - `name` (required), `inherit_id` (to `populate.blueprint`, ondelete `set null`), `definition_xml` (Char), `definition_json` (Json), `definition` (computed Json, read-only; XML takes precedence over JSON, and inheritance specs are applied).
  - SQL constraint: an XML or a JSON definition must be provided. Validation rejects inheritance cycles, unknown models and unknown non-virtual fields.
- **populate.session** — one execution run of a blueprint.
  - `seed` (random by default), `scaling_factor`, `worker_count` (default 1), `blueprint_id` (required), `job_ids` (One2many to `populate.job`, restricted to root jobs).
  - A session must have jobs; `progress`, `is_done`, `is_parallel` and `is_running` are computed properties.
- **populate.job** — one unit of work, automatically split into subjobs.
  - `seed`, `parent_id` / `child_ids` / `parent_path`, `blueprint_id` (stored related, indexed), `session_id` (required), `is_done`, `ref`, `model_name` (required), `record_count` (default 1), `type` (`create` / `write`), `parallel` (default True), `context`, `instructions`.
  - Constraints: strictly positive `record_count` (write jobs excepted), a single hierarchy level, same session across a hierarchy, no cycles, and unique `ref` per session for root create jobs.
- **populate.model.data** — external reference to every generated record: `res_model`, `res_id` (`Many2oneReference`), `job_id` (cascade), related `ref` / `session_id` / `blueprint_id`, unique `(job_id, res_id)` index, no audit log.

Behaviour deltas worth noting: jobs above 1000 records are split into subjobs with derived, unique seeds; each job commits on completion; write jobs target records through their `ref` instead of re-scanning the table.

## How your habits should change

- Custom Python generation scripts and model hooks are gone: describe *what* to create in an XML or JSON blueprint and let the framework decide *how*, including parallelism.
- Scope a blueprint to the models you actually benchmark — unrelated models are no longer populated automatically.
- Use `inherit_id` (XPath specs) to extend an existing blueprint instead of duplicating it; adding or removing a field is typically a one-line XML change.
- Give jobs a `ref` and rely on `populate.model.data` to link and later retrieve generated records.
- Expect parallel execution (`-j <N>` or `-j auto`) and resumable runs (`--resume`) rather than one long single-core script.
- For functional users, day-to-day work is unchanged; only the way test databases are produced changes.

## What you gain by migrating

- **Credible benchmarks:** statistical distributions (normal, uniform, exponential, beta, poisson, triangular) and rich generators produce plausible data instead of uniformly random or copied noise.
- **Speed:** work is parallelised transparently across workers, with frequent commits keeping memory under control.
- **Reproducibility:** seeds make a run replayable, and `--resume` picks up an interrupted session instead of restarting it.
- **Traceability:** every generated record is referenced in `populate.model.data`, so it can be linked across blueprints and cleaned up afterwards.
- **Lower maintenance:** blueprints are short XML/JSON files, quick to bootstrap, extend and review — reviewers can reproduce a realistic test database with a single command.
