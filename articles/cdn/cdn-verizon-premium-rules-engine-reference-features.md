---
title: Azure CDN z funkcji aparatu reguł Verizon Premium | Microsoft Docs
description: Dokumentacja referencyjna Azure CDN z funkcji aparatu reguł Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: d2d4090934a940809fe75ad70e0650eb1c9353f1
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872714"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN z funkcji aparatu reguł Verizon Premium

W tym artykule przedstawiono szczegółowe opisy funkcji [aparatu reguł](cdn-verizon-premium-rules-engine.md)usługi Azure Content Delivery Network (CDN).

Trzecią częścią reguły jest funkcja. Funkcja definiuje typ akcji, która jest stosowana do typu żądania, który jest identyfikowany przez zestaw warunków dopasowywania.


Najnowsze funkcje można znaleźć w [dokumentacji aparatu reguł Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).


## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Informacje o Azure CDN z funkcji aparatu reguł Verizon Premium

---

### <a name="age-response-header"></a>Nagłówek odpowiedzi dla wieku

**Cel**: określa, czy nagłówek odpowiedzi na wiek jest uwzględniony w odpowiedzi wysyłanej do osoby żądającej.

Wartość|Wynik
--|--
Enabled (Włączony) | Nagłówek odpowiedzi na wiek jest uwzględniany w odpowiedzi wysyłanej do osoby żądającej.
Disabled (Wyłączony) | Nagłówek odpowiedzi na wiek jest wykluczony z odpowiedzi wysyłanej do osoby żądającej.

**Zachowanie domyślne**: wyłączone.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Parametry przepustowości

**Cel:** Określa, czy parametry ograniczenia przepustowości (na przykład ec_rate i ec_prebuf) są aktywne.

Parametry ograniczania przepustowości określają, czy szybkość transferu danych dla żądania klienta jest ograniczona do szybkości niestandardowej.

Wartość|Wynik
--|--
Enabled (Włączony)|Umożliwia punktom pop uznawanie żądań ograniczenia przepustowości.
Disabled (Wyłączony)|Powoduje ignorowanie parametrów ograniczenia przepustowości. Żądana zawartość jest zwykle obsługiwana (bez ograniczania przepustowości).

**Zachowanie domyślne:** Dostępny.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Ograniczanie przepustowości

**Cel:** Ogranicza przepustowość odpowiedzi dostarczonych przez punkty obecności.

Aby prawidłowo skonfigurować ograniczanie przepustowości, należy zdefiniować obie poniższe opcje.

Opcja|Opis
--|--
Kilobajty na sekundę|Ustaw tę opcję na maksymalną przepustowość (KB na sekundę), która może być używana do dostarczania odpowiedzi.
Prebuf sekund|Ustaw tę opcję na liczbę sekund oczekiwania dla punktów obecności do momentu ograniczenia przepustowości. Ten okres nieograniczonej przepustowości polega na zapobieganiu Stuttering lub buforowania problemów spowodowanych ograniczeniami przepustowości przez odtwarzacz multimedialny.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Pomiń pamięć podręczną

**Cel:** Określa, czy żądanie powinno obejść buforowanie.

Wartość|Wynik
--|--
Enabled (Włączony)|Powoduje, że wszystkie żądania przepadają na serwer pierwotny, nawet jeśli zawartość była wcześniej buforowana w punktach obecności.
Disabled (Wyłączony)|Powoduje, że punkty obecności buforują zasoby zgodnie z zasadami pamięci podręcznej zdefiniowanymi w jego nagłówkach odpowiedzi.

**Zachowanie domyślne:**

- **Duże http:** Wyłączony

<!---
- **ADN:** Enabled

--->

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Metody HTTP w pamięci podręcznej

**Cel:** Określa zestaw dodatkowych metod HTTP, które mogą być buforowane w sieci.

Informacje o kluczu:

- Ta funkcja zakłada, że odpowiedzi GET powinny być zawsze buforowane. W efekcie Metoda GET HTTP nie powinna być uwzględniana podczas ustawiania tej funkcji.
- Ta funkcja obsługuje tylko metodę POST protokołu HTTP. Włącz buforowanie odpowiedzi POST, ustawiając tę funkcję na `POST` .
- Domyślnie tylko żądania, których treść jest mniejsza niż 14 KB, są buforowane. Użyj funkcji rozmiaru treści żądania pamięci podręcznej w celu ustawienia maksymalnego rozmiaru treści żądania.

**Zachowanie domyślne:** Tylko odpowiedzi GET są buforowane.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Rozmiar treści żądania pamięci podręcznej

**Cel:** Definiuje próg określania, czy odpowiedź na wpis może być buforowana.

Ten próg jest określany przez określenie maksymalnego rozmiaru treści żądania. Żądania zawierające większą treść żądania nie są buforowane.

Informacje o kluczu:

- Ta funkcja ma zastosowanie tylko wtedy, gdy odpowiedzi POST są uprawnione do buforowania. Aby włączyć buforowanie żądań POST, użyj funkcji HTTP metod w pamięci podręcznej.
- Treść żądania jest brana pod uwagę dla:
    - wartości x-www-form-urlencoded
    - Zapewnianie unikatowego klucza pamięci podręcznej
- Definiowanie dużego maksymalnego rozmiaru treści żądania może mieć wpływ na wydajność dostarczania danych.
    - **Zalecana wartość:** 14 KB
    - **Wartość minimalna:** 1 KB

**Zachowanie domyślne:** 14 KB

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Przetwarzanie nagłówka kontroli pamięci podręcznej

**Cel:** Steruje generowaniem `Cache-Control` nagłówków w punkcie pop, gdy aktywna jest funkcja max-age.

Najprostszym sposobem osiągnięcia tego typu konfiguracji jest umieszczenie zewnętrznego maksymalnego wieku i funkcji obróbki nagłówka Cache-Control w tej samej instrukcji.

Wartość|Wynik
--|--
Zastąp|Zapewnia, że wystąpią następujące akcje:<br/> — Zastępuje `Cache-Control` nagłówek wygenerowany przez serwer pochodzenia. <br/>— Dodaje `Cache-Control` nagłówek utworzony przez zewnętrzną funkcję max-age do odpowiedzi.
Przekazuj|Zapewnia, że `Cache-Control` nagłówek utworzony przez zewnętrzną funkcję max-age nigdy nie zostanie dodany do odpowiedzi. <br/> Jeśli serwer pochodzenia tworzy `Cache-Control` nagłówek, przechodzi do użytkownika końcowego. <br/> Jeśli serwer pierwotny nie tworzy `Cache-Control` nagłówka, ta opcja może spowodować, że nagłówek odpowiedzi nie będzie zawierał `Cache-Control` nagłówka.
Dodaj, jeśli brakuje|Jeśli `Cache-Control` nagłówek nie został odebrany z serwera pochodzenia, ta opcja spowoduje dodanie `Cache-Control` nagłówka utworzonego przez zewnętrzną funkcję max-age. Ta opcja jest przydatna do zapewnienia, że wszystkie zasoby są przypisane do `Cache-Control` nagłówka.
Usuń| Ta opcja zapewnia, że `Cache-Control` nagłówek nie jest dołączony do odpowiedzi nagłówka. Jeśli `Cache-Control` nagłówek został już przypisany, zostaje usunięty z odpowiedzi nagłówka.

**Zachowanie domyślne:** Pisz.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Ciąg zapytania klucza pamięci podręcznej

**Cel:** Określa, czy klucz pamięci podręcznej dołącza lub wyklucza parametry ciągu zapytania skojarzone z żądaniem.

Informacje o kluczu:

- Określ jedną lub więcej nazw parametrów ciągu zapytania i oddziel poszczególne nazwy parametrów pojedynczym spacją.
- Ta funkcja określa, czy parametry ciągu zapytania są dołączone lub wykluczone z klucza pamięci podręcznej. Dodatkowe informacje są dostępne dla każdej opcji w poniższej tabeli.

Typ|Opis
--|--
 Uwzględnij|  Wskazuje, że każdy określony parametr powinien zostać uwzględniony w kluczu pamięci podręcznej. Dla każdego żądania, które zawiera unikatową wartość dla parametru ciągu zapytania zdefiniowanego w tej funkcji, generowany jest unikatowy klucz pamięci podręcznej.
 Uwzględnij wszystko  |Wskazuje, że dla każdego żądania do elementu zawartości jest tworzony unikatowy klucz pamięci podręcznej, który zawiera unikatowy ciąg zapytania. Ten typ konfiguracji nie jest zazwyczaj zalecany, ponieważ może to prowadzić do niewielkiej wartości procentowej trafień w pamięci podręcznej. Niska liczba trafień w pamięci podręcznej zwiększa obciążenie na serwerze źródłowym, ponieważ musi obciążać więcej żądań. Ta konfiguracja duplikuje zachowanie buforowania znane jako "unikatowy bufor" na stronie buforowanie ciągu zapytania.
 Exclude | Wskazuje, że tylko określone parametry są wykluczone z klucza pamięci podręcznej. Wszystkie inne parametry ciągu zapytania są zawarte w kluczu pamięci podręcznej.
 Wyklucz wszystko  |Wskazuje, że wszystkie parametry ciągu zapytania są wykluczone z klucza pamięci podręcznej. Ta konfiguracja duplikuje domyślne zachowanie buforowania "standardowa pamięć podręczna" na stronie buforowanie ciągu zapytania.  

Aparat reguł pozwala dostosować sposób, w jaki są implementowane buforowanie ciągu zapytania. Na przykład można określić, że buforowanie ciągu zapytania jest wykonywane tylko w określonych lokalizacjach lub typach plików.

Aby zduplikować zachowanie buforowania ciągu zapytania "Brak pamięci podręcznej" na stronie buforowanie ciągu zapytania, Utwórz regułę zawierającą warunek dopasowania symboli wieloznacznych zapytania URL i funkcję pomijania pamięci podręcznej. Ustaw warunek dopasowania symboli wieloznacznych zapytania URL na gwiazdkę (*).

>[!IMPORTANT]
> Jeśli autoryzacja tokenu jest włączona dla dowolnej ścieżki na tym koncie, tryb pamięci podręcznej jest jedynym trybem, który może być używany do buforowania ciągu zapytania. Aby uzyskać więcej informacji, zobacz [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md) (Sterowanie zachowaniem buforowania usługi CDN za pomocą ciągów zapytań).

#### <a name="sample-scenarios"></a>Przykładowe scenariusze

Następujące przykładowe użycie tej funkcji zapewnia przykładowe żądanie i domyślny klucz pamięci podręcznej:

- **Przykładowe żądanie:** http://wpc.0001.&lt ;D omain &gt; /800001/Origin/folder/Asset.htm? identyfikator_sesji = 1234&language = EN&UserID = 01
- **Domyślny klucz pamięci podręcznej:** /800001/Origin/folder/Asset.htm

##### <a name="include"></a>Uwzględnij

Przykładowa konfiguracja:

- **Typ:** Być
- **Parametry:** język

Ten typ konfiguracji spowoduje wygenerowanie następującego klucza pamięci podręcznej parametru ciągu zapytania:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Uwzględnij wszystko

Przykładowa konfiguracja:

- **Typ:** Uwzględnij wszystko

Ten typ konfiguracji spowoduje wygenerowanie następującego klucza pamięci podręcznej parametru ciągu zapytania:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Przykładowa konfiguracja:

- **Typ:** Klucza
- **Parametry: Identyfikator** użytkownika sesji

Ten typ konfiguracji spowoduje wygenerowanie następującego klucza pamięci podręcznej parametru ciągu zapytania:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Wyklucz wszystko

Przykładowa konfiguracja:

- **Typ:** Wyklucz wszystko

Ten typ konfiguracji spowoduje wygenerowanie następującego klucza pamięci podręcznej parametru ciągu zapytania:

    /800001/Origin/folder/asset.htm

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Pamięć podręczna — ponowne zapisywanie klucza

**Cel:** Ponownie zapisuje klucz pamięci podręcznej skojarzony z żądaniem.

Klucz pamięci podręcznej jest ścieżką względną identyfikującą element zawartości na potrzeby buforowania. Innymi słowy serwery sprawdzają, czy w pamięci podręcznej znajduje się buforowana wersja elementu zawartości, zgodnie ze ścieżką określoną przez jego klucz pamięci podręcznej.

Skonfiguruj tę funkcję przez zdefiniowanie obu następujących opcji:

Opcja|Opis
--|--
Oryginalna ścieżka| Zdefiniuj ścieżkę względną do typów żądań, których klucz buforu został ponownie zapisany. Ścieżkę względną można zdefiniować, wybierając podstawową ścieżkę źródłową, a następnie definiując wzorzec wyrażenia regularnego.
Nowa ścieżka|Zdefiniuj ścieżkę względną dla nowego klucza pamięci podręcznej. Ścieżkę względną można zdefiniować, wybierając podstawową ścieżkę źródłową, a następnie definiując wzorzec wyrażenia regularnego. Tę ścieżkę względną można dynamicznie budować przy użyciu [zmiennych http](cdn-http-variables.md).

**Zachowanie domyślne:** Klucz pamięci podręcznej żądania jest określany przez identyfikator URI żądania.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Komentarz

**Cel:** Umożliwia dodanie notatki w ramach reguły.

Jednym z zastosowań tej funkcji jest dostarczenie dodatkowych informacji na ogólnym przeznaczeniu reguły lub dlaczego określony warunek dopasowania lub funkcja została dodana do reguły.

Informacje o kluczu:

- Można określić maksymalnie 150 znaków.
- Używaj tylko znaków alfanumerycznych.
- Ta funkcja nie ma wpływu na zachowanie reguły. Wystarczy podać obszar, w którym można podać informacje do użytku w przyszłości lub, które mogą być pomocne podczas rozwiązywania problemów z regułą.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Wypełnienie kompletnej pamięci podręcznej

**Cel:** Określa, co się dzieje, gdy żądanie spowoduje przeznaczenie częściowej pamięci podręcznej w punkcie obecności.

Wartość chybień częściowej pamięci podręcznej opisuje stan pamięci podręcznej dla zasobu, który nie został całkowicie pobrany do punktu obecności. Jeśli zasób jest tylko częściowo buforowany w punkcie obecności, następne żądanie dla tego elementu zawartości zostanie ponownie przekazane do serwera pochodzenia.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Część przełączenia pamięci podręcznej zwykle występuje, gdy użytkownik przerywa pobieranie lub dla zasobów, które są wymagane wyłącznie przy użyciu żądań zakresu HTTP. Ta funkcja jest najbardziej przydatna w przypadku dużych zasobów, które nie są zwykle pobierane od początku do końca (na przykład wideo). W związku z tym ta funkcja jest domyślnie włączona na dużej platformie protokołu HTTP. Jest on wyłączony na wszystkich innych platformach.

Zachowaj domyślną konfigurację dla dużej platformy HTTP, ponieważ zmniejsza obciążenie serwera pochodzenia klienta i zwiększa szybkość, z jaką klienci pobierają zawartość.

Wartość|Wynik
--|--
Enabled (Włączony)|Przywraca zachowanie domyślne. Domyślnym zachowaniem jest wymuszenie, aby wymusić zainicjowanie przez punkt obecności w tle pobierania elementu zawartości z serwera pochodzenia. Następnie zasób będzie znajdować się w lokalnej pamięci podręcznej.
Disabled (Wyłączony)|Zapobiega wykonaniu przez punkt obecności pobierania w tle dla elementu zawartości. W efekcie następne żądanie dla tego elementu zawartości z tego regionu powoduje, że punkt obecności zażąda go od serwera pochodzenia klienta.

**Zachowanie domyślne:** Dostępny.

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:

- Numer AS
- Adres IP klienta
- Parametr cookie
- Wyrażenie regularne parametru cookie
- Kraj
- Urządzenie
- Rekord CNAME przeglądarki Microsoft Edge
- Odwołuje się do domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania URL
- Wyrażenie regularne kwerendy adresu URL
- Symbol wieloznaczny zapytania URL
- Parametr zapytania URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Kompresuj typy plików

**Cel:** Definiuje formaty plików skompresowanych na serwerze.

Format pliku można określić za pomocą typu nośnika internetowego (na przykład Content-Type). Typ nośnika internetowego to metadane niezależne od platformy, które umożliwiają serwerom identyfikację formatu pliku określonego elementu zawartości. Poniżej przedstawiono listę typowych typów multimediów internetowych.

Typ multimediów internetowych|Opis
--|--
tekst/zwykły|Pliki zwykłego tekstu
text/html| Pliki HTML
text/css|Kaskadowe arkusze stylów (CSS)
aplikacja/x — JavaScript|JavaScript
Aplikacja/JavaScript|JavaScript

Informacje o kluczu:

- Określ wiele typów nośników internetowych, ograniczając każdą z nich pojedynczym miejscem.
- Ta funkcja kompresuje tylko zasoby, których rozmiar jest mniejszy niż 1 MB. Większe zasoby nie są kompresowane przez serwery.
- Niektóre typy zawartości, takie jak obrazy, wideo i zasoby multimediów audio (na przykład JPG, MP3, MP4 itp.), zostały już skompresowane. Ponieważ dodatkowa kompresja dla tych typów zasobów nie zmniejsza znacząco rozmiaru pliku, zaleca się, aby nie włączać na nich kompresji.
- Symbole wieloznaczne, takie jak gwiazdki, nie są obsługiwane.
- Przed dodaniem tej funkcji do reguły należy ustawić opcję kompresja wyłączona na stronie kompresja dla platformy, do której zostanie zastosowana ta reguła.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Pole dziennika niestandardowego 1

**Cel:** Określa format i zawartość, która zostanie przypisana do pola dziennika niestandardowego w nieprzetworzonym pliku dziennika.

To pole niestandardowe pozwala określić, które żądania i wartości nagłówka odpowiedzi są przechowywane w plikach dziennika.

Domyślnie pole dziennika niestandardowego nosi nazwę "x-ec_custom-1". Nazwę tego pola można dostosować na stronie ustawień nieprzetworzonych dzienników.

Format służący do określania nagłówków żądań i odpowiedzi definiuje się w następujący sposób:

Typ nagłówka|Format|Przykłady
-|-|-
Nagłówek żądania|`%{[RequestHeader]()}[i]()` | % {Accept-Encoding} i <br/> {Odłożone} i <br/> % {Authorization} i
Nagłówek odpowiedzi|`%{[ResponseHeader]()}[o]()`| % {Wiek} o <br/> % {Content-Type} o <br/> % {Cookie} o

Informacje o kluczu:

- Pole dziennika niestandardowego może zawierać dowolną kombinację pól nagłówka i zwykłego tekstu.
- Prawidłowe znaki dla tego pola są następujące: alfanumeryczne (0-9, a-z, a-Z), kreski, dwukropek, pół litery, apostrofy, przecinki, kropki, znaki podkreślenia, znaku równości, nawiasy klamrowe i spacje. Symbol procentu i nawiasy klamrowe są dozwolone tylko w przypadku określenia pola nagłówka.
- Pisownia dla każdego określonego pola nagłówka musi być zgodna z nazwą żądanego nagłówka żądania/odpowiedzi.
- Jeśli chcesz określić wiele nagłówków, Użyj separatora, aby wskazać każdy nagłówek. Na przykład można użyć skrótu dla każdego nagłówka:
    - AE:% {Accept-Encoding} i A:% {Authorization} i CT:% {Content-Type} o

**Wartość domyślna:** -

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Nagłówki odpowiedzi w pamięci podręcznej debugowania

**Cel:** Określa, czy odpowiedź może zawierać [nagłówki odpowiedzi X-we-Debug](cdn-http-debug-headers.md), które zawierają informacje dotyczące zasad pamięci podręcznej dla żądanego elementu zawartości.

Nagłówki odpowiedzi pamięci podręcznej debugowania zostaną uwzględnione w odpowiedzi, gdy są spełnione oba poniższe warunki:

- Funkcja nagłówków odpowiedzi w pamięci podręcznej debugowania została włączona w określonym żądaniu.
- Określone żądanie definiuje zestaw nagłówków odpowiedzi w pamięci podręcznej debugowania, które zostaną uwzględnione w odpowiedzi.

Nagłówki odpowiedzi pamięci podręcznej debugowania mogą być wymagane przez dołączenie następującego nagłówka i określonych dyrektyw w żądaniu:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Przykład:**

X-we-Debug: x-we-cache, x-we-Check-Cached, x-EC-cache-Key, x-EC-cache-State

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania dla nagłówków odpowiedzi w pamięci podręcznej debugowania zwracają odpowiedź obejmującą nagłówek X-we-Debug.
Disabled (Wyłączony)|Nagłówek odpowiedzi X-we-Debug zostanie wykluczony z odpowiedzi.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Domyślny maksymalny wiek wewnętrzny

**Cel:** Określa domyślny interwał maksymalnego wieku dla punktu obecności do ponownego sprawdzania poprawności pamięci podręcznej serwera. Innymi słowy czas, który zostanie przekazany przed punktem obecności, sprawdzi, czy buforowany element zawartości jest zgodny z zasobem przechowywanym na serwerze źródłowym.

Informacje o kluczu:

- Ta akcja będzie odbywać się tylko w przypadku odpowiedzi z serwera pochodzenia, który nie przypisał znaku Max-Age w `Cache-Control` `Expires` nagłówku lub.
- Ta akcja nie będzie wykonywana dla zasobów, które nie są uznawane za pamięci podręcznej.
- Ta akcja nie ma wpływu na walidacje pamięci podręcznej przeglądarki. Te typy walidacji są określane przez `Cache-Control` lub w `Expires` nagłówkach wysyłanych do przeglądarki, które można dostosować przy użyciu zewnętrznej funkcji max-age.
- Wyniki tej akcji nie mają zauważalnego wpływu na nagłówki odpowiedzi i zawartość zwróconą z punktów obecności dla zawartości, ale mogą mieć wpływ na ilość ruchu zwrotnego wysyłanego z punktów obecności na serwer pierwotny.
- Skonfiguruj tę funkcję przez:
    - Wybieranie kodu stanu, dla którego można zastosować domyślny maksymalny wiek wewnętrzny.
    - Określenie wartości całkowitej, a następnie wybranie odpowiedniej jednostki czasu (na przykład sekund, minut, godzin itd.). Ta wartość definiuje domyślny wewnętrzny interwał maksymalny w wieku.

- Ustawienie jednostki czasu na wartość "off" spowoduje przypisanie domyślnego wewnętrznego interwału maksymalnego wieku wynoszącego 7 dni dla żądań, do których nie przypisano znaku Max-Age w `Cache-Control` `Expires` nagłówku lub.

**Wartość domyślna:** 7 dni

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:
- Numer AS
- Adres IP klienta
- Parametr cookie
- Wyrażenie regularne parametru cookie
- Kraj
- Urządzenie
- Krawędź CNAME
- Odwołuje się do domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania URL
- Wyrażenie regularne kwerendy adresu URL
- Symbol wieloznaczny zapytania URL
- Parametr zapytania URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Odmów dostępu (403)

**Cel**: określa, czy wszystkie żądania są odrzucane z niedostępną odpowiedzią 403.

Wartość | Wynik
------|-------
Enabled (Włączony)| Powoduje, że wszystkie żądania, które spełniają kryteria dopasowywania, zostaną odrzucone z niedostępną odpowiedzią 403.
Disabled (Wyłączony)| Przywraca zachowanie domyślne. Domyślne zachowanie polega na umożliwieniu serwerowi pochodzenia określenia typu odpowiedzi, która zostanie zwrócona.

**Zachowanie domyślne**: wyłączone

> [!TIP]
   > Jednym z możliwych użycia tej funkcji jest skojarzenie jej z warunkiem dopasowania nagłówka żądania, aby zablokować dostęp do odwołujących się elementów HTTP, które używają linków wbudowanych do zawartości.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Wygasa traktowanie nagłówków

**Cel:** Steruje generowaniem `Expires` nagłówków w punkcie pop, gdy aktywna jest funkcja max-age.

Najprostszym sposobem osiągnięcia tego typu konfiguracji jest umieszczenie w tej samej instrukcji funkcji przedziału zewnętrzny maksymalny wiek i wygaśnięcie.

Wartość|Wynik
--|--
Zastąp|Zapewnia następujące działania:<br/>— Zastępuje `Expires` nagłówek wygenerowany przez serwer pochodzenia.<br/>— Dodaje `Expires` nagłówek utworzony przez zewnętrzną funkcję max-age do odpowiedzi.
Przekazuj|Zapewnia, że `Expires` nagłówek utworzony przez zewnętrzną funkcję max-age nigdy nie zostanie dodany do odpowiedzi. <br/> Jeśli serwer pochodzenia generuje `Expires` nagłówek, przejdzie do użytkownika końcowego. <br/>Jeśli serwer pierwotny nie wygenerował `Expires` nagłówka, ta opcja może spowodować, że nagłówek odpowiedzi nie będzie zawierał `Expires` nagłówka.
Dodaj, jeśli brakuje| Jeśli `Expires` nagłówek nie został odebrany z serwera pochodzenia, ta opcja spowoduje dodanie `Expires` nagłówka utworzonego przez zewnętrzną funkcję max-age. Ta opcja jest przydatna do zapewnienia, że do wszystkich zasobów zostanie przypisany `Expires` nagłówek.
Usuń| Zapewnia, że `Expires` nagłówek nie jest dołączony do odpowiedzi nagłówka. Jeśli `Expires` nagłówek został już przypisany, zostaje usunięty z odpowiedzi nagłówka.

**Zachowanie domyślne:** Pisz

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Zewnętrzny maksymalny wiek

**Cel:** Określa maksymalny interwał ważności przeglądarki w celu ponownego sprawdzania poprawności pamięci podręcznej. Innymi słowy, czas, który zostanie przekazany, zanim przeglądarka będzie mogła sprawdzić nową wersję elementu zawartości z punktu obecności.

Włączenie tej funkcji spowoduje wygenerowanie `Cache-Control: max-age` i utworzenie `Expires` nagłówków punktów obecności oraz wysłanie ich do klienta http. Domyślnie te nagłówki zostaną zastąpione tymi nagłówkami utworzonymi przez serwer pochodzenia. Niemniej jednak w celu zmiany tego zachowania można użyć funkcji obróbki nagłówka i buforu kontroli pamięci podręcznej.

Informacje o kluczu:

- Ta akcja nie ma wpływu na walidacje pamięci podręcznej serwera z punktem obecności. Te typy ponownej walidacji są określane przez `Cache-Control` nagłówki i `Expires` odebrane z serwera pochodzenia i można je dostosować przy użyciu domyślnej wartości maksymalnego wieku i wymuszone wewnętrzne funkcje maksymalnego wieku.
- Skonfiguruj tę funkcję, określając wartość całkowitą i wybierając odpowiednią jednostkę czasu (na przykład sekundy, minuty, godziny itp.).
- Ustawienie tej funkcji na wartość ujemną powoduje, że punkty obecności wysyłają `Cache-Control: no-cache` i `Expires` czas, który jest ustawiany w przeszłości, z każdą odpowiedzią na przeglądarkę. Mimo że klient HTTP nie będzie buforujący odpowiedzi, to ustawienie nie będzie miało wpływu na możliwość buforowania odpowiedzi z serwera pochodzenia.
- Ustawienie jednostki czasu na wartość "off" spowoduje wyłączenie tej funkcji. `Cache-Control`Nagłówki i w `Expires` pamięci podręcznej z odpowiedzią serwera pochodzenia będą przekazywane do przeglądarki.

**Zachowanie domyślne:** Logowanie

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Wykonaj przekierowania

**Cel:** Określa, czy żądania mogą być przekierowywane do nazwy hosta zdefiniowanej w nagłówku lokalizacji zwróconej przez serwer pochodzenia klienta.

Informacje o kluczu:

- Żądania mogą być przekierowywane tylko do rekordów CNAME, które odpowiadają tej samej platformie.

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania mogą być przekierowywane.
Disabled (Wyłączony)|Żądania nie zostaną przekierowane.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Wymuszaj wewnętrzny maksymalny wiek

**Cel:** Określa interwał maksymalnego okresu ważności pamięci podręcznej serwera POP do pochodzenia. Innymi słowy, ilość czasu, która będzie przebiegać przed punktem obecności, może sprawdzić, czy buforowany element zawartości jest zgodny z zasobem przechowywanym na serwerze źródłowym.

Informacje o kluczu:

- Ta funkcja spowoduje przesłonięcie interwału maksymalnego wieku zdefiniowanego w `Cache-Control` `Expires` nagłówkach lub wygenerowanych z serwera pochodzenia.
- Ta funkcja nie ma wpływu na walidacje pamięci podręcznej przeglądarki. Te typy ponownej walidacji są określane przez `Cache-Control` lub w `Expires` nagłówkach wysłanych do przeglądarki.
- Ta funkcja nie ma zauważalnego wpływu na odpowiedź dostarczoną przez punkt obecności do osoby żądającej. Może jednak mieć wpływ na ilość danych o ponownej walidacji wysyłanych z punktów obecności do serwera pochodzenia.
- Skonfiguruj tę funkcję przez:
    - Wybieranie kodu stanu, dla którego zostanie zastosowany wewnętrzny maksymalny wiek.
    - Określanie wartości całkowitej i Wybieranie odpowiedniej jednostki czasu (na przykład sekund, minut, godzin itd.). Ta wartość definiuje maksymalny interwał żądania.

- Ustawienie jednostki czasu na wartość "off" powoduje wyłączenie tej funkcji. Wewnętrzny interwał maksymalnego wieku nie zostanie przypisany do żądanych zasobów. Jeśli oryginalny nagłówek nie zawiera instrukcji buforowania, element zawartości zostanie zbuforowany zgodnie z aktywnym ustawieniem w domyślnej wewnętrznej funkcji max-age.

**Zachowanie domyślne:** Logowanie

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:
- Numer AS
- Adres IP klienta
- Parametr cookie
- Wyrażenie regularne parametru cookie
- Kraj
- Urządzenie
- Krawędź CNAME
- Odwołuje się do domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania URL
- Wyrażenie regularne kwerendy adresu URL
- Symbol wieloznaczny zapytania URL
- Parametr zapytania URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>Obsługa H. 264 (pobieranie progresywne HTTP)

**Cel:** Określa typy formatów plików H. 264, które mogą być używane do przesyłania strumieniowego zawartości.

Informacje o kluczu:

- Zdefiniuj zestaw rozdzielany spacjami dozwolonych rozszerzeń nazw plików H. 264 w opcji rozszerzenia pliku. Opcja rozszerzenia pliku zastąpi zachowanie domyślne. Obsługa plików MP4 i F4V przez uwzględnienie tych rozszerzeń w przypadku ustawienia tej opcji.
- Uwzględnij okres, po którym określisz każde rozszerzenie nazwy pliku (na przykład _. mp4_, _. F4V_).

**Zachowanie domyślne:** Pobieranie progresywne HTTP obsługuje domyślnie multimedia MP4 i F4V.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Uznają żądanie braku pamięci podręcznej

**Cel:** Określa, czy żądania braku pamięci podręcznej klienta HTTP będą przekazywane do serwera pochodzenia.

Żądanie bez pamięci podręcznej występuje, gdy klient HTTP wysyła do `Cache-Control: no-cache` żądania HTTP i/lub `Pragma: no-cache` nagłówek.

Wartość|Wynik
--|--
Enabled (Włączony)|Zezwala na przekazywanie żądań braku pamięci podręcznej klienta HTTP do serwera pochodzenia, a serwer pochodzenia zwróci nagłówki odpowiedzi i treść z powrotem do klienta HTTP.
Disabled (Wyłączony)|Przywraca zachowanie domyślne. Domyślnym zachowaniem jest uniemożliwienie przekazywania żądań braku pamięci podręcznej do serwera pochodzenia.

W przypadku całego ruchu produkcyjnego zdecydowanie zaleca się pozostawienie tej funkcji w domyślnym stanie Disabled. W przeciwnym razie serwery pierwotne nie będą chronione przed użytkownikami końcowymi, którzy mogą przypadkowo wyzwolić wiele żądań braku pamięci podręcznej podczas odświeżania stron sieci Web lub z wielu popularnych odtwarzaczy multimedialnych, które są kodowane w celu wysyłania nagłówka bez pamięci podręcznej z każdym żądaniem wideo. Jednak ta funkcja może być przydatna w przypadku niektórych nieprodukcyjnych katalogów przemieszczania lub testowania, aby umożliwić ściąganie nowej zawartości na żądanie z serwera pochodzenia.

Stan pamięci podręcznej, który jest raportowany dla żądania, które można przesłać dalej do serwera źródłowego z powodu tej funkcji `TCP_Client_Refresh_Miss` . Raport Stany pamięci podręcznej, który jest dostępny w podstawowym module raportowania, zawiera informacje statystyczne według stanu pamięci podręcznej. Ten raport umożliwia śledzenie liczby i procent żądań, które są przekazywane do serwera źródłowego z powodu tej funkcji.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignoruj Źródło bez pamięci podręcznej

**Cel:** Określa, czy Usługa CDN zignoruje następujące dyrektywy, które są obsługiwane przez serwer pochodzenia:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informacje o kluczu:

- Skonfiguruj tę funkcję, definiując rozdzielaną spacjami listę kodów stanu, dla których powyższe dyrektywy zostaną zignorowane.
- Zestaw prawidłowych kodów stanu dla tej funkcji to: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414 i 415.
- Wyłącz tę funkcję, ustawiając ją na wartość pustą.

**Zachowanie domyślne:** Zachowaniem domyślnym jest przestrzeganie powyższych dyrektyw.

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:
- Numer AS
- Adres IP klienta
- Parametr cookie
- Wyrażenie regularne parametru cookie
- Kraj
- Urządzenie
- Krawędź CNAME
- Odwołuje się do domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania URL
- Wyrażenie regularne kwerendy adresu URL
- Symbol wieloznaczny zapytania URL
- Parametr zapytania URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignoruj zakresy Unsatisfiable

**Cel:** Określa odpowiedź, która zostanie zwrócona do klientów, gdy żądanie 416 generuje kod stanu niewłaściwego.

Domyślnie ten kod stanu jest zwracany, gdy określone żądanie zakresu bajtów nie może zostać spełnione przez punkt obecności i nie określono pola nagłówka żądania if-Range.

Wartość|Wynik
-|-
Enabled (Włączony)|Uniemożliwia punktom obecności reagowanie na nieprawidłowe żądanie zakresu bajtów o 416 żądany zakres nie niewłaściwego kodu stanu. Zamiast tego serwery będą dostarczać żądany zasób i zwracają 200 OK do klienta.
Disabled (Wyłączony)|Przywraca zachowanie domyślne. Domyślnym zachowaniem 416 jest przestrzeganie kodu stanu niewłaściwego żądanego zakresu.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Wewnętrzna maksymalna — nieodświeżona

**Cel:** Określa, jak długo przeszło czas normalnego czasu wygaśnięcia w pamięci podręcznej, gdy punkt obecności nie może ponownie sprawdzić poprawności pamięci podręcznej z serwerem pochodzenia.

Zwykle po upływie limitu czasu maksymalnego poziomu zasobów punkt POP wyśle żądanie ponownego sprawdzania poprawności do serwera pochodzenia. Serwer pierwotny odpowie z niezmodyfikowanym 304, aby udostępnić punkt obecności jako nową dzierżawę w pamięci podręcznej lub w przeciwnym razie z 200 OK, aby udostępnić punkt obecności w zaktualizowanej wersji pamięci podręcznej.

Jeśli punkt obecności nie może nawiązać połączenia z serwerem pochodzenia przy próbie takiego ponownego sprawdzania poprawności, ta wewnętrzna maksymalna — nieodświeżona funkcja kontroluje, czy i na jak długo punkt obecności może nadal obsłużyć teraz nieodświeżony element zawartości.

Należy pamiętać, że ten przedział czasu zaczyna się, gdy wygasa maksymalny wiek zasobu, a nie w przypadku niepowodzenia ponownej walidacji. W związku z tym maksymalny okres, w którym zasób może być obsługiwany bez pomyślnego ponownego sprawdzania poprawności, to ilość czasu określona przez kombinację max-age Plus Max-stary. Na przykład, jeśli zasób został zbuforowany o godzinie 9:00 i maksymalnie 30 minut, a maksymalna liczba-nieodświeżona wynosi 15 minut, to nieudana próba ponownej walidacji w 9:44 spowoduje, że użytkownik końcowy otrzyma nieodświeżony buforowany element zawartości, podczas gdy próba nieudanej weryfikacji w 9:46 spowoduje, że użytkownik końcowy otrzyma limit czasu bramy 504.

Każda wartość skonfigurowana dla tej funkcji jest zastępowana przez `Cache-Control: must-revalidate` lub `Cache-Control: proxy-revalidate` nagłówki odebrane z serwera pochodzenia. Jeśli jeden z tych nagłówków zostanie odebrany z serwera pochodzenia, gdy zasób jest początkowo w pamięci podręcznej, wówczas punkt obecności nie będzie w starym pamięci podręcznej. W takim przypadku, jeśli punkt obecności nie może ponownie sprawdzić poprawności przy pochodzeniu po wygaśnięciu interwału maksymalnego poziomu ważności zasobu, POP zwraca błąd limitu czasu bramy 504.

Informacje o kluczu:

- Skonfiguruj tę funkcję przez:
    - Wybieranie kodu stanu, dla którego zostanie zastosowane Max-stary.
    - Określenie wartości całkowitej, a następnie wybranie odpowiedniej jednostki czasu (na przykład sekund, minut, godzin itd.). Ta wartość definiuje wewnętrzne maksymalne-nieodświeżone, które zostaną zastosowane.

- Ustawienie jednostki czasu na wartość "off" spowoduje wyłączenie tej funkcji. Buforowany element zawartości nie będzie obsługiwany dłużej niż jego normalny czas wygaśnięcia.

**Zachowanie domyślne:** Dwie minuty

#### <a name="compatibility"></a>Zgodność

Ze względu na sposób, w jaki są śledzone ustawienia pamięci podręcznej, ta funkcja nie może być skojarzona z następującymi warunkami dopasowania:
- Numer AS
- Adres IP klienta
- Parametr cookie
- Wyrażenie regularne parametru cookie
- Kraj
- Urządzenie
- Krawędź CNAME
- Odwołuje się do domeny
- Literał nagłówka żądania
- Wyrażenie regularne nagłówka żądania
- Symbol wieloznaczny nagłówka żądania
- Request, metoda
- Schemat żądania
- Literał zapytania URL
- Wyrażenie regularne kwerendy adresu URL
- Symbol wieloznaczny zapytania URL
- Parametr zapytania URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Ciąg zapytania dziennika

**Cel:** Określa, czy ciąg zapytania będzie przechowywany wraz z adresem URL w dziennikach dostępu.

Wartość|Wynik
-|-
Enabled (Włączony)|Umożliwia przechowywanie ciągów zapytań podczas rejestrowania adresów URL w dzienniku dostępu. Jeśli adres URL nie zawiera ciągu zapytania, ta opcja nie będzie miała żadnego efektu.
Disabled (Wyłączony)|Przywraca zachowanie domyślne. Domyślnym zachowaniem jest ignorowanie ciągów zapytań podczas rejestrowania adresów URL w dzienniku dostępu.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maksymalna liczba żądań Keep-Alive

**Cel:** Określa maksymalną liczbę żądań dla połączenia Keep-Alive przed zamknięciem.

Ustawienie maksymalnej liczby żądań na niską wartość jest niezalecane i może skutkować obniżeniem wydajności.

Informacje o kluczu:

- Określ tę wartość jako liczbę całkowitą.
- W określonej wartości nie należy umieszczać przecinków ani kropek.

**Wartość domyślna:** 10 000 żądań

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Modyfikowanie nagłówka żądania klienta

**Cel:** Każde żądanie zawiera zestaw nagłówków żądań, które opisują go. Ta funkcja może:

- Dołącz lub Zastąp wartość przypisaną do nagłówka żądania. Jeśli określony nagłówek żądania nie istnieje, ta funkcja doda go do żądania.
- Usuń nagłówek żądania z żądania.

Żądania przekazywane do serwera pochodzenia będą odzwierciedlać zmiany wprowadzone przez tę funkcję.

Jedną z następujących akcji można wykonać w nagłówku żądania:

Opcja|Opis|Przykład
-|-|-
Append|Określona wartość zostanie dodana na końcu istniejącej wartości nagłówka żądania.|**Wartość nagłówka żądania (klient):**<br/>Sekwencj<br/>**Wartość nagłówka żądania (aparat reguł):**<br/>Wartość2 <br/>**Nowa wartość nagłówka żądania:** <br/>Value1Value2
Zastąp|Wartość nagłówka żądania zostanie ustawiona na określoną wartość.|**Wartość nagłówka żądania (klient):**<br/>Sekwencj<br/>**Wartość nagłówka żądania (aparat reguł):**<br/>Wartość2<br/>**Nowa wartość nagłówka żądania:**<br/> Wartość2 <br/>
Usuń|Usuwa określony nagłówek żądania.|**Wartość nagłówka żądania (klient):**<br/>Sekwencj<br/>**Modyfikuj konfigurację nagłówka żądania klienta:**<br/>Usuń nagłówek żądania.<br/>**Wynika**<br/>Określony nagłówek żądania nie zostanie przekazany do serwera pochodzenia.

Informacje o kluczu:

- Upewnij się, że wartość określona w opcji Nazwa jest dokładnym dopasowaniem dla żądanego nagłówka żądania.
- Nie jest uwzględniana wielkość liter na potrzeby identyfikacji nagłówka. Na przykład, można użyć dowolnej z następujących odmian `Cache-Control` nazwy nagłówka do identyfikacji:
    - Cache-Control
    - CACHE-CONTROL
    - cachE-Control
- Podczas określania nazwy nagłówka należy używać tylko znaków alfanumerycznych, łączników lub podkreśleń.
- Usunięcie nagłówka uniemożliwi przekazanie go do serwera pochodzenia przez punkty obecności.
- Następujące nagłówki są zarezerwowane i nie mogą być modyfikowane przez tę funkcję:
    - przesłać
    - host
    - Korzystając
    - warning
    - x-Forwarded-For
    - Wszystkie nazwy nagłówków rozpoczynające się od "x-EC" są zarezerwowane.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Modyfikowanie nagłówka odpowiedzi klienta

Każda odpowiedź zawiera zestaw nagłówków odpowiedzi opisujących go. Ta funkcja może:

- Dołącz lub Zastąp wartość przypisaną do nagłówka odpowiedzi. Jeśli określony nagłówek odpowiedzi nie istnieje, ta funkcja doda go do odpowiedzi.
- Usuń nagłówek odpowiedzi z odpowiedzi.

Domyślnie wartości nagłówka odpowiedzi są definiowane przez serwer źródłowy i punkty obecności.

Jedną z następujących akcji można wykonać w nagłówku odpowiedzi:

Opcja|Opis|Przykład
-|-|-
Append|Określona wartość zostanie dodana na końcu istniejącej wartości nagłówka odpowiedzi.|**Wartość nagłówka odpowiedzi (klient):**<br />Sekwencj<br/>**Wartość nagłówka odpowiedzi (aparat reguł):**<br/>Wartość2<br/>**Nowa wartość nagłówka odpowiedzi:**<br/>Value1Value2
Zastąp|Wartość nagłówka odpowiedzi zostanie ustawiona na określoną wartość.|**Wartość nagłówka odpowiedzi (klient):**<br/>Sekwencj<br/>**Wartość nagłówka odpowiedzi (aparat reguł):**<br/>Wartość2 <br/>**Nowa wartość nagłówka odpowiedzi:**<br/>Wartość2 <br/>
Usuń|Usuwa określony nagłówek odpowiedzi.|**Wartość nagłówka odpowiedzi (klient):**<br/>Sekwencj<br/>**Modyfikuj konfigurację nagłówka odpowiedzi klienta:**<br/>Usuń nagłówek odpowiedzi z pytania.<br/>**Wynika**<br/>Określony nagłówek odpowiedzi nie zostanie przekazany do osoby żądającej.

Informacje o kluczu:

- Upewnij się, że wartość określona w opcji Nazwa jest dokładnym dopasowaniem dla żądanego nagłówka odpowiedzi.
- Nie jest uwzględniana wielkość liter na potrzeby identyfikacji nagłówka. Na przykład, można użyć dowolnej z następujących odmian `Cache-Control` nazwy nagłówka do identyfikacji:
    - Cache-Control
    - CACHE-CONTROL
    - cachE-Control
- Usunięcie nagłówka uniemożliwia przekazanie go do osoby żądającej.
- Następujące nagłówki są zarezerwowane i nie mogą być modyfikowane przez tę funkcję:
    - Akceptuj — kodowanie
    - wiek
    - połączenie
    - Kodowanie zawartości
    - Długość zawartości
    - zakres zawartości
    - data
    - serwer
    - końcowy
    - Transfer-Encoding
    - uaktualnienie
    - zmienia
    - Korzystając
    - warning
    - Wszystkie nazwy nagłówków rozpoczynające się od "x-EC" są zarezerwowane.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Częściowe udostępnianie pamięci podręcznej

**Cel:** Określa, czy żądanie może generować częściowo buforowaną zawartość.

Tej częściowej pamięci podręcznej można następnie użyć do spełnienia nowych żądań dotyczących tej zawartości, dopóki żądana zawartość nie będzie w pełni buforowana.

Wartość|Wynik
-|-
Enabled (Włączony)|Żądania mogą generować częściowo buforowaną zawartość.
Disabled (Wyłączony)|Żądania mogą generować tylko w pełni buforowaną wersję wymaganej zawartości.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Wstępne Weryfikowanie zawartości w pamięci podręcznej

**Cel:** Określa, czy zawartość pamięci podręcznej będzie kwalifikować się do wczesnego sprawdzania poprawności przed upływem czasu wygaśnięcia.

Zdefiniuj ilość czasu przed wygaśnięciem wartości czasu wygaśnięcia żądanej zawartości, która będzie kwalifikować się do wczesnego ponownej walidacji.

Informacje o kluczu:

- Wybranie opcji "off" oznacza, że jednostka czasu wymaga ponownej walidacji po wygaśnięciu czasu wygaśnięcia zawartości w pamięci podręcznej. Nie należy określać czasu i jest on ignorowany.

**Zachowanie domyślne:** Logowanie. Ponowne sprawdzanie poprawności może odbywać się tylko po wygaśnięciu czasu wygaśnięcia zawartości w pamięci podręcznej.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Specjalne nagłówki serwera proxy

**Cel:** Definiuje zestaw Verizon dla [konkretnych nagłówków żądań HTTP](cdn-verizon-http-headers.md) , które będą przekazywane z punktu POP do serwera źródłowego.

Informacje o kluczu:

- Każdy nagłówek żądania specyficzny dla usługi CDN zdefiniowany w tej funkcji jest przekazywany do serwera pochodzenia. Wykluczone nagłówki nie są przekazywane dalej.
- Aby zapobiec przekazywaniu nagłówka żądania specyficznego dla sieci CDN, usuń go z listy rozdzielanej spacjami w polu listy nagłówków.

Następujące nagłówki HTTP znajdują się na liście domyślnej:
- Korzystając
- X-Forwarded-For
- X-Forwarded-proto
- X-Host
- X-Midgress
- Lista X-Gateway
- X-we-Name
- Host

**Zachowanie domyślne:** Wszystkie nagłówki żądań specyficzne dla usługi CDN zostaną przekazane do serwera pochodzenia.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Odświeżanie plików pamięci podręcznej zero bajtów

**Cel:** Określa, w jaki sposób żądanie klienta HTTP dla zasobu pamięci podręcznej 0-bajtowej jest obsługiwane przez punkty obecności.

Prawidłowe wartości:

Wartość|Wynik
--|--
Enabled (Włączony)|Powoduje, że punkt obecności ponownie pobierze zasób z serwera pochodzenia.
Disabled (Wyłączony)|Przywraca zachowanie domyślne. Domyślnym zachowaniem jest zapełnienie prawidłowych zasobów pamięci podręcznej na żądanie.

Ta funkcja nie jest wymagana do poprawnego buforowania i dostarczania zawartości, ale może być przydatna jako obejście problemu. Na przykład dynamiczne generatory zawartości na serwerach pochodzenia mogą przypadkowo spowodować, że odpowiedzi są przesyłane do punktów obecności. Te typy odpowiedzi są zwykle zapisywane w pamięci podręcznej przez punkty obecności. Jeśli wiesz, że odpowiedź 0-bajtowa nigdy nie jest prawidłową odpowiedzią na tę zawartość, ta funkcja może uniemożliwić klientom obsługę tych typów zasobów.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Ustawianie kodów stanu pamięci podręcznej

**Cel:** Definiuje zestaw kodów stanu, które mogą prowadzić do zawartości w pamięci podręcznej.

Domyślnie buforowanie jest włączone tylko dla odpowiedzi 200 OK.

Zdefiniuj zestaw rozdzielany spacjami dla żądanych kodów stanu.

Informacje o kluczu:

- Włącz funkcję Ignoruj Źródło bez pamięci podręcznej. Jeśli ta funkcja nie jest włączona, odpowiedzi w trybie innym niż 200 nie mogą być buforowane.
- Zestaw prawidłowych kodów stanu dla tej funkcji to: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415 i 416.
- Ta funkcja nie może być używana do wyłączania buforowania dla odpowiedzi generujących kod stanu 200 OK.

**Zachowanie domyślne:** Buforowanie jest włączone tylko w przypadku odpowiedzi generujących kod stanu 200 OK.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Ustawianie niestandardowego nagłówka adresu IP klienta

**Cel:** Dodaje niestandardowy nagłówek, który identyfikuje klienta wysyłającego żądanie przez adres IP do żądania.

Opcja Nazwa nagłówka definiuje nazwę niestandardowego nagłówka żądania, w którym przechowywany jest adres IP klienta.

Ta funkcja umożliwia serwerowi pochodzenia klienta Znajdowanie adresów IP klientów za pomocą niestandardowego nagłówka żądania. Jeśli żądanie jest obsługiwane z pamięci podręcznej, serwer pochodzenia nie zostanie poinformowany o adresie IP klienta. W związku z tym zaleca się używanie tej funkcji z zasobami, które nie są buforowane.

Upewnij się, że określona nazwa nagłówka nie jest zgodna z żadną z następujących nazw:

- Nazwy standardowego nagłówka żądania. Listę standardowych nazw nagłówków można znaleźć w [dokumencie RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Zastrzeżone nazwy nagłówków:
    - przesłane dalej — dla
    - host
    - zmienia
    - Korzystając
    - warning
    - x-Forwarded-For
    - Wszystkie nazwy nagłówków rozpoczynające się od "x-EC" są zarezerwowane.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Nieodświeżone dostarczanie zawartości w przypadku błędu

**Cel:** Określa, czy wygasła buforowana zawartość zostanie dostarczona, gdy wystąpi błąd podczas ponownej walidacji pamięci podręcznej lub gdy pobiera żądaną zawartość z serwera pochodzenia klienta.

Wartość|Wynik
-|-
Enabled (Włączony)|Nieodświeżona zawartość jest obsługiwana dla żądającego, gdy wystąpi błąd podczas nawiązywania połączenia z serwerem pochodzenia.
Disabled (Wyłączony)|Błąd serwera pochodzenia jest przekazywany do osoby żądającej.

**Zachowanie domyślne:** Wyłączony

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Nieodświeżone podczas weryfikacji

**Cel:** Zwiększa wydajność dzięki umożliwieniu obecności nieodświeżonej zawartości do osoby żądającej podczas ponownej walidacji.

Informacje o kluczu:

- Zachowanie tej funkcji różni się w zależności od wybranej jednostki czasu.
    - **Jednostka czasu:** Określ czas i wybierz jednostkę czasu (na przykład sekundy, minuty, godziny itd.), aby umożliwić nieodświeżone dostarczanie zawartości. Ten typ instalacji pozwala usłudze CDN zwiększyć długość czasu, przez który może dostarczyć zawartość przed wymaganiem weryfikacji zgodnie z następującą formułą: **czas wygaśnięcia TTL**podczas ponownej  +  **weryfikacji czasu**
    - **Wyłączone:** Wybierz opcję "off", aby wymagać ponownej walidacji, zanim będzie można obsłużyć żądanie dotyczące nieodświeżonej zawartości.
        - Nie określaj długości czasu, ponieważ nie ma zastosowania i zostanie zignorowana.

**Zachowanie domyślne:** Logowanie. Aby można było obsłużyć żądaną zawartość, należy wykonać ponowną walidację.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Uwierzytelnianie tokenu

**Cel:** Określa, czy uwierzytelnianie oparte na tokenach zostanie zastosowane do żądania.

Jeśli jest włączone uwierzytelnianie oparte na tokenach, zostaną uznane tylko żądania zapewniające zaszyfrowaną token i zgodność z wymaganiami określonymi przez ten token.

Klucz szyfrowania używany do szyfrowania i odszyfrowywania wartości tokenów jest określany przez klucz podstawowy i opcje klucza kopii zapasowej na stronie uwierzytelnianie tokenu. Należy pamiętać, że klucze szyfrowania są specyficzne dla platformy.

**Zachowanie domyślne:** Wyłączony.

Ta funkcja ma pierwszeństwo przed większością funkcji, z wyjątkiem funkcji ponownego zapisywania adresów URL.

Wartość | Wynik
------|---------
Enabled (Włączony) | Chroni żądaną zawartość przy użyciu uwierzytelniania opartego na tokenach. Zostaną uznane tylko żądania od klientów, którzy dostarczają prawidłowy token i spełniają jego wymagania. Transakcje FTP są wykluczone z uwierzytelniania opartego na tokenach.
Disabled (Wyłączony)| Przywraca zachowanie domyślne. Domyślne zachowanie polega na umożliwieniu konfiguracji uwierzytelniania opartego na tokenach w celu ustalenia, czy żądanie zostanie zabezpieczone.

#### <a name="compatibility"></a>Zgodność

Nie używaj uwierzytelniania tokenu z warunkiem zawsze pasującym.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Kod odmowy uwierzytelniania tokenu

**Cel:** Określa typ odpowiedzi, która zostanie zwrócona użytkownikowi, gdy żądanie zostanie odrzucone z powodu uwierzytelniania opartego na tokenie.

Dostępne kody odpowiedzi są wymienione w poniższej tabeli.

Kod odpowiedzi|Nazwa odpowiedzi|Opis
-------------|-------------|--------
301|Przeniesiono trwale|Ten kod stanu przekierowuje nieautoryzowanych użytkowników do adresu URL określonego w nagłówku lokalizacji.
302|Znaleziono|Ten kod stanu przekierowuje nieautoryzowanych użytkowników do adresu URL określonego w nagłówku lokalizacji. Ten kod stanu jest standardową metodą wykonywania przekierowania.
307|Tymczasowe przekierowanie|Ten kod stanu przekierowuje nieautoryzowanych użytkowników do adresu URL określonego w nagłówku lokalizacji.
401|Brak autoryzacji|Połączenie tego kodu stanu z nagłówkiem odpowiedzi WWW-Authentication umożliwia wyświetlenie monitu o uwierzytelnienie użytkownika.
403|Forbidden|Ten komunikat jest komunikatem o stanie niedostępnym w standardzie 403, który zostanie wyświetlony przez nieautoryzowanego użytkownika podczas próby uzyskania dostępu do chronionej zawartości.
404|Nie znaleziono pliku|Ten kod stanu wskazuje, że klient HTTP mógł komunikować się z serwerem, ale nie znaleziono wymaganej zawartości.

#### <a name="compatibility"></a>Zgodność

Nie należy używać kodu odmowy uwierzytelniania tokenu z warunkiem zawsze pasującym. Zamiast tego należy użyć sekcji **Niestandardowa obsługa odmowy** na stronie **uwierzytelnianie tokenu** w portalu **zarządzania** . Aby uzyskać więcej informacji, zobacz [Zabezpieczanie zasobów Azure CDN przy użyciu uwierzytelniania tokenu](cdn-token-auth.md).

#### <a name="url-redirection"></a>Przekierowywanie adresów URL

Ta funkcja obsługuje przekierowywanie adresów URL do adresu URL zdefiniowanego przez użytkownika, gdy jest on skonfigurowany do zwracania kodu stanu 3xx. Ten adres URL zdefiniowany przez użytkownika można określić, wykonując następujące czynności:

1. Wybierz kod odpowiedzi 3xx dla funkcji kodu odmowy uwierzytelniania tokenu.
2. Wybierz pozycję "lokalizacja" z opcjonalnej nazwy nagłówka opcji.
3. Ustaw opcjonalną opcję wartości nagłówka na żądany adres URL.

Jeśli adres URL nie jest zdefiniowany dla kodu stanu 3xx, na stronie standardowej odpowiedzi dla kodu stanu 3xx zostanie zwrócony użytkownik.

Przekierowywanie adresów URL dotyczy tylko kodów odpowiedzi 3xx.

Opcjonalna opcja wartości nagłówka obsługuje znaki alfanumeryczne, znaki cudzysłowu i spacje.

#### <a name="authentication"></a>Authentication

Ta funkcja obsługuje funkcję dołączania nagłówka WWW-Authenticate podczas odpowiadania na nieautoryzowane żądanie zawartości chronionej przez uwierzytelnianie oparte na tokenach. Jeśli nagłówek WWW-Authenticate został ustawiony na wartość "podstawowa" w konfiguracji, wówczas nieautoryzowany użytkownik zostanie poproszony o podanie poświadczeń konta.

Powyższą konfigurację można osiągnąć, wykonując następujące czynności:

1. Wybierz "401" jako kod odpowiedzi dla funkcji kodu odmowy uwierzytelniania tokenu.
2. Wybierz opcję "WWW-Authenticate" z opcjonalnej nazwy nagłówka.
3. Ustaw opcjonalną opcję wartości nagłówka na "podstawowa".

Nagłówek WWW-Authenticate dotyczy tylko kodów odpowiedzi 401.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Adres URL ignorowania tokenu uwierzytelniania

**Cel:** Określa, czy porównania adresów URL wykonywane przez uwierzytelnianie oparte na tokenach są rozróżniane wielkości liter.

Parametry, których dotyczy ta funkcja, to:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Prawidłowe wartości:

Wartość|Wynik
---|----
Enabled (Włączony)|Powoduje ignorowanie wielkości liter podczas porównywania adresów URL dla parametrów uwierzytelniania opartych na tokenach.
Disabled (Wyłączony)|Przywraca zachowanie domyślne. Domyślnym zachowaniem porównania adresów URL w przypadku uwierzytelniania tokenu jest uwzględnianie wielkości liter.

**Zachowanie domyślne:** Wyłączony.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Parametr uwierzytelniania tokenu

**Cel:** Określa, czy należy zmienić nazwę parametru ciągu zapytania uwierzytelniania opartego na tokenie.

Informacje o kluczu:

- Opcja wartość definiuje nazwę parametru ciągu zapytania, za pomocą którego można określić token.
- Nie można ustawić opcji Value na "ec_token".
- Upewnij się, że nazwa zdefiniowana w opcji value zawiera tylko prawidłowe znaki adresu URL.

Wartość|Wynik
----|----
Enabled (Włączony)|Opcja wartość definiuje nazwę parametru ciągu zapytania, za pomocą którego mają być zdefiniowane tokeny.
Disabled (Wyłączony)|Token może być określony jako niezdefiniowany parametr ciągu zapytania w adresie URL żądania.

**Zachowanie domyślne:** Wyłączony. Token może być określony jako niezdefiniowany parametr ciągu zapytania w adresie URL żądania.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>Przekierowanie adresu URL

**Cel:** Przekierowuje żądania za pośrednictwem nagłówka lokalizacji.

Konfiguracja tej funkcji wymaga ustawienia następujących opcji:

Opcja|Opis
-|-
Kod|Wybierz kod odpowiedzi, który zostanie zwrócony do zleceniodawcy.
Wzorzec & źródła| Te ustawienia definiują wzorzec identyfikatora URI żądania, który identyfikuje typ żądań, które mogą zostać przekierowane. Przekierowane zostaną tylko żądania, których adres URL spełnia następujące kryteria: <br/> <br/> **Źródło (lub punkt dostępu do zawartości):** Wybierz ścieżkę względną identyfikującą serwer pochodzenia. Ta ścieżka jest sekcją _/xxxx/_ i nazwą punktu końcowego. <br/><br/> **Źródło (wzorzec):** Wzorzec, który identyfikuje żądania według ścieżki względnej, musi być zdefiniowany. Ten wzorzec wyrażenia regularnego musi definiować ścieżkę rozpoczynającą się bezpośrednio po wcześniej wybranym punkcie dostępu do zawartości (Zobacz powyżej). <br/> -Upewnij się, że kryteria identyfikatora URI żądania (czyli wzorca & źródłowego) nie powodują konfliktu ze wszystkimi warunkami dopasowania zdefiniowanymi dla tej funkcji. <br/> -Określ wzorzec; w przypadku używania wartości pustej jako wzorca wszystkie ciągi są dopasowywane.
Element docelowy| Zdefiniuj adres URL, do którego zostaną przekierowane powyższe żądania. <br/><br/> Dynamicznie Skonstruuj ten adres URL przy użyciu: <br/> -Wzorzec wyrażenia regularnego <br/>- [Zmienne HTTP](cdn-http-variables.md) <br/><br/> Zastąp wartości przechwycone we wzorcu źródłowym do wzorca docelowego za pomocą $_n_ , gdzie _n_ identyfikuje wartość w kolejności, w jakiej została przechwycona. Na przykład $1 reprezentuje pierwszą wartość przechwyconą we wzorcu źródłowym, natomiast $2 reprezentuje drugą wartość. <br/>

Zdecydowanie zaleca się używanie bezwzględnego adresu URL. Użycie względnego adresu URL może przekierować adresy URL usługi CDN do nieprawidłowej ścieżki.

**Przykładowy scenariusz**

W tym przykładzie pokazano, jak przekierować adres URL granicy CNAME, który jest rozpoznawany jako podstawowy adres URL sieci CDN: http: \/ /Marketing.azureedge.NET/Brochures

Żądania kwalifikujące zostaną przekierowane do tego adresu URL CNAME podstawowej krawędzi: http: \/ /CDN.mydomain.com/resources

Ten adres URL można uzyskać, korzystając z następującej konfiguracji: ![ Przekierowywanie adresu URL](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Kluczowe punkty:**

- Funkcja przekierowanie adresu URL definiuje adresy URL żądań, które zostaną przekierowane. W związku z tym dodatkowe warunki dopasowania nie są wymagane. Chociaż warunek dopasowania został zdefiniowany jako "always", tylko żądania wskazujące folder "broszury" w pochodzeniu odbiorcy "Marketing" zostaną przekierowane.
- Wszystkie zgodne żądania zostaną przekierowane do adresu URL granicy CNAME zdefiniowanej w opcji miejsce docelowe.
    - Przykładowy #1 scenariusza:
        - Przykładowe żądanie (adres URL usługi CDN): http: \/ /Marketing.azureedge.NET/Brochures/widgets.PDF
        - Adres URL żądania (po przekierowaniu): http: \/ /CDN.mydomain.com/resources/widgets.PDF  
    - Przykładowy #2 scenariusza:
        - Przykładowe żądanie (adres URL granicy CNAME): http: \/ /Marketing.mydomain.com/Brochures/widgets.PDF
        - Adres URL żądania (po przekierowaniu): \/ Przykładowy scenariusz http:/CDN.mydomain.com/resources/widgets.PDF
    - Przykładowy #3 scenariusza:
        - Przykładowe żądanie (adres URL granicy CNAME): http: \/ /Brochures.mydomain.com/campaignA/Final/productC.ppt
        - Adres URL żądania (po przekierowaniu): http: \/ /CDN.mydomain.com/resources/campaignA/Final/productC.ppt 
- W opcji docelowej jest używany schemat żądania (% {Schema}), dzięki czemu schemat żądania pozostaje niezmieniony po przekierowaniu.
- Segmenty adresu URL przechwycone z żądania są dołączane do nowego adresu URL za pośrednictwem "$1".

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>Ponowne zapisywanie adresów URL

**Cel:** Ponownie zapisuje adres URL żądania.

Informacje o kluczu:

- Konfiguracja tej funkcji wymaga ustawienia następujących opcji:

Opcja|Opis
-|-
 Wzorzec & źródła | Te ustawienia definiują wzorzec identyfikatora URI żądania, który identyfikuje typ żądań, które mogą zostać ponownie napisane. Tylko żądania, których adres URL spełnia oba poniższe kryteria, zostaną napisaną: <br/><br/>  - **Źródło (lub punkt dostępu do zawartości):** Wybierz ścieżkę względną identyfikującą serwer pochodzenia. Ta ścieżka jest sekcją _/xxxx/_ i nazwą punktu końcowego. <br/><br/> - **Źródło (wzorzec):** Wzorzec, który identyfikuje żądania według ścieżki względnej, musi być zdefiniowany. Ten wzorzec wyrażenia regularnego musi definiować ścieżkę rozpoczynającą się bezpośrednio po wcześniej wybranym punkcie dostępu do zawartości (Zobacz powyżej). <br/> Sprawdź, czy kryteria identyfikatora URI żądania (czyli wzorca & źródłowego) nie powodują konfliktu ze wszystkimi warunkami dopasowania zdefiniowanymi dla tej funkcji. Określ wzorzec; w przypadku używania wartości pustej jako wzorca wszystkie ciągi są dopasowywane.
 Element docelowy  |Zdefiniuj względny adres URL, na który będą zapisywane powyższe żądania: <br/>    1. Wybieranie punktu dostępu do zawartości, który identyfikuje serwer pochodzenia. <br/>    2. Definiowanie ścieżki względnej przy użyciu: <br/>        -Wzorzec wyrażenia regularnego <br/>        - [Zmienne HTTP](cdn-http-variables.md) <br/> <br/> Zastąp wartości przechwycone we wzorcu źródłowym do wzorca docelowego za pomocą $_n_ , gdzie _n_ identyfikuje wartość w kolejności, w jakiej została przechwycona. Na przykład $1 reprezentuje pierwszą wartość przechwyconą we wzorcu źródłowym, natomiast $2 reprezentuje drugą wartość.

 Ta funkcja umożliwia punktom obecności ponowne zapisywanie adresu URL bez wykonywania tradycyjnych przekierowań. Oznacza to, że obiekt żądający otrzymuje ten sam kod odpowiedzi, co w przypadku, gdy zażądano zarejestrowanego adresu URL.

**Przykładowy scenariusz 1**

W tym przykładzie pokazano, jak przekierować adres URL granicy CNAME, który jest rozpoznawany jako podstawowy adres URL sieci CDN: http: \/ /Marketing.azureedge.NET/Brochures/

Żądania kwalifikujące zostaną przekierowane do tego adresu URL CNAME podstawowej krawędzi: http: \/ /MyOrigin.azureedge.NET/Resources/

Ten adres URL można uzyskać, korzystając z następującej konfiguracji: ![ Przekierowywanie adresu URL](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Przykładowy scenariusz 2**

W tym przykładzie pokazano, jak przekierować adres URL granicy CNAME z wielkich do małych liter przy użyciu wyrażeń regularnych.

Ten adres URL można uzyskać, korzystając z następującej konfiguracji: ![ Przekierowywanie adresu URL](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Kluczowe punkty:**

- Funkcja ponownego zapisywania adresu URL definiuje adresy URL żądań, które zostaną ponownie zapisane. W związku z tym dodatkowe warunki dopasowania nie są wymagane. Chociaż warunek dopasowania został zdefiniowany jako "always", tylko żądania wskazujące folder "broszury" w pochodzeniu klienta "Marketing" zostaną ponownie napisaną.

- Segmenty adresu URL przechwycone z żądania są dołączane do nowego adresu URL za pośrednictwem "$1".

#### <a name="compatibility"></a>Zgodność

Ta funkcja zawiera kryteria dopasowywania, które muszą zostać spełnione, zanim będzie można je zastosować do żądania. Aby zapobiec ustawianiu kryteriów dopasowywania konfliktów, ta funkcja jest niezgodna z następującymi warunkami dopasowania:

- Numer AS
- Źródło CDN
- Adres IP klienta
- Pochodzenie klienta
- Schemat żądania
- Katalog ścieżki URL
- Rozszerzenie ścieżki URL
- Nazwa pliku ścieżki URL
- Literał ścieżki adresu URL
- Wyrażenie regularne ścieżki adresu URL
- Symbol wieloznaczny ścieżki adresu URL
- Literał zapytania URL
- Parametr zapytania URL
- Wyrażenie regularne kwerendy adresu URL
- Symbol wieloznaczny zapytania URL

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Zmienna użytkownika

**Cel:** Tylko do użytku wewnętrznego.

[Powrót do początku](#azure-cdn-from-verizon-premium-rules-engine-features)
## <a name="next-steps"></a>Następne kroki

- [Dokumentacja aparatu reguł](cdn-verizon-premium-rules-engine-reference.md)
- [Wyrażenia warunkowe aparatu reguł](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Warunki dopasowań aparatu reguł](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Zastępowanie zachowania HTTP przy użyciu aparatu reguł](cdn-verizon-premium-rules-engine.md)
- [Przegląd Azure CDN](cdn-overview.md)
