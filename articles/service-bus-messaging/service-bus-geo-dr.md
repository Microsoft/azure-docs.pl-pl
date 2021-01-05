---
title: Azure Service Bus odzyskiwania po awarii geograficznej | Microsoft Docs
description: Jak używać regionów geograficznych do przełączania do trybu failover i odzyskiwania po awarii w Azure Service Bus
ms.topic: article
ms.date: 01/04/2021
ms.openlocfilehash: c07721c07923a40da9fe28e0e3116bfd6a52210f
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862363"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geograficznie — odzyskiwanie po awarii

Odporność na katastrofalne przestoju zasobów przetwarzania danych jest wymagana dla wielu przedsiębiorstw, a w niektórych przypadkach jest wymagana przez regulacje branżowe. 

Azure Service Bus już rozłożyć ryzyko katastrofalnych awarii poszczególnych maszyn, a nawet kompletnych stojaków w klastrach obejmujących wiele domen awarii w centrum danych i implementuje przejrzyste mechanizmy wykrywania niepowodzeń i trybu failover w taki sposób, aby usługa kontynuowała działanie w ramach gwarantowanych poziomów usług i zwykle bez zauważalnych przerw w wystąpieniu takich niepowodzeń. Jeśli Service Bus przestrzeń nazw została utworzona z opcją Enabled dla [stref dostępności](../availability-zones/az-overview.md), ryzyko wystąpienia awarii będzie jeszcze bardziej rozłożone na trzy fizycznie rozdzielone urządzenia, a usługa ma wystarczającą ilość rezerw, aby szybko sprostać całkowitej, krytycznej utracie całego obiektu. 

Model klastra usługi all-Active Azure Service Bus z obsługą stref dostępności jest wyższy dla każdego lokalnego produktu brokera komunikatów pod kątem odporności na uszkodzenia sprzętu i nawet w wyniku katastrofalnej utraty całego centrum danych. Nadal mogą wystąpić poważne sytuacje w przypadku rozległego zniszczenia fizycznego, które nawet te środki nie mogą zapewnić wystarczającej obrony. 

Service Bus funkcja odzyskiwania geograficznego po awarii została zaprojektowana, aby ułatwić odzyskanie sprawności od awarii i porzucić niepowodzenie regionu platformy Azure dla dobra i bez konieczności zmiany konfiguracji aplikacji. Opuszczenie regionu platformy Azure zwykle obejmuje kilka usług, a ta funkcja ma głównie na celu zachowanie integralności konfiguracji złożonej aplikacji. Ta funkcja jest globalnie dostępna dla jednostki SKU Service Bus Premium. 

Funkcja odzyskiwania Geo-Disaster zapewnia, że cała konfiguracja przestrzeni nazw (kolejek, tematów, subskrypcji, filtrów) jest ciągle replikowana z podstawowej przestrzeni nazw do pomocniczej przestrzeni nazw po sparowaniu i umożliwia zainicjowanie przejścia w tryb failover tylko raz z poziomu podstawowego do pomocniczego w dowolnym momencie. Przeniesienie do trybu failover spowoduje ponowne wskazanie wybranej nazwy aliasu dla przestrzeni nazw dla pomocniczej przestrzeni nazw, a następnie przerwanie parowania. Tryb failover jest niemal natychmiast po zainicjowaniu. 

> [!IMPORTANT]
> Funkcja umożliwia natychmiastowe ciągłość operacji z tą samą konfiguracją, ale **nie replikuje komunikatów przechowywanych w kolejkach lub subskrypcjach tematów ani w kolejkach utraconych wiadomości**. Aby zachować semantykę kolejki, taka replikacja będzie wymagała nie tylko replikacji danych komunikatów, ale każda zmiana stanu w brokerze. W przypadku większości Service Bus przestrzeni nazw wymagany ruch związany z replikacją znacznie przekracza ruch aplikacji i kolejki o wysokiej przepływności, większość komunikatów nadal będzie replikowana do pomocniczego, podczas gdy są one już usuwane z podstawowego, powodując nadmierny ruch wasteful. W przypadku tras replikacji o wysokim opóźnieniu odnoszących się do wielu par, które mają być przeznaczone do odzyskiwania po awarii geograficznej, może być również niemożliwe, aby ruch związany z replikacją mógł utrzymywać ruch aplikacji z powodu efektów ograniczających opóźnienia.
 
> [!TIP]
> W przypadku replikowania zawartości kolejek i subskrypcji tematów oraz działania odpowiednich przestrzeni nazw w konfiguracjach aktywnych/aktywnych w celu sprostania awariom i katastrofom, nie należy obsłużyć tego zestawu funkcji odzyskiwania po awarii geograficznej, ale postępuj zgodnie ze [wskazówkami dotyczącymi replikacji](service-bus-federation-overview.md).  

## <a name="outages-and-disasters"></a>Awarie i katastrofy

Ważne jest, aby zauważyć rozróżnienie między "awariami" i "katastrofami". 

*Awaria* to tymczasowa niedostępność Azure Service Bus i może mieć wpływ na niektóre składniki usługi, takie jak magazyn komunikatów, a nawet całe centrum danych. Jednak po rozwiązaniu problemu Service Bus znów będzie dostępny. Zazwyczaj awaria nie powoduje utraty komunikatów ani innych danych. Przykładem takiego przestoju może być awaria w centrum danych. Niektóre przestoje są tylko krótkimi stratami połączenia z powodu problemów przejściowych lub sieciowych. 

*Awaria* jest definiowana jako trwała lub w długim czasie utrata Service Bus klastra, regionu platformy Azure lub centrum danych. Region lub centrum danych może lub nie staje się dostępne ponownie lub mogą być wyłączone w godzinach lub dniach. Przykładami takich awarii są pożary, powodziowe lub ziemie. Awaria, która stała się trwała, może spowodować utratę niektórych komunikatów, zdarzeń lub innych danych. Jednak w większości przypadków nie powinno się odzyskać utraty danych, a komunikaty mogą być odzyskiwane po utworzeniu kopii zapasowej centrum danych.

Funkcja odzyskiwania po awarii geograficznej Azure Service Bus jest rozwiązaniem odzyskiwania po awarii. Koncepcje i przepływ pracy opisane w tym artykule mają zastosowanie do scenariuszy awaryjnych, a nie przejściowych lub tymczasowych przestojów. Aby uzyskać szczegółowe omówienie odzyskiwania po awarii w Microsoft Azure, zobacz [ten artykuł](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Podstawowe pojęcia i postanowienia

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadanych i opiera się na podstawowych i pomocniczych przestrzeniach nazw odzyskiwania po awarii. Należy pamiętać, że funkcja odzyskiwania geograficznego po awarii jest dostępna tylko dla [jednostki SKU Premium](service-bus-premium-messaging.md) . Nie musisz wprowadzać żadnych zmian parametrów połączenia, ponieważ połączenie jest nawiązywane za pośrednictwem aliasu.

W tym artykule są używane następujące terminy:

-  *Alias*: Nazwa skonfigurowanej konfiguracji odzyskiwania po awarii. Alias zawiera pojedyncze stałe parametry połączenia w pełni kwalifikowanej nazwy domeny (FQDN). Aplikacje używają tego ciągu połączenia z aliasem do łączenia się z przestrzenią nazw. Użycie aliasu gwarantuje, że parametry połączenia nie zmienią się podczas wyzwolenia trybu failover.

-  *Podstawowa/pomocnicza przestrzeń nazw*: przestrzenie nazw, które odpowiadają aliasu. Podstawowa przestrzeń nazw jest "aktywna" i odbiera komunikaty (może to być istniejąca lub Nowa przestrzeń nazw). Pomocnicza przestrzeń nazw jest "pasywna" i nie odbiera komunikatów. Metadane między obydwoma są zsynchronizowane, dzięki czemu oba mogą bezproblemowo akceptować komunikaty bez konieczności zmiany kodu aplikacji ani parametrów połączenia. Aby upewnić się, że tylko aktywna przestrzeń nazw odbiera komunikaty, należy użyć aliasu. 

-  *Metadane*: jednostki, takie jak kolejki, tematy i subskrypcje; i ich właściwości usługi, które są skojarzone z przestrzenią nazw. Należy pamiętać, że tylko jednostki i ich ustawienia są replikowane automatycznie. Komunikaty nie są replikowane.

-  *Tryb failover*: proces aktywowania pomocniczej przestrzeni nazw.

## <a name="setup"></a>Konfigurowanie

Poniższa sekcja zawiera omówienie konfigurowania parowania między przestrzeniami nazw.

![1][]

Proces instalacji jest następujący:

1. Zapewnij a ***podstawowy** _ Service Bus przestrzeń nazw Premium.

2. Zainicjuj obsługę _*_pomocniczej_*_ przestrzeni nazw Service Bus Premium w regionie _different, z której ma miejsce podstawowa przestrzeń nazw. Pomoże to zapewnić izolację błędów w różnych regionach centrum danych.

3. Utwórz parowanie między podstawową przestrzenią nazw i pomocniczą przestrzenią nazw, aby uzyskać ***alias** _.

    >[!NOTE] 
    > Jeśli [przeprowadzono migrację Azure Service Bus standardowej przestrzeni nazw do Azure Service Bus Premium](service-bus-migrate-standard-premium.md), należy użyć istniejącego aliasu (tj. Service Bus parametrów połączenia standardowej przestrzeni nazw), aby utworzyć konfigurację odzyskiwania po awarii za pomocą _ *PS/CLI** lub **interfejsu API REST**.
    >
    >
    > Wynika to z faktu, że podczas migracji Azure Service Bus standardowe parametry połączenia z przestrzenią nazw/sama nazwa DNS staną się aliasem przestrzeni nazw Azure Service Bus Premium.
    >
    > Aplikacje klienckie muszą korzystać z tego aliasu (tj. Azure Service Bus standardowe parametry połączenia z przestrzenią nazw) do nawiązywania połączenia z przestrzenią nazw Premium, w której skonfigurowano parowanie odzyskiwania po awarii.
    >
    > W przypadku korzystania z portalu w celu skonfigurowania konfiguracji odzyskiwania po awarii, w portalu zostaną one abstrakcyjne.


4. Użyj *_aliasu_**, uzyskanego w kroku 3, aby połączyć aplikacje klienckie z podstawową przestrzenią nazw obsługującą funkcję odzyskiwania po awarii. Początkowo alias wskazuje podstawową przestrzeń nazw.

5. Obowiązkowe Dodaj monitorowanie, aby wykryć, czy w trybie failover jest wymagane.

## <a name="failover-flow"></a>Przepływ pracy awaryjnej

Przełączenie w tryb failover jest wyzwalane ręcznie przez klienta (jawnie za pomocą polecenia lub za pomocą logiki biznesowej należącej do klienta, która wyzwala polecenie), a nie przez platformę Azure. Zapewnia to klientowi pełną własność i widoczność na potrzeby rozwiązywania awarii w sieci szkieletowej platformy Azure.

![4][]

Po wyzwoleniu trybu failover

1. Parametry połączenia _*_aliasu_*_ są aktualizowane w celu wskazywania pomocniczej przestrzeni nazw w warstwie Premium.

2. Klienci (nadawcy i odbiorcy) automatycznie łączą się z pomocniczą przestrzenią nazw.

3. Istniejące parowanie między podstawową i pomocniczą przestrzenią nazw Premium jest zerwane.

Po zainicjowaniu trybu failover —

1. W przypadku wystąpienia innej awarii należy ponownie przejść do trybu failover. W związku z tym Skonfiguruj inną pasywną przestrzeń nazw i zaktualizuj parowanie. 

2. Po ponownym udostępnieniu wiadomości ściągających z wcześniejszej podstawowej przestrzeni nazw. Następnie należy użyć tej przestrzeni nazw do zwykłych komunikatów poza konfiguracją odzyskiwania geograficznego lub usunąć starą podstawową przestrzeń nazw.

> [!NOTE]
> Obsługiwane są tylko semantyki z przekazywaniem niepowodzeniem. W tym scenariuszu nastąpi przełączenie w tryb failover, a następnie ponowne Parowanie z nową przestrzenią nazw. Powrót po awarii nie jest obsługiwany; na przykład w klastrze SQL. 

Możesz zautomatyzować tryb failover z systemami monitorowania lub z wbudowanymi rozwiązaniami do monitorowania. Jednak taka Automatyzacja wymaga dodatkowego planowania i pracy, która jest poza zakresem tego artykułu.

![2][]

## <a name="management"></a>Zarządzanie

Jeśli popełniono błąd; na przykład podczas początkowej konfiguracji sparowano nieprawidłowe regiony, można przerwać parowanie dwóch przestrzeni nazw w dowolnym momencie. Jeśli chcesz używać par przestrzeni nazw jako zwykłych przestrzeni nazw, Usuń alias.

## <a name="use-existing-namespace-as-alias"></a>Użyj istniejącej przestrzeni nazw jako aliasu

Jeśli masz scenariusz, w którym nie można zmienić połączeń producentów i konsumentów, możesz ponownie użyć nazwy przestrzeni nazw jako aliasu. Zapoznaj się z [przykładowym kodem w witrynie GitHub tutaj](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Samples

[Przykłady w witrynie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) pokazują, jak skonfigurować i zainicjować tryb failover. Te przykłady przedstawiają następujące koncepcje:

- Przykład i ustawienia programu .NET, które są wymagane w Azure Active Directory do użycia Azure Resource Manager z Service Bus, w celu skonfigurowania i włączenia geograficznego odzyskiwania po awarii.
- Kroki wymagane do wykonania przykładowego kodu.
- Jak używać istniejącej przestrzeni nazw jako aliasu.
- Kroki umożliwiające Alternatywnie umożliwienie odzyskiwania po awarii geograficznej za pomocą programu PowerShell lub interfejsu wiersza polecenia.
- [Wyślij i Odbierz](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z bieżącej podstawowej lub pomocniczej przestrzeni nazw przy użyciu aliasu.

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy zwrócić uwagę na następujące kwestie, które należy wziąć pod uwagę w tej wersji:

1. W planowaniu trybu failover należy również wziąć pod uwagę współczynnik czasu. Jeśli na przykład utracisz łączność dłużej niż od 15 do 20 minut, możesz zdecydować się na zainicjowanie trybu failover.

2. Fakt, że żadne dane nie są replikowane, oznacza, że aktualnie aktywne sesje nie są replikowane. Ponadto może nie zadziałała funkcja wykrywania duplikatów i zaplanowanych komunikatów. Nowe sesje, nowe zaplanowane wiadomości i nowe duplikaty będą działały. 

3. Przełączenie w tryb failover złożonej infrastruktury rozproszonej powinno być [rehearsed](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) co najmniej raz.

4. Synchronizowanie jednostek może zająć trochę czasu, około 50-100 jednostek na minutę. Subskrypcje i reguły są również liczone jako jednostki.

## <a name="availability-zones"></a>Strefy dostępności

Jednostka SKU Service Bus Premium obsługuje także [strefy dostępności](../availability-zones/az-overview.md), zapewniając lokalizacje izolowane w regionie platformy Azure.

> [!NOTE]
> Strefy dostępności wsparcia dla Azure Service Bus Premium jest dostępna tylko w [regionach świadczenia usługi Azure](../availability-zones/az-region.md) , w których znajdują się strefy dostępności.

Strefy dostępności można włączyć tylko dla nowych przestrzeni nazw, korzystając z Azure Portal. Service Bus nie obsługuje migracji istniejących przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po włączeniu jej w przestrzeni nazw.

![3][]

## <a name="private-endpoints"></a>Prywatne punkty końcowe
Ta sekcja zawiera dodatkowe zagadnienia dotyczące korzystania z funkcji odzyskiwania po awarii geograficznej z przestrzeniami nazw, które używają prywatnych punktów końcowych. Aby dowiedzieć się, jak ogólnie korzystać z prywatnych punktów końcowych z Service Bus, zobacz [integrowanie Azure Service Bus z prywatnym łączem platformy Azure](private-link-service.md).

### <a name="new-pairings"></a>Nowe pary
Jeśli spróbujesz utworzyć parowanie między podstawową przestrzenią nazw z prywatnym punktem końcowym i pomocniczą przestrzenią nazw bez prywatnego punktu końcowego, parowanie nie powiedzie się. Parowanie powiedzie się tylko wtedy, gdy zarówno podstawowa, jak i pomocnicza przestrzeń nazw mają prywatne punkty końcowe. Zalecamy używanie tych samych konfiguracji w podstawowych i pomocniczych obszarach nazw oraz w sieciach wirtualnych, w których są tworzone prywatne punkty końcowe. 

> [!NOTE]
> Podczas próby sparowania podstawowej przestrzeni nazw z prywatnym punktem końcowym i pomocniczą przestrzenią nazw proces sprawdzania poprawności sprawdza tylko, czy prywatny punkt końcowy istnieje w pomocniczej przestrzeni nazw. Nie sprawdza ona, czy punkt końcowy działa lub będzie działać po przejściu do trybu failover. Odpowiedzialność za zapewnienie, że pomocnicza przestrzeń nazw z prywatnym punktem końcowym będzie działała zgodnie z oczekiwaniami po przejściu do trybu failover.
>
> Aby sprawdzić, czy konfiguracje prywatnych punktów końcowych są takie same, Wyślij żądanie [Get Queues](/rest/api/servicebus/stable/queues/get) do pomocniczej przestrzeni nazw spoza sieci wirtualnej i sprawdź, czy jest wyświetlany komunikat o błędzie z usługi.

### <a name="existing-pairings"></a>Istniejące pary
Jeśli parowanie między podstawową i pomocniczą przestrzenią nazw już istnieje, tworzenie prywatnego punktu końcowego w podstawowej przestrzeni nazw zakończy się niepowodzeniem. Aby rozwiązać ten problem, należy najpierw utworzyć prywatny punkt końcowy w pomocniczej przestrzeni nazw, a następnie utworzyć jeden dla podstawowej przestrzeni nazw.

> [!NOTE]
> Chociaż zezwalamy na dostęp tylko do odczytu do pomocniczej przestrzeni nazw, dozwolone są aktualizacje konfiguracji prywatnych punktów końcowych. 

### <a name="recommended-configuration"></a>Zalecana konfiguracja
Podczas tworzenia konfiguracji odzyskiwania po awarii dla aplikacji i Service Bus należy utworzyć prywatne punkty końcowe dla podstawowych i pomocniczych przestrzeni nazw Service Bus dla sieci wirtualnych obsługujących zarówno podstawowe, jak i pomocnicze wystąpienia aplikacji.

Załóżmy, że masz dwie sieci wirtualne: Sieć wirtualna 1, Sieć wirtualna 2 i następujące podstawowe i drugie przestrzenie nazw: ServiceBus-Namespace1-Primary, ServiceBus-Namespace2-pomocniczy. Należy wykonać następujące czynności: 

- W przypadku ServiceBus-Namespace1-Primary Utwórz dwa prywatne punkty końcowe, które używają podsieci z sieci VNET-1 i sieci wirtualnej 2
- Na stronie ServiceBus-Namespace2-pomocnicze Utwórz dwa prywatne punkty końcowe, które używają tych samych podsieci z sieci wirtualnej-1 i sieci wirtualnej 2 

![Prywatne punkty końcowe i sieci wirtualne](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


Zaletą tego podejścia jest to, że przełączenie w tryb failover może wystąpić w przypadku warstwy aplikacji niezależnej od Service Bus przestrzeni nazw. Rozważ następujące scenariusze: 

Przełączenie w *tryb failover tylko do aplikacji:** tutaj, aplikacja nie będzie istnieć w sieci wirtualnej 1, ale przejdzie do sieci wirtualnej-2. Ponieważ zarówno prywatne punkty końcowe są skonfigurowane zarówno w sieci wirtualnej, jak i wirtualnej — 2 dla podstawowych i pomocniczych przestrzeni nazw, aplikacja będzie działać. 

Service Bus przełączenia w **tryb failover tylko dla obszaru nazw**: w tym miejscu, ponieważ oba prywatne punkty końcowe są konfigurowane w obu sieciach wirtualnych dla podstawowych i pomocniczych przestrzeni nazw, aplikacja będzie działać. 

> [!NOTE]
> Aby uzyskać wskazówki dotyczące odzyskiwania po awarii geograficznej sieci wirtualnej, zobacz [Virtual Network — ciągłość](../virtual-network/virtual-network-disaster-recovery-guidance.md)działania.

## <a name="next-steps"></a>Następne kroki

- Zobacz [Informacje o interfejsie API REST](/rest/api/servicebus/stable/disasterrecoveryconfigs)odzyskiwania po awarii geograficznej w tym miejscu.
- Uruchom przykład odzyskiwania geograficznego po awarii [w serwisie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Zobacz przykład odzyskiwania geograficznego po awarii [, który wysyła komunikaty do aliasu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące artykuły:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Interfejs API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
