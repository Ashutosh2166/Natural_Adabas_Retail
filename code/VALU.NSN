* >Natural Source Code: VALU
* SUBPROGRAM: VALU - COMMON VALIDATION UTILITY
* DESCRIPTION: PROVIDES COMMON VALIDATION FUNCTIONS
*              FOR DATA ENTRY AND PROCESSING
* LIBRARY: RETAILCORE
* CREATED: 1998-05-01  R. PETERSON
* MODIFIED: 2007-03-15 - ADDED EMAIL VALIDATION
* MODIFIED: 2013-10-20 - ADDED INTERNATIONAL PHONE
* MODIFIED: 2019-04-05 - UPDATED STATE CODE LIST
* MODIFIED: 2022-08-18 - ADDED CURRENCY VALIDATION
* -------------------------------------------------------
* OPERATION CODES:
*   EMAL - VALIDATE EMAIL
*   PHON - VALIDATE PHONE
*   ZIP  - VALIDATE ZIP CODE
*   STAT - VALIDATE STATE CODE
*   CTRY - VALIDATE COUNTRY CODE
*   CURR - VALIDATE CURRENCY CODE
*   NUMR - VALIDATE NUMERIC VALUE
*   ALPH - VALIDATE ALPHABETIC
*   ALNM - VALIDATE ALPHANUMERIC
* -------------------------------------------------------
DEFINE DATA
PARAMETER
  1 P-OPERATION           (A4)    /* OPERATION CODE */
  1 P-VALUE               (A80)   /* VALUE TO VALIDATE */
  1 P-VALID-FLAG          (A1)    /* Y=VALID N=INVALID */
  1 P-ERROR-TEXT          (A60)   /* ERROR MESSAGE */
*
LOCAL
  1 #IDX                  (N3)
  1 #LEN                  (N3)
  1 #CHAR                 (A1)
  1 #AT-FOUND             (A1)
  1 #DOT-FOUND            (A1)
  1 #DIGIT-COUNT          (N3)
  1 #ALPHA-COUNT          (N3)
  1 #HAS-DIGIT            (A1)
*
* STATE CODES (US)
  1 #STATES               (A2/52)
    INIT <'AL','AK','AZ','AR','CA','CO','CT','DE','FL','GA',
          'HI','ID','IL','IN','IA','KS','KY','LA','ME','MD',
          'MA','MI','MN','MS','MO','MT','NE','NV','NH','NJ',
          'NM','NY','NC','ND','OH','OK','OR','PA','RI','SC',
          'SD','TN','TX','UT','VT','VA','WA','WV','WI','WY',
          'DC','PR'>
  1 #STATE-IDX            (N2)
  1 #STATE-FOUND          (A1)
*
* COUNTRY CODES (COMMON)
  1 #COUNTRIES            (A3/20)
    INIT <'USA','CAN','GBR','DEU','FRA','ESP','ITA','JPN',
          'CHN','AUS','BRA','MEX','IND','KOR','NLD','SWE',
          'NOR','CHE','AUT','BEL'>
  1 #CTRY-IDX             (N2)
  1 #CTRY-FOUND           (A1)
*
* CURRENCY CODES
  1 #CURRENCIES           (A3/10)
    INIT <'USD','EUR','GBP','CAD','AUD','JPY','CHF','SEK',
          'NOK','MXN'>
  1 #CURR-IDX             (N2)
  1 #CURR-FOUND           (A1)
END-DEFINE
*
* -------------------------------------------------------
* INITIALIZE
* -------------------------------------------------------
MOVE 'Y' TO P-VALID-FLAG
RESET P-ERROR-TEXT
*
* CHECK FOR BLANK VALUE
IF P-VALUE = ' '
  MOVE 'N' TO P-VALID-FLAG
  MOVE 'VALUE IS BLANK' TO P-ERROR-TEXT
  ESCAPE ROUTINE
END-IF
*
* -------------------------------------------------------
* ROUTE TO VALIDATION
* -------------------------------------------------------
DECIDE ON FIRST VALUE OF P-OPERATION
  VALUE 'EMAL'
    PERFORM VALIDATE-EMAIL
  VALUE 'PHON'
    PERFORM VALIDATE-PHONE
  VALUE 'ZIP'
    PERFORM VALIDATE-ZIP
  VALUE 'STAT'
    PERFORM VALIDATE-STATE
  VALUE 'CTRY'
    PERFORM VALIDATE-COUNTRY
  VALUE 'CURR'
    PERFORM VALIDATE-CURRENCY
  VALUE 'NUMR'
    PERFORM VALIDATE-NUMERIC
  VALUE 'ALPH'
    PERFORM VALIDATE-ALPHA
  VALUE 'ALNM'
    PERFORM VALIDATE-ALPHANUM
  NONE VALUE
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'VALU: INVALID OPERATION CODE' TO P-ERROR-TEXT
END-DECIDE
*
* =======================================
* VALIDATION SUBROUTINES
* =======================================
*
* -------------------------------------------------------
* VALIDATE EMAIL FORMAT
* MODIFIED 2007-03-15
* BASIC CHECK: MUST CONTAIN @ AND . AFTER @
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-EMAIL
  EXAMINE P-VALUE FOR ' ' GIVING LENGTH IN #LEN
  IF #LEN < 5
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'EMAIL TOO SHORT' TO P-ERROR-TEXT
    ESCAPE ROUTINE
  END-IF
*
  MOVE 'N' TO #AT-FOUND
  MOVE 'N' TO #DOT-FOUND
  FOR #IDX = 1 TO #LEN
    MOVE SUBSTRING(P-VALUE, #IDX, 1) TO #CHAR
    IF #CHAR = '@'
      IF #AT-FOUND = 'Y'
        MOVE 'N' TO P-VALID-FLAG
        MOVE 'EMAIL HAS MULTIPLE @' TO P-ERROR-TEXT
        ESCAPE ROUTINE
      END-IF
      MOVE 'Y' TO #AT-FOUND
    END-IF
    IF #CHAR = '.' AND #AT-FOUND = 'Y'
      MOVE 'Y' TO #DOT-FOUND
    END-IF
  END-FOR
*
  IF #AT-FOUND NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'EMAIL MISSING @' TO P-ERROR-TEXT
    ESCAPE ROUTINE
  END-IF
  IF #DOT-FOUND NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'EMAIL MISSING DOMAIN' TO P-ERROR-TEXT
  END-IF
END-SUBROUTINE
*
* -------------------------------------------------------
* VALIDATE PHONE NUMBER
* MODIFIED 2013-10-20 - INTERNATIONAL SUPPORT
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-PHONE
  RESET #DIGIT-COUNT
  EXAMINE P-VALUE FOR ' ' GIVING LENGTH IN #LEN
  IF #LEN < 7
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'PHONE TOO SHORT' TO P-ERROR-TEXT
    ESCAPE ROUTINE
  END-IF
  FOR #IDX = 1 TO #LEN
    MOVE SUBSTRING(P-VALUE, #IDX, 1) TO #CHAR
    IF #CHAR >= '0' AND #CHAR <= '9'
      ADD 1 TO #DIGIT-COUNT
    ELSE
      IF #CHAR NE '-' AND #CHAR NE '(' AND #CHAR NE ')'
          AND #CHAR NE ' ' AND #CHAR NE '+' AND #CHAR NE '.'
        MOVE 'N' TO P-VALID-FLAG
        MOVE 'PHONE HAS INVALID CHARACTERS' TO P-ERROR-TEXT
        ESCAPE ROUTINE
      END-IF
    END-IF
  END-FOR
  IF #DIGIT-COUNT < 7 OR #DIGIT-COUNT > 15
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'PHONE DIGIT COUNT INVALID' TO P-ERROR-TEXT
  END-IF
END-SUBROUTINE
*
* -------------------------------------------------------
* VALIDATE ZIP CODE (US FORMAT)
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-ZIP
  EXAMINE P-VALUE FOR ' ' GIVING LENGTH IN #LEN
  IF #LEN NE 5 AND #LEN NE 10
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'ZIP MUST BE 5 OR 10 CHARS (XXXXX-XXXX)' TO P-ERROR-TEXT
    ESCAPE ROUTINE
  END-IF
  * CHECK FIRST 5 ARE DIGITS
  FOR #IDX = 1 TO 5
    MOVE SUBSTRING(P-VALUE, #IDX, 1) TO #CHAR
    IF #CHAR < '0' OR #CHAR > '9'
      MOVE 'N' TO P-VALID-FLAG
      MOVE 'ZIP CONTAINS NON-NUMERIC' TO P-ERROR-TEXT
      ESCAPE ROUTINE
    END-IF
  END-FOR
  * CHECK ZIP+4 FORMAT
  IF #LEN = 10
    MOVE SUBSTRING(P-VALUE, 6, 1) TO #CHAR
    IF #CHAR NE '-'
      MOVE 'N' TO P-VALID-FLAG
      MOVE 'ZIP+4 MISSING HYPHEN' TO P-ERROR-TEXT
    END-IF
  END-IF
END-SUBROUTINE
*
* -------------------------------------------------------
* VALIDATE US STATE CODE
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-STATE
  MOVE 'N' TO #STATE-FOUND
  FOR #STATE-IDX = 1 TO 52
    IF P-VALUE = #STATES(#STATE-IDX)
      MOVE 'Y' TO #STATE-FOUND
      ESCAPE BOTTOM
    END-IF
  END-FOR
  IF #STATE-FOUND NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'INVALID STATE CODE' TO P-ERROR-TEXT
  END-IF
END-SUBROUTINE
*
* -------------------------------------------------------
* VALIDATE COUNTRY CODE
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-COUNTRY
  MOVE 'N' TO #CTRY-FOUND
  FOR #CTRY-IDX = 1 TO 20
    IF P-VALUE = #COUNTRIES(#CTRY-IDX)
      MOVE 'Y' TO #CTRY-FOUND
      ESCAPE BOTTOM
    END-IF
  END-FOR
  IF #CTRY-FOUND NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'INVALID COUNTRY CODE' TO P-ERROR-TEXT
  END-IF
END-SUBROUTINE
*
* -------------------------------------------------------
* VALIDATE CURRENCY CODE
* ADDED 2022-08-18
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-CURRENCY
  MOVE 'N' TO #CURR-FOUND
  FOR #CURR-IDX = 1 TO 10
    IF P-VALUE = #CURRENCIES(#CURR-IDX)
      MOVE 'Y' TO #CURR-FOUND
      ESCAPE BOTTOM
    END-IF
  END-FOR
  IF #CURR-FOUND NE 'Y'
    MOVE 'N' TO P-VALID-FLAG
    MOVE 'INVALID CURRENCY CODE' TO P-ERROR-TEXT
  END-IF
END-SUBROUTINE
*
* -------------------------------------------------------
* VALIDATE NUMERIC
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-NUMERIC
  EXAMINE P-VALUE FOR ' ' GIVING LENGTH IN #LEN
  FOR #IDX = 1 TO #LEN
    MOVE SUBSTRING(P-VALUE, #IDX, 1) TO #CHAR
    IF #CHAR < '0' OR #CHAR > '9'
      IF #CHAR NE '-' AND #CHAR NE '.'
        MOVE 'N' TO P-VALID-FLAG
        MOVE 'VALUE IS NOT NUMERIC' TO P-ERROR-TEXT
        ESCAPE ROUTINE
      END-IF
    END-IF
  END-FOR
END-SUBROUTINE
*
* -------------------------------------------------------
* VALIDATE ALPHABETIC
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-ALPHA
  EXAMINE P-VALUE FOR ' ' GIVING LENGTH IN #LEN
  FOR #IDX = 1 TO #LEN
    MOVE SUBSTRING(P-VALUE, #IDX, 1) TO #CHAR
    IF NOT (#CHAR >= 'A' AND #CHAR <= 'Z')
        AND NOT (#CHAR >= 'a' AND #CHAR <= 'z')
        AND #CHAR NE ' '
      MOVE 'N' TO P-VALID-FLAG
      MOVE 'VALUE CONTAINS NON-ALPHA CHARS' TO P-ERROR-TEXT
      ESCAPE ROUTINE
    END-IF
  END-FOR
END-SUBROUTINE
*
* -------------------------------------------------------
* VALIDATE ALPHANUMERIC
* -------------------------------------------------------
DEFINE SUBROUTINE VALIDATE-ALPHANUM
  EXAMINE P-VALUE FOR ' ' GIVING LENGTH IN #LEN
  FOR #IDX = 1 TO #LEN
    MOVE SUBSTRING(P-VALUE, #IDX, 1) TO #CHAR
    IF NOT (#CHAR >= 'A' AND #CHAR <= 'Z')
        AND NOT (#CHAR >= 'a' AND #CHAR <= 'z')
        AND NOT (#CHAR >= '0' AND #CHAR <= '9')
        AND #CHAR NE ' ' AND #CHAR NE '-' AND #CHAR NE '_'
      MOVE 'N' TO P-VALID-FLAG
      MOVE 'VALUE CONTAINS INVALID CHARS' TO P-ERROR-TEXT
      ESCAPE ROUTINE
    END-IF
  END-FOR
END-SUBROUTINE
*
END
