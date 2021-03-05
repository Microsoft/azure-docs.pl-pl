---
title: Programowe tworzenie subskrypcji platformy Azure dla Umowy z Klientem Microsoft przy użyciu najnowszych interfejsów API
description: Dowiedz się, jak programowo tworzyć subskrypcje platformy Azure dla Umowy z Klientem Microsoft przy użyciu najnowszych wersji interfejsu API REST, interfejsu wiersza polecenia platformy Azure i usługi Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2b0eaef7940b84dbd1e1325b5ae3bfb7b65dcef6
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200567"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>Programowe tworzenie subskrypcji platformy Azure dla Umowy z Klientem Microsoft przy użyciu najnowszych interfejsów API

Ten artykuł ułatwia programowe tworzenie subskrypcji platformy Azure dla Umowy z Klientem Microsoft przy użyciu najnowszych wersji interfejsu API. Jeśli nadal używasz starszej wersji zapoznawczej, zobacz [Programowe tworzenie subskrypcji platformy Azure za pomocą interfejsów API w wersji zapoznawczej](programmatically-create-subscription-preview.md). 

W tym artykule dowiesz się, jak programowo tworzyć subskrypcje za pomocą usługi Azure Resource Manager.

Subskrypcja platformy Azure utworzona programowo podlega warunkom umowy, w ramach której nabyto usługi platformy Azure od firmy Microsoft lub autoryzowanego odsprzedawcy. Aby dowiedzieć się więcej, zobacz [informacje prawne dotyczące platformy Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć subskrypcje, musisz mieć rolę właściciela, współautora lub twórcy subskrypcji platformy Azure w sekcji faktury albo rolę właściciela lub współautora w profilu rozliczeniowym lub na koncie rozliczeniowym. Aby uzyskać więcej informacji, zobacz [Role rozliczeniowe i zadania w subskrypcji](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Jeśli nie wiesz, czy masz dostęp do konta Umowy z Klientem Microsoft, zobacz [Sprawdzanie dostępu do Umowy z Klientem Microsoft](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

W poniższych przykładach użyto interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

## <a name="find-billing-accounts-that-you-have-access-to"></a>Odnajdowanie kont rozliczeniowych, do których masz dostęp

Wykonaj następujące żądanie, aby wyświetlić listę wszystkich kont rozliczeniowych.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
Odpowiedź interfejsu API będzie zawierała listę wszystkich kont rozliczeniowych, do których masz dostęp.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Użyj właściwości `displayName`, aby określić konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że parametr agreeementType tego konta to *MicrosoftCustomerAgreement*. Skopiuj wartość `name` tego konta.  Przykładowo aby utworzyć subskrypcję dla konta rozliczeniowego `Contoso`, skopiuj wartość `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Odnajdowanie profilów rozliczeniowych i sekcji faktury w celu utworzenia subskrypcji

Opłaty za subskrypcję są wyświetlane w sekcji faktury profilu rozliczeniowego. Użyj poniższego interfejsu API, aby uzyskać listę profilów rozliczeniowych i sekcji faktury, w ramach których masz uprawnienie do tworzenia subskrypcji platformy Azure.

Najpierw uzyskasz listę profilów rozliczeniowych w ramach konta rozliczeniowego, do którego masz dostęp.

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)
```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
Odpowiedź interfejsu API zawiera listę wszystkich profilów rozliczeniowych, w ramach których masz dostęp do tworzenia subskrypcji:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Skopiuj wartość elementu `id`, aby dalej identyfikować sekcje faktury poniżej profilu rozliczeniowego. Na przykład skopiuj wartość `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` i wywołaj następujący interfejs API.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>Reakcja

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Użyj właściwości `id`, aby określić sekcję faktury, dla której chcesz utworzyć subskrypcje. Skopiuj cały ciąg. Na przykład `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-for-an-invoice-section"></a>Tworzenie subskrypcji dla sekcji faktury

W poniższym przykładzie zostanie utworzona subskrypcja o nazwie *Dev Team subscription* dla sekcji faktury *Development*. Ta subskrypcja jest rozliczana w profilu rozliczeniowym *Contoso Billing Profile* i pojawia się w sekcji *Development* na tej fakturze. Użyjesz skopiowanego zakresu rozliczeniowego z poprzedniego kroku: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="rest"></a>[REST](#tab/rest-MCA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Treść żądania

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
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

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell-MCA)

Aby zainstalować najnowszą wersję modułu zawierającego polecenie cmdlet `New-AzSubscriptionAlias`, uruchom polecenie `Install-Module Az.Subscription`. Aby zainstalować najnowszą wersję modułu PowerShellGet, zobacz [Uzyskiwanie modułu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Uruchom następujące polecenie [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription), używając zakresu rozliczeniowego `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
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

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli-MCA)

Najpierw zainstaluj to rozszerzenie, uruchamiając polecenia `az extension add --name account` i `az extension add --name alias`.

Uruchom polecenie [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create).

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
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

## <a name="next-steps"></a>Następne kroki

* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
