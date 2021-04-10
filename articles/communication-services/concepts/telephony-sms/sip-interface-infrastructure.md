---
title: Wymagania dotyczące infrastruktury SIP Interface — usługi Azure Communication Services
description: Zapoznaj się z wymaganiami dotyczącymi infrastruktury dla konfiguracji interfejsu SIP usługi Azure Communication Services
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ede650ae072ef53ed40a9372a292ab69fe8cc1af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492732"
---
# <a name="sip-interface-infrastructure-requirements"></a>Wymagania dotyczące infrastruktury interfejsu SIP 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
W tym artykule opisano informacje o łączności infrastruktury, licencjonowania i kontrolera borderers Controller (SBC), które należy wziąć pod uwagę podczas planowania wdrożenia interfejsu SIP.


## <a name="infrastructure-requirements"></a>Wymagania dotyczące infrastruktury
Wymagania dotyczące infrastruktury dla obsługiwanych interfejsów SBCs, domen i innych wymagań dotyczących łączności sieciowej w celu wdrożenia interfejsu SIP są wymienione w poniższej tabeli:  

|Wymagania dotyczące infrastruktury|Potrzebne są następujące elementy:|
|:--- |:--- |
|Kontroler graniczny sesji (SBC)|Obsługiwana wartość SBC. Aby uzyskać więcej informacji, zobacz temat [obsługiwane SBCs](#supported-session-border-controllers-sbcs).|
|Magistrale telefonii połączone z usługą SBC|Co najmniej jeden z magistrali telefonii połączonej z usługą SBC. Na jednym końcu usługa SBC nawiązuje połączenie z usługą Azure Communications za pośrednictwem interfejsu SIP. Firma SBC może również łączyć się z jednostkami telefonii innych firm, takimi jak PBXs, analogowe karty telefoniczne itd. Każda opcja łączności PSTN, która jest połączona z usługą SBC, będzie działała. (W celu skonfigurowania łączy sieci PSTN do usługi SBC należy zapoznać się z dostawcami usług SBC lub dostawcami magistrali).|
|Subskrypcja platformy Azure|Subskrypcja platformy Azure używana do tworzenia zasobów ACS oraz konfiguracja i połączenie z usługą SBC.|
|Token dostępu do usług komunikacyjnych|Aby wykonać wywołania, wymagany jest prawidłowy token dostępu z `voip` zakresem. Zobacz [tokeny dostępu](../identity-model.md#access-tokens)|
|Publiczny adres IP dla usługi SBC|Publiczny adres IP, którego można użyć do nawiązania połączenia z usługą SBC. Na podstawie typu SBC można użyć translatora adresów sieciowych.|
|W pełni kwalifikowana nazwa domeny (FQDN) dla usługi SBC|Nazwa FQDN dla SBC, gdzie część domeny FQDN nie jest zgodna z zarejestrowanymi domenami w organizacji Microsoft 365 lub Office 365. Aby uzyskać więcej informacji, zobacz [nazwy domen SBC](#sbc-domain-names).|
|Publiczny wpis DNS dla SBC |Publiczny wpis DNS mapowanie nazwy FQDN usługi SBC na publiczny adres IP. |
|Publiczny zaufany certyfikat dla usługi SBC |Certyfikat dla elementu SBC, który ma być używany do komunikacji z interfejsem SIP. Aby uzyskać więcej informacji, zobacz [publiczny zaufany certyfikat dla programu SBC](#public-trusted-certificate-for-the-sbc).|
|Adresy IP i porty zapory na potrzeby sygnalizowania i nośnika SIP |Usługa SBC komunikuje się z następującymi usługami w chmurze:<br/><br/>Serwer proxy SIP, który obsługuje sygnalizowanie<br/>Procesor multimediów obsługujący multimedia<br/><br/>Te dwie usługi mają oddzielne adresy IP w Microsoft Cloud, opisane w dalszej części tego dokumentu.


## <a name="sbc-domain-names"></a>Nazwy domen SBC

Klienci bez pakietu Office 365 mogą używać dowolnej nazwy domeny, dla której mogą uzyskać certyfikat publiczny.

W poniższej tabeli przedstawiono przykłady nazw DNS zarejestrowanych dla dzierżawy, czy nazwa może być używana jako w pełni kwalifikowana nazwa domeny (FQDN) dla SBC i przykłady prawidłowych nazw FQDN:

|Nazwa DNS|Może być używany dla nazwy FQDN usługi SBC|Przykłady nazw FQDN|
|:--- |:--- |:--- |
contoso.com|Tak|**Prawidłowe nazwy:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|Nie|Używanie domen *. onmicrosoft.com nie jest obsługiwane w przypadku nazw SBC

Jeśli jesteś klientem z pakietem Office 365, nazwa domeny SBC nie może być zgodna z zarejestrowane w domenach dzierżawy pakietu Office 365. Poniżej znajduje się przykład współistnienia usług Office 365 i Azure Communications Service:

|Domena zarejestrowana w pakiecie Office 365|Przykłady nazwy FQDN programu SBC w zespołach|Przykłady nazw FQDN usługi SBC w usłudze ACS|
|:--- |:--- |:--- |
**contoso.com** (domena drugiego poziomu)|**SBC.contoso.com** (nazwa w domenie drugiego poziomu)|**SBC.ACS.contoso.com** (nazwa w domenie trzeciego poziomu)<br/>**SBC.fabrikam.com** (dowolna nazwa w innej domenie)|
|**O365.contoso.com** (domena trzeciego poziomu)|**SBC.O365.contoso.com** (nazwa w domenie trzeciego poziomu)|**SBC.contoso.com** (nazwa w domenie drugiego poziomu)<br/>**SBC.ACS.O365.contoso.com** (nazwa w domenie czwartego poziomu)<br/>**SBC.fabrikam.com** (dowolna nazwa w innej domenie)

Parowanie SBC działa na poziomie zasobów usług komunikacyjnych, co oznacza, że można sparować wiele SBCs z jednym zasobem usług komunikacyjnych, ale nie można sparować pojedynczej klasy SBC z więcej niż jednym zasobem usług komunikacyjnych. Unikatowe nazwy FQDN usługi SBC są wymagane do parowania z różnymi zasobami.

## <a name="public-trusted-certificate-for-the-sbc"></a>Publiczny zaufany certyfikat dla usługi SBC

Firma Microsoft zaleca, aby zażądać certyfikatu dla usługi SBC, generując żądanie podpisania certyfikatu (CSR). Aby uzyskać szczegółowe instrukcje dotyczące generowania CSR dla usługi SBC, zapoznaj się z instrukcjami lub dokumentacją dotyczącymi połączeń oferowanych przez dostawców SBC. 

  > [!NOTE]
  > Większość urzędów certyfikacji wymaga, aby rozmiar klucza prywatnego był co najmniej 2048. Należy pamiętać o tym podczas generowania CSR.

Certyfikat musi mieć nazwę FQDN programu SBC w postaci nazwy pospolitej (CN) lub pola alternatywnej nazwy podmiotu (SAN). Certyfikat powinien być wystawiony bezpośrednio od urzędu certyfikacji, a nie od dostawcy pośredniego.

Alternatywnie interfejs SIP usług komunikacyjnych obsługuje symbol wieloznaczny w CN i/lub sieci SAN, a symbol wieloznaczny jest zgodny ze standardowym [RFC http przez protokół TLS](https://tools.ietf.org/html/rfc2818#section-3.1). 

Przykładem będzie użycie, `\*.contoso.com` które byłoby zgodne z nazwą FQDN usługi SBC `sbc.contoso.com` , ale nie byłoby zgodne z `sbc.test.contoso.com` .

Certyfikat musi zostać wygenerowany przez jeden z następujących głównych urzędów certyfikacji:

- AffirmTrust
- $ Ufaj zewnętrznym certyfikatem głównym urzędu certyfikacji
- Baltimore CyberTrust, główny *
- Buypass
- Cybertrust
- Publiczny podstawowy urząd certyfikacji klasy 3
- Comodo bezpiecznego głównego urzędu certyfikacji
- Deutsche Telekom 
- Globalny główny urząd certyfikacji DigiCert
- Główny urząd certyfikacji EV DigiCert o wysokim poziomie zabezpieczeń
- Zobowiązanie
- GlobalSign
- Przejdź do Daddy
- GeoTrust
- VeriSign, Inc. 
- SSL.com
- Starfield
- Główny certyfikat firmy Symantec Enterprise Mobile dla firmy Microsoft 
- SwissSign
- Thawte sygnatura czasowa urzędu certyfikacji
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis

Firma Microsoft pracuje nad dodawaniem dodatkowych urzędów certyfikacji na podstawie żądań klientów. 

## <a name="sip-signaling-fqdns"></a>Sygnalizowanie SIP: nazwy FQDN 

Punkty połączenia dla interfejsu SIP usług komunikacyjnych to następujące trzy nazwy FQDN:

- **SIP.pstnhub.Microsoft.com** — globalna nazwa FQDN — najpierw należy podjąć próbę. Gdy usługa SBC wysyła żądanie rozpoznania tej nazwy, serwery DNS Microsoft Azure zwracają adres IP wskazujący podstawowe centrum danych platformy Azure przypisane do usługi SBC. Przypisanie jest oparte na metrykach wydajności centrów danych i geograficznie sąsiedztwu do SBC. Zwrócony adres IP odpowiada podstawowej nazwie FQDN.
- **sip2.pstnhub.Microsoft.com** — pomocnicza nazwa FQDN — geograficznie mapuje do drugiego regionu priorytetu.
- **sip3.pstnhub.Microsoft.com** — priorytetowa nazwa FQDN — geograficznie mapuje do regionu trzeciego priorytetu.

Umieszczenie tych trzech nazw FQDN jest wymagane w celu:

- Zapewnij optymalne środowisko (mniej załadowane i najbliższe centrum danych SBC przypisane przez zapytanie do pierwszej nazwy FQDN).
- Przełączenie w tryb failover, gdy połączenie z centrum SBC zostanie utworzone do centrów danych, w których występuje tymczasowy problem. Aby uzyskać więcej informacji, zobacz [mechanizm trybu failover](#failover-mechanism-for-sip-signaling) poniżej.  

Nazwy FQDN — sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com i sip3.pstnhub.microsoft.com — zostaną rozpoznane jako jeden z następujących adresów IP:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Otwórz porty zapory dla tych adresów IP, aby zezwalać na ruch przychodzący i wychodzący do i z adresów na potrzeby sygnalizowania. Jeśli zapora obsługuje nazwy DNS, nazwa FQDN jest `sip-all.pstnhub.microsoft.com` rozpoznawana jako wszystkie te adresy IP. 

## <a name="sip-signaling-ports"></a>Sygnalizowanie SIP: porty

Użyj następujących portów dla interfejsu SIP usług komunikacyjnych:

|Ruch|Źródło|Działanie|Port źródłowy|Port docelowy|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|Serwer proxy SIP|BAJT|1024 – 65535|Zdefiniowano na potrzeby usługi SBC (w przypadku pakietu Office 365 w serwisie Microsoft w zatoce (w przypadku portu 5061 o wysokim/DoD)|
SIP/TLS|BAJT|Serwer proxy SIP|Zdefiniowane na serwerze SBC|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Mechanizm trybu failover dla sygnalizacji SIP

Usługa SBC wykonuje zapytanie DNS w celu rozwiązania sip.pstnhub.microsoft.com. Na podstawie lokalizacji SBC i metryki wydajności centrum danych jest wybierane główne centrum danych. Jeśli główne centrum danych napotyka problem, wypróbuje on sip2.pstnhub.microsoft.com, który jest rozpoznawany jako drugi przypisany centrum danych, i w rzadkich przypadkach, gdy centra danych w dwóch regionach nie są dostępne, a w przypadku niedostępności ostatnich nazw FQDN (sip3.pstnhub.microsoft.com), które udostępniają adres IP centrum danych.

## <a name="media-traffic-ip-and-port-ranges"></a>Ruch multimedialny: zakresy adresów IP i portów

Ruch związany z multimediami jest przenoszony do i z osobnej usługi nazywanej procesorem multimediów. W momencie publikowania procesora multimediów dla usług ACS może być używany dowolny adres IP platformy Azure. Pobierz [pełną listę adresów](https://www.microsoft.com/download/details.aspx?id=56519).

### <a name="port-range"></a>Zakres portów
Zakres portów procesorów multimediów przedstawiono w poniższej tabeli: 

|Ruch|Źródło|Działanie|Port źródłowy|Port docelowy|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Procesor multimediów|BAJT|3478-3481 i 49152 – 53247|Zdefiniowane na serwerze SBC|
|UDP/SRTP|BAJT|Procesor multimediów|Zdefiniowane na serwerze SBC|3478-3481 i 49152 – 53247|

  > [!NOTE]
  > Firma Microsoft zaleca co najmniej dwa porty dla każdego współbieżnego wywołania na serwerze SBC.


## <a name="media-traffic-media-processors-geography"></a>Ruch związany z multimediami: dane geograficzne procesorów multimedialnych

Ruch związany z multimediami odbywa się za pośrednictwem składników nazywanych procesorami nośników. Procesory multimediów są umieszczane w tych samych centrach danych co serwery proxy SIP. Ponadto dostępne są dodatkowe procesory multimedialne pozwalające zoptymalizować przepływ multimediów. Na przykład firma Microsoft nie ma teraz składnika proxy SIP w Australii (SIP Flows przez Singapur lub Hongkong SAR), ale oferujemy procesor multimediów lokalnie w Australii. Konieczność lokalnego przetworzenia procesorów multimedialnych zależy od opóźnień, które zapewnia, że ruch jest długi, na przykład od Australii do Singapuru lub Hongkong SAR. Podczas gdy opóźnienie w przykładzie ruchu pochodzącego z Australii do Hongkong SAR lub Singapur jest akceptowalne, można zachować dobrą jakość wywołań dla ruchu SIP, w przypadku ruchu multimedialnego w czasie rzeczywistym.

Lokalizacje, w których wdrożono zarówno serwer proxy SIP, jak i składniki procesora multimediów:
- US (dwa w zachodnich stanach USA i regionach USA)
- Europa (Amsterdam i Dublin centra danych)
- Azja (Singapur i Hongkong SAR)
- Australia (AU wschód i Południowo-środkowe centra danych)

Lokalizacje, w których wdrożono tylko procesory multimedialne (przepływy SIP przez najbliższe centrum danych wymienione powyżej):
- Japonia (JP wschód i zachodnie centra danych)


## <a name="media-traffic-codecs"></a>Ruch związany z multimediami: kodeki

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Rozgałęzienie między procesorem usługi SBC, a klientem usługi Microsoft Teams.
Dotyczy zarówno przypadków obejścia nośnika, jak i braku obejścia.

Interfejs routingu bezpośredniego na etapie rozgałęzienia między kontrolerem obramowania sesji a procesorem nośników w chmurze może używać następujących koderów-dekoder:

- JEDWAB, G. 711, G. 722, G. 729

Można wymusić użycie określonego kodeka na kontrolerze granicy sesji, wykluczając niepożądane kodery-dekoder z oferty.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Etap między aplikacją zestawu ACS SDK a procesorem multimediów w chmurze

Na etapie rozgałęzienia między procesorem multimediów w chmurze i aplikacją zestawu ACS SDK jest używana wartość jedwab lub G. 722. Wybór kodera-dekoder na tym etapie opiera się na algorytmach firmy Microsoft, które uwzględniają wiele parametrów. 

## <a name="supported-session-border-controllers-sbcs"></a>Obsługiwane kontrolery przygraniczne sesji (SBCs)

Certyfikacja jest w toku. W tym czasie klienci mogą korzystać z [kontrolerów granic sesji certyfikowanych przez zespoły](/MicrosoftTeams/direct-routing-border-controllers). 

## <a name="next-steps"></a>Następne kroki

### <a name="conceptual-documentation"></a>Dokumentacja dotycząca pojęć

- [Koncepcja telefonii](./telephony-concept.md)
- [Typy numerów telefonów w usłudze Azure Communications Services](./plan-solution.md)
- [Cennik](../pricing.md)

### <a name="quickstarts"></a>Przewodniki Szybki start

- [Wywołanie telefonu](../../quickstarts/voice-video-calling/pstn-call.md)