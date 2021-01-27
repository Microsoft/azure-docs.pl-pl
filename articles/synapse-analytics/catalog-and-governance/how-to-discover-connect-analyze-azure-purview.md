---
title: Odkrywanie, łączenie i Eksplorowanie danych w programie Synapse przy użyciu usługi Azure kontrolą
description: Przewodnik po odnajdywaniu danych, łączeniu ich i eksplorowaniu w usłudze Synapse
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a3ed7979584787627c97cfec3bb2e19c147f7152
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98872670"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Odkrywanie, łączenie i Eksplorowanie danych w programie Synapse przy użyciu usługi Azure kontrolą 

W tym dokumencie przedstawiono typ interakcji, które można wykonać podczas rejestrowania konta usługi Azure kontrolą w usłudze Synapse. 

## <a name="prerequisites"></a>Wymagania wstępne 

- [Konto usługi Azure kontrolą](../../purview/create-catalog-portal.md) 
- [Obszar roboczy Synapse](../quickstart-create-workspace.md) 
- [Łączenie konta usługi Azure kontrolą w usłudze Synapse](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Korzystanie z usługi Azure kontrolą w Synapse 

Korzystanie z usługi Azure kontrolą w Synapse wymaga posiadania dostępu do tego konta kontrolą. Synapse Pass-through the kontrolą. Jeśli masz na przykład rolę uprawnienia Curator, będziesz mieć możliwość edytowania metadanych przeskanowanych przez usługę Azure kontrolą. 

### <a name="data-discovery-search-datasets"></a>Odnajdywanie danych: Przeszukaj zbiory 

Aby odnajdywać dane zarejestrowane i przeskanowane przez usługę Azure kontrolą, możesz użyć paska wyszukiwania w górnym centrum obszaru roboczego Synapse. Upewnij się, że wybrano pozycję Azure kontrolą, aby wyszukać wszystkie dane organizacji. 

[![Wyszukaj zasoby usługi Azure kontrolą](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Akcje usługi Azure kontrolą 

Poniżej znajduje się lista funkcji usługi Azure kontrolą, które są dostępne w Synapse: 
- **Przegląd** metadanych 
- Wyświetlanie i edytowanie **schematu** metadanych przy użyciu klasyfikacji, terminów słownika, typów danych i opisów 
- Wyświetl **elementy powiązane, aby** zrozumieć zależności i przeanalizować wpływ. Aby uzyskać więcej informacji [na temat, zobacz temat](../../purview/catalog-lineage-user-guide.md) elementy powiązane
- Wyświetlaj i edytuj **kontakty** , aby wiedzieć, kto jest właścicielem lub ekspertem nad zestawem danych 
- **Powiązane** z zrozumienie hierarchicznych zależności określonego zestawu danych. To środowisko ułatwia przeglądanie hierarchii danych.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Akcje, które można wykonywać na zestawach danych przy użyciu zasobów Synapse 

### <a name="connect-data-to-synapse"></a>Połącz dane z Synapse 

- Można utworzyć **nową połączoną usługę** do Synapse. Ta akcja będzie wymagana do kopiowania danych do Synapse lub ich w centrum danych (dla obsługiwanych źródeł danych, takich jak ADLSg2) 
- W przypadku obiektów, takich jak pliki, foldery lub tabele, można bezpośrednio utworzyć **Nowy zestaw danych integracji** i użyć istniejącej połączonej usługi, jeśli został już utworzony. 

Nie jesteśmy jeszcze w stanie stwierdzić, czy istnieje już połączona usługa lub zestaw danych integracji. 

###  <a name="develop-in-synapse"></a>Programowanie w Synapse 

Istnieją trzy akcje, które można wykonać: **Nowy skrypt SQL**, **Nowy Notes** i **Nowy przepływ danych**. 

Za pomocą **nowego skryptu SQL**, w zależności od typu pomocy technicznej, można: 
- Wyświetl pierwsze 100 wierszy, aby zrozumieć kształt danych. 
- Tworzenie tabeli zewnętrznej z bazy danych SQL Synapse 
- Ładowanie danych do bazy danych SQL Synapse 
 
Za pomocą **nowego notesu** można: 
- Ładowanie danych do ramki Dataframe platformy Spark 
- Utwórz tabelę Spark (w przypadku przekroczenia formatu parquet) tworzy również tabelę puli SQL bez serwera). 
 
Przy użyciu **nowego przepływu danych** można utworzyć zestaw danych integracji, który może być używany jako źródło w potoku przepływu danych. Przepływ danych to brak możliwości programisty, który umożliwia przekształcanie danych. Więcej informacji o [używaniu przepływu danych w programie Synapse](../quickstart-data-flow.md).

##  <a name="nextsteps"></a>Następne kroki 

- [Rejestrowanie i skanowanie zasobów usługi Azure Synapse na platformie Azure kontrolą](../../purview/register-scan-azure-synapse-analytics.md)
- [Jak wyszukiwać dane w usłudze Azure kontrolą Data Catalog](../../purview/how-to-search-catalog.md)
