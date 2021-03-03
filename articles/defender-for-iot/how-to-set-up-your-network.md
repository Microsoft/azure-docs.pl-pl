---
title: Konfigurowanie sieci
description: Dowiedz się więcej o architekturze rozwiązań, przygotowaniu sieci, wymaganiach wstępnych i innych informacjach wymaganych do pomyślnej konfiguracji sieci do pracy z usługą Azure Defender dla urządzeń IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 02/18/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 0f85eebbfa8fcdfd9ad6e31a564f27b5d9bfbdfc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733248"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>Informacje o usłudze Azure Defender dla sieci IoT — konfiguracja

Usługa Azure Defender for IoT oferuje ciągłe monitorowanie zagrożeń i odnajdywanie na urządzeniach. Platforma obejmuje następujące składniki:

Usługa **Defender dla czujników IoT:** Czujniki zbierają ruch sieciowy usługi ICS przy użyciu pasywnego monitorowania (bez agentów). Pasywne i nieinwazyjne czujniki nie mają wpływu na wydajność i sieci i urządzenia IoT. Czujnik nawiązuje połączenie z portem SPAN lub NACIŚNIĘCIem sieci i natychmiast rozpoczyna monitorowanie sieci. Wykrycia są wyświetlane w konsoli czujnika. W tym miejscu możesz wyświetlać, badać i analizować je w mapie sieciowej, spisie urządzeń i wielu różnych raportach. Przykłady obejmują raporty oceny ryzyka, zapytania wyszukiwania danych i wektory ataków. 

Usługa **Defender dla lokalnej konsoli zarządzania usługi IoT**: lokalna Konsola zarządzania udostępnia skonsolidowany widok wszystkich urządzeń sieciowych. W czasie rzeczywistym są wyświetlane kluczowe wskaźniki ryzyka związane z niektórymi i alertami oraz alerty dotyczące wszystkich Twoich obiektów. Ścisła integracja z przepływami pracy SOC i elementy PlayBook umożliwia łatwe określanie priorytetów działań zaradczych oraz korelację zagrożeń między lokacjami. 

**Portal usługi Defender dla IoT:** Aplikacja Defender for IoT może pomóc zakupić urządzenia rozwiązania, zainstalować i zaktualizować oprogramowanie oraz zaktualizować pakiety analizy czasowej. 

Ten artykuł zawiera informacje o architekturze rozwiązania, przygotowaniu sieci, wymaganiach wstępnych i innych ułatwieniach w pomyślnym skonfigurowaniu sieci do pracy z usługą Defender dla urządzeń IoT. Czytelnicy korzystający z informacji zawartych w tym artykule powinni mieć doświadczenie w działaniu i zarządzaniu niektórymi sieciami i IoT. Przykłady obejmują inżynierów automatyzacji, menedżerów zakładów, usługodawców infrastruktury sieciowej, zespołów cyberbezpieczeństwa, CISOs lub dyrektorzy działu informatyki.

Aby uzyskać pomoc lub pomoc techniczną, skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="on-site-deployment-tasks"></a>Zadania wdrażania w lokacji

Zadania wdrażania lokacji obejmują:

- [Zbieranie informacji o lokacji](#collect-site-information)

- [Przygotowywanie stacji roboczej konfiguracji](#prepare-a-configuration-workstation)

- [Planowanie instalacji stojaka](#planning-rack-installation)

### <a name="collect-site-information"></a>Zbieranie informacji o lokacji

Rejestrowanie informacji o witrynie, takich jak:

- Informacje o sieci zarządzania czujnikami.

- Architektura sieci lokacji.

- Środowisko fizyczne.

- Integracje systemu.

- Poświadczenia planowanego użytkownika.

- Konfiguracja stacji roboczej.

- Certyfikaty SSL (opcjonalne, ale zalecane).

- Uwierzytelnianie SMTP (opcjonalne). Aby użyć serwera SMTP z uwierzytelnianiem, przygotuj poświadczenia wymagane dla serwera.

- Serwery DNS (opcjonalnie). Przygotuj adres IP i nazwę hosta serwera DNS.

Aby uzyskać szczegółową listę i opis ważnych informacji o lokacji, zobacz [Przykładowa książka witryn](#example-site-book).

#### <a name="successful-monitoring-guidelines"></a>Wytyczne dotyczące monitorowania pomyślne

Aby znaleźć optymalne miejsce do połączenia urządzenia w każdej z sieci produkcyjnych, zalecamy wykonanie poniższej procedury:

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="Przykład diagramu konfiguracji sieci produkcyjnej.":::

### <a name="prepare-a-configuration-workstation"></a>Przygotowywanie stacji roboczej konfiguracji

Przygotuj stację roboczą z systemem Windows, w tym następujące:

- Łączność z interfejsem zarządzania czujnikami.

- Obsługiwana przeglądarka

- Oprogramowanie terminalowe, np..

Upewnij się, że na stacji roboczej są otwarte wymagane reguły zapory. Aby uzyskać szczegółowe informacje, zobacz [wymagania dotyczące dostępu do sieci](#network-access-requirements) .

#### <a name="supported-browsers"></a>Obsługiwane przeglądarki

Następujące przeglądarki są obsługiwane w przypadku aplikacji sieci Web czujników i lokalnych konsoli zarządzania:

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10 +

### <a name="network-access-requirements"></a>Wymagania dotyczące dostępu do sieci

Sprawdź, czy zasady zabezpieczeń organizacji umożliwiają dostęp do następujących danych:

| Protokół | Transport | We/Wy | Port | Użyte | Przeznaczenie | Element źródłowy | Element docelowy |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | WE/WYCHODZĄCE | 443 | Konsola sieci Web czujnika i lokalnej konsoli zarządzania | Dostęp do konsoli sieci Web | Klient | Czujnik i lokalna Konsola zarządzania |
| Protokół SSH | TCP | WE/WYCHODZĄCE | 22 | Interfejs wiersza polecenia | Dostęp do interfejsu wiersza polecenia | Klient | Czujnik i lokalna Konsola zarządzania |
| Protokół SSL | TCP | WE/WYCHODZĄCE | 443 | Czujnik i lokalna Konsola zarządzania | Połączenie między platformą CyberX a centralną platformą zarządzania | czujnik | Lokalna Konsola zarządzania |
| NTP | UDP | IN | 123 | Synchronizacja czasu | Lokalna Konsola zarządzania używana jako NTP do czujnika | czujnik | lokalna Konsola zarządzania |
| NTP | UDP | WE/WYCHODZĄCE | 123 | Synchronizacja czasu | Czujnik połączony z zewnętrznym serwerem NTP, gdy nie ma zainstalowanej lokalnej konsoli zarządzania | czujnik | NTP |
| SMTP | TCP | OKREŚLONĄ | 25 | E-mail | Połączenie między platformą CyberX a platformą zarządzania i serwerem poczty | Czujnik i lokalna Konsola zarządzania | Serwer poczty e-mail |
| Dziennik systemu | UDP | OKREŚLONĄ | 514 | LEEF | Dzienniki wysyłane z lokalnej konsoli zarządzania do serwera dziennika systemowego | Lokalna Konsola zarządzania i czujnik | Serwer Syslog |
| DNS |  | WE/WYCHODZĄCE | 53 | DNS | Port serwera DNS | Lokalna Konsola zarządzania i czujnik | Serwer DNS |
| LDAP | TCP | WE/WYCHODZĄCE | 389 | Active Directory | Połączenie między platformą CyberX a platformą zarządzania Active Directory | Lokalna Konsola zarządzania i czujnik | Serwer LDAP |
| PROTOKOŁU LDAPS | TCP | WE/WYCHODZĄCE | 636 | Active Directory | Połączenie między platformą CyberX a platformą zarządzania Active Directory | Lokalna Konsola zarządzania i czujnik | Serwer LDAPs |
| SNMP | UDP | OKREŚLONĄ | 161 | Monitorowanie | Zdalne moduły zbierające protokołu SNMP. | Lokalna Konsola zarządzania i czujnik | Serwer SNMP |
| WMI | UDP | OKREŚLONĄ | 135 | monitorowanie | Monitorowanie punktów końcowych systemu Windows | Czujnik | Odpowiedni element sieci |
| Tunelowanie | TCP | IN | 9000 <br /><br />-na szczycie portu 443 <br /><br />Od użytkownika końcowego do lokalnej konsoli zarządzania. <br /><br />-Port 22 z czujnika do lokalnej konsoli zarządzania  | monitorowanie | Tunelowanie | Czujnik | Lokalna Konsola zarządzania |

### <a name="planning-rack-installation"></a>Planowanie instalacji stojaka

Aby zaplanować instalację stojaka:

1. Przygotuj monitor i klawiaturę dla ustawień sieci urządzenia.

1. Przydziel miejsce w stojaku dla urządzenia.

1. Dostępna jest energia AC dla urządzenia.
1. Przygotuj kabel sieci LAN do łączenia zarządzania z przełącznikiem sieci.
1. Przygotuj kable sieci LAN do łączenia portów przedziału (dublowania) i przełączania sieci z urządzeniem Defender for IoT. 
1. Skonfiguruj, Połącz i Weryfikuj zakresy portów w przełącznikach dublowanych zgodnie z opisem w sesji przeglądu architektury.
1. Połącz skonfigurowany port SPAN z komputerem z uruchomionym programem Wireshark i sprawdź, czy port jest prawidłowo skonfigurowany.
1. Otwórz wszystkie odpowiednie porty zapory.

## <a name="about-passive-network-monitoring"></a>Informacje o monitorowaniu sieci pasywnych

Urządzenie odbiera ruch z wielu źródeł, przełączając porty dublowane (obejmujące porty) lub naciskając sieci. Port zarządzania jest podłączony do sieci zarządzania w firmie, firmowej lub czujnika z łącznością z lokalną konsolą zarządzania lub portalem usługi Defender for IoT.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="Diagram zarządzanego przełącznika z funkcją dublowania portów.":::

### <a name="purdue-model"></a>Model Purdue

W poniższych sekcjach opisano poziomy Purdue.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Diagram modelu Purdue.":::

####  <a name="level-0-cell-and-area"></a>Poziom 0: komórka i obszar  

Poziom 0 obejmuje szeroką gamę czujników, siłowników i urządzeń, które są wykorzystywane w podstawowym procesie produkcyjnym. Te urządzenia wykonują podstawowe funkcje systemu automatyzacji i kontroli przemysłowej, takie jak:

- Napędzanie silnika.

- Mierzenie zmiennych.
- Ustawianie danych wyjściowych.
- Wykonywanie kluczowych funkcji, takich jak malowanie, spawanie i zginanie.

#### <a name="level-1-process-control"></a>Poziom 1: sterowanie procesem

Poziom 1 składa się z kontrolerów osadzonych, które kontrolują i manipulują procesem produkcyjnym, którego kluczową funkcją jest komunikacja z poziomu 0 urządzeń. W przypadku produkcji dyskretnej te urządzenia są programowalnymi kontrolerami logiki (PLCs) lub zdalnymi jednostkami telemetrii (RTUs). W procesie produkcji kontroler podstawowy nosi nazwę systemu kontroli rozproszonej (DC).

#### <a name="level-2-supervisory"></a>Poziom 2: nadzorcze

Poziom 2 reprezentuje systemy i funkcje związane z nadzorem środowiska uruchomieniowego i działaniem obszaru produkcyjnego. Zwykle są to następujące elementy: 

- Interfejsy operatora lub HMIs

- Alarmy lub systemy ostrzegania

- Przetwarzanie Historian i systemów zarządzania partiami

- Stacje robocze do sterowania

Te systemy komunikują się z PLCs i RTUs na poziomie 1. W niektórych przypadkach komunikują się oni lub udostępniają dane w systemach i aplikacjach (poziom 4 i 5). Te systemy są głównie oparte na standardowych urządzeniach obliczeniowych i systemach operacyjnych (UNIX lub Microsoft Windows).

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>Poziomy 3 i 3,5: sieć obwodowa na poziomie lokacji i przemysłowych

Poziom lokacji reprezentuje najwyższy poziom automatyzacji i systemów kontroli przemysłowej. Systemy i aplikacje, które istnieją na tym poziomie, zarządzają automatyzacją i funkcjami kontroli przemysłowej w całej lokacji. Poziomy od 0 do 3 są uznawane za krytyczne dla operacji lokacji. Systemy i funkcje, które istnieją na tym poziomie mogą obejmować następujące elementy:

- Raportowanie produkcyjne (na przykład czasy cykli, indeks jakości, konserwacja predykcyjna)

- Historian zakładu

- Szczegółowe planowanie produkcji

- Zarządzanie operacjami na poziomie witryny

- Zarządzanie urządzeniami i materiałami

- Serwer uruchamiania poprawek

- Serwer plików

- Domena przemysłowa, Active Directory, serwer terminali

Te systemy komunikują się ze strefą produkcyjną i współdzielą dane z systemami i aplikacjami przedsiębiorstwa (poziom 4 i 5).  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>Poziomy 4 i 5: sieci biznesowe i korporacyjne

Poziom 4 i poziom 5 reprezentują lokację lub sieć przedsiębiorstwa, w której istnieją Centralne systemy i funkcje IT. Organizacja IT bezpośrednio zarządza usługami, systemami i aplikacjami na tych poziomach.

## <a name="planning-for-network-monitoring"></a>Planowanie monitorowania sieci

Poniższe przykłady przedstawiają różne typy topologii dla sieci kontroli przemysłowej wraz z zagadnieniami dotyczącymi optymalnego monitorowania i rozmieszczenia czujników.

### <a name="what-should-be-monitored"></a>Co powinno być monitorowane?

Należy monitorować ruch, który przechodzi przez warstwy 1 i 2.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>Jak ma nawiązywać połączenie z usługą Defender dla urządzenia IoT?

Urządzenie Defender for IoT powinno łączyć się z przełącznikami zarządzanymi, które widzą komunikację branżową między warstwami 1 i 2 (w niektórych przypadkach również warstwy 3).

Poniższy diagram jest ogólnym abstrakcją Multilayer sieci wielodostępnej, a ekosystem rozległych cyberbezpieczeństwa jest obsługiwany przez SOC i MSSP.

Zazwyczaj czujniki NTA są wdrażane w warstwach od 0 do 3 modelu OSI.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="Diagram modelu OSI.":::

#### <a name="example-ring-topology"></a>Przykład: topologia pierścienia

Sieć pierścieniowa jest topologią, w której każdy przełącznik lub węzeł łączy się dokładnie z dwoma innymi przełącznikami, tworząc pojedynczą ciągłą transpozycję dla ruchu.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="Diagram topologii pierścieniowej.":::

#### <a name="example-linear-bus-and-star-topology"></a>Przykład: Magistrala liniowa i topologia gwiazdy

W sieci gwiazdy każdy host jest połączony z koncentratorem centralnym. W najprostszej formie jedno centralne centrum działa jako przewód do przesyłania komunikatów. W poniższym przykładzie niższe przełączniki nie są monitorowane, a ruch, który pozostanie lokalny dla tych przełączników, nie będzie widoczny. Urządzenia mogą być identyfikowane na podstawie komunikatów ARP, ale nie ma informacji o połączeniu.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="Diagram magistrali liniowej i topologii gwiazdy.":::

#### <a name="multisensor-deployment"></a>Wdrożenie wieloczujnikowe

Poniżej przedstawiono kilka zaleceń dotyczących wdrażania wielu czujników:

| **Liczba** | **Liczniki** | **Zależność** | **Liczba czujników** |
|--|--|--|--|
| Maksymalna odległość między przełącznikami | 80 metrów | Przygotowany kabel Ethernet | Więcej niż 1 |
| Liczba nieot sieci | Więcej niż 1 | Brak łączności fizycznej | Więcej niż 1 |
| Liczba przełączników | Można użyć konfiguracji RSPAN | Do ośmiu przełączników z zakresem lokalnym blisko czujnika przez odległość okablowania | Więcej niż 1 |

#### <a name="traffic-mirroring"></a>Dublowanie ruchu  

Aby wyświetlić tylko istotne informacje dotyczące analizy ruchu, należy połączyć usługę Defender for IoT z portem dublowania na przełączniku lub NACIŚNIĘCIu, który obejmuje tylko przemysłowe usługi ICS i ruch SCADA. 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="Użyj tego przełącznika dla konfiguracji.":::

Ruch przełączania można monitorować, korzystając z następujących metod:

- [Przełącz port zakresu](#switch-span-port)

- [ZAKRES zdalny (RSPAN)](#remote-span-rspan)

- [WYBÓR aktywnej i pasywnej agregacji](#active-and-passive-aggregation-tap)

ZAKRES i RSPAN są w terminologii firmy Cisco. Inne marki przełączników mają podobną funkcjonalność, ale mogą korzystać z innej terminologii.

#### <a name="switch-span-port"></a>Przełącz port zakresu

Analizator portu przełącznika odzwierciedla ruch lokalny z interfejsów w przełączniku do interfejsu w tym samym przełączniku. Poniżej przedstawiono kilka kwestii:

- Sprawdź, czy odpowiedni przełącznik obsługuje funkcję dublowania portów.  

- Opcja dublowania jest domyślnie wyłączona.

- Zalecamy skonfigurowanie wszystkich portów przełącznika, nawet jeśli żadne dane nie są połączone z nimi. W przeciwnym razie nieautoryzowane urządzenie może być podłączone do niemonitorowanego portu i nie będzie otrzymywać alertów na czujniku.

- W przypadku niektórych sieci korzystających z komunikatów emisji lub multiemisji należy skonfigurować przełącznik do dublowania tylko transmisji RX (odbieranie). W przeciwnym razie komunikaty multiemisji będą powtarzane dla tylu portów, a przepustowość zostanie pomnożona.

Poniższe przykłady konfiguracji służą wyłącznie do celów referencyjnych i są oparte na przełączniku Cisco 2960 (24 Ports) z systemem IOS. Są one tylko Typowymi przykładami, więc nie używaj ich jako instrukcji. Porty dublowane w innych systemach operacyjnych Cisco i innych firmach przełączników są skonfigurowane inaczej.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="Diagram punktu końcowego konfiguracji portu zakresu.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="Diagram trybu konfiguracji portu zakresu.":::

##### <a name="monitoring-multiple-vlans"></a>Monitorowanie wielu sieci VLAN

Usługa Defender for IoT umożliwia monitorowanie sieci VLAN skonfigurowanych w sieci. Nie jest wymagana żadna konfiguracja usługi Defender for IoT. Użytkownik musi upewnić się, że przełącznik w sieci jest skonfigurowany do wysyłania tagów sieci VLAN do usługi Defender for IoT.

Poniższy przykład przedstawia wymagane polecenia, które muszą być skonfigurowane na przełączniku Cisco, aby umożliwić monitorowanie sieci VLAN w usłudze Defender dla IoT:

**Monitoruj sesję**: to polecenie jest odpowiedzialne za proces wysyłania sieci VLAN do portu span.

- monitorowanie interfejsu źródłowego sesji 1 Gi1/2

- Monitoruj sesję 1 typ pakietu filtru dobry RX

- Monitoruj interfejs docelowy sesji 1 fastEthernet1 hermetyzacji/1 dot1q

**Monitorowanie portu magistrali F.E. Gi1/1**: sieci VLAN są konfigurowane na porcie magistrali.

- GigabitEthernet1 interfejsu/1

- switchport hermetyzacji magistrali dot1q

- Magistrala trybu switchport

#### <a name="remote-span-rspan"></a>ZAKRES zdalny (RSPAN)

Sesja zakresów zdalnych odzwierciedla ruch z wielu rozproszonych portów źródłowych do dedykowanej zdalnej sieci VLAN.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="Diagram zakresu zdalnego.":::

Dane w sieci VLAN są następnie dostarczane za pośrednictwem portów przez wiele przełączników do określonego przełącznika, który zawiera fizyczny port docelowy. Ten port nawiązuje połączenie z usługą Defender for IoT.  

##### <a name="more-about-rspan"></a>Więcej informacji na temat RSPAN

- RSPAN to zaawansowana funkcja, która wymaga specjalnej sieci VLAN do przenoszenia ruchu OBEJMUJĄCEgo monitory między przełącznikami. RSPAN nie jest obsługiwana dla wszystkich przełączników. Sprawdź, czy przełącznik obsługuje funkcję RSPAN.

- Opcja dublowania jest domyślnie wyłączona.

- Zdalna sieć VLAN musi być dozwolona na magistrali między przełącznikami źródłowymi i docelowymi.

- Wszystkie przełączniki łączące tę samą sesję RSPAN muszą pochodzić od tego samego dostawcy.

> [!NOTE]
> Upewnij się, że port magistrali, który udostępnia zdalną sieć VLAN między przełącznikami, nie jest zdefiniowany jako lustrzany port źródłowy sesji.
>
> Zdalna sieć VLAN zwiększa przepustowość na magistrali przez rozmiar pasma sesji dublowanej. Sprawdź, czy port magistrali przełącznika obsługuje tę funkcję.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="Diagram zdalnej sieci VLAN.":::

#### <a name="rspan-configuration-examples"></a>Przykłady konfiguracji RSPAN

RSPAN: oparta na Cisco Catalyst 2960 (24 porty).

**Przykład konfiguracji przełącznika źródła:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="Zrzut ekranu przedstawiający konfigurację RSPAN.":::

1. Przejdź do trybu konfiguracji globalnej.

1. Utwórz dedykowaną sieć VLAN.

1. Zidentyfikuj sieć VLAN jako sieć VLAN RSPAN.

1. Wróć do trybu "Konfigurowanie terminalu".

1. Skonfiguruj wszystkie 24 porty jako źródła sesji.

1. Skonfiguruj sieć VLAN RSPAN jako lokalizację docelową sesji.

1. Wróć do trybu Privileged EXEC.

1. Sprawdź konfigurację funkcji dublowania portów.

**Przykład konfiguracji przełącznika docelowego:**

1. Przejdź do trybu konfiguracji globalnej.

1. Skonfiguruj sieć VLAN RSPAN jako źródło sesji.

1. Skonfiguruj port fizyczny 24 jako lokalizację docelową sesji.

1. Wróć do trybu Privileged EXEC.

1. Sprawdź konfigurację funkcji dublowania portów.

1. Zapisz konfigurację.

#### <a name="active-and-passive-aggregation-tap"></a>WYBÓR aktywnej i pasywnej agregacji

Aktywny lub pasywny wybór agregacji jest instalowany wewnętrznie z kablem sieciowym. Duplikuje zarówno RX, jak i TX, do czujnika monitorowania.

Punkt dostępu terminalu (TAP) to urządzenie sprzętowe, które pozwala na przepływ ruchu sieciowego z portu A do portu B oraz z portu B do portu A, bez przeszkód. Tworzy dokładną kopię obu stron przepływu ruchu, nieustannie bez naruszania integralności sieci. Niektóre naciśnięcia agregują przesyłanie i odbieranie ruchu przy użyciu ustawień przełącznika w razie potrzeby. Jeśli agregacja nie jest obsługiwana, każdy wybór używa dwóch portów czujników do monitorowania ruchu wysyłania i odbierania.

Krany są korzystne z różnych powodów. Są one oparte na sprzęcie i nie mogą zostać naruszone. Przekazują cały ruch, nawet uszkodzone komunikaty, które przełączniki często są używane. Nie są one zależne od procesora, dlatego czasy pakietów są dokładne, gdzie przełączniki obsługują funkcję dublowania jako zadanie o niskim priorytecie, które może wpływać na czas tworzenia dublowanych pakietów. Na potrzeby śledczej NACIŚNIĘCIe jest najlepszym urządzeniem.

Do monitorowania portów można także używać agregatorów TAP. Urządzenia te są oparte na procesorach i nie są tak bezpieczne, jak naciśnięcie sprzętowe. Mogą nie odzwierciedlać dokładnego chronometrażu pakietu.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="Diagram aktywacji aktywnych i pasywnych.":::

##### <a name="common-tap-models"></a>Popularne modele TAP

Te modele zostały przetestowane pod kątem zgodności. Inni dostawcy i modele mogą również być zgodne.

| Image (Obraz) | Model |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Zrzut ekranu przedstawiający Garland P1GCCAS."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="Zrzut ekranu przedstawiający IXIA TPA2-CU3."::: | IXIA TPA2 — CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="Zrzut ekranu Stanów Zjednoczonych USR 4503."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>Specjalna konfiguracja TAP

| Garland | NACIŚNIĘCIe firmy US Robotics |
| ----------- | --------------- |
| Upewnij się, że zworki są ustawione w następujący sposób:<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="Zrzut ekranu przełącznika firmy US (Stany Zjednoczone).":::| Upewnij się, że tryb agregacji jest aktywny. |

## <a name="deployment-validation"></a>Weryfikacja wdrożenia

### <a name="engineering-self-review"></a>Samoobsługowe przeglądy inżynieryjne  

Przeglądanie diagramu sieci z oddziałami i ICS jest najbardziej wydajnym sposobem definiowania najlepszego miejsca do połączenia, w którym można uzyskać najbardziej odpowiedni ruch do monitorowania.

Inżynierowie witryn wiedzą, jak wygląda sieć. Sesja przeglądu z siecią lokalną i zespołami operacyjnymi zazwyczaj wyjaśnia oczekiwania i definiuje najlepsze miejsce do połączenia urządzenia.

Istotne informacje:

- Lista znanych urządzeń (arkusz kalkulacyjny)  

- Szacowana liczba urządzeń  

- Dostawcy i protokoły przemysłowe

- Model przełączników, aby sprawdzić, czy opcja dublowania portów jest dostępna

- Informacje o tym, kto zarządza przełącznikami (na przykład) i czy są zasobami zewnętrznymi

- Lista niektórych sieci w lokacji

#### <a name="common-questions"></a>Często zadawane pytania

- Jakie są ogólne cele implementacji? Czy pełny spis i dokładna mapa sieci są ważne?

- Czy istnieje wiele lub nadmiarowe sieci w usłudze ICS? Czy wszystkie sieci są monitorowane?

- Czy istnieje komunikacja między usługą ICS a siecią firmową? Czy te powiadomienia są monitorowane?

- Czy w projekcie sieci są skonfigurowane sieci VLAN?

- Jak odbywa się konserwacja usługi ICS z urządzeniami stałymi lub przejściowymi?

- Gdzie są zainstalowane zapory w monitorowanych sieciach?

- Czy w monitorowanych sieciach jest dowolna usługa routingu?

- Jakie protokoły są aktywne w monitorowanych sieciach?

- Jeśli połączysz się z tym przełącznikiem, zobaczymy komunikat między HMI a PLCs?

- Jaka jest fizyczna odległość między przełącznikami ICS i zaporą przedsiębiorstwa? 

- Czy Przełączniki niezarządzane mogą zostać zastąpione przełącznikami zarządzanymi?

- Czy w sieci występuje jakakolwiek komunikacja szeregowa? Jeśli tak, Pokaż ją na diagramie sieciowym.

- Jeśli urządzenie Defender for IoT powinno być połączone z tym przełącznikiem, czy w tym pliku jest dostępne fizyczne miejsce w stojaku?

#### <a name="other-considerations"></a>Inne zagadnienia

Celem usługi Defender for IoT jest monitorowanie ruchu z warstw 1 i 2.

W przypadku niektórych architektur urządzenie Defender for IoT będzie również monitorować warstwę 3, jeśli ruch istnieje w tej warstwie. Podczas przeglądania architektury lokacji i decydowania, czy należy monitorować przełącznik, należy wziąć pod uwagę następujące zmienne:

- Jaki jest koszt/korzyść, a także ważność monitorowania tego przełącznika?

- Jeśli przełącznik jest niezarządzany, czy będzie można monitorować ruch z przełącznika wyższego poziomu?

  Jeśli architektura usługi ICS jest topologią pierścienia, należy monitorować tylko jeden przełącznik w tym pierścieniu.

- Co to jest ryzyko związane z bezpieczeństwem lub działaniem w tej sieci?

- Czy jest możliwe monitorowanie sieci VLAN przełącznika? Czy sieć VLAN jest widoczna w innym przełączniku, który możemy monitorować?

#### <a name="technical-validation"></a>Weryfikacja techniczna

Odbieranie przykładowego zarejestrowanego ruchu (plik PCAP) z portu przedziału (lub dublowania) może pomóc:

- Sprawdź, czy przełącznik jest prawidłowo skonfigurowany.

- Upewnij się, czy ruch przechodzący przez przełącznik jest istotny dla monitorowania (ruch przychodzący).

- Zidentyfikuj przepustowość i szacowaną liczbę urządzeń w tym przełączniku.

Możesz zarejestrować przykładowy plik PCAP (kilka minut), łącząc komputer przenośny z już skonfigurowanym portem SPAN za pomocą aplikacji Wireshark.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="Zrzut ekranu komputera przenośnego połączonego z portem SPAN.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="Zrzut ekranu przedstawiający nagrywanie przykładowego pliku PCAP.":::

#### <a name="wireshark-validation"></a>Sprawdzanie poprawności w programie Wireshark

- Sprawdź, czy *pakiety emisji pojedynczej* są obecne w ruchu związanym z ruchem. Emisja pojedyncza pochodzi z jednego adresu do drugiego. Jeśli większość ruchu sieciowego jest komunikatem ARP, Konfiguracja przełącznika jest niepoprawna.

- Przejdź do   >  **hierarchii protokołu** statystyk. Sprawdź, czy istnieją protokoły przemysłowe.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Zrzut ekranu przedstawiający weryfikację programu Wireshark.":::

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Te sekcje umożliwiają rozwiązywanie problemów:

- [Nie można nawiązać połączenia przy użyciu interfejsu sieci Web](#cant-connect-by-using-a-web-interface)

- [Urządzenie nie odpowiada](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>Nie można nawiązać połączenia przy użyciu interfejsu sieci Web

1. Sprawdź, czy komputer, który próbujesz nawiązać połączenie, znajduje się w tej samej sieci co urządzenie.

2. Sprawdź, czy sieć graficznego interfejsu użytkownika jest połączona z portem zarządzania czujnika.

3. Wyślij polecenie ping do adresu IP urządzenia. Jeśli nie ma odpowiedzi na polecenie ping:

    1. Połącz monitor i klawiaturę z urządzeniem.

    1. Zaloguj się przy użyciu użytkownika i hasła do **pomocy technicznej** .

    1. Użyj **listy sieć** poleceń, aby wyświetlić bieżący adres IP.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Zrzut ekranu przedstawiający polecenie Network list.":::

4. Jeśli parametry sieciowe są błędnie skonfigurowane, użyj następującej procedury, aby ją zmienić:

    1. Użyj polecenia **Edytuj ustawienia sieci**.

    1. Aby zmienić adres IP sieci zarządzania, wybierz pozycję **Y**.

    1. Aby zmienić maskę podsieci, wybierz pozycję **Y**.

    1. Aby zmienić serwer DNS, wybierz pozycję **Y**.

    1. Aby zmienić domyślny adres IP bramy, wybierz pozycję **Y**.

    1. Dla zmiany interfejsu wejściowego (tylko dla czujnika) wybierz pozycję **Y**.

    1. Dla interfejsu mostka wybierz pozycję **N**.

    1. Aby zastosować ustawienia, wybierz pozycję **Y**.

5. Po ponownym uruchomieniu programu Połącz się z pomocą techniczną użytkownika i użyj polecenia **Network list** , aby sprawdzić, czy parametry zostały zmienione.

6. Spróbuj ponownie wykonać polecenie ping i nawiązać połączenie z graficznym interfejsem użytkownika.

### <a name="appliance-is-not-responding"></a>Urządzenie nie odpowiada

1. Połącz się z urządzeniem za pomocą monitora i klawiatury albo użyj polecenia "Wykorzystaj", aby połączyć się zdalnie z interfejsem wiersza polecenia.

2. Zaloguj się przy użyciu poświadczeń obsługi.

3. Użyj polecenia **system Sanity** i sprawdź, czy wszystkie procesy są uruchomione.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="Zrzut ekranu przedstawiający polecenie System Sanity.":::

W przypadku innych problemów skontaktuj się z firmą [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="example-site-book"></a>Przykładowa książka witryny

Przykładowa książka witryny umożliwia pobieranie i przeglądanie ważnych informacji potrzebnych do konfiguracji sieci.

### <a name="site-checklist"></a>Lista kontrolna witryny

Przejrzyj tę listę przed wdrożeniem lokacji:

| **#** | **Zadanie lub działanie** | **Stan** | **Komentarze** |
|--|--|--|--|
| 1 | Zamawianie urządzeń. | ☐ |  |
| 2 | Przygotuj listę podsieci w sieci. | ☐ |  |
| 3 | Podaj listę sieci VLAN w sieciach produkcyjnych. | ☐ |  |
| 4 | Podaj listę modeli przełącznika w sieci. | ☐ |  |
| 5 | Podaj listę dostawców i protokołów sprzętu przemysłowego. | ☐ |  |
| 6 | Podaj szczegóły sieci dla czujników (adres IP, podsieć, D-GW, DNS). | ☐ |  |
| 7 | Utwórz niezbędne reguły zapory i listę dostępu. | ☐ |  |
| 8 | Należy utworzyć łączenie portów na przełącznikach monitorowania portów lub skonfigurować kurki sieci zgodnie z potrzebami. | ☐ |  |
| 9 | Przygotuj miejsce na stojaku dla urządzeń czujników. | ☐ |  |
| 10 | Przygotuj stację roboczą dla personelu. | ☐ |  |
| 11 | Podaj klawiaturę, monitor i mysz dla urządzeń w stojaku usługi IoT. | ☐ |  |
| 12 | Stelaż i okablowanie urządzeń. | ☐ |  |
| 13 | Przydzielanie zasobów lokacji w celu obsługi wdrożenia. | ☐ |  |
| 14 | Tworzenie grup Active Directory lub użytkowników lokalnych. | ☐ |  |
| 15 | Szkolenia dotyczące konfigurowania (samodzielnego uczenia się). | ☐ |  |
| 16 | Przejdź lub nie — Przejdź. | ☐ |  |
| 17 | Zaplanuj datę wdrożenia. | ☐ |  |


| **Data** | **Uwaga** | **Data wdrożenia** | **Uwaga** |
|--|--|--|--|
| Defender dla IoT |  | Nazwa lokacji * |  |
| Nazwa |  | Nazwa |  |
| Położenie |  | Położenie |  |

#### <a name="architecture-review"></a>Przegląd architektury

Omówienie diagramu sieci przemysłowej umożliwi zdefiniowanie odpowiedniej lokalizacji dla sprzętu usługi Defender for IoT.

1.  Wyświetl globalny Diagram sieciowy środowiska nieprzemysłowego. Na przykład:

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="Diagram środowiska przemysłowego niezwiązanego z siecią globalną.":::

    > [!NOTE]
    > Urządzenie Defender for IoT powinno być połączone z przełącznikiem niższego poziomu, który widzi ruch między portami na przełączniku.  

2. Podaj przybliżoną liczbę monitorowanych urządzeń sieciowych. Te informacje będą potrzebne podczas dołączania subskrypcji do portalu usługi Azure Defender dla IoT. Podczas procesu dołączania zostanie wyświetlony monit o wprowadzenie liczby urządzeń z przyrostem 1000.

3. Podaj listę podsieci dla sieci produkcyjnych i opis (opcjonalnie). 

    |  **#**  | **Nazwa podsieci** | **Opis** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. Podaj listę sieci VLAN w sieciach produkcyjnych.

    | **#** | **Nazwa sieci VLAN** | **Opis** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. Aby sprawdzić, czy przełączniki mają funkcję dublowania portów, podaj numery modeli przełączników, z którymi ma się łączyć platforma Defender for IoT:

    | **#** | **Przełącznik** | **Model** | **Obsługa dublowania ruchu (SPAN, RSPAN lub None)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    Czy osoby trzecie zarządzają przełącznikami? T lub N 

    Jeśli tak, kto? __________________________________ 

    Jakie są zasady? __________________________________ 

    Na przykład:

    - Firmie

    - Automatyzacja Rockwell — Ethernet lub IP

    - Firmy Emerson – DeltaV, Ovation
    
6. Czy istnieją urządzenia, które komunikują się za pośrednictwem połączenia szeregowego w sieci? Tak lub Nie 

    Jeśli tak, określ, który protokół komunikacji szeregowej: ________________ 

    Jeśli tak, Oznacz na diagramie sieci, które urządzenia komunikują się z protokołami szeregowymi i skąd są: 
 
    <Add your network diagram with marked serial connection> 

7. Dla opcji QoS ustawieniem domyślnym czujnika jest 1,5 MB/s. Określ, czy chcesz je zmienić: ________________ 

   Jednostka biznesowa (komp): ________________ 

### <a name="specifications-for-site-equipment"></a>Specyfikacje dotyczące sprzętu lokacji

#### <a name="network"></a>Sieć  

Urządzenie czujnika jest połączone z portem przełączenia przez kartę sieciową. Jest on połączony z siecią firmową klienta w celu zarządzania przez inną dedykowaną kartę sieciową.

Podaj szczegóły adresu karty sieciowej czujnika, która będzie podłączona w sieci firmowej: 

|  Element               | Urządzenie 1 | Urządzenie 2 | Urządzenie 3 |
| --------------- | ------------- | ------------- | ------------- |
| Adres IP urządzenia    |               |               |               |
| Podsieć          |               |               |               |
| Brama domyślna |               |               |               |
| DNS             |               |               |               |
| Nazwa hosta       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/MOP/zarządzanie serwerem

|       Element          | Urządzenie 1 | Urządzenie 2 | Urządzenie 3 |
| --------------- | ------------- | ------------- | ------------- |
| Adres IP urządzenia     |               |               |               |
| Podsieć          |               |               |               |
| Brama domyślna |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>Lokalna Konsola zarządzania  

|       Element          | Aktywna | Pasywne (w przypadku korzystania z HA) |
| --------------- | ------ | ----------------------- |
| Adres IP             |        |                         |
| Podsieć          |        |                         |
| Brama domyślna |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Element              | Szczegóły |
| --------------- | ------ |
| Adres IP              |        |
| Adres IP | |
| Nazwa użytkownika | |
| Hasło | |
| Typ uwierzytelniania | MD5 lub SHA |
| Szyfrowanie | DES lub AES |
| Klucz tajny | |
| Ciąg identyfikacyjny protokołu SNMP w wersji 2 |

### <a name="on-premises-management-console-ssl-certificate"></a>Certyfikat SSL lokalnej konsoli zarządzania

Czy planowane jest użycie certyfikatu SSL? Tak lub Nie

Jeśli tak, która usługa zostanie użyta do jego wygenerowania? Jakie atrybuty zostaną uwzględnione w certyfikacie (na przykład domena lub adres IP)?

### <a name="smtp-authentication"></a>Uwierzytelnianie SMTP

Czy planowane jest użycie protokołu SMTP do przesyłania dalej alertów do serwera poczty e-mail? Tak lub Nie

Jeśli tak, jakiej metody uwierzytelniania będziesz używać?  

### <a name="active-directory-or-local-users"></a>Active Directory lub Użytkownicy lokalni

Skontaktuj się z administratorem Active Directory, aby utworzyć grupę użytkowników witryny Active Directory lub utworzyć użytkowników lokalnych. Upewnij się, że użytkownicy są gotowi do wdrożenia. 

### <a name="iot-device-types-in-the-network"></a>Typy urządzeń IoT w sieci

| Typ urządzenia | Liczba urządzeń w sieci | Średnia przepustowość |
| --------------- | ------ | ----------------------- |
| Aparat fotograficzny | |
| Maszyna X-ray | |

## <a name="see-also"></a>Zobacz też

[Informacje o instalacji usługi Defender for IoT](how-to-install-software.md)
