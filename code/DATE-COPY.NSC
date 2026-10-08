* >Natural Source Code: DATE-COPY
* RETAILCORE - DATE HANDLING DEFINITIONS
* COMMON DATE STRUCTURES AND CONSTANTS
* CREATED: 1998-04-01
* MODIFIED: 2016-07-12 - ADDED FISCAL YEAR FIELDS
* MODIFIED: 2021-02-28 - ADDED TIMEZONE SUPPORT
* -------------------------------------------------------
*
* DATE WORK FIELDS
*
1 DATE-WORK
  2 WK-DATE               (D)
  2 WK-DATE-NUM           (N8)    /* YYYYMMDD */
  2 WK-DATE-ALPHA         (A10)   /* YYYY-MM-DD */
  2 WK-YEAR               (N4)
  2 WK-MONTH              (N2)
  2 WK-DAY                (N2)
  2 WK-DAY-OF-WEEK        (N1)    /* 1=MON 7=SUN */
  2 WK-WEEK-NUMBER        (N2)
  2 WK-JULIAN-DAY         (N3)
*
* DATE RANGE
*
1 DATE-RANGE
  2 DR-FROM-DATE          (D)
  2 DR-TO-DATE            (D)
  2 DR-DAYS-BETWEEN       (N5)
  2 DR-VALID-FLAG         (A1)    /* Y/N */
*
* FISCAL YEAR
*
1 FISCAL-YEAR-DATA
  2 FY-YEAR               (N4)
  2 FY-PERIOD             (N2)    /* 1-13 */
  2 FY-QUARTER            (N1)    /* 1-4 */
  2 FY-START-DATE         (D)
  2 FY-END-DATE           (D)
  2 FY-CLOSED-FLAG        (A1)    /* Y=CLOSED N=OPEN */
*
* RETURN WINDOW / BUSINESS DATE CALCULATIONS
*
1 RETURN-WINDOW-DATA
  2 RW-ORDER-DATE         (D)
  2 RW-SHIP-DATE          (D)
  2 RW-DELIVERY-DATE      (D)
  2 RW-RETURN-BY-DATE     (D)
  2 RW-DAYS-REMAINING     (N3)
  2 RW-ELIGIBLE-FLAG      (A1)    /* Y=ELIGIBLE N=EXPIRED */
*
* PROMOTION DATE FIELDS
*
1 PROMO-DATE-DATA
  2 PD-START-DATE         (D)
  2 PD-END-DATE           (D)
  2 PD-IS-ACTIVE          (A1)    /* Y/N */
  2 PD-DAYS-LEFT          (N5)
*
* STANDARD DATE CONSTANTS
*
1 DATE-CONSTANTS
  2 DC-MIN-DATE           (D)     INIT <D'1990-01-01'>
  2 DC-MAX-DATE           (D)     INIT <D'2099-12-31'>
  2 DC-RETURN-WINDOW-DAYS (N3)    INIT <30>
  2 DC-PROMO-MAX-DAYS     (N3)    INIT <90>
  2 DC-PO-LEAD-DAYS       (N3)    INIT <14>
  2 DC-PAYMENT-SETTLE-DAYS (N2)   INIT <3>
  2 DC-COUPON-DEFAULT-DAYS (N3)   INIT <60>
*
END-DEFINE
