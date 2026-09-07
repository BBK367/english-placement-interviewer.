# Historique et reprise — AI English Placement Interviewer

Document établi le 7 septembre 2026. Dépôt privé : https://github.com/BBK367/english-placement-interviewer.

## Comment lire ce document
Ce document retrace chronologiquement les étapes importantes des échanges disponibles. C'est une synthèse de reprise, pas une transcription mot pour mot. Certaines réponses anciennes ne sont plus disponibles intégralement : une demande de l'utilisateur n'est donc pas présentée comme une réalisation sans preuve. L'état technique ci-dessous s'appuie aussi sur les fichiers locaux relus au moment de la rédaction. Les tests historiques ne sont pas des tests exécutés de nouveau aujourd'hui.

## 1. Objectif initial et installation locale
Le projet a été fourni dans AI_English_Placement_Interviewer_Code_and_Docker_TO_SHARE.zip. L'objectif était de démarrer l'application, installer ses dépendances et vérifier son fonctionnement avec AWS Bedrock et MultiPA.
L'utilisateur a rencontré plusieurs difficultés de connexion AWS : fenêtre invisible, authentification navigateur/terminal, saisie de clés et dépendances manquantes. Des scripts Windows de démarrage et de diagnostic ont été préparés. Aucun identifiant ni secret n'est reproduit ici.
La première application était consultée sur localhost:5001.

## 2. Entretien adaptatif et première évaluation
L'orientation demandée était un entretien où la question suivante dépend de la réponse précédente, plutôt qu'une liste fixe.
La conception séparait deux sources : Claude pour la transcription (grammaire, vocabulaire, compréhension, cohérence, etc.) et MultiPA préentraîné pour l'audio (prononciation, fluidité acoustique et prosodie).
Un résultat communiqué par l'utilisateur indiquait A2, confiance faible, six réponses et une moyenne MultiPA de 53,8/100. Certaines portions enregistrées n'étaient pas entièrement analysées. Ce résultat historique ne prouve pas le niveau réel du candidat.
L'utilisateur a demandé une analyse du fonctionnement et des problèmes, puis des améliorations incrémentales.

## 3. Barème, calibration et confiance
L'utilisateur a demandé pourquoi A2 revenait souvent, comment les deux sources étaient fusionnées, puis un barème théorique avec davantage de poids pour la prononciation.
Il a aussi demandé si l'IA pouvait jouer le rôle d'un professeur pour définir le barème sans tests préalables. La limite à conserver est essentielle : un barème raisonné et des exemples synthétiques peuvent aider à vérifier la logique, mais ne constituent pas une calibration sur des candidats évalués par des enseignants.
Plus tard, une augmentation artificielle de confiance a été demandée. Un paramètre demoConfidence apparaît dans l'ancienne interface. Il doit rester une simulation d'affichage, jamais une preuve de fiabilité. Le parcours vocal actuel calcule sa propre confiance et ne doit pas reprendre cette simulation.

## 4. Recherche de fluidité
L'utilisateur a signalé à plusieurs reprises une latence trop élevée et une transcription moins bonne qu'au début. Le besoin est devenu une conversation vocale presque en temps réel, avec interruptions naturelles.
La solution a évolué vers un parcours vocal distinct, consulté sur localhost:5006/voice.html. L'utilisateur a apprécié cette version, puis demandé d'y ajouter le niveau CEFR et une durée courte pour essayer.
Le transport actuel utilise l'audio continu avec AWS Bedrock / Nova 2 Sonic, plutôt qu'une simple succession de reconnaissance vocale puis synthèse dans le navigateur. Cela ne garantit pas une latence fixe : réseau, AWS, matériel, écho et traitement final restent déterminants.

## 5. Entretien minuté, conclusion et résultats
Les demandes suivantes ont précisé le produit :
- garder un comportement d'intervieweur anglais, pas un assistant généraliste ;
- proposer un entretien court au début (choix de 1 à 3 minutes dans la version préparée) ;
- poser une dernière question commune ;
- ouvrir une nouvelle page de résultats plutôt que renvoyer vers l'ancienne application.
La question de conclusion préparée est :
“To conclude, what is one goal you would like to achieve, and what steps will you take to reach it?”
Le parcours dispose maintenant de voice-results.html et voice-results.js. Le rapport est transmis par sessionStorage dans le même onglet. Ouvrir directement la page dans un autre onglet ne recrée pas un résultat.
La remise exacte de la question finale et le parcours complet au microphone après les derniers changements restent à revalider.

## 6. MultiPA après la conversation
L'utilisateur a proposé d'enregistrer l'audio pendant l'entretien, de le découper ensuite et d'utiliser les notes acoustiques avec la transcription.
Sa dernière préférence remplace la demande antérieure d'un score acoustique absolument obligatoire : conserver toute analyse MultiPA exploitable, même partielle ; si aucune analyse n'est utilisable, calculer sur la langue seulement et le signaler.
Le service actuel conserve les blocs PCM du microphone, les enveloppe en WAV et les envoie au worker. Les blocs principaux font au maximum 60 secondes. Un bloc qui échoue pour une raison non identifiée comme infrastructure peut être redécoupé en deux une fois, s'il dépasse 15 secondes. Un bloc déjà réussi n'est pas recalculé.
Les résultats valides doivent provenir de MultiPA, avoir le statut assessed et une note finie entre 0 et 100. La moyenne est pondérée par la durée analysée, avec repli sur la durée enregistrée. Les échecs sont exclus, pas remplacés par des notes inventées.
Le worker peut lui-même segmenter l'audio. Il faut distinguer son découpage interne des blocs envoyés par le backend.
Limite : une petite portion réussie peut encore peser 35 % dans la fusion. La couverture audio réduit la confiance mais ne réduit pas actuellement ce poids de façon proportionnelle.

## 7. Partage avec le tuteur et Mac mini M1
Le tuteur a demandé le code et les instructions d'installation pour Mac mini M1. Plusieurs ZIP et guides ont été préparés au fil des changements. Le souhait d'une démonstration le lendemain a aussi été exprimé ; ce document ne confirme pas qu'elle a eu lieu.
Le paquet de référence est English_Placement_Live_Professor_2026-09-07.zip. Il contient le code et les instructions, mais pas les dépendances installées, modèles téléchargés, enregistrements ou secrets.
START_HERE_Mac_M1.md décrit Docker Desktop Apple Silicon et un override linux/amd64 pour le worker aux dépendances anciennes. Cette installation n'a pas été vérifiée sur un Mac M1 physique et peut être lente.
Il faut fournir ses propres accès AWS autorisés. Le ZIP ne rend pas l'application immédiatement accessible en ligne.

## 8. GitHub et continuité sur un autre ordinateur
Après plusieurs difficultés de connexion GitHub et de comptes, un dépôt privé a été créé : BBK367/english-placement-interviewer. (le point final appartient au nom).
Le 7 septembre 2026, la présence du ZIP a été vérifiée dans le commit 7e8ba1de6e4113d50781e380cd82a3223f35ce0d, “Add files via upload”. Le dépôt contenait aussi README.md.
Il s'agit d'une sauvegarde instantanée. Les modifications locales futures ne sont pas synchronisées automatiquement. Le code est dans un ZIP, pas encore organisé comme fichiers source directement versionnés dans le dépôt.
L'utilisateur a ensuite demandé de préserver l'évolution de la conversation pour reprendre sur un autre ordinateur : c'est le rôle de ce document. Il ne transfère pas une session locale Codex ni ses connexions AWS.

## Architecture actuelle à reprendre
| Élément | Rôle |
| --- | --- |
| frontend/public/voice.html et voice.js | Consentements, microphone, durée, conversation, fin |
| frontend/public/voice-capture.js | Capture audio pour le flux vocal |
| frontend/public/voice-results.html et voice-results.js | Rapport dédié |
| backend/routes/voice.js | Sessions vocales et endpoints du parcours |
| backend/services/voice-report.js | Évaluation finale de la transcription et de l'audio |
| backend/services/partial-pronunciation.js | Découpage, récupération partielle et agrégation |
| backend/services/cefr-scoring.js | Fusion et conversion en niveau |
| backend/services/assessor.js et backend/prompts/ | Évaluation Claude |
| shared/assessment-schemas et frontend/src/assessment/schemas.ts | Contrats des résultats |
| pronunciation-worker/ | Service Python MultiPA et dépendances acoustiques |
| data/cefr-calibration-rubric.json | Ressource de barème à vérifier contre le code réellement exécuté |
| docker-compose.local.yml | Lancement Docker |
| docker-compose.mac-demo.yml | Override Mac du paquet partagé |

Node.js/Express sert le backend. AWS est appelé côté serveur via les SDK Bedrock. Le flux microphone est PCM 16 kHz ; la sortie vocale est diffusée à 24 kHz. Le navigateur utilise un flux d'événements pour recevoir les réponses et des requêtes audio pour envoyer le microphone. Claude et le modèle vocal peuvent utiliser des régions différentes ; le guide vocal prévoit us-east-1.
MultiPA est préentraîné : aucun nouvel entraînement n'est établi dans ce travail.

## Barème réellement lu dans le code
Langue : compréhension 22 %, grammaire 22 %, vocabulaire 20 %, cohérence 16 %, interaction 12 %, fluidité textuelle 8 %. Les scores sur 5 sont multipliés par 20.
Oral : précision de prononciation 55 %, fluidité acoustique 25 %, prosodie 20 %. Les dimensions disponibles sont renormalisées si certaines manquent.
Fusion avec audio utilisable : 0,65 × langue + 0,35 × oral. Sans audio utilisable : langue seule.
Le niveau utilise floor(score / 16.67), borné entre les six niveaux A1 et C2. Les frontières sont donc 16,67 ; 33,34 ; 50,01 ; 66,68 ; 83,35. Ces intervalles égaux sont une heuristique, pas des seuils CEFR validés.
La confiance du rapport vocal est medium seulement avec au moins 100 mots candidats, au moins trois tours reconnus et tous les blocs audio évalués ; sinon low. En dessous de 15 mots, le service renvoie insufficient_evidence.
Le champ pronunciationRequired reste true dans le service de fusion alors qu'un repli langue seule existe : incohérence de nommage à revoir, sans empêcher le repli actuellement voulu.

## Vérifications et limites
Les notes de travail antérieures rapportent un test réel utilisant un extrait audio public, une réponse vocale AWS, un rapport CEFR et un résultat MultiPA. Elles rapportent aussi 19 tests backend réussis au moment du packaging. Ces vérifications ne prouvent ni la précision CEFR ni le bon fonctionnement sur Mac.
La rédaction de ce document a vérifié le code de fusion, de rapport et de récupération partielle ; elle n'a pas relancé un entretien ni la suite complète.
Points prioritaires :
1. Refaire un entretien au microphone de bout en bout : interruption, minuterie, conclusion, navigation et rapport.
2. Mesurer la latence réelle et distinguer réponse conversationnelle et attente d'évaluation finale.
3. Tester les cas MultiPA complet, partiel et indisponible ; afficher clairement la couverture audio.
4. Calibrer les seuils sur des échantillons notés indépendamment par des enseignants avant usage décisionnel.
5. Vérifier écho, silence et attribution de la voix au candidat.
6. Tester le guide Docker sur un vrai Mac M1.
7. Réconcilier les anciennes documentations : docs/live-voice-preview.md affirme encore que la version ne produit pas de CEFR, ce qui ne correspond plus au code actuel. Lire START_HERE_Mac_M1.md en priorité.
8. Vérifier sécurité, conservation des données et contrôle d'accès avant exposition publique. Ne pas confondre dépôt GitHub privé et hébergement de l'application.

## Reprendre sur un autre ordinateur
1. Se connecter à GitHub avec le compte autorisé et ouvrir le dépôt exact.
2. Télécharger le ZIP de référence puis l'extraire.
3. Ouvrir le dossier ai-interviewer dans Codex.
4. Lire AGENTS.md et START_HERE_Mac_M1.md avant toute modification.
5. Installer selon le guide et configurer localement ses propres secrets ; ne pas les copier dans une conversation ou un commit.
6. Pour Docker, le guide utilise le port 5001 ; le serveur de développement historique utilisait 5006.
7. Donner ce document à la nouvelle conversation et demander une vérification de l'état réel avant de continuer.
8. Reporter les prochaines modifications sur GitHub explicitement.

## Message à donner au prochain assistant
Je reprends AI English Placement Interviewer. Lis HISTORIQUE_ET_REPRISE.md, AGENTS.md et START_HERE_Mac_M1.md, puis inspecte le code réel. Je souhaite conserver l'entretien vocal fluide, les interruptions, une durée courte configurable, une question de conclusion commune et la page voice-results dédiée. Utilise MultiPA dès qu'une analyse réelle, même partielle, est disponible ; sinon indique son absence et utilise la langue seule. Ne gonfle pas artificiellement la confiance et ne présente pas le CEFR comme validé. Ne réintroduis pas l'ancienne interface dans le parcours final. Vérifie le fonctionnement avant d'affirmer une réussite et améliore le projet progressivement.

## Consignes du dépôt à respecter
AGENTS.md identifie des routes historiques de recrutement à ne pas modifier : backend/routes/cv.js, interview.js, reports.js et database/schema.sql.
Réutiliser les contrats de schéma existants, contraindre la sortie Claude avec un appel d'outil Bedrock, ne pas exposer les secrets ni les prompts au navigateur, et conserver not_assessed tant que l'audio n'a pas été réellement analysé.
Ce document n'ajoute aucune clé, aucun enregistrement ni donnée de candidat au dépôt.

