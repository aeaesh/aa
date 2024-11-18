ad_test <- function(x) {
  # Vérification de la longueur de l'échantillon
  if (length(x) <= 7) {
    stop("Le nombre de valeurs dans x doit être supérieur à 7")
  }

  # Préparation des données et calcul des paramètres
  n <- length(x)
  x_sorted <- sort(x, na.last = NA)
  mu <- mean(x, na.rm = TRUE)
  sigma <- sd(x, na.rm = TRUE)

  # Transformation en valeurs de probabilité pour la loi normale
  z <- pnorm((x_sorted - mu) / sigma)

  # Calcul de la statistique d'Anderson-Darling
  A2 <- -n - sum((2 * (1:n) - 1) * (log(z) + log(1 - rev(z))) / n)
  
  # Ajustement de la statistique pour une distribution normale
  A2_star <- A2 * (1 + 0.75 / n + 2.25 / (n^2))

  # Calcul de la p-valeur en utilisant une approximation via la distribution de Pearson
  # ou un ajustement pour que la statistique s'approche d'une distribution bien connue
  p_value <- 1 - pchisq(A2_star, df = 1) # df=1 ici est une approximation pour les grands n
  
  # Création de la liste de sortie avec la classe htest
  result <- list(
    statistic = A2_star,
    p.value = p_value,
    method = "Anderson-Darling normality test",
    data.name = deparse(substitute(x))
  )
  
  class(result) <- "htest"
  return(result)
}

# Exemple d'utilisation
x <- rnorm(100)  # Génération d'un échantillon de données normales
result <- ad_test(x)
print(result)








