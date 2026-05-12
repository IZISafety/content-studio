# Content Studio — Guide de déploiement

## Ce que tu vas faire
1. Créer la base de données sur Supabase (5 min)
2. Copier 2 clés dans les fichiers HTML (2 min)
3. Mettre en ligne sur GitHub Pages (5 min)

---

## ÉTAPE 1 — Créer le projet Supabase

1. Va sur **https://supabase.com** → "Start your project" → Crée un compte gratuit
2. Clique **"New project"**
   - Name : `content-studio`
   - Database Password : note-le quelque part (tu n'en auras plus besoin ensuite)
   - Region : choisis la plus proche (ex: West EU)
3. Attends ~2 minutes que le projet soit prêt

---

## ÉTAPE 2 — Créer la table `videos`

1. Dans ton projet Supabase, clique sur **"SQL Editor"** dans le menu gauche
2. Clique **"New query"**
3. Copie-colle exactement ce SQL et clique **"Run"** :

```sql
create table videos (
  id uuid default gen_random_uuid() primary key,
  created_at timestamp with time zone default now(),
  title text not null,
  category text not null,
  status text not null default 'idea',
  target_date date,
  video_url text,
  storyboard_url text,
  notes text
);

-- Autoriser la lecture publique (vue usagers)
alter table videos enable row level security;

create policy "Lecture publique" on videos
  for select using (true);

create policy "Ecriture admin via anon key" on videos
  for all using (true);
```

4. Tu devrais voir **"Success. No rows returned"** → c'est bon ✅

---

## ÉTAPE 3 — Récupérer les clés API

1. Dans Supabase, clique sur **"Project Settings"** (icône engrenage en bas à gauche)
2. Clique **"API"**
3. Copie ces deux valeurs :
   - **Project URL** → ressemble à `https://xxxxxxxxxxxx.supabase.co`
   - **anon public** key → longue chaîne de caractères

---

## ÉTAPE 4 — Coller les clés dans les fichiers HTML

Ouvre `index.html` et `admin.html` avec un éditeur de texte (Notepad, VS Code, etc.)

Dans **chaque fichier**, trouve ces lignes et remplace les valeurs :

```javascript
const SUPABASE_URL      = 'VOTRE_SUPABASE_URL';      // ← colle ton Project URL
const SUPABASE_ANON_KEY = 'VOTRE_SUPABASE_ANON_KEY'; // ← colle ta clé anon
```

Dans `admin.html` uniquement, tu peux aussi changer le mot de passe admin :
```javascript
const ADMIN_PASSWORD = 'admin2024'; // ← change ici
```

---

## ÉTAPE 5 — Mettre en ligne sur GitHub Pages

1. Va sur **https://github.com** → connecte-toi → **"New repository"**
   - Repository name : `content-studio`
   - Visibility : **Public**
   - Clique **"Create repository"**

2. Sur la page du repo, clique **"uploading an existing file"**
3. Glisse-dépose les 3 fichiers : `index.html`, `admin.html`, `README.md`
4. Clique **"Commit changes"**

5. Va dans **Settings** → **Pages** (menu gauche)
   - Source : **"Deploy from a branch"**
   - Branch : **main** → dossier **/ (root)**
   - Clique **Save**

6. Attends 1-2 minutes → ton site est en ligne à :
   - Vue publique : `https://TON_USERNAME.github.io/content-studio/`
   - Vue admin   : `https://TON_USERNAME.github.io/content-studio/admin.html`

---

## URLs finales

| Qui | URL |
|-----|-----|
| Usagers (lecture) | `https://TON_USERNAME.github.io/content-studio/` |
| Admin (ta collègue) | `https://TON_USERNAME.github.io/content-studio/admin.html` |

---

## En cas de problème

- **Page blanche** → vérifie que les clés Supabase sont bien collées sans espaces
- **"Failed to fetch"** → vérifie ton SUPABASE_URL (pas de / à la fin)
- **Données qui n'apparaissent pas** → vérifie que le SQL de l'étape 2 a bien été exécuté

---

## Sécurité — notes importantes

- La clé `anon` Supabase est conçue pour être publique — c'est normal qu'elle soit dans le code
- Le mot de passe admin dans le code est simple mais suffisant pour un usage interne
- Si tu veux plus de sécurité plus tard, on peut migrer vers Supabase Auth (email + mot de passe)
- **Ne partage pas le lien `/admin.html`** avec les usagers, seulement avec ta collègue
