## **7 Core Principles of Software Testing** {#7-core-principles-of-software-testing}

The principles, often adopted from the International Software Testing Qualifications Board (ISTQB), form the foundation of effective testing strategy:

### **Testing Shows the Presence of Defects, Not Their Absence** {#testing-shows-the-presence-of-defects,-not-their-absence}

Testing can only prove that defects exist, not that the software is perfect or completely defect-free. Even after thorough testing, undetected issues may remain.

### **Exhaustive Testing is Impossible** {#exhaustive-testing-is-impossible}

Testing every possible combination of inputs, conditions, and paths is not feasible for any but the most trivial software. Testers must use risk and prioritization to focus their efforts.

### **Early Testing Saves Time and Money (Shift Left)** {#early-testing-saves-time-and-money-(shift-left)}

Defects are significantly cheaper and easier to fix when found early in the Software Development Lifecycle (SDLC), for example, during the requirements or design phase.

### **Defects Cluster Together** {#defects-cluster-together}

A small number of modules or components often contain the majority of defects. This is related to the Pareto Principle (80/20 rule), suggesting that approximately 80% of problems are found in 20% of the code. Testing should therefore focus on these known or suspected problem areas.

### **Pesticide Paradox** {#pesticide-paradox}

If you repeat the same tests over and over, they will eventually stop finding new bugs. To overcome this, test cases must be regularly reviewed, updated, and new and different tests (like exploratory testing) must be created to find new defects.

### **Testing is Context Dependent** {#testing-is-context-dependent}

The approach to testing depends entirely on the context of the system being developed. For instance, testing a life-critical avionics system is vastly different from testing a simple e-commerce website or a mobile game.

### **Absence-of-Errors Fallacy** {#absence-of-errors-fallacy}

Finding and fixing a large number of defects doesn't matter if the system built is unusable or fails to meet the end-users' needs and expectations. The software must be fit for purpose.

Software Testing Life Cycle (STLC) Theory

The Software Testing Life Cycle (STLC) provides a systematic and structured sequence of phases that guides testing activities from start to finish. It is a subset of the broader Software Development Life Cycle (SDLC).

## **Phases of the STLC** {#phases-of-the-stlc}

### **Requirement Analysis** {#requirement-analysis}

The QA team analyzes the software requirements (functional and non-functional) to determine the scope of testing, identify testable requirements, and prioritize features.

### **Test Planning** {#test-planning}

The test manager defines the overall test strategy, estimates effort and resources, identifies the tools to be used, and creates the Test Plan document.

### **Test Case Development** {#test-case-development}

Testers design, write, and verify test cases, test scripts, and prepare the test data based on the requirements and the test plan.

### **Test Environment Setup** {#test-environment-setup}

The hardware, software, and network configuration needed for testing are set up and verified for readiness.

### **Test Execution** {#test-execution}

Test cases are run, and actual results are compared to expected results. Defects are logged, retested, and tracked until they're resolved. Regression testing is often done during this phase.

### **Test Cycle Closure** {#test-cycle-closure}

Testing activities are concluded by compiling a Test Summary Report, evaluating the success of the testing effort against the objectives, and analyzing lessons learned.

## **🔐 Authentication (Who are you?)** {#🔐-authentication-(who-are-you?)}

Authentication is the process of verifying the identity of a user, system, or application. It answers the question: "Are you who you claim to be?"

Goal: To prove identity.

Process: A user provides credentials (like a username and password) that are checked against a stored reference.

Key Techniques (Factors):

Something you know (Password, PIN).

Something you have (Security token, phone for SMS code).

Something you are (Fingerprint, facial scan, retina scan).

Analogy: Showing your driver's license or passport to enter an airport.

## **🔑 Authorization (What can you do?)** {#🔑-authorization-(what-can-you-do?)}

Authorization is the process of determining what permissions a successfully authenticated user has. It answers the question: "What are you allowed to do or access?"

Goal: To grant or deny access to a resource or function.

Process: Once authenticated, the system checks the user's role or permissions (e.g., Administrator, Guest, Billing Manager) against a set of access policies.

Key Techniques:

Role-Based Access Control (RBAC): Permissions are tied to the user's role (e.g., all "Managers" can approve expenses).

Attribute-Based Access Control (ABAC): Access is based on a set of attributes about the user, the resource, and the environment.

Analogy: The permissions granted on your airline ticket (e.g., seating class, baggage limit, lounge access).
