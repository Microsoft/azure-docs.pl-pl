---
title: Błędy rejestracji dostawcy zasobów
description: Opisuje sposób rozwiązywania problemów z rejestracją dostawcy zasobów platformy Azure podczas wdrażania zasobów przy użyciu Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 143cf03a33739f43a29af94fc2f8a336fb3aef8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89076671"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Usuwanie błędów związanych z rejestracją dostawcy zasobów

W tym artykule opisano błędy, które można napotkać podczas korzystania z dostawcy zasobów, który nie był wcześniej używany w Twojej subskrypcji.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

Podczas wdrażania zasobu może zostać wyświetlony następujący kod błędu i komunikat:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Można też otrzymać podobny komunikat informujący o tym, że:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Komunikat o błędzie powinien zawierać sugestie dotyczące obsługiwanych lokalizacji i wersji interfejsu API. Możesz zmienić szablon na jedną z sugerowanych wartości. Większość dostawców jest automatycznie rejestrowana przez Azure Portal lub interfejs wiersza polecenia, którego używasz, ale nie wszystkie. Jeśli nie użyto dostawcy zasobów określonego wcześniej, może być konieczne zarejestrowanie tego dostawcy.

W przypadku wyłączenia automatycznego zamykania maszyn wirtualnych może zostać wyświetlony komunikat o błędzie podobny do:

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>Przyczyna

Te błędy są wyświetlane z jednego z następujących powodów:

* Wymagany dostawca zasobów nie został zarejestrowany dla Twojej subskrypcji
* Wersja interfejsu API nie jest obsługiwana dla typu zasobu
* Lokalizacja nie jest obsługiwana dla typu zasobu
* W przypadku automatycznego zamykania maszyn wirtualnych dostawca zasobów Microsoft. wspólny musi być zarejestrowany.

## <a name="solution-1---powershell"></a>Rozwiązanie 1 — PowerShell

W przypadku programu PowerShell Użyj polecenia **Get-AzResourceProvider** , aby wyświetlić stan rejestracji.

```powershell
Get-AzResourceProvider -ListAvailable
```

Aby zarejestrować dostawcę, należy użyć **register-AzResourceProvider** i podać nazwę dostawcy zasobów, który ma zostać zarejestrowany.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

Aby uzyskać obsługiwane lokalizacje dla określonego typu zasobu, użyj:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Aby uzyskać obsługiwane wersje interfejsu API dla określonego typu zasobu, użyj:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>Rozwiązanie 2 — interfejs wiersza polecenia platformy Azure

Aby sprawdzić, czy dostawca jest zarejestrowany, użyj `az provider list` polecenia.

```azurecli-interactive
az provider list
```

Aby zarejestrować dostawcę zasobów, użyj `az provider register` polecenia i określ *przestrzeń nazw* do zarejestrowania.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Aby wyświetlić obsługiwane lokalizacje i wersje interfejsu API dla typu zasobu, użyj:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>Rozwiązanie 3 — Azure Portal

Możesz zobaczyć stan rejestracji i zarejestrować przestrzeń nazw dostawcy zasobów za pomocą portalu.

1. W portalu wybierz pozycję **wszystkie usługi**.

   ![Wybierz wszystkie usługi](./media/error-register-resource-provider/select-all-services.png)

1. Wybierz pozycję **Subskrypcje**.

   ![Wybieranie subskrypcji](./media/error-register-resource-provider/select-subscriptions.png)

1. Z listy subskrypcji wybierz subskrypcję, której chcesz używać do rejestrowania dostawcy zasobów.

   ![Wybierz subskrypcję, aby zarejestrować dostawcę zasobów](./media/error-register-resource-provider/select-subscription-to-register.png)

1. W przypadku subskrypcji wybierz pozycję **dostawcy zasobów**.

   ![Wybierz dostawców zasobów](./media/error-register-resource-provider/select-resource-provider.png)

1. Zapoznaj się z listą dostawców zasobów i w razie potrzeby wybierz łącze **zarejestruj** , aby zarejestrować dostawcę zasobów typu, który próbujesz wdrożyć.

   ![Wyświetl listę dostawców zasobów](./media/error-register-resource-provider/list-resource-providers.png)
