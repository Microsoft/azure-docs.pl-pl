---
title: Pobieranie interfejsu API operacji — Azure Marketplace
description: Interfejs API umożliwiający pobranie wszystkich operacji z oferty lub pobranie określonej operacji dla określonego operationId.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: cb44d977407a7e854603e6bbacf3591752b109c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87271947"
---
# <a name="retrieve-operations"></a>Pobieranie operacji

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Pobiera wszystkie operacje z oferty lub w celu uzyskania określonej operacji dla określonego operationId. Klient może użyć parametrów zapytania, aby odfiltrować uruchomione operacje.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>Parametry identyfikatora URI

|  **Nazwa**          |      **Opis**                                                                                           | **Typ danych** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identyfikator wydawcy, na przykład `Contoso`                                                                   |  Ciąg       |
|  offerId           |  Identyfikator oferty                                                                                              |  Ciąg       |
|  operationId       |  Identyfikator GUID, który jednoznacznie identyfikuje operację na ofercie. OperationId może zostać pobrany za pomocą tego interfejsu API i jest również zwracany w nagłówku HTTP odpowiedzi dla dowolnej długotrwałej operacji, takiej jak interfejs API [oferty publikacji](./cloud-partner-portal-api-publish-offer.md) .  |   Guid (identyfikator GUID)   |
|  api-version       | Najnowsza wersja interfejsu API |    Date (Data)      |
|  |  |  |

## <a name="header"></a>Nagłówek

|  **Nazwa**          |  **Wartość**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autoryzacja     | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Przykład treści

### <a name="response"></a>Reakcja

#### <a name="get-operations"></a>Pobierz operacje

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>Pobierz operację

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>Właściwości treści odpowiedzi

|  **Nazwa**                    |  **Opis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  identyfikator                          | Identyfikator GUID, który jednoznacznie identyfikuje operację                                                       |
|  submisja              | Identyfikuje typ operacji raportowanej dla oferty, na przykład `Publish/GoLive`      |
|  createdDateTime             | UTC DateTime podczas tworzenia operacji                                                       |
|  lastActionDateTime          | Data i godzina UTC ostatniej aktualizacji dla operacji                                       |
|  status                      | Stan operacji `not started` \| `running` \| `failed` \| `completed` . W danym momencie może istnieć tylko jedna operacja `running` . |
|  error                       | Komunikat o błędzie dla operacji zakończonych niepowodzeniem                                                               |
|  |  |

### <a name="response-step-properties"></a>Właściwości kroku odpowiedzi

|  **Nazwa**                    |  **Opis**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | Szacowany czas trwania tej operacji |
| identyfikator | Unikatowy identyfikator procesu kroku |
| description (opis) | Opis kroku |
| krokname | Przyjazna nazwa kroku |
| status | Stan kroku, `notStarted` \| `running` \| `failed` albo \|`completed` |
| z chmury do urządzenia | Wszystkie powiadomienia lub ostrzeżenia wystąpiły w trakcie tego kroku. Tablica ciągów |
| progressPercentage | Liczba całkowita z zakresu od 0 do 100 wskazująca postęp kroku |
| | |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Kod**  |   **Opis**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` -Żądanie zostało pomyślnie przetworzone i zostały zwrócone żądane operacje.        |
|  400      | `Bad/Malformed request` -Treść odpowiedzi błędu może zawierać więcej informacji.                    |
|  403      | `Forbidden` -Klient nie ma dostępu do określonego obszaru nazw.                          |
|  404      | `Not found` -Określona jednostka nie istnieje.                                                 |
|  |  |
