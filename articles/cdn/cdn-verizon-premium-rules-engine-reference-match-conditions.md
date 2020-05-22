---
title: Azure CDN z warunków dopasowania aparatu programu Verizon Premium | Microsoft Docs
description: Dokumentacja referencyjna dotycząca platformy Azure Content Delivery Network z reguł Verizon w warstwie Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 3bc439e3244be63bff1c54d3230eda17dfb9d88d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745594"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN z warunków dopasowania aparatu programu Verizon Premium

W tym artykule przedstawiono szczegółowe opisy warunków dopasowania dostępne dla usługi Azure Content Delivery Network (CDN) z [aparatu reguł](cdn-verizon-premium-rules-engine.md)Verizon Premium.

Drugą częścią reguły jest warunek dopasowania. Warunek dopasowania służy do identyfikowania określonych typów żądań, dla których zostanie wykonany zestaw funkcji.

Można na przykład użyć warunku dopasowywania do:

- Filtrowanie żądań dotyczących zawartości w określonej lokalizacji.
- Filtruj żądania wygenerowane na podstawie określonego adresu IP lub kraju/regionu.
- Filtruj żądania według informacji nagłówka.

## <a name="always-match-condition"></a>Zawsze Dopasuj warunek

Warunek zawsze pasujący stosuje domyślny zestaw funkcji do wszystkich żądań.

Nazwa | Przeznaczenie
-----|--------
[Zawsze](#always) | Stosuje domyślny zestaw funkcji do wszystkich żądań.

## <a name="device-match-condition"></a>Warunek dopasowania urządzenia

Warunek dopasowania urządzenia identyfikuje żądania wysyłane z urządzenia przenośnego na podstawie jego właściwości.  

Nazwa | Przeznaczenie
-----|--------
[Urządzenie](#device) | Identyfikuje żądania wysyłane z urządzenia przenośnego na podstawie jego właściwości.

## <a name="location-match-conditions"></a>Warunki dopasowania lokalizacji

Warunki dopasowania lokalizacji określają żądania na podstawie lokalizacji obiektu żądającego.

Nazwa | Przeznaczenie
-----|--------
[Numer AS](#as-number) | Identyfikuje żądania pochodzące z określonej sieci.
[Kraj](#country) | Identyfikuje żądania pochodzące z określonych krajów/regionów.

## <a name="origin-match-conditions"></a>Warunki dopasowania pochodzenia

Warunki zgodności pochodzenia identyfikują żądania wskazujące Content Delivery Network magazynem lub serwerem pochodzenia klientów.

Nazwa | Przeznaczenie
-----|--------
[Źródło CDN](#cdn-origin) | Identyfikuje żądania dotyczące zawartości przechowywanej w magazynie Content Delivery Network.
[Pochodzenie klienta](#customer-origin) | Identyfikuje żądania dotyczące zawartości przechowywanej na określonym serwerze źródłowym klienta.

## <a name="request-match-conditions"></a>Warunki dopasowania żądania

Warunki dopasowania żądania identyfikują żądania na podstawie ich właściwości.

Nazwa | Przeznaczenie
-----|--------
[Adres IP klienta](#client-ip-address) | Identyfikuje żądania, które pochodzą z określonego adresu IP.
[Parametr cookie](#cookie-parameter) | Sprawdza pliki cookie skojarzone z poszczególnymi żądaniami dla określonej wartości.
[Wyrażenie regularne parametru cookie](#cookie-parameter-regex) | Sprawdza pliki cookie skojarzone z poszczególnymi żądaniami dla określonego wyrażenia regularnego.
[Krawędź CNAME](#edge-cname) | Identyfikuje żądania wskazujące na określoną krawędź CNAME.
[Odwołuje się do domeny](#referring-domain) | Identyfikuje żądania, które zostały określone w określonych nazwach hostów.
[Literał nagłówka żądania](#request-header-literal) | Identyfikuje żądania, które zawierają określony nagłówek ustawiony dla określonej wartości.
[Wyrażenie regularne nagłówka żądania](#request-header-regex) | Identyfikuje żądania zawierające określony nagłówek ustawiony na wartość zgodną z określonym wyrażeniem regularnym.
[Symbol wieloznaczny nagłówka żądania](#request-header-wildcard) | Identyfikuje żądania zawierające określony nagłówek ustawiony na wartość zgodną z określonym wzorcem.
[Request, metoda](#request-method) | Identyfikuje żądania według metody HTTP.
[Schemat żądania](#request-scheme) | Identyfikuje żądania według protokołu HTTP.

## <a name="url-match-conditions"></a>Warunki dopasowania adresu URL

Adresy URL odpowiadają na żądania na podstawie ich adresów URL.

Nazwa | Przeznaczenie
-----|--------
[Katalog ścieżki URL](#url-path-directory) | Identyfikuje żądania według ich ścieżki względnej.
[Rozszerzenie ścieżki URL](#url-path-extension) | Identyfikuje żądania według ich rozszerzenia nazwy pliku.
[Nazwa pliku ścieżki URL](#url-path-filename) | Identyfikuje żądania według nazwy pliku.
[Literał ścieżki adresu URL](#url-path-literal) | Porównuje względną ścieżkę żądania z określoną wartością.
[Wyrażenie regularne ścieżki adresu URL](#url-path-regex) | Porównuje względną ścieżkę żądania do określonego wyrażenia regularnego.
[Symbol wieloznaczny ścieżki adresu URL](#url-path-wildcard) | Porównuje względną ścieżkę żądania z określonym wzorcem.
[Literał zapytania URL](#url-query-literal) | Porównuje ciąg zapytania żądania do określonej wartości.
[Parametr zapytania URL](#url-query-parameter) | Identyfikuje żądania zawierające określony parametr ciągu zapytania ustawiony na wartość zgodną z określonym wzorcem.
[Wyrażenie regularne kwerendy adresu URL](#url-query-regex) | Identyfikuje żądania zawierające określony parametr ciągu zapytania ustawiony na wartość zgodną z określonym wyrażeniem regularnym.
[Symbol wieloznaczny zapytania URL](#url-query-wildcard) | Porównuje określoną wartość z ciągiem zapytania żądania.

## <a name="reference-for-rules-engine-match-conditions"></a>Dokumentacja warunków dopasowania aparatu reguł

---

### <a name="always"></a>Zawsze

Warunek zawsze pasujący stosuje domyślny zestaw funkcji do wszystkich żądań.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>Numer AS

Sieć numeru AS jest definiowana przez numer systemu autonomicznego (ASN). 

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek zgodności z liczbą:

- **Dopasowania**: wymaga, aby numer ASN sieci klienta odpowiadał jednemu z określonych numerów WPW. 
- **Niezgodne**: wymaga, aby numer ASN sieci klienta nie był zgodny z żadnym z określonych numerów WPW.

Informacje o kluczu:

- Określ wiele numerów ASN, ograniczając każdą z nich pojedynczym miejscem. Na przykład 64514 64515 dopasowuje żądania, które nadeszły z 64514 lub 64515.
- Niektóre żądania mogą nie zwracać prawidłowego numeru ASN. Znak zapytania (?) będzie odpowiadał na żądania, dla których nie można określić prawidłowego numeru ASN.
- Określ cały numer ASN dla żądanej sieci. Wartości częściowe nie zostaną dopasowane.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>Źródło CDN

Warunek dopasowania źródła CDN jest spełniony, gdy spełnione są oba z następujących warunków:

- Zażądano zawartości z magazynu CDN.
- Identyfikator URI żądania używa typu punktu dostępu do zawartości (na przykład/000001) zdefiniowanego w tym warunku dopasowywania:
  - Adres URL sieci CDN: identyfikator URI żądania musi zawierać wybrany punkt dostępu do zawartości.
  - Adres URL CNAME krawędzi: odpowiednia konfiguracja CNAME musi wskazywać na wybrany punkt dostępu do zawartości.
  
Informacje o kluczu:

- Punkt dostępu do zawartości identyfikuje usługę, która powinna obsłużyć żądaną zawartość.
- Nie używaj instrukcji AND IF, aby połączyć pewne warunki dopasowania. Na przykład połączenie warunku dopasowania źródła CDN z warunkiem dopasowania pochodzenia klienta spowoduje utworzenie wzorca dopasowania, który nigdy nie zostanie dopasowany. Z tego powodu dwa warunki dopasowania pochodzenia sieci CDN nie mogą być łączone za poorednictwem instrukcji i IF.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Adres IP klienta

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania adresu IP klienta:

- **Dopasowuje**: wymaga, aby adres IP klienta odpowiadał jednemu z określonych adresów IP. 
- Nie **pasuje**: wymaga, aby adres IP klienta nie był zgodny z żadnym z określonych adresów IP. 

Informacje o kluczu:

- Użyj notacji CIDR.
- Określ wiele adresów IP i/lub bloków adresów IP, ograniczając każdą z nich pojedynczym miejscem. Przykład:
  - **Przykład IPv4**: 1.2.3.4 10.20.30.40 dopasowuje wszystkie żądania, które dotarły do adresu 1.2.3.4 lub 10.20.30.40.
  - **Przykład IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 dopasowuje wszystkie żądania, które nadeszły z adresu 1:2:3:4:5:6:7:8 lub 10:20:30:40:50:60:70:80.
- Składnia bloku adresów IP to podstawowy adres IP, po którym następuje ukośnik i rozmiar prefiksu. Przykład:
  - **Przykład IPv4**: 5.5.5.64/26 dopasowuje wszystkie żądania odbierane z adresów 5.5.5.64 przez 5.5.5.127.
  - **Przykład IPv6**: 1:2:3:/48 dopasowuje wszystkie żądania odbierane z adresów 1:2:3:0:0:0:0:0 do 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Parametr cookie

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania parametru cookie.

- **Pasuje**: wymaga żądania, aby zawierało określony plik cookie z wartością, która pasuje do co najmniej jednej z wartości, które są zdefiniowane w tym warunku dopasowywania.
- **Niezgodne**: wymaga, aby żądanie spełniało jedno z następujących kryteriów:
  - Nie zawiera określonego pliku cookie.
  - Zawiera określony plik cookie, ale jego wartość nie jest zgodna z żadną z wartości, które są zdefiniowane w tym warunku dopasowywania.
  
Informacje o kluczu:

- Nazwa pliku cookie:
  - Ponieważ wartości symboli wieloznacznych, w tym gwiazdki (*), nie są obsługiwane podczas określania nazwy pliku cookie, tylko dokładne dopasowania nazw plików cookie kwalifikują się do porównania.
  - Dla każdego wystąpienia tego warunku dopasowywania można określić tylko jedną nazwę pliku cookie.
  - Porównania nazw plików cookie nie uwzględniają wielkości liter.
- Wartość pliku cookie:
  - Określ wiele wartości plików cookie, ograniczając każdą z nich pojedynczym miejscem.
  - Wartość cookie może korzystać z [symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Jeśli nie określono wartości symbolu wieloznacznego, tylko dokładne dopasowanie będzie spełniało ten warunek dopasowania. Na przykład określenie "value" będzie pasować do wartości "value", ale nie "wartość1" lub "wartość2".
  - Użyj opcji **Ignoruj wielkość liter** , aby określić, czy w odniesieniu do wartości cookie żądania jest wykonywane porównanie z uwzględnieniem wielkości liter.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Wyrażenie regularne parametru cookie

Warunek dopasowania wyrażenia regularnego parametru cookie definiuje nazwę i wartość pliku cookie. Można użyć [wyrażeń regularnych](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) , aby zdefiniować żądaną wartość pliku cookie.

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania wyrażenia regularnego parametru cookie.

- **Pasuje**: wymaga żądania, aby zawierało określony plik cookie o wartości zgodnej z określonym wyrażeniem regularnym.
- **Niezgodne**: wymaga, aby żądanie spełniało jedno z następujących kryteriów:
  - Nie zawiera określonego pliku cookie.
  - Zawiera określony plik cookie, ale jego wartość jest niezgodna z określonym wyrażeniem regularnym.
  
Informacje o kluczu:

- Nazwa pliku cookie:
  - Ponieważ wyrażenia regularne i wartości symboli wieloznacznych, w tym gwiazdki (*), nie są obsługiwane podczas określania nazwy pliku cookie, tylko dokładne dopasowania nazw plików cookie kwalifikują się do porównania.
  - Dla każdego wystąpienia tego warunku dopasowywania można określić tylko jedną nazwę pliku cookie.
  - Porównania nazw plików cookie nie uwzględniają wielkości liter.
- Wartość pliku cookie:
  - Wartość cookie może korzystać z wyrażeń regularnych.
  - Użyj opcji **Ignoruj wielkość liter** , aby określić, czy w odniesieniu do wartości cookie żądania jest wykonywane porównanie z uwzględnieniem wielkości liter.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Kraj

Kraj można określić za pomocą jego kodu kraju. 

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania kraju:

- **Pasuje**: wymaga, aby żądanie zawierało określone wartości kodu kraju. 
- Nie **pasuje**: wymaga, aby żądanie nie zawierało określonych wartości kodu kraju.

Informacje o kluczu:

- Określ wiele kodów krajów, ograniczając każdy z nich pojedynczym miejscem.
- Symbole wieloznaczne nie są obsługiwane podczas określania kodu kraju.
- Kody krajów "UE" i "AP" nie obejmują wszystkich adresów IP w tych regionach.
- Niektóre żądania mogą nie zwracać prawidłowego kodu kraju. Znak zapytania (?) będzie odpowiadał na żądania, dla których nie można określić prawidłowego kodu kraju.
- W kodach kraju jest rozróżniana wielkość liter.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Implementowanie filtrowania krajów przy użyciu aparatu reguł

Ten warunek dopasowania umożliwia wykonywanie wielu modyfikacji na podstawie lokalizacji, z której pochodzi żądanie. Na przykład zachowanie funkcji filtrowania kraju może być replikowane za pomocą następującej konfiguracji:

- Dopasowanie symboli wieloznacznych ścieżki adresu URL: Ustaw [warunek dopasowania symboli wieloznacznych dla ścieżki URL](#url-path-wildcard) do katalogu, który ma być zabezpieczony. 
    Dołącz gwiazdkę do końca ścieżki względnej, aby upewnić się, że dostęp do wszystkich jej elementów podrzędnych będzie ograniczony przez tę regułę.

- Dopasowanie kraju: Ustaw warunek dopasowania kraju na żądany zestaw krajów/regionów.
  - Zezwalaj: Ustaw warunek dopasowania kraju na **niezgodne** , aby zezwalać na dostęp tylko określonym krajom/regionom do zawartości przechowywanej w lokalizacji zdefiniowanej przez warunek dopasowania symboli wieloznacznych ścieżki adresu URL.
  - Blokuj: Ustaw warunek dopasowania kraju na **dopasowania** , aby zablokować określonym krajom/regionom dostęp do zawartości przechowywanej w lokalizacji zdefiniowanej przez warunek dopasowania symboli wieloznacznych ścieżki adresu URL.

- Funkcja Odmów dostępu (403): Włącz [funkcję Odmów dostępu (403)](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) , aby replikować część zezwalania lub blokowania funkcji filtrowania krajów.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Pochodzenie klienta

Informacje o kluczu:

- Warunek dopasowania pochodzenia klienta jest spełniony niezależnie od tego, czy zawartość jest zażądana przez adres URL sieci CDN, czy też adres URL granicy CNAME wskazujący na wybrane źródło odbiorcy.
- Nie można usunąć konfiguracji pochodzenia klienta, do której odwołuje się reguła, na stronie pierwotnej klienta. Przed podjęciem próby usunięcia konfiguracji pochodzenia klienta upewnij się, że następujące konfiguracje nie odwołują się do niej:
  - Warunek dopasowania pochodzenia klienta
  - Konfiguracja CNAME krawędzi
- Nie używaj instrukcji AND IF, aby połączyć pewne warunki dopasowania. Na przykład połączenie warunku dopasowania pochodzenia klienta z warunkiem dopasowania pochodzenia sieci CDN spowoduje utworzenie wzorca dopasowania, który nigdy nie zostanie dopasowany. Z tego powodu dwa warunki dopasowania pochodzenia klientów nie mogą być łączone za poorednictwem instrukcji i IF.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Urządzenie

Warunek dopasowania urządzenia identyfikuje żądania wysyłane z urządzenia przenośnego na podstawie jego właściwości. Wykrywanie urządzeń przenośnych jest realizowane za poorednictwem [WURFL](http://wurfl.sourceforge.net/). 

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania urządzenia:

- **Dopasowuje**: wymaga, aby urządzenie żądające pasowało do określonej wartości. 
- **Niezgodne**: wymaga, aby urządzenie żądające nie odpowiadało określonej wartości.

Informacje o kluczu:

- Użyj opcji **Ignoruj wielkość liter** , aby określić, czy w określonej wartości jest rozróżniana wielkość liter.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

#### <a name="string-type"></a>Typ ciągu

Funkcja WURFL zazwyczaj akceptuje dowolną kombinację cyfr, liter i symboli. Ze względu na elastyczną naturę tej możliwości należy wybrać sposób interpretowania wartości skojarzonej z tym warunkiem dopasowywania. W poniższej tabeli opisano dostępne opcje:

Typ     | Opis
---------|------------
Wpisać  | Wybierz tę opcję, aby zapobiec podejmowaniu większości znaków przy użyciu ich [wartości literału](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Znaku | Wybierz tę opcję, aby korzystać ze wszystkich [symboli wieloznacznych] ([wartości](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)wieloznaczne.
Wyrażeń    | Wybierz tę opcję, aby użyć [wyrażeń regularnych](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Wyrażenia regularne są przydatne do definiowania wzorca znaków.

#### <a name="wurfl-capabilities"></a>Możliwości WURFL

Funkcja WURFL odnosi się do kategorii opisującej urządzenia przenośne. Wybrana możliwość określa typ opisu urządzenia przenośnego, który jest używany do identyfikowania żądań.

W poniższej tabeli wymieniono możliwości WURFL i ich zmienne dla aparatu reguł.

> [!NOTE]
> Poniższe zmienne są obsługiwane w **nagłówku żądania Modyfikuj klienta** i **modyfikacji nagłówka odpowiedzi klienta** .

Możliwość | Zmienna | Opis | Przykładowe wartości
-----------|----------|-------------|----------------
Nazwa marki | % {wurfl_cap_brand_name} | Ciąg określający nazwę marki urządzenia. | Samsung
System operacyjny urządzenia | % {wurfl_cap_device_os} | Ciąg wskazujący system operacyjny zainstalowany na urządzeniu. | System iOS
Wersja systemu operacyjnego urządzenia | % {wurfl_cap_device_os_version} | Ciąg wskazujący numer wersji systemu operacyjnego zainstalowanego na urządzeniu. | 1.0.1
Podwójna Orientacja | % {wurfl_cap_dual_orientation} | Wartość logiczna wskazująca, czy urządzenie obsługuje podwójną orientację. | true
Preferowany plik DTD HTML | % {wurfl_cap_html_preferred_dtd} | Ciąg wskazujący, że dla zawartości HTML jest preferowana definicja typu dokumentu urządzenia przenośnego (DTD). | brak<br/>xhtml_basic<br/>HTML5
Deobramowanie obrazu | % {wurfl_cap_image_inlining} | Wartość logiczna wskazująca, czy urządzenie obsługuje obrazy kodowane algorytmem Base64. | fałsz
Jest systemem Android | % {wurfl_vcap_is_android} | Wartość logiczna wskazująca, czy urządzenie korzysta z systemu operacyjnego Android. | true
Jest systemem IOS | % {wurfl_vcap_is_ios} | Wartość logiczna wskazująca, czy urządzenie używa systemu iOS. | fałsz
Telewizja inteligentna | % {wurfl_cap_is_smarttv} | Wartość logiczna wskazująca, czy urządzenie jest inteligentną TV. | fałsz
Jest smartfonem | % {wurfl_vcap_is_smartphone} | Wartość logiczna wskazująca, czy urządzenie jest urządzeniem smartphone. | true
Jest tabletem | % {wurfl_cap_is_tablet} | Wartość logiczna wskazująca, czy urządzenie jest tabletem. Ten opis jest niezależny od systemu operacyjnego. | true
Jest urządzeniem bezprzewodowym | % {wurfl_cap_is_wireless_device} | Wartość logiczna wskazująca, czy urządzenie jest uznawane za urządzenie bezprzewodowe. | true
Nazwa marketingowa | % {wurfl_cap_marketing_name} | Ciąg określający nazwę marketingową urządzenia. | BlackBerry 8100 Pearl
Przeglądarka mobilna | % {wurfl_cap_mobile_browser} | Ciąg wskazujący przeglądarkę, która jest używana do żądania zawartości z urządzenia. | Chrome
Wersja przeglądarki mobilnej | % {wurfl_cap_mobile_browser_version} | Ciąg wskazujący wersję przeglądarki, która jest używana do żądania zawartości z urządzenia. | 31
Nazwa modelu | % {wurfl_cap_model_name} | Ciąg określający nazwę modelu urządzenia. | S3
Pobieranie progresywne | % {wurfl_cap_progressive_download} | Wartość logiczna wskazująca, czy urządzenie obsługuje odtwarzanie audio i wideo, gdy jest wciąż pobierane. | true
Data wydania | % {wurfl_cap_release_date} | Ciąg, który wskazuje rok i miesiąc, w którym urządzenie zostało dodane do bazy danych WURFL.<br/><br/>Formatowanie`yyyy_mm` | 2013_december
Wysokość rozwiązania | % {wurfl_cap_resolution_height} | Liczba całkowita, która wskazuje wysokość urządzenia w pikselach. | 768
Szerokość rozdzielczości | % {wurfl_cap_resolution_width} | Liczba całkowita wskazująca szerokość urządzenia w pikselach. | 1024

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Krawędź CNAME

Informacje o kluczu:

- Lista dostępnych rekordów CNAME krawędzi jest ograniczona do tych rekordów CNAME, które zostały skonfigurowane na stronie CNAMEs dla platformy, na której jest konfigurowany aparat reguł.
- Przed podjęciem próby usunięcia konfiguracji rekordu CNAME upewnij się, że warunek dopasowania CNAME krawędzi nie odwołuje się do niego. Nie można usunąć konfiguracji CNAME krawędzi, które zostały zdefiniowane w regule, na stronie CNAME krawędzi.
- Nie używaj instrukcji AND IF, aby połączyć pewne warunki dopasowania. Na przykład połączenie warunku dopasowania CNAME krawędzi z warunkiem dopasowania pochodzenia klienta spowoduje utworzenie wzorca dopasowania, który nigdy nie zostanie dopasowany. Z tego powodu dwa niegraniczne warunki dopasowania CNAME nie mogą być łączone za poorednictwem instrukcji i IF.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Odwołuje się do domeny

Nazwa hosta skojarzona z odciskiem, za pomocą którego zażądano wymagania, określa, czy odwołuje się do niej warunek domeny.

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania domeny odwołującej:

- **Dopasowuje**: wymaga, aby nazwa hosta odwołująca się do określonych wartości. 
- **Niezgodne**: wymaga, aby nazwa odwołującego hosta nie odpowiadała określonej wartości.

Informacje o kluczu:

- Określ wiele nazw hostów, ograniczając każdą z nich pojedynczym miejscem.
- Ten warunek dopasowania obsługuje [wartości symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Jeśli określona wartość nie zawiera gwiazdki, musi być dokładnym dopasowaniem nazwy hosta odwołującego się. Na przykład określenie "mydomain.com" nie jest zgodne z "www.mydomain.com".
- Użyj opcji **Ignoruj wielkość liter** , aby określić, czy jest wykonywane porównywanie z uwzględnieniem wielkości liter.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Literał nagłówka żądania

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania literału nagłówka żądania.

- **Pasuje**: wymaga żądania, aby zawierało określony nagłówek. Jego wartość musi być zgodna z tą, która jest zdefiniowana w tym warunku dopasowywania.
- **Niezgodne**: wymaga, aby żądanie spełniało jedno z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera określony nagłówek, ale jego wartość jest niezgodna z tą, która jest zdefiniowana w tym warunku dopasowywania.
  
Informacje o kluczu:

- Porównania nazw nagłówków zawsze uwzględniają wielkość liter. Użyj opcji **Ignoruj wielkość liter** , aby kontrolować uwzględnianie wielkości liter podczas porównywania wartości nagłówka.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Wyrażenie regularne nagłówka żądania

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania wyrażenia regularnego nagłówka żądania.

- **Pasuje**: wymaga żądania, aby zawierało określony nagłówek. Jego wartość musi być zgodna ze wzorcem zdefiniowanym w określonym [wyrażeniu regularnym](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Niezgodne**: wymaga, aby żądanie spełniało jedno z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera określony nagłówek, ale jego wartość jest niezgodna z określonym wyrażeniem regularnym.

Informacje o kluczu:

- Nazwa nagłówka:
  - Porównania nazw nagłówków nie uwzględniają wielkości liter.
  - Zamień spacje w nazwie nagłówka na "%20".
- Wartość nagłówka:
  - Wartość nagłówka może korzystać z wyrażeń regularnych.
  - Użyj opcji **Ignoruj wielkość liter** , aby kontrolować uwzględnianie wielkości liter podczas porównywania wartości nagłówka.
  - Warunek dopasowania jest spełniony tylko wtedy, gdy wartość nagłówka dokładnie pasuje do co najmniej jednego z określonych wzorców.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Symbol wieloznaczny nagłówka żądania

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania symboli wieloznacznych nagłówka żądania.

- **Pasuje**: wymaga żądania, aby zawierało określony nagłówek. Wartość musi być zgodna z co najmniej jedną z wartości, które są zdefiniowane w tym warunku dopasowywania.
- **Niezgodne**: wymaga, aby żądanie spełniało jedno z następujących kryteriów:
  - Nie zawiera określonego nagłówka.
  - Zawiera określony nagłówek, ale jego wartość nie jest zgodna z żadną z określonych wartości.
  
Informacje o kluczu:

- Nazwa nagłówka:
  - Porównania nazw nagłówków nie uwzględniają wielkości liter.
  - Spacje w nazwie nagłówka powinny być zastępowane "%20". Możesz również użyć tej wartości, aby określić spacje w wartości nagłówka.
- Wartość nagłówka:
  - Wartość nagłówka może korzystać z [symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Użyj opcji **Ignoruj wielkość liter** , aby kontrolować uwzględnianie wielkości liter podczas porównywania wartości nagłówka.
  - Ten warunek dopasowania jest spełniony, gdy wartość nagłówka dokładnie pasuje do co najmniej jednego z określonych wzorców.
  - Określ wiele wartości, ograniczając każdą z nich pojedynczym odstępem.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Request, metoda

Warunek dopasowania metody żądania jest spełniony tylko wtedy, gdy zasoby są żądane przez wybraną metodę żądania. Dostępne metody żądania to:

- GET
- HEAD
- POST
- Opcje
- PUT
- DELETE
- TRACE
- ŁĄCZONE

Informacje o kluczu:

- Domyślnie tylko Metoda GET Request może generować zawartość z pamięci podręcznej w sieci. Wszystkie pozostałe metody żądań są przekazywane za pomocą sieci.
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Schemat żądania

Warunek dopasowania schematu żądania jest spełniony tylko wtedy, gdy zasoby są żądane za pomocą wybranego protokołu. Dostępne są następujące protokoły:

- HTTP
- HTTPS

Informacje o kluczu:

- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
  - Wypełnienie kompletnej pamięci podręcznej
  - Domyślny maksymalny wiek wewnętrzny
  - Wymuszaj wewnętrzny maksymalny wiek
  - Ignoruj Źródło bez pamięci podręcznej
  - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>Katalog ścieżki URL

Identyfikuje żądanie przy użyciu ścieżki względnej, która wyklucza nazwę pliku żądanego elementu zawartości.

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania katalogu ścieżki adresu URL.

- **Pasuje**: wymaga, aby żądanie zawierało względną ścieżkę URL, z wyłączeniem nazwy pliku, która pasuje do określonego wzorca adresu URL.
- **Niezgodne**: wymaga, aby żądanie zawierało względną ścieżkę URL, z wyłączeniem nazwy pliku, która nie pasuje do określonego wzorca adresu URL.

Informacje o kluczu:

- Użyj opcji od **do** , aby określić, czy porównanie adresów URL rozpoczyna się przed czy po punkcie dostępu do zawartości. Punkt dostępu do zawartości jest częścią ścieżki, która pojawia się między nazwą hosta usługi Verizon CDN a ścieżką względną do żądanego zasobu (na przykład/800001/CustomerOrigin). Identyfikuje lokalizację według typu serwera (np. sieci CDN lub pochodzenia klienta) oraz numeru konta klienta.

   Następujące wartości są dostępne dla opcji **względnej** :
  - **Katalog główny**: wskazuje, że punkt porównania adresu URL rozpoczyna się bezpośrednio po nazwie hosta sieci CDN. 

  Na przykład: http: \/ /WPC.0001. &lt; Domena &gt; / **800001/weborigin/**/index.htm

  - **Źródło**: wskazuje, że punkt porównania adresu URL rozpoczyna się po punkcie dostępu do zawartości (na przykład/000001 lub/800001/myorigin). Ponieważ \* azureedge.NET rekord CNAME jest tworzony względem katalogu pierwotnego na nazwie hosta usługi Verizon CDN domyślnie, Azure CDN użytkownicy powinni używać wartości **pochodzenia** . 

  Na przykład: https: \/ / &lt; Endpoint &gt; . azureedge.NET/**folder**/index.htm 

  Ten adres URL wskazuje na następującą nazwę hosta usługi CDN Verizon: http: \/ /WPC.0001. &lt; Domena &gt; /800001/myorigin/**folder**/index.htm

- Adres URL brzegowej CNAME zostanie ponownie zapisany w adresie URL usługi CDN przed porównaniem adresu URL.

    Na przykład oba poniższe adresy URL wskazują ten sam element zawartości i dlatego mają tę samą ścieżkę URL.
  - Adres URL sieci CDN: http: \/ /WPC.0001. &lt; Domena &gt; /800001/CustomerOrigin/Path/Asset.htm
    
  - Adres URL CNAME krawędzi: http: \/ / &lt; Endpoint &gt; . azureedge.NET/Path/Asset.htm
    
    Informacje dodatkowe:
  - Domena niestandardowa: https: \/ /My.domain.com/Path/Asset.htm
    
    - Ścieżka adresu URL (względem katalogu głównego):/800001/CustomerOrigin/path/
    
    - Ścieżka adresu URL (względem źródła):/Path/

- Część adresu URL, która jest używana na potrzeby porównania adresów URL kończąca się tuż przed nazwą pliku żądanego elementu zawartości. Końcowy ukośnik jest ostatnim znakiem w ścieżce tego typu.

- Zastąp wszystkie spacje we wzorcu ścieżki URL "%20".

- Każdy wzorzec ścieżki URL może zawierać co najmniej jedną gwiazdkę (*), gdzie każda Gwiazdka dopasowuje sekwencję do jednego lub większej liczby znaków.

- Określ wiele ścieżek URL w wzorcu przez ograniczenie każdego z nich pojedynczym miejscem.

    Na przykład: */Sales/*/Marketing/

- Specyfikacja ścieżki URL może korzystać z [wartości wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Użyj opcji **Ignoruj wielkość liter** , aby określić, czy jest wykonywane porównywanie z uwzględnieniem wielkości liter.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>Rozszerzenie ścieżki URL

Identyfikuje żądania według rozszerzenia pliku żądanego elementu zawartości.

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania rozszerzenia ścieżki adresu URL.

- **Dopasowuje**: wymaga, aby adres URL żądania zawierał rozszerzenie pliku, które dokładnie pasuje do określonego wzorca.

   Na przykład w przypadku określenia "htm", "htm" są dopasowywane zasoby, ale nie "HTML".  

- Nie **pasuje**: wymaga, aby żądanie adresu URL zawierało rozszerzenie pliku, które nie jest zgodne z określonym wzorcem.

Informacje o kluczu:

- Określ rozszerzenia plików do dopasowania w polu **wartość** . Nie uwzględniaj kropki wiodącej; na przykład użyj wartości htm zamiast. htm.

- Użyj opcji **Ignoruj wielkość liter** , aby określić, czy jest wykonywane porównywanie z uwzględnieniem wielkości liter.

- Określ wiele rozszerzeń plików, ograniczając każde rozszerzenie o pojedynczej spacji. 

    Na przykład: htm HTML

- Na przykład określenie "htm" pasuje do elementów "htm", ale nie "HTML".

#### <a name="sample-scenario"></a>Przykładowy scenariusz

W poniższej konfiguracji przykładowej przyjęto założenie, że ten warunek dopasowania jest spełniony, gdy żądanie jest zgodne z jednym z określonych rozszerzeń.

Specyfikacja wartości: ASP aspx php html

Ten warunek dopasowania jest spełniony w przypadku znalezienia adresów URL kończących się na następujących rozszerzeniach:

- . ASP
- . aspx
- . php
- .html,

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>Nazwa pliku ścieżki URL

Identyfikuje żądania według nazwy pliku żądanego elementu zawartości. Na potrzeby tego warunku dopasowania nazwa pliku składa się z nazwy żądanego zasobu, kropki i rozszerzenia pliku (na przykład index. html).

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania nazwy pliku ścieżki URL.

- **Pasuje**: wymaga, aby żądanie zawierało nazwę pliku w jego ścieżce URL, która jest zgodna z określonym wzorcem.
- **Niezgodne**: wymaga, aby żądanie zawierało nazwę pliku w jego ścieżce URL, która nie jest zgodna z określonym wzorcem.

Informacje o kluczu:

- Użyj opcji **Ignoruj wielkość liter** , aby określić, czy jest wykonywane porównywanie z uwzględnieniem wielkości liter.

- Aby określić wiele rozszerzeń plików, rozdziel każde rozszerzenie za pomocą jednego miejsca.

    Na przykład: index. htm index. html

- Zamień spacje w nazwie pliku na wartość "%20".

- Wartość nazwy pliku może korzystać z [symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Na przykład każdy wzorzec nazwy pliku może składać się z co najmniej jednej gwiazdki (*), gdzie każda Gwiazdka dopasowuje sekwencję składającą się z co najmniej jednego znaku.

- Jeśli nie określono symboli wieloznacznych, tylko dokładne dopasowanie będzie spełniało ten warunek dopasowania.

    Na przykład określenie "Presentation. ppt" dopasowuje element zawartości o nazwie "Presentation. ppt", ale nie jeden o nazwie "Presentation. pptx".

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>Literał ścieżki adresu URL

Porównuje ścieżkę URL żądania, w tym nazwę pliku, z określoną wartością.

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania literału ścieżki adresu URL.

- **Pasuje**: wymaga, aby żądanie zawierało ścieżkę URL zgodną z określonym wzorcem.
- **Niezgodne**: wymaga, aby żądanie zawierało ścieżkę URL niezgodną z określonym wzorcem.

Informacje o kluczu:

- Użyj opcji **względem** , aby określić, czy punkt porównania adresu URL rozpoczyna się przed czy po punkcie dostępu do zawartości. 

    Następujące wartości są dostępne dla opcji **względnej** :
  - **Katalog główny**: wskazuje, że punkt porównania adresu URL rozpoczyna się bezpośrednio po nazwie hosta sieci CDN.

    Na przykład: http: \/ /WPC.0001. &lt; &gt; / **800001/myorigin/MyFolder/index.htm** domeny

  - **Źródło**: wskazuje, że punkt porównania adresu URL rozpoczyna się po punkcie dostępu do zawartości (na przykład/000001 lub/800001/myorigin). Ponieważ \* azureedge.NET rekord CNAME jest tworzony względem katalogu pierwotnego na nazwie hosta usługi Verizon CDN domyślnie, Azure CDN użytkownicy powinni używać wartości **pochodzenia** . 

    Na przykład: https: \/ / &lt; Endpoint &gt; . azureedge.NET/**MyFolder/index.htm**

  Ten adres URL wskazuje na następującą nazwę hosta usługi CDN Verizon: http: \/ /WPC.0001. &lt; Domena &gt; /800001/myorigin/**MyFolder/index.htm**

- Adres URL brzegowej CNAME zostanie ponownie zapisany w adresie URL usługi CDN przed porównaniem adresu URL.

Na przykład oba poniższe adresy URL wskazują ten sam element zawartości i dlatego mają tę samą ścieżkę URL:

- Adres URL sieci CDN: http: \/ /WPC.0001. &lt; Domena &gt; /800001/CustomerOrigin/Path/Asset.htm
- Adres URL CNAME krawędzi: http: \/ / &lt; Endpoint &gt; . azureedge.NET/Path/Asset.htm

    Informacje dodatkowe:
    
    - Ścieżka adresu URL (względem katalogu głównego):/800001/CustomerOrigin/path/asset.htm
   
    - Ścieżka adresu URL (względem źródła):/path/asset.htm

- Ciągi zapytania w adresie URL są ignorowane.
- Użyj opcji **Ignoruj wielkość liter** , aby określić, czy jest wykonywane porównywanie z uwzględnieniem wielkości liter.
- Wartość określona dla tego warunku dopasowania jest porównywana z ścieżką względną dokładnego żądania wykonanego przez klienta.

- Aby dopasować wszystkie żądania do określonego katalogu, użyj [katalogu ścieżki URL](#url-path-directory) lub warunku dopasowania [symboli wieloznacznych ścieżki adresu URL](#url-path-wildcard) .

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>Wyrażenie regularne ścieżki adresu URL

Porównuje ścieżkę URL żądania do określonego [wyrażenia regularnego](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania wyrażenia regularnego ścieżki adresu URL.

- **Pasuje**: wymaga, aby żądanie zawierało ścieżkę URL zgodną z określonym wyrażeniem regularnym.
- **Niezgodne**: wymaga, aby żądanie zawierało ścieżkę URL, która nie jest zgodna z określonym wyrażeniem regularnym.

Informacje o kluczu:

- Adres URL usługi CNAME zostanie ponownie zapisany w adresie URL usługi CDN przed porównaniem adresów URL.

    Na przykład oba adresy URL wskazują ten sam element zawartości i dlatego mają tę samą ścieżkę URL.

     - Adres URL sieci CDN: http: \/ /WPC.0001. &lt; Domena &gt; /800001/CustomerOrigin/Path/Asset.htm

     - Adres URL CNAME krawędzi: http: \/ /My.domain.com/Path/Asset.htm

    Informacje dodatkowe:
    
     - Ścieżka URL:/800001/CustomerOrigin/path/asset.htm

- Ciągi zapytania w adresie URL są ignorowane.
    
- Użyj opcji **Ignoruj wielkość liter** , aby określić, czy jest wykonywane porównywanie z uwzględnieniem wielkości liter.
    
- Spacje w ścieżce URL powinny być zastępowane "%20".

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>Symbol wieloznaczny ścieżki adresu URL

Porównuje względną ścieżkę URL żądania z określonym wzorcem symbolu wieloznacznego.

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania symboli wieloznacznych ścieżki adresu URL.

- **Pasuje**: wymaga, aby żądanie zawierało ścieżkę URL zgodną z określonym wzorcem symboli wieloznacznych.
- **Niezgodne**: wymaga, aby żądanie zawierało ścieżkę URL, która nie jest zgodna z określonym wzorcem symboli wieloznacznych.

Informacje o kluczu:

- **Względem** opcji: Ta opcja określa, czy punkt porównania adresu URL rozpoczyna się przed czy po punkcie dostępu do zawartości.

   Ta opcja może mieć następujące wartości:
     - **Katalog główny**: wskazuje, że punkt porównania adresu URL rozpoczyna się bezpośrednio po nazwie hosta sieci CDN.

       Na przykład: http: \/ /WPC.0001. &lt; &gt; / **800001/myorigin/MyFolder/index.htm** domeny

     - **Źródło**: wskazuje, że punkt porównania adresu URL rozpoczyna się po punkcie dostępu do zawartości (na przykład/000001 lub/800001/myorigin). Ponieważ \* azureedge.NET rekord CNAME jest tworzony względem katalogu pierwotnego na nazwie hosta usługi Verizon CDN domyślnie, Azure CDN użytkownicy powinni używać wartości **pochodzenia** . 

       Na przykład: https: \/ / &lt; Endpoint &gt; . azureedge.NET/**MyFolder/index.htm**

     Ten adres URL wskazuje na następującą nazwę hosta usługi CDN Verizon: http: \/ /WPC.0001. &lt; Domena &gt; /800001/myorigin/**MyFolder/index.htm**

- Adres URL usługi CNAME zostanie ponownie zapisany w adresie URL usługi CDN przed porównaniem adresów URL.

    Na przykład oba poniższe adresy URL wskazują ten sam element zawartości i dlatego mają tę samą ścieżkę URL:
     - Adres URL sieci CDN: http://wpc.0001.&lt ;d omain &gt; /800001/CustomerOrigin/Path/Asset.htm
     - Adres URL CNAME krawędzi: http: \/ / &lt; Endpoint &gt; . azureedge.NET/Path/Asset.htm
    
    Informacje dodatkowe:
    
     - Ścieżka adresu URL (względem katalogu głównego):/800001/CustomerOrigin/path/asset.htm
    
     - Ścieżka adresu URL (względem źródła):/path/asset.htm
    
- Określ wiele ścieżek URL, ograniczając każdą z nich pojedynczym miejscem.

   Na przykład:/Marketing/Asset. */Sales/*. htm

- Ciągi zapytania w adresie URL są ignorowane.
    
- Użyj opcji **Ignoruj wielkość liter** , aby określić, czy jest wykonywane porównywanie z uwzględnieniem wielkości liter.
    
- Zastąp spacje w ścieżce URL "%20".
    
- Wartość określona dla ścieżki URL może korzystać z [symboli wieloznacznych](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Każdy wzorzec ścieżki URL może zawierać co najmniej jedną gwiazdkę (*), gdzie każda Gwiazdka może być zgodna z sekwencją co najmniej jednego znaku.

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

W przykładowych konfiguracjach w poniższej tabeli założono, że ten warunek dopasowania jest spełniony, gdy żądanie pasuje do określonego wzorca adresu URL:

Wartość                   | Względem    | Wynik 
------------------------|----------------|-------
*/test.html */test.php  | Katalog główny lub Źródło | Ten wzorzec jest dopasowywany przez żądania dotyczące zasobów o nazwie "test. html" lub "test. php" w dowolnym folderze.
/80ABCD/origin/text/*   | Główny           | Ten wzorzec jest dopasowywany, gdy żądany zasób spełnia następujące kryteria: <br />-Musi znajdować się w pochodzeniu klienta o nazwie "Origin" (Źródło). <br />-Ścieżka względna musi rozpoczynać się od folderu o nazwie "text". Oznacza to, że żądany zasób może być umieszczony w folderze "text" lub w jednym z jego podfolderów cyklicznych.
*/CSS/* */js/*          | Katalog główny lub Źródło | Ten wzorzec jest dopasowywany przez wszystkie adresy URL sieci CDN lub brzegowe CNAME zawierające folder CSS lub js.
*. jpg *. gif *. png       | Katalog główny lub Źródło | Ten wzorzec jest dopasowywany przez wszystkie adresy URL sieci CDN lub brzegowe CNAME kończące się na. jpg,. gif lub. png. Alternatywny sposób określenia tego wzorca jest z [warunkiem dopasowania rozszerzenia ścieżki URL](#url-path-extension).
/images/* /media/*      | Origin         | Ten wzorzec jest dopasowywany przez usługi CDN lub brzegowe adresy URL CNAME, których ścieżka względna rozpoczyna się od folderu "obrazy" lub "Multimedia". <br />-Adres URL usługi CDN: http: \/ /WPC.0001. &lt; &gt;/800001/myorigin/images/Sales/event1.png domeny<br />— Przykładowy adres URL CNAME Edge: http: \/ /CDN.mydomain.com/images/Sales/event1.png

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>Literał zapytania URL

Porównuje ciąg zapytania żądania do określonej wartości.

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których spełniony jest warunek dopasowania literału zapytania adresu URL.

- **Pasuje**: wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który pasuje do określonego ciągu zapytania.
- **Niezgodne**: wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który jest niezgodny z określonym ciągiem zapytania.

Informacje o kluczu:

- Tylko dokładne dopasowania ciągu zapytania spełniają ten warunek dopasowania.
    
- Użyj opcji **Ignoruj wielkość liter** , aby kontrolować uwzględnianie wielkości liter w porównaniach ciągów zapytań.
    
- W tekście wartości ciągu zapytania nie należy umieszczać wiodącego znaku zapytania (?).
    
- Niektóre znaki wymagają kodowania adresów URL. Użyj symbolu procentu do kodowania adresów URL następujące znaki:

   Znak | Kodowanie adresu URL
   ----------|---------
   Miejsce     | %20
   &         | %25

- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Wypełnienie kompletnej pamięci podręcznej
   - Domyślny maksymalny wiek wewnętrzny
   - Wymuszaj wewnętrzny maksymalny wiek
   - Ignoruj Źródło bez pamięci podręcznej
   - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>Parametr zapytania URL

Identyfikuje żądania zawierające określony parametr ciągu zapytania. Ten parametr jest ustawiony na wartość zgodną z określonym wzorcem. Parametry ciągu zapytania (na przykład parametr = wartość) w adresie URL żądania określają, czy ten warunek jest spełniony. Ten warunek dopasowania identyfikuje parametr ciągu zapytania według jego nazwy i akceptuje co najmniej jedną wartość wartości parametru. 

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania parametru zapytania adresu URL.

- **Pasuje**: wymaga żądania, aby zawierało określony parametr z wartością, która pasuje do co najmniej jednej z wartości, które są zdefiniowane w tym warunku dopasowywania.
- **Niezgodne**: wymaga, aby żądanie spełniało jedno z następujących kryteriów:
  - Nie zawiera określonego parametru.
  - Zawiera określony parametr, ale jego wartość nie jest zgodna z żadną z wartości, które są zdefiniowane w tym warunku dopasowywania.

Ten warunek dopasowania zapewnia łatwy sposób określania kombinacji nazwy i wartości parametrów. Aby uzyskać większą elastyczność w przypadku dopasowania parametru ciągu zapytania, należy rozważyć użycie warunku dopasowania [symboli wieloznacznych zapytania adresu URL](#url-query-wildcard) .

Informacje o kluczu:

- Dla każdego wystąpienia tego warunku dopasowywania można określić tylko nazwę parametru kwerendy pojedynczego adresu URL.
    
- Ponieważ wartości symboli wieloznacznych nie są obsługiwane, jeśli określono nazwę parametru, tylko dokładne dopasowania nazw parametrów mogą być porównywane.
- Wartości parametrów mogą zawierać [symbole wieloznaczne](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Każdy wzorzec wartości parametru może składać się z co najmniej jednej gwiazdki (*), gdzie każda Gwiazdka może być zgodna z sekwencją składającą się z co najmniej jednego znaku.
   - Niektóre znaki wymagają kodowania adresów URL. Użyj symbolu procentu do kodowania adresów URL następujące znaki:

       Znak | Kodowanie adresu URL
       ----------|---------
       Miejsce     | %20
       &         | %25

- Określ wiele wartości parametrów ciągu zapytania, ograniczając każdą z nich pojedynczym odstępem. Ten warunek dopasowania jest spełniony, gdy żądanie zawiera jedną z określonych kombinacji nazwa/wartość.

   - Przykład 1:

     - Konfiguracja:

       Wartość Wartośćb

     - Ta konfiguracja dopasowuje następujące parametry ciągu zapytania:

       Parametr1 = wartość
    
       Parametr1 = Wartośćb

   - Przykład 2:

     - Konfiguracja: 

        Wartość% 20A wartość% 20B

     - Ta konfiguracja dopasowuje następujące parametry ciągu zapytania:

       Parametr1 = wartość% 20A

       Parametr1 = wartość% 20B

- Ten warunek dopasowania jest spełniony tylko wtedy, gdy istnieje dokładne dopasowanie do co najmniej jednego z określonych kombinacji nazwa/wartość ciągu zapytania.

   Na przykład jeśli używasz konfiguracji w poprzednim przykładzie, kombinacja nazwa/wartość parametru "parametr1 = ValueAdd" nie zostanie uznana za dopasowanie. Jeśli jednak określisz jedną z następujących wartości, będzie ona pasować do tej kombinacji nazwa/wartość:

   - Wartość Wartośćb ValueAdd
   - Wartość a * Wartośćb

- Użyj opcji **Ignoruj wielkość liter** , aby kontrolować uwzględnianie wielkości liter w porównaniach ciągów zapytań.
    
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Wypełnienie kompletnej pamięci podręcznej
   - Domyślny maksymalny wiek wewnętrzny
   - Wymuszaj wewnętrzny maksymalny wiek
   - Ignoruj Źródło bez pamięci podręcznej
   - Wewnętrzna maksymalna — nieodświeżona

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Poniższy przykład ilustruje sposób działania tej opcji w określonych sytuacjach:

Nazwa  | Wartość |  Wynik
------|-------|--------
Użytkownik  | Jan   | Ten wzorzec jest dopasowywany, gdy ciąg zapytania dla żądanego adresu URL to "? User = Jan".
Użytkownik  | *     | Ten wzorzec jest dopasowywany, gdy ciąg zapytania dla żądanego adresu URL zawiera parametr użytkownika.
Poczta e-mail | Jan\* | Ten wzorzec jest dopasowywany, gdy ciąg zapytania dla żądanego adresu URL zawiera parametr poczty E-mail zaczynający się od ciągu "Jan".

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>Wyrażenie regularne kwerendy adresu URL

Identyfikuje żądania zawierające określony parametr ciągu zapytania. Ten parametr jest ustawiony na wartość zgodną z określonym [wyrażeniem regularnym](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których spełniony jest warunek dopasowania wyrażenia regularnego zapytania adresu URL.

- **Pasuje**: wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który odpowiada określonemu wyrażeniu regularnemu.
- **Niezgodne**: wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który nie jest zgodny z określonym wyrażeniem regularnym.

Informacje o kluczu:

- Ten warunek dopasowania spełnia tylko dokładne dopasowania do określonego wyrażenia regularnego.
    
- Użyj opcji **Ignoruj wielkość liter** , aby kontrolować uwzględnianie wielkości liter w porównaniach ciągów zapytań.
    
- Na potrzeby tej opcji ciąg zapytania rozpoczyna się od pierwszego znaku po znaku zapytania (?) dla ciągu zapytania.
    
- Niektóre znaki wymagają kodowania adresów URL. Użyj symbolu procentu do kodowania adresów URL następujące znaki:

   Znak | Kodowanie adresu URL | Wartość
   ----------|--------------|------
   Miejsce     | %20          | \%20C
   &         | %25          | \%6,25

   Należy zauważyć, że symbole procentowe muszą być zmienione.

- Podwójne ucieczki specjalne znaki wyrażenia regularnego (na przykład \^ $. +), aby uwzględnić ukośnik odwrotny w wyrażeniu regularnym.

   Przykład:

   Wartość | Interpretowane jako 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Wypełnienie kompletnej pamięci podręcznej
   - Domyślny maksymalny wiek wewnętrzny
   - Wymuszaj wewnętrzny maksymalny wiek
   - Ignoruj Źródło bez pamięci podręcznej
   - Wewnętrzna maksymalna — nieodświeżona

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>Symbol wieloznaczny zapytania URL

Porównuje określone wartości z ciągiem zapytania żądania.

Opcja **dopasowania**nie / **zgadza** się określa warunki, w których jest spełniony warunek dopasowania symboli wieloznacznych zapytania adresu URL.

- **Pasuje**: wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który jest zgodny z określoną wartością symbolu wieloznacznego.
- **Niezgodne**: wymaga, aby żądanie zawierało ciąg zapytania adresu URL, który nie jest zgodny z określoną wartością symbolu wieloznacznego.

Informacje o kluczu:

- Na potrzeby tej opcji ciąg zapytania rozpoczyna się od pierwszego znaku po znaku zapytania (?) dla ciągu zapytania.
- Wartości parametrów mogą zawierać [symbole wieloznaczne](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Każdy wzorzec wartości parametru może składać się z co najmniej jednej gwiazdki (*), gdzie każda Gwiazdka może być zgodna z sekwencją składającą się z co najmniej jednego znaku.
   - Niektóre znaki wymagają kodowania adresów URL. Użyj symbolu procentu do kodowania adresów URL następujące znaki:

     Znak | Kodowanie adresu URL
     ----------|---------
     Miejsce     | %20
     &         | %25

- Określ wiele wartości, ograniczając każdą z nich pojedynczym odstępem.

   Na przykład: *parametr1 = wartość* *Wartośćb* *parametr1 = ValueC&parametr2 = Value*

- Ten warunek dopasowania spełnia tylko dokładne dopasowania do co najmniej jednego z określonych wzorców ciągu zapytania.
    
- Użyj opcji **Ignoruj wielkość liter** , aby kontrolować uwzględnianie wielkości liter w porównaniach ciągów zapytań.
    
- Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ten warunek dopasowania jest niezgodny z następującymi funkcjami:
   - Wypełnienie kompletnej pamięci podręcznej
   - Domyślny maksymalny wiek wewnętrzny
   - Wymuszaj wewnętrzny maksymalny wiek
   - Ignoruj Źródło bez pamięci podręcznej
   - Wewnętrzna maksymalna — nieodświeżona

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Poniższy przykład ilustruje sposób działania tej opcji w określonych sytuacjach:

 Nazwa                 | Opis
 ---------------------|------------
Użytkownik = Jan              | Ten wzorzec jest dopasowywany, gdy ciąg zapytania dla żądanego adresu URL to "? User = Jan".
\*Użytkownik = \* \* optout =\* | Ten wzorzec jest dopasowywany, gdy zapytanie adresu URL sieci CDN zawiera parametr User lub OptOut.

[Powrót do początku](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>Następne kroki

- [Omówienie usługi Azure Content Delivery Network](cdn-overview.md)
- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funkcje aparatu reguł](cdn-verizon-premium-rules-engine-reference-features.md)
- [Zastępowanie domyślnego zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
