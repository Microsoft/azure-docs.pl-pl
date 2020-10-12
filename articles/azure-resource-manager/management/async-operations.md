---
title: Stan operacji asynchronicznych
description: Opisuje sposób śledzenia operacji asynchronicznych na platformie Azure. Pokazuje wartości używane do uzyskania stanu długotrwałej operacji.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: e2c5ba137d5277466cf1b382d2b0b1bc02259f00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723456"
---
# <a name="track-asynchronous-azure-operations"></a>Śledź asynchroniczne operacje na platformie Azure

Niektóre operacje REST platformy Azure są uruchamiane asynchronicznie, ponieważ nie można szybko ukończyć operacji. W tym artykule opisano sposób śledzenia stanu operacji asynchronicznych za pomocą wartości zwracanych w odpowiedzi.  

## <a name="status-codes-for-asynchronous-operations"></a>Kody stanu operacji asynchronicznych

Operacja asynchroniczna początkowo zwraca kod stanu HTTP:

* 201 (utworzone)
* 202 (zaakceptowane)

Po pomyślnym zakończeniu operacji zwraca jedną z:

* 200 (OK)
* 204 (brak zawartości)

Zapoznaj się z [dokumentacją interfejsu API REST](/rest/api/azure/) , aby zobaczyć odpowiedzi dla wykonywanej operacji.

Po otrzymaniu kodu odpowiedzi 201 lub 202 można przystąpić do monitorowania stanu operacji.

## <a name="url-to-monitor-status"></a>Adres URL do monitorowania stanu

Istnieją dwa różne sposoby monitorowania stanu operacji asynchronicznej. Należy określić prawidłowe podejście, sprawdzając wartości nagłówka, które są zwracane z oryginalnego żądania. Najpierw Wyszukaj:

* `Azure-AsyncOperation` -Adres URL służący do sprawdzania stanu trwającego operacji. Jeśli operacja zwróci tę wartość, użyj jej do śledzenia stanu operacji.
* `Retry-After` — Liczba sekund oczekiwania przed sprawdzeniem stanu operacji asynchronicznej.

Jeśli `Azure-AsyncOperation` nie jest jedną z wartości nagłówka, Wyszukaj:

* `Location` -Adres URL służący do określania, kiedy operacja została ukończona. Ta wartość jest używana tylko wtedy, gdy Azure-AsyncOperation nie jest zwracana.
* `Retry-After` — Liczba sekund oczekiwania przed sprawdzeniem stanu operacji asynchronicznej.

## <a name="azure-asyncoperation-request-and-response"></a>Żądanie i odpowiedź Azure-AsyncOperation

Jeśli masz adres URL z `Azure-AsyncOperation` wartości nagłówka, Wyślij żądanie Get do tego adresu URL. Użyj wartości z, `Retry-After` Aby zaplanować częstotliwość sprawdzania stanu. Uzyskasz obiekt odpowiedzi, który wskazuje stan operacji. Podczas sprawdzania stanu operacji przy użyciu adresu URL jest zwracana inna odpowiedź `Location` . Aby uzyskać więcej informacji na temat odpowiedzi z adresu URL lokalizacji, zobacz [Tworzenie konta magazynu (202 z lokalizacją i ponawianie operacji po)](#create-storage-account-202-with-location-and-retry-after).

Właściwości odpowiedzi mogą się różnić, ale zawsze zawierają stan operacji asynchronicznej.

```json
{
    "status": "{status-value}"
}
```

W poniższym przykładzie pokazano inne wartości, które mogą zostać zwrócone w wyniku operacji:

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

Obiekt Error jest zwracany, gdy stan jest zakończony niepowodzeniem lub anulowane. Wszystkie inne wartości są opcjonalne. Otrzymana odpowiedź może wyglądać inaczej niż w przypadku przykładu.

## <a name="provisioningstate-values"></a>provisioningState wartości

Operacje, które tworzą, aktualizują lub usuwają (PUT, PATCH, Usuń) zasobu zwykle zwracają `provisioningState` wartość. Po zakończeniu operacji jest zwracana jedna z następujących trzech wartości:

* Sukces
* Niepowodzenie
* Anulowane

Wszystkie inne wartości wskazują, że operacja jest nadal uruchomiona. Dostawca zasobów może zwrócić dostosowaną wartość wskazującą jej stan. Na przykład może zostać **zaakceptowany** , gdy żądanie zostanie odebrane i uruchomione.

## <a name="example-requests-and-responses"></a>Przykładowe żądania i odpowiedzi

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Uruchom maszynę wirtualną (202 z platformą Azure-AsyncOperation)

Ten przykład pokazuje, jak określić stan [operacji uruchamiania maszyn wirtualnych](/rest/api/compute/virtualmachines/start). Początkowe żądanie ma następujący format:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

Zwraca kod stanu 202. Wśród wartości nagłówka widoczne są następujące dane:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Aby sprawdzić stan operacji asynchronicznej, wysyłaj kolejne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
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

Ten przykład pokazuje, jak określić stan operacji wdrażania [na potrzeby wdrażania zasobów](/rest/api/resources/deployments/createorupdate) na platformie Azure. Początkowe żądanie ma następujący format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

Zwraca kod stanu 201. Treść odpowiedzi obejmuje:

```json
"provisioningState":"Accepted",
```

Wśród wartości nagłówka widoczne są następujące dane:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Aby sprawdzić stan operacji asynchronicznej, wysyłaj kolejne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Treść odpowiedzi zawiera stan operacji:

```json
{
    "status": "Running"
}
```

Po zakończeniu wdrożenia odpowiedź zawiera:

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Utwórz konto magazynu (202 z lokalizacją i ponów próbę po)

Ten przykład pokazuje, jak określić stan [operacji tworzenia dla kont magazynu](/rest/api/storagerp/storageaccounts/create). Początkowe żądanie ma następujący format:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

Treść żądania zawiera właściwości konta magazynu:

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Zwraca kod stanu 202. Spośród wartości nagłówka widoczne są następujące dwie wartości:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Po upływie liczby sekund określonej w polu ponów próbę Sprawdź stan operacji asynchronicznej, wysyłając kolejne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

Jeśli żądanie jest nadal uruchomione, zostanie wyświetlony kod stanu 202. Jeśli żądanie zostało zakończone, otrzymano kod stanu 200 i treść odpowiedzi zawiera właściwości konta magazynu, które zostało utworzone.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać dokumentację dotyczącą każdej operacji REST, zobacz [dokumentację interfejsu API REST](/rest/api/azure/).
* Aby uzyskać informacje na temat wdrażania szablonów za pomocą interfejsu API REST Menedżer zasobów, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejs API rest Menedżer zasobów](../templates/deploy-rest.md).