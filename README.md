# image-mining

## Atelier 1 : Système CBIR

### A. Analyse scientifique détaillée (par image et transformation)

Rappel des blocs de features :

A — Couleurs1 : moments (Mean, Std) sur R,G,B (6 dims)

B — Couleurs2 : histogramme HSV quantifié (H=8, S=2, V=2 → 32 dims)

C — Texture : GLCM → Contrast, Correlation, Energy, Homogeneity (4 dims)

D — Forme : Hu-moments (7 dims)
Les vecteurs sont concaténés (49 dims) et comparés par distance Euclidienne.
