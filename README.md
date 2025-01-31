
 
ad.test.manuel <- function(x) {
  # Taille de l'échantillon
  n <- length(x)
  
  # Ordonner les données
  x_sorted <- sort(x)
  
  # Calculer les quantiles théoriques de la distribution normale
  mu <- mean(x)
  sigma <- sd(x)
  F <- pnorm(x_sorted, mean = mu, sd = sigma)  # Fonction de répartition de la normale
  
  # Calcul de la statistique A^2
  sum_term <- 0
  for (i in 1:n) {
    sum_term <- sum_term + (2 * i - 1) * (log(F[i]) + log(1 - F[n - i + 1]))
  }
  
  A2 <- -n - (1/n) * sum_term
  
  # Valeur p approximative via la table de Anderson-Darling ou une simulation (complexe ici)
  # Pour simplification, retournons A2 et une approximation de p-value (à affiner pour des tests rigoureux)
  p_value <- 1 - pnorm(sqrt(A2))  # Approximation très basique
  
  # Retourner les résultats
  return(list(statistic = A2, p_value = p_value))
}

# Exemple d'utilisation :
set.seed(123)
data <- rnorm(50)  # Données normalement distribuées
result <- ad.test.manuel(data)
print(result)
