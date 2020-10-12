---
title: Korzystanie z interfejsów API REST usługi Azure łańcucha bloków Workbench
description: Scenariusze korzystania z interfejsu API REST usługi Azure łańcucha bloków Workbench Preview
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87003070"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure łańcucha bloków Workbench Preview

Interfejs API REST usługi Azure łańcucha bloków Workbench w wersji zapoznawczej oferuje deweloperom i pracownikom przetwarzającym informacje możliwość tworzenia rozbudowanych integracji z aplikacjami łańcucha bloków. W tym artykule przedstawiono kilka scenariuszy korzystania z interfejsu API REST Workbench. Załóżmy na przykład, że chcesz utworzyć niestandardowego klienta łańcucha bloków, który umożliwia zalogowanym użytkownikom wyświetlanie ich przypisanych aplikacji łańcucha bloków i korzystanie z nich. Klient może używać interfejsu API łańcucha bloków Workbench, aby wyświetlać wystąpienia kontraktu i podejmować działania dotyczące inteligentnych kontraktów.

## <a name="blockchain-workbench-api-endpoint"></a>Punkt końcowy interfejsu API łańcucha bloków Workbench

Interfejsy API usługi łańcucha bloków Workbench są dostępne za poorednictwem punktu końcowego wdrożenia. Aby uzyskać adres URL punktu końcowego interfejsu API dla wdrożenia:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz nazwę grupy zasobów wdrożonej łańcucha bloków Workbench.
1. Wybierz nagłówek kolumny **Typ** , aby posortować listę alfabetycznie według typu.
1. Istnieją dwa zasoby z typem **App Service**. Wybierz zasób typu **App Service** *z* sufiksem "-API".
1. W App Service **Przegląd**skopiuj wartość **adresu URL** , która reprezentuje adres URL punktu końcowego interfejsu API do wdrożonego łańcucha bloków Workbench.

    ![Adres URL punktu końcowego interfejsu API usługi App Service](media/use-api/app-service-api.png)

## <a name="authentication"></a>Uwierzytelnianie

Żądania do interfejsu API REST usługi łańcucha bloków Workbench są chronione za pomocą Azure Active Directory (Azure AD).

Aby wysłać żądanie do interfejsów API REST, kod klienta wymaga uwierzytelnienia z prawidłowymi poświadczeniami, zanim będzie można wywołać interfejs API. Uwierzytelnianie jest koordynowane między różnymi aktorami przez usługę Azure AD i zapewnia klientowi [token dostępu](../../active-directory/develop/developer-glossary.md#access-token) jako potwierdzenie uwierzytelniania. Token jest następnie wysyłany w nagłówku autoryzacji HTTP żądań interfejsu API REST. Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure AD, zobacz [Azure Active Directory dla deweloperów](../../active-directory/develop/index.yml).

Zobacz przykłady [interfejsu API REST](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) , aby zapoznać się z przykładami uwierzytelniania.

## <a name="using-postman"></a>Używanie narzędzia Postman

Jeśli chcesz przetestować interfejsy API Workbench lub eksperymentować z nich, możesz użyć programu [Poster](https://www.postman.com) , aby wykonać wywołania interfejsu API do wdrożenia. [Pobierz przykładową kolekcję Workbench żądań interfejsu API](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) z usługi GitHub. Zobacz plik Readme, aby uzyskać szczegółowe informacje o uwierzytelnianiu i korzystaniu z przykładowych żądań interfejsu API.

## <a name="create-an-application"></a>Tworzenie aplikacji

Do utworzenia aplikacji łańcucha bloków Workbench służą dwa wywołania interfejsu API. Tę metodę można wykonać tylko przez użytkowników, którzy są administratorami Workbench.

Użyj [interfejsu API Applications post](/rest/api/azure-blockchain-workbench/applications/applicationspost) , aby przekazać plik JSON aplikacji i uzyskać identyfikator aplikacji.

### <a name="applications-post-request"></a>Żądanie POST aplikacji

Użyj parametru **appFile** , aby wysłać plik konfiguracyjny jako część treści żądania.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Odpowiedź na wpis aplikacji

Utworzony identyfikator aplikacji jest zwracany w odpowiedzi. Musisz mieć identyfikator aplikacji, aby skojarzyć plik konfiguracji z plikiem kodu podczas wywoływania następnego interfejsu API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Żądanie POST kodu kontraktu

Aby przesłać plik kodu o stanie trwałym, należy użyć [kodu umowy aplikacji post API](/rest/api/azure-blockchain-workbench/applications/contractcodepost) przez przekazanie identyfikatora aplikacji. Ładunek może być pojedynczym plikiem SSD lub plikiem skompresowanym zawierającym pliki o pełnej postaci.

Zastąp następujące wartości:

| Parametr | Wartość |
|-----------|-------|
| Identyfikator | Wartość zwracana z interfejsu API aplikacji. |
| {ledgerId} | Indeks księgi. Wartość jest zwykle 1. Możesz również sprawdzić w [tabeli księgi](data-sql-management-studio.md) wartość. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Odpowiedź na wpis kodu kontraktu

Jeśli to się powiedzie, odpowiedź zawiera identyfikator kodu utworzonego kontraktu z [tabeli ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Przypisywanie ról do użytkowników

Aby utworzyć mapowanie między użytkownikami w określonej aplikacji łańcucha bloków, użyj identyfikatora aplikacji, identyfikatora użytkownika i identyfikatora roli aplikacji do [przypisania roli aplikacji](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) . Tę metodę można wykonać tylko przez użytkowników, którzy są administratorami Workbench.

### <a name="role-assignments-post-request"></a>Żądanie POST przypisań ról

Zastąp następujące wartości:

| Parametr | Wartość |
|-----------|-------|
| Identyfikator | Wartość zwracana z interfejsu API aplikacji. |
| Nazwa | Wartość identyfikatora użytkownika z [tabeli użytkownika](data-sql-management-studio.md). |
| {applicationRoleId} | Wartość identyfikatora roli aplikacji skojarzona z IDENTYFIKATORem aplikacji z [tabeli ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Odpowiedź po przypisaniu ról

Jeśli to się powiedzie, odpowiedź obejmuje utworzony identyfikator przypisania roli z [tabeli RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Wyświetlanie listy aplikacji

Użyj [interfejsu API Applications Get](/rest/api/azure-blockchain-workbench/applications/applicationsget) , aby pobrać wszystkie aplikacje łańcucha bloków Workbench dla użytkownika. W tym przykładzie zalogowany użytkownik ma dostęp do dwóch aplikacji:

- [Transfer zasobów](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Transport mrożonek](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Żądania GET aplikacji

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Odpowiedź na pobieranie aplikacji

Odpowiedź zawiera listę wszystkich aplikacji łańcucha bloków, do których użytkownik ma dostęp w usłudze łańcucha bloków Workbench. Łańcucha bloków Workbench administratorzy uzyskują każdą aplikację łańcucha bloków. Administratorzy niebędący Workbench otrzymują wszystkie aplikacje łańcucha bloków, dla których mają co najmniej jedną skojarzoną rolę aplikacji lub skojarzoną rolę wystąpienia kontraktu inteligentnego.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Wyświetlanie listy przepływów pracy dla aplikacji

Za pomocą [przepływów pracy aplikacji Pobierz interfejs API](/rest/api/azure-blockchain-workbench/applications/workflowsget) , aby wyświetlić listę wszystkich przepływów pracy określonej aplikacji łańcucha bloków, do których użytkownik ma dostęp w usłudze łańcucha bloków Workbench. Każda aplikacja łańcucha bloków ma co najmniej jeden przepływ pracy, a każdy przepływ pracy ma zero lub więcej wystąpień kontraktu inteligentnego. W przypadku aplikacji klienckiej łańcucha bloków, która ma tylko jeden przepływ pracy, zalecamy Pominięcie przepływu pracy użytkownika, który umożliwia użytkownikom wybranie odpowiedniego przepływu.

### <a name="application-workflows-request"></a>Żądanie przepływów pracy aplikacji

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Odpowiedź przepływów pracy aplikacji

Łańcucha bloków Workbench Administratorzy mogą uzyskać każdy przepływ pracy łańcucha bloków. Administratorzy niebędący Workbench otrzymują wszystkie przepływy pracy, dla których mają co najmniej jedną skojarzoną rolę aplikacji lub są skojarzone z rolą wystąpienia kontraktu inteligentnego.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>Tworzenie wystąpienia kontraktu

Utwórz nowe wystąpienie kontraktu inteligentnego dla przepływu pracy przy użyciu programu [kontrakty w wersji 2](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) . Użytkownicy mogą tworzyć nowe wystąpienie kontraktu inteligentnego tylko wtedy, gdy użytkownik jest skojarzony z rolą aplikacji, która może inicjować wystąpienie kontraktu inteligentnego dla przepływu pracy.

> [!NOTE]
> W tym przykładzie używany jest program w wersji 2 interfejsu API. Interfejsy API kontraktu w wersji 2 zapewniają większy poziom szczegółowości dla skojarzonych pól ProvisioningStatus.

### <a name="contracts-post-request"></a>Żądanie POST kontraktów

Zastąp następujące wartości:

| Parametr | Wartość |
|-----------|-------|
| WorkflowId | Wartość identyfikatora przepływu pracy to ConstructorID kontraktu z [tabeli przepływu pracy](data-sql-management-studio.md). |
| {contractCodeId} | Wartość identyfikatora kodu kontraktu z [tabeli ContractCode](data-sql-management-studio.md). Skorelowanie identyfikatora aplikacji i identyfikatora księgi dla wystąpienia kontraktu, które chcesz utworzyć. |
| ConnectionID | Wartość identyfikatora połączenia z [tabeli Connection](data-sql-management-studio.md). |

Dla treści żądania Ustaw wartości przy użyciu następujących informacji:

| Parametr | Wartość |
|-----------|-------|
| workflowFunctionID | Identyfikator z [tabeli WorkflowFunction](data-sql-management-studio.md). |
| workflowActionParameters | Pary nazw wartości parametrów przesłane do konstruktora. Dla każdego parametru należy użyć wartości workflowFunctionParameterID z tabeli [WorkflowFunctionParameter](data-sql-management-studio.md) . |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Odpowiedź POST kontraktów

Jeśli to się powiedzie, interfejs API przypisań ról zwróci ContractActionID z [tabeli ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Wyświetlanie listy wystąpień kontraktu inteligentnego dla przepływu pracy

Aby wyświetlić wszystkie wystąpienia inteligentnej umowy dla przepływu pracy, użyj [funkcji API Get](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) . Można też pozwolić użytkownikom na głębokie szczegółowe w dowolne z pokazanych wystąpień kontraktu inteligentnego.

### <a name="contracts-request"></a>Żądanie kontraktów

W tym przykładzie zakładamy, ze użytkownik chce wejść w interakcję z jednym z wystąpień kontraktu inteligentnego, aby wykonać akcję.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Odpowiedź na kontrakty

Odpowiedź zawiera listę wszystkich wystąpień kontraktów inteligentnych określonego przepływu pracy. Administratorzy Workbench uzyskują wszystkie wystąpienia inteligentne kontraktu. Administratorzy niebędący Workbench otrzymują każde wystąpienie kontraktu inteligentnego, dla którego ma co najmniej jedną skojarzoną rolę aplikacji lub jest skojarzone z rolą inteligentnego wystąpienia kontraktu.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Wyświetlanie listy dostępnych akcji dla kontraktu

Użyj [interfejsu API akcji kontraktu](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) , aby wyświetlić dostępne akcje użytkownika z uwzględnieniem stanu kontraktu. 

### <a name="contract-action-request"></a>Żądanie akcji kontraktu

W tym przykładzie użytkownik przegląda wszystkie dostępne akcje dla nowego utworzonego przez siebie kontraktu inteligentnego.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Odpowiedź akcji kontraktu

Odpowiedź zawiera listę wszystkich akcji, które użytkownik może wykonać dla danego bieżącego stanu określonego wystąpienia kontraktu inteligentnego.

* Zmień: umożliwia użytkownikowi zmodyfikowanie opisu i ceny zasobu.
* Przerwij: umożliwia użytkownikowi zakończenie kontraktu elementu zawartości.

Użytkownicy uzyskują wszystkie odpowiednie akcje, jeśli użytkownik ma skojarzoną rolę aplikacji lub jest skojarzony z rolą wystąpienia kontraktu inteligentnego dla bieżącego stanu określonego wystąpienia kontraktu inteligentnego.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Wykonywanie akcji dla kontraktu

Użyj [funkcji post API akcji kontraktu](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) , aby wykonać akcję dla określonego wystąpienia kontraktu inteligentnego.

### <a name="contract-action-post-request"></a>Żądanie POST akcji kontraktu

W tym przypadku Rozważmy scenariusz, w którym użytkownik chce zmodyfikować opis i cenę elementu zawartości.

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Użytkownicy mogą wykonać akcję tylko w przypadku danego bieżącego stanu określonego wystąpienia kontraktu inteligentnego i skojarzonej roli aplikacji lub roli wystąpienia kontraktu inteligentnego użytkownika.

### <a name="contract-action-post-response"></a>Odpowiedź na wpis akcji kontraktu

Jeśli tworzenie zakończy się pomyślnie, zwrócona zostanie odpowiedź HTTP 200 OK bez treści odpowiedzi.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Następne kroki

Informacje referencyjne dotyczące interfejsów API łańcucha bloków Workbench można znaleźć w [dokumentacji interfejsu API REST usługi Azure łańcucha bloków Workbench](/rest/api/azure-blockchain-workbench).
