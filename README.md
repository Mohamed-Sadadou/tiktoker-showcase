<div align="center">

# 🎬 Tiktoker

**Une fabrique locale de vidéos courtes verticales générées par IA : du sujet au MP4 monté, sous-titré et vérifié, sur une seule carte graphique grand public.**

![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-async-009688?logo=fastapi&logoColor=white)
![Next.js](https://img.shields.io/badge/Next.js-16-000000?logo=nextdotjs&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-2.8%20cu128-EE4C2C?logo=pytorch&logoColor=white)
![Claude](https://img.shields.io/badge/LLM-Claude%20CLI-D97757)
![GPU](https://img.shields.io/badge/GPU-RTX%205070%20Ti%2012%20Go-76b900?logo=nvidia&logoColor=white)
![Code](https://img.shields.io/badge/code-priv%C3%A9-lightgrey)
![Statut](https://img.shields.io/badge/statut-actif-brightgreen)

<a href="assets/demo.mp4"><img src="assets/demo.gif" alt="Démo de Tiktoker : script, images, voix, montage motion design et vidéo produite" width="760"></a>

*Aperçu animé de la démo (25 s). **[Vidéo en qualité HD](assets/demo.mp4)**, effets sonores sans musique.*

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

Tout le calcul lourd (images, voix, transcription, rendu) tourne **sur la machine**, sur une RTX 5070 Ti de 12 Go.
Seule l'écriture passe par Claude.

---

## 🎭 Les modes

Chaque type de vidéo est un **mode**, décrit en données et entièrement configurable depuis l'interface : champs du
script, gabarits, prompts envoyés à Claude, voix, direction artistique, style de montage, grille de relecture et
garde-fous. Le pipeline est le même pour tous : **aucune branche par mode dans le code**.

Huit modes sont livrés ; on peut les modifier, les dupliquer, les réinitialiser ou en créer de nouveaux.

| Mode | Principe | Ce qui le distingue |
|---|---|---|
| 🏛️ **Citations de philosophes** | une citation réelle, racontée | niveau de confiance de l'attribution · montage « Marbre et or » |
| 🕊️ **Récits mémoriels** | figures de la guerre d'indépendance algérienne | sources, relecture obligatoire, photos d'archive réelles restaurées, portrait jamais généré · montage « Archive » |
| 🌀 **Parodie humoristique** | les petites galères du quotidien racontées avec les codes d'un anime | voix de narrateur dédiée · montage « Énergie occulte » |
| ⚔️ **Répliques d'anime** | une réplique, son contexte, sa portée | la voix suit le personnage via un profil · montage « Encre et feu » |
| 📜 **Histoires** | récits historiques courts | sources, relecture obligatoire · montage « Parchemin et bronze » |
| 🧠 **Micro-cours IA** | une notion d'IA en 50 à 60 s, en série | schémas animés avec temps de lecture, parcours rangé du plus simple au plus complexe · montage « Labo » |
| 🤪 **Cours IA décalés** | les mêmes notions, ton provocateur | accroche « provocation + verdict », mèmes de réaction choisis par Claude · montage « WTF » |
| ⚽ **Analyse tactique** | football : un match, une équipe, un système | tableaux tactiques et graphiques animés, sources · montage « Tableau tactique » |

Un mode livré se met à jour quand ses gabarits évoluent, **sans jamais écraser une personnalisation**.

<p align="center">
  <img src="assets/screens/08-mode-champs.png" alt="Éditeur de mode : champs du script" width="49%">
  <img src="assets/screens/11-mode-garde-fous.png" alt="Éditeur de mode : garde-fous" width="49%">
</p>

---

## 🧭 Le parcours d'une vidéo

```mermaid
flowchart LR
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
```

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
5. **Publier.** Légende et hashtags générés, MP4 prêt à téléverser.

<p align="center">
  <img src="assets/screens/01-studio-script.png" alt="Studio : script écrit par Claude" width="49%">
  <img src="assets/screens/02-studio-images.png" alt="Studio : images générées" width="49%">
</p>
<p align="center">
  <img src="assets/screens/03-studio-voix.png" alt="Studio : voix ligne par ligne" width="49%">
  <img src="assets/screens/06-studio-video-motion.png" alt="Studio : montage motion design" width="49%">
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

> **Provenance.** Captures réelles de l'application, prises **le 6 octobre 2026, en lecture seule** (aucune requête
> d'écriture autorisée pendant la capture). L'interface a évolué depuis : trois modes et les écrans de production,
> de relecture et de mèmes n'y figurent pas encore. Les deux vidéos montrées dans la démo ont été produites par
> Tiktoker ; l'une est diffusée sans son (voix clonée non diffusable), l'autre garde sa voix de synthèse libre.

---

## 📍 État du projet

**Actif.** La refonte en modes génériques est terminée et fusionnée (octobre 2026).

- **Fait** : huit modes éditables, relecture notée avec boucle d'amélioration, trois files de production avec
  reprise, montage motion design vérifié par Claude, schémas animés, mèmes de réaction, création et clonage de voix,
  atelier d'images, recherche et restauration d'archives, accès depuis le téléphone (Wi-Fi ou VPN maillé).
- **Tests** : environ 140 tests pytest côté backend.
- **Ordre de grandeur** : ~20 000 lignes Python (tests compris) et ~15 000 lignes TypeScript côté interface.
- **Limites** : publication arrêtée à l'export MP4 (pas de mise en ligne automatique) ; un seul poste, pas de
  comptes utilisateurs.

---

## 🔒 Pourquoi le code est privé

Tiktoker est un outil personnel en évolution rapide, branché sur des services locaux et des comptes personnels.
Ce dépôt présente le produit et ses choix techniques ; le code n'est pas publié. Les modèles et outils tiers
restent soumis à leurs propres licences (notamment XTTS-v2, non commercial) ; une voix clonée et une photo
d'archive ont leurs propres ayants droit. Les vidéos produites sont étiquetées comme contenus générés par IA.

## 🎬 Démo sur demande

Une démonstration en direct ou un parcours commenté du code est possible sur demande, dans le cadre d'un échange
professionnel.

**Mohamed Sadadou** · [github.com/Mohamed-Sadadou](https://github.com/Mohamed-Sadadou)

<sub>Captures, vidéo et textes © Mohamed Sadadou, tous droits réservés.</sub>
