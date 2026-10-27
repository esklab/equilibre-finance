# 📊 ÉQUILIBRE v1.1 — Grand Livre, Portefeuilles & Bilan Financier Personnel

Application moderne de gestion financière personnelle et de comptabilité avec synchronisation cloud en temps réel, design néo-brutaliste géométrique et support multi-appareils.

![Aperçu Équilibre](https://img.shields.io/badge/Version-1.1-emerald)
![Database](https://img.shields.io/badge/Database-Supabase%20PostgreSQL-3ecf8e)
![Hosting](https://img.shields.io/badge/Deploy-Vercel-black)

---

## ✨ Fonctionnalités

- 🔐 **Authentification & Cloud Sync (Supabase PostgreSQL)** : Chaque utilisateur crée son compte et retrouve ses données en temps réel sur smartphone, tablette et ordinateur.
- 💳 **Multi-Portefeuilles & Comptes** : Gestion distincte de vos comptes (Banque, Espèces, Mobile Money Wave/Orange/MTN, etc.) avec solde individuel et part dans le patrimoine total.
- ⇄ **Virements & Transferts Inter-Comptes** : Déplacement d'argent d'un portefeuille à un autre avec gestion des frais éventuels (retraits, rechargements) sans fausser vos revenus/dépenses réels.
- 🏷 **Catégories Synchronisées Cloud** : Création et suppression de catégories personnalisées enregistrées directement en base de données Supabase.
- 🎯 **Objectifs d'Épargne & Projets Cloud** : Suivi des cagnottes, projets vacances ou fonds d'urgence avec jauge de progression et contribution rapide.
- 📐 **Allocation Budgétaire & Méthodes (70/20/10, 50/30/20, Sur-mesure)** : Répartition automatique des revenus entre charges indispensables, épargne/investissements et loisirs avec détection de surconsommation en temps réel.
- ✍️ **Saisie Rapide & Description Optionnelle** : La description n'est plus obligatoire ; une valeur par défaut intelligente est automatiquement assignée.
- 📊 **Onglet Bilan & Totaux** : Grands cartouches Total Entrées, Total Sorties, Solde Net Résultant, taux d'épargne et jauge de consommation du budget.
- 🗓 **Filtres Dynamiques Mois & Année** : Navigation intuitive d'un mois à l'autre avec recalcul instantané des indicateurs et graphiques.
- 📝 **Historique & Grand Livre Avancé** : Modification (`✎`), suppression, filtre par portefeuille, recherche textuelle, tri par colonnes et sous-totaux.
- 💱 **Multi-Devises** : Support XOF (CFA), EUR (€), USD ($), CAD (C$), GBP (£), MAD (DH).
- 📥 **Export CSV Enrichi** : Téléchargement instantané incluant les portefeuilles et frais.

---

## ⚡ Installation & Base de Données

Exécutez ce script SQL dans le **SQL Editor** de votre projet [Supabase](https://supabase.com) :

```sql
-- 1. Table des transactions
create table if not exists transactions (
  id text primary key,
  user_id uuid references auth.users not null,
  type text not null, -- 'income', 'expense', 'transfer'
  amount_eur numeric not null,
  description text default '',
  category text not null,
  date text not null,
  wallet_id text default 'wallet_cash',
  wallet_to_id text,
  fee_eur numeric default 0,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);

alter table transactions enable row level security;
create policy "User can manage their own transactions" on transactions for all using (auth.uid() = user_id);

-- Si la table transactions existait déjà, exécutez ces ajouts de colonnes :
alter table transactions add column if not exists wallet_id text default 'wallet_cash';
alter table transactions add column if not exists wallet_to_id text;
alter table transactions add column if not exists fee_eur numeric default 0;

-- 2. Table des catégories personnalisées
create table if not exists categories (
  id text primary key,
  user_id uuid references auth.users not null,
  name text not null,
  type text not null, -- 'expense' ou 'income'
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);

alter table categories enable row level security;
create policy "User can manage their own categories" on categories for all using (auth.uid() = user_id);

-- 3. Table des portefeuilles
create table if not exists wallets (
  id text primary key,
  user_id uuid references auth.users not null,
  name text not null,
  type text not null, -- 'bank', 'cash', 'mobile_money', 'other'
  initial_balance_eur numeric default 0,
  icon text default 'wallet',
  color text default 'emerald',
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);

alter table wallets enable row level security;
create policy "User can manage their own wallets" on wallets for all using (auth.uid() = user_id);

-- 4. Table des objectifs d'épargne (Goals)
create table if not exists goals (
  id text primary key,
  user_id uuid references auth.users not null,
  title text not null,
  subtitle text default '',
  icon text default '🏖',
  current_eur numeric not null default 0,
  target_eur numeric not null default 0,
  days_left integer default 30,
  bar_color text default 'bg-emerald-500',
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);

alter table goals enable row level security;
create policy "User can manage their own goals" on goals for all using (auth.uid() = user_id);

-- 5. Table des règles d'allocation budgétaire
create table if not exists budget_allocations (
  id text default gen_random_uuid()::text primary key,
  user_id uuid references auth.users not null unique,
  active_method text not null default '70_20_10',
  benchmark_income_eur numeric default 0,
  methods_config jsonb,
  updated_at timestamp with time zone default timezone('utc'::text, now()) not null
);

alter table budget_allocations enable row level security;
create policy "User can manage their own budget allocations" on budget_allocations for all using (auth.uid() = user_id);
```

---

## 🚀 Déploiement

Le projet est configuré pour un déploiement instantané sur **Vercel** (`vercel.json` inclus).

