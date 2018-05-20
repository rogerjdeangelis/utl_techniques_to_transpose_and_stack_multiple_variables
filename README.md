# utl_techniques_to_transpose_and_stack_multiple_variables
Techniques to transpose and stack multiple variables. Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.

    Techniques to transpose and stack multiple variables

    github
    https://tinyurl.com/ybteqt5h
    https://github.com/rogerjdeangelis/utl_techniques_to_transpose_and_stack_multiple_variables

    Alea Iacta gather macro
    https://github.com/clindocu

    The resulting dat structure is very useful it is like a question/answer or dictionary.

     THREE SOLUTIONS

          1. Only complete solution. Handles duplcate rows or ids, mixtures of numeric
             and character and provides variable type and length.

          2. Requires at least a row number but handles mixture of numeric and character.
             Does not handle type and length.

          3. Handles mixture of numeric and character as long as
             there are no exact duplicate rows.
             Usefull solution for many input data structures?
             Does not handle type and length.


    INPUT
    =====

     WORK.HAVE total obs=9

        TED      TIM      LIZ     WINTED    WINTIM    WINLIZ

       Slots    Poker    Poker       1         0         0
       Slots    Poker    Poker       1         0         0    * this is why 'proc transpose
                                                              *  cannot stack variacles
       Slots    Poker    Poker       0         1         1
       Poker    Poker    Poker       1         0         0
       Slots    Poker    Poker       1         1         0
       Poker    Poker    Slots       1         1         0
       Poker    Poker    Poker       0         0         0
       Poker    Slots    Slots       1         0         0
       Slots    Slots    Slots       0         0         0


    EXAMPLE OD BEST OUTPUT

     WORK.DATA1 total obs=54

        VAR       VAL      _COLFORMAT    _COLTYP

        TED       Slots     $9.             C
        TIM       Poker     $9.             C
        LIZ       Poker     $9.             C
        WINTED    1         BEST12.         N
        WINTIM    0         BEST12.         N
        WINLIZ    0         BEST12.         N

        TED       Slots     $9.             C   * records from duplicate row
        TIM       Poker     $9.             C
        LIZ       Poker     $9.             C
        WINTED    1         BEST12.         N
        WINTIM    0         BEST12.         N
        WINLIZ    0         BEST12.         N
       ...


    PROCESS
    =======

     1. Complete solution

         %utl_gather(have,var,val,,havGat,valformat=$10.,WithFormats=Y);

     2. Requires a view to add uniqueness

        data havVue/view=havVue;
           retain seq 0;
           set have;
           seq=_n_;
        run;quit;

        proc transpose data=havVue out=havXpo(drop=seq where=(upcase(_name_) ne "SEQ"));
           by seq;
           var _all_;
        run;quit;

     3. If rows are unique

        * drop duplicate row (this is probably the case or should be);
        proc sort data=have out=havUnq nodupkey;
          by _all_;
        run;quit;

        proc transpose data=havUnq out=havXpo(drop=ted--winliz);
           by _all_ notsorted;
           var _all_;
        run;quit;


    OUTPUT
    ======

       1. Complete Solution

           WORK.HAVGAT total obs=54

            VAR       VAL      _COLFORMAT    _COLTYP

            TED       Slots     $9.             C
            TIM       Poker     $9.             C
            LIZ       Poker     $9.             C
            WINTED    1         BEST12.         N
            WINTIM    0         BEST12.         N
            WINLIZ    0         BEST12.         N

            TED       Slots     $9.             C
            TIM       Poker     $9.             C
            LIZ       Poker     $9.             C
            WINTED    1         BEST12.         N
            WINTIM    0         BEST12.         N
            WINLIZ    0         BEST12.         N
            TED       Slots     $9.             C
          ...

       2.  Requires a view to add uniqueness

            WORK.HAVXPO total obs=54

              _NAME_        COL1

              TED       Slots
              TIM       Poker
              LIZ       Poker
              WINTED               1
              WINTIM               0
              WINLIZ               0

              TED       Slots
              TIM       Poker
              LIZ       Poker
              WINTED               1
              WINTIM               0
              WINLIZ               0
            ....

      3. With unique rows

           WORK.HAVXPO total obs=48

              _NAME_        COL1

              TED       Poker
              TIM       Poker
              LIZ       Poker
              WINTED               0
              WINTIM               0
              WINLIZ               0
              TED       Poker
              TIM       Poker
              LIZ       Poker
              WINTED               1
              WINTIM               0
              WINLIZ               0

    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    data have;
      call streaminit(5731);
      array matC[3] $9 ted tim liz;
      array matN[3]  winTed winTim winLiz;
      array Game[0:1] $9 game1-game2 ('Poker' 'Slots');
      do i=1 to 8;
         do j=1 to dim(matC);
           matN[j] = RAND('BINOMIAL',.5,1);
           matC[j] = game[RAND('BINOMIAL',.5,1)];
         end;
         if i=1 then output;
         output;
      end;
      drop game1 game2 i j;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| '_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

    ;

    see process

