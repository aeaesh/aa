%macro rename_with_prefix(data=, out=, prefix=);
    proc sql;
        select name
        into :col_list separated by ' '
        from dictionary.columns
        where libname = 'WORK' and memname = upcase("&data.") and name not in ('ID', 'ID2');
    quit;

    data &out.;
        set &data.;
        rename 
            %do i = 1 %to %sysfunc(countw(&col_list.));
                %let col = %scan(&col_list., &i);
                &col = &prefix.&col
            %end;
        ;
    run;
%mend;

/* Ajouter le préfixe A_ et B_ */
%rename_with_prefix(data=A, out=A_prefixed, prefix=A_);
%rename_with_prefix(data=B, out=B_prefixed, prefix=B_);

/* Faire la jointure */
proc sql;
    create table comparison as
    select 
        A.id,
        A.id2,
        A.*,
        B.*
    from A_prefixed as A
    full join B_prefixed as B
    on A.id = B.id and A.id2 = B.id2;
quit;








