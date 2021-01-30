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
ms.openlocfilehash: 36eac4c60e0d7fd54ec304f0f17c2ecb454f0629
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063455"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Łączenie Data Factory z usługą Azure kontrolą (wersja zapoznawcza)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule wyjaśniono, jak połączyć Data Factory z usługą Azure kontrolą oraz jak raportować dane odnoszące się do Azure Data Factory działań Kopiuj dane, przepływ danych i Uruchom pakiet usług SSIS.


## <a name="connect-data-factory-to-azure-purview"></a>Łączenie Data Factory z usługą Azure kontrolą
Azure kontrolą to nowa usługa w chmurze, która umożliwia użytkownikom danych centralne zarządzanie nadzorem danych w zakresie danych obejmujących środowisko chmury i środowiska Premium. Możesz połączyć Data Factory z usługą Azure kontrolą, a połączenie umożliwia korzystanie z usługi Azure kontrolą do przechwytywania danych związanych z kopiowaniem, przepływem danych i wykonywaniem pakietów usług SSIS. Istnieją dwa sposoby łączenia fabryki danych z usługą Azure kontrolą:
### <a name="register-azure-purview-account-to-data-factory"></a>Zarejestruj konto usługi Azure kontrolą, aby Data Factory
1. W portalu ADF przejdź do pozycji **Zarządzanie**  ->  **usługą Azure kontrolą**. Wybierz pozycję **Połącz z kontem kontrolą**. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Zrzut ekranu przedstawiający rejestrowanie konta kontrolą.":::
2. Możesz wybrać jedną **z subskrypcji platformy Azure** lub **wprowadzić ją ręcznie**. **W ramach subskrypcji platformy Azure** możesz wybrać konto, do którego masz dostęp. 
3. Po nawiązaniu połączenia powinna być widoczna nazwa konta kontrolą na karcie **konta kontrolą**. 
4. Możesz użyć paska wyszukiwania w górnym centrum Azure Data Factory portalu, aby wyszukać dane. 

Jeśli po zarejestrowaniu konta usługi Azure kontrolą na Data Factory zobaczysz ostrzeżenie w portalu usługi Azure Data Factor, wykonaj następujące kroki, aby rozwiązać ten problem:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Zrzut ekranu przedstawiający ostrzeżenie o rejestrowaniu konta usługi kontrolą.":::

1. Przejdź do Azure Portal i Znajdź fabrykę danych. Wybierz sekcję "Tagi" i sprawdź, czy istnieje tag o nazwie **catalogUri**. Jeśli nie, odłącz i ponownie Połącz konto usługi Azure kontrolą w portalu ADF.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Zrzut ekranu przedstawiający Tagi rejestrowania konta kontrolą.":::

2. Sprawdź, czy uprawnienie jest przyznane do rejestracji konta usługi Azure kontrolą w celu Data Factory. Zobacz [, jak nawiązać połączenie Azure Data Factory i Azure kontrolą](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>Rejestrowanie Data Factory w usłudze Azure kontrolą
Aby dowiedzieć się, jak zarejestrować Data Factory w usłudze Azure kontrolą, zobacz [jak nawiązać połączenie Azure Data Factory i Azure kontrolą](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Raportowanie danych z platformy Azure do kontrolą
Gdy klienci uruchamiają działania kopiowania, przepływu danych lub wykonywania pakietów usług SSIS w Azure Data Factory, klienci mogą uzyskać relację zależności i zapewnić wysoki poziom całego procesu przepływu pracy między źródłami danych i miejscem docelowym.
Aby dowiedzieć się, jak zbierać elementy powiązane z Azure Data Factory, zobacz temat informacje o usłudze [Data Factory](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Następne kroki
[Podręcznik użytkownika z wykazem](../purview/catalog-lineage-user-guide.md)

[Samouczek: wypychanie Data Factory danych pochodzenie do usługi Azure kontrolą](turorial-push-lineage-to-purview.md)