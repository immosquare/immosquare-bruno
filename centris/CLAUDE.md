# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Collection Bruno pour l'API **Centris Data Distribution** (RESO standard). API immobilière pour le Québec.

Documentation complète : https://docs.datadistributionqc.centris.ca/fr/getting-started/

## Environnements

| Environnement | API URL | Identity Provider |
|---------------|---------|-------------------|
| Production | `https://datadistributionqc.centris.ca` | `https://accounts.centris.ca` |
| Staging | `https://stg-datadistributionqc.centristst.ca` | `https://stg-accounts.centristst.ca` |

## Authentification

Deux méthodes :
1. **API Key statique** : `Authorization: Bearer {{apiKey}}`
2. **OAuth2 Client Credentials** : POST vers `{{identityProviderUrl}}/connect/token` avec `client_id` + `client_secret`

## Structure de la Collection

```
environments/
  Production.bru
  Staging.bru
Authentication/
  Get Token.bru            - OAuth2 Client Credentials
Metadata/
  Get Schema.bru
Standard Resources/
  Property/     - Propriétés (listings)
  Member/       - Courtiers/agents
  Office/       - Bureaux immobiliers
  Media/        - Photos/vidéos
  OpenHouse/    - Visites libres
  Lookup/       - Tables de référence + Fields
  Other/        - OUID, Associations, Translations, OtherPhone, SocialMedia, EntityEvent
Local Resources/
  Address/      - Villes, quartiers, régions, rues, regroupements, villes desservies
  Property Extensions/  - Toutes les sous-ressources de Property
Advanced Queries/
  - Exemples de filtres avancés
  - Pagination
  - Synchronisation incrémentale
```

## Paramètres OData

| Paramètre | Description |
|-----------|-------------|
| `$select` | Champs à retourner |
| `$filter` | Conditions de filtrage |
| `$orderby` | Tri (asc/desc) |
| `$expand` | Inclure ressources liées |
| `$top` | Limite de résultats |
| `$skip` | Pagination (offset) |
| `$count` | Inclure le total |

## Ressources Principales

**Standard (RESO):** Property, Member, Office, Media, OpenHouse, Lookup, Field, Association, OUID, OtherPhone, SocialMedia, PropertyGreenVerification, PropertyUnitTypes, EntityEvent, Translation

**Locales:** Address, CityOrTownship, CityOrTownshipGrouping, MemberCityOrTownshipServed, Neighborhood, StateRegion, Street, PropertyRooms, PropertyUnit, PropertyParking, PropertyEquipment, PropertyExpense, PropertyRenovation, PropertyMortgage, PropertyZoning, PropertyAdditionalBuilding, PropertyAdditionalSpace, PropertyAnimal, PropertyBuyer, PropertyBuyerAgentOffice, PropertyCompensation, PropertyListAgentOffice, PropertyOwner, PropertyRentedEquipment, PropertySubdivision

## Rate Limits

- 5 requêtes/seconde
- 200 requêtes/minute
- Code 429 + header `Retry-After` si dépassé

## Synchronisation

Stratégie recommandée:
1. `$filter=ModificationTimestamp gt {lastSync}`
2. `$orderby=ModificationTimestamp asc`
3. Paginer avec `$skip`/`$top`
4. Comparer IDs pour détecter suppressions (404 = supprimé)

Note: ModificationTimestamp se met à jour quand la propriété OU ses enfants changent.
