---
title: Monitorowanie kondycji w Service Fabric
description: Wprowadzenie do modelu monitorowania kondycji usługi Azure Service Fabric, który umożliwia monitorowanie klastra i jego aplikacji i usług.
ms.topic: conceptual
ms.date: 2/28/2018
ms.openlocfilehash: a1c545048739182e3baba3e3d94da1accca227d1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627419"
---
# <a name="introduction-to-service-fabric-health-monitoring"></a>Wprowadzenie do monitorowania kondycji usługi Service Fabric
Na platformie Azure Service Fabric wprowadzono model kondycji, który zapewnia rozbudowane, elastyczne i rozszerzalne oceny kondycji oraz raportowanie. Model umożliwia monitorowanie stanu klastra i usług działających w czasie niemal w czasie rzeczywistym. Możesz łatwo uzyskać informacje o kondycji i rozwiązać potencjalne problemy, zanim staną się one kaskadowe i powodują ogromne przestoje. W typowym modelu usługi wysyłają raporty na podstawie widoków lokalnych, a informacje te są agregowane w celu zapewnienia ogólnego widoku poziomu klastra.

Składniki Service Fabric korzystają z tego bogatego modelu kondycji, aby zgłosić swój bieżący stan. Za pomocą tego samego mechanizmu można raportować kondycję aplikacji. Jeśli zainwestowano w Raportowanie kondycji o wysokiej jakości, które przechwytuje warunki niestandardowe, możesz łatwiej wykrywać i rozwiązywać problemy z uruchomioną aplikacją.

> [!NOTE]
> Rozpocząłmy podsystem kondycji, aby sprostać potrzebom monitorowanych uaktualnień. Service Fabric udostępnia monitorowane uaktualnienia aplikacji i klastra zapewniające pełną dostępność, brak przestojów i nie tylko w przypadku braku interwencji użytkownika. Aby osiągnąć te cele, uaktualnienie sprawdza kondycję opartą na skonfigurowanych zasadach uaktualniania. Uaktualnienie można wykonać tylko wtedy, gdy kondycja uwzględnia odpowiednie progi. W przeciwnym razie uaktualnienie jest automatycznie wycofywane lub wstrzymane, aby umożliwić administratorom możliwość rozwiązania problemów. Aby dowiedzieć się więcej o uaktualnieniach aplikacji, zobacz [ten artykuł](service-fabric-application-upgrade.md).
> 
> 

## <a name="health-store"></a>Magazyn kondycji
Magazyn kondycji przechowuje informacje dotyczące kondycji jednostek w klastrze w celu łatwego pobierania i oceny. Jest ona zaimplementowana jako Service Fabric utrwalonej usługi stanowej w celu zapewnienia wysokiej dostępności i skalowalności. Magazyn kondycji jest częścią **sieci szkieletowej:/aplikacji systemowej** i jest dostępny, gdy klaster jest uruchomiony.

## <a name="health-entities-and-hierarchy"></a>Jednostki i hierarchia kondycji
Jednostki kondycji są zorganizowane w hierarchii logicznej, która przechwytuje interakcje i zależności między różnymi jednostkami. Magazyn kondycji automatycznie kompiluje jednostki kondycji i hierarchię na podstawie raportów otrzymanych od składników Service Fabric.

Jednostki kondycji odzwierciedlają jednostki Service Fabric. (Na przykład **Jednostka aplikacji kondycji** pasuje do wystąpienia aplikacji wdrożonego w klastrze, a **Jednostka węzła kondycji** dopasowuje węzeł klastra Service Fabric). Hierarchia kondycji przechwytuje interakcje jednostek systemowych i jest podstawą dla zaawansowanej oceny kondycji. Informacje o najważniejszych pojęciach dotyczących Service Fabric można znaleźć w [Service Fabric omówienie techniczne](service-fabric-technical-overview.md). Aby uzyskać więcej informacji na temat aplikacji, zobacz [Service Fabric model aplikacji](service-fabric-application-model.md).

Jednostki kondycji i hierarchia umożliwiają efektywne zgłaszanie, debugowanie i monitorowanie klastra oraz aplikacji. Model kondycji zapewnia dokładną, *szczegółową* reprezentację kondycji wielu elementów w klastrze.

![Jednostki kondycji.][1]
Jednostki kondycji zorganizowane w hierarchii na podstawie relacji nadrzędny-podrzędny.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Jednostki kondycji są następujące:

* **Klaster**. Reprezentuje kondycję klastra Service Fabricowego. Raporty kondycji klastra opisują warunki, które mają wpływ na cały klaster. Te warunki mają wpływ na wiele jednostek w klastrze lub w samym klastrze. W oparciu o warunek raport nie może zawęzić problemu do co najmniej jednego elementu podrzędnego w złej kondycji. Przykłady obejmują mózg dzielenia klastra ze względu na partycjonowanie sieci lub problemy z komunikacją.
* **Węzeł**. Reprezentuje kondycję Service Fabric węzła. Raporty kondycji węzła opisują warunki, które mają wpływ na funkcjonalność węzła. Zwykle mają wpływ na wszystkie wdrożone jednostki. Przykłady obejmują węzeł poza miejscem na dysku (lub innymi właściwościami całego komputera, takimi jak pamięć, połączenia) i kiedy węzeł nie działa. Jednostka węzła jest identyfikowana przez nazwę węzła (ciąg).
* **Aplikacja**. Reprezentuje kondycję wystąpienia aplikacji działającego w klastrze. Raporty kondycji aplikacji opisują warunki, które mają wpływ na ogólną kondycję aplikacji. Nie można ich zawęzić do poszczególnych elementów podrzędnych (usług lub wdrożonych aplikacji). Przykłady obejmują kompleksowe interakcje między różnymi usługami w aplikacji. Jednostka aplikacji jest identyfikowana przy użyciu nazwy aplikacji (URI).
* **Usługa**. Reprezentuje kondycję usługi działającej w klastrze. Raporty kondycji usługi opisują warunki, które mają wpływ na ogólną kondycję usługi. Program reporter nie może zawęzić problemu do partycji lub repliki w złej kondycji. Przykłady obejmują konfigurację usługi (np. port lub zewnętrzny udział plików), która powoduje problemy dla wszystkich partycji. Jednostka usługi jest identyfikowana przy użyciu nazwy usługi (URI).
* **Partycja**. Reprezentuje kondycję partycji usługi. Raporty kondycji partycji opisują warunki, które mają wpływ na cały zestaw replik. Przykłady obejmują, kiedy liczba replik jest mniejsza niż liczba docelowa i kiedy partycja jest w utracie kworum. Jednostka partycji jest identyfikowana przez identyfikator partycji (GUID).
* **Replika**. Reprezentuje prawidłowość repliki usługi stanowej lub wystąpienia usługi bezstanowej. Replika jest najmniejszą jednostką, która może być raportowana przez licznik i składniki systemowe dla aplikacji. W przypadku usług stanowych przykłady obejmują replikę podstawową, która nie może replikować operacji do serwerów pomocniczych i spowalniać replikację. Ponadto wystąpienie bezstanowe może zgłaszać czas braku zasobów lub problemy z łącznością. Jednostka repliki jest identyfikowana przy użyciu identyfikatora partycji (GUID) i repliki lub identyfikatora wystąpienia (Long).
* **DeployedApplication**. Reprezentuje kondycję *aplikacji uruchomionej w węźle*. Wdrożone raporty kondycji aplikacji opisują warunki specyficzne dla aplikacji w węźle, którego nie można zawęzić do pakietów usług wdrożonych w tym samym węźle. Przykłady obejmują błędy, gdy nie można pobrać pakietu aplikacji w tym węźle i problemy z konfigurowaniem podmiotów zabezpieczeń aplikacji w węźle. Wdrożona aplikacja jest identyfikowana przez nazwę aplikacji (URI) i nazwę węzła (ciąg).
* **DeployedServicePackage**. Reprezentuje kondycję pakietu usługi działającego w węźle w klastrze. Opisano w nim warunki dotyczące pakietu usługi, które nie mają wpływu na inne pakiety usług w tym samym węźle dla tej samej aplikacji. Przykłady obejmują pakiet kodu w pakiecie usługi, którego nie można uruchomić, oraz pakiet konfiguracyjny, którego nie można odczytać. Wdrożony pakiet usługi jest identyfikowany przez nazwę aplikacji (URI), nazwę węzła (ciąg), nazwę manifestu usługi (ciąg) i identyfikator aktywacji pakietu usługi (ciąg).

Stopień szczegółowości modelu kondycji ułatwia wykrywanie i rozwiązywanie problemów. Jeśli na przykład usługa nie odpowiada, możliwe jest zgłoszenie, że wystąpienie aplikacji jest w złej kondycji. Raportowanie na tym poziomie nie jest jednak idealne, ponieważ problem może nie wpływać na wszystkie usługi w ramach tej aplikacji. Raport powinien zostać zastosowany do usługi złej kondycji lub do określonej partycji podrzędnej, jeśli więcej informacji wskazuje na tę partycję. Dane są automatycznie wyświetlane przez hierarchię, a partycja w złej kondycji jest widoczna na poziomach usług i aplikacji. Ta agregacja ułatwia szybkie lokalizowanie i rozwiązywanie głównych przyczyn problemów.

Hierarchia kondycji składa się z relacji nadrzędny-podrzędny. Klaster składa się z węzłów i aplikacji. Aplikacje mają usługi i wdrożone aplikacje. Wdrożone aplikacje mają wdrożone pakiety usług. Usługi mają partycje, a każda partycja zawiera co najmniej jedną replikę. Istnieje specjalna relacja między węzłami i wdrożonymi jednostkami. Węzeł w złej kondycji, który jest raportowany przez składnik systemu, Menedżer trybu failover usługi, ma wpływ na wdrożone aplikacje, pakiety usług i repliki wdrożone na tym komputerze.

Hierarchia kondycji reprezentuje najnowszy stan systemu w oparciu o najnowsze raporty dotyczące kondycji, czyli niemal informacje w czasie rzeczywistym.
Wewnętrzne i zewnętrzne alarmy mogą raportować te same jednostki na podstawie logiki specyficznej dla aplikacji lub niestandardowych warunków monitorowania. Raporty użytkowników współistnieją z raportami systemowymi.

Zaplanuj inwestowanie w sposób zgłaszania i reagowania na kondycję podczas projektowania dużej usługi w chmurze. Te inwestycje z góry ułatwiają debugowanie, monitorowanie i obsługiwanie usługi.

## <a name="health-states"></a>Stany kondycji
Service Fabric używa trzech stanów kondycji, aby określić, czy jednostka jest w dobrej kondycji: OK, ostrzeżenie i błąd. Wszystkie raporty wysyłane do magazynu kondycji muszą określać jeden z tych stanów. Wynik oceny kondycji jest jednym z tych stanów.

Możliwe są następujące [Stany kondycji](/dotnet/api/system.fabric.health.healthstate) :

* **OK**. Jednostka jest w dobrej kondycji. Nie ma żadnych znanych problemów dotyczących IT lub jego elementów podrzędnych (jeśli ma zastosowanie).
* **Ostrzeżenie**. Jednostka ma pewne problemy, ale nadal może działać poprawnie. Na przykład występują opóźnienia, ale nie powodują jeszcze żadnych problemów funkcjonalnych. W niektórych przypadkach warunek ostrzegawczy może zostać naprawiony bez interwencji zewnętrznego. W takich przypadkach raporty kondycji zgłaszają świadomość i zapewniają wgląd w to, co się dzieje. W innych przypadkach warunek ostrzegawczy może obniżyć poziom poważnych problemów bez interwencji użytkownika.
* **Błąd**. Jednostka jest w złej kondycji. Należy wykonać akcję, aby naprawić stan jednostki, ponieważ nie może ona działać prawidłowo.
* **Nieznany**. Jednostka nie istnieje w magazynie kondycji. Ten wynik można uzyskać z zapytań rozproszonych, które scalają wyniki z wielu składników. Na przykład zapytanie Get node list przechodzi do **trybu failovermanager**, **Clustermanager** i **HealthManager**; zapytanie Get list aplikacji przechodzi do programu **clustermanager** i **kondycji**. Te zapytania scalają wyniki z wielu składników systemu. Jeśli inny składnik systemowy zwraca jednostkę, która nie jest obecna w magazynie kondycji, scalony wynik ma nieznany stan kondycji. Jednostka nie znajduje się w magazynie, ponieważ raporty kondycji nie zostały jeszcze przetworzone lub jednostka została oczyszczona po usunięciu.

## <a name="health-policies"></a>Zasady dotyczące kondycji
Magazyn kondycji stosuje zasady dotyczące kondycji, aby określić, czy jednostka jest w dobrej kondycji na podstawie raportów i jego elementów podrzędnych.

> [!NOTE]
> Zasady dotyczące kondycji można określić w manifeście klastra (na potrzeby oceny kondycji klastra i węzła) lub w manifeście aplikacji (na potrzeby oceny aplikacji i dowolnego z jej elementów podrzędnych). Żądania oceny kondycji mogą również zostać przekazane do niestandardowych zasad oceny kondycji, które są używane tylko dla tej oceny.
> 
> 

Domyślnie Service Fabric stosuje rygorystyczne reguły (wszystkie elementy muszą mieć dobrą kondycję) dla relacji hierarchicznej nadrzędny-podrzędny. Jeśli nawet jeden z elementów podrzędnych ma jedno zdarzenie w złej kondycji, element nadrzędny jest uznawany za nieprawidłowy.

### <a name="cluster-health-policy"></a>Zasady kondycji klastra
[Zasady kondycji klastra](/dotnet/api/system.fabric.health.clusterhealthpolicy) służą do oszacowania stanu kondycji klastra i Stanów kondycji węzła. Zasady można definiować w manifeście klastra. Jeśli nie istnieje, zostanie użyta domyślna zasada (niedozwolone zero).

Zasady dotyczące kondycji klastra obejmują:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Określa, czy raporty kondycji ostrzeżeń mają być traktowane jako błędy podczas oceny kondycji. Wartość domyślna: false.
* [MaxPercentUnhealthyApplications](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications). Określa maksymalny dopuszczalny procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd.
* [MaxPercentUnhealthyNodes](/dotnet/api/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes). Określa maksymalny dopuszczalny procent węzłów, które mogą być złej kondycji, zanim klaster zostanie uznany za błąd. W dużych klastrach niektóre węzły są zawsze wyłączone lub wychodzące w celu naprawy, więc ta wartość procentowa powinna być skonfigurowana do tolerowania.
* [ApplicationTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap). Mapa zasad kondycji typu aplikacji może być używana podczas oceny kondycji klastra do opisywania specjalnych typów aplikacji. Domyślnie wszystkie aplikacje są umieszczane w puli i oceniane przy użyciu MaxPercentUnhealthyApplications. Jeśli niektóre typy aplikacji powinny być traktowane inaczej, mogą one zostać pobrane z puli globalnej. Zamiast tego są oceniane względem wartości procentowych skojarzonych z ich nazwą typu aplikacji na mapie. Na przykład w klastrze istnieją tysiące aplikacji różnych typów oraz kilka wystąpień aplikacji typu "Special". Aplikacje sterujące nigdy nie powinny być w ogóle błędne. Można określić globalne MaxPercentUnhealthyApplications do 20%, aby tolerować błędy, ale dla typu aplikacji "ControlApplicationType" ustawić MaxPercentUnhealthyApplications na 0. W ten sposób, jeśli niektóre z wielu aplikacji są w złej kondycji, ale poniżej globalnej wartości procentowej w złej kondycji, klaster zostanie oceniony jako ostrzegawczy. Ostrzegawczy stan kondycji nie ma wpływu na uaktualnienie klastra ani inne monitorowanie wyzwalane przez stan kondycji błędu. Jednak nawet jedna aplikacja sterująca w ramach błędu powoduje złej kondycji klastra, która wyzwala wycofywanie lub wstrzymuje uaktualnienie klastra, w zależności od konfiguracji uaktualnienia.
  W przypadku typów aplikacji zdefiniowanych na mapie wszystkie wystąpienia aplikacji są pobierane z globalnej puli aplikacji. Są one oceniane na podstawie łącznej liczby aplikacji typu aplikacji przy użyciu określonego MaxPercentUnhealthyApplications z mapy. Wszystkie pozostałe aplikacje pozostają w puli globalnej i są oceniane przy użyciu MaxPercentUnhealthyApplications.

  Poniższy przykład to fragment z manifestu klastra. Aby zdefiniować wpisy na mapie typu aplikacji, należy prefiksować nazwę parametru z "ApplicationTypeMaxPercentUnhealthyApplications-", po którym następuje nazwa typu aplikacji.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
    </Section>
  </FabricSettings>
  ```

* [NodeTypeHealthPolicyMap](/dotnet/api/system.fabric.health.clusterhealthpolicy.nodetypehealthpolicymap). Mapa zasad kondycji typu węzła może być używana podczas oceny kondycji klastra do opisywania specjalnych typów węzłów. Typy węzłów są oceniane względem wartości procentowych skojarzonych z ich nazwą typu węzła na mapie. Ustawienie tej wartości nie ma wpływu na pulę globalną węzłów używanych dla programu `MaxPercentUnhealthyNodes` . Na przykład klaster zawiera setki węzłów różnych typów i kilka typów węzłów, które obsługują ważne działania. Nie powinno być żadnych węzłów w tym typie. Możesz określić globalne `MaxPercentUnhealthyNodes` do 20%, aby tolerować błędy dla wszystkich węzłów, ale dla typu węzła `SpecialNodeType` , ustaw wartość `MaxPercentUnhealthyNodes` na 0. W ten sposób, jeśli niektóre z wielu węzłów są w złej kondycji, ale poniżej wartości procentowej w złej kondycji, klaster będzie oceniany jako w stanie kondycji ostrzegawczej. Ostrzegawczy stan kondycji nie wpływa na uaktualnienie klastra ani inne monitorowanie wyzwalane przez stan kondycji błędu. Jednak nawet jeden węzeł typu `SpecialNodeType` w stanie kondycji błędów spowodowałaby wystąpienie klastra w złej kondycji i wyzwoli wycofanie lub wstrzymanie uaktualniania klastra, w zależności od konfiguracji uaktualnienia. Z kolei ustawienie globalne `MaxPercentUnhealthyNodes` na 0 i ustawienie `SpecialNodeType` maksymalnej liczby węzłów w złej kondycji na 100 z jednym węzłem typu `SpecialNodeType` w stanie błędu nadal powoduje wystąpienie błędu, ponieważ globalne ograniczenie jest bardziej rygorystyczne w tym przypadku. 

  Poniższy przykład to fragment z manifestu klastra. Aby zdefiniować wpisy na mapie typu węzła, poprzedź nazwę parametru prefiksem "NodeTypeMaxPercentUnhealthyNodes-", po którym następuje nazwa typu węzła.

  ```xml
  <FabricSettings>
    <Section Name="HealthManager/ClusterHealthPolicy">
      <Parameter Name="ConsiderWarningAsError" Value="False" />
      <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
      <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
      <Parameter Name="NodeTypeMaxPercentUnhealthyNodes-SpecialNodeType" Value="0" />
    </Section>
  </FabricSettings>
  ```

### <a name="application-health-policy"></a>Zasady dotyczące kondycji aplikacji
[Zasady kondycji aplikacji](/dotnet/api/system.fabric.health.applicationhealthpolicy) opisują sposób obliczania agregacji zdarzeń i Stanów podrzędnych w przypadku aplikacji i ich elementów podrzędnych. Można ją zdefiniować w manifeście aplikacji, **ApplicationManifest.xml** w pakiecie aplikacji. Jeśli nie określono żadnych zasad, Service Fabric zakłada, że jednostka jest w złej kondycji, jeśli ma raport o kondycji lub podrzędny stan kondycji ostrzeżenia lub błędu.
Konfigurowalne zasady są następujące:

* [ConsiderWarningAsError](/dotnet/api/system.fabric.health.clusterhealthpolicy.considerwarningaserror). Określa, czy raporty kondycji ostrzeżeń mają być traktowane jako błędy podczas oceny kondycji. Wartość domyślna: false.
* [MaxPercentUnhealthyDeployedApplications](/dotnet/api/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications). Określa maksymalny dopuszczalny procent wdrożonych aplikacji, które mogą być w złej kondycji, zanim aplikacja zostanie uznana za błąd. Ta wartość procentowa jest obliczana przez podzielenie liczby wdrożonych aplikacji w złej kondycji na liczbę węzłów, w których aplikacje są obecnie wdrożone w klastrze. Obliczenia są zaokrąglane w górę, aby tolerować jeden błąd w niewielkiej liczbie węzłów. Wartość domyślna: zero.
* [DefaultServiceTypeHealthPolicy](/dotnet/api/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy). Określa domyślną zasadę kondycji typu usługi, która zastępuje domyślne zasady kondycji dla wszystkich typów usług w aplikacji.
* [ServiceTypeHealthPolicyMap](/dotnet/api/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap). Zawiera mapę zasad dotyczących kondycji usług na typ usługi. Te zasady zastępują domyślne zasady kondycji typu usługi dla każdego określonego typu usługi. Na przykład jeśli aplikacja ma typ usługi bramy bezstanowej i typ usługi aparatu stanowego, można skonfigurować zasady kondycji dla ich oceny w różny sposób. W przypadku określania zasad dla typu usługi można uzyskać bardziej szczegółową kontrolę kondycji usługi.

### <a name="service-type-health-policy"></a>Zasady dotyczące kondycji typu usługi
[Zasady kondycji typu usługi](/dotnet/api/system.fabric.health.servicetypehealthpolicy) określają, jak oszacować i agregować usługi i elementy podrzędne usług. Zasady zawierają:

* [MaxPercentUnhealthyPartitionsPerService](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice). Określa maksymalny dopuszczalny procent partycji w złej kondycji, zanim usługa zostanie uznana za złą. Wartość domyślna: zero.
* [MaxPercentUnhealthyReplicasPerPartition](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition). Określa maksymalny dopuszczalny procent replik w złej kondycji, zanim partycja zostanie uznana za złą. Wartość domyślna: zero.
* [MaxPercentUnhealthyServices](/dotnet/api/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices). Określa maksymalny dopuszczalny procent usług w złej kondycji, zanim aplikacja zostanie uznana za złą. Wartość domyślna: zero.

Poniższy przykład to fragment z manifestu aplikacji:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Ocena kondycji
Użytkownicy i zautomatyzowane usługi mogą oszacować kondycję dla każdej jednostki w dowolnym momencie. Aby oszacować kondycję jednostki, magazyn kondycji agreguje wszystkie raporty dotyczące kondycji jednostki i szacuje wszystkie jego elementy podrzędne (jeśli ma zastosowanie). Algorytm agregacji kondycji korzysta z zasad dotyczących kondycji, które określają sposób oszacowania raportów o kondycji i sposobu agregowania podrzędnych Stanów kondycji (jeśli ma to zastosowanie).

### <a name="health-report-aggregation"></a>Agregacja raportu kondycji
Jedna jednostka może mieć wiele raportów o kondycji wysyłanych przez różne raporty (składniki systemowe lub alarmy) dla różnych właściwości. Agregacja używa skojarzonych zasad dotyczących kondycji, w szczególności elementu członkowskiego ConsiderWarningAsError zasad dotyczących kondycji aplikacji lub klastra. ConsiderWarningAsError określa sposób szacowania ostrzeżeń.

Zagregowany stan kondycji jest wyzwalany przez *najgorszy* Raport kondycji jednostki. Jeśli istnieje co najmniej jeden raport o kondycji błędów, zagregowany stan kondycji jest błędem.

![Agregacja raportu kondycji z raportem o błędach.][2]

Jednostka kondycji, która ma co najmniej jeden raport kondycji błędów, jest oceniana jako błąd. Ta sama wartość dotyczy wygasłego raportu kondycji, niezależnie od jego stanu kondycji.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Jeśli nie ma żadnych raportów o błędach i co najmniej jednego ostrzeżenia, zagregowany stan kondycji to ostrzeżenie lub błąd, w zależności od flagi zasad ConsiderWarningAsError.

![Agregacja raportu kondycji z raportem ostrzegawczym i ConsiderWarningAsError false.][3]

Agregacja raportu kondycji z raportem ostrzegawczym i ConsiderWarningAsError ustawionym na wartość false (domyślnie).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agregacja kondycji podrzędnej
Zagregowany stan kondycji jednostki odzwierciedla podrzędne Stany kondycji (gdy ma to zastosowanie). Algorytm agregowania Stanów kondycji podrzędnej używa zasad dotyczących kondycji, które są stosowane na podstawie typu jednostki.

![Agregacja kondycji jednostek podrzędnych.][4]

Agregacja podrzędna oparta na zasadach dotyczących kondycji.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Po przeprowadzeniu oceny wszystkich elementów podrzędnych przez magazyn kondycji agreguje stan kondycji na podstawie skonfigurowanego maksymalnego procentu elementów podrzędnych w złej kondycji. Ta wartość procentowa jest pobierana z zasad na podstawie jednostki i typu podrzędnego.

* Jeśli wszystkie elementy podrzędne mają poprawne Stany, podrzędny zagregowany stan kondycji jest prawidłowy.
* Jeśli elementy podrzędne mają stan OK i ostrzeżenie, podrzędny zagregowany stan kondycji jest ostrzegawczy.
* Jeśli istnieją elementy podrzędne z Stanami błędów, które nie przestrzegają maksymalnej dozwolonej wartości procentowej w złej kondycji, zagregowany nadrzędny stan kondycji jest błędem.
* Jeśli elementy podrzędne z błędami są zgodne z maksymalną dozwoloną wartością procentową złej kondycji, zagregowany nadrzędny stan kondycji to ostrzeżenie.

## <a name="health-reporting"></a>Raportowanie kondycji
Składniki systemowe, aplikacje sieci szkieletowej systemu i wewnętrzne/zewnętrzne alarmy mogą raportować względem jednostek Service Fabric. Raporty umożliwiają *lokalne* Określanie kondycji monitorowanych jednostek na podstawie warunków, które są monitorowane. Nie muszą oni przeglądać stanu globalnego ani zagregowanych danych. Odpowiednie zachowanie ma na celu posiadanie prostych raportów, a nie złożone organizmy, które wymagają poszukania wielu rzeczy w celu wywnioskowania, jakie informacje należy wysłać.

Aby wysłać dane o kondycji do magazynu kondycji, musi on identyfikować jednostkę, której to dotyczy, i utworzyć raport kondycji. Aby wysłać raport, użyj interfejsu API [FabricClient. HealthClient. ReportHealth](/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) , raportów usługi API Health, które są uwidocznione na `Partition` `CodePackageActivationContext` obiektach, poleceniach CMDLET programu PowerShell lub Rest.

### <a name="health-reports"></a>Raporty dotyczące kondycji
[Raporty kondycji](/dotnet/api/system.fabric.health.healthreport) dla każdej jednostki w klastrze zawierają następujące informacje:

* **SourceId**. Ciąg unikatowo identyfikujący raport zdarzenia kondycji.
* **Identyfikator jednostki**. Identyfikuje jednostkę, w której jest stosowany raport. Różni się on w zależności od [typu jednostki](service-fabric-health-introduction.md#health-entities-and-hierarchy):
  
  * Hosta. Brak.
  * Większości. Nazwa węzła (ciąg).
  * Aplikacja. Nazwa aplikacji (URI). Reprezentuje nazwę wystąpienia aplikacji wdrożonego w klastrze.
  * Usługi. Nazwa usługi (URI). Reprezentuje nazwę wystąpienia usługi wdrożonego w klastrze.
  * Podzielić. Identyfikator partycji (GUID). Reprezentuje unikatowy identyfikator partycji.
  * Niesiona. IDENTYFIKATOR repliki usługi stanowej lub identyfikator wystąpienia usługi bezstanowej (INT64).
  * DeployedApplication. Nazwa aplikacji (URI) i nazwa węzła (ciąg).
  * DeployedServicePackage. Nazwa aplikacji (URI), nazwa węzła (ciąg) i nazwa manifestu usługi (ciąg).
* **Właściwość**. *Ciąg* (nie stałe Wyliczenie), które umożliwia programowi reporter klasyfikowanie zdarzenia kondycji dla określonej właściwości jednostki. Na przykład program reporter A może zgłosić kondycję właściwości "Storage" Node01 i reporter B może zgłosić kondycję właściwości Node01 "Connectivity". W magazynie kondycji te raporty są traktowane jako osobne zdarzenia dotyczące kondycji dla jednostki Node01.
* **Opis**. Ciąg, który umożliwia zgłaszanie szczegółowych informacji o zdarzeniu kondycji. Właściwości **SourceId**, **Property** i **HealthState** powinny w pełni opisać raport. Opis dodaje informacje, które można odczytać w celu odczytania raportu. Tekst ułatwia administratorom i użytkownikom zrozumienie raportu o kondycji.
* **HealthState**. [Wyliczenie](service-fabric-health-introduction.md#health-states) opisujące stan kondycji raportu. Akceptowane wartości to OK, ostrzeżenie i błąd.
* **TimeToLive**. Obiekt TimeSpan, który wskazuje, jak długo Raport kondycji jest prawidłowy. W połączeniu z usługą **RemoveWhenExpired**, dzięki czemu magazyn kondycji wie, jak oszacować wygasłe zdarzenia. Domyślnie wartość jest nieskończona, a raport jest nieprawidłowy.
* **RemoveWhenExpired**. Wartość logiczna. W przypadku ustawienia wartości true raport o kondycji wygasłej jest automatycznie usuwany z magazynu kondycji, a raport nie ma wpływu na ocenę kondycji jednostki. Używany, gdy raport jest ważny tylko przez określony przedział czasu, a raport nie musi wyraźnie czyścić go. Jest on również używany do usuwania raportów z magazynu kondycji (na przykład, gdy licznik alarmowy został zmieniony i zatrzyma wysyłanie raportów z poprzednią źródłem i właściwością). Może wysłać raport z krótkim TimeToLive wraz z RemoveWhenExpired, aby wyczyścić poprzedni stan z magazynu kondycji. Jeśli wartość jest równa false, wygasły raport jest traktowany jako błąd na ocenie kondycji. Wartość false powoduje sygnalizowanie magazynu kondycji, który Źródło powinien raportować okresowo według tej właściwości. Jeśli tak nie jest, może wystąpić problem z licznikiem licznika. Kondycja licznika danych jest przechwytywana przez rozważenie zdarzenia jako błędu.
* **SequenceNumber**. Dodatnia liczba całkowita, która musi być coraz większa, reprezentuje kolejność raportów. Jest on używany przez magazyn kondycji do wykrywania starych raportów, które są odbierane z powodu opóźnień sieci lub innych problemów. Raport jest odrzucany, jeśli numer sekwencyjny jest mniejszy lub równy ostatnio zastosowanej liczbie dla tej samej jednostki, źródła i właściwości. Jeśli nie jest określony, numer sekwencyjny jest generowany automatycznie. Numer sekwencyjny należy umieścić tylko podczas raportowania zmian stanu. W tej sytuacji Źródło musi pamiętać, które raporty są wysyłane i zachować informacje do odzyskania w trybie failover.

Te cztery informacje--SourceId, identyfikator jednostki, właściwość i HealthState — są wymagane dla każdego raportu kondycji. Ciąg SourceId nie może rozpoczynać się od prefiksu "**System.**", który jest zarezerwowany dla raportów systemowych. Dla tej samej jednostki istnieje tylko jeden raport dla tego samego źródła i właściwości. Wiele raportów dla tego samego źródła i właściwości przesłania siebie nawzajem, po stronie klienta kondycji (jeśli są przetwarzane w partii) lub po stronie magazynu kondycji. Zastąpienie jest oparte na numerach sekwencyjnych; nowsze raporty (z wyższymi numerami sekwencji) zamieniają starsze raporty.

### <a name="health-events"></a>Zdarzenia dotyczące kondycji
Wewnętrznie magazyn kondycji przechowuje [zdarzenia dotyczące kondycji](/dotnet/api/system.fabric.health.healthevent), które zawierają wszystkie informacje z raportów i dodatkowe metadane. Metadane obejmują czas przyznany przez raport klientowi kondycji i godzinę jego modyfikacji po stronie serwera. Zdarzenia dotyczące kondycji są zwracane przez [zapytania o kondycję](service-fabric-view-entities-aggregated-health.md#health-queries).

Dodane metadane zawierają:

* **SourceUtcTimestamp**. Czas, przez jaki raport został udzielony klientowi kondycji (uniwersalny czas koordynowany).
* **LastModifiedUtcTimestamp**. Godzina ostatniej modyfikacji raportu po stronie serwera (uniwersalny czas koordynowany).
* Data **wygaśnięcia**. Flaga wskazująca, czy raport wygasł, gdy zapytanie zostało wykonane przez magazyn kondycji. Zdarzenie może być wygasłe tylko wtedy, gdy RemoveWhenExpired ma wartość false. W przeciwnym razie zdarzenie nie zostanie zwrócone przez zapytanie i zostanie usunięte ze sklepu.
* **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. Ostatnia godzina przejścia do stanu OK/ostrzeżenia/błędu. Te pola zawierają historię przejść stanu kondycji zdarzenia.

Pola przechodzenia stanu mogą służyć do inteligentniejszych alertów lub informacji o zdarzeniach dotyczących kondycji historycznej. Umożliwiają one włączenie scenariuszy takich jak:

* Zgłoś alert, gdy właściwość została wystawiona z ostrzeżeniem lub błędem przez ponad X minut. Sprawdzanie warunku przez pewien czas pozwala uniknąć alertów w warunkach tymczasowych. Na przykład alert, jeśli kondycja ostrzeżenia o więcej niż pięć minut może zostać przetłumaczona na (HealthState = = Warning i Now-LastWarningTransitionTime > 5 minut).
* Alert dotyczący tylko warunków, które zostały zmienione w ciągu ostatnich X minut. Jeśli raport został już z błędem przed określonym czasem, można go zignorować, ponieważ został już wcześniej zasygnalizowani.
* Jeśli właściwość jest przełączana między ostrzeżeniem a błędem, ustal, jak długi czas został w złej kondycji (to znaczy nie OK). Na przykład alert, jeśli właściwość nie była w dobrej kondycji przez więcej niż pięć minut można przetłumaczyć na (HealthState! = OK i teraz-LastOkTransitionTime > 5 minut).

## <a name="example-report-and-evaluate-application-health"></a>Przykład: raportowanie i szacowanie kondycji aplikacji
Poniższy przykład wysyła raport kondycji za pośrednictwem programu PowerShell w **sieci szkieletowej aplikacji:/WORDCOUNT** **ze źródła.** Raport kondycji zawiera informacje o właściwości kondycji "dostępność" w stanie kondycji błędów z nieskończoną TimeToLiveą. Następnie wysyła zapytanie do kondycji aplikacji, która zwraca zagregowane błędy stanu kondycji i raportowane zdarzenia dotyczące kondycji na liście zdarzeń dotyczących kondycji.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Użycie modelu kondycji
Model kondycji umożliwia skalowanie usług w chmurze i podstawowej platformy Service Fabric, ponieważ oceny monitorowania i kondycji są dystrybuowane między różnymi monitorami w klastrze.
Inne systemy mają jedną scentralizowaną usługę na poziomie klastra, która analizuje wszystkie *potencjalnie* przydatne informacje emitowane przez usługi. Takie podejście utrudnia ich skalowalność. Nie zezwala również na zbieranie określonych informacji w celu ułatwienia zidentyfikowania problemów i potencjalnych problemów, jak najbliżej głównej przyczyny problemu.

Model kondycji jest wielokrotnie używany do monitorowania i diagnozowania, do oceny kondycji klastra i aplikacji oraz dla monitorowanych uaktualnień. Inne usługi wykorzystują dane dotyczące kondycji w celu przeprowadzania automatycznych napraw, tworzenia historii kondycji klastra i wystawiania alertów w określonych warunkach.

## <a name="next-steps"></a>Następne kroki
[Wyświetlanie raportów o kondycji Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Używanie raportów kondycji systemu do rozwiązywania problemów](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Jak raportować i sprawdzać kondycję usługi](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Dodawanie niestandardowych raportów o kondycji Service Fabric](service-fabric-report-health.md)

[Lokalne monitorowanie i diagnozowanie usług](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric uaktualniania aplikacji](service-fabric-application-upgrade.md)
