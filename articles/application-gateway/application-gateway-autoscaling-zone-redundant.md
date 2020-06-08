---
title: Skalowanie automatyczne i strefowo nadmiarowa brama aplikacji (wersja 2)
description: W tym artykule wprowadzono Standard_v2 aplikacji platformy Azure i WAF_v2 jednostki SKU, które obejmują funkcję skalowania automatycznego i strefowo nadmiarowe.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: df68bec859a3c2456dd1cf5f69387e8affb4b3e2
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484541"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Skalowanie automatyczne i strefowo nadmiarowa brama aplikacji (wersja 2) 

Application Gateway i Zapora aplikacji sieci Web (WAF) są również dostępne w ramach Standard_v2 i WAF_v2 jednostki SKU. Jednostka SKU v2 oferuje ulepszenia wydajności i dodaje obsługę krytycznych nowych funkcji, takich jak Skalowanie automatyczne, nadmiarowość stref i obsługa statycznych adresów VIP. Istniejące funkcje w ramach jednostki SKU w warstwie Standardowa i WAF nadal są obsługiwane w nowej jednostce SKU v2 z kilkoma wyjątkami wymienionymi w sekcji [porównanie](#differences-from-v1-sku) .

Nowa jednostka SKU w wersji 2 obejmuje następujące udoskonalenia:

- **Skalowanie**automatyczne: wdrożenia Application Gateway lub WAF w ramach jednostki SKU skalowania automatycznego można skalować w górę lub w dół w zależności od zmiany wzorców obciążenia ruchu sieciowego. Dzięki skalowaniu automatycznemu nie trzeba również wybierać rozmiaru wdrożenia ani liczby wystąpień podczas aprowizowania usługi. Ta jednostka SKU oferuje prawdziwą elastyczność. W Standard_v2 i WAF_v2 jednostki SKU Application Gateway mogą działać zarówno w stałej pojemności (wyłączone Skalowanie automatyczne), jak i w trybie włączonym skalowaniem automatycznym. Tryb stałej wydajności jest przydatny w scenariuszach z spójnymi i przewidywalnymi obciążeniami. Tryb skalowania automatycznego jest korzystny w aplikacjach, które zobaczą wariancję ruchu aplikacji.
- **Nadmiarowość stref**: wdrożenie Application Gateway lub WAF może obejmować wiele strefy dostępności, eliminując konieczność udostępniania oddzielnych wystąpień Application Gateway w każdej strefie za pomocą Traffic Manager. Można wybrać jedną strefę lub wiele stref, w których są wdrożone Application Gateway wystąpienia, co sprawia, że jest to bardziej odporne na awarie strefy. Pula zaplecza dla aplikacji może być podobnie dystrybuowana w strefach dostępności.

  Nadmiarowość stref jest dostępna tylko w przypadku, gdy dostępne są strefy platformy Azure. W innych regionach obsługiwane są wszystkie inne funkcje. Aby uzyskać więcej informacji, zobacz [regiony i strefy dostępności na platformie Azure](../availability-zones/az-overview.md)
- **Statyczny adres VIP**: jednostka SKU Application Gateway v2 obsługuje wyłącznie statyczny typ adresu VIP. Dzięki temu wirtualne adresy IP skojarzone z bramą aplikacji nie ulegają zmianie w cyklu życia wdrożenia nawet po ponownym uruchomieniu.  W wersji 1 nie ma statycznego adresu VIP, dlatego do App Services za pośrednictwem bramy aplikacji należy użyć adresu URL usługi Application Gateway zamiast adresu IP.
- Ponowne **Zapisywanie nagłówka**: Application Gateway umożliwia dodawanie, usuwanie lub aktualizowanie nagłówków żądań i odpowiedzi HTTP z jednostką SKU v2. Aby uzyskać więcej informacji, zobacz [Zapisywanie nagłówków HTTP przy użyciu Application Gateway](rewrite-http-headers.md)
- **Integracja Key Vault**: Application Gateway v2 obsługuje integrację z Key Vault dla certyfikatów serwera, które są dołączone do odbiorników z WŁĄCZONYm protokołem HTTPS. Aby uzyskać więcej informacji, zobacz temat [zakończenie protokołu TLS z certyfikatami Key Vault](key-vault-certs.md).
- **Kontroler usługi transferu danych w usłudze Azure Kubernetes**: kontroler transferu danych przychodzących w systemie Application Gateway v2 umożliwia korzystanie z Application Gateway platformy Azure jako ruchu przychodzącego dla usługi Azure Kubernetes Service (AKS) znanej jako klaster AKS. Aby uzyskać więcej informacji, zobacz [co to jest Application Gateway kontroler](ingress-controller-overview.md)transferu danych przychodzących?
- **Udoskonalenia wydajności**: jednostka SKU v2 oferuje do pięciokrotną lepszej wydajności odciążania TLS w porównaniu z jednostką SKU Standard/WAF.
- **Szybsze wdrażanie i aktualizowanie czasu** Jednostka SKU v2 zapewnia szybszy czas wdrożenia i aktualizacji w porównaniu do jednostki SKU Standard/WAF. Obejmuje to również zmiany konfiguracji WAF.

![Diagram strefy automatycznego skalowania.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Obsługiwane regiony

Jednostki SKU Standard_v2 i WAF_v2 są dostępne w następujących regionach: Północno-środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Wschodnie stany USA, Wschodnie stany USA 2, środkowe stany USA, Europa Północna, Europa Zachodnia, Azja Południowo-Wschodnia, francja środkowa, japonia Wschodnia, Japonia Południowo-Wschodnia, zachodnie Zjednoczone Królestwo Korea środkowa Azja Wschodnia , Południowe Zjednoczone Królestwo, Indie Środkowe, Indie Zachodnie, Indie Południowe.

## <a name="pricing"></a>Cennik

W przypadku jednostki SKU v2 model cenowy jest oparty na zużyciu i nie jest już dołączony do liczby wystąpień ani rozmiarów. Cennik wersji 2 jednostki SKU ma dwa składniki:

- **Stała cena** — cena godzinowa (lub częściowa godzina) do aprowizacji Standard_v2 lub WAF_v2 bramy. Należy pamiętać, że 0 dodatkowe wystąpienia w dalszym ciągu zapewniają wysoką dostępność usługi, która jest zawsze dostępna ze stałą ceną.
- **Cena jednostkowa wydajności** — koszt oparty na zużyciu jest naliczany wraz z kosztem stałym. Opłata za jednostkę wydajności jest również obliczana co godzinę lub częściej godzinowo. Istnieją trzy wymiary jednostki obliczeniowej, trwałe połączenia i przepływność. Jednostka obliczeniowa to miara używanej pojemności procesora. Czynniki wpływające na jednostki obliczeniowe to liczba połączeń TLS/s, obliczenia ponownego zapisu adresu URL oraz przetwarzanie reguł zapory aplikacji internetowej. Połączenie trwałe to miara ustanowionych połączeń TCP z bramą aplikacji w danym interwale rozliczeniowym. Przepływność jest średnimi Megabitami/s przetworzonymi przez system w danym interwale rozliczeniowym.  Rozliczenia odbywają się na poziomie jednostki pojemności dla wszystkiego powyżej liczby wystąpień zarezerwowanych.

Każda jednostka pojemności składa się z co najwyżej: 1 jednostka obliczeniowa, 2500 połączeń trwałych i przepływności do 2,22 MB/s.

Wskazówki dotyczące jednostek obliczeniowych:

- **Standard_v2** — każda jednostka obliczeniowa jest w stanie około 50 połączeń na sekundę z certyfikatem klucza RSA 2048-bitowym.
- **WAF_v2** — każda jednostka obliczeniowa może obsłużyć około 10 współbieżnych żądań na sekundę w przypadku 70-30% ruchu z 70% żądań mniejszych niż 2 KB Get/post i pozostałych. Obecnie nie ma to wpływ na wydajność WAF.

> [!NOTE]
> Każde wystąpienie może obecnie obsługiwać około 10 jednostek pojemności.
> Liczba żądań, które może obsłużyć jednostka obliczeniowa, zależy od różnych kryteriów, takich jak rozmiar klucza certyfikatu TLS, algorytm wymiany kluczy, ponowne zapisywanie nagłówka i wielkość żądania przychodzącego WAF. Zalecamy przeprowadzanie testów aplikacji w celu określenia liczby żądań na jednostkę obliczeniową. Jednostka pojemności i jednostka obliczeniowa zostaną udostępnione jako Metryka przed rozpoczęciem rozliczania.

W poniższej tabeli przedstawiono przykładowe ceny i służą tylko do celów ilustracyjnych.

**Cennik w regionie Wschodnie stany USA**:

|              Nazwa jednostki SKU                             | Stała cena ($/godz.)  | Cena jednostkowa wydajności ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0.36             | 0,0144                          |

Aby uzyskać więcej informacji o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/application-gateway/). 

**Przykład 1**

Standard_v2 Application Gateway jest inicjowana bez skalowania automatycznego w trybie skalowania ręcznego ze stałą pojemnością pięciu wystąpień.

Stała cena = 744 (godziny) * $0,20 = $148,8 <br>
Jednostki pojemności = 744 (godz.) * 10 jednostek wydajności na wystąpienie * 5 wystąpień * $0,008 za godzinę jednostki pojemności = $297,6

Łączna cena = $148,8 + $297,6 = $446,4

**Przykład 2**

Application Gateway standard_v2 jest inicjowana przez miesiąc, z zerowymi wystąpieniami i w tym momencie otrzymuje 25 nowych połączeń TLS/s, średnia z 8,88-MB/s. Przy założeniu, że połączenia są krótkotrwałe, cena będzie:

Stała cena = 744 (godziny) * $0,20 = $148,8

Cena jednostkowa wydajności = 744 (godz.) * Max (25/50 jednostek obliczeniowych dla połączeń/s, jednostka pojemności 8.88/2.22 na potrzeby przepływności) * $0,008 = 744 * 4 * 0,008 = $23,81

Łączna cena = $148.8 + 23.81 = $172,61

Jak widać, opłaty są naliczane tylko za cztery jednostki pojemności, a nie dla całego wystąpienia. 

> [!NOTE]
> Funkcja Max zwraca największą wartość w parze wartości.


**Przykład 3**

Application Gateway standard_v2 jest inicjowana przez miesiąc z co najmniej pięcioma wystąpieniami. Przy założeniu, że ruch i połączenia są krótkotrwałe, cena będzie następująca:

Stała cena = 744 (godziny) * $0,20 = $148,8

Cena jednostki wydajności = 744 (godz.) * Max (0/50 jednostek obliczeniowych dla połączeń/s, 0/2.22 Jednostka pojemności dla przepływności) * $0,008 = 744 * 50 * 0,008 = $297,60

Łączna cena = $148.80 + 297.60 = $446,4

W tym przypadku opłaty są naliczane w całości z pięciu wystąpień nawet wtedy, gdy nie ma żadnego ruchu.

**Przykład 4**

Application Gateway standard_v2 jest inicjowana przez miesiąc, z co najmniej pięcioma wystąpieniami, ale w tym momencie istnieje średnia 125-MB/s transferu danych i 25 połączeń TLS na sekundę. Przy założeniu, że ruch i połączenia są krótkotrwałe, cena będzie następująca:

Stała cena = 744 (godziny) * $0,20 = $148,8

Cena jednostkowa wydajności = 744 (godz.) * Max (25/50 jednostek obliczeniowych dla połączeń/s, jednostka pojemności 125/2.22 dla przepływności) * $0,008 = 744 * 57 * 0,008 = $339,26

Łączna cena = $148.80 + 339.26 = $488,06

W tym przypadku opłaty są naliczane za całe pięć wystąpień oraz siedem jednostek pojemności (czyli 7/10 wystąpienia).  

**Przykład 5**

WAF_v2 Application Gateway jest inicjowany przez miesiąc. W tym czasie otrzymujesz 25 nowych połączeń TLS/s, średnia z 8,88-MB/s transmisji danych i wysyła żądania 80 na sekundę. Przy założeniu, że połączenia są krótkotrwałe i obliczenia jednostek obliczeniowych dla aplikacji obsługują 10 RPS pliku na jednostkę obliczeniową, cena będzie:

Stała cena = 744 (godziny) * $0,36 = $267,84

Cena jednostkowa wydajności = 744 (godz.) * Max (maksymalna liczba jednostek obliczeniowych (25/50 dla połączeń/s, 80/10 WAF RPS pliku), 8.88/2.22 jednostki pojemności dla przepływności) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Łączna cena = $267,84 + $85,71 = $353,55

> [!NOTE]
> Funkcja Max zwraca największą wartość w parze wartości.

## <a name="scaling-application-gateway-and-waf-v2"></a>Skalowanie Application Gateway i WAF v2

Application Gateway i WAF można skonfigurować do skalowania w dwóch trybach:

- **Skalowanie** automatyczne — z włączonym skalowaniem automatycznym jednostki SKU Application Gateway i WAF v2 są skalowane w górę lub w dół na podstawie wymagań dotyczących ruchu aplikacji. Ten tryb oferuje lepszą elastyczność aplikacji i eliminuje konieczność odgadnięcia rozmiaru bramy aplikacji lub liczby wystąpień. Ten tryb pozwala także zaoszczędzić koszt przez niewymaganie, aby brama działała na szczytowej pojemności dla przewidywanego maksymalnego obciążenia ruchem. Należy określić minimalną i opcjonalną maksymalną liczbę wystąpień. Minimalna pojemność zapewnia, że Application Gateway i WAF v2 nie spadnie poniżej minimalnej określonej liczby wystąpień, nawet w przypadku braku ruchu. Każde wystąpienie jest w przybliżeniu równe 10 dodatkowym zarezerwowanym jednostkom pojemności. Zero oznacza brak zarezerwowanej pojemności i ma charakter wyłącznie Skalowanie automatyczne. Opcjonalnie można również określić maksymalną liczbę wystąpień, która zapewnia, że Application Gateway nie będzie skalowana poza określoną liczbę wystąpień. Opłaty są naliczane tylko za ilość ruchu obsługiwanego przez bramę. Liczba wystąpień może być z zakresu od 0 do 125. Wartość domyślna maksymalna liczba wystąpień to 20, jeśli nie zostanie określona.
- **Ręczne** — możesz alternatywnie wybrać tryb ręczny, w którym Brama nie będzie automatycznie skalowana. W tym trybie, jeśli ruch jest większy niż obsługiwane Application Gateway lub WAF, może to spowodować utratę ruchu. W trybie ręcznym określenie liczby wystąpień jest obowiązkowe. Liczba wystąpień może się różnić od 1 do 125 wystąpień.

## <a name="autoscaling-and-high-availability"></a>Skalowanie automatyczne i wysoka dostępność

Bramy aplikacji platformy Azure są zawsze wdrażane w sposób wysokiej dostępności. Usługa została utworzona z wielu wystąpień utworzonych zgodnie z konfiguracją (Jeśli automatyczne skalowanie jest wyłączone) lub wymagane przez obciążenie aplikacji (jeśli Skalowanie automatyczne jest włączone). Należy zauważyć, że z perspektywy użytkownika nie musisz mieć wglądu w poszczególne wystąpienia, ale tylko w ramach usługi Application Gateway jako całości. Jeśli pewne wystąpienie ma problem i przestaje działać, usługa Azure Application Gateway będzie w sposób niewidoczny dla użytkownika utworzyć nowe wystąpienie.

Należy pamiętać, że nawet jeśli skonfigurujesz Skalowanie automatyczne z zerem o wartości zero, usługa nadal będzie o wysokiej dostępności, która zawsze jest uwzględniana w ustalonej cenie.

Jednak utworzenie nowego wystąpienia może zająć trochę czasu (około sześć lub siedem minut). W związku z tym, jeśli nie chcesz poradzić sobie z tym przestojem, możesz skonfigurować minimalną liczbę wystąpień równą 2, najlepiej z obsługą strefy dostępności. W ten sposób będziesz mieć co najmniej dwa wystąpienia w ramach usługi Azure Application Gateway w normalnych warunkach, więc jeśli jeden z nich wystąpił problem, drugi spróbuje poradzić sobie z ruchem, podczas gdy nowe wystąpienie zostanie utworzone. Należy pamiętać, że wystąpienie usługi Azure Application Gateway może obsłużyć około 10 jednostek pojemności, więc w zależności od ilości ruchu, który zwykle może chcieć skonfigurować ustawienie skalowania automatycznego o wartości większej niż 2.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Porównanie funkcji między jednostką SKU V1 i jednostką SKU v2

Poniższa tabela zawiera porównanie funkcji dostępnych w poszczególnych jednostkach SKU.

|                                                   | jednostka SKU w wersji 1   | jednostka SKU v2   |
| ------------------------------------------------- | -------- | -------- |
| Skalowanie automatyczne                                       |          | &#x2713; |
| Nadmiarowość stref                                   |          | &#x2713; |
| Statyczny adres VIP                                        |          | &#x2713; |
| Kontroler danych przychodzących usługi Azure Kubernetes Service (AKS) |          | &#x2713; |
| Integracja magazynu kluczy Azure                       |          | &#x2713; |
| Zapisz ponownie nagłówki HTTP (S)                           |          | &#x2713; |
| Routing oparty na adresach URL                                 | &#x2713; | &#x2713; |
| Hostowanie wielu witryn                             | &#x2713; | &#x2713; |
| Przekierowywanie ruchu                               | &#x2713; | &#x2713; |
| Zapora aplikacji internetowej                    | &#x2713; | &#x2713; |
| Reguły niestandardowe zapory aplikacji internetowej                                  |          | &#x2713; |
| Zakończenie Transport Layer Security (TLS)/Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| Kompleksowe szyfrowanie protokołu TLS                         | &#x2713; | &#x2713; |
| Koligacja sesji                                  | &#x2713; | &#x2713; |
| Niestandardowe strony błędów                                | &#x2713; | &#x2713; |
| Obsługa protokołu WebSocket                                 | &#x2713; | &#x2713; |
| Obsługa protokołu HTTP/2                                    | &#x2713; | &#x2713; |
| Opróżnianie połączeń                               | &#x2713; | &#x2713; |

> [!NOTE]
> Jednostka SKU skalowania w wersji 2 obsługuje teraz [domyślne sondy kondycji](application-gateway-probe-overview.md#default-health-probe) w celu automatycznego monitorowania kondycji wszystkich zasobów w puli zaplecza oraz wyróżniania tych członków zaplecza, które są uznawane za złej kondycji. Domyślna sonda kondycji jest automatycznie konfigurowana dla frontonów, które nie mają niestandardowej konfiguracji sondowania. Aby dowiedzieć się więcej, zobacz [sondy kondycji w usłudze Application Gateway](application-gateway-probe-overview.md).

## <a name="differences-from-v1-sku"></a>Różnice między jednostką SKU v1

W tej sekcji opisano funkcje i ograniczenia dotyczące jednostki SKU w wersji 2, która różni się od jednostki SKU w wersji 1.

|Różnica|Szczegóły|
|--|--|
|Certyfikat uwierzytelniania|Nieobsługiwane.<br>Aby uzyskać więcej informacji, zobacz [Omówienie kompleksowej usługi TLS z Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Mieszanie Standard_v2 i Application Gateway standardowych w tej samej podsieci|Nieobsługiwane|
|Trasa zdefiniowana przez użytkownika (UDR) w podsieci Application Gateway|Obsługiwane (określone scenariusze). W wersji zapoznawczej.<br> Aby uzyskać więcej informacji na temat obsługiwanych scenariuszy, zobacz [Omówienie konfiguracji Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).|
|SIECIOWEJ grupy zabezpieczeń dla zakresu portów przychodzących| -65200 do 65535 dla Standard_v2 jednostki SKU<br>-65503 do 65534 dla standardowej jednostki SKU.<br>Aby uzyskać więcej informacji, zobacz [często zadawane pytania](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Dzienniki wydajności w usłudze Diagnostyka Azure|Nieobsługiwane.<br>Należy używać metryk platformy Azure.|
|Rozliczenia|Rozliczenia zaplanowane do rozpoczęcia od 1 lipca 2019.|
|Tryb FIPS|Te nie są obecnie obsługiwane.|
|Tryb tylko ILB|Nie jest to obecnie obsługiwane. Tryb publiczny i ILB razem są obsługiwane.|
|Integracja obserwatora sieciowego|Nieobsługiwane.|
|Integracja z usługą Azure Security Center|Jeszcze niedostępne.

## <a name="migrate-from-v1-to-v2"></a>Migrowanie z wersji 1 do wersji 2

Skrypt Azure PowerShell jest dostępny w galerii programu PowerShell, aby ułatwić migrację z poziomu wersji V1 Application Gateway/WAF do jednostki SKU skalowania automatycznego w wersji 2. Ten skrypt ułatwia skopiowanie konfiguracji z bramy v1. Migracja ruchu nadal jest odpowiedzialna. Aby uzyskać więcej informacji, zobacz [Migrowanie platformy Azure Application Gateway z wersji 1 do wersji 2](migrate-v1-v2.md).

## <a name="next-steps"></a>Następne kroki

- [Szybki start: bezpośredni ruch internetowy w usłudze Azure Application Gateway — Azure Portal](quick-create-portal.md)
- [Tworzenie automatycznego skalowania, nadmiarowej bramy aplikacji z zastrzeżonym wirtualnym adresem IP przy użyciu Azure PowerShell](tutorial-autoscale-ps.md)
- Dowiedz się więcej o [Application Gateway](overview.md).
- Dowiedz się więcej o [zaporze platformy Azure](../firewall/overview.md).
