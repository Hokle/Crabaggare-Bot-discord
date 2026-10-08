# Feuille de route — Crabaggare Bot

État actuel : squelette de bot discord.js v14 (14.8.0) avec un chargeur de commandes,
un chargeur d'événements et une seule commande slash, `/crabe`.
Aucune issue, pull request ou note TODO n'existait au moment de la rédaction : cette liste
est tirée de la lecture du code.

## 1. Corrections (prioritaire)

- [ ] **`/crabe` plante en message privé** — `Commands/global/crabe_cmd.js:9` lit
      `interaction.guild.name`, mais `interaction.guild` vaut `null` en DM. Les commandes
      globales étant disponibles en DM par défaut, l'utilisateur reçoit le message d'erreur.
      Ajouter `.setDMPermission(false)` au builder (ou gérer le cas sans serveur).
- [ ] **Intents inutilisés** — `index.js:7-8` demande `GuildMessages` et `MessageContent`
      alors qu'aucun événement `messageCreate` n'existe. `MessageContent` est un intent
      privilégié : s'il n'est pas activé dans le portail développeur, la connexion échoue
      (« Used disallowed intents »). Les retirer tant qu'aucune fonctionnalité ne les utilise.
- [ ] **Chargement des commandes non protégé** — `Commands/index.js:17` fait un `require`
      sans `try/catch`, contrairement à `Events/index.js`. Une commande cassée empêche le
      bot de démarrer.
- [ ] **Déploiement des commandes non attendu** — `scripts/deploy-commands.js:5` lance une
      fonction async sans l'attendre ; en cas d'erreur le bot continue de démarrer
      comme si de rien n'était.

## 2. Sécurité et hygiène du dépôt

- [ ] **`.env` est versionné** malgré le `.gitignore` (valeurs actuellement vides, donc pas de
      fuite). Le retirer du suivi (`git rm --cached .env`) et ajouter un `.env.example`
      listant `BOT_TOKEN` et `BOT_CLIENT_ID`.
- [ ] Gérer l'échec de `client.login` (`index.js:16`) et ajouter un gestionnaire
      `process.on("unhandledRejection")` pour journaliser les erreurs non capturées.
- [ ] Supprimer le second `require("dotenv").config()` (`Commands/index.js:1`), déjà fait
      dans `index.js`.

## 3. Cohérence du code

- [ ] Utiliser `Events.InteractionCreate` au lieu de la chaîne `"interactionCreate"`
      (`Events/global/interactionCreate_event.js:2`), comme le fait déjà `ready_event.js`.
- [ ] Remplacer `.map()` par `.forEach()` dans les chargeurs (`Commands/index.js:7`,
      `Events/index.js:5`) : la valeur de retour n'est pas utilisée.
- [ ] Découvrir automatiquement les sous-dossiers de `Commands/` et `Events/` au lieu de
      la liste codée en dur `["global"]`, pour ajouter des catégories sans toucher au chargeur.
- [ ] Lors d'une mise à jour de discord.js, remplacer `ephemeral: true` par
      `flags: MessageFlags.Ephemeral` (option dépréciée dans les versions 14 récentes).

## 4. Outillage

- [ ] Scripts npm : `start` (`node index.js`) et `deploy` (déploiement des commandes seul,
      sans connecter le bot), à la place du drapeau `--deploy-commands` non documenté.
- [ ] Renseigner `package.json` : `description`, `author`, `engines.node` (`>=16.9.0` exigé
      par discord.js 14.8), et harmoniser le nom (`crabagare` / `Crabaggare`).
- [ ] Ajouter un `README.md` : installation, variables d'environnement, déploiement des
      commandes, ajout d'une commande ou d'un événement.
- [ ] ESLint + Prettier, puis une CI GitHub Actions qui lance le lint.
- [ ] Remplacer le script `test` qui échoue toujours par de vrais tests (ou le retirer).

## 5. Fonctionnalités à étudier (propositions, à valider)

- [ ] `/aide` : liste générée automatiquement depuis `client.commands`.
- [ ] `/ping` : latence du bot et de l'API Discord.
- [ ] Enrichir `/crabe` : distinguer humains et bots dans le compte des membres
      (`memberCount` inclut les bots).
- [ ] Si les intents de messages sont conservés : une fonctionnalité qui les utilise
      (par exemple réagir 🦀 aux messages contenant « crabe »).
