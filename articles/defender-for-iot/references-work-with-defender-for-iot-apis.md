---
title: Praca z interfejsami API usługi Defender for IoT
description: Użyj zewnętrznego interfejsu API REST, aby uzyskać dostęp do danych odnalezionych przez czujniki i konsole zarządzania oraz wykonywać akcje na tych danych.
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: e7833a20d4f708ecb5b80394fae2c56fc07c9489
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752736"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Interfejsy API konsoli zarządzania i czujnika usługi Defender dla IoT

Użyj zewnętrznego interfejsu API REST, aby uzyskać dostęp do danych odnalezionych przez czujniki i konsole zarządzania oraz wykonywać akcje na tych danych.

Połączenia są zabezpieczone za pośrednictwem protokołu SSL.

## <a name="getting-started"></a>Wprowadzenie

Ogólnie rzecz biorąc, jeśli używasz zewnętrznego interfejsu API w czujniku Azure Defender dla IoT lub lokalnej konsoli zarządzania, musisz wygenerować token dostępu. Tokeny nie są wymagane w przypadku interfejsów API uwierzytelniania, których używasz w czujniku i lokalnej konsoli zarządzania.

Aby wygenerować token:

1. W **oknie Ustawienia** systemowe wybierz pozycję **Tokeny dostępu.**
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="Zrzut ekranu przedstawiający okna Ustawień systemowych z wyróżniony przycisk Tokeny dostępu.":::

2. Wybierz **pozycję Generuj nowy token**.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="Wybierz przycisk , aby wygenerować nowy token.":::

3. Opisz przeznaczenie nowego tokenu i wybierz pozycję **Dalej.**
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="Wygeneruj nowy token i wprowadź nazwę skojarzonej z nim integracji.":::

4. Zostanie wyświetlony token dostępu. Skopiuj go, ponieważ nie zostanie on ponownie wyświetlony.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="Skopiuj token dostępu do integracji.":::

5. Wybierz pozycję **Zakończ**. Tokeny, które tworzysz, są wyświetlane w **oknie dialogowym Tokeny** dostępu.
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="Zrzut ekranu przedstawiający okno dialogowe Tokeny urządzeń z wypełnionymi tokenami":::

   **Element** Used wskazuje, kiedy ostatnio odebrano wywołanie zewnętrzne z tym tokenem.

   Jeśli w polu Używany  dla tego tokenu jest wyświetlany komunikat **N/A,** połączenie między czujnikiem a połączonym serwerem nie działa.

6. Dodaj do żądania nagłówek HTTP o **tytule Authorization** (Autoryzacja) i ustaw jego wartość na wygenerowany token.

## <a name="sensor-api-specifications"></a>Specyfikacje interfejsu API czujników

W tej sekcji opisano następujące interfejsy API czujników:

- [Pobieranie informacji o urządzeniu — /api/v1/devices](#retrieve-device-information---apiv1devices)

- [Pobieranie informacji o połączeniu urządzenia — /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [Pobieranie informacji dotyczących cve — /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [Pobieranie informacji o alertach — /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [Pobieranie zdarzeń osi czasu — /api/v1/events](#retrieve-timeline-events---apiv1events)

- [Pobieranie informacji o lukach w zabezpieczeniach — /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [Pobieranie luk w zabezpieczeniach — /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [Pobieranie luk w zabezpieczeniach operacyjnych — /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [Weryfikowanie poświadczeń użytkownika — /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [Zmienianie hasła — /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [Aktualizacja hasła użytkownika przez administratora systemu — /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>Pobieranie informacji o urządzeniu — /api/v1/devices

Ten interfejs API umożliwia zażądanie listy wszystkich urządzeń wykrytych przez czujnik usługi Defender dla IoT.

#### <a name="method"></a>Metoda

**Pobierz**

Żąda listy wszystkich urządzeń wykrytych przez czujnik usługi Defender dla IoT.

#### <a name="query-parameters"></a>Parametry zapytania

- **autoryzowane:** do filtrowania tylko autoryzowanych i nieautoryzowanych urządzeń.

  **Przykłady:**

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących urządzenia.

#### <a name="device-fields"></a>Pola urządzenia

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **id** | Numeryczny | Nie | - |
| **ipAddresses** | Tablica JSON | Tak | Adresy IP (może to być więcej niż jeden adres w przypadku adresów internetowych lub urządzenia z dwiema karty sieciowe) |
| **name** | Ciąg | Nie | - |
| **Typu** | Ciąg | Nie | Unknown, Engineering Station,TAP, HMI, Historian, Domain Controller, DB Server, Wireless Access Point, Router, Switch, Server, Workstation, IP Camera, Printer, Firewall, Terminal Station, VPN Gateway, Internet lub Multiemisja i emisja |
| **macAddresses** | Tablica JSON | Tak | Adresy MAC (może to być więcej niż jeden adres w przypadku urządzenia z dwiema karty sieciowe) |
| **operatingSystem** | Ciąg | Tak | - |
| **engineeringStation** | Wartość logiczna | Nie | Prawda czy fałsz |
| **Skaner** | Wartość logiczna | Nie | Prawda czy fałsz |
| **Autoryzowanych** | Wartość logiczna | Nie | Prawda czy fałsz |
| **Dostawcy** | Ciąg | Tak | - |
| **Protokołów** | Tablica JSON | Tak | Obiekt protokołu |
| **Firmware** | Tablica JSON | Tak | Obiekt oprogramowania układowego |

#### <a name="protocol-fields"></a>Pola protokołu

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Nazwa** | Ciąg | Nie |  |
| **Adresy** | Tablica JSON | Tak | Wartości główne lub liczbowe |

#### <a name="firmware-fields"></a>Pola oprogramowania układowego

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Seryjny** | Ciąg | Nie | N/A lub wartość rzeczywista |
| **Modelu** | Ciąg | Nie | N/A lub wartość rzeczywista |
| **firmwareVersion** | Double | Nie | N/A lub wartość rzeczywista |
| **additionalData** | Ciąg | Nie | N/A lub wartość rzeczywista |
| **moduleAddress** | Ciąg | Nie | N/W lub wartość rzeczywista |
| **Rack** | Ciąg | Nie | N/W lub wartość rzeczywista |
| **Gniazdo** | Ciąg | Nie | N/W lub wartość rzeczywista |
| **Adres** | Ciąg | Nie | N/W lub wartość rzeczywista |

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices | curl -k -H "Autoryzacja: 1234b734a9244d54ab8d40aedddcabcd" https: <span> //127 <span> .0.0.1/api/v1/devices?authorized=true |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>Pobieranie informacji o połączeniu urządzenia — /api/v1/devices/connections

Ten interfejs API umożliwia zażądanie listy wszystkich połączeń dla każdego urządzenia.

#### <a name="method"></a>Metoda

**Pobierz**

#### <a name="query-parameters"></a>Parametry zapytania

Jeśli nie ustawisz parametrów zapytania, zostaną zwrócone wszystkie połączenia urządzenia.

**Przykład:**

`/api/v1/devices/connections`

- **deviceId:** filtruj według określonego identyfikatora urządzenia, aby wyświetlić jego połączenia.

  **Przykład:**

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes:** czas od teraz wstecz, według minuty, w którym połączenia były aktywne.

  **Przykład:**

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore:** filtruj tylko te połączenia, które zostały wykryte przed określoną godziną (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter:** filtruj tylko połączenia wykryte po określonej godzinie (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących połączenia urządzeń.

#### <a name="fields"></a>Pola

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **firstDeviceId** | Numeryczny | Nie | - |
| **secondDeviceId** | Numeryczny | Nie | - |
| **lastSeen** | Numeryczny | Nie | Epoka (UTC) |
| **Odkrył** | Numeryczny | Nie | Epoka (UTC) |
| **Porty** | Tablica liczb | Nie | - |
| **Protokołów** | Tablica JSON | Nie | Pole Protokół |

#### <a name="protocol-field"></a>Pole Protokół

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **name** | Ciąg | Nie | - |
| **Polecenia** | Tablica ciągów | Nie | - |

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

> [!div class="mx-tdBreakAll"]
> | Typ | Interfejsy API | Przykład |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/connections |
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/ <deviceId> /connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" <span> 'https:/ /127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=159450986000' |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>Pobieranie informacji dotyczących dokumentów CVE — /api/v1/devices/cves

Ten interfejs API umożliwia zażądanie listy wszystkich znanych luk CVE odnalezionych na urządzeniach w sieci.

#### <a name="method"></a>Metoda

**Pobierz**

#### <a name="query-parameters"></a>Parametry zapytania

Domyślnie ten interfejs API udostępnia listę wszystkich adresów IP urządzeń z listą CVE, maksymalnie 100 najlepiej punktowanych plików CVE dla każdego adresu IP.

**Przykład:**

`/api/v1/devices/cves`

- **deviceId:** filtruj według określonego adresu IP urządzenia, aby uzyskać maksymalnie 100 najlepiej punktowanych cve zidentyfikowanych na tym konkretnym urządzeniu.

  **Przykład:**

  `/api/v1/devices/<ipAddress>/cves`

- **top:** ile najlepiej punktowych cve ma zostać pobranych dla każdego adresu IP urządzenia.

  **Przykład:**

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON, które reprezentują wartości CVE zidentyfikowane na adresach IP.

#### <a name="fields"></a>Pola

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **cveId** | Ciąg | Nie | - |
| **Ipaddress** | Ciąg | Nie | Adres IP |
| **Ocena** | Ciąg | Nie | 0.0 - 10.0 |
| **attackVector** | Ciąg | Nie | Sieć, sąsiadująca sieć, lokalna lub fizyczna |
| **Opis** | Ciąg | Nie | - |

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves | curl -k -H "Autoryzacja: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/cves |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/ <deviceIpAddress> /cves?top= | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50 |

### <a name="retrieve-alert-information---apiv1alerts"></a>Pobieranie informacji o alertach — /api/v1/alerts

Ten interfejs API umożliwia zażądanie listy wszystkich alertów wykrytych przez czujnik usługi Defender dla IoT.

#### <a name="method"></a>Metoda

**Pobierz**

#### <a name="query-parameters"></a>Parametry zapytania

- **state:** aby filtrować tylko obsługiwane lub nieobsługiwane alerty.

  **Przykład:**

  `/api/v1/alerts?state=handled`

- **fromTime:** filtrowanie alertów utworzonych w określonym czasie (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** filtrowanie alertów utworzonych tylko przed określoną godziną (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/alerts?toTime=<epoch>`

- **typ**: Aby filtrować alerty według określonego typu. Istniejące typy do filtrowania: nieoczekiwane nowe urządzenia, rozłączenia.

  **Przykład:**

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących alerty.

#### <a name="alert-fields"></a>Pola alertów

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **ID (Identyfikator)** | Numeryczny | Nie | - |
| **Czas** | Numeryczny | Nie | Epoka (UTC) |
| **title** | Ciąg | Nie | - |
| **Komunikat** | Ciąg | Nie | - |
| **Ważności** | Ciąg | Nie | Ostrzeżenie, Pomocniczy, Główny lub Krytyczny |
| **Silnika** | Ciąg | Nie | Naruszenie protokołu, naruszenie zasad, złośliwe oprogramowanie, anomalia lub działanie |
| **sourceDevice** | Numeryczny | Tak | Identyfikator urządzenia |
| **destinationDevice** | Numeryczny | Tak | Identyfikator urządzenia |
| **sourceDeviceAddress** | Numeryczny | Tak | IP, MAC, Null |
| **destinationDeviceAddress** | Numeryczny | Tak | IP, MAC, Null |
| **remediationSteps (Kroki korygowania)** | Ciąg | Tak | Kroki korygowania opisane w alercie |
| **additionalInformation** | Obiekt dodatkowych informacji | Tak | - |

Należy pamiętać, że /api/v2/ jest wymagany do następujących informacji:

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps (Kroki korygowania)

#### <a name="additional-information-fields"></a>Pola dodatkowych informacji

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Opis** | Ciąg | Nie | - |
| **Informacji** | Tablica JSON | Nie | Ciąg |

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

> [!div class="mx-tdBreakAll"]
> | Typ | Interfejsy API | Przykład |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" <span> 'https:/ /127.0.0.1/api/v1/alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections' |

### <a name="retrieve-timeline-events---apiv1events"></a>Pobieranie zdarzeń osi czasu — /api/v1/events

Ten interfejs API umożliwia zażądanie listy zdarzeń zgłoszonych na osi czasu zdarzeń.

#### <a name="method"></a>Metoda

**Pobierz**

#### <a name="query-parameters"></a>Parametry zapytania

- **minutesTimeFrame:** ramy czasowe od teraz wstecz, minuty, w których zdarzenia zostały zgłoszone.

  **Przykład:**

  `/api/v1/events?minutesTimeFrame=20`

- **typ**: Aby filtrować listę zdarzeń według określonego typu.

  **Przykłady:**

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących alerty.

#### <a name="event-fields"></a>Pola zdarzeń

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|--|
| **Sygnatury czasowej** | Numeryczny | Nie | Epoka (UTC) |
| **title** | Ciąg | Nie | - |
| **Ważności** | Ciąg | Nie | INFORMACJE, POWIADOMIENIA lub ALERTY |
| **Właściciel** | Ciąg | Tak | Jeśli zdarzenie zostało utworzone ręcznie, to pole będzie zawierać nazwę użytkownika, która utworzyła zdarzenie |
| **zawartość** | Ciąg | Nie | - |

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" <span> 'https:/ /127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>Pobieranie informacji o lukach w zabezpieczeniach — /api/v1/reports/vulnerabilities/devices

Ten interfejs API umożliwia żądanie wyników oceny luk w zabezpieczeniach dla każdego urządzenia.

#### <a name="method"></a>Metoda

**Pobierz**

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
| **name** | Ciąg | Nie | - |
| **ipAddresses** | Tablica JSON | Nie | - |
| **securityScore** | Numeryczny | Nie | - |
| **Dostawcy** | Ciąg | Tak |  |
| **firmwareVersion** | Ciąg | Tak | - |
| **Modelu** | Ciąg | Tak | - |
| **isWirelessAccessPoint** | Wartość logiczna | Nie | Prawda czy fałsz |
| **operatingSystem** | Obiekt systemu operacyjnego | Tak | - |
| **Luki** | Obiekt luki w zabezpieczeniach | Tak | - |

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
| **Programy antywirusowe** | Tablica JSON | Tak | Nazwy programów antywirusowych |
| **plainTextPasswords** | Tablica JSON | Tak | Obiekty haseł |
| **Remoteaccess** | Tablica JSON | Tak | Obiekty dostępu zdalnego |
| **isBackupServer** | Wartość logiczna | Nie | Prawda czy fałsz |
| **openedPorts** | Tablica JSON | Tak | Otwarte obiekty portów |
| **isEngineeringStation** | Wartość logiczna | Nie | Prawda czy fałsz |
| **isKnownScanner** | Wartość logiczna | Nie | Prawda czy fałsz |
| **cves** | Tablica JSON | Tak | Obiekty CVE |
| **isUnauthorized** | Wartość logiczna | Nie | Prawda czy fałsz |
| **malwareIndicationsDetected** | Wartość logiczna | Nie | Prawda czy fałsz |
| **weakAuthentication** | Tablica JSON | Tak | Wykryte aplikacje używające słabego uwierzytelniania |

#### <a name="password-fields"></a>Pola hasła

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **hasło** | Ciąg | Nie | - |
| **Protokół** | Ciąg | Nie | - |
| **Siły** | Ciąg | Nie | Bardzo słaba, słaba, średnia lub silna |

#### <a name="remote-access-fields"></a>Pola dostępu zdalnego

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Portu** | Numeryczny | Nie | - |
| **Transportu** | Ciąg | Nie | TCP lub UDP |
| **Klienta** | Ciąg | Nie | Adres IP |
| **clientSoftware** | Ciąg | Nie | SSH, VNC, pulpit zdalny lub program Team Viewer |

#### <a name="open-port-fields"></a>Otwieranie pól portów

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Portu** | Numeryczny | Nie | - |
| **Transportu** | Ciąg | Nie | TCP lub UDP |
| **Protokół** | Ciąg | Tak | - |
| **isConflictingWithFirewall** | Wartość logiczna | Nie | Prawda czy fałsz |

#### <a name="cve-fields"></a>Pola CVE

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **ID (Identyfikator)** | Ciąg | Nie | - |
| **Ocena** | Numeryczny | Nie | Double |
| **Opis** | Ciąg | Nie | - |

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices | curl -k -H "Autoryzacja: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>Pobieranie luk w zabezpieczeniach — /api/v1/reports/vulnerabilities/security

Ten interfejs API umożliwia żądanie wyników ogólnej oceny luk w zabezpieczeniach. Ta ocena zapewnia wgląd w poziom zabezpieczeń systemu.

Ta ocena jest oparta na ogólnych informacjach o sieci i systemie, a nie na ocenie określonego urządzenia.

#### <a name="method"></a>Metoda

**Pobierz**

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Obiekt JSON reprezentujący ocenione wyniki. Każdy klucz może mieć wartość null. W przeciwnym razie będzie on zawierać obiekt JSON z kluczami nienadaymiaymi się do wartości null.

### <a name="result-fields"></a>Pola wyników

**Klucze**

**unauthorizedDevices**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **Adres** | Ciąg | Adres IP |
| **name** | Ciąg | - |
| **firstDetectionTime** | Numeryczny | Epoka (UTC) |
| lastSeen | Numeryczny | Epoka (UTC) |

**illegalTrafficByFirewallRules**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **Serwera** | Ciąg | Adres IP |
| **Klienta** | Ciąg | Adres IP |
| **Portu** | Numeryczny | - |
| **Transportu** | Ciąg | TCP, UDP lub ICMP |

**weakFirewallRules**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **sources** | Tablica źródeł JSON. Każde źródło może mieć dowolny z czterech formatów. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Miejsc** | Tablica JSON miejsc docelowych. Każdy obiekt docelowy może mieć dowolny z czterech formatów. | "Any", "ip address (Host)", "from ip-to ip (RANGE)", "ip address, subnet mask (NETWORK)" |
| **Porty** | Tablica portów JSON w dowolnym z trzech formatów | "Any", "port (protokół, jeśli wykryto)", "z portu do portu (protokół, jeśli wykryto)" |

**accessPoints**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **adres macAddress** | Ciąg | Adres MAC |
| **Dostawcy** | Ciąg | Nazwa dostawcy |
| **Ipaddress** | Ciąg | Adres IP lub nie dotyczy |
| **name** | Ciąg | Nazwa urządzenia lub Nie ma |
| **Bezprzewodowy** | Ciąg | Nie, Podejrzenie lub Tak |

**connectionsBetweenSubnets**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **Serwera** | Ciąg | Adres IP |
| **Klienta** | Ciąg | Adres IP |

**industrialMalwareIndicators**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **detectionTime** | Numeryczny | Epoka (UTC) |
| **alertMessage** | Ciąg | - |
| **Opis** | Ciąg | - |
| **Urządzeń** | Tablica JSON | Nazwy urządzeń | 

**internetConnections**

| Nazwa pola | Typ | Lista wartości |
| ---------- | ---- | -------------- |
| **internalAddress** | Ciąg | Adres IP |
| **Autoryzowanych** | Wartość logiczna | Tak lub Nie | 
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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>Pobieranie luk w zabezpieczeniach operacyjnych — /api/v1/reports/vulnerabilities/operational

Ten interfejs API umożliwia żądanie wyników ogólnej oceny luk w zabezpieczeniach. Ta ocena zapewnia wgląd w stan operacyjny sieci. Jest on oparty na ogólnych informacjach o sieci i systemie, a nie na ocenie określonego urządzenia.

#### <a name="method"></a>Metoda

**Pobierz**

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Obiekt JSON reprezentujący ocenione wyniki. Każdy klucz zawiera tablicę wyników JSON.

#### <a name="result-fields"></a>Pola wyników

**Klucze**

**backupServer**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| **Źródła** | Ciąg | Adres IP |
| **Docelowy** | Ciąg | Adres IP |
| **Portu** | Numeryczny | - |
| **Transportu** | Ciąg | TCP lub UDP |
| **backupMaximalInterval** | Ciąg | - |
| **lastSeenBackup** | Numeryczny | Epoka (UTC) |

**ipNetworks**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| **adres e-mail** | Numeryczny | - |
| **network** | Ciąg | Adres IP |
| **maska** | Ciąg | Maska podsieci |

**protocolProblems (problemy z protokołem)**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| **Protokół** | Ciąg | - |
| **Adresy** | Tablica JSON | Adresy IP |
| **Alert** | Ciąg | - |
| **reportTime** | Numeryczny | Epoka (UTC) |

**protocolDataVolumes**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| Protokół | Ciąg | - |
| wolumin | Ciąg | "numer woluminu MB" |

**Rozłączenia**

| Nazwa pola | Typ | Lista wartości |
|--|--|--|
| **assetAddress** | Ciąg | Adres IP |
| **assetName (nazwa zasobu)** | Ciąg | - |
| **lastDetectionTime** | Numeryczny | Epoka (UTC) |
| **backToNormalTime** | Numeryczny | Epoka (UTC) |     

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/v1/reports/vulnerabilities/operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>Weryfikowanie poświadczeń użytkownika — /api/external/authentication/validation

Ten interfejs API umożliwia zweryfikowanie nazwy użytkownika i hasła usługi Defender dla IoT. Wszystkie role użytkownika usługi Defender dla IoT mogą współpracować z interfejsem API.

Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender dla IoT.

#### <a name="method"></a>Metoda

**POST**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="query-parameters"></a>Parametry zapytania

| **Nazwa** | **Typ** | **Nullable** |
|--|--|--|
| **Nazwę użytkownika** | Ciąg | Nie |
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

Ciąg komunikatu ze szczegółami stanu operacji:

- **Powodzenie — msg:** uwierzytelnianie zakończyło się pomyślnie

- **Niepowodzenie — błąd:** Weryfikacja poświadczeń nie powiodła się

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/api/external/authentication/validation |

### <a name="change-password---externalauthenticationset_password"></a>Zmienianie hasła — /external/authentication/set_password

Ten interfejs API umożliwia użytkownikom zmienianie własnych haseł. Wszystkie role użytkowników usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender dla IoT.

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

Ciąg komunikatu ze szczegółami stanu operacji:

- **Powodzenie — msg:** hasło zostało zastąpione

- **Niepowodzenie — błąd:** Niepowodzenie uwierzytelniania użytkownika

- **Błąd — błąd:** Hasło nie jest zgodne z zasadami zabezpieczeń

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

| **Nazwa** | **Typ** | **Nullable** |
|--|--|--|
| **Nazwę użytkownika** | Ciąg | Nie |
| **hasło** | Ciąg | Nie |
| **new_password** | Ciąg | Nie |

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/api/external/authentication/set_password | curl -k -d '{"username": "myUser","password": " 1234@abcd ","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualizacja hasła użytkownika przez administratora systemu — /external/authentication/set_password_by_admin

Ten interfejs API umożliwia administratorom systemu zmianę haseł dla określonych użytkowników. Role użytkownika administratora usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender dla IoT.

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

Ciąg komunikatu ze szczegółami stanu operacji:

- **Powodzenie — msg:** hasło zostało zastąpione

- **Niepowodzenie — błąd:** Niepowodzenie uwierzytelniania użytkownika

- **Błąd — błąd:** Użytkownik nie istnieje

- **Niepowodzenie — błąd:** Hasło nie jest zgodne z zasadami zabezpieczeń

- **Niepowodzenie — błąd:** Użytkownik nie ma uprawnień do zmiany hasła

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

| **Nazwa** | **Typ** | **Nullable** |
|--|--|--|
| **admin_username** | Ciąg | Nie |
| **admin_password** | Ciąg | Nie |
| **Nazwę użytkownika** | Ciąg | Nie |
| **new_password** | Ciąg | Nie |

#### <a name="curl-command"></a>Polecenie narzędzia Curl

> [!div class="mx-tdBreakAll"]
> | Typ | Interfejsy API | Przykład |
> |--|--|--|
> | POST | curl -k -d "{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}" -H "Content-Type: application/json" https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": " 1234@abcd ","username": "myUser","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/api/external/authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>Specyfikacje interfejsu API lokalnej konsoli zarządzania ##

W tej sekcji opisano interfejsy API lokalnej konsoli zarządzania dla:
- Wykluczenia alertów
- Informacje o urządzeniu
- Informacje o alertach

### <a name="alert-exclusions"></a>Wykluczenia alertów ###

Zdefiniuj warunki, w których alerty nie będą wysyłane. Na przykład zdefiniuj i zaktualizuj godziny zatrzymania i rozpoczęcia, urządzenia lub podsieci, które powinny zostać wykluczone podczas wyzwalania alertów, lub usługę Defender dla aparatów IoT, które powinny zostać wykluczone. Na przykład podczas okna obsługi można zatrzymać dostarczanie wszystkich alertów, z wyjątkiem alertów dotyczących złośliwego oprogramowania na urządzeniach krytycznych. Elementy, które definiujesz w tym miejscu,  są wyświetlane w oknie wykluczeń alertów lokalnej konsoli zarządzania jako reguły wykluczeń tylko do odczytu.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **Przykład odpowiedzi**

- **Odpowiedzi:**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>Zmienianie hasła — /external/authentication/set_password 

Ten interfejs API umożliwia użytkownikom zmienianie własnych haseł. Wszystkie role użytkowników usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender dla IoT.

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualizacja hasła użytkownika przez administratora systemu — /external/authentication/set_password_by_admin 

Ten interfejs API umożliwia administratorom systemu zmianę haseł dla określonych użytkowników. Role użytkownika administratora usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender dla IoT.

### <a name="retrieve-device-information---externalv1devices"></a>Pobieranie informacji o urządzeniu — /external/v1/devices ###

Ten interfejs API żąda listy wszystkich urządzeń wykrytych przez czujniki usługi Defender dla IoT, które są połączone z lokalną konsolą zarządzania.

#### <a name="method"></a>Metoda

**Pobierz**

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących urządzenia.

#### <a name="query-parameters"></a>Parametry zapytania

- **autoryzowane:** do filtrowania tylko autoryzowanych i nieautoryzowanych urządzeń.

- **siteId:** aby filtrować tylko urządzenia związane z określonymi lokacjami.

- **zoneId:** aby filtrować tylko urządzenia związane z określonymi strefami. [1](#1)

- **sensorId:** filtrowanie tylko urządzeń wykrytych przez określone czujniki. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *Możesz nie mieć identyfikatora witryny i strefy. W takim przypadku należy odpytować wszystkie urządzenia, aby pobrać identyfikator witryny i strefy.*

#### <a name="query-parameters-example"></a>Przykład parametrów zapytania

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>Pola urządzenia

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **sensorId** | Numeryczny | Nie | - |
| **zoneId** | Numeryczny | Tak | - |
| **siteId** | Numeryczny | Tak | - |
| **ipAddresses** | Tablica JSON | Tak | Adresy IP (może to być więcej niż jeden adres w przypadku adresów internetowych lub urządzenia z dwiema karty sieciowe) |
| **name** | Ciąg | Nie | - |
| **Typu** | Ciąg | Nie | Unknown, Engineering Station,TAP, HMI, Historian, Domain Controller, DB Server, Wireless Access Point, Router, Switch, Server, Workstation, IP Camera, Printer, Firewall, Terminal Station, VPN Gateway, Internet lub Multiemisja i emisja |
| **macAddresses** | Tablica JSON | Tak | Adresy MAC (może to być więcej niż jeden adres w przypadku urządzenia z dwiema karty sieciowe) |
| **operatingSystem** | Ciąg | Tak | - |
| **engineeringStation** | Wartość logiczna | Nie | Prawda czy fałsz |
| **Skaner** | Wartość logiczna | Nie | Prawda czy fałsz |
| **Autoryzowanych** | Wartość logiczna | Nie | Prawda czy fałsz |
| **Dostawcy** | Ciąg | Tak | - |
| **Protokoły** | Tablica JSON | Tak | Obiekt protokołu |
| **Firmware** | Tablica JSON | Tak | Obiekt oprogramowania układowego |

#### <a name="protocol-fields"></a>Pola protokołu

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| Nazwa | Ciąg | Nie | - |
| Adresy | Tablica JSON | Tak | Wartości główne lub liczbowe |

#### <a name="firmware-fields"></a>Pola oprogramowania układowego

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Seryjny** | Ciąg | Nie | N/A lub wartość rzeczywista |
| **Modelu** | Ciąg | Nie | N/A lub wartość rzeczywista |
| **firmwareVersion** | Double | Nie | N/A lub wartość rzeczywista |
| **additionalData** | Ciąg | Nie | N/A lub wartość rzeczywista |
| **moduleAddress** | Ciąg | Nie | N/A lub wartość rzeczywista |
| **Rack** | Ciąg | Nie | N/A lub wartość rzeczywista |
| **Gniazdo** | Ciąg | Nie | N/W lub wartość rzeczywista |
| **Adres** | Ciąg | Nie | N/W lub wartość rzeczywista |

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized=' | curl -k -H "Autoryzacja: 1234b734a9244d54ab8d40aedddcabcd" "https:/ <span> /127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true" |

### <a name="retrieve-alert-information---externalv1alerts"></a>Pobieranie informacji o alertach — /external/v1/alerts

Ten interfejs API umożliwia pobranie wszystkich lub przefiltrowanych alertów z lokalnej konsoli zarządzania.

#### <a name="method"></a>Metoda

**Pobierz**

#### <a name="query-parameters"></a>Parametry zapytania

- **state:** aby filtrować tylko obsługiwane i nieobsługiwane alerty.

  **Przykład:**

  `/api/v1/alerts?state=handled`

- **fromTime:** filtrowanie alertów utworzonych w określonym czasie (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime:** filtrowanie alertów utworzonych tylko przed określoną godziną (w milisekundach, UTC).

  **Przykład:**

  `/api/v1/alerts?toTime=<epoch>`

- **siteId:** lokacja, w której został wykryty alert.
- **zoneId:** strefa, w której został wykryty alert.
- **sensor:** czujnik, na którym został wykryty alert.

*Być może nie masz identyfikatora witryny i strefy. W takim przypadku należy odpytować wszystkie urządzenia, aby pobrać identyfikator witryny i strefy.*

#### <a name="alert-fields"></a>Pola alertów 

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **ID (Identyfikator)** | Numeryczny | Nie | - |
| **Czas** | Numeryczny | Nie | Epoka (UTC) |
| **title** | Ciąg | Nie | - |
| **Komunikat** | Ciąg | Nie | - |
| **Ważności** | Ciąg | Nie | Ostrzeżenie, Pomocniczy, Główny lub Krytyczny |
| **Silnika** | Ciąg | Nie | Naruszenie protokołu, naruszenie zasad, złośliwe oprogramowanie, anomalia lub działanie |
| **sourceDevice** | Numeryczny | Tak | Identyfikator urządzenia |
| **destinationDevice** | Numeryczny | Tak | Identyfikator urządzenia |
| **sourceDeviceAddress** | Numeryczny | Tak | IP, MAC, Null |
| **destinationDeviceAddress** | Numeryczny | Tak | IP, MAC, Null |
| **remediationSteps (Kroki korygowania)** | Ciąg | Tak | Kroki korygowania wyświetlane w alercie|
| **sensorName** | Ciąg | Tak | Nazwa czujnika zdefiniowanego przez użytkownika w konsoli|
|**Nazwa_strefy** | Ciąg | Tak | Nazwa strefy skojarzonej z czujnikiem w konsoli|
| **Sitename** | Ciąg | Tak | Nazwa lokacji skojarzonej z czujnikiem w konsoli programu |
| **additionalInformation** | Obiekt dodatkowych informacji | Tak | - |

Należy pamiętać, że /api/v2/ jest wymagany do następujących informacji:

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps (Kroki korygowania)
- sensorName (nazwa czujnika)
- Nazwa_strefy
- Sitename

#### <a name="additional-information-fields"></a>Pola dodatkowych informacji

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **Opis** | Ciąg | Nie | - |
| **Informacji** | Tablica JSON | Nie | Ciąg |

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

#### <a name="curl-command"></a>Polecenie narzędzia Curl

> [!div class="mx-tdBreakAll"]
> | Typ | Interfejsy API | Przykład |
> |--|--|--|
> | GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor=' | curl -k -H "Autoryzacja: 1234b734a9244d54ab8d40aedddcabcd" "https:/ <span> /127.0.0.1/external/v1/alerts?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1' |

### <a name="qradar-alerts"></a>Alerty QRadar

Integracja aplikacji QRadar z usługą Defender dla IoT ułatwia identyfikowanie alertów generowanych przez usługę Defender dla IoT i wykonywanie akcji za pomocą tych alertów. Aplikacja QRadar odbiera dane z usługi Defender dla IoT, a następnie kontaktuje się ze składnikiem lokalnej konsoli zarządzania publicznego interfejsu API.

Aby wysłać dane wykryte przez usługę Defender for IoT do aplikacji QRadar, zdefiniuj regułę przekazywania w systemie usługi Defender dla IoT i wybierz opcję Zdalna obsługa **alertów** pomocy technicznej.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="Edytuj reguły przekazywania zgodnie z potrzebami.":::

Po wybraniu tej opcji podczas konfigurowania reguł przekazywania w aplikacji QRadar zostaną wyświetlone następujące dodatkowe pola:

- **UUID:** unikatowy identyfikator alertu, taki jak 1-1555245116250.

- **Lokacja:** lokacja, w której został wykryty alert.

- **Strefa:** strefa, w której został odnaleziony alert.

Przykład ładunku wysyłanego do QRadar:

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/ &lt; UUID&gt;

#### <a name="method"></a>Metoda

**PUT**

#### <a name="request-type"></a>Typ żądania

**JSON**

#### <a name="request-content"></a>Żądanie zawartości

Obiekt JSON reprezentujący akcję do wykonania dla alertu zawierającego identyfikator UUID.

#### <a name="action-fields"></a>Pola akcji

| Nazwa | Typ | Dopuszczający wartość null | Lista wartości |
|--|--|--|--|
| **action** | Ciąg | Nie | handle lub handleAndLearn |

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
| **zawartość/błąd** | Ciąg | Nie | Jeśli żądanie zostanie pomyślnie wyświetlone, zostanie wyświetlona właściwość content. W przeciwnym razie zostanie wyświetlona właściwość error. |

#### <a name="possible-content-values"></a>Możliwe wartości zawartości

| Kod stanu | Wartość zawartości | Opis |
|--|--|--|
| 200 | Żądanie aktualizacji alertu zostało zakończone pomyślnie. | Żądanie aktualizacji zostało zakończone pomyślnie. Brak komentarzy. |
| 200 | Alert został już obsłużony **(dojście**). | Alert został już obsłużony po otrzymaniu żądania dojścia do alertu.<br />Alert pozostaje **obsługiwany.** |
| 200 | Alert został już obsłużony i nauczony (**handleAndLearn**). | Alert został już obsłużony i poznany po otrzymaniu żądania **obsługi i** uczenia się.<br />Alert pozostaje w stanie **handledAndLearn.** |
| 200 | Alert został już obsłużony **(obsłużony).**<br />W przypadku alertu wykonano obsługę i uczenia **(handleAndLearn).** | Alert został już obsłużony po otrzymaniu żądania **obsługiandLearn.**<br />Alert staje się **handleAndLearn**. |
| 200 | Alert został już obsłużony i nauczony (**handleAndLearn**). Ignorowane żądanie dojścia. | Alert został już **obsłużny po** otrzymaniu żądania obsługi alertu. Alert pozostaje obsługiwany **przez iWięcej.** |
| 500 | Nieprawidłowa akcja. | Wysłana akcja nie jest prawidłową akcją do wykonania dla alertu. |
| 500 | Wystąpił nieoczekiwany błąd. | Wystąpił nieoczekiwany błąd. Aby rozwiązać ten problem, skontaktuj się z pomocą techniczną. |
| 500 | Nie można wykonać żądania, ponieważ nie znaleziono alertu dla tego UUID. | Nie znaleziono określonego alertu UUID w systemie. |

#### <a name="response-example"></a>Przykład odpowiedzi

**Pomyślne**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**Nieudanych**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| PUT | curl -k -X PUT -d '{"action": " <ACTION> "}" -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID> | curl -k -X PUT -d '{"action": "handle"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/alerts/1-1594550943000 |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>Wykluczenia alertów (okno obsługi) — /external/v1/maintenanceWindow

Zdefiniuj warunki, w których alerty nie będą wysyłane. Na przykład zdefiniuj i zaktualizuj godziny zatrzymania i rozpoczęcia, urządzenia lub podsieci, które powinny zostać wykluczone podczas wyzwalania alertów, lub usługę Defender dla aparatów IoT, które powinny zostać wykluczone. Na przykład w oknie obsługi można zatrzymać dostarczanie alertów dla wszystkich alertów, z wyjątkiem alertów złośliwego oprogramowania na urządzeniach krytycznych.

Interfejsy API, które definiujesz w tym miejscu, są wyświetlane w oknie wykluczeń **alertów** lokalnej konsoli zarządzania jako reguła wykluczania tylko do odczytu.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="Okno Wykluczenia alertów z listą wszystkich reguł wykluczeń. ":::

#### <a name="method---post"></a>Metoda — POST

#### <a name="query-parameters"></a>Parametry zapytania

- **ticketId:** definiuje identyfikator biletu konserwacji w systemach użytkownika.

- **ttl:** definiuje czas wygaśnięcia (czas wygaśnięcia), czyli czas trwania okna obsługi w minutach. Po upływie czasu, który definiuje ten parametr, system automatycznie rozpoczyna wysyłanie alertów.

- **aparaty:** określa, z którego aparatu zabezpieczeń mają być pomijane alerty podczas procesu konserwacji:

   - Anomalia

   - Złośliwego oprogramowania

   - Operacyjne

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds:** określa, z którego czujnika usługi Defender dla IoT mają być pomijane alerty podczas procesu konserwacji. Jest to ten sam identyfikator pobrany z /api/v1/appliances (GET).

- **podsieci: określa,** z której podsieci mają być pomijane alerty podczas procesu konserwacji. Podsieć jest wysyłana w następującym formacie: 192.168.0.0/16.

#### <a name="error-codes"></a>Kody błędów

- **201 (Utworzono):** akcja została pomyślnie ukończona.

- **400 (Złe żądanie):** pojawia się w następujących przypadkach:

   - Parametr **TTL** nie jest liczbowy lub nie jest dodatni.

   - Parametr **podsieci został zdefiniowany** przy użyciu nieprawidłowego formatu.

   - Brak **parametru ticketId.**

   - Parametr **aparatu** nie pasuje do istniejących aparatów zabezpieczeń.

- **404 (Nie znaleziono):** jeden z czujników nie istnieje.

- **409 (konflikt):** identyfikator biletu jest połączony z innym otwartym oknem obsługi.

- **500 (Wewnętrzny błąd serwera):** dowolny inny nieoczekiwany błąd.

> [!NOTE]
> Upewnij się, że identyfikator biletu nie jest połączony z istniejącym otwartym oknem. Generowana jest następująca reguła wykluczania: Maintenance-{nazwa tokenu}-{identyfikator biletu}.

#### <a name="method---put"></a>Metoda — PUT

Umożliwia zaktualizowanie czasu trwania okna obsługi po rozpoczęciu procesu konserwacji przez zmianę **parametru czasu** wygaśnięcia. Nowa definicja czasu trwania zastępuje poprzednią.

Ta metoda jest przydatna, gdy chcesz ustawić dłuższy czas trwania niż aktualnie skonfigurowany czas trwania.

#### <a name="query-parameters"></a>Parametry zapytania

- **ticketId:** definiuje identyfikator biletu konserwacji w systemach użytkownika.

- **ttl:** definiuje czas trwania okna w minutach.

#### <a name="error-code"></a>Kod błędu

- **200 (OK)**: Akcja została pomyślnie ukończona.

- **400 (Złe żądanie):** pojawia się w następujących przypadkach:

   - Parametr **ttl** nie jest liczbowy lub nie jest dodatni.

   - Brak **parametru ticketId.**

   - Brak **parametru ttl.**

- **404 (Nie znaleziono):** identyfikator biletu nie jest połączony z otwartym oknem obsługi.

- **500 (Wewnętrzny błąd serwera)**: dowolny inny nieoczekiwany błąd.

> [!NOTE]
> Upewnij się, że identyfikator biletu jest połączony z istniejącym otwartym oknem.

#### <a name="method---delete"></a>Metoda — DELETE

Zamyka istniejące okno obsługi.

#### <a name="query-parameters"></a>Parametry zapytania

- **ticketId:** rejestruje identyfikator biletu konserwacji w systemach użytkownika.

#### <a name="error-code"></a>Kod błędu

- **200 (OK)**: Akcja została pomyślnie ukończona.

- **400 (Złe żądanie):** brak **parametru ticketId.**

- **404 (Nie znaleziono):** identyfikator biletu nie jest połączony z otwartym oknem obsługi.

- **500 (Wewnętrzny błąd serwera)**: dowolny inny nieoczekiwany błąd.

> [!NOTE]
> Upewnij się, że identyfikator biletu jest połączony z istniejącym otwartym oknem.

#### <a name="method---get"></a>Metoda — GET

Pobierz dziennik wszystkich akcji otwierania, zamykania i aktualizowania, które zostały wykonane w systemie podczas konserwacji. Dziennik można pobrać tylko dla okien obsługi, które były aktywne w przeszłości i zostały zamknięte.

#### <a name="query-parameters"></a>Parametry zapytania

- **fromDate**: filtruje dzienniki ze wstępnie zdefiniowanej daty i nowszych. Format to 2019-12-30.

- **toDate:** filtruje dzienniki do wstępnie zdefiniowanej daty. Format to 2019-12-30.

- **ticketId:** filtruje dzienniki związane z określonym identyfikatorem biletu.

- **tokenName:** filtruje dzienniki związane z określoną nazwą tokenu.

#### <a name="error-code"></a>Kod błędu 

- **200 (OK):** Akcja została pomyślnie ukończona.

- **400 (Nieprawidłowe żądanie):** format daty jest nieprawidłowy.

- **204 (Brak zawartości):** nie ma żadnych danych do pokazania.

- **500 (Wewnętrzny błąd serwera):** dowolny inny nieoczekiwany błąd.

#### <a name="response-type"></a>Typ odpowiedzi

**JSON**

#### <a name="response-content"></a>Zawartość odpowiedzi

Tablica obiektów JSON reprezentujących operacje okna obsługi.

#### <a name="response-structure"></a>Struktura odpowiedzi

| Nazwa | Typ | Komentarz | Dopuszczający wartość null |
|--|--|--|--|
| **Datetime** | Ciąg | Przykład: "2012-04-23T18:25:43.511Z" | nie |
| **ticketId** | Ciąg | Przykład: "9a5fe99c-d914-4bda-9332-307384fe40bf" | nie |
| **tokenName** | Ciąg | - | nie |
| **Silniki** | Tablica ciągów | - | tak |
| **sensorIds** | Tablica ciągów | - | tak |
| **Podsieci** | Tablica ciągów | - | tak |
| **czas wygaśnięcia** | Numeryczny | - | tak |
| **Operationtype** | Ciąg | Wartości to "OPEN", "UPDATE" i "CLOSE" | nie |

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| POST | curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2... ENGINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2... SENSOR_IDn>],"podsieci": [<SUBNET1, SUBNET2.... SUBNETn>]}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"], "sensorIds": ["5",""3"], "subnets": ["10.0.0.3"]}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| PUT | curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20"}' -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| DELETE | curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}' -H "Authorization: <AUTH_TOKEN>" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow | curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}" -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" https:/ <span> /127.0.0.1/external/v1/maintenanceWindow |
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" "https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aeddcabcd" <span> "https:/ /127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-0 1-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a' |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>Uwierzytelnianie poświadczeń użytkownika — /external/authentication/validation

Ten interfejs API umożliwia weryfikowanie poświadczeń użytkownika. Wszystkie role użytkowników usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender dla IoT.

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

Ciąg komunikatu ze szczegółami stanu operacji:

- **Powodzenie — msg:** uwierzytelnianie zakończyło się pomyślnie

- **Niepowodzenie — błąd:** Weryfikacja poświadczeń nie powiodła się

#### <a name="device-fields"></a>Pola urządzenia

| **Nazwa** | **Typ** | **Nullable** |
|--|--|--|
| **Nazwę użytkownika** | Ciąg | Nie |
| **hasło** | Ciąg | Nie |

#### <a name="response-example"></a>Przykład odpowiedzi

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| POST | curl -k -d '{"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation' | curl -k -d '{"username":"myUser","password":" 1234@abcd "}" "https:/ <span> /127.0.0.1/external/authentication/validation" |

### <a name="change-password---externalauthenticationset_password"></a>Zmienianie hasła — /external/authentication/set_password

Ten interfejs API umożliwia użytkownikom zmienianie własnych haseł. Wszystkie role użytkownika usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender dla IoT.

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

Ciąg komunikatu ze szczegółami stanu operacji:

- **Powodzenie — msg:** hasło zostało zastąpione

- **Niepowodzenie — błąd:** Niepowodzenie uwierzytelniania użytkownika

- **Błąd — błąd:** Hasło nie jest zgodne z zasadami zabezpieczeń

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

| **Nazwa** | **Typ** | **Nullable** |
|--|--|--|
| **Nazwę użytkownika** | Ciąg | Nie |
| **hasło** | Ciąg | Nie |
| **new_password** | Ciąg | Nie |

#### <a name="curl-command"></a>Polecenie narzędzia Curl

| Typ | Interfejsy API | Przykład |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password | curl -k -d '{"username": "myUser","password": " 1234@abcd ","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>Aktualizacja hasła użytkownika przez administratora systemu — /external/authentication/set_password_by_admin

Ten interfejs API umożliwia administratorom systemu zmianę haseł dla określonych użytkowników. Role użytkownika administratora usługi Defender dla IoT mogą współpracować z interfejsem API. Do korzystania z tego interfejsu API nie jest potrzebny token dostępu usługi Defender dla IoT.

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

Ciąg komunikatu ze szczegółami stanu operacji:

- **Powodzenie — msg:** hasło zostało zastąpione

- **Niepowodzenie — błąd:** Niepowodzenie uwierzytelniania użytkownika

- **Błąd — błąd:** Użytkownik nie istnieje

- **Błąd — błąd:** Hasło nie jest zgodne z zasadami zabezpieczeń

- **Błąd — błąd:** Użytkownik nie ma uprawnień do zmiany hasła

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

| **Nazwa** | **Typ** | **Nullable** |
|--|--|--|
| **admin_username** | Ciąg | Nie |
| **admin_password** | Ciąg | Nie |
| **Nazwę użytkownika** | Ciąg | Nie |
| **new_password** | Ciąg | Nie |

#### <a name="curl-command"></a>Polecenie narzędzia Curl

> [!div class="mx-tdBreakAll"]
> | Typ | Interfejsy API | Przykład |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json' https://<IP_ADDRESS>/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": " 1234@abcd ","username": "myUser","new_password": " abcd@1234 "}" -H 'Content-Type: application/json' https:/ <span> /127.0.0.1/external/authentication/set_password_by_admin |

## <a name="next-steps"></a>Następne kroki

- [Badanie wykryć czujników w spisie urządzeń](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [Badanie wszystkich wykryć czujników przedsiębiorstwa w spisie urządzeń](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
