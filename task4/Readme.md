# Task 4: University Course Registration System

## Overview

This task models a **University Course Registration System** that handles student authentication, course enrollment with multi-gate validation, course dropping, and GPA-based advisor approval. The system enforces complex constraints including capacity limits, prerequisite checks, schedule conflict detection, and credit limits.

---

## Files

| File | Description |
|------|-------------|
| `pseudocode.md` | Full pseudocode with nested validation gates and advisor approval logic |
| `flowchart.mmd` | Mermaid flowchart diagram with sequential validation gates |
| `flowchart.dot` | Graphviz DOT diagram with clustered registration logic engine |
| `llm_conversation.txt` | Link to the LLM conversation used to generate this task |

---

## System Flow

The system follows **4 main steps**:

### Step 1: Authentication
- Student inputs ID and password
- System validates credentials against the university database
- Access denied on failure

### Step 2: Data Initialization
- Load student profile (GPA, completed courses)
- Fetch current enrollment and calculate total credits

### Step 3: Main Registration Loop
- **Add Course** — passes through 4 sequential validation gates
- **Drop Course** — removes course and updates credit count
- **Finish** — exits the registration loop

### Step 4: Advisor Approval & Finalization
- Display registration summary
- If GPA < 2.5 → status set to "Pending Advisor Approval"
- If GPA >= 2.5 → status set to "Registered"
- Save to database

---

## Validation Gates

| Gate | Check | Error Message |
|------|-------|---------------|
| Gate 1 | Course capacity full? | "Course is full (Quota reached)" |
| Gate 2 | Prerequisites completed? | "You have not completed [prerequisite]" |
| Gate 3 | Schedule time conflict? | "This course overlaps with your current schedule" |
| Gate 4 | Total credits > 35? | "Adding this would exceed the 35-credit limit" |

---

## Pseudocode

```
BEGIN Registration_System

    // STEP 1: AUTHENTICATION
    INPUT studentID, password
    IF NOT AuthenticateUser(studentID, password) THEN EXIT

    // STEP 2: DATA INITIALIZATION
    studentData = FETCH StudentProfile(studentID)
    currentSchedule = FETCH CurrentEnrollment(studentID)
    totalCredits = SUM(currentSchedule.credits)

    // STEP 3: MAIN REGISTRATION LOOP
    WHILE registrationActive IS TRUE
        INPUT choice (1: Add, 2: Drop, 3: Finish)

        IF choice == 1 THEN
            INPUT selectedCourseID
            // 4 VALIDATION GATES (sequential)
            IF course full → Error
            ELSE IF prereqs not met → Error
            ELSE IF time conflict → Error
            ELSE IF credits > 35 → Error
            ELSE → Add course, update credits

        ELSE IF choice == 2 THEN
            Remove course, update credits

        ELSE IF choice == 3 THEN
            EXIT loop
    END WHILE

    // STEP 4: ADVISOR APPROVAL
    IF studentData.GPA < 2.5 THEN
        status = "PENDING_ADVISOR_APPROVAL"
    ELSE
        status = "REGISTERED"
    END IF

    SAVE_TO_DATABASE(studentID, currentSchedule, status)
END
```

---

## Flowchart (Mermaid)

```mermaid
flowchart TD
    Start([Start Registration]) --> Login[Input ID & Password]
    Login --> Auth{Auth Success?}
    
    Auth -- No --> Denied[Exit: Access Denied]
    Auth -- Yes --> Init[Load Student GPA & History]
    
    Init --> Menu{Select Action}
    
    Menu -- "1. Add Course" --> View[Display Available Courses]
    View --> Select[Input Course ID]
    
    Select --> Gate1{Quota Full?}
    Gate1 -- Yes --> Error1[Display: Course Full] --> Menu
    Gate1 -- No --> Gate2{Prereqs Met?}
    
    Gate2 -- No --> Error2[Display: Missing Prereq] --> Menu
    Gate2 -- Yes --> Gate3{Time Conflict?}
    
    Gate3 -- Yes --> Error3[Display: Schedule Overlap] --> Menu
    Gate3 -- No --> Gate4{Total Credits > 35?}
    
    Gate4 -- Yes --> Error4[Display: Credit Limit Exceeded] --> Menu
    Gate4 -- No --> Success[Add Course & Update Total Credits] --> Menu
    
    Menu -- "2. Drop Course" --> Drop[Remove Course & Update Credits] --> Menu
    
    Menu -- "3. Finish" --> Summary[Display Registration Summary]
    
    Summary --> GPA{GPA < 2.5?}
    GPA -- Yes --> Pending[Status: Pending Advisor Approval]
    GPA -- No --> Registered[Status: Registered]
    
    Pending --> Save[Save to Database]
    Registered --> Save
    Save --> End([End Session])
```

---

## Flowchart (Graphviz DOT)

```dot
digraph UniversityRegistration {
    graph [rankdir=LR, fontname="Helvetica", nodesep=0.5];
    node [fontname="Helvetica", shape=rect, style=filled, fillcolor="#f9f9f9"];
    edge [fontname="Helvetica", fontsize=10];

    Student [shape=ellipse, fillcolor="#e1f5fe", label="Student User"];
    DB [shape=cylinder, fillcolor="#fff9c4", label="University DB\n(Courses/Grades)"];
    
    subgraph cluster_logic {
        label = "Registration Logic Engine";
        style = dashed;
        color = "#546e7a";
        
        Auth [label="Authentication"];
        V_Quota [label="Capacity Filter"];
        V_Prereq [label="Prerequisite Filter"];
        V_Conflict [label="Conflict Filter"];
        V_Credits [label="Credit Limit Filter"];
        GPA_Check [label="GPA Rule Engine"];
    }

    Student -> Auth [label="Login"];
    Auth -> DB [label="Verify"];
    
    DB -> V_Quota [label="Check Slots"];
    V_Quota -> V_Prereq [label="Pass"];
    V_Prereq -> V_Conflict [label="Pass"];
    V_Conflict -> V_Credits [label="Pass"];
    
    V_Credits -> DB [label="Commit Selection"];
    
    DB -> GPA_Check [label="Read GPA"];
    GPA_Check -> Student [label="Final Status\n(Registered vs Pending)"];

    V_Quota -> Student [label="Fail", color=red];
    V_Prereq -> Student [label="Fail", color=red];
}
```

---

## Key Features

| Feature | Description |
|---------|-------------|
| Multi-Gate Validation | 4 sequential checks before course enrollment is allowed |
| Credit Limit | Maximum 35 credits per registration |
| GPA-Based Approval | Students with GPA < 2.5 require advisor approval |
| Add/Drop Support | Full support for adding and dropping courses |
| Database Persistence | Final schedule saved with registration status |

---

## LLM Conversation

[View the LLM conversation on Gemini](https://gemini.google.com/share/87184209bde4)
