---
title: Aktywność elementu Webhook w fabryce danych platformy Azure
description: Działanie elementu webhook nie kontynuuje wykonywania potoku, dopóki nie sprawdza poprawności dołączonego zestawu danych z określonymi kryteriami określonymi przez użytkownika.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417882"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Aktywność elementu Webhook w fabryce danych platformy Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie elementu webhook można kontrolować wykonywanie potoków za pośrednictwem kodu niestandardowego. Dzięki aktywności elementu webhook kod klientów może wywołać punkt końcowy i przekazać mu adres URL wywołania zwrotnego. Uruchomienie potoku czeka na wywołanie zwrotne, zanim przejdzie do następnego działania.

## <a name="syntax"></a>Składnia

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
**Nazwa** | Nazwa działania elementu webhook. | Ciąg | Tak |
**Typu** | Musi być ustawiona na "WebHook". | Ciąg | Tak |
**Metoda** | Metoda interfejsu API REST dla docelowego punktu końcowego. | Ciąg. Obsługiwanym typem jest "POST". | Tak |
**Adres url** | Docelowy punkt końcowy i ścieżka. | Ciąg lub wyrażenie z **wynikiemType** wartość ciągu. | Tak |
**Nagłówki** | Nagłówki, które są wysyłane do żądania. Oto przykład, który ustawia język i wpisz `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`na żądanie: . | Ciąg lub wyrażenie z **wynikiemType** wartość ciągu. | Tak. Wymagany `Content-Type` jest `"headers":{ "Content-Type":"application/json"}` nagłówek podobny. |
**Ciała** | Reprezentuje ładunek, który jest wysyłany do punktu końcowego. | Prawidłowe JSON lub wyrażenie z **wynikiemType** wartość JSON. Zobacz [Żądanie schematu ładunku](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema) dla schematu ładunku żądania. | Tak |
**Uwierzytelniania** | Metoda uwierzytelniania używana do wywoływania punktu końcowego. Obsługiwane typy to "Basic" i "ClientCertificate". Aby uzyskać więcej informacji, zobacz [Authentication](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication) (Uwierzytelnianie). Jeśli uwierzytelnianie nie jest wymagane, wyklucz tę właściwość. | Ciąg lub wyrażenie z **wynikiemType** wartość ciągu. | Nie |
**timeout** | Jak długo działanie czeka na wywołanie zwrotne określone przez **callBackUri** do wywołania. Wartość domyślna to 10 minut ("00:10:00"). Wartości mają format TimeSpan *d*. *hh*:*mm*:*ss*. | Ciąg | Nie |
**Raport stanu wywołania zwrotnego** | Umożliwia użytkownikowi zgłaszanie stanu niepowodzenia działania elementu webhook. | Wartość logiczna | Nie |

## <a name="authentication"></a>Authentication

Działanie elementu webhook obsługuje następujące typy uwierzytelniania.

### <a name="none"></a>Brak

Jeśli uwierzytelnianie nie jest wymagane, nie należy dołączać właściwości **uwierzytelniania.**

### <a name="basic"></a>Podstawowy

Określ nazwę użytkownika i hasło do użycia z uwierzytelnianiem podstawowym.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certyfikat klienta

Określ zakodowaną w bazie podstawową zawartość pliku PFX i hasło.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Tożsamość zarządzana

Użyj tożsamości zarządzanej fabryki danych, aby określić identyfikator URI zasobu, dla którego jest wymagany token dostępu. Aby wywołać interfejs API `https://management.azure.com/`zarządzania zasobami platformy Azure, należy użyć programu . Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych, zobacz [omówienie tożsamości zarządzanych dla zasobów platformy Azure.](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Jeśli fabryka danych jest skonfigurowana z repozytorium Git, należy przechowywać poświadczenia w usłudze Azure Key Vault, aby używać uwierzytelniania podstawowego lub certyfikatu klienta. Usługa Azure Data Factory nie przechowuje haseł w usłudze Git.

## <a name="additional-notes"></a>Uwagi dodatkowe

Fabryka danych przekazuje dodatkową właściwość **callBackUri** w treści wysyłane do punktu końcowego adresu URL. Usługa Data Factory oczekuje, że ten identyfikator URI zostanie wywołany przed określoną wartością limitu czasu. Jeśli identyfikator URI nie jest wywoływany, działanie kończy się niepowodzeniem ze stanem "TimedOut".

Działanie elementu webhook kończy się niepowodzeniem, gdy wywołanie niestandardowego punktu końcowego nie powiedzie się. Każdy komunikat o błędzie można dodać do treści wywołania zwrotnego i używane w późniejszym działaniu.

Dla każdego wywołania interfejsu API REST, czas oczekiwania klienta, jeśli punkt końcowy nie odpowiada w ciągu jednej minuty. To zachowanie jest standardową najlepszą praktyką http. Aby rozwiązać ten problem, zaimplementuj wzorzec 202. W bieżącym przypadku punkt końcowy zwraca 202 (Zaakceptowane) i sonduje klienta.

Jednominutowy limit czasu na żądanie nie ma nic wspólnego z limitem czasu działania. Ten ostatni jest używany do oczekiwania na wywołanie zwrotne określone przez **callbackUri**.

Treść przekazana z powrotem do wywołania zwrotnego identyfikatora URI musi być prawidłowa JSON. Ustaw `Content-Type` nagłówek `application/json`na .

Korzystając z **raportu stanu na wywołanie zwrotnym** właściwości, należy dodać następujący kod do treści podczas oddzwaniania zwrotnego:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące działania przepływu sterowania obsługiwane przez fabrykę danych:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)
