# Learner_BERT_Reproduction_Doc_Struct

**Ce projet consiste à reproduire une expérience déjà réalisée auparavant dans l'article « Evaluating the Generalisation of an Artificial Learner » de Bernardo Stearns et ses collègues. Vous pouvez retrouver l'article et leur expérience dans le lien suivant : [Evaluating the Generalisation of an Artificial Learner](https://aclanthology.org/2024.nlp4call-1.15.pdf)**

#### **Objectif du projet**  
Explorer la possibilité de développer un modèle unique capable de simuler le comportement d’apprenants dans divers contextes. En entraînant un modèle de langage sur des données issues d’apprenants, il serait envisageable de créer un "apprenant artificiel" d’anglais reproduisant les particularités des apprenants réels.  

#### **Problématiques de l'article d'origine**  
1. Dans quelle mesure l’apprenant artificiel peut-il prédire avec précision ce qu’un apprenant réel dirait ?  
2. Quelle est la confiance du modèle dans ses prédictions ?  
3. En quoi le comportement de l’apprenant artificiel diffère-t-il d’un modèle représentant un locuteur natif ?  

#### **Corpus utilisé**  
##### **Corpus d’entraînement**  
1. **Corpus EFCAMDAT** :  
   - Ce corpus contient 723,282 textes produits dans des écoles de langue Englishtown.  
   - Les apprenants ont écrit des textes en réponse à des consignes, comme "se présenter par e-mail".  
   - Les productions couvrent 16 niveaux de compétence linguistique.  
2. **Corpus C4200M** :  
   - Il s’agit d’un ensemble de phrases incorrectes générées synthétiquement pour la correction automatique d’erreurs grammaticales.  
   - À partir de phrases correctes et d’un type d’erreur défini selon l’outil d’annotation ERRANT, ce corpus génère des phrases contenant des erreurs spécifiques : https://github.com/chrisjbryant/errant.  
   - L’objectif est de trouver un équilibre entre la qualité des textes authentiques et la quantité des textes générés.  
##### **Corpus de test**  
- Composé de textes rédigés par des étudiants français (niveau universitaire) utilisant l’anglais à des fins spécifiques : https://nakala.fr/10.34847/nkl.41d57kb0.  
- Les apprenants ont répondu à l’une des trois consignes proposées dans le cadre d’une tâche écrite en classe de 45 minutes.  
- Tous les textes ont été annotés en fonction des niveaux de compétence CECRL.  
#### **Traitement des données**  
1. **Création du modèle d’apprenant artificiel (ALL)** :  
   - Entraînement préliminaire basé sur des corpus de productions d’apprenants.  
   - Les textes bruts ont été fournis à un collateur de masquage linguistique, suivant la stratégie standard utilisée dans l’entraînement de BERT (https://github.com/google-research/bert).  
2. **Analyse des prédictions** :  
   - Masquage d’un token à la fois dans une phrase pour créer des phrases uniques masquées.  
   - Ces phrases ont été analysées par les modèles d’apprenant et de locuteur natif pour comparer leurs prédictions.  
3. **Annotation POS** :  
   - Chaque token des textes a été annoté avec sa catégorie grammaticale à l’aide de l’outil UDPipe, ce qui enrichit les données pour des analyses plus fines.  
4. **Filtrage des textes longs** :  
   - Les textes dépassant 512 tokens WordPiece ont été exclus afin de respecter les limites du modèle BERT de base.  
#### **Pré-entraînement adaptatif au domaine**  
- Affinage d’un modèle BERT déjà pré-entraîné sur des données générales, en l’adaptant spécifiquement aux textes produits par des apprenants.  
- Données utilisées : corpus EFCAMDAT et C4200M.  
- Méthode : masquage aléatoire de 15 % des tokens WordPiece par phrase pour que le modèle apprenne à prédire les mots manquants à partir du contexte.  
#### **Évaluation des modèles**  
Les performances des modèles (apprenant artificiel et locuteur natif) ont été comparées sur des textes d’apprenants externes.  
***Remarque : Étant donné que l'évaluation du modèle EFCAMDAT sur le corpus complet entraîne toujours la déconnection de l'environnement, ici on prend seulement les 200 premiers textes du corpus.***
##### **Métriques utilisées**  
1. **Recall-at-k (R@k)** :  
   - Mesure la précision des modèles : combien de fois le mot correct figure parmi les k prédictions les plus probables.  
   - Calculé pour `k = 1, 5, 10`.  
2. **Divergence KL (Kullback-Leibler)** :  
   - Quantifie la différence entre les distributions de probabilité prédites par les modèles.  
   - Utilisée pour comparer les choix de tokens entre le modèle natif et les modèles d’apprenants.  
3. **Calibration** :  
   - Évalue la confiance du modèle dans ses prédictions. Une bonne calibration signifie que la probabilité attribuée à une prédiction reflète correctement son exactitude.

### Les résultats des évaluations sont sauvegardés dans le dossiers "Resultats".