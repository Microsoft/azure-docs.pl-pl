---
title: Co to jest usługa Azure ARC z włączonym skalowaniem PostgreSQL?
description: Co to jest usługa Azure ARC z włączonym skalowaniem PostgreSQL?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 10f21067f48155a394ac20337d77e3e82aae64d8
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985941"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Co to jest usługa Azure ARC z włączonym skalowaniem PostgreSQL?

Usługa Azure ARC z włączonym skalowaniem PostgreSQL jest jednym z usług bazy danych dostępnych w ramach usług danych z obsługą usługi Azure Arc. Usługa Azure Arc umożliwia uruchamianie usług danych platformy Azure lokalnie, na brzegu i w chmurach publicznych przy użyciu Kubernetes i wybranej infrastruktury. Wartość propozycji usługi Data Services z włączonym usługą Azure ARC:
- Zawsze bieżące
- Elastyczne skalowanie
- Samoobsługowe Inicjowanie obsługi administracyjnej
- Ujednolicone zarządzanie
- Obsługa scenariuszy rozłączonych

Więcej szczegółów można znaleźć pod adresem:
- [Co to są usługi danych z włączonym usługą Azure Arc](overview.md)
- [Tryby i wymagania dotyczące łączności](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Porównanie rozwiązań

W tej sekcji opisano sposób, w jaki usługa Azure ARC z włączonym skalowaniem PostgreSQL różni się Azure Database for PostgreSQL od funkcji wieloskalowania (Citus)?

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Hiperskala usługi Azure Database for PostgreSQL (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database skalowania PostgreSQL (Citus)":::

Jest to współczynnik formularza ze skalowaniem dla aparatu bazy danych Postgres, który jest dostępny jako usługa na platformie Azure (PaaS). Jest on obsługiwany przez rozszerzenie Citus, które umożliwia korzystanie z funkcji skalowania w górę. W tym formularzu usługa jest uruchamiana w centrach danych firmy Microsoft i jest obsługiwana przez firmę Microsoft.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>Usługa Azure ARC z włączonym skalowaniem PostgreSQL

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Usługa Azure ARC z włączonym skalowaniem PostgreSQL":::

Jest to współczynnik formularza ze skalowaniem dla aparatu bazy danych Postgres, który jest dostępny z usługą Azure ARC z włączonymi usługami danych. Jest on również obsługiwany przez rozszerzenie Citus, które umożliwia korzystanie z funkcji skalowania w górę. W tym celu nasz klienci udostępniają infrastrukturę, która hostuje systemy i obsługują je.

## <a name="next-steps"></a>Następne kroki
- **Wypróbuj go.** Szybko Rozpocznij pracę z usługą [Azure Arc szybko Rozpocznij pracę](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) w usłudze Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (eks), aparacie Google Cloud Kubernetes Engine (GKE) lub na maszynie wirtualnej platformy Azure. 

- **Utwórz własne.** Wykonaj następujące kroki, aby utworzyć własny klaster Kubernetes: 
   1. [Instalowanie narzędzi klienta](install-client-tools.md)
   2. [Utwórz kontroler danych usługi Azure Arc](create-data-controller.md)
   3. [Tworzenie grupy serwerów z Azure Database for PostgreSQLm skalowaniem w usłudze Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Przeczytaj więcej na temat usług danych z włączonym usługą Azure Arc](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Przeczytaj informacje o usłudze Azure Arc](https://aka.ms/azurearc)
