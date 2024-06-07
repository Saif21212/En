; LC-3 Character Counter Program
; This program counts the frequency of each character in a user's full name.
; It reads the input, counts each character, and displays the frequencies.

.ORIG x3000

; Prompts the user for input
LEA R0, PROMPT
PUTS

; Read user input
LEA R1, INPUT_BUFFER
LD R2, BUFFER_SIZE
READ_CHAR_LOOP
    GETC
    BRz READ_CHAR_END
    STR R0, R1, #0
    ADD R1, R1, #1
    ADD R2, R2, #-1
    BRp READ_CHAR_LOOP
READ_CHAR_END
    AND R0, R0, #0 ; Null-terminate the string
    STR R0, R1, #0

; Initialize character counter array
LEA R2, CHAR_COUNT_ARRAY
LD R3, ARRAY_SIZE
CLEAR_COUNTERS_LOOP
    AND R0, R0, #0 ; Clear R0
    STR R0, R2, #0
    ADD R2, R2, #1
    ADD R3, R3, #-1
    BRp CLEAR_COUNTERS_LOOP

; Count characters
LEA R1, INPUT_BUFFER
LD R4, ASCII_OFFSET ; Load ASCII offset (-32) into R4
COUNT_CHAR_LOOP
    LDR R3, R1, #0
    BRz DISPLAY_RESULTS
    ADD R3, R3, R4 ; Adjust ASCII to array index
    LEA R2, CHAR_COUNT_ARRAY
    ADD R2, R2, R3 ; Calculate the address in the array
    LDR R5, R2, #0
    ADD R5, R5, #1
    STR R5, R2, #0
    LEA R2, CHAR_COUNT_ARRAY ; Reset R2 to the base address
    ADD R1, R1, #1
    BRnzp COUNT_CHAR_LOOP

DISPLAY_RESULTS
; Display the character counts
LEA R2, CHAR_COUNT_ARRAY
LD R3, ARRAY_SIZE
DISPLAY_LOOP
    LDR R4, R2, #0
    BRz SKIP_CHAR
    ADD R0, R2, R4 ; Load character into R0
    OUT
    LEA R0, SPACE
    PUTS
SKIP_CHAR
    ADD R2, R2, #1
    ADD R3, R3, #-1
    BRp DISPLAY_LOOP

LEA R0, NEWLINE
PUTS

; Halt the program
HALT

; Subroutine to read a string from the console
READ_STRING
LD R2, BUFFER_SIZE
READ_CHAR_LOOP_SUB
    GETC
    BRz READ_CHAR_END_SUB
    STR R0, R1, #0
    ADD R1, R1, #1
    ADD R2, R2, #-1
    BRp READ_CHAR_LOOP_SUB
READ_CHAR_END_SUB
    AND R0, R0, #0 ; Null-terminate the string
    STR R0, R1, #0
    RET

; Data Section
PROMPT .STRINGZ "Enter your full name: "
NEWLINE .STRINGZ "\n"
SPACE .STRINGZ " "
INPUT_BUFFER .BLKW x001E ; 30 characters
CHAR_COUNT_ARRAY .BLKW x0080 ; 128 characters
BUFFER_SIZE .FILL x001D ; 29
ASCII_OFFSET .FILL #-32
ARRAY_SIZE .FILL x0080 ; 128

.END
