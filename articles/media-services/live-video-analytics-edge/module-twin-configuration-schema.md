---
title: Schemat kodu JSON w module — platforma Azure
description: W tym temacie opisano schemat kodu JSON w module dla analizy filmów wideo na żywo na IoT Edge.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8bd86bdc2c8de9ee586e785db2074fa772100420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87053063"
---
# <a name="module-twin-json-schema"></a>Schemat JSON

Bliźniaczych reprezentacji urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia, w tym metadane, konfiguracje i warunki. Usługa Azure IoT Hub utrzymuje bliźniaczą reprezentację urządzenia dla każdego urządzenia połączonego z usługą IoT Hub. Szczegółowe wyjaśnienie można znaleźć [w temacie Omówienie i Używanie modułu bliźniaczych reprezentacji w IoT Hub](../../iot-hub/iot-hub-devguide-module-twins.md)

W tym temacie opisano schemat kodu JSON w module dla analizy filmów wideo na żywo na IoT Edge.

> [!NOTE]
> Aby uzyskać prawa dostępu do zasobów usługi Media Services i do interfejsu API usługi Media Services, należy się najpierw uwierzytelnić. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do interfejsu API Azure Media Services](../latest/media-services-apis-overview.md#accessing-the-azure-media-services-api).

## <a name="module-twin-properties"></a>Właściwości sznurka modułu

Usługa Analiza filmów wideo na żywo na IoT Edge uwidacznia następujące właściwości sznurka modułu. 

|Właściwość |Wymagany |Dynamiczny |Opis |
|---|---|---|---|
|applicationDataDirectory |Tak |Nie |Ścieżka do zainstalowanego woluminu w celu utrwalenia konfiguracji. |
|azureMediaServicesArmId |Tak |Nie |Unikatowy identyfikator zarządzania zasobami platformy Azure dla konta Media Services.|
|aadTenantId |Tak |Nie |Identyfikator dzierżawy usługi Azure AD klienta.|
|aadServicePrincipalAppId |Tak |Tak |Klient utworzył usługę Azure AD AppId.|
|aadServicePrincipalCertificate |Opcję<sup>*</sup>  |Tak |Klient utworzył certyfikat AppId usługi Azure AD.|
|aadServicePrincipalPassword |Opcję<sup>*</sup>  |Tak |Klient utworzył hasło AppId usługi Azure AD.|
|aadEndpoint |Nie |Nie |Punkt końcowy usługi Azure AD związany z chmurą. <br/>Wartooć `https://login.microsoftonline.com` |
|aadResourceId |Nie |Nie |Specyficzne dla chmury grupy odbiorców/zasobów usługi Azure AD <br/>Wartooć `https://management.core.windows.net/` |
|armEndpoint |Nie |Nie |Punkt końcowy zarządzania zasobami platformy Azure związany z chmurą. <br/>Wartooć `https://management.azure.com/` |
|diagnosticsLevel |Nie |Tak |Poziom szczegółowości zdarzeń: <br/>Informacje & # x02758; Ostrzeżenie & # x02758; Błąd & # x02758; Krytyczny & # x02758; Dawaj |
|diagnosticsEventsOutputName |Nie |Tak |Dane wyjściowe centrum dla zdarzeń diagnostycznych. <br/>(Puste oznacza, że Diagnostyka nie jest publikowana)|
|operationalEventsOutputName|Nie|Tak|Dane wyjściowe centrum dla zdarzeń operacyjnych.<br/>(Puste oznacza, że zdarzenia operacyjne nie są publikowane)
|logLevel|Nie|Tak|Jeden z poniższych programów: <br/>& # x000B7; Pełne<br/>& # x000B7; Informacje (domyślne)<br/>& # x000B7; Wyświetlania<br/>& # x000B7; Porn<br/>& # x000B7; Dawaj|
|logCategories|Nie|Tak|Rozdzielana przecinkami lista następujących elementów: Application, MediaPipeline, Events <br/>Domyślne: aplikacja, zdarzenia|
|debugLogsDirectory|Nie|Tak|Katalog dla dzienników debugowania. Jeśli dzienniki są generowane, jeśli nie są obecne, Dzienniki debugowania są wyłączone.

<sup>*</sup>MUSISZ podać certyfikat lub hasło jednostki usługi. 

Właściwości dynamiczne można aktualizować bez ponownego uruchamiania modułu. Możesz uzyskać wartości dla kilku z tych właściwości, postępując zgodnie z instrukcjami w artykule [Uzyskiwanie dostępu do Media Services interfejsu API](../latest/access-api-howto.md) . 

Zapoznaj się z artykułem dotyczącym [monitorowania i rejestrowania](monitoring-logging.md) , aby uzyskać więcej informacji na temat roli opcjonalnych ustawień diagnostycznych.

```
{ 
    "properties.desired": { 
        // Required 
        "applicationDataDirectory": "/var/lib/azuremediaservices", 
        "azureMediaServicesArmId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/microsoft.media/mediaservices/<ams_account>", 
        "aadTenantId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalAppId": "00000000-0000-0000-0000-000000000000", 
        "aadServicePrincipalPassword": "{Service principal password}", 

        // Optional API Access 
        "aadEndpoint": "https://<aad-endpoint>", 
        "aadResourceId": "https://management.core.windows.net/", 
        "armEndpoint": "https://management.azure.com/", 
        
        // Optional Diagnostics 
        "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
        "operationalEventsOutputName": "lvaEdgeOperational",
        "logLevel": "Information",
        "logCategories": "Application,Events"
    } 
} 
```

## <a name="next-steps"></a>Następne kroki

[Metody bezpośrednie](direct-methods.md)
