# Development Methodology Rules

## Development Methodology

The development methodology follows a structured four-phase approach: Requirements → Design → Tasks → Implementation. Each phase builds on the previous one and requires explicit approval before proceeding.

### Phase 1: Requirements (Business Analyst Perspective)

#### EARS Format Requirements
Every requirement must be written in EARS format:
- **Easy** to understand by all stakeholders
- **Approachable** with clear language
- **Rational** with clear business justification  
- **Structured** with consistent format

#### Requirement Structure Template
```
1. The system shall [action] [object] [condition]
Business Justification: [Why this requirement exists]
Acceptance Criteria:
- [ ] [Specific testable condition 1]
- [ ] [Specific testable condition 2] 
- [ ] [Specific testable condition 3]
Priority: [High/Medium/Low]
Dependencies: [Other requirements this depends on]

1.1 [Sub-requirement under requirement 1]
1.2 [Another sub-requirement under requirement 1]

2. The system shall [action] [object] [condition]
[Continue with numbered format...]
```

#### Requirements Gathering Checklist
- [ ] Functional requirements identified and documented
- [ ] Non-functional requirements (performance, security, scalability) defined
- [ ] User personas and use cases created
- [ ] Acceptance criteria specified for each requirement
- [ ] Business justification provided for each requirement
- [ ] Dependencies between requirements mapped
- [ ] Stakeholder approval obtained for requirements scope

### Phase 2: Design (Solutions Architect Perspective)

#### Design Documentation Requirements
Every design must address:
- **System Architecture**: High-level component structure
- **Data Models**: Database schemas, APIs, data flows
- **Security Architecture**: Authentication, authorization, data protection
- **Performance Requirements**: Response times, throughput, scalability
- **Error Handling**: Failure modes, recovery strategies, user messaging
- **Integration Points**: External APIs, services, dependencies

#### Architectural Decision Records (ADRs)
For each significant architectural decision, document:
```
# ADR-[ID]: [Decision Title]

## Status
[Proposed/Accepted/Superseded]

## Context
[What is the issue that we're seeing that is motivating this decision or change?]

## Decision
[What is the change that we're proposing or have agreed to implement?]

## Consequences
[What becomes easier or more difficult to do and any risks introduced by this change?]

## Alternatives Considered
[What other options were evaluated and why were they not chosen?]
```

#### Design Review Checklist
- [ ] All functional requirements addressed in design
- [ ] Non-functional requirements (security, performance, scalability) incorporated
- [ ] API specifications defined with request/response formats
- [ ] Data models designed with appropriate relationships and constraints
- [ ] Error handling strategy defined for all failure modes
- [ ] Security measures integrated at appropriate layers
- [ ] Performance characteristics estimated and validated
- [ ] Integration points with external systems documented
- [ ] Architectural decisions recorded with reasoning
- [ ] Design reviewed and approved by relevant stakeholders

### Phase 3: Tasks (Project Planning Perspective)

Create an actionable implementation plan with a checklist of coding tasks based on the requirements and design.

#### Task Creation Requirements
Each task must be:
- A specific, implementable coding activity
- Referenced to specific requirements from the requirements document
- Focused on writing, modifying, or testing code
- Sequenced to build incrementally on previous tasks

#### Task Format Template
Tasks should be organized into logical groups with descriptive names and brief overviews, followed by ASCII tables showing the detailed breakdown.

**TASK 1: Set up Project Foundation**
"Initialize project repository, configure development environment, and establish coding standards."
╭──────┬──────────────────────────────────────────┬─────────────┬──────────────┬───────────────╮
│ ID   │ Title                                    │ Complexity  │ Dependencies │ Requirements  │
├──────┼──────────────────────────────────────────┼─────────────┼──────────────┼───────────────┤
│ 1    │ [∘] Set up project repository            │ 3 / 10      │ None         │ 1, 2          │
│ 1.1  │   └ Initialize Git repo and structure    │ 2 / 10      │ None         │ 1.1           │
│ 1.2  │   └ Configure development environment    │ 3 / 10      │ 1.1          │ 1.2           │
│ 1.3  │   └ Set up linting and formatting        │ 2 / 10      │ 1.1, 1.2     │ 2.1           │
╰──────┴──────────────────────────────────────────┴─────────────┴──────────────┴───────────────╯

**TASK 2: Implement User Authentication**
"Build secure user authentication system with JWT tokens and session management."
╭──────┬──────────────────────────────────────────┬─────────────┬──────────────┬───────────────╮
│ ID   │ Title                                    │ Complexity  │ Dependencies │ Requirements  │
├──────┼──────────────────────────────────────────┼─────────────┼──────────────┼───────────────┤
│ 2    │ [●] User authentication system           │ 8 / 10      │ 1            │ 3, 4          │
│ 2.1  │   └ Create JWT service                   │ 4 / 10      │ 1            │ 3.1           │
│ 2.2  │   └ Implement password hashing           │ 5 / 10      │ 2.1          │ 3.2           │
│ 2.3  │   └ Build login/logout endpoints         │ 6 / 10      │ 2.1, 2.2     │ 4.1           │
╰──────┴──────────────────────────────────────────┴─────────────┴──────────────┴───────────────╯

**TASK 3: Database Integration**
"Set up database schema, models, and data access layer."
╭──────┬──────────────────────────────────────────┬─────────────┬──────────────┬───────────────╮
│ ID   │ Title                                    │ Complexity  │ Dependencies │ Requirements  │
├──────┼──────────────────────────────────────────┼─────────────┼──────────────┼───────────────┤
│ 3    │ [✓] Database setup and models            │ 7 / 10      │ 1            │ 5, 6          │
│ 3.1  │   └ Design database schema               │ 4 / 10      │ 1            │ 5.1           │
│ 3.2  │   └ Create user and data models          │ 5 / 10      │ 3.1          │ 5.2           │
│ 3.3  │   └ Set up migrations and seeders        │ 3 / 10      │ 3.1, 3.2     │ 6.1           │
╰──────┴──────────────────────────────────────────┴─────────────┴──────────────┴───────────────╯

#### Complexity Scoring Guidelines (1-10 Scale)
- **1-2 (Trivial)**: Single function, basic logic, <20 lines, no external dependencies
- **3-4 (Simple)**: Multiple functions, straightforward logic, <50 lines, minimal integration
- **5-6 (Moderate)**: Multiple files, moderate logic, some integration points, <150 lines
- **7-8 (Complex)**: Complex business logic, multiple integrations, error handling, <300 lines
- **9-10 (Very High)**: MUST be broken down into subtasks of complexity 1-6

#### Task Dependencies
- **Dependencies**: List task numbers that must be completed before this task can start
- **None**: Task has no dependencies and can be started immediately
- **Examples**: 
  - Dependencies: 1, 3 (tasks 1 and 3 must be complete)
  - Dependencies: 2.1, 2.3 (subtasks 2.1 and 2.3 must be complete)

#### Requirements Traceability
- **Requirements**: Reference specific requirement numbers that justify this task's existence
- **Format**: Use simple numbers (1, 2.1, 4.4, etc.) matching the requirements document
- **Examples**:
  - Requirements: 1, 3 (task addresses requirements 1 and 3)
  - Requirements: 2.1, 2.3 (task addresses sub-requirements 2.1 and 2.3)

#### Task Status Tracking
- **[∘] pending**: Task not yet begun
- **[●] in progress**: Currently being worked on  
- **[✓] complete**: Task finished and verified
- **[✗] blocked**: Cannot proceed due to dependency
- **[⚠] review**: Task complete, awaiting review

#### ASCII Table Format Requirements
Each task group must be presented using the ASCII table format with the following structure:

**Column Specifications:**
- **ID**: Task/subtask identifier (1, 2, 2.1, 2.2, etc.)
- **Title**: Clear, actionable description with subtask indentation (└)
- **Complexity**: Difficulty score in X/10 format (1-10 scale)
- **Dependencies**: Prerequisites (task IDs or "None")
- **Requirements**: Requirement traceability (simple numbers: 1, 2.1, 4.4, etc.)

**Table Structure:**
- Use box-drawing characters for professional appearance
- Maintain consistent column widths for readability
- Group related tasks under descriptive headings
- Include brief task group overview before each table

#### Formatting Guidelines
- Use descriptive task group names with brief overview
- Indent subtasks with `└` for visual hierarchy
- Keep task titles concise but actionable
- Use consistent status symbols in brackets
- Complex tasks (8-10) must be broken into subtasks (1-6)
- Dependencies reference task IDs (e.g., "1", "2.1, 2.2")
- Requirements use simple numbers matching requirements document

#### Task Review Checklist
- [ ] All tasks are coding-focused (no deployment, user testing, etc.)
- [ ] Each task has complexity score (1-10) assigned
- [ ] Complex tasks (8-10) are broken down into subtasks (1-6)
- [ ] Each task references specific requirements (simple number format)
- [ ] Dependencies are clearly defined for each task
- [ ] Tasks build incrementally without big complexity jumps
- [ ] All requirements are covered by implementation tasks
- [ ] Tasks are sequenced with proper dependencies
- [ ] Each task is actionable by a developer
- [ ] No circular dependencies exist in task sequence

### Phase 4: Implementation (Senior Developer + QA Engineer Perspectives)

#### Story Creation Template
Each implementation story must include:
```
# Story: [Brief Title]

## Context
[Background information and business context]

## Requirements
[Specific requirements this story addresses]

## Design Reference
[Link to relevant design documents and ADRs]

## Implementation Tasks
- [ ] [Specific implementable task 1]
- [ ] [Specific implementable task 2]
- [ ] [Specific implementable task 3]

## Acceptance Criteria
- [ ] [Testable condition 1]
- [ ] [Testable condition 2]
- [ ] [Testable condition 3]

## Testing Strategy
- Unit tests: [What will be unit tested]
- Integration tests: [What integration scenarios]
- End-to-end tests: [What user workflows]

## Definition of Done
- [ ] Code implemented and reviewed
- [ ] Unit tests written and passing
- [ ] Integration tests written and passing  
- [ ] Documentation updated
- [ ] Security review completed
- [ ] Performance requirements validated
- [ ] Acceptance criteria verified
```

#### Implementation Quality Gates
Before marking any story complete:
- [ ] All code follows established conventions and patterns
- [ ] Comprehensive unit tests cover all business logic
- [ ] Integration tests validate external dependencies
- [ ] Error handling implemented for all failure modes
- [ ] Security measures properly implemented
- [ ] Performance requirements met or exceeded
- [ ] Code reviewed by another developer
- [ ] Documentation updated to reflect changes
- [ ] Acceptance criteria fully verified

## Workflow Integration Patterns

### Requirements → Design Transition
- All requirements must be explicitly approved before design begins
- Design must address each requirement with clear architectural solutions
- Requirements traceability maintained throughout design process
- Any requirement changes require user approval and design updates

### Design → Tasks Transition
- Tasks cannot be created without approved design
- Each task must reference specific design elements and requirements
- Task breakdown must cover all aspects of the approved design
- Design changes during task creation require architectural review

### Tasks → Implementation Transition
- Implementation cannot begin without approved task list
- Each implementation session focuses on one task at a time
- Task completion requires meeting all acceptance criteria
- Implementation changes that affect other tasks require review

### Cross-Phase Quality Assurance
- Security considerations integrated at every phase transition
- Performance implications evaluated throughout the workflow
- All decisions and changes documented for traceability
- User approval required at each major phase transition
- Testing strategy developed during design and refined during tasks

## Context Preservation

### Project State Management
Clyde maintains awareness of:
- **Current Phase**: Requirements, Design, Tasks, or Implementation
- **Completed Work**: Approved requirements, design decisions, finished tasks
- **Active Context**: Current task being worked on and its dependencies
- **Decision History**: Key architectural and design decisions with reasoning
- **Quality Status**: Security reviews, testing coverage, performance validation

### Session Continuity Protocol
At the start of each session, Clyde automatically:
1. Assesses current project phase and status
2. Identifies the logical next step in the workflow
3. Checks for any blockers or missing dependencies
4. Proactively suggests what to work on next
5. Maintains context from previous work without requiring re-explanation

### Efficient Context Handoff
When switching between phases or tasks:
- Previous work is automatically referenced and built upon
- Decisions and constraints are carried forward
- User doesn't need to re-explain completed work
- Focus remains on moving forward efficiently

## Self-Improvement and Error Prevention

### Error Learning Protocol
When development errors occur:

1. **Document the Error**
   - What went wrong and at what stage
   - Root cause analysis
   - Impact on project timeline/quality

2. **Assess Preventability**
   - Could this have been caught with a rule?
   - Was it a process gap or knowledge gap?
   - Is it a recurring pattern?

3. **Create Prevention Measures**
   - Add to quality checklists if applicable
   - Update task templates to include checks
   - Enhance complexity scoring criteria
   - Improve requirement gathering questions

4. **Update Standards**
   - Modify coding standards if needed
   - Enhance security checklists
   - Improve testing requirements
   - Update architectural guidelines

### Continuous Improvement Areas

#### Task Complexity Refinement
- Track actual vs. estimated complexity
- Identify patterns in complexity underestimation
- Refine scoring criteria based on real outcomes
- Update task breakdown strategies

#### Quality Gate Enhancement
- Monitor which quality issues slip through
- Strengthen checklists based on missed items
- Improve review processes
- Add automated checks where possible

#### Process Optimization
- Identify workflow bottlenecks
- Streamline approval processes
- Improve context handoff between phases
- Enhance communication patterns

### Learning Integration
- Apply lessons learned to similar future tasks
- Share insights across project contexts
- Update templates and examples
- Refine expert perspective guidelines



