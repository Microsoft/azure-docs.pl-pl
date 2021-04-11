---
title: Azure IoT Hub wysoka dostępność i odzyskiwanie po awarii | Microsoft Docs
description: Opisuje funkcje platformy Azure i IoT Hub, które ułatwiają tworzenie rozwiązań usługi Azure IoT o wysokiej dostępności przy użyciu funkcji odzyskiwania po awarii.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: 9d2ffac813456398c02066c978c37bdb09501aeb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628989"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii usługi IoT Hub

Pierwszy krok w kierunku implementacji odpornego rozwiązania IoT, architektów, deweloperów i właścicieli firmy musi definiować cele w zakresie czasu, w którym są tworzone. Cele te można definiować przede wszystkim w zależności od konkretnych celów firmy dla każdego scenariusza. W tym kontekście [Wskazówki techniczne dotyczące ciągłości biznesowej platformy Azure](/azure/architecture/resiliency/) opisują ogólną strukturę ułatwiającą rozważanie ciągłości działania i odzyskiwania po awarii. Dokument dotyczący [odzyskiwania po awarii i wysokiej dostępności dla aplikacji platformy Azure](/azure/architecture/reliability/disaster-recovery) zawiera wskazówki dotyczące architektury dla aplikacji platformy Azure w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii (Dr).

W tym artykule omówiono funkcje HA i DR oferowane w ramach usługi IoT Hub. Rozległe obszary omówione w tym artykule są następujące:

- W regionie HA
- Międzyregionowe odzyskiwanie po awarii
- Osiągnięcie międzyregionowej wysokiej dostępności

W zależności od celów czasowych zdefiniowanych dla rozwiązań IoT należy określić, które z opcji opisanych poniżej najlepiej odpowiadają celom biznesowym. Dołączenie dowolnego z tych alternatyw w zakresie HA/DR do rozwiązania IoT wymaga starannej oceny zalet:

- Wymagany poziom odporności 
- Implementacja i złożoność konserwacji
- Wpływ KWS

## <a name="intra-region-ha"></a>W regionie HA

Usługa IoT Hub zapewnia międzyregionową HA przez implementację nadmiarowości w prawie wszystkich warstwach usługi. [Umowa SLA opublikowana przez usługę IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) jest realizowana przy użyciu tych nadmiarowości. Deweloperzy rozwiązania IoT nie muszą wykonywać żadnych dodatkowych czynności, aby korzystać z tych funkcji HA. Chociaż IoT Hub zapewnia rozsądną gwarancję o wysokiej dostępności, przejściowe błędy nadal mogą być uznawane za z każdą platformą rozproszoną. Jeśli dopiero zaczynasz Migrowanie rozwiązań do chmury z rozwiązania lokalnego, fokus musi przejść od optymalizacji "Średni czas między awariami" na "Średni czas do odzyskania". Inaczej mówiąc, przejściowe błędy są uważane za normalne podczas pracy z chmurą w połączeniu. Odpowiednie [zasady ponawiania](iot-hub-reliability-features-in-sdks.md) muszą być wbudowane w składniki, które współdziałają z aplikacją w chmurze, aby przejmować się błędami przejściowymi.

> [!NOTE]
> Niektóre usługi platformy Azure oferują również dodatkowe warstwy dostępności w obrębie regionu przez integrację z usługą [strefy dostępności (AZs)](../availability-zones/az-overview.md). AZs nie są obecnie obsługiwane przez usługę IoT Hub.

## <a name="cross-region-dr"></a>Międzyregionowe odzyskiwanie po awarii

Zdarza się sytuacje, w których wystąpiły przerwy w działaniu centrum danych z powodu awarii lub innych awarii dotyczących zasobów fizycznych. Takie zdarzenia są rzadkimi informacjami, w których opisana powyżej funkcja HA w regionie powyżej może nie zawsze pomóc. IoT Hub udostępnia wiele rozwiązań do odzyskiwania z takich rozszerzonych awarii. 

Opcje odzyskiwania dostępne dla klientów w takiej sytuacji to zainicjowane w [trybie failover przez firmę Microsoft](#microsoft-initiated-failover) i ręczne przełączenie w [tryb failover](#manual-failover). Podstawowa różnica między nimi polega na tym, że firma Microsoft inicjuje pierwsze, a użytkownik inicjuje ten ostatni. Ponadto ręczna praca awaryjna zapewnia dolny cel czasu odzyskiwania (RTO) w porównaniu z opcją trybu failover zainicjowaną przez firmę Microsoft. Konkretne RTO dostępne dla każdej opcji zostały omówione w poniższych sekcjach. W przypadku korzystania z jednej z tych opcji w celu przełączenia w tryb failover Centrum IoT Hub z jego regionu podstawowego centrum będzie w pełni funkcjonalne w odpowiednim [regionie geograficznym platformy Azure](../best-practices-availability-paired-regions.md).

Obie te opcje trybu failover oferują następujące cele punktu odzyskiwania (RPO):

| Typ danych | Cele punktu odzyskiwania (RPO) |
| --- | --- |
| Rejestr tożsamości |0-5 minut utrata danych |
| Dane dotyczące sznurka urządzenia |0-5 minut utrata danych |
| Komunikaty z chmury do urządzenia<sup>1</sup> |0-5 minut utrata danych |
| Zadania nadrzędne<sup>1</sup> i urządzenia |0-5 minut utrata danych |
| Komunikaty z urządzenia do chmury |Wszystkie nieprzeczytane wiadomości są tracone |
| Komunikaty monitorowania operacji |Wszystkie nieprzeczytane wiadomości są tracone |
| Wiadomości z opiniami z chmury do urządzeń |Wszystkie nieprzeczytane wiadomości są tracone |

<sup>1</sup> Komunikaty z chmury do urządzenia i zadania nadrzędne nie są odzyskiwane w ramach ręcznego przełączania do trybu failover.

Po zakończeniu operacji przejścia w tryb failover dla usługi IoT Hub należy oczekiwać, że wszystkie operacje z urządzenia i aplikacji zaplecza będą działać bez konieczności ręcznej interwencji. Oznacza to, że komunikaty z urządzenia do chmury powinny być nadal wykonywane, a cały rejestr urządzenia jest nienaruszony. Zdarzenia emitowane za pośrednictwem Event Grid mogą być używane za pośrednictwem tych samych subskrypcji skonfigurowanych wcześniej, o ile te subskrypcje Event Grid nadal są dostępne. Nie jest wymagana żadna dodatkowa obsługa dla niestandardowych punktów końcowych.

> [!CAUTION]
> - Nazwa zgodna z centrum zdarzeń i punkt końcowy IoT Hub wbudowanego punktu końcowego zdarzeń po przejściu w tryb failover. Podczas otrzymywania komunikatów telemetrycznych z wbudowanego punktu końcowego przy użyciu klienta centrum zdarzeń lub hosta procesora zdarzeń należy [użyć parametrów połączenia usługi IoT Hub](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) w celu nawiązania połączenia. Dzięki temu aplikacje zaplecza będą nadal działały bez konieczności ręcznego wprowadzania interwencji w trybie failover. Jeśli używasz bezpośrednio nazwy i punktu końcowego zgodnego z centrum zdarzeń w aplikacji, musisz [pobrać nowy punkt końcowy zgodny z centrum zdarzeń](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) po przejściu w tryb failover, aby kontynuować operacje. 
>
> - W przypadku używania Azure Functions lub Azure Stream Analytics do łączenia wbudowanego punktu końcowego zdarzeń może być konieczne **ponowne uruchomienie komputera**. Wynika to z faktu, że podczas poprzedniego przesunięcia w tryb failover nie są już prawidłowe.
>
> - W przypadku kierowania do magazynu zalecamy wyświetlenie listy obiektów blob lub plików, a następnie przeprowadzenie iteracji dla nich, aby upewnić się, że wszystkie obiekty blob lub pliki są odczytywane bez tworzenia założeń partycji. Zakres partycji może ulec zmianie podczas pracy w trybie failover zainicjowanej przez firmę Microsoft lub ręcznego przejścia w tryb failover. Korzystając z [interfejsu API listy obiektów BLOB](/rest/api/storageservices/list-blobs) , można wyliczyć listę obiektów blob lub [listę interfejsów API ADLS Gen2](/rest/api/storageservices/datalakestoragegen2/filesystem/listpaths) dla listy plików. Aby dowiedzieć się więcej, zobacz [Azure Storage jako punkt końcowy routingu](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

## <a name="microsoft-initiated-failover"></a>Tryb failover zainicjowany przez firmę Microsoft

Zainicjowane przez firmę Microsoft tryb failover jest realizowane przez firmę Microsoft w rzadkich sytuacjach, gdy wszystkie centra IoT są przełączene z regionu, którego to dotyczy, do odpowiadającego mu regionu geograficznego. Ten proces jest opcją domyślną (brak sposobu, w jaki użytkownicy mogą zrezygnować) i nie wymaga interwencji użytkownika. Firma Microsoft zastrzega sobie prawo do określenia, kiedy ta opcja zostanie zrealizowana. Ten mechanizm nie obejmuje zgody użytkownika przed przełączeniem do trybu failover centrum użytkownika. Zainicjowana przez firmę Microsoft tryb failover ma cel czasu odzyskiwania (RTO) wynoszący 2-26 godzin. 

Duże RTO to spowodowane tym, że firma Microsoft musi wykonać operację przełączenia w tryb failover w imieniu wszystkich klientów, których dotyczy ten region. W przypadku korzystania z mniej krytycznych rozwiązań IoT, które mogą utrzymywać przestoje w ciągu dnia, jest to rozwiązanie, które pozwala na spełnienie tych opcji w celu spełnienia ogólnych celów odzyskiwania po awarii dla rozwiązania IoT. Łączny czas wykonywania operacji środowiska uruchomieniowego w pełni operacyjny po wyzwoleniu tego procesu został opisany w sekcji "czas do odzyskania".

## <a name="manual-failover"></a>Ręczne przełączenie w tryb failover

Jeśli cele działania Twojej firmy nie są spełnione przez RTO zainicjowanej przez firmę Microsoft w trybie failover, należy rozważyć użycie ręcznego trybu failover w celu samodzielnego uruchomienia procesu przełączania do trybu failover. RTO przy użyciu tej opcji może być dowolnym z zakresu od 10 minut do kilku godzin. RTO jest obecnie funkcją liczby urządzeń zarejestrowanych w usłudze IoT Hub przełączonej w tryb failover. Można spodziewać się, że RTO koncentratora hostuje około 100 000 urządzeń w przybliżoną 15 minut. Łączny czas wykonywania operacji środowiska uruchomieniowego w pełni operacyjny po wyzwoleniu tego procesu został opisany w sekcji "czas do odzyskania".

Opcja ręcznego przełączania do trybu failover jest zawsze dostępna do użytku niezależnie od tego, czy w regionie podstawowym występuje przestój, czy nie. W związku z tym ta opcja może być używana do wykonywania planowanych przełączeń w tryb failover. Przykładem użycia planowanej pracy w trybie failover jest przeprowadzenie okresowych przechodzenia do trybu failover. Należy zachować ostrożność w przypadku, gdy planowana operacja trybu failover powoduje przestoje dla centrum przez okres zdefiniowany przez RTO dla tej opcji, a także powoduje utratę danych zgodnie z definicją powyższą w tabeli celu. Można rozważyć skonfigurowanie przykładowego wystąpienia Centrum IoT Hub, aby okresowo korzystać z opcji planowanej pracy w trybie failover w celu uzyskania pewności, że masz pewność, że kompleksowe rozwiązania mają być w stanie działać w przypadku rzeczywistej awarii.

Ręczna praca awaryjna jest dostępna bez dodatkowych kosztów dla centrów IoT utworzonych po 18 maja 2017

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Wykonywanie ręcznego przełączania do trybu failover dla Centrum IoT Hub](tutorial-manual-failover.md)

### <a name="running-test-drills"></a>Wykonywanie testów testowych

Testów testowych nie należy wykonywać w centrach IoT, które są używane w środowiskach produkcyjnych.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Nie używaj ręcznego przełączania do trybu failover w celu migrowania Centrum IoT do innego regionu

Ręczne przełączenie w tryb failover *nie* powinno być stosowane jako mechanizm trwałej migracji centrum między różnymi regionami geograficznymi platformy Azure. Zwiększa to opóźnienie operacji wykonywanych względem usługi IoT Hub z urządzeń znajdujących się w starym regionie podstawowym.

## <a name="failback"></a>Powrót po awarii

Powrót po awarii do starego regionu podstawowego można osiągnąć, wyzwalając akcję trybu failover w innym czasie. Jeśli pierwotna Operacja trybu failover została wykonana w celu odzyskania po awarii rozszerzonej w pierwotnym regionie podstawowym, zaleca się powrót po awarii centrum do oryginalnej lokalizacji po odzyskaniu tej lokalizacji po awarii.

> [!IMPORTANT]
> - Użytkownicy mogą wykonywać dwie pomyślne przełączenia w tryb failover i 2 pomyślne operacje powrotu po awarii dziennie.
>
> - Powrót do trybu failover/operacji powrotu po awarii jest niedozwolony. Musisz poczekać przez 1 godzinę między tymi operacjami.

## <a name="time-to-recover"></a>Czas do odzyskania

Podczas gdy nazwa FQDN (i w związku z tym parametry połączenia) wystąpienia usługi IoT Hub pozostaje na tym samym wpisem w trybie failover, zmieni się źródłowy adres IP. W związku z tym łączny czas wykonywania operacji środowiska uruchomieniowego względem wystąpienia usługi IoT Hub, aby stał się w pełni funkcjonalny po wyzwoleniu procesu przejścia w tryb failover, można wyrazić przy użyciu następującej funkcji.

Czas do odzyskania = RTO [10 minut w przypadku ręcznego przełączania do trybu failover | 2-26 godzin dla zainicjowanej pracy w trybie failover przez firmę Microsoft] + opóźnienie propagacji systemu DNS + czas podejmowane przez aplikację kliencką w celu odświeżenia zbuforowanego IoT Hub adresu IP.

> [!IMPORTANT]
> Zestawy SDK IoT nie buforują adresu IP Centrum IoT Hub. Zalecamy, aby kod użytkownika, który wchodzi w skład zestawów SDK, nie powinien buforować adresu IP Centrum IoT Hub.

## <a name="achieve-cross-region-ha"></a>Osiągnięcie międzyregionowej wysokiej dostępności

Jeśli cele działania Twojej firmy nie są spełnione przez RTO, że opcje trybu failover zainicjowane przez firmę Microsoft lub ręczne przełączenia w tryb failover, należy rozważyć implementację mechanizmu trybu failover dla poszczególnych urządzeń.
Pełne podejście do topologii wdrożenia w rozwiązaniach IoT wykracza poza zakres tego artykułu. W tym artykule omówiono model wdrażania w *trybie failover* w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii.

W modelu regionalnej pracy awaryjnej zaplecze rozwiązania działa głównie w jednej lokalizacji centrum danych. Pomocnicze Centrum IoT i zaplecza są wdrażane w innej lokalizacji centrum danych. Jeśli Centrum IoT w regionie podstawowym spada na awarię lub połączenie sieciowe z urządzenia do regionu podstawowego zostanie przerwane, urządzenia będą używać punktu końcowego usługi pomocniczej. Dostępność rozwiązania można poprawić, implementując model trybu failover między regionami, a nie w obrębie jednego regionu. 

Na wysokim poziomie, aby zaimplementować regionalny model trybu failover z IoT Hub, należy wykonać następujące czynności:

* **Dodatkowa logika usługi IoT Hub i routingu urządzeń**: Jeśli usługa w regionie podstawowym zostanie zakłócona, urządzenia muszą rozpocząć łączenie się z regionem pomocniczym. Mając na uwadze charakter większości usług, które są wykorzystywane, często Administratorzy rozwiązań mogą wyzwalać proces trybu failover między regionami. Najlepszym sposobem na przekazanie nowego punktu końcowego do urządzeń przy zachowaniu kontroli nad procesem jest regularne sprawdzenie usługi *Concierge* dla bieżącego aktywnego punktu końcowego. Usługa Concierge może być aplikacją sieci Web, która jest replikowana i dostępna przy użyciu technik przekierowywania DNS (na przykład przy użyciu [usługi Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > Usługa IoT Hub nie jest obsługiwanym typem punktu końcowego w usłudze Azure Traffic Manager. Zalecenie polega na zintegrowaniu proponowanej usługi Concierge z usługą Azure Traffic Manager, implementując interfejs API sondowania kondycji punktu końcowego.

* **Replikacja rejestru tożsamości**: aby można było korzystać z programu, pomocnicze Centrum IoT musi zawierać wszystkie tożsamości urządzeń, które mogą łączyć się z rozwiązaniem. Rozwiązanie powinno zachować kopie zapasowe replikowanych tożsamości urządzeń i przekazać je do pomocniczego Centrum IoT przed przełączeniem aktywnego punktu końcowego dla urządzeń. Funkcja eksportowania tożsamości urządzeń IoT Hub jest przydatna w tym kontekście. Aby uzyskać więcej informacji, zobacz [IoT Hub przewodnik dewelopera — rejestr tożsamości](iot-hub-devguide-identity-registry.md).

* **Scalanie logiki**: po ponownym udostępnieniu regionu podstawowego wszystkie Stany i dane, które zostały utworzone w lokacji dodatkowej, muszą zostać zmigrowane z powrotem do regionu podstawowego. Ten stan i dane odnoszą się głównie do tożsamości urządzeń i metadanych aplikacji, które muszą zostać scalone z podstawowym Centrum IoT i innymi magazynami specyficznymi dla aplikacji w regionie podstawowym. 

Aby uprościć ten krok, należy użyć operacji idempotentne. Operacje idempotentne minimalizują efekty uboczne od ostatecznego rozkładu zdarzeń oraz ze zduplikowanych lub niezamówionych dostaw zdarzeń. Ponadto logika aplikacji powinna być zaprojektowana w celu tolerowania potencjalnych niespójności lub nieaktualnego stanu. Taka sytuacja może wystąpić z powodu dodatkowego czasu, który jest potrzebny, aby system mógł zostać zaleczony w oparciu o cele punktu odzyskiwania (RPO).

## <a name="choose-the-right-hadr-option"></a>Wybierz odpowiednią opcję HA/DR

Poniżej znajduje się podsumowanie opcji HA/DR przedstawionych w tym artykule, których można użyć jako ramki odwołania w celu wybrania odpowiedniej opcji, która działa dla danego rozwiązania.

| HA/DR — opcja | Cel czasu odzyskiwania | Cel punktu odzyskiwania | Czy wymaga ręcznej interwencji? | Złożoność implementacji | Dodatkowy wpływ na koszty|
| --- | --- | --- | --- | --- | --- |
| Tryb failover zainicjowany przez firmę Microsoft |2-26 godzin|Odwołaj się do tabeli celu punktu odzyskiwania|Nie|Brak|Brak|
| Ręczne przełączenie w tryb failover |10 min-2 godz.|Odwołaj się do tabeli celu punktu odzyskiwania|Tak|Bardzo niska. Musisz tylko wyzwolić tę operację z portalu.|Brak|
| HA w różnych regionach |< 1 min|Zależy od częstotliwości replikacji niestandardowego rozwiązania o wysokiej dostępności|Nie|Wys.|> 1x koszt 1 IoT Hub|

## <a name="next-steps"></a>Następne kroki

* [Czym jest usługa Azure IoT Hub?](about-iot-hub.md)
* [Wprowadzenie do centrów IoT Hub (Szybki Start)](quickstart-send-telemetry-dotnet.md)
* [Samouczek: Wykonywanie ręcznego przełączania do trybu failover dla Centrum IoT Hub](tutorial-manual-failover.md)
