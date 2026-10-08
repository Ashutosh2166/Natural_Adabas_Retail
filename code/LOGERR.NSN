* >Natural Source Code: LOGERR
* SUBPROGRAM: LOGERR - ERROR LOGGING SUBPROGRAM
* DESCRIPTION: WRITES ERROR RECORDS TO THE ERROR LOG
*              ADABAS FILE FOR TRACKING AND ANALYSIS
* LIBRARY: RETAILCORE
* ADABAS FILE: 200 (ERROR-LOG)
* CREATED: 1998-04-01  R. PETERSON
* MODIFIED: 2003-06-18 - ADDED ERROR COUNT TRACKING
* MODIFIED: 2010-09-22 - ADDED STACK TRACE CAPTURE
* MODIFIED: 2016-02-14 - ADDED DAILY ERROR LIMIT CHECK
* MODIFIED: 2021-07-30 - INCREASED TEXT FIELD LENGTH
* -------------------------------------------------------
DEFINE DATA
PARAMETER
  1 P-ERROR-CODE          (N4)
  1 P-ERROR-TEXT           (A80)
  1 P-SEVERITY            (A1)
  1 P-MODULE              (A8)
  1 P-USER                (A8)
*
LOCAL
  1 #TIMESTAMP            (T)
  1 #CURRENT-DATE         (D)
  1 #LOG-SEQ              (N10)
  1 #ERROR-COUNT          (N6)
  1 #DAILY-LIMIT          (N6)  INIT <10000>
  1 #DB-RESPONSE          (N4)
  1 #RETRY-COUNT          (N2)
  1 #MAX-RETRIES          (N2)  INIT <3>
  1 #WRITE-OK             (A1)
  1 #TERMINAL-ID          (A8)
*
* ADABAS FILE 200 - ERROR LOG
*
  1 ERROR-LOG-VIEW VIEW OF ERROR-LOG
    2 EL-SEQ-NO            (N10)
    2 EL-DATE              (D)
    2 EL-TIMESTAMP         (T)
    2 EL-ERROR-CODE        (N4)
    2 EL-ERROR-TEXT         (A80)
    2 EL-SEVERITY          (A1)
    2 EL-MODULE            (A8)
    2 EL-USER              (A8)
    2 EL-TERMINAL          (A8)
    2 EL-PROGRAM           (A8)
    2 EL-LIBRARY           (A8)
    2 EL-STACK-LEVEL       (N2)
*
* DAILY ERROR COUNT VIEW
*
  1 ERROR-COUNT-VIEW VIEW OF ERROR-LOG
    2 EL-DATE              (D)
    2 EL-SEVERITY          (A1)
END-DEFINE
*
* -------------------------------------------------------
* INITIALIZE
* -------------------------------------------------------
MOVE *TIMESTMP TO #TIMESTAMP
MOVE *DATX     TO #CURRENT-DATE
MOVE *INIT-ID  TO #TERMINAL-ID
MOVE 'N'       TO #WRITE-OK
RESET #RETRY-COUNT
*
* -------------------------------------------------------
* VALIDATE SEVERITY CODE
* -------------------------------------------------------
IF P-SEVERITY NE 'I' AND P-SEVERITY NE 'W'
    AND P-SEVERITY NE 'E' AND P-SEVERITY NE 'F'
  MOVE 'E' TO P-SEVERITY   /* DEFAULT TO ERROR */
END-IF
*
* -------------------------------------------------------
* CHECK DAILY ERROR LIMIT TO PREVENT LOG FLOODING
* MODIFIED 2016-02-14 - ADDED DAILY LIMIT CHECK
* -------------------------------------------------------
RESET #ERROR-COUNT
FIND NUMBER ERROR-COUNT-VIEW
  WITH EL-DATE = #CURRENT-DATE
    AND EL-SEVERITY = 'E' THRU 'F'
  IF *NUMBER > #DAILY-LIMIT
    /* DAILY LIMIT EXCEEDED - SKIP LOGGING */
    /* BUT STILL LOG FATAL ERRORS */
    IF P-SEVERITY NE 'F'
      ESCAPE ROUTINE
    END-IF
  END-IF
END-FIND
*
* -------------------------------------------------------
* GET NEXT SEQUENCE NUMBER
* -------------------------------------------------------
CALLNAT 'SEQNON' 'ERRL' #LOG-SEQ
*
* -------------------------------------------------------
* WRITE ERROR LOG RECORD WITH RETRY
* -------------------------------------------------------
REPEAT
  ADD 1 TO #RETRY-COUNT
*
  STORE ERROR-LOG-VIEW
    EL-SEQ-NO    := #LOG-SEQ
    EL-DATE      := #CURRENT-DATE
    EL-TIMESTAMP := #TIMESTAMP
    EL-ERROR-CODE := P-ERROR-CODE
    EL-ERROR-TEXT := P-ERROR-TEXT
    EL-SEVERITY  := P-SEVERITY
    EL-MODULE    := P-MODULE
    EL-USER      := P-USER
    EL-TERMINAL  := #TERMINAL-ID
    EL-PROGRAM   := *PROGRAM
    EL-LIBRARY   := *LIBRARY-ID
    EL-STACK-LEVEL := *LEVEL
  END-STORE
*
  ON ERROR
    IF #RETRY-COUNT < #MAX-RETRIES
      /* WAIT AND RETRY */
      /* LEGACY COMPATIBILITY - SLEEP NOT AVAILABLE IN ALL VERSIONS */
      ESCAPE BOTTOM
    ELSE
      /* CANNOT LOG - WRITE TO PRINT AS FALLBACK */
      WRITE 'LOGERR: FAILED TO WRITE ERROR LOG'
      WRITE 'CODE:' P-ERROR-CODE 'MODULE:' P-MODULE
      WRITE 'TEXT:' P-ERROR-TEXT
      ESCAPE ROUTINE
    END-IF
  END-ERROR
*
  MOVE 'Y' TO #WRITE-OK
  END OF TRANSACTION
  ESCAPE BOTTOM
*
  WHEN #RETRY-COUNT >= #MAX-RETRIES
    ESCAPE BOTTOM
END-REPEAT
*
* -------------------------------------------------------
* FOR FATAL ERRORS - ADDITIONAL NOTIFICATION
* -------------------------------------------------------
IF P-SEVERITY = 'F' AND #WRITE-OK = 'Y'
  /* LOG ADDITIONAL CONTEXT FOR FATAL ERRORS */
  WRITE 'FATAL ERROR LOGGED - SEQ:' #LOG-SEQ
  WRITE 'MODULE:' P-MODULE 'CODE:' P-ERROR-CODE
END-IF
*
END
