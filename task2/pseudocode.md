START E_COMMERCE_SYSTEM_PROCESS
    // SECTION 1: USER LOGIN
    START **USER_AUTHENTICATION**
        IF user_is_logged_in THEN
            PROCEED to PRODUCT_BROWSING
        ELSE
            DISPLAY login_screen
            INPUT credentials
            IF credentials_valid THEN
                SET user_session = ACTIVE
                PROCEED to PRODUCT_BROWSING
            ELSE
                DISPLAY "Invalid Login"
                RESTART USER_AUTHENTICATION
            END
        END
    END

    // SECTION 2: PRODUCT BROWSING & CART ADDITION
    START **PRODUCT_BROWSING**
        LOOP while browsing_is_active
            DISPLAY category_list
            IF category_selected THEN
                LOOP for each product in category_results
                    DISPLAY product_info
                    IF add_to_cart_clicked THEN
                        // Control Point: Initial Stock Check
                        IF stock_count > 0 THEN
                            ADD product_id TO cart_array
                            DISPLAY "Item added"
                        ELSE
                            DISPLAY "Item currently unavailable"
                        END
                    END
                END
            END
            IF view_cart_clicked THEN
                BREAK LOOP
            END
        END
    END

    // SECTION 3: CART MANAGEMENT
    START **CART_MANAGEMENT**
        LOOP while user_is_reviewing_cart
            DISPLAY cart_array
            CALCULATE running_subtotal
            
            IF change_quantity_clicked THEN
                INPUT new_quantity
                // Control Point: Stock Validation
                IF new_quantity <= available_stock THEN
                    UPDATE cart_item_quantity
                ELSE
                    SET quantity = available_stock
                    DISPLAY "Only " + available_stock + " units available"
                END
            END

            IF remove_item_clicked THEN
                DELETE item FROM cart_array
            END

            IF checkout_button_pressed THEN
                BREAK LOOP
            END
        END
    END

    // SECTION 4: CHECKOUT CALCULATIONS
    START **CHECKOUT_CALCULATION**
        // Control Point: Minimum Order Validation
        IF running_subtotal < 10 THEN
            DISPLAY "Minimum order amount is $10"
            RETURN to CART_MANAGEMENT
        END

        // Control Point: Discount Application
        IF promo_code_applied THEN
            IF promo_code_is_valid THEN
                SET discount_amount = (running_subtotal * discount_rate)
                SET running_subtotal = (running_subtotal - discount_amount)
            ELSE
                DISPLAY "Invalid code"
            END
        END

        // Control Point: Shipping Threshold
        IF running_subtotal >= 40 THEN
            SET shipping_fee = 0
            DISPLAY "Free Shipping Applied"
        ELSE
            SET shipping_fee = 5.99
        END

        SET final_total = running_subtotal + shipping_fee
    END

    // SECTION 5: PAYMENT & FINALIZATION
    START **PAYMENT_PROCESSING**
        DISPLAY final_total
        DISPLAY "Select Payment: [Credit Card, PayPal, Crypto]"
        INPUT payment_selection

        IF payment_selection_valid THEN
            // Control Point: Final Payment Authorization
            IF payment_gateway_status == "SUCCESS" THEN
                UPDATE inventory_database (SUBTRACT purchased_items)
                GENERATE order_id
                DISPLAY "Order Confirmed: " + order_id
                SEND_EMAIL_RECEIPT
            ELSE
                DISPLAY "Payment Denied"
                RETRY PAYMENT_PROCESSING
            END
        END
    END
END