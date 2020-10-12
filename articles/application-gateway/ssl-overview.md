---
title: Włączanie kompleksowego protokołu TLS na platformie Azure Application Gateway
description: Ten artykuł zawiera omówienie Application Gateway kompleksowej obsługi protokołu TLS.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: 3d714b579bebb096745a47410da3f8f458e27161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723303"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway

Transport Layer Security (TLS), wcześniej znany jako SSL (SSL), jest standardową technologią zabezpieczeń służącą do nawiązywania zaszyfrowanego połączenia między serwerem sieci Web a przeglądarką. Ten link zapewnia, że wszystkie dane przesyłane między serwerem sieci Web i przeglądarkami pozostają w trybie prywatnym i zaszyfrowanym. Brama Application Gateway obsługuje zarówno zakończenie protokołu TLS na bramie, jak i kompleksowe szyfrowanie TLS.

## <a name="tls-termination"></a>Zakończenie protokołu TLS

Application Gateway obsługuje zakończenie protokołu TLS w bramie, po którym ruch przeważnie jest przenoszony do serwerów zaplecza. Istnieje wiele zalet zaprzestania działania protokołu TLS w usłudze Application Gateway:

- **Zwiększona wydajność** — największe zwiększenie wydajności podczas odszyfrowywania protokołu TLS jest początkową uzgadnianiem. Aby zwiększyć wydajność, serwer wykonujący odszyfrowywanie buforuje identyfikatory sesji TLS i zarządza biletami sesji TLS. W takim przypadku wszystkie żądania od tego samego klienta mogą korzystać z buforowanych wartości. Jeśli jest to wykonywane na serwerach zaplecza, po każdym zażądaniu klienta przejdź do innego serwera, klient musi ponownie przeprowadzić uwierzytelnienie. Korzystanie z biletów TLS może pomóc w ograniczeniu tego problemu, ale nie są one obsługiwane przez wszystkich klientów i mogą być trudne do skonfigurowania i zarządzania.
- **Lepsze wykorzystanie serwerów wewnętrznej bazy danych** — przetwarzanie za pośrednictwem protokołu SSL/TLS jest bardzo czasochłonne i zwiększa się w miarę wzrostu rozmiaru kluczy. Usunięcie tej pracy z serwerów zaplecza pozwala im skupić się na tym, co są najbardziej wydajne, dostarczając zawartość.
- **Inteligentne Routing** — przez odszyfrowanie ruchu, Brama aplikacji ma dostęp do zawartości żądania, takiej jak nagłówki, identyfikator URI itd., i może używać tych danych do przesyłania żądań.
- **Zarządzanie certyfikatami** — certyfikaty muszą być zakupione i zainstalowane tylko w bramie aplikacji, a nie na wszystkich serwerach zaplecza. Pozwala to zaoszczędzić czas i pieniądze.

Aby skonfigurować zakończenie protokołu TLS, do odbiornika musi zostać dodany certyfikat TLS/SSL, aby umożliwić Application Gateway uzyskanie klucza symetrycznego zgodnie ze specyfikacją protokołu TLS/SSL. Klucz symetryczny jest następnie używany do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat TLS/SSL musi być w formacie wymiany informacji osobistych (PFX). Ten format pliku umożliwia wyeksportowanie klucza prywatnego wymaganego przez bramę aplikacji w celu przeprowadzenia szyfrowania i odszyfrowywania ruchu.

> [!IMPORTANT] 
> Certyfikat w odbiorniku wymaga przeładowania całego łańcucha certyfikatów (certyfikatu głównego z urzędu certyfikacji, pośrednich i certyfikatu liścia) w celu ustanowienia łańcucha zaufania. 


> [!NOTE] 
>
> Usługa Application Gateway nie oferuje żadnej możliwości tworzenia nowego certyfikatu ani wysyłania żądania certyfikatu do urzędu certyfikacji.

Aby połączenie TLS działało, należy się upewnić, że certyfikat TLS/SSL spełnia następujące warunki:

- Bieżąca data i godzina przypada w zakresie dat "ważny od" i "ważny do" w odniesieniu do certyfikatu.
- „Nazwa pospolita” (CN) certyfikatu musi być zgodna z nagłówkiem hosta w żądaniu. Jeśli na przykład klient wysyła żądanie do domeny `https://www.contoso.com/`, nazwą pospolitą (CN) musi być `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certyfikaty obsługiwane przez zakończenie protokołu TLS

Brama aplikacji obsługuje następujące typy certyfikatów:

- Certyfikat urzędu certyfikacji: certyfikat urzędu certyfikacji jest certyfikatem cyfrowym wystawionym przez urząd certyfikacji (CA)
- Certyfikat EV (rozszerzone sprawdzanie poprawności): certyfikat EV jest certyfikatem, który jest zgodny ze standardowymi zaleceniami dotyczącymi certyfikatów branżowych. Spowoduje to przekształcenie paska lokalizatora przeglądarki na zielony i opublikowanie nazwy firmy.
- Certyfikat z symbolami wieloznacznymi: ten certyfikat obsługuje dowolną liczbę poddomen opartych na *. site.com, gdzie poddomena zastąpi *. Nie jest to jednak obsługiwane w site.com, więc w przypadku, gdy użytkownicy uzyskują dostęp do witryny sieci Web bez konieczności pisania wiodącego "www", certyfikat wieloznaczny nie będzie uwzględniał tego.
- Self-Signed certyfikaty: przeglądarki klienta nie ufają tym certyfikatom i ostrzegają użytkownika o tym, że certyfikat usługi wirtualnej nie jest częścią łańcucha zaufania. Certyfikaty z podpisem własnym są przydatne w przypadku testowania lub środowisk, w których Administratorzy kontrolują klientów i mogą bezpiecznie pomijać alerty zabezpieczeń przeglądarki. Obciążenia produkcyjne nigdy nie powinny korzystać z certyfikatów z podpisem własnym.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zakończenia protokołu TLS przy użyciu usługi Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Rozmiar certyfikatu
Sprawdź sekcję [limity Application Gateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) , aby poznać maksymalny obsługiwany rozmiar certyfikatu TLS/SSL.

## <a name="end-to-end-tls-encryption"></a>Kompleksowe szyfrowanie protokołu TLS

Nie należy zaszyfrować nieszyfrowanej komunikacji z serwerami zaplecza. Mogą istnieć wymagania dotyczące zabezpieczeń, wymagania dotyczące zgodności lub aplikacja może akceptować tylko bezpieczne połączenie. Aby zapewnić obsługę tych wymagań, usługa Azure Application Gateway ma kompleksowe szyfrowanie TLS.

Kompleksowa warstwa TLS umożliwia szyfrowanie i bezpieczne przesyłanie poufnych danych do zaplecza podczas korzystania z funkcji równoważenia obciążenia warstwy 7 Application Gateway. Te funkcje obejmują koligację sesji na podstawie plików cookie, routing oparty na adresach URL, obsługę routingu opartego na witrynach, możliwość ponownego zapisu lub iniekcji nagłówków X-Forwarded-* i tak dalej.

W przypadku skonfigurowania z kompleksowym trybem komunikacji TLS Application Gateway przerywa sesje protokołu TLS na bramie i odszyfrowuje ruch użytkownika. Następnie stosuje skonfigurowane reguły, aby wybrać odpowiednie wystąpienie puli serwerów zaplecza w celu skierowania do nich ruchu. Application Gateway następnie Inicjuje nowe połączenie TLS z serwerem zaplecza i ponownie szyfruje dane przy użyciu certyfikatu klucza publicznego serwera zaplecza przed przekazaniem żądania do zaplecza. Każda odpowiedź z serwera sieci Web przechodzi przez ten sam proces z powrotem do użytkownika końcowego. Kompleksowa obsługa protokołu TLS jest włączana przez ustawienie protokołu w ustawieniu [http zaplecza](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na https, który następnie jest stosowany do puli zaplecza.

W przypadku jednostki SKU Application Gateway i WAF V1 zasady protokołu TLS dotyczą zarówno ruchu frontonu, jak i zaplecza. Na frontonie Application Gateway działa jako serwer i wymusza zasady. W zapleczu Application Gateway pełni rolę klienta i wysyła informacje o protokole i szyfrowania jako preferencję podczas uzgadniania TLS.

W przypadku jednostki SKU Application Gateway i WAF v2 zasady protokołu TLS dotyczą tylko ruchu frontonu, a wszystkie szyfry są oferowane na serwerze wewnętrznej bazy danych, który ma kontrolę w celu wybrania określonych szyfrów i wersji protokołu TLS podczas uzgadniania.

Application Gateway komunikują się tylko z tymi serwerami zaplecza, które mają zezwolenie na wymieniony certyfikat Application Gateway lub którego certyfikaty są podpisane przez dobrze znane urzędy urzędów certyfikacji, a nazwa POSPOLITa certyfikatu jest zgodna z nazwą hosta w ustawieniach zaplecza protokołu HTTP. Należą do nich zaufane usługi platformy Azure, takie jak Azure App Service/Web Apps i API Management platformy Azure.

Jeśli certyfikaty elementów członkowskich w puli zaplecza nie są podpisane przez dobrze znane urzędy certyfikacji, każde wystąpienie w puli zaplecza z włączonym kompleksowym protokołem TLS musi być skonfigurowane przy użyciu certyfikatu w celu umożliwienia bezpiecznej komunikacji. Dodanie certyfikatu zapewnia, że Brama aplikacji komunikuje się tylko ze znanymi wystąpieniami zaplecza. Pozwala to na zapewnienie kompleksowej komunikacji.

> [!NOTE] 
>
> Certyfikat dodany do **Ustawienia protokołu HTTP zaplecza** służącego do uwierzytelniania serwerów zaplecza może być taki sam jak certyfikat dodany do **odbiornika** protokołu TLS w usłudze Application Gateway lub inny w celu zwiększenia bezpieczeństwa.

![scenariusz kompleksowego protokołu TLS][1]

W tym przykładzie żądania przy użyciu protokołu TLS 1.2 są kierowane do serwerów zaplecza w Pool1 przy użyciu kompleksowej usługi TLS.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>Zakończenie do końca protokołu TLS i Zezwalanie na listę certyfikatów

Application Gateway komunikuje się tylko z znanymi wystąpieniami zaplecza, które mają zezwolenie na wyświetlanie certyfikatu w usłudze Application Gateway. Istnieją pewne różnice w procesie kompleksowej konfiguracji protokołu TLS w odniesieniu do używanej wersji programu Application Gateway. W poniższej sekcji opisano je pojedynczo.

## <a name="end-to-end-tls-with-the-v1-sku"></a>Kompleksowa wartość TLS z jednostką SKU v1

Aby włączyć kompleksową transakcję TLS z serwerami zaplecza i aby Application Gateway do kierowania do nich żądań, sondy kondycji muszą być pomyślne i zwracać w dobrej kondycji.

W przypadku sond kondycji protokołu HTTPS Application Gateway jednostka SKU w wersji 1 używa dokładnego dopasowania certyfikatu uwierzytelniania (klucza publicznego certyfikatu serwera wewnętrznej bazy danych, a nie certyfikatu głównego) do przekazania do ustawień HTTP.

Dozwolone są tylko połączenia z znanymi i dozwolonymi punktami końcowymi. Pozostałe punkty końcowe są uznawane za niezdrowe przez sondy kondycji. Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowych i nie są zalecane dla obciążeń w środowisku produkcyjnym. Takie certyfikaty muszą być dozwolone na liście przy użyciu bramy aplikacji, jak opisano w poprzednich krokach, zanim będzie można ich użyć.

> [!NOTE]
> Konfiguracja uwierzytelniania i zaufanego certyfikatu głównego nie są wymagane dla zaufanych usług platformy Azure, takich jak Azure App Service. Są one traktowane jako zaufane domyślnie.

## <a name="end-to-end-tls-with-the-v2-sku"></a>Kompleksowa wersja protokołu TLS z jednostką SKU v2

Certyfikaty uwierzytelniania zostały wycofane i zastąpione przez zaufane certyfikaty główne w jednostce SKU Application Gateway v2. Działają one podobnie jak w przypadku certyfikatów uwierzytelniania z kilkoma kluczowymi różnicami:

- Certyfikaty podpisane przez dobrze znane urzędy urzędów certyfikacji, których nazwa POSPOLITa jest zgodna z nazwą hosta w ustawieniach zaplecza protokołu HTTP, nie wymagają żadnego dodatkowego kroku dla kompleksowego protokołu TLS do pracy. 

   Na przykład, jeśli certyfikaty zaplecza są wystawiane przez dobrze znany urząd certyfikacji i ma nazwę POSPOLITą contoso.com, a pole hosta ustawienia http zaplecza ma również wartość contoso.com, nie są wymagane żadne dodatkowe kroki. Można ustawić protokół ustawień protokołu HTTP zaplecza na HTTPS, a zarówno sonda kondycji, jak i ścieżka danych będzie włączona protokół TLS. Jeśli używasz Azure App Service lub innych usług sieci Web platformy Azure jako zaplecza, to są one niejawnie zaufane i nie są wymagane żadne dalsze kroki dla kompleksowego protokołu TLS.
   
> [!NOTE] 
>
> Aby certyfikat TLS/SSL był zaufany, certyfikat serwera wewnętrznej bazy danych musi być wystawiony przez urząd certyfikacji, który jest dobrze znany. Jeśli certyfikat nie został wystawiony przez zaufany urząd certyfikacji, następnie Usługa Application Gateway sprawdzi, czy certyfikat wystawiającego urzędu certyfikacji został wystawiony przez zaufany urząd certyfikacji i tak dalej, dopóki nie zostanie znaleziony zaufany urząd certyfikacji (w którym zostanie nawiązane zaufane, bezpieczne połączenie) lub nie zostanie znaleziony żaden zaufany urząd certyfikacji (w tym momencie Brama aplikacji oznaczy niezdrowy). W związku z tym zaleca się, aby certyfikat serwera wewnętrznej bazy danych zawierał zarówno główny, jak i pośredni urząd certyfikacji.

- Jeśli certyfikat jest podpisany z podpisem własnym lub podpisany przez nieznane pośredniki, a następnie aby włączyć protokół TLS w wersji 2, należy zdefiniować zaufany certyfikat główny. Application Gateway komunikują się tylko z zapleczem, którego certyfikat główny certyfikatu serwera jest zgodny z jedną z listy zaufanych certyfikatów głównych w ustawieniu protokołu HTTP zaplecza skojarzonym z pulą.

- Oprócz zgodności z certyfikatem głównym Application Gateway v2 sprawdza także, czy ustawienie hosta określone w ustawieniu protokołu HTTP zaplecza pasuje do nazwy pospolitej (CN) przedstawionej przez certyfikat TLS/SSL serwera wewnętrznej bazy danych. Podczas próby nawiązania połączenia TLS z zapleczem Application Gateway v2 ustawia rozszerzenie Oznaczanie nazwy serwera (SNI) na host określony w ustawieniu protokołu HTTP zaplecza.

- Jeśli **wybierzesz opcję Nazwa hosta z elementu docelowego zaplecza** zamiast pola host w ustawieniu http zaplecza, nagłówek SNI jest zawsze ustawiany jako nazwa FQDN puli zaplecza, a CN w certyfikacie serwera wewnętrznej bazy danych TLS/SSL musi być zgodna z jego nazwą FQDN. Elementy członkowskie puli zaplecza z adresami IP nie są obsługiwane w tym scenariuszu.

- Certyfikat główny to zakodowany w formacie base64 certyfikat główny z certyfikatów serwera wewnętrznej bazy danych.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>SNI różnice w jednostkach SKU V1 i v2

Jak wspomniano wcześniej, Application Gateway przerywa ruch TLS od klienta na odbiorniku Application Gateway (Przywołaj go do usługi frontonu), odszyfrowuje ruch, stosuje niezbędne reguły w celu określenia serwera wewnętrznej bazy danych, do którego ma zostać przesłane żądanie, i ustanawia nową sesję protokołu TLS z serwerem wewnętrznej bazy danych (Przywołaj ją do połączenia zaplecza).

W poniższych tabelach przedstawiono różnice w SNI między jednostką SKU V1 i v2 w zakresie połączeń frontonu i zaplecza.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Połączenie TLS frontonu (klient z bramą aplikacji)

---
Scenariusz | wersjach | v2 |
| --- | --- | --- |
| Jeśli klient określi nagłówek SNI i wszystkie odbiorniki z obsługą wiele lokacji są włączone przy użyciu flagi "Wymagaj SNI" | Zwróć odpowiedni certyfikat i jeśli witryna nie istnieje (zgodnie z server_name), połączenie zostanie zresetowane. | Zwraca odpowiedni certyfikat, jeśli jest dostępny, w przeciwnym razie zwraca certyfikat pierwszego skonfigurowanego odbiornika HTTPS (w kolejności)|
| Jeśli klient nie określi nagłówka SNI i wszystkie nagłówki wielolokacjowe są włączone z opcją "Wymagaj SNI" | Resetuje połączenie | Zwraca certyfikat pierwszego skonfigurowanego odbiornika HTTPS (w kolejności)
| Jeśli klient nie określi nagłówka SNI i istnieje podstawowy odbiornik skonfigurowany z certyfikatem | Zwraca certyfikat skonfigurowany w podstawowym odbiorniku do klienta (certyfikat domyślny lub rezerwowy) | Zwraca certyfikat pierwszego skonfigurowanego odbiornika HTTPS (w kolejności) |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Połączenie TLS zaplecza (Brama aplikacji do serwera wewnętrznej bazy danych)

#### <a name="for-probe-traffic"></a>Ruch sondy

---
Scenariusz | wersjach | v2 |
| --- | --- | --- |
| Nagłówek SNI (server_name) podczas uzgadniania protokołu TLS jako nazwy FQDN | Ustaw jako nazwę FQDN z puli zaplecza. Zgodnie z [RFC 6066](https://tools.ietf.org/html/rfc6066), literały adresów IPv4 i IPv6 nie są dozwolone w nazwie hosta SNI. <br> **Uwaga:** Nazwa FQDN w puli zaplecza powinna być rozpoznawana przez system DNS jako adres IP serwera wewnętrznej bazy danych (Public lub private) | Nagłówek SNI (server_name) jest ustawiany jako nazwa hosta z sondy niestandardowej dołączonej do ustawień protokołu HTTP (jeśli są skonfigurowane), w przeciwnym razie od nazwy hosta wymienionej w ustawieniach protokołu HTTP, w przeciwnym razie z nazwy FQDN wymienionej w puli zaplecza. Kolejność pierwszeństwa to niestandardowa sonda > ustawienia protokołu HTTP > puli zaplecza. <br> **Uwaga:** Jeśli nazwy hostów skonfigurowane w ustawieniach protokołu HTTP i sondy niestandardowej są inne, a następnie zgodnie z pierwszeństwem, SNI zostanie ustawiona jako nazwa hosta z sondy niestandardowej.
| Jeśli adres puli zaplecza jest adresem IP (v1) lub w przypadku skonfigurowania nazwy hosta sondy niestandardowej jako adresu IP (v2) | SNI (server_name) nie zostanie ustawiona. <br> **Uwaga:** W takim przypadku serwer wewnętrznej bazy danych powinien mieć możliwość zwrócenia certyfikatu domyślnego/rezerwowego i powinno to być dozwolone na liście ustawień protokołu HTTP w obszarze certyfikat uwierzytelniania. Jeśli na serwerze wewnętrznej bazy danych nie ma skonfigurowanego domyślnego/rezerwowego certyfikatu, a SNI jest oczekiwany, serwer może zresetować połączenie i prowadzić do błędów sondowania | Zgodnie z powyższym pierwszeństwem, jeśli ma adres IP jako nazwę hosta, SNI nie zostanie ustawiona zgodnie z [RFC 6066](https://tools.ietf.org/html/rfc6066). <br> **Uwaga:** SNI również nie zostanie ustawiona w sondach v2, jeśli nie jest skonfigurowana żadna niestandardowa sonda i nie zostanie ustawiona nazwa hosta dla ustawień HTTP lub puli zaplecza |

> [!NOTE] 
> Jeśli niestandardowa Sonda nie jest skonfigurowana, Application Gateway wysyła domyślną sondę w tym formacie — \<protocol\> ://127.0.0.1: \<port\> /. Na przykład dla domyślnej sondy HTTPS zostanie ona wysłana jako https://127.0.0.1:443/ . Należy pamiętać, że adres 127.0.0.1 podany w tym miejscu jest używany tylko jako nagłówek hosta HTTP i zgodnie z opisem w dokumencie RFC 6066, nie będzie używany jako nagłówek SNI. Aby uzyskać więcej informacji o błędach sondy kondycji, zobacz [Przewodnik dotyczący rozwiązywania problemów z kondycją zaplecza](application-gateway-backend-health-troubleshooting.md)

#### <a name="for-live-traffic"></a>Dla ruchu na żywo

---
Scenariusz | wersjach | v2 |
| --- | --- | --- |
| Nagłówek SNI (server_name) podczas uzgadniania protokołu TLS jako nazwy FQDN | Ustaw jako nazwę FQDN z puli zaplecza. Zgodnie z [RFC 6066](https://tools.ietf.org/html/rfc6066), literały adresów IPv4 i IPv6 nie są dozwolone w nazwie hosta SNI. <br> **Uwaga:** Nazwa FQDN w puli zaplecza powinna być rozpoznawana przez system DNS jako adres IP serwera wewnętrznej bazy danych (Public lub private) | Nagłówek SNI (server_name) jest ustawiany jako nazwa hosta z ustawień HTTP, w przeciwnym razie, jeśli wybrano opcję *PickHostnameFromBackendAddress* lub jeśli nie określono żadnej nazwy hosta, zostanie ona ustawiona jako nazwa FQDN w konfiguracji puli zaplecza
| Jeśli adres puli zaplecza jest adresem IP lub nazwa hosta nie jest ustawiona w ustawieniach protokołu HTTP | SNI nie zostanie ustawiona zgodnie ze standardem [RFC 6066](https://tools.ietf.org/html/rfc6066) , Jeśli wpis puli zaplecza nie jest nazwą FQDN | SNI zostanie ustawiona jako nazwa hosta z wejściowej nazwy FQDN z klienta, a CN certyfikatu wewnętrznej bazy danych musi pasować do tej nazwy hosta.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z kompleksowym protokołem TLS przejdź do strony [Konfigurowanie kompleksowej usługi TLS przy użyciu Application Gateway z programem PowerShell](application-gateway-end-to-end-ssl-powershell.md) , aby utworzyć bramę aplikacji przy użyciu kompleksowej usługi TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
