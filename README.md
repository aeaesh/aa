%else %do;
            /* Ajouter les nouvelles colonnes directement */
            proc sql;
                alter table freq_result_combined
                add percent_table1_&i_synt num,
                    percent_table2_&i_synt num;
                
                update freq_result_combined as c
                set percent_table1_&i_synt = 
                    (select percent from table1_freq_&var as a where a.&var = c.&var),
                    percent_table2_&i_synt = 
                    (select percent from table2_freq_&var as b where b.&var = c.&var);
            quit;
        %end;









