---
title: Obsługa wyjątków & scenariusz rejestrowania błędów
description: Rzeczywisty przypadek użycia i scenariusz dla zaawansowanej obsługi wyjątków i rejestrowania błędów w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: fdf5f25ae6f89ccc06c95ee1be021691dab0047a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000355"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenariusz: Obsługa wyjątków i rejestrowanie błędów na potrzeby aplikacji logiki

W tym scenariuszu opisano sposób, w jaki można rozłożyć aplikację logiki w celu zapewnienia lepszej obsługi wyjątków. Użycie przypadku użycia w czasie rzeczywistym w celu udzielenia odpowiedzi na pytanie: "czy Azure Logic Apps obsługiwać wyjątek i obsługę błędów?".

> [!NOTE]
> Bieżący schemat Azure Logic Apps zawiera standardowy szablon odpowiedzi akcji. Ten szablon zawiera wewnętrzne walidacje i odpowiedzi na błędy zwracane z aplikacji interfejsu API.

## <a name="scenario-and-use-case-overview"></a>Scenariusz i przypadek użycia — Omówienie

Poniżej przedstawiono historię przypadku użycia w tym scenariuszu: 

Dobrze znana organizacja opieki zdrowotnej zaangażowana w opracowywanie rozwiązania platformy Azure, które utworzy Portal pacjenta, za pomocą usługi Microsoft Dynamics CRM Online. Są one konieczne do wysyłania rekordów terminów między portalem programu Dynamics CRM Online i usługą Salesforce. Otrzymaliśmy prośbę o użycie standardu [HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) w przypadku wszystkich rekordów pacjenta.

Projekt ma dwa główne wymagania:  

* Metoda rejestrowania rekordów wysyłanych z portalu usługi Dynamics CRM Online
* Sposób wyświetlania wszelkich błędów, które wystąpiły w ramach przepływu pracy

> [!TIP]
> Aby uzyskać ogólne wideo na temat tego projektu, zobacz [Grupa użytkowników integracji](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Grupa użytkowników integracji").

## <a name="how-we-solved-the-problem"></a>Jak rozwiązać problem

Wybrano [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") jako repozytorium dla rekordów dziennika i błędów (Cosmos DB odwołuje się do rekordów jako dokumentów). Ponieważ Azure Logic Apps ma standardowy szablon dla wszystkich odpowiedzi, nie trzeba tworzyć schematu niestandardowego. Możemy utworzyć aplikację interfejsu API do **wstawiania** i **wykonywania zapytań** dotyczących rekordów błędów i dzienników. Możemy również zdefiniować schemat dla każdej z nich w aplikacji interfejsu API.  

Innym wymaganiem było przeczyszczenie rekordów po określonej dacie. Cosmos DB ma właściwość o nazwie [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Czas wygaśnięcia") (TTL), która pozwala nam ustawić wartość **czasu wygaśnięcia** dla każdego rekordu lub kolekcji. Ta funkcja eliminuje konieczność ręcznego usuwania rekordów w Cosmos DB.

> [!IMPORTANT]
> Aby ukończyć ten samouczek, musisz utworzyć bazę danych Cosmos DB i dwie kolekcje (rejestrowanie i błędy).

## <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

Pierwszym krokiem jest utworzenie aplikacji logiki i otwarcie aplikacji w Projektancie aplikacji logiki. W tym przykładzie używane są aplikacje logiki nadrzędny-podrzędny. Załóżmy, że utworzono już element nadrzędny i zamierzasz utworzyć jedną podrzędną aplikację logiki.

Ponieważ będziemy rejestrować rekordy wychodzące z usługi Dynamics CRM Online, Zacznijmy od początku. Musimy używać wyzwalacza **żądania** , ponieważ nadrzędna aplikacja logiki wyzwala ten element podrzędny.

### <a name="logic-app-trigger"></a>Wyzwalacz aplikacji logiki

Używamy wyzwalacza **żądania** , jak pokazano w następującym przykładzie:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Kroki

Należy zarejestrować źródło (żądanie) rekordu pacjenta z portalu usługi Dynamics CRM Online.

1. Musimy uzyskać nowy rekord terminu z usługi Dynamics CRM Online.

   Wyzwalacz pochodzący z programu CRM udostępnia nam program **CRM PatentId**, **Typ rekordu**, **nowy lub zaktualizowany rekord** (Nowa lub zaktualizowana wartość logiczna) i **SalesforceId**. **SalesforceId** może mieć wartość null, ponieważ jest używany tylko dla aktualizacji.
   Pobieramy rekord CRM przy użyciu programu CRM **PatientID** i **typu rekordu**.

2. Następnie musimy dodać naszą Azure Cosmos DB operacji **InsertLogEntry** aplikacji interfejsu API SQL, jak pokazano tutaj w Projektancie aplikacji logiki.

   **Wstaw wpis dziennika**

   ![Zrzut ekranu z projektanta aplikacji logiki przedstawiający ustawienia konfiguracji InsertLogEntry.](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Wstaw wpis błędu**

   ![Zrzut ekranu z projektanta aplikacji logiki przedstawiający ustawienia konfiguracji CreateErrorRecord.](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Sprawdź, czy nie powiodło się utworzenie rekordu**

   ![Zrzut ekranu przedstawiający ekran CreateErrorRecord w Projektancie aplikacji logiki, pokazujący pola służące do tworzenia wpisu błędu.](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Kod źródłowy aplikacji logiki

> [!NOTE]
> Poniższe przykłady są tylko przykładami. Ponieważ ten samouczek jest oparty na implementacji teraz w środowisku produkcyjnym, wartość **węzła źródłowego** może nie wyświetlać właściwości, które są związane z planowaniem terminu. > 

### <a name="logging"></a>Rejestrowanie

Poniższy przykładowy kod aplikacji logiki pokazuje, jak obsługiwać rejestrowanie.

#### <a name="log-entry"></a>Wpis dziennika

Poniżej znajduje się kod źródłowy aplikacji logiki służący do wstawiania wpisu dziennika.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Żądanie dziennika

Oto komunikat żądania dziennika ogłoszony w aplikacji interfejsu API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Odpowiedź dziennika

Oto komunikat odpowiedzi na dziennik z aplikacji interfejsu API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Teraz przyjrzyjmy się krokom obsługi błędów.

### <a name="error-handling"></a>Obsługa błędów

Poniższy przykładowy kod aplikacji logiki pokazuje, jak można zaimplementować obsługę błędów.

#### <a name="create-error-record"></a>Utwórz rekord błędu

Oto kod źródłowy aplikacji logiki służący do tworzenia rekordu błędu.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Błąd wstawiania do Cosmos DB--żądanie

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Wstaw błąd do Cosmos DB--Response

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Odpowiedź na błąd usługi Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Zwracanie odpowiedzi z powrotem do nadrzędnej aplikacji logiki

Po otrzymaniu odpowiedzi można przekazać odpowiedź z powrotem do nadrzędnej aplikacji logiki.

#### <a name="return-success-response-to-parent-logic-app"></a>Zwracanie odpowiedzi sukcesu do nadrzędnej aplikacji logiki

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Zwróć odpowiedź na błąd do nadrzędnej aplikacji logiki

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB repozytorium i Portal

Nasze rozwiązanie dodaliśmy możliwości z [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Portal zarządzania błędami

Aby wyświetlić błędy, można utworzyć aplikację sieci Web MVC do wyświetlania rekordów błędów z Cosmos DB. Operacje **list**, **szczegółów**, **edycji** i **usuwania** są zawarte w bieżącej wersji.

> [!NOTE]
> Edycja operacji: Cosmos DB zamienia cały dokument. Rekordy widoczne na **liście** i w widokach **szczegółów** są tylko przykładami. Nie są to rzeczywiste rekordy terminów pacjenta.

Oto przykłady szczegółów aplikacji MVC utworzonych przy użyciu opisanego wcześniej podejścia.

#### <a name="error-management-list"></a>Lista zarządzania błędami
![Lista błędów](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Widok szczegółów zarządzania błędami
![Szczegóły błędu](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal zarządzania dziennikami

Aby wyświetlić dzienniki, utworzono również aplikację sieci Web MVC. Oto przykłady szczegółów aplikacji MVC utworzonych przy użyciu opisanego wcześniej podejścia.

#### <a name="sample-log-detail-view"></a>Przykładowy widok szczegółów dziennika
![Widok szczegółów dziennika](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Szczegóły aplikacji interfejsu API

#### <a name="logic-apps-exception-management-api"></a>Interfejs API zarządzania wyjątkami Logic Apps

Nasza aplikacja interfejsu API zarządzania wyjątkami Azure Logic Apps Open Source zawiera funkcje opisane w tym miejscu — istnieją dwa kontrolery:

* **ErrorController** wstawia rekord błędu (dokument) do kolekcji Azure Cosmos DB.
* **LogController** Wstawia rekord dziennika (dokument) do kolekcji Azure Cosmos DB.

> [!TIP]
> Oba kontrolery używają `async Task<dynamic>` operacji, umożliwiając wykonywanie operacji w czasie wykonywania, dzięki czemu możemy utworzyć schemat Azure Cosmos DB w treści operacji. 
> 

Każdy dokument w Azure Cosmos DB musi mieć unikatowy identyfikator. Używamy `PatientId` i dodawania sygnatury czasowej, która jest konwertowana na wartość sygnatury czasowej systemu UNIX (Double). Obcinamy wartość, aby usunąć wartość ułamkową.

Możesz wyświetlić kod źródłowy interfejsu API kontrolera błędów z usługi [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Interfejs API jest wywoływany z aplikacji logiki przy użyciu następującej składni:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Wyrażenie w poprzednim przykładzie kodu sprawdza stan *Create_NewPatientRecord* **nie powiodło się**.

## <a name="summary"></a>Podsumowanie

* W aplikacji logiki można łatwo zaimplementować rejestrowanie i obsługę błędów.
* Azure Cosmos DB można używać jako repozytorium dla rekordów dziennika i błędów (dokumentów).
* Za pomocą MVC można utworzyć Portal do wyświetlania rekordów dzienników i błędów.

### <a name="source-code"></a>Kod źródłowy

Kod źródłowy aplikacji interfejsu API zarządzania wyjątkami Logic Apps jest dostępny w tym [repozytorium GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Interfejs API zarządzania wyjątkami aplikacji logiki").

## <a name="next-steps"></a>Następne kroki

* [Zobacz więcej przykładów i scenariuszy dotyczących aplikacji logiki](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps.md)
* [Tworzenie wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
