---
title: Grupy reguł i reguły dla KSR
titleSuffix: Azure Web Application Firewall
description: Ta strona zawiera informacje o grupach i regułach reguł KSR aplikacji sieci Web.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e2c88091072921f1ca674868e401c34d354418de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746513"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Zasady i reguły reguły KSR zapory aplikacji sieci Web

Application Gateway Zapora aplikacji sieci Web (WAF) chroni aplikacje sieci Web przed typowymi lukami w zabezpieczeniach i lukami w zabezpieczeniach. Odbywa się to za pomocą reguł, które są zdefiniowane w oparciu o OWASP podstawowe zestawy reguł 3,1, 3,0 lub 2.2.9. Te reguły można wyłączyć na zasadzie reguły po regule. Ten artykuł zawiera bieżące reguły i oferowane zestawy reguł.

## <a name="core-rule-sets"></a>Podstawowe zestawy reguł

Program Application Gateway WAF domyślnie udostępnia wstępnie skonfigurowany z obsługą KSR 3,0. Ale zamiast tego możesz użyć opcji KSR 3,1 lub KSR 2.2.9. Program KSR 3,1 oferuje nowe zestawy reguł, które mają na celu obronę przed infekcjami w języku Java, początkowy zestaw kontroli przekazywania plików, stałe fałszywe pozytywne i inne. Program KSR 3,0 oferuje zredukowane fałszywe wartości dodatnie w porównaniu z 2.2.9em KSR. Możesz również [dostosować reguły do swoich potrzeb](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![Zarządza regułami](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF chroni przed następującymi lukami w zabezpieczeniach sieci Web:

- Ataki z iniekcją SQL
- Ataki skryptów między lokacjami
- Inne typowe ataki, takie jak iniekcja poleceń, przemytie żądań HTTP, podział odpowiedzi HTTP i zdalne Dołączanie plików
- Naruszenia protokołu HTTP
- Anomalie protokołu HTTP, takie jak brak agenta użytkownika hosta i akceptowanie nagłówków
- Boty, przeszukiwarki i skanery
- Typowe konfiguracje aplikacji (na przykład Apache i IIS)

### <a name="owasp-crs-31"></a>OWASP KSR 3,1

Program KSR 3,1 zawiera 13 grup reguł, jak pokazano w poniższej tabeli. Każda grupa zawiera wiele reguł, które można wyłączyć.

> [!NOTE]
> KSR 3,1 jest dostępny tylko w jednostce SKU WAF_v2.

|Grupa reguł|Opis|
|---|---|
|**[Ogólne](#general-31)**|Grupa ogólna|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Metody blokowania (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Ochrona przed skanerami portów i środowisk|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Ochrona przed problemami z protokołem i kodowaniem|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Ochrona przed iniekcją nagłówka, przemyciem żądań i podziałem odpowiedzi|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Ochrona przed atakami plików i ścieżek|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Ochrona przed atakami zdalnego dołączania plików (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Chroń ponownie zdalne ataki wykonywania kodu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Ochrona przed atakami polegającymi na iniekcji PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Ochrona przed atakami na skrypty między lokacjami|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Ochrona przed atakami polegającymi na iniekcji SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Ochrona przed atakami z wykorzystaniem utrwalania sesji|
|**[ŻĄDANIE-944 — APLIKACJA-ATAK — SESJA — JAVA](#crs944-31)**|Ochrona przed atakami JAVA|

### <a name="owasp-crs-30"></a>OWASP KSR 3,0

Program KSR 3,0 obejmuje 12 grup reguł, jak pokazano w poniższej tabeli. Każda grupa zawiera wiele reguł, które można wyłączyć.

|Grupa reguł|Opis|
|---|---|
|**[Ogólne](#general-30)**|Grupa ogólna|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Metody blokowania (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Ochrona przed skanerami portów i środowisk|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Ochrona przed problemami z protokołem i kodowaniem|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Ochrona przed iniekcją nagłówka, przemyciem żądań i podziałem odpowiedzi|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Ochrona przed atakami plików i ścieżek|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Ochrona przed atakami zdalnego dołączania plików (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Chroń ponownie zdalne ataki wykonywania kodu|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Ochrona przed atakami polegającymi na iniekcji PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Ochrona przed atakami na skrypty między lokacjami|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Ochrona przed atakami polegającymi na iniekcji SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Ochrona przed atakami z wykorzystaniem utrwalania sesji|

### <a name="owasp-crs-229"></a>OWASP KSR 2.2.9

2.2.9 KSR zawiera 10 grup reguł, jak pokazano w poniższej tabeli. Każda grupa zawiera wiele reguł, które można wyłączyć.

|Grupa reguł|Opis|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Ochrona przed naruszeniami protokołów (na przykład nieprawidłowymi znakami lub POBIERAniem treści żądania)|
|**[crs_21_protocol_anomalies](#crs21)**|Ochrona przed niepoprawnymi informacjami nagłówka|
|**[crs_23_request_limits](#crs23)**|Ochrona przed argumentami lub plikami, które przekraczają ograniczenia|
|**[crs_30_http_policy](#crs30)**|Ochrona przed ograniczonymi metodami, nagłówkami i typami plików|
|**[crs_35_bad_robots](#crs35)**|Ochrona przed przedziałami i skanerami sieci Web|
|**[crs_40_generic_attacks](#crs40)**|Ochrona przed atakami ogólnymi (takimi jak utrwalanie sesji, Dołączanie plików zdalnych i iniekcja kodu PHP)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Ochrona przed atakami polegającymi na iniekcji SQL|
|**[crs_41_xss_attacks](#crs41xss)**|Ochrona przed atakami na skrypty między lokacjami|
|**[crs_42_tight_security](#crs42)**|Ochrona przed atakami polegającymi na przechodzeniu między ścieżkami|
|**[crs_45_trojans](#crs45)**|Ochrona przed tylne wejście koniami trojańskimi|

Następujące grupy reguł i reguły są dostępne w przypadku używania zapory aplikacji sieci Web na Application Gateway.

# <a name="owasp-31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a> Zestawy reguł

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Ogólne</p>

|RuleId|Opis|
|---|---|
|200004|Możliwa wieloczęściowa niezgodna granica.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Opis|
|---|---|
|911100|Metoda nie jest dozwolona przez zasady|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Opis|
|---|---|
|913100|Znaleziono User-Agent skojarzone ze skanerem zabezpieczeń|
|913101|Znaleziono User-Agent skojarzone z skryptem/ogólnym klientem HTTP|
|913102|Znaleziono User-Agent skojarzone z przeszukiwarką sieci Web/bot|
|913110|Znaleziono nagłówek żądania skojarzony ze skanerem zabezpieczeń|
|913120|Znaleziono nazwę pliku żądania/argument skojarzony z skanerem zabezpieczeń|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Opis|
|---|---|
|920100|Nieprawidłowy wiersz żądania HTTP|
|920120|Próba wykonania wieloczęściowego/formularza — obejście danych|
|920121|Próba wykonania wieloczęściowego/formularza — obejście danych|
|920130|Nie można przeanalizować treści żądania.|
|920140|Nieczęściowa weryfikacja treści żądania nie powiodła się|
|920160|Nagłówek HTTP o długości zawartości nie jest wartością numeryczną.|
|920170|Pobierz lub zamów żądanie z zawartością treści.|
|920171|Pobieranie lub żądanie żądania przy użyciu kodowania transferu.|
|920180|Żądanie POST nie zawiera nagłówka Content-Length.|
|920190|Zakres = nieprawidłowa wartość ostatniego bajtu.|
|920200|Zakres = zbyt wiele pól (co najmniej 6)|
|920201|Zakres = zbyt wiele pól dla żądania PDF (35 lub więcej)|
|920202|Zakres = zbyt wiele pól dla żądania PDF (co najmniej 6)|
|920210|Znaleziono wiele lub sprzeczne dane nagłówka połączenia.|
|920220|Próba ataku przy użyciu kodowania adresu URL|
|920230|Wykryto wiele kodowań adresów URL|
|920240|Próba ataku przy użyciu kodowania adresu URL|
|920250|Próba ataku przy użyciu kodowania UTF8|
|920260|Próba ataków typu Pełna/połówkowa w formacie Unicode|
|920270|Nieprawidłowy znak w żądaniu (znak null)|
|920271|Nieprawidłowy znak w żądaniu (znaki niedrukowalne)|
|920272|Nieprawidłowy znak w żądaniu (na zewnątrz znaków drukowalnych poniżej ASCII 127)|
|920273|Nieprawidłowy znak w żądaniu (poza bardzo rygorystycznym zestawem)|
|920274|Nieprawidłowy znak w nagłówkach żądania (poza bardzo rygorystycznym zestawem)|
|920280|W żądaniu brakuje nagłówka hosta|
|920290|Pusty nagłówek hosta|
|920300|W żądaniu brakuje nagłówka Accept|
|920310|Żądanie ma pusty nagłówek Accept|
|920311|Żądanie ma pusty nagłówek Accept|
|920320|Brak nagłówka agenta użytkownika|
|920330|Pusty nagłówek agenta użytkownika|
|920340|Żądanie zawierające zawartość, ale brak nagłówka Content-Type|
|920341|Żądanie zawierające zawartość wymaga nagłówka Content-Type|
|920350|Nagłówek hosta jest numerycznym adresem IP|
|920360|Zbyt długa nazwa argumentu|
|920370|Zbyt długa wartość argumentu|
|920380|Zbyt wiele argumentów w żądaniu|
|920390|Przekroczono łączny rozmiar argumentów|
|920400|Rozmiar przekazanego pliku jest zbyt duży|
|920410|Łączny rozmiar przekazanych plików jest zbyt duży|
|920420|Typ zawartości żądania nie jest dozwolony przez zasady|
|920430|Wersja protokołu HTTP nie jest dozwolona przez zasady|
|920440|Rozszerzenie pliku adresu URL jest ograniczone przez zasady|
|920450|Nagłówek HTTP jest ograniczony przez zasady (% @ {MATCHED_VAR})|
|920460|Nietypowe znaki ucieczki|
|920470|Niedozwolony nagłówek Content-Type|
|920480|Ogranicz Parametr charset w nagłówku Content-Type|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Opis|
|---|---|
|921110|Atak na przemycie żądania HTTP|
|921120|Atak dzielenia odpowiedzi HTTP|
|921130|Atak dzielenia odpowiedzi HTTP|
|921140|Atak z iniekcją nagłówka HTTP za pośrednictwem nagłówków|
|921150|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto CR/LF)|
|921151|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto CR/LF)|
|921160|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto znaki CR/LF i nagłówek-Name)|
|921170|Zanieczyszczenia parametrów HTTP|
|921180|Zanieczyszczenia parametrów HTTP (% {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Opis|
|---|---|
|930100|Atak przechodzenia do lokalizacji (/.. /)|
|930110|Atak przechodzenia do lokalizacji (/.. /)|
|930120|Próba dostępu do pliku systemu operacyjnego|
|930130|Próba dostępu do pliku z ograniczeniami|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Opis|
|---|---|
|931100|Możliwy atak pliku zdalnego dołączania (RFI) = adres URL przy użyciu adresu IP|
|931110|Możliwe ataki zdalnego dołączania plików (RFI) = wspólna RFI Nazwa parametru używana z ładunkiem w adresie URL|
|931120|Możliwy atak na zdalny dołączenie do pliku (RFI) = ładunek adresu URL używany z końcowym znakiem zapytania (?)|
|931130|Możliwe ataki zdalnego dołączania plików (RFI) = Off-Domain odwołanie/link|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Opis|
|---|---|
|932100|Zdalne wykonywanie poleceń: iniekcja poleceń systemu UNIX|
|932105|Zdalne wykonywanie poleceń: iniekcja poleceń systemu UNIX|
|932106|Zdalne wykonywanie poleceń: iniekcja poleceń systemu UNIX|
|932110|Zdalne wykonywanie poleceń: iniekcja poleceń systemu Windows|
|932115|Zdalne wykonywanie poleceń: iniekcja poleceń systemu Windows|
|932120|Zdalne wykonywanie poleceń = znaleziono polecenie programu Windows PowerShell|
|932130|Wykonanie polecenia zdalnego = znaleziono wyrażenie powłoki systemu UNIX|
|932140|Zdalne wykonywanie poleceń = znaleziono polecenie systemu Windows FOR/IF|
|932150|Zdalne wykonywanie poleceń: bezpośrednie wykonywanie polecenia w systemie UNIX|
|932160|Wykonanie polecenia zdalnego = znaleziono kod powłoki systemu UNIX|
|932170|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|
|932171|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|
|932180|Próba przekazania pliku z ograniczeniami|
|932190|Zdalne wykonywanie poleceń: próba wieloznacznej techniki obejścia|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Opis|
|---|---|
|933100|Atak z iniekcją PHP = znaleziono tag otwierającego/zamykającego|
|933110|Atak z iniekcją PHP = znaleziono plik skryptu PHP|
|933111|Atak z iniekcją PHP: znaleziono przekazywanie pliku skryptu PHP|
|933120|Atak z iniekcją PHP = znaleziono dyrektywę Configuration|
|933130|Atak z iniekcją PHP = znaleziono zmienne|
|933131|Atak z iniekcją PHP: znaleziono zmienne|
|933140|Atak z iniekcją PHP: znaleziono strumień we/wy|
|933150|Atak z iniekcją PHP = High-Risk znaleziono nazwę funkcji PHP|
|933151|Atak z iniekcją PHP: znaleziono Medium-Risk nazwę funkcji PHP|
|933160|Atak z iniekcją PHP = High-Risk znaleziono wywołanie funkcji PHP|
|933161|Atak z iniekcją PHP: znaleziono wywołanie funkcji języka PHP Low-Value|
|933170|Atak z iniekcją PHP: serializacja serializowanego obiektu|
|933180|Atak z iniekcją PHP = znaleziono wywołanie funkcji zmiennej|
|933190|Atak z iniekcją PHP: znaleziono tag zamykający PHP|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Opis|
|---|---|
|941100|Atak XSS wykryty za pośrednictwem libinjection|
|941101|Atak XSS wykryty za pośrednictwem libinjection|
|941110|Filtr XSS — kategoria 1 = wektor tagów skryptu|
|941130|Filtr XSS — Kategoria 3 = wektor atrybutu|
|941140|Filtr XSS — Kategoria 4 = wektor identyfikatora URI JavaScript|
|941150|Filtr XSS — kategoria 5 = niedozwolone atrybuty HTML|
|941160|NoScript XSS InjectionChecker: iniekcja kodu HTML|
|941170|NoScript XSS InjectionChecker: iniekcja atrybutów|
|941180|Node-Validator słowa kluczowe listy blokowania|
|941190|XSS przy użyciu arkuszy stylów|
|941200|XSS przy użyciu ramek VML|
|941210|XSS przy użyciu zasłoniętego języka JavaScript|
|941220|XSS przy użyciu zasłoniętego skryptu VB|
|941230|XSS przy użyciu tagu "embed"|
|941240|XSS przy użyciu atrybutu "Import" lub "implementacja"|
|941250|Filtry XSS programu IE — wykryto atak|
|941260|XSS przy użyciu tagu "meta"|
|941270|XSS przy użyciu "link" href|
|941280|XSS przy użyciu tagu "Base"|
|941290|XSS przy użyciu tagu "applet"|
|941300|XSS przy użyciu tagu "Object"|
|941310|Filtr XSS z nieprawidłowo sformułowanym kodowaniem ASCII — wykryto atak.|
|941320|Możliwe ataki XSS wykryto procedurę obsługi tagów HTML|
|941330|Filtry XSS programu IE — wykryto atak.|
|941340|Filtry XSS programu IE — wykryto atak.|
|941350|Kodowanie UTF-7 IE XSS-atak wykryty.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Opis|
|---|---|
|942100|Atak na wstrzyknięcie kodu SQL został wykryty za pośrednictwem libinjection|
|942110|Atak iniekcji kodu SQL: wykryto wspólne testowanie iniekcji|
|942120|Atak iniekcji kodu SQL: wykryto operator SQL|
|942130|Atak iniekcji kodu SQL: wykryto tautology języka SQL.|
|942140|Atak iniekcji SQL = wykryto wspólną nazwę bazy danych|
|942150|Atak iniekcji kodu SQL|
|942160|Wykrywa testy niewidomych SQLi za pomocą uśpienia () lub testu porównawczego ().|
|942170|Wykrywa testy porównawcze SQL i iniekcje uśpienia, w tym zapytania warunkowe|
|942180|Wykrywa podstawowe próby obejścia uwierzytelniania SQL 1/3|
|942190|Wykrywa wykonywanie kodu MSSQL i prób zbierania informacji|
|942200|Wykrywa Komentarze MySQL — iniekcje/Space-Obfuscated i zakończenie taktu|
|942210|Wykrywa łańcuch prób iniekcji kodu SQL 1/2|
|942220|Szukasz ataków za przepełnienie całkowite, są one pobierane z skipfish, z wyjątkiem 3.0.00738585072|
|942230|Wykrywa warunkowe próby iniekcji SQL|
|942240|Wykrywa próby przełączenia zestawu znaków MySQL i usługi MSSQL DoS|
|942250|Wykrywa dopasowanie, SCALAnie i wykonywanie NATYCHMIASTowego wstrzykiwania|
|942251|Wykrywa iniekcje|
|942260|Wykrywa podstawowe próby obejścia uwierzytelniania SQL 2/3|
|942270|Wyszukiwanie podstawowej iniekcji kodu SQL. Typowy ciąg ataku dla programu MySQL Oracle i innych|
|942280|Wykrywa Postgres pg_sleep, ataki opóźnień i zamknięcia bazy danych|
|942290|Znajdowanie podstawowych prób iniekcji kodu SQL MongoDB|
|942300|Wykrywa Komentarze programu MySQL, warunki i wstrzyknięcia języka r (a)|
|942310|Wykrywa łańcuch prób iniekcji kodu SQL 2/2|
|942320|Wykrywa procedury składowane MySQL i PostgreSQL oraz iniekcje funkcji.|
|942330|Wykrywa badanie klasycznego wstrzykiwania kodu SQL 1/2|
|942340|Wykrywa podstawowe próby obejścia uwierzytelniania SQL 3/3|
|942350|Wykrywa wprowadzanie danych UDF w formacie MySQL oraz inne próby manipulowania danymi/strukturą|
|942360|Wykrywa połączone, podstawowe próby iniekcji SQL i SQLLFI|
|942361|Wykrywa podstawowe iniekcje kodu SQL na podstawie zmiany lub złożenia słowa kluczowego|
|942370|Wykrywa badanie klasycznego wstrzykiwania kodu SQL 2/2|
|942380|Atak iniekcji kodu SQL|
|942390|Atak iniekcji kodu SQL|
|942400|Atak iniekcji kodu SQL|
|942410|Atak iniekcji kodu SQL|
|942420|Ograniczone wykrywanie anomalii znaków SQL (pliki cookie): Przekroczono liczbę znaków specjalnych (8)|
|942421|Ograniczone wykrywanie anomalii znaków SQL (pliki cookie): Przekroczono liczbę znaków specjalnych (3)|
|942430|Ograniczone wykrywanie anomalii znaków SQL (args): Przekroczono liczbę znaków specjalnych (12)|
|942431|Ograniczone wykrywanie anomalii znaków SQL (args): Przekroczono liczbę znaków specjalnych (6)|
|942432|Ograniczone wykrywanie anomalii znaków SQL (args): Przekroczono liczbę znaków specjalnych (2)|
|942440|Wykryto sekwencję komentarzy SQL.|
|942450|Zidentyfikowano kodowanie szesnastkowe języka SQL|
|942460|Alert wykrywania anomalii Meta-Character — powtarzające się znaki niebędące słowami|
|942470|Atak iniekcji kodu SQL|
|942480|Atak iniekcji kodu SQL|
|942490|Wykrywa badanie klasycznego wstrzykiwania kodu SQL 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Opis|
|---|---|
|943100|Możliwy atak utrwalania sesji = Ustawianie wartości plików cookie w kodzie HTML|
|943110|Możliwy atak utrwalania sesji = Nazwa parametru SessionID z Off-Domain odwołującym|
|943120|Możliwy atak utrwalania sesji = Nazwa parametru SessionID bez odwołującego się|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">ŻĄDANIE-944 — APLIKACJA-ATAK — SESJA — JAVA</p>

|RuleId|Opis|
|---|---|
|944120|Możliwe wykonanie ładunku i wykonanie polecenia zdalnego|
|944130|Podejrzane klasy języka Java|
|944200|Wykorzystanie deserializacji platformy Apache Commons Attribution|

# <a name="owasp-30"></a>[OWASP 3,0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a> Zestawy reguł

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Ogólne</p>

|RuleId|Opis|
|---|---|
|200004|Możliwa wieloczęściowa niezgodna granica.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Opis|
|---|---|
|911100|Metoda nie jest dozwolona przez zasady|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Opis|
|---|---|
|913100|Znaleziono User-Agent skojarzone ze skanerem zabezpieczeń|
|913110|Znaleziono nagłówek żądania skojarzony ze skanerem zabezpieczeń|
|913120|Znaleziono nazwę pliku żądania/argument skojarzony z skanerem zabezpieczeń|
|913101|Znaleziono User-Agent skojarzone z skryptem/ogólnym klientem HTTP|
|913102|Znaleziono User-Agent skojarzone z przeszukiwarką sieci Web/bot|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Opis|
|---|---|
|920100|Nieprawidłowy wiersz żądania HTTP|
|920130|Nie można przeanalizować treści żądania.|
|920140|Nieczęściowa weryfikacja treści żądania nie powiodła się|
|920160|Nagłówek HTTP o długości zawartości nie jest wartością numeryczną.|
|920170|Pobierz lub zamów żądanie z zawartością treści.|
|920180|Żądanie POST nie zawiera nagłówka Content-Length.|
|920190|Zakres = nieprawidłowa wartość ostatniego bajtu.|
|920210|Znaleziono wiele lub sprzeczne dane nagłówka połączenia.|
|920220|Próba ataku przy użyciu kodowania adresu URL|
|920240|Próba ataku przy użyciu kodowania adresu URL|
|920250|Próba ataku przy użyciu kodowania UTF8|
|920260|Próba ataków typu Pełna/połówkowa w formacie Unicode|
|920270|Nieprawidłowy znak w żądaniu (znak null)|
|920280|W żądaniu brakuje nagłówka hosta|
|920290|Pusty nagłówek hosta|
|920310|Żądanie ma pusty nagłówek Accept|
|920311|Żądanie ma pusty nagłówek Accept|
|920330|Pusty nagłówek agenta użytkownika|
|920340|Żądanie zawierające zawartość, ale brak nagłówka Content-Type|
|920350|Nagłówek hosta jest numerycznym adresem IP|
|920380|Zbyt wiele argumentów w żądaniu|
|920360|Zbyt długa nazwa argumentu|
|920370|Zbyt długa wartość argumentu|
|920390|Przekroczono łączny rozmiar argumentów|
|920400|Rozmiar przekazanego pliku jest zbyt duży|
|920410|Łączny rozmiar przekazanych plików jest zbyt duży|
|920420|Typ zawartości żądania nie jest dozwolony przez zasady|
|920430|Wersja protokołu HTTP nie jest dozwolona przez zasady|
|920440|Rozszerzenie pliku adresu URL jest ograniczone przez zasady|
|920450|Nagłówek HTTP jest ograniczony przez zasady (% @ {MATCHED_VAR})|
|920200|Zakres = zbyt wiele pól (co najmniej 6)|
|920201|Zakres = zbyt wiele pól dla żądania PDF (35 lub więcej)|
|920230|Wykryto wiele kodowań adresów URL|
|920300|W żądaniu brakuje nagłówka Accept|
|920271|Nieprawidłowy znak w żądaniu (znaki niedrukowalne)|
|920320|Brak nagłówka agenta użytkownika|
|920272|Nieprawidłowy znak w żądaniu (na zewnątrz znaków drukowalnych poniżej ASCII 127)|
|920202|Zakres = zbyt wiele pól dla żądania PDF (co najmniej 6)|
|920273|Nieprawidłowy znak w żądaniu (poza bardzo rygorystycznym zestawem)|
|920274|Nieprawidłowy znak w nagłówkach żądania (poza bardzo rygorystycznym zestawem)|
|920460|Nietypowe znaki ucieczki|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Opis|
|---|---|
|921100|Atak na żądanie HTTP.|
|921110|Atak na przemycie żądania HTTP|
|921120|Atak dzielenia odpowiedzi HTTP|
|921130|Atak dzielenia odpowiedzi HTTP|
|921140|Atak z iniekcją nagłówka HTTP za pośrednictwem nagłówków|
|921150|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto CR/LF)|
|921160|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto znaki CR/LF i nagłówek-Name)|
|921151|Atak z iniekcją nagłówka HTTP za pośrednictwem ładunku (wykryto CR/LF)|
|921170|Zanieczyszczenia parametrów HTTP|
|921180|Zanieczyszczenia parametrów HTTP (% @ {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Opis|
|---|---|
|930100|Atak przechodzenia do lokalizacji (/.. /)|
|930110|Atak przechodzenia do lokalizacji (/.. /)|
|930120|Próba dostępu do pliku systemu operacyjnego|
|930130|Próba dostępu do pliku z ograniczeniami|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Opis|
|---|---|
|931100|Możliwy atak pliku zdalnego dołączania (RFI) = adres URL przy użyciu adresu IP|
|931110|Możliwe ataki zdalnego dołączania plików (RFI) = wspólna RFI Nazwa parametru używana z ładunkiem w adresie URL|
|931120|Możliwy atak na zdalny dołączenie do pliku (RFI) = ładunek adresu URL używany z końcowym znakiem zapytania (?)|
|931130|Możliwe ataki zdalnego dołączania plików (RFI) = Off-Domain odwołanie/link|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Opis|
|---|---|
|932120|Zdalne wykonywanie poleceń = znaleziono polecenie programu Windows PowerShell|
|932130|Wykonanie polecenia zdalnego = znaleziono wyrażenie powłoki systemu UNIX|
|932140|Zdalne wykonywanie poleceń = znaleziono polecenie systemu Windows FOR/IF|
|932160|Wykonanie polecenia zdalnego = znaleziono kod powłoki systemu UNIX|
|932170|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|
|932171|Wykonanie polecenia zdalnego = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Opis|
|---|---|
|933100|Atak z iniekcją PHP = znaleziono tag otwierającego/zamykającego|
|933110|Atak z iniekcją PHP = znaleziono plik skryptu PHP|
|933120|Atak z iniekcją PHP = znaleziono dyrektywę Configuration|
|933130|Atak z iniekcją PHP = znaleziono zmienne|
|933150|Atak z iniekcją PHP = High-Risk znaleziono nazwę funkcji PHP|
|933160|Atak z iniekcją PHP = High-Risk znaleziono wywołanie funkcji PHP|
|933180|Atak z iniekcją PHP = znaleziono wywołanie funkcji zmiennej|
|933151|Atak z iniekcją PHP = Medium-Risk znaleziono nazwę funkcji PHP|
|933131|Atak z iniekcją PHP = znaleziono zmienne|
|933161|Atak z iniekcją PHP = Low-Value znaleziono wywołanie funkcji PHP|
|933111|Atak z iniekcją PHP = znaleziono plik skryptu PHP|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Opis|
|---|---|
|941100|Atak XSS wykryty za pośrednictwem libinjection|
|941110|Filtr XSS — kategoria 1 = wektor tagów skryptu|
|941130|Filtr XSS — Kategoria 3 = wektor atrybutu|
|941140|Filtr XSS — Kategoria 4 = wektor identyfikatora URI JavaScript|
|941150|Filtr XSS — kategoria 5 = niedozwolone atrybuty HTML|
|941180|Node-Validator słowa kluczowe listy blokowania|
|941190|XSS przy użyciu arkuszy stylów|
|941200|XSS przy użyciu ramek VML|
|941210|XSS przy użyciu zasłoniętego języka JavaScript|
|941220|XSS przy użyciu zasłoniętego skryptu VB|
|941230|XSS przy użyciu tagu "embed"|
|941240|XSS przy użyciu atrybutu "Import" lub "implementacja"|
|941260|XSS przy użyciu tagu "meta"|
|941270|XSS przy użyciu "link" href|
|941280|XSS przy użyciu tagu "Base"|
|941290|XSS przy użyciu tagu "applet"|
|941300|XSS przy użyciu tagu "Object"|
|941310|Filtr XSS z nieprawidłowo sformułowanym kodowaniem ASCII — wykryto atak.|
|941330|Filtry XSS programu IE — wykryto atak.|
|941340|Filtry XSS programu IE — wykryto atak.|
|941350|Kodowanie UTF-7 IE XSS-atak wykryty.|
|941320|Możliwe ataki XSS wykryto procedurę obsługi tagów HTML|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Opis|
|---|---|
|942100|Atak na wstrzyknięcie kodu SQL został wykryty za pośrednictwem libinjection|
|942110|Atak iniekcji kodu SQL: wykryto wspólne testowanie iniekcji|
|942130|Atak iniekcji kodu SQL: wykryto tautology języka SQL.|
|942140|Atak iniekcji SQL = wykryto wspólną nazwę bazy danych|
|942160|Wykrywa testy niewidomych SQLi za pomocą uśpienia () lub testu porównawczego ().|
|942170|Wykrywa testy porównawcze SQL i iniekcje uśpienia, w tym zapytania warunkowe|
|942190|Wykrywa wykonywanie kodu MSSQL i prób zbierania informacji|
|942200|Wykrywa Komentarze MySQL — iniekcje/Space-Obfuscated i zakończenie taktu|
|942230|Wykrywa warunkowe próby iniekcji SQL|
|942260|Wykrywa podstawowe próby obejścia uwierzytelniania SQL 2/3|
|942270|Wyszukiwanie podstawowej iniekcji kodu SQL. Typowy ciąg ataku dla programu MySQL Oracle i innych.|
|942290|Znajdowanie podstawowych prób iniekcji kodu SQL MongoDB|
|942300|Wykrywa Komentarze programu MySQL, warunki i wstrzyknięcia języka r (a)|
|942310|Wykrywa łańcuch prób iniekcji kodu SQL 2/2|
|942320|Wykrywa procedury składowane MySQL i PostgreSQL oraz iniekcje funkcji.|
|942330|Wykrywa badanie klasycznego wstrzykiwania kodu SQL 1/2|
|942340|Wykrywa podstawowe próby obejścia uwierzytelniania SQL 3/3|
|942350|Wykrywa wprowadzanie danych UDF w formacie MySQL oraz inne próby manipulowania danymi/strukturą|
|942360|Wykrywa połączone, podstawowe próby iniekcji SQL i SQLLFI|
|942370|Wykrywa badanie klasycznego wstrzykiwania kodu SQL 2/2|
|942150|Atak iniekcji kodu SQL|
|942410|Atak iniekcji kodu SQL|
|942430|Ograniczone wykrywanie anomalii znaków SQL (args): Przekroczono liczbę znaków specjalnych (12)|
|942440|Wykryto sekwencję komentarzy SQL.|
|942450|Zidentyfikowano kodowanie szesnastkowe języka SQL|
|942251|Wykrywa iniekcje|
|942460|Alert wykrywania anomalii Meta-Character — powtarzające się znaki niebędące słowami|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Opis|
|---|---|
|943100|Możliwy atak utrwalania sesji = Ustawianie wartości plików cookie w kodzie HTML|
|943110|Możliwy atak utrwalania sesji = Nazwa parametru SessionID z Off-Domain odwołującym|
|943120|Możliwy atak utrwalania sesji = Nazwa parametru SessionID bez odwołującego się|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a> Zestawy reguł

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|Opis|
|---|---|
|960911|Nieprawidłowy wiersz żądania HTTP|
|981227|Błąd Apache = nieprawidłowy identyfikator URI w żądaniu.|
|960912|Nie można przeanalizować treści żądania.|
|960914|Nieczęściowa weryfikacja treści żądania nie powiodła się|
|960915|Parser wieloczęściowy wykrył możliwe niepasującą granicę.|
|960016|Nagłówek HTTP o długości zawartości nie jest wartością numeryczną.|
|960011|Pobierz lub zamów żądanie z zawartością treści.|
|960012|Żądanie POST nie zawiera nagłówka Content-Length.|
|960902|Nieprawidłowe użycie kodowania tożsamości.|
|960022|Oczekiwano, że nagłówek nie jest dozwolony dla HTTP 1,0.|
|960020|Nagłówek Pragma wymaga nagłówka Cache-Control dla żądań HTTP/1.1.|
|958291|Zakres = pole istnieje i zaczyna się od 0.|
|958230|Zakres = nieprawidłowa wartość ostatniego bajtu.|
|958295|Znaleziono wiele lub sprzeczne dane nagłówka połączenia.|
|950107|Próba ataku przy użyciu kodowania adresu URL|
|950109|Wykryto wiele kodowań adresów URL|
|950108|Próba ataku przy użyciu kodowania adresu URL|
|950801|Próba ataku przy użyciu kodowania UTF8|
|950116|Próba ataków typu Pełna/połówkowa w formacie Unicode|
|960901|Nieprawidłowy znak w żądaniu|
|960018|Nieprawidłowy znak w żądaniu|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Opis|
|---|---|
|960008|W żądaniu brakuje nagłówka hosta|
|960007|Pusty nagłówek hosta|
|960015|W żądaniu brakuje nagłówka Accept|
|960021|Żądanie ma pusty nagłówek Accept|
|960009|W żądaniu brakuje nagłówka agenta użytkownika|
|960006|Pusty nagłówek agenta użytkownika|
|960904|Żądanie zawierające zawartość, ale brak nagłówka Content-Type|
|960017|Nagłówek hosta jest numerycznym adresem IP|

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|RuleId|Opis|
|---|---|
|960209|Zbyt długa nazwa argumentu|
|960208|Zbyt długa wartość argumentu|
|960335|Zbyt wiele argumentów w żądaniu|
|960341|Przekroczono łączny rozmiar argumentów|
|960342|Rozmiar przekazanego pliku jest zbyt duży|
|960343|Łączny rozmiar przekazanych plików jest zbyt duży|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|RuleId|Opis|
|---|---|
|960032|Metoda nie jest dozwolona przez zasady|
|960010|Typ zawartości żądania nie jest dozwolony przez zasady|
|960034|Wersja protokołu HTTP nie jest dozwolona przez zasady|
|960035|Rozszerzenie pliku adresu URL jest ograniczone przez zasady|
|960038|Nagłówek HTTP jest ograniczony przez zasady|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|RuleId|Opis|
|---|---|
|990002|Żądanie oznacza, że skaner zabezpieczeń przeskanował lokację|
|990901|Żądanie oznacza, że skaner zabezpieczeń przeskanował lokację|
|990902|Żądanie oznacza, że skaner zabezpieczeń przeskanował lokację|
|990012|Nieautoryzowany przeszukiwarka witryn sieci Web|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|RuleId|Opis|
|---|---|
|960024|Alert wykrywania anomalii Meta-Character — powtarzające się znaki niebędące słowami|
|950008|Iniekcja nieudokumentowanych tagów ColdFusion|
|950010|Atak iniekcji LDAP|
|950011|Atak z iniekcją SSI|
|950018|Wykryto adres URL XSS uniwersalnego pliku PDF.|
|950019|Atak z iniekcją poczty e-mail|
|950012|Atak na żądanie HTTP.|
|950910|Atak dzielenia odpowiedzi HTTP|
|950911|Atak dzielenia odpowiedzi HTTP|
|950117|Atak z dołączeniem do pliku zdalnego|
|950118|Atak z dołączeniem do pliku zdalnego|
|950119|Atak z dołączeniem do pliku zdalnego|
|950120|Możliwe ataki zdalnego dołączania plików (RFI) = Off-Domain odwołanie/link|
|981133|Reguła 981133|
|981134|Reguła 981134|
|950009|Atak utrwalania sesji|
|950003|Utrwalanie sesji|
|950000|Utrwalanie sesji|
|950005|Próba dostępu do pliku zdalnego|
|950002|Dostęp do poleceń systemowych|
|950006|Iniekcja poleceń systemowych|
|959151|Atak z iniekcją PHP|
|958976|Atak z iniekcją PHP|
|958977|Atak z iniekcją PHP|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Opis|
|---|---|
|981231|Wykryto sekwencję komentarzy SQL.|
|981260|Zidentyfikowano kodowanie szesnastkowe języka SQL|
|981320|Atak iniekcji SQL = wykryto wspólną nazwę bazy danych|
|981300|Reguła 981300|
|981301|Reguła 981301|
|981302|Reguła 981302|
|981303|Reguła 981303|
|981304|Reguła 981304|
|981305|Reguła 981305|
|981306|Reguła 981306|
|981307|Reguła 981307|
|981308|Reguła 981308|
|981309|Reguła 981309|
|981310|Reguła 981310|
|981311|Reguła 981311|
|981312|Reguła 981312|
|981313|Reguła 981313|
|981314|Reguła 981314|
|981315|Reguła 981315|
|981316|Reguła 981316|
|981317|Alert wykrywania anomalii w instrukcji SELECT języka SQL|
|950007|Atak polegający na wstrzyknięciu kodu SQL|
|950001|Atak iniekcji kodu SQL|
|950908|Atak iniekcji kodu SQL.|
|959073|Atak iniekcji kodu SQL|
|981272|Wykrywa testy niewidomych SQLi za pomocą uśpienia () lub testu porównawczego ().|
|981250|Wykrywa testy porównawcze SQL i iniekcje uśpienia, w tym zapytania warunkowe|
|981241|Wykrywa warunkowe próby iniekcji SQL|
|981276|Wyszukiwanie podstawowej iniekcji kodu SQL. Typowy ciąg ataku dla programu MySQL Oracle i innych.|
|981270|Znajdowanie podstawowych prób iniekcji kodu SQL MongoDB|
|981253|Wykrywa procedury składowane MySQL i PostgreSQL oraz iniekcje funkcji.|
|981251|Wykrywa wprowadzanie danych UDF w formacie MySQL oraz inne próby manipulowania danymi/strukturą|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Opis|
|---|---|
|973336|Filtr XSS — kategoria 1 = wektor tagów skryptu|
|973338|Filtr XSS — Kategoria 3 = wektor identyfikatora URI JavaScript|
|981136|Reguła 981136|
|981018|Reguła 981018|
|958016|Atak na skrypty między lokacjami (XSS)|
|958414|Atak na skrypty między lokacjami (XSS)|
|958032|Atak na skrypty między lokacjami (XSS)|
|958026|Atak na skrypty między lokacjami (XSS)|
|958027|Atak na skrypty między lokacjami (XSS)|
|958054|Atak na skrypty między lokacjami (XSS)|
|958418|Atak na skrypty między lokacjami (XSS)|
|958034|Atak na skrypty między lokacjami (XSS)|
|958019|Atak na skrypty między lokacjami (XSS)|
|958013|Atak na skrypty między lokacjami (XSS)|
|958408|Atak na skrypty między lokacjami (XSS)|
|958012|Atak na skrypty między lokacjami (XSS)|
|958423|Atak na skrypty między lokacjami (XSS)|
|958002|Atak na skrypty między lokacjami (XSS)|
|958017|Atak na skrypty między lokacjami (XSS)|
|958007|Atak na skrypty między lokacjami (XSS)|
|958047|Atak na skrypty między lokacjami (XSS)|
|958410|Atak na skrypty między lokacjami (XSS)|
|958415|Atak na skrypty między lokacjami (XSS)|
|958022|Atak na skrypty między lokacjami (XSS)|
|958405|Atak na skrypty między lokacjami (XSS)|
|958419|Atak na skrypty między lokacjami (XSS)|
|958028|Atak na skrypty między lokacjami (XSS)|
|958057|Atak na skrypty między lokacjami (XSS)|
|958031|Atak na skrypty między lokacjami (XSS)|
|958006|Atak na skrypty między lokacjami (XSS)|
|958033|Atak na skrypty między lokacjami (XSS)|
|958038|Atak na skrypty między lokacjami (XSS)|
|958409|Atak na skrypty między lokacjami (XSS)|
|958001|Atak na skrypty między lokacjami (XSS)|
|958005|Atak na skrypty między lokacjami (XSS)|
|958404|Atak na skrypty między lokacjami (XSS)|
|958023|Atak na skrypty między lokacjami (XSS)|
|958010|Atak na skrypty między lokacjami (XSS)|
|958411|Atak na skrypty między lokacjami (XSS)|
|958422|Atak na skrypty między lokacjami (XSS)|
|958036|Atak na skrypty między lokacjami (XSS)|
|958000|Atak na skrypty między lokacjami (XSS)|
|958018|Atak na skrypty między lokacjami (XSS)|
|958406|Atak na skrypty między lokacjami (XSS)|
|958040|Atak na skrypty między lokacjami (XSS)|
|958052|Atak na skrypty między lokacjami (XSS)|
|958037|Atak na skrypty między lokacjami (XSS)|
|958049|Atak na skrypty między lokacjami (XSS)|
|958030|Atak na skrypty między lokacjami (XSS)|
|958041|Atak na skrypty między lokacjami (XSS)|
|958416|Atak na skrypty między lokacjami (XSS)|
|958024|Atak na skrypty między lokacjami (XSS)|
|958059|Atak na skrypty między lokacjami (XSS)|
|958417|Atak na skrypty między lokacjami (XSS)|
|958020|Atak na skrypty między lokacjami (XSS)|
|958045|Atak na skrypty między lokacjami (XSS)|
|958004|Atak na skrypty między lokacjami (XSS)|
|958421|Atak na skrypty między lokacjami (XSS)|
|958009|Atak na skrypty między lokacjami (XSS)|
|958025|Atak na skrypty między lokacjami (XSS)|
|958413|Atak na skrypty między lokacjami (XSS)|
|958051|Atak na skrypty między lokacjami (XSS)|
|958420|Atak na skrypty między lokacjami (XSS)|
|958407|Atak na skrypty między lokacjami (XSS)|
|958056|Atak na skrypty między lokacjami (XSS)|
|958011|Atak na skrypty między lokacjami (XSS)|
|958412|Atak na skrypty między lokacjami (XSS)|
|958008|Atak na skrypty między lokacjami (XSS)|
|958046|Atak na skrypty między lokacjami (XSS)|
|958039|Atak na skrypty między lokacjami (XSS)|
|958003|Atak na skrypty między lokacjami (XSS)|
|973300|Możliwe ataki XSS wykryto procedurę obsługi tagów HTML|
|973301|Wykryto atak typu XSS|
|973302|Wykryto atak typu XSS|
|973303|Wykryto atak typu XSS|
|973304|Wykryto atak typu XSS|
|973305|Wykryto atak typu XSS|
|973306|Wykryto atak typu XSS|
|973307|Wykryto atak typu XSS|
|973308|Wykryto atak typu XSS|
|973309|Wykryto atak typu XSS|
|973311|Wykryto atak typu XSS|
|973313|Wykryto atak typu XSS|
|973314|Wykryto atak typu XSS|
|973331|Filtry XSS programu IE — wykryto atak.|
|973315|Filtry XSS programu IE — wykryto atak.|
|973330|Filtry XSS programu IE — wykryto atak.|
|973327|Filtry XSS programu IE — wykryto atak.|
|973326|Filtry XSS programu IE — wykryto atak.|
|973346|Filtry XSS programu IE — wykryto atak.|
|973345|Filtry XSS programu IE — wykryto atak.|
|973324|Filtry XSS programu IE — wykryto atak.|
|973323|Filtry XSS programu IE — wykryto atak.|
|973348|Filtry XSS programu IE — wykryto atak.|
|973321|Filtry XSS programu IE — wykryto atak.|
|973320|Filtry XSS programu IE — wykryto atak.|
|973318|Filtry XSS programu IE — wykryto atak.|
|973317|Filtry XSS programu IE — wykryto atak.|
|973329|Filtry XSS programu IE — wykryto atak.|
|973328|Filtry XSS programu IE — wykryto atak.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|RuleId|Opis|
|---|---|
|950103|Atak z przechodzeniem ścieżki|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|Opis|
|---|---|
|950110|Dostęp tylne wejście|
|950921|Dostęp tylne wejście|
|950922|Dostęp tylne wejście|

---

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie reguł zapory aplikacji sieci Web przy użyciu Azure Portal](application-gateway-customize-waf-rules-portal.md)
