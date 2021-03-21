---
title: Informacje o schemacie normalizacji danych wskaźnikowych platformy Azure | Microsoft Docs
description: Ten artykuł zawiera schemat normalizacji danych wskaźnikowych platformy Azure.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 4cd97aef5d8c959aeb2e0314e051790fd0421585
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806939"
---
# <a name="azure-sentinel-data-normalization-schema-reference"></a>Dokumentacja schematu normalizacji danych wskaźnikowych platformy Azure

## <a name="terminology"></a>Terminologia

W schematach wskaźnikowych platformy Azure jest używana następująca terminologia:

| Okres | Definicja |
| ---- | ---------- |
| Urządzenie raportujące | System wysyła rekordy do kontrolki wskaźnikowej platformy Azure. Może to nie być system podmiotu rekordu. |
| Rekord | Jednostka danych wysyłana z urządzenia raportowania. Jest to często określane jako "log", "Event" lub "Alert", ale nie musi być jednym z nich. |
|

## <a name="data-types-and-formats"></a>Typy i formaty danych

Wartości powinny być znormalizowane zgodnie z poniższymi wskazówkami. Jest to wymagane w przypadku znormalizowanych pól i zalecane dla innych pól. 

| Typ danych | Typ fizyczny | Format i wartość |
| --------- | ------------- | ---------------- |
| **Data/godzina** | W zależności od użycia możliwości metody pozyskiwania w kolejności malejącej:<ul><li>Log Analytics wbudowany typ DateTime</li><li>Pole typu Integer używające reprezentacji liczbowej Log Analytics DateTime</li><li>Pole ciągu wykorzystujące reprezentację liczbową Log Analytics DateTime</li></ul> | Log Analytics reprezentacja DateTime. <br></br>Log Analytics Data & godzina reprezentacji jest podobna w rodzaju, ale różni się od systemu UNIX reprezentacja czasu. Zapoznaj się z tymi wskazówkami dotyczącymi konwersji. <br></br>Data & czas powinien być dostosowany do strefy czasowej. |
| **Adres MAC** | Ciąg | Notacja Colon-Hexadecimal |
| **Adres IP** | Adres IP | Schemat nie ma oddzielnych adresów IPv4 i IPv6. Dowolne pole adresu IP może zawierać adres IPv4 lub IPv6:<ul><li>IPv4 w notacji dziesiętnej</li><li>Protokół IPv6 w notacji 8 hextets, który umożliwia korzystanie z krótkich formularzy opisanych tutaj.</li></ul> |
| **Użytkownik** | Ciąg | Dostępne są następujące 3 pola użytkownika:<ul><li>Nazwa użytkownika</li><li>Nazwa UPN użytkownika</li><li>Domena użytkownika</li></ul> |
| **Identyfikator użytkownika** | Ciąg | Następujące 2 identyfikatory użytkowników są obecnie obsługiwane:<ul><li>Identyfikator SID użytkownika</li><li>Identyfikator usługi Azure Active Directory</li></ul> |
| **Urządzenie** | Ciąg | Obsługiwane są następujące 3 kolumny urządzenia/hosta:<ul><li>ID (Identyfikator)</li><li>Nazwa</li><li>W pełni kwalifikowana nazwa domeny (FQDN)</li></ul> |
| **Kraj** | Ciąg | Ciąg używający normy ISO 3166-1 zgodnie z tym priorytetem:<ul><li>Kody Alpha-2 (tj. US dla Stany Zjednoczone)</li><li>Kody Alfa-3 (tj. USA dla Stany Zjednoczone)</li><li>Krótka nazwa</li></ul> |
| **Region** | Ciąg | Nazwa podpodziału kraju z zastosowaniem ISO 3166-2 |
| **City (Miasto)** | Ciąg | |
| **Długość geograficzna** | Double | Reprezentacja współrzędnych ISO 6709 (ze znakiem dziesiętnym) |
| **Rodziny** | Double | Reprezentacja współrzędnych ISO 6709 (ze znakiem dziesiętnym) |
| **Algorytm wyznaczania wartości skrótu** | Ciąg | Obsługiwane są następujące 4 kolumny skrótów:<ul><li>MD5</li><li>SHA1</li><li>SHA256</li><li>SHA512</li></ul> |
| **Typ pliku** | Ciąg | Typ typu pliku:<ul><li>Wewnętrzny</li><li>Klasa</li><li>Nazwanytype</li></ul> |
| 

## <a name="network-sessions-table-schema"></a>Schemat tabeli sesji sieciowych

Poniżej znajduje się schemat tabeli sesji sieciowych, w wersji 1.0.0

| Nazwa pola | Typ wartości | Przykład | Opis | Skojarzone jednostki OSSEM |
|-|-|-|-|-|
| Typ zdarzenia | Ciąg | Ruch | Typ zbieranych zdarzeń | Zdarzenie |
| EventSubType | Ciąg | Authentication | Dodatkowy opis typu, jeśli dotyczy | Zdarzenie |
| EventCount | Liczba całkowita  | 10 | Liczba zagregowanych zdarzeń, jeśli ma zastosowanie. | Zdarzenie |
| EventEndTime | Data/godzina | Zobacz "typy danych" | Czas zakończenia zdarzenia | Zdarzenie |
| EventMessage | ciąg |  Odmowa dostępu | Ogólny komunikat lub opis zawarty w lub wygenerowany z rekordu | Zdarzenie |
| DvcIpAddr | Adres IP |  23.21.23.34 | Adres IP urządzenia generującego rekord | Pliku<br>Adres IP |
| DvcMacAddr | Ciąg | 06:9F: EB: 8F: 14 | Adres MAC interfejsu sieciowego urządzenia raportowania, z którego wysłano zdarzenie. | Pliku<br>Mac |
| DvcHostname | Nazwa urządzenia (ciąg) | syslogserver1.contoso.com | Nazwa urządzenia urządzenia generującego komunikat. | Urządzenie |
| EventProduct | Ciąg | OfficeSharepoint | Produkt generujący zdarzenie. | Zdarzenie |
| EventProductVersion | ciąg | 9.0 |  Wersja produktu generującego zdarzenie. | Zdarzenie |
| EventResourceId | Identyfikator urządzenia (ciąg) | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | Identyfikator zasobu urządzenia generującego komunikat. | Zdarzenie |
| EventReportUrl | Ciąg | https://192.168.1.1/repoerts/ae3-56.htm | Link do pełnego raportu utworzonego przez urządzenie raportujące | Zdarzenie |
| EventVendor | Ciąg | Microsoft | Dostawca produktu generującego zdarzenie. | Zdarzenie |
| EventResult | Z wieloma wartościami: sukces, częściowy, Niepowodzenie, [pusty] (ciąg) | Powodzenie | Wynik raportowany dla działania. Wartość pusta, gdy nie ma zastosowania. | Zdarzenie |
| EventResultDetails | Ciąg | Nieprawidłowe hasło | Przyczyna lub szczegóły wyniku zgłoszonego w EventResult | Zdarzenie |
| EventSchemaVersion | Rzeczywiste | 0.1 | Wersja schematu wskaźnikowego platformy Azure. Obecnie 0,1. | Zdarzenie |
| EventSeverity | Ciąg | Niski | Jeśli zgłoszone działanie ma wpływ na bezpieczeństwo, oznacza to, że ważność tego wpływu jest istotna. | Zdarzenie |
| EventOriginalUid | Ciąg | af6ae8fe-ff43-4a4c-b537-8635976a2b51 | Identyfikator rekordu na urządzeniu sprawozdawczym. | Zdarzenie |
| EventStartTime | Data/godzina | Zobacz "typy danych" | Czas, w którym podano zdarzenie | Zdarzenie |
| TimeGenerated | Data/godzina | Zobacz "typy danych" | Czas wystąpienia zdarzenia (zgłoszone przez źródło raportu). | Pole niestandardowe |
| EventTimeIngested | Data/godzina | Zobacz "typy danych" | Czas pozyskania zdarzenia na platformie Azure. Zostanie dodany przez wskaźnik na platformie Azure. | Zdarzenie |
| EventUid | Identyfikator GUID (ciąg) | 516a64e3-8360-4f1e-a67c-d96b3d52df54 | Unikatowy identyfikator używany przez wskaźnik platformy Azure do oznaczania wiersza. | Zdarzenie |
| NetworkApplicationProtocol | Ciąg | HTTPS | Protokół warstwy aplikacji używany przez połączenie lub sesję. | Sieć |
| DstBytes | int | 32455 | Liczba bajtów wysłanych z lokalizacji docelowej do źródła dla połączenia lub sesji. | Element docelowy |
| SrcBytes | int | 46536 | Liczba bajtów wysłanych ze źródła do miejsca docelowego dla połączenia lub sesji. | Element źródłowy |
| NetworkBytes | int | 78991 | Liczba bajtów wysłanych w obu kierunkach. Jeśli istnieją zarówno BytesReceived, jak i BytesSent, BytesTotal powinny być równe ich sumie. | Sieć |
| NetworkDirection | Wiele wartości: przychodzące, wychodzące (ciąg) | Przychodzący | Kierunek połączenia lub sesji, do lub z organizacji. | Sieć |
| DstGeoCity | Ciąg | Burlington | Miasto skojarzone z docelowym adresem IP | Punktu<br>Obszar geograficzny |
| DstGeoCountry | Country (ciąg) | USA | Kraj skojarzony ze źródłowym adresem IP | Punktu<br>Obszar geograficzny |
| DstDvcHostname | Nazwa urządzenia (ciąg) |  victim_pc | Nazwa urządzenia docelowego | Element docelowy<br>Urządzenie |
| DstDvcFqdn | Ciąg | victim_pc. contoso. Local | W pełni kwalifikowana nazwa domeny hosta, na którym został utworzony dziennik | Punktu<br>Urządzenie |
| DstDomainHostname | ciąg | CONTOSO | Domena docelowa, domena hosta docelowego (witryna sieci Web, nazwa domeny itp.), na przykład wyszukiwania DNS lub wyszukiwania NS | Element docelowy |
| DstInterfaceName | ciąg | Karta sieciowa Microsoft Hyper-V | Interfejs sieciowy używany do połączenia lub sesji przez urządzenie docelowe. | Element docelowy |
| DstInterfaceGuid | ciąg | 2BB33827-6BB6-48DB-8DE6-DB9E0B9F9C9B | Identyfikator GUID interfejsu sieciowego, który został użyty w żądaniu uwierzytelniania  | Element docelowy |
| DstIpAddr | Adres IP | 2001: db8:: FF00:42:8329 | Adres IP połączenia lub miejsca docelowego sesji, najczęściej określany jako docelowy adres IP w pakiecie sieciowym | Punktu<br>Adres IP |
| DstDvcIpAddr | Adres IP | 75.22.12.2 | Docelowy adres IP urządzenia, które nie jest bezpośrednio skojarzone z pakietem sieciowym | Punktu<br>Pliku<br>Adres IP
| DstGeoLatitude | Szerokość geograficzna (Double) | 44,475833 | Szerokość geograficzna współrzędnej geograficznej skojarzonej z docelowym adresem IP | Punktu<br>Obszar geograficzny |
| DstMacAddr | Ciąg | 06:9F: EB: 8F: 14 | Adres MAC interfejsu sieciowego, na którym nawiązane zostało połączenie lub sesja, najczęściej określany jako docelowy adres MAC w pakiecie sieciowym | Punktu<br>Komputery |
| DstDvcMacAddr | Ciąg | 06:9F: EB: 8F: 14 | Docelowy adres MAC urządzenia, które nie jest bezpośrednio skojarzone z pakietem sieciowym. | Punktu<br>Pliku<br>Komputery |
| DstDvcDomain | Ciąg | CONTOSO | Domena urządzenia docelowego. | Punktu<br>Urządzenie |
| DstPortNumber | Liczba całkowita | 443 | Docelowy port IP. | Punktu<br>Port |
| DstGeoRegion | Region (ciąg) | Vermont | Region w kraju skojarzony z docelowym adresem IP | Punktu<br>Obszar geograficzny |
| DstResourceId | Identyfikator urządzenia (ciąg) |  /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /victim | Identyfikator zasobu urządzenia docelowego. | Element docelowy |
| DstNatIpAddr | Adres IP | 2::1 | W przypadku zgłoszenia przez pośredniczące urządzenie NAT, takie jak zapora, adres IP używany przez urządzenie NAT do komunikacji ze źródłem. | Docelowy translator adresów sieciowych<br>Adres IP |
| DstNatPortNumber | int | 443 | Jeśli zgłoszono przez pośredniczące urządzenie NAT, takie jak zapora, port używany przez urządzenie NAT do komunikacji ze źródłem. | Docelowy translator adresów sieciowych<br>Port |
| DstUserSid | Identyfikator SID użytkownika |  S-12-1445 | Identyfikator użytkownika tożsamości skojarzonej z lokalizacją docelową sesji. Zazwyczaj tożsamość użyta do uwierzytelnienia serwera. Aby uzyskać szczegółowe informacje, zobacz "typy danych". | Punktu<br>Użytkownik |
| DstUserAadId | Ciąg (GUID) | ae92b0b4-cfba-4b42-85a0-fbd862f4df54 | Identyfikator obiektu konta usługi Azure AD użytkownika na końcu sesji | Punktu<br>Użytkownik |
| DstUserName | Nazwa użytkownika (ciąg) | Jan | Nazwa użytkownika tożsamości skojarzonej z lokalizacją docelową sesji.  | Punktu<br>Użytkownik |
| DstUserUpn | ciąg | johnd@anon.com | Nazwa UPN tożsamości skojarzonej z lokalizacją docelową sesji. | Punktu<br>Użytkownik |
| DstUserDomain | ciąg | GRUPY roboczej | Nazwa domeny lub komputera konta w miejscu docelowym sesji | Punktu<br>Użytkownik |
| DstZone | Ciąg | DMZ | Strefa sieciowa miejsca docelowego, zgodnie z definicją w urządzeniu sprawozdawczym. | Element docelowy |
| DstGeoLongitude | Długość geograficzna (Double) | -73,211944 | Długość geograficzna współrzędnej geograficznej skojarzonej z docelowym adresem IP | Punktu<br>Obszar geograficzny |
| DvcAction | Wiele wartości: Allow, Deny, Drop (String) | Zezwalaj | Jeśli zostało zgłoszone przez urządzenie pośredniczące, takie jak zapora, Akcja podejmowana przez urządzenie. | Urządzenie |
| DvcInboundInterface | Ciąg | eth0 | Jeśli zostało zgłoszone przez urządzenie pośredniczące, takie jak zapora, interfejs sieciowy używany przez niego do połączenia z urządzeniem źródłowym. | Urządzenie |
| DvcOutboundInterface | Ciąg  | Ethernet adapter Ethernet 4 | Jeśli zostało zgłoszone przez urządzenie pośredniczące, takie jak zapora, interfejs sieciowy używany przez niego do połączenia z urządzeniem docelowym. | Urządzenie |
| NetworkDuration | Liczba całkowita | 1500 | Czas (w milisekundach) kończenia sesji lub połączenia sieciowego | Sieć |
| NetworkIcmpCode | Liczba całkowita | 34 | W przypadku komunikatu ICMP wpisz wartość liczbową typ komunikatu ICMP (RFC 2780 lub RFC 4443). | Sieć |
| NetworkIcmpType | Ciąg | Miejsce docelowe jest nieosiągalne | Dla komunikatu ICMP, Reprezentacja tekstowa typu komunikatu ICMP (RFC 2780 lub RFC 4443). | Sieć |
| DstPackets | int  | 446 | Liczba pakietów wysłanych z lokalizacji docelowej do źródła dla połączenia lub sesji. Znaczenie pakietu jest definiowane przez urządzenie do raportowania. | Element docelowy |
| SrcPackets | int  | 6478 | Liczba pakietów wysłanych ze źródła do miejsca docelowego dla połączenia lub sesji. Znaczenie pakietu jest definiowane przez urządzenie do raportowania. | Element źródłowy |
| NetworkPackets | int  | 0 | Liczba pakietów wysłanych w obu kierunkach. Jeśli istnieją zarówno PacketsReceived, jak i PacketsSent, BytesTotal powinny być równe ich sumie. | Sieć |
| HttpRequestTime | Liczba całkowita | 700 | Ilość czasu, jaką zajęło wysłanie żądania do serwera, jeśli ma to zastosowanie. | HTTP |
| HttpResponseTime | Liczba całkowita | 800 | Ilość czasu, jaką zajęło odebranie odpowiedzi na serwerze, jeśli ma to zastosowanie. | HTTP |
| NetworkRuleName | Ciąg | AnyAnyDrop | Nazwa lub identyfikator reguły, na podstawie której podjęto decyzję DeviceAction | Sieć |
| NetworkRuleNumber | int |  23 | Numer dopasowanej reguły  | Sieć |
| NetworkSessionId | ciąg | 172_12_53_32_4322__123_64_207_1_80 | Identyfikator sesji zgłoszony przez urządzenie raportujące. Na przykład identyfikator sesji P7 dla określonych aplikacji po uwierzytelnieniu | Sieć |
| SrcGeoCity | Ciąg | Burlington | Miasto skojarzone ze źródłowym adresem IP | Zewnętrz<br>Obszar geograficzny |
| SrcGeoCountry | Country (ciąg) | USA | Kraj skojarzony ze źródłowym adresem IP | Zewnętrz<br>Obszar geograficzny |
| SrcDvcHostname | Nazwa urządzenia (ciąg) |  villain | Nazwa urządzenia źródłowego | Zewnętrz<br>Urządzenie |
| SrcDvcFqdn | ciąg | Villain.malicious.com | W pełni kwalifikowana nazwa domeny hosta, na którym został utworzony dziennik | Zewnętrz<br>Urządzenie |
| SrcDvcDomain | ciąg | EVILORG | Domena urządzenia, z której została zainicjowana sesja | Zewnętrz<br>Urządzenie |
| SrcDvcOs | Ciąg | iOS | System operacyjny urządzenia źródłowego | Zewnętrz<br>Urządzenie |
| SrcDvcModelName | Ciąg | Notatka Samsung Galaxy | Nazwa modelu urządzenia źródłowego | Zewnętrz<br>Urządzenie |
| SrcDvcModelNumber | Ciąg | 10,0 | Numer modelu urządzenia źródłowego | Zewnętrz<br>Urządzenie |
| SrcDvcType | Ciąg | Aplikacje mobilne | Typ urządzenia źródłowego | Zewnętrz<br> Urządzenie |
| SrcIntefaceName | Ciąg | eth01 | Interfejs sieciowy używany dla połączenia lub sesji przez urządzenie źródłowe. | Element źródłowy |
| SrcInterfaceGuid | Ciąg | 46ad544b-eaf0-47ef-827c-266030f545a6 | Identyfikator GUID używanego interfejsu sieciowego | Element źródłowy |
| SrcIpAddr | Adres IP | 77.138.103.108 | Adres IP, z którego pochodzi połączenie lub sesja. | Zewnętrz<br>Adres IP |
| SrcDvcIpAddr | Adres IP | 77.138.103.108 | Źródłowy adres IP urządzenia, które nie jest bezpośrednio skojarzone z pakietem sieciowym (zbierane przez dostawcę lub jawnie obliczone). | Zewnętrz<br>Pliku<br>Adres IP |
| SrcGeoLatitude | Szerokość geograficzna (Double) | 44,475833 | Szerokość geograficzna współrzędnej geograficznej skojarzonej ze źródłowym adresem IP | Zewnętrz<br>Obszar geograficzny |
| SrcGeoLongitude | Długość geograficzna (Double) | -73,211944 | Długość geograficzna współrzędnej geograficznej skojarzonej ze źródłowym adresem IP | Zewnętrz<br>Obszar geograficzny |
| SrcMacAddr | Ciąg | 06:9F: EB: 8F: 14 | Adres MAC interfejsu sieciowego, z którego pochodzi sesja połączenia od. | Zewnętrz<br>Mac |
| SrcDvcMacAddr | Ciąg | 06:9F: EB: 8F: 14 | Źródłowy adres MAC urządzenia, które nie jest bezpośrednio skojarzone z pakietem sieciowym. | Zewnętrz<br>Pliku<br>Mac |
| SrcPortNumber | Liczba całkowita | 2335 | Port IP, z którego pochodzi połączenie. Może nie być istotne w przypadku sesji składającej się z wielu połączeń. | Zewnętrz<br>Port |
| SrcGeoRegion | Region (ciąg) | Vermont | Region w kraju skojarzony ze źródłowym adresem IP | Zewnętrz<br>Obszar geograficzny |
| SrcResourceId | Ciąg | /subscriptions/3c1bb38c-82e3-4f8d-a115-a7110ba70d05 /resourcegroups/contoso77/providers /microsoft.compute/virtualmachines /syslogserver1 | Identyfikator zasobu urządzenia generującego komunikat. | Element źródłowy |
| SrcNatIpAddr | Adres IP | 4.3.2.1 | W przypadku zgłoszenia przez pośredniczące urządzenie NAT, takie jak zapora, adres IP używany przez urządzenie NAT do komunikacji z miejscem docelowym. | Źródłowy translator adresów sieciowych,<br>Adres IP |
| SrcNatPortNumber | Liczba całkowita | 345 | Jeśli zgłoszono przez pośredniczące urządzenie NAT, takie jak zapora, port używany przez urządzenie NAT do komunikacji z miejscem docelowym. | Źródłowy translator adresów sieciowych,<br>Port |
| SrcUserSid | Identyfikator użytkownika (ciąg) | S-15-1445 | Identyfikator użytkownika tożsamości skojarzonej ze źródłem sesji. Zazwyczaj użytkownik wykonuje akcję na kliencie. Aby uzyskać szczegółowe informacje, zobacz "typy danych". | Zewnętrz<br>Użytkownik |
| SrcUserAadId | Ciąg (GUID) | 16c8752c-7dd2-4cad-9e03-fb5d1cee5477 | Identyfikator obiektu konta usługi Azure AD użytkownika na końcu sesji | Zewnętrz<br>Użytkownik |
| SrcUserName | Nazwa użytkownika (ciąg) | wiadomość | Nazwa użytkownika tożsamości skojarzonej ze źródłem sesji. Zazwyczaj użytkownik wykonuje akcję na kliencie. Aby uzyskać szczegółowe informacje, zobacz "typy danych". | Element źródłowy<br>Użytkownik |
| SrcUserUpn | ciąg | bob@alice.com | Nazwa UPN konta inicjującego sesję | Zewnętrz<br>Użytkownik |
| SrcUserDomain | ciąg | KOMPUTERÓW | Domena konta inicjującego sesję | Zewnętrz<br>Użytkownik |
| SrcZone | Ciąg | Naciskanie | Strefa sieciowa źródła, zgodnie z definicją w urządzeniu sprawozdawczym. | Element źródłowy |
| NetworkProtocol | Ciąg | TCP | Protokół IP używany przez połączenie lub sesję. Zwykle TCP, UDP lub ICMP | Sieć |
| CloudAppName | Ciąg | Facebook | Nazwa aplikacji docelowej dla aplikacji HTTP identyfikowanej przez serwer proxy. | Chmura |
| CloudAppId | Ciąg | 124 | Identyfikator aplikacji docelowej dla aplikacji HTTP identyfikowanej przez serwer proxy. Ta wartość jest zwykle specyficzna dla używanego serwera proxy. | Chmura |
| CloudAppOperation | Ciąg | DeleteFile | Operacja wykonywana przez użytkownika w kontekście aplikacji docelowej dla aplikacji HTTP identyfikowanej przez serwer proxy. Ta wartość jest zwykle specyficzna dla używanego serwera proxy. | Chmura |
| CloudAppRiskLevel | Ciąg | 3 | Poziom ryzyka skojarzony z aplikacją HTTP identyfikowaną przez serwer proxy. Ta wartość jest zwykle specyficzna dla używanego serwera proxy. | Chmura |
| Nazwa pliku | Ciąg | ImNotMalicious.exe | Nazwa pliku przesyłana przez połączenia sieciowe dla protokołów takich jak FTP i HTTP, które zawierają informacje o nazwie pliku. | Plik |
| Parametr | Ciąg | C:\Malicious\ImNotMalicious.exe | Pełna ścieżka, w tym nazwa pliku, pliku | Plik |
| FileHashMd5 | Ciąg | 51BC68715FC7C109DCEA406B42D9D78F | Wartość skrótu MD5 pliku przesyłanego za pośrednictwem połączeń sieciowych dla protokołów. | Plik |
| FileHashSha1 | Ciąg | 491AE3... C299821476F4 | Wartość skrótu SHA1 pliku przesyłanego za pośrednictwem połączeń sieciowych dla protokołów. | Plik |
| FileHashSha256 | Ciąg | 9B8F8EDB... C129976F03 | Wartość skrótu SHA256 pliku przesyłanego za pośrednictwem połączeń sieciowych dla protokołów. | Plik |
| FileHashSha512 | Ciąg | 5E127D... F69F73F01F361 | Wartość skrótu SHA512 pliku przesyłanego za pośrednictwem połączeń sieciowych dla protokołów. | Plik |
| FileExtension |  Ciąg | exe | Typ pliku przesyłanego za pośrednictwem połączeń sieciowych dla protokołów takich jak FTP i HTTP. | Plik
| FileMimeType | Ciąg | Aplikacja/MSWord | Typ MIME pliku przesyłanego za pośrednictwem połączeń sieciowych dla protokołów takich jak FTP i HTTP | Plik |
| Rozmiar pliku | Liczba całkowita | 23500 | Rozmiar pliku (w bajtach) przesyłanego przez połączenia sieciowe dla protokołów. | Plik |
| HttpVersion | Ciąg | 2.0 | Wersja żądania HTTP dla połączeń sieciowych HTTP/HTTPS. | HTTP |
| HttpRequestMethod | Ciąg | GET | Metoda HTTP dla sesji sieci HTTP/HTTPS. | HTTP |
| HttpStatusCode | Ciąg | 404 | Kod stanu HTTP dla sesji sieci HTTP/HTTPS. | HTTP |
| HttpContentType | Ciąg | wieloczęściowe/formularz-dane; granica = coś | Nagłówek typu zawartości odpowiedzi HTTP dla sesji sieci HTTP/HTTPS. | HTTP |
| HttpReferrerOriginal | Ciąg | https://developer.mozilla.org/en-US/docs/Web/JavaScript | Nagłówek odwołującego HTTP dla sesji sieci HTTP/HTTPS. | HTTP |
| HttpUserAgentOriginal | Ciąg | Mozilla/5.0 (Windows NT 10,0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.97 Safari/537.36 | Nagłówek agenta użytkownika protokołu HTTP dla sesji sieci HTTP/HTTPS. | HTTP |
| HttpRequestXff | Ciąg | 120.12.41.1 | HTTP X-Forwarded-For dla sesji sieciowych HTTP/HTTPS. | HTTP |
| UrlCategory | Ciąg | Wyszukiwarki | Zdefiniowane grupowanie adresów URL (lub może być oparte na domenie w adresie URL) związanych z tym, co to jest (tj.: Dorosła, wiadomości, reklamy, zaparkowane domeny itp.) | url |
| UrlOriginal | Ciąg | https://contoso.com/fo/?k=v&q = u # f | Adres URL żądania HTTP dla sesji sieci HTTP/HTTPS. | Url |
| UrlHostname | Ciąg | contoso.com | Część domeny adresu URL żądania HTTP dla sesji sieci HTTP/HTTPS. | Url |
| ThreatCategory | Ciąg | Programami | Kategoria zagrożenia identyfikowana przez system zabezpieczeń, na przykład brama zabezpieczeń sieci Web dla adresów IP i skojarzona z tą sesją sieciową. | Zagrożenie |
| ThreatId | Ciąg | TR. 124 | Identyfikator zagrożenia identyfikowanego przez system zabezpieczeń, taki jak brama zabezpieczeń sieci Web dla adresów IP i skojarzony z tą sesją sieciową. | Zagrożenie |
| Threatname | Ciąg | Plik testowy EICAR | Nazwa zidentyfikowanego zagrożenia lub złośliwego oprogramowania | Zagrożenie |
| AdditionalFields | Dynamiczny (zbiór JSON) | {<br>Property1: "Val1",<br>Property2: "val2"<br>} | Gdy w schemacie nie ma odpowiedniej kolumny, dodatkowe pola mogą być przechowywane w zbiorze JSON.<br>W przypadku analizy czasu zapytania zaleca się, aby nie używać tej metody, ponieważ dane pakowania do formatu JSON obniżają wydajność zapytań. Zamiast tego zaleca się podwyższenie poziomu dodatkowych kolumn.<br>W przypadku przyszłych scenariuszy analizy czasu pozyskiwania w tej kolumnie zbioru JSON zostaną zebrane dodatkowe dane. | Pole niestandardowe |
| 
