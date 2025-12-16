# Bank Accounts Process API

API Process qui orchestre la récupération et l'agrégation des données financières (comptes et cartes) pour un client.

## Description

Cette API fournit une vue agrégée des comptes bancaires et cartes de crédit d'un client en appelant plusieurs System APIs. Elle suit le pattern API-Led Connectivity comme Process API.

## Endpoints

### GET /api/process/bank-accounts/accounts
Récupère le résumé financier d'un client (comptes + cartes).

**Query Parameters:**
- `customerGlobalId` (required): ID global du client

### GET /api/process/bank-accounts/accounts/{accountGlobalId}
Récupère les détails d'un compte spécifique depuis Global Data.

## Configuration

### Connexions HTTP Requises

Cette API fait des appels HTTP vers:
- **Global Financial Account System API** (port 8081)
- **Credit Card System API** (port 8081)

Configurer dans `global.xml`:
- `Global_Financial_Account_System_API_Config`
- `Credit_Card_System_API_Config`

### Port

- **Port HTTP**: 8082

## Architecture Technique

### Flows Business-Logic

- `get-financial-summary-business-logic`: Agrégation parallèle des comptes et cartes
- `get-account-details-business-logic`: Détails d'un compte depuis Global Data

### Stratégie d'Agrégation

Le flow `get-financial-summary-business-logic` utilise un **parallel** pour récupérer simultanément:
1. Comptes depuis Global Financial Account System API
2. Cartes depuis Credit Card System API

Puis agrège les résultats dans une réponse unifiée.

## Exemples de Requêtes

### GET /api/process/bank-accounts/accounts

```bash
curl -X GET "http://localhost:8082/api/process/bank-accounts/accounts?customerGlobalId=550e8400-e29b-41d4-a716-446655440000"
```

**Response:**
```json
{
  "customer": {
    "globalId": "550e8400-e29b-41d4-a716-446655440000"
  },
  "accounts": [...],
  "creditCards": [...]
}
```

