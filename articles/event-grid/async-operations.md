---
title: Stan Event Grid operacji asynchronicznych
description: Opisuje sposób śledzenia Event Grid operacji asynchronicznych na platformie Azure. Pokazuje wartości używane do uzyskania stanu długotrwałej operacji.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: baae7b097a0b696d405c0e7ea3d3bdeb326f23b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89011688"
---
# <a name="track-event-grid-asynchronous-azure-operations"></a>Śledź Event Grid asynchroniczne operacje na platformie Azure
Niektóre operacje REST platformy Azure są uruchamiane asynchronicznie, ponieważ nie można szybko ukończyć operacji. W tym artykule opisano sposób śledzenia stanu operacji asynchronicznych za pomocą wartości zwracanych w odpowiedzi.  

## <a name="status-codes-for-asynchronous-operations"></a>Kody stanu operacji asynchronicznych
Operacja asynchroniczna początkowo zwraca kod stanu HTTP:

* 201 (utworzone)
* 202 (zaakceptowane) 

Po pomyślnym zakończeniu operacji zwraca jedną z:

* 200 (OK)
* 204 (brak zawartości) 

Zapoznaj się z [dokumentacją interfejsu API REST](/rest/api/) , aby zobaczyć odpowiedzi dla wykonywanej operacji.

## <a name="monitor-status-of-operation"></a>Monitoruj stan operacji
Asynchroniczne operacje REST zwracają wartości nagłówka, których można użyć do określenia stanu operacji. Istnieją potencjalnie trzy wartości nagłówka do sprawdzenia:

* `Azure-AsyncOperation` -Adres URL służący do sprawdzania stanu trwającego operacji. Jeśli operacja zwróci tę wartość, zawsze używaj jej (zamiast lokalizacji) do śledzenia stanu operacji.
* `Location` -Adres URL służący do określania, kiedy operacja została ukończona. Ta wartość jest używana tylko wtedy, gdy Azure-AsyncOperation nie jest zwracana.
* `Retry-After` — Liczba sekund oczekiwania przed sprawdzeniem stanu operacji asynchronicznej.

Jednak nie każda operacja asynchroniczna zwraca wszystkie te wartości. Na przykład może być konieczne oszacowanie wartości nagłówka Azure-AsyncOperation dla jednej operacji oraz wartości nagłówka lokalizacji dla innej operacji. 

Możesz pobrać wartości nagłówka, tak jak pobieranie dowolnej wartości nagłówka dla żądania. Na przykład w języku C# pobierana jest wartość nagłówka z `HttpWebResponse` obiektu o nazwie `response` przy użyciu następującego kodu:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Żądanie i odpowiedź Azure-AsyncOperation

Aby uzyskać stan operacji asynchronicznej, Wyślij żądanie GET do adresu URL w Azure-AsyncOperation wartości nagłówka.

Treść odpowiedzi z tej operacji zawiera informacje o operacji. W poniższym przykładzie przedstawiono możliwe wartości zwracane przez operację:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

`status`Zwracana jest tylko dla wszystkich odpowiedzi. Obiekt Error jest zwracany, gdy stan jest zakończony niepowodzeniem lub anulowane. Wszystkie inne wartości są opcjonalne; w związku z tym odpowiedź, którą otrzymasz, może wyglądać inaczej niż w przypadku przykładu.

## <a name="provisioningstate-values"></a>provisioningState wartości

Operacje, które tworzą, aktualizują lub usuwają (PUT, PATCH, Usuń) zasobu zwykle zwracają `provisioningState` wartość. Po zakończeniu operacji jest zwracana jedna z następujących trzech wartości: 

* Powodzenie
* Niepowodzenie
* Anulowane

Wszystkie inne wartości wskazują, że operacja jest nadal uruchomiona. Dostawca zasobów może zwrócić dostosowaną wartość wskazującą jej stan. Na przykład może zostać **zaakceptowany** , gdy żądanie zostanie odebrane i uruchomione.


## <a name="example-requests-and-responses"></a>Przykładowe żądania i odpowiedzi

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Uruchom maszynę wirtualną (202 z platformą Azure-AsyncOperation)
Ten przykład pokazuje, jak określić stan operacji **uruchamiania** maszyn wirtualnych. Początkowe żądanie ma następujący format:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Zwraca kod stanu 202. Wśród wartości nagłówka widoczne są następujące dane:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Aby sprawdzić stan operacji asynchronicznej, wysyłaj kolejne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Treść odpowiedzi zawiera stan operacji:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Wdrażanie zasobów (201 przy użyciu platformy Azure — AsyncOperation)

Ten przykład pokazuje, jak określić **stan operacji wdrażania na potrzeby wdrażania zasobów** na platformie Azure. Początkowe żądanie ma następujący format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Zwraca kod stanu 201. Treść odpowiedzi obejmuje:

```json
"provisioningState":"Accepted",
```

Wśród wartości nagłówka widoczne są następujące dane:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Aby sprawdzić stan operacji asynchronicznej, wysyłaj kolejne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Treść odpowiedzi zawiera stan operacji:

```json
{"status":"Running"}
```

Po zakończeniu wdrożenia odpowiedź zawiera:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Utwórz konto magazynu (202 z lokalizacją i ponów próbę po)

Ten przykład pokazuje, jak określić stan operacji **tworzenia** dla kont magazynu. Początkowe żądanie ma następujący format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

Treść żądania zawiera właściwości konta magazynu:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Zwraca kod stanu 202. Spośród wartości nagłówka widoczne są następujące dwie wartości:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Po upływie liczby sekund określonej w polu ponów próbę Sprawdź stan operacji asynchronicznej, wysyłając kolejne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Jeśli żądanie jest nadal uruchomione, zostanie wyświetlony kod stanu 202. Jeśli żądanie zostało zakończone, otrzymano kod stanu 200 i treść odpowiedzi zawiera właściwości konta magazynu, które zostało utworzone.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać dokumentację dotyczącą każdej operacji REST, zobacz [dokumentację interfejsu API REST](/rest/api/).
* Aby uzyskać informacje na temat wdrażania szablonów za pomocą interfejsu API REST Menedżer zasobów, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejs API rest Menedżer zasobów](../azure-resource-manager/templates/deploy-rest.md).