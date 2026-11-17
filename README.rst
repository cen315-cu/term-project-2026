CEN315 Term Project 2026: Testing the Bistro Order Service
==========================================================

CEN315 Introduction to Test Engineering, Çukurova University, Fall 2026.
Instructor: Dr. Yunus Emre Çoğurcu.

**AI tools are not allowed in this project.** Read the section "Academic integrity" before you start.


The story
---------

Bistro is a restaurant chain that is about to replace its paper order slips with tablets. Waiters will open a table, add dishes, print the bill and take card payments through a new back end, the Bistro Order Service. An outside vendor wrote the service and delivered it as a Docker image, without the source code, together with a contract that lists the business rules it must follow.

The chain does not want to rely on the vendor's own testing. Before the tablets go into the restaurants it has hired your group as an independent test team, and your results decide whether the chain accepts the delivery or sends it back. The managers want four things from you:

1. Does the service follow the contract? For every rule it breaks, they want an automated test that shows the failure and a bug report the vendor can act on. This is the work of R1 and R2.
2. Will it survive a Saturday evening with every table full? They want to know at what load it becomes too slow for the waiters, which request slows down as the day's orders pile up, and whether it runs out of memory over a long shift. This is R4.
3. Is it safe, and what happens when something around it fails? Can one waiter read another waiter's orders, and what happens to a customer's payment when the card provider is slow or down? This is R5, who also keeps the team's CI running.
4. The chain writes two small pieces itself: a bill splitter for tables that pay separately, and the client that talks to the card provider. Your team builds them with tests written first, and the chain will run its own acceptance tests on them before it uses them. This is R3.

Every group gets its own copy of the vendor's delivery. The numbers in the contract (table limits, discount threshold, happy hour, kitchen capacity and so on) differ from group to group, and so do the faults in the delivery. Your variant card is your copy of the contract, and another group's results will not match yours.


Key dates
---------

All times are Turkey time (UTC+3).

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Date
     - What happens
   * - Now
     - The sign-up form on the course page is open and the service images are published. A group with a complete and correct entry gets its private repository, the invitations and its variant card within about half an hour, so it can start right away (see "Signing up"). The repository is named after the group id, for example ``group-1-07``, and the card is the issue "Your variant card" in it.
   * - Friday 20 November, 17:00
     - Group sign-up closes. A student who is not in a group by then counts as not having taken the project.
   * - Sunday 29 November, 23:59
     - Checkpoint 1. Roles are fixed from this point on.
   * - Sunday 6 December, 23:59
     - Checkpoint 2
   * - Saturday 12 December, 23:59
     - Repositories close. Pushes after this time are not accepted and the repositories become read-only.
   * - Monday 14 December, 10:00
     - Written exam on your own repository, both sections at the same time
   * - Thursday 17 December
     - Oral exam, only for students who are called (see "Exam")


First steps
-----------

1. Form a group of 4 or 5 students from your section. One member fills in the sign-up form on the course page, and everybody checks their own student number and GitHub username in it. The sooner the entry is correct, the sooner your repository arrives; the last moment is Friday 20 November, 17:00.
2. Install Git, Python 3.12 (the version the CI uses) and Docker. On Windows and macOS this is Docker Desktop; on Windows it needs WSL 2. Turn off AI completion in your editor.
3. Accept the invitation to your group repository within 48 hours. GitHub sends it to the e-mail address of your account, and you can also see it at https://github.com/cen315-cu.
4. Clone the repository and make sure your commits are linked to your account (see "From group score to your score")::

       git clone https://github.com/cen315-cu/group-1-07.git
       cd group-1-07
       git config user.email "the e-mail address of your GitHub account"

5. Agree on the roles. Each member commits their own row of ``docs/roles.csv`` (see "Choosing roles").
6. Open the issue "Your variant card" in your repository. Copy ``.env.example`` to ``.env``, fill in the values from the card, start the service (see "Starting the service") and run ``pytest tests/blackbox/test_health.py``.
7. Read the business rules together and start the test plan. R3 can start the tests of the own module, R1 the decision table for rule 5, R2 the list of parameters for pairwise testing, R4 can install k6 or Locust and R5 OWASP ZAP.
8. Checkpoint 1 is on Sunday 29 November, 23:59: every member's row in ``docs/roles.csv``, ``docs/test-plan.pdf``, and at least one passing test pushed by every member.

Everyone pushes to the same ``main`` branch, so run ``git pull --rebase`` before ``git push`` to avoid overwriting each other's work. Force pushes to ``main`` are blocked. Only the state of ``main`` at 23:59 on 12 December is graded; other branches are ignored.


Groups and roles
----------------

Groups have 4 or 5 members from the same section. Each member owns one role.

Signing up
~~~~~~~~~~

One member of each group fills in the sign-up form on the course page by Friday 20 November, 17:00. The form asks for the section and, for each member, the student number and the GitHub username, written as ``2023555012 ayse-yilmaz``. The student number must be on the class list of your section, and the GitHub username must belong to an existing account that you use.

The answers are checked every 15 minutes. As soon as an entry has at least four members whose student number and GitHub username are correct, the group gets its repository and every member gets an invitation by e-mail. Accept it within 48 hours. The GitHub username must belong to an existing account; a line with a username that GitHub does not know is not accepted.

The form also asks for the e-mail address of the member who sends it. That address gets a copy of the entry with its edit link, and an e-mail listing any line that was not accepted and why. Correct the line with the edit link; the next check, within 15 minutes, takes the correction. This also works for a member who is already in a group but was entered with a username that has no GitHub account. If a correct entry has brought no invitation after a few hours, check the entry with the edit link, and then ask the instructor.

Once a group exists:

- A member added to the entry later, with the edit link, is invited to the same repository, up to five members.
- A student who is already in a group stays in it. Another entry that lists them is ignored for that student.
- Removing a member or moving to another group goes through the instructor.

List only students who have agreed to be in your group, with their own GitHub accounts. Writing in another student's number, or a GitHub account that is not theirs, is academic misconduct. If your number was used without your consent, write to the instructor at once: you are moved to the right group and the entry is handled under the misconduct rules. At the written exam every student's identity is checked against the group list.

Each student is responsible for their own entry, so check it with the member who sends the form. A line with a student number that is not on the class list of the section, an incomplete line or a wrong username is ignored. A student who is not in a group when the sign-up closes on Friday 20 November, 17:00, counts as not having taken the project. Entries are not accepted after the deadline.

.. list-table::
   :header-rows: 1
   :widths: 8 37 55

   * - Role
     - Your job in the test team
     - Course topics
   * - R1
     - Functional tester. Checks every business rule from the outside with black-box tests and writes the decision table for the bill.
     - equivalence partitioning, boundary values, decision tables, state transitions
   * - R2
     - Test designer. Finds the inputs and combinations nobody wrote by hand, with properties that must always hold and a pairwise set.
     - Hypothesis, pairwise testing
   * - R3
     - Developer. Builds the chain's own bill splitter and payment client with test-driven development.
     - unit testing, TDD, mocking, coverage, mutation testing
   * - R4
     - Performance engineer. Answers the Saturday-evening question: capacity, slow requests and memory over a long run.
     - load, spike and soak tests, percentiles, Little's Law, capacity
   * - R5
     - Security and reliability engineer. Looks for ways to misuse the service, breaks the card provider on purpose to see what the service does, and keeps the CI working.
     - threat modelling, OWASP ZAP, chaos experiments, CI pipeline

Everyone writes code. Your role decides which part of the repository you are responsible for, and the written exam asks you most about that part. It also asks about one part that a teammate owns, so read each other's work.

Choosing roles
~~~~~~~~~~~~~~

The group decides who takes which role. Each member then fills in their own row of ``docs/roles.csv`` with their GitHub username and commits it from their own account, at the latest by checkpoint 1 (Sunday 29 November, 23:59). A row committed from someone else's account does not count.

::

    role,github_username,also_owns
    R1,ayse-yilmaz,
    R2,mehmet-demir,

The roles in ``docs/roles.csv`` at checkpoint 1 are final. The grading scripts and the exam use them, so a group cannot move roles around at the end of the term. A change after checkpoint 1 is possible only when a member leaves the course, and only with the instructor's approval. If a group has not filled in the file by checkpoint 1, the instructor gives out the roles in alphabetical order of GitHub usernames, R1 first.

In a group of four, R5 has no owner of its own. R1 and R4 share its work, and the ``also_owns`` column of their two rows says which part each of them takes, for example ``threat model and ZAP`` and ``chaos and CI``. If a member leaves the course, tell the instructor. A group that goes from five to four members shares the roles as described above, and a group that drops below four is reorganised by the instructor.

The own module of R3 is the same for every group: the bill splitter and the payment client. What differs between groups are the limits and rules printed on the variant card.


The system under test
---------------------

The Bistro Order Service is a REST API that waiters use to open orders for tables, add dishes, print the bill and take payment. It runs in Docker together with a mock payment provider.

Money is always an integer number of kuruş (1 TL = 100 kuruş). Every request except ``GET /menu`` and ``GET /health`` needs an ``X-Waiter-Key`` header. Your variant card gives you two waiter keys.

Endpoints
~~~~~~~~~

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - Method and path
     - Purpose
   * - ``GET /health``
     - Liveness check
   * - ``GET /menu``
     - Menu items: id, name, category (starter, main, dessert, drink), price, stock (null means unlimited)
   * - ``POST /orders``
     - Open an order: ``{"table": t, "guests": g}``
   * - ``GET /orders/{id}``
     - One order, visible only to the waiter who opened it
   * - ``GET /orders?status=OPEN``
     - The calling waiter's orders, filtered by status
   * - ``POST /orders/{id}/items``
     - Add a dish: ``{"item_id": i, "qty": q}``
   * - ``DELETE /orders/{id}/items/{item_id}``
     - Remove a dish
   * - ``GET /orders/{id}/bill``
     - Subtotal, discounts, service charge and total
   * - ``POST /orders/{id}/pay``
     - Pay: ``{"method": "cash" or "card", "idempotency_key": k}``
   * - ``POST /orders/{id}/cancel``
     - Cancel the order

The optional header ``X-Clock: HH:MM`` sets the time of day that the service uses for the happy hour rule. Use it to test time-dependent behaviour without waiting. Without the header the service uses the current time in Turkey.

Responses
~~~~~~~~~

Every response body is JSON, and an error looks like ``{"error": "the order is not open"}``. The main bodies are:

- an order: ``{"id", "table", "guests", "status", "lines", "payment"}``, where each line is ``{"item_id", "name", "category", "qty", "price", "line_total"}`` and ``payment`` is null until the order is paid;
- the bill: ``{"subtotal", "happy_hour_discount", "order_discount", "service_charge", "total"}``, all in kuruş, with total = subtotal − happy_hour_discount − order_discount + service_charge;
- a payment: ``{"id", "status", "method", "payment_id", "amount"}``, where ``payment_id`` comes from the payment provider and is null for cash;
- lists: ``{"items": [...]}`` from ``GET /menu`` and ``{"orders": [...]}`` from ``GET /orders``.

.. list-table::
   :header-rows: 1
   :widths: 12 88

   * - Status
     - When the correct service returns it
   * - 200
     - The request was carried out
   * - 201
     - ``POST /orders`` created an order
   * - 400
     - Invalid input: a missing field or a value of the wrong type, ``table``, ``guests`` or ``qty`` outside its range in a single request, an unknown payment method, a card payment without ``idempotency_key``, a malformed ``X-Clock`` header, an unknown ``status`` filter, a body that is not a JSON object
   * - 401
     - ``X-Waiter-Key`` is missing or unknown
   * - 404
     - Unknown order (an order opened by the other waiter counts as unknown), unknown dish, a dish that is not in the order, unknown path
   * - 405
     - The path exists but not with this method
   * - 409
     - The order is not OPEN; the line would go above Q_MAX; the order would have more than L_MAX different dishes; not enough stock; a card payment of the order is in progress
   * - 429
     - The kitchen is full (rule 6)
   * - 503
     - The payment provider did not confirm the payment (rule 7)

Business rules
~~~~~~~~~~~~~~

The names in capitals are parameters. Their values for your group are printed on your variant card, and the card is part of this specification.

1. An order is OPEN when it is created. It becomes PAID or CANCELLED and never changes after that. Dishes can be added or removed, and an order can be paid or cancelled, only while it is OPEN.
2. ``table`` is between 1 and T_MAX and ``guests`` is between 1 and G_MAX.
3. A line has a quantity between 1 and Q_MAX. An order has at most L_MAX different dishes. Adding a dish that is already in the order adds to its quantity.
4. A dish with limited stock cannot be ordered beyond its stock. Removing a dish or cancelling an order returns the stock.
5. The bill is computed in this order:

   - subtotal = sum of price × quantity;
   - during happy hour, from HH_START up to but not including HH_END, every drink line gets HH_PERCENT % off;
   - if the amount after the happy hour discount reaches DISCOUNT_THRESHOLD, the order gets DISCOUNT_PERCENT % off that amount (your card says whether the threshold itself counts);
   - if the order has at least SERVICE_GUESTS guests, a service charge of SERVICE_PERCENT % is added to the amount after discounts;
   - every percentage is rounded half up to a whole kuruş at the step where it is applied: the happy hour discount for each drink line, the order discount and the service charge once for the whole order.

6. The kitchen can work on at most KITCHEN_SLOTS open orders that contain a main course. Adding a main course to one more order is refused with status 429. An order frees its slot when it is paid or cancelled, or when its last main course is removed.
7. A card payment goes to the payment provider. If the provider does not answer within PAY_TIMEOUT_MS or answers with an error, the order stays OPEN and the service answers 503. A payment repeated with the same idempotency key must never charge the customer twice: after a successful payment the same request returns the same payment with status 200. While a card payment of an order is in progress, the order cannot be changed, cancelled or paid again.
8. Invalid input gets a 4xx status with a JSON error message, never a 5xx status and never a stack trace.


Your repository
---------------

Your group repository is created with these starter files. Keep this layout, because the grading scripts look for files in these places.

.. list-table::
   :header-rows: 1
   :widths: 40 47 13

   * - Path
     - Content
     - Role
   * - ``docs/roles.csv``
     - One row per role: role, GitHub username and, in a group of four, the part of R5 a member takes. No names or student numbers.
     - all
   * - ``docs/test-plan.pdf``
     - Scope, technique for each business rule, environment and hardware, entry and exit criteria, risks
     - all
   * - ``tests/blackbox/``
     - pytest tests that call the running service
     - R1
   * - ``docs/decision-table.csv``
     - Decision table for rule 5
     - R1
   * - ``tests/property/``
     - Hypothesis properties of the service
     - R2
   * - ``tests/pairwise/``
     - Generated pairwise set ``pairwise_set.csv``, the tool command in ``command.txt``, and the tests that run the set
     - R2
   * - ``src/bistro/``
     - Your own module: ``splitter.py`` and ``payment_client.py``
     - R3
   * - ``tests/unit/``
     - Unit tests of your own module
     - R3
   * - ``perf/``
     - k6 or Locust scripts in ``perf/scripts/``, raw results in ``perf/results/``, and ``perf/findings.json``
     - R4
   * - ``docs/threat-model.pdf``
     - STRIDE table of the system and how you tested each threat
     - R5
   * - ``security/``
     - OWASP ZAP baseline report and ``notes.txt`` on its warnings
     - R5
   * - ``chaos/``
     - One text file per chaos experiment
     - R5
   * - ``.github/workflows/ci.yml``
     - CI that runs the unit tests on every push
     - R5
   * - ``bugs/bugs.csv``
     - One row per fault you found
     - all

Starting the service
~~~~~~~~~~~~~~~~~~~~

The service images are published on ghcr.io and can be pulled without logging in. They run on Windows, Linux and macOS, including Apple Silicon.

1. Install Docker Desktop (Windows, macOS) or Docker Engine (Linux).
2. Copy ``.env.example`` to ``.env`` and fill in the values from your variant card (the issue "Your variant card" in your repository). Never commit ``.env``; it is already in ``.gitignore``.
3. Run ``docker compose up -d``. The order service answers on http://localhost:8080 and the payment provider on http://localhost:8081.
4. Check it with ``curl http://localhost:8080/health``.

Running the tests::

    python -m venv .venv
    source .venv/bin/activate        # Windows: .venv\Scripts\activate
    pip install -r requirements.txt
    pytest tests/unit                                       # own module, no service needed
    pytest tests/blackbox tests/property tests/pairwise     # needs the service running

Black-box tests (R1)
~~~~~~~~~~~~~~~~~~~~

Use the ``base_url``, ``payments_url`` and ``waiter_keys`` fixtures from ``tests/conftest.py``, never a hard-coded address or key, because the grader runs your tests against other copies of your variant. Each test must be independent: it creates the orders it needs and does not rely on the order in which tests run or on data left by another test.

Tests derived from the decision table use ``pytest.mark.parametrize`` with one ``pytest.param(..., id="<rule_id>")`` per row, where ``<rule_id>`` is the value in the ``rule_id`` column of ``docs/decision-table.csv``. The grader matches these ids.

Property-based and pairwise tests (R2)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Each property is a test function decorated with Hypothesis ``@given``. A property must hold for the correct service, because the grader runs it against the fault-free copy of your variant. Keep ``max_examples`` modest (for example 50).

For pairwise testing, list the parameters and their values, generate the set with PICT, allpairspy or a similar tool, commit the output as ``tests/pairwise/pairwise_set.csv`` and the exact command as ``tests/pairwise/command.txt``, and write tests that run every row against the service.

Your own module (R3)
~~~~~~~~~~~~~~~~~~~~

R3 builds two pieces with test-driven development. Write each test first and commit it before the code that makes it pass, so that the history shows the red, green, refactor cycle. The signatures are fixed, because hidden acceptance tests call them. The docstrings in ``src/bistro/splitter.py`` and ``src/bistro/payment_client.py`` give the full rules; the limits, the rounding rule and the backoff base come from your variant card.

.. code-block:: python

    def split_bill(total_kurus: int, people: int, tip_percent: int = 0) -> list[int]
    def split_weighted(total_kurus: int, weights: list[int]) -> list[int]

    class PaymentClient:
        def __init__(self, transport, max_retries: int, timeout_s: float, sleep=time.sleep): ...
        def charge(self, order_id: str, amount_kurus: int, idempotency_key: str) -> str: ...

Import them as ``from bistro.splitter import split_bill``. Your unit tests must replace ``transport`` and ``sleep`` with test doubles (for example ``unittest.mock.Mock``), so that no test opens a network connection or actually sleeps.

Coverage::

    pytest tests/unit --cov=bistro --cov-branch --cov-report=term-missing

Mutation testing with mutmut needs Linux or macOS. On Windows run it inside WSL, or in Docker from the repository root in PowerShell::

    mutmut run
    mutmut results

    docker run --rm -v "${PWD}:/w" -w /w python:3.12 sh -c "pip install -q -r requirements.txt && mutmut run; mutmut results"

Performance and reliability (R4)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Use an open workload model (k6 arrival-rate executors, or a Locust load shape with a fixed arrival rate). Commit the raw output of every run you rely on (JSON or CSV, not screenshots), and write the CPU, memory and operating system of the machine in your test plan. ``perf/findings.json`` has these fields:

- ``group``: your group id, for example "1-07";
- ``tool``: "k6" or "locust";
- ``p95_limit_ms``: the p95 limit printed on your variant card;
- ``capacity_rps``: the arrival rate, in requests per second, at which the p95 response time of ``POST /orders/{id}/items`` goes above ``p95_limit_ms``;
- ``growing_endpoint``: the endpoint whose response time grows with the number of stored orders, written as method and path without the query string, for example "GET /orders/{id}" or "GET /orders";
- ``leaking_endpoint``: the endpoint whose calls make the service's memory grow during a soak test, written the same way;
- ``evidence``: paths of the result files that support each answer.

Security, resilience and CI (R5)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Run an OWASP ZAP baseline scan against your own running variant and commit the report in ``security/``. In ``security/notes.txt`` write the command you used and one line for each warning: a real problem (with the test and the row in ``bugs/bugs.csv``) or a false positive (with the reason).

The mock payment provider listens on port 8081. Switch its behaviour with::

    curl -X POST http://localhost:8081/admin/mode -H "Content-Type: application/json" -d '{"mode": "slow", "delay_ms": 3000}'

The modes are ``normal`` (answers at once), ``slow`` (waits ``delay_ms`` milliseconds), ``down`` (answers every charge with 503) and ``flaky`` (fails a share ``error_rate`` of the charges with 503, for example ``{"mode": "flaky", "error_rate": 0.3}``). ``GET http://localhost:8081/charges?order_id=<id>`` lists the charges the provider recorded for an order, so you can count them, and ``POST /admin/reset`` forgets every charge and goes back to normal mode. A charge repeated with the same ``Idempotency-Key`` header is recorded once. You can also stop and start the container with ``docker compose stop payments`` and ``docker compose start payments``. For each experiment, write one text file in ``chaos/`` with the steady state you expect in numbers, the hypothesis, the fault and how long it lasted, what you observed with the commands and outputs, and whether the hypothesis held.

The CI workflow in the starter files runs only the unit tests. Keep it working.

Bug reports
~~~~~~~~~~~

Add one row to ``bugs/bugs.csv`` for every fault you find. The columns are ``id`` (BUG-001, BUG-002, ...), ``title``, ``rule`` (number of the business rule), ``found_by`` (GitHub username), ``test`` (path and test name, for example ``tests/blackbox/test_bill.py::test_threshold_exact``), ``severity`` (low, medium or high), ``steps``, ``expected`` and ``actual``.


Automatic grading
-----------------

The project grade is computed by scripts from the state of your repository at Saturday 12 December, 23:59. Nobody reads your tests to decide whether they are good. The scripts run them. The scripts and the hidden acceptance tests run in a private course repository and are never copied into yours.

How a fault counts as found
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Your variant contains ten seeded faults: functional, state, concurrency, security and resilience faults. For each fault the grader builds a copy of your variant in which only that fault is repaired. A fault counts as found when at least one of your black-box tests fails on your variant and passes on the copy where that fault is repaired. Black-box tests here are all the tests in ``tests/blackbox``, ``tests/property`` and ``tests/pairwise``. A test that fails on the fully correct service is a false alarm: it costs points and finds nothing. The grader runs your whole suite on each copy, and then runs single tests again on a freshly started service, so a test that only failed because of data left by another test is not held against you. Each run of your service tests gets 10 minutes and each single test 60 seconds. A test that runs out of time counts as failed, and tests that never start count as not run, so keep long load tests in ``perf/`` and out of the test folders.

Group score (100 points)
~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1
   :widths: 25 10 65

   * - Part
     - Points
     - How it is measured
   * - Faults found
     - 40
     - 4 points for each seeded fault your tests detect
   * - Performance findings
     - 10
     - capacity within ±20 % of the true value: 5; growing endpoint named: 2.5; leaking endpoint named: 2.5
   * - Own module
     - 25
     - hidden acceptance tests: 10; branch coverage of your unit tests (full points at 95 %, none below 60 %): 5; mutation score with mutmut (full points at 90 %, none below 40 %): 10
   * - Test design
     - 10
     - every decision table row is used by a parametrised test: 5; at least three Hypothesis properties that pass on the correct service: 3; pairwise set generated by a tool and executed: 2
   * - Engineering
     - 15
     - unit tests pass in a clean container and the CI workflow runs them: 5; checkpoint 1 complete: 3; checkpoint 2 complete: 3; every found fault has a complete row in ``bugs/bugs.csv``: 4
   * - False alarms
     - up to −10
     - −1 for each black-box test that fails on the correct service
   * - History rewriting
     - −5
     - a force push to ``main`` after checkpoint 2

Checkpoint 1 is complete when every member has committed their own row of ``docs/roles.csv``, ``docs/test-plan.pdf`` is in the repository and every member has pushed at least one passing test. Checkpoint 2 is complete when every member has pushed tests in their own area and ``bugs/bugs.csv`` has at least three rows.

From group score to your score
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Each student's project score is the group score multiplied by a participation factor. The factor is computed with ``git blame`` on the final commit: it is your share of the lines under ``tests/``, ``src/`` and ``perf/`` that were committed from your GitHub account, compared with an equal share for each member. Half of an equal share or more gives a factor of 1. Less than that lowers the factor in proportion, down to 0 for a student with no lines.

Commit from your own account, with an e-mail address that is linked to your GitHub account. The private no-reply address that GitHub gives you under Settings, Emails also works. Commits that GitHub cannot link to you count for nobody.

Assignment grade
~~~~~~~~~~~~~~~~

Assignment grade = 50 % project score + 50 % exam score.


Exam
----

The written exam is on Monday 14 December at 10:00, for both sections at the same time. Rooms will be announced on the course page by group id. It lasts 45 minutes and is closed book.

Each paper is generated from your own group repository as it was at 23:59 on 12 December. You get questions about the part that you own (your role in ``docs/roles.csv`` at checkpoint 1), and at least one question about a part owned by a teammate. A typical question shows one of your tests and asks what it checks, which input would make it fail, or what happens if one line of it changes.

You will be told individually and called to a short oral exam on Thursday 17 December if your written exam result does not match your contribution in the repository, for example a written score below 40 % while your project score is above 70 %. You can also ask for an oral exam in writing within 24 hours after the written results are announced. When an oral exam takes place, its result replaces your written exam score.


Personal data
-------------

No list of names, student numbers or grades is published at any point of this project, on GitHub or anywhere else.

What is collected
   The sign-up form asks for the section, for each member the student number and the GitHub username, and the e-mail address of the member who sends it. That address is used only to send the copy of the entry and the messages about lines that were not accepted. Your group repository holds what you push to it. The grading scripts and the exam produce your scores. Nothing else is collected.

What it is used for
   Forming the groups, giving you access to your repository and your variant card, grading the project, generating your exam paper and entering your grade.

Who sees it
   Only the instructor sees the sign-up answers, the scores and the link between GitHub usernames and student numbers. Your group repository is private: the members of your group and the instructor can see it, nobody else. Each student is added to their own group repository only, not to the GitHub organization, so nobody can see another group's repository or a list of the other students.

Where it is kept
   GitHub stores the repositories on servers outside Turkey. The repositories contain GitHub usernames but no names and no student numbers. The link between usernames and student numbers stays with the instructor and is never uploaded to GitHub. You may use a GitHub account that does not show your real name.

How you get your results
   Checkpoint results and the breakdown of your group score are posted as an issue in your own group repository, where only your group sees them. Individual scores (participation factor, exam score and assignment grade) are never posted on GitHub. You see your grade in the university's student information system (OBS), and you can ask the instructor for the breakdown of your own score.

Keep personal data out of your repository
   Do not put names, student numbers, e-mail addresses or phone numbers in files, commit messages or test data. The service only needs made-up data.

How long it is kept
   The sign-up answers, the repositories and the scores are kept as exam records for as long as the university's rules require, and are deleted after that.

Your rights
   The data controller is Çukurova University. Under Article 11 of the Turkish Personal Data Protection Law (KVKK, Law No. 6698) you can ask which data about you is processed and have it corrected. Write to the instructor or to the university's personal data contact.


Academic integrity
------------------

AI tools are not allowed
~~~~~~~~~~~~~~~~~~~~~~~~

You may not use AI tools for any part of this project. This covers chat assistants (ChatGPT, Claude, Gemini, Copilot Chat, DeepSeek and others), code completion in the editor (GitHub Copilot, Cursor, Codeium, Tabnine, the built-in AI completion of your IDE and similar), and coding agents that run commands or edit files for you. It applies to code, tests, test data, scripts, configuration, bug reports, the test plan and the threat model. Turn off AI completion in your editor before you work on your repository.

The point of the project is that you design the tests, write the code and find the faults yourselves. The exam is built from your own repository. A student who cannot explain the tests and code committed from their account will lose most of the exam half of the grade, whoever or whatever wrote them.

Ordinary documentation is allowed: the manuals of Python, pytest, Hypothesis, mutmut, k6, Locust, OWASP ZAP and Docker, the course slides, textbooks, and search engines used to read documentation and forum answers written by people.

Other rules
~~~~~~~~~~~

1. Keep your group repository private. Do not fork it, copy it to a public place, or share its contents with other groups.
2. Do not share your variant card or your variant key. Do not run another group's variant.
3. Every member commits their own work from their own GitHub account. Do not commit on behalf of a teammate.
4. Do not rewrite the history of the ``main`` branch. Force pushes to ``main`` and deleting it are blocked, and every push is recorded by GitHub.
5. Do not attack the infrastructure of the course or other groups. Security testing is allowed only against your own variant running on your own computer.

Suspected violations are handled under Çukurova University's regulations on academic misconduct and student discipline. The instructor may call any student to an oral exam on the project, and the result of that exam replaces the written exam score.


Questions
---------

Ask questions in class or during office hours, or write to the instructor's university e-mail address, which is on the course page. Clarifications given in class are part of the specification and will also be posted on the course page.
