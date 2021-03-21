---
title: Skalowalność usług Service Fabric Services
description: Poznaj skalowanie w usłudze Azure Service Fabric i różne techniki służące do skalowania aplikacji.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 87ac89edc1c9996afc03e7c2bd6743202fdfcb52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786195"
---
# <a name="scaling-in-service-fabric"></a>Skalowanie w Service Fabric
Usługa Azure Service Fabric ułatwia tworzenie skalowalnych aplikacji przez zarządzanie usługami, partycjami i replikami w węzłach klastra. Uruchamianie wielu obciążeń na tym samym sprzęcie pozwala na maksymalne wykorzystanie zasobów, ale również zapewnia elastyczność w zakresie skalowania obciążeń. Ten film wideo Channel 9 opisuje, jak można tworzyć skalowalne aplikacje mikrousług:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Skalowanie w Service Fabric jest realizowane na kilka różnych sposobów:

1. Skalowanie przez tworzenie lub usuwanie bezstanowych wystąpień usługi
2. Skalowanie przez tworzenie lub usuwanie nowych nazwanych usług
3. Skalowanie przez tworzenie lub usuwanie nowych nazwanych wystąpień aplikacji
4. Skalowanie przy użyciu usług partycjonowanych
5. Skalowanie przez dodawanie i usuwanie węzłów z klastra 
6. Skalowanie przy użyciu metryk Menedżer zasobów klastra

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skalowanie przez tworzenie lub usuwanie bezstanowych wystąpień usługi
Jednym z najprostszych sposobów skalowania w ramach Service Fabric współpracuje z usługami bezstanowymi. Podczas tworzenia usługi bezstanowej uzyskasz możliwość zdefiniowania `InstanceCount` . `InstanceCount` definiuje, ile uruchomionych kopii kodu usługi jest tworzonych podczas uruchamiania usługi. Załóżmy na przykład, że w klastrze znajdują się węzły 100. Załóżmy również, że usługa została utworzona z `InstanceCount` 10. W czasie wykonywania te 10 uruchomione kopie kodu mogły być zbyt zajęte (lub nie mogą być zbyt małe). Jednym ze sposobów skalowania obciążenia jest zmiana liczby wystąpień. Na przykład część kodu monitorowania lub zarządzania może zmienić istniejącą liczbę wystąpień na 50 lub 5, w zależności od tego, czy obciążenie wymaga skalowania w poziomie, czy w dół na podstawie obciążenia. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Narzędzia

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>Używanie dynamicznej liczby wystąpień
W odniesieniu do usług bezstanowych Service Fabric oferuje automatyczny sposób zmiany liczby wystąpień. Pozwala to na dynamiczne skalowanie usługi przy użyciu liczby dostępnych węzłów. Aby zrezygnować z tego zachowania, należy ustawić liczbę wystąpień =-1. InstanceCount =-1 jest instrukcją Service Fabric, która mówi "Uruchom tę usługę bezstanową na każdym węźle". W przypadku zmiany liczby węzłów Service Fabric automatycznie zmienia liczbę wystąpień na zgodną, co zapewnia, że usługa jest uruchomiona na wszystkich prawidłowych węzłach. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Narzędzia

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Skalowanie przez tworzenie lub usuwanie nowych nazwanych usług
Nazwane wystąpienie usługi jest określonym wystąpieniem typu usługi (zobacz [Service Fabric cyklu życia aplikacji](service-fabric-application-lifecycle.md)) w przypadku niektórych nazwanych wystąpień aplikacji w klastrze. 

Nowe nazwane wystąpienia usługi można utworzyć (lub usunąć), ponieważ usługi stają się bardziej lub mniej zajęte. Pozwala to na rozproszenie żądań w większej liczbie wystąpień usługi, zwykle umożliwiając zmniejszenie obciążeń istniejących usług. Podczas tworzenia usług klaster Service Fabric Menedżer zasobów umieści usługi w klastrze w sposób rozproszony. Dokładne decyzje podlegają [metrykom](service-fabric-cluster-resource-manager-metrics.md) w klastrze i innymi regułami umieszczania. Usługi można tworzyć na kilka różnych sposobów, ale najczęściej są one za pomocą akcji administracyjnych, takich jak ktoś wywołujący [`New-ServiceFabricService`](/powershell/module/servicefabric/new-servicefabricservice) lub wywołujący kod [`CreateServiceAsync`](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) . `CreateServiceAsync` może być nawet wywoływana z innych usług uruchomionych w klastrze.

Dynamiczne tworzenie usług może być używane we wszystkich różnych scenariuszach i jest typowym wzorcem. Rozważmy na przykład usługę stanową, która reprezentuje konkretny przepływ pracy. Wywołania reprezentujące prace będą wyświetlane w tej usłudze, a ta usługa będzie wykonywała kroki do tego przepływu pracy i rejestrowania postępu. 

Jak można utworzyć tę konkretną skalę usługi? Usługa może być wielodostępna w niektórych formach i akceptować wywołania i uruchamiać kroki dla wielu różnych wystąpień tego samego przepływu pracy jednocześnie. Jednak może to spowodować, że kod jest bardziej skomplikowany, ponieważ teraz trzeba martwić się o wiele różnych wystąpień tego samego przepływu pracy, na różnych etapach i od różnych klientów. Ponadto obsługa wielu przepływów pracy jednocześnie nie rozwiązuje problemu ze skalą. Dzieje się tak, ponieważ w pewnym momencie ta usługa będzie zużywać zbyt wiele zasobów do dopasowania na konkretnym komputerze. Wiele usług nieskompilowanych dla tego wzorca w pierwszym miejscu ma także trudności z powodu nietypowego wąskiego gardła lub spowolnienia w kodzie. Te typy problemów powodują, że usługa nie będzie działała prawidłowo, gdy liczba współbieżnych przepływów pracy, które są śledzone, jest większa.  

Rozwiązaniem jest utworzenie wystąpienia tej usługi dla każdego innego wystąpienia przepływu pracy, który ma być śledzony. Jest to doskonały wzorzec i działa niezależnie od tego, czy usługa jest bezstanowa, czy stanowa. Aby ten wzorzec działał, zazwyczaj istnieje inna usługa, która działa jako "Usługa Menedżera obciążeń". Zadaniem tej usługi jest odbieranie żądań i kierowanie tych żądań do innych usług. Menedżer może dynamicznie utworzyć wystąpienie usługi obciążenia, gdy odbierze komunikat, a następnie przekazać żądania do tych usług. Usługa Menedżer może również odbierać wywołania zwrotne, gdy dana usługa przepływu pracy ukończy zadanie. Gdy Menedżer odbiera te wywołania zwrotne, może usunąć to wystąpienie usługi przepływu pracy lub pozostawić ją, jeśli oczekiwane jest zwiększenie liczby wywołań. 

Zaawansowane wersje tego typu Menedżera mogą nawet tworzyć pule zarządzanych usług. Pula pomaga upewnić się, że w przypadku pojawienia się nowego żądania nie trzeba czekać na zakończenie działania usługi. Zamiast tego Menedżer może po prostu wybrać usługę przepływu pracy, która nie jest obecnie zajęta w puli, lub losowo ją rozesłać. Utrzymywanie puli dostępnych usług sprawia, że nowe żądania są szybsze, ponieważ jest mniej przyczyną, że żądanie musi czekać na nową usługę. Tworzenie nowych usług jest szybkie, ale nie bezpłatnie ani natychmiastowo. Pula pomaga zminimalizować ilość czasu oczekiwania żądania przed podjęciem obsługi. Ten Menedżer i wzorzec puli często są wyświetlane, gdy czasy odpowiedzi są najważniejsze. Kolejkowanie żądania i tworzenie usługi w tle, a _następnie_ przekazanie jej do programu jest również popularnym wzorcem Menedżera, jak tworzenie i usuwanie usług na podstawie pewnych śledzenia ilości pracy, która aktualnie oczekuje. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Skalowanie przez tworzenie lub usuwanie nowych nazwanych wystąpień aplikacji
Tworzenie i usuwanie całych wystąpień aplikacji jest podobne do wzorca tworzenia i usuwania usług. W przypadku tego wzorca istnieje pewna Usługa Menedżera, która wprowadza decyzję na podstawie żądań, które widzi, oraz informacji otrzymywanych z innych usług w klastrze. 

Kiedy należy utworzyć nowe nazwane wystąpienie aplikacji zamiast tworzyć nowe nazwane wystąpienia usługi w już istniejącej aplikacji? Istnieje kilka przypadków:

  * Nowe wystąpienie aplikacji jest przeznaczone dla klienta, którego kod musi być uruchomiony w ramach określonej tożsamości lub ustawień zabezpieczeń.
    * Service Fabric umożliwia definiowanie różnych pakietów kodu do uruchamiania w określonych tożsamościach. Aby można było uruchomić ten sam pakiet kodu pod różnymi tożsamościami, aktywacje muszą wystąpić w różnych wystąpieniach aplikacji. Rozważ przypadek, w którym masz wdrożone obciążenia istniejącego klienta. Mogą one działać w ramach określonej tożsamości, aby można było monitorować i kontrolować dostęp do innych zasobów, takich jak zdalne bazy danych lub inne systemy. W tym przypadku, gdy nowy klient zostanie zarejestrowani, prawdopodobnie nie chcesz aktywować kodu w tym samym kontekście (przestrzeni procesu). Chociaż można to utrudnić, kod usługi może działać w kontekście określonej tożsamości. Zwykle trzeba mieć większy kod zabezpieczeń, izolacji i zarządzania tożsamościami. Zamiast korzystać z różnych nazwanych wystąpień usługi w ramach tego samego wystąpienia aplikacji, a tym samym obszaru procesu, można użyć różnych nazwanych wystąpień aplikacji Service Fabric. Ułatwia to Definiowanie różnych kontekstów tożsamości.
  * Nowe wystąpienie aplikacji również służy jako sposób konfiguracji.
    * Domyślnie wszystkie nazwane wystąpienia usługi określonego typu usługi w ramach wystąpienia aplikacji będą uruchamiane w tym samym procesie dla danego węzła. Oznacza to, że chociaż można skonfigurować każde wystąpienie usługi w inny sposób, to jest skomplikowane. Usługi muszą mieć jakiś token używany do wyszukiwania konfiguracji w ramach pakietu konfiguracyjnego. Zwykle jest to tylko nazwa usługi. To działa prawidłowo, ale Couples konfigurację do nazw poszczególnych wystąpień usługi nazwanych w ramach tego wystąpienia aplikacji. Może to być mylące i trudne do zarządzania, ponieważ konfiguracja jest zwykle artefaktem czasu projektowania z wartościami specyficznymi dla wystąpienia aplikacji. Tworzenie kolejnych usług zawsze oznacza więcej uaktualnień aplikacji, aby zmienić informacje w ramach pakietów konfiguracyjnych lub wdrożyć nowe, aby nowe usługi mogły wyszukiwać określone informacje. Często łatwiej jest utworzyć całe nowe nazwane wystąpienie aplikacji. Następnie możesz użyć parametrów aplikacji, aby ustawić dowolną konfigurację, która jest niezbędna dla usług. W ten sposób wszystkie usługi, które są tworzone w nazwanym wystąpieniu aplikacji, mogą dziedziczyć określone ustawienia konfiguracji. Na przykład zamiast używania jednego pliku konfiguracji z ustawieniami i dostosowaniami dla każdego klienta, takich jak wpisy tajne lub limity zasobów klienta, zamiast tego należy użyć innego wystąpienia aplikacji dla każdego klienta z tymi ustawieniami. 
  * Nowa aplikacja służy jako granica uaktualnienia
    * W Service Fabric różne nazwane wystąpienia aplikacji służą jako granice dla uaktualnienia. Uaktualnienie jednego nazwanego wystąpienia aplikacji nie będzie miało wpływu na kod, na którym działa inne nazwane wystąpienie aplikacji. Różne aplikacje będą na bieżąco uruchamiać różne wersje tego samego kodu w tych samych węzłach. Może to być czynnik, gdy konieczne jest podjęcie decyzji dotyczącej skalowania, ponieważ można określić, czy nowy kod powinien postępować zgodnie z takimi uaktualnieniami, jak inna usługa, czy nie. Załóżmy na przykład, że wywołanie dociera do usługi Menedżera, która jest odpowiedzialna za skalowanie obciążeń określonego klienta przez automatyczne tworzenie i usuwanie usług. W takim przypadku wywołanie dotyczy obciążenia związanego z _nowym_ klientem. Większość klientów, takich jak odizolowane od siebie, nie tylko w przypadku wymienionych wcześniej powodów związanych z zabezpieczeniami i konfiguracją, ale ponieważ zapewnia większą elastyczność w zakresie uruchamiania określonych wersji oprogramowania i wybierania ich podczas uaktualniania. Możesz również utworzyć nowe wystąpienie aplikacji i utworzyć usługę, po prostu aby dodatkowo podzielić ilość usług, na które będzie można nawiązać uaktualnienie. Oddzielne wystąpienia aplikacji zapewniają większy stopień szczegółowości podczas przeprowadzania uaktualnień aplikacji, a także umożliwiają testowanie a/B oraz wdrożenia niebieskie/zielone. 
  * Istniejące wystąpienie aplikacji jest zapełnione
    * W Service Fabric [wydajność aplikacji](service-fabric-cluster-resource-manager-application-groups.md) to koncepcja, za pomocą której można kontrolować ilość zasobów dostępnych dla konkretnych wystąpień aplikacji. Na przykład możesz zdecydować, że dana usługa musi mieć utworzone inne wystąpienie, aby można było skalować ją. Jednak to wystąpienie aplikacji nie jest pojemnością dla określonej metryki. Jeśli tego konkretnego klienta lub obciążenia nadal można udzielić większej ilości zasobów, można zwiększyć istniejącą pojemność aplikacji lub utworzyć nową aplikację. 

## <a name="scaling-at-the-partition-level"></a>Skalowanie na poziomie partycji
Service Fabric obsługuje partycjonowanie. Partycjonowanie dzieli usługę na kilka sekcji logicznych i fizycznych, z których każda działa niezależnie. Jest to przydatne w przypadku usług stanowych, ponieważ żaden zestaw replik nie musi obsługiwać wszystkich wywołań i manipulować wszystkimi Stanami jednocześnie. [Omówienie partycjonowania](service-fabric-concepts-partitioning.md) zawiera informacje na temat typów obsługiwanych schematów partycjonowania. Repliki każdej partycji są rozmieszczane w węzłach klastra, rozpowszechniać obciążenie tej usługi i upewniając się, że żadna usługa jako cała lub żadna partycja nie ma single point of failure. 

Należy wziąć pod uwagę usługę, która używa schematu partycjonowania z zakresem o niskim kluczu równym 0, wysoki klucz 99 i liczbę partycji wynoszącą 4. W klastrze z trzema węzłami usługa może zostać ustanowiona przy użyciu czterech replik, które współużytkują zasoby w poszczególnych węzłach, jak pokazano poniżej:

<center>

![Układ partycji z trzema węzłami](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

W przypadku zwiększenia liczby węzłów Service Fabric spowoduje przeniesienie niektórych istniejących replik. Załóżmy na przykład, że liczba węzłów zostanie zwiększona do czterech, a repliki zostaną rozdystrybuowane. Teraz usługa ma trzy repliki uruchomione w każdym węźle, z których każda należy do różnych partycji. Pozwala to na lepsze wykorzystanie zasobów od momentu, gdy nowy węzeł nie jest zimny. Zwykle zwiększa wydajność, ponieważ każda usługa ma więcej dostępnych zasobów.

<center>

![Układ partycji z czterema węzłami](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Skalowanie przy użyciu Menedżer zasobów klastra Service Fabric i metryk
[Metryki](service-fabric-cluster-resource-manager-metrics.md) to sposób, w jaki usługa wyraża użycie zasobów do Service Fabric. Dzięki metrykom klaster Menedżer zasobów możliwość reorganizowania i optymalizowania układu klastra. Na przykład może istnieć duża ilość zasobów w klastrze, ale mogą one nie zostać przydzieleni do usług, które obecnie działają. Użycie metryk umożliwia Menedżer zasobów klastra zreorganizować klaster w celu zapewnienia, że usługi mają dostęp do dostępnych zasobów. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skalowanie przez dodawanie i usuwanie węzłów z klastra 
Kolejną opcją skalowania przy użyciu Service Fabric jest zmiana rozmiaru klastra. Zmiana rozmiaru klastra oznacza dodanie lub usunięcie węzłów dla co najmniej jednego typu węzła w klastrze. Rozważmy na przykład przypadek, w którym wszystkie węzły w klastrze są gorąca. Oznacza to, że zasoby klastra są prawie zużyte. W takim przypadku Dodawanie większej liczby węzłów do klastra jest najlepszym sposobem skalowania. Po dołączeniu nowych węzłów do klastra Service Fabric klaster Menedżer zasobów przenosi do nich usługi, co zmniejsza całkowite obciążenie istniejących węzłów. W przypadku usług bezstanowych z liczbą wystąpień =-1 automatycznie tworzone są więcej wystąpień usługi. Pozwala to na przejście niektórych wywołań z istniejących węzłów do nowych węzłów. 

Aby uzyskać więcej informacji, zobacz [skalowanie klastra](service-fabric-cluster-scaling.md).

## <a name="choosing-a-platform"></a>Wybieranie platformy

Ze względu na różnice między systemami operacyjnymi korzystanie z Service Fabric z systemem Windows lub Linux może być istotną częścią skalowania aplikacji. Jedną z potencjalnych barier jest to, jak odbywa się rejestrowanie etapowe. Service Fabric w systemie Windows używa sterownika jądra dla dziennika jednego komputera, współużytkowanego przez repliki stanowe. Ten dziennik waży od około 8 GB. Z drugiej strony system Linux używa dziennika przemieszczania 256 MB dla każdej repliki, co sprawia, że jest to mniej idealne dla aplikacji, które chcą zmaksymalizować liczbę lekkich replik usług uruchomionych w danym węźle. Te różnice w tymczasowych wymaganiach dotyczących magazynu mogą potencjalnie poinformować żądaną platformę do wdrożenia klastra Service Fabric.

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Zajmiemy się wszystkimi pomysłami, które zostały tutaj omówione, i zapoznaj się z przykładem. Weź pod uwagę następujące usługi: próbujesz utworzyć usługę, która działa jako książka adresowa, która znajduje się na nazwach i informacjach kontaktowych. 

Z prawej strony masz wiele pytań związanych ze skalą: ile użytkowników chcesz mieć? Ile kontaktów będzie magazynów poszczególnych użytkowników? Próba wyszukania wszystkich danych w przypadku, gdy usługa jest po raz pierwszy jest trudna. Załóżmy, że przejdziesz do jednej usługi statycznej z określoną liczbą partycji. Konsekwencje pobrania nieprawidłowej liczby partycji mogą spowodować problemy ze skalą w dalszej części. Podobnie nawet w przypadku wybrania odpowiedniej liczby może nie być wszystkich potrzebnych informacji. Na przykład trzeba również określić rozmiar klastra na początku, zarówno w odniesieniu do liczby węzłów, jak i ich rozmiarów. Zwykle trudno jest przewidzieć, ile zasobów usługa ma zużywać w okresie istnienia. Może być również trudno wiedzieć od czasu, w którym usługa rzeczywiście widzi ten wzorzec ruchu. Na przykład użytkownicy mogli dodawać i usuwać kontakty tylko po raz pierwszy rano lub być rozdysponowane nawet w ciągu dnia. W zależności od tego może być konieczne skalowanie w poziomie i w sposób dynamiczny. Może się okazać, że można dowiedzieć się, Kiedy planujesz skalować w poziomie i w programie, ale w ten sposób prawdopodobnie będziesz potrzebować reakcji na zmianę zużycia zasobów przez usługę. Może to wiązać się z zmianami rozmiaru klastra w celu zapewnienia większej ilości zasobów podczas restrukturyzacji użycia istniejących zasobów. 

Ale dlaczego mimo to próbować wybrać jeden schemat partycji dla wszystkich użytkowników? Dlaczego należy ograniczyć się do jednej usługi i jednego klastra statycznego? Rzeczywista sytuacja jest zwykle bardziej dynamiczna. 

Podczas kompilowania na potrzeby skalowania należy wziąć pod uwagę następujący wzorzec dynamiczny. Może być konieczne dostosowanie go do swojej sytuacji:

1. Zamiast próbować wybrać schemat partycjonowania dla każdego z wierzchu, Utwórz "usługę menedżera".
2. Zadaniem usługi Menedżer jest sprawdzenie informacji o klientach podczas rejestrowania się w usłudze. Następnie w zależności od tego, czy usługa Menedżer tworzy wystąpienie _rzeczywistej_ usługi magazynu kontaktowego _dla tego klienta_. Jeśli wymagają konkretnej konfiguracji, izolacji lub uaktualnień, możesz również wybrać opcję uruchomienia wystąpienia aplikacji dla tego klienta. 

Ten wzorzec dynamicznego tworzenia ma wiele zalet:

  - Nie próbujesz odgadnąć liczby poprawnych partycji dla wszystkich użytkowników lub utworzyć pojedynczą usługę, która jest nieskończonie skalowalna w całości. 
  - Różni użytkownicy nie muszą mieć tej samej liczby partycji, liczby replik, ograniczeń umieszczania, metryk, obciążeń domyślnych, nazw usług, ustawień DNS ani żadnych innych właściwości określonych na poziomie usługi lub aplikacji. 
  - Uzyskujesz dodatkową segmentację danych. Każdy klient ma własną kopię usługi
    - Każdą usługę klienta można skonfigurować w różny sposób i przydzielić więcej lub mniej zasobów, z co najmniej większą liczbą partycji lub replik na podstawie ich oczekiwanej skali.
      - Załóżmy na przykład, że klient płacił za warstwę "Gold" — może uzyskać więcej replik lub większą liczbę partycji oraz potencjalnie zasoby przeznaczone dla usług za pośrednictwem metryk i pojemności aplikacji.
      - Lub Załóżmy, że podano informacje wskazujące, że liczba wymaganych kontaktów była "mała" — spowoduje to uzyskanie tylko kilku partycji lub nawet umieszczenie w udostępnionej puli usług innym klientom.
  - Nie korzystasz z wielu wystąpień usługi ani replik, gdy czekasz na wyświetlenie klientów
  - Jeśli klient kiedykolwiek opuści te informacje, usuwanie ich z usługi jest tak proste, jakby Menedżer usunął tę usługę lub aplikację, którą utworzyła.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pojęć Service Fabric, zobacz następujące artykuły:

* [Dostępność usług Service Fabric Services](service-fabric-availability-services.md)
* [Partycjonowanie Service Fabric usług](service-fabric-concepts-partitioning.md)
