# utl-obtain-the-sum-of-squared-elements-of-an-array-in-one-line
Obtain the sum of squared elements of an array in one line
    Obtain the sum of squared elements of a datastep array in one line

    * for datastep column and row redution of arrays;
    * see Rick's comment about IML, I agree.
    https://github.com/rogerjdeangelis/utl_datastep_array_row_col_reductions_sumOf

    PROBLEM Obvious code fails?
    =======

        1. Sum the squares of each element of an array
        2. Sum the square of each element of an array

    Many other functions are possible.

    data _null;;

      array mat[3] (4,4,4);
      sumSquares=sum(of mat[*]**2);
      sumRoots=sum(of sqrt(mat[*]));
      put sumSquares=;
      put sumRoots=;

    run;quit;

    LOG

    714   data _null;;
    715     array mat[3] (4,4,4);
    716     sumSquares=sum(of mat[*]**2);
                                    --
                                    22
                                    200
    717     sumRoots=sum(of sqrt(mat[*]));
                                     -
                                     386
                                     200
                                     76
    ERROR: Undeclared array referenced: SQRT.
    ERROR 22-322: Syntax error, expecting one of the following: a name, ), ',', _ALL_, _CHARACTER_, _CHAR_, _NUMERIC_.
    ERROR 200-322: The symbol is not recognized and will be ignored.
    ERROR 386-185: Expecting an arithmetic expression.
    ERROR 76-322: Syntax error, statement will be ignored.


    SOLUTION
    ========

    Sum SquareRoots;

    data _null;;

      array mat[3] (4,4,4);
      sumSquares = sum(%utl_aryFun(mat,3,fun=%str(**2)));
      sumRoots   = sum(%utl_aryFun(mat,3,fun=sqrt));
      put sumSquares=;
      put sumRoots=;

    run;quit;

    SUMSQUARES=48
    SUMROOTS=6

    *
     _ __ ___   __ _  ___ _ __ ___
    | '_ ` _ \ / _` |/ __| '__/ _ \
    | | | | | | (_| | (__| | | (_) |
    |_| |_| |_|\__,_|\___|_|  \___/

    ;

    %symdel ary len fun / nowarn;

    %macro utl_aryFun(ary,len,fun=%str(**2))/des="sum an array after appling an arbitrary function to each element";

      %let rc=%sysfunc(dosubl('
         data chk;
            length lst $32756;
            do idx=1 to symgetn("len");
              if notalpha(substr(symget("fun"),1,1)) then
                lst=catx(",",lst,cats("(",symget("ary"),"[",idx,"])",symget("fun")));
            else
                lst=catx(",",lst,cats(symget("fun"),"(",symget("ary"),"[",idx,"])"));

            end;
            call symputx("lst",lst,"G");
         run;quit;
         '));
      &lst

    %mend utl_aryFun;

    ** test CASE;;
    %put %utl_aryFun(zzz,3,fun=%str(**2));

    (zzz[1])**2,(zzz[2])**2,(zzz[3])**2

    *               _
      ___ _ __   __| |
     / _ \ '_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    ;

