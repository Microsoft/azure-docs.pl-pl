---
title: Często zadawane pytania dotyczące Microsoft Azure Service Fabric
description: Często zadawane pytania dotyczące Service Fabric, w tym możliwości, przypadków użycia i typowych scenariuszy.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: 1655a8ed03b1f678cc5dba0a165e0bcca1d2517a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292852"
---
# <a name="commonly-asked-service-fabric-questions"></a>Często zadawane pytania dotyczące usługi Service Fabric

Istnieje wiele często zadawanych pytań na temat tego, co Service Fabric może zrobić, i jak powinny być używane. Ten dokument zawiera wiele typowych pytań i odpowiedzi na nie.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Konfigurowanie i zarządzanie klastrami

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Jak mogę wycofać certyfikat mojego Service Fabric klastra?

Wycofanie wszelkich uaktualnień do aplikacji wymaga wykrywania błędów kondycji przed zatwierdzeniem przez Service Fabric kworum klastra zmiany; zatwierdzone zmiany mogą być rzutowane tylko do przodu. W celu odzyskania klastra może być wymagane przeprowadzenie przez specjalistę eskalacji, jeśli wprowadzono niemonitorowaną zmianę certyfikatu.  [Uaktualnienie aplikacji Service Fabric](./service-fabric-application-upgrade.md?branch=master) stosuje [Parametry uaktualnienia aplikacji](./service-fabric-application-upgrade-parameters.md?branch=master)i zapewnia nieprzerwane obietnice uaktualniania.  Zgodnie z naszym zalecanym trybem monitorowania aplikacji, automatyczny postęp przy użyciu domeny aktualizacji jest oparty na testach kondycji, które są przekazywane automatycznie, jeśli aktualizacja usługi domyślnej nie powiedzie się.
 
Jeśli klaster nadal wykorzystuje Właściwość klasycznego odcisku palca certyfikatu w szablonie Menedżer zasobów, zaleca się [zmianę klastra z odcisku palca certyfikatu na nazwę pospolitą](./service-fabric-cluster-change-cert-thumbprint-to-cn.md), aby korzystać z nowoczesnych funkcji zarządzania kluczami tajnymi.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Czy mogę utworzyć klaster, który obejmuje wiele regionów platformy Azure lub własne centra danych?

Tak. 

Podstawowa technologia klastrowania Service Fabric może służyć do łączenia maszyn działających w dowolnym miejscu na świecie, o ile mają one łączność sieciową ze sobą. Jednak Kompilowanie i uruchamianie takiego klastra może być skomplikowane.

Jeśli interesuje Cię ten scenariusz, zachęcamy Cię do skontaktowania Service Fabric się z [listą problemów](https://github.com/azure/service-fabric-issues) w witrynie GitHub, aby uzyskać dodatkowe wskazówki. Zespół Service Fabric pracuje nad zapewnieniem dodatkowego przejrzystości, wskazówek i zaleceń dotyczących tego scenariusza. 

Niektóre zagadnienia, które należy wziąć pod uwagę: 

1. Zasób klastra Service Fabric na platformie Azure jest obecnie regionalny, podobnie jak w przypadku zestawów skalowania maszyn wirtualnych, na których jest oparty klaster. Oznacza to, że w przypadku awarii regionalnej można utracić możliwość zarządzania klastrem za pośrednictwem Azure Resource Manager lub Azure Portal. Może to być spowodowane tym, że klaster jest uruchomiony i będzie można z nim korzystać bezpośrednio. Ponadto platforma Azure obecnie nie oferuje możliwości korzystania z jednej sieci wirtualnej, która jest używana w różnych regionach. Oznacza to, że klaster wieloregionowy na platformie Azure wymaga [publiczne adresy IP dla każdej maszyny wirtualnej w zestawach skalowania maszyn wirtualnych lub w](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) [bramach sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md). Te wybory sieci mają różne wpływ na koszty, wydajność i w pewnym stopniu projekcie aplikacji, dlatego należy zachować ostrożność analizy i planowania przed zarządzeniem takim środowiskiem.
2. Konserwacja, zarządzanie i monitorowanie tych maszyn może stać się skomplikowany, szczególnie w przypadku, gdy są one łączone w różne _typy_ środowisk, takie jak między różnymi dostawcami chmury lub między zasobami lokalnymi i platformą Azure. Należy pamiętać, aby upewnić się, że uaktualnienia, monitorowanie, zarządzanie i Diagnostyka są zrozumiałe dla klastra i aplikacji przed uruchomieniem obciążeń produkcyjnych w takim środowisku. Jeśli masz już doświadczenie w rozwiązywaniu tych problemów na platformie Azure lub w własnych centrach danych, prawdopodobnie te same rozwiązania można zastosować podczas tworzenia lub uruchamiania klastra Service Fabric. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Czy Service Fabric węzły automatycznie odbierają aktualizacje systemu operacyjnego?

Teraz można użyć funkcji [automatycznego aktualizowania obrazu systemu operacyjnego dla zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) .

W przypadku klastrów, które nie są uruchomione na platformie Azure, firma Microsoft [udostępniła aplikację](service-fabric-patch-orchestration-application.md) do poprawiania systemów operacyjnych pod węzłami Service Fabric.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Czy można używać dużych zestawów skalowania maszyn wirtualnych w klastrze SF? 

**Krótka odpowiedź** — nie. 

**Długa odpowiedź** — chociaż duże zestawy skalowania maszyn wirtualnych umożliwiają skalowanie zestawu skalowania maszyn wirtualnych do 1000 wystąpień maszyn wirtualnych, robi to za pomocą grup umieszczania (PGs). Domeny błędów (domenami błędów) i domeny uaktualnienia (do) są spójne tylko w obrębie grupy umieszczania Usługa Service Fabric używa domenami błędów i, aby podejmować decyzje dotyczące umieszczania replik usługi/wystąpień usługi. Ponieważ domenami błędów i rz są porównywalne tylko w obrębie grupy umieszczania, SF nie może go użyć. Na przykład jeśli VM1 w PG1 ma topologię FD = 0 i VM9 w grupy PG2 ma topologię FD = 4, nie oznacza to, że VM1 i VM2 znajdują się w dwóch różnych stojakach sprzętowych, dlatego SF nie może użyć wartości FD w tym przypadku, aby podjąć decyzje dotyczące umieszczania.

Obecnie istnieją inne problemy z dużymi zestawami skalowania maszyn wirtualnych, takie jak brak obsługi równoważenia obciążenia poziomu 4. Aby uzyskać [szczegółowe informacje na temat dużych zestawów skalowania](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) , zobacz.



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Jaki jest minimalny rozmiar klastra Service Fabricowego? Dlaczego nie można go zmniejszyć?

Minimalny obsługiwany rozmiar klastra Service Fabric z uruchomionymi obciążeniami produkcyjnymi to pięć węzłów. W przypadku scenariuszy deweloperskich obsługujemy jeden węzeł (zoptymalizowany pod kątem szybkiego programowania w programie Visual Studio) i pięć węzłów klastra.

Firma Microsoft wymaga, aby klaster produkcyjny miał co najmniej pięć węzłów z następujących trzech powodów:
1. Nawet wtedy, gdy nie są uruchomione żadne usługi użytkownika, klaster Service Fabric uruchamia zestaw stanowych usług systemowych, w tym usługi nazewnictwa i usługi Menedżer trybu failover. Te usługi systemowe są niezbędne, aby klaster pozostawał w działaniu.
2. Zawsze umieszczamy jedną replikę usługi na węzeł, więc rozmiar klastra to górny limit liczby replik, które może mieć usługa (w rzeczywistości partycja).
3. Ponieważ uaktualnienie klastra spowoduje przełączenie co najmniej jednego węzła, chcemy buforować co najmniej jeden węzeł, dlatego chcemy, aby klaster produkcyjny miał co najmniej dwa węzły *oprócz* minimum dla systemu operacyjnego. Minimum od zera to rozmiar kworum usługi systemowej, jak wyjaśniono poniżej.  

Chcemy, aby klaster był dostępny w przypadku równoczesnego uszkodzenia dwóch węzłów. Aby klaster Service Fabric był dostępny, usługi systemowe muszą być dostępne. Stanowe usługi systemowe, takie jak nazwa usługi i Menedżer trybu failover, które śledzą, jakie usługi zostały wdrożone w klastrze i gdzie są obecnie hostowane, zależą od silnej spójności. Ta silna spójność z kolei zależy od możliwości uzyskania *kworum* dla każdej danej aktualizacji stanu tych usług, gdzie kworum reprezentuje ścisłą większość replik (N/2 + 1) dla danej usługi. Z tego względu, jeśli chcemy odporny na równoczesną utratę dwóch węzłów (w ten sposób jednocześnie przełączać dwie repliki usługi systemowej), musimy mieć ClusterSize-QuorumSize >= 2, co wymusza, aby minimalny rozmiar wynosił pięć. Aby to sprawdzić, należy wziąć pod uwagę, że klaster ma N węzłów, a istnieją N replik usługi systemowej — jeden w każdym węźle. Rozmiar kworum usługi systemowej to (N/2 + 1). Powyższa nierówność wygląda jak N-(N/2 + 1) >= 2. Istnieją dwa przypadki, które należy wziąć pod uwagę: gdy N jest parzyste i gdy N jest nieparzysty. Jeśli N jest nawet, należy powiedzieć N = 2 \* m, gdzie m >= 1, nierówność wygląda jak 2 \* m-(2 \* m/2 + 1) >= 2 lub m >= 3. Minimalna wartość dla N to 6 i osiągnięta, gdy m = 3. Z drugiej strony, jeśli N jest nieparzysta, powiedz N = 2 \* m + 1, gdzie m >= 1, nierówność wygląda następująco: 2 \* m + 1-((2 \* m + 1)/2 + 1) >= 2 lub 2 \* m + 1-(m + 1) >= 2 lub m >= 2. Minimalna wartość dla N to 5 i osiągnięta przy m = 2. W związku z tym między wszystkimi wartościami N, które spełniają nierówność ClusterSize-QuorumSize >= 2, minimalna wartość to 5.

Należy pamiętać, że w powyższym argumencie przyjęto, że każdy węzeł ma replikę usługi systemowej, w ten sposób rozmiar kworum jest obliczany na podstawie liczby węzłów w klastrze. Jednak przez zmianę *wartość targetreplicasetsize* rozmiar kworum może być mniejszy niż (N/2 + 1), co może dać wrażenie, że klaster jest mniejszy niż 5 węzłów i nadal ma 2 dodatkowe węzły powyżej rozmiaru kworum. Na przykład w klastrze z 4 węzłami, jeśli ustawimy wartość targetreplicasetsize na 3, rozmiar kworum oparty na wartość targetreplicasetsize to (3/2 + 1) lub 2, dlatego mamy ClusterSize-QuorumSize = 4-2 >= 2. Niemniej jednak firma Microsoft nie może zagwarantować, że usługa systemowa będzie znajdować się na poziomie kworum lub wyższym. w przypadku utraty obu par węzłów może się zdarzyć, że dwa węzły zostały przez nas utracone, a więc usługa systemowa przejdzie do utraty kworum (ma tylko jedną replikę) i stanie się niedostępna.

Korzystając z tego tła, Przeanalizujmy niektóre możliwe konfiguracje klastrów:

**Jeden węzeł**: Ta opcja nie zapewnia wysokiej dostępności, ponieważ utrata jednego węzła z jakiegokolwiek powodu spowoduje utratę całego klastra.

**Dwa węzły**: kworum dla usługi wdrożone w dwóch węzłach (N = 2) jest 2 (2/2 + 1 = 2). W przypadku utraty pojedynczej repliki nie można utworzyć kworum. Ponieważ wykonanie uaktualnienia usługi wymaga tymczasowego przeprowadzenia repliki, nie jest to użyteczna konfiguracja.

**Trzy węzły**: z trzema węzłami (N = 3), wymagania dotyczące tworzenia kworum nadal są dwa węzły (3/2 + 1 = 2). Oznacza to, że można utracić pojedynczy węzeł i nadal utrzymują kworum, ale jednoczesne awarie dwóch węzłów spowoduje utratę kworum usług systemowych i spowoduje, że klaster stanie się niedostępny.

**Cztery węzły**: z czterema węzłami (N = 4) wymagania dotyczące tworzenia kworum to trzy węzły (4/2 + 1 = 3). Oznacza to, że można utracić pojedynczy węzeł i nadal utrzymują kworum, ale jednoczesne awarie dwóch węzłów spowoduje utratę kworum usług systemowych i spowoduje, że klaster stanie się niedostępny.

**Pięć węzłów**: z pięcioma węzłami (N = 5), wymagania dotyczące tworzenia kworum nadal są trzy węzły (5/2 + 1 = 3). Oznacza to, że można utracić dwa węzły w tym samym czasie i nadal obsługiwać kworum dla usług systemowych.

W przypadku obciążeń produkcyjnych musisz być odporne na jednoczesne awarie co najmniej dwóch węzłów (na przykład jeden z nich ze względu na uaktualnienie klastra, jeden z innych przyczyn), dlatego wymagane jest pięć węzłów.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Czy mogę wyłączyć mój klaster w nocy/weekendy, aby zaoszczędzić koszty?

Ogólnie rzecz biorąc — nie. Service Fabric przechowuje stan na lokalnych, tymczasowych dyskach, co oznacza, że jeśli maszyna wirtualna zostanie przeniesiona do innego hosta, dane nie zostaną przesunięte na inne hosty. W normalnej operacji nie jest to problem, ponieważ nowy węzeł jest uaktualniany przez inne węzły. Jeśli jednak zatrzymasz wszystkie węzły i uruchomisz je ponownie później, istnieje znacząca możliwość, że większość węzłów zaczyna się na nowych hostach i nie będzie można odzyskać systemu.

Jeśli chcesz utworzyć klastry do testowania aplikacji przed jej wdrożeniem, zalecamy dynamiczne tworzenie tych klastrów w ramach [potoku ciągłej integracji/ciągłego wdrażania](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Jak mogę uaktualnić mój system operacyjny (na przykład z systemu Windows Server 2012 do systemu Windows Server 2016)?

Mimo że pracujemy nad udoskonalonym doświadczeniem, już dziś użytkownik jest odpowiedzialny za uaktualnienie. Obraz systemu operacyjnego należy uaktualnić na maszynach wirtualnych klastra pojedynczo. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Czy można zaszyfrować dołączone dyski danych w typie węzła klastra (zestaw skalowania maszyn wirtualnych)?
Tak.  Aby uzyskać więcej informacji, zobacz [Tworzenie klastra z dołączonymi dyskami danych](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) i [Azure Disk Encryption dla Virtual Machine Scale Sets](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Czy można używać maszyn wirtualnych o niskim priorytecie w typie węzła klastra (zestaw skalowania maszyn wirtualnych)?
Nie. Maszyny wirtualne o niskim priorytecie nie są obsługiwane. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Jakie są katalogi i procesy, które należy wykluczyć podczas uruchamiania programu antywirusowego w moim klastrze?

| **Wykluczone katalogi oprogramowania antywirusowego** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (z konfiguracji klastra) |
| FabricLogRoot (z konfiguracji klastra) |

| **Wykluczone procesy programu antywirusowego** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |
 
### <a name="how-can-my-application-authenticate-to-key-vault-to-get-secrets"></a>Jak aplikacja może uwierzytelniać się w usłudze Key Vault, aby uzyskać wpisy tajne?
Poniżej przedstawiono sposób, w jaki aplikacja uzyskuje poświadczenia do uwierzytelniania w Key Vault:

A. Podczas tworzenia i pakowania aplikacji można ściągnąć certyfikat do pakietu danych aplikacji SF i użyć go do uwierzytelniania w Key Vault.
B. W przypadku hostów z włączonym zestawem skalowania maszyn wirtualnych można utworzyć prostą SetupEntryPoint programu PowerShell dla aplikacji SF, aby uzyskać [token dostępu z punktu końcowego MSI](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), a następnie [pobrać klucze tajne z Key Vault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Projekt aplikacji

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Jaki jest najlepszy sposób wykonywania zapytań dotyczących danych między partycjami niezawodnej kolekcji?

Niezawodne kolekcje są zwykle [podzielone na partycje](service-fabric-concepts-partitioning.md) , aby umożliwić skalowanie w poziomie w celu zwiększenia wydajności i przepływności. Oznacza to, że stan danej usługi może być rozłożony na dziesiątki lub setki maszyn. Aby wykonać operacje w tym pełnym zestawie danych, masz kilka opcji:

- Utwórz usługę, która będzie wysyłać zapytania do wszystkich partycji innej usługi, aby ściągnąć wymagane dane.
- Utwórz usługę, która może odbierać dane ze wszystkich partycji innej usługi.
- Okresowe wypychanie danych z każdej usługi do magazynu zewnętrznego. To podejście jest odpowiednie tylko wtedy, gdy wykonywane zapytania nie są częścią podstawowej logiki biznesowej, ponieważ dane ze sklepu zewnętrznego będą przestarzałe.
- Alternatywnie można przechowywać dane, które muszą obsługiwać zapytania dla wszystkich rekordów bezpośrednio w magazynie danych, a nie w niezawodnej kolekcji. Eliminuje to problem z nieodświeżonymi danymi, ale nie pozwala na korzystanie z zalet niezawodnych kolekcji.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Jaki jest najlepszy sposób wykonywania zapytań dotyczących danych w moich aktorach?

Aktory są przeznaczone do niezależnych jednostek stanu i obliczeń, dlatego nie zaleca się wykonywania szerokich zapytań dotyczących stanu aktora w czasie wykonywania. Jeśli istnieje potrzeba zapytania w całym zestawie stanu aktora, należy rozważyć następujące kwestie:

- Wymiana usług aktora przy użyciu niezawodnych usług stanowych, dzięki czemu liczba żądań sieciowych zbierających wszystkie dane z liczby aktorów do liczby partycji w usłudze.
- Projektowanie aktorów do okresowego wypychania swojego stanu do magazynu zewnętrznego w celu ułatwienia wykonywania zapytań. Jak wspomniano powyżej, takie podejście jest możliwe tylko wtedy, gdy wykonywane zapytania nie są wymagane do zachowania środowiska uruchomieniowego.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Ile danych można przechowywać w niezawodnej kolekcji?

Niezawodne usługi są zwykle podzielone na partycje, więc ilość przechowywanych danych jest ograniczona tylko przez liczbę maszyn, które znajdują się w klastrze, i ilość pamięci dostępnej na tych komputerach.

Załóżmy na przykład, że masz niezawodną kolekcję w usłudze z 100 partycjami i 3 replikami, przechowując obiekty o średniej wielkości 1 KB. Teraz Załóżmy, że masz 10 klastrów maszynowych z 16gb pamięci na maszynę. W celu uproszczenia i zapewnienia ostrożności należy założyć, że system operacyjny i usługi systemowe, środowisko uruchomieniowe Service Fabric i usługi zużywają 6 GB z tego, pozostawiając 10 GB dostępne na maszynę, 100 czyli dla klastra.

Pamiętaj, że każdy obiekt musi być przechowywany trzy razy (jedna podstawowa i dwie repliki), za mało pamięci dla około 35 000 000 obiektów w kolekcji, gdy działa w pełni pojemności. Zalecamy jednak odporność na równoczesną utratę domeny awarii i domeny uaktualnienia, która reprezentuje około 1/3 pojemności i zmniejsza liczbę do około 23 000 000.

To obliczenie zakłada również, że:

- Dystrybucja danych między partycjami jest w przybliżeniu jednorodna lub że raportowane są metryki obciążenia do Menedżer zasobów klastra. Domyślnie Równoważenie obciążenia Service Fabric jest zależne od liczby replik. W poprzednim przykładzie, który spowodowałoby 10 replik podstawowych i 20 replik pomocniczych w każdym węźle klastra. Jest to dobre rozwiązanie w przypadku obciążenia, które jest równomiernie rozłożone na partycje. Jeśli obciążenie nie jest równe, należy zgłosić obciążenie, tak aby Menedżer zasobów może spakować mniejsze repliki razem i umożliwić większym replikom zużywać więcej pamięci w poszczególnych węzłach.

- Ta niezawodna usługa jest jedynym stanem przechowywania w klastrze. Ponieważ można wdrożyć wiele usług w klastrze, należy zastanowić się nad zasobami, które muszą być uruchomione i zarządzać jego stanem.

- Czy sam klaster nie rośnie ani nie zmniejsza. Jeśli dodasz więcej maszyn, Service Fabric będzie zrównoważyć repliki, aby wykorzystać dodatkową pojemność do momentu przekroczenia liczby partycji w usłudze, ponieważ pojedyncza replika nie może obejmować maszyn. Z drugiej strony, jeśli zmniejszy się rozmiar klastra przez usunięcie maszyn, repliky są pakowane bardziej mocno i mają mniej ogólną pojemność.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Ile danych można przechowywać w aktorze?

Podobnie jak w przypadku niezawodnych usług, ilość danych, które można przechowywać w usłudze aktora, jest ograniczona tylko przez całkowitą ilość miejsca na dysku i ilość dostępnej pamięci w węzłach klastra. Jednak poszczególne aktory są najbardziej efektywne, gdy są używane do hermetyzacji niewielkiej ilości stanu i skojarzonej logiki biznesowej. Ogólnie rzecz biorąc, każdy aktor powinien mieć stan, który jest mierzony w kilobajtach.


### <a name="where-does-azure-service-fabric-resource-provider-store-customer-data"></a>Gdzie usługa Azure Service Fabric Resource Provider przechowuje dane klienta?

Dostawca zasobów usługi Azure Service Fabric nie przenosi ani nie zapisuje danych klienta z regionu, w którym jest wdrożony.


## <a name="other-questions"></a>Inne pytania

### <a name="how-does-service-fabric-relate-to-containers"></a>Jak Service Fabric odnosić się do kontenerów?

Kontenery oferują prosty sposób na spakowanie usług i ich zależności, tak że działają one spójnie we wszystkich środowiskach i mogą działać w sposób izolowany na pojedynczym komputerze. Service Fabric oferuje sposób wdrażania usług i zarządzania nimi, w tym [usług, które zostały spakowane w kontenerze](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Czy planowane jest Service Fabric Open Source?

Firma Microsoft udostępnia części typu "open source" Service Fabric ([Struktura niezawodnych usług](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [niezawodna struktura aktorów](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [biblioteki integracji ASP.NET Core](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)i [interfejs wiersza polecenia Service Fabric](https://github.com/Azure/service-fabric-cli)) w witrynie GitHub oraz akceptują wkłady społeczności do tych projektów. 

[Niedawno ogłoszono](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) , że planujemy otwarcie źródła Service Fabric środowiska uruchomieniowego. W tym momencie mamy [Service Fabric repozytorium](https://github.com/Microsoft/service-fabric/) w witrynie GitHub przy użyciu narzędzi do kompilowania i testowania systemu Linux, co oznacza, że można sklonować repozytorium, kompilować Service Fabric dla systemu Linux, uruchamiać podstawowe testy, otwierać problemy i przesyłać żądania ściągnięcia. Pracujemy nad udostępnieniem środowiska kompilacji systemu Windows, a także z kompletnym środowiskiem CI.

Aby uzyskać szczegółowe informacje na temat ogłaszania, postępuj zgodnie z [Service Fabric blogu](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [podstawowych pojęciach](service-fabric-technical-overview.md) i [najlepszych rozwiązaniach](service-fabric-best-practices-overview.md) Service Fabric
