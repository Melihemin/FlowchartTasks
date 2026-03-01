START ATM_PROCESS
    WHILE Card_Inserted IS TRUE:
        SET pin_attempts = 0
        SET authenticated = FALSE
        
        // PIN Verification Loop
        WHILE pin_attempts < 3 AND authenticated IS FALSE:
            INPUT user_pin
            IF user_pin IS CORRECT:
                SET authenticated = TRUE
            ELSE:
                INCREMENT pin_attempts
                PRINT "Incorrect PIN"
        
        IF authenticated IS FALSE:
            PRINT "Card Blocked"
            EXIT SYSTEM
        
        // Transaction Loop
        DO:
            DISPLAY account_balance
            INPUT withdrawal_amount
            
            // Validation Checks
            IF withdrawal_amount > account_balance:
                PRINT "Error: Insufficient Balance"
            ELSE IF withdrawal_amount % 20 != 0:
                PRINT "Error: Amount must be a multiple of 20TL"
            ELSE IF withdrawal_amount > daily_limit:
                PRINT "Error: Daily limit exceeded"
            ELSE:
                // Success path
                DISPENSE cash
                PRINT receipt
                UPDATE account_balance
            
            INPUT "Would you like another transaction?" (choice)
        WHILE choice IS "YES"
        
        EJECT_CARD
        BREAK // Exit main loop
    END WHILE
END ATM_PROCESS