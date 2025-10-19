# Atelier 1 : Système CBIR
## Question 1 : CBIR avec intensités en niveaux de gris

CBIR (Content-Based Image Retrieval) consiste à rechercher des images dans une base de données en se basant sur leur contenu visuel (et non sur des mots-clés ou métadonnées).

Ici, la consigne est d’utiliser les intensités des pixels en niveaux de gris comme caractéristique.

Étapes principales :

a) Conversion en niveaux de gris

Chaque image RGB est convertie en image en niveaux de gris (1 canal).

La valeur de chaque pixel est entre 0 et 255.

Cela réduit la dimensionnalité et met l’accent sur l’information de luminosité et texture globale.

gray_image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)


b) Représentation de l’image comme vecteur

Pour un CBIR simple basé sur intensité, l’image peut être aplatie en un vecteur 1D :

feature_vector = gray_image.flatten()

Si les images sont de tailles différentes, il faudra redimensionner toutes les images à une taille standard, par exemple 128x128 :

gray_image = cv2.resize(gray_image, (128, 128))

feature_vector = gray_image.flatten()  # vecteur de taille 16384 (128*128)
c) Mesure de similarité

Une fois que chaque image de la base est représentée par son vecteur d’intensité, on peut comparer une image requête avec toutes les images de la base.

Les mesures courantes sont :

Distance Euclidienne 
Distance de Manhattan (somme des différences absolues)
Cosine similarity (si vecteur est normalisé)

dist = np.linalg.norm(feature_vector_query - feature_vector_db)

d) Récupération des images similaires

On trie toutes les images de la base par distance croissante.

Les images avec les plus petites distances sont considérées comme les plus proches visuellement de la requête.

e) Affichage des résultats

Pour visualiser la robustesse ou la performance, on peut afficher les k images les plus proches côte à côte avec la requête.
plt.figure(figsize=(12, 4))
plt.subplot(1, len(top_images)+1, 1)
plt.imshow(gray_query, cmap='gray')
plt.title("Requête")
for i, img in enumerate(top_images):
    plt.subplot(1, len(top_images)+1, i+2)
    plt.imshow(img, cmap='gray')
plt.show()

Explications / Points importants :

Avantages :

Méthode simple et rapide.

Fonctionne bien si les images ont des différences de couleurs minimes.

Limites :

Sensible aux variations de rotation, translation et échelle.

Ne capture pas bien les couleurs et motifs complexes.

Avec des images très différentes mais avec luminosité similaire, des confusions peuvent apparaître.

Prétraitements possibles :

Normaliser les intensités pour éviter que les images trop sombres ou trop claires ne biaisent la distance.

Appliquer un filtrage (lissage, débruitage) avant de construire le vecteur.

## Question 2 : CBIR avec extraction de caractéristiques (features)

L’idée est de représenter chaque image par un vecteur de caractéristiques concaténées, plutôt que seulement par les intensités en niveaux de gris.

A. Couleurs 1 : Moments statistiques (Mean & Std)

Pour chaque canal R, G, B, on calcule :

Moyenne (mean)

Écart-type (std)

On obtient donc 6 caractéristiques par image (3 canaux × 2 moments)

Permet de capturer l’information globale des couleurs.

B. Couleurs 2 : Histogramme quantifié HSV

Convertir l’image en espace HSV (Hue, Saturation, Value)

Quantification :

H → 8 niveaux

S → 2 niveaux

V → 2 niveaux

L’histogramme combiné (H×S×V) donne 32 bins (8×2×2)

Normaliser l’histogramme pour obtenir un vecteur comparable entre images.

C. Texture : Matrice de co-occurrence de niveau de gris (GLCM)

Convertir l’image en niveaux de gris

Calculer la GLCM (Grey Level Co-occurrence Matrix)

Extraire 4 caractéristiques :

Contrast

Correlation

Energy

Homogeneity

Permet de capturer la structure et motifs de texture.

D. Forme : Moments invariants de Hu

Convertir l’image en niveaux de gris

Calculer les 7 moments de Hu : invariants aux transformations géométriques (translation, rotation, échelle)

Capturent l’information de forme globale de l’objet.

Vecteur final

Pour chaque image, on concatène toutes les caractéristiques :

vecteur final=[Couleurs 1 (6),Couleurs 2 (32),Texture (4),Forme (7)]

Taille totale = 6 + 32 + 4 + 7 = 49 caractéristiques

Ce vecteur sera utilisé pour calculer la distance Euclidienne entre la requête et les images de la base.

## Question 3
Test de robustesse face aux transformations géométriques

Transformations considérées : rotation, translation, mise à l’échelle, flip horizontal/vertical.

Méthode : appliquer une transformation à la requête, extraire les mêmes caractéristiques que pour la base, puis comparer.

