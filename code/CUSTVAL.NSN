* >Natural Source Code: CUSTVAL
* SUBPROGRAM: CUSTVAL - CUSTOMER VALIDATION
* DESCRIPTION: VALIDATES ALL CUSTOMER DATA FIELDS
*              CALLED BEFORE CREATE AND UPDATE
* LIBRARY: RETAILCORE
* CREATED: 1998-06-05  M. JOHNSON
* MODIFIED: 2007-09-10 - ADDED EMAIL VALIDATION
* MODIFIED: 2014-03-18 - ADDED DUPLICATE CHECK
* MODIFIED: 2020-11-05 - ADDED GDPR VALIDATION
* -------------------------------------------------------
DEFINE DATA
PARAMETER
  1 P-CUSTOMER-DATA
    2 P-CD-ID             (N8)
    2 P-CD-NO             (A10)
    2 P-CD-TYPE           (A2)
    2 P-CD-STATUS         (A1)
    2 P-CD-TITLE          (A5)
    2 P-CD-FIRST-NAME     (A30)
    2 P-CD-LAST-NAME      (A40)
    2 P-CD-DOB            (D)
    2 P-CD-GENDER         (A1)
  1 P-CUSTOMER-CONTACT
    2 P-CC-EMAIL          (A80)
    2 P-CC-PHONE-HOME     (A20)
    2 P-CC-PHONE-MOBILE   (A20)
  1 P-CUSTOMER-ADDRESS
    2 P-CA-ADDR-LINE1     (A40)
    2 P-CA-ADDR-LINE2     (A40)
    2 P-CA-CITY           (A30)
    2 P-CA-STATE          (A2)
    2 P-CA-ZIP            (A10)
    2 P-CA-COUNTRY        (A3)
  1 P-VALID-FLAG          (A1)
  1 P-ERROR-MSG           (A60)
*
LOCAL
  1 #VAL-FLAG             (A1)
  1 #VAL-MSG              (A60)
  1 #DUP-COUNT            (N5)
*
* DUPLICATE CHECK VIEW
  1 CUST-DUP-VIEW VIEW OF CUSTOMER-FILE
    2 CU-ID                (N8)
    2 CU-EMAIL             (A80)
    2 CU-LAST-NAME         (A40)
    2 CU-FIRST-NAME        (A30)
END-DEFINE
*
* -------------------------------------------------------
* INITIALIZE
* -------------------------------------------------------
MOVE 'Y' TO P-VALID-FLAG
RESET P-ERROR-MSG
*
* -------------------------------------------------------
* VALIDATE REQUIRED FIELDS
* -------------------------------------------------------
IF P-CD-FIRST-NAME = ' '
  MOVE 'N' TO P-VALID-FLAG
  MOVE 'FIRST NAME IS REQUIRED' TO P-ERROR-MSG
  ESCAPE ROUTINE
END-IF
*
IF P-CD-LAST-NAME = ' '
  MOVE 'N' TO P-VALID-FLAG
  MOVE 'LAST NAME IS REQUIRED' TO P-ERROR-MSG
  ESCAPE ROUTINE
END-IF
*
* -------------------------------------------------------
* VALIDATE CUSTOMER TYPE
* -------------------------------------------------------
IF P-CD-TYPE NE 'RE' AND P-CD-TYPE NE 'WH'
    AND P-CD-TYPE NE 'EM' AND P-CD-TYPE NE 'CO'
  MOVE 'N' TO P-VALID-FLAG
  MOVE 'INVALID CUSTOMER TYPE' TO P-ERROR-MSG
  ESCAPE ROUTINE
END-IF
*
* -------------------------------------------------------
* VALIDATE STATUS (IF UPDATE)
* -------------------------------------------------------
IF P-CD-STATUS NE ' '
  IF P-CD-STATUS NE 'A' AND P-CD-STATUS NE 'I'
      AND P-CD-STATUS NE 'S' AND P-CD-STATUS NE 'D'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'INVALID CUSTOMER STATUS' TO P-ERROR-MSG
    ESCAPE ROUTINE
  END-IF
END-IF
*
* -------------------------------------------------------
* VALIDATE EMAIL
* MODIFIED 2007-09-10
* -------------------------------------------------------
IF P-CC-EMAIL NE ' '
  CALLNAT 'VALU' 'EMAL' P-CC-EMAIL #VAL-FLAG #VAL-MSG
  IF #VAL-FLAG NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    COMPRESS 'INVALID EMAIL:' #VAL-MSG INTO P-ERROR-MSG
    ESCAPE ROUTINE
  END-IF
END-IF
*
* -------------------------------------------------------
* VALIDATE PHONE
* -------------------------------------------------------
IF P-CC-PHONE-HOME NE ' '
  CALLNAT 'VALU' 'PHON' P-CC-PHONE-HOME #VAL-FLAG #VAL-MSG
  IF #VAL-FLAG NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    COMPRESS 'INVALID HOME PHONE:' #VAL-MSG INTO P-ERROR-MSG
    ESCAPE ROUTINE
  END-IF
END-IF
*
* -------------------------------------------------------
* VALIDATE ADDRESS FIELDS
* -------------------------------------------------------
IF P-CA-ADDR-LINE1 = ' '
  MOVE 'N' TO P-VALID-FLAG
  MOVE 'ADDRESS LINE 1 IS REQUIRED' TO P-ERROR-MSG
  ESCAPE ROUTINE
END-IF
*
IF P-CA-CITY = ' '
  MOVE 'N' TO P-VALID-FLAG
  MOVE 'CITY IS REQUIRED' TO P-ERROR-MSG
  ESCAPE ROUTINE
END-IF
*
* VALIDATE STATE CODE
IF P-CA-STATE NE ' '
  CALLNAT 'VALU' 'STAT' P-CA-STATE #VAL-FLAG #VAL-MSG
  IF #VAL-FLAG NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'INVALID STATE CODE' TO P-ERROR-MSG
    ESCAPE ROUTINE
  END-IF
END-IF
*
* VALIDATE ZIP CODE
IF P-CA-ZIP NE ' '
  CALLNAT 'VALU' 'ZIP' P-CA-ZIP #VAL-FLAG #VAL-MSG
  IF #VAL-FLAG NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    COMPRESS 'INVALID ZIP CODE:' #VAL-MSG INTO P-ERROR-MSG
    ESCAPE ROUTINE
  END-IF
END-IF
*
* VALIDATE COUNTRY CODE
IF P-CA-COUNTRY NE ' '
  CALLNAT 'VALU' 'CTRY' P-CA-COUNTRY #VAL-FLAG #VAL-MSG
  IF #VAL-FLAG NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'INVALID COUNTRY CODE' TO P-ERROR-MSG
    ESCAPE ROUTINE
  END-IF
ELSE
  MOVE 'USA' TO P-CA-COUNTRY   /* DEFAULT */
END-IF
*
* -------------------------------------------------------
* CHECK FOR DUPLICATE CUSTOMER
* MODIFIED 2014-03-18
* -------------------------------------------------------
IF P-CC-EMAIL NE ' '
  RESET #DUP-COUNT
  FIND NUMBER CUST-DUP-VIEW WITH CU-EMAIL = P-CC-EMAIL
    MOVE *NUMBER TO #DUP-COUNT
  END-FIND
*
  * EXCLUDE SELF ON UPDATE
  IF P-CD-ID > 0
    IF #DUP-COUNT > 1
      MOVE 'N' TO P-VALID-FLAG
      MOVE 'DUPLICATE EMAIL ADDRESS FOUND' TO P-ERROR-MSG
      ESCAPE ROUTINE
    END-IF
  ELSE
    IF #DUP-COUNT > 0
      MOVE 'N' TO P-VALID-FLAG
      MOVE 'EMAIL ALREADY EXISTS IN SYSTEM' TO P-ERROR-MSG
      ESCAPE ROUTINE
    END-IF
  END-IF
END-IF
*
* -------------------------------------------------------
* VALIDATE TITLE
* -------------------------------------------------------
IF P-CD-TITLE NE ' '
  IF P-CD-TITLE NE 'MR' AND P-CD-TITLE NE 'MRS'
      AND P-CD-TITLE NE 'MS' AND P-CD-TITLE NE 'DR'
      AND P-CD-TITLE NE 'MISS'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'INVALID TITLE' TO P-ERROR-MSG
    ESCAPE ROUTINE
  END-IF
END-IF
*
* ALL VALIDATIONS PASSED
MOVE 'Y' TO P-VALID-FLAG
*
END
