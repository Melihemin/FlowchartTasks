// MAIN SYSTEM CONTROLLER
BEGIN Hospital_System
    SET system_active = TRUE
    
    WHILE system_active IS TRUE DO
        // 1. Identity Verification
        DISPLAY "Please enter your National ID Number:"
        INPUT patient_id
        
        IF CALL validate_identity(patient_id) IS FALSE THEN
            DISPLAY "Error: Identity could not be verified."
            CONTINUE // Restart loop for new input
        ENDIF

        // 2. Main Menu (Action Selection)
        DISPLAY "Main Menu: 1-Make Appointment, 2-View Test Results, 3-Exit"
        INPUT user_action

        SWITCH user_action:
            CASE 1:
                CALL Module_Make_Appointment(patient_id)
            CASE 2:
                CALL Module_View_Results(patient_id)
            CASE 3:
                SET system_active = FALSE
                DISPLAY "Thank you for using our system."
            DEFAULT:
                DISPLAY "Invalid selection. Please try again."
        END SWITCH

        // 3. Repeat Loop Check
        IF system_active IS TRUE THEN
            DISPLAY "Would you like to perform another action? (Y/N)"
            INPUT repeat_choice
            IF repeat_choice == 'N' OR 'n' THEN
                SET system_active = FALSE
            ENDIF
        ENDIF
    END WHILE
END

// ---------------------------------------------------------
// MODULE 1: APPOINTMENT SYSTEM
// ---------------------------------------------------------
FUNCTION Module_Make_Appointment(patient_id)
    SET booking_complete = FALSE
    
    WHILE booking_complete IS FALSE DO
        DISPLAY "Available Clinics: Cardiology, ENT, Pediatrics, General Surgery"
        INPUT selected_clinic
        
        DISPLAY CALL get_doctor_list(selected_clinic)
        INPUT selected_doctor
        
        DISPLAY CALL get_available_slots(selected_doctor)
        INPUT selected_time
        
        DISPLAY "Confirm Appointment for " + selected_time + "? (Y/N)"
        INPUT confirmation
        
        IF confirmation == 'Y' THEN
            CALL save_appointment(patient_id, selected_doctor, selected_time)
            CALL send_sms_notification(patient_id, "Your appointment is confirmed for " + selected_time)
            DISPLAY "Success: Appointment booked and SMS sent."
            SET booking_complete = TRUE
        ELSE
            DISPLAY "Process restarted."
        ENDIF
    END WHILE
ENDFUNCTION

// ---------------------------------------------------------
// MODULE 2: LABORATORY RESULTS SYSTEM
// ---------------------------------------------------------
FUNCTION Module_View_Results(patient_id)
    // Check if any tests are associated with the ID
    IF CALL check_tests_exist(patient_id) IS FALSE THEN
        DISPLAY "No lab records found for this ID."
        RETURN
    ENDIF

    // Check if the lab has finished processing them
    IF CALL check_results_ready(patient_id) IS TRUE THEN
        DISPLAY CALL fetch_result_data(patient_id)
        
        DISPLAY "Would you like to download results as PDF? (Y/N)"
        INPUT download_choice
        IF download_choice == 'Y' THEN
            CALL generate_pdf_report(patient_id)
            DISPLAY "Download started..."
        ENDIF
    ELSE
        DISPLAY "Status: Results are not ready yet. Please check back later."
    ENDIF
ENDFUNCTION