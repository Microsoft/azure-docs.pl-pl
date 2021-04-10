---
title: Programowe tworzenie subskrypcji umowy Azure Enterprise Agreement przy użyciu najnowszych interfejsów API
description: Dowiedz się, jak tworzyć subskrypcje Enterprise Agreement platformy Azure programowo przy użyciu najnowszych wersji interfejsów API REST, interfejsu wiersza polecenia platformy Azure, Azure PowerShell i szablonów Azure Resource Manager.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8857582e29da03785427c59277c605b0b4d6c6b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932577"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Programowe tworzenie subskrypcji umowy Azure Enterprise Agreement przy użyciu najnowszych interfejsów API

Ten artykuł pomaga w programowym tworzeniu subskrypcji umowy Azure Enterprise Agreement dla konta rozliczeniowego umowy EA przy użyciu najnowszych wersji interfejsu API. Jeśli nadal używasz starszej wersji zapoznawczej, zobacz [Programowe tworzenie subskrypcji platformy Azure za pomocą interfejsów API w wersji zapoznawczej](programmatically-create-subscription-preview.md). 

W tym artykule dowiesz się, jak programowo tworzyć subskrypcje za pomocą usługi Azure Resource Manager.

Subskrypcja platformy Azure utworzona programowo podlega warunkom umowy, w ramach której nabyto usługi platformy Azure od firmy Microsoft lub autoryzowanego odsprzedawcy. Aby dowiedzieć się więcej, zobacz [informacje prawne dotyczące platformy Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję, musisz mieć rolę właściciela na koncie rejestracji. Istnieją dwa sposoby, aby uzyskać tę rolę:

* Administrator przedsiębiorstwa Twojej rejestracji może [przyznać Ci rolę właściciela konta](https://ea.azure.com/helpdocs/addNewAccount) (wymagane jest zalogowanie się), dzięki czemu zostaniesz właścicielem konta rejestracji.
* Aktualny właściciel konta rejestracji może [przyznać Ci dostęp](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Analogicznie, aby utworzyć subskrypcję umowy EA za pomocą jednostki usługi, musisz [przyznać jednostce usługi możliwość tworzenia subskrypcji](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). 
  > [!NOTE]
  > Upewnij się, że używasz prawidłowej wersji interfejsu API, aby nadać uprawnienia właściciela konta rejestracji. Na potrzeby tego artykułu oraz interfejsów API, które opisano w tym artykule, należy użyć interfejsu API w wersji [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). W przypadku migrowania do używania nowszych interfejsów API należy ponownie przyznać uprawnienia właściciela przy użyciu wersji [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Poprzednia konfiguracja, utworzona za pomocą [wersji 2015-07-01](grant-access-to-create-subscription.md), nie jest automatycznie konwertowana do użycia z nowszymi interfejsami API.

## <a name="find-accounts-you-have-access-to"></a>Odnajdowanie kont, do których masz dostęp

Po dodaniu Cię do konta rejestracji skojarzonego z właścicielem konta platforma Azure na podstawie relacji konto-rejestracja określa, komu wystawić rachunek za opłaty związane z subskrypcją. Wszystkie subskrypcje utworzone w ramach tego konta są rozliczane w ramach rejestracji umowy EA, do której przypisane jest konto. Aby utworzyć subskrypcje, musisz przekazać wartości dotyczące konta rejestracji i jednostki usługi w celu zostania właścicielem subskrypcji.

Aby uruchomić poniższe polecenia, zaloguj się w *katalogu macierzystym* właściciela konta, czyli katalogu, w którym domyślnie tworzone są subskrypcje.

### <a name="rest"></a>[REST](#tab/rest)

Wykonaj żądanie wyświetlenia listy wszystkich kont rejestracji, do których masz dostęp:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

Odpowiedź interfejsu API będzie zawierała listę wszystkich kont rejestracji, do których masz dostęp:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

Wartości dla zakresu rozliczeń i `id` są takie same. Element `id` dla Twojego konta rejestracji to zakres rozliczeniowy, w ramach którego inicjowane jest żądanie subskrypcji. Znajomość identyfikatora jest ważna, ponieważ jest to parametr wymagany, używany w dalszej części artykułu do utworzenia subskrypcji.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uzyskać tę wartość, należy użyć interfejsu wiersza polecenia platformy Azure lub interfejsu API REST.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Wykonaj żądanie wyświetlenia listy wszystkich kont rejestracji, do których masz dostęp:

```azurecli-interactive
> az billing account list
```

Odpowiedź zawiera listę wszystkich kont rejestracji, do których masz dostęp

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

Wartości dla zakresu rozliczeń i `id` są takie same. Element `id` dla Twojego konta rejestracji to zakres rozliczeniowy, w ramach którego inicjowane jest żądanie subskrypcji. Znajomość identyfikatora jest ważna, ponieważ jest to parametr wymagany, używany w dalszej części artykułu do utworzenia subskrypcji.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Tworzenie subskrypcji w ramach konkretnego konta rejestracji

W poniższym przykładzie na koncie rejestracji wybranym w poprzednim kroku zostanie utworzona subskrypcja o nazwie *Dev Team Subscription*. 

### <a name="rest"></a>[REST](#tab/rest)

Wywołaj interfejs API PUT, aby utworzyć żądanie utworzenia subskrypcji/alias.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

W treści żądania podaj jako element `billingScope` wartość `id` z jednego z elementów `enrollmentAccounts`.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Dozwolone wartości dla elementu `Workload` to `Production` i `DevTest`.

#### <a name="response"></a>Reakcja

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

Aby uzyskać stan żądania, możesz wykonać instrukcję GET dla tego samego adresu URL.

### <a name="request"></a>Żądanie

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Reakcja

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Stan w toku jest zwracany jako stan `Accepted` w elemencie `provisioningState`.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby zainstalować najnowszą wersję modułu zawierającego polecenie cmdlet `New-AzSubscriptionAlias`, uruchom polecenie `Install-Module Az.Subscription`. Aby zainstalować najnowszą wersję modułu PowerShellGet, zobacz [Uzyskiwanie modułu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Uruchom następujące polecenie [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription), używając zakresu rozliczeniowego `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

W ramach odpowiedzi z polecenia otrzymasz wartość identyfikatora subscriptionId.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw zainstaluj to rozszerzenie, uruchamiając polecenia `az extension add --name account` i `az extension add --name alias`.

Uruchom następujące polecenie [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create) i dołącz wartości elementów `billing-scope` oraz `id` z jednego z elementów `enrollmentAccounts`. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

W ramach odpowiedzi z polecenia otrzymasz wartość identyfikatora subscriptionId.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template"></a>Korzystanie z szablonu usługi Resource Manager

W poprzedniej sekcji pokazano, jak utworzyć subskrypcję za pomocą programu PowerShell, interfejsu wiersza polecenia lub API REST. Jeśli musisz zautomatyzować tworzenie subskrypcji, rozważ użycie szablonu Azure Resource Manager (szablon ARM).

Poniższy szablon tworzy subskrypcję. W `billingScope` polu podaj identyfikator konta rejestracji. W przypadku programu `targetManagementGroup` Podaj grupę zarządzania, w której chcesz utworzyć subskrypcję.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        },
        "targetManagementGroup": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the target management group to place the subscription."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]",
                "managementGroupId": "[tenantResourceId('Microsoft.Management/managementGroups/', parameters('targetManagementGroup'))]"
            }
        }
    ],
    "outputs": {}
}
```

Wdróż szablon na [poziomie grupy zarządzania](../../azure-resource-manager/templates/deploy-to-management-group.md).

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

Z treścią żądania:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      },
      "targetManagementGroup": {
        "value": "mg2"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321' targetManagementGroup=mg2
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ograniczenia interfejsu API tworzenia subskrypcji Enterprise platformy Azure

- Przy użyciu tego interfejsu API można tworzyć tylko subskrypcje Enterprise platformy Azure.
- Obowiązuje limit 2000 subskrypcji na jedno konto rejestracji. Po przekroczeniu tego limitu kolejne subskrypcje dla tego konta można utworzyć wyłącznie w witrynie Azure Portal. Aby utworzyć więcej subskrypcji za pomocą interfejsu API, musisz utworzyć kolejne konto rejestracji. Liczba anulowanych, usuniętych i przesłanych subskrypcji zbliża się do granicy 2000.
- Użytkownicy, którzy nie są właścicielami konta, ale zostali dodani do konta rejestracji przy użyciu systemu RBAC platformy Azure, nie mogą tworzyć subskrypcji w witrynie Azure Portal.
- Nie można wybrać dzierżawy, w której ma zostać utworzona subskrypcja. Subskrypcja jest zawsze tworzona w głównej dzierżawie właściciela konta. Jeśli chcesz przenieść subskrypcję do innej dzierżawy, zobacz [Zmiana dzierżawy subskrypcji](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Następne kroki

* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
* Aby zmienić grupę zarządzania dla subskrypcji, zobacz [przenoszenie subskrypcji](../../governance/management-groups/manage.md#move-subscriptions).
