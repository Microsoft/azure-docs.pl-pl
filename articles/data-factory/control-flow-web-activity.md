---
title: Aktywność w sieci Web w fabryce danych platformy Azure
description: Dowiedz się, jak można użyć działania sieci Web, jeden z działań przepływu sterowania obsługiwane przez fabrykę danych, do wywołania punktu końcowego REST z potoku.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: a5cdb24a80dcbd95e4ccc59dd55f4acb9ae18060
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417899"
---
# <a name="web-activity-in-azure-data-factory"></a>Aktywność w sieci Web w fabryce danych platformy Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Działanie WebActivity może być używane do wywoływania niestandardowego punktu końcowego REST z potoku usługi Data Factory. Można przekazywać zestawy danych i połączone usługi do zużycia i dostępu przez działanie.

> [!NOTE]
> Aktywność w sieci Web może wywoływać tylko publicznie udostępniane adresy URL. Nie jest obsługiwany dla adresów URL, które są hostowane w prywatnej sieci wirtualnej.

## <a name="syntax"></a>Składnia

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagany
-------- | ----------- | -------------- | --------
name | Nazwa aktywności w internecie | Ciąg | Tak
type | Musi być ustawiona na **WebActivity**. | Ciąg | Tak
method | Metoda interfejsu API odpoczynku dla docelowego punktu końcowego. | Ciąg. <br/><br/>Obsługiwane typy: "GET", "POST", "PUT" | Tak
url | Docelowy punkt końcowy i ścieżka | Ciąg (lub wyrażenie z wynikiemType ciągu). Aktywność przesunie limit czasu o 1 minutę z błędem, jeśli nie otrzyma odpowiedzi z punktu końcowego. | Tak
Nagłówki | Nagłówki, które są wysyłane do żądania. Na przykład, aby ustawić język i `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`wpisać na żądanie: . | Ciąg (lub wyrażenie z wynikiemTylią ciągu) | Tak, wymagany jest nagłówek typu zawartości. `"headers":{ "Content-Type":"application/json"}`
body | Reprezentuje ładunek, który jest wysyłany do punktu końcowego.  | Ciąg (lub wyrażenie z wynikiemType ciągu). <br/><br/>Zobacz schemat ładunku żądania w sekcji [Schemat ładunku żądania.](#request-payload-schema) | Wymagane dla metod POST/PUT.
uwierzytelnianie | Metoda uwierzytelniania używana do wywoływania punktu końcowego. Obsługiwane typy to "Basic, or ClientCertificate". Aby uzyskać więcej informacji, zobacz [sekcję uwierzytelniania.](#authentication) Jeśli uwierzytelnianie nie jest wymagane, wyklucz tę właściwość. | Ciąg (lub wyrażenie z wynikiemTylią ciągu) | Nie
zestawy danych | Lista zestawów danych przekazanych do punktu końcowego. | Tablica odwołań do zestawu danych. Może być pustą tablicą. | Tak
linkedServices (usługi linked) | Lista połączonych usług przekazanych do punktu końcowego. | Tablica odwołań do usługi połączonej. Może być pustą tablicą. | Tak

> [!NOTE]
> REST punktów końcowych, które wywołuje działanie sieci web musi zwrócić odpowiedź typu JSON. Aktywność przesunie limit czasu o 1 minutę z błędem, jeśli nie otrzyma odpowiedzi z punktu końcowego.

W poniższej tabeli przedstawiono wymagania dotyczące zawartości JSON:

| Typ wartości | Treść żądania | Treść odpowiedzi |
|---|---|---|
|Obiekt JSON | Obsługiwane | Obsługiwane |
|Tablica JSON | Obsługiwane <br/>(Obecnie tablice JSON nie działają w wyniku błędu. Poprawka jest w toku.) | Nieobsługiwane |
| Wartość JSON | Obsługiwane | Nieobsługiwane |
| Typ nienawiązywać się do JSON | Nieobsługiwane | Nieobsługiwane |
||||

## <a name="authentication"></a>Authentication

Poniżej znajdują się obsługiwane typy uwierzytelniania w aktywności sieci web.

### <a name="none"></a>Brak

Jeśli uwierzytelnianie nie jest wymagane, nie należy dołączać właściwości "uwierzytelnianie".

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

Określ zakodowaną w base64 zawartość pliku PFX i hasła.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Tożsamość zarządzana

Określ identyfikator uri zasobu, dla którego token dostępu będzie wymagany przy użyciu tożsamości zarządzanej dla fabryki danych. Aby wywołać interfejs API `https://management.azure.com/`zarządzania zasobami platformy Azure, należy użyć programu . Aby uzyskać więcej informacji na temat działania tożsamości zarządzanych, zobacz [stronę omówienie tożsamości zarządzanych zasobów platformy Azure.](/azure/active-directory/managed-identities-azure-resources/overview)

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Jeśli fabryka danych jest skonfigurowana z repozytorium git, należy przechowywać poświadczenia w usłudze Azure Key Vault, aby używać uwierzytelniania certyfikatów podstawowych lub klienckich. Usługa Azure Data Factory nie przechowuje haseł w git.

## <a name="request-payload-schema"></a>Żądanie schematu ładunku
Podczas korzystania z POST/PUT metody, właściwość treści reprezentuje ładunek, który jest wysyłany do punktu końcowego. Połączone usługi i zestawy danych można przekazać jako część ładunku. Oto schemat ładunku:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Przykład
W tym przykładzie aktywność sieci web w potoku wywołuje punkt końcowy REST. Przekazuje usługę połączony sql platformy Azure i zestaw danych SQL platformy Azure do punktu końcowego. Punkt końcowy REST używa ciągu połączenia SQL platformy Azure do łączenia się z serwerem SQL platformy Azure i zwraca nazwę wystąpienia serwera SQL.

### <a name="pipeline-definition"></a>Definicja rurociągu

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Wartości parametrów potoku

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Kod punktu końcowego usługi sieci Web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Następne kroki
Zobacz inne działania przepływu sterowania obsługiwane przez fabrykę danych:

- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie wyszukiwania](control-flow-lookup-activity.md)
