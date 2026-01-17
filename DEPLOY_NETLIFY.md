# Déploiement sur Netlify

Ce fichier décrit les étapes pour builder et déployer l'API sur Netlify (Functions TypeScript).

## Prérequis

- Node.js (recommandé: 18 ou 20)
- npm
- Compte Netlify et dépôt Git connecté au site (ou `netlify-cli` pour déployer manuellement)

## Variables d'environnement à configurer sur Netlify

Dans **Site > Settings > Build & deploy > Environment**, ajoutez:

- `SUPABASE_URL` — URL Supabase
- `SUPABASE_SERVICE_ROLE_KEY` — clé `service_role` Supabase (sensible, ne pas committer)
- `WALLET_SERVICE_URL` — (optionnel) URL publique du endpoint wallet, ex:
  `https://<votre-site>.netlify.app/.netlify/functions/wallet/api/wallets`

Remarque: ne jamais committer de clés dans le dépôt.

## Build (local)

1. Installer les dépendances (incluant devDependencies pour TypeScript):

```powershell
npm ci --include=dev
```

2. Compiler TypeScript:

```powershell
npm run build
```

Après la compilation, vous devez voir:

- `dist/src/server.js` (build du serveur)
- `dist/netlify/functions/*.js` (fonctions Netlify compilées: `user.js`, `wallet.js`, `transaction.js`)

## Déploiement via Git (recommandé)

1. Pousser vos commits sur la branche connectée au site Netlify (ex: `main`).
2. Netlify utilisera `netlify.toml` et exécutera la commande de build définie:

```
npm ci --include=dev && npm run build
```

3. Vérifier les variables d'environnement dans l'interface Netlify et lancer un déploiement.

## Déploiement manuel avec `netlify-cli` (optionnel)

1. (Si nécessaire) installer/login:

```bash
npm i -g netlify-cli
npx netlify login
```

2. Créer un site (si vous n'en avez pas):

```bash
npx netlify sites:create --name mon-api-studentpay
```

3. Déployer (pré-build):

```bash
# Assurez-vous d'avoir exécuté `npm run build` avant
npx netlify deploy --prod --dir=. --message "deploy"
```

Remarque: Netlify lira `netlify.toml` (chemin des functions = `dist/netlify/functions`).

## Configurer les variables via CLI (optionnel)

```bash
npx netlify env:set SUPABASE_URL "https://xxxx"
npx netlify env:set SUPABASE_SERVICE_ROLE_KEY "xxxx"
```

## Vérifications après déploiement

- Ouvrir le panneau Functions dans Netlify (Site > Functions) pour voir les logs et erreurs.
- Tester les routes publiques selon `netlify.toml` (ex: `GET /api/wallets/user/:userId`).

## Bonnes pratiques

- Ne pas exposer `SUPABASE_SERVICE_ROLE_KEY` au client.
- Restreindre les permissions côté Supabase si possible.
- Spécifier la version Node dans Netlify (Build & deploy > Environment > Node version) si vous voulez contrôler l'environnement.

## Souhaitez-vous que j'automatise la création du site et des variables d'environnement via `netlify-cli` ?
