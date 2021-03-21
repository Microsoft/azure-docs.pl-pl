---
title: Informacje o typach jednostek wskaźnikowych platformy Azure | Microsoft Docs
description: W tym artykule przedstawiono typy jednostek wskaźnikowych platformy Azure i ich wymagane identyfikatory.
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102456307"
---
# <a name="azure-sentinel-entity-types-reference"></a>Informacje o typach jednostek wskaźnikowych platformy Azure

## <a name="entity-types-and-identifiers"></a>Typy jednostek i identyfikatory

W poniższej tabeli przedstawiono **typy jednostek** , które są obecnie dostępne do mapowania [w punkcie](map-data-fields-to-entities.md) kontrolnym platformy Azure, oraz **atrybuty** dostępne jako **identyfikatory** dla każdego typu jednostki, które są wyświetlane na liście rozwijanej **identyfikatory** w [Kreatorze reguły analizy](tutorial-detect-threats-custom.md).

Każdy z identyfikatorów w kolumnie **wymagane identyfikatory** jest minimalnym niezbędny do zidentyfikowania swojej jednostki. Jednak wymagany identyfikator może nie być, przez siebie, jest wystarczający, aby zapewnić *unikatową* identyfikację. Im więcej identyfikatorów jest używanych, tym większe prawdopodobieństwo unikatowej identyfikacji. Do mapowania pojedynczej jednostki można użyć maksymalnie trzech identyfikatorów.

Aby uzyskać najlepsze wyniki — w przypadku gwarantowanej unikatowej tożsamości — Jeśli to możliwe, należy używać identyfikatorów z kolumny **najsilniejszych identyfikatorów** . Użycie wielu silnych identyfikatorów umożliwia korelację między silnymi identyfikatorami z różnych źródeł danych i schematów. Dzięki temu można uzyskać dokładniejszy wgląd w szczegółowe dane dla danej jednostki.

| Typ jednostki | Identyfikatory | Wymagane identyfikatory | Silne identyfikatory |
| - | - | - | - |
| [**Konto użytkownika**](#user-account)<br>*Koncie* | Nazwa<br>Pełna nazwa<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>SID<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>Nazwa wyświetlana<br>ObjectGuid | Pełna nazwa<br>SID<br>Nazwa<br>AadUserId<br>PUID<br>ObjectGuid | Nazwa i NTDomain<br>Nazwa i UPNSuffix<br>AADUserId<br>SID |
| [**Host**](#host) | DnsDomain<br>NTDomain<br>HostName<br>Pełna nazwa<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | Pełna nazwa<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | Nazwa hosta + NTDomain<br>Nazwa hosta + DnsDomain<br>System Biosname + NTDomain<br>System Biosname + DnsDomain<br>AzureID<br>OMSAgentID |
| [**Adres IP**](#ip-address)<br>*PRZEGLĄD* | Adres | Adres | |
| [**Złośliwe oprogramowanie**](#malware) | Nazwa<br>Kategoria | Nazwa | |
| [**Plik**](#file) | Katalog<br>Nazwa | Nazwa | |
| [**Proces**](#process) | Identyfikator procesu<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>Identyfikator procesu | |
| [**Aplikacja w chmurze**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Nazwa<br>InstanceName | AppId<br>Nazwa | |
| [**Nazwa domeny**](#domain-name)<br>*USŁUGĄ* | DomainName | DomainName | |
| [**Zasób platformy Azure**](#azure-resource) | ResourceId | ResourceId | |
| [**Wartość skrótu pliku**](#file-hash)<br>*(FileHash)* | Algorytm<br>Wartość | Algorytm + wartość | |
| [**Klucz rejestru**](#registry-key) | Hive<br>Klucz | Hive<br>Klucz | Gałąź i klucz |
| [**Wartość rejestru**](#registry-value) | Nazwa<br>Wartość<br>ValueType | Nazwa | |
| [**Grupa zabezpieczeń**](#security-group) | DistinguishedName<br>Identyfikator SID<br>ObjectGuid | DistinguishedName<br>Identyfikator SID<br>ObjectGuid | |
| [**Adres URL**](#url) | Url | Url | |
| [**Urządzenie IoT**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Element źródłowy<br>SourceRef<br>Producent<br>Model<br>OperatingSystem<br>Adresu<br>MacAddress<br>Protokoły<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**Mailbox**](#mailbox) | MailboxPrimaryAddress<br>Nazwa wyświetlana<br>Głównej<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**Klaster poczty**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Zagrożenia<br>Zapytanie<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>Element źródłowy<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Zapytanie<br>Element źródłowy | Zapytanie + Źródło |
| [**Wiadomość e-mail**](#mail-message) | Adresat<br>Adresy<br>Zagrożenia<br>Nadawca<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>Temat<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Język<br>ThreatDetectionMethods | NetworkMessageId<br>Adresat | NetworkMessageId + odbiorca |
| [**Przesyłanie poczty**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Osoba przesyłająca<br>NetworkMessageId<br>Znacznik czasu<br>Adresat<br>Nadawca<br>SenderIp<br>Temat<br>ReportType | SubmissionId<br>NetworkMessageId<br>Adresat<br>Osoba przesyłająca |  |
|

## <a name="entity-type-schemas"></a>Schematy typów jednostek

Poniżej znajduje się bardziej szczegółowy sposób wyświetlania pełnych schematów poszczególnych typów jednostek. Należy zauważyć, że wiele z tych schematów zawiera linki do innych typów jednostek — na przykład schemat konta użytkownika zawiera łącze do typu jednostki hosta, ponieważ jeden atrybut konta użytkownika to host, na którym jest on zdefiniowany. Te połączone zewnętrznie jednostki nie mogą być używane jako identyfikatory mapowania jednostek, ale są bardzo przydatne w przypadku podawania kompletnego obrazu jednostek na stronach jednostki i grafie badania.

> [!NOTE]
> Znak zapytania po wartości w kolumnie **Typ** wskazuje, że pole dopuszcza wartość null.

## <a name="user-account"></a>Konto użytkownika

*Nazwa jednostki: konto*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | koncie |
| Nazwa | Ciąg | Nazwa konta. W tym polu powinna być przechowywana tylko nazwa bez dodanej domeny. |
| *FullName* | *NIE DOTYCZY* | *Nie jest częścią schematu, która jest dołączona do zgodności z poprzednimi wersjami z poprzednią wersją mapowania jednostek.*
| NTDomain | Ciąg | Nazwa domeny NETBIOS wyświetlana w formacie alertu — domena \ nazwa_użytkownika. Przykłady: Finanse, Urząd NT |
| DnsDomain | Ciąg | W pełni kwalifikowana nazwa DNS domeny. Przykłady: finance.contoso.com |
| UPNSuffix | Ciąg | Sufiks głównej nazwy użytkownika dla konta. W niektórych przypadkach jest to również nazwa domeny. Przykłady: contoso.com |
| Host | Jednostka | Host, który zawiera konto, jeśli jest kontem lokalnym. |
| SID | Ciąg | Identyfikator zabezpieczeń konta, na przykład S-1-5-18. |
| AadTenantId | Ident? | Identyfikator dzierżawy usługi Azure AD, jeśli jest znany. |
| AadUserId | Ident? | Identyfikator obiektu konta usługi Azure AD, jeśli jest znany. |
| PUID | Ident? | Identyfikator użytkownika paszportu usługi Azure AD, jeśli jest znany. |
| IsDomainJoined | Logiczna? | Określa, czy jest to konto domeny. |
| Nazwa wyświetlana | Ciąg | Nazwa wyświetlana konta. |
| ObjectGuid | Ident? | Atrybut objectGUID jest atrybutem jednowartościowym, który jest unikatowym identyfikatorem obiektu przypisanym przez Active Directory. |
|

Silne identyfikatory jednostki konta:

- Nazwa i UPNSuffix
- AadUserId
- Identyfikator SID i Host (wymagane w przypadku identyfikatorów SID wbudowanych kont)
- Identyfikator SID (z wyjątkiem identyfikatorów SID wbudowanych kont)
- Nazwa i NTDomain (chyba że NTDomain jest domeną wbudowaną, na przykład "Workgroup")
- Nazwa i Host (jeśli NTDomain jest domeną wbudowaną, na przykład "Workgroup")
- Nazwa i DnsDomain
- PUID
- ObjectGuid

Słabe identyfikatory jednostki konta:

- Nazwa

## <a name="host"></a>Host

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | "host" |
| DnsDomain | Ciąg | Domena DNS, do której należy ten host. Powinien zawierać pełny sufiks DNS dla domeny, jeśli jest znany. |
| NTDomain | Ciąg | Domena NT, do której należy ten host. |
| HostName | Ciąg | Nazwa hosta bez sufiksu domeny. |
| *FullName* | *NIE DOTYCZY* | *Nie jest częścią schematu, która jest dołączona do zgodności z poprzednimi wersjami z poprzednią wersją mapowania jednostek.*
| NetBiosName | Ciąg | Nazwa hosta (wcześniejsza niż Windows 2000). |
| IoTDevice | Jednostka | Jednostka urządzenia IoT (Jeśli ten host reprezentuje urządzenie IoT). |
| AzureID | Ciąg | Identyfikator zasobu platformy Azure dla maszyny wirtualnej, jeśli jest znany. |
| OMSAgentID | Ciąg | Identyfikator agenta pakietu OMS, jeśli na hoście jest zainstalowany agent pakietu OMS. |
| OSFamily | Podstawowe? | Jedna z następujących wartości: <li>Linux<li>Windows<li>Android<li>System iOS |
| OSVersion | Ciąg | Reprezentacja niezależna systemu operacyjnego.<br>To pole jest przeznaczone do przechowywania określonych wersji, które są bardziej szczegółowe niż Rodzina systemów operacyjnych, lub przyszłe wartości nieobsługiwane przez Wyliczenie Rodzina systemów operacyjnych. |
| IsDomainJoined | Wartość logiczna | Określa, czy ten host należy do domeny. |
|

Silne identyfikatory jednostki hosta:
- Nazwa hosta + NTDomain
- Nazwa hosta + DnsDomain
- System Biosname + NTDomain
- System Biosname + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (nieobsługiwane dla mapowania jednostek)

Słabe identyfikatory jednostki hosta:
- HostName
- NetBiosName

## <a name="ip-address"></a>Adres IP

*Nazwa jednostki: adres IP*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | przegląd |
| Adres | Ciąg | Adres IP jako ciąg, np. 127.0.0.1 (w protokole IPv4 lub IPv6). |
| Lokalizacja | Geolokalizacja | Kontekst lokalizacji geograficznej dołączonej do jednostki IP. |
|

Silne identyfikatory jednostki IP:
- Adres

## <a name="malware"></a>Złośliwe oprogramowanie

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | szkodliwe |
| Nazwa | Ciąg | Nazwa złośliwego oprogramowania dostawcy, na przykład `Win32/Toga!rfn` . |
| Kategoria | Ciąg | Kategoria złośliwego oprogramowania dostawcy, np. koń trojański. |
| Pliki | Lista\<Entity> | Lista połączonych obiektów plików, na których znaleziono złośliwe oprogramowanie. Może zawierać jednostki pliku wbudowane lub jako odwołanie.<br>Aby uzyskać dodatkowe informacje na temat struktury, zobacz temat jednostka pliku. |
| Procesy | Lista\<Entity> | Lista połączonych jednostek procesu, na których znaleziono złośliwe oprogramowanie. Jest to często używane, gdy alert zostanie wyzwolony dla działania bezplikowego.<br>Zobacz jednostkę [procesu](#process) , aby uzyskać dodatkowe szczegóły dotyczące struktury. |
|

Silne identyfikatory jednostki złośliwego oprogramowania:

- Nazwa i Kategoria

## <a name="file"></a>Plik

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | rozszerzeniem |
| Katalog | Ciąg | Pełna ścieżka do pliku. |
| Nazwa | Ciąg | Nazwa pliku bez ścieżki (Niektóre alerty mogą nie zawierać ścieżki). |
| Host | Jednostka | Host, na którym zapisano plik. |
| FileHashes | &lt;Jednostka listy&gt; | Skróty plików skojarzone z tym plikiem. |
|

Silne identyfikatory jednostki pliku:
- Nazwa + katalog
- Nazwa i FileHash
- Nazwa + katalog i FileHash

## <a name="process"></a>Proces

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | podstawowych |
| Identyfikator procesu | Ciąg | Identyfikator procesu. |
| CommandLine | Ciąg | Wiersz polecenia służący do tworzenia procesu. |
| ElevationToken | Podstawowe? | Token podniesienia uprawnień skojarzony z procesem.<br>Możliwe wartości:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | Datę? | Godzina rozpoczęcia uruchamiania procesu. |
| Obiekcie | Jednostka (plik) | Może zawierać jednostkę pliku w tekście lub jako odwołanie.<br>Aby uzyskać dodatkowe informacje na temat struktury, zobacz temat jednostka pliku. |
| Konto | Jednostka | Konto, na którym są uruchamiane procesy.<br>Może zawierać jednostkę [konta](#user-account) w tekście lub jako odwołanie.<br>Dodatkowe szczegóły dotyczące struktury można znaleźć w jednostce [konta](#user-account) . |
| ParentProcess | Jednostka (proces) | Jednostka procesu nadrzędnego. <br>Może zawierać częściowe dane, np. tylko identyfikator PID. |
| Host | Jednostka | Host, na którym uruchomiono proces. |
| LogonSession | Jednostka (HostLogonSession) | Sesja, w której uruchomiono proces. |
|

Silne identyfikatory jednostki procesu:

- Host + identyfikator procesu i CreationTimeUtc
- Host + ParentProcessId + CreationTimeUtc + CommandLine
- Host + ProcessId + CreationTimeUtc + ImageFile
- Host + ProcessId + CreationTimeUtc + ImageFile. FileHash

Słabe identyfikatory jednostki procesu:

- ProcessId + CreationTimeUtc + CommandLine (i brak hosta)
- ProcessId + CreationTimeUtc + ImageFile (i brak hosta)

## <a name="cloud-application"></a>Aplikacja w chmurze

*Nazwa jednostki: CloudApplication*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | "chmura-aplikacja" |
| AppId | int | Identyfikator techniczny aplikacji. Powinna to być jedna z wartości zdefiniowanych na liście [identyfikatorów aplikacji w chmurze](#cloud-application-identifiers). Wartość pola AppId jest opcjonalna. |
| Nazwa | Ciąg | Nazwa powiązanej aplikacji w chmurze. Wartość nazwy aplikacji jest opcjonalna. |
| InstanceName | Ciąg | Zdefiniowana przez użytkownika nazwa wystąpienia aplikacji w chmurze. Często jest używany do rozróżniania między kilkoma aplikacjami tego samego typu, które klient ma. |
|

Silne identyfikatory jednostki aplikacji w chmurze:
 - AppId (bez InstanceName)
 - Nazwa (bez InstanceName)
 - Identyfikator AppId + InstanceName
 - Nazwa + InstanceName

## <a name="domain-name"></a>Nazwa domeny

*Nazwa jednostki: DNS*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | usługą |
| DomainName | Ciąg | Nazwa rekordu DNS skojarzonego z alertem. |
| Adresu | Lista &lt; jednostek (IP)&gt; | Jednostki odpowiadające rozpoznanym adresom IP. |
| DnsServerIp | Jednostka (IP) | Jednostka reprezentująca serwer DNS rozpoznawania żądania. |
| HostIpAddress | Jednostka (IP) | Jednostka reprezentująca klienta żądania DNS. |
|

Silne identyfikatory jednostki DNS:
- Nazwa_domeny + DnsServerIp + HostIpAddress

Słabe identyfikatory jednostki DNS:
- Nazwa_domeny + HostIpAddress

## <a name="azure-resource"></a>Zasób platformy Azure

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | "Azure-Resource" |
| ResourceId | Ciąg | Identyfikator zasobu platformy Azure dla zasobu. |
| SubscriptionId | Ciąg | Identyfikator subskrypcji zasobu. |
| TryGetResourceGroup | Wartość logiczna | Wartość grupy zasobów, jeśli istnieje. |
| TryGetProvider | Wartość logiczna | Wartość dostawcy, jeśli istnieje. |
| TryGetName | Wartość logiczna | Wartość nazwy, jeśli istnieje. |
|

Silne identyfikatory jednostki zasobów platformy Azure:
- ResourceId

## <a name="file-hash"></a>Wartość skrótu pliku

*Nazwa jednostki: FileHash*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | 'filehash' |
| Algorytm | Wyliczenie | Typ algorytmu wyznaczania wartości skrótu. Możliwe wartości:<li>Nieznane<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Wartość | Ciąg | Wartość skrótu. |
|

Silne identyfikatory jednostki skrótu pliku:
- Algorytm + wartość

## <a name="registry-key"></a>Klucz rejestru

*Nazwa jednostki: RegistryKey*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | "Rejestr-klucz" |
| Hive | Podstawowe? | Jedna z następujących wartości:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Klucz | Ciąg | Ścieżka klucza rejestru. |
|

Silne identyfikatory jednostki klucza rejestru:
- Gałąź i klucz

## <a name="registry-value"></a>Wartość rejestru

*Nazwa jednostki: RegistryValue*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | "Registry-Value" |
| Klucz | Jednostka (RegistryKey) | Jednostka klucza rejestru. |
| Nazwa | Ciąg | Nazwa wartości rejestru. |
| Wartość | Ciąg | Reprezentacja danych wartości w formacie ciągu. |
| ValueType | Podstawowe? | Jedna z następujących wartości:<li>Ciąg<li>Binarne<li>DWord<li>Qword<li>FixedLength<li>ExpandString<li>Brak<li>Nieznane<br>Wartości powinny być zgodne z wyliczeniem Microsoft. Win32. RegistryValueKind. |
|

Silne identyfikatory jednostki wartości rejestru:
- Klucz + nazwa

Słabe identyfikatory jednostki wartości rejestru:
- Nazwa (bez klucza)

## <a name="security-group"></a>Grupa zabezpieczeń

*Nazwa jednostki: Security*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | "zabezpieczenia-Grupa" |
| DistinguishedName | Ciąg | Nazwa wyróżniająca grupy. |
| Identyfikator SID | Ciąg | Atrybut SID jest atrybutem jednowartościowym, który określa identyfikator zabezpieczeń (SID) grupy. |
| ObjectGuid | Ident? | Atrybut objectGUID jest atrybutem jednowartościowym, który jest unikatowym identyfikatorem obiektu przypisanym przez Active Directory. |
|

Silne identyfikatory jednostki grupy zabezpieczeń:
 - DistinguishedName
 - Identyfikator SID
 - ObjectGuid

## <a name="url"></a>Adres URL

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | adres URL |
| Url | Adresu | Pełny adres URL, do którego wskazuje jednostka. |
|

Silne identyfikatory jednostki adresu URL:
- Adres URL (gdy bezwzględny adres URL)

Słabe identyfikatory jednostki adresu URL:
- Adres URL (w przypadku względnego adresu URL)

## <a name="iot-device"></a>Urządzenie IoT

*Nazwa jednostki: IoTDevice*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | 'iotdevice' |
| IoTHub | Jednostka (AzureResource) | Jednostka AzureResource reprezentująca IoT Hub, do której należy urządzenie. |
| DeviceId | Ciąg | Identyfikator urządzenia w kontekście IoT Hub. |
| DeviceName | Ciąg | Przyjazna nazwa urządzenia. |
| IoTSecurityAgentId | Ident? | Identyfikator agenta usługi *Defender dla usługi IoT* działającego na urządzeniu. |
| DeviceType | Ciąg | Typ urządzenia ("czujnik temperatury", "zamrażarka", "Turbina wiatrowa" itp.). |
| Element źródłowy | Ciąg | Źródło (Firma Microsoft/dostawca) jednostki urządzenia. |
| SourceRef | Jednostka (adres URL) | Odwołanie do adresu URL elementu źródłowego, z którym urządzenie jest zarządzane. |
| Producent | Ciąg | Producent urządzenia. |
| Model | Ciąg | Model urządzenia. |
| OperatingSystem | Ciąg | System operacyjny, na którym działa urządzenie. |
| Adresu | Jednostka (IP) | Bieżący adres IP urządzenia. |
| MacAddress | Ciąg | Adres MAC urządzenia. |
| Protokoły | &lt;Ciąg listy&gt; | Lista protokołów obsługiwanych przez urządzenie. |
| SerialNumber | Ciąg | Numer seryjny urządzenia. |
|

Silne identyfikatory jednostki urządzenia IoT:
- IoTHub + DeviceId

Słabe identyfikatory jednostki urządzenia IoT:
- DeviceId (bez IoTHub)

## <a name="mailbox"></a>Mailbox

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | górn |
| MailboxPrimaryAddress | Ciąg | Adres podstawowy skrzynki pocztowej. |
| Nazwa wyświetlana | Ciąg | Nazwa wyświetlana skrzynki pocztowej. |
| Głównej | Ciąg | Nazwa UPN skrzynki pocztowej. |
| RiskLevel | Podstawowe? | Poziom ryzyka tej skrzynki pocztowej. Możliwe wartości:<li>Brak<li>Niski<li>Śred.<li>Wys. |
| ExternalDirectoryObjectId | Ident? | Identyfikator AzureAD skrzynki pocztowej. Podobnie jak w przypadku AadUserId w jednostce konta, ale ta właściwość jest specyficzna dla obiektu skrzynki pocztowej po stronie biura. |
|

Silne identyfikatory jednostki skrzynki pocztowej:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>Klaster poczty

*Nazwa jednostki: MailCluster*

> [!NOTE]
> **MDO**  =  Usługa **Microsoft Defender dla Office 365**, znana wcześniej jako Office 365 Advanced Threat Protection (O365 ATP).

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | "poczta-klaster" |
| NetworkMessageIds | &lt;Ciąg IList&gt; | Identyfikatory wiadomości e-mail, które są częścią klastra poczty. |
| CountByDeliveryStatus | &lt;Ciąg IDictionary, int&gt; | Liczba wiadomości e-mail według reprezentacji ciągu DeliveryStatus. |
| CountByThreatType | &lt;Ciąg IDictionary, int&gt; | Liczba wiadomości e-mail według reprezentacji ciągu "Threattype". |
| CountByProtectionStatus | &lt;Ciąg IDictionary, Long&gt; | Liczba wiadomości e-mail według stanu ochrony przed zagrożeniami. |
| Zagrożenia | &lt;Ciąg IList&gt; | Zagrożenia związane z wiadomościami e-mail, które są częścią klastra poczty. |
| Zapytanie | Ciąg | Zapytanie używane do identyfikowania komunikatów klastra poczty. |
| QueryTime | Datę? | Czas zapytania. |
| MailCount | ZAOKR? | Liczba wiadomości e-mail będących częścią klastra poczty e-mail. |
| IsVolumeAnomaly | Logiczna? | Określa, czy jest to klaster poczty z anomalią woluminu. |
| Element źródłowy | Ciąg | Źródłem klastra poczty (wartość domyślna to "O365 ATP"). |
| ClusterSourceIdentifier | Ciąg | Identyfikator komunikatu sieciowego poczty, która jest źródłem tego klastra poczty. |
| ClusterSourceType | Ciąg | Typ źródła klastra poczty. To mapowanie do ustawienia MailClusterSourceType z MDO (patrz Uwaga powyżej). |
| ClusterQueryStartTime | Datę? | Czas rozpoczęcia klastra — używany jako czas rozpoczęcia zapytania dotyczącego liczby klastrów. Zazwyczaj daty do czasu zakończenia minus DaysToLookBack ustawienie z MDO (patrz Uwaga powyżej). |
| ClusterQueryEndTime | Datę? | Czas zakończenia klastra — używany jako czas zakończenia zapytania o liczniki klastrów. Zazwyczaj czas odebrania danych poczty. |
| ClusterGroup | Ciąg | Odnosi się do klucza zapytania Kusto używanego w MDO (patrz Uwaga powyżej). |
|

Silne identyfikatory jednostki klastra poczty:
- Zapytanie + Źródło

## <a name="mail-message"></a>Wiadomość e-mail

*Nazwa jednostki: MailMessage*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | "poczta-wiadomość" |
| Pliki | &lt;Plik IList&gt; | Jednostki plików załączników tej wiadomości e-mail. |
| Adresat | Ciąg | Odbiorca wiadomości e-mail. W przypadku wielu adresatów wiadomość e-mail jest kopiowana, a każda kopia ma jednego adresata. |
| Adresy | &lt;Ciąg IList&gt; | Adresy URL zawarte w tej wiadomości e-mail. |
| Zagrożenia | &lt;Ciąg IList&gt; | Zagrożenia zawarte w tej wiadomości e-mail. |
| Nadawca | Ciąg | Adres e-mail nadawcy. |
| P1Sender | Ciąg | Identyfikator poczty e-mail (delegowany) użytkownika, który wysłał tę pocztę "w imieniu użytkownika P2 (podstawowego)". Jeśli wiadomość e-mail nie jest wysyłana przez delegata, ta wartość jest równa P2Sender. |
| P1SenderDisplayName | Ciąg | Nazwa wyświetlana użytkownika (delegowanego), który wysłał tę wiadomość e-mail w imieniu użytkownika P2 (podstawowego). Reprezentowane w nagłówku wiadomości e-mail przez właściwość "OnbehalfofSenderDisplayName". |
| P1SenderDomain | Ciąg | Domena poczty e-mail (delegowany) użytkownika, który wysłał tę pocztę "w imieniu użytkownika P2 (podstawowego)". Jeśli wiadomość e-mail nie jest wysyłana przez delegata, ta wartość jest równa P2SenderDomain. |
| P2Sender | Ciąg | Adres e-mail użytkownika (podstawowego), w imieniu którego wiadomość e-mail została wysłana. |
| P2SenderDisplayName | Ciąg | Nazwa wyświetlana użytkownika (podstawowego) w imieniu kogo wysłano wiadomość e-mail. Jeśli wiadomość e-mail nie jest wysyłana przez delegata, reprezentuje nazwę wyświetlaną nadawcy. |
| P2SenderDomain | Ciąg | Domena poczty e-mail użytkownika (podstawowego), w imieniu kogo ta wiadomość e-mail została wysłana. Jeśli wiadomość e-mail nie jest wysyłana przez delegata, reprezentuje ona domenę nadawcy. |
| SenderIP | Ciąg | Adres IP nadawcy. |
| ReceivedDate | DateTime | Data odebrania tej wiadomości. |
| NetworkMessageId | Ident? | Identyfikator komunikatu sieciowego tej wiadomości e-mail. |
| InternetMessageId | Ciąg | Identyfikator wiadomości internetowej tej wiadomości e-mail. |
| Temat | Ciąg | Temat tej wiadomości e-mail. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Używane przez usługę Microsoft Defender dla pakietu Office 365 do znajdowania pasujących lub podobnych wiadomości e-mail. |
| AntispamDirection | Podstawowe? | Kierunkowość tej wiadomości e-mail. Możliwe wartości:<li>Nieznane<li>Przychodzący<li>Wychodzący<li>Intraorg (wewnętrzny) |
| DeliveryAction | Podstawowe? | Akcja dostarczania tej wiadomości e-mail. Możliwe wartości:<li>Nieznane<li>DeliveredAsSpam<li>Dostarczono<li>Zablokowane<li>Zastąpienie |
| DeliveryLocation | Podstawowe? | Lokalizacja dostarczania tej wiadomości e-mail. Możliwe wartości:<li>Nieznane<li>Skrzynka odbiorcza<li>JunkFolder<li>DeletedFolder<li>Kwarantanna<li>Zewnętrzna<li>Niepowodzenie<li>Porzucony<li>Przesłać |
| Język | Ciąg | Język, w którym napisano zawartość wiadomości e-mail. |
| ThreatDetectionMethods | &lt;Ciąg IList&gt; | Lista metod wykrywania zagrożeń zastosowanych w tej wiadomości e-mail. |
|

Silne identyfikatory jednostki wiadomości e-mail:
- NetworkMessageId + odbiorca

## <a name="submission-mail"></a>Przesyłanie poczty

*Nazwa jednostki: SubmissionMail*

| Pole | Typ | Opis |
| ----- | ---- | ----------- |
| Typ | Ciąg | 'SubmissionMail' |
| SubmissionId | Ident? | Identyfikator przesłania. |
| SubmissionDate | Datę? | Zgłoszono datę i godzinę dla tego przesłania. |
| Osoba przesyłająca | Ciąg | Adres e-mail osoby przesyłającej. |
| NetworkMessageId | Ident? | Identyfikator komunikatu sieciowego, do którego należy przesyłanie. |
| Znacznik czasu | Datę? | Sygnatura czasowa odebrania komunikatu (poczta). |
| Adresat | Ciąg | Odbiorca wiadomości e-mail. |
| Nadawca | Ciąg | Nadawca wiadomości e-mail. |
| SenderIp | Ciąg | Adres IP nadawcy. |
| Temat | Ciąg | Temat przesłania wiadomości e-mail. |
| ReportType | Ciąg | Typ przesłania dla danego wystąpienia. To mapuje na wiadomości-śmieci, phishing, złośliwe oprogramowanie lub NotJunk. |
|

Silne identyfikatory jednostki SubmissionMail:
- SubmissionId, osoba przesyłająca, NetworkMessageId, odbiorca

## <a name="cloud-application-identifiers"></a>Identyfikatory aplikacji w chmurze

Poniższa lista zawiera definicje identyfikatorów znanych aplikacji w chmurze. Wartość identyfikatora aplikacji jest używana jako identyfikator jednostki [aplikacji w chmurze](#cloud-application) .

|Identyfikator aplikacji|Nazwa|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|Cornerstone OnDemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive oprogramowanie|
|11114|SalesForce|
|11161|Office 365|
|11162|Microsoft OneNote online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive dla Firm|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Cykl życia Autodesk Fusion|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|Microsoft Skype dla firm|
|25988|Dokumentacja firmy Google|
|26055|Centrum administracyjne Microsoft Office 365|
|26060|OPSWAT koła zębate|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Dysk Google|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|Emulator serwera proxy CAS|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics talent|
|

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono informacje o strukturze, identyfikatorach i schematach jednostki na platformie Azure.

Dowiedz się więcej o [mapowaniu](map-data-fields-to-entities.md) [jednostek](entities-in-azure-sentinel.md) i jednostek. 
