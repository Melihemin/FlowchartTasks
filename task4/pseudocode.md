// UNIVERSITY COURSE REGISTRATION SYSTEM (TASK 4)
// Logic for handling high-complexity constraints and advisor approval

BEGIN Registration_System

    // --- STEP 1: AUTHENTICATION ---
    INPUT studentID, password
    validated = CALL AuthenticateUser(studentID, password)
    
    IF NOT validated THEN
        DISPLAY "Invalid Credentials. Access Denied."
        EXIT
    END IF

    // --- STEP 2: DATA INITIALIZATION ---
    studentData = FETCH StudentProfile(studentID) // Includes GPA and CompletedCourses
    currentSchedule = FETCH CurrentEnrollment(studentID)
    totalCredits = CALCULATE_SUM(currentSchedule.credits)
    registrationActive = TRUE

    // --- STEP 3: MAIN REGISTRATION LOOP ---
    WHILE registrationActive IS TRUE
        DISPLAY "Current Credits: " + totalCredits + " / 35"
        DISPLAY "1. View/Add Course | 2. Drop Course | 3. Finish"
        INPUT choice

        IF choice == 1 THEN
            DISPLAY AvailableCourses
            INPUT selectedCourseID
            course = FETCH CourseDetails(selectedCourseID)

            // --- NESTED VALIDATION GATES ---
            
            // GATE 1: Capacity Check
            IF course.Enrolled >= course.Capacity THEN
                DISPLAY "Error: Course is full (Quota reached)."
            
            // GATE 2: Prerequisite Check
            ELSE IF NOT studentData.CompletedCourses.CONTAINS(course.PrerequisiteID) THEN
                DISPLAY "Error: You have not completed " + course.PrerequisiteName

            // GATE 3: Schedule Conflict Check
            ELSE IF CHECK_TIME_CONFLICT(course.TimeSlot, currentSchedule) THEN
                DISPLAY "Error: This course overlaps with your current schedule."

            // GATE 4: Credit Limit Check
            ELSE IF (totalCredits + course.Credits) > 35 THEN
                DISPLAY "Error: Adding this would exceed the 35-credit limit."

            ELSE
                // ALL GATES PASSED
                ADD course TO currentSchedule
                totalCredits = totalCredits + course.Credits
                DISPLAY "Course added successfully."
            END IF

        ELSE IF choice == 2 THEN
            INPUT dropCourseID
            REMOVE dropCourseID FROM currentSchedule
            totalCredits = totalCredits - FETCH_CREDITS(dropCourseID)
            DISPLAY "Course removed."

        ELSE IF choice == 3 THEN
            registrationActive = FALSE
        END IF
    END WHILE

    // --- STEP 4: ADVISOR APPROVAL & FINALIZATION ---
    DISPLAY "--- Registration Summary ---"
    FOR EACH item IN currentSchedule
        DISPLAY item.Name + " (" + item.Credits + " credits)"
    ENDFOR

    // Conditional rule for low GPA
    IF studentData.GPA < 2.5 THEN
        registrationStatus = "PENDING_ADVISOR_APPROVAL"
        DISPLAY "Notice: Your GPA is below 2.5. Advisor approval is required."
    ELSE
        registrationStatus = "REGISTERED"
        DISPLAY "Registration confirmed successfully."
    END IF

    SAVE_TO_DATABASE(studentID, currentSchedule, registrationStatus)

END Registration_System