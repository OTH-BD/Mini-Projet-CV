# 🔬 Projet de Recherche : Segmentation d'Images en Vision par Ordinateur

**Réalisé par :** Othmane Boudali  🔬 Projet de Recherche : Segmentation d'Images en Vision par Ordinateur

**Réalisé par :** Othmane Boudali 
**Formation :** Master Ingénierie Informatique, Big Data & Cloud Computing

---

## 📖 Sommaire
1. [Contexte et Objectif](#1-contexte-et-objectif)
2. [Fonction Attendue](#2-fonction-attendue)
3. [Présentation de l'Architecture (U-Net)](#3-présentation-de-larchitecture-u-net)
4. [Principe de Fonctionnement](#4-principe-de-fonctionnement)
5. [Exemple d'Application : Plateforme E-Santé](#5-exemple-dapplication--plateforme-e-santé)
6. [Évaluation et Métriques](#6-évaluation-et-métriques)
7. [Limites et Perspectives](#7-limites-et-perspectives)
8. [Conclusion](#8-conclusion)

---

## 1. Contexte et Objectif

Contrairement à la classification (qui identifie le contenu global d'une image) ou à la détection d'objets (qui dessine des boîtes englobantes), la **Segmentation d'images** va beaucoup plus loin : elle classe **chaque pixel** de l'image dans une catégorie spécifique. 

L'objectif de ce travail de recherche est d'explorer cette approche ultra-précise, essentielle dans des domaines où la délimitation exacte est critique. Nous nous concentrerons sur l'architecture **U-Net**, devenue le standard absolu pour la segmentation sémantique, particulièrement dans le domaine biomédical.

---

## 2. Fonction Attendue

Le système de segmentation agit comme un filtre d'analyse pixel par pixel.

### 📥 Entrées (Inputs)
* **Image source :** Scan médical (IRM, radiographie, scanner CT) au format JPG, PNG ou DICOM.
* **Canaux :** Image en niveaux de gris (1 canal) ou RGB (3 canaux).

### 📤 Sorties (Outputs)
* **Masque de segmentation :** Une image binaire (noir et blanc) ou multiclasse ayant exactement les mêmes dimensions que l'image d'entrée.
* **Format des données :** Une matrice où chaque pixel contient l'identifiant de la classe prédite (ex: 0 pour le fond, 1 pour une tumeur).

---

## 3. Présentation de l'Architecture (U-Net)

Créé initialement en 2015 pour l'imagerie médicale, l'architecture U-Net a révolutionné le domaine de la vision par ordinateur. Son nom vient de sa forme symétrique en "U". Elle se divise en trois concepts clés :

1. **L'encodeur (Voie de contraction) :** Agit comme un Réseau de Neurones Convolutifs (CNN) classique. Il capture le contexte global et extrait les caractéristiques en réduisant progressivement la résolution spatiale de l'image via des opérations de MaxPooling.
2. **Le décodeur (Voie d'expansion) :** Reconstruit l'image pixel par pixel à partir des caractéristiques extraites via des convolutions transposées (Up-sampling).
3. **Les "Skip Connections" (Le secret de U-Net) :** L'architecture relie directement les couches de l'encodeur à celles du décodeur. Cela permet au modèle de récupérer les détails spatiaux haute résolution perdus lors de la contraction, garantissant des contours extrêmement nets et précis.

---

## 4. Principe de Fonctionnement

Le flux de traitement (pipeline) se déroule en plusieurs étapes :

1. **Prétraitement :** Normalisation des pixels (valeurs entre 0 et 1), redimensionnement standard (ex: $256 \times 256$), et augmentation de données (rotations, symétries) pour robustifier le modèle.
2. **Inférence (Forward Pass) :** L'image traverse l'encodeur pour en extraire la sémantique profonde, puis remonte par le décodeur qui reconstruit la carte spatiale en s'aidant des *Skip Connections*.
3. **Couche de sortie :** Une fonction d'activation (Sigmoid pour une segmentation binaire, Softmax pour du multiclasse) est appliquée à la dernière couche pour attribuer une probabilité d'appartenance à chaque pixel.
4. **Apprentissage (Loss Function) :** Face au déséquilibre de classes (ex: une petite lésion sur un grand fond noir), on privilégie souvent la **Dice Loss** à la simple Cross-Entropy, car elle force le modèle à optimiser le chevauchement exact entre la prédiction et la vérité terrain (Ground Truth).

---

## 5. Exemple d'Application : Plateforme E-Santé

La segmentation d'images prend toute sa dimension lorsqu'elle est intégrée au sein d'un système d'information complet pour assister les professionnels métier.

**Scénario :** Intégration d'un modèle U-Net de détection de tumeurs cérébrales (entraîné sur le dataset BraTS) dans une architecture logicielle moderne.

* **Backend (Spring Boot / Java) :** Une architecture microservices reçoit l'imagerie médicale du patient via une API REST sécurisée. Le microservice de traitement délègue l'inférence au modèle U-Net, calcule le volume exact de la zone segmentée, et sauvegarde les métadonnées dans la base de données de l'hôpital.
* **Frontend (Angular) :** Une interface web ergonomique permet au médecin de visualiser instantanément la superposition du masque généré par l'IA sur l'image d'origine. Le praticien conserve le contrôle final pour valider ou ajuster le diagnostic.

Cette synergie entre le Deep Learning et le développement Full Stack permet de déployer l'IA dans des conditions réelles de production.

---

## 6. Évaluation et Métriques

L'évaluation de la segmentation nécessite des métriques de chevauchement spatial. Soit $A$ l'ensemble des pixels réels de l'objet (vérité terrain) et $B$ l'ensemble des pixels prédits par le modèle :

* **Intersection over Union (IoU) / Jaccard Index :** Mesure le ratio entre l'intersection de la zone prédite et de la réalité, divisé par leur union.
$$IoU = \frac{|A \cap B|}{|A \cup B|}$$

* **Dice Coefficient (F1-Score spatial) :**
Très utilisé en imagerie médicale, il donne un poids légèrement plus important à l'intersection pure.
$$Dice = \frac{2 \times |A \cap B|}{|A| + |B|}$$

---

## 7. Limites et Perspectives

**Limites :**
* **Coût d'annotation :** La création d'un dataset de segmentation exige qu'un expert humain détoure manuellement chaque objet, pixel par pixel, ce qui est très coûteux.
* **Gourmandise en ressources :** La conservation des cartes de caractéristiques haute résolution en mémoire (via les Skip Connections) nécessite des GPU dotés d'une grande VRAM.

**Perspectives :**
* **U-Net 3D :** Évolution de l'architecture pour traiter directement des volumes entiers (scanners 3D) sans perdre l'information contextuelle entre les différentes coupes.
* **Vision Transformers :** L'intégration de mécanismes d'attention (ex: Swin-Unet) pour mieux modéliser les dépendances globales de l'image par rapport aux convolutions classiques.

---

## 8. Conclusion

La segmentation d'images représente le niveau de précision ultime en Computer Vision. L'architecture **U-Net** s'est imposée comme une référence incontournable grâce à sa capacité à allier compréhension sémantique et précision spatiale. Au-delà de l'imagerie médicale, ces concepts sont aujourd'hui au cœur des véhicules autonomes, de l'agriculture de précision et de l'analyse satellitaire. Couplée à des architectures logicielles robustes, cette technologie offre des perspectives d'automatisation et d'assistance décisionnelle sans précédent.
**Formation :** Master Ingénierie Informatique, Big Data & Cloud Computing

---

## 📖 Sommaire
1. [Contexte et Objectif](#1-contexte-et-objectif)
2. [Fonction Attendue](#2-fonction-attendue)
3. [Présentation de l'Architecture (U-Net)](#3-présentation-de-larchitecture-u-net)
4. [Principe de Fonctionnement](#4-principe-de-fonctionnement)
5. [Exemple d'Application : Plateforme E-Santé](#5-exemple-dapplication--plateforme-e-santé)
6. [Évaluation et Métriques](#6-évaluation-et-métriques)
7. [Limites et Perspectives](#7-limites-et-perspectives)
8. [Conclusion](#8-conclusion)

---

## 1. Contexte et Objectif

Contrairement à la classification (qui identifie le contenu global d'une image) ou à la détection d'objets (qui dessine des boîtes englobantes), la **Segmentation d'images** va beaucoup plus loin : elle classe **chaque pixel** de l'image dans une catégorie spécifique. 

L'objectif de ce travail de recherche est d'explorer cette approche ultra-précise, essentielle dans des domaines où la délimitation exacte est critique. Nous nous concentrerons sur l'architecture **U-Net**, devenue le standard absolu pour la segmentation sémantique, particulièrement dans le domaine biomédical.

---

## 2. Fonction Attendue

Le système de segmentation agit comme un filtre d'analyse pixel par pixel.

### 📥 Entrées (Inputs)
* **Image source :** Scan médical (IRM, radiographie, scanner CT) au format JPG, PNG ou DICOM.
* **Canaux :** Image en niveaux de gris (1 canal) ou RGB (3 canaux).

### 📤 Sorties (Outputs)
* **Masque de segmentation :** Une image binaire (noir et blanc) ou multiclasse ayant exactement les mêmes dimensions que l'image d'entrée.
* **Format des données :** Une matrice où chaque pixel contient l'identifiant de la classe prédite (ex: 0 pour le fond, 1 pour une tumeur).

---

## 3. Présentation de l'Architecture (U-Net)

Créé initialement en 2015 pour l'imagerie médicale, l'architecture U-Net a révolutionné le domaine de la vision par ordinateur. Son nom vient de sa forme symétrique en "U". Elle se divise en trois concepts clés :

1. **L'encodeur (Voie de contraction) :** Agit comme un Réseau de Neurones Convolutifs (CNN) classique. Il capture le contexte global et extrait les caractéristiques en réduisant progressivement la résolution spatiale de l'image via des opérations de MaxPooling.
2. **Le décodeur (Voie d'expansion) :** Reconstruit l'image pixel par pixel à partir des caractéristiques extraites via des convolutions transposées (Up-sampling).
3. **Les "Skip Connections" (Le secret de U-Net) :** L'architecture relie directement les couches de l'encodeur à celles du décodeur. Cela permet au modèle de récupérer les détails spatiaux haute résolution perdus lors de la contraction, garantissant des contours extrêmement nets et précis.

---

## 4. Principe de Fonctionnement

Le flux de traitement (pipeline) se déroule en plusieurs étapes :

1. **Prétraitement :** Normalisation des pixels (valeurs entre 0 et 1), redimensionnement standard (ex: $256 \times 256$), et augmentation de données (rotations, symétries) pour robustifier le modèle.
2. **Inférence (Forward Pass) :** L'image traverse l'encodeur pour en extraire la sémantique profonde, puis remonte par le décodeur qui reconstruit la carte spatiale en s'aidant des *Skip Connections*.
3. **Couche de sortie :** Une fonction d'activation (Sigmoid pour une segmentation binaire, Softmax pour du multiclasse) est appliquée à la dernière couche pour attribuer une probabilité d'appartenance à chaque pixel.
4. **Apprentissage (Loss Function) :** Face au déséquilibre de classes (ex: une petite lésion sur un grand fond noir), on privilégie souvent la **Dice Loss** à la simple Cross-Entropy, car elle force le modèle à optimiser le chevauchement exact entre la prédiction et la vérité terrain (Ground Truth).

---

## 5. Exemple d'Application : Plateforme E-Santé

La segmentation d'images prend toute sa dimension lorsqu'elle est intégrée au sein d'un système d'information complet pour assister les professionnels métier.

**Scénario :** Intégration d'un modèle U-Net de détection de tumeurs cérébrales (entraîné sur le dataset BraTS) dans une architecture logicielle moderne.

* **Backend (Spring Boot / Java) :** Une architecture microservices reçoit l'imagerie médicale du patient via une API REST sécurisée. Le microservice de traitement délègue l'inférence au modèle U-Net, calcule le volume exact de la zone segmentée, et sauvegarde les métadonnées dans la base de données de l'hôpital.
* **Frontend (Angular) :** Une interface web ergonomique permet au médecin de visualiser instantanément la superposition du masque généré par l'IA sur l'image d'origine. Le praticien conserve le contrôle final pour valider ou ajuster le diagnostic.

Cette synergie entre le Deep Learning et le développement Full Stack permet de déployer l'IA dans des conditions réelles de production.

---

## 6. Évaluation et Métriques

L'évaluation de la segmentation nécessite des métriques de chevauchement spatial. Soit $A$ l'ensemble des pixels réels de l'objet (vérité terrain) et $B$ l'ensemble des pixels prédits par le modèle :

* **Intersection over Union (IoU) / Jaccard Index :** Mesure le ratio entre l'intersection de la zone prédite et de la réalité, divisé par leur union.
$$IoU = \frac{|A \cap B|}{|A \cup B|}$$

* **Dice Coefficient (F1-Score spatial) :**
Très utilisé en imagerie médicale, il donne un poids légèrement plus important à l'intersection pure.
$$Dice = \frac{2 \times |A \cap B|}{|A| + |B|}$$

---

## 7. Limites et Perspectives

**Limites :**
* **Coût d'annotation :** La création d'un dataset de segmentation exige qu'un expert humain détoure manuellement chaque objet, pixel par pixel, ce qui est très coûteux.
* **Gourmandise en ressources :** La conservation des cartes de caractéristiques haute résolution en mémoire (via les Skip Connections) nécessite des GPU dotés d'une grande VRAM.

**Perspectives :**
* **U-Net 3D :** Évolution de l'architecture pour traiter directement des volumes entiers (scanners 3D) sans perdre l'information contextuelle entre les différentes coupes.
* **Vision Transformers :** L'intégration de mécanismes d'attention (ex: Swin-Unet) pour mieux modéliser les dépendances globales de l'image par rapport aux convolutions classiques.

---

## 8. Conclusion

La segmentation d'images représente le niveau de précision ultime en Computer Vision. L'architecture **U-Net** s'est imposée comme une référence incontournable grâce à sa capacité à allier compréhension sémantique et précision spatiale. Au-delà de l'imagerie médicale, ces concepts sont aujourd'hui au cœur des véhicules autonomes, de l'agriculture de précision et de l'analyse satellitaire. Couplée à des architectures logicielles robustes, cette technologie offre des perspectives d'automatisation et d'assistance décisionnelle sans précédent.
