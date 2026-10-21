<div align="center">

# 🎬 Tiktoker

**Une fabrique locale de vidéos courtes verticales générées par IA : du sujet au MP4 monté, sous-titré et vérifié, sur une seule carte graphique grand public. En production : ses vidéos sont publiées sur TikTok.**

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-async-009688?logo=fastapi&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=nextdotjs&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-2.8%20cu128-EE4C2C?logo=pytorch&logoColor=white)
![Claude](https://img.shields.io/badge/LLM-Claude%20CLI-D97757)
![GPU](https://img.shields.io/badge/GPU-RTX%205070%20Ti%2012%20Go-76b900?logo=nvidia&logoColor=white)
![Code](https://img.shields.io/badge/code-priv%C3%A9-lightgrey)
![Statut](https://img.shields.io/badge/statut-en%20production-brightgreen)

<a href="assets/demo.mp4"><img src="assets/demo.gif" alt="Démo de Tiktoker : le mode Cours IA Flash, le studio, la chaîne de production, la publication et une vraie vidéo produite" width="760"></a>

*Aperçu animé du film (39,6 s). **[Vidéo en qualité HD](assets/demo.mp4)**, effets sonores sans musique, avec la voix de la vidéo montrée. Film black-motion du 21 octobre 2026.*

</div>

> **Ce dépôt est une vitrine.** Il présente le produit, son architecture et ses choix techniques, avec des
> captures et une vidéo de démonstration. Le code source est privé et n'y figure pas.

---

## ✨ En bref

| | |
|---|---|
| ✍️ **Le texte** | Claude écrit le script et son storyboard, puis un relecteur le note sur 5 et réécrit chaque partie faible jusqu'à ce que l'accroche, l'histoire et la chute tiennent toutes à 4/5. |
| 🎙️ **La voix** | Clonage ou création de voix par description (Qwen3-TTS, serveur voix dédié), texte préparé pour l'oral, retouche ligne par ligne sans tout resynthétiser. |
| 🖼️ **Les images** | SDXL ou FLUX avec une direction artistique par mode, de vraies photos d'archive restaurées, ou des **schémas animés** dessinés en SVG. |
| 🎞️ **Le montage** | Motion design (Hyperframes, HTML + GSAP) : accroche animée, sous-titres calés au mot, encarts, schémas étape par étape, mèmes de réaction, bruitages. |
| 🔍 **La vérification** | Avant le rendu, Claude regarde chaque plan et le compare à ce que dit la voix ; les images incohérentes sont régénérées. |
| 🏭 **La production** | Des dizaines de vidéos en file, réparties sur trois files (GPU, LLM, rendu), avec arrêt et reprise sans perte. |
| 📲 **La publication** | Les vidéos sont **publiées sur TikTok** : légende et hashtags écrits par Claude, mise en ligne dans TikTok, projet marqué « publié » dans Tiktoker. |

Tout le calcul lourd (images, voix, transcription, rendu) tourne **sur la machine**, sur une RTX 5070 Ti de 12 Go.
Seule l'écriture passe par Claude.

---

## 🎭 Les modes

Chaque type de vidéo est un **mode**, décrit en données et entièrement configurable depuis l'interface : champs du
script, gabarits, prompts envoyés à Claude, voix, direction artistique, style de montage, grille de relecture et
garde-fous. Le pipeline est le même pour tous : **aucune branche par mode dans le code**.

Neuf modes sont livrés ; on peut les modifier, les dupliquer, les réinitialiser ou en créer de nouveaux.

| Mode | Principe | Ce qui le distingue |
|---|---|---|
| 🏛️ **Citations de philosophes** | une citation réelle, racontée | niveau de confiance de l'attribution · montage « Marbre et or » |
| 🕊️ **Récits mémoriels** | figures de la guerre d'indépendance algérienne | sources, relecture obligatoire, photos d'archive réelles restaurées, portrait jamais généré · montage « Archive » |
| 🌀 **Parodie humoristique** | les petites galères du quotidien racontées avec les codes d'un anime | voix de narrateur dédiée · montage « Énergie occulte » |
| ⚔️ **Répliques d'anime** | une réplique, son contexte, sa portée | la voix suit le personnage via un profil · montage « Encre et feu » |
| 📜 **Histoires** | récits historiques courts | sources, relecture obligatoire · montage « Parchemin et bronze » |
| 🧠 **Micro-cours IA** | une notion d'IA en 50 à 60 s, en série | schémas animés avec temps de lecture, parcours rangé du plus simple au plus complexe · montage « Labo » |
| ⚡ **Cours IA Flash** | les mêmes notions en 20 à 35 s, au format de rétention | image choc et mot-choc dès 0,0 s, ouverture animée, un seul mécanisme, relecture notée sur la rétention ; chaque cours passe d'un format à l'autre |
| 🤪 **Cours IA décalés** | les mêmes notions, ton provocateur | accroche « provocation + verdict », mèmes de réaction choisis par Claude · montage « WTF » |
| ⚽ **Analyse tactique** | football : un match, une équipe, un système | tableaux tactiques et graphiques animés, sources · montage « Tableau tactique » |

Un mode livré se met à jour quand ses gabarits évoluent, **sans jamais écraser une personnalisation**.

<p align="center">
  <img src="assets/screens/08-mode-champs.png" alt="Éditeur de mode : champs du script" width="49%">
  <img src="assets/screens/11-mode-garde-fous.png" alt="Éditeur de mode : garde-fous" width="49%">
</p>

---

## ⚡ Réglé pour la rétention : le format Cours IA Flash

Tiktoker est en production ; la phase actuelle consiste à adapter le format pour que le spectateur reste. Le mode **Cours IA
Flash** part d'un constat écrit dans sa consigne : *le spectateur décide en deux secondes s'il reste*. Tout le format
est construit autour de ces deux secondes, et la relecture le vérifie.

| Moment | Règle du format (écrite dans le mode) |
|---|---|
| 0,0 s | une **image choc** en pleine lumière et un **mot-choc** de 2 ou 3 mots ; la voix démarre aussitôt sur une accroche de 10 mots au plus, vraie et vérifiable |
| 1 à 2,5 s | une **ouverture animée** posée sur l'image choc, qui montre le mécanisme propre à la notion (le filtre qui glisse pour un CNN, les liens d'attention pour un transformer…) |
| vers 1,5 à 2 s | la **rupture** (« Mais… », « Sauf que… ») qui retourne le choc |
| avant 8 s | le **pourquoi** : un seul mécanisme, sur le même exemple, en 30 à 55 mots, sans temps mort |
| fin | la **chute** : la définition qu'on retient, qui nomme la notion |

- **Ouvertures et mécaniques animées** : 7 ouvertures et 30 mécaniques (tokens, attention, convolution, descente de
  gradient, k-means…), chacune dans son dossier « fiche + code », sur le modèle d'un skill. Claude choisit dans la
  liste en écrivant le storyboard ; une mécanique mal remplie est refusée par la validation et la scène redevient une
  image générée. Ajouter une mécanique, c'est ajouter un dossier.
- **Relecture orientée rétention** : la grille note l'accroche, l'explication et la chute avec des plafonds
  explicites (accroche en question, faux choc, ouverture d'une autre famille de notions, plus de 85 mots…). Une
  partie sous 4/5 est réécrite avec une consigne précise ; les autres restent figées.
- **Deux formats pour une notion** : un cours passe de Micro-cours IA à Cours IA Flash, et retour, sans perdre son sujet.
- **Lancement par phases** : pour une série, toutes les relectures, puis toutes les voix, puis toutes les images,
  puis tous les montages, avec reprise à une phase donnée.

<p align="center">
  <img src="assets/screens/13-mode-cours-ia-flash.png" alt="Le mode Cours IA Flash : cours, vidéos prêtes et publiées" width="49%">
  <img src="assets/screens/14-studio-format-flash.png" alt="Studio d'un cours IA Flash : accroche, mot-choc, image choc, ouverture animée, relecture notée" width="49%">
</p>

---

## 🧭 Le parcours d'une vidéo

```mermaid
flowchart LR
    R[Radar<br/>actualité IA] -.-> A
    A[Sujet] --> B[Script + storyboard<br/>Claude]
    B --> C{Relecture<br/>≥ 4/5 ?}
    C -- non --> D[Réécriture des<br/>parties faibles]
    D --> C
    C -- oui --> E[Images / schémas<br/>SDXL · FLUX · archives]
    E --> F[Voix<br/>Qwen3-TTS]
    F --> G[Alignement mot à mot<br/>faster-whisper]
    G --> H[Plan de montage<br/>Claude]
    H --> I{Vérification<br/>image par image}
    I -- incohérent --> E
    I -- cohérent --> J[Rendu Hyperframes<br/>MP4 1080×1920]
    J --> K[Légende + hashtags]
    K --> L[Publication<br/>sur TikTok]
```

0. **Trouver un sujet** *(en cours de développement)*. Les sujets d'actualité IA repérés par **Radar**, mon outil de
   veille, peuvent être importés : seuls ceux qui s'appuient sur une source primaire deviennent des scripts, toujours
   **à relire**, sans appel au LLM ni production automatique.
1. **Écrire.** Dans un mode, Claude produit un lot de scripts, chacun avec son **storyboard** : 3 à 8 scènes calées
   sur les mots du texte lu, avec le visuel, le mouvement de caméra et la transition. Au-delà de trois scripts, la
   demande est découpée en sujets écrits en parallèle.
2. **Relire.** Chaque script est noté (accroche, histoire, chute). Une partie sous 4/5 est réécrite avec les
   remarques de la relecture, **les parties validées restant figées** : d'abord avec un modèle rapide, puis avec un
   modèle plus fort si besoin. Une retouche à la main ne déclenche qu'une nouvelle notation.
3. **Produire.** Étape par étape dans le Studio, d'un clic (« Pipeline complet »), ou en lot. L'option « ne jamais
   demander » enchaîne tout sans pause.
4. **Vérifier.** Claude compare chaque plan à la voix ; un plan incohérent est régénéré automatiquement, sinon une
   fenêtre de décision s'ouvre depuis n'importe quelle page.
5. **Publier.** Légende et hashtags écrits par Claude, MP4 prêt ; la vidéo est mise en ligne dans TikTok, puis le
   projet est marqué « publié », ce qui permet de suivre ce qui reste à sortir.

<p align="center">
  <img src="assets/screens/01-studio-script.png" alt="Studio : script écrit par Claude" width="49%">
  <img src="assets/screens/02-studio-images.png" alt="Studio : images générées" width="49%">
</p>
<p align="center">
  <img src="assets/screens/03-studio-voix.png" alt="Studio : voix ligne par ligne" width="49%">
  <img src="assets/screens/06-studio-video-motion.png" alt="Studio : montage motion design" width="49%">
</p>
<p align="center">
  <img src="assets/screens/15-studio-publication-tiktok.png" alt="Studio : onglet Publication, légende TikTok et hashtags" width="49%">
  <img src="assets/screens/16-production-files.png" alt="Production : les jobs en cours, en direct" width="49%">
</p>

Chaque vidéo garde un **dossier autonome** (manifeste JSON, images avec prompts et seeds, voix ligne par ligne,
horodatage des mots, sous-titres ASS réutilisables dans un logiciel de montage, composition motion, MP4) : on peut
la remonter à la main hors de l'application.

---

## 🛠️ Ce qui est intéressant techniquement

**1. Les formats de vidéo sont des données, pas du code.**
Un mode décrit ses champs, ses gabarits `{champ}`, ses prompts et ses garde-fous ; le contrat JSON demandé à
Claude est généré à partir des champs. Les modes livrés se mettent à jour par comparaison d'empreintes, sans
écraser une colonne retouchée à la main.

**2. Une relecture qui converge.**
Le texte n'est pas accepté parce que le LLM l'a écrit : il est noté par partie, et seules les parties faibles
sont réécrites, avec escalade de modèle. Chaque usage de Claude (écrire, relire, planifier le montage, vérifier)
a son propre modèle et ses propres prompts, réglables depuis l'interface.

**3. Tenir dans 12 Go de VRAM.**
SDXL, FLUX, Whisper et la restauration ne tiennent pas ensemble (un pic SDXL a été mesuré à 10,3 Go). Un
ordonnanceur GPU sérialise le travail et décharge les modèles les moins récents en vérifiant la VRAM **et** la RAM
réellement libres, autres programmes compris ; il préfère refuser que déborder. FLUX tient grâce à la
quantification NF4.

**4. Trois files au lieu d'une.**
Les étapes GPU (une place), les appels au LLM (en parallèle) et les rendus Chrome + ffmpeg (deux places, sans GPU)
ont chacun leur file. Un pipeline cède sa place GPU pendant qu'il attend Claude ou un rendu, les attentes sont
expliquées, et chaque job s'arrête et reprend sans perdre ce qui est fait, même après un redémarrage.

**5. Vérifier que l'image correspond à ce qui est dit.**
Un plan de montage peut afficher un désert quand la voix parle d'une ville. Avant le rendu, Claude reçoit chaque
image avec le texte prononcé pendant qu'elle est à l'écran, en lecture seule ; le plan est corrigé, ou l'image
régénérée à partir de ce que dit la voix.

**6. Le LLM comme composant contraint.**
Claude est appelé en sous-processus (`claude -p`), prompt sur stdin, sortie JSON extraite puis validée par un
modèle Pydantic tolérant (un élément invalide est écarté, le lot survit). Profils d'outils au moindre privilège,
chaque appel comptabilisé, budget quotidien avec mode dégradé.

**7. Une voix qui lit exactement ce qu'on veut.**
Nombres, dates, ordinaux et chiffres romains écrits en lettres, sigles épelés, lexique de prononciation par mode,
silences de bord coupés puis remplacés par des pauses mesurées, ligne revenue presque muette rejetée. Les
sous-titres sont recalés sur le **texte d'origine**, pas sur la transcription.

**8. Archives : ne jamais fabriquer un visage.**
Pour les formats historiques, les photos sont de vraies archives trouvées avec leur provenance, restaurées de façon
reconstructive (Real-ESRGAN par tuiles). Les modèles de restauration de visage sont exclus : ils inventeraient les
traits d'une personne réelle. Aucun rendu tant qu'un humain n'a pas validé le script.

Plongée détaillée : **[ARCHITECTURE.md](./ARCHITECTURE.md)**.

---

## 🧱 Stack technique

| Couche | Technologies |
|---|---|
| Interface | Next.js 16, React 19, TypeScript, Tailwind CSS v4, shadcn/ui, TanStack Query |
| API | FastAPI, Python 3.12 (uv), Pydantic v2, SQLAlchemy 2 async + SQLite (WAL) |
| LLM | Claude via le CLI Claude Code en sous-processus, un modèle par usage |
| Calcul | PyTorch 2.8 cu128 (Blackwell), RTX 5070 Ti 12 Go |
| Images | diffusers : SDXL Juggernaut XL v9 (fp16), FLUX.1-schnell (NF4), IP-Adapter, ControlNet, Depth Anything V2 |
| Restauration | Real-ESRGAN x4plus (spandrel), par tuiles |
| Voix | Qwen3-TTS 1.7B (clonage et VoiceDesign, serveur voix dédié en Docker), XTTS-v2, Kokoro |
| Alignement | faster-whisper `large-v3-turbo` |
| Montage | Hyperframes (HTML + GSAP rendu image par image), ffmpeg + libass |
| Tests | pytest, sans jamais appeler le vrai LLM |

---

## 🖼️ Galerie

| | |
|---|---|
| ![Studio, onglet Vidéo](assets/screens/04-studio-video.png) | ![Studio, onglet Publication](assets/screens/05-studio-publication.png) |
| ![Mode : réglages généraux](assets/screens/07-mode-general.png) | ![Mode : prompts Claude](assets/screens/09-mode-prompts.png) |
| ![Mode : visuel et vidéo](assets/screens/10-mode-visuel.png) | ![Atelier d'images](assets/screens/12-atelier-images.png) |

> **Provenance.** Captures réelles de l'application, **en lecture seule** (aucune requête d'écriture autorisée pendant
> la capture) : 01 à 12 prises le 6 octobre 2026, 13 à 16 le 21 octobre 2026 sur l'instance en production. La vidéo
> montrée dans le film (« Le CNN », Cours IA Flash) a été produite par Tiktoker ; sa voix est une voix de synthèse
> créée par description, qui ne reproduit personne.

---

## 📍 État du projet

**En production.** Tiktoker produit en continu et ses vidéos sont publiées sur TikTok ; le travail en cours porte sur
la rétention (format Cours IA Flash, ouvertures et mécaniques animées) et sur l'import des sujets de Radar.

- **Fait** : neuf modes éditables, relecture notée avec boucle d'amélioration, trois files de production avec
  reprise, montage motion design vérifié par Claude, schémas, ouvertures et mécaniques animés, mèmes de réaction,
  création et clonage de voix, atelier d'images, recherche et restauration d'archives, accès depuis le téléphone
  (Wi-Fi ou VPN maillé).
- **En cours** : import des sujets de Radar ; les ouvertures, les mécaniques et le mode Cours IA Flash évoluent
  encore au fil des vidéos publiées.
- **Prévu** : envoi de la vidéo **en brouillon** sur TikTok par l'API officielle ; la publication et la
  programmation resteront faites à la main dans TikTok Studio.
- **Tests** : environ 160 fonctions de test pytest côté backend, sans jamais appeler le vrai LLM.
- **Ordre de grandeur** (relevé du 21 octobre 2026) : ~22 000 lignes Python (dont ~3 100 de tests), ~15 700 lignes
  TypeScript côté interface, ~1 400 lignes JavaScript pour les ouvertures et mécaniques animées, environ 130 routes d'API.

---

## 🎬 Démo sur demande

Une démonstration en direct ou un parcours commenté du code est possible sur demande, dans le cadre d'un échange
professionnel.

**Mohamed Sadadou** · [github.com/Mohamed-Sadadou](https://github.com/Mohamed-Sadadou)

<sub>Captures, vidéo et textes © Mohamed Sadadou, tous droits réservés.</sub>
