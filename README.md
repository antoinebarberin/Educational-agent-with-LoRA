**IMPORTANT : pour des raisons de confidentialité il faut ajouter le cours au format pdf sur le projet en local sur sa machine pour pouvoir utiliser ce notebook**

🎓 Maths-LoRA-Tutor : Agent Éducatif Mathématique sur Hardware Grand Public

Ce projet explore la création d'un agent conversationnel spécialisé dans l'enseignement des mathématiques de niveau ingénieur (Mathématiques Déterministes).L'objectif principal est de démontrer l'efficacité de la méthode LoRA (Low-Rank Adaptation) pour spécialiser un "petit" modèle de langage (< 1 milliard de paramètres) sur un domaine complexe, en utilisant uniquement des ressources de calcul grand public.

📄 Contexte & Référence Théorique

Ce travail s'appuie sur le papier de recherche :LoRA: Low-Rank Adaptation of Large Language Models > Edward J. Hu et al. (Microsoft), 2021 - arXiv:2106.09685
Plutôt que de réentraîner tous les paramètres du modèle (Full Fine-Tuning), LoRA gèle les poids pré-entraînés et injecte des matrices de rang faible entraînables dans les couches d'attention. Cela permet de réduire drastiquement le coût mémoire et computationnel.

🛠️ Méthodologie

Le projet compare plusieurs approches implémentées dans le notebook Agent éducatif avec LoRA.ipynb :
Préparation des Données :
Extraction et nettoyage d'un polycopié de cours (PDF → Txt).
Génération synthétique de paires Questions/Réponses (Instruction Tuning) via un LLM tiers.
Fine-Tuning LoRA (Deux stratégies) :
Causal LM : Entraînement sur le texte brut avec fenêtre glissante pour apprendre le "style" et le LaTeX.
Instruct Tuning : Entraînement sur le dataset de questions/réponses pour apprendre le format "Flashcard".Config : Rang $r=8$, Alpha=16/32, Cible q_proj, v_proj.

Comparaison avec RAG (Retrieval Augmented Generation) :
Implémentation d'un pipeline RAG avec FAISS et Cross-Encoder pour fournir le contexte au modèle de base.

📊 Résultats Clés

L'évaluation a été réalisée sur un jeu de test indépendant en utilisant la similarité sémantique (Cosine Similarity) et le score ROUGE.

ApprochePerplexité (Test)
Score Sémantique Moyen
ObservationModèle de Base
27.11-Hallucinations fréquentes, réponses vagues.RAG-0.21Pertinent sur la récupération, mais échec de la synthèse par le modèle 0.5B.LoRA (Instruct )5.440.50Meilleure performance. Style concis, vocabulaire mathématique précis ($L^p$, Sobolev).

Constat : Sur un très petit modèle (0.5B), le Fine-Tuning LoRA s'avère plus efficace que le RAG pour capturer le jargon et la syntaxe spécifique, le modèle manquant de capacités de raisonnement pour exploiter efficacement le contexte RAG.

🚀 Installation & Usage

Cloner le repo :Bashgit clone https://github.com/VOTRE_USERNAME/Maths-LoRA-Tutor.git
cd Maths-LoRA-Tutor

Installer les dépendances :Bashpip install torch transformers peft datasets bitsandbytes accelerate
pip install langchain-community faiss-cpu sentence-transformers rouge-score

Lancer le Notebook :
Ouvrez Agent éducatif avec LoRA.ipynb dans Jupyter ou VS Code. Le code détecte automatiquement l'accélération matérielle (CUDA pour Nvidia, MPS pour Apple Silicon).📂 Structure du Répertoire

Agent éducatif avec LoRA.ipynb : Le cœur du projet (Nettoyage, Entraînement LoRA, RAG, Évaluation).
**.pdf : Le document source que vous devrez ajouter pour l'entraînement (Cours de mathématiques).**
maths_flashcards.json : Le dataset d'instruction généré.
qwen_instruct_math et model_qwen_trained_with_sw_r=8 : peuvent être utilisés directement pour éviter de refaire l'entraînement avec la méthode LoRA

⚠️ Limitations

Taille du modèle : Qwen2.5-0.5B est très léger. Bien qu'il apprenne parfaitement la forme (LaTeX, définitions), il peut manquer de fond sur des raisonnements logiques complexes comparé à des modèles 7B+.
Hallucination : Comme tout LLM fine-tuné sans RAG externe, le modèle peut halluciner des formules plausibles mais fausses.
