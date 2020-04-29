---
title: Pobierz stan oferty | Portal Azure Marketplace
description: Interfejs API Pobiera bieżący stan oferty.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 9cf6ca27101a08ff58f32dcd31413256762490a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255921"
---
# <a name="retrieve-offer-status"></a>Pobieranie stanu oferty

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Pobiera bieżący stan oferty.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametry identyfikatora URI

|  **Nazwa**       |   **Opis**                            |  **Typ danych** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identyfikator wydawcy, na przykład`Contoso`  |     String     |
|  offerId        | Identyfikator GUID, który jednoznacznie identyfikuje ofertę      |     String     |
|  api-version    | Najnowsza wersja interfejsu API                        |     Date       |
|  |  |


## <a name="header"></a>Nagłówek


|  Nazwa           |  Wartość               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Autoryzacja  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Przykład treści


### <a name="response"></a>Odpowiedź

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
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
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```


### <a name="response-body-properties"></a>Właściwości treści odpowiedzi

|  **Nazwa**             |    **Opis**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Stan oferty. Aby uzyskać listę możliwych wartości, zobacz temat [status oferty](#offer-status) poniżej. |
|  z chmury do urządzenia             | Tablica komunikatów skojarzonych z ofertą                                                    |
|  kroki                | Tablica kroków, przez jaką oferta przechodzi w ramach publikacji oferty                      |
|  estimatedTimeFrame   | Oszacowanie czasu, jaki mógłby wykonać ten krok, w przyjaznym formacie                       |
|  id                   | Identyfikator kroku                                                                         |
|  krokname             | Nazwa kroku                                                                               |
|  description          | Opis kroku                                                                        |
|  status               | Stan kroku. Aby uzyskać listę możliwych wartości, zobacz [Step status](#step-status) poniżej.    |
|  z chmury do urządzenia             | Tablica komunikatów dotyczących kroku                                                          |
|  processPercentage    | Procent wykonania kroku                                                              |
|  previewLinks         | *Obecnie nie zaimplementowane*                                                                    |
|  liveLinks            | *Obecnie nie zaimplementowane*                                                                    |
|  notificationEmails   | Przestarzałe w przypadku ofert migrowanych do Centrum partnerskiego. Wiadomości e-mail z powiadomieniami o zmigrowanych ofertach zostaną wysłane do wiadomości e-mail określonej w obszarze Ustawienia konta w ramach informacji kontaktowych sprzedawcy.<br><br>W przypadku ofert, które nie zostały zmigrowane, rozdzielana przecinkami lista adresów e-mail do powiadomienia o postępie operacji        |
|  |  |

### <a name="response-status-codes"></a>Kody stanu odpowiedzi

| **Code** |   **Opis**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`-Żądanie zostało pomyślnie przetworzone i został zwrócony bieżący stan oferty. |
|  400     | `Bad/Malformed request`-Treść odpowiedzi błędu może zawierać więcej informacji.                 |
|  404     | `Not found`-Określona jednostka nie istnieje.                                                |
|  |  |

### <a name="offer-status"></a>Stan oferty

|  **Nazwa**                    |    **Opis**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Oferta nie została nigdy opublikowana.                          |
|  NotStarted                  | Oferta jest nowa i nie została uruchomiona.                            |
|  WaitingForPublisherReview   | Oferta oczekuje na zatwierdzenie przez wydawcę.                 |
|  Działanie                     | Przesyłanie oferty jest przetwarzane.                     |
|  Sukces                   | Przesyłanie oferty zostało zakończone.               |
|  Anulowane                    | Przesyłanie oferty zostało anulowane.                           |
|  Niepowodzenie                      | Nie można przesłać oferty.                                 |
|  |  |

### <a name="step-status"></a>Stan kroku

|  **Nazwa**                    |    **Opis**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Krok nie został uruchomiony.                        |
|  Toku                  | Krok jest uruchomiony.                             |
|  WaitingForPublisherReview   | Krok oczekuje na zatwierdzenie przez wydawcę.      |
|  WaitingForApproval          | Krok oczekuje na zatwierdzenie procesu.        |
|  Zablokowane                     | Krok jest zablokowany.                             |
|  Odrzucone                    | Krok został odrzucony.                            |
|  Complete                    | Krok został ukończony.                            |
|  Anulowane                    | Krok został anulowany.                           |
|  |  |
