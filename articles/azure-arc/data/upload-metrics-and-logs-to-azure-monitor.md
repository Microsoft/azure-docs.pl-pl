---
title: Przekazywanie danych użycia, metryk i dzienników do Azure Monitor
description: Przekazywanie spisu zasobów, danych użycia, metryk i dzienników do Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: a522a650413be056ff64d26e90b6c15cf88d9a7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643494"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Przekazywanie danych użycia, metryk i dzienników do Azure Monitor

Okresowo można eksportować informacje o użyciu dotyczące rozliczeń, metryk monitorowania i dzienników, a następnie przekazać je na platformę Azure. Eksportowanie i przekazywanie dowolnych z tych trzech typów danych spowoduje również utworzenie i zaktualizowanie zasobów grupy serwerów, wystąpienia zarządzanego SQL i PostgreSQL na platformie Azure.

> [!NOTE] 
> W okresie zapoznawczym nie ma kosztu korzystania z usług danych z obsługą usługi Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Przed przekazaniem danych użycia, metryk lub dzienników należy:

* Instalowanie narzędzi 
* [Rejestrowanie `Microsoft.AzureArcData` dostawcy zasobów](#register-the-resource-provider) 
* [Tworzenie jednostki usługi](#create-service-principal)

## <a name="install-tools"></a>Instalowanie narzędzi

Wymagane narzędzia obejmują: 
* Interfejs wiersza polecenia platformy Azure (az) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

Zobacz [Instalowanie narzędzi](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Przed przekazaniem metryk lub danych użytkownika do platformy Azure musisz upewnić się, że w subskrypcji platformy Azure `Microsoft.AzureArcData` zarejestrowano dostawcę zasobów.

Aby sprawdzić dostawcę zasobów, uruchom następujące polecenie:

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Jeśli dostawca zasobów nie jest obecnie zarejestrowany w Twojej subskrypcji, możesz go zarejestrować. Aby go zarejestrować, uruchom następujące polecenie.  Wykonanie polecenia może potrwać minutę lub dwie.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Tworzenie jednostki usługi

Nazwa główna usługi jest używana do przekazywania danych użycia i metryk.

Wykonaj następujące polecenia, aby utworzyć nazwę główną usługi przekazywania metryk:

> [!NOTE]
> Tworzenie jednostki usługi wymaga [pewnych uprawnień na platformie Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Aby utworzyć nazwę główną usługi, zaktualizuj Poniższy przykład. Zastąp `<ServicePrincipalName>` `SubscriptionId` `resourcegroup` wartości i wartościami i uruchom polecenie:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/{SubscriptionId}/resourceGroups/{resourcegroup}
```

Jeśli wcześniej utworzono nazwę główną usługi i wystarczy uzyskać bieżące poświadczenia, uruchom następujące polecenie, aby zresetować poświadczenia.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Na przykład aby utworzyć nazwę główną usługi o nazwie `azure-arc-metrics` , uruchom następujące polecenie

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

Przykładowe dane wyjściowe:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Zapisz `appId` wartości, `password` i `tenant` w zmiennej środowiskowej do późniejszego użycia. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Po utworzeniu jednostki usługi Przypisz jednostkę usługi do odpowiedniej roli. 

## <a name="assign-roles-to-the-service-principal"></a>Przypisywanie ról do jednostki usługi

Uruchom to polecenie, aby przypisać nazwę główną usługi do `Monitoring Metrics Publisher` roli w subskrypcji, w której znajdują się zasoby wystąpienia bazy danych:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Podczas uruchamiania ze środowiska systemu Windows należy użyć podwójnych cudzysłowów dla nazw ról.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/{SubscriptionID}/resourceGroups/{resourcegroup}
```

::: zone-end

Przykładowe dane wyjściowe:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Za pomocą jednostki usługi przypisanej do odpowiedniej roli można kontynuować przekazywanie metryk lub danych użytkownika. 

## <a name="upload-logs-metrics-or-user-data"></a>Przekazywanie dzienników, metryk lub danych użytkownika

Konkretne kroki przekazywania dzienników, metryk lub danych użytkownika różnią się w zależności od typu przekazywanych informacji. 

[Przekaż dzienniki do Azure Monitor](upload-logs.md)

[Przekaż metryki do Azure Monitor](upload-metrics.md)

[Przekazywanie danych użycia do Azure Monitor](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Ogólne wskazówki dotyczące eksportowania i przekazywania użycia, metryk

Operacje tworzenia, odczytu, aktualizacji i usuwania (CRUD) w usługach danych z obsługą usługi Azure Arc są rejestrowane na potrzeby rozliczeń i monitorowania. Istnieją usługi w tle, które monitorują te operacje CRUD i odpowiednio obliczają zużycie. Rzeczywiste obliczenie użycia lub zużycia odbywa się zgodnie z harmonogramem i jest wykonywane w tle. 

W trakcie okresu zapoznawczego ten proces odbywa się nocnie. Ogólne wskazówki to przekazywanie użycia tylko raz dziennie. Gdy informacje o użyciu zostaną wyeksportowane i przekazane wiele razy w tym samym okresie 24 godzin, tylko spis zasobów zostanie zaktualizowany w Azure Portal ale nie w przypadku użycia zasobów.

W przypadku przekazywania metryk usługa Azure monitor akceptuje tylko ostatnie 30 minut danych ([Dowiedz się więcej](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Wskazówki dotyczące przekazywania metryk polegają na przekazaniu metryk bezpośrednio po utworzeniu pliku eksportu, aby można było wyświetlić cały zestaw danych w Azure Portal. Na przykład jeśli zostały wyeksportowane metryki o godzinie 2:00 PM i uruchomiono polecenie przekazywania o godzinie 2:50 PM. Ponieważ Azure Monitor akceptuje tylko dane z ostatnich 30 minut, w portalu mogą nie być widoczne żadne dane. 

## <a name="next-steps"></a>Następne kroki

[Informacje o jednostkach usługi](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Przekaż dane dotyczące rozliczeń na platformę Azure i Wyświetl je w Azure Portal](view-billing-data-in-azure.md)

[Wyświetlanie zasobu usługi Azure Arc Data Controller w Azure Portal](view-data-controller-in-azure-portal.md)
