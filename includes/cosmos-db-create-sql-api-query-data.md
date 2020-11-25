---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 448c445f49fb4baa300ce6636288a39080da3baf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028117"
---
Możesz użyć zapytań w Eksplorator danych, aby pobrać i filtrować dane.

1. W górnej części karty **elementy** w Eksplorator danych zapoznaj się z kwerendą domyślną `SELECT * FROM c` . To zapytanie pobiera i wyświetla wszystkie dokumenty z kontenera uporządkowanego według identyfikatora. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Domyślne zapytanie w Eksplorator danych to SELECT * FROM c":::
   
1. Aby zmienić zapytanie, wybierz pozycję **Edytuj filtr**, Zastąp zapytanie domyślne za pomocą `ORDER BY c._ts DESC` , a następnie wybierz pozycję **Zastosuj filtr**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Zmień zapytanie domyślne przez dodanie ORDER BY c._ts DESC i kliknij pozycję Zastosuj filtr":::

   Zmodyfikowane zapytanie wyświetla dokumenty w kolejności malejącej na podstawie ich sygnatury czasowej, dlatego teraz drugi dokument jest wyświetlany na liście jako pierwszy. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Zmieniono zapytanie na ORDER BY c._ts DESC i klikając przycisk Zastosuj filtr":::

Jeśli znasz składnię SQL, możesz wprowadzić dowolne obsługiwane [zapytania SQL](../articles/cosmos-db/sql-query-getting-started.md) w polu predykatu zapytania. Za pomocą Eksplorator danych można także tworzyć procedury składowane, UDF i wyzwalacze dla logiki biznesowej po stronie serwera. 

Eksplorator danych zapewnia łatwy Azure Portal dostęp do wszystkich wbudowanych funkcji dostępu do danych programistycznych dostępnych w interfejsach API. Portal umożliwia również skalowanie przepływności, uzyskiwanie kluczy i parametrów połączenia oraz Przeglądanie metryk i umowy SLA dla konta Azure Cosmos DB.