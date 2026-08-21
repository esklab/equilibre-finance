# 📊 ÉQUILIBRE v1.0 — Grand Livre & Bilan Financier Personnel

Application moderne de gestion financière personnelle et de comptabilité avec synchronisation cloud en temps réel, design néo-brutaliste géométrique et support multi-appareils.

![Aperçu Équilibre](https://img.shields.io/badge/Version-1.0-emerald)
![Database](https://img.shields.io/badge/Database-Supabase%20PostgreSQL-3ecf8e)
![Hosting](https://img.shields.io/badge/Deploy-Vercel-black)

---

## ✨ Fonctionnalités

- 🔐 **Authentification & Cloud Sync (Supabase PostgreSQL)** : Chaque utilisateur crée son compte et retrouve ses données en temps réel sur smartphone, tablette et ordinateur.
- 📊 **Onglet Bilan & Totaux** : Grands cartouches Total Entrées, Total Sorties, Solde Net Résultant, taux d'épargne et jauge de consommation du budget.
- 🏷 **Catégories Personnalisées** : Création de catégories d'entrées et de sorties à la volée ou via le gestionnaire dédié.
- 🗓 **Filtres Dynamiques Mois & Année** : Navigation intuitive d'un mois à l'autre avec recalcul instantané des indicateurs et graphiques.
- 📝 **Historique & Grand Livre Avancé** : Modification (`✎`), suppression, recherche textuelle, tri par colonnes et sous-totaux.
- 💱 **Multi-Devises** : Support XOF (CFA), EUR (€), USD ($), CAD (C$), GBP (£), MAD (DH).
- 📥 **Export CSV** : Téléchargement instantané des écritures financières.

---

## ⚡ Installation & Base de Données

Exécutez ce script SQL dans le **SQL Editor** de votre projet [Supabase](https://supabase.com) :

```sql
create table if not exists transactions (
  id text primary key,
  user_id uuid references auth.users not null,
  type text not null,
  amount_eur numeric not null,
  description text not null,
  category text not null,
  date text not null,
  created_at timestamp with time zone default timezone('utc'::text, now()) not null
);

alter table transactions enable row level security;
create policy "User can manage their own transactions" on transactions for all using (auth.uid() = user_id);
```

---

## 🚀 Déploiement

Le projet est configuré pour un déploiement instantané sur **Vercel** (`vercel.json` inclus).
