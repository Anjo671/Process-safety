# Code
The code is written in Structured text (ST) and spread over multipel units (POU's)

## 1. Global Variable List (GVL)

```pascal
VAR_GLOBAL
    bRequestCoffee : BOOL; // Student wants coffe.
    bCoffeeReady   : BOOL; // Coffe done.
END_VAR
```

## 2. Machine Control (PRG_Machine)
The 2 tasks both use ENUM.



```pascal
TYPE E_StudentState :
(
    STUDYING := 0,   // student aan het leren (Timer loopt)
    ORDERING := 10,  // keuze op apparaat (GVL true maken)
    WAITING  := 20,  // Waiting on machine
    DRINKING := 30   // Koffie drinken 
);
END_TYPE
```

```pascal
TYPE E_MachineState :
(
    IDLE        := 0,   // Wait
    CALCULATING := 10,  // call function
    BREWING     := 20,  // Making coffe
    NOTIFY      := 30   // coffe = done
);
END_TYPE
```

## 3. POUs
1.  FC_CalcTime
```pascal
FUNCTION FC_CalcTime : TIME
VAR_INPUT
    iInput : INT;
END_VAR
VAR
END_VAR
```
```pascal
IF iInput <= 0 THEN
    FC_CalcTime := T#2S;
ELSE
    FC_CalcTime := INT_TO_TIME(iInput * 1500); 
END_IF
//Berekening tijd
```
2.  PRG_Machine
```pascal
PROGRAM PRG_Machine
VAR
    eState : E_MachineState; // status (Idle - Calculatie - Brewing - notify)
    
    // Variabelen
    tZetTijd      : TIME;
    iKoffieSterkte: INT := 4; // Standaard sterkte koffie zodat er gecommuniceerd wordt
    bPompAan      : BOOL;
    
    // Hoelang voor koffie
    fbBrewTimer   : TON;
	
	iInput: INT;
END_VAR
```
```pascal
CASE eState OF

    E_MachineState.IDLE:
        // Rust					 Alles uit
        bPompAan := FALSE;
        GVL_Data.bCoffeeReady := FALSE;
        
        // Wachten op melding van student
        IF GVL_Data.bRequestCoffee = TRUE THEN
            eState := E_MachineState.CALCULATING;
        END_IF

    E_MachineState.CALCULATING:
        // info krijgen voor tijd 
        tZetTijd := FC_CalcTime(iInput := iKoffieSterkte);
        
        // is tijd logisch dan gebruiken.
        IF tZetTijd > T#0S THEN
            eState := E_MachineState.BREWING;
        END_IF

    E_MachineState.BREWING:
        bPompAan := TRUE;
        // timer startem
        fbBrewTimer(IN := TRUE, PT := tZetTijd);
        
        
        IF fbBrewTimer.Q THEN
            fbBrewTimer(IN := FALSE); 
            bPompAan := FALSE;        
            eState := E_MachineState.NOTIFY;
        END_IF

    E_MachineState.NOTIFY:
        // Koffie klaar melding.
        GVL_Data.bCoffeeReady := TRUE;
        
           IF GVL_Data.bRequestCoffee = FALSE THEN
            // Terug naar begin
            eState := E_MachineState.IDLE;
        END_IF

END_CASE
```
3.  PRG_Student
```pascal
PROGRAM PRG_Student
VAR
    eState : E_StudentState; // status student(studeren - wachten - drinken)
    
    // timer
    fbStudyTimer : TON;      
    fbDrinkTimer : TON;      
    
    //Knop zodat timer(studytimer) meteen voorbij gaat.
    Koffietijd : BOOL := FALSE;
END_VAR
```
```pascal
CASE eState OF

    E_StudentState.STUDYING:
        
        fbStudyTimer(IN := TRUE, PT := T#5S);
        
        // Tijd of knop
        IF fbStudyTimer.Q OR Koffietijd THEN
            fbStudyTimer(IN := FALSE); // reset
            Koffietijd := FALSE;             // reset
            eState := E_StudentState.ORDERING;
        END_IF

    E_StudentState.ORDERING:
        // In GVL staat wens koffie 
        GVL_Data.bRequestCoffee := TRUE;
        
        //apparaat werkt
        eState := E_StudentState.WAITING;

    E_StudentState.WAITING:
        //wacht op machine
        
        IF GVL_Data.bCoffeeReady = TRUE THEN
            eState := E_StudentState.DRINKING;
        END_IF

    E_StudentState.DRINKING:
        
        GVL_Data.bRequestCoffee := FALSE;
        
       //drink tijd
        fbDrinkTimer(IN := TRUE, PT := T#3S);
        
       
        IF fbDrinkTimer.Q = TRUE THEN //koffie op
            fbDrinkTimer(IN := FALSE); // Reset timer
            
           //reset cycles
            eState := E_StudentState.STUDYING;
        END_IF

END_CASE
```
