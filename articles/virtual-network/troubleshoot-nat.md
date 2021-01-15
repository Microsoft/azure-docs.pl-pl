---
title: Rozwiązywanie problemów z łącznością NAT na platformie Azure Virtual Network
titleSuffix: Azure Virtual Network
description: Rozwiązywanie problemów z Virtual Network translatora adresów sieciowych.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 70410e58acb30c7694e6fe4a6dcaff57bee98607
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223435"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Rozwiązywanie problemów z łącznością NAT na platformie Azure Virtual Network

Ten artykuł ułatwia administratorom diagnozowanie i rozwiązywanie problemów z łącznością podczas korzystania z Virtual Network translatora adresów sieciowych.

## <a name="problems"></a>Problemy

* [Wyczerpanie adresów współlotowych](#snat-exhaustion)
* [Polecenie ping protokołu ICMP kończy się niepowodzeniem](#icmp-ping-is-failing)
* [Błędy łączności](#connectivity-failures)
* [Współistnienie IPv6](#ipv6-coexistence)
* [Połączenie nie pochodzi z adresów IP bramy translatora adresów sieciowych](#connection-doesnt-originate-from-nat-gateway-ips)

Aby rozwiązać te problemy, wykonaj kroki opisane w następnej sekcji.

## <a name="resolution"></a>Rozwiązanie

### <a name="snat-exhaustion"></a>Wyczerpanie adresów współlotowych

Pojedynczy [zasób bramy translatora adresów sieciowych](nat-gateway-resource.md) obsługuje od 64 000 do 1 000 000 współbieżnych przepływów.  Każdy adres IP zapewnia 64 000 portów do dostępnego spisu. Możesz użyć maksymalnie 16 adresów IP na zasób bramy NAT.  Mechanizm podstawcy adresów sieciowych został opisany [tutaj](nat-gateway-resource.md#source-network-address-translation) bardziej szczegółowo.

Często główną przyczyną wyczerpania ruchu związanego z ruchem przychodzącym jest wzorzec dla połączeń wychodzących, zarządzanych lub konfigurowalnych czasomierzy zmieniony z wartości domyślnych.  Uważnie przejrzyj tę sekcję.

#### <a name="steps"></a>Kroki

1. Sprawdź, czy zmodyfikowano domyślny limit czasu bezczynności do wartości większej niż 4 minuty.
2. Zbadaj, w jaki sposób aplikacja tworzy łączność wychodzącą (na przykład przegląd kodu lub przechwycenie pakietu). 
3. Ustal, czy to działanie jest oczekiwane, czy też czy aplikacja jest błędna.  Skorzystaj z [metryk](nat-metrics.md) w Azure monitor, aby uzasadnić swoje ustalenia. Użyj kategorii "Niepowodzenie" dla metryki połączeń z podłączaniem adresów sieciowych.
4. Oceń, czy są obserwowane odpowiednie wzorce.
5. Oceń, czy wyczerpanie portów podadresów IP powinno być skorygowane przy użyciu dodatkowych adresów, przypisanych do zasobu bramy translatora adresów sieciowych.

#### <a name="design-patterns"></a>Wzorce projektowe

Zawsze korzystaj z zalet użycia połączenia i puli połączeń, jeśli to możliwe.  Wzorce te pozwolą uniknąć problemów z wyczerpaniem zasobów i powodować przewidywalne zachowanie. Elementy pierwotne dla tych wzorców można znaleźć w wielu bibliotekach i strukturach programistycznych.

_**Rozwiązanie:**_ Korzystanie z odpowiednich wzorców i najlepszych rozwiązań

- Zasoby bramy NAT mają domyślny limit czasu bezczynności protokołu TCP wynoszący 4 minuty.  Jeśli to ustawienie zostanie zmienione na wyższą wartość, nastąpi przeprowadzenie translacji adresów sieciowych w celu przeprowadzenia dłuższego czasu i może spowodować [niepotrzebne obciążenie spisu portów](nat-gateway-resource.md#timers).
- Żądania niepodzielne (jedno żądanie na połączenie) to słaba opcja projektowania. Takie limity antywzorców są skalowane, zmniejszają wydajność i zmniejszają niezawodność. Zamiast tego należy ponownie użyć połączeń HTTP/S, aby zmniejszyć liczbę połączeń i skojarzonych z nimi portów. Skalowanie aplikacji zwiększy się i poprawi wydajność ze względu na zmniejszone uzgodnienia, obciążenie i koszt operacji kryptograficznej przy korzystaniu z protokołu TLS.
- System DNS może wprowadzić wiele pojedynczych przepływów w woluminie, gdy klient nie buforuje wyniku rozpoznawania nazw DNS. Użyj buforowania.
- Przepływy UDP (na przykład wyszukiwania DNS) przydzielą porty przydziałów adresów sieciowych przez czas trwania bezczynności. Im dłuższy limit czasu bezczynności, tym wyższe ciśnienie w portach źródłowego. Użyj krótkiego limitu czasu bezczynności (na przykład 4 minuty).
- Użyj pul połączeń do kształtowania woluminu połączenia.
- Nigdy nie wolno odrzucać przepływu TCP i polegać na czasomierzach TCP w celu oczyszczenia przepływu. Jeśli nie zezwolisz na bezpośrednie zamknięcie połączenia protokołu TCP, stan pozostanie przydzielony w systemach pośrednich i punktach końcowych, a porty dla innych połączeń są niedostępne. Ten wzorzec może wyzwolić awarie aplikacji i wyczerpanie adresów współdziałania. 
- Nie zmieniaj wartości czasomierza zamknięcia protokołu TCP na poziomie systemu operacyjnego bez specjalistycznej wiedzy o wpływie na nie. Gdy stos TCP zostanie odzyskany, wydajność aplikacji może mieć negatywny wpływ, gdy punkty końcowe połączenia mają niezgodne oczekiwania. Zamiarem zmiany czasomierzy jest zwykle znak podstawowego problemu projektowego. Przejrzyj poniższe zalecenia.

Układ wydechowy można także wzmocnić z innymi antywzorcem w aplikacji źródłowej. Zapoznaj się z tymi dodatkowymi wzorcami i najlepszymi rozwiązaniami, aby zwiększyć skalowalność i niezawodność usługi.

- Zbadaj wpływ zmniejszenia [limitu czasu bezczynności protokołu TCP](nat-gateway-resource.md#timers) na niższe wartości, łącznie z domyślnym limitem czasu bezczynności wynoszącym 4 minuty, aby zwolnić wcześniej spis portów.
- Należy rozważyć [asynchroniczne wzorce sondowania](/azure/architecture/patterns/async-request-reply) dla długotrwałych operacji w celu zwolnienia zasobów połączenia dla innych operacji.
- Przepływy długotrwałe (na przykład ponowne użycie połączeń TCP) powinny korzystać z utrzymywania aktywności protokołu TCP lub warstwy aplikacji, aby uniknąć przekroczenia limitu czasu w systemach pośrednich. Zwiększenie limitu czasu bezczynności jest ostatnim etapem i może nie rozpoznać głównej przyczyny. Długi limit czasu może powodować błędy niskiej szybkości, gdy limit czasu wygasa i wprowadza opóźnienie i niepotrzebne błędy.
- [Wzorce ponowień](/azure/architecture/patterns/retry) należy stosować, aby uniknąć agresywnych ponownych prób/serii podczas przejściowej awarii lub odzyskiwania po awarii.
Tworzenie nowego połączenia TCP dla każdej operacji HTTP (nazywanej również "połączeniami niepodzielnymi") jest antywzorcem.  Połączenia niepodzielne uniemożliwią aplikacji również skalowanie zasobów.  Zawsze potoku wiele operacji w ramach tego samego połączenia.  Twoja aplikacja będzie korzystać z szybkości transakcji i kosztów zasobów.  Gdy aplikacja korzysta z szyfrowania warstwy transportowej (na przykład TLS), istnieje znaczący koszt związany z przetwarzaniem nowych połączeń.  Zapoznaj się ze [wzorcami projektowymi chmury platformy Azure](/azure/architecture/patterns/) , aby uzyskać dodatkowe wzorce najlepszych rozwiązań.

#### <a name="additional-possible-mitigations"></a>Dodatkowe możliwe środki zaradcze

_**Rozwiązanie:**_ Skaluj łączność wychodzącą w następujący sposób:

| Scenariusz | Świadectwa |Ograniczanie ryzyka |
|---|---|---|
| Wystąpiła rywalizacja o porty i wyczerpanie portów przydziałów adresów sieciowych w okresach o wysokim poziomie użycia. | Kategoria "Niepowodzenie" dla [metryki](nat-metrics.md) połączeń protokołu wiązania w Azure monitor pokazuje przejściowe lub trwałe błędy w porównaniu z ilością czasu i dużym połączeniem.  | Ustal, czy można dodać dodatkowe zasoby publicznego adresu IP lub zasobów prefiksu publicznego adresu IP. Spowoduje to dodanie maksymalnie 16 adresów IP do bramy translatora adresów sieciowych. To dodanie spowoduje udostępnienie większej ilości zasobów dla dostępnych portów (64 000 na adres IP) i pozwala na dalsze skalowanie scenariusza.|
| Podano już 16 adresów IP, a nadal wystąpiły wyczerpanie portów. | Próba dodania dodatkowego adresu IP kończy się niepowodzeniem. Łączna liczba adresów IP z zasobów publicznych adresów IP lub zasobów prefiksu publicznego adresu IP przekracza całkowitą liczbę 16. | Dystrybuuj środowisko aplikacji w wielu podsieciach i podaj zasób bramy NAT dla każdej podsieci.  Ponownie Oceń wzorce projektu, aby zoptymalizować je w oparciu o wcześniejsze [wskazówki](#design-patterns). |

>[!NOTE]
>Ważne jest, aby zrozumieć, dlaczego następuje wyczerpanie adresów. Upewnij się, że używasz właściwych wzorców do skalowalnych i niezawodnych scenariuszy.  Dodanie więcej portów do scenariusza bez znajomości przyczyny powinno być ostatnim etapem. Jeśli nie rozumiesz, dlaczego scenariusz stosuje nacisk na spis portów IP, dodanie większej liczby portów do spisu przez dodanie większej liczby adresów IP spowoduje opóźnienie tego samego błędu wyczerpania, co w przypadku skalowania aplikacji.  Mogą być maskowane inne nieefektywność i antywzorce.

### <a name="icmp-ping-is-failing"></a>Polecenie ping protokołu ICMP kończy się niepowodzeniem

[Virtual Network translator adresów sieciowych](nat-overview.md) obsługuje protokoły UDP IPv4 i TCP. Protokół ICMP nie jest obsługiwany i oczekiwany jest błąd.  

_**Rozwiązanie:**_ Zamiast tego należy użyć testów połączeń TCP (na przykład "TCP ping") i testów warstwy aplikacji specyficznych dla protokołu UDP w celu zweryfikowania kompleksowej łączności.

W poniższej tabeli można użyć punktu wyjścia, dla którego narzędzia do uruchomienia testów.

| System operacyjny | Ogólny test połączenia TCP | Test warstwy aplikacji TCP | UDP |
|---|---|---|---|
| Linux | NC (ogólny test połączenia) | zwinięcie (test warstwy aplikacji TCP) | specyficzne dla aplikacji |
| Windows | [PsPing](/sysinternals/downloads/psping) | Wywołanie programu PowerShell [— żądanie WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specyficzne dla aplikacji |

### <a name="connectivity-failures"></a>Błędy łączności

Problemy z łącznością z [Virtual Network translatora adresów sieciowych](nat-overview.md) mogą być spowodowane kilkoma różnymi problemami:

* stałe błędy spowodowane błędami konfiguracji.
* przejściowe lub trwałe [wyczerpanie](#snat-exhaustion) elementu przekroczenia bramy TRANSLATORA adresów sieciowych,
* Błędy przejściowe w infrastrukturze platformy Azure, 
* przejściowe błędy w ścieżce między platformą Azure a publicznym miejscem docelowym Internetu, 
* Błędy przejściowe lub trwałe w publicznym miejscu docelowym Internetu.

Użyj narzędzi, takich jak następujące, aby sprawdzić poprawność łączności. [Polecenie ping protokołu ICMP nie jest obsługiwane](#icmp-ping-is-failing).

| System operacyjny | Ogólny test połączenia TCP | Test warstwy aplikacji TCP | UDP |
|---|---|---|---|
| Linux | NC (ogólny test połączenia) | zwinięcie (test warstwy aplikacji TCP) | specyficzne dla aplikacji |
| Windows | [PsPing](/sysinternals/downloads/psping) | Wywołanie programu PowerShell [— żądanie WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specyficzne dla aplikacji |

#### <a name="configuration"></a>Konfiguracja

Sprawdź konfigurację:
1. Czy zasób bramy translatora adresów sieciowych ma co najmniej jeden zasób publicznego adresu IP lub jeden zasób prefiksu publicznego adresu IP? Aby zapewnić łączność wychodzącą, musisz mieć co najmniej jeden adres IP skojarzony z bramą translatora adresów sieciowych.
2. Czy podsieć sieci wirtualnej jest skonfigurowana do korzystania z bramy translatora adresów sieciowych?
3. Czy używasz UDR (trasa zdefiniowana przez użytkownika) i chcesz zastępowanie miejsca docelowego?  Zasoby bramy NAT stają się domyślną trasą (0/0) w skonfigurowanych podsieciach.

#### <a name="snat-exhaustion"></a>Wyczerpanie adresów współlotowych

Przejrzyj sekcję dotyczącą [wyczerpania adresów](#snat-exhaustion) w tym artykule.

#### <a name="azure-infrastructure"></a>Infrastruktura platformy Azure

Platforma Azure monitoruje i współpracuje z infrastrukturą. Mogą wystąpić błędy przejściowe, nie ma gwarancji, że transmisje są bezstratne.  Użyj wzorców projektowych, które pozwalają na retransmisję SYN dla aplikacji TCP. Limity czasu połączenia są wystarczająco duże, aby zezwolić na ponowną transmisję protokołu TCP SYN w celu ograniczenia przejściowych wpływów spowodowanych przez utracony pakiet SYN.

_**Narzędzie**_

* Sprawdź, czy jest [wyczerpany wyczerpanie adresów](#snat-exhaustion).
* Parametr konfiguracji w stosie protokołu TCP kontrolujący zachowanie retransmisji SYN ma nazwę RTO ([limit czasu retransmisji](https://tools.ietf.org/html/rfc793)). Wartość RTO jest ustawiana, ale zazwyczaj jest domyślnie 1 sekunda lub wyższa, z możliwością wycofywania wykładniczego.  Jeśli limit czasu połączenia aplikacji jest za krótki (na przykład 1 s), można zobaczyć sporadyczne limity czasu połączenia.  Zwiększ limit czasu połączenia aplikacji.
* Jeśli obserwujesz dłużej, nieoczekiwane limity czasu przy użyciu domyślnych zachowań aplikacji, Otwórz przypadek pomocy technicznej w celu dalszego rozwiązywania problemów.

Nie zalecamy sztucznego zmniejszania limitu czasu połączenia TCP ani dostrajania parametru RTO.

#### <a name="public-internet-transit"></a>Publiczne tranzyt internetowy

Szanse na błędy przejściowe zwiększają się o dłuższą ścieżkę do miejsca docelowego i większej liczby systemów pośrednich. Oczekuje się, że przejściowe błędy mogą zwiększyć częstotliwość w przypadku [infrastruktury platformy Azure](#azure-infrastructure). 

Postępuj zgodnie z tymi samymi wskazówkami jak w poprzedniej sekcji [infrastruktury platformy Azure](#azure-infrastructure) .

#### <a name="internet-endpoint"></a>Internetowy punkt końcowy

Poprzednie sekcje dotyczą programu wraz z internetowym punktem końcowym, z którym nawiązuje się komunikacja. Inne czynniki, które mogą mieć wpływ na łączność, są następujące:

* zarządzanie ruchem na stronie docelowej, w tym
- Ograniczanie szybkości interfejsu API nakładane po stronie docelowej
- Pomiarowe środki zaradcze DDoS lub transkształtowanie ruchu warstwy transportowej
* Zapora lub inne składniki w miejscu docelowym 

Zwykle przechwycenia pakietu w lokalizacji źródłowej i docelowej (jeśli jest dostępna) jest wymagana do określenia, co ma miejsce.

_**Narzędzie**_

* Sprawdź, czy jest [wyczerpany wyczerpanie adresów](#snat-exhaustion). 
* Sprawdź poprawność łączności z punktem końcowym w tym samym regionie lub w innym miejscu do porównania.  
* Jeśli tworzysz testy dotyczące dużej ilości pamięci lub transakcji, zbadaj, czy zmniejszenie szybkości zmniejsza liczbę błędów.
* Jeśli zmiana szybkości ma wpływ na liczbę błędów, sprawdź, czy osiągnięto limity szybkości interfejsu API lub inne ograniczenia na stronie docelowej.
* Jeśli badanie jest niejednoznaczne, Otwórz zgłoszenie do pomocy technicznej, aby uzyskać dalsze Rozwiązywanie problemów.

#### <a name="tcp-resets-received"></a>Odebrane resety TCP

Brama translatora adresów sieciowych generuje resety TCP na źródłowej maszynie wirtualnej dla ruchu, który nie jest rozpoznawany jako w toku.

Jedną z możliwych przyczyn jest to, że połączenie TCP ma limit czasu bezczynności.  Limit czasu bezczynności można dostosować z 4 minut do 120 minut.

Resetowanie protokołu TCP nie jest generowane po stronie publicznej zasobów bramy translatora adresów sieciowych. Resetowanie protokołu TCP po stronie docelowej jest generowane przez źródłową maszynę wirtualną, a nie zasób bramy translatora adresów sieciowych.

_**Narzędzie**_

* Przejrzyj zalecenia dotyczące [wzorców projektowych](#design-patterns) .  
* Aby uzyskać dalsze Rozwiązywanie problemów, w razie potrzeby należy otworzyć zgłoszenie do pomocy technicznej.

### <a name="ipv6-coexistence"></a>Współistnienie IPv6

[Virtual Network translator adresów sieciowych](nat-overview.md) obsługuje protokoły UDP IPv4 i TCP oraz wdrożenie w [podsieci z prefiksem IPv6 nie jest obsługiwane](nat-overview.md#limitations).

_**Rozwiązanie:**_ Wdróż bramę translatora adresów sieciowych w podsieci bez prefiksu IPv6.

Możesz wskazać zainteresowanie dodatkowymi możliwościami za pośrednictwem [Virtual Network translatora adresów sieciowych](https://aka.ms/natuservoice)w usłudze UserVoice.

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>Połączenie nie pochodzi z adresów IP bramy translatora adresów sieciowych

Należy skonfigurować bramę translatora adresów sieciowych, adresy IP do użycia i podsieć, dla której ma być używany zasób bramy translatora adresów sieciowych. Jednak połączenia z wystąpień maszyn wirtualnych, które istniały przed wdrożeniem bramy translatora adresów sieciowych, nie używają adresów IP.  Wydaje się, że używają adresów IP, które nie są używane z zasobem bramy translatora adresów sieciowych.

_**Narzędzie**_

[Virtual Network translator adresów sieciowych](nat-overview.md) zastępuje łączność wychodzącą dla podsieci, w której jest skonfigurowana. W przypadku przejścia z domyślnego lub przychodzącego ruchu wychodzącego modułu równoważenia obciążenia do korzystania z bram translatora adresów sieciowych nowe połączenia będą od razu rozpoczynać korzystanie z adresów IP skojarzonych z zasobem bramy translatora adresów sieciowych.  Jeśli jednak maszyna wirtualna nadal ma ustanowione połączenie podczas przełączania do zasobu bramy NAT, połączenie będzie kontynuowane przy użyciu starego adresu IP, który został przypisany podczas ustanawiania połączenia.  Przed ponownym użyciem połączenia, które już istniało, należy się upewnić, że w systemie operacyjnym lub przeglądarce buforowanie połączeń w puli połączeń jest naprawdę nawiązywane.  Na przykład _podczas korzystania z_ zapełnień w programie PowerShell należy określić parametr _-DisableKeepalive_ , aby wymusić nowe połączenie.  Jeśli używasz przeglądarki, połączenia mogą również być w puli.

Nie jest konieczne ponowne uruchomienie maszyny wirtualnej w celu skonfigurowania podsieci dla zasobu bramy translatora adresów sieciowych.  Jeśli jednak maszyna wirtualna jest ponownie uruchamiana, stan połączenia jest opróżniany.  Gdy stan połączenia został opróżniony, wszystkie połączenia rozpoczną korzystanie z adresów IP (y) zasobów bramy translatora adresów sieciowych.  Jest to jednak efekt uboczny maszyny wirtualnej, która jest uruchamiana ponownie, a nie wskaźnik, że wymagane jest ponowne uruchomienie.

Jeśli nadal występują problemy, otwórz sprawę pomocy technicznej, aby uzyskać dalsze Rozwiązywanie problemów.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Virtual Network translatora adresów sieciowych](nat-overview.md)
* Informacje o [zasobie bramy translatora adresów sieciowych](nat-gateway-resource.md)
* Informacje o [metrykach i alertach dotyczących zasobów bramy translatora adresów sieciowych](nat-metrics.md).
* [Powiedz nam, co należy utworzyć obok Virtual Network translatora adresów sieciowych w usłudze UserVoice](https://aka.ms/natuservoice).