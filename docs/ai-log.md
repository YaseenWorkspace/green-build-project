# AI log

How I used AI on this project, what I learnt, and the mistakes I made along the way.
I'm keeping this honest so anyone reviewing the project can see when, where and how AI helped.

## How AI helped (by session)

| Date | What AI helped with | What I did / decided myself | Kept / changed / rejected |
|---|---|---|---|
| 2026-10-15 | Read the Data School meet & greet slides; suggested 11 project ideas; drafted a project brief in the same format as my ECS project brief | Attended the event; chose idea 6 (Green Builds) myself | Kept the brief structure; changed the default from London region to national GB data after learning electricity is used where the servers are |
| 2026-10-15 | Explained cron schedules; reviewed my LinkedIn follow-up to Harriet Owen | Wrote and sent the LinkedIn message myself | Kept my message as sent |
| 2026-10-17 | Researched the business case (Carbon Aware SDK, GHG Protocol, Google, Xbox, GitLab, TU Berlin paper, VMO2 targets); wrote a business case and a senior review question bank into my brief | Decided not to apply to this cohort; to build to the Information Lab's standard and share it with Harriet and Caolan when finished | Kept the honest limits: time-shifting is a small win, and reported emissions may not change |
| 2026-10-17 | Explained APIs, packages, virtual environments, UTC vs BST, marts, cron lines, MIT licence; drew pipeline diagrams | Asked follow-up questions until I understood | Chose to install packages one step at a time instead of all at once |
| 2026-10-17 | Claude in VS Code created this file and `.gitignore` (commit e90558f) | Created the GitHub repo, wrote its description, created the venv, fixed `.gitignore` myself (commit a6abe20) | Plan: use AI for setup and explanations, write the SQL models myself |
| 2026-10-17 | Explained curl flags, status codes, exit codes, `raise_for_status()` and the request flow | Found `-i`, `-s`, `-o`, `-w`, `-f` in `curl --help all` myself and tested real calls | Chose to find code in the official docs rather than have AI write it |
| 2026-10-18 | Explained PyPI vs pip vs `.venv`, activating the venv, how `pip freeze` works, `requests`' helper packages, and a structure for explaining decisions | Installed `requests`, spotted it had gone into global Python with `pip show`, and wrote `requirements.txt` with `pip freeze` | Kept the decision structure: what I chose → why → the alternative → why not → the trade-off |

## What I've learnt so far

- **Data engineering:** get data in, shape it, serve it (from the Data School event).
- **API vs package vs server:** the API is the service I ask for data (NESO's front door); a package (like `requests`) is a tool I install; the server is the computer running the API.
- **The request flow:** my code sends a request → the API checks it → NESO looks up the data → the API sends back JSON plus a status code → my code checks the status → saves the data untouched.
- **Status codes:** 200 = OK, 400 = I asked wrongly, 500 = the server had a problem (worth retrying).
- **`raise_for_status()`:** my code checks the status code and stops if it's an error, so I never save an error message as if it were data.
- **curl flags:** `-i` shows headers, `-s` hides the progress bar, `-o` saves output to a file instead of the screen, `-w "%{http_code}"` prints the status code, `-f` fails on HTTP errors.
- **Exit code vs status code:** exit code 0 means the command ran; status 200 means the API said OK. They're different numbers.
- **Virtual environments:** a private folder of Python packages for one project. Not like Docker: it doesn't store an image or run anything.
- **`requirements.txt`:** the list of packages and exact versions; `pip freeze` writes it, `pip install -r` rebuilds it. `.venv/` is never committed.
- **UTC vs BST:** the API and GitHub Actions cron use UTC; London uses BST in summer (UTC+1) and GMT in winter. The clocks go back on 25 Oct 2026, so that night has 50 half-hour slots.
- **Cron lines:** five fields (minute, hour, day, month, weekday), always in UTC in GitHub Actions.
- **Pipeline layers:** raw (untouched) → staging (tidy) → intermediate (add meaning) → marts (the answer) → app (show it).
- **Licences:** my code is MIT; the API's data is CC BY 4.0 and must be credited.
- **Costs:** DuckDB is free and runs on my laptop; Snowflake and MotherDuck are paid cloud services I don't need.
- **Electricity is used where the servers are,** not where the team sits, so national GB data is a better default than London's region.
- **Who does what:** PyPI is the online store of packages; pip downloads and installs them; `.venv` is the project's private folder where they're stored.
- **Activating the venv:** `source .venv/Scripts/activate` puts `.venv/Scripts` at the front of the PATH, so `python` and `pip` mean the project's copies. The prompt shows `(.venv)` when it's on.
- **How `pip freeze` works:** it reads the `.dist-info` label of every package in the active Python's `site-packages` folder and prints one `name==version` line each; `>` writes them into `requirements.txt`.
- **`requests`' helpers:** `certifi` (trusted HTTPS certificates), `charset-normalizer` (reads text encoding), `idna` (international web addresses), `urllib3` (the low-level network connection). `pip freeze` pins these too.
- **Explaining a decision:** what I chose → why → the alternative → why not → the trade-off I accept.

## Mistakes I made (and what I learnt)

| Mistake | What I learnt |
|---|---|
| Curled the API docs page and got HTML back | The docs (`carbon-intensity.github.io`) and the API (`api.carbonintensity.org.uk`) are different addresses |
| Mixed up `-o` and `-O` in the curl help | Capital and lowercase flags are different options |
| Thought `-o` sent output to my screen | It does the opposite: it sends output to a file |
| Thought `-o` only saved when the status was wrong | `-o` always saves whatever comes back; `-f` is the flag that stops on errors |
| Mixed up exit codes and status codes | Exit code 0 = the command ran; status 200 = the API said OK |
| Thought `-s` was about proxies | The help list is alphabetical; the proxy options just sit below `-s` |
| Thought DuckDB was expensive | It was Snowflake (and MotherDuck) that cost money; DuckDB is free |
| Thought a venv stores an image and runs my app like Docker | A venv is just a folder of packages; `requirements.txt` is the recipe |
| Thought the API was pre-written code | That's a package; the API is a service on someone else's server |
| Thought I needed to find an application like in the ECS project | In this project I build the pipeline; the idea + API was the "pick an app" step |
| Thought `extract.py` would clean the data | It only fetches and saves; cleaning happens in SQL |
| Thought the API checks the status code | My code checks it with `raise_for_status()` |
| Commit message said `*.duckdb` was ignored to protect secrets | Only `.env` is about secrets; the DuckDB file is ignored because it's rebuilt from raw data and SQL |
| Didn't activate the venv before `pip install requests` | `pip show requests` showed it went into my global Python 3.13. Activating puts `.venv/Scripts` first in the PATH so pip uses the project's Python |
| Thought `.venv` downloads packages | pip downloads them from PyPI; `.venv` is just where they're stored |
| Thought `.venv` belongs in `requirements.txt` | `.venv` goes in `.gitignore`; `requirements.txt` only lists package names and versions |
| Thought installing globally just made things slower | The real problems are version clashes with other tools, a wrong `requirements.txt`, and `ModuleNotFoundError` inside the venv |
| Thought `touch requirements.txt` would provide the packages | It creates an empty file; `pip freeze > requirements.txt` fills it with `name==version` lines |
