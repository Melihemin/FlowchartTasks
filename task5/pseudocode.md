// ==========================================
// TASK 5: SMART HOME SECURITY SYSTEM
// ==========================================

START System
DEFINE AlarmLevel = 0
DEFINE SystemActive = TRUE

// MAIN INFINITE LOOP
WHILE (SystemActive is TRUE) DO

    // 1. SENSOR READING LOOP (Continuous)
    READ MotionSensorStatus
    READ DoorWindowSensorStatus
    
    // Check if any breach is detected
    IF (MotionSensorStatus IS TRIGGERED) OR (DoorWindowSensorStatus IS TRIGGERED) THEN
        
        // 2. FALSE ALARM CHECK
        // Check if the homeowner's smartphone is within the Geofence or Home Wi-Fi
        IF (isHomeownerPresent() IS TRUE) THEN
            PRINT "Homeowner detected: Ignoring trigger."
            WAIT (Short duration)
            CONTINUE // Skip back to the start of the loop
        END IF

        // 3. THREAT RESPONSE
        ACTIVATE IP_Camera
        RECORD Video_Clip
        
        // 4. DETERMINE ALARM LEVEL (Conditions)
        IF (MotionSensorStatus AND DoorWindowSensorStatus) THEN
            SET AlarmLevel = 3 // High: Full breach
        ELSE IF (DoorWindowSensorStatus) THEN
            SET AlarmLevel = 2 // Medium: Physical entry point opened
        ELSE
            SET AlarmLevel = 1 // Low: Motion only (could be a pet or outside movement)
        END IF

        // 5. SEND NOTIFICATIONS (Multi-channel)
        CALL Send_SMS_Alert(AlarmLevel)
        CALL Send_App_Push_Notification(AlarmLevel)
        CALL Send_Security_Email(AlarmLevel, Attached_Camera_Clip)

        // 6. WAIT AND RE-CHECK (Loop Maintenance)
        PRINT "Alert active. Monitoring for Reset..."
        WAIT (10 Seconds) 

        // 7. RESET ALARM OR CONTINUE
        IF (User_Reset_Command_Received() IS TRUE) THEN
            SET AlarmLevel = 0
            DEACTIVATE IP_Camera
            PRINT "System Reset: Returning to monitoring mode."
        ELSE
            PRINT "No reset received: Continuing alert state."
            // Note: The loop naturally cycles back to re-check sensors
        END IF

    ELSE
        // System is active but no sensors triggered
        SET AlarmLevel = 0
        PERFORM_IDLE_SYSTEM_DIAGNOSTICS()
    END IF

    // Small delay to prevent CPU overheating/loop saturation
    WAIT (100 Milliseconds) 

END WHILE

END System