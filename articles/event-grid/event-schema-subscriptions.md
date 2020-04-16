---
title: Subskrypcja platformy Azure jako źródło usługi Event Grid
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń subskrypcji za pomocą usługi Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fa88fe4e05ac968588a65d67a2f075bcae48ba7a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393226"
---
# <a name="azure-subscription-as-an-event-grid-source"></a>Subskrypcja platformy Azure jako źródło usługi Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń subskrypcji platformy Azure.Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).

Subskrypcje platformy Azure i grupy zasobów emitują te same typy zdarzeń. Typy zdarzeń są związane ze zmianami zasobów lub akcjami. Podstawowa różnica polega na tym, że grupy zasobów emitują zdarzenia dla zasobów w ramach grupy zasobów, a subskrypcje platformy Azure emitują zdarzenia dla zasobów w ramach subskrypcji.

Zdarzenia zasobów są tworzone dla operacji PUT, PATCH, `management.azure.com`POST i DELETE, które są wysyłane do programu . Operacje GET nie tworzą zdarzeń. Operacje wysyłane do płaszczyzny `myaccount.blob.core.windows.net`danych (np. ) nie tworzą zdarzeń. Zdarzenia akcji zapewniają dane zdarzeń dla operacji, takich jak wyświetlanie listy kluczy dla zasobu.

Po zasubskrybowaniu zdarzeń dla subskrypcji platformy Azure, punkt końcowy odbiera wszystkie zdarzenia dla tej subskrypcji. Zdarzenia mogą obejmować zdarzenie, które chcesz zobaczyć, takie jak aktualizowanie maszyny wirtualnej, ale także zdarzenia, które mogą nie być dla Ciebie ważne, takie jak pisanie nowego wpisu w historii wdrażania. Można odbierać wszystkie zdarzenia w punkcie końcowym i napisać kod, który przetwarza zdarzenia, które mają być obsługiwane. Lub można ustawić filtr podczas tworzenia subskrypcji zdarzenia.

Aby programowo obsługiwać zdarzenia, można sortować `operationName` zdarzenia, patrząc na wartość. Na przykład punkt końcowy zdarzenia może przetwarzać zdarzenia `Microsoft.Compute/virtualMachines/write` tylko `Microsoft.Storage/storageAccounts/write`dla operacji, które są równe lub .

Temat zdarzenia jest identyfikatorem zasobu, który jest celem operacji. Aby filtrować zdarzenia dla zasobu, podaj identyfikator zasobu podczas tworzenia subskrypcji zdarzenia. Aby filtrować według typu zasobu, użyj wartości w następującym formacie:`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`


## <a name="event-grid-event-schema"></a>Schemat zdarzenia w siatce zdarzeń

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Subskrypcje platformy Azure emitują zdarzenia zarządzania z usługi Azure Resource Manager, takie jak podczas tworzenia maszyny wirtualnej lub usuwania konta magazynu.

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Zasoby firmy Microsoft.ResourceActionCancel | Wywoływane, gdy akcja na zasób jest anulowana. |
| Microsoft.Resources.ResourceActionFailure | Wywoływane, gdy akcja na zasób nie powiedzie się. |
| Microsoft.Resources.ResourceActionSuccess | Wywoływane, gdy akcja na zasób powiedzie się. |
| Witryna Microsoft.Resources.ResourceDeleteCancel | Wywoływane po anulowaniu operacji usuwania. To zdarzenie ma miejsce, gdy wdrożenie szablonu zostanie anulowane. |
| Microsoft.Resources.ResourceDeleteFailure | Wywoływane, gdy operacja usuwania nie powiedzie się. |
| Microsoft.Resources.ResourceDeleteSuccess | Wywoływane po pomyślnym zakończeniu operacji usuwania. |
| Zasoby firmy Microsoft.ResourceWriteCancel | Wywoływane podczas tworzenia lub aktualizowania operacji jest anulowana. |
| Microsoft.Resources.ResourceWriteFailure | Wywoływane, gdy operacja tworzenia lub aktualizacji kończy się niepowodzeniem. |
| Microsoft.Resources.ResourceWriteSuccess | Wywoływane po pomyślnym utworzeniu lub aktualizacji operacji. |

### <a name="example-event"></a>Przykładowe zdarzenie

W poniższym przykładzie przedstawiono schemat zdarzenia **ResourceWriteSuccess.** Ten sam schemat jest używany dla **zdarzeń ResourceWriteFailure** i **ResourceWriteCancel** o różnych wartościach dla `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

W poniższym przykładzie przedstawiono schemat zdarzenia **ResourceDeleteSuccess.** Ten sam schemat jest używany dla **resourcedeletefailure** i **ResourceDeleteCancel** zdarzeń z różnymi wartościami dla `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

W poniższym przykładzie przedstawiono schemat zdarzenia **ResourceActionSuccess.** Ten sam schemat jest używany dla **ResourceActionFailure** i **ResourceActionCancel** zdarzeń o różnych wartościach dla `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia subskrypcji. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| autoryzacja | obiekt | Żądana autoryzacja dla operacji. |
| oświadczenia | obiekt | Właściwości oświadczeń. Aby uzyskać więcej informacji, zobacz [specyfikację JWT](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | ciąg | Identyfikator operacji do rozwiązywania problemów. |
| HttpRequest (Prośba o | obiekt | Szczegóły operacji. Ten obiekt jest uwzględniany tylko podczas aktualizowania istniejącego zasobu lub usuwania zasobu. |
| resourceProvider | ciąg | Dostawca zasobów dla operacji. |
| resourceUri | ciąg | Identyfikator URI zasobu w operacji. |
| operationName | ciąg | Operacja, która została podjęta. |
| status | ciąg | Stan operacji. |
| subscriptionId | ciąg | Identyfikator subskrypcji zasobu. |
| identyfikator dzierżawy | ciąg | Identyfikator dzierżawy zasobu. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł |Opis  |
|---------|---------|
| [Samouczek: Automatyzacja platformy Azure z siatką zdarzeń i zespołami Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Utwórz maszynę wirtualną, która wysyła zdarzenie. Zdarzenie wyzwala element runbook automatyzacji, który oznacza maszynę wirtualną i wyzwala komunikat, który jest wysyłany do kanału usługi Microsoft Teams. |
| [Jak: subskrybować wydarzenia za pośrednictwem portalu](subscribe-through-portal.md) | Użyj portalu, aby subskrybować zdarzenia dla subskrypcji platformy Azure. |
| [Interfejsu wiersza polecenia platformy Azure: subskrybowanie zdarzeń w ramach subskrypcji platformy Azure](./scripts/event-grid-cli-azure-subscription.md) |Przykładowy skrypt, który tworzy subskrypcję usługi Event Grid do subskrypcji platformy Azure i wysyła zdarzenia do elementu WebHook. |
| [Program PowerShell: subskrybowanie zdarzeń w ramach subskrypcji platformy Azure](./scripts/event-grid-powershell-azure-subscription.md)| Przykładowy skrypt, który tworzy subskrypcję usługi Event Grid do subskrypcji platformy Azure i wysyła zdarzenia do elementu WebHook. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
