---
title: plik dołączania
description: plik dołączania
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85115215"
---
Możesz użyć zapytań w Eksplorator danych, aby pobrać i filtrować dane.

1. W górnej części karty **elementy** w Eksplorator danych zapoznaj się z kwerendą domyślną `SELECT * FROM c` . To zapytanie pobiera i wyświetla wszystkie dokumenty z kontenera uporządkowanego według identyfikatora. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Domyślne zapytanie w Eksplorator danych to SELECT * FROM c":::
   
1. Aby zmienić zapytanie, wybierz pozycję **Edytuj filtr**, Zastąp zapytanie domyślne za pomocą `ORDER BY c._ts DESC` , a następnie wybierz pozycję **Zastosuj filtr**.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Domyślne zapytanie w Eksplorator danych to SELECT * FROM c":::

   Zmodyfikowane zapytanie wyświetla dokumenty w kolejności malejącej na podstawie ich sygnatury czasowej, dlatego teraz drugi dokument jest wyświetlany na liście jako pierwszy. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Domyślne zapytanie w Eksplorator danych to SELECT * FROM c":::

Jeśli znasz składnię SQL, możesz wprowadzić dowolne obsługiwane [zapytania SQL](../articles/cosmos-db/sql-api-sql-query.md) w polu predykatu zapytania. Za pomocą Eksplorator danych można także tworzyć procedury składowane, UDF i wyzwalacze dla logiki biznesowej po stronie serwera. 

Eksplorator danych zapewnia łatwy Azure Portal dostęp do wszystkich wbudowanych funkcji dostępu do danych programistycznych dostępnych w interfejsach API. Portal umożliwia również skalowanie przepływności, uzyskiwanie kluczy i parametrów połączenia oraz Przeglądanie metryk i umowy SLA dla konta Azure Cosmos DB. 

