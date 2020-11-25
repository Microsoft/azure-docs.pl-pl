---
title: Programowe tworzenie subskrypcji platformy Azure dla umowy Microsoft Partner Agreement przy użyciu najnowszych interfejsów API
description: Dowiedz się, jak programowo tworzyć subskrypcje platformy Azure dla umowy Microsoft Partner Agreement przy użyciu najnowszych wersji interfejsu API REST, interfejsu wiersza polecenia platformy Azure i usługi Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: ab5e51186fba7fc0dc8264026d314c32ce81d73f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94850928"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>Programowe tworzenie subskrypcji platformy Azure dla umowy Microsoft Partner Agreement przy użyciu najnowszych interfejsów API

Ten artykuł ułatwia programowe tworzenie subskrypcji platformy Azure dla umowy Microsoft Partner Agreement przy użyciu najnowszych wersji interfejsu API. Jeśli nadal używasz starszej wersji zapoznawczej, zobacz [Programowe tworzenie subskrypcji platformy Azure za pomocą interfejsów API w wersji zapoznawczej](programmatically-create-subscription-preview.md). 

W tym artykule dowiesz się, jak programowo tworzyć subskrypcje za pomocą usługi Azure Resource Manager.

Subskrypcja platformy Azure utworzona programowo podlega warunkom umowy, w ramach której nabyto usługi platformy Azure od firmy Microsoft lub autoryzowanego odsprzedawcy. Aby dowiedzieć się więcej, zobacz [informacje prawne dotyczące platformy Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję dla konta rozliczeniowego, musisz mieć rolę globalnego administratora lub agenta administratora na koncie dostawcy rozwiązań w chmurze w swojej organizacji. Aby dowiedzieć się więcej, zobacz [Centrum partnerskie — przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

Jeśli nie wiesz, czy masz dostęp do konta umowy Microsoft Partner Agreement, zobacz [Sprawdzanie dostępu do umowy Microsoft Partner Agreement](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement).

W poniższych przykładach użyto interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>Odnajdowanie kont rozliczeniowych, do których masz dostęp

Wykonaj następujące żądanie, aby wyświetlić listę wszystkich kont rozliczeniowych, do których masz dostęp.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccount-MPA)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

Odpowiedź interfejsu API zawiera listę wszystkich kont rozliczeniowych.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Użyj właściwości `displayName`, aby określić konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że parametr agreementType tego konta to *MicrosoftPartnerAgreement*. Skopiuj wartość `name` tego konta. Przykładowo aby utworzyć subskrypcję dla konta rozliczeniowego `Contoso`, skopiuj wartość `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts-MPA)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts-MPA)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-customers-that-have-azure-plans"></a>Odnajdowanie klientów z planami platformy Azure

Wykonaj następujące żądanie z wartością elementu `name` skopiowaną z pierwszego kroku (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```), aby uzyskać listę wszystkich klientów na koncie rozliczeniowym, dla których możesz utworzyć subskrypcje platformy Azure.

### <a name="rest"></a>[REST](#tab/rest-getCustomers)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

Odpowiedź interfejsu API będzie zawierała listę klientów z planami platformy Azure na koncie rozliczeniowym. Dla tych klientów można utworzyć subskrypcje.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

Użyj właściwości `displayName`, aby określić klienta, dla którego chcesz utworzyć subskrypcje. Skopiuj wartość `id` klienta. Przykładowo aby utworzyć subskrypcję dla konta `Fabrikam toys`, skopiuj wartość `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Wklej tę wartość w dogodnym miejscu, aby można było z niej skorzystać w kolejnych krokach.

<!--
### [PowerShell](#tab/azure-powershell-getCustomers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getCustomers)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="get-the-resellers-for-a-customer"></a>Uzyskiwanie odsprzedawców dla klienta

Ta sekcja jest opcjonalna tylko dla dostawców pośrednich.

Jeśli jesteś dostawcą pośrednim w modelu dwuwarstwowym dostawców usług w chmurze, możesz określić odsprzedawcę podczas tworzenia subskrypcji dla klientów.

Wykonaj następujące żądanie z wartością elementu `id` skopiowaną w drugim kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```), aby uzyskać listę wszystkich odsprzedawców dostępnych dla klienta.

### <a name="rest"></a>[REST](#tab/rest-getIndirectResellers)

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
Odpowiedź interfejsu API będzie zawierała listę odsprzedawców dla klienta:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

Użyj właściwości `description`, aby określić odsprzedawcę, który będzie skojarzony z subskrypcją. Skopiuj wartość `resellerId` odsprzedawcy. Przykładowo aby skojarzyć odsprzedawcę `Wingtip`, skopiuj wartość `3xxxxx`. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku.

<!--
### [PowerShell](#tab/azure-powershell-getIndirectResellers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getIndirectResellers)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-for-a-customer"></a>Tworzenie subskrypcji dla klienta

W poniższym przykładzie zostanie utworzona subskrypcja o nazwie *Dev Team Subscription* dla klienta *Fabrikam Toys*, ze skojarzonym odsprzedawcą *Wingtip*. Użyjesz skopiowanego zakresu rozliczeniowego z poprzedniego kroku: `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest-MPA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Treść żądania

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>Reakcja

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

Przekaż opcjonalną wartość *resellerId* skopiowaną w drugim kroku w treści żądania interfejsu API.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell-MPA)

Aby zainstalować najnowszą wersję modułu zawierającego polecenie cmdlet `New-AzSubscriptionAlias`, uruchom polecenie `Install-Module Az.Subscription`. Aby zainstalować najnowszą wersję modułu PowerShellGet, zobacz [Uzyskiwanie modułu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Uruchom następujące polecenie [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription), używając zakresu rozliczeniowego `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

W ramach odpowiedzi z polecenia otrzymasz wartość identyfikatora subscriptionId.

```azurepowershell
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

Przekaż opcjonalną wartość *resellerId* skopiowaną w drugim kroku w wywołaniu polecenia `New-AzSubscriptionAlias`.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli-MPA)

Najpierw zainstaluj to rozszerzenie, uruchamiając polecenia `az extension add --name account` i `az extension add --name alias`.

Uruchom następujące polecenie [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true). 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
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

Przekaż opcjonalną wartość *resellerId* skopiowaną w drugim kroku w wywołaniu polecenia `az account alias create`.

---

## <a name="next-steps"></a>Następne kroki

* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
