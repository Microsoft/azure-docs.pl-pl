---
title: Połącz Qlik sens, aby Azure Cosmos DB i wizualizować dane
description: W tym artykule opisano kroki wymagane do nawiązania połączenia Azure Cosmos DB Qlik i wizualizacji danych.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 2401786a82b6a3e5bf6c6a893a8e7cd3656f3402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996965"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Połącz Qlik sens, aby Azure Cosmos DB i wizualizować dane
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Qlik sens to narzędzie do wizualizacji danych łączące dane z różnych źródeł w jeden widok. Qlik wykrywa indeksy każdej możliwej relacji w danych, dzięki czemu możesz uzyskać natychmiastowy wgląd w dane. Możesz wizualizować Azure Cosmos DB danych, używając sensu Qlik. W tym artykule opisano kroki wymagane do nawiązania połączenia Azure Cosmos DB Qlik i wizualizacji danych. 

> [!NOTE]
> Łączenie Qlik sens z Azure Cosmos DB jest obecnie obsługiwane w przypadku interfejsów API SQL i interfejsu API Azure Cosmos DB tylko dla konta MongoDB.

Możesz połączyć Qlik sens Azure Cosmos DB z:

* Cosmos DB interfejsu API SQL przy użyciu łącznika ODBC.

* Interfejs API Azure Cosmos DB dla MongoDB przy użyciu łącznika Qlik wykrywania MongoDB (obecnie w wersji zapoznawczej).

* Interfejs API Azure Cosmos DB dla MongoDB i interfejsu API SQL przy użyciu łącznika interfejsu API REST w sensie Qlik.

* Cosmos DB interfejs API usługi Mongo DB przy użyciu łącznika gRPC dla Qlik Core.
W tym artykule opisano szczegóły dotyczące nawiązywania połączenia z Cosmos DB interfejsem API SQL przy użyciu łącznika ODBC.

W tym artykule opisano szczegóły dotyczące nawiązywania połączenia z Cosmos DB interfejsem API SQL przy użyciu łącznika ODBC.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem instrukcji zawartych w tym artykule upewnij się, że masz gotowe do skorzystania z następujących zasobów:

* Pobierz [pulpit Qlik sens](https://www.qlik.com/us/try-or-buy/download-qlik-sense) lub skonfiguruj Qlik sens na platformie Azure, [instalując element witryny Marketplace wykrywania Qlik](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Pobierz [dane gry wideo](https://www.kaggle.com/gregorut/videogamesales), te przykładowe dane są w formacie CSV. Te dane będą przechowywane na koncie Cosmos DB i wizualizowane w Qlik sensie.

* Utwórz konto Azure Cosmos DB interfejsu API SQL, wykonując kroki opisane w sekcji [Tworzenie konta](create-sql-api-dotnet.md#create-account) w artykule Szybki Start.

* [Tworzenie bazy danych i kolekcji](create-sql-api-java.md#add-a-container) — można użyć ustawienia wartości przepływności kolekcji na 1000 ru/s. 

* Załaduj przykładowe dane sprzedaży gry wideo do konta Cosmos DB. Dane można zaimportować za pomocą narzędzia do migracji danych Azure Cosmos DB. można wykonać [sekwencyjne](import-data.md#SQLSeqTarget) lub [zbiorcze Importowanie](import-data.md#SQLBulkTarget) danych. Importowanie danych do konta Cosmos DB trwa około 3-5 minut.

* Pobierz, zainstaluj i skonfiguruj sterownik ODBC, wykonując kroki opisane w artykule [nawiązywanie połączenia z Cosmos DB ze sterownikiem ODBC](odbc-driver.md) . Dane gry wideo są prostym zestawem danych i nie trzeba edytować schematu, wystarczy użyć domyślnego schematu mapowania kolekcji.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Połącz Qlik sens z Cosmos DB

1. Otwórz Qlik sens i wybierz pozycję **Utwórz nową aplikację**. Podaj nazwę aplikacji i wybierz pozycję **Utwórz**.

   :::image type="content" source="./media/visualize-qlik-sense/create-new-qlik-sense-app.png" alt-text="Utwórz nową aplikację Qlik wykrywania":::

2. Po pomyślnym utworzeniu nowej aplikacji wybierz pozycję **Otwórz aplikację** , a następnie wybierz pozycję **Dodaj dane z plików i innych źródeł**. 

3. Ze źródeł danych wybierz pozycję **ODBC** , aby otworzyć okno Konfiguracja nowego połączenia. 

4. Przejdź do **nazwy DSN użytkownika** i wybierz utworzone wcześniej połączenie ODBC. Podaj nazwę połączenia i wybierz pozycję **Utwórz**. 

   :::image type="content" source="./media/visualize-qlik-sense/create-new-connection.png" alt-text="Tworzenie nowego połączenia":::

5. Po utworzeniu połączenia możesz wybrać bazę danych, kolekcję, w której znajdują się dane gry wideo, a następnie wyświetlić jej podgląd.

   :::image type="content" source="./media/visualize-qlik-sense/choose-database-and-collection.png" alt-text="Wybieranie bazy danych i kolekcji"::: 

6. Następnie wybierz pozycję **Dodaj dane** , aby załadować dane do Qlik sens. Po załadowaniu danych do Qlikego można generować szczegółowe informacje i przeprowadzać analizę danych. Możesz użyć szczegółowych informacji lub utworzyć własną aplikację Eksplorowanie sprzedaży gier wideo. Na poniższej ilustracji przedstawiono 

   :::image type="content" source="./media/visualize-qlik-sense/visualize-data.png" alt-text="Wizualizowanie danych":::

### <a name="limitations-when-connecting-with-odbc"></a>Ograniczenia dotyczące nawiązywania połączenia z ODBC 

Cosmos DB to rozproszona baza danych bez schematu z sterownikami modelowymi na potrzeby deweloperów. Sterownik ODBC wymaga bazy danych ze schematem do wnioskowania kolumn, ich typów danych i innych właściwości. Regularne zapytania SQL lub Składnia DML z możliwością relacyjną nie mają zastosowania do Cosmos DB interfejsu API SQL, ponieważ interfejs API SQL nie jest ANSI SQL. Z tego powodu instrukcje SQL wydane za pośrednictwem sterownika ODBC są tłumaczone na składnię SQL specyficzną dla Cosmos DB, która nie ma odpowiedników dla wszystkich konstrukcji. Aby uniknąć tych problemów z tłumaczeniem, należy zastosować schemat podczas konfigurowania połączenia ODBC. Artykuł [łączenie ze sterownikiem ODBC](odbc-driver.md) zawiera sugestie i metody ułatwiające Konfigurowanie schematu. Upewnij się, że to mapowanie ma zostać utworzone dla każdej bazy danych/kolekcji w ramach konta Cosmos DB.

## <a name="next-steps"></a>Następne kroki

Jeśli używasz innego narzędzia do wizualizacji, takiego jak Power BI, możesz połączyć się z nim przy użyciu instrukcji w następującym dokumencie:

* [Wizualizowanie Cosmos DB danych przy użyciu łącznika Power BI](powerbi-visualize.md)
