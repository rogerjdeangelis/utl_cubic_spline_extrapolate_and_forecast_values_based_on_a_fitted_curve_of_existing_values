# utl_cubic_spline_extrapolate_and_forecast_values_based_on_a_fitted_curve_of_existing_values
Cubic spline extrapolate or forecast values based on a fitted curve of existing values.  Keywords: sas sql join merge big data analytics macros oracle teradata mysql sas communities stackoverflow statistics artificial inteligence AI Python R Java Javascript WPS Matlab SPSS Scala Perl C C# Excel MS Access JSON graphics maps NLP natural language processing machine learning igraph DOSUBL DOW loop stackoverflow SAS community.
    Cubic spline extrapolate or forecast values based on a fitted curve of existing values

    WPS Proc R

    see github
    https://tinyurl.com/yam7peu7
    https://github.com/rogerjdeangelis/utl_cubic_spline_extrapolate_and_forecast_values_based_on_a_fitted_curve_of_existing_values

    https://tinyurl.com/yb3se2st
    https://communities.sas.com/t5/SAS-Forecasting-and-Econometrics/How-to-extrapolate-or-forecast-values-based-on-a-fitted-curve-of/m-p/47

    TheLateMail profile
    https://stackoverflow.com/users/496803/thelatemail

    INPUT
    =====

     SD1.HAVE total obs=8

         X        Y

       2008     .
       2009     .
       2010    0.3624
       2011    0.3971
       2012    0.4366
       2013    0.4804
       2014    0.5291
       2015    0.5859

    0.6 +
        |                             *
        |
        |
        |                         *
    0.5 +
        |                     *
    Y   |
        |                 *
        |
    0.4 +             *
        |
        |         *
        |
        |     ??   Need to fill in these two
    0.3 +          missing points
        |  ??
        --+-------+-------+-------+-------+-
        2008    2010    2012    2014    2016

                          X

    EXAMPLE OUTPUT
    --------------

      SD1.HAVE total obs=8

          X        Y     EST_Y

        2008     .      0.29506
        2009     .      0.32873
        2010    0.3624  0.36240
        2011    0.3971  0.39710
        2012    0.4366  0.43660
        2013    0.4804  0.48040
        2014    0.5291  0.52910
        2015    0.5859  0.58590


    PROCESS (WPS Proc R working code)
    ===================================
      smoo <- with(have[!is.na(have$Y),],smooth.spline(X,Y));
      want <- with(have,predict(smoo,X[is.na(Y)]));

      COMMENT
      Cubic spline two stage fit.
      Fit splines without missing then use result to predict missing.


    OUTPUT
    ======

      WORK.WANT total obs=8

          X         Y

        2008   0.29506
        2009   0.32873
        2010   0.36240
        2011   0.39710
        2012   0.43660
        2013   0.48040
        2014   0.52910
        2015   0.58590


    0.6 +
        |                             *
        |
        |
        |                         *
    0.5 +
        |                     *
    YYY |
        |                 *
        |
    0.4 +             *
        |
        |         *
        |
        |     E   Here are the estimates
    0.3 + E
        |
        --+-------+-------+-------+-------+-
        2008    2010    2012    2014    2016

                          X
    *                _               _       _
     _ __ ___   __ _| | _____     __| | __ _| |_ __ _
    | '_ ` _ \ / _` | |/ / _ \   / _` |/ _` | __/ _` |
    | | | | | | (_| |   <  __/  | (_| | (_| | || (_| |
    |_| |_| |_|\__,_|_|\_\___|   \__,_|\__,_|\__\__,_|

    ;

    options validvarname=upcase;
    libname sd1 "d:/sd1";
    data sd1.have;
     input x y;
    cards4;
    2008 .
    2009 .
    2010 0.3624
    2011 0.3971
    2012 0.4366
    2013 0.4804
    2014 0.5291
    2015 0.5859
    ;;;;
    run;quit;

    *          _       _   _
     ___  ___ | |_   _| |_(_) ___  _ __
    / __|/ _ \| | | | | __| |/ _ \| '_ \
    \__ \ (_) | | |_| | |_| | (_) | | | |
    |___/\___/|_|\__,_|\__|_|\___/|_| |_|

    ;

    %utl_submit_wps64('
    libname sd1 "d:/sd1";
    options set=R_HOME "C:/Program Files/R/R-3.3.2";
    libname wrk  sas7bdat "%sysfunc(pathname(work))";
    libname hlp  sas7bdat "C:\Progra~1\SASHome\SASFoundation\9.4\core\sashelp";
    proc r;
    submit;
    source("C:/Program Files/R/R-3.3.2/etc/Rprofile.site", echo=T);
    source("C:/Program Files/R/R-3.3.2/etc/Rprofile.site", echo=T);
    library(haven);
    have<-read_sas("d:/sd1/have.sas7bdat");
    have;
    smoo <- with(have[!is.na(have$Y),],smooth.spline(X,Y));
    want <- with(have,predict(smoo,X[is.na(Y)]));
    endsubmit;
    import r=want data=wrk.want;
    run;quit;
    ');

    data wantFix;
      update sd1.have want;
      by x;
    run;quit;

