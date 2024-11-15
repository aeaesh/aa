%macro compare_pairs(input_table, output_table);
    data &output_table;
        set &input_table;
        array vars {*} _numeric_; /* Inclure toutes les colonnes numériques sauf NAME */
        retain keep_flag 0; /* Indicateur pour garder les lignes */
        length reason $500; /* Liste des colonnes avec des différences */

        /* Comparer les paires de lignes (1 vs 2, 3 vs 4, etc.) */
        if mod(_n_, 2) = 1 then do;
            keep_flag = 0; /* Réinitialiser l'indicateur de garde */
            reason = '';   /* Réinitialiser la liste des différences */
        end;
        else do;
            /* Comparer chaque colonne des deux lignes successives */
            do i = 1 to dim(vars);
                if vars[i] ne lag(vars[i]) then do;
                    keep_flag = 1; /* Indiquer qu'il y a une différence */
                    reason = catx(',', reason, vname(vars[i])); /* Ajouter la colonne avec différence */
                end;
            end;
            if keep_flag = 1 then output; /* Garder la paire de lignes si des différences existent */
        end;
        drop i keep_flag; /* Supprimer les colonnes temporaires inutiles */
    run;
%mend compare_pairs;










