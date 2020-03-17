# utl-combine-text-in-an-excel-column-down-multiple-rows-by-group
Combine text in an excel an column down multiple rows by group
    Combine text in an excel an column down multiple rows by group;

    github
    https://tinyurl.com/v72lw5s
    https://github.com/rogerjdeangelis/utl-combine-text-in-an-excel-column-down-multiple-rows-by-group

    SAS Forum
    https://tinyurl.com/sdzzoz7
    https://communities.sas.com/t5/SAS-Programming/Converting-multiple-excel-rows-into-single-dataline/m-p/632538

    *_                   _
    (_)_ __  _ __  _   _| |_
    | | '_ \| '_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    ;

    * create workbook and sheet;

    %utlfkil(d:/xls/have.xlsx);    * just in case it exista;

    libname xel "d:/xls/have.xlsx";
    data xel.have;
     input id:$1. f2:$1. order:$8.;
    cards4;
    J . .
    . . 3
    . . 1003
    . . 5
    . . 40
    . . 6050
    . . .
    K . 150
    . . 1500005
    ;;;;
    run;quit;
    libname xel clear;


    SHEETNAME and NAMED RANGE 'HAVE' IN WORKBOOK D:/XLS/HAVE.XLSX

      +--------------------------------------+
      |     A      |    B       |     C      |
      +--------------------------------------+
    1 |    ID      |            |  ORDER     |
      +------------+------------+------------+
    2 |     J      |            |            |
      +------------+------------+------------+
    3 |            |            |   003      |
      +------------+------------+------------+
    4 |            |            |  01003     |
      +------------+------------+------------+
    5 |            |            |    05      |
      +------------+------------+------------+
    6 |            |            |    40      |
      +------------+------------+------------+
    7 |            |            |            |
      +------------+------------+------------+
    8 |     K      |            |   150      |
      +------------+------------+------------+
    9 |            |            | 1500005    |
      +------------+------------+------------+

    [HAVE]

    *            _               _
      ___  _   _| |_ _ __  _   _| |_
     / _ \| | | | __| '_ \| | | | __|
    | (_) | |_| | |_| |_) | |_| | |_
     \___/ \__,_|\__| .__/ \__,_|\__|
                    |_|
    ;

    The generated code is sent to the log and to a file

    In the log

    data hav;
    infile cards4 dsd;
    input ID:$1. ORDER:$200.;
    cards4;
    J,^3|1003|5|40|6050
    K,^150|1500005
    ;;;;
    run;quit;


    d:/sas/want.sas"

    data hav;
    infile cards4 dsd;
    input ID:$1. ORDER:$200.;
    cards4;
    J,^3|1003|5|40|6050
    K,^150|1500005
    ;;;;
    run;quit;

    *
     _ __  _ __ ___   ___ ___  ___ ___
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|
    | |_) | | | (_) | (_|  __/\__ \__ \
    | .__/|_|  \___/ \___\___||___/___/
    |_|
    ;

    proc datasets lib=work nolist;  * just in case;
      delete havFil;
    run;quit;

    %utlfkil(d:/sas/want.sas);      * just in case;

    * YOU NEED THIS MACRO;

    %macro dosubl(arg);
      %let rc=%qsysfunc(dosubl(&arg));
    %mend dosubl;

    libname xel "d:/xls/have.xlsx";

    data _null_;

      if _n_=0 then do;
          %dosubl('
              * fill in the missing ids;
              data havFil;
                 retain idx;
                 set xel.have;
                 if id ne "" then idx=id;
                 drop id f2;
              run;quit;
          ');
      end;

      length concat $200;
      retain concat;

      set havFil end=dne;

        by idx notsorted;

        file "d:/sas/want.sas";

        if _n_=1 then do;
           put
              'data have;' /
              'infile cards4 dsd;'  /
              'input ID:$1. ORDER:$200.;'  /
              'cards4;'
           ;
           putlog
              'data have;' /
              'infile cards4 dsd;'  /
              'input ID:$1. ORDER:$200.;'  /
              'cards4;'
           ;
        end;

        concat = catx('|',concat,order);

        if last.idx then do;
           concat=cats(idx,",^",concat);
           put concat;
           putlog concat;
           concat="";
        end;
        if dne then do;
           put ';;;;' / 'run;quit;';
           putlog ';;;;' / 'run;quit;';
        end;
    run;quit;

    libname xel clear;

