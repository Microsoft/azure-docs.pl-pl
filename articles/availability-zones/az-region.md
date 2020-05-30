---
title: Regiony obsługujące Strefy dostępności na platformie Azure
description: Aby utworzyć aplikacje o wysokiej dostępności i odporności na platformie Azure, Strefy dostępności zapewnić fizycznie oddzielne lokalizacje, za pomocą których można uruchamiać zasoby.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 04/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 903bfd0be6c82cb5836248a784805b45edcabf36
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196373"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regiony obsługujące Strefy dostępności na platformie Azure

Strefy dostępności to oferta wysokiej dostępności, która chroni Twoje aplikacje i dane przed awariami centrów danych. Aby uzyskać więcej informacji na temat Strefy dostępności, zobacz [regiony i strefy dostępności na platformie Azure](az-overview.md).

## <a name="services-support-by-region"></a>Obsługa usług według regionów

Ta sekcja zawiera listę usług i regionów platformy Azure, które obsługują Strefy dostępności.

Usługi dostępne w poszczególnych regionach wraz z nadchodzącym planem dostępności można znaleźć w temacie [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/).

|                                 |Ameryka Północna i Południowa |              |           |           | Europa |              |          |              | Azja i Pacyfik |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |Środkowe stany USA|Wschodnie stany USA|Wschodnie stany USA 2|Zachodnie stany USA 2|Francja Środkowa|Europa Północna|Południowe Zjednoczone Królestwo|Europa Zachodnia|Japonia Wschodnia|Azja Południowo-Wschodnia|Australia Wschodnia|
| **Obliczanie**                         |            |              |           |           |                |              |          |             |            |                |                |
| Maszyny wirtualne z systemem Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Maszyny wirtualne z systemem Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Zestawy skali maszyn wirtualnych      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| ILB środowiska Azure App Service | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Dyski zarządzane                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Magazyn strefowo nadmiarowy          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Sieć**                     |            |              |           |           |                |              |          |             |            |                |                |
| Standardowy adres IP        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| usługa Load Balancer w warstwie Standardowa     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Brama ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Application Gateway (wersja 2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Bazy danych**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| Baza danych SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003; (wersja zapoznawcza) | &#10003;       | &#10003; (wersja zapoznawcza)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB      | &#10003;   |  &#10003;  |  &#10003; | &#10003; |  &#10003;  | &#10003;    | &#10003; |  &#10003;   |     &#10003;       | &#10003;    | &#10003;    |
| **Analiza**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |                |
| **Integration**                     |            |              |           |           |                |              |          |             |            |                |                |
| Service Bus (tylko warstwa Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Tożsamość**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |


## <a name="next-steps"></a>Następne kroki

- [Regiony i Strefy dostępności na platformie Azure](az-overview.md)
