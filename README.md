# utl-sqllite-working-with-dates-in-r-and-python
Simplifying date manipulation in R and Python sqllite
    %let pgm=utl-sqllite-working-with-dates-in-r-and-python;

    %stop_submission; /* stops submission if you accidentally submit the entire program */

    Simplifying date manipulation in R and Python sqllite

    github
    http://tinyurl.com/mrye28re
    https://github.com/rogerjdeangelis/utl-sqllite-working-with-dates-in-r-and-python

    I am switching my focus to helping programers integrate other languages
    with R and python including wps and sas.

    You need to preprocess the raw sas datasets using R and python to convert
    R and Python character dates to unix epoc numerics (times since 1-1-1970).
    Working with time since an epoc is more universal than chracter times.

      SOLUTIONS

          1 python sql
          2 r sql
          3 enhanced python submit macro

    You will need to save and reload R and panda dataframes
    Siring and retrieving dataframes is more commpn to mixed langusgaes.

    Save and reload a python panda dataframe
    Python
    df.to_pickle("d:/pkl/df.pkl")
    df= pd.read_pickle("d./dfpkl")

    R
    save(df, file = "d:/rds/df.rds");
    load("d:/rds/df.rds");

    Related repos

    Passing R constants to sqllite R and python
    github
    https://tinyurl.com/yc7r96t3
    https://github.com/rogerjdeangelis/utl-passing-arguments-to-sqldf-using-wps-python-f-text-function

    github
    https://tinyurl.com/5aeuz244
    https://github.com/rogerjdeangelis/utl-passing-arguments-to-sqldf-wps-r-sql-functional-sql

    Sharing sqllite database with panda and r
    https://github.com/rogerjdeangelis/utl-exporting-python-panda-dataframes-to-wps-r-using-a-shared-sqllite-database

    You may need to install SASxport from the archives, the haven version is not as flexible

    I updated the drop down macro to python  utl_submit_py64_310x, on end
    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories

    /*               _     _
     _ __  _ __ ___ | |__ | | ___ _ __ ___
    | `_ \| `__/ _ \| `_ \| |/ _ \ `_ ` _ \
    | |_) | | | (_) | |_) | |  __/ | | | | |
    | .__/|_|  \___/|_.__/|_|\___|_| |_| |_|
    |_|
    */

    /************************************************************************************************************************/
    /*                                                                                                                      */
    /*              INPUT (PROCESSED RAW INPUT (ADDED NUMBER OD SECONdS SINCE 1-1-1970)                                     */
    /*              ===================================================================                                     */
    /*                                                                                                                      */
    /*  CHARACTER DATES HAVE BEEN COVERTED TO NUMBER OF SECONDS SINCE 1-01-1970                                             */
    /*                                                                                                                      */
    /*  PATIENT  AETERM      STDT       ENDT      NUM_STDT  NUM_VISDT   NUM_ENDT                                            */
    /*                                                                                                                      */
    /* 0  1002  FOOTPAIN  2015-05-28  2015-05-30 1432771200 1432684800 1432944000                                           */
    /* 1  1002  BACKPAIN  2015-06-23  2015-06-22 1435017600 1434931200 1434931200                                           */
    /* 2  1002  ELBOPAIN  2015-06-20  2015-06-24 1434758400 1434931200 1435104000                                           */
    /* 3  1002  HEADACHE  2015-10-12  2015-10-21 1444608000 1444694400 1445385600                                           */
    /* 4  1002  KNEEPAIN  2015-11-14  2015-12-30 1447459200 1444780800 1451433600                                           */
    /*                                                                                                                      */
    /*                                                                                                                      */
    /*             PROCESS (86400 IS THE NUMBER SECONS IN A DAY - DO NOT NEED THE CHARACTER DATE VARIABLES                  */
    /*             ========================================================================================                 */
    /*  select                                                                                                              */
    /*    patient                                                                                                           */
    /*   ,aeterm                                                                                                            */
    /*   ,date(num_stdt, \'unixepoch\' )        as chr_stdt                                                                 */
    /*   ,date(num_visdt, \'unixepoch\')        as chr_visdt                                                                */
    /*   ,date(num_endt, \'unixepoch\' )        as chr_endt                                                                 */
    /*   ,date(num_stdt-7*86400, \'unixepoch\') as stdt_minus_7_days                                                        */
    /*   ,date(num_stdt+7*86400, \'unixepoch\') as stdt_plus_7_days                                                         */
    /*   ,date(stdt, \'+1 months\')             as stdt_plus_1_month                                                        */
    /*   ,date(date(num_stdt, \'unixepoch\' ), \'+1 months\') as nstdt_plus_1_month  ==same as previous==                   */
    /*   ,(num_endt-num_stdt)/86400             as days_endt_minus_stdt                                                     */
    /*  from                                                                                                                */
    /*    adv                                                                                                               */
    /*  where                                                                                                               */
    /*    num_visdt > num_stdt or date(num_stdt, \'unixepoch\' > \'2015-05-27\' )                                           */
    /*                                                                                                                      */
    /*                                                                                                                      */
    /*            OUTPUT Converting days since 1-1-1970 to character dates)                                                 */
    /*            ======                                                                                                    */
    /*                                                                                                                      */
    /*                                              STDT_                               STDT_   WINDOW                      */
    /*                                             MINUS_                STDT_PLUS_   PLUS_1_   DAYS_ENDT_                  */
    /*  PATIENT   AETERM    CHR_STDT   CHR_ENDT    7_DAYS     CHR_STDT     7_DAYS      MONTH    MINUS_STDT                  */
    /*                                                                                                                      */
    /*    1002   ELBOPAIN  2015-06-20 2015-06-24 2015-06-13  2015-06-20  2015-06-27  2015-07-20      4                      */
    /*    1002   HEADACHE  2015-10-12 2015-10-21 2015-10-05  2015-10-12  2015-10-19  2015-11-12      9                      */
    /*                                                                                                                      */
    /************************************************************************************************************************/

    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=upcase;
    data sd1.have;
    retain patient;
    format
       aeterm $8. stdt visdt endt date9. ;
    informat
       aeterm $8. stdt visdt endt date9.;
    input patient aeterm visdt stdt endt;
    cards4;
    1002 FOOTPAIN 27MAY2015 28MAY2015 30MAY2015.
    1002 BACKPAIN 22JUN2015 23JUN2015 22JUN2015
    1002 ELBOPAIN 22JUN2015 20JUN2015 24JUN2015
    1002 HEADACHE 13OCT2015 12OCT2015 21OCT2015
    1002 KNEEPAIN 14OCT2015 14NOV2015 30DEC2015
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* SD1.HAVE with formatted variables  total obs=5                                                                         */
    /*                                                                                                                        */
    /* Obs    PATIENT     AETERM       STDT         VISDT        ENDT                                                         */
    /*                                                                                                                        */
    /*  1       1002     FOOTPAIN    28MAY2015    27MAY2015    30MAY2015                                                      */
    /*  2       1002     BACKPAIN    23JUN2015    22JUN2015    22JUN2015                                                      */
    /*  3       1002     ELBOPAIN    20JUN2015    22JUN2015    24JUN2015                                                      */
    /*  4       1002     HEADACHE    12OCT2015    13OCT2015    21OCT2015                                                      */
    /*  5       1002     KNEEPAIN    14NOV2015    14OCT2015    30DEC2015                                                      */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
    /*                                                                     _   _
     _ __  _ __ ___ _ __  _ __ ___   ___ ___  ___ ___   _ __   _ __  _   _| |_| |__   ___  _ __
    | `_ \| `__/ _ \ `_ \| `__/ _ \ / __/ _ \/ __/ __| | `__| | `_ \| | | | __| `_ \ / _ \| `_ \
    | |_) | | |  __/ |_) | | | (_) | (_|  __/\__ \__ \ | |    | |_) | |_| | |_| | | | (_) | | | |
    | .__/|_|  \___| .__/|_|  \___/ \___\___||___/___/ |_|    | .__/ \__, |\__|_| |_|\___/|_| |_|
    |_|            |_|                                        |_|    |___/
                 _   _
     _ __  _   _| |_| |__   ___  _ __
    | `_ \| | | | __| `_ \ / _ \| `_ \
    | |_) | |_| | |_| | | | (_) | | | |
    | .__/ \__, |\__|_| |_|\___/|_| |_|
    |_|    |___/
    */

    /*----                                                                   ----*/
    /*---- convert python character date type to seconds since 1-1-1970      ----*/
    /*---- It seems that you almost have to learn a new language to handl    ----*/
    /*---- unneeded new data types (less is more)                            ----*/
    /*----                                                                   ----*/

    %utl_submit_py64_310x('
    import pyreadstat as ps;
    from os import path;
    import pandas as pd;
    import numpy as np;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    sqlite3conn.load_extension("c:/temp/libsqlitefunctions.dll");
    mysql = lambda q: sqldf(q, globals());
    ae, meta = ps.read_sas7bdat("d:/sd1/have.sas7bdat");
    ae.info();
    print(ae);
    offset="2440587.5";
    advtxt = f"""
     select
        patient
       ,aeterm
       ,stdt
       ,visdt
       ,endt
       ,(julianday(stdt) -{offset})*86400 as num_stdt
       ,(julianday(visdt)-{offset})*86400 as num_visdt
       ,(julianday(endt) -{offset})*86400 as num_endt
     from
        ae
    """;
    adv = pdsql(advtxt);
    pd.set_option("display.max_columns", None);
    pd.options.display.float_format = "{:.2f}".format ;
    print(adv);
    adv.info();
    adv.to_pickle("d:/pkl/adv.pkl");
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /* PREPROCESSED RAW DATA FOR SQL DATE PROCESSING PYTHON                                                                   */
    /*                                                                   Added these columns                                  */
    /*                            STRINGS IN R and PYTHON        Converted python character dates to unix epoc                */
    /*                       ==================================  =============================================                */
    /*                                                                                                                        */
    /*    PATIENT    AETERM        STDT       VISDT        ENDT      NUM_STDT      NUM_VISDT      NUM_ENDT                    */
    /*                                                                                                                        */
    /* 0  1002.00  FOOTPAIN  2015-05-28  2015-05-27  2015-05-30 1432771200.00  1432684800.00 1432944000.00                    */
    /* 1  1002.00  BACKPAIN  2015-06-23  2015-06-22  2015-06-22 1435017600.00  1434931200.00 1434931200.00                    */
    /* 2  1002.00  ELBOPAIN  2015-06-20  2015-06-22  2015-06-24 1434758400.00  1434931200.00 1435104000.00                    */
    /* 3  1002.00  HEADACHE  2015-10-12  2015-10-13  2015-10-21 1444608000.00  1444694400.00 1445385600.00                    */
    /* 4  1002.00  KNEEPAIN  2015-11-14  2015-10-14  2015-12-30 1447459200.00  1444780800.00 1451433600.00                    */
    /*                                                                                                                        */
    /* Pickle file                                                                                                            */
    /*                                                                                                                        */
    /*   d:/pkl/adv.pkl    1,299 bytes                                                                                        */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*
     _ __
    | `__|
    | |
    |_|

    */

    /*----                                                                   ----*/
    /*---- note I am passing an R constant to sqldf                          ----*/
    /*----                                                                   ----*/

    %utlfkil(d:/rds/adv.rds);

    %utl_submit_r64x('
    library(haven);
    library(sqldf);
    ae<-read_sas("d:/sd1/have.sas7bdat");
    offset<-"2440587.5";
    adv<-fn$sqldf("
     select
        patient
       ,aeterm
       ,stdt
       ,visdt
       ,endt
       ,((julianday(stdt) -$offset)+$offset)*86400    as num_stdt
       ,((julianday(visdt) -$offset)+$offset)*86400   as num_visdt
       ,((julianday(endt) -$offset)+$offset)*86400    as num_endt
     from
        ae
     where
       num_visdt > num_stdt or date(num_stdt, \'unixepoch\' > \'2015-05-27\' )
    ");
    adv;
    save(adv, file = "d:/rds/adv.rds");
    ');

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                  Added this columns                                    */
    /*                                                          Converted r character dates types to uinix epoc               */
    /*                                                          ================================================              */
    /*                                                                                                                        */
    /*   PATIENT   AETERM       STDT      VISDT       ENDT               NUM_STDT  NUM_VISDT   NUM_ENDT                       */
    /*                                                                                                                        */
    /* 1    1002 FOOTPAIN 2015-05-28 2015-05-27 2015-05-30             1432771200 1432684800 1432944000                       */
    /* 2    1002 BACKPAIN 2015-06-23 2015-06-22 2015-06-22             1435017600 1434931200 1434931200                       */
    /* 3    1002 ELBOPAIN 2015-06-20 2015-06-22 2015-06-24             1434758400 1434931200 1435104000                       */
    /* 4    1002 HEADACHE 2015-10-12 2015-10-13 2015-10-21             1444608000 1444694400 1445385600                       */
    /* 5    1002 KNEEPAIN 2015-11-14 2015-10-14 2015-12-30             1447459200 1444780800 1451433600                       */
    /*                                                                                                                        */
    /*                                                                                                                        */
    /* RDS file (single dataframe file)                                                                                       */
    /*                                                                                                                        */
    /*   d:/rds/adv.rds  381 bytes (zipped-unzipped 816 bytes)                                                                */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*         _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __  ___
    / __|/ _ \| | | | | __| |/ _ \| `_ \/ __|
    \__ \ (_) | | |_| | |_| | (_) | | | \__ \
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|___/

    */

    /*               _   _                             _
    / |  _ __  _   _| |_| |__   ___  _ __    ___  __ _| |
    | | | `_ \| | | | __| `_ \ / _ \| `_ \  / __|/ _` | |
    | | | |_) | |_| | |_| | | | (_) | | | | \__ \ (_| | |
    |_| | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__, |_|
        |_|    |___/                                |_|
    */

    %utlfkil("d:/xpt/want.xpt");

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;
    proc datasets lib=work nolist mt=data mt=view nodetails;delete want want_py_long_names; run;quit;


    %utl_submit_py64_310x("
    import pyreadstat as ps;
    from os import path;
    import pandas as pd;
    import numpy as np;
    from pandasql import sqldf;
    mysql = lambda q: sqldf(q, globals());
    from pandasql import PandaSQL;
    pdsql = PandaSQL(persist=True);
    sqlite3conn = next(pdsql.conn.gen).connection.connection;
    sqlite3conn.enable_load_extension(True);
    mysql = lambda q: sqldf(q, globals());
    adv = pd.read_pickle('d:/pkl/adv.pkl');
    print(adv);
    sqltxt = f'''
      select
        patient
       ,aeterm
       ,date(num_stdt, \'unixepoch\' )        as chr_stdt
       ,date(num_visdt, \'unixepoch\')        as chr_visdt
       ,date(num_endt, \'unixepoch\' )        as chr_endt
       ,date(num_stdt-7*86400, \'unixepoch\') as stdt_minus_7_days
       ,date(num_stdt+7*86400, \'unixepoch\') as stdt_plus_7_days
       ,date(stdt, \'+1 months\')             as stdt_plus_1_month
       ,date(date(num_stdt, \'unixepoch\' ), \'+1 months\') as nstdt_plus_1_month
       ,(num_endt-num_stdt)/86400             as days_endt_minus_stdt
      from
        adv
      where
        num_visdt > num_stdt or date(num_stdt, \'unixepoch\' > \'2015-05-27\' )

    ''';
    want = pdsql(sqltxt);
    pd.set_option('display.max_columns', None);
    print(want);
     ps.write_xport(want,'d:\\xpt\\want.xpt',table_name='want',file_format_version=5
     ,column_labels=[
      'PATIENT','AETERM','CHR_STDT','CHR_VISDT','CHR_ENDT','STDT_MINUS_7_DAYS','STDT_PLUS_7_DAYS'
      ,'STDT_PLUS_1_MONTH','NSTDT_PLUS_1_MONTH','DAYS_ENDT_MINUS_STDT']);
    ");

    /*--- handles long variable names by using the label to rename the variables  ----*/

    libname xpt xport "d:/xpt/want.xpt";
    proc contents data=xpt._all_;
    run;quit;

    proc print data=xpt.want;
    run;quit;

    proc datasets lib=work nolist mt=data mt=view nodetails;delete want want_py_long_names; run;quit;

    data want_py_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;
    libname xpt clear;

    proc print data=want_py_long_names width=min;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                      STDT_                       NSTDT_                */
    /*                                                                      MINUS_      ENDT_PLUS_     PLUS_1_      DAYS_ENDT_*/
    /* Obs PATIENT   AETERM      CHR_STDT     CHR_VISDT      CHR_ENDT       7_DAYS        7_DAYS        MONTH       MINUS_STDT*/
    /*                                                                                                                        */
    /*  1    1002   ELBOPAIN    2015-06-20    2015-06-22    2015-06-24    2015-06-13    2015-06-27    2015-07-20         4    */
    /*  2    1002   HEADACHE    2015-10-12    2015-10-13    2015-10-21    2015-10-05    2015-10-19    2015-11-12         9    */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                     _
    |___ \   _ __   ___  __ _| |
      __) | | `__| / __|/ _` | |
     / __/  | |    \__ \ (_| | |
    |_____| |_|    |___/\__, |_|
                           |_|
    */

    %utlfkil("d:/xpt/want.xpt");

    proc datasets lib=sd1 nolist mt=data mt=view nodetails;delete want; run;quit;
    proc datasets lib=work nolist mt=data mt=view nodetails;delete want want_py_long_names; run;quit;

    /*----                                                                   ----*/
    /*---- 86400 is the number of seconds ia a day                           ----*/
    /*---- I wish R would recognize that num_stdt was a number and default   ----*/
    /*---- to unixepoch                                                      ----*/
    /*----                                                                   ----*/

    %utl_submit_r64x("
    library(haven);
    library(sqldf);
    library(SASxport);
    load('d:/rds/adv.rds');
    save(adv, file = 'd:/rds/adv.rds');
    adv;
    want<-sqldf('
      select
        patient
       ,aeterm
       ,date(num_stdt, \'unixepoch\' )        as chr_stdt
       ,date(num_visdt, \'unixepoch\')        as chr_visdt
       ,date(num_endt, \'unixepoch\' )        as chr_endt
       ,date(num_stdt-7*86400, \'unixepoch\') as stdt_minus_7_days
       ,date(date(num_stdt, \'unixepoch\' ), \'+1 months\') as nstdt_plus_1_month
       ,(num_endt-num_stdt)/86400             as days_endt_minus_stdt
      from
        adv
      where
        num_visdt > num_stdt or date(num_stdt, \'unixepoch\' > \'2015-05-27\' )
    ');
    want;
    for (i in 1:ncol(want)) {
          label(want[,i])<-colnames(want)[i];
          print(label(want[,i])); };
    write.xport(want,file='d:/xpt/want.xpt');
    ");;

    /*--- handles long variable names by using the label to rename the variables  ----*/

    libname xpt xport "d:/xpt/want.xpt";
    proc contents data=xpt._all_;
    run;quit;

    data want_r_long_names;
      %utl_rens(xpt.want) ;
      set want;
    run;quit;
    libname xpt clear;

    proc print data=want_r_long_names;
    run;quit;

    /**************************************************************************************************************************/
    /*                                                                                                                        */
    /*                                                                      STDT_         NSTDT_                              */
    /*                                                                      MINUS_       PLUS_1_      DAYS_ENDT_              */
    /*   PATIENT     AETERM      CHR_STDT     CHR_VISDT      CHR_ENDT       7_DAYS        MONTH       MINUS_STDT              */
    /*                                                                                                                        */
    /*     1002     ELBOPAIN    2015-06-20    2015-06-22    2015-06-24    2015-06-13    2015-07-20         4                  */
    /*     1002     HEADACHE    2015-10-12    2015-10-13    2015-10-21    2015-10-05    2015-11-12         9                  */
    /*                                                                                                                        */
    /**************************************************************************************************************************/
                                                                       _   _                             _               _ _
    /*____              _                              _   _ __  _   _| |_| |__   ___  _ __    ___ _   _| |__  _ __ ___ (_) |_
    |___ /    ___ _ __ | |__   __ _ _ __   ___ ___  __| | | `_ \| | | | __| `_ \ / _ \| `_ \  / __| | | | `_ \| `_ ` _ \| | __|
      |_ \   / _ \ `_ \| `_ \ / _` | `_ \ / __/ _ \/ _` | | |_) | |_| | |_| | | | (_) | | | | \__ \ |_| | |_) | | | | | | | |_
     ___) | |  __/ | | | | | | (_| | | | | (_|  __/ (_| | | .__/ \__, |\__|_| |_|\___/|_| |_| |___/\__,_|_.__/|_| |_| |_|_|\__|
    |____/   \___|_| |_|_| |_|\__,_|_| |_|\___\___|\__,_| |_|    |___/


    */

    I updated the drop down macro to python  utl_submit_py64_310x, on end
    macros
    https://tinyurl.com/y9nfugth
    https://github.com/rogerjdeangelis/utl-macros-used-in-many-of-rogerjdeangelis-repositories

    %macro utl_submit_py64_310x(
          pgm
         ,return=  /* name for the macro variable from Python */
         )/des="Semi colon separated set of python commands - drop down to python";
      * delete temporary files;
      %local length
             _loc
             _stderr
             _stdout
      ;
      %utlfkil(%sysfunc(pathname(work))/py_pgm.py);
      %utlfkil(%sysfunc(pathname(work))/stderr.txt);
      %utlfkil(%sysfunc(pathname(work))/stdout.txt);
      %let len=%eval(%length(&pgm) + 4096);
      filename py_pgm "%sysfunc(pathname(work))/py_pgm.py" lrecl=32766 recfm=v;
      data _null_;
          length pgm  $32755 cmd cmd1 $32755;
          file py_pgm ;
          pgm=trim(resolve(&pgm));
          semi=countc(pgm,";");
            do idx=1 to semi;
              cmd=cats(scan(pgm,idx,";"));
              if cmd=:". " then
                 cmd=trim(substr(cmd,2));
               if index(cmd,"`") then cmd=tranwrd(cmd,"`","27"x);
               cmd=trim(cmd);
               put cmd $char32755.;
               cmd1=compbl(cmd);
               *putlog cmd1 $char&len..;
            end;
        run;quit;
      %let _loc   =%sysfunc(pathname(py_pgm));
      %let _stderr=%sysfunc(pathname(work))/stderr.txt;
      %let _stdout=%sysfunc(pathname(work))/stdout.txt;
      filename rut pipe  "d:\Python310\python.exe &_loc 2> &_stderr";
      data _null_;
        file print;
        infile rut;
        input;
        put _infile_;
      run;
      filename rut clear;
      filename py_pgm clear;
     data _null_;
        file print;
        infile "%sysfunc(pathname(work))/stderr.txt";
        input;
        put _infile_;
      run;
      filename rut clear;
      filename py_pgm clear;
      * use the clipboard to create macro variable;
      %if "&return" ^= "" %then %do;
        filename clp clipbrd ;
        data _null_;
         length txt $200;
         infile clp;
         input;
         putlog "xxxxxx  " _infile_;
         call symputx("&return",_infile_,"G");
        run;quit;
      %end;
    %mend utl_submit_py64_310x;

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
