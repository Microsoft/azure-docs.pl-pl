---
title: Rozwiązywanie problemów z połączeniami wychodzącymi w Azure Load Balancer
description: Rozwiązania typowych problemów z łącznością wychodzącą za pośrednictwem Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: c37c0e9b914854ff41053526740d3454c5c23f90
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628999"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Rozwiązywanie problemów z błędami połączeń wychodzących

Ten artykuł ma na celu umożliwienie rozwiązywania typowych problemów z połączeniami wychodzącymi z Azure Load Balancer. Większość problemów z łącznością wychodzącą, którą zapewnia klient, wynika z wyczerpania portów i limitów czasu połączenia prowadzących do porzucenia pakietów. W tym artykule przedstawiono procedurę łagodzenia poszczególnych problemów.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Zarządzanie wyczerpaniem portów (z)
[Porty](load-balancer-outbound-connections.md) tymczasowe [używane do](load-balancer-outbound-connections.md) nadania to zasób exhaustible, zgodnie z opisem w [autonomicznej maszynie wirtualnej bez publicznego adresu IP](load-balancer-outbound-connections.md) i [maszyny wirtualnej ze zrównoważonym obciążeniem bez publicznego adresu IP](load-balancer-outbound-connections.md). Możesz monitorować użycie portów tymczasowych i porównać z bieżącą alokacją, aby określić ryzyko dla lub potwierdzić wyczerpanie adresów w [tym](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) przewodniku.

Jeśli wiesz, że masz wiele wychodzących połączeń TCP lub UDP z tym samym docelowym adresem IP i portem, a następnie zauważysz, że połączenia wychodzące są zakończone niepowodzeniem lub są zalecane w przypadku wyczerpania [portów przydzielonego](load-balancer-outbound-connections.md#preallocatedports) [dostępu do danych](load-balancer-outbound-connections.md)wyjściowych, masz kilka ogólnych opcji zaradczych. Zapoznaj się z tymi opcjami i zdecyduj, co jest dostępne i najlepsze dla Twojego scenariusza. Istnieje możliwość, że co najmniej jedna może pomóc w zarządzaniu tym scenariuszem.

Jeśli masz problemy z zrozumieniem zachowania połączenia wychodzącego, możesz użyć statystyk stosu IP (netstat). Może być również pomocne w obserwowanie zachowań połączenia za pomocą przechwytywania pakietów. Można wykonać te przechwycenia pakietu w systemie operacyjnym gościa wystąpienia lub użyć [Network Watcher do przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Ręcznie Przydziel porty podrzędnego TRANSLATORa adresów sieciowych na maszynę wirtualną
Zgodnie z definicją w wstępnie [przydzielone porty](load-balancer-outbound-connections.md#preallocatedports)moduł równoważenia obciążenia automatycznie przydzieli porty na podstawie liczby maszyn wirtualnych w zapleczu. Domyślnie jest to bardziej ostrożne w celu zapewnienia skalowalności. Jeśli znasz maksymalną liczbę maszyn wirtualnych, które będą znajdować się w zapleczu, możesz ręcznie przydzielić porty w ramach każdej reguły ruchu wychodzącego. Na przykład, Jeśli wiesz, że masz maksymalnie 10 maszyn wirtualnych, możesz przydzielić 6 400 porty na maszynę wirtualną, a nie domyślną 1 024. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modyfikowanie aplikacji w celu ponownego użycia połączeń 
Można zmniejszyć zapotrzebowanie na porty tymczasowe, które są używane w przypadku usługi reportcy, przez ponowną obsługę połączeń w aplikacji. Ponowne użycie połączenia jest szczególnie przydatne w przypadku protokołów takich jak HTTP/1.1, w przypadku których użycie połączenia jest domyślne. Inne protokoły używające protokołu HTTP jako transportu (na przykład REST) mogą korzystać z tej usługi. 

Ponowne użycie jest zawsze lepsze niż pojedyncze, niepodzielne połączenia TCP dla każdego żądania. Wielokrotne użycie powoduje skuteczniejsze, bardzo wydajne transakcje TCP.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modyfikowanie aplikacji tak, aby używała puli połączeń
Można użyć schematu puli połączeń w aplikacji, w którym żądania są dystrybuowane wewnętrznie w ramach ustalonego zestawu połączeń (z każdym z nich, gdzie to możliwe). Ten schemat ogranicza liczbę używanych portów tymczasowych i tworzy bardziej przewidywalne środowisko. Ten schemat może również zwiększyć przepływność żądań przez umożliwienie wielu równoczesnych operacji, gdy jedno połączenie blokuje odpowiedzi na odpowiedź operacji.  

Pule połączeń mogą już istnieć w ramach platformy, która jest używana do tworzenia aplikacji lub ustawień konfiguracji aplikacji. Pulę połączeń można połączyć z wielokrotnym użyciem połączenia. Wiele żądań korzysta z ustalonej, przewidywalnej liczby portów na ten sam docelowy adres IP i port. Żądania korzystają również z efektywnego wykorzystania transakcji TCP redukujących opóźnienia i wykorzystanie zasobów. Transakcje UDP mogą również być korzystne, ponieważ zarządzanie liczbą przepływów UDP może mieć na celu uniknięcie przekroczenia warunków i zarządzanie wykorzystaniem portów przystawek.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modyfikowanie aplikacji tak, aby była używana mniej agresywna logika ponawiania
Gdy [wstępnie alokowany porty](load-balancer-outbound-connections.md#preallocatedports) tymczasowe są używane na potrzeby odbierania [lub występują błędy](load-balancer-outbound-connections.md) aplikacji, agresywne lub bezproblemowe wymuszanie ponawiania prób bez pozostałego działania lub logiki wycofywania. Możesz zmniejszyć zapotrzebowanie na tymczasowe porty przy użyciu mniej agresywnej logiki ponawiania. 

Porty tymczasowe mają 4-minutowy limit czasu bezczynności (nieregulowane). Jeśli ponowna próba jest zbyt agresywna, wyczerpanie nie ma możliwości samodzielnego wyczyszczenia. W związku z tym rozważać, jak i jak często — transakcje ponawiania prób aplikacji są istotną częścią projektu.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Przypisz publiczny adres IP do każdej maszyny wirtualnej
Przypisanie publicznego adresu IP zmienia Twój Scenariusz na [publiczny adres IP na maszynę wirtualną](load-balancer-outbound-connections.md). Wszystkie tymczasowe porty publicznego adresu IP, które są używane dla każdej maszyny wirtualnej, są dostępne dla maszyny wirtualnej. (W przeciwieństwie do scenariuszy, w których porty tymczasowe publicznego adresu IP są udostępniane wszystkim maszynom wirtualnym skojarzonym z odpowiednią pulą zaplecza). Istnieją kompromisy, które należy wziąć pod uwagę, takie jak dodatkowe koszty publicznych adresów IP i potencjalny wpływ filtrowania dużej liczby pojedynczych adresów IP.

>[!NOTE] 
>Ta opcja nie jest dostępna dla ról procesów roboczych sieci Web.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Korzystanie z wielu frontonów
W przypadku korzystania z publicznej usługa Load Balancer w warstwie Standardowa należy przypisać [wiele adresów IP frontonu dla połączeń wychodzących](load-balancer-outbound-connections.md) i [pomnożyć liczbę dostępnych portów](load-balancer-outbound-connections.md#preallocatedports).  Utwórz konfigurację adresu IP frontonu, regułę i pulę zaplecza, aby wyzwolić programowanie do publicznego adresu IP frontonu.  Reguła nie musi działać i sonda kondycji nie musi się powieść.  Jeśli używasz również wielu frontonów dla ruchu przychodzącego (a nie tylko dla ruchu wychodzącego), użyj niestandardowych sond kondycji, aby zapewnić niezawodność.

>[!NOTE]
>W większości przypadków wyczerpanie portów z podsiecią adresów sieciowych jest znakiem nieprawidłowego projektu.  Upewnij się, że rozumiesz, dlaczego są wyczerpani portów przed użyciem większej liczby frontonów do dodawania portów.  Może wystąpić problem z maską problemu, który może prowadzić do późniejszego błędu.

## <a name="scale-out"></a><a name="scaleout"></a>Skalowanie w poziomie
Wstępnie [przydzielone porty](load-balancer-outbound-connections.md#preallocatedports) są przypisywane w oparciu o rozmiar puli zaplecza i pogrupowane w warstwy, aby zminimalizować zakłócenia, gdy niektóre porty muszą zostać ponownie przydzielone w celu uwzględnienia kolejnej większej warstwy rozmiaru puli zaplecza.  Może istnieć możliwość zwiększenia wykorzystania portów dla danego frontonu przez przeskalowanie puli zaplecza do maksymalnego rozmiaru dla danej warstwy.  Należy pamiętać, że domyślna alokacja portów jest wymagana, aby aplikacja mogła wydajnie skalować się bez ryzyka wyczerpania.

Na przykład dwie maszyny wirtualne w puli zaplecza będą mieć 1024 portów protokołu reportowego dla każdej konfiguracji IP, co pozwoli na całkowite porty przyłączone do 2048 dla wdrożenia.  Jeśli wdrożenie zostało zwiększone do 50 maszyn wirtualnych, mimo że liczba wstępnie przydzielonych portów pozostaje stała na maszynę wirtualną, wdrożenie może być używane przez łącznie 51 200 (50 x 1024) portów.  Jeśli chcesz skalować wdrożenie, sprawdź liczbę wstępnie [przydzieloną portów](load-balancer-outbound-connections.md#preallocatedports) na warstwę, aby upewnić się, że skalujesz skalę do wartości maksymalnej dla odpowiedniej warstwy.  W poprzednim przykładzie, jeśli wybrano opcję skalowania w poziomie do 51 zamiast wystąpień 50, należy postępować do następnej warstwy i zakończyć z mniejszą liczbą portów przystawek adresów sieciowych na maszynę wirtualną, a także łącznie.

W przypadku skalowania w poziomie do następnej większej warstwy rozmiaru puli zaplecza dla niektórych połączeń wychodzących istnieje możliwość przekroczenia limitu czasu w przypadku konieczności ponownej alokowania przyznanych portów.  Jeśli używasz tylko niektórych portów podzestawów adresów sieciowych, skalowanie w poziomie w następnym większym rozmiarze puli zaplecza jest nieaktualne.  Połowa istniejących portów zostanie przydzielona przy każdym przejściu do następnej warstwy puli zaplecza.  Jeśli nie chcesz, aby to zrobić, musisz pomieścić wdrożenie w rozmiarze warstwy.  Lub upewnij się, że aplikacja może wykryć i ponowić próbę w razie potrzeby.  Utrzymywanie aktywności protokołu TCP może pomóc w wykrywaniu, kiedy porty protokołu reportowego już nie działają z powodu ich ponownie przydzielenia.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Aby zresetować limit czasu bezczynności dla ruchu wychodzącego, użyj utrzymywania aktywności
Połączenia wychodzące mają 4-minutowy limit czasu bezczynności. Ten limit czasu jest dostosowywany za pośrednictwem [reguł ruchu wychodzącego](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout). W razie potrzeby można również użyć transportu (na przykład aktywności protokołu TCP) lub warstwy aplikacji, aby odświeżyć przepływ bezczynności i zresetować ten limit czasu bezczynności.  

W przypadku korzystania z funkcji utrzymywania aktywności protokołu TCP wystarczy włączyć je po jednej stronie połączenia. Na przykład wystarczy włączyć je po stronie serwera tylko w celu zresetowania czasomierza bezczynności przepływu i nie jest konieczne dla obu stron w celu zainicjowania obsługi protokołu TCP.  Podobne pojęcia istnieją dla warstwy aplikacji, w tym konfiguracje klienta bazy danych.  Sprawdź po stronie serwera, jakie opcje istnieją dla nieaktywności związanych z aplikacjami.

## <a name="next-steps"></a>Następne kroki
Zawsze staramy się ulepszyć środowisko naszych klientów. Jeśli występują problemy z łącznością wychodzącą, której nie ma na liście lub nie zostały rozwiązane w tym artykule, Prześlij opinię za pośrednictwem usługi GitHub w dolnej części tej strony, a my będziemy zająć się opinią tak szybko, jak to możliwe.
