/* Étape 1 : Obtenir les noms des colonnes commençant par "FLAG" */
proc sql noprint;
    select name into :flag_columns separated by ' '
    from dictionary.columns
    where libname = 'WORK' /* Remplacer 'WORK' par la bibliothèque où est stockée la table */
      and memname = 'VOTRE_TABLE' /* Remplacer par le nom de votre table */
      and upcase(name) like 'FLAG%'; /* Colonnes commençant par "FLAG" */
quit;

/* Étape 2 : Isoler les observations avec des valeurs manquantes dans ces colonnes */
data flags_missing;
    set VOTRE_TABLE; /* Remplacer par le nom de votre table */
    array flags {*} &flag_columns; /* Liste des colonnes FLAG récupérées */
    missing_count = 0; /* Compteur de valeurs manquantes */
    do i = 1 to dim(flags);
        if missing(flags[i]) then missing_count + 1;
    end;
    if missing_count > 0 then output; /* Conserver uniquement les lignes avec valeurs manquantes */
    drop i missing_count; /* Nettoyage des variables temporaires */
run;

/* Étape 3 : Analyser les données manquantes */
proc print data=flags_missing;
    title "Lignes avec des valeurs manquantes dans les colonnes FLAG";
run;









