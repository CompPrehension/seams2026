# SEAMS 2026 Artifact

This repository provides Docker Compose artifacts to reproduce experiments from the associated SEAMS 2026 paper.

## Artifact Structure

The setup launches three containers:

- **database**: MySQL 9.3.0 instance with pre-populated problem bank from `dump.sql.gz`.
- **generator**: Problem-generation application that monitors the generation requests queue and generates problems when needed.
- **its**: Application that emulates student interactions with the tutoring system to load-test the problem bank.

By default the `its`application starts a debug exercise emulation with 10 users (25 questions per exercise, ~30s solve time per question, with randomization). You can customize run settings using the .env file (see configuration section below).

All meaningful logs are written to the `./logs` folder. 


## Prerequisites

- Linux or Windows with WSL2.
- Docker with Compose plugin.
- GitHub Personal Access Token with repository contents read permissions.

## GitHub Token Generation

1. Log in to your GitHub account.
2. Go to `Settings` > `Developer settings` > `Personal access tokens` > `Tokens (classic)`.
3. Click `Generate new token (classic)`.
4. Set an expiration date (scopes optional).
5. Copy the generated token.

## Quick Start

1. Clone the repository:

   ```
   git clone --recurse-submodules https://github.com/CompPrehension/seams2026.git
   ```

2. Change to the repository directory:

   ```
   cd seams2026
   ```

3. Create `.env` file from `.env.example`
    
   On Linux or WSL2:
   ```
   cp .env.example .env
   ```
   On native Windows (CMD):
   ```
   copy .env.example .env
   ```

4. Edit `.env` with a text editor:

   Set the required GITHUB_API_TOKEN to your generated token value, e.g.:

   ```
   GITHUB_API_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
   ```

   Other settings use prepared defaults but can be adjusted for tutor emulation:

   | Variable | Description | Default |
   | :-- | :-- | :-- |
   | `TUTOR_RUN_SCHEDULE` | Cron schedule or "once" for single simulation | once |
   | `TUTOR_INITIAL_DELAY` | Initial delay before simulation start (seconds) | 0 |
   | `TUTOR_N_LEFT` | Generator threshold (min top-rated problems to trigger generation) | 42 |
   | `TUTOR_N_GEN` | Number of problems to generate per generation request | 3 |
   | `TUTOR_SKIP_DELAY_FOR_PROBLEMS_WITHOUT_GENERATION` | Skip delays for exercise problems not requiring generation | false |
   | `TUTOR_USERS_COUNT` | Number of simultaneous users | 7 |
   | `TUTOR_EXERCISE_START_DELAY_MIN` | Min random delay before exercise start (seconds) | 0 |
   | `TUTOR_EXERCISE_START_DELAY_MAX` | Max random delay before exercise start (seconds) | 30 |
   | `TUTOR_POST_PROBLEM_DELAY_MIN` | Min post-problem delay (seconds) | 3 |
   | `TUTOR_POST_PROBLEM_DELAY_MAX` | Max post-problem delay (seconds) | 10 |

   Other settings (do not change unless needed):

   | Variable | Default |
   | :-- | :-- |
   | `DB_NAME` | compprehension |
   | `DB_USER` | user |
   | `DB_PASSWORD` | p@ssw0rd |
   | `DB_PORT` | 3306 |
   | `LOG_FILE_TEMPLATE` | full_log.log |
   | `RANDOM_SEED` |  |

4. Start the services:

   ```
   docker compose up
   ```

   The first run builds images and initializes the database, which takes several minutes. All containers data persists in `./containers_data`. All meaningful logs writes to `./logs` directory.
   To stop the services, run `Ctrl+C`.
   Delete contents from  `./containers_data` and `./logs` for a clean rerun if needed.
