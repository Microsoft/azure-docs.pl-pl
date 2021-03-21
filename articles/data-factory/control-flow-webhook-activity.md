---
title: Działanie elementu webhook w Azure Data Factory
description: Działanie elementu webhook nie kontynuuje wykonywania potoku do momentu zweryfikowania dołączonego zestawu danych z określonymi kryteriami, które użytkownik określi.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 59aa395db27c26a7c94eebdc0e3b34d7776ee75f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592000"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Działanie elementu webhook w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie elementu webhook może kontrolować wykonywanie potoków za pomocą kodu niestandardowego. Za pomocą działania elementu webhook kod klienci może wywoływać punkt końcowy i przekazać go do adresu URL wywołania zwrotnego. Uruchomienie potoku oczekuje na wywołanie wywołania zwrotnego przed przejściem do następnego działania.

> [!IMPORTANT]
> Działanie elementu webhook umożliwia teraz wypróbowanie powierzchni stanu błędu i komunikatów niestandardowych z powrotem do działania i potoku. Ustaw _reportStatusOnCallBack_ na true, a w ładunku wywołania zwrotnego Uwzględnij wartość _StatusCode_ i _błąd_ . Aby uzyskać więcej informacji, zobacz sekcję [dodatkowe uwagi](#additional-notes) .

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
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
**name** | Nazwa działania elementu webhook. | Ciąg | Tak |
**Wprowadź** | Musi być ustawiona na "webhook". | Ciąg | Tak |
**Method** | Metoda interfejsu API REST dla docelowego punktu końcowego. | Ciąg. Obsługiwany typ to "POST". | Tak |
**adres URL** | Docelowy punkt końcowy i ścieżka. | Ciąg lub wyrażenie z wartością **ResultType** ciągu. | Tak |
**nagłówka** | Nagłówki wysyłane do żądania. Oto przykład, który ustawia język i typ żądania: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Ciąg lub wyrażenie z wartością **ResultType** ciągu. | Tak. `Content-Type` `"headers":{ "Content-Type":"application/json"}` Wymagany jest następujący nagłówek. |
**jednostce** | Reprezentuje ładunek, który jest wysyłany do punktu końcowego. | Prawidłowy kod JSON lub wyrażenie z wartością **ResultType** JSON. Zobacz [schemat ładunku żądania](./control-flow-web-activity.md#request-payload-schema) dla schematu ładunku żądania. | Tak |
**uwierzytelnianie** | Metoda uwierzytelniania użyta do wywołania punktu końcowego. Obsługiwane typy to "Basic" i "ClientCertificate". Aby uzyskać więcej informacji, zobacz [Authentication](./control-flow-web-activity.md#authentication) (Uwierzytelnianie). Jeśli uwierzytelnianie nie jest wymagane, Wyklucz tę właściwość. | Ciąg lub wyrażenie z wartością **ResultType** ciągu. | Nie |
**timeout** | Jak długo działanie czeka na wywołanie wywołania zwrotnego określonego przez **callBackUri** . Wartość domyślna to 10 minut ("00:10:00"). Wartości mają format TimeSpan *d*. *hh*:*mm*:*SS*. | Ciąg | Nie |
**Stan raportu dla wywołania zwrotnego** | Umożliwia użytkownikowi zgłaszanie stanu niepowodzenia działania elementu webhook. | Wartość logiczna | Nie |

## <a name="authentication"></a>Authentication

Działanie elementu webhook obsługuje następujące typy uwierzytelniania.

### <a name="none"></a>Brak

Jeśli uwierzytelnianie nie jest wymagane, nie należy uwzględniać właściwości **uwierzytelnianie** .

### <a name="basic"></a>Podstawowa

Określ nazwę użytkownika i hasło, które mają być używane z uwierzytelnianiem podstawowym.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certyfikat klienta

Określ zawartość pliku PFX i hasło w formacie base64.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Tożsamość zarządzana

Użyj tożsamości zarządzanej fabryki danych, aby określić identyfikator URI zasobu, dla którego żądano tokenu dostępu. Aby wywołać interfejs API zarządzania zasobami platformy Azure, użyj programu `https://management.azure.com/` . Aby uzyskać więcej informacji o tym, jak działają zarządzane tożsamości, zobacz [Omówienie zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Jeśli Fabryka danych została skonfigurowana przy użyciu repozytorium git, musisz przechowywać poświadczenia w Azure Key Vault, aby używać uwierzytelniania podstawowego lub certyfikatu klienta. Azure Data Factory nie zapisuje haseł w usłudze git.

## <a name="additional-notes"></a>Uwagi dodatkowe

Data Factory przekazuje dodatkową właściwość **callBackUri** w treści wysyłanej do punktu końcowego adresu URL. Data Factory oczekuje, że ten identyfikator URI będzie wywoływany przed określoną wartością limitu czasu. Jeśli identyfikator URI nie zostanie wywołany, działanie kończy się niepowodzeniem ze stanem "TimedOut".

Działanie elementu webhook kończy się niepowodzeniem, gdy wywołanie do niestandardowego punktu końcowego zakończy się niepowodzeniem. Dowolny komunikat o błędzie można dodać do treści wywołania zwrotnego i użyć w późniejszym działaniu.

W przypadku każdego wywołania interfejsu API REST klient przekracza limit czasu, jeśli punkt końcowy nie odpowie w ciągu minuty. To zachowanie jest standardowym najlepszym rozwiązaniem HTTP. Aby rozwiązać ten problem, zaimplementuj wzorzec 202. W bieżącym przypadku punkt końcowy zwraca 202 (zaakceptowane) i sondy klienta.

Limit czasu dla żądania nie ma nic do wykonania z limitem czasu działania. Ten ostatni jest używany do oczekiwania na wywołanie zwrotne określone przez **callbackUri**.

Treść przeniesiona z powrotem do identyfikatora URI wywołania zwrotnego musi być prawidłowym kodem JSON. Ustaw `Content-Type` nagłówek na `application/json` .

Korzystając ze **stanu raportu dla właściwości wywołania zwrotnego** , należy dodać następujący kod do treści podczas wywołania zwrotnego:

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

Zobacz następujące działania przepływu sterowania obsługiwane przez Data Factory:

- [Działanie If Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
- [Działanie internetowe](control-flow-web-activity.md)
- [Działanie Until](control-flow-until-activity.md)