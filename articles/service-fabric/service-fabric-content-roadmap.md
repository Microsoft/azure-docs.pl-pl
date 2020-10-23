---
title: Dowiedz się więcej o usłudze Azure Service Fabric
description: Zapoznaj się z podstawowymi pojęciami i Głównymi obszarami Service Fabric platformy Azure. Zawiera dodatkowe Omówienie Service Fabric i sposobu tworzenia mikrousług.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 6cbc99b56df18448add47a70b42742aa8dabbeb5
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461502"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Czy chcesz dowiedzieć się więcej o Service Fabric?
Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych i niezawodnych mikrousług oraz zarządzanie nimi.  Service Fabric ma jednak duże powierzchnie, ale istnieje wiele informacji.  Ten artykuł zawiera streszczenie Service Fabric i zawiera opis podstawowych pojęć, modeli programowania, cyklu życia aplikacji, testowania, klastrów i monitorowania kondycji. Zapoznaj się z [omówieniem](service-fabric-overview.md) i [co to są mikrousługi?](service-fabric-overview-microservices.md) , aby zapoznać się z wprowadzeniem i jak Service Fabric może służyć do tworzenia mikrousług. Ten artykuł nie zawiera obszernej listy zawartości, ale łączy się z artykułami dotyczącymi przeglądu i uruchamiania dla każdego obszaru Service Fabric. 

## <a name="core-concepts"></a>Podstawowe pojęcia
[Service Fabric terminologia](service-fabric-technical-overview.md), [model aplikacji](service-fabric-application-model.md)i [obsługiwane modele programowania](service-fabric-choose-framework.md) zawierają więcej pojęć i opisów, ale poniżej przedstawiono podstawowe informacje.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Czas projektowania: typ usługi, pakiet usługi i manifest, typ aplikacji, pakiet aplikacji i manifest
Typ usługi to nazwa/wersja przypisana do pakietów kodu usługi, pakietów danych i pakietów konfiguracyjnych. Jest to zdefiniowane w pliku ServiceManifest.xml. Typ usługi składa się z ustawień kodu wykonywalnego i konfiguracji usługi, które są ładowane w czasie wykonywania, oraz danych statycznych, które są używane przez usługę.

Pakiet usługi to katalog dysku zawierający plik ServiceManifest.xml typu usługi, który odwołuje się do kodu, danych statycznych i pakietów konfiguracyjnych dla typu usługi. Na przykład pakiet usługi może odwoływać się do kodu, danych statycznych i pakietów konfiguracyjnych tworzących usługę bazy danych.

Typ aplikacji to nazwa/wersja przypisana do kolekcji typów usług. Jest to zdefiniowane w pliku ApplicationManifest.xml.

![Service Fabric typów aplikacji i typów usług][cluster-imagestore-apptypes]

Pakiet aplikacji jest katalogiem dysku, który zawiera plik ApplicationManifest.xml typu aplikacji, który odwołuje się do pakietów usługi dla każdego typu usługi tworzącego typ aplikacji. Na przykład pakiet aplikacji typu aplikacja poczty e-mail może zawierać odwołania do pakietu usługi kolejki, pakietu usługi frontonu i pakietu usługi bazy danych.  

Pliki w katalogu pakietu aplikacji są kopiowane do magazynu obrazów klastra Service Fabric. Następnie można utworzyć nazwaną aplikację z tego typu aplikacji, która następnie jest uruchamiana w klastrze. Po utworzeniu nazwanej aplikacji można utworzyć nazwę usługi na podstawie jednego z typów usług typu aplikacji. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Czas wykonywania: klastry i węzły, nazwane aplikacje, nazwane usługi, partycje i repliki
[Klaster Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Klastry mogą obejmować nawet tysiące maszyn.

Maszyna lub maszyna wirtualna, która jest częścią klastra, jest nazywana węzłem. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania. Każda maszyna lub maszyna wirtualna ma uruchomioną funkcję autostartu systemu Windows, `FabricHost.exe` która jest uruchamiana po rozruchu, a następnie uruchamia dwa pliki wykonywalne: `Fabric.exe` i `FabricGateway.exe` . Te dwa pliki wykonywalne składają się na węzeł. W przypadku scenariuszy programistycznych lub testowych można hostować wiele węzłów na jednej maszynie lub maszynie wirtualnej, uruchamiając wiele wystąpień `Fabric.exe` i `FabricGateway.exe` .

Nazwana aplikacja to kolekcja nazwanych usług, która wykonuje określoną funkcję lub funkcje. Usługa wykonuje pełną i autonomiczną funkcję (można uruchamiać i uruchamiać niezależnie od innych usług) i składa się z kodu, konfiguracji i danych. Po skopiowaniu pakietu aplikacji do magazynu obrazów można utworzyć wystąpienie aplikacji w klastrze, określając typ aplikacji pakietu aplikacji (przy użyciu jego nazwy/wersji). Każde wystąpienie typu aplikacji ma przypisaną nazwę identyfikatora URI, która wygląda jak *Sieć szkieletowa:/MyNamedApp*. W ramach klastra można utworzyć wiele nazwanych aplikacji z jednego typu aplikacji. Można również tworzyć nazwane aplikacje z różnych typów aplikacji. Każda nazwana aplikacja jest zarządzana i niezależnie od wersji.

Po utworzeniu nazwanej aplikacji można utworzyć wystąpienie jednego z jego typów usług (nazwanej usługi) w klastrze, określając typ usługi (przy użyciu jej nazwy/wersji). Każde wystąpienie typu usługi ma przypisaną nazwę identyfikatora URI w zakresie identyfikatora URI aplikacji. Jeśli na przykład utworzysz nazwę usługi "Moja baza danych" w nazwie "MyNamedApp", identyfikator URI wygląda następująco: *Fabric:/MyNamedApp/webdatabase*. W ramach nazwanej aplikacji można utworzyć co najmniej jedną nazwę usługi. Każda nazwana usługa może mieć własny schemat partycji oraz liczbę wystąpień/replik. 

Istnieją dwa typy usług: bezstanowe i stanowe. Usługi bezstanowe nie przechowują stanu w ramach usługi. Usługi bezstanowe nie mają trwałego magazynu ani nie przechowują trwałego stanu w ramach zewnętrznej usługi magazynu, takiej jak Azure Storage, Azure SQL Database lub Azure Cosmos DB. Usługa stanowa zapisuje stan w ramach usługi i używa niezawodnych kolekcji lub Reliable Actors modeli programowania, aby zarządzać stanem. 

Podczas tworzenia nazwanej usługi należy określić schemat partycji. Usługi z dużymi ilościami danych dzielą dane między partycjami. Każda partycja jest odpowiedzialna za część pełnego stanu usługi, która jest rozłożona w węzłach klastra.  

Na poniższym diagramie przedstawiono relację między aplikacjami i wystąpieniami usług, partycjami i replikami.

![Partycje i repliki w ramach usługi][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partycjonowanie, skalowanie i dostępność
[Partycjonowanie](service-fabric-concepts-partitioning.md) nie jest unikatowe dla Service Fabric. Dobrze znana postać partycjonowania to Partycjonowanie danych lub fragmentowania. Usługi stanowe z dużymi ilościami danych dzielą dane między partycjami. Każda partycja jest odpowiedzialna za część pełnego stanu usługi. 

Repliki każdej partycji są rozmieszczane w węzłach klastra, co pozwala na [skalowanie](service-fabric-concepts-scalability.md)stanu nazwanej usługi. W miarę wzrostu ilości danych partycje rosną i Service Fabric zrównoważą partycje w różnych węzłach w celu efektywnego wykorzystania zasobów sprzętowych. W przypadku dodania nowych węzłów do klastra program Service Fabric będzie ponownie zrównoważyć repliki partycji w większej liczbie węzłów. Ogólna wydajność aplikacji zwiększa się i rywalizacja o zmniejszenie ilości pamięci. Jeśli węzły w klastrze nie są efektywnie używane, można zmniejszyć liczbę węzłów w klastrze. Service Fabric ponownie zrównoważy repliki partycji na zmniejszonej liczbie węzłów, aby lepiej wykorzystać sprzęt w każdym węźle.

W ramach partycji usługi bezstanowe mają wystąpienia, podczas gdy stanowe nazwy usług mają repliki. Zwykle usługi bezstanowe mają już jedną partycję, ponieważ nie mają wewnętrznego stanu, chociaż [istnieją wyjątki](https://docs.microsoft.com/azure/service-fabric/service-fabric-concepts-partitioning#partition-service-fabric-stateless-services). Wystąpienia partycji zapewniają [dostępność](service-fabric-availability-services.md). Jeśli jedno wystąpienie nie powiedzie się, inne wystąpienia nadal działają normalnie, a następnie Service Fabric tworzy nowe wystąpienie. Stanowe nazwy usług utrzymują swój stan w replikach, a każda partycja ma swój własny zestaw replik. Operacje odczytu i zapisu są wykonywane w jednej replice (nazywanej głównym). Zmiany stanu z operacji zapisu są replikowane do wielu innych replik (nazywanych Active pomocniczymi). Jeśli replika nie powiedzie się, Service Fabric kompiluje nową replikę z istniejących replik.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Mikrousługi stanowe i bezstanowe dla usługi Service Fabric
Usługa Service Fabric umożliwia tworzenie aplikacji składających się z mikrousług lub kontenerów. Mikrousługi bezstanowe (na przykład bramy protokołów i internetowe serwery proxy) nie utrzymują modyfikowalnego stanu poza żądaniem i odpowiedzią serwera. Przykładem usługi bezstanowej są procesy robocze usług Azure Cloud Services. Mikrousługi stanowe (na przykład konta użytkowników, bazy danych, urządzenia, koszyki zakupów i kolejki) utrzymują modyfikowalny, autorytatywny stan poza żądaniem i odpowiedzią. Współczesne aplikacje internetowe łączą w sobie mikrousługi stanowe i bezstanowe. 

Kluczową różnicą Service Fabric jest jej silny skoncentrowano się na tworzeniu usług stanowych, z [wbudowanymi modelami programowania](service-fabric-choose-framework.md) lub z kontenerami usługi stanowe. [Scenariusze zastosowania](service-fabric-application-scenarios.md) opisują sytuacje, w których używane są usługi stanowe.

Dlaczego mikrousługi stanowe mają być bezstanowe? Istnieją dwa główne przyczyny:

* Możesz tworzyć usługi przetwarzania transakcji online o wysokiej przepływności, małych opóźnieniach i odpornych na uszkodzenia, zachowując kod i dane na tym samym komputerze. Przykłady to interaktywne witryny sklepu, wyszukiwania, systemy Internet rzeczy (IoT), systemy handlowe, przetwarzanie kart kredytowych i systemy wykrywania oszustw oraz osobiste Zarządzanie rekordami.
* Można uprościć projektowanie aplikacji. Mikrousługi stanowe usuwają potrzebę dodatkowych kolejek i pamięci podręcznych, które są tradycyjnie wymagane w celu rozwiązania wymagań dotyczących dostępności i opóźnienia w bezstanowej aplikacji. Usługi stanowe są naturalną wysoką dostępnością i małymi opóźnieniami, co zmniejsza liczbę przenoszonych części do zarządzania w aplikacji jako całości.

## <a name="supported-programming-models"></a>Obsługiwane modele programowania
Service Fabric oferuje wiele sposobów zapisywania usług i zarządzania nimi. Usługi mogą korzystać z interfejsów API Service Fabric, aby w pełni korzystać z funkcji platformy i struktur aplikacji. Usługi mogą być również dowolnym skompilowanym programem wykonywalnym zapisanym w dowolnym języku i hostowanym w klastrze Service Fabric. Aby uzyskać więcej informacji, zobacz [obsługiwane modele programowania](service-fabric-choose-framework.md).

### <a name="containers"></a>Kontenery
Domyślnie program Service Fabric wdraża i aktywuje usługi jako procesy. Service Fabric można również wdrożyć usługi w [kontenerach](service-fabric-containers-overview.md). Ważne, można mieszać usługi w ramach procesów i usług w kontenerach w tej samej aplikacji. Service Fabric obsługuje wdrażanie kontenerów systemu Linux i kontenerów Windows w systemie Windows Server 2016. W kontenerach można wdrażać istniejące aplikacje, usługi bezstanowe lub usługi stanowe. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) jest lekkim środowiskiem do pisania usług, które integrują się z platformą Service Fabric i korzystają z pełnego zestawu funkcji platformy. Reliable Services mogą być bezstanowe (podobne do większości platform usług, takich jak serwery sieci Web lub role procesów roboczych na platformie Azure Cloud Services), gdzie stan jest utrwalany w rozwiązaniu zewnętrznym, takim jak Azure DB lub Azure Table Storage. Reliable Services może być również stanowa, gdzie stan jest utrwalany bezpośrednio w samej usłudze przy użyciu niezawodnych kolekcji. Stan jest [wysoce dostępny](service-fabric-availability-services.md) za pomocą replikacji i dystrybuowany za pomocą [partycjonowania](service-fabric-concepts-partitioning.md), które są zarządzane automatycznie przez Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
W oparciu o Reliable Services, [niezawodna struktura aktora](service-fabric-reliable-actors-introduction.md) jest strukturą aplikacji, która implementuje wzorzec aktora wirtualnego na podstawie wzorca projektu aktora. Niezawodna struktura aktora używa niezależnych jednostek obliczeniowych i stanu z jednowątkowym wykonywaniem nazywanym aktorami. Niezawodna struktura aktora zapewnia wbudowaną komunikację dla aktorów i wstępnie ustawionych ustawień trwałości stanu i skalowalnych w poziomie.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric integruje się z [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) jako model programowania pierwszej klasy do tworzenia aplikacji sieci Web i interfejsu API.  ASP.NET Core można używać na dwa różne sposoby w Service Fabric:

- Hostowane jako plik wykonywalny gościa. Jest to używane przede wszystkim do uruchamiania istniejących aplikacji ASP.NET Core na Service Fabric bez zmian w kodzie.
- Uruchom wewnątrz niezawodnej usługi. Pozwala to na lepszą integrację z Service Fabric środowiska uruchomieniowego i umożliwia stanowe usługi ASP.NET Core.

### <a name="guest-executables"></a>Pliki wykonywalne gościa
[Plik wykonywalny gościa](service-fabric-guest-executables-introduction.md) to istniejący, dowolny plik wykonywalny (zapisany w dowolnym języku) hostowany w klastrze Service Fabric obok innych usług. Pliki wykonywalne gościa nie są integrowane bezpośrednio z interfejsami API Service Fabric. Jednak nadal korzystają z funkcji oferowanych przez platformę, takich jak niestandardowe kondycje i raportowanie obciążeń i odnajdowanie usług przez wywoływanie interfejsów API REST. Mają także pełną pomoc techniczną dotyczącą cyklu życia aplikacji. 

## <a name="application-lifecycle"></a>Cykl życia aplikacji
Podobnie jak w przypadku innych platform, aplikacja na Service Fabric zwykle przechodzi przez następujące fazy: projektowanie, programowanie, testowanie, wdrażanie, uaktualnianie, konserwacja i usuwanie. Service Fabric zapewnia obsługę pierwszej klasy dla pełnego cyklu życia aplikacji w chmurze, od projektowania przez wdrażanie, codzienne zarządzanie i konserwację do ostatecznego wycofania. Model usług umożliwia wielu różnym rolom uczestnictwo niezależnie w cyklu życia aplikacji. [Cykl życia aplikacji Service Fabric](service-fabric-application-lifecycle.md) zawiera omówienie interfejsów API i ich użycia przez różne role w fazie cyklu życia aplikacji Service Fabric. 

Cały cykl życia aplikacji można zarządzać przy użyciu poleceń [cmdlet programu PowerShell](/powershell/module/ServiceFabric/), [poleceń interfejsu wiersza polecenia](service-fabric-sfctl.md), interfejsów API języka [C#](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [interfejsów API języka Java](/java/api/overview/azure/servicefabric)i [interfejsów API REST](/rest/api/servicefabric/). Możesz również skonfigurować potoki ciągłej integracji/ciągłego wdrażania przy użyciu narzędzi, takich jak [Azure Pipelines](./service-fabric-tutorial-deploy-app-with-cicd-vsts.md) lub [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster).

## <a name="test-applications-and-services"></a>Testowanie aplikacji i usług
Aby stworzyć prawdziwą usługę w skali chmury, należy sprawdzić, czy aplikacje i usługi mogą wytrzymywać rzeczywiste błędy. Usługa błędów analizy jest przeznaczona do testowania usług, które są oparte na Service Fabric. Za pomocą [usługi błędów analizy](service-fabric-testability-overview.md)można wywołać znaczące błędy i uruchamiać kompletne scenariusze testów dla aplikacji. Te usterki i scenariusze wykonują działania oraz weryfikują liczne Stany i przejścia, które usługa będzie w całym okresie istnienia, w sposób kontrolowany, bezpieczny i spójny.

[Akcje](service-fabric-testability-actions.md) są przeznaczone dla usługi do testowania przy użyciu pojedynczych błędów. Deweloperzy usług mogą używać ich jako bloków konstrukcyjnych do pisania skomplikowanych scenariuszy. Przykłady symulowanych błędów:

* Uruchom ponownie węzeł, aby symulować dowolną liczbę sytuacji, w których maszyna lub maszyna wirtualna jest uruchamiana ponownym uruchomieniu.
* Przenieś replikę usługi stanowej w celu zasymulowania równoważenia obciążenia, pracy w trybie failover lub uaktualnienia aplikacji.
* Wywołaj utratę kworum w usłudze stanowej, aby utworzyć sytuację, w której nie można wykonać operacji zapisu, ponieważ nie ma wystarczającej liczby replik "kopia zapasowa" lub "pomocniczych", aby akceptować nowe dane.
* Wywołaj utratę danych w usłudze stanowej, aby utworzyć sytuację, w której cały stan w pamięci zostanie całkowicie wyczyszczony.

[Scenariusze](service-fabric-testability-scenarios.md) to złożone operacje składające się z co najmniej jednej akcji. Usługa analizy błędów oferuje dwa wbudowane scenariusze:

* [Scenariusz chaos](service-fabric-controlled-chaos.md)— symuluje ciągłość, przeplatane błędy (zarówno bezpieczne, jak i nieprolongaty) w całym klastrze przez dłuższy czas.
* [Scenariusz trybu failover](service-fabric-testability-scenarios.md#failover-test)— wersja scenariusza testowego chaos, który jest przeznaczony dla określonej partycji usługi, pozostawiając inne nienaruszone usługi.

## <a name="clusters"></a>Klastry
[Klaster Service Fabric](service-fabric-deploy-anywhere.md) jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Klastry mogą obejmować nawet tysiące maszyn. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem klastra. Każdy węzeł ma przypisaną nazwę węzła (ciąg). Węzły mają swoje właściwości — na przykład właściwości dotyczące umieszczania. Każda maszyna lub maszyna wirtualna ma usługę Autostart, `FabricHost.exe` która zaczyna działać po rozruchu, a następnie uruchamia dwa pliki wykonywalne: Fabric.exe i FabricGateway.exe. Te dwa pliki wykonywalne składają się na węzeł. Scenariusze testowania umożliwiają hostowanie wielu węzłów na pojedynczej maszynie lub maszynie wirtualnej przez uruchomienie wielu wystąpień `Fabric.exe` i `FabricGateway.exe` .

Klastry Service Fabric można tworzyć na maszynach wirtualnych lub fizycznych z systemem Windows Server lub Linux. Można wdrażać i uruchamiać aplikacje Service Fabric w dowolnym środowisku, w którym istnieje zestaw połączonych komputerów z systemem Windows Server lub Linux: lokalnie, na Microsoft Azure lub w dowolnym dostawcy chmury.

### <a name="clusters-on-azure"></a>Klastry na platformie Azure
Uruchamianie Service Fabric klastrów na platformie Azure zapewnia integrację z innymi funkcjami i usługami platformy Azure, dzięki czemu operacje i zarządzanie klastrem są łatwiejsze i bardziej niezawodne. Klaster jest zasobem Azure Resource Manager, dzięki czemu można modelować klastry podobne do innych zasobów na platformie Azure. Menedżer zasobów umożliwia również łatwe zarządzanie wszystkimi zasobami używanymi przez klaster jako pojedynczą jednostką. Klastry na platformie Azure są zintegrowane z usługą Azure Diagnostics i dziennikami Azure Monitor. Typy węzłów klastra to [zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/index.yml), więc funkcja skalowania automatycznego jest wbudowana.

Możesz utworzyć klaster na platformie Azure za pośrednictwem [Azure Portal](service-fabric-cluster-creation-via-portal.md), z [szablonu](service-fabric-cluster-creation-via-arm.md)lub z [programu Visual Studio](./service-fabric-cluster-creation-via-arm.md).

Service Fabric w systemie Linux umożliwia tworzenie i wdrażanie wysoce skalowalnych aplikacji o wysokiej dostępności w systemie Linux, a także zarządzanie nimi, tak jak w przypadku systemu Windows. Struktury Service Fabric (Reliable Services i Reliable Actors) są dostępne w języku Java w systemie Linux, a także w języku C# (.NET Core). Można również tworzyć [usługi wykonywalne gościa](service-fabric-guest-executables-introduction.md) przy użyciu dowolnego języka lub struktury. Obsługiwane jest również organizowanie kontenerów platformy Docker. Kontenery platformy Docker mogą uruchamiać pliki wykonywalne gościa lub natywne usługi Service Fabric, które używają struktur Service Fabric. Aby uzyskać więcej informacji, przeczytaj informacje o [Service Fabric w systemie Linux](service-fabric-deploy-anywhere.md).

Istnieją pewne funkcje, które są obsługiwane w systemie Windows, ale nie w systemie Linux. Aby dowiedzieć się więcej, Przeczytaj [różnice między Service Fabric w systemie Linux i Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Klastry autonomiczne
Service Fabric udostępnia pakiet instalacyjny, który umożliwia tworzenie autonomicznych klastrów Service Fabric lokalnie lub u dowolnego dostawcy chmury. Klastry autonomiczne umożliwiają hostowanie klastra w dowolnym miejscu. Jeśli dane podlegają zgodności lub ograniczeniom wykonawczym lub chcesz zachować lokalne dane, możesz hostować własny klaster i aplikacje. Aplikacje Service Fabric mogą działać w wielu środowiskach hostingu bez zmian, dzięki czemu Wiedza o tworzeniu aplikacji jest przenoszone z jednego środowiska hostingu do innego. 

[Tworzenie pierwszego autonomicznego klastra usługi Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Klastry autonomiczne systemu Linux nie są jeszcze obsługiwane.

### <a name="cluster-security"></a>Zabezpieczenia klastra
Klastry muszą być zabezpieczone, aby uniemożliwić nieautoryzowanym użytkownikom łączenie się z klastrem, zwłaszcza gdy ma uruchomione na nim obciążenia produkcyjne. Chociaż istnieje możliwość utworzenia niezabezpieczonego klastra, dzięki temu anonimowi użytkownicy mogą się z nim łączyć, jeśli punkty końcowe zarządzania są widoczne dla publicznego Internetu. Nie jest możliwe późniejsze włączenie zabezpieczeń w niezabezpieczonym klastrze: zabezpieczenia klastra są włączane podczas tworzenia klastra.

Scenariusze zabezpieczeń klastra są następujące:
* Zabezpieczenia między węzłami
* Zabezpieczenia między klientem a węzłem
* Kontrola dostępu oparta na rolach (RBAC)

Aby uzyskać więcej informacji, przeczytaj temat [Zabezpieczanie klastra](service-fabric-cluster-security.md).

### <a name="scaling"></a>Skalowanie
W przypadku dodania nowych węzłów do klastra program Service Fabric ponownie zrównoważy repliki partycji i wystąpienia w większej liczbie węzłów. Ogólna wydajność aplikacji zwiększa się i rywalizacja o zmniejszenie ilości pamięci. Jeśli węzły w klastrze nie są efektywnie używane, można zmniejszyć liczbę węzłów w klastrze. Service Fabric ponownie zrównoważy repliki partycji i wystąpienia na zmniejszonej liczbie węzłów, aby lepiej wykorzystać sprzęt w każdym węźle. Klastry można skalować na platformie Azure [ręcznie](service-fabric-cluster-scale-in-out.md) lub [programowo](service-fabric-cluster-programmatic-scaling.md). Klastry autonomiczne mogą być skalowane [ręcznie](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Uaktualnienia klastra
Okresowo są wydawane nowe wersje środowiska uruchomieniowego Service Fabric. Przeprowadzaj środowisko uruchomieniowe lub Sieć szkieletowa, uaktualnienia w klastrze, aby zawsze działać [obsługiwana wersja](service-fabric-support.md). Oprócz uaktualnień sieci szkieletowej można także aktualizować konfigurację klastra, na przykład certyfikaty lub porty aplikacji.

Klaster Service Fabric jest posiadanym zasobem, ale jest częścią zarządzaną przez firmę Microsoft. Firma Microsoft jest odpowiedzialna za stosowanie poprawek do podstawowego systemu operacyjnego i przeprowadzanie uaktualnień sieci szkieletowej w klastrze. Można ustawić, aby klaster otrzymywał automatyczne uaktualnienia sieci szkieletowej, podczas gdy firma Microsoft wyprowadzi nową wersję, lub wybrać wybraną obsługiwaną wersję sieci szkieletowej. Uaktualnienia sieci szkieletowej i konfiguracji można ustawić za pomocą Azure Portal lub Menedżer zasobów. Aby uzyskać więcej informacji, przeczytaj artykuł [Uaktualnij klaster Service Fabric](service-fabric-cluster-upgrade.md). 

Autonomiczny klaster jest zasobem, który jesteś całkowicie własnym. Użytkownik jest odpowiedzialny za stosowanie poprawek do podstawowego systemu operacyjnego i Inicjowanie uaktualnień sieci szkieletowej. Jeśli klaster może nawiązać połączenie z usługą [https://www.microsoft.com/download](https://www.microsoft.com/download) , można ustawić, aby klaster automatycznie pobierał i udostępniał nowy pakiet Service Fabric środowiska uruchomieniowego. Następnie można zainicjować uaktualnienie. Jeśli klaster nie może uzyskać dostępu [https://www.microsoft.com/download](https://www.microsoft.com/download) , możesz ręcznie pobrać nowy pakiet środowiska uruchomieniowego z maszyny podłączonej do Internetu, a następnie zainicjować uaktualnienie. Aby uzyskać więcej informacji, przeczytaj temat [uaktualnianie autonomicznego klastra Service Fabric](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitorowanie kondycji
W Service Fabric wprowadzono [model kondycji](service-fabric-health-introduction.md) umożliwiający Flagowanie klastrów w złej kondycji i aplikacji na określonych jednostkach (takich jak węzły klastra i repliki usług). Model kondycji używa raportów kondycji (składniki systemowe i alarmy). Celem jest łatwe i Szybkie diagnozowanie i naprawa. Moduły zapisujące usługi muszą myśleć o kondycji i sposobach [projektowania raportów o kondycji](service-fabric-report-health.md#design-health-reporting). Każdy warunek, który może mieć wpływ na kondycję, powinien być raportowany w szczególności, jeśli może pomóc w oflagowaniu problemów blisko poziomu głównego. Informacje o kondycji mogą zaoszczędzić czas i wysiłku związane z debugowaniem i badaniem, gdy usługa jest uruchomiona i działa na dużą skalę w środowisku produkcyjnym.

Monitorowanie Service Fabric raporty określiło warunki zainteresowania. Składają raporty dotyczące tych warunków w zależności od ich widoku lokalnego. [Magazyn kondycji](service-fabric-health-introduction.md#health-store) agreguje dane o kondycji wysyłane przez wszystkich raportów, aby określić, czy jednostki są w dobrej kondycji. Model ma być bogaty, elastyczny i łatwy w użyciu. Jakość raportów o kondycji określa dokładność widoku kondycji klastra. Fałszywie dodatnie, które źle wyświetlają problemy w złej kondycji, mogą mieć negatywny wpływ na uaktualnienia lub inne usługi korzystające z danych o kondycji. Przykładami takich usług są naprawy usługi i mechanizmy powiadamiania o alertach. W związku z tym niektóre zagadnienia są potrzebne do dostarczania raportów, które przechwytują warunki zainteresowania w najlepszym możliwym przypadku.

Raportowanie można wykonać z:
* Monitorowana replika usługi Service Fabric lub wystąpienie.
* Wewnętrzne alarmy wdrożone jako usługa Service Fabric (na przykład Service Fabric usługa bezstanowa, która monitoruje warunki i raporty problemów). Licznik Alarms można wdrożyć na wszystkich węzłach lub z koligacją dla monitorowanej usługi.
* Wewnętrzne alarmy, które działają w węzłach Service Fabric, ale nie są implementowane jako usługi Service Fabric.
* Zewnętrzne liczniki alarmowe, które sonduje zasób spoza klastra Service Fabric (na przykład usługa monitorowania, taka jak Gomez).

Z pola Service Fabric składniki raportów kondycji wszystkich jednostek w klastrze. [Raporty kondycji systemu](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) umożliwiają wgląd w funkcje klastra i aplikacji oraz Flagowanie problemów z kondycją. W przypadku aplikacji i usług raporty kondycji systemu sprawdzają, czy jednostki są zaimplementowane i działają prawidłowo z perspektywy środowiska uruchomieniowego Service Fabric. Raporty nie zapewniają monitorowania kondycji logiki biznesowej usługi lub wykrywają procesy, które przestały odpowiadać. Aby dodać informacje o kondycji specyficzne dla logiki usługi, [Zaimplementuj niestandardowe Raportowanie kondycji](service-fabric-report-health.md) w usługach.

Service Fabric zapewnia wiele sposobów [wyświetlania raportów kondycji](service-fabric-view-entities-aggregated-health.md) zagregowanych w magazynie kondycji:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) lub inne narzędzia do wizualizacji.
* Zapytania dotyczące kondycji (za pomocą [programu PowerShell](/powershell/module/ServiceFabric/), [interfejsu wiersza polecenia](service-fabric-sfctl.md), interfejsów API języka [C# FabricClient](/dotnet/api/system.fabric.fabricclient.healthclient) oraz [interfejsów API języka Java FabricClient](/java/api/system.fabric)i [interfejsów API REST](/rest/api/servicefabric)).
* Ogólne zapytania, które zwracają listę jednostek, które mają kondycję jako jedną z właściwości (za pomocą programu PowerShell, interfejsu wiersza polecenia, interfejsów API lub REST).

## <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka
[Monitorowanie i Diagnostyka](service-fabric-diagnostics-overview.md) mają kluczowe znaczenie dla opracowywania, testowania i wdrażania aplikacji i usług w dowolnym środowisku. Service Fabric rozwiązania działają najlepiej, gdy planujesz i zaimplementuję monitorowanie i diagnostykę, która pomaga zapewnić, że aplikacje i usługi działają zgodnie z oczekiwaniami w lokalnym środowisku programistycznym lub w produkcji.

Główne cele monitorowania i diagnostyki są następujące:

- Wykrywanie i diagnozowanie problemów ze sprzętem i infrastrukturą
- Wykrywanie problemów z oprogramowaniem i aplikacjami, zmniejszanie przestoju usługi
- Informacje o zużyciu zasobów i podejmowaniu decyzji dotyczących operacji
- Optymalizowanie wydajności aplikacji, usług i infrastruktury
- Generowanie szczegółowych informacji o firmie i identyfikowanie obszarów poprawy
 
Ogólny przepływ pracy monitorowania i diagnostyki składa się z trzech kroków:

1. Generowanie zdarzeń: dotyczy to zdarzeń (dzienników, śladów, zdarzeń niestandardowych) na poziomie infrastruktury (klastra), platformy i aplikacji/usługi
2. Agregacja zdarzeń: wygenerowane zdarzenia muszą być zbierane i agregowane, aby można je było wyświetlić
3. Analiza: zdarzenia muszą być wizualizowane i dostępne w niektórych formatach, aby umożliwić analizę i wyświetlanie w razie potrzeby

Dostępnych jest wiele produktów obejmujących te trzy obszary i można wybrać różne technologie dla każdej z nich. Aby uzyskać więcej informacji, przeczytaj temat [monitorowanie i Diagnostyka dla Service Fabric platformy Azure](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć [klaster na platformie Azure](service-fabric-cluster-creation-via-portal.md) lub [autonomiczny klaster w systemie Windows](service-fabric-cluster-creation-for-windows-server.md).
* Spróbuj utworzyć usługę za pomocą usług [Reliable Services](service-fabric-reliable-services-quick-start.md) lub modelu programowania [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Dowiedz się, jak [przeprowadzić migrację z Cloud Services](service-fabric-cloud-services-migration-differences.md).
* Dowiedz się [, jak monitorować i diagnozować usługi](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Dowiedz się, jak [testować aplikacje i usługi](service-fabric-testability-overview.md).
* Dowiedz się [, jak zarządzać zasobami klastra i organizować](service-fabric-cluster-resource-manager-introduction.md)je.
* Zapoznaj się z [przykładami Service Fabric](/samples/browse/?products=azure).
* Dowiedz się więcej o [opcjach pomocy technicznej Service Fabric](service-fabric-support.md).
* Przeczytaj [Blog zespołu](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) , aby zapoznać się z artykułami i ogłoszeniami.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png