---
title: Replikacja geograficzna — odzyskiwanie po awarii — Event Hubs platformy Azure | Microsoft Docs
description: Jak używać regionów geograficznych do przełączania awaryjnego i wykonywania odzyskiwania po awarii na platformie Azure Event Hubs
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: 091c6c61b079ceb8f96f04e62fb772d91732eb2f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311213"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs — odzyskiwanie geograficzne 

Odporność na katastrofalne przestoju zasobów przetwarzania danych jest wymagana dla wielu przedsiębiorstw, a w niektórych przypadkach jest wymagana przez regulacje branżowe. 

Platforma Azure Event Hubs już rozłożyć ryzyko katastrofalnych awarii poszczególnych maszyn, a nawet kompletnych stojaków w klastrach obejmujących wiele domen awarii w centrum danych i implementuje przejrzyste mechanizmy wykrywania błędów i trybu failover w taki sposób, aby usługa kontynuowała działanie w ramach gwarantowanych poziomów usług i zwykle bez zauważalnych przerw w wystąpieniu takich niepowodzeń. Jeśli Event Hubs przestrzeń nazw została utworzona z włączoną opcją dla [stref dostępności](../availability-zones/az-overview.md), ryzyko przestoju jest dodatkowo rozłożone na trzy fizycznie oddzielone urządzenia, a usługa ma wystarczającą ilość rezerw, aby szybko sprostać całkowitej, krytycznej utracie całego obiektu. 

Model klastra "All-Active" platformy Azure Event Hubs z obsługą stref dostępności zapewnia odporność na słabe błędy sprzętu i nawet katastrofalną utratę całych centrów danych. Nadal mogą wystąpić poważne sytuacje w przypadku rozległego zniszczenia fizycznego, które nawet te środki nie mogą zapewnić wystarczającej obrony. 

Event Hubs funkcja odzyskiwania geograficznego po awarii została zaprojektowana, aby ułatwić odzyskanie sprawności od awarii i porzucić niepowodzenie regionu platformy Azure dla dobra i bez konieczności zmiany konfiguracji aplikacji. Opuszczenie regionu platformy Azure zwykle obejmuje kilka usług, a ta funkcja ma głównie na celu zachowanie integralności konfiguracji złożonej aplikacji.  

Funkcja odzyskiwania Geo-Disaster zapewnia, że cała konfiguracja przestrzeni nazw (Event Hubs, grup konsumentów i ustawień) jest ciągle replikowana z podstawowej przestrzeni nazw do pomocniczej przestrzeni nazw po sparowaniu i umożliwia zainicjowanie przejścia w tryb failover tylko raz z poziomu podstawowego do pomocniczego w dowolnym momencie. Przeniesienie do trybu failover spowoduje ponowne wskazanie wybranej nazwy aliasu dla przestrzeni nazw dla pomocniczej przestrzeni nazw, a następnie przerwanie parowania. Tryb failover jest niemal natychmiast po zainicjowaniu. 

> [!IMPORTANT]
> Ta funkcja umożliwia natychmiastowe ciągłość operacji z tą samą konfiguracją, ale **nie replikuje danych zdarzenia**. O ile awaria spowodowała utratę wszystkich stref, dane zdarzenia, które są zachowywane w podstawowym centrum zdarzeń po przejściu w tryb failover, będą możliwe do odzyskania, a po przywróceniu dostępu można pobrać zdarzenia historyczne. Aby replikować dane zdarzeń i obsłużyć odpowiednie przestrzenie nazw w konfiguracjach aktywnych/aktywnych w celu sprostania awariom i katastrofom, nie należy obsłużyć tej funkcji odzyskiwania po awarii geograficznej, ale postępuj zgodnie ze [wskazówkami dotyczącymi replikacji](event-hubs-federation-overview.md).  

## <a name="outages-and-disasters"></a>Awarie i katastrofy

Ważne jest, aby zauważyć rozróżnienie między "awariami" i "katastrofami". **Awaria** to tymczasowa niedostępność usługi Azure Event Hubs i może mieć wpływ na niektóre składniki usługi, takie jak magazyn komunikatów, a nawet całe centrum danych. Jednak po rozwiązaniu problemu Event Hubs znów będzie dostępny. Zazwyczaj awaria nie powoduje utraty komunikatów ani innych danych. Przykładem takiego przestoju może być awaria w centrum danych. Niektóre przestoje dotyczą tylko krótkich strat połączenia z powodu problemów przejściowych lub w sieci. 

*Awaria* jest definiowana jako trwała lub w długim czasie utrata Event Hubs klastra, regionu platformy Azure lub centrum danych. Region lub centrum danych może lub nie staje się dostępne ponownie lub mogą być wyłączone w godzinach lub dniach. Przykładami takich awarii są pożary, powodziowe lub ziemie. Awaria, która stała się trwała, może spowodować utratę niektórych komunikatów, zdarzeń lub innych danych. Jednak w większości przypadków nie powinno się odzyskać utraty danych, a komunikaty mogą być odzyskiwane po utworzeniu kopii zapasowej centrum danych.

Funkcja odzyskiwania po awarii geograficznej platformy Azure Event Hubs to rozwiązanie do odzyskiwania po awarii. Koncepcje i przepływ pracy opisane w tym artykule mają zastosowanie do scenariuszy awaryjnych, a nie przejściowych lub tymczasowych przestojów. Aby uzyskać szczegółowe omówienie odzyskiwania po awarii w Microsoft Azure, zobacz [ten artykuł](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Podstawowe pojęcia i postanowienia

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadanych i opiera się na podstawowych i pomocniczych przestrzeniach nazw odzyskiwania po awarii. 

Funkcja odzyskiwania geograficznego po awarii jest dostępna tylko dla [standardowych i dedykowanych jednostek SKU](https://azure.microsoft.com/pricing/details/event-hubs/) . Nie musisz wprowadzać żadnych zmian parametrów połączenia, ponieważ połączenie jest nawiązywane za pośrednictwem aliasu.

W tym artykule są używane następujące terminy:

-  *Alias*: Nazwa skonfigurowanej konfiguracji odzyskiwania po awarii. Alias zawiera pojedyncze stałe parametry połączenia w pełni kwalifikowanej nazwy domeny (FQDN). Aplikacje używają tego ciągu połączenia z aliasem do łączenia się z przestrzenią nazw. 

-  *Podstawowa/pomocnicza przestrzeń nazw*: przestrzenie nazw, które odpowiadają aliasu. Podstawowa przestrzeń nazw jest "aktywna" i odbiera komunikaty (może to być istniejąca lub Nowa przestrzeń nazw). Pomocnicza przestrzeń nazw jest "pasywna" i nie odbiera komunikatów. Metadane między obydwoma są zsynchronizowane, dzięki czemu oba mogą bezproblemowo akceptować komunikaty bez konieczności zmiany kodu aplikacji ani parametrów połączenia. Aby upewnić się, że tylko aktywna przestrzeń nazw odbiera komunikaty, należy użyć aliasu.
-  *Metadane*: jednostki, takie jak centra zdarzeń i grupy konsumentów; i ich właściwości usługi, które są skojarzone z przestrzenią nazw. Tylko jednostki i ich ustawienia są replikowane automatycznie. Komunikaty i zdarzenia nie są replikowane. 
-  *Tryb failover*: proces aktywowania pomocniczej przestrzeni nazw.

## <a name="supported-namespace-pairs"></a>Obsługiwane pary przestrzeni nazw
Obsługiwane są następujące kombinacje podstawowych i pomocniczych przestrzeni nazw:  

| Podstawowa przestrzeń nazw | Pomocnicza przestrzeń nazw | Obsługiwane | 
| ----------------- | -------------------- | ---------- |
| Standardowa (Standard) | Standardowa (Standard) | Tak | 
| Standardowa (Standard) | Dedykowane | Tak | 
| Dedykowane | Dedykowane | Tak | 
| Dedykowane | Standardowa (Standard) | Nie | 

> [!NOTE]
> Nie można sparować przestrzeni nazw, które znajdują się w tym samym dedykowanym klastrze. Można sparować przestrzenie nazw, które znajdują się w osobnych klastrach. 

## <a name="setup-and-failover-flow"></a>Konfiguracja i przepływ pracy awaryjnej

Poniższa sekcja zawiera omówienie procesu pracy w trybie failover i wyjaśnia, jak skonfigurować początkową pracę w trybie failover. 

![1][]

### <a name="setup"></a>Konfiguracja

Najpierw należy utworzyć lub użyć istniejącej głównej przestrzeni nazw oraz nowej pomocniczej przestrzeni nazw, a następnie sparować te dwa. Ta parowanie zapewnia alias, którego można użyć do nawiązania połączenia. Ponieważ używasz aliasu, nie musisz zmieniać parametrów połączenia. Do parowania trybu failover można dodawać tylko nowe przestrzenie nazw. 

1. Utwórz podstawową przestrzeń nazw.
1. Utwórz pomocniczą przestrzeń nazw w innym regionie. Ta czynność jest opcjonalna. Można utworzyć pomocniczą przestrzeń nazw podczas tworzenia parowania w następnym kroku. 
1. W Azure Portal przejdź do podstawowej przestrzeni nazw.
1. Wybierz opcję **odzyskiwanie geograficzne** z menu po lewej stronie, a następnie wybierz pozycję **Inicjuj parowanie** na pasku narzędzi. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Inicjuj Parowanie z podstawowej przestrzeni nazw":::    
1. Na stronie **Inicjowanie parowania** wykonaj następujące kroki:
    1. Wybierz istniejącą pomocniczą przestrzeń nazw lub utwórz ją w innym regionie. W tym przykładzie wybrano istniejącą przestrzeń nazw.  
    1. Dla **aliasu** wprowadź alias dla parowania geograficznego odzyskiwania po awarii. 
    1. Następnie wybierz przycisk **Utwórz**. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Wybierz pomocniczą przestrzeń nazw":::        
1. Powinna zostać wyświetlona strona **aliasu Geo-Dr** . Możesz również przejść do tej strony z podstawowej przestrzeni nazw, wybierając opcję **odzyskiwanie geograficzne** w menu po lewej stronie.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Strona aliasu Geo-DR":::    
1. Na stronie **aliasu Geo-Dr** wybierz pozycję **zasady dostępu współdzielonego** w menu po lewej stronie, aby uzyskać dostęp do podstawowych parametrów połączenia dla aliasu. Użyj tych parametrów połączenia zamiast używać parametrów połączenia do bezpośredniej/pomocniczej przestrzeni nazw. 
1. Na tej stronie **przeglądu** można wykonać następujące czynności: 
    1. Przerwij parowanie między podstawowymi i pomocniczymi przestrzeniami nazw. Wybierz pozycję **Przerwij parowanie** na pasku narzędzi. 
    1. Ręczne przełączenie w tryb failover do pomocniczej przestrzeni nazw. Wybierz pozycję **tryb failover** na pasku narzędzi. 
    
        > [!WARNING]
        > Przełączenie w tryb failover spowoduje uaktywnienie pomocniczej przestrzeni nazw i usunięcie podstawowej przestrzeni nazw z parowania odzyskiwania Geo-Disaster. Utwórz inną przestrzeń nazw, aby miała nową parę odzyskiwania po awarii geograficznej. 

Na koniec należy dodać monitorowanie w celu wykrycia, czy jest konieczne przełączenie w tryb failover. W większości przypadków usługa jest jedną częścią dużego ekosystemu, dzięki czemu automatyczne przełączanie w tryb failover jest mało prawdopodobne, ponieważ często należy wykonać synchronizację z pozostałym podsystemem lub infrastrukturą.

### <a name="example"></a>Przykład

W jednym z przykładów tego scenariusza Rozważmy rozwiązanie punktu sprzedaży (POS), które emituje komunikaty lub zdarzenia. Event Hubs przekazuje te zdarzenia do niektórych mapowań lub ponownego formatowania, a następnie przekazuje zmapowane dane do innego systemu w celu dalszej obróbki. W tym momencie wszystkie te systemy mogą być hostowane w tym samym regionie świadczenia usługi Azure. Decyzja o tym, kiedy i co część przełączenia w tryb failover zależy od przepływu danych w infrastrukturze. 

Możesz zautomatyzować tryb failover z systemami monitorowania lub z wbudowanymi rozwiązaniami do monitorowania. Jednak taka Automatyzacja wymaga dodatkowego planowania i pracy, która jest poza zakresem tego artykułu.

### <a name="failover-flow"></a>Przepływ pracy awaryjnej

Po zainicjowaniu trybu failover wymagane są dwa kroki:

1. W przypadku wystąpienia innej awarii należy ponownie przejść do trybu failover. W związku z tym Skonfiguruj inną pasywną przestrzeń nazw i zaktualizuj parowanie. 

2. Po ponownym udostępnieniu wiadomości ściągających z dawnej podstawowej przestrzeni nazw. Następnie należy użyć tej przestrzeni nazw do zwykłych komunikatów poza konfiguracją odzyskiwania geograficznego lub usunąć starą podstawową przestrzeń nazw.

> [!NOTE]
> Obsługiwane są tylko semantyki z przekazywaniem niepowodzeniem. W tym scenariuszu nastąpi przełączenie w tryb failover, a następnie ponowne Parowanie z nową przestrzenią nazw. Powrót po awarii nie jest obsługiwany; na przykład w klastrze SQL. 

![2][]

## <a name="management"></a>Zarządzanie

Jeśli popełniono błąd; na przykład podczas początkowej konfiguracji sparowano nieprawidłowe regiony, można przerwać parowanie dwóch przestrzeni nazw w dowolnym momencie. Jeśli chcesz używać par przestrzeni nazw jako zwykłych przestrzeni nazw, Usuń alias.

## <a name="samples"></a>Samples

[Przykład w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) pokazuje, jak skonfigurować i zainicjować tryb failover. Ten przykład ilustruje następujące koncepcje:

- Ustawienia wymagane w Azure Active Directory do użycia Azure Resource Manager z Event Hubs. 
- Kroki wymagane do wykonania przykładowego kodu. 
- Wyślij i Odbierz z bieżącej podstawowej przestrzeni nazw. 

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy pamiętać o następujących kwestiach, które należy wziąć pod uwagę:

1. Zgodnie z projektem, Event Hubs odzyskiwanie geograficznego systemu nie replikuje danych i w związku z tym nie można ponownie użyć starej wartości przesunięcia głównego centrum zdarzeń w pomocniczym centrum zdarzeń. Zalecamy ponowne uruchomienie odbiornika zdarzeń przy użyciu jednej z następujących metod:

   - *EventPosition. FromStart ()* — Jeśli chcesz odczytywać wszystkie dane z pomocniczego centrum zdarzeń.
   - *EventPosition. FromEnd ()* — Jeśli chcesz odczytywać wszystkie nowe dane z czasu połączenia z pomocniczym centrum zdarzeń.
   - *EventPosition. FromEnqueuedTime (DateTime)* — Jeśli chcesz odczytywać wszystkie dane odebrane w pomocniczym centrum zdarzeń, rozpoczynając od danego dnia i godziny.

2. W planowaniu trybu failover należy również wziąć pod uwagę współczynnik czasu. Jeśli na przykład utracisz łączność dłużej niż od 15 do 20 minut, możesz zdecydować się na zainicjowanie trybu failover. 
 
3. Fakt, że żadne dane nie są replikowane, oznacza, że bieżące aktywne sesje nie są replikowane. Ponadto może nie zadziałała funkcja wykrywania duplikatów i zaplanowanych komunikatów. Nowe sesje, zaplanowane wiadomości i nowe duplikaty będą działały. 

4. Przełączenie w tryb failover złożonej infrastruktury rozproszonej powinno być [rehearsed](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) co najmniej raz. 

5. Synchronizowanie jednostek może zająć trochę czasu, około 50-100 jednostek na minutę.

## <a name="availability-zones"></a>Strefy dostępności 

Standardowa jednostka SKU Event Hubs obsługuje [strefy dostępności](../availability-zones/az-overview.md), zapewniając lokalizację izolowaną od błędów w regionie świadczenia usługi Azure. 

> [!NOTE]
> Strefy dostępności pomoc techniczna dla usługi Azure Event Hubs Standard jest dostępna tylko w [regionach świadczenia usługi Azure](../availability-zones/az-region.md) , w których znajdują się strefy dostępności.

Strefy dostępności można włączyć tylko dla nowych przestrzeni nazw, korzystając z Azure Portal. Event Hubs nie obsługuje migracji istniejących przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po włączeniu jej w przestrzeni nazw.

W przypadku korzystania ze stref dostępności zarówno metadane, jak i dane (zdarzenia) są replikowane między centrami danych w strefie dostępności. 

![3][]

## <a name="private-endpoints"></a>Prywatne punkty końcowe
Ta sekcja zawiera dodatkowe zagadnienia dotyczące korzystania z funkcji odzyskiwania po awarii geograficznej z przestrzeniami nazw, które używają prywatnych punktów końcowych. Aby dowiedzieć się więcej na temat używania prywatnych punktów końcowych z Event Hubs ogólnie, zobacz [Konfigurowanie prywatnych punktów końcowych](private-link-service.md).

### <a name="new-pairings"></a>Nowe pary
Jeśli spróbujesz utworzyć parowanie między podstawową przestrzenią nazw z prywatnym punktem końcowym i pomocniczą przestrzenią nazw bez prywatnego punktu końcowego, parowanie nie powiedzie się. Parowanie powiedzie się tylko wtedy, gdy zarówno podstawowa, jak i pomocnicza przestrzeń nazw mają prywatne punkty końcowe. Zalecamy używanie tych samych konfiguracji w podstawowych i pomocniczych obszarach nazw oraz w sieciach wirtualnych, w których są tworzone prywatne punkty końcowe.  

> [!NOTE]
> Podczas próby sparowania podstawowej przestrzeni nazw z prywatnym punktem końcowym i pomocniczą przestrzenią nazw proces sprawdzania poprawności sprawdza tylko, czy prywatny punkt końcowy istnieje w pomocniczej przestrzeni nazw. Nie sprawdza ona, czy punkt końcowy działa lub będzie działać po przejściu do trybu failover. Odpowiedzialność za zapewnienie, że pomocnicza przestrzeń nazw z prywatnym punktem końcowym będzie działała zgodnie z oczekiwaniami po przejściu do trybu failover.
>
> Aby sprawdzić, czy konfiguracje prywatnych punktów końcowych są takie same w podstawowych i pomocniczych przestrzeniach nazw, Wyślij żądanie odczytu (na przykład: [Pobierz centrum zdarzeń](/rest/api/eventhub/get-event-hub)) do pomocniczej przestrzeni nazw spoza sieci wirtualnej i sprawdź, czy jest wyświetlany komunikat o błędzie z usługi.

### <a name="existing-pairings"></a>Istniejące pary
Jeśli parowanie między podstawową i pomocniczą przestrzenią nazw już istnieje, tworzenie prywatnego punktu końcowego w podstawowej przestrzeni nazw zakończy się niepowodzeniem. Aby rozwiązać ten problem, należy najpierw utworzyć prywatny punkt końcowy w pomocniczej przestrzeni nazw, a następnie utworzyć jeden dla podstawowej przestrzeni nazw.

> [!NOTE]
> Chociaż zezwalamy na dostęp tylko do odczytu do pomocniczej przestrzeni nazw, dozwolone są aktualizacje konfiguracji prywatnych punktów końcowych. 

### <a name="recommended-configuration"></a>Zalecana konfiguracja
Podczas tworzenia konfiguracji odzyskiwania po awarii dla aplikacji i Event Hubs przestrzeni nazw, należy utworzyć prywatne punkty końcowe dla podstawowych i pomocniczych przestrzeni nazw Event Hubs z sieciami wirtualnymi obsługującymi zarówno podstawowe, jak i pomocnicze wystąpienia aplikacji. 

Załóżmy, że masz dwie sieci wirtualne: Sieć wirtualna 1, Sieć wirtualna 2 i podstawowe i pomocnicze przestrzenie nazw: EventHubs-Namespace1-Primary, EventHubs-Namespace2-pomocniczy. Należy wykonać następujące czynności: 

- W przypadku EventHubs-Namespace1-Primary Utwórz dwa prywatne punkty końcowe, które używają podsieci z sieci VNET-1 i sieci wirtualnej 2
- Na stronie EventHubs-Namespace2-pomocnicze Utwórz dwa prywatne punkty końcowe, które używają tych samych podsieci z sieci wirtualnej-1 i sieci wirtualnej 2 

![Prywatne punkty końcowe i sieci wirtualne](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Zaletą tego podejścia jest to, że przełączenie w tryb failover może wystąpić w przypadku warstwy aplikacji niezależnej od Event Hubs przestrzeni nazw. Rozważ następujące scenariusze: 

**Tryb failover tylko w aplikacji:** W tym miejscu aplikacja nie będzie istnieć w sieci wirtualnej 1, ale przejdzie do sieci VNET-2. Ponieważ zarówno prywatne punkty końcowe są skonfigurowane zarówno w sieci wirtualnej, jak i wirtualnej — 2 dla podstawowych i pomocniczych przestrzeni nazw, aplikacja będzie działać. 

Event Hubs przełączenia w **tryb failover tylko dla obszaru nazw**: w tym miejscu, ponieważ oba prywatne punkty końcowe są konfigurowane w obu sieciach wirtualnych dla podstawowych i pomocniczych przestrzeni nazw, aplikacja będzie działać. 

> [!NOTE]
> Aby uzyskać wskazówki dotyczące odzyskiwania po awarii geograficznej sieci wirtualnej, zobacz [Virtual Network — ciągłość](../virtual-network/virtual-network-disaster-recovery-guidance.md)działania.
 
## <a name="next-steps"></a>Następne kroki

* [Przykład w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) zawiera prosty przepływ pracy, który tworzy parowanie geograficzne i inicjuje tryb failover w przypadku scenariusza odzyskiwania po awarii.
* [Dokumentacja interfejsu API REST](/rest/api/eventhub/) zawiera opis interfejsów API służących do wykonywania konfiguracji odzyskiwania po awarii geograficznej.

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.yml)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
