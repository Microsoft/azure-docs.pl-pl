---
title: Współpraca z usługą Defender dla interfejsów API IoT
description: Użyj zewnętrznego interfejsu API REST, aby uzyskać dostęp do danych wykrytych przez czujniki i konsole zarządzania oraz wykonywać działania z tymi danymi.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: d49aa50b1b8843dfb5c3d32983ff0bb129543bb0
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843676"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Usługa Defender dla interfejsów API konsoli zarządzania i czujnika IoT

Użyj zewnętrznego interfejsu API REST, aby uzyskać dostęp do danych wykrytych przez czujniki i konsole zarządzania oraz wykonywać działania z tymi danymi.

Połączenia są zabezpieczane za pośrednictwem protokołu SSL.

## <a name="getting-started"></a>Wprowadzenie

Ogólnie rzecz biorąc, gdy używasz zewnętrznego interfejsu API w usłudze Azure Defender dla czujnika IoT lub lokalnej konsoli zarządzania, musisz wygenerować token dostępu. Tokeny nie są wymagane do uwierzytelniania interfejsów API, które są używane na czujniku i lokalnej konsoli zarządzania.

Aby wygenerować token:

1. W oknie **Ustawienia systemu** wybierz pozycję **tokeny dostępu**.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Zrzut ekranu przedstawiający okno Ustawienia systemowe z wyróżnionym przyciskiem tokeny dostępu.":::

2. Wybierz pozycję **Generuj nowy token**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Wybierz przycisk, aby wygenerować nowy token.":::

3. Opisz przeznaczenie nowego tokenu i wybierz pozycję **dalej**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Wygeneruj nowy token i wprowadź nazwę powiązanej integracji.":::

4. Zostanie wyświetlony token dostępu. Skopiuj, ponieważ nie będzie ponownie wyświetlany.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Skopiuj token dostępu do integracji.":::

5. Wybierz pozycję **Zakończ**. Utworzone tokeny są wyświetlane w oknie dialogowym **tokeny dostępu** .
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe tokeny zasobów z wypełnieniem tokenów":::

   **Używany** wskazuje czas ostatniego wywołania zewnętrznego z tym tokenem.

   Jeśli w **używanym** polu dla tego tokenu zostanie wyświetlona wartość **N/A** , połączenie między czujnikiem i połączonym serwerem nie działa.

6. Dodaj nagłówek HTTP zatytułowany **autoryzacja** do żądania i ustaw jego wartość na wygenerowany token.

## <a name="sensor-api-specifications"></a>Specyfikacje interfejsu API czujnika

W tej sekcji opisano następujące interfejsy API czujnika:

- /api/v1/devices

- /api/v1/devices/connections

- /api/v1/devices/cves

- /api/v1/alerts

- /api/v1/events

- /api/v1/reports/vulnerabilities/devices

- /api/v1/reports/vulnerabilities/security

- /api/v1/reports/vulnerabilities/operational

- /api/external/authentication/validation

- /External/Authentication/set_password

- /External/Authentication/set_password_by_admin

### <a name="retrieve-device-information"></a>Pobieranie informacji o urządzeniu

Użyj tego interfejsu API, aby zażądać listy wszystkich urządzeń, dla których wykryto czujnik usługi Defender for IoT.

#### <a name="apiv1devices"></a>/api/v1/devices

#### <a name="method"></a>Metoda

**GET**

Żąda listy wszystkich urządzeń, dla których wykryto czujnik usługi Defender for IoT.

#### <a name="query-parameters"></a>Parametry zapytania

- **autoryzowane**: filtrowanie tylko autoryzowanych i nieautoryzowanych urządzeń.

  **Przykłady**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących urządzenia.

#### <a name="device-fields"></a>Pola urządzenia

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **id** | Numeryczne | Nie | - |
| **Adresy IP** | Tablica JSON | Tak | Adresy IP (mogą zawierać więcej niż jeden adres w przypadku adresów internetowych lub urządzeń z dwoma kartami sieciowymi) |
| **Nazwij** | Ciąg | Nie | - |
| **Wprowadź** | Ciąg | Nie | Nieznany, stacja robocza, PLC, HMI, Historian, kontroler domeny, serwer bazy danych, punkt dostępu bezprzewodowego, router, przełącznik, serwer, stacja robocza, kamera IP, drukarka, Zapora, stacja terminalowa, VPN Gateway, Internet lub Multiemisja i emisja |
| **macAddresses** | Tablica JSON | Tak | Adresy MAC (w przypadku urządzenia z dwoma kartami sieciowymi może być więcej niż jeden adres) |
| **operatingSystem** | Ciąg | Tak | - |
| **engineeringStation** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **modelu** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **wejście** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **Vendor** | Ciąg | Tak | - |
| **protokołów** | Tablica JSON | Tak | Obiekt protokołu |
| **wbudowane** | Tablica JSON | Tak | Obiekt oprogramowania układowego |

#### <a name="protocol-fields"></a>Pola protokołów

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Nazwa** | Ciąg | Nie |  |
| **Adresy** | Tablica JSON | Tak | Wartości główne lub liczbowe |

#### <a name="firmware-fields"></a>Pola oprogramowania układowego

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Kolejną** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **wzorów** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **firmwareVersion** | Double | Nie | Nie dotyczy lub wartość rzeczywista |
| **additionalData** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **moduleAddress** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **montowany** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **gniazd** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **Ulica** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

### <a name="retrieve-device-connection-information"></a>Pobierz informacje o połączeniu z urządzeniem

Użyj tego interfejsu API, aby zażądać listy wszystkich połączeń na urządzenie.

#### <a name="apiv1devicesconnections"></a>/api/v1/devices/connections

#### <a name="method"></a>Metoda

**GET**

#### <a name="query-parameters"></a>Parametry zapytania

Jeśli nie ustawisz parametrów zapytania, zostaną zwrócone wszystkie połączenia urządzeń.

**Przykład:**

`/api/v1/devices/connections`

- **deviceId**: Filtruj według określonego identyfikatora urządzenia, aby wyświetlić jego połączenia.

  **Przykład:**

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: przedział czasu od teraz na minutę, podczas którego połączenia były aktywne.

  **Przykład:**

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: Przefiltruj tylko połączenia wykryte przed upływem określonego czasu (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: filtrowanie tylko połączeń, które zostały wykryte po upływie określonego czasu (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących połączenia urządzeń.

#### <a name="fields"></a>Pola

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **firstDeviceId** | Numeryczne | Nie | - |
| **secondDeviceId** | Numeryczne | Nie | - |
| **lastSeen** | Numeryczne | Nie | Epoka (UTC) |
| **odnalezione** | Numeryczne | Nie | Epoka (UTC) |
| **np** | Tablica liczb | Nie | - |
| **protokołów** | Tablica JSON | Nie | Pole protokołu |

#### <a name="protocol-field"></a>Pole protokołu

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Nazwij** | Ciąg | Nie | - |
| **polecenia** | Tablica ciągów | Nie | - |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

### <a name="retrieve-information-on-cves"></a>Pobieranie informacji o CVEs

Użyj tego interfejsu API, aby zażądać listy wszystkich znanych CVEs odnalezionych na urządzeniach w sieci.

#### <a name="apiv1devicescves"></a>/api/v1/devices/cves

#### <a name="method"></a>Metoda

**GET**

#### <a name="query-parameters"></a>Parametry zapytania

Domyślnie ten interfejs API udostępnia listę wszystkich adresów IP urządzeń za pomocą usługi CVEs, do 100 z góry CVEs dla każdego z nich.

**Przykład:**

`/api/v1/devices/cves`

- **deviceId**: Filtruj według określonego adresu IP urządzenia, aby uzyskać maksymalnie 100 CVEs z określoną na określonym urządzeniu.

  **Przykład:**

  `/api/v1/devices/<ipAddress>/cves`

- **Góra**: ile CVEs z góry ma być pobieranych dla każdego adresu IP urządzenia.

  **Przykład:**

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON, która reprezentuje CVEs identyfikowane w adresach IP.

#### <a name="fields"></a>Pola

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **cveId** | Ciąg | Nie | - |
| **Adresu** | Ciąg | Nie | Adres IP |
| **dały** | Ciąg | Nie | 0,0 – 10,0 |
| **attackVector** | Ciąg | Nie | Sieć, sąsiadująca sieć, lokalna lub fizyczna |
| **zharmonizowan** | Ciąg | Nie | - |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

### <a name="retrieve-alert-information"></a>Pobieranie informacji o alercie

Użyj tego interfejsu API, aby zażądać listy wszystkich alertów wykrytych przez usługę Defender for IoT.

#### <a name="apiv1alerts"></a>/api/v1/alerts

#### <a name="method"></a>Metoda

**GET**

#### <a name="query-parameters"></a>Parametry zapytania

- **State**: filtrowanie tylko obsłużonych lub nieobsłużonych alertów.

  **Przykład:**

  `/api/v1/alerts?state=handled`

- **fromTime**: aby odfiltrować alerty utworzone na podstawie określonego czasu (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: aby odfiltrować alerty utworzone dopiero przed upływem określonego czasu (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/alerts?toTime=<epoch>`

- **Typ**: Aby filtrować alerty według określonego typu. Istniejące typy do filtrowania według: nieoczekiwane nowe urządzenia, rozłączenia.

  **Przykład:**

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON, które reprezentują alerty.

#### <a name="alert-fields"></a>Pola alertu

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **ID (Identyfikator)** | Numeryczne | Nie | - |
| **pierwszym** | Numeryczne | Nie | Epoka (UTC) |
| **title** | Ciąg | Nie | - |
| **Komunikat** | Ciąg | Nie | - |
| **obrażeń** | Ciąg | Nie | Ostrzeżenie, pomocniczy, główny lub krytyczny |
| **wyszukiwarce** | Ciąg | Nie | Naruszenie protokołu, naruszenie zasad, złośliwe oprogramowanie, anomalia lub operacyjna |
| **sourceDevice** | Numeryczne | Tak | Identyfikator urządzenia |
| **destinationDevice** | Numeryczne | Tak | Identyfikator urządzenia |
| **additionalInformation** | Obiekt informacji dodatkowych | Tak | - |

#### <a name="additional-information-fields"></a>Dodatkowe pola informacji

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **zharmonizowan** | Ciąg | Nie | - |
| **zawartych** | Tablica JSON | Nie | Ciąg |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

### <a name="retrieve-timeline-events"></a>Pobieranie zdarzeń osi czasu

Użyj tego interfejsu API, aby zażądać listy zdarzeń raportowanych do osi czasu zdarzeń.

#### <a name="apiv1events"></a>/api/v1/events

#### <a name="method"></a>Metoda

**GET**

#### <a name="query-parameters"></a>Parametry zapytania

- **minutesTimeFrame**: przedział czasu od teraz do tyłu, na minutę, w którym zdarzenia zostały zgłoszone.

  **Przykład:**

  `/api/v1/events?minutesTimeFrame=20`

- **Typ**: Aby filtrować listę zdarzeń według określonego typu.

  **Przykłady**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON, które reprezentują alerty.

#### <a name="event-fields"></a>Pola zdarzeń

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|--|
| **znacznik czasu** | Numeryczne | Nie | Epoka (UTC) |
| **title** | Ciąg | Nie | - |
| **obrażeń** | Ciąg | Nie | Informacja, powiadomienie lub ALERT |
| **właociciela** | Ciąg | Tak | Jeśli zdarzenie zostało utworzone ręcznie, to pole będzie zawierać nazwę użytkownika, który utworzył zdarzenie |
| **treści** | Ciąg | Nie | - |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

### <a name="retrieve-vulnerability-information"></a>Pobierz informacje dotyczące luk w zabezpieczeniach

Użyj tego interfejsu API, aby zażądać wyników oceny luk w zabezpieczeniach dla każdego urządzenia.

#### <a name="apiv1reportsvulnerabilitiesdevices"></a>/api/v1/reports/vulnerabilities/devices

#### <a name="method"></a>Metoda

**GET**

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących oceniane urządzenia.

Obiekt urządzenia zawiera:

- Dane ogólne

- Ocena oceny

- Luki w zabezpieczeniach

#### <a name="device-fields"></a>Pola urządzenia

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Nazwij** | Ciąg | Nie | - |
| **Adresy IP** | Tablica JSON | Nie | - |
| **securityScore** | Numeryczne | Nie | - |
| **Vendor** | Ciąg | Tak |  |
| **firmwareVersion** | Ciąg | Tak | - |
| **wzorów** | Ciąg | Tak | - |
| **isWirelessAccessPoint** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **operatingSystem** | Obiekt systemu operacyjnego | Tak | - |
| **mogąc** | Obiekt luk w zabezpieczeniach | Tak | - |

#### <a name="operating-system-fields"></a>Pola systemu operacyjnego

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Nazwa** | Ciąg | Tak | - |
| **Typ** | Ciąg | Tak | - |
| **Wersja** | Ciąg | Tak | - |
| **latestVersion** | Ciąg | Tak | - |

#### <a name="vulnerabilities-fields"></a>Pola luk w zabezpieczeniach
 
| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **antywirusowe** | Tablica JSON | Tak | Nazwy antywirusowe |
| **plainTextPasswords** | Tablica JSON | Tak | Obiekty hasła |
| **Ras** | Tablica JSON | Tak | Obiekty dostępu zdalnego |
| **isBackupServer** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **openedPorts** | Tablica JSON | Tak | Otwarte obiekty portu |
| **isEngineeringStation** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **isKnownScanner** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **cves** | Tablica JSON | Tak | Obiekty CVE |
| **isautoryzowane** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **malwareIndicationsDetected** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **weakAuthentication** | Tablica JSON | Tak | Wykryte aplikacje korzystające z słabego uwierzytelniania |

#### <a name="password-fields"></a>Pola hasła

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **hasło** | Ciąg | Nie | - |
| **protokol** | Ciąg | Nie | - |
| **naprężeni** | Ciąg | Nie | Bardzo słaby, słaby, średni lub silny |

#### <a name="remote-access-fields"></a>Pola dostępu zdalnego

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **przewożąc** | Numeryczne | Nie | - |
| **transportu** | Ciąg | Nie | TCP lub UDP |
| **Klient** | Ciąg | Nie | Adres IP |
| **clientSoftware** | Ciąg | Nie | SSH, VNC, pulpit zdalny lub przeglądarka Team Viewer |

#### <a name="open-port-fields"></a>Otwórz pola portu

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **przewożąc** | Numeryczne | Nie | - |
| **transportu** | Ciąg | Nie | TCP lub UDP |
| **protokol** | Ciąg | Tak | - |
| **isConflictingWithFirewall** | Wartość logiczna | Nie | Prawda lub FAŁSZ |

#### <a name="cve-fields"></a>Pola CVE

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **ID (Identyfikator)** | Ciąg | Nie | - |
| **dały** | Numeryczne | Nie | Double |
| **zharmonizowan** | Ciąg | Nie | - |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

### <a name="retrieve-security-vulnerabilities"></a>Pobieranie luk w zabezpieczeniach

Ten interfejs API służy do żądania wyników ogólnej oceny luk w zabezpieczeniach. Ta ocena zawiera szczegółowe informacje na temat poziomu zabezpieczeń systemu.

Ta ocena jest oparta na ogólnych informacjach dotyczących sieci i systemu, a nie na określonej ewaluacji urządzenia.

#### <a name="apiv1reportsvulnerabilitiessecurity"></a>/api/v1/reports/vulnerabilities/security

#### <a name="method"></a>Metoda

**GET**

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Obiekt JSON reprezentujący szacowane wyniki. Każdy klucz może być dopuszczany do wartości null. W przeciwnym razie będzie zawierać obiekt JSON z kluczami, które nie dopuszczają wartości null.

### <a name="result-fields"></a>Pola wyników

**Klucze**

**unauthorizedDevices**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **Ulica** | Ciąg | Adres IP |
| **Nazwij** | Ciąg | - |
| **firstDetectionTime** | Numeryczne | Epoka (UTC) |
| lastSeen | Numeryczne | Epoka (UTC) |

**illegalTrafficByFirewallRules**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **Server** | Ciąg | Adres IP |
| **Klient** | Ciąg | Adres IP |
| **przewożąc** | Numeryczne | - |
| **transportu** | Ciąg | TCP, UDP lub ICMP |

**weakFirewallRules**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **sources** | Tablica źródeł JSON. Każde źródło może znajdować się w jednym z czterech formatów. | "Any", "adres IP (Host)", "od IP do IP (zakres)", "adres IP, maska podsieci (Sieć)" |
| **docelowym** | Tablica obiektów docelowych JSON. Każda lokalizacja docelowa może być w dowolnym z czterech formatów. | "Any", "adres IP (Host)", "od IP do IP (zakres)", "adres IP, maska podsieci (Sieć)" |
| **np** | Tablica JSON portów w jednym z trzech formatów | "Dowolny", "Port (protokół, jeśli wykryto)", "z portu do portu (protokół, jeśli został wykryty)" |

**accessPoints**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **macAddress** | Ciąg | Adres MAC |
| **Vendor** | Ciąg | Nazwa dostawcy |
| **Adresu** | Ciąg | Adres IP lub nie ma |
| **Nazwij** | Ciąg | Nazwa urządzenia lub nie |
| **sieci** | Ciąg | Nie, podejrzane lub tak |

**connectionsBetweenSubnets**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **Server** | Ciąg | Adres IP |
| **Klient** | Ciąg | Adres IP |

**industrialMalwareIndicators**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **detectionTime** | Numeryczne | Epoka (UTC) |
| **Określony komunikat alarmu** | Ciąg | - |
| **zharmonizowan** | Ciąg | - |
| **urządzeniem** | Tablica JSON | Nazwy urządzeń | 

**internetConnections**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **internalAddress** | Ciąg | Adres IP |
| **wejście** | Wartość logiczna | Tak lub Nie | 
| **externalAddresses** | Tablica JSON | Adres IP |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

### <a name="retrieve-operational-vulnerabilities"></a>Pobieranie luk w zabezpieczeniach

Ten interfejs API służy do żądania wyników ogólnej oceny luk w zabezpieczeniach. Ta ocena zapewnia wgląd w informacje o stanie operacyjnym sieci. Jest on oparty na ogólnych informacjach dotyczących sieci i systemu, a nie na określonej ocenie urządzenia.

#### <a name="apiv1reportsvulnerabilitiesoperational"></a>/api/v1/reports/vulnerabilities/operational

#### <a name="method"></a>Metoda

**GET**

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Obiekt JSON reprezentujący szacowane wyniki. Każdy klucz zawiera tablicę wyników JSON.

#### <a name="result-fields"></a>Pola wyników

**Klucze**

**backupServer**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| **zewnętrz** | Ciąg | Adres IP |
| **punktu** | Ciąg | Adres IP |
| **przewożąc** | Numeryczne | - |
| **transportu** | Ciąg | TCP lub UDP |
| **backupMaximalInterval** | Ciąg | - |
| **lastSeenBackup** | Numeryczne | Epoka (UTC) |

**ipNetworks**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| **adresowanie** s | Numeryczne | - |
| **NFS** | Ciąg | Adres IP |
| **maska** | Ciąg | Maska podsieci |

**protocolProblems**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| **protokol** | Ciąg | - |
| **adres** | Tablica JSON | Adresy IP |
| **wiadom** | Ciąg | - |
| **reportTime** | Numeryczne | Epoka (UTC) |

**protocolDataVolumes**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| protokol | Ciąg | - |
| wolumin | Ciąg | "numer woluminu MB" |

**odłączenia**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| **assetAddress** | Ciąg | Adres IP |
| **element zawartości** | Ciąg | - |
| **lastDetectionTime** | Numeryczne | Epoka (UTC) |
| **backToNormalTime** | Numeryczne | Epoka (UTC) |     

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

### <a name="validate-user-credentials"></a>Weryfikowanie poświadczeń użytkownika

Ten interfejs API umożliwia Weryfikowanie nazwy użytkownika i hasła usługi Defender dla usługi IoT. Wszystkie role użytkowników usługi Defender for IoT mogą współpracować z interfejsem API.

Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender for IoT.

#### <a name="apiexternalauthenticationvalidation"></a>/api/external/authentication/validation

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="query-parameters"></a>Parametry zapytania

| **Nazwa** | **Typ** | **Wymaga** |
|--|--|--|
| **uż** | Ciąg | Nie |
| **hasło** | Ciąg | Nie |

#### <a name="request-example"></a>Przykład żądania

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Ciąg komunikatu zawierającego szczegóły stanu operacji:

- **Sukces-MSG**: uwierzytelnianie zakończyło się pomyślnie

- **Niepowodzenie — błąd**: weryfikowanie poświadczeń nie powiodło się

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

### <a name="change-password"></a>Zmień hasło

Użyj tego interfejsu API, aby umożliwić użytkownikom zmianę własnych haseł. Wszystkie role użytkowników usługi Defender for IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender for IoT.

#### <a name="externalauthenticationset_password"></a>/External/Authentication/set_password

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="request-example"></a>Przykład żądania

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Ciąg komunikatu zawierającego szczegóły stanu operacji:

- **Powodzenie — MSG**: hasło zostało zastąpione

- **Niepowodzenie — błąd**: niepowodzenie uwierzytelniania użytkownika

- **Niepowodzenie — błąd**: hasło jest niezgodne z zasadami zabezpieczeń

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Pola urządzenia

| **Nazwa** | **Typ** | **Wymaga** |
|--|--|--|
| **uż** | Ciąg | Nie |
| **hasło** | Ciąg | Nie |
| **new_password** | Ciąg | Nie |

### <a name="user-password-update-by-system-admin"></a>Aktualizacja hasła użytkownika przez administratora systemu

Użyj tego interfejsu API, aby umożliwić administratorom systemu Zmienianie haseł dla określonych użytkowników. Usługi Defender dla administratorów IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender for IoT.

#### <a name="externalauthenticationset_password_by_admin"></a>/External/Authentication/set_password_by_admin

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="request-example"></a>Przykład żądania

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Ciąg komunikatu zawierającego szczegóły stanu operacji:

- **Powodzenie — MSG**: hasło zostało zastąpione

- **Niepowodzenie — błąd**: niepowodzenie uwierzytelniania użytkownika

- **Niepowodzenie — błąd**: użytkownik nie istnieje

- Błąd **— błąd**: hasło nie jest zgodne z zasadami zabezpieczeń

- **Niepowodzenie — błąd**: użytkownik nie ma uprawnień do zmiany hasła

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Pola urządzenia

| **Nazwa** | **Typ** | **Wymaga** |
|--|--|--|
| **admin_username** | Ciąg | Nie |
| **admin_password** | Ciąg | Nie |
| **uż** | Ciąg | Nie |
| **new_password** | Ciąg | Nie |

## <a name="on-premises-management-console-api-specifications"></a>Specyfikacje interfejsu API lokalnej konsoli zarządzania

W tej sekcji opisano następujące interfejsy API lokalnej konsoli zarządzania:

- **/external/v1/alerts/<UUID>**

- **Wykluczenia alertów (okno obsługi)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Okno wykluczenia alertu z aktywnymi regułami.":::

Zdefiniuj warunki, w których alerty nie będą wysyłane. Można na przykład definiować i aktualizować czasy zatrzymywania i uruchamiania, urządzenia lub podsieci, które powinny być wykluczone podczas wyzwalania alertów, lub aparat usługi Defender for IoT, który powinien zostać wykluczony. Na przykład w oknie obsługi można zatrzymać dostarczanie wszystkich alertów z wyjątkiem alertów złośliwego oprogramowania na urządzeniach krytycznych.

Zdefiniowane w tym miejscu interfejsy API są wyświetlane w oknie **wykluczenia alertów** w lokalnej konsoli zarządzania jako reguła wykluczania tylko do odczytu.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Przykład odpowiedzi**

- **reakcji**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password"></a>Zmień hasło

Użyj tego interfejsu API, aby umożliwić użytkownikom zmianę własnych haseł. Wszystkie role użytkowników usługi Defender for IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender for IoT.

- **/External/Authentication/set_password**

#### <a name="user-password-update-by-system-admin"></a>Aktualizacja hasła użytkownika przez administratora systemu

Użyj tego interfejsu API, aby umożliwić administratorom systemu Zmienianie haseł dla określonych użytkowników. Role użytkownika administratora usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender for IoT.

- **/External/Authentication/set_password_by_admin**

### <a name="retrieve-device-information"></a>Pobieranie informacji o urządzeniu

Ten interfejs API żąda listy wszystkich urządzeń wykrywanych przez usługę Defender dla czujników IoT, które są połączone z lokalną konsolą zarządzania.

- **/external/v1/devices**

#### <a name="method"></a>Metoda

**GET**

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących urządzenia.

#### <a name="query-parameters"></a>Parametry zapytania

- **autoryzowane**: filtrowanie tylko autoryzowanych i nieautoryzowanych urządzeń.

- Identyfikator **witryny**: filtrowanie tylko urządzeń związanych z określonymi lokacjami.

- **zoneId**: aby odfiltrować tylko urządzenia powiązane z określonymi strefami. [1](#1)

- **sensorId**: aby odfiltrować tylko urządzenia wykryte przez określone czujniki. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *być może nie masz witryny i identyfikatora strefy. W takim przypadku należy wykonać zapytanie o wszystkie urządzenia, aby pobrać identyfikator witryny i strefy.*

#### <a name="query-parameters-example"></a>Przykład parametrów zapytania

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Pola urządzenia

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **sensorId** | Numeryczne | Nie | - |
| **zoneId** | Numeryczne | Tak | - |
| **Witryny** | Numeryczne | Tak | - |
| **Adresy IP** | Tablica JSON | Tak | Adresy IP (mogą zawierać więcej niż jeden adres w przypadku adresów internetowych lub urządzeń z dwoma kartami sieciowymi) |
| **Nazwij** | Ciąg | Nie | - |
| **Wprowadź** | Ciąg | Nie | Nieznany, stacja robocza, PLC, HMI, Historian, kontroler domeny, serwer bazy danych, punkt dostępu bezprzewodowego, router, przełącznik, serwer, stacja robocza, kamera IP, drukarka, Zapora, stacja terminalowa, VPN Gateway, Internet lub Multiemisja i emisja |
| **macAddresses** | Tablica JSON | Tak | Adresy MAC (w przypadku urządzenia z dwoma kartami sieciowymi może być więcej niż jeden adres) |
| **operatingSystem** | Ciąg | Tak | - |
| **engineeringStation** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **modelu** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **wejście** | Wartość logiczna | Nie | Prawda lub FAŁSZ |
| **Vendor** | Ciąg | Tak | - |
| **Protokoły** | Tablica JSON | Tak | Obiekt protokołu |
| **wbudowane** | Tablica JSON | Tak | Obiekt oprogramowania układowego |

#### <a name="protocol-fields"></a>Pola protokołów

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| Nazwa | Ciąg | Nie | - |
| Adresy | Tablica JSON | Tak | Wartości główne lub liczbowe |

#### <a name="firmware-fields"></a>Pola oprogramowania układowego

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Kolejną** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **wzorów** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **firmwareVersion** | Double | Nie | Nie dotyczy lub wartość rzeczywista |
| **additionalData** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **moduleAddress** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **montowany** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **gniazd** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |
| **Ulica** | Ciąg | Nie | Nie dotyczy lub wartość rzeczywista |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

### <a name="retrieve-alert-information"></a>Pobieranie informacji o alercie

Ten interfejs API umożliwia pobieranie wszystkich lub odfiltrowanych alertów z lokalnej konsoli zarządzania.

#### <a name="externalv1alerts"></a>/external/v1/alerts

#### <a name="method"></a>Metoda

**GET**

#### <a name="query-parameters"></a>Parametry zapytania

- **State**: filtrowanie tylko obsłużonych i nieobsłużonych alertów.

  **Przykład:**

  `/api/v1/alerts?state=handled`

- **fromTime**: aby odfiltrować alerty utworzone na podstawie określonego czasu (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**: aby odfiltrować alerty utworzone dopiero przed upływem określonego czasu (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/alerts?toTime=<epoch>`

- **Identyfikator witryny**: Witryna, w której został odnaleziony alert. [2](#2)

- **zoneId**: strefa, w której został odnaleziony alert. [2](#2)

- **czujnik**: czujnik, na którym wykryto alert.

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *być może nie masz witryny i identyfikatora strefy. W takim przypadku należy wykonać zapytanie o wszystkie urządzenia, aby pobrać identyfikator witryny i strefy.*

#### <a name="alert-fields"></a>Pola alertu

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **ID (Identyfikator)** | Numeryczne | Nie | - |
| **pierwszym** | Numeryczne | Nie | Epoka (UTC) |
| **title** | Ciąg | Nie | - |
| **Komunikat** | Ciąg | Nie | - |
| **obrażeń** | Ciąg | Nie | Ostrzeżenie, pomocniczy, główny lub krytyczny |
| **wyszukiwarce** | Ciąg | Nie | Naruszenie protokołu, naruszenie zasad, złośliwe oprogramowanie, anomalia lub operacyjna |
| **sourceDevice** | Numeryczne | Tak | Identyfikator urządzenia |
| **destinationDevice** | Numeryczne | Tak | Identyfikator urządzenia |
| **additionalInformation** | Obiekt informacji dodatkowych | Tak | - |

#### <a name="additional-information-fields"></a>Dodatkowe pola informacji

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **zharmonizowan** | Ciąg | Nie | - |
| **zawartych** | Tablica JSON | Nie | Ciąg |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

### <a name="qradar-alerts"></a>Alerty QRadar

Integracja QRadar z usługą Defender for IoT ułatwia identyfikowanie alertów wygenerowanych przez usługę Defender for IoT i wykonywanie akcji przy użyciu tych alertów. Usługa QRadar odbiera dane z usługi Defender for IoT, a następnie kontaktuje się z publicznym składnikiem lokalnej konsoli zarządzania.

Aby wysłać dane wykryte przez usługę Defender for IoT do QRadar, zdefiniuj regułę przekazywania w systemie Defender for IoT i wybierz opcję **Obsługa alertów dotyczących obsługi zdalnej** .

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Edytuj reguły przekazywania w celu dopasowania do Twoich potrzeb.":::

Po wybraniu tej opcji podczas procesu konfigurowania reguł przekazywania następujące dodatkowe pola są wyświetlane w QRadar:

- **UUID**: unikatowy identyfikator alertu, na przykład 1-1555245116250.

- **Lokacja**: Witryna, w której został odnaleziony alert.

- **Strefa**: strefa, w której został odnaleziony alert.

Przykład ładunku wysyłanego do QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/External/V1/Alerts/ &lt; UUID&gt;

#### <a name="method"></a>Metoda

**PUT**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="request-content"></a>Żądaj zawartości

Obiekt JSON, który reprezentuje akcję do wykonania dla alertu, który zawiera identyfikator UUID.

#### <a name="action-fields"></a>Pola akcji

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **transakcji** | Ciąg | Nie | uchwyt lub handleAndLearn |

#### <a name="request-example"></a>Przykład żądania

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących urządzenia.

#### <a name="response-fields"></a>Pola odpowiedzi


| Nazwa | Typ | Dopuszczający wartość null | Opis |
|--|--|--|--|
| **zawartość/błąd** | Ciąg | Nie | Jeśli żądanie zakończy się pomyślnie, zostanie wyświetlona Właściwość Content (zawartość). W przeciwnym razie zostanie wyświetlona Właściwość błędu. |

#### <a name="possible-content-values"></a>Możliwe wartości zawartości

| Kod stanu | Wartość zawartości | Opis |
|--|--|--|
| 200 | Żądanie aktualizacji alertu zakończyło się pomyślnie. | Żądanie aktualizacji zostało zakończone pomyślnie. Brak komentarzy. |
| 200 | Alert został już obsłużony (**dojście**). | Alert został już obsłużony, gdy otrzymano żądanie obsługi dla alertu.<br />Alert pozostaje **obsługiwany**. |
| 200 | Alert został już obsłużony i nauczyć się (**handleAndLearn**). | Alert został już obsłużony i nauczył się, że Otrzymano żądanie **handleAndLearn** .<br />Alert pozostaje w stanie **handledAndLearn** . |
| 200 | Alert został już obsłużony (**obsłużony**).<br />Obsługa i uczenie (**handleAndLearn**) zostały wykonane dla alertu. | Alert został już obsłużony, gdy otrzymano żądanie **handleAndLearn** .<br />Alert zmieni się na **handleAndLearn**. |
| 200 | Alert został już obsłużony i nauczyć się (**handleAndLearn**). Zignorowano żądanie obsługi. | Alert został już **handleAndLearn** , gdy otrzymano żądanie obsługi alertu. Alert pozostaje **handleAndLearn**. |
| 500 | Nieprawidłowa akcja. | Wysłana akcja nie jest prawidłową akcją do wykonania w ramach alertu. |
| 500 | Wystąpił nieoczekiwany błąd. | Wystąpił nieoczekiwany błąd. Aby rozwiązać ten problem, skontaktuj się z pomocą techniczną. |
| 500 | Nie można wykonać żądania, ponieważ nie znaleziono alertu dla tego identyfikatora UUID. | Nie znaleziono określonego identyfikatora UUID alertu w systemie. |

#### <a name="response-example"></a>Przykład odpowiedzi

**Wybran**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Niepomyślnych**

```rest
{
    "error": "Invalid action"
}
```

### <a name="alert-exclusions-maintenance-window"></a>Wykluczenia alertów (okno obsługi)

Zdefiniuj warunki, w których alerty nie będą wysyłane. Można na przykład definiować i aktualizować czasy zatrzymywania i uruchamiania, urządzenia lub podsieci, które powinny być wykluczone podczas wyzwalania alertów, lub aparat usługi Defender for IoT, który powinien zostać wykluczony. Na przykład w oknie obsługi można zatrzymać dostarczanie alertów dla wszystkich alertów z wyjątkiem alertów złośliwego oprogramowania na urządzeniach krytycznych.

Zdefiniowane w tym miejscu interfejsy API są wyświetlane w oknie **wykluczenia alertów** w lokalnej konsoli zarządzania jako reguła wykluczania tylko do odczytu.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Okno wykluczenia alertów, pokazujące listę wszystkich reguł wykluczeń. ":::

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

#### <a name="method---post"></a>Metoda — POST

#### <a name="query-parameters"></a>Parametry zapytania

- **ticketId**: Określa identyfikator biletu konserwacji w systemach użytkownika.

- **czas wygaśnięcia**: określa czas wygaśnięcia (Time to Live), który jest czasem trwania okna obsługi w ciągu kilku minut. Po upływie tego czasu przez ten parametr system automatycznie rozpoczyna wysyłanie alertów.

- **Aparaty**: określa, z którego aparatu zabezpieczeń mają być pomijane alerty w procesie konserwacji:

   - ANOMALII

   - SZKODLIWE

   - DZIAŁAŁ

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**: określa, z którego usługi Defender for IoT Sensor ma pomijać alerty w trakcie procesu konserwacji. Jest to ten sam identyfikator, który został pobrany z/API/V1/Appliances (GET).

- **podsieci**: określa, z której podsieci w celu pomijania alertów w trakcie procesu konserwacji. Podsieć jest wysyłana w następującym formacie: 192.168.0.0/16.

#### <a name="error-codes"></a>Kody błędów

- **201 (utworzono)**: Akcja została pomyślnie ukończona.

- **400 (złe żądanie)**: występuje w następujących przypadkach:

   - Parametr **TTL** nie jest wartością numeryczną ani wartością dodatnią.

   - Parametr **Subnets** został zdefiniowany przy użyciu nieprawidłowego formatu.

   - Brak parametru **ticketId** .

   - Parametr **aparatu** nie jest zgodny z istniejącymi aparatami zabezpieczeń.

- **404 (nie znaleziono)**: jeden z czujników nie istnieje.

- **409 (konflikt)**: Identyfikator biletu jest połączony z innym otwartym oknem obsługi.

- **500 (wewnętrzny błąd serwera)**: wszelkie inne nieoczekiwane błędy.

> [!NOTE]
> Upewnij się, że identyfikator biletu nie jest połączony z istniejącym otwartym oknem. Wygenerowana jest następująca reguła wykluczania: konserwacja — {nazwa tokenu} — {identyfikator biletu}.

#### <a name="method---put"></a>Metoda PUT

Umożliwia zaktualizowanie czasu trwania okna obsługi po rozpoczęciu procesu konserwacji przez zmianę parametru **TTL** . Nowa definicja czasu trwania zastępuje poprzednią.

Ta metoda jest przydatna, gdy chcesz ustawić dłuższy czas trwania niż aktualnie skonfigurowany czas trwania.

#### <a name="query-parameters"></a>Parametry zapytania

- **ticketId**: Określa identyfikator biletu konserwacji w systemach użytkownika.

- **czas wygaśnięcia**: określa czas trwania okna w minutach.

#### <a name="error-code"></a>Kod błędu

- **200 (ok)**: Akcja została pomyślnie ukończona.

- **400 (złe żądanie)**: występuje w następujących przypadkach:

   - Parametr **TTL** nie jest wartością numeryczną ani wartością dodatnią.

   - Brak parametru **ticketId** .

   - Brak parametru **TTL** .

- **404 (nie znaleziono)**: Identyfikator biletu nie jest połączony z otwartym oknem obsługi.

- **500 (wewnętrzny błąd serwera)**: wszelkie inne nieoczekiwane błędy.

> [!NOTE]
> Upewnij się, że identyfikator biletu jest połączony z istniejącym otwartym oknem.

#### <a name="method---delete"></a>Metoda — usuwanie

Zamyka istniejące okno obsługi.

#### <a name="query-parameters"></a>Parametry zapytania

- **ticketId**: rejestruje identyfikator biletu konserwacji w systemach użytkownika.

#### <a name="error-code"></a>Kod błędu

- **200 (ok)**: Akcja została pomyślnie ukończona.

- **400 (Nieprawidłowe żądanie)**: brak parametru **ticketId** .

- **404 (nie znaleziono)**: Identyfikator biletu nie jest połączony z otwartym oknem obsługi.

- **500 (wewnętrzny błąd serwera)**: wszelkie inne nieoczekiwane błędy.

> [!NOTE]
> Upewnij się, że identyfikator biletu jest połączony z istniejącym otwartym oknem.

#### <a name="method---get"></a>Metoda — pobieranie

Pobierz dziennik wszystkich akcji otwierania, zamykania i aktualizacji, które zostały wykonane w systemie podczas konserwacji. Dziennik można pobrać tylko dla okien obsługi, które były aktywne w przeszłości i zostały zamknięte.

#### <a name="query-parameters"></a>Parametry zapytania

- **fromDate**: filtruje dzienniki we wstępnie zdefiniowanej dacie i nowszych. Format to 2019-12-30.

- **ToDate**: filtruje dzienniki do wstępnie zdefiniowanej daty. Format to 2019-12-30.

- **ticketId**: filtruje dzienniki powiązane z określonym identyfikatorem biletu.

- **tokenname**: filtruje dzienniki powiązane z określoną nazwą tokenu.

#### <a name="error-code"></a>Kod błędu

- **200 (ok)**: Akcja została pomyślnie ukończona.

- **400 (Nieprawidłowe żądanie)**: format daty jest nieprawidłowy.

- **204 (brak zawartości)**: nie ma danych do wyświetlenia.

- **500 (wewnętrzny błąd serwera)**: wszelkie inne nieoczekiwane błędy.

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentująca operacje okna obsługi.

#### <a name="response-structure"></a>Struktura odpowiedzi

| Nazwa | Typ | Komentarz | Dopuszczający wartość null |
|--|--|--|--|
| **Datę** | Ciąg | Przykład: "2012-04-23T18:25:43.511 Z" | nie |
| **ticketId** | Ciąg | Przykład: "9a5fe99c-D914-4bda-9332-307384fe40bf" | nie |
| **nazwa tokenu** | Ciąg | - | nie |
| **silnika** | Tablica ciągów | - | tak |
| **sensorIds** | Tablica ciągów | - | tak |
| **podsieci** | Tablica ciągów | - | tak |
| **wartość** | Numeryczne | - | tak |
| **operationType** | Ciąg | Wartości to "OPEN", "UPDATE" i "CLOSE" | nie |

### <a name="authenticate-user-credentials"></a>Uwierzytelnianie poświadczeń użytkownika

Użyj tego interfejsu API do zweryfikowania poświadczeń użytkownika. Wszystkie role użytkowników usługi Defender for IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender for IoT.

#### <a name="externalauthenticationvalidation"></a>/external/authentication/validation

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="request-example"></a>Przykład żądania

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Ciąg komunikatu zawierającego szczegóły stanu operacji:

- **Sukces — komunikat**: uwierzytelnianie zakończyło się pomyślnie

- **Niepowodzenie — błąd**: Weryfikacja poświadczeń nie powiodła się

#### <a name="device-fields"></a>Pola urządzenia

| **Nazwa** | **Typ** | **Wymaga** |
|--|--|--|
| **uż** | Ciąg | Nie |
| **hasło** | Ciąg | Nie |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

### <a name="change-password"></a>Zmień hasło

Użyj tego interfejsu API, aby umożliwić użytkownikom zmianę własnych haseł. Wszystkie role użytkowników usługi Defender for IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender for IoT.

#### <a name="externalauthenticationset_password"></a>/External/Authentication/set_password

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="request-example"></a>Przykład żądania

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Ciąg komunikatu zawierającego szczegóły stanu operacji:

- **Powodzenie — MSG**: hasło zostało zastąpione

- **Niepowodzenie — błąd**: niepowodzenie uwierzytelniania użytkownika

- **Niepowodzenie — błąd**: hasło jest niezgodne z zasadami zabezpieczeń

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>Pola urządzenia

| **Nazwa** | **Typ** | **Wymaga** |
|--|--|--|
| **uż** | Ciąg | Nie |
| **hasło** | Ciąg | Nie |
| **new_password** | Ciąg | Nie |

### <a name="user-password-update-by-system-admin"></a>Aktualizacja hasła użytkownika przez administratora systemu

Użyj tego interfejsu API, aby umożliwić administratorom systemu Zmienianie haseł dla określonych użytkowników. Role użytkownika administratora usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender for IoT.

#### <a name="externalauthenticationset_password_by_admin"></a>/External/Authentication/set_password_by_admin

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="request-example"></a>Przykład żądania

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Ciąg komunikatu zawierającego szczegóły stanu operacji:

- **Powodzenie — MSG**: hasło zostało zastąpione

- **Niepowodzenie — błąd**: niepowodzenie uwierzytelniania użytkownika

- **Niepowodzenie — błąd**: użytkownik nie istnieje

- Błąd **— błąd**: hasło nie jest zgodne z zasadami zabezpieczeń

- **Niepowodzenie — błąd**: użytkownik nie ma uprawnień do zmiany hasła

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>Pola urządzenia

| **Nazwa** | **Typ** | **Wymaga** |
|--|--|--|
| **admin_username** | Ciąg | Nie |
| **admin_password** | Ciąg | Nie |
| **uż** | Ciąg | Nie |
| **new_password** | Ciąg | Nie |

## <a name="see-also"></a>Zobacz także
[Badanie wykrywania czujników w spisie urządzeń](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 [Zbadaj wszystkie wykrycia czujnika przedsiębiorstwa w spisie urządzeń](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
