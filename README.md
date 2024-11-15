%macro freq_percent(table1, table2, vars);

    %let var_count = %sysfunc(countw(&vars)); /* Nombre de variables qualitatives */
    
    %do i = 1 %to &var_count;
        %let var = %scan(&vars, &i); /* Extraire chaque variable */
        
        /* Pour la première table */
        proc freq data=&table1 noprint;
            tables &var / out=table1_freq_&var (keep=&var percent);
        run;

        /* Pour la deuxième table */
        proc freq data=&table2 noprint;
            tables &var / out=table2_freq_&var (keep=&var percent);
        run;

        /* Créer un tableau combiné des pourcentages */
        proc sql;
            create table freq_result_&var as
            select 
                coalesce(a.&var, b.&var) as &var,
                a.percent as percent_table1,
                b.percent as percent_table2
            from table1_freq_&var as a
            full join table2_freq_&var as b
            on a.&var = b.&var;
        quit;

        /* Résultat : Table freq_result_&var contient les pourcentages pour cette variable */
    %end;

%mend;

/* Exemple d'appel */
%freq_percent(table1=A_prep, table2=B_prep, vars=var1 var2 var3);


%macro compare_percent_periods(base_name, periods, vars);

    %let var_count = %sysfunc(countw(&vars)); /* Nombre de variables qualitatives */
    %let period_count = %sysfunc(countw(&periods)); /* Nombre de périodes */

    %do i = 1 %to &var_count;
        %let var = %scan(&vars, &i); /* Extraire chaque variable */

        /* Créer une table vide pour accumuler les résultats */
        data compare_result_&var;
            length period $20. &var $50. percent 8.;
        run;

        %do j = 1 %to &period_count;
            %let period = %scan(&periods, &j); /* Extraire chaque période */
            
            /* Générer le nom de la table */
            %let table = &base_name._&period;

            /* Effectuer le PROC FREQ pour la période actuelle */
            proc freq data=&table noprint;
                tables &var / out=freq_&var._&period (keep=&var percent);
            run;

            /* Ajouter la colonne période */
            data freq_&var._&period;
                set freq_&var._&period;
                period = "&period";
            run;

            /* Ajouter les résultats à la table cumulative */
            proc append base=compare_result_&var data=freq_&var._&period force;
            run;
        %end;

        /* Transformer les résultats pour un tableau final : périodes = colonnes */
        proc transpose data=compare_result_&var out=final_compare_&var(drop=_name_);
            by &var;
            id period;
            var percent;
        run;

        /* Résultat : final_compare_&var contient le tableau comparatif pour cette variable */
    %end;

%mend;

/* Exemple d'appel */
%compare_percent_periods(base_name=A, periods=201001 201002 201003, vars=var1 var2 var3);







%macro freq_percent(table1, table2, vars);

    %let var_count = %sysfunc(countw(&vars)); /* Nombre de variables qualitatives */
    
    %do i = 1 %to &var_count;
        %let var = %scan(&vars, &i); /* Extraire chaque variable */
        
        /* Pour la première table */
        proc freq data=&table1 noprint;
            tables &var / out=table1_freq_&var (keep=&var percent);
        run;

        /* Pour la deuxième table */
        proc freq data=&table2 noprint;
            tables &var / out=table2_freq_&var (keep=&var percent);
        run;

        /* Créer un tableau combiné des pourcentages */
        proc sql;
            create table freq_result_&var as
            select 
                coalesce(a.&var, b.&var) as &var,
                a.percent as percent_table1,
                b.percent as percent_table2
            from table1_freq_&var as a
            full join table2_freq_&var as b
            on a.&var = b.&var;
        quit;

        /* Résultat : Table freq_result_&var contient les pourcentages pour cette variable */
    %end;

%mend;

/* Exemple d'appel */
%freq_percent(table1=A_prep, table2=B_prep, vars=var1 var2 var3);


%macro compare_percent_periods(base_name, periods, vars);

    %let var_count = %sysfunc(countw(&vars)); /* Nombre de variables qualitatives */
    %let period_count = %sysfunc(countw(&periods)); /* Nombre de périodes */

    %do i = 1 %to &var_count;
        %let var = %scan(&vars, &i); /* Extraire chaque variable */

        /* Créer une table vide pour accumuler les résultats */
        data compare_result_&var;
            length period $20. &var $50. percent 8.;
        run;

        %do j = 1 %to &period_count;
            %let period = %scan(&periods, &j); /* Extraire chaque période */
            
            /* Générer le nom de la table */
            %let table = &base_name._&period;

            /* Effectuer le PROC FREQ pour la période actuelle */
            proc freq data=&table noprint;
                tables &var / out=freq_&var._&period (keep=&var percent);
            run;

            /* Ajouter la colonne période */
            data freq_&var._&period;
                set freq_&var._&period;
                period = "&period";
            run;

            /* Ajouter les résultats à la table cumulative */
            proc append base=compare_result_&var data=freq_&var._&period force;
            run;
        %end;

        /* Transformer les résultats pour un tableau final : périodes = colonnes */
        proc transpose data=compare_result_&var out=final_compare_&var(drop=_name_);
            by &var;
            id period;
            var percent;
        run;

        /* Résultat : final_compare_&var contient le tableau comparatif pour cette variable */
    %end;

%mend;

/* Exemple d'appel */
%compare_percent_periods(base_name=A, periods=201001 201002 201003, vars=var1 var2 var3);










