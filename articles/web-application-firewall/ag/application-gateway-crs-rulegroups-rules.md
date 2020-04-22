---
title: Grupy reguł i reguły KSR
titleSuffix: Azure Web Application Firewall
description: Ta strona zawiera informacje o grupach reguł i regułach crs zapory aplikacji sieci web.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 59409c428aba938c49fc37647db82f30d783a629
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730572"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Grupy i reguły zasad zapory zapory aplikacji sieci Web

Zapora aplikacji sieci Web bramy aplikacji (WAF) chroni aplikacje internetowe przed typowymi lukami w zabezpieczeniach i exploitami. Odbywa się to za pomocą reguł, które są zdefiniowane na podstawie podstawowych zestawów reguł OWASP 3.1, 3.0 lub 2.2.9. Reguły te można wyłączyć na zasadzie reguły po regułie. Ten artykuł zawiera bieżące reguły i zestawy reguł.

## <a name="core-rule-sets"></a>Podstawowe zestawy reguł

Brama aplikacji WAF jest wstępnie skonfigurowana z programem CRS 3.0 domyślnie. Ale zamiast tego możesz użyć CRS 3.1 lub CRS 2.2.9. CRS 3.1 oferuje nowe zestawy reguł broniących przed infekcjami Java, początkowy zestaw kontroli przekazywania plików, stałe fałszywe alarmy i wiele innych. CRS 3.0 oferuje zmniejszenie liczby fałszywych alarmów w porównaniu z CRS 2.2.9. Można również [dostosować reguły do własnych potrzeb](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![Zarządza regułami](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF chroni przed następującymi lukami w zabezpieczeniach sieci Web:

- Ataki iniekcji SQL
- Ataki skryptów między witrynami
- Inne typowe ataki, takie jak iniekcja poleceń, przemycanie żądań HTTP, podział odpowiedzi HTTP i zdalne dołączanie plików
- Naruszenia protokołu HTTP
- Anomalie protokołu HTTP, takie jak brak agenta użytkownika hosta i nagłówki akceptowania
- Boty, przeszukiwarki i skanery
- Typowe błędne konfiguracje aplikacji (na przykład Apache i IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3.1

CRS 3.1 zawiera 13 grup reguł, jak pokazano w poniższej tabeli. Każda grupa zawiera wiele reguł, które można wyłączyć.

|Grupa reguł|Opis|
|---|---|
|**[Ogólne](#general-31)**|Grupa ogólna|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Metody blokowania (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Ochrona przed skanerami portów i środowiska|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Ochrona przed problemami z protokołem i kodowaniem|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Ochrona przed wtryskiem nagłówka, przemytem żądań i podziałem odpowiedzi|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Ochrona przed atakami na pliki i ścieżki|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Ochrona przed atakami zdalnego dołączania plików (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Ochrona ponownie ataków zdalnego wykonywania kodu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Ochrona przed atakami wtrysku PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Ochrona przed atakami skryptów między witrynami|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Ochrona przed atakami wtrysku SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Ochrona przed atakami utrwalania sesji|
|**[ŻĄDANIE-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-31)**|Ochrona przed atakami JAVA|

### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 zawiera 12 grup reguł, jak pokazano w poniższej tabeli. Każda grupa zawiera wiele reguł, które można wyłączyć.

|Grupa reguł|Opis|
|---|---|
|**[Ogólne](#general-30)**|Grupa ogólna|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Metody blokowania (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Ochrona przed skanerami portów i środowiska|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Ochrona przed problemami z protokołem i kodowaniem|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Ochrona przed wtryskiem nagłówka, przemytem żądań i podziałem odpowiedzi|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Ochrona przed atakami na pliki i ścieżki|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Ochrona przed atakami zdalnego dołączania plików (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Ochrona ponownie ataków zdalnego wykonywania kodu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Ochrona przed atakami wtrysku PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Ochrona przed atakami skryptów między witrynami|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Ochrona przed atakami wtrysku SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Ochrona przed atakami utrwalania sesji|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 zawiera 10 grup reguł, jak pokazano w poniższej tabeli. Każda grupa zawiera wiele reguł, które można wyłączyć.

|Grupa reguł|Opis|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Ochrona przed naruszeniami protokołu (takich jak nieprawidłowe znaki lub GET z treścią żądania)|
|**[crs_21_protocol_anomalies](#crs21)**|Ochrona przed nieprawidłowymi informacjami nagłówka|
|**[crs_23_request_limits](#crs23)**|Ochrona przed argumentami lub plikami przekraczającymi ograniczenia|
|**[crs_30_http_policy](#crs30)**|Ochrona przed metodami z ograniczeniami, nagłówkami i typami plików|
|**[crs_35_bad_robots](#crs35)**|Ochrona przed robotami i skanerami internetowymi|
|**[crs_40_generic_attacks](#crs40)**|Ochrona przed ogólnymi atakami (takimi jak utrwalanie sesji, zdalne dołączanie plików i wstrzyknięcie PHP)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Ochrona przed atakami wtrysku SQL|
|**[crs_41_xss_attacks](#crs41xss)**|Ochrona przed atakami skryptów między witrynami|
|**[crs_42_tight_security](#crs42)**|Ochrona przed atakami przechodzącymi przez ścieżkę|
|**[crs_45_trojans](#crs45)**|Ochrona przed trojanami backdoor|

Następujące grupy reguł i reguły są dostępne podczas korzystania z Zapory aplikacji sieci Web w bramie aplikacji.

# <a name="owasp-31"></a>[OWASP 3.1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a>Zestawy reguł

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Ogólne</p>

|Ruleid|Opis|
|---|---|
|200004|Możliwe wieloczęściowe niedopasowane granice.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|Ruleid|Opis|
|---|---|
|911100|Metoda nie jest dozwolona przez zasady|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|Ruleid|Opis|
|---|---|
|913100|Znaleziono agenta użytkownika skojarzonego ze skanerem zabezpieczeń|
|913101|Znaleziono agenta użytkownika skojarzonego ze skryptami/ogólnym klientem HTTP|
|913102|Znaleziono agenta użytkownika skojarzonego z robotem internetowym/botem|
|913110|Znaleziono nagłówek żądania skojarzony ze skanerem zabezpieczeń|
|913120|Znaleziono nazwę pliku/argument żądania skojarzony ze skanerem zabezpieczeń|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|Ruleid|Opis|
|---|---|
|920100|Nieprawidłowy wiersz żądania HTTP|
|920120|Próba obejścia wieloczęściowego/formularza danych|
|920121|Próba obejścia wieloczęściowego/formularza danych|
|920130|Nie można przeanalizować treści żądania.|
|920140|Wieloczęściowa treść żądania nie powiodła się, aby nie można było zawładnić rygorystyczną weryfikacją|
|920160|Nagłówek HTTP o długości zawartości nie jest numeryczny.|
|920170|GET lub HEAD Request z treścią treści.|
|920171|GET lub HEAD Wniosek z transferem kodowania.|
|920180|Post żądanie brak nagłówka długość zawartości.|
|920190|Zakres = nieprawidłowa wartość ostatniego bajtu.|
|920200|Zakres = zbyt wiele pól (6 lub więcej)|
|920201|Zakres = Zbyt wiele pól dla żądania pdf (35 lub więcej)|
|920202|Zakres = Zbyt wiele pól dla żądania pdf (6 lub więcej)|
|920210|Znaleziono wiele/powodujących konflikt danych nagłówka połączenia.|
|920220|Próba ataku na nadużycie kodowania adresów URL|
|920230|Wykryto wiele kodowania adresów URL|
|920240|Próba ataku na nadużycie kodowania adresów URL|
|920250|Próba ataku na nadużycie kodowania UTF8|
|920260|Próba ataku nadużycia Unicode Full/Half Width|
|920270|Nieprawidłowy znak w żądaniu (znak zerowy)|
|920271|Nieprawidłowy znak w żądaniu (znaki nie nadajalne)|
|920272|Nieprawidłowy znak na żądanie (poza znakami do wydrukowania poniżej ascii 127)|
|920273|Nieprawidłowy znak na żądanie (poza bardzo ścisłym zestawem)|
|920274|Nieprawidłowy znak w nagłówkach żądań (poza bardzo ścisłym zestawem)|
|920280|Żądanie braku nagłówka hosta|
|920290|Pusty nagłówek hosta|
|920300|Żądanie braku nagłówka akceptowania|
|920310|Żądanie ma pusty nagłówek akceptowania|
|920311|Żądanie ma pusty nagłówek akceptowania|
|920320|Brak nagłówka agenta użytkownika|
|920330|Nagłówek pustego agenta użytkownika|
|920340|Żądanie zawierające zawartość, ale brak nagłówka typu zawartości|
|920341|Żądanie zawierające zawartość wymaga nagłówka typu zawartości|
|920350|Nagłówek hosta jest numerycznym adresem IP|
|920360|Zbyt długa nazwa argumentu|
|920370|Zbyt długa wartość argumentu|
|920380|Zbyt wiele argumentów w żądaniu|
|920390|Przekroczono całkowity rozmiar argumentów|
|920400|Zbyt duży rozmiar przesłany pliku|
|920410|Całkowity rozmiar przesłanych plików jest zbyt duży|
|920420|Typ zawartości żądania nie jest dozwolony przez zasady|
|920430|Wersja protokołu HTTP nie jest dozwolona przez zasady|
|920440|Rozszerzenie pliku URL jest ograniczone przez zasady|
|920450|Nagłówek HTTP jest ograniczony przez zasady (%@{MATCHED_VAR})|
|920460|Nieprawidłowe znaki ucieczki|
|920470|Nagłówek typu zawartości niezgodnej z prawem|
|920480|Ogranicz parametr zestawu znaków w nagłówku typu zawartości|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|Ruleid|Opis|
|---|---|
|921110|Http Wniosek przemytu ataku|
|921120|Atak dzielenia odpowiedzi HTTP|
|921130|Atak dzielenia odpowiedzi HTTP|
|921140|Atak iniekcji nagłówka HTTP za pośrednictwem nagłówków|
|921150|Atak iniekcji nagłówka HTTP przez ładunek (wykryto CR/LF)|
|921151|Atak iniekcji nagłówka HTTP przez ładunek (wykryto CR/LF)|
|921160|Atak iniekcji nagłówka HTTP przez ładunek (CR/LF i wykryto nazwę nagłówka)|
|921170|Zanieczyszczenie parametrami HTTP|
|921180|Zanieczyszczenie parametrów HTTP (%{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|Ruleid|Opis|
|---|---|
|930100|Atak przechodzenia przez ścieżkę (/.. /)|
|930110|Atak przechodzenia przez ścieżkę (/.. /)|
|930120|Próba dostępu do pliku systemu operacyjnego|
|930130|Próba dostępu do pliku z ograniczeniami|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|Ruleid|Opis|
|---|---|
|931100|Możliwy atak zdalnego dołączania plików (RFI) = parametr ADRESU URL przy użyciu adresu IP|
|931110|Możliwy atak zdalnego dołączania plików (RFI) = nazwa parametru wrażliwego na RFI używana w/URL Ładunku|
|931120|Możliwy atak zdalnego dołączania plików (RFI) = adres URL Payload Używany w/Trailing Question Mark Character (?)|
|931130|Możliwy atak zdalnego dołączania plików (RFI) = odwołanie/łącze poza domeną|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|Ruleid|Opis|
|---|---|
|932100|Zdalne wykonywanie polecenia: Iniekcja polecenia Unix|
|932105|Zdalne wykonywanie polecenia: Iniekcja polecenia Unix|
|932106|Zdalne wykonywanie polecenia: Iniekcja polecenia Unix|
|932110|Zdalne wykonywanie polecenia: wtrysk polecenia systemu Windows|
|932115|Zdalne wykonywanie polecenia: wtrysk polecenia systemu Windows|
|932120|Zdalne wykonywanie polecenia = Znaleziono polecenie programu Windows PowerShell|
|932130|Zdalne wykonanie polecenia = Znaleziono wyrażenie powłoki Unix|
|932140|Zdalne wykonanie polecenia = Znaleziono polecenie WINDOWS FOR/IF|
|932150|Zdalne wykonywanie polecenia: bezpośrednie wykonywanie polecenia Unix|
|932160|Zdalne wykonanie polecenia = Znaleziono kod powłoki Unix|
|932170|Zdalne wykonanie polecenia = Shellshock (CVE-2014-6271)|
|932171|Zdalne wykonanie polecenia = Shellshock (CVE-2014-6271)|
|932180|Próba przesłania pliku z ograniczeniami|
|932190|Zdalne wykonywanie polecenia: próba obejścia symboli wieloznacznych|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|Ruleid|Opis|
|---|---|
|933100|Php Injection Attack = Znaleziono znacznik otwierający/zamykający|
|933110|Php Injection Attack = Znaleziono upload pliku skryptu PHP|
|933111|Php Injection Attack: Znaleziono upload pliku skryptu PHP|
|933120|Php Injection Attack = Znaleziono dyrektywę konfiguracją|
|933130|Php Injection Attack = Znaleziono zmienne|
|933131|Php Injection Attack: Znaleziono zmienne|
|933140|Php Injection Attack: Znaleziono strumień we/wy|
|933150|Php Injection Attack = Znaleziono nazwę funkcji PHP wysokiego ryzyka|
|933151|Php Injection Attack: Znaleziono nazwę funkcji PHP średniego ryzyka|
|933160|Php Injection Attack = Znaleziono wywołanie funkcji PHP wysokiego ryzyka|
|933161|Php Injection Attack: Znaleziono wywołanie funkcji PHP o niskiej wartości|
|933170|Php Injection Attack: Seryjny wtrysk obiektu|
|933180|Atak wtrysku PHP = Znaleziono wywołanie funkcji zmiennej|
|933190|Php Injection Attack: Znaleziono tag zamykający PHP|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|Ruleid|Opis|
|---|---|
|941100|Atak XSS wykryty przez libinjection|
|941101|Atak XSS wykryty przez libinjection|
|941110|Filtr XSS — kategoria 1 = Wektor znacznika skryptu|
|941130|Filtr XSS - Kategoria 3 = Wektor atrybutu|
|941140|Filtr XSS - Kategoria 4 = Javascript URI Vector|
|941150|Filtr XSS — kategoria 5 = niedozwolone atrybuty HTML|
|941160|NoScript XSS InjectionChecker: Wtrysk HTML|
|941170|NoScript XSS InjectionChecker: Iniekcja atrybutu|
|941180|Słowa kluczowe czarnej listy walidatora węzłów|
|941190|XSS przy użyciu arkuszy stylów|
|941200|XSS przy użyciu ramek VML|
|941210|XSS przy użyciu zaciemnionego javascriptu|
|941220|XSS przy użyciu zaciemnionego skryptu VB|
|941230|XSS za pomocą tagu "osadzaj"|
|941240|XSS przy użyciu atrybutu "import" lub "implementacja"|
|941250|Filtry IE XSS - Wykryto atak|
|941260|XSS za pomocą tagu meta|
|941270|XSS za pomocą 'link' href|
|941280|XSS przy użyciu tagu "base"|
|941290|XSS przy użyciu tagu 'apletu'|
|941300|XSS przy użyciu znacznika "obiekt"|
|941310|US-ASCII nieprawidłowo sformułowany kodowanie XSS filter - wykryto atak.|
|941320|Wykryto możliwy atak XSS - Program obsługi tagów HTML|
|941330|Filtry IE XSS - Wykryto atak.|
|941340|Filtry IE XSS - Wykryto atak.|
|941350|Kodowanie UTF-7 IE XSS - wykryto atak.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|Ruleid|Opis|
|---|---|
|942100|Atak iniekcji SQL wykryty przez libinjection|
|942110|Atak iniekcji SQL: Wykryto typowe testy iniekcji|
|942120|Atak iniekcji SQL: Wykryto operatora SQL|
|942130|Atak iniekcji SQL: Wykryto tautologię SQL.|
|942140|Atak iniekcji SQL = wykryto typowe nazwy baz danych|
|942150|Atak iniekcji SQL|
|942160|Wykrywa ślepe testy sqli za pomocą sleep() lub benchmark().|
|942170|Wykrywa próby wtrysku i wtrysku za pomocą testów porównawczych SQL, w tym zapytania warunkowe|
|942180|Wykrywa podstawowe próby pomijania uwierzytelniania SQL 1/3|
|942190|Wykrywa próby wykonania kodu MSSQL i zbierania informacji|
|942200|Wykrywa mysql komentarz-/space-zaciemnione zastrzyki i zakończenie backtick|
|942210|Wykrywa próby wtrysku SQL przykuty 1/2|
|942220|Szukam ataków przepełnienia liczby całkowitej, są one pobierane z skipfish, z wyjątkiem 3.0.00738585072|
|942230|Wykrywa warunkowe próby iniekcji SQL|
|942240|Wykrywa przełącznik zestawu znaków MySQL i próby MSSQL DoS|
|942250|Wykrywa match against, merge i execute immediate injections|
|942251|Wykrywa zastrzyki HAVING|
|942260|Wykrywa podstawowe próby pomijania uwierzytelniania SQL 2/3|
|942270|Szukam podstawowego wstrzyknięcia sql. Wspólny ciąg ataku dla wyroczni mysql i innych|
|942280|Wykrywa postgres pg_sleep iniekcji, czeka na ataki opóźnienia i próby zamknięcia bazy danych|
|942290|Wyszukuje podstawowe próby iniekcji SQL MongoDB|
|942300|Wykrywa komentarze, warunki i zastrzyki mysql|
|942310|Wykrywa próby wtrysku SQL 2/2|
|942320|Wykrywa mysql i postgresql składowane procedury / funkcji zastrzyki|
|942330|Wykrywa klasyczne sondy wtryskowe SQL 1/2|
|942340|Wykrywa podstawowe próby pomijania uwierzytelniania SQL 3/3|
|942350|Wykrywa wtrysk UDF MySQL i inne próby manipulacji danymi/strukturą|
|942360|Wykrywa połączone podstawowe iniekcje SQL i próby SQLLFI|
|942361|Wykrywa podstawowy iniekcję SQL na podstawie zmiany lub związku słów kluczowych|
|942370|Wykrywa klasyczne sondy wtryskowe SQL 2/2|
|942380|Atak iniekcji SQL|
|942390|Atak iniekcji SQL|
|942400|Atak iniekcji SQL|
|942410|Atak iniekcji SQL|
|942420|Ograniczone wykrywanie anomalii znaków SQL (pliki cookie): liczba znaków specjalnych przekroczonych (8)|
|942421|Ograniczone wykrywanie anomalii znaków SQL (pliki cookie): liczba znaków specjalnych przekroczonych (3)|
|942430|Ograniczone wykrywanie anomalii znaków SQL (args): liczba znaków specjalnych przekroczonych (12)|
|942431|Ograniczone wykrywanie anomalii znaków SQL (args): liczba znaków specjalnych przekroczonych (6)|
|942432|Ograniczone wykrywanie anomalii znaków SQL (args): liczba znaków specjalnych przekroczonych (2)|
|942440|Wykryto sekwencję komentarzy SQL.|
|942450|Zidentyfikowane kodowanie hex SQL|
|942460|Alert wykrywania anomalii meta-znaków — powtarzające się znaki inne niż wyrazy|
|942470|Atak iniekcji SQL|
|942480|Atak iniekcji SQL|
|942490|Wykrywa klasyczne sondy wtryskowe SQL 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|Ruleid|Opis|
|---|---|
|943100|Możliwy atak fiksacji sesji = Ustawianie wartości plików cookie w html|
|943110|Możliwy atak ustalania sesji = Nazwa parametru SessionID z poleceniem poza domeną|
|943120|Możliwy atak ustalania sesji = Nazwa parametru SessionID bez odsyłacza|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">ŻĄDANIE-944-APPLICATION-ATTACK-SESSION-JAVA</p>

|Ruleid|Opis|
|---|---|
|944120|Możliwe wykonanie ładunku i zdalne wykonanie polecenia|
|944130|Podejrzane klasy Java|
|944200|Eksploatacja deserializacji Java Apache Commons|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a>Zestawy reguł

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Ogólne</p>

|Ruleid|Opis|
|---|---|
|200004|Możliwe wieloczęściowe niedopasowane granice.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|Ruleid|Opis|
|---|---|
|911100|Metoda nie jest dozwolona przez zasady|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|Ruleid|Opis|
|---|---|
|913100|Znaleziono agenta użytkownika skojarzonego ze skanerem zabezpieczeń|
|913110|Znaleziono nagłówek żądania skojarzony ze skanerem zabezpieczeń|
|913120|Znaleziono nazwę pliku/argument żądania skojarzony ze skanerem zabezpieczeń|
|913101|Znaleziono agenta użytkownika skojarzonego ze skryptami/ogólnym klientem HTTP|
|913102|Znaleziono agenta użytkownika skojarzonego z robotem internetowym/botem|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|Ruleid|Opis|
|---|---|
|920100|Nieprawidłowy wiersz żądania HTTP|
|920130|Nie można przeanalizować treści żądania.|
|920140|Wieloczęściowa treść żądania nie powiodła się, aby nie można było zawładnić rygorystyczną weryfikacją|
|920160|Nagłówek HTTP o długości zawartości nie jest numeryczny.|
|920170|GET lub HEAD Request z treścią treści.|
|920180|Post żądanie brak nagłówka długość zawartości.|
|920190|Zakres = nieprawidłowa wartość ostatniego bajtu.|
|920210|Znaleziono wiele/powodujących konflikt danych nagłówka połączenia.|
|920220|Próba ataku na nadużycie kodowania adresów URL|
|920240|Próba ataku na nadużycie kodowania adresów URL|
|920250|Próba ataku na nadużycie kodowania UTF8|
|920260|Próba ataku nadużycia Unicode Full/Half Width|
|920270|Nieprawidłowy znak w żądaniu (znak zerowy)|
|920280|Żądanie braku nagłówka hosta|
|920290|Pusty nagłówek hosta|
|920310|Żądanie ma pusty nagłówek akceptowania|
|920311|Żądanie ma pusty nagłówek akceptowania|
|920330|Nagłówek pustego agenta użytkownika|
|920340|Żądanie zawierające zawartość, ale brak nagłówka typu zawartości|
|920350|Nagłówek hosta jest numerycznym adresem IP|
|920380|Zbyt wiele argumentów w żądaniu|
|920360|Zbyt długa nazwa argumentu|
|920370|Zbyt długa wartość argumentu|
|920390|Przekroczono całkowity rozmiar argumentów|
|920400|Zbyt duży rozmiar przesłany pliku|
|920410|Całkowity rozmiar przesłanych plików jest zbyt duży|
|920420|Typ zawartości żądania nie jest dozwolony przez zasady|
|920430|Wersja protokołu HTTP nie jest dozwolona przez zasady|
|920440|Rozszerzenie pliku URL jest ograniczone przez zasady|
|920450|Nagłówek HTTP jest ograniczony przez zasady (%@{MATCHED_VAR})|
|920200|Zakres = zbyt wiele pól (6 lub więcej)|
|920201|Zakres = Zbyt wiele pól dla żądania pdf (35 lub więcej)|
|920230|Wykryto wiele kodowania adresów URL|
|920300|Żądanie braku nagłówka akceptowania|
|920271|Nieprawidłowy znak w żądaniu (znaki nie nadajalne)|
|920320|Brak nagłówka agenta użytkownika|
|920272|Nieprawidłowy znak na żądanie (poza znakami do wydrukowania poniżej ascii 127)|
|920202|Zakres = Zbyt wiele pól dla żądania pdf (6 lub więcej)|
|920273|Nieprawidłowy znak na żądanie (poza bardzo ścisłym zestawem)|
|920274|Nieprawidłowy znak w nagłówkach żądań (poza bardzo ścisłym zestawem)|
|920460|Nieprawidłowe znaki ucieczki|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|Ruleid|Opis|
|---|---|
|921100|HTTP wniosek o atak przemytu.|
|921110|Http Wniosek przemytu ataku|
|921120|Atak dzielenia odpowiedzi HTTP|
|921130|Atak dzielenia odpowiedzi HTTP|
|921140|Atak iniekcji nagłówka HTTP za pośrednictwem nagłówków|
|921150|Atak iniekcji nagłówka HTTP przez ładunek (wykryto CR/LF)|
|921160|Atak iniekcji nagłówka HTTP przez ładunek (CR/LF i wykryto nazwę nagłówka)|
|921151|Atak iniekcji nagłówka HTTP przez ładunek (wykryto CR/LF)|
|921170|Zanieczyszczenie parametrami HTTP|
|921180|Zanieczyszczenie parametrów HTTP (%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|Ruleid|Opis|
|---|---|
|930100|Atak przechodzenia przez ścieżkę (/.. /)|
|930110|Atak przechodzenia przez ścieżkę (/.. /)|
|930120|Próba dostępu do pliku systemu operacyjnego|
|930130|Próba dostępu do pliku z ograniczeniami|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|Ruleid|Opis|
|---|---|
|931100|Możliwy atak zdalnego dołączania plików (RFI) = parametr ADRESU URL przy użyciu adresu IP|
|931110|Możliwy atak zdalnego dołączania plików (RFI) = nazwa parametru wrażliwego na RFI używana w/URL Ładunku|
|931120|Możliwy atak zdalnego dołączania plików (RFI) = adres URL Payload Używany w/Trailing Question Mark Character (?)|
|931130|Możliwy atak zdalnego dołączania plików (RFI) = odwołanie/łącze poza domeną|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|Ruleid|Opis|
|---|---|
|932120|Zdalne wykonywanie polecenia = Znaleziono polecenie programu Windows PowerShell|
|932130|Zdalne wykonanie polecenia = Znaleziono wyrażenie powłoki Unix|
|932140|Zdalne wykonanie polecenia = Znaleziono polecenie WINDOWS FOR/IF|
|932160|Zdalne wykonanie polecenia = Znaleziono kod powłoki Unix|
|932170|Zdalne wykonanie polecenia = Shellshock (CVE-2014-6271)|
|932171|Zdalne wykonanie polecenia = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|Ruleid|Opis|
|---|---|
|933100|Php Injection Attack = Znaleziono znacznik otwierający/zamykający|
|933110|Php Injection Attack = Znaleziono upload pliku skryptu PHP|
|933120|Php Injection Attack = Znaleziono dyrektywę konfiguracją|
|933130|Php Injection Attack = Znaleziono zmienne|
|933150|Php Injection Attack = Znaleziono nazwę funkcji PHP wysokiego ryzyka|
|933160|Php Injection Attack = Znaleziono wywołanie funkcji PHP wysokiego ryzyka|
|933180|Atak wtrysku PHP = Znaleziono wywołanie funkcji zmiennej|
|933151|Php Injection Attack = Znaleziono nazwę funkcji PHP średniego ryzyka|
|933131|Php Injection Attack = Znaleziono zmienne|
|933161|Php Injection Attack = Znaleziono wywołanie funkcji PHP o niskiej wartości|
|933111|Php Injection Attack = Znaleziono upload pliku skryptu PHP|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|Ruleid|Opis|
|---|---|
|941100|Atak XSS wykryty przez libinjection|
|941110|Filtr XSS — kategoria 1 = Wektor znacznika skryptu|
|941130|Filtr XSS - Kategoria 3 = Wektor atrybutu|
|941140|Filtr XSS - Kategoria 4 = Javascript URI Vector|
|941150|Filtr XSS — kategoria 5 = niedozwolone atrybuty HTML|
|941180|Słowa kluczowe czarnej listy walidatora węzłów|
|941190|XSS przy użyciu arkuszy stylów|
|941200|XSS przy użyciu ramek VML|
|941210|XSS przy użyciu zaciemnionego javascriptu|
|941220|XSS przy użyciu zaciemnionego skryptu VB|
|941230|XSS za pomocą tagu "osadzaj"|
|941240|XSS przy użyciu atrybutu "import" lub "implementacja"|
|941260|XSS za pomocą tagu meta|
|941270|XSS za pomocą 'link' href|
|941280|XSS przy użyciu tagu "base"|
|941290|XSS przy użyciu tagu 'apletu'|
|941300|XSS przy użyciu znacznika "obiekt"|
|941310|US-ASCII nieprawidłowo sformułowany kodowanie XSS filter - wykryto atak.|
|941330|Filtry IE XSS - Wykryto atak.|
|941340|Filtry IE XSS - Wykryto atak.|
|941350|Kodowanie UTF-7 IE XSS - wykryto atak.|
|941320|Wykryto możliwy atak XSS - Program obsługi tagów HTML|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|Ruleid|Opis|
|---|---|
|942100|Atak iniekcji SQL wykryty przez libinjection|
|942110|Atak iniekcji SQL: Wykryto typowe testy iniekcji|
|942130|Atak iniekcji SQL: Wykryto tautologię SQL.|
|942140|Atak iniekcji SQL = wykryto typowe nazwy baz danych|
|942160|Wykrywa ślepe testy sqli za pomocą sleep() lub benchmark().|
|942170|Wykrywa próby wtrysku i wtrysku za pomocą testów porównawczych SQL, w tym zapytania warunkowe|
|942190|Wykrywa próby wykonania kodu MSSQL i zbierania informacji|
|942200|Wykrywa mysql komentarz-/space-zaciemnione zastrzyki i zakończenie backtick|
|942230|Wykrywa warunkowe próby iniekcji SQL|
|942260|Wykrywa podstawowe próby pomijania uwierzytelniania SQL 2/3|
|942270|Szukam podstawowego wstrzyknięcia sql. Wspólny ciąg ataku dla mysql oracle i innych.|
|942290|Wyszukuje podstawowe próby iniekcji SQL MongoDB|
|942300|Wykrywa komentarze, warunki i zastrzyki mysql|
|942310|Wykrywa próby wtrysku SQL 2/2|
|942320|Wykrywa mysql i postgresql składowane procedury / funkcji zastrzyki|
|942330|Wykrywa klasyczne sondy wtryskowe SQL 1/2|
|942340|Wykrywa podstawowe próby pomijania uwierzytelniania SQL 3/3|
|942350|Wykrywa wtrysk UDF MySQL i inne próby manipulacji danymi/strukturą|
|942360|Wykrywa połączone podstawowe iniekcje SQL i próby SQLLFI|
|942370|Wykrywa klasyczne sondy wtryskowe SQL 2/2|
|942150|Atak iniekcji SQL|
|942410|Atak iniekcji SQL|
|942430|Ograniczone wykrywanie anomalii znaków SQL (args): liczba znaków specjalnych przekroczonych (12)|
|942440|Wykryto sekwencję komentarzy SQL.|
|942450|Zidentyfikowane kodowanie hex SQL|
|942251|Wykrywa zastrzyki HAVING|
|942460|Alert wykrywania anomalii meta-znaków — powtarzające się znaki inne niż wyrazy|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|Ruleid|Opis|
|---|---|
|943100|Możliwy atak fiksacji sesji = Ustawianie wartości plików cookie w html|
|943110|Możliwy atak ustalania sesji = Nazwa parametru SessionID z poleceniem poza domeną|
|943120|Możliwy atak ustalania sesji = Nazwa parametru SessionID bez odsyłacza|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a>Zestawy reguł

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a>crs_20_protocol_violations

|Ruleid|Opis|
|---|---|
|960911|Nieprawidłowy wiersz żądania HTTP|
|981227|Błąd apache = nieprawidłowy identyfikator URI w żądaniu.|
|960912|Nie można przeanalizować treści żądania.|
|960914|Wieloczęściowa treść żądania nie powiodła się, aby nie można było zawładnić rygorystyczną weryfikacją|
|960915|Wieloczęściowy analizator wykrył możliwą niedopasowane granice.|
|960016|Nagłówek HTTP o długości zawartości nie jest numeryczny.|
|960011|GET lub HEAD Request z treścią treści.|
|960012|Post żądanie brak nagłówka długość zawartości.|
|960902|Nieprawidłowe użycie kodowania tożsamości.|
|960022|Expect Header nie jest dozwolony dla protokołu HTTP 1.0.|
|960020|Pragma Header wymaga cache-control header dla żądań HTTP/1.1.|
|958291|Zakres = pole istnieje i zaczyna się od 0.|
|958230|Zakres = nieprawidłowa wartość ostatniego bajtu.|
|958295|Znaleziono wiele/powodujących konflikt danych nagłówka połączenia.|
|950107|Próba ataku na nadużycie kodowania adresów URL|
|950109|Wykryto wiele kodowania adresów URL|
|950108|Próba ataku na nadużycie kodowania adresów URL|
|950801|Próba ataku na nadużycie kodowania UTF8|
|950116|Próba ataku nadużycia Unicode Full/Half Width|
|960901|Nieprawidłowy znak w żądaniu|
|960018|Nieprawidłowy znak w żądaniu|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a>crs_21_protocol_anomalies

|Ruleid|Opis|
|---|---|
|960008|Żądanie braku nagłówka hosta|
|960007|Pusty nagłówek hosta|
|960015|Żądanie braku nagłówka akceptowania|
|960021|Żądanie ma pusty nagłówek akceptowania|
|960009|Żądanie braku nagłówka agenta użytkownika|
|960006|Nagłówek pustego agenta użytkownika|
|960904|Żądanie zawierające zawartość, ale brak nagłówka typu zawartości|
|960017|Nagłówek hosta jest numerycznym adresem IP|

### <a name="crs_23_request_limits"></a><a name="crs23"></a>crs_23_request_limits

|Ruleid|Opis|
|---|---|
|960209|Zbyt długa nazwa argumentu|
|960208|Zbyt długa wartość argumentu|
|960335|Zbyt wiele argumentów w żądaniu|
|960341|Przekroczono całkowity rozmiar argumentów|
|960342|Zbyt duży rozmiar przesłany pliku|
|960343|Całkowity rozmiar przesłanych plików jest zbyt duży|

### <a name="crs_30_http_policy"></a><a name="crs30"></a>crs_30_http_policy

|Ruleid|Opis|
|---|---|
|960032|Metoda nie jest dozwolona przez zasady|
|960010|Typ zawartości żądania nie jest dozwolony przez zasady|
|960034|Wersja protokołu HTTP nie jest dozwolona przez zasady|
|960035|Rozszerzenie pliku URL jest ograniczone przez zasady|
|960038|Nagłówek HTTP jest ograniczony przez zasady|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a>crs_35_bad_robots

|Ruleid|Opis|
|---|---|
|990002|Żądanie wskazuje skaner zabezpieczeń zeskanowany witrynę|
|990901|Żądanie wskazuje skaner zabezpieczeń zeskanowany witrynę|
|990902|Żądanie wskazuje skaner zabezpieczeń zeskanowany witrynę|
|990012|Nieuczciwy robot witryny sieci Web|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a>crs_40_generic_attacks

|Ruleid|Opis|
|---|---|
|960024|Alert wykrywania anomalii meta-znaków — powtarzające się znaki inne niż wyrazy|
|950008|Wtrysk nieudokumentowanych tagów ColdFusion|
|950010|Atak wtrysku LDAP|
|950011|Atak wtrysku SSI|
|950018|Wykryto uniwersalny adres URL PDF XSS.|
|950019|Atak iniekcji poczty e-mail|
|950012|HTTP wniosek o atak przemytu.|
|950910|Atak dzielenia odpowiedzi HTTP|
|950911|Atak dzielenia odpowiedzi HTTP|
|950117|Atak zdalnego dołączania plików|
|950118|Atak zdalnego dołączania plików|
|950119|Atak zdalnego dołączania plików|
|950120|Możliwy atak zdalnego dołączania plików (RFI) = odwołanie/łącze poza domeną|
|981133|Artykuł 981133|
|981134|Artykuł 981134|
|950009|Atak utrwalania sesji|
|950003|Utrwalenie sesji|
|950000|Utrwalenie sesji|
|950005|Próba zdalnego dostępu do plików|
|950002|Dostęp do polecenia systemu|
|950006|Wstrzyknięcie polecenia systemu|
|959151|Atak wtrysku PHP|
|958976|Atak wtrysku PHP|
|958977|Atak wtrysku PHP|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a>crs_41_sql_injection_attacks

|Ruleid|Opis|
|---|---|
|981231|Wykryto sekwencję komentarzy SQL.|
|981260|Zidentyfikowane kodowanie hex SQL|
|981320|Atak iniekcji SQL = wykryto typowe nazwy baz danych|
|981300|Artykuł 981300|
|981301|Artykuł 981301|
|981302|Artykuł 981302|
|981303|Artykuł 981303|
|981304|Artykuł 981304|
|981305|Artykuł 981305|
|981306|Artykuł 981306|
|981307|Artykuł 981307|
|981308|Artykuł 981308|
|981309|Artykuł 981309|
|981310|Artykuł 981310|
|981311|Artykuł 981311|
|981312|Artykuł 981312|
|981313|Artykuł 981313|
|981314|Artykuł 981314|
|981315|Artykuł 981315|
|981316|Artykuł 981316|
|981317|Alert wykrywania anomalii instrukcji SQL SELECT|
|950007|Ślepy atak wtrysku SQL|
|950001|Atak iniekcji SQL|
|950908|Atak iniekcji SQL.|
|959073|Atak iniekcji SQL|
|981272|Wykrywa ślepe testy sqli za pomocą sleep() lub benchmark().|
|981250|Wykrywa próby wtrysku i wtrysku za pomocą testów porównawczych SQL, w tym zapytania warunkowe|
|981241|Wykrywa warunkowe próby iniekcji SQL|
|981276|Szukam podstawowego wstrzyknięcia sql. Wspólny ciąg ataku dla mysql oracle i innych.|
|981270|Wyszukuje podstawowe próby iniekcji SQL MongoDB|
|981253|Wykrywa mysql i postgresql składowane procedury / funkcji zastrzyki|
|981251|Wykrywa wtrysk UDF MySQL i inne próby manipulacji danymi/strukturą|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a>crs_41_xss_attacks

|Ruleid|Opis|
|---|---|
|973336|Filtr XSS — kategoria 1 = Wektor znacznika skryptu|
|973338|Filtr XSS — kategoria 3 = wektor URI języka Javascript|
|981136|Artykuł 981136|
|981018|Artykuł 981018|
|958016|Atak skryptów krzyżowych (XSS)|
|958414|Atak skryptów krzyżowych (XSS)|
|958032|Atak skryptów krzyżowych (XSS)|
|958026|Atak skryptów krzyżowych (XSS)|
|958027|Atak skryptów krzyżowych (XSS)|
|958054|Atak skryptów krzyżowych (XSS)|
|958418|Atak skryptów krzyżowych (XSS)|
|958034|Atak skryptów krzyżowych (XSS)|
|958019|Atak skryptów krzyżowych (XSS)|
|958013|Atak skryptów krzyżowych (XSS)|
|958408|Atak skryptów krzyżowych (XSS)|
|958012|Atak skryptów krzyżowych (XSS)|
|958423|Atak skryptów krzyżowych (XSS)|
|958002|Atak skryptów krzyżowych (XSS)|
|958017|Atak skryptów krzyżowych (XSS)|
|958007|Atak skryptów krzyżowych (XSS)|
|958047|Atak skryptów krzyżowych (XSS)|
|958410|Atak skryptów krzyżowych (XSS)|
|958415|Atak skryptów krzyżowych (XSS)|
|958022|Atak skryptów krzyżowych (XSS)|
|958405|Atak skryptów krzyżowych (XSS)|
|958419|Atak skryptów krzyżowych (XSS)|
|958028|Atak skryptów krzyżowych (XSS)|
|958057|Atak skryptów krzyżowych (XSS)|
|958031|Atak skryptów krzyżowych (XSS)|
|958006|Atak skryptów krzyżowych (XSS)|
|958033|Atak skryptów krzyżowych (XSS)|
|958038|Atak skryptów krzyżowych (XSS)|
|958409|Atak skryptów krzyżowych (XSS)|
|958001|Atak skryptów krzyżowych (XSS)|
|958005|Atak skryptów krzyżowych (XSS)|
|958404|Atak skryptów krzyżowych (XSS)|
|958023|Atak skryptów krzyżowych (XSS)|
|958010|Atak skryptów krzyżowych (XSS)|
|958411|Atak skryptów krzyżowych (XSS)|
|958422|Atak skryptów krzyżowych (XSS)|
|958036|Atak skryptów krzyżowych (XSS)|
|958000|Atak skryptów krzyżowych (XSS)|
|958018|Atak skryptów krzyżowych (XSS)|
|958406|Atak skryptów krzyżowych (XSS)|
|958040|Atak skryptów krzyżowych (XSS)|
|958052|Atak skryptów krzyżowych (XSS)|
|958037|Atak skryptów krzyżowych (XSS)|
|958049|Atak skryptów krzyżowych (XSS)|
|958030|Atak skryptów krzyżowych (XSS)|
|958041|Atak skryptów krzyżowych (XSS)|
|958416|Atak skryptów krzyżowych (XSS)|
|958024|Atak skryptów krzyżowych (XSS)|
|958059|Atak skryptów krzyżowych (XSS)|
|958417|Atak skryptów krzyżowych (XSS)|
|958020|Atak skryptów krzyżowych (XSS)|
|958045|Atak skryptów krzyżowych (XSS)|
|958004|Atak skryptów krzyżowych (XSS)|
|958421|Atak skryptów krzyżowych (XSS)|
|958009|Atak skryptów krzyżowych (XSS)|
|958025|Atak skryptów krzyżowych (XSS)|
|958413|Atak skryptów krzyżowych (XSS)|
|958051|Atak skryptów krzyżowych (XSS)|
|958420|Atak skryptów krzyżowych (XSS)|
|958407|Atak skryptów krzyżowych (XSS)|
|958056|Atak skryptów krzyżowych (XSS)|
|958011|Atak skryptów krzyżowych (XSS)|
|958412|Atak skryptów krzyżowych (XSS)|
|958008|Atak skryptów krzyżowych (XSS)|
|958046|Atak skryptów krzyżowych (XSS)|
|958039|Atak skryptów krzyżowych (XSS)|
|958003|Atak skryptów krzyżowych (XSS)|
|973300|Wykryto możliwy atak XSS - Program obsługi tagów HTML|
|973301|Wykryto atak XSS|
|973302|Wykryto atak XSS|
|973303|Wykryto atak XSS|
|973304|Wykryto atak XSS|
|973305|Wykryto atak XSS|
|973306|Wykryto atak XSS|
|973307|Wykryto atak XSS|
|973308|Wykryto atak XSS|
|973309|Wykryto atak XSS|
|973311|Wykryto atak XSS|
|973313|Wykryto atak XSS|
|973314|Wykryto atak XSS|
|973331|Filtry IE XSS - Wykryto atak.|
|973315|Filtry IE XSS - Wykryto atak.|
|973330|Filtry IE XSS - Wykryto atak.|
|973327|Filtry IE XSS - Wykryto atak.|
|973326|Filtry IE XSS - Wykryto atak.|
|973346|Filtry IE XSS - Wykryto atak.|
|973345|Filtry IE XSS - Wykryto atak.|
|973324|Filtry IE XSS - Wykryto atak.|
|973323|Filtry IE XSS - Wykryto atak.|
|973348|Filtry IE XSS - Wykryto atak.|
|973321|Filtry IE XSS - Wykryto atak.|
|973320|Filtry IE XSS - Wykryto atak.|
|973318|Filtry IE XSS - Wykryto atak.|
|973317|Filtry IE XSS - Wykryto atak.|
|973329|Filtry IE XSS - Wykryto atak.|
|973328|Filtry IE XSS - Wykryto atak.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a>crs_42_tight_security

|Ruleid|Opis|
|---|---|
|950103|Atak na ścieżkę|

### <a name="crs_45_trojans"></a><a name="crs45"></a>crs_45_trojans

|Ruleid|Opis|
|---|---|
|950110|Dostęp do tylnych drzwi|
|950921|Dostęp do tylnych drzwi|
|950922|Dostęp do tylnych drzwi|

---

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie reguł zapory aplikacji sieci Web przy użyciu portalu Azure](application-gateway-customize-waf-rules-portal.md)
