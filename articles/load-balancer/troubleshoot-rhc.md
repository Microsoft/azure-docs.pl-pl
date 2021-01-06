---
title: Rozwiązywanie problemów dotyczących kondycji zasobów Azure Load Balancer, frontonu i dostępności zaplecza
description: Korzystając z dostępnych metryk, można zdiagnozować obniżone lub niedostępne usługa Load Balancer w warstwie Standardowa platformy Azure.
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2020
ms.author: errobin
ms.openlocfilehash: 3acaaba86c9a546a0bd45b5386287908168d50d0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955624"
---
# <a name="troubleshoot-resource-health-and-inbound-availability-issues"></a>Rozwiązywanie problemów z kondycją zasobów i problemy z dostępnością ruchu przychodzącego 

Ten artykuł zawiera wskazówki dotyczące badania problemów mających wpływ na dostępność adresów IP frontonu usługi równoważenia obciążenia i zasobów zaplecza. 

Sprawdzenie Resource Health (systemie RHC występuje) dla Load Balancer służy do określania kondycji modułu równoważenia obciążenia. Analizuje metrykę dostępności ścieżki danych przez **2-minutowy** interwał, aby określić, czy dostępne są punkty końcowe równoważenia obciążenia, kombinacje adresów IP frontonu i portów frontonu z regułami równoważenia obciążenia.

Poniższa tabela zawiera opis logiki systemie RHC występuje używanej do określania kondycji modułu równoważenia obciążenia.

| Stan kondycji zasobu | Opis |
| --- | --- |
| Dostępne | Zasób standardowego modułu równoważenia obciążenia jest w dobrej kondycji i jest dostępny. |
| Obniżona wydajność | Moduł równoważenia obciążenia w warstwie Standardowa ma zdarzenia zainicjowane przez platformę lub użytkownika, które mają wpływ na wydajność. Metryka Dostępność ścieżki danych od co najmniej dwóch minut zgłasza kondycję niższą niż 90%, ale wyższą niż 25%. Zostanie napotkany umiarkowany wpływ na wydajność. 
| Niedostępny | Zasób standardowego modułu równoważenia obciążenia nie jest w dobrej kondycji. Metryka dostępności ścieżki datapath zgłosiła mniej niż 25% kondycji przez co najmniej dwie minuty. Wystąpi znaczny wpływ na wydajność lub brak dostępności dla łączności przychodzącej. Mogą istnieć zdarzenia użytkownika lub platformy powodujące niedostępność. |
| Nieznane | Stan kondycji zasobu dla zasobu standardowego modułu równoważenia obciążenia nie został jeszcze zaktualizowany lub nie otrzymał informacji o dostępności ścieżki danych dla ostatnich 10 minut. Ten stan powinien występować przejściowo i zmienić się na prawidłowy stan po otrzymaniu danych. |


## <a name="about-the-metrics-well-use"></a>Informacje o metrykach, z których będziemy korzystać
Dwie metryki, które mają być używane są stanem *dostępności ścieżki danych* i *sondy kondycji* i są ważne, aby zrozumieć ich znaczenie w celu uzyskania poprawnych szczegółowych informacji. 

## <a name="data-path-availability"></a>Dostępność ścieżki danych
Metryka dostępności ścieżki danych jest generowana przez polecenie TCP ping co 25 sekund na wszystkich portach frontonu, dla których skonfigurowano reguły równoważenia obciążenia i przychodzącego NAT. Ten ping protokołu TCP będzie następnie kierowany do dowolnych w dobrej kondycji (z sondą) wystąpień zaplecza. Jeśli usługa otrzymuje odpowiedź na polecenie ping, jest uznawana za sukces, a suma metryk będzie powtarzana, jeśli nie będzie działać. Licznik tej metryki to 1/100 łącznej liczby poleceń ping TCP na próbkowanie. W ten sposób chcemy rozważyć średnią, co spowoduje wyświetlenie średniej wartości sum/Count dla danego okresu. Metryka dostępności ścieżki danych agregowana w ten sposób daje nam stawkę procentową sukcesu dla poleceń ping protokołu TCP w adresie IP frontonu: port dla każdej reguły równoważenia obciążenia i ruchu przychodzącego NAT.

## <a name="health-probe-status"></a>Stan sondy kondycji
Metryka stanu sondy kondycji jest generowana za pomocą polecenia ping protokołu zdefiniowanego w sondy kondycji. To polecenie ping jest wysyłane do każdego wystąpienia w puli zaplecza i na porcie zdefiniowanym w sondie kondycji. W przypadku sond protokołu HTTP i HTTPS pomyślne polecenie ping wymaga odpowiedzi HTTP 200 OK, gdy z sondami TCP dowolna odpowiedź jest uznawana za pomyślne. Kolejne sukcesy i niepowodzenia każdej sondy określają, czy wystąpienie zaplecza jest w dobrej kondycji i może odbierać ruch dla reguł równoważenia obciążenia, do których przypisano pulę zaplecza. Podobnie jak w przypadku dostępności ścieżki danych używamy średniej agregacji, która informuje nas o średnim pomyślnym/łącznym użyciu poleceń ping w interwale próbkowania. Ta wartość stanu sondy kondycji wskazuje kondycję zaplecza w izolacji od modułu równoważenia obciążenia przez sondowanie wystąpień zaplecza bez wysyłania ruchu przez fronton.

>[!IMPORTANT]
>Stan sondy kondycji jest próbkowany co minutę. Może to prowadzić do drobnych wahań w przypadku stałej wartości w inny sposób. Na przykład jeśli istnieją dwa wystąpienia zaplecza, jeden z sondowaniem i jedna sonda w dół, usługa badania kondycji może przechwytywać 7 próbek dla wystąpienia kondycji i 6 dla wystąpienia w złej kondycji. Spowoduje to, że wcześniej stała wartość 50 wyświetlana jako 46,15 przez jedną minutę. 

## <a name="diagnose-degraded-and-unavailable-load-balancers"></a>Diagnozowanie obniżonych i niedostępnych modułów równoważenia obciążenia
Zgodnie z opisem w [artykule dotyczącym kondycji zasobów](load-balancer-standard-diagnostics.md#resource-health-status)obniżona usługa równoważenia obciążenia jest taka, która pokazuje od 25% i 90% dostępności ścieżki danych, a niedostępna usługa równoważenia obciążenia jest taka, która ma mniej niż 25% czasu dostępności ścieżki danych w ciągu dwóch minut. Te same kroki można wykonać w celu zbadania awarii widocznej w każdym skonfigurowanym stanie sondy kondycji lub w ścieżce danych. Zapoznajemy się z sytuacją, w której sprawdzono nasze informacje o kondycji zasobów i że nasza usługa równoważenia obciążenia jest niedostępna z dostępnością ścieżki danych wynoszącą 0%-naszej usługi.

Najpierw przejdźmy do widoku szczegółowych metryk w bloku szczegółowe dane usługi równoważenia obciążenia. Można to zrobić za pośrednictwem bloku zasobów modułu równoważenia obciążenia lub linku w komunikacie dotyczącym kondycji zasobu.  Następnie nawiguj do karty frontonu i dostęp do zaplecza i zapoznaj się z oknem 30-minutowym okresu, gdy wystąpił stan obniżonej sprawności lub niedostępności. Jeśli zobaczysz, że nasza ścieżka danych była równa 0%, wiemy, że występuje problem uniemożliwiający ruch dla wszystkich naszych reguł równoważenia obciążenia i ruchu przychodzącego NAT oraz jak długo ten wpływ zakończył się. 

Następnym miejscem, w którym należy się zapoznać, jest Metryka stanu sondy kondycji, aby określić, czy nasza ścieżka danych jest niedostępna, ponieważ nie mamy żadnych prawidłowych wystąpień zaplecza do obsługi ruchu. Jeśli mamy co najmniej jedno wystąpienie zaplecza w dobrej kondycji dla wszystkich naszych reguł równoważenia obciążenia i zasad ruchu przychodzącego, wiemy, że nie jest to nasza konfiguracja powodująca niedostępność ścieżek danych. Ten scenariusz wskazuje na problem z platformą Azure, chociaż rzadko nie Fret, jeśli znajdziesz je jako zautomatyzowany alert do naszego zespołu, aby szybko rozwiązać wszystkie problemy z platformą.

## <a name="diagnose-health-probe-failures"></a>Diagnozuj błędy sondy kondycji
Załóżmy, że sprawdzimy nasz stan sondy kondycji i dowiesz się, że wszystkie wystąpienia są wyświetlane jako w złej kondycji. W tym artykule wyjaśniono, dlaczego nasza ścieżka danych jest niedostępna, ponieważ ruch nie ma miejsca. Następnie należy przejść przez poniższą listę kontrolną, aby wykluczyć typowe błędy konfiguracji:
* Sprawdź użycie procesora CPU przez zasoby, aby sprawdzić, czy Twoje wystąpienia są w dobrej kondycji
  * Możesz to sprawdzić 
* W przypadku korzystania z sondy HTTP lub HTTPS, jeśli aplikacja jest w dobrej kondycji i reaguje
  * Sprawdź, czy jest to funkcjonalne, bezpośrednio uzyskując dostęp do aplikacji przez prywatny adres IP lub publiczny adres IP na poziomie wystąpienia skojarzony z wystąpieniem zaplecza
* Przejrzyj sieciowe grupy zabezpieczeń stosowane do naszych zasobów zaplecza. Upewnij się, że nie ma żadnych reguł o wyższym priorytecie niż AllowAzureLoadBalancerInBound, które zablokują sondę kondycji
  * Można to zrobić, odwiedzając blok sieć maszyn wirtualnych zaplecza lub Virtual Machine Scale Sets
  * Jeśli znajdziesz ten problem sieciowej grupy zabezpieczeń, Przenieś istniejącą regułę Zezwalaj lub Utwórz nową regułę o wysokim priorytecie, aby zezwolić na ruch AzureLoadBalancer
* Sprawdź system operacyjny. Upewnij się, że maszyny wirtualne nasłuchują na porcie sondowania i przejrzyj reguły zapory systemu operacyjnego, aby upewnić się, że nie blokują one ruchu sondowania pochodzącego z adresów IP 168.63.129.16
  * Porty nasłuchiwania można sprawdzić, uruchamiając polecenie netstat — wiersz polecenia systemu Windows lub netstat-l w terminalu z systemem Linux
* Nie umieszczaj maszyny wirtualnej urządzenie WUS na zaporze w puli zaplecza modułu równoważenia obciążenia, użyj [tras zdefiniowanych przez użytkownika](../virtual-network/virtual-networks-udr-overview.md#user-defined) do kierowania ruchu do wystąpień zaplecza za pomocą zapory
* Upewnij się, że używasz odpowiedniego protokołu, jeśli korzystasz z protokołu HTTP do sondowania portu nasłuchiwania aplikacji innych niż HTTP, sonda zakończy się niepowodzeniem

Jeśli użytkownik wykorzystał z tej listy kontrolnej i nadal będzie znajdował błędy sondowania kondycji, mogą występować rzadkie problemy z platformą wpływające na usługę sondowania dla wystąpień. W takim przypadku platforma Azure ma swoją kopię zapasową i zautomatyzowany alert jest wysyłany do naszego zespołu, aby szybko rozwiązać wszystkie problemy z platformą.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat sondy kondycji Azure Load Balancer](load-balancer-custom-probe-overview.md)
* [Dowiedz się więcej o metrykach Azure Load Balancer](load-balancer-standard-diagnostics.md)
