---
title: Różnice między Cloud Services i Service Fabric
description: Omówienie pojęć dotyczących migrowania aplikacji z Cloud Services do Service Fabric.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c98aeaff3ba39a28fad68454d76f6f4d33f44e5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836738"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Dowiedz się więcej o różnicach między Cloud Services i Service Fabric przed migracją aplikacji.
Microsoft Azure Service Fabric to platforma aplikacji w chmurze nowej generacji dla wysoce skalowalnych, wysoce niezawodnych aplikacji rozproszonych. Wprowadzono wiele nowych funkcji tworzenia pakietów aplikacji w chmurze i wdrażania ich oraz zarządzania nimi. 

Jest to przewodnik wprowadzający do migrowania aplikacji z Cloud Services do Service Fabric. Koncentruje się głównie na architekturze i różnicach projektowych między Cloud Services i Service Fabric.

## <a name="applications-and-infrastructure"></a>Aplikacje i infrastruktura
Podstawową różnicą między Cloud Services i Service Fabric jest relacja między maszynami wirtualnymi, obciążeniami i aplikacjami. Obciążenie w tym miejscu jest zdefiniowane jako kod, który można napisać w celu wykonania określonego zadania lub zapewnienia usługi.

* **Cloud Services zawiera informacje o wdrażaniu aplikacji jako maszyn wirtualnych.** Napisany kod jest ściśle połączony z wystąpieniem maszyny wirtualnej, na przykład rolą sieci Web lub procesu roboczego. Aby wdrożyć obciążenie w Cloud Services, należy wdrożyć co najmniej jedno wystąpienie maszyny wirtualnej, które uruchamia obciążenie. Nie ma rozdzielania aplikacji i maszyn wirtualnych, dlatego nie istnieje formalna definicja aplikacji. Aplikacja może być uważana za zestaw wystąpień roli sieć Web lub proces roboczy w ramach wdrożenia Cloud Services lub całego wdrożenia Cloud Services. W tym przykładzie aplikacja jest wyświetlana jako zestaw wystąpień roli.

![Cloud Services aplikacji i topologii][1]

* **Service Fabric zawiera informacje o wdrażaniu aplikacji na istniejących maszynach wirtualnych lub maszynach z systemem Service Fabric w systemie Windows lub Linux.** Zapisane usługi są całkowicie oddzielone od podstawowej infrastruktury, która jest niezależna od platformy aplikacji Service Fabric, dzięki czemu aplikacja może zostać wdrożona w wielu środowiskach. Obciążenie w Service Fabric jest nazywane "usługą", a co najmniej jedna usługa jest pogrupowana w aplikacji, która jest uruchamiana na platformie aplikacji Service Fabric. Wiele aplikacji można wdrożyć w pojedynczym klastrze Service Fabric.

![Service Fabric aplikacji i topologii][2]

Sama Service Fabric to warstwa platformy aplikacji działająca w systemie Windows lub Linux, natomiast Cloud Services jest systemem do wdrażania maszyn wirtualnych zarządzanych przez platformę Azure z dołączonymi obciążeniami.
Model aplikacji Service Fabric ma wiele zalet:

* Czasy szybkiego wdrażania. Tworzenie wystąpień maszyn wirtualnych może być czasochłonne. W Service Fabric maszyny wirtualne są wdrażane tylko raz, aby utworzyć klaster obsługujący Service Fabric platformę aplikacji. Od tego momentu pakiety aplikacji mogą być wdrażane do klastra bardzo szybko.
* Hosting o dużej gęstości. W Cloud Services maszyna wirtualna roli proces roboczy obsługuje jedno obciążenie. W Service Fabric aplikacje są oddzielone od maszyn wirtualnych, na których są uruchamiane, co oznacza, że można wdrożyć dużą liczbę aplikacji na niewielką liczbę maszyn wirtualnych, co może obniżyć całkowity koszt większych wdrożeń.
* Platforma Service Fabric może działać w dowolnym miejscu, w którym znajdują się maszyny z systemem Windows Server lub Linux, niezależnie od tego, czy jest to platforma Azure, czy lokalna. Platforma zapewnia warstwę abstrakcji nad podstawową infrastrukturą, dzięki czemu aplikacja może działać w różnych środowiskach. 
* Rozproszone zarządzanie aplikacjami. Service Fabric to platforma, która nie tylko obsługuje aplikacje rozproszone, ale pomaga również zarządzać cyklem życia niezależnie od hosta maszyny wirtualnej lub cyklu życia maszyny.

## <a name="application-architecture"></a>Architektura aplikacji
Architektura aplikacji Cloud Services obejmuje zazwyczaj wiele zależności usług zewnętrznych, takich jak Service Bus, Azure Table i Blob Storage, SQL, Redis i innych, aby zarządzać stanem i danymi aplikacji oraz komunikacji między rolami sieci Web i procesu roboczego w ramach wdrożenia Cloud Services. Przykład kompletnej aplikacji Cloud Services może wyglądać następująco:  

![Architektura Cloud Services][9]

Aplikacje Service Fabric mogą również korzystać z tych samych usług zewnętrznych w kompletnej aplikacji. Korzystając z tego przykładu Cloud Services architekturze, najprostsza ścieżka migracji z Cloud Services do Service Fabric polega na zastępowaniu tylko wdrożenia Cloud Services z aplikacją Service Fabric, utrzymując ogólną architekturę. Role sieci Web i procesu roboczego można przenieść do Service Fabric usług bezstanowych przy minimalnych zmianach kodu.

![Architektura Service Fabric po prostej migracji][10]

Na tym etapie system powinien kontynuować pracę tak samo jak wcześniej. Korzystając z funkcji stanowych Service Fabric, zewnętrzne magazyny stanu mogą być wewnętrzne jako usługi stanowe, jeśli ma to zastosowanie. Jest to wydajniejsze niż prosta migracja ról Sieć Web i proces roboczy, aby Service Fabric bezstanowe usługi, ponieważ wymaga to zapisania niestandardowych usług, które zapewniają równorzędną funkcjonalność aplikacji jako usługi zewnętrzne. Korzyści wynikające z tego obejmują: 

* Usuwanie zależności zewnętrznych 
* Ujednolicenie modeli wdrożenia, zarządzania i uaktualnienia. 

Przykładową architekturą internalizing te usługi mogą wyglądać następująco:

![Architektura Service Fabric po pełnej migracji][11]

## <a name="communication-and-workflow"></a>Komunikacja i przepływ pracy
Większość aplikacji usług w chmurze składa się z więcej niż jednej warstwy. Podobnie aplikacja Service Fabric składa się z więcej niż jednej usługi (zazwyczaj wiele usług). Dwa popularne modele komunikacji to bezpośrednia komunikacja i przez zewnętrzny magazyn trwały.

### <a name="direct-communication"></a>Bezpośrednia komunikacja
Dzięki bezpośredniej komunikacji warstwy mogą komunikować się bezpośrednio za pośrednictwem punktu końcowego uwidocznionego w poszczególnych warstwach. W środowiskach bezstanowych, takich jak Cloud Services, oznacza to wybranie wystąpienia roli maszyny wirtualnej, losowo lub okrężnej, aby zrównoważyć obciążenie i połączyć się bezpośrednio z punktem końcowym.

![Cloud Services komunikacji bezpośredniej][5]

 Bezpośrednia komunikacja jest powszechnym modelem komunikacji w Service Fabric. Kluczową różnicą między Service Fabric i Cloud Services jest to, że w Cloud Services nawiązywania połączenia z maszyną wirtualną, podczas gdy program Service Fabric nawiązuje połączenie z usługą. Jest to ważna różnica z kilku powodów:

* Usługi w Service Fabric nie są powiązane z maszynami wirtualnymi, które je obsługują; usługi mogą poruszać się w klastrze, a w rzeczywistości są oczekiwać na różne przyczyny: Równoważenie zasobów, przechodzenie w tryb failover, uaktualnienia aplikacji i infrastruktury oraz ograniczenia umieszczania i ładowania. Oznacza to, że adres wystąpienia usługi może się zmienić w dowolnym momencie. 
* Maszyna wirtualna w Service Fabric może obsługiwać wiele usług, z których każdy ma unikatowe punkty końcowe.

Service Fabric zapewnia mechanizm odnajdowania usług nazywany Usługa nazewnictwa, który może służyć do rozpoznawania adresów punktów końcowych usług. 

![Service Fabric komunikacji bezpośredniej][6]

### <a name="queues"></a>Kolejki
Typowy mechanizm komunikacji między warstwami w środowiskach bezstanowych, takich jak Cloud Services, to użycie zewnętrznej kolejki magazynu do trwale przechowywania zadań roboczych z jednej warstwy do innej. Typowym scenariuszem jest warstwa sieci Web, która wysyła zadania do kolejki platformy Azure lub Service Bus gdzie wystąpienia roli procesu roboczego mogą dekolejkować i przetwarzać zadania.

![Komunikacja z kolejką Cloud Services][7]

Ten sam model komunikacji może być używany w Service Fabric. Może to być przydatne w przypadku migrowania istniejącej aplikacji Cloud Services do Service Fabric. 

![Service Fabric komunikacji bezpośredniej][8]

## <a name="parity"></a>Parity
[Cloud Services jest podobna do Service Fabric w stopniu kontroli i łatwość użycia, ale jest teraz starszą wersją usługi i Service Fabric jest zalecana dla nowego rozwoju](/azure/architecture/guide/technology-choices/compute-decision-tree). Poniżej znajduje się porównanie interfejsów API:


| **Interfejs API usługi w chmurze** | **Interfejs API Service Fabric** | **Uwagi** |
| --- | --- | --- |
| RoleInstance. GetId — | FabricRuntime. GetNodeContext. NodeId lub. NodeName | ID jest właściwością nodename |
| RoleInstance. GetFaultDomain | FabricClient. Querymanager. GetNodeList | Filtrowanie w węźle nodename i używanie właściwości FD |
| RoleInstance. GetUpgradeDomain | FabricClient. Querymanager. GetNodeList | Filtrowanie w węźle nodename i używanie właściwości upgrade |
| RoleInstance. GetInstanceEndpoints | FabricRuntime. GetActivationContext lub nazwa (ResolveService) | CodePackageActivationContext, który jest dostarczany zarówno przez FabricRuntime. GetActivationContext, jak i w replikach za pośrednictwem ServiceInitializationParameters. CodePackageActivationContext. Inicjacj |
| RoleEnvironment. GetRoles | FabricClient. Querymanager. GetNodeList | Jeśli chcesz wykonać te same operacje filtrowania według typu, możesz uzyskać listę typów węzłów z manifestu klastra za pomocą FabricClient. Clustermanager. GetClusterManifest i pobrać z niego typy ról/węzłów. |
| RoleEnvironment.GetIsAvailable | Connect-WindowsFabricCluster lub Utwórz element FabricRuntime, który wskazywał określony węzeł | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext. log/temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext. log/temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext. log/temp/Work | * |
| Rola. GetInstances | FabricClient. Querymanager. GetNodeList lub ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime. GetActivationContext lub nazwa (ResolveService) | * |

## <a name="next-steps"></a>Następne kroki
Najprostszą ścieżką migracji z Cloud Services do Service Fabric jest zamienienie tylko wdrożenia Cloud Services z aplikacją Service Fabric, dzięki czemu ogólna architektura aplikacji jest w przybliżeniu taka sama. W poniższym artykule przedstawiono przewodnik ułatwiający konwertowanie roli sieci Web lub procesu roboczego na Service Fabricową usługę bezstanową.

* [Prosta migracja: konwertowanie roli sieci Web lub procesu roboczego na Service Fabricową usługę bezstanową](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png