---
title: Odnajdywanie, łączenie i eksplorowanie danych w usłudze Synapse przy użyciu usługi Azure Purview
description: Przewodnik po tym, jak odnajdywać dane, łączyć je i eksplorować w u usługi Synapse
author: Rodrigossz
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: f98507fa72f4503700bf39393063dd1ecc650e91
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567897"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Odnajdywanie, łączenie i eksplorowanie danych w usłudze Synapse przy użyciu usługi Azure Purview 

W tym dokumencie poznasz typy interakcji, które można wykonywać podczas rejestrowania konta usługi Azure Purview w usłudze Synapse. 

## <a name="prerequisites"></a>Wymagania wstępne 

- [Konto usługi Azure Purview](../../purview/create-catalog-portal.md) 
- [Obszar roboczy synapse](../quickstart-create-workspace.md) 
- [Łączenie konta usługi Azure Purview z usługą Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Korzystanie z usługi Azure Purview w usłudze Synapse 

Korzystanie z usługi Azure Purview w usłudze Synapse wymaga dostępu do tego konta programu Purview. Synapse przekazuje uprawnienie purview. Jeśli na przykład masz rolę uprawnień curatora, możesz edytować metadane skanowane przez usługę Azure Purview. 

### <a name="data-discovery-search-datasets"></a>Odnajdywanie danych: wyszukiwanie zestawów danych 

Aby odnaleźć dane zarejestrowane i zeskanowane przez usługę Azure Purview, możesz użyć paska wyszukiwania w górnej części obszaru roboczego usługi Synapse. Upewnij się, że wybierasz pozycję Azure Purview, aby wyszukać wszystkie dane organizacji. 

[![Wyszukiwanie zasobów usługi Azure Purview](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Akcje usługi Azure Purview 

Oto lista funkcji usługi Azure Purview dostępnych w usłudze Synapse: 
- **Omówienie** metadanych 
- Wyświetlanie i edytowanie **schematu** metadanych z klasyfikacjami, terminami słownika, typami danych i opisami 
- Wyświetl **informacje o pokładzie,** aby zrozumieć zależności i wykonać analizę wpływu. Aby uzyskać więcej informacji na temat, zobacz [temat](../../purview/catalog-lineage-user-guide.md)
- Wyświetlanie i edytowanie **kontaktów,** aby dowiedzieć się, kto jest właścicielem lub ekspertem w zestawie danych 
- **Powiązane** w celu zrozumienia zależności hierarchicznych określonego zestawu danych. To środowisko jest przydatne podczas przeglądania hierarchii danych.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Akcje, które można wykonywać na zestawach danych za pomocą zasobów synapse 

### <a name="connect-data-to-synapse"></a>Łączenie danych z synapse 

- Możesz utworzyć nową **usługę połączona z usługą** Synapse. Ta akcja będzie wymagana do skopiowania danych do usługi Synapse lub ich ustawienia w centrum danych (w przypadku obsługiwanych źródeł danych, takich jak ADLSg2) 
- W przypadku obiektów, takich jak pliki, foldery lub tabele, można bezpośrednio utworzyć nowy zestaw danych integracji i skorzystać z istniejącej połączonej usługi, jeśli została już utworzona  

Nie możemy jeszcze wywnioskować, czy istnieje połączona usługa lub zestaw danych integracji. 

###  <a name="develop-in-synapse"></a>Opracowywanie w uciekinie Synapse 

Istnieją trzy akcje, które można wykonać: Nowy skrypt **SQL,** **Nowy notes** i Nowy **Przepływ danych**. 

Za **pomocą nowego skryptu SQL** w zależności od typu pomocy technicznej można: 
- Wyświetl 100 górnych wierszy, aby zrozumieć kształt danych. 
- Tworzenie tabeli zewnętrznej na Synapse SQL danych 
- Ładowanie danych do bazy Synapse SQL danych 
 
Za **pomocą nowego notesu** można: 
- Ładowanie danych do ramki danych platformy Spark 
- Utwórz tabelę platformy Spark (jeśli to zrobisz za pośrednictwem formatu Parquet, zostanie również tworzymy tabelę puli SQL bez serwera). 
 
Za **pomocą nowego przepływu** danych można utworzyć zestaw danych integracji, który może być używany jako źródło w potoku przepływu danych. Przepływ danych to możliwość wykonywania przekształceń danych przez deweloperów bez kodu. Aby uzyskać więcej informacji na [temat korzystania z przepływu danych w u usługi Synapse.](../quickstart-data-flow.md)

##  <a name="nextsteps"></a>Następne kroki 

- [Rejestrowanie i skanowanie zasobów Azure Synapse usłudze Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)
- [Jak wyszukiwać dane w usłudze Azure Purview Data Catalog](../../purview/how-to-search-catalog.md)
