# populate migration guide (19.0 -> 20.0)

`populate` is a development/testing addon that generates synthetic data. In 20.0 it is a brand-new addon: the diff reports 49 added method signatures, 0 modified and 0 removed, so no pre-existing code object of this addon changed. In 19.0 the equivalent "populate" logic was not an addon at all — it was the tool merged in v18 that copied records from an initial dataset.

## What's new for users

No section of the official Odoo release notes matched this addon, so everything below comes from the module's own patches and commit messages. Nothing described here is Enterprise-only.

- **Blueprints (XML or JSON)** declare what to generate: models, record counts, and per-field generators. Plain data instead of Python scripts.
- **Targeted population**: a blueprint only fills the models you are benchmarking, instead of walking the whole dependency graph.
- **Parallel generation** through the standard `-j <N>` / `-j auto` options. No parallel API to learn.
- **Realistic figures**, not uniform noise: scalar, textual, temporal, choice, relational, reference, faker, misc and properties generators, with normal, uniform, exponential, beta, poisson and triangular distributions.
- **Virtual fields** for intermediate computations, **write jobs** to update records already created, **blueprint inheritance** (XPath) and XML `<import>` to compose shared fragments, and **function jobs** to call your own code.
- **Traceability**: generated records carry external references (`ref`), usable across blueprints, later writes, or cleanup.
- **Sessions** track the run, allow `--resume` after an interruption, and offer a runtime profiling option.
- The stated goal: reproduce a realistic test database with a single command.

## Technical data model changes

New models (all new in 20.0):
- `populate.blueprint` — `name`, `inherit_id`, `definition_xml`, `definition_json`, computed JSON `definition`; SQL check requiring XML *or* JSON; validation of models, fields and inheritance cycles.
- `populate.job` — `seed`, `parent_id`/`child_ids`/`parent_path`, `blueprint_id`, `session_id`, `is_done`, `ref`, `model_name`, `record_count`, `type` (`create`/`write`, default `create`), `parallel`, `context`, `instructions`; record-count invariant, one `ref` per session for create jobs, dedicated index.
- `populate.session` — `seed`, `scaling_factor`, `worker_count`, `blueprint_id`, `job_ids`; a session must own at least one job.
- `populate.model.data` — `res_model`, `res_id`, `job_id`, `ref`, `session_id`, `blueprint_id`; links each generated record back to its job.

Method signatures: 49 added, 0 modified, 0 removed. New executors `JobExecutor`, `SequentialExecutor` and `ParallelExecutor` (context-manager based); job-level `_execute`, `_execute_create`, `_execute_write`, `_execute_function`, `_create_subjobs`, `progress`, `is_executable`; session-level `_instantiate`, `is_done`, `is_parallel`, `is_running`, `is_profiling`, `progress`; blueprint `_compute_definition`, `_check_definition`, `_register_hook`.

Later 20.0 changes refine the same models: typed blueprint operation blocks, `<import>` composition, `function` jobs, domains on write jobs, and runtime profiling.

## How your habits should change

- **Stop writing populate scripts.** Express the data in a blueprint; adding or removing a field is usually a one-line XML edit.
- **Install `populate`** and run a session, instead of relying on demo data or on copies of an existing dataset (which yield skewed distributions).
- **Resume rather than restart**: an interrupted session re-executes only its pending jobs.
- **Scale with `-j`**; note that a parallel session is refused during automated tests, and can be disabled platform-wide via `ODOO_POPULATE_MULTIPROCESS_ENABLE`.
- **Use `ref` in blueprints** to write back to previously created records, and to clean them up afterwards.
- **One session runs at a time** — a database-level lock prevents starting a session that is already running.

## What you gain by migrating

Reproducible, realistically distributed test databases that a reviewer can rebuild with a single command; much faster generation thanks to transparent parallelism; deterministic, resumable runs driven by seeds; and far lower maintenance, because scenarios are declared as data instead of coded in Python.
