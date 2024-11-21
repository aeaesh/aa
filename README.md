modifier_date <- function(date, unite, signe, valeur) {
  # Vérification des entrées
  if (!is.numeric(date) || !is.numeric(valeur) || !unite %in% c("mois", "annee") || !signe %in% c("+", "-")) {
    stop("Les paramètres doivent être : date (numérique), unite ('mois' ou 'annee'), signe ('+' ou '-'), valeur (numérique)")
  }
  
  # Conversion de la date en format R Date
  annee <- as.integer(date %/% 100)  # Extraction de l'année
  mois <- as.integer(date %% 100)   # Extraction du mois
  
  if (mois < 1 || mois > 12) {
    stop("Le mois dans la date doit être compris entre 1 et 12.")
  }
  
  # Calcul de l'ajustement
  ajustement <- if (signe == "+") valeur else -valeur
  
  if (unite == "mois") {
    mois <- mois + ajustement
    annee <- annee + (mois - 1) %/% 12
    mois <- (mois - 1) %% 12 + 1
  } else if (unite == "annee") {
    annee <- annee + ajustement
  }
  
  # Construction du nouveau format AAAAMM
  nouvelle_date <- annee * 100 + mois
  return(nouvelle_date)
}

# Exemple d'utilisation
modifier_date(201401, "mois", "+", 3)  # Devrait donner 201404
modifier_date(201405, "annee", "-", 1)  # Devrait donner 201305
