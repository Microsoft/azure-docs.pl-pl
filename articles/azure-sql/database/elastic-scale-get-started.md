---
title: Wprowadzenie do narzędzi Elastic Database
description: Podstawowe objaśnienie funkcji Elastic Database Tools Azure SQL Database, w tym łatwej do uruchomienia przykładowej aplikacji.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 74343b2f05bb4a59e475449c87524ff66cdd605d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919548"
---
# <a name="get-started-with-elastic-database-tools"></a>Wprowadzenie do narzędzi Elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym dokumencie przedstawiono środowisko programistyczne dla [biblioteki klienta Elastic Database](elastic-database-client-library.md) , ułatwiając uruchamianie przykładowej aplikacji. Przykładowa aplikacja tworzy prostą aplikację podzielonej na fragmenty i eksploruje kluczowe możliwości funkcji Elastic Database Tools Azure SQL Database. Koncentruje się na przypadkach użycia w przypadku [zarządzania mapami fragmentu](elastic-scale-shard-map-management.md), [routingu zależnego od danych](elastic-scale-data-dependent-routing.md)i [zapytań wielofragmentuowych](elastic-scale-multishard-querying.md). Biblioteka kliencka jest dostępna dla platformy .NET i języka Java.

## <a name="elastic-database-tools-for-java"></a>Narzędzia Elastic Database dla języka Java

### <a name="prerequisites"></a>Wymagania wstępne

* Zestaw Java developer Kit (JDK), wersja 1,8 lub nowsza
* [Maven](https://maven.apache.org/download.cgi)
* SQL Database lub lokalne wystąpienie SQL Server

### <a name="download-and-run-the-sample-app"></a>Pobieranie i uruchamianie przykładowej aplikacji

Aby skompilować pliki JAR i zacząć pracę z przykładowym projektem, wykonaj następujące czynności:

1. Sklonuj [repozytorium GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) zawierające bibliotekę kliencką wraz z przykładową aplikacją.

2. Edytuj plik _./sample/src/Main/Resources/Resource.Properties_ , aby ustawić następujące elementy:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Aby skompilować przykładowy projekt, w katalogu _./sample_ Uruchom następujące polecenie:

    ```
    mvn install
    ```

4. Aby uruchomić przykładowy projekt, w katalogu _./sample_ Uruchom następujące polecenie:

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. Aby dowiedzieć się więcej o możliwościach biblioteki klienta, należy eksperymentować z różnymi opcjami. Możesz zapoznać się z kodem, aby dowiedzieć się więcej o implementacji przykładowej aplikacji.

    ![Postęp — Java][5]

Gratulacje! Pomyślnie skompilowano i uruchomiono pierwszą aplikację podzielonej na fragmenty przy użyciu narzędzi Elastic Database w Azure SQL Database. Użyj programu Visual Studio lub SQL Server Management Studio, aby nawiązać połączenie z bazą danych, i zapoznaj się z fragmentów, aby utworzyć próbkę. Zobaczysz nowe przykładowe bazy danych fragmentu oraz bazę danych Menedżera map fragmentu utworzoną przez przykład.

Aby dodać bibliotekę kliencką do własnego projektu Maven, Dodaj następującą zależność w pliku pliku pom:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>Narzędzia Elastic Database dla platformy .NET

### <a name="prerequisites"></a>Wymagania wstępne

* Program Visual Studio 2012 lub nowszy przy użyciu języka C#. Pobierz bezpłatną wersję przy [pobieraniu z programu Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* Pakiet NuGet 2,7 lub nowszy. Aby uzyskać najnowszą wersję, zobacz [Instalowanie programu NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Pobieranie i uruchamianie przykładowej aplikacji

Aby zainstalować bibliotekę, przejdź do [witryny Microsoft. Azure. SQLDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Biblioteka jest instalowana z przykładową aplikacją, która została opisana w następnej sekcji.

Aby pobrać i uruchomić przykład, wykonaj następujące kroki:

1. Pobierz [Narzędzie Elastic DB Tools for Azure SQL — przykład wprowadzenie](https://github.com/Azure/elastic-db-tools). Rozpakuj przykład do wybranej lokalizacji.

2. Aby utworzyć projekt, Otwórz rozwiązanie *ElasticDatabaseTools. sln* z katalogu *Elastic-DB-Tools-Master* . 

3. Ustaw projekt *ElasticScaleStarterKit* jako projekt startowy.

4. W projekcie *ElasticScaleStarterKit* otwórz plik *App.config* . Następnie postępuj zgodnie z instrukcjami w pliku, aby dodać nazwę serwera i informacje logowania (nazwa użytkownika i hasło).

5. Skompiluj i uruchom aplikację. Po wyświetleniu monitu włącz program Visual Studio, aby przywrócić pakiety NuGet rozwiązania. Ta akcja spowoduje pobranie najnowszej wersji biblioteki klienta Elastic Database z narzędzia NuGet.

6. Aby dowiedzieć się więcej o możliwościach biblioteki klienta, należy eksperymentować z różnymi opcjami. Należy zwrócić uwagę na kroki wykonywane przez aplikację w danych wyjściowych w konsoli, aby poznać kod w tle.

   ![Postęp][4]

Gratulacje! Pomyślnie skompilowano i uruchomiono pierwszą aplikację podzielonej na fragmenty przy użyciu narzędzi Elastic Database w SQL Database. Użyj programu Visual Studio lub SQL Server Management Studio, aby nawiązać połączenie z bazą danych, i zapoznaj się z fragmentów, aby utworzyć próbkę. Zobaczysz nowe przykładowe bazy danych fragmentu oraz bazę danych Menedżera map fragmentu utworzoną przez przykład.

> [!IMPORTANT]
> Zalecamy, aby zawsze używać najnowszej wersji Management Studio, aby zachować synchronizację z aktualizacjami platformy Azure i SQL Database. [Zaktualizuj program SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="key-pieces-of-the-code-sample"></a>Najważniejsze fragmenty przykładu kodu

* **Zarządzanie mapami fragmentów i fragmentu**: kod ilustruje sposób pracy z fragmentów, zakresami i mapowaniami w pliku *ShardManagementUtils. cs* . Aby uzyskać więcej informacji, zobacz [skalowanie baz danych za pomocą Menedżera mapy fragmentu](https://go.microsoft.com/?linkid=9862595).  

* **Routing zależny od danych**: Routing transakcji do prawej fragmentu jest wyświetlany w pliku *DataDependentRoutingSample. cs* . Aby uzyskać więcej informacji, zobacz [Routing zależny od danych](https://go.microsoft.com/?linkid=9862596).

* **Wykonywanie zapytania dotyczącego wielu fragmentów**: zapytania w fragmentów są zilustrowane w pliku *MultiShardQuerySample. cs* . Aby uzyskać więcej informacji, zobacz [zapytania dotyczące wielu fragmentu](https://go.microsoft.com/?linkid=9862597).

* **Dodawanie pustego fragmentów**: iteracyjne Dodawanie nowej pustej fragmentów jest wykonywane przez kod w pliku *CreateShardSample. cs* . Aby uzyskać więcej informacji, zobacz [skalowanie baz danych za pomocą Menedżera mapy fragmentu](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Inne operacje skalowania elastycznego

* **Dzielenie istniejącej fragmentu**: możliwość podziału fragmentów jest zapewniana przez narzędzie Split-Merge. Aby uzyskać więcej informacji, zobacz temat [przeniesienie danych między skalowanymi bazami danych w chmurze](elastic-scale-overview-split-and-merge.md).

* **Scalanie istniejących fragmentów**: scalanie fragmentu odbywa się również przy użyciu narzędzia Split-Merge. Aby uzyskać więcej informacji, zobacz temat [przeniesienie danych między skalowanymi bazami danych w chmurze](elastic-scale-overview-split-and-merge.md).

## <a name="cost"></a>Koszt

Biblioteka narzędzi Elastic Database Tools jest bezpłatna. W przypadku korzystania z Elastic Database narzędzi nie są naliczane żadne dodatkowe opłaty poza kosztem korzystania z platformy Azure.

Przykładowo Przykładowa aplikacja tworzy nowe bazy danych. Koszt tej możliwości zależy od wybranej wersji SQL Database oraz użycia platformy Azure dla aplikacji.

Aby uzyskać informacje o cenach, zobacz [szczegóły cennika SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat narzędzi Elastic Database, zobacz następujące artykuły:

* Przykłady kodu:
  * Narzędzia Elastic Database ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Narzędzia Elastic Database dla integracji z usługą Azure SQL — Entity Framework](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Elastyczność fragmentu w centrum skryptów](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [powiadomienie dotyczące skalowania elastycznego](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9: [wideo — Omówienie skalowania elastycznego](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Forum dyskusyjne: [Microsoft Q&stronę pytania dla Azure SQL Database](/answers/topics/azure-sql-database.html)
* Aby zmierzyć wydajność: [liczniki wydajności dla Menedżera map fragmentu](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG