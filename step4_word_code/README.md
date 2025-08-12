BEGIN Automated_Pet_Feeder
 // Step 1: Load feeding schedule
    LOAD feedingTimes FROM memory
    INITIALIZE hopperLevelSensor, bowlWeightSensor, motor, alertSystem
    LOOP forever:
        currentTime ← GET current system time
        // Step 2: Check if it’s feeding time
        IF currentTime MATCHES feedingTimes THEN
            // Step 3: Check hopper food level
            IF hopperLevelSensor > MIN_HOPPER_LEVEL THEN
                // Step 4: Dispense food
                ACTIVATE motor FOR DISPENSE_DURATION
                WAIT DISPENSE_CONFIRM_DELAY
                // Step 5: Confirm food dispensed
                IF bowlWeightSensor > MIN_BOWL_WEIGHT THEN
                    // Step 6: Monitor food consumption
                    START timer FOR CONSUMPTION_CHECK_DELAY
                    WAIT until timer EXPIRES
                    IF (initialBowlWeight - currentBowlWeight) ≥ 80% THEN
                        LOG "Meal consumed successfully"
                    ELSE
                        SEND alert "Food not eaten"
                        LOG "Food not eaten"
                    ENDIF
                ELSE
                    SEND alert "No food dispensed"
                    LOG "No food dispensed"
                ENDIF
            ELSE
                SEND alert "Low hopper level"
                LOG "Low hopper level"
            ENDIF
        ENDIF
    END LOOP
END Automated_Pet_Feeder
