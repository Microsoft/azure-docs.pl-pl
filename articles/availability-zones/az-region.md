---
title: Regiony obsługujące Strefy dostępności na platformie Azure
description: Aby utworzyć aplikacje o wysokiej dostępności i odporności na platformie Azure, Strefy dostępności zapewnić fizycznie oddzielne lokalizacje, za pomocą których można uruchamiać zasoby.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/05/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: cf1fc81ea63db21d2e864c00e1987eec3d376b59
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853164"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regiony obsługujące Strefy dostępności na platformie Azure

Strefy dostępności to oferta wysokiej dostępności, która chroni Twoje aplikacje i dane przed awariami centrów danych. Aby uzyskać więcej informacji na temat Strefy dostępności, zobacz [regiony i strefy dostępności na platformie Azure](az-overview.md).

Ta sekcja zawiera listę usług i regionów platformy Azure, które obsługują Strefy dostępności.

Usługi dostępne w poszczególnych regionach wraz z nadchodzącym planem dostępności można znaleźć w temacie [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="americas"></a>Ameryka Północna i Południowa

| Usługa | Central US | East US | Wschodnie stany USA 2 | Zachodnie stany USA 2 |
| --- | :---: | :---: | :---: | :---: |
| **Środowisko obliczeniowe** |  |  |  |  |
| Linux Virtual Machines             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows Virtual Machines           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Zestawy skali maszyn wirtualnych         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB środowiska Azure App Service | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Dyski zarządzane                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Magazyn strefowo nadmiarowy             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sieć** |  |  |  |  |
| Standardowy adres IP                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| usługa Load Balancer w warstwie Standardowa             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Brama ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (wersja 2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bazy danych** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Baza danych SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_check_mark: (wersja zapoznawcza) |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analiza** |  |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integracja** |  |  |  |  |
| Service Bus (tylko warstwa Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Tożsamość** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="europe"></a>Europa

| Usługa | Francja Środkowa | Europa Północna | Południowe Zjednoczone Królestwo | West Europe |
| --- | :---: | :---: | :---: | :---: |
| **Środowisko obliczeniowe** |  |  |  |  |
| Linux Virtual Machines             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows Virtual Machines           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Zestawy skali maszyn wirtualnych         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB środowiska Azure App Service | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Dyski zarządzane                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Magazyn strefowo nadmiarowy             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sieć** |  |  |  |  |
| Standardowy adres IP                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| usługa Load Balancer w warstwie Standardowa             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Brama ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (wersja 2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bazy danych** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Baza danych SQL                       | :heavy_check_mark: | : heavy_check_mark: (wersja zapoznawcza) | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analiza** |  |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integracja**  |  |  |  |  |
| Service Bus (tylko warstwa Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Tożsamość** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="asia-pacific"></a>Azja i Pacyfik

| Usługa | Japan East | Southeast Asia | Australia Wschodnia |
| --- | :---: | :---: | :---: |
| **Środowisko obliczeniowe** |  |  |  |
| Linux Virtual Machines             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows Virtual Machines           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Zestawy skali maszyn wirtualnych         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB środowiska Azure App Service | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Kubernetes Service           | :heavy_check_mark: | :heavy_check_mark: |  |
| **Storage** |  |  |  |
| Dyski zarządzane                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Magazyn strefowo nadmiarowy             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Sieć** |  |  |  |
| Standardowy adres IP                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| usługa Load Balancer w warstwie Standardowa             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN Gateway                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Brama ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Application Gateway (wersja 2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Firewall                     | :heavy_check_mark: | :heavy_check_mark: |  |
| **Bazy danych** |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: |  |
| Baza danych SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cache for Redis              | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Analiza** |  |  |  |
| Event Hubs                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integracja** |  |  |  |
| Service Bus (tylko warstwa Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Event Grid                         | :heavy_check_mark: | :heavy_check_mark: |  |
| **Tożsamość** |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>Inne

Platforma Azure oferuje również pomoc techniczną Strefy dostępności w następujących regionach:

- US Gov Wirginia
- Północna Republika Południowej Afryki
- South Central US
- Kanada Środkowa

Aby dowiedzieć się więcej na temat pomocy technicznej Strefy dostępności w tych czterech regionach, skontaktuj się z przedstawicielem firmy Microsoft dotyczącym sprzedaży lub klienta lub Otwórz żądanie pomocy technicznej.

## <a name="next-steps"></a>Następne kroki

- [Regiony i strefy dostępności na platformie Azure](az-overview.md)
