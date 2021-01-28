---
title: Łączenie usługi Data Factory z usługą Azure Purview
description: Dowiedz się, jak połączyć Data Factory z platformą Azure kontrolą
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: d1a07a3733bddc1516a4d2e19f3cac31a7702954
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944938"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Łączenie Data Factory z usługą Azure kontrolą (wersja zapoznawcza)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule wyjaśniono, jak połączyć fabrykę danych z usługą Azure kontrolą oraz jak raportować dane odnoszące się do danych o działaniach ADF Kopiuj dane, przepływ danych i pakiet SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Łączenie fabryki danych z usługą Azure kontrolą
Azure kontrolą to nowa usługa w chmurze, która umożliwia użytkownikom danych centralne zarządzanie nadzorem danych w zakresie danych obejmujących środowisko chmury i środowiska Premium. Możesz połączyć fabrykę danych z platformą Azure kontrolą, a połączenie umożliwia korzystanie z usługi Azure kontrolą do przechwytywania danych z obszaru kopiowania, przepływu danych i wykonywania pakietów usług SSIS. Aby dowiedzieć się, jak zarejestrować fabrykę danych w usłudze Azure kontrolą, zobacz [jak nawiązać połączenie Azure Data Factory i Azure kontrolą](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Raportowanie danych z platformy Azure do kontrolą
Gdy klienci uruchamiają działania kopiowania, przepływu danych lub wykonywania pakietów usług SSIS w Azure Data Factory, klienci mogą uzyskać relację zależności i zapewnić wysoki poziom całego procesu przepływu pracy między źródłami danych i miejscem docelowym.
Aby dowiedzieć się, jak zbierać elementy powiązane z Azure Data Factory, zobacz temat informacje o usłudze [Data Factory](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Następne kroki
[Podręcznik użytkownika z wykazem](../purview/catalog-lineage-user-guide.md)

[Samouczek: wypychanie Data Factory danych pochodzenie do usługi Azure kontrolą](turorial-push-lineage-to-purview.md)