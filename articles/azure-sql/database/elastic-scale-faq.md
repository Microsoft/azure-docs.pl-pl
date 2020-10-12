---
title: Elastyczne skalowanie — często zadawane pytania
description: Często zadawane pytania dotyczące Azure SQL Database elastycznym skalowaniu.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8998f03fa44529a5f006936a01f711a279178245
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84045683"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Narzędzia elastycznej bazy danych — często zadawane pytania
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Jeśli mam jedną dzierżawę na fragmentu i brak klucza fragmentowania, jak wypełnić klucz fragmentowania dla informacji o schemacie

Obiekt informacji o schemacie służy tylko do dzielenia scenariuszy scalania. Jeśli aplikacja jest z natury pojedynczej dzierżawy, nie wymaga narzędzia do dzielenia i dlatego nie ma potrzeby wypełniania obiektu informacji o schemacie.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Mam zainicjowaną bazę danych i mam już Menedżera mapy fragmentu, jak zarejestrować nową bazę danych jako fragmentu

Zobacz [Dodawanie fragmentu do aplikacji przy użyciu biblioteki klienta Elastic Database](elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Koszt narzędzi elastycznych baz danych

Korzystanie z biblioteki klienta Elastic Database nie wiąże się z żadnymi kosztami. Koszty naliczane są tylko dla baz danych w Azure SQL Database, które są używane dla fragmentów i Menedżera mapy fragmentu, a także ról sieci Web/procesu roboczego, które można udostępniać dla narzędzia Split Merge.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Dlaczego moje poświadczenia nie działają po dodaniu fragmentu z innego serwera

Nie używaj poświadczeń w postaci "User ID = username@servername ", zamiast tego po prostu Użyj "User ID = username".  Upewnij się również, że nazwa logowania "username" ma uprawnienia do fragmentu.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Czy muszę utworzyć Menedżera mapy fragmentu i wypełnić fragmentów przy każdym uruchomieniu aplikacji

Nie — tworzenie Menedżera mapy fragmentu (na przykład [ShardMapManagerFactory. CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) jest operacją jednorazową.  Aplikacja powinna używać wywołania [ShardMapManagerFactory. TryGetSqlShardMapManager ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) podczas uruchamiania aplikacji.  Powinno istnieć tylko jedno takie wywołanie dla każdej domeny aplikacji.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Mam pytania dotyczące korzystania z narzędzi elastycznych baz danych, jak uzyskać odpowiedzi na nie

Skontaktuj się z nami na [stronie pytania&pytań SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html).

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Gdy Uzyskuję połączenie z bazą danych przy użyciu klucza fragmentowania, mogę nadal wykonywać zapytania o dane dla innych kluczy fragmentowania w tym samym fragmentu.  Jest to zaprojektowane przez

Interfejsy API skalowania elastycznego zapewniają połączenie z poprawną bazą danych dla klucza fragmentowania, ale nie zapewniają funkcji filtrowania kluczy fragmentowania.  Dodaj klauzule **WHERE** do zapytania, aby ograniczyć zakres do podanego klucza fragmentowania, w razie potrzeby.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Czy mogę użyć innej wersji SQL Database dla każdego fragmentu w moim zestawie fragmentu

Tak, fragmentu jest pojedynczą bazą danych, a więc jedną fragmentu może być wersja Premium, a inna to wersja Standard. Ponadto wydanie fragmentu może być skalowane w górę lub w dół w okresie istnienia fragmentuu.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Czy narzędzie do rozdzielnego scalania udostępnia (lub usuwa) bazę danych podczas operacji dzielenia lub scalania

Nie. W przypadku operacji **podziału** docelowa baza danych musi istnieć z odpowiednim schematem i być zarejestrowana za pomocą Menedżera mapy fragmentu.  W przypadku operacji **scalania** należy usunąć fragmentu z Menedżera mapy fragmentu, a następnie usunąć bazę danych.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]