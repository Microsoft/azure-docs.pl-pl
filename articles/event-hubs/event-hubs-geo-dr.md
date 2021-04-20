---
title: Geograficzne odzyskiwanie po awarii — Azure Event Hubs| Microsoft Docs
description: Jak używać regionów geograficznych do pracy w trybie fail over i odzyskiwania po awarii w Azure Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: b2cf2b0ebef2b460b626e45d6b52309c9281d6ce
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739246"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs — geograficzne odzyskiwanie po awarii 

Odporność na katastrofalne awarie zasobów przetwarzania danych jest wymagana dla wielu przedsiębiorstw, a w niektórych przypadkach nawet przez przepisy branżowe. 

Azure Event Hubs już rozłożone ryzyko katastrofalnych awarii poszczególnych maszyn lub nawet kompletnych regałów w klastrach, które obejmują wiele domen awarii w centrum danych, i implementuje przezroczyste mechanizmy wykrywania awarii i trybu failover, dzięki którym usługa będzie nadal działać na wyższych poziomach usług i zwykle bez zauważalnych przerw w przypadku wystąpienia takich awarii. Jeśli przestrzeń nazw usługi Event Hubs została utworzona z włączoną opcją dla stref [dostępności,](../availability-zones/az-overview.md)ryzyko wystąpienia wyjścia jest jeszcze bardziej rozłożone na trzy fizycznie oddzielone obiekty, a usługa ma wystarczająco dużo rezerw pojemności, aby natychmiast poradzić sobie z całkowitą, katastrofalną utratą całego obiektu. 

Model klastra wszystkich aktywnych Azure Event Hubs z obsługą strefy dostępności zapewnia odporność na awarie sprzętu sieciowego, a nawet katastrofalną utratę całych obiektów centrów danych. Mimo to mogą wystąpić sytuacje, w których nawet te środki nie mogą wystarczająco się przed nim obronić. 

Funkcja Event Hubs geograficznego odzyskiwania po awarii została zaprojektowana w celu ułatwienia odzyskiwania po awarii takiej wielkości i porzucenia awarii regionu świadczenia usługi Azure na dobre i bez konieczności zmiany konfiguracji aplikacji. Porzucenie regionu świadczenia usługi Azure zwykle wiąże się z kilkoma usługami, a ta funkcja ma przede wszystkim na celu pomoc w zachowaniu integralności złożonej konfiguracji aplikacji.  

Funkcja odzyskiwania Geo-Disaster zapewnia, że cała konfiguracja przestrzeni nazw (Event Hubs, grup odbiorców i ustawień) jest stale replikowana z podstawowej przestrzeni nazw do pomocniczej przestrzeni nazw, gdy jest sparowana, i umożliwia zainicjowanie przenoszenia trybu failover tylko raz z podstawowej do pomocniczej w dowolnym momencie. Przeniesienie trybu failover spowoduje ponowne wskazanie nazwy aliasu wybranego dla przestrzeni nazw do pomocniczej przestrzeni nazw, a następnie przerwie parowanie. Po zainicjowanym zakończeniu pracy w przypadku trybu failover proces ten jest niemal natychmiastowy. 

> [!IMPORTANT]
> Ta funkcja umożliwia natychmiastową ciągłość operacji z taką samą konfiguracją, ale **nie replikuje danych zdarzenia**. Jeśli awaria nie spowodowała utraty wszystkich stref, dane zdarzeń, które są zachowywane w podstawowym centrum zdarzeń po zakończeniu pracy w awarii, będą można odzyskać, a po przywróceniu dostępu można uzyskać z niego zdarzenia historyczne. W przypadku replikowania danych zdarzeń i obsługi odpowiednich przestrzeni nazw w konfiguracjach aktywne/aktywne w celu radzenia sobie z awariami i awariami nie należy opierać się na tym zestawie funkcji geograficznego odzyskiwania po awarii, ale postępuj zgodnie ze wskazówkami [replikacji](event-hubs-federation-overview.md).  

## <a name="outages-and-disasters"></a>Awarie i awarie

Ważne jest, aby zwrócić uwagę na rozróżnienie między "awariami" i "awariami". Niedostępność **to** tymczasowa niedostępność usługi Azure Event Hubs, która może mieć wpływ na niektóre składniki usługi, takie jak magazyn obsługi komunikatów, a nawet całe centrum danych. Jednak po naprawieniu problemu Event Hubs ponownie dostępne. Zazwyczaj nie powoduje to utraty komunikatów ani innych danych. Przykładem takiej awarii może być awaria zasilania w centrum danych. Niektóre awarie to tylko krótkie straty połączeń z powodu przejściowych problemów lub problemów z siecią. 

*Awaria* jest definiowana jako trwała lub długoterminowa utrata klastra Event Hubs, regionu platformy Azure lub centrum danych. Region lub centrum danych może stać się ponownie dostępne lub może nie być dostępne przez wiele godzin lub dni. Przykłady takich awarii to pożar, powódź lub trzęsienia ziemi. Awaria, która stanie się trwała, może spowodować utratę niektórych komunikatów, zdarzeń lub innych danych. Jednak w większości przypadków nie powinna być utrata danych i komunikaty mogą być odzyskiwane po kopii zapasowej centrum danych.

Funkcja geograficznego odzyskiwania po awarii systemu Azure Event Hubs rozwiązanie do odzyskiwania po awarii. Pojęcia i przepływ pracy opisane w tym artykule dotyczą scenariuszy awarii, a nie przejściowych lub tymczasowych awarii. Aby uzyskać szczegółowe omówienie odzyskiwania po awarii w Microsoft Azure, zobacz [ten artykuł.](/azure/architecture/resiliency/disaster-recovery-azure-applications)

## <a name="basic-concepts-and-terms"></a>Podstawowe pojęcia i terminy

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadanych i opiera się na podstawowych i pomocniczych przestrzeniach nazw odzyskiwania po awarii. 

Funkcja geograficznego odzyskiwania po awarii jest dostępna tylko dla [standardowych i dedykowanych jednostki SKU.](https://azure.microsoft.com/pricing/details/event-hubs/) Nie musisz wprowadzać żadnych zmian parametrów połączenia, ponieważ połączenie jest nawiązaniu za pośrednictwem aliasu.

W tym artykule są używane następujące terminy:

-  *Alias:* nazwa konfiguracji odzyskiwania po awarii, która jest konfigurowana. Alias udostępnia jeden stabilny w pełni kwalifikowanej nazwy domeny (FQDN) parametrów połączenia. Aplikacje używają tych parametrów połączenia aliasu do nawiązywania połączenia z przestrzenią nazw. 

-  *Podstawowa/pomocnicza przestrzeń nazw:* przestrzenie nazw, które odpowiadają aliasowi. Podstawowa przestrzeń nazw jest "aktywna" i odbiera komunikaty (może to być istniejąca lub nowa przestrzeń nazw). Pomocnicza przestrzeń nazw jest "pasywna" i nie odbiera komunikatów. Metadane między nimi są zsynchronizowane, więc oba mogą bezproblemowo akceptować komunikaty bez konieczności zmiany kodu aplikacji ani parametrów połączenia. Aby upewnić się, że komunikaty są odbierane tylko przez aktywną przestrzeń nazw, należy użyć aliasu .
-  *Metadane:* jednostki, takie jak centra zdarzeń i grupy odbiorców; i ich właściwości usługi, które są skojarzone z przestrzenią nazw . Tylko jednostki i ich ustawienia są replikowane automatycznie. Komunikaty i zdarzenia nie są replikowane. 
-  *Failover*: proces aktywowania pomocniczej przestrzeni nazw.

## <a name="supported-namespace-pairs"></a>Obsługiwane pary przestrzeni nazw
Obsługiwane są następujące kombinacje podstawowych i pomocniczych przestrzeni nazw:  

| Podstawowa przestrzeń nazw | Pomocnicza przestrzeń nazw | Obsługiwane | 
| ----------------- | -------------------- | ---------- |
| Standardowa (Standard) | Standardowa (Standard) | Tak | 
| Standardowa (Standard) | Dedykowane | Tak | 
| Dedykowane | Dedykowane | Tak | 
| Dedykowane | Standardowa (Standard) | Nie | 

> [!NOTE]
> Nie można parować przestrzeni nazw, które znajdują się w tym samym dedykowanym klastrze. Przestrzenie nazw, które znajdują się w oddzielnych klastrach, można parować. 

## <a name="setup-and-failover-flow"></a>Przepływ instalacji i trybu failover

W poniższej sekcji opisano proces trybu failover i wyjaśniono sposób skonfigurowania początkowego trybu failover. 

![1][]

### <a name="setup"></a>Konfiguracja

Najpierw utwórz istniejącą podstawową przestrzeń nazw i nową pomocniczą przestrzeń nazw lub użyj jej, a następnie sparuj te dwie przestrzenie. To parowanie zapewnia alias, za pomocą których można nawiązać połączenie. Ponieważ używasz aliasu, nie musisz zmieniać parametry połączenia. Do parowania trybu failover można dodawać tylko nowe przestrzenie nazw. 

1. Utwórz podstawową przestrzeń nazw.
1. Utwórz pomocniczą przestrzeń nazw w innym regionie. Ta czynność jest opcjonalna. Pomocniczą przestrzeń nazw można utworzyć podczas tworzenia parowania w następnym kroku. 
1. W Azure Portal przejdź do podstawowej przestrzeni nazw.
1. Wybierz **pozycję Odzyskiwanie geograficzne** w menu po lewej stronie, a następnie wybierz pozycję **Zainicjuj parowanie** na pasku narzędzi. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Inicjowanie parowania z podstawowej przestrzeni nazw":::    
1. Na stronie **Inicjowanie parowania** wykonaj następujące kroki:
    1. Wybierz istniejącą pomocniczą przestrzeń nazw lub utwórz ją w innym regionie. W tym przykładzie wybrana jest istniejąca przestrzeń nazw.  
    1. W **przypadku aliasu** wprowadź alias dla parowania geograficznego dr. 
    1. Następnie wybierz przycisk **Utwórz**. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Wybieranie pomocniczej przestrzeni nazw":::        
1. Powinna zostać wyświetlony strona **Alias geograficznego dr.** Możesz również przejść do tej strony z podstawowej przestrzeni nazw, wybierając pozycję **Odzyskiwanie** geograficzne w menu po lewej stronie.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Strona aliasu geograficznego dr":::    
1. Na stronie **Alias geograficznego dr wybierz** pozycję **Zasady** dostępu współdzielonych w menu po lewej stronie, aby uzyskać dostęp do podstawowych parametrów połączenia aliasu. Użyj tych parametrów połączenia zamiast używać parametrów połączenia bezpośrednio z podstawową/pomocniczą przestrzenią nazw. 
1. Na tej **stronie** Przegląd można wykonać następujące czynności: 
    1. Przerwij parowanie między podstawową i pomocniczą przestrzenią nazw. Wybierz **pozycję Przerwij parowanie** na pasku narzędzi. 
    1. Ręcznego trybu failover do pomocniczej przestrzeni nazw. Wybierz **pozycję Failover (Trybu failover)** na pasku narzędzi. 
    
        > [!WARNING]
        > Tryb pracy w trybie pracy w trybie pracy w trybie Geo-Disaster nazw aktywuje pomocniczą przestrzeń nazw i usunie podstawową przestrzeń nazw z Geo-Disaster odzyskiwania. Utwórz kolejną przestrzeń nazw, aby mieć nową parę odzyskiwania po awarii geograficznej. 

Na koniec należy dodać pewne monitorowanie, aby wykryć, czy jest wymagane trybu failover. W większości przypadków usługa jest jedną z części dużego ekosystemu, w związku z tym automatyczne tryby failover są rzadko możliwe, ponieważ często tryb failover musi być zsynchronizowany z pozostałym podsystemem lub infrastrukturą.

### <a name="example"></a>Przykład

W jednym z przykładów tego scenariusza rozważ rozwiązanie punktu sprzedaży (POS), które emituje komunikaty lub zdarzenia. Event Hubs przekazuje te zdarzenia do jakiegoś rozwiązania mapowania lub zmiany sformatowania, które następnie przekazuje zamapowane dane do innego systemu w celu dalszego przetwarzania. W tym momencie wszystkie te systemy mogą być hostowane w tym samym regionie świadczenia usługi Azure. Decyzja o tym, kiedy i w jakiej części ma być przejechy w trybu fail over, zależy od przepływu danych w infrastrukturze. 

Możesz zautomatyzować trybu failover za pomocą systemów monitorowania lub za pomocą niestandardowych rozwiązań do monitorowania. Jednak taka automatyzacja wymaga dodatkowego planowania i pracy, co jest poza zakresem tego artykułu.

### <a name="failover-flow"></a>Przepływ trybu failover

W przypadku zainicjowania trybu failover wymagane są dwa kroki:

1. W przypadku wystąpienia innej awarii chcesz mieć możliwość pracy awaryjnej ponownie. W związku z tym skonfiguruj inną pasywną przestrzeń nazw i zaktualizuj parowanie. 

2. Ściągnij komunikaty z poprzedniej podstawowej przestrzeni nazw, gdy będzie ona ponownie dostępna. Następnie użyj tej przestrzeni nazw do regularnej obsługi komunikatów poza konfiguracją odzyskiwania geograficznego lub usuń starą podstawową przestrzeń nazw.

> [!NOTE]
> Obsługiwana jest tylko semantyka przekazywania po awarii. W tym scenariuszu przejedziesz w tryb fail over, a następnie ponownie sparujesz z nową przestrzenią nazw. Powrót po awarii nie jest obsługiwany; na przykład w klastrze SQL. 

![2][]

## <a name="management"></a>Zarządzanie

Jeśli popełnisz błąd; Na przykład podczas początkowej konfiguracji sparowane niewłaściwe regiony można przerwać parowanie dwóch przestrzeni nazw w dowolnym momencie. Jeśli chcesz używać sparowanych przestrzeni nazw jako zwykłych przestrzeni nazw, usuń alias.

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy pamiętać o następujących kwestiach:

1. Domyślnie geograficzne Event Hubs po awarii nie replikuje danych, dlatego nie można ponownie użyć starej wartości przesunięcia podstawowego centrum zdarzeń w pomocniczym centrum zdarzeń. Zalecamy ponowne uruchomienie odbiornika zdarzeń przy użyciu jednej z następujących metod:

   - *EventPosition.FromStart()* — jeśli chcesz odczytać wszystkie dane w pomocniczym centrum zdarzeń.
   - *EventPosition.FromEnd()* — jeśli chcesz odczytać wszystkie nowe dane od czasu połączenia z pomocniczym centrum zdarzeń.
   - *EventPosition.FromEnqueuedTime(dateTime)* — jeśli chcesz odczytać wszystkie dane odebrane w pomocniczym centrum zdarzeń, zaczynając od danej daty i czasu.

2. Podczas planowania trybu failover należy również wziąć pod uwagę czynnik czasu. Jeśli na przykład utracisz łączność przez okres dłuższy niż 15–20 minut, możesz zdecydować się na zainicjowanie trybu failover. 
 
3. Fakt, że żadne dane nie są replikowane, oznacza, że bieżące aktywne sesje nie są replikowane. Ponadto wykrywanie duplikatów i zaplanowane komunikaty mogą nie działać. Nowe sesje, zaplanowane komunikaty i nowe duplikaty będą działać. 

4. Co najmniej raz należy ponownie przechowycać [złożoną infrastrukturę](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) rozproszoną. 

5. Synchronizacja jednostek może zająć trochę czasu — około 50–100 jednostek na minutę.

## <a name="availability-zones"></a>Strefy dostępności 

Jednostki SKU Event Hubs Standardowa obsługują [Strefy dostępności](../availability-zones/az-overview.md), zapewniając lokalizacje izolowane od błędów w regionie świadczenia usługi Azure. 

> [!NOTE]
> Obsługa Strefy dostępności usługi Azure Event Hubs Standardowa jest dostępna tylko w regionach świadczenia [usługi Azure,](../availability-zones/az-region.md) w których znajdują się strefy dostępności.

Możesz włączyć Strefy dostępności tylko w nowych przestrzeniach nazw przy użyciu Azure Portal. Event Hubs nie obsługuje migracji istniejących przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po włączeniu jej w przestrzeni nazw.

W przypadku korzystania ze stref dostępności zarówno metadane, jak i dane (zdarzenia) są replikowane w centrach danych w strefie dostępności. 

![3][]

## <a name="private-endpoints"></a>Prywatne punkty końcowe
Ta sekcja zawiera więcej kwestii do rozważenia podczas korzystania z geograficznego odzyskiwania po awarii z przestrzeniami nazw korzystającymi z prywatnych punktów końcowych. Aby dowiedzieć się więcej o używaniu prywatnych punktów końcowych z Event Hubs, zobacz [Konfigurowanie prywatnych punktów końcowych](private-link-service.md).

### <a name="new-pairings"></a>Nowe pary
Jeśli spróbujesz utworzyć parowanie między podstawową przestrzenią nazw z prywatnym punktem końcowym i pomocniczą przestrzenią nazw bez prywatnego punktu końcowego, parowanie nie powiedzie się. Parowanie powiedzie się tylko wtedy, gdy zarówno podstawowa, jak i pomocnicza przestrzeń nazw mają prywatne punkty końcowe. Zalecamy używanie tych samych konfiguracji w podstawowych i pomocniczych przestrzeniach nazw oraz w sieciach wirtualnych, w których tworzone są prywatne punkty końcowe.  

> [!NOTE]
> Podczas próby sparowania podstawowej przestrzeni nazw z prywatnym punktem końcowym i pomocniczą przestrzenią nazw proces walidacji sprawdza tylko, czy prywatny punkt końcowy istnieje w pomocniczej przestrzeni nazw. Nie sprawdza, czy punkt końcowy działa lub będzie działać po zakończeniu pracy w trybu failover. Twoim zadaniem jest zapewnienie, że pomocnicza przestrzeń nazw z prywatnym punktem końcowym będzie działać zgodnie z oczekiwaniami po zakończeniu pracy w trybu failover.
>
> Aby sprawdzić, czy konfiguracje prywatnego punktu końcowego są takie same w podstawowej i pomocniczej przestrzeni nazw, wyślij żądanie odczytu (na przykład [Pobierz](/rest/api/eventhub/get-event-hub)centrum zdarzeń ) do pomocniczej przestrzeni nazw spoza sieci wirtualnej i sprawdź, czy usługa otrzymuje komunikat o błędzie.

### <a name="existing-pairings"></a>Istniejące pary
Jeśli istnieje już parowanie między podstawową i pomocniczą przestrzenią nazw, tworzenie prywatnego punktu końcowego w podstawowej przestrzeni nazw nie powiedzie się. Aby rozwiązać ten problem, najpierw utwórz prywatny punkt końcowy w pomocniczej przestrzeni nazw, a następnie utwórz go dla podstawowej przestrzeni nazw.

> [!NOTE]
> Zezwalamy na dostęp tylko do odczytu do pomocniczej przestrzeni nazw, ale aktualizacje konfiguracji prywatnego punktu końcowego są dozwolone. 

### <a name="recommended-configuration"></a>Zalecana konfiguracja
Podczas tworzenia konfiguracji odzyskiwania po awarii dla aplikacji i przestrzeni nazw usługi Event Hubs należy utworzyć prywatne punkty końcowe dla podstawowych i pomocniczych przestrzeni nazw usługi Event Hubs względem sieci wirtualnych hostowania zarówno podstawowego, jak i pomocniczego wystąpienia aplikacji. 

Załóżmy, że masz dwie sieci wirtualne: VNET-1, VNET-2 oraz podstawowe i pomocnicze przestrzenie nazw: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. Należy wykonać następujące czynności: 

- W obszarze EventHubs-Namespace1-Primary utwórz dwa prywatne punkty końcowe, które używają podsieci z sieci VNET-1 i VNET-2
- W obszarze EventHubs-Namespace2-Secondary utwórz dwa prywatne punkty końcowe, które używają tych samych podsieci z sieci VNET-1 i VNET-2 

![Prywatne punkty końcowe i sieci wirtualne](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Zaletą tego podejścia jest to, że do trybu failover może dojść w warstwie aplikacji niezależnie od Event Hubs nazw. Rozważ następujące scenariusze: 

**Trybu failover tylko dla aplikacji:** W tym miejscu aplikacja nie będzie istnieć w sieci VNET-1, ale zostanie przesłonięcie do sieci VNET-2. Ponieważ oba prywatne punkty końcowe są skonfigurowane zarówno w sieci VNET-1, jak i VNET-2 dla podstawowej i pomocniczej przestrzeni nazw, aplikacja po prostu będzie działać. 

Event Hubs trybu **failover** tylko dla przestrzeni nazw: ponownie, ponieważ oba prywatne punkty końcowe są skonfigurowane w obu sieciach wirtualnych dla podstawowych i pomocniczych przestrzeni nazw, aplikacja po prostu będzie działać. 

> [!NOTE]
> Aby uzyskać wskazówki dotyczące geograficznego odzyskiwania po awarii sieci wirtualnej, [zobacz Virtual Network — ciągłość działania.](../virtual-network/virtual-network-disaster-recovery-guidance.md)
 
## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższymi przykładami lub dokumentacją referencyjną. 
- [Przykład GeoDR dla .NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/DotNet/GeoDRClient) 
- [Przykład GeoDR języka Java](https://github.com/Azure-Samples/eventhub-java-manage-event-hub-geo-disaster-recovery)
- [.NET — przykłady dla usługi Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [.NET — przykłady dla pakietu Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)
- [Java — przykłady dla usługi azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Java — przykłady azure-eventhubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
- [Przykłady w języku Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
- [Przykłady skryptów w języku JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [Przykłady języka TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
- [Dokumentacja interfejsu API REST](/rest/api/eventhub/)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
