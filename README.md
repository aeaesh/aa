Étape 1 : Identifier les colonnes communes
Déterminez les colonnes sur lesquelles effectuer la jointure et excluez explicitement defaut12m. Supposons que les colonnes de jointure soient id_contrat et periode.

Étape 2 : Préparer les bases
Ajoutez des préfixes aux colonnes des deux bases pour simplifier l'analyse des écarts après la jointure.

data A_prep;
    set A(rename=(defaut12m=defaut12m_A));
run;

data B_prep;
    set B(rename=(defaut12m=defaut12m_B));
run;
Étape 3 : Effectuer la jointure
Effectuez une jointure sur les deux colonnes communes (id_contrat et periode) et incluez toutes les colonnes pour la comparaison.

proc sql;
    create table comparaison as
    select 
        coalesce(A_prep.id_contrat, B_prep.id_contrat) as id_contrat,
        coalesce(A_prep.periode, B_prep.periode) as periode,
        A_prep.*, 
        B_prep.*
    from A_prep
    full join B_prep
    on A_prep.id_contrat = B_prep.id_contrat
       and A_prep.periode = B_prep.periode;
quit;
Étape 4 : Identifier les différences
Créez un indicateur pour chaque colonne afin de vérifier si les valeurs sont égales entre les deux bases, sauf pour defaut12m.

Générer une macro pour automatiser la comparaison des colonnes :

%macro comparer_colonnes();
    data comparaison_resultat;
        set comparaison;
        length ecart $500.;
        ecart = '';

        %let colonnes = variable1 variable2 variable3; /* Liste des colonnes sauf defaut12m */

        %do i = 1 %to %sysfunc(countw(&colonnes.));
            %let colonne = %scan(&colonnes., &i.);

            if A_prep.&colonne. ne B_prep.&colonne. then
                ecart = catx(', ', ecart, "&colonne.");
        %end;

        /* Indicateur global : s'il y a des différences */
        if ecart = '' then ecart = 'Aucune différence';
    run;
%mend;

%comparer_colonnes();
Cette macro compare toutes les colonnes spécifiées (par exemple, variable1, variable2, etc.) et liste les différences dans une variable ecart.

Étape 5 : Vérification globale
Pour vérifier si des écarts existent globalement :

proc freq data=comparaison_resultat;
    tables ecart / nocum;
run;
Étape 6 : Exporter les résultats
Vous pouvez exporter les résultats pour analyse plus approfondie.

proc export data=comparaison_resultat
    outfile="chemin/rapport_differences.xlsx"
    dbms=xlsx
    replace;
run;
Résultat attendu
Une table indiquant pour chaque enregistrement si les valeurs sont identiques ou différentes, colonne par colonne.
Une vue synthétique des écarts pour faciliter l'interprétation.
Besoin d'aide pour adapter cette méthode à vos données spécifiques ?






