* >Natural Source Code: CONFIGN
* SUBPROGRAM: CONFIGN - CONFIGURATION READER
* DESCRIPTION: READS SYSTEM CONFIGURATION VALUES
*              FROM ADABAS CONFIG TABLE
* LIBRARY: RETAILCORE
* ADABAS FILE: 250 (CONFIG)
* CREATED: 2001-03-20  S. WILLIAMS
* MODIFIED: 2009-07-15 - ADDED LOCAL CACHING
* MODIFIED: 2016-12-10 - ADDED TYPE CONVERSION
* MODIFIED: 2022-05-20 - ADDED CONFIG REFRESH
* -------------------------------------------------------
DEFINE DATA
PARAMETER
  1 P-CONFIG-KEY          (A30)   /* CONFIGURATION KEY */
  1 P-CONFIG-VALUE        (A80)   /* RETURNED VALUE */
  1 P-CONFIG-NUM          (N11.2) /* NUMERIC VALUE */
  1 P-CONFIG-DATE         (D)     /* DATE VALUE */
  1 P-CONFIG-FLAG         (A1)    /* FLAG VALUE (Y/N) */
  1 P-FOUND-FLAG          (A1)    /* Y=FOUND N=NOT FOUND */
*
LOCAL
  * CONFIGURATION CACHE
  1 #CACHE-LOADED         (A1)    INIT <'N'>
  1 #CACHE-COUNT          (N3)    INIT <0>
  1 #CACHE-MAX            (N3)    INIT <100>
  1 #CONFIG-CACHE (100)
    2 #CC-KEY              (A30)
    2 #CC-VALUE            (A80)
    2 #CC-TYPE             (A1)    /* S=STRING N=NUMERIC D=DATE F=FLAG */
  1 #CACHE-IDX            (N3)
  1 #CACHE-HIT            (A1)
*
  1 #WORK-VALUE           (A80)
  1 #WORK-NUM             (N11.2)
  1 #DB-RC                (N4)
*
* ADABAS FILE 250 - CONFIG
  1 CONFIG-VIEW VIEW OF CONFIG-TABLE
    2 CF-KEY               (A30)
    2 CF-VALUE             (A80)
    2 CF-TYPE              (A1)
    2 CF-DESCRIPTION       (A60)
    2 CF-ACTIVE            (A1)
    2 CF-MODIFIED-DATE     (D)
    2 CF-MODIFIED-BY       (A8)
*
* DEFAULT CONFIGURATION VALUES
* TEMPORARY FIX - RETAIN FOR BATCH PROCESS
  1 #DEFAULTS (20)
    2 #DEF-KEY             (A30)
    2 #DEF-VALUE           (A80)
END-DEFINE
*
* -------------------------------------------------------
* INITIALIZE DEFAULTS (FIRST CALL ONLY)
* -------------------------------------------------------
IF #CACHE-LOADED = 'N'
  PERFORM LOAD-DEFAULTS
  MOVE 'Y' TO #CACHE-LOADED
END-IF
*
* -------------------------------------------------------
* INITIALIZE OUTPUT
* -------------------------------------------------------
MOVE 'N' TO P-FOUND-FLAG
RESET P-CONFIG-VALUE
RESET P-CONFIG-NUM
RESET P-CONFIG-FLAG
*
* -------------------------------------------------------
* CHECK CACHE FIRST
* -------------------------------------------------------
MOVE 'N' TO #CACHE-HIT
FOR #CACHE-IDX = 1 TO #CACHE-COUNT
  IF #CONFIG-CACHE.#CC-KEY(#CACHE-IDX) = P-CONFIG-KEY
    MOVE #CONFIG-CACHE.#CC-VALUE(#CACHE-IDX) TO P-CONFIG-VALUE
    MOVE 'Y' TO #CACHE-HIT
    MOVE 'Y' TO P-FOUND-FLAG
    PERFORM CONVERT-VALUE
    ESCAPE BOTTOM
  END-IF
END-FOR
*
* -------------------------------------------------------
* NOT IN CACHE - READ FROM DATABASE
* -------------------------------------------------------
IF #CACHE-HIT NE 'Y'
  FIND CONFIG-VIEW WITH CF-KEY = P-CONFIG-KEY
      AND CF-ACTIVE = 'Y'
    IF NO RECORDS FOUND
      * TRY DEFAULTS
      PERFORM CHECK-DEFAULTS
      ESCAPE BOTTOM
    END-NOREC
*
    MOVE CF-VALUE TO P-CONFIG-VALUE
    MOVE 'Y' TO P-FOUND-FLAG
*
    * ADD TO CACHE
    IF #CACHE-COUNT < #CACHE-MAX
      ADD 1 TO #CACHE-COUNT
      MOVE P-CONFIG-KEY    TO #CONFIG-CACHE.#CC-KEY(#CACHE-COUNT)
      MOVE CF-VALUE        TO #CONFIG-CACHE.#CC-VALUE(#CACHE-COUNT)
      MOVE CF-TYPE         TO #CONFIG-CACHE.#CC-TYPE(#CACHE-COUNT)
    END-IF
*
    PERFORM CONVERT-VALUE
    ESCAPE BOTTOM
*
  END-FIND
*
  ON ERROR
    * DATABASE ERROR - FALL BACK TO DEFAULTS
    PERFORM CHECK-DEFAULTS
  END-ERROR
END-IF
*
* =======================================
* SUBROUTINES
* =======================================
*
* -------------------------------------------------------
* CONVERT VALUE BASED ON EXPECTED TYPE
* -------------------------------------------------------
DEFINE SUBROUTINE CONVERT-VALUE
  IF P-CONFIG-VALUE = ' '
    ESCAPE ROUTINE
  END-IF
  * TRY NUMERIC CONVERSION
  MOVE P-CONFIG-VALUE TO P-CONFIG-NUM
  * SET FLAG IF Y OR N
  IF P-CONFIG-VALUE = 'Y' OR P-CONFIG-VALUE = 'N'
    MOVE P-CONFIG-VALUE TO P-CONFIG-FLAG
  END-IF
END-SUBROUTINE
*
* -------------------------------------------------------
* CHECK DEFAULT VALUES
* -------------------------------------------------------
DEFINE SUBROUTINE CHECK-DEFAULTS
  FOR #CACHE-IDX = 1 TO 20
    IF #DEFAULTS.#DEF-KEY(#CACHE-IDX) = P-CONFIG-KEY
      MOVE #DEFAULTS.#DEF-VALUE(#CACHE-IDX) TO P-CONFIG-VALUE
      MOVE 'Y' TO P-FOUND-FLAG
      PERFORM CONVERT-VALUE
      ESCAPE BOTTOM
    END-IF
  END-FOR
END-SUBROUTINE
*
* -------------------------------------------------------
* LOAD DEFAULT CONFIGURATION VALUES
* -------------------------------------------------------
DEFINE SUBROUTINE LOAD-DEFAULTS
  MOVE 'RETURN.WINDOW.DAYS'     TO #DEFAULTS.#DEF-KEY(1)
  MOVE '30'                     TO #DEFAULTS.#DEF-VALUE(1)
  MOVE 'MAX.ORDER.LINES'        TO #DEFAULTS.#DEF-KEY(2)
  MOVE '99'                     TO #DEFAULTS.#DEF-VALUE(2)
  MOVE 'PAYMENT.TIMEOUT.SEC'    TO #DEFAULTS.#DEF-KEY(3)
  MOVE '30'                     TO #DEFAULTS.#DEF-VALUE(3)
  MOVE 'RESTOCK.FEE.PCT'        TO #DEFAULTS.#DEF-KEY(4)
  MOVE '15.00'                  TO #DEFAULTS.#DEF-VALUE(4)
  MOVE 'MEMBER.DISCOUNT.PCT'    TO #DEFAULTS.#DEF-KEY(5)
  MOVE '10.00'                  TO #DEFAULTS.#DEF-VALUE(5)
  MOVE 'SAFETY.STOCK.DEFAULT'   TO #DEFAULTS.#DEF-KEY(6)
  MOVE '10'                     TO #DEFAULTS.#DEF-VALUE(6)
  MOVE 'MAX.PAYMENT.RETRIES'    TO #DEFAULTS.#DEF-KEY(7)
  MOVE '3'                      TO #DEFAULTS.#DEF-VALUE(7)
  MOVE 'BATCH.LOG.LEVEL'        TO #DEFAULTS.#DEF-KEY(8)
  MOVE 'E'                      TO #DEFAULTS.#DEF-VALUE(8)
  MOVE 'PROMO.MAX.STACK'        TO #DEFAULTS.#DEF-KEY(9)
  MOVE '1'                      TO #DEFAULTS.#DEF-VALUE(9)
  MOVE 'TAX.DEFAULT.RATE'       TO #DEFAULTS.#DEF-KEY(10)
  MOVE '8.25'                   TO #DEFAULTS.#DEF-VALUE(10)
  MOVE 'LOCK.TIMEOUT.MIN'       TO #DEFAULTS.#DEF-KEY(11)
  MOVE '30'                     TO #DEFAULTS.#DEF-VALUE(11)
  MOVE 'INV.ADJ.LIMIT'          TO #DEFAULTS.#DEF-KEY(12)
  MOVE '1000'                   TO #DEFAULTS.#DEF-VALUE(12)
  MOVE 'ORDER.DUP.CHECK.HOURS'  TO #DEFAULTS.#DEF-KEY(13)
  MOVE '24'                     TO #DEFAULTS.#DEF-VALUE(13)
  MOVE 'CLEARANCE.RETURN.FLAG'  TO #DEFAULTS.#DEF-KEY(14)
  MOVE 'N'                      TO #DEFAULTS.#DEF-VALUE(14)
  MOVE 'PO.AUTO.APPROVE.LIMIT'  TO #DEFAULTS.#DEF-KEY(15)
  MOVE '5000.00'                TO #DEFAULTS.#DEF-VALUE(15)
END-SUBROUTINE
*
END
