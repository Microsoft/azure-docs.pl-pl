---
title: Rozwiązywanie problemów z połączeniami wychodzącymi w Azure Load Balancer
description: Rozwiązania typowych problemów z łącznością wychodzącą za pośrednictwem Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 1f52900086afef09d69b80bf44474d5514e25235
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748884"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Rozwiązywanie problemów z błędami połączeń wychodzących

Ten artykuł ma na celu zapewnienie rozwiązania typowych problemów, które mogą wystąpić w przypadku połączeń wychodzących z Azure Load Balancer. Większość problemów z łącznością wychodzącą, które klienci odczuwają, jest spowodowana wyczerpaniem portów SNAT i przecięciem limitu czasu połączenia, co prowadzi do porzucania pakietów. Ten artykuł zawiera instrukcje dotyczące ograniczania każdego z tych problemów.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Zarządzanie wyczerpaniem portów SNAT
[Porty efemeracyjne](load-balancer-outbound-connections.md) [](load-balancer-outbound-connections.md) używane na potrzeby patu czasowego są zasobem wyczerpywalnym, zgodnie z opisem w tece Autonomiczna maszyna wirtualna bez publicznego adresu [IP](load-balancer-outbound-connections.md) i Maszyna wirtualna o zrównoważonym obciążeniu bez [publicznego adresu IP.](load-balancer-outbound-connections.md) Korzystając z tego przewodnika, możesz monitorować użycie portów efemeralnych i porównywać je z bieżącą [alokacją,](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) aby określić ryzyko wystąpienia lub potwierdzić wyczerpanie SNAT.

Jeśli wiesz, że inicjujesz wiele wychodzących połączeń TCP lub UDP do tego samego docelowego adresu IP i portu, i obserwujesz niepowodzenie połączeń wychodzących lub zaleca się pomoc techniczną, że wyczerpane są porty SNAT (wstępnie nieprzydzielone porty [efemericzne](load-balancer-outbound-connections.md#preallocatedports) używane przez protokół [PAT),](load-balancer-outbound-connections.md)dostępnych jest kilka ogólnych opcji ograniczania ryzyka. Przejrzyj te opcje i zdecyduj, co jest dostępne i najlepsze dla Twojego scenariusza. Jest możliwe, że co najmniej jeden może pomóc w zarządzaniu tym scenariuszem.

Jeśli masz problemy ze zrozumieniem zachowania połączenia wychodzącego, możesz użyć statystyk stosu adresów IP (netstat). Pomocne może też być obserwowanie zachowania połączenia przy użyciu przechwytywania pakietów. Te przechwycenia pakietów można wykonać w systemach operacyjnych gościa wystąpienia lub użyć Network Watcher [do przechwytywania pakietów.](../network-watcher/network-watcher-packet-capture-manage-portal.md) 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Ręczne przydzielanie portów SNAT w celu zmaksymalizowania portów SNAT na maszynę wirtualną
Zgodnie z [definicją we wstępnie przydzielanych](load-balancer-outbound-connections.md#preallocatedports)portach, równoważenie obciążenia automatycznie przydziela porty na podstawie liczby maszyn wirtualnych w za zaplecza. Domyślnie odbywa się to w sposób zachowawczy w celu zapewnienia skalowalności. Jeśli znasz maksymalną liczbę maszyn wirtualnych, które będziesz mieć w za zaplecza, możesz ręcznie przydzielić porty SNAT w każdej regułie ruchu wychodzącego. Jeśli na przykład wiesz, że będziesz mieć maksymalnie 10 maszyn wirtualnych, możesz przydzielić 6400 portów SNAT na maszynę wirtualną, a nie domyślną liczbę 1024. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modyfikowanie aplikacji w celu ponownego użycia połączeń 
Możesz zmniejszyć zapotrzebowanie na porty efemeracyjne, które są używane na potrzeby SNAT, ponownie korzystając z połączeń w aplikacji. Ponowne użycie połączenia jest szczególnie istotne w przypadku protokołów, takich jak HTTP/1.1, gdzie ponowne użycie połączenia jest ustawieniem domyślnym. Z kolei inne protokoły, które używają protokołu HTTP jako transportu (na przykład REST), mogą skorzystać z zalet. 

Ponowne użycie jest zawsze lepsze niż pojedyncze niepodzielne połączenia TCP dla każdego żądania. Ponowne użycie powoduje bardziej wydajne, bardzo wydajne transakcje TCP.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modyfikowanie aplikacji w celu używania puli połączeń
W aplikacji można stosować schemat puli połączeń, w którym żądania są wewnętrznie dystrybuowane w ramach stałego zestawu połączeń (przy każdym z nich jest to możliwe). Ten schemat ogranicza liczbę portów efemerów w użyciu i tworzy bardziej przewidywalne środowisko. Ten schemat może również zwiększyć przepływność żądań, zezwalając na wiele jednoczesnych operacji, gdy pojedyncze połączenie blokuje odpowiedź operacji.  

Pule połączeń mogą już istnieć w ramach struktury, za pomocą którego opracowywano aplikację, lub w ustawieniach konfiguracji aplikacji. Możesz połączyć pulę połączeń z ponownym użyciem połączenia. Następnie wiele żądań zużywa stałą, przewidywalną liczbę portów dla tego samego docelowego adresu IP i portu. Żądania korzystają również z efektywnego korzystania z transakcji TCP, co zmniejsza opóźnienie i wykorzystanie zasobów. Transakcje UDP mogą również skorzystać z zalet, ponieważ zarządzanie liczbą przepływów UDP może z kolei uniknąć warunków wyczerpania i zarządzać wykorzystaniem portów SNAT.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modyfikowanie aplikacji w celu używania mniej agresywnej logiki ponawiania prób
W przypadku wyczerpania wstępnie nieprzydzielonych [](load-balancer-outbound-connections.md) portów [efemeralnych](load-balancer-outbound-connections.md#preallocatedports) używanych do korzystania z patu lub gdy wystąpią błędy aplikacji, agresywne lub siłowe próby ponownego użycia bez utraty i logiki odroczenia powodują wyczerpanie lub utrwalanie. Możesz zmniejszyć zapotrzebowanie na porty efemeracyjne, używając mniej agresywnej logiki ponawiania prób. 

Porty efemeracyjne mają limit czasu bezczynności na 4 minuty (nie można go dostosowywać). Jeśli ponowne próby są zbyt agresywne, wyczerpanie nie ma możliwości wyczyszczenia samodzielnie. W związku z tym rozważenie sposobu i liczby ponownych prób transakcji w aplikacji jest krytyczną częścią projektu.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Przypisywanie publicznego adresu IP do każdej maszyny wirtualnej
Przypisanie publicznego adresu IP zmienia twój scenariusz na [publiczny adres IP maszyny wirtualnej.](load-balancer-outbound-connections.md) Wszystkie porty efemeracyjne publicznego adresu IP, które są używane dla każdej maszyny wirtualnej, są dostępne dla maszyny wirtualnej. (W przeciwieństwie do scenariuszy, w których efemeralne porty publicznego adresu IP są współdzielone ze wszystkimi maszynami wirtualnych skojarzonymi z odpowiednią pulą zaplecza). Istnieją korzyści, które należy wziąć pod uwagę, takie jak dodatkowy koszt publicznych adresów IP i potencjalny wpływ filtrowania dużej liczby poszczególnych adresów IP.

>[!NOTE] 
>Ta opcja nie jest dostępna dla ról procesu roboczego sieci Web.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Korzystanie z wielu frontendów
W przypadku usługa Load Balancer w warstwie Standardowa adresów sieciowych należy przypisać wiele adresów [IP frontonu](load-balancer-outbound-connections.md) dla połączeń wychodzących i pomnożyć liczbę dostępnych [portów SNAT.](load-balancer-outbound-connections.md#preallocatedports)  Utwórz konfigurację, regułę i pulę zaplecza adresu IP frontendu, aby wyzwolić programowanie SNAT na publicznym adresie IP frontendu.  Reguła nie musi działać, a sonda kondycji nie musi działać pomyślnie.  Jeśli używasz również wielu frontendów dla ruchu przychodzącego (a nie tylko dla ruchu wychodzącego), w celu zapewnienia niezawodności należy używać niestandardowych sond kondycji.

>[!NOTE]
>W większości przypadków wyczerpanie portów SNAT jest oznaką złej konstrukcji.  Przed użyciem większej liczby frontendów do dodawania portów SNAT upewnij się, dlaczego porty są wyczerpane.  Problem może zostać zamaskowany, co może później prowadzić do awarii.

## <a name="scale-out"></a><a name="scaleout"></a>Skalowanie w zewnątrz
[Wstępnie przydzielone](load-balancer-outbound-connections.md#preallocatedports) porty są przypisywane na podstawie rozmiaru puli zaplecza i grupowane w warstwy w celu zminimalizowania zakłóceń, gdy niektóre porty muszą zostać ponownie przydzielone w celu uwzględnienia następnej większej warstwy rozmiaru puli zaplecza.  Możesz mieć możliwość zwiększenia wykorzystania portów SNAT dla danego frontonia przez skalowanie puli zaplecza do maksymalnego rozmiaru dla danej warstwy.  Należy pamiętać, że domyślna alokacja portów jest wymagana do wydajnego skalowania aplikacji bez ryzyka wyczerpania SNAT.

Na przykład dwie maszyny wirtualne w puli zaplecza będą miały 1024 porty SNAT dostępne dla konfiguracji adresów IP, co łącznie pozwala na wdrożenie 2048 portów SNAT.  Jeśli wdrożenie zostałoby zwiększone do 50 maszyn wirtualnych, mimo że liczba wstępnie nieprzydzielonych portów pozostaje stała na maszynę wirtualną, wdrożenie może używać łącznie 51 200 (50 x 1024) portów SNAT.  Jeśli chcesz skalować wdrożenie w poziomie, [](load-balancer-outbound-connections.md#preallocatedports) sprawdź liczbę wstępnie nieprzydzielonych portów na warstwę, aby upewnić się, że skalowanie w poziomie jest maksymalne dla odpowiedniej warstwy.  W poprzednim przykładzie, jeśli wybrano skalowanie w poziomie do 51 zamiast 50 wystąpień, należy przejść do następnej warstwy i zakończyć się mniejszą liczbą portów SNAT na maszynę wirtualną, a także łącznie.

W przypadku skalowania w poziomie do następnej większej warstwy rozmiaru puli zaplecza może być możliwe, że niektóre połączenia wychodzące przejdą w przypadku ponownego przydzielenia przydzielonych portów.  Jeśli używasz tylko niektórych portów SNAT, skalowanie na zewnątrz do następnego większego rozmiaru puli zaplecza jest niesekwencjonalne.  Połowa istniejących portów będzie ponownie lokalizowana przy każdym przechodzeniu do następnej warstwy puli zaplecza.  Jeśli nie chcesz, aby miało to miejsce, musisz ukształtować wdrożenie do rozmiaru warstwy.  Możesz też upewnić się, że aplikacja może wykrywać i ponawiać próby zgodnie z potrzebami.  Keepalives TCP może pomóc w wykrywaniu, gdy porty SNAT nie działają z powodu ponownego alokacji.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Resetowanie limitu czasu bezczynności ruchu wychodzącego przy użyciu funkcji keepalives
W przypadku połączeń wychodzących limit czasu bezczynności wynosi 4 minuty. Ten limit czasu można dostosowywać za pomocą [reguł ruchu wychodzącego.](outbound-rules.md) Możesz również użyć transportu (na przykład keepalives PROTOKOŁU TCP) lub środków utrzymania w warstwie aplikacji, aby odświeżyć bezczynny przepływ i zresetować ten limit czasu bezczynności, jeśli jest to konieczne.  

W przypadku korzystania z funkcji keepalives protokołu TCP wystarczy włączyć je po jednej stronie połączenia. Na przykład wystarczy włączyć je po stronie serwera tylko w celu zresetowania bezczynnego czasomierza przepływu i nie jest konieczne, aby obie strony inicjowały na bieżąco pakiety TCP.  Istnieją podobne pojęcia dotyczące warstwy aplikacji, w tym konfiguracje klient-serwer bazy danych.  Sprawdź po stronie serwera, jakie opcje istnieją dla poszczególnych aplikacji.

## <a name="next-steps"></a>Następne kroki
Zawsze staramy się ulepszyć środowisko naszych klientów. Jeśli występują problemy z łącznością wychodzącą, których nie wymieniono lub które nie zostały rozwiązane w tym artykule, prześlij opinię za pośrednictwem usługi GitHub za pośrednictwem dolnej części tej strony, a my jak najszybciej rozstrzygniemy Twoją opinię.
