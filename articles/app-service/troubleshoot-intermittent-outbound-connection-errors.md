---
title: Rozwiązywanie problemów sporadyczne błędy połączenia wychodzącego w Azure App Service
description: Rozwiązywanie sporadycznych błędów połączeń i związanych z nimi problemów z wydajnością w Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80367553"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Rozwiązywanie problemów sporadyczne błędy połączenia wychodzącego w Azure App Service

Ten artykuł pomaga w rozwiązywaniu problemów z sporadycznymi błędami połączeń i powiązanymi problemami z wydajnością w [Azure App Service](https://docs.microsoft.com/azure/app-service/overview). Ten temat zawiera więcej informacji na temat metod rozwiązywania problemów z portami translacji sieci adresów źródłowych (NAT). Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, skontaktuj się z ekspertami platformy Azure w [witrynie MSDN Azure i na forach Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.

## <a name="symptoms"></a>Objawy

Aplikacje i funkcje hostowane w usłudze Azure App Service mogą mieć co najmniej jeden z następujących objawów:

* Wolne czasy odpowiedzi dla wszystkich lub niektórych wystąpień w planie usług.
* Sporadyczne 5xx lub **złe błędy bramy**
* Komunikaty o błędach limitu czasu
* Nie można nawiązać połączenia z zewnętrznymi punktami końcowymi (na przykład SQLDB, Service Fabric, innymi usługami App Services itp.)

## <a name="cause"></a>Przyczyna

Główną przyczyną tych objawów jest fakt, że wystąpienie aplikacji nie może otworzyć nowego połączenia z zewnętrznym punktem końcowym, ponieważ osiągnęło jeden z następujących limitów:

* Połączenia TCP: istnieje limit liczby połączeń wychodzących, które mogą zostać wykonane. Jest to związane z rozmiarem używanego procesu roboczego.
* Porty protokołu Resources: jak opisano w [połączeniach wychodzących na platformie Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections), platforma Azure korzysta z translatora adresów sieciowych (Resources) i Load Balancer (nieujawnione klientom) do komunikowania się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP. Każde wystąpienie w usłudze Azure App Service początkowo uzyskało wstępnie przydzieloną liczbę portów przydziałów adresów sieciowych **128** . Ten limit wpływa na otwieranie połączeń z tym samym hostem i kombinacją portów. Jeśli aplikacja tworzy połączenia z kombinacją kombinacji adresów i portów, nie będziesz używać portów. Porty przyłączone do nich są używane, gdy powtórzone wywołania tego samego adresu i kombinacji portów. Gdy port zostanie wystawiony, port będzie dostępny do ponownego użycia w razie potrzeby. Moduł równoważenia obciążenia sieci platformy Azure ponownie przejmuje port z zamkniętych połączeń dopiero po upływie 4 minut.

Gdy aplikacje lub funkcje szybko otwierają nowe połączenie, mogą szybko wyczerpać wstępnie przydzieloną liczbę portów 128. Są one następnie blokowane do momentu udostępnienia nowego portu protokołu reportowego, poprzez dynamiczne przydzielanie dodatkowych portów lub ponowne użycie odnoszącego się do niego portu. Aplikacje lub funkcje, które są blokowane z powodu niemożności utworzenia nowych połączeń, zostaną uruchomione co najmniej jednego problemu opisanego w sekcji **objawy** tego artykułu.

## <a name="avoiding-the-problem"></a>Unikanie problemu

Unikanie problemu z portem podłączania adresów sieciowych oznacza uniknięcie tworzenia nowych połączeń wielokrotnie na tym samym hoście i porcie.

Ogólne strategie rozwiązywania problemów z wyczerpaniem portów [przydziałów](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) adresów sieciowych zostały omówione w sekcji rozwiązanie problemu dotyczącej **połączeń wychodzących dokumentacji platformy Azure** . Z tych strategii dotyczą aplikacje i funkcje hostowane w usłudze Azure App Service.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modyfikowanie aplikacji tak, aby używała puli połączeń

* W celu buforowania połączeń HTTP Przejrzyj [pulę połączeń HTTP z HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Aby uzyskać informacje dotyczące SQL Server puli połączeń, zapoznaj się z tematem [pule połączeń SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).
* W celu zaimplementowania puli przy użyciu aplikacji Entity Framework, przejrzyj [pulę DbContext](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Poniżej znajduje się kolekcja linków do implementowania puli połączeń przy użyciu innego stosu rozwiązań.

#### <a name="node"></a>Węzeł

Domyślnie połączenia dla NodeJS nie są aktywne. Poniżej znajdują się popularne bazy danych i pakiety dla puli połączeń, które zawierają przykłady sposobu ich implementacji.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

Utrzymywanie HTTP-Alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Dokumentacja środowiska Node. js v 13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Poniżej przedstawiono popularne biblioteki używane do tworzenia puli połączeń JDBC, które zawierają przykłady sposobu ich implementacji: JDBC pule połączeń.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Buforowanie połączeń HTTP

* [Zarządzanie połączeniami Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [PoolingHttpClientConnectionManager klasy](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Chociaż język PHP nie obsługuje puli połączeń, można spróbować użyć trwałych połączeń bazy danych z serwerem zaplecza.
 
* Serwer MySQL

   * [Mysqli połączenia](https://www.php.net/manual/mysqli.quickstart.connections.php) dla nowszych wersji
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) dla starszych wersji języka PHP

* Inne źródła danych

   * [Zarządzanie połączeniami PHP](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (Uwaga: SQLAlchemy może być używana z innymi bazami danych poza serwerem MicrosoftSQL)
* [Utrzymywanie http](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive jest automatyczne w przypadku używania sesji [Session-Objects](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)).

W przypadku innych środowisk zapoznaj się z tematem dostawca lub dokumenty specyficzne dla sterownika dotyczące implementowania puli połączeń w aplikacjach.

### <a name="modify-the-application-to-reuse-connections"></a>Modyfikowanie aplikacji w celu ponownego użycia połączeń

*  Aby uzyskać dodatkowe wskaźniki i przykłady dotyczące zarządzania połączeniami w usłudze Azure Functions, zapoznaj się z tematem [Zarządzanie połączeniami w Azure Functions](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modyfikowanie aplikacji tak, aby była używana mniej agresywna logika ponawiania

* Aby uzyskać dodatkowe wskazówki i przykłady, przejrzyj [wzorzec ponawiania prób](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Aby zresetować limit czasu bezczynności dla ruchu wychodzącego, użyj utrzymywania aktywności

* W celu zaimplementowania nieaktywności dla aplikacji node. js Sprawdź, [czy aplikacja mojego węzła sprawia nadmierne wywołania wychodzące](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Dodatkowe wskazówki dotyczące App Service:

* [Test obciążenia](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) powinien symulować rzeczywiste dane świata w stałej szybkości żywienia. Testowanie aplikacji i funkcji w ramach rzeczywistego środowiska światowego może identyfikować i rozwiązywać problemy z wyczerpaniem portów podkluczy adresów w czasie.
* Upewnij się, że usługi zaplecza mogą szybko zwracać odpowiedzi. Rozwiązywanie problemów z wydajnością w usłudze Azure SQL Database można znaleźć w tematach [Rozwiązywanie problemów z wydajnością Azure SQL Database z Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow).
* Skaluj w poziomie plan App Service do większej liczby wystąpień. Aby uzyskać więcej informacji na temat skalowania, zobacz [skalowanie aplikacji w Azure App Service](https://docs.microsoft.com/azure/app-service/manage-scale-up). Każde wystąpienie procesu roboczego w planie usługi App Service ma przydzieloną liczbę portów przydziałów adresów sieciowych. W przypadku rozłożenia użycia w większej liczbie wystąpień można uzyskać dostęp do portów dla danego wystąpienia, poniżej zalecanego limitu 100 połączeń wychodzących, na unikatowy zdalny punkt końcowy.
* Rozważ przeniesienie do [App Service Environment (ASE)](https://docs.microsoft.com/azure/app-service/environment/using-an-ase), w którym ma zostać przydzielony pojedynczy wychodzący adres IP, a limity dla połączeń i portów podłączania adresów sieciowych są znacznie wyższe.

Uniknięcie limitów wychodzącego protokołu TCP jest łatwiejsze do rozwiązania, ponieważ limity są ustawiane przez rozmiar procesu roboczego. Limity można zobaczyć w obszarze [limity liczbowe między maszynami wirtualnymi piaskownic — połączenia TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nazwa limitu|Opis|Małe (a1)|Średni (a2)|Duże (A3)|Warstwa izolowana (ASE)|
|---|---|---|---|---|---|
|Połączenia|Liczba połączeń między całym maszyną wirtualną|1920|3968|8064|16 000|

Aby uniknąć wychodzących limitów TCP, można zwiększyć rozmiar procesów roboczych lub skalować w poziomie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Znajomość dwóch rodzajów limitów połączeń wychodzących i zawartości aplikacji powinna ułatwić rozwiązywanie problemów. Jeśli wiesz, że aplikacja wykonuje wiele wywołań do tego samego konta magazynu, może się zdarzyć, że zostanie osiągnięty limit przestrzeni adresów sieciowych. Jeśli aplikacja tworzy wspaniałe wiele wywołań punktów końcowych przez Internet, podejrzewasz, że zbliżasz się do limitu maszyn wirtualnych.

Jeśli nie wiesz, że zachowanie aplikacji jest wystarczające, aby szybko ustalić przyczynę problemu, w App Service mogą być dostępne pewne narzędzia i techniki.

### <a name="find-snat-port-allocation-information"></a>Znajdowanie informacji o alokacji portu dla translatora adresów sieciowych

Możesz użyć [diagnostyki App Service](https://docs.microsoft.com/azure/app-service/overview-diagnostics) , aby znaleźć informacje o alokacji portu dla interfejsu podrzędnego i obserwować metrykę alokacji portów dla App Service lokacji. Aby znaleźć informacje o alokacji portów dla translatora adresów sieciowych, wykonaj następujące czynności:

1. Aby uzyskać dostęp do diagnostyki App Service, przejdź do aplikacji internetowej App Service lub App Service Environment w [Azure Portal](https://portal.azure.com/). W lewym okienku nawigacji wybierz pozycję **Diagnozuj i rozwiąż problemy**.
2. Wybierz kategorię dostępności i wydajności
3. Na liście dostępnych kafelków w kategorii wybierz pozycję kafelek wyczerpania portów. Celem jest zachowanie go poniżej 128.
Jeśli to konieczne, można nadal otworzyć bilet pomocy technicznej, a inżynier pomocy technicznej otrzyma metrykę od zaplecza.

Należy pamiętać, że ponieważ użycie portu przez nie jest dostępne jako Metryka, nie jest możliwe automatyczne skalowanie na podstawie użycia portów protokołu podrzędnego lub skonfigurowanie automatycznego skalowania na podstawie metryki alokacji portów protokołu źródłowego.

### <a name="tcp-connections-and-snat-ports"></a>Połączenia TCP i porty podłączania adresów sieciowych

Połączenia TCP i porty podłączania adresów sieciowych nie są bezpośrednio powiązane. Detektor użycia połączeń TCP znajduje się w bloku diagnozowanie i rozwiązywanie problemów w dowolnej lokacji App Service. Wyszukaj frazę "połączenia TCP", aby je znaleźć.

* Porty ze strumieniem adresów sieciowych są używane tylko w przypadku przepływów sieci zewnętrznych, a całkowita liczba połączeń TCP obejmuje połączenia lokalnego sprzężenia zwrotnego.
* Port IP może być współużytkowany przez różne przepływy, jeśli przepływy różnią się w zależności od protokołu lub portu. Metryka połączeń TCP zlicza każde połączenie TCP.
* Limit połączeń TCP odbywa się na poziomie wystąpienia procesu roboczego. Wychodzące Równoważenie obciążenia sieciowego platformy Azure nie używa metryki połączeń TCP do ograniczania portów.
* Limity połączeń TCP są opisane w obszarze [limity liczbowe między maszynami wirtualnymi piaskownicy — połączenia TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nazwa limitu|Opis|Małe (a1)|Średni (a2)|Duże (A3)|Warstwa izolowana (ASE)|
|---|---|---|---|---|---|
|Połączenia|Liczba połączeń między całym maszyną wirtualną|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>Zadania WebJob i połączenia bazy danych
 
Jeśli porty podłączane są wyczerpane, gdzie Zadania WebJob nie mogą nawiązać połączenia z bazą danych SQL Azure, nie ma żadnej metryki, aby pokazać, ile połączeń jest otwartych przez poszczególne procesy aplikacji sieci Web. Aby znaleźć problematyczne zadanie WebJob, Przenieś kilka zadań WebJobs do innego planu App Service, aby sprawdzić, czy sytuacja się poprawi, czy problem pozostanie w jednym z planów. Powtarzaj proces do momentu znalezienia problematycznego Zadania WebJob.

### <a name="using-snat-ports-sooner"></a>Korzystanie z portów adresów sieciowych wcześniej

Nie można zmienić żadnych ustawień platformy Azure w celu szybszego wydawania używanych portów ze współdziałaniem, ponieważ wszystkie porty podstawki adresów sieciowych zostaną wydane zgodnie z poniższymi warunkami, a zachowanie jest zaprojektowane.
 
* Jeśli serwer lub klient wysyła FINACK, port dla tego elementu [zostanie wydaną](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) po 240 sekundach.
* Jeśli jest widoczny parametr RST, port elementu źródłowego zostanie wystawiony po 15 sekundach.
* Po osiągnięciu limitu czasu bezczynności port zostanie wystawiony.
 
## <a name="additional-information"></a>Dodatkowe informacje

* [Za pomocą App Service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Rozwiązywanie problemów z wydajnością wolniejszych aplikacji w Azure App Service](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
