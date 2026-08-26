# CFOS Data Model — v1

## Goal

The v1 database must support this complete flow:

Client
→ Project
→ Intake
→ Project Brain
→ Deliverables
→ Work Units
→ Roles / Executors
→ Execution

The schema should stay simple enough to implement quickly, while remaining extensible later.

---

## clients

Represents one Creative Formula client.

Core fields:

- id
- name
- status
- notes
- client_brain
- created_at
- updated_at

`client_brain` stores structured persistent client context in JSON for v1.

Later parts of Client Brain can be normalized into separate tables if needed.

---

## projects

Represents one project, campaign, or recurring content cycle.

Core fields:

- id
- client_id
- name
- summary
- status
- cadence
- creative_ownership
- governance_class
- requested_deadline
- project_brain
- next_action
- next_action_owner
- health
- created_at
- updated_at

Enums/concepts:

cadence:
- recurring
- one_time

creative_ownership:
- client_led
- collaborative
- agency_led

governance_class:
- G0
- G1
- G2
- G3

health:
- healthy
- at_risk
- blocked
- waiting

`project_brain` stores the current structured source of truth in JSON for v1.

---

## intake_sources

Stores raw incoming project information.

Core fields:

- id
- client_id
- project_id
- source_type
- raw_content
- extracted_data
- created_at

source_type examples:

- meeting
- email
- message
- voice_note
- client_brief
- internal_update
- recurring_trigger

The raw source is always preserved.

`extracted_data` contains the AI-structured interpretation.

---

## deliverables

Represents what must ultimately be delivered.

Core fields:

- id
- project_id
- name
- objective
- deliverable_type
- finish_standard
- platform
- target_release_at
- status
- requirements
- created_at
- updated_at

finish_standard:

- E
- D
- C
- B
- A
- custom

status:

- planned
- in_production
- qa
- awaiting_approval
- release_ready
- delivered
- complete

---

## roles

Represents abstract operating roles.

Core fields:

- id
- name
- description
- capabilities
- default_executor_types
- created_at

Examples:

- Project Lead
- Producer
- Creative Lead
- Scriptwriter
- Camera Operator
- Production Assistant
- Editor
- Actor
- Social Publisher
- QA Reviewer

---

## executors

Represents anything that can execute work.

Core fields:

- id
- name
- executor_type
- active
- capabilities
- hourly_cost
- availability
- notes
- created_at
- updated_at

executor_type:

- human
- freelancer
- ai
- automation

Examples:

- Founder
- Employee
- Freelancer
- AI Scriptwriter
- Automatic File Processor

---

## work_units

Represents one executable unit of work.

Core fields:

- id
- project_id
- deliverable_id
- role_id
- executor_id
- name
- objective
- inputs
- context_requirements
- expected_output
- acceptance_criteria
- status
- estimated_minutes
- deadline
- dependency_data
- allowed_executor_types
- created_at
- updated_at

status:

- not_ready
- ready
- in_progress
- review
- changes_needed
- approved
- complete

A Work Unit should remain valid regardless of whether it is executed by a human, freelancer, AI agent, or automation.

---

# Key relationships

Client
1 → many Projects

Client
1 → many Intake Sources

Project
1 → many Intake Sources

Project
1 → many Deliverables

Project
1 → many Work Units

Deliverable
1 → many Work Units

Role
1 → many Work Units

Executor
1 → many Work Units

---

# Important v1 principles

1. Project Brain and Client Brain use JSON initially so we can iterate rapidly without constant database migrations.

2. Deliverables and Work Units remain separate.

3. Roles and Executors remain separate.

4. Founders are Executors, not required system dependencies.

5. AI agents and automations are modeled as Executors from the first version.

6. The schema should be expanded only after real projects reveal a need.
