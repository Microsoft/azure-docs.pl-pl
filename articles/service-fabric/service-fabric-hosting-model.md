---
title: Model hostingu Service Fabric platformy Azure
description: Opisuje relację między replikami (lub wystąpieniami) wdrożonej usługi Service Fabric i procesu hosta usługi.
author: harahma
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: harahma
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f3f6238bb72704d13fef4a7171aeaebee5f9141
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708700"
---
# <a name="azure-service-fabric-hosting-model"></a>Model hostingu Service Fabric platformy Azure
Ten artykuł zawiera omówienie modeli hostingu aplikacji udostępnianych przez usługę Azure Service Fabric i opisuje różnice między **procesem udostępnionym** i modelami **procesów wyłącznych** . Opisano w nim, jak wdrożona aplikacja szuka węzła Service Fabric i relacji między replikami (lub wystąpieniami) usługi i procesem hosta usługi.

Przed kontynuowaniem upewnij się, że rozumiesz różne koncepcje i relacje wyjaśnione w temacie [model a Application w Service Fabric][a1]. 

> [!NOTE]
> W tym artykule, o ile nie określono inaczej jako znaczenia:
>
> - *Replika* odnosi się zarówno do repliki usługi stanowej, jak i wystąpienia usługi bezstanowej.
> - *CodePackage* jest traktowany jako równoważny z procesem *ServiceHost* , który rejestruje wartość *ServiceType*i obsługuje repliki usług tego *programu.*
>

Aby zrozumieć model hostingu, przejdźmy na przykład. Załóżmy, że mamy element *ApplicationType* "mojaapltype", który ma *Typ ServiceType* "serviceservicetype". Element "webservicetype" jest dostarczany przez *servicepackage* "MyServicePackage", który ma *CodePackage* "MyCodePackage". "MyCodePackage" rejestruje *Typ* elementu "serviceservicetype" podczas jego uruchamiania.

Załóżmy, że mamy klaster z trzema węzłami i tworzymy *application* **sieć szkieletową aplikacji:/APP1** typu "mojaapltype". W tej **sieci szkieletowej aplikacji:/APP1**tworzymy usługę Service **Fabric:/APP1/Service** typu "serviceservicetype". Ta usługa ma dwie partycje (na przykład **P1** i **P2**) oraz trzy repliki na partycję. Na poniższym diagramie przedstawiono widok tej aplikacji, gdy zostanie ona zakończona wdrożoną w węźle.


![Diagram pokazujący widok tej aplikacji w miarę kończenia wdrożenia w węźle.][node-view-one]


Service Fabric aktywowano "MyServicePackage", który uruchomił element "MyCodePackage", który obsługuje repliki z obu partycji. Wszystkie węzły w klastrze mają ten sam widok, ponieważ wybrano liczbę replik na partycję równą liczbie węzłów w klastrze. Utwórzmy kolejną usługę, **Sieć szkieletowa:/APP1/ServiceB**w **sieci szkieletowej aplikacji:/APP1**. Ta usługa ma jedną partycję (na przykład **P3**) i trzy repliki na partycję. Na poniższym diagramie przedstawiono nowy widok w węźle:


![Diagram, który pokazuje nowy widok w węźle.][node-view-two]


Service Fabric umieścić nową replikę dla partycji **P3** usługi Service **Fabric:/APP1/ServiceB** w istniejącej aktywacji elementu "MyServicePackage". Znajdź. Utwórz kolejną **sieć szkieletową aplikacji:/APP2** typu "mojaapltype". Wewnątrz **sieci szkieletowej:/APP2**, tworzenie usługi Service **Fabric:/APP2/Service**. Ta usługa ma dwie partycje (**P4** i **P5**) oraz trzy repliki na partycję. Na poniższym diagramie przedstawiono nowy widok węzła:


![Diagram pokazujący nowy widok węzła.][node-view-three]


Service Fabric aktywuje nową kopię "MyServicePackage", która uruchamia nową kopię "MyCodePackage". Repliki z obu partycji usługi Service **Fabric:/APP2/Service-** (**P4** i **P5**) są umieszczane w tej nowej kopii "MyCodePackage".

## <a name="shared-process-model"></a>Model procesu współużytkowanego
W poprzedniej sekcji opisano domyślny model hostingu dostarczany przez Service Fabric, nazywany modelem procesu udostępnionego. W tym modelu dla danej aplikacji uaktywniona jest tylko jedna kopia danego elementu *servicepackage* w węźle (który uruchamia wszystkie zawarte w nim *CodePackages* ). Wszystkie repliki wszystkich usług danego elementu *ServiceType* są umieszczane w *CodePackage* , które rejestrują ten *Typ*. Innymi słowy, wszystkie repliki wszystkich usług w węźle danego elementu *Service* mają ten sam proces.

## <a name="exclusive-process-model"></a>Model procesów wyłącznych
Innym modelem hostingu dostarczonym przez Service Fabric jest model procesu wyłącznego. W tym modelu w danym węźle każda replika jest w osobnym dedykowanym procesie. Service Fabric aktywuje nową kopię *pakietu servicepackage* (która uruchamia wszystkie zawarte w nim *CodePackages* ). Repliki są umieszczane w *CodePackage* , który zarejestrował *Typ* ServiceType usługi, do której należy replika. 

Jeśli używasz Service Fabric w wersji 5,6 lub nowszej, możesz wybrać model procesu wyłącznego w momencie tworzenia usługi (za pomocą [programu PowerShell][p1], [rest][r1]lub [FabricClient][c1]). Określ **Korzystanie** jako "ExclusiveProcess".

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Jeśli masz domyślną usługę w manifeście aplikacji, możesz wybrać model procesu wyłącznego, określając atrybut **Korzystanie** :

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Teraz Utwórzmy kolejną usługę, **Sieć szkieletowa:/APP1/ServiceC**w **sieci szkieletowej aplikacji:/APP1**. Ta usługa ma dwie partycje (na przykład **P6** i **P7**) oraz trzy repliki na partycję. Ustawiasz **Korzystanie** na "ExclusiveProcess". Na poniższym diagramie przedstawiono nowy widok w węźle:


![Diagram widoku węzła wdrożonej aplikacji][node-view-four]


Jak widać, Service Fabric aktywowane dwie nowe kopie "MyServicePackage" (jeden dla każdej repliki z partycji **P6** i **P7**). Service Fabric umieścić każdą replikę w swojej dedykowanej kopii *CodePackage*. W przypadku korzystania z modelu procesu wyłącznego dla danej aplikacji wiele kopii danego elementu *servicepackage* może być aktywnych w węźle. W poprzednim przykładzie trzy kopie "MyServicePackage" są aktywne dla **sieci szkieletowej:/APP1**. Z każdą z tych aktywnych kopii "MyServicePackage" jest skojarzona **ServicePackageActivationId** . Ten identyfikator Określa, że kopia w **sieci szkieletowej aplikacji:/APP1**.

Jeśli używasz tylko modelu procesu udostępnionego dla aplikacji, w węźle istnieje tylko jedna aktywna kopia *pakietu servicepackage* . **ServicePackageActivationId** dla tej aktywacji elementu *servicepackage* jest pustym ciągiem. Dotyczy to na przykład **sieci szkieletowej:/APP2**.

> [!NOTE]
>- Model hostingu procesu udostępnionego odpowiada **Korzystanie** **SharedProcess**. Jest to domyślny model hostingu, a **Korzystanie** nie musi być określony w momencie tworzenia usługi.
>
>- Model hostingu procesu wyłącznego odpowiada **Korzystanie** **ExclusiveProcess**. Aby użyć tego ustawienia, należy określić je jawnie w momencie tworzenia usługi. 
>
>- Aby wyświetlić model hostingu usługi, zbadaj [Opis usługi][p2]i sprawdź wartość **Korzystanie**.
>
>

## <a name="work-with-a-deployed-service-package"></a>Współpraca ze wdrożonym pakietem usługi
Aktywna kopia *servicepackage* w węźle jest określana jako [wdrożony pakiet usługi][p3]. W przypadku korzystania z modelu procesów z wyłącznoocią do tworzenia usług dla danej aplikacji może istnieć wiele wdrożonych pakietów usługi dla tego samego *pakietu servicepackage*. Jeśli wykonujesz operacje specyficzne dla wdrożonego pakietu usługi, należy podać **ServicePackageActivationId** , aby zidentyfikować określony wdrożony pakiet usługi. Podaj na przykład identyfikator w przypadku [zgłaszania kondycji wdrożonego pakietu usługi][p4] lub [ponownego uruchomienia pakietu kodu wdrożonego pakietu usługi][p5].

Aby dowiedzieć się, jak **ServicePackageActivationId** wdrożony pakiet usługi, wykonaj zapytanie o listę [wdrożonych pakietów usług][p3] w węźle. Podczas wykonywania zapytania dotyczącego [wdrożonych typów usług][p6], [wdrożonych replik][p7]i [wdrożonych pakietów kodu][p8] w węźle, wynik zapytania zawiera również **ServicePackageActivationId** zestawu nadrzędny wdrożony pakiet usługi.

> [!NOTE]
>- W ramach modelu hostingu procesu udostępnionego w danym węźle dla danej aplikacji aktywowana jest tylko jedna kopia *pakietu servicepackage* . Ma **ServicePackageActivationId** równy *pusty ciąg*i nie musi być określony podczas wykonywania operacji związanych ze wdrożonym pakietem usługi. 
>
> - W ramach modelu hostingu na wyłączność procesu w danym węźle dla danej aplikacji może być aktywna co najmniej jedna kopia *pakietu servicepackage* . Każda aktywacja ma *niepustą* **ServicePackageActivationId**, określoną podczas wykonywania operacji związanych ze wdrożonym pakietem usługi. 
>
> - W przypadku pominięcia **ServicePackageActivationId** wartość domyślna to *pusty ciąg*. Jeśli istnieje wdrożony pakiet usługi, który został aktywowany w ramach modelu procesu współużytkowanego, operacja zostanie wykonana na tym komputerze. W przeciwnym razie operacja zakończy się niepowodzeniem.
>
> - Nie wykonuj zapytań jednokrotnie i Buforuj **ServicePackageActivationId**. Identyfikator jest generowana dynamicznie i może ulec zmianie z różnych powodów. Przed wykonaniem operacji wymagającej **ServicePackageActivationId**należy najpierw zbadać listę [wdrożonych pakietów usług][p3] w węźle. Następnie użyj **ServicePackageActivationId** z wyniku zapytania, aby wykonać pierwotną operację.
>
>

## <a name="guest-executable-and-container-applications"></a>Plik wykonywalny gościa i aplikacje kontenera
Service Fabric traktuje [plik wykonywalny gościa][a2] i aplikacje [kontenerów][a3] jako usługi bezstanowe, które są samodzielne. Brak środowiska uruchomieniowego Service Fabric w elemencie *ServiceHost* (proces lub kontener). Ponieważ te usługi są samodzielne, liczba replik na *hosta* nie ma zastosowania do tych usług. Najbardziej typową konfiguracją używaną w ramach tych usług jest pojedyncza partycja z [InstanceCount][c2] równa-1 (jedna kopia kodu usługi działająca w każdym węźle klastra). 

Domyślnym **Korzystanie** dla tych usług jest **SharedProcess**, w takim przypadku Service Fabric aktywuje tylko jedną kopię *servicepackage* w węźle dla danej aplikacji.  Oznacza to, że tylko jedna kopia kodu usługi uruchomi węzeł. Jeśli chcesz, aby wiele kopii kodu usługi działało w węźle, określ **Korzystanie** jako **ExclusiveProcess** w momencie tworzenia usługi. Można to zrobić na przykład w przypadku tworzenia wielu usług (*Service1* do *ServiceN*) z *ServiceType* (określonych w elemencie *servicemanifest*) lub w przypadku, gdy usługa jest wielowarstwowa. 

## <a name="change-the-hosting-model-of-an-existing-service"></a>Zmień model hostingu istniejącej usługi
W danym momencie nie można zmienić modelu hostingu istniejącej usługi z procesu udostępnionego na proces wyłącznych (lub odwrotnie).

## <a name="choose-between-the-hosting-models"></a>Wybór między modelami hostingu
Należy oszacować, który model hostingu najlepiej odpowiada Twoim wymaganiom. Model procesu współużytkowanego używa zasobów systemu operacyjnego lepiej, ponieważ duplikowane są mniejsze procesy, a wiele replik w tym samym procesie może współużytkować porty. Jeśli jednak jedna z replik zawiera błąd, gdy musi on zostać wyłączony hosta usługi, ma wpływ na wszystkie inne repliki w tym samym procesie.

 Model procesu wyłącznego zapewnia lepszą izolację, z każdą repliką we własnym procesie. Jeśli jedna z replik ma błąd, nie ma to wpływu na inne repliki. Ten model jest przydatny w przypadkach, w których Udostępnianie portów nie jest obsługiwane przez protokół komunikacyjny. Ułatwia ona stosowanie zarządzania zasobami na poziomie repliki. Jednak proces wyłączny zużywa więcej zasobów systemu operacyjnego, ponieważ duplikuje jeden proces dla każdej repliki w węźle.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Zagadnienia dotyczące modelu procesów i modelu aplikacji
W przypadku większości aplikacji można modelować aplikację w Service Fabric, zachowując jeden *Typ* usługi na *pakiet servicepackage*. 

W niektórych przypadkach Service Fabric również zezwala na więcej niż jedną liczbę *ServiceType* na *servicepackage* (i jeden *CodePackage* może zarejestrować więcej niż jeden *ServiceType*). Poniżej przedstawiono niektóre scenariusze, w których mogą być przydatne te konfiguracje:

- Chcesz zoptymalizować wykorzystanie zasobów przez duplikowanie mniejszej liczby procesów i większą gęstość replik na proces.
- Repliki z różnych *typów ServiceType* muszą udostępniać niektóre typowe dane o wysokim poziomie inicjalizacji lub pamięci.
- Masz bezpłatną ofertę usługi i chcesz wprowadzić limit wykorzystania zasobów, umieszczając wszystkie repliki usługi w tym samym procesie.

Model hostingu na wyłączność procesów nie jest spójny z modelem aplikacji z wieloma *typem* *servicepackage*. Wynika to z faktu, że wiele *typów ServiceType* na *servicepackage* została zaprojektowana w celu zwiększenia współużytkowania zasobów między replikami i zapewnia wyższą gęstość repliki na proces. Model procesu wyłącznego został zaprojektowany w celu osiągnięcia różnych wyników.

Rozważmy przypadek wielu *typów ServiceType* dla *servicepackage*, a inne *CodePackage* zarejestrowali każdy typ *ServiceType*. Załóżmy, że mamy *pakiet servicepackage* "MultiTypeServicePackage", który ma dwie *CodePackages*:

- "MyCodePackageA", który rejestruje *Typ ServiceType* "Moja Service".
- "MyCodePackageB", który rejestruje *Typ ServiceType* "MyServiceTypeB".

Teraz Załóżmy, że tworzymy aplikację, **Sieć szkieletowa:/SpecialApp**. W **sieci szkieletowej:/SpecialApp**tworzymy następujące dwie usługi z modelem procesu wyłącznego:

- Service **Fabric:/SpecialApp/Service** of typu "", z dwiema partycjami (na przykład **P1** i **P2**) i trzema replikami na partycję.
- Service **Fabric:/SpecialApp/ServiceB** typu "MyServiceTypeB" z dwiema partycjami (**P3** i **P4**) i trzema replikami na partycję.

W danym węźle obie usługi mają dwie repliki. Ponieważ do tworzenia usług został użyty model procesów z wyłącznoocią, Service Fabric aktywuje nową kopię "MyServicePackage" dla każdej repliki. Każda aktywacja elementu "MultiTypeServicePackage" powoduje uruchomienie kopii elementu "MyCodePackageA" i "MyCodePackageB". Jednak tylko jeden z MyCodePackageA ' lub MyCodePackageB ' obsługuje replikę, dla której aktywowano "MultiTypeServicePackage". Na poniższym diagramie przedstawiono widok węzła:


![Diagram pokazujący widok węzła.][node-view-five]


W ramach aktywacji elementu "MultiTypeServicePackage" dla repliki partycji **P1** z usługi Service **Fabric:/SpecialApp/Service**. usługa "MyCodePackageA" obsługuje replikę. "MyCodePackageB" jest uruchomiony. Podobnie w przypadku aktywacji elementu "MultiTypeServicePackage" w przypadku repliki partycji **P3** usługi Service **Fabric:/SpecialApp/ServiceB**, "MyCodePackageB" obsługuje replikę. "MyCodePackageA" jest uruchomiony. W związku z tym im większa jest liczba *CodePackages* (rejestrowanie różnych elementów *ServiceType*) na każdy *pakiet servicepackage*, tym wyższe użycie zasobów nadmiarowe. 
 
 Jeśli jednak utworzysz usługi **Fabric:/SpecialApp/Service** i **Fabric:/SpecialApp/ServiceB** przy użyciu modelu procesu współużytkowanego, Service Fabric aktywuje tylko jedną kopię "MultiTypeServicePackage" dla **sieci szkieletowej aplikacji:/SpecialApp**. Element "MyCodePackageA" obsługuje wszystkie repliki dla usługi Service **Fabric:/SpecialApp/Service**. Element "MyCodePackageB" obsługuje wszystkie repliki dla usługi Service **Fabric:/SpecialApp/ServiceB**. Na poniższym diagramie przedstawiono widok węzła w tym ustawieniu: 


![Diagram widoku węzła wdrożonej aplikacji][node-view-six]


W powyższym przykładzie można wziąć pod uwagę, że jeśli "MyCodePackageA" rejestruje zarówno element "MyServiceTypeB", jak i opcję "MyCodePackageB", nie będzie można używać nadmiarowych *CodePackage* . Chociaż jest to poprawne, ten model aplikacji nie jest wyrównany z modelem hostingu na wyłączność procesów. Jeśli celem jest umieszczenie każdej repliki we własnym dedykowanym procesie, nie ma potrzeby rejestrowania obu *typów ServiceType* z tego samego *CodePackage*. Zamiast tego wystarczy umieścić każdy *Typ ServiceType* w osobnym *pakiecie servicepackage*.

### <a name="reliable-services-and-actor-forking-subprocesses"></a>Podprocesy przetworzenia rozwidlenia Reliable Services i aktora

Service Fabric nie obsługuje niezawodnych usług, a następnie niezawodnych aktorów rozwidlenia podprocesów. Przykładem nieobsługiwanego elementu jest [CodePackageActivationContext](/dotnet/api/system.fabric.codepackageactivationcontext?view=azure-dotnet) nie można użyć do zarejestrowania nieobsługiwanego podprocesu, a tokeny anulowania są wysyłane tylko do zarejestrowanych procesów; w wyniku wszystkich rodzajów problemów, takich jak błędy uaktualniania, gdy podprocesy nie są zamykane po odebraniu tokenu anulowania przez proces nadrzędny.

## <a name="next-steps"></a>Następne kroki
Utwórz [pakiet aplikacji][a4] i przygotuj go do wdrożenia.

[Wdrażaj i usuwaj aplikacje][a5]. W tym artykule opisano sposób użycia programu PowerShell do zarządzania wystąpieniami aplikacji.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-guest-executables-introduction.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: /rest/api/servicefabric/sfclient-api-createservice

[c1]: /dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: /dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: /powershell/module/servicefabric/new-servicefabricservice
[p2]: /powershell/module/servicefabric/get-servicefabricservicedescription
[p3]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[p4]: /powershell/module/servicefabric/send-servicefabricdeployedservicepackagehealthreport
[p5]: /powershell/module/servicefabric/restart-servicefabricdeployedcodepackage
[p6]: /powershell/module/servicefabric/get-servicefabricdeployedservicetype
[p7]: /powershell/module/servicefabric/get-servicefabricdeployedreplica
[p8]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
