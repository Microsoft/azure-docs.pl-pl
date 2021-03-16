---
title: Dostawcy zasobów i typy zasobów
description: Opisuje dostawców zasobów, którzy obsługują Azure Resource Manager. Opisano w nim schematy, dostępne wersje interfejsu API i regiony, które mogą hostować zasoby.
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 584f3065d0e696f2ee379a8cf6c048994a1e68d5
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493139"
---
# <a name="azure-resource-providers-and-types"></a>Dostawcy i typy zasobów platformy Azure

Podczas wdrażania zasobów często konieczne jest pobranie informacji o dostawcach zasobów i typach. Na przykład w celu przechowywania kluczy i kluczy tajnych należy użyć dostawcy zasobów Microsoft.KeyVault. Ten dostawca zasobów udostępnia typ zasobu o nazwie magazyny umożliwiający utworzenie magazynu kluczy.

Nazwa typu zasobu ma następujący format: **{dostawca_zasobów}/{typ_zasobu}**. Typ zasobu dla magazynu kluczy to **Microsoft.KeyVault/magazyny**.

W tym artykule omówiono sposób wykonywania następujących zadań:

* Wyświetlanie wszystkich dostawców zasobów na platformie Azure
* Sprawdź stan rejestracji dostawcy zasobów
* Rejestrowanie dostawcy zasobów
* Wyświetlanie typów zasobów dla dostawcy zasobów
* Wyświetl prawidłowe lokalizacje dla typu zasobu
* Wyświetl prawidłowe wersje interfejsu API dla typu zasobu

Kroki te można wykonać za pomocą Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać listę, która mapuje dostawców zasobów na usługi platformy Azure, zobacz [dostawcy zasobów dla usług platformy Azure](azure-services-resource-providers.md).

## <a name="register-resource-provider"></a>Rejestrowanie dostawcy zasobów

Przed użyciem dostawcy zasobów należy zarejestrować subskrypcję platformy Azure dla dostawcy zasobów. Rejestracja konfiguruje subskrypcję do pracy z dostawcą zasobów. Niektórzy dostawcy zasobów są domyślnie rejestrowani. Aby uzyskać listę dostawców zasobów zarejestrowanych domyślnie, zobacz [dostawcy zasobów dla usług platformy Azure](azure-services-resource-providers.md).

Inni dostawcy zasobów są zarejestrowani automatycznie podczas wykonywania określonych działań. Podczas wdrażania szablonu Azure Resource Manager wszystkie wymagane dostawcy zasobów są automatycznie rejestrowane. Po utworzeniu zasobu za pomocą portalu dostawca zasobów jest zwykle zarejestrowany dla Ciebie. W przypadku innych scenariuszy może być konieczne ręczne zarejestrowanie dostawcy zasobów. 

W tym artykule pokazano, jak sprawdzić stan rejestracji dostawcy zasobów i zarejestrować go w razie potrzeby. Musisz mieć uprawnienia do wykonania `/register/action` operacji dla dostawcy zasobów. Uprawnienie jest zawarte w rolach współautor i właściciela.

> [!IMPORTANT]
> Zarejestruj dostawcę zasobów tylko wtedy, gdy wszystko jest gotowe do użycia. Krok rejestracji umożliwia zachowanie najniższych uprawnień w ramach subskrypcji. Złośliwy użytkownik nie może używać dostawców zasobów, którzy nie są zarejestrowani.

Kod aplikacji nie powinien blokować tworzenia zasobów dla dostawcy zasobów, który jest w stanie **rejestrowania** . Po zarejestrowaniu dostawcy zasobów operacja jest wykonywana osobno dla każdego obsługiwanego regionu. Aby można było tworzyć zasoby w regionie, rejestracja musi zostać zakończona tylko w tym regionie. Gdy dostawca zasobów nie jest blokowany w stanie rejestracji, aplikacja może kontynuować pracę znacznie szybciej niż oczekiwanie na ukończenie wszystkich regionów.

Nie można wyrejestrować dostawcy zasobów, gdy nadal masz typy zasobów od tego dostawcy zasobów w ramach subskrypcji.

## <a name="azure-portal"></a>Azure Portal

### <a name="register-resource-provider"></a>Rejestrowanie dostawcy zasobów

Aby wyświetlić wszystkich dostawców zasobów i status rejestracji dla Twojej subskrypcji:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu Azure Portal Wyszukaj pozycję **subskrypcje**. Wybierz ją z dostępnych opcji.

   :::image type="content" source="./media/resource-providers-and-types/search-subscriptions.png" alt-text="Wyszukaj subskrypcje":::

1. Wybierz subskrypcję, którą chcesz wyświetlić.

   :::image type="content" source="./media/resource-providers-and-types/select-subscription.png" alt-text="Wybieranie subskrypcji":::

1. W menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **dostawcy zasobów**.

   :::image type="content" source="./media/resource-providers-and-types/select-resource-providers.png" alt-text="Wybierz dostawców zasobów":::

6. Znajdź dostawcę zasobów, który chcesz zarejestrować, a następnie wybierz pozycję **zarejestruj**. Aby zachować najmniejsze uprawnienia w subskrypcji, należy zarejestrować tych dostawców zasobów, których można użyć.

   :::image type="content" source="./media/resource-providers-and-types/register-resource-provider.png" alt-text="Zarejestruj dostawców zasobów":::

### <a name="view-resource-provider"></a>Wyświetl dostawcę zasobów

Aby wyświetlić informacje dotyczące konkretnego dostawcy zasobów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.
3. W polu **wszystkie usługi** wprowadź **Eksplorator zasobów**, a następnie wybierz pozycję **Eksplorator zasobów**.

    ![Wybierz wszystkie usługi](./media/resource-providers-and-types/select-resource-explorer.png)

4. Rozwiń węzeł **dostawcy** , wybierając strzałkę w prawo.

    ![Wybierz dostawców](./media/resource-providers-and-types/select-providers.png)

5. Rozwiń dostawcę zasobów i typ zasobu, który chcesz wyświetlić.

    ![Wybierz typ zasobu](./media/resource-providers-and-types/select-resource-type.png)

6. Menedżer zasobów jest obsługiwana we wszystkich regionach, ale wdrażane zasoby mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą wystąpić ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób. Eksplorator zasobów wyświetla prawidłowe lokalizacje dla typu zasobu.

    ![Pokaż lokalizacje](./media/resource-providers-and-types/show-locations.png)

7. Wersja interfejsu API odpowiada wersji operacji interfejsu API REST wydanej przez dostawcę zasobów. Ponieważ dostawca zasobów udostępnia nowe funkcje, zwalnia nową wersję interfejsu API REST. Eksplorator zasobów wyświetla prawidłowe wersje interfejsu API dla typu zasobu.

    ![Pokaż wersje interfejsu API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i status rejestracji dla Twojej subskrypcji, użyj:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Zwracające wyniki podobne do:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Aby wyświetlić wszystkich zarejestrowanych dostawców zasobów dla subskrypcji, użyj:

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

Aby zachować najmniejsze uprawnienia w subskrypcji, należy zarejestrować tych dostawców zasobów, których można użyć. Aby zarejestrować dostawcę zasobów, użyj:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Zwracające wyniki podobne do:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Aby wyświetlić informacje dotyczące konkretnego dostawcy zasobów, użyj:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Zwracające wyniki podobne do:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Aby wyświetlić typy zasobów dla dostawcy zasobów, użyj:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Które zwraca:

```output
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST wydanej przez dostawcę zasobów. Ponieważ dostawca zasobów udostępnia nowe funkcje, zwalnia nową wersję interfejsu API REST.

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, użyj:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Które zwraca:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwana we wszystkich regionach, ale wdrażane zasoby mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą wystąpić ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób.

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, użyj.

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Które zwraca:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wyświetlić wszystkich dostawców zasobów na platformie Azure i status rejestracji dla Twojej subskrypcji, użyj:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Zwracające wyniki podobne do:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Aby wyświetlić wszystkich zarejestrowanych dostawców zasobów dla subskrypcji, użyj:

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

Aby zachować najmniejsze uprawnienia w subskrypcji, należy zarejestrować tych dostawców zasobów, których można użyć. Aby zarejestrować dostawcę zasobów, użyj:

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

Zwraca komunikat informujący o tym, że rejestracja jest w toku.

Aby wyświetlić informacje dotyczące konkretnego dostawcy zasobów, użyj:

```azurecli-interactive
az provider show --namespace Microsoft.Batch
```

Zwracające wyniki podobne do:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Aby wyświetlić typy zasobów dla dostawcy zasobów, użyj:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Które zwraca:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

Wersja interfejsu API odpowiada wersji operacji interfejsu API REST wydanej przez dostawcę zasobów. Ponieważ dostawca zasobów udostępnia nowe funkcje, zwalnia nową wersję interfejsu API REST.

Aby uzyskać dostępne wersje interfejsu API dla typu zasobu, użyj:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Które zwraca:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Menedżer zasobów jest obsługiwana we wszystkich regionach, ale wdrażane zasoby mogą nie być obsługiwane we wszystkich regionach. Ponadto mogą wystąpić ograniczenia dotyczące subskrypcji, które uniemożliwiają korzystanie z niektórych regionów, które obsługują zasób.

Aby uzyskać obsługiwane lokalizacje dla typu zasobu, użyj.

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Które zwraca:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat tworzenia szablonów Menedżer zasobów, zobacz [tworzenie Azure Resource Manager szablonów](../templates/template-syntax.md). 
* Aby wyświetlić Schematy szablonów dostawcy zasobów, zobacz [Dokumentacja szablonu](/azure/templates/).
* Aby uzyskać listę, która mapuje dostawców zasobów na usługi platformy Azure, zobacz [dostawcy zasobów dla usług platformy Azure](azure-services-resource-providers.md).
* Aby wyświetlić operacje dla dostawcy zasobów, zobacz [interfejs API REST platformy Azure](/rest/api/).
