# Study Guide

## Interview notes powered by Gemini AI 

[To Place in Document](#to-place-in-document)

[List of items from feedback and thoughts that should be elaborated on and given a specific action in this document](#list-of-items-from-feedback-and-thoughts-that-should-be-elaborated-on-and-given-a-specific-action-in-this-document)

[Hard Skills](#hard-skills)

[Questions](#questions)

[JAVA](#java)

[Garbage Collection](#garbage-collection)

[Static variables](#static-variables)

[Int, Double, Float, BigDecimal](#int,-double,-float,-bigdecimal)

[7 Core Principles of Software Testing](#7-core-principles-of-software-testing)

[Testing Shows the Presence of Defects, Not Their Absence](#testing-shows-the-presence-of-defects,-not-their-absence)

[Exhaustive Testing is Impossible](#exhaustive-testing-is-impossible)

[Early Testing Saves Time and Money (Shift Left)](#early-testing-saves-time-and-money-\(shift-left\))

[Defects Cluster Together](#defects-cluster-together)

[Pesticide Paradox](#pesticide-paradox)

[Testing is Context Dependent](#testing-is-context-dependent)

[Absence-of-Errors Fallacy](#absence-of-errors-fallacy)

[Phases of the STLC](#phases-of-the-stlc)

[Requirement Analysis](#requirement-analysis)

[Test Planning](#test-planning)

[Test Case Development](#test-case-development)

[Test Environment Setup](#test-environment-setup)

[Test Execution](#test-execution)

[Test Cycle Closure](#test-cycle-closure)

[🔐 Authentication (Who are you?)](#🔐-authentication-\(who-are-you?\))

[🔑 Authorization (What can you do?)](#🔑-authorization-\(what-can-you-do?\))

[Languages, Frameworks, and Libraries](#languages,-frameworks,-and-libraries)

[Code Reviews](#code-reviews)

[Correctness and Functional Integrity ⚙️](#correctness-and-functional-integrity-⚙️)

[Readability and Maintainability (Style) 📖](#readability-and-maintainability-\(style\)-📖)

[Performance and Efficiency ⚡](#performance-and-efficiency-⚡)

[Security and Error Handling 🔒](#security-and-error-handling-🔒)

[Test Coverage and Extensibility 🌱](#test-coverage-and-extensibility-🌱)

[Git](#git)

[Branching Strategy](#branching-strategy)

[1\. GitFlow](#1.-gitflow)

[2\. GitHub Flow](#2.-github-flow)

[3\. Trunk-Based Development (TBD)](#3.-trunk-based-development-\(tbd\))

[The Key Difference](#the-key-difference)

[Git cli](#git-cli)

[Viewing All Configuration Settings](#viewing-all-configuration-settings)

[Initial Setup and Configuration](#initial-setup-and-configuration)

[Creating a New Project](#creating-a-new-project)

[Basic Workflow (Staging and Committing)](#basic-workflow-\(staging-and-committing\))

[Working with a Remote Repository](#working-with-a-remote-repository)

[Branching and Merging](#branching-and-merging)

[Git Merge Versus Git Rebase](#git-merge-versus-git-rebase)

[Git Merge](#git-merge)

[Git Rebase](#git-rebase)

[Git Revert, Reset, Hard and Soft](#git-revert,-reset,-hard-and-soft)

[git revert](#git-revert)

[git reset](#git-reset)

[Design Pattern](#design-pattern)

[Factory Pattern](#factory-pattern)

[Page Object Model](#page-object-model)

[S.O.L.I.D](#s.o.l.i.d)

[S \- Single Responsibility Principle](#s---single-responsibility-principle)

[Real-World Analogy](#real-world-analogy)

[Summary](#summary)

[O \- Open Closed Principle](#o---open-closed-principle)

[Real-World Analogy](#real-world-analogy-1)

[Summary](#summary-1)

[L \- Liskov Substitution Principle](#l---liskov-substitution-principle)

[Real-World Analogy](#real-world-analogy-2)

[Summary](#summary-2)

[I \- Interface Segregation Principle](#i---interface-segregation-principle)

[Real-World Analogy](#real-world-analogy-3)

[Summary](#summary-3)

[D \- Dependency Inversion Principle](#d---dependency-inversion-principle)

[Real-World Analogy](#real-world-analogy-4)

[Summary](#summary-4)

[REST Operations](#rest-operations)

[Create: POST](#create:-post)

[Read: GET](#read:-get)

[Update: PUT and PATCH](#update:-put-and-patch)

[Delete: DELETE](#delete:-delete)

[REST Response Codes](#rest-response-codes)

[1xx: Informational](#1xx:-informational)

[2xx: Success](#2xx:-success)

[3xx: Redirection](#3xx:-redirection)

[4xx: Client Error](#4xx:-client-error)

[5xx: Server Error](#5xx:-server-error)

[Object-Oriented design](#object-oriented-design)

[Encapsulation](#encapsulation)

[Inheritance](#inheritance)

[Polymorphism](#polymorphism)

[Abstraction](#abstraction)

[Interfaces](#interfaces)

[Abstract](#abstract)

[Interface and Abstract example](#interface-and-abstract-example)

[Leetcode](#leetcode)

[Java Collections Framework](#java-collections-framework)

[List (ordered, index-based, allows duplicates)](#list-\(ordered,-index-based,-allows-duplicates\))

[ArrayList](#arraylist)

[LinkedList](#linkedlist)

[Vector](#vector)

[Stack](#stack)

[Set (unique elements, no duplicates)](#set-\(unique-elements,-no-duplicates\))

[HashSet](#hashset)

[LinkedHashSet](#linkedhashset)

[TreeSet](#treeset)

[Queue / Deque (FIFO or double-ended, or priority)](#queue-/-deque-\(fifo-or-double-ended,-or-priority\))

[ArrayDeque](#arraydeque)

[PriorityQueue](#priorityqueue)

[LinkedList](#linkedlist-1)

[Map (key-value lookup)](#map-\(key-value-lookup\))

[HashMap](#hashmap)

[LinkedHashMap](#linkedhashmap)

[TreeMap](#treemap)

[Hashtable](#hashtable)

[ConcurrentHashMap](#concurrenthashmap)

[Quick Selection Guide](#quick-selection-guide)

# To Place in Document {#to-place-in-document}

## **List of items from feedback and thoughts that should be elaborated on and given a specific action in this document** {#list-of-items-from-feedback-and-thoughts-that-should-be-elaborated-on-and-given-a-specific-action-in-this-document}

How would you approach testing a new feature

More dev examples of what I did.

What problems did I solve? Looking for a problem solver more than a coding hero.

When presented with a problem, how did I solve it. Answer very methodically, first talking about asking for more info, how many users? 100 or 1,000,000, what region is it deployed. Then talk about spiking out a demo, after talking with stakeholders. If possible… have a real example of when I did this.

Use the STAR method

CV idea’s to show I’m more dev than automation engineer. 1\. Put the languages at the top. 2\. Sell myself as an engineer who gives solutions, sometimes they are dev solutions, sometimes they are automation solutions.

Yarn and npm, differences

# Hard Skills {#hard-skills}

## **Questions** {#questions}

…

## **JAVA** {#java}

### **Garbage Collection** {#garbage-collection}

In simple terms, **Garbage Collection (GC)** is Java’s automatic memory management system.

In older languages like C++, developers have to manually "allocate" memory for an object and then "deallocate" (delete) it when they are done. If they forget, the program leaks memory and eventually crashes. Java handles this for you: it automatically identifies objects that are no longer being used and clears them out to free up space.

---

How it Works: The "Reachability" Test

The Garbage Collector follows a simple rule: **If an object cannot be reached by any live part of the program, it is eligible for deletion.**

1. **Live Objects:** Objects currently being used or referenced by a variable in your code.  
2. **Dead Objects:** Objects that no longer have a "path" to them.  
   * *Example:* You set a variable to `null` or a method finishes and its local variables disappear. The object that variable was pointing to is now "orphaned."

---

The Three Steps

The GC generally follows a **"Mark and Sweep"** pattern:

1. **Mark:** The GC starts from the "Roots" (active threads, static variables, etc.) and follows every reference to find every object that is still "reachable." It marks these as "Alive."  
2. **Sweep:** It goes through the heap (where objects live) and removes everything that wasn't marked.  
3. **Compact (Optional):** It moves the remaining live objects closer together to prevent "fragmentation" (holes in memory), making room for large new objects.

---

Memory Structure: Generational Strategy

Java assumes that **most objects die young** (like temporary strings in a loop). To be efficient, it divides the Heap into generations:

* **Young Generation:** Where new objects are born. GC happens here frequently (**Minor GC**). It's very fast.  
* **Old Generation:** If an object survives several rounds of GC in the Young Generation, it gets "promoted" here. GC happens here less often (**Major GC**).  
* **Permanent Generation (Metaspace):** Where the actual class definitions and static metadata are stored.

---

Key Things to Remember

* **It’s Non-Deterministic:** You can’t predict *exactly* when the GC will run. You can suggest it using `System.gc()`, but the JVM usually ignores you (it knows better).  
* **Stop-the-World:** In some GC versions, the entire application pauses briefly while the GC cleans up. Modern Java versions (like G1 or ZGC) are designed to make these pauses almost invisible.  
* **Memory Leaks still happen:** Even with GC, you can have a "leak" if you accidentally keep a reference to an object you don't need (e.g., adding objects to a `static List` and never removing them).

### **Static variables** {#static-variables}

In Java, the `static` keyword means that a member (variable or method) belongs to the class itself, rather than to a specific instance (object) of that class.

```java
class Player {
    String name;          // Instance variable: each player has their own name
    static int count = 0; // Static variable: shared by ALL players

    Player(String name) {
        this.name = name;
        count++; // Increment the shared counter every time a player is created
    }
}
```

### **Int, Double, Float, BigDecimal** {#int,-double,-float,-bigdecimal}

Cast between them all….

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

## **Languages, Frameworks, and Libraries** {#languages,-frameworks,-and-libraries}

Java

C\#

Javascript/Typescript

SQL

React.js

Angular

Selenium

Cypress

Playwright

## **Code Reviews** {#code-reviews}

Five critical areas to ensure the code is robust, maintainable, and correct:

### **Correctness and Functional Integrity ⚙️** {#correctness-and-functional-integrity-⚙️}

Does the code actually solve the problem it was intended to solve? I'd check that all requirements are met, edge cases (like null inputs, zero values, or empty collections) are handled correctly, and that there are no obvious logic errors or race conditions in concurrent code.

### **Readability and Maintainability (Style) 📖** {#readability-and-maintainability-(style)-📖}

Is the code easy for a future developer (or me) to understand? I look for clear, descriptive variable and method names, minimal complexity (avoiding deep nesting or overly long methods), adherence to established style guides (like Java conventions), and sufficient, up-to-date comments where the why of the code isn't immediately obvious.

### **Performance and Efficiency ⚡** {#performance-and-efficiency-⚡}

Are there any major bottlenecks or inefficient algorithms? This involves spotting things like unnecessary database queries within a loop, using inefficient data structures (e.g., searching an ArrayList repeatedly when a HashSet is needed), or generating excessive garbage objects. The focus is on finding low-hanging performance fruit, not micro-optimizations.

### **Security and Error Handling 🔒** {#security-and-error-handling-🔒}

Is the code safe and resilient? I specifically check for proper input validation (preventing injection attacks like SQL or XSS), correct use of logging (avoiding logging sensitive data), and robust error handling. The code should catch appropriate exceptions, re-throw or handle them gracefully, and never expose internal implementation details to the user.

### **Test Coverage and Extensibility 🌱** {#test-coverage-and-extensibility-🌱}

Is the new code adequately tested and does it fit well within the existing architecture? I ensure that unit tests are provided, that these tests cover the new logic (especially complex or tricky parts), and that the design adheres to established principles like SOLID (especially the Single Responsibility and Open/Closed principles), making it easy to add new features without modifying existing code.

## **Git** {#git}

### **Branching Strategy** {#branching-strategy}

Git branching strategies are sets of rules and conventions that dictate how a team uses Git branches to manage a project's development. The most common strategies are GitFlow, GitHub Flow, and Trunk-Based Development. Choosing the right one depends on your team's size, project complexity, and release frequency.

---

#### **1\. GitFlow** {#1.-gitflow}

GitFlow is a complex, structured branching model for projects with scheduled release cycles. It uses two main long-lived branches and three supporting branches. Typically for waterfall.

This is a good example from youtube

[Gitflow Explained](https://youtu.be/Aa8RpP0sf-Y)

* **`main` (or `master`)**: This branch contains the **production-ready** code. It is the official release history, and commits here are tagged with version numbers.  
* **`develop`**: This is the main development branch. All new feature work is integrated here.  
* **`feature/*`**: Short-lived branches created from `develop` to develop new features. They are merged back into `develop` when complete.  
* **`release/*`**: Short-lived branches created from `develop` when a new version is ready for release. They are used for final bug fixes and are then merged into **both** `main` and `develop`.  
* **`hotfix/*`**: Short-lived branches created from `main` to fix urgent bugs in production. They are merged back into **both** `main` and `develop`.

**Best for**: Projects with long, planned release cycles. 🗓️

![][image1]

---

#### **2\. GitHub Flow** {#2.-github-flow}

GitHub Flow is a simpler, more lightweight strategy built for continuous delivery. It revolves around a single, always-deployable branch.

[Git Flow vs GitHub Flow: What You Need to Know](https://youtu.be/hG_P6IRAjNQ)

* **`main` (or `master`)**: The **only** long-lived branch. All code on this branch is considered deployable at any time.  
* **`feature/*`**: Short-lived branches for new features or bug fixes. They are created from `main`, and when the work is complete, a pull request is opened, reviewed, and merged directly back into `main`.

**Best for**: Agile teams practicing continuous integration and delivery, especially for web applications. 🚀

![][image2]

---

#### **3\. Trunk-Based Development (TBD)** {#3.-trunk-based-development-(tbd)}

TBD is an extreme form of continuous integration where all developers work on a **single, shared branch** called the "trunk" or "mainline." It prioritizes fast, frequent code merges.

* **`trunk` (or `main`)**: The single source of truth. Developers commit to this branch multiple times a day.  
* **Short-lived feature branches**: In some variations, developers may create branches for a few hours at most, and then merge them back into the trunk as quickly as possible. This is often done in combination with **feature flags** to hide incomplete features from users.

**Best for**: Highly disciplined teams focused on high-frequency releases. This is a core practice for DevOps. ⚡

#### **The Key Difference** {#the-key-difference}

While both use a single long-lived branch and short-lived feature branches, the distinction is in the **frequency of integration** and the tools used to achieve it.

| Feature | GitHub Flow | Trunk-Based Development (TBD) |
| :---- | :---- | :---- |
| **Branch Lifespan** | **Short-lived**, but can last for a while (days or weeks) depending on the feature's size. | **Extremely short-lived** (hours, not days). Commits happen multiple times a day. |
| **Core Principle** | Pull Requests as a collaboration tool. | Frequent integration to avoid conflicts. |
| **Key Enabler** | Pull Requests and code reviews. | **Feature flags** and **robust automated testing**. |

### **Git cli** {#git-cli}

#### **Viewing All Configuration Settings** {#viewing-all-configuration-settings}

* **`git config --list`**: This is the most common command. When run inside a repository, it will show all configuration settings from the **system**, **global**, and **local** levels. Local settings override global, which in turn override system settings.  
* The most direct command to open your Git configuration file in your default text editor is **`git config --global --edit`**.

#### **Initial Setup and Configuration** {#initial-setup-and-configuration}

Before you start, you'll want to tell Git who you are. This information is attached to your commits. This is a one-time setup on your computer.

* `git config --global user.name "Your Name"`  
* `git config --global user.email "youremail@example.com"`

#### **Creating a New Project** {#creating-a-new-project}

If you're starting a new project, you'll create a local repository from scratch.

* `git init` \- Initializes a new, empty Git repository in the current directory. This creates a hidden `.git` folder.

#### **Basic Workflow (Staging and Committing)** {#basic-workflow-(staging-and-committing)}

This is the core loop of using Git. You make changes to your files, tell Git which changes to track, and then save a snapshot of those changes.

* `git status` \- Shows the status of your working directory. It tells you which files have been modified, are new (untracked), or are ready to be committed (staged).  
* `git add <file>` \- Adds a specific file to the **staging area**. This marks the file to be included in the next commit.  
* `git add .` \- Adds all new and modified files in the current directory and its subdirectories to the staging area.  
* `git commit -m "Your commit message"` \- Takes a snapshot of the files in the staging area and saves it to the local repository. The `-m` flag lets you add a concise message describing the changes.

#### **Working with a Remote Repository** {#working-with-a-remote-repository}

Often, you'll be working with a project that already exists on a service like GitHub, GitLab, or Bitbucket.

* `git clone <repository_url>` \- Creates a local copy of a remote repository. This is what you do when you first start working on a project.  
* `git push` \- Uploads your local commits to the remote repository.  
* `git pull` \- Downloads the latest changes from the remote repository and merges them into your local branch.

#### **Branching and Merging** {#branching-and-merging}

Branches allow you to work on new features or bug fixes in an isolated environment without affecting the main codebase.

* `git branch` \- Lists all local branches in your repository.  
* `git branch <new_branch_name>` \- Creates a new branch, but you remain on your current branch.  
* `git checkout <branch_name>` \- Switches to a different branch.  
* `git checkout -b <new_branch_name>` \- Creates a new branch and switches to it in one command.  
* `git merge <branch_to_merge>` \- Merges the changes from a specified branch into your current branch. This is how you integrate finished work.

#### **Git Merge Versus Git Rebase** {#git-merge-versus-git-rebase}

The key difference between `git merge` and `git rebase` is how they integrate changes from one branch into another. Both commands serve a similar purpose, but they do so in fundamentally different ways that affect your repository's history.

---

#### **Git Merge** {#git-merge}

**`git merge`** combines the histories of two branches by creating a **new merge commit**. This commit has two parent commits—one from each of the branches being merged. This approach preserves the complete history of both branches, including the chronological order of all commits. It's a non-destructive operation because it doesn't alter the existing commits.

**Workflow:** You are on the `main` branch and want to integrate a feature branch.

* `git checkout main`  
* `git merge feature-branch`

**When to use it:** Use `git merge` when you want to preserve an accurate and traceable history of how branches were integrated. It's the safer option, especially when working on a public or shared branch, as it avoids rewriting history that other developers might be relying on.

---

#### **Git Rebase** {#git-rebase}

**`git rebase`** moves or "re-bases" a sequence of commits to a new base commit, effectively rewriting the project's history. Instead of creating a new merge commit, it takes all the commits from your feature branch and reapplies them, one by one, on top of the latest commit on the target branch. This results in a clean, linear history.

**Workflow:** You are on your feature branch and want to rebase it onto the `main` branch.

* `git checkout feature-branch`  
* `git rebase main`

**When to use it:** Use `git rebase` to maintain a clean and linear project history. This makes the `git log` easier to read and can simplify debugging with tools like `git bisect`. The golden rule is to **never rebase a public or shared branch** because rewriting its history can cause major problems for other developers. It's best used for cleaning up your personal, local branches before integrating them into a shared branch.

#### **Git Revert, Reset, Hard and Soft** {#git-revert,-reset,-hard-and-soft}

**The primary difference is how they handle your commit history.**

`git revert` is a **safe**, forward-moving command that creates a **new commit** to undo changes from a previous commit, preserving the history. `git reset` is a **destructive** command that **erases** commits from the branch's history by moving the branch pointer, and it has three modes (`soft`, `mixed`, and `hard`) that determine what happens to your files.

---

#### **git revert** {#git-revert}

This command creates a new commit that **reverses the changes** of a specified commit. It does not delete any commits from the history. This is the **safest** way to undo changes, especially on a shared or public branch, because it doesn't rewrite history and won't cause problems for other developers.

**When to use it:** To undo a commit that has already been pushed to a remote repository.

---

#### **git reset** {#git-reset}

This command changes where your branch pointer is pointing, effectively **rewriting history** by removing commits from the end of the branch. Because it changes history, it is only safe to use on **local, un-pushed commits**. It is a dangerous command if used incorrectly, as it can lead to data loss.

`git reset` has three modes that affect the state of your files in the working directory and staging area:

* **`git reset --soft <commit_hash>`**  
  * **Action:** Moves the branch pointer to the specified commit.  
  * **Files:** Keeps all changes from the "removed" commits and puts them in the **staging area** (ready to be committed again). Your working directory is untouched.  
  * **Use case:** You made a few commits but want to combine them into a single, cleaner commit.  
* **`git reset --mixed <commit_hash>`** (This is the default mode)  
  * **Action:** Moves the branch pointer to the specified commit.  
  * **Files:** Keeps the changes from the "removed" commits but puts them in the **working directory** as unstaged changes.  
  * **Use case:** You made some commits but realized they were bad and want to restart by re-adding and re-committing the files.  
* **`git reset --hard <commit_hash>`** ⚠️  
  * **Action:** Moves the branch pointer to the specified commit.  
  * **Files:** **Discards all changes** from the "removed" commits. All uncommitted changes in your working directory and staging area are permanently deleted.  
  * **Use case:** You made a bunch of changes and commits, but you want to completely throw away all of that work and go back to a clean state. Use this with extreme caution.

## **Design Pattern** {#design-pattern}

### **Factory Pattern** {#factory-pattern}

The Factory Pattern is a powerful creational design pattern that should be used primarily because it promotes loose coupling and adheres to the **Open/Closed Principle (OCP)**. By delegating object instantiation to a specialized factory class, the client code interacts only with a common **interface** (like Bird), never with the concrete classes (Eagle or Condor). This separation means that if you need to introduce a new bird type, **you only modify the factory**—the client code that uses the birds remains untouched. This dramatically increases the maintainability and flexibility of the code, making it easier to manage complex object creation logic and scale the application without breaking existing functionality

.

```java
public class BirdFactory {
    // Enum to make the type checking safer and clearer
    public enum BirdType {
        EAGLE,
        CONDOR
    }

    /**
     * Factory method to create an instance of a Bird.
     * @param type The type of bird to create.
     * @return A concrete Bird object (Eagle or Condor).
     */
    public Bird createBird(BirdType type) {
        if (type == null) {
            return null;
        }

        switch (type) {
            case EAGLE:
                return new Eagle();
            case CONDOR:
                return new Condor();
            default:
                throw new IllegalArgumentException("Unknown bird type: " + type);
        }
    }
}
```

```java
public class FactoryDemo {
    public static void main(String[] args) {
        BirdFactory factory = new BirdFactory();

        // Create an Eagle object
        Bird eagle = factory.createBird(BirdFactory.BirdType.EAGLE);
        System.out.println("--- Creating an Eagle ---");
        eagle.fly();
        eagle.makeSound();

        System.out.println("\n----------------------------\n");

        // Create a Condor object
        Bird condor = factory.createBird(BirdFactory.BirdType.CONDOR);
        System.out.println("--- Creating a Condor ---");
        condor.fly();
        condor.makeSound();
    }
}
```

### **Page Object Model** {#page-object-model}

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LoginPage {
    private final WebDriver driver;
    private final By usernameInput = By.id("user"); // Minimal ID
    private final By passwordInput = By.name("pass"); // Minimal name
    private final By loginButton = By.tagName("button"); // Minimal tag

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public void login(String user, String pass) {
        driver.findElement(usernameInput).sendKeys(user);
        driver.findElement(passwordInput).sendKeys(pass);
        driver.findElement(loginButton).click();
    }
}
```

## **S.O.L.I.D** {#s.o.l.i.d}

### **S \- Single Responsibility Principle** {#s---single-responsibility-principle}

The Single Responsibility Principle says:

**A class should have only one reason to change.**

That means:

* A class should focus on one job only.  
* If a class has multiple responsibilities (e.g., data storage, printing, persistence), it becomes harder to maintain, test, and extend.

Why It Matters

* If a class does too many things, changes in one responsibility can break another.  
* Having separate classes for separate responsibilities makes the code easier to maintain and extend.  
* Helps with clean architecture and avoids “god classes.”

Bird Example: Without SRP

Suppose we have a Bird class that handles everything:

```java
// ❌ Bad: multiple responsibilities
class Bird {
    String name;

    void saveToDatabase() {
        // code to save bird in DB
    }

    void printDetails() {
        System.out.println("Bird: " + name);
    }
}
```

❌ Problems:

* This class is responsible for:  
  * Data model (bird’s name)  
  * Persistence (saving to DB)  
  * Presentation (printing details)  
* If the database changes → you must modify this class.  
* If printing changes (e.g., print JSON instead of plain text) → you must modify this class.  
* Too many reasons to change → violates SRP.

Bird Example: With SRP

Let’s separate responsibilities into distinct classes:

```java
// ✅ Class focused only on bird data
class Bird {
    String name;

    Bird(String name) {
        this.name = name;
    }
}

// ✅ Handles saving birds (persistence responsibility)
class BirdRepository {
    void save(Bird bird) {
        System.out.println("Saving " + bird.name + " to database...");
    }
}

// ✅ Handles printing birds (presentation responsibility)
class BirdPrinter {
    void print(Bird bird) {
        System.out.println("Bird: " + bird.name);
    }
}

// Usage
public class SanctuaryApp {
    public static void main(String[] args) {
        Bird eagle = new Bird("Eagle");

        BirdRepository repo = new BirdRepository();
        BirdPrinter printer = new BirdPrinter();

        repo.save(eagle);
        printer.print(eagle);
    }
}
```

✅ Advantages:

* Bird only cares about representing a bird.  
* BirdRepository cares about saving birds.  
* BirdPrinter cares about displaying birds.  
* Each class has exactly one reason to change.

#### **Real-World Analogy** {#real-world-analogy}

Think of a bird sanctuary team:

* A bird keeper looks after the bird’s well-being.  
* A scientist records data about the bird.  
* A tour guide talks about the bird to visitors.  
* You wouldn’t expect one person to do all of these jobs at once — it would be inefficient and error-prone.

#### **Summary** {#summary}

* Without SRP → one Bird class does everything (data, persistence, printing).  
* With SRP → responsibilities are split: Bird, BirdRepository, BirdPrinter.  
* This makes the system modular, testable, and easier to extend.

### **O - Open Closed Principle** {#o---open-closed-principle}

The Open/Closed Principle says:

**A class should be open for extension but closed for modification.**

That means:

* You should be able to add new functionality (extend behavior)  
* Without having to change the existing, stable code (modify classes that are already tested and deployed).

Bird Example: Without OCP

```java
class BirdInfoPrinter {
    void printInfo(String birdType) {
        if (birdType.equals("Eagle")) {
            System.out.println("This is an Eagle");
        } else if (birdType.equals("Condor")) {
            System.out.println("This is a Condor");
        } else {
            System.out.println("Unknown bird");
        }
    }
}
```

❌ Problems:

* If you add a new bird (say Parrot), you must go back and edit BirdInfoPrinter.  
* Every new bird requires modifying old code — violating OCP.  
* Over time, the class becomes cluttered with if/else or switch statements.

Bird Example: With OCP

Instead, you design an abstract class (or interface) Bird that defines the behavior you want to extend:

```java
abstract class Bird {
    abstract String getType();
}

class Eagle extends Bird {
    String getType() { return "Eagle"; }
}

class Condor extends Bird {
    String getType() { return "Condor"; }
}

class BirdInfoPrinter {
    void printInfo(List<Bird> birds) {
        for (Bird b : birds) {
            System.out.println("This is a " + b.getType());
        }
    }
}
```

✅ Advantages:

* BirdInfoPrinter no longer needs to know about specific bird types.  
* To add a new bird (say Parrot), you extend the system with a new subclass:

```java
class Parrot extends Bird {
    String getType() { return "Parrot"; }
}
```

#### **Real-World Analogy** {#real-world-analogy-1}

Think of BirdInfoPrinter like a bird-watching guidebook:

* Instead of rewriting the whole book every time a new bird species is discovered, you just add a new bird entry card to the guide.  
* The guidebook’s structure (the printing system) doesn’t change.

#### **Summary** {#summary-1}

* Open for extension → you can add new birds like Parrot, Falcon, Owl.  
* Closed for modification → BirdInfoPrinter doesn’t need changes when new bird types appear.  
* This keeps your codebase stable, flexible, and less error-prone as it grows.

### **L \- Liskov Substitution Principle** {#l---liskov-substitution-principle}

Subtypes must be substitutable for their base types.

```java
class Bird {
    String fly() { return "Flying high"; }
}

class Eagle extends Bird {
    @Override
    String fly() { return "Eagle soars gracefully"; }
}

class Condor extends Bird {
    @Override
    String fly() { return "Condor glides on thermals"; }
}

// Works fine: any Bird reference can hold an Eagle or Condor
public class Sanctuary {
    public static void main(String[] args) {
        Bird b1 = new Eagle();
        Bird b2 = new Condor();
        System.out.println(b1.fly());
        System.out.println(b2.fly());
    }
}
```

```text
Eagle soars gracefully
Condor glides on thermals
```

#### **Real-World Analogy** {#real-world-analogy-2}

Think of a bird sanctuary app:

* If you say “all birds can fly,” then adding a penguin breaks your program (like UnsupportedOperationException).  
* By respecting LSP, you model reality better: not all birds fly, so only FlyableBirds should promise fly().

#### **Summary** {#summary-2}

* Subtypes (Eagle, Condor) can replace Bird safely → fly() always works.  
* Avoid lying subclasses (like a Penguin that throws error in fly()).  
* LSP keeps polymorphism safe and ensures your code doesn’t break when you add new types.

### **I \- Interface Segregation Principle** {#i---interface-segregation-principle}

The Interface Segregation Principle says:

**No client should be forced to depend on methods it does not use.**

That means:

* Instead of one fat interface with many unrelated methods, create smaller, focused interfaces.  
* Classes should only implement what makes sense for them.

Why It Matters

* Prevents unnecessary code (empty or dummy method implementations).  
* Keeps interfaces clean and focused.  
* Makes the system more flexible — different birds can implement different capabilities without being forced into irrelevant ones.

Bird Example: Without ISP

Imagine we create one big BirdActions interface:

```java
// ❌ Fat interface
interface BirdActions {
    void fly();
    void swim();
    void scavenge();
}

class Eagle implements BirdActions {
    public void fly() { System.out.println("Eagle soars!"); }
    public void swim() { /* ??? Eagles don't swim */ }
    public void scavenge() { /* ??? not typical */ }
}

class Condor implements BirdActions {
    public void fly() { System.out.println("Condor glides!"); }
    public void swim() { /* ??? Condors don't swim */ }
    public void scavenge() { System.out.println("Condor scavenges!"); }
}
```

❌ Problems:

* Eagle is forced to implement swim() even though it doesn’t make sense.  
* Condor may or may not scavenge, but the method is still mandatory.  
* Violates ISP — classes depend on methods they don’t need.

Bird Example: With ISP

Instead, split the big interface into smaller, focused ones:

```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

interface Scavenger {
    void scavenge();
}

class Eagle implements Flyable {
    public void fly() { System.out.println("Eagle soars!"); }
}

class Condor implements Flyable, Scavenger {
    public void fly() { System.out.println("Condor glides!"); }
    public void scavenge() { System.out.println("Condor scavenges!"); }
}
```

✅ Advantages:

* Eagle only implements what it can actually do: Flyable.  
* Condor implements both Flyable and Scavenger.  
* If we later add a Penguin, it could just implement Swimmable.  
* Each bird only depends on methods that are relevant.

#### **Real-World Analogy** {#real-world-analogy-3}

Think of a bird-care system in a sanctuary:

* You wouldn’t hand every bird the same care checklist (“teach to fly, teach to swim, teach to scavenge”).  
* Instead, you’d have separate specialized routines.  
* A penguin would follow the swimming routine, not the flying one.

#### **Summary** {#summary-3}

* Without ISP → one bloated interface forces irrelevant methods (Eagle having to “swim”).  
* With ISP → smaller, focused interfaces (Flyable, Swimmable, Scavenger) let each bird implement only what it actually does.  
* This makes code cleaner, easier to maintain, and closer to reality.

### **D \- Dependency Inversion Principle** {#d---dependency-inversion-principle}

The Dependency Inversion Principle says:

* **High-level modules should not depend on low-level modules. Both should depend on abstractions.**  
* **Abstractions should not depend on details. Details should depend on abstractions.**

That means:

* High-level logic (big-picture features) shouldn’t be tightly coupled to specific implementations.  
* Instead, both depend on interfaces/abstract classes, so swapping implementations is easy.

Why It Matters

* Prevents “hard-wiring” your system to specific classes (e.g., only Eagle sounds).  
* Makes it easy to swap or extend functionality (plug in a Condor, Parrot, or Duck later).  
* Enables unit testing (you can mock interfaces instead of needing real birds).

Bird Example: Without DIP

```java
// ❌ High-level class depends on concrete class
class BirdNotifier {
    private Eagle eagle;  // tightly coupled

    public BirdNotifier() {
        this.eagle = new Eagle();
    }

    public void alert() {
        System.out.println("Eagle says: Screech!");
    }
}

class Eagle {
    public void makeSound() {
        System.out.println("Screech!");
    }
}
```

❌ Problems:

* BirdNotifier can only ever use Eagle.  
* If we want a Condor, we must modify BirdNotifier.  
* Breaks Open/Closed and Dependency Inversion.

Bird Example: With DIP

Introduce an abstraction (BirdSound) that both Eagle and Condor can implement:

```java
// Abstraction
interface BirdSound {
    void makeSound();
}

// Low-level implementations
class EagleSound implements BirdSound {
    public void makeSound() {
        System.out.println("Screech!");
    }
}

class CondorSound implements BirdSound {
    public void makeSound() {
        System.out.println("Croak!");
    }
}

// High-level class depends on abstraction, not concrete details
class BirdNotifier {
    private BirdSound sound;

    public BirdNotifier(BirdSound sound) {
        this.sound = sound;
    }

    public void alert() {
        sound.makeSound();
    }
}

// Usage
public class SanctuaryApp {
    public static void main(String[] args) {
        BirdNotifier eagleNotifier = new BirdNotifier(new EagleSound());
        eagleNotifier.alert();  // Screech!

        BirdNotifier condorNotifier = new BirdNotifier(new CondorSound());
        condorNotifier.alert(); // Croak!
    }
}
```

✅ Advantages:

BirdNotifier is no longer tied to Eagle or Condor.

* You can pass in any BirdSound implementation — even a MockBirdSound for testing.  
* Adding a new bird doesn’t require touching BirdNotifier at all.

#### **Real-World Analogy** {#real-world-analogy-4}

Think of a bird alarm system in a sanctuary:

* Instead of wiring the system directly to a specific eagle, you design it to accept any bird sound.  
* That way, you can “plug in” different species without changing the wiring.

#### **Summary** {#summary-4}

* Without DIP → High-level BirdNotifier directly depends on Eagle.  
* With DIP → Both BirdNotifier (high-level) and EagleSound/CondorSound (low-level) depend on BirdSound (abstraction).  
* This makes the design flexible, extensible, and testable.

## **REST Operations** {#rest-operations}

REST operations, which stands for **Representational State Transfer**, are a set of standardized **HTTP methods** used to interact with resources on a server. The most common REST operations map to the **CRUD** (Create, Read, Update, Delete) paradigm.

### **Create: POST** {#create:-post}

The `POST` method is used to **create a new resource** on the server. When you send a `POST` request, you include data in the body of the request, and the server typically responds with a `201 Created` status code and the URI of the newly created resource.

### **Read: GET** {#read:-get}

The `GET` method is used to **retrieve or read** a representation of a resource. `GET` requests should not have a body and should not change the state of the server. They are considered "safe" and **idempotent**, meaning you can call them multiple times without causing any side effects.

### **Update: PUT and PATCH** {#update:-put-and-patch}

The `PUT` and `PATCH` methods are used to **update an existing resource**.

* `PUT` is for **complete replacement**. It's used to replace an entire resource with the data provided in the request body. If the resource doesn't exist, a `PUT` request can sometimes create it.  
* `PATCH` is for **partial updates**. It's used to apply a partial modification to a resource. For example, if you only want to update a user's email address, you would use `PATCH` and only send the new email value.

### **Delete: DELETE** {#delete:-delete}

The `DELETE` method is used to **delete a specified resource** from the server. A successful `DELETE` request typically returns a `204 No Content` status code.

## **REST Response Codes** {#rest-response-codes}

### **1xx: Informational** {#1xx:-informational}

These codes indicate that the request was received and is continuing. They are provisional responses.

* **100 Continue**: The server has received the request headers and the client should proceed to send the request body.  
* **101 Switching Protocols**: The client has asked the server to switch protocols and the server has agreed to do so.

### **2xx: Success** {#2xx:-success}

These codes indicate that the request was successfully received, understood, and accepted.

* **200 OK**: The request has succeeded. This is the most common status code.  
* **201 Created**: The request has been fulfilled and a new resource has been created as a result.  
* **202 Accepted**: The request has been accepted for processing, but the processing has not been completed.  
* **204 No Content**: The server successfully processed the request, but is not returning any content.  
* **205 Reset Content**: The server successfully processed the request, but the client should reset the document view.  
* **206 Partial Content**: The server is delivering only part of the resource due to a range header sent by the client.

### **3xx: Redirection** {#3xx:-redirection}

These codes indicate that further action needs to be taken by the client to fulfill the request.

* **301 Moved Permanently**: The requested resource has been assigned a new permanent URI.  
* **302 Found**: The requested resource resides temporarily under a different URI.  
* **303 See Other**: The response to the request can be found under a different URI and should be retrieved using a GET method.  
* **304 Not Modified**: The resource has not been modified since the version specified by the request headers. The client can use a cached copy.  
* **307 Temporary Redirect**: The request should be repeated with another URI; however, future requests should still use the original URI.  
* **308 Permanent Redirect**: The request and all future requests should be repeated using a new URI.

### **4xx: Client Error** {#4xx:-client-error}

These codes are for situations where the client appears to have made an error.

* **400 Bad Request**: The server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing).  
* **401 Unauthorized**: The request requires user authentication.  
* **403 Forbidden**: The server understood the request, but is refusing to fulfill it.  
* **404 Not Found**: The server cannot find the requested resource.  
* **405 Method Not Allowed**: The method specified in the request is not allowed for the resource identified by the URI.  
* **409 Conflict**: The request could not be completed due to a conflict with the current state of the target resource.  
* **410 Gone**: The requested resource is no longer available at the server and no forwarding address is known.  
* **415 Unsupported Media Type**: The server refuses to accept the request because the payload format is not supported by the resource.

### **5xx: Server Error** {#5xx:-server-error}

These codes indicate that the server failed to fulfill an apparently valid request.

* **500 Internal Server Error**: The server encountered an unexpected condition that prevented it from fulfilling the request. This is a generic "catch-all" status code.  
* **501 Not Implemented**: The server does not support the functionality required to fulfill the request.  
* **502 Bad Gateway**: The server, while acting as a gateway or proxy, received an invalid response from an inbound server it accessed in attempting to fulfill the request.  
* **503 Service Unavailable**: The server is currently unable to handle the request due to a temporary overload or scheduled maintenance.  
* **504 Gateway Timeout**: The server, while acting as a gateway or proxy, did not receive a timely response from the upstream server specified by the URI.

## **Object-Oriented design** {#object-oriented-design}


The main concepts of Object-Oriented Programming (OOP) are:

### **Encapsulation** {#encapsulation}

Bundling data (attributes) and the methods that operate on that data into a single unit, a class. It hides the internal state of an object from the outside world and only allows access through a public interface.

**Definition**: Wrapping attributes (fields) and behaviors (methods) into one class, and controlling access with visibility modifiers (private, protected, public).

**Example**:  
The Bird class has a name field that is private, and we access it through a getter (getName()), not directly.

```java
private String name;
public String getName() { return name; }
```

This keeps the internal state safe and controlled.

### **Inheritance** {#inheritance}

A mechanism that allows a new class (subclass) to inherit properties and behaviors (methods) from an existing class (superclass). This promotes code reuse and establishes a hierarchical relationship between classes.

**Definition**: A way to form new classes from existing ones, reusing attributes and methods.

**Example**:  
Eagle and Condor inherit from Bird. They automatically get name and can use getName() without redefining them.

```java
class Eagle extends Bird { ... }
class Condor extends Bird { ... }
```

This prevents repeating common bird logic in every subclass.

### **Polymorphism** {#polymorphism}

The ability of an object to take on many forms. It allows a single interface to be used for different data types. For example, a single method name can be used to perform different tasks depending on the object it's called on.

**Definition**: The ability for the same method or interface to take different forms depending on the object.

**Example:**  
Both Eagle and Condor override fly() and makeSound().  
At runtime, the method called depends on the object type, even if we reference them as Bird:

```java
Bird eagle = new Eagle("Golden Eagle");
Bird condor = new Condor("Andean Condor");

eagle.fly();   // Eagle's version
condor.fly();  // Condor's version
```

Same method names, different behaviors.

### **Abstraction** {#abstraction}

The process of hiding complex implementation details and showing only the essential features of an object. It focuses on what an object does rather than how it does it. This simplifies the complexity of a system for the user.

**Definition**: Showing only essential details while hiding implementation.

In your example:

Bird is abstract and defines what a bird does (e.g., makeSound()) without saying how.  
Each subclass provides the implementation:

```java
public abstract void makeSound();
```

Users of Bird don’t care how each bird makes a sound, only that it can.

### **Interfaces** {#interfaces}

An interface in Java is a reference type that defines a **contract** for classes. It specifies what methods a class must implement without providing the actual implementation (except for default and static methods).

When to use interfaces

* When you want to define a contract that multiple unrelated classes can follow  
* When you need multiple inheritance capabilities  
* When you want to define behavior that can be implemented by any class  
* When you want to create pluggable components  
* When you're designing APIs or frameworks

### **Abstract** {#abstract}

An abstract class in Java is a class that cannot be instantiated directly and is designed to be a base class for other classes. It can contain both abstract methods (methods without implementation) and concrete methods (methods with implementation). It serves as a blueprint for related classes.

When to use Abstract classes

* When you have related classes that share common functionality  
* When you want to provide some default behavior while forcing specific implementations  
* When you need to maintain state (instance variables) in the base class  
* When you have a clear "is-a" relationship between classes  
* When you want to control the inheritance hierarchy  
* When you need constructors in your base class  
* When you want to provide common utility methods to subclasses

### **Interface and Abstract example** {#interface-and-abstract-example}

```java
// The Flyable interface
interface Flyable {
    void fly(); // contract: all flyable creatures must implement fly()
}

// The abstract Bird class
abstract class Bird implements Flyable {
    private String name;

    public Bird(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    // Abstract method: each bird has its own sound
    public abstract void makeSound();
}

// Condor class
class Condor extends Bird {
    public Condor(String name) {
        super(name);
    }

    @Override
    public void fly() {
        System.out.println(getName() + " soars slowly at high altitudes.");
    }

    @Override
    public void makeSound() {
        System.out.println(getName() + " makes a hissing sound.");
    }
}

// Eagle class
class Eagle extends Bird {
    public Eagle(String name) {
        super(name);
    }

    @Override
    public void fly() {
        System.out.println(getName() + " flies swiftly and powerfully.");
    }

    @Override
    public void makeSound() {
        System.out.println(getName() + " screeches loudly.");
    }
}

// Example usage
public class Main {
    public static void main(String[] args) {
        Bird condor = new Condor("Andean Condor");
        Bird eagle = new Eagle("Golden Eagle");

        condor.fly();
        condor.makeSound();

        eagle.fly();
        eagle.makeSound();
    }
}
```

```text
Andean Condor soars slowly at high altitudes.
Andean Condor makes a hissing sound.
Golden Eagle flies swiftly and powerfully.
Golden Eagle screeches loudly.
```

## **Leetcode**  {#leetcode}

## **Java Collections Framework** {#java-collections-framework}

Main Ideas

* List: Ordered, allows duplicates  
* Set: No duplicates  
* Queue / Deque: FIFO, LIFO, or priority-based retrieval  
* Map: Key-value pairs (not a Collection, but related)

### **List (ordered, index-based, allows duplicates)** {#list-(ordered,-index-based,-allows-duplicates)}

#### **ArrayList** {#arraylist}

Resizable array. Fast access. Slower middle inserts/removals.

#### **LinkedList** {#linkedlist}

Nodes linked. Fast inserts/removals. Slow access by index.

#### **Vector** {#vector}

Thread-safe ArrayList (legacy).

#### **Stack** {#stack}

Legacy stack. Use ArrayDeque instead.

### **Set (unique elements, no duplicates)** {#set-(unique-elements,-no-duplicates)}

#### **HashSet** {#hashset}

Fast operations. No order guaranteed.

#### **LinkedHashSet** {#linkedhashset}

Keeps insertion order.

#### **TreeSet** {#treeset}

Automatically sorted elements. Based on Red-Black Tree.

### **Queue / Deque (FIFO or double-ended, or priority)** {#queue-/-deque-(fifo-or-double-ended,-or-priority)}

#### **ArrayDeque** {#arraydeque}

Recommended queue or stack replacement. Very fast.

#### **PriorityQueue** {#priorityqueue}

Min-heap by default. Removes smallest first.

#### **LinkedList** {#linkedlist-1}

Can act as queue/deque. Slower than ArrayDeque.

*Deque \= double-ended queue (add and remove from both ends)*

### **Map (key-value lookup)** {#map-(key-value-lookup)}

#### **HashMap** {#hashmap}

Fast lookup. No order guaranteed.

#### **LinkedHashMap** {#linkedhashmap}

Maintains insertion order.

#### **TreeMap** {#treemap}

Keys sorted automatically. Red-Black Tree.

#### **Hashtable** {#hashtable}

Old synchronized map. Avoid in new code.

#### **ConcurrentHashMap** {#concurrenthashmap}

Thread-safe and efficient for concurrency.

### **Quick Selection Guide** {#quick-selection-guide}

Just read down until one matches:

* I want **fast lookups by index** → **ArrayList**  
* I want **fast inserts/removals anywhere** → **LinkedList**  
* I want **no duplicates** → **HashSet**  
* I want **no duplicates \+ sorted** → **TreeSet**  
* I want a **queue** → **ArrayDeque**  
* I want a **stack** → **ArrayDeque** (not `Stack`)  
* I want to always remove **smallest/largest item** → **PriorityQueue**  
* I want **key-value storage** → **HashMap**  
* I want key-value but **sorted keys** → **TreeMap**

