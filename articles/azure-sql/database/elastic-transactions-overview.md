---
title: Transakcje rozproszone w bazach danych w chmurze (wersja zapoznawcza)
description: Przegląd transakcji Elastic Database z Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 60f6863bbe051338308c30e22c6969d84670dc64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91409735"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Transakcje rozproszone w bazach danych w chmurze (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Transakcje elastycznej bazy danych dla Azure SQL Database umożliwiają uruchamianie transakcji obejmujących kilka baz danych w programie SQL Database. Transakcje Elastic Database dla SQL Database są dostępne dla aplikacji .NET przy użyciu programu ADO.NET i integrują się z znanym środowiskiem programistycznym korzystającym z klas [System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) . Aby uzyskać bibliotekę, zobacz [.NET Framework 4.6.1 (Instalator sieci Web)](https://www.microsoft.com/download/details.aspx?id=49981).

W środowisku lokalnym taki scenariusz zwykle wymaga uruchomionego programu Microsoft Distributed Transaction Coordinator (MSDTC). Ponieważ usługa MSDTC nie jest dostępna dla aplikacji platformy jako usługi na platformie Azure, możliwość skoordynowania transakcji rozproszonych została teraz zintegrowana bezpośrednio z SQL Database. Aplikacje mogą łączyć się z dowolnymi bazami danych w SQL Database do uruchamiania transakcji rozproszonych, a jedna z baz danych w sposób niewidoczny dla użytkownika koordynuje transakcję rozproszoną, jak pokazano na poniższej ilustracji.

  ![Transakcje rozproszone z Azure SQL Database przy użyciu transakcji Elastic Database ][1]

## <a name="common-scenarios"></a>Typowe scenariusze

Transakcje elastycznej bazy danych dla SQL Database umożliwiają aplikacjom wprowadzanie niepodzielnych zmian danych przechowywanych w kilku różnych bazach danych w programie SQL Database. Wersja zapoznawcza koncentruje się na środowiskach deweloperskich po stronie klienta w językach C# i .NET. Środowisko pracy po stronie serwera przy użyciu języka T-SQL jest planowane w późniejszym czasie.  
Transakcje elastycznej bazy danych są przeznaczone dla następujących scenariuszy:

* Aplikacje z wieloma bazami danych na platformie Azure: w tym scenariuszu dane są partycjonowane w pionie w wielu bazach danych w SQL Database, w taki sposób, że różne rodzaje danych znajdują się w różnych baz danych. Niektóre operacje wymagają zmian danych, które są przechowywane w co najmniej dwóch bazach danych. Aplikacja używa transakcji Elastic Database do koordynowania zmian w bazach danych i zapewniania niepodzielności.
* Podzielonej na fragmenty bazy danych na platformie Azure: w tym scenariuszu warstwa danych używa [biblioteki klienta Elastic Database](elastic-database-client-library.md) lub fragmentowania w poziomie, aby podzielić dane między wiele baz danych w SQL Database. Jednym z widocznych przypadków użycia jest konieczność wykonywania niepodzielnych zmian w podzielonej na fragmenty aplikacji wielodostępnej, gdy zmiany obejmują dzierżawców. Pomyśl o wystąpieniu transferu między dzierżawcami, które znajdują się w różnych bazach danych. Drugi przypadek jest szczegółowym fragmentowania, aby sprostać potrzebom związanym z pojemnością dla dużej dzierżawy, co z kolei oznacza, że niektóre operacje niepodzielne muszą być rozciągane między kilka baz danych używanych dla tej samej dzierżawy. Trzecia sprawa jest niepodzielnymi aktualizacjami odwołań do danych replikowanych między bazami danych. Niepodzielna, transakcyjna, operacje w tych wierszach, można teraz koordynować w kilku bazach danych przy użyciu wersji zapoznawczej.
  Transakcje elastycznej bazy danych korzystają z dwufazowego zatwierdzania, aby zapewnić niepodzielność transakcji między bazami danych. Jest to dobre dopasowanie w przypadku transakcji obejmujących mniej niż 100 baz danych jednocześnie w ramach jednej transakcji. Te limity nie są wymuszane, ale jeden z nich powinien oczekiwać wydajności i częstotliwości sukcesów dla transakcji Elastic Database, gdy przekraczają te limity.

## <a name="installation-and-migration"></a>Instalacja i migracja

Możliwości transakcji Elastic Database w SQL Database są udostępniane za pomocą aktualizacji bibliotek .NET System.Data.dll i System.Transactions.dll. Biblioteki DLL zapewniają, że dwufazowe zatwierdzanie jest używane w razie potrzeby w celu zapewnienia niepodzielności. Aby rozpocząć tworzenie aplikacji przy użyciu transakcji Elastic Database, zainstaluj [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) lub nowszą wersję. W przypadku uruchomienia w starszej wersji programu .NET Framework transakcje nie będą poddawane promocji do transakcji rozproszonej i zostanie zgłoszony wyjątek.

Po zakończeniu instalacji można używać interfejsów API transakcji rozproszonych w programie System. Transactions z połączeniami do SQL Database. Jeśli masz istniejące aplikacje usługi MSDTC korzystające z tych interfejsów API, po zainstalowaniu przeglądarki 4.6.1 należy po prostu ponownie skompilować istniejące aplikacje dla programu .NET 4,6. Jeśli projekty są przeznaczone dla platformy .NET 4,6, będą automatycznie używały zaktualizowanych bibliotek DLL z nowej wersji platformy i wywołań interfejsu API transakcji rozproszonych w połączeniu z połączeniami z usługą SQL Database.

Pamiętaj, że transakcje Elastic Database nie wymagają instalowania usługi MSDTC. Zamiast tego transakcje Elastic Database są bezpośrednio zarządzane przez program i w ramach SQL Database. Znacznie upraszcza to scenariusze w chmurze, ponieważ wdrożenie usługi MSDTC nie jest konieczne do korzystania z transakcji rozproszonych z SQL Database. Sekcja 4 wyjaśnia bardziej szczegółowo, jak wdrażać transakcje Elastic Database i wymagane środowisko .NET Framework razem z aplikacjami w chmurze na platformie Azure.

## <a name="development-experience"></a>Środowisko programistyczne

### <a name="multi-database-applications"></a>Aplikacje z obsługą kilku baz danych

Następujący przykładowy kod używa znanego środowiska programistycznego w programie .NET system. Transactions. Klasa TransactionScope tworzy otaczającą transakcję w programie .NET. ("Otoczenia transakcji" to ten, który znajduje się w bieżącym wątku). Wszystkie połączenia otwarte w ramach elementu TransactionScope biorą udział w transakcji. W przypadku korzystania z różnych baz danych transakcja zostanie automatycznie podwyższona do transakcji rozproszonej. Wynik transakcji jest kontrolowany przez ustawienie zakresu do ukończenia, aby wskazać zatwierdzenie.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Aplikacje bazy danych podzielonej na fragmenty

Transakcje elastycznej bazy danych dla SQL Database obsługują również koordynowanie transakcji rozproszonych w przypadku używania metody OpenConnectionForKey biblioteki klienta Elastic Database do otwierania połączeń dla warstwy danych skalowanych w poziomie. Rozważ przypadki, w których należy zagwarantowanie spójności transakcyjnej dla zmian w kilku różnych wartościach klucza fragmentowania. Połączenia z fragmentów hostowania różnych wartości klucza fragmentowania są obsługiwane przez brokera przy użyciu OpenConnectionForKey. W ogólnym przypadku połączenia mogą być różne fragmentów w taki sposób, że zapewnienie transakcyjnych gwarancji wymaga transakcji rozproszonej.
Poniższy przykład kodu ilustruje to podejście. Przyjęto założenie, że zmienna o nazwie shardmap jest używana do reprezentowania mapy fragmentu z biblioteki klienta Elastic Database:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="net-installation-for-azure-cloud-services"></a>Instalacja platformy .NET dla usługi Azure Cloud Services

Platforma Azure oferuje kilka ofert do hostowania aplikacji platformy .NET. Porównanie różnych ofert jest dostępne w ramach [porównania Azure App Service, Cloud Services i Virtual Machines](/azure/architecture/guide/technology-choices/compute-decision-tree). Jeśli system operacyjny gościa oferty jest mniejszy niż .NET 4.6.1 wymagany dla transakcji elastycznych, musisz uaktualnić system operacyjny gościa do 4.6.1.

W przypadku Azure App Service uaktualnienia do systemu operacyjnego gościa nie są obecnie obsługiwane. W przypadku usługi Azure Virtual Machines wystarczy zalogować się do maszyny wirtualnej i uruchomić Instalatora w celu uzyskania najnowszej wersji programu .NET Framework. W przypadku usługi Azure Cloud Services należy dołączyć instalację nowszej wersji platformy .NET do zadań uruchamiania danego wdrożenia. Koncepcje i kroki są udokumentowane w temacie [Instalowanie programu .NET w ramach roli usługi w chmurze](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Należy pamiętać, że Instalator programu .NET 4.6.1 może wymagać więcej tymczasowego magazynu podczas procesu uruchamiania w usługach Azure Cloud Services niż Instalator programu .NET 4,6. Aby zapewnić pomyślną instalację, należy zwiększyć tymczasowy magazyn dla usługi w chmurze platformy Azure w pliku ServiceDefinition. csdef w sekcji LocalResources i ustawieniach środowiska zadania uruchamiania, jak pokazano w następującym przykładzie:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="transactions-across-multiple-servers"></a>Transakcje na wielu serwerach

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Transakcje Elastic Database są obsługiwane na różnych serwerach w Azure SQL Database. W przypadku transakcji między serwerami, serwery uczestniczące muszą zostać wprowadzone w relacji wzajemnej komunikacji. Po ustanowieniu relacji komunikacji każda baza danych na jednym z tych dwóch serwerów może uczestniczyć w transakcjach elastycznych z bazami danych z innego serwera. W przypadku transakcji obejmujących więcej niż dwa serwery, relacja komunikacji musi być stosowane dla dowolnej pary serwerów.

Użyj następujących poleceń cmdlet programu PowerShell do zarządzania relacjami komunikacji między serwerami dla transakcji Elastic Database:

* **New-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby utworzyć nową relację komunikacji między dwoma serwerami w Azure SQL Database. Relacja jest symetryczna, co oznacza, że oba serwery mogą inicjować transakcje z innym serwerem.
* **Get-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby pobrać istniejące relacje komunikacji i ich właściwości.
* **Remove-AzSqlServerCommunicationLink**: Użyj tego polecenia cmdlet, aby usunąć istniejącą relację komunikacji.

## <a name="monitoring-transaction-status"></a>Monitorowanie stanu transakcji

Użyj dynamicznych widoków zarządzania (widoków DMV) w SQL Database, aby monitorować stan i postęp trwających transakcji Elastic Database. Wszystkie widoków DMV związane z transakcjami są istotne dla transakcji rozproszonych w SQL Database. Odpowiednią listę widoków DMV można znaleźć tutaj: [powiązane z transakcją dynamiczne widoki zarządzania i funkcje (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Te widoków DMV są szczególnie przydatne:

* ** \_ \_ aktywne \_ transakcje sys.DM przeładunku**: Wyświetla aktualnie aktywne transakcje i ich stan. Kolumna "jednostka pracy" może identyfikować różne transakcje podrzędne należące do tej samej transakcji rozproszonej. Wszystkie transakcje w ramach tej samej transakcji rozproszonej mają tę samą wartość JEDNOSTKowy. Aby uzyskać więcej informacji, zobacz [dokumentację DMV](https://msdn.microsoft.com/library/ms174302.aspx) .
* ** \_ \_ \_ transakcje bazy danych sys.DM przeładunku**: zawiera dodatkowe informacje o transakcjach, takich jak umieszczanie transakcji w dzienniku. Aby uzyskać więcej informacji, zobacz [dokumentację DMV](https://msdn.microsoft.com/library/ms186957.aspx) .
* ** \_ \_ blokady sys.DM**: zawiera informacje o blokadach, które są obecnie przechowywane przez bieżące transakcje. Aby uzyskać więcej informacji, zobacz [dokumentację DMV](https://msdn.microsoft.com/library/ms190345.aspx) .

## <a name="limitations"></a>Ograniczenia

W SQL Database są obecnie stosowane następujące ograniczenia dotyczące transakcji Elastic Database:

* Obsługiwane są tylko transakcje między bazami danych w SQL Database. Inni dostawcy zasobów (X) i bazy danych platformy [XA](https://en.wikipedia.org/wiki/X/Open_XA) poza programem SQL Database nie mogą uczestniczyć w transakcjach Elastic Database. Oznacza to, że transakcje Elastic Database nie mogą być rozciągane między SQL Serverami lokalnymi i Azure SQL Database. W przypadku transakcji rozproszonych w środowisku lokalnym Kontynuuj korzystanie z usługi MSDTC.
* Obsługiwane są tylko transakcje skoordynowane przez klienta z aplikacji .NET. Obsługa po stronie serwera dla języka T-SQL, taka jak Rozpocznij transakcję rozproszoną, jest planowana, ale nie jest jeszcze dostępna.
* Transakcje w ramach usług WCF nie są obsługiwane. Na przykład masz metodę usługi WCF, która wykonuje transakcję. Załączenie wywołania w ramach zakresu transakcji zakończy się niepowodzeniem jako [System. ServiceModel. ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać odpowiedzi na pytania, skontaktuj się z nami na [stronie pytań firmy&Microsoft dotyczącym SQL Database](https://docs.microsoft.com/answers/topics/azure-sql-database.html). W przypadku żądań funkcji należy je dodać do [forum opinii SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
 