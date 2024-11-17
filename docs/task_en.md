# Backend test task

Expected completion time: 3-5 hours

Below a short description of a problem, along with several basic restrictions and requirements. The task will evaluate your ability to implement a solution using our tech stack, and we expect a well-documented and maintainable codebase that adheres to best practices.

Deliverables
- Repository: copy provided repository template and submit your work as a pull request (in new copied repository) via [this Typeform](https://hiretechfast.typeform.com/to/QZ55qiDi)
- Documentation: Include a brief manual on how to set up the project with your changes. Provide documentation that explains your technical solution and design choices. Including an architectural diagram is optional but recommended.
- Code Quality: Ensure your code is readable, maintainable, and formatted consistently.
- Testing: Implement comprehensive unit tests for all new code. Use pytest with pytest-django for fixtures where appropriate.
- Logs and Tracing: Implement structured logging using structlog (which is already included in the project) and add tracing using Sentry transactions or similar.


## Stack

- Tech Stack
- Languages and Frameworks: Python, Django
- Testing: pytest (pytest-django preferred for fixtures)
- Infrastructure: Docker & docker-compose
- Database: PostgreSQL
- Background Tasks: Celery & Celery Beat
- Logging Database: ClickHouse (included in the Docker environment)

## The Problem
Our application sends event logs, which are later used for business analysis, incident investigations, and security audits. Logs are currently sent directly to ClickHouse in a column-based "One Big Table" (OBT) format:

| Column           | Type     |
|------------------|----------|
| event_type       | String   |
| event_date_time  | DateTime |
| environment      | String   |
| event_context    | JSON     |
| metadata_version | UInt64   |

This approach has caused several problems, such as:
- due to the lack of transactionality logs are missed in case of a web-worker failure before the business-logic step is executed
- Clickhouse network write errors cause poor UX
- Clickhouse struggles with large numbers of small inserts (see https://clickhouse.com/blog/common-getting-started-issues-with-clickhouse#many-small-inserts)
- We need to implement a new write mechanism that will eliminate those problems and provide a convenient interface for publishing logs


## Requirements
- Pattern Choice: Use the transactional outbox pattern or a similar well-documented approach.
Solution Simplicity: Avoid using Kafka or file storage like AWS S3. Stick with a simple and efficient solution that utilizes well-known technologies to keep the setup maintainable and monitorable.
- Batching: Implement batching to minimize the number of inserts to ClickHouse, reducing system strain.
- Avoid Hardcoding: Do not hardcode values like URLs; follow the 12-factor app principles.
- Logging and Error Handling: Integrate with existing structured logging (structlog) to avoid fragmentation.
- Avoid Redundant Patterns: Ensure any design patterns add real value. Avoid patterns that simply wrap the ORM without enhancing readability or abstraction.
- Reusability and Separation of Concerns: Decouple core log-processing logic from Celery tasks to increase reusability.
- Testing: Where possible, test directly against the ClickHouse instance included in Docker. Avoid using mocks unnecessarily.

tips: Teamlead Review [comments from previos hiring](https://docs.google.com/spreadsheets/d/1S_I4E7wtbob8rcmypViGBisWG7w-72e1x0alnPs8Prg/edit?gid=1887153698#gid=1887153698)
