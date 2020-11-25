---
title: Programowe tworzenie subskrypcji umowy Azure Enterprise Agreement przy użyciu najnowszych interfejsów API
description: Dowiedz się, jak programowo tworzyć subskrypcje umowy Azure Enterprise Agreement przy użyciu najnowszych wersji interfejsu API REST, interfejsu wiersza polecenia platformy Azure i usługi Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 34fe909c7fca3c91845c58b41abb0d8885e156e6
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94850944"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Programowe tworzenie subskrypcji umowy Azure Enterprise Agreement przy użyciu najnowszych interfejsów API

Ten artykuł pomaga w programowym tworzeniu subskrypcji umowy Azure Enterprise Agreement dla konta rozliczeniowego umowy EA przy użyciu najnowszych wersji interfejsu API. Jeśli nadal używasz starszej wersji zapoznawczej, zobacz [Programowe tworzenie subskrypcji platformy Azure za pomocą interfejsów API w wersji zapoznawczej](programmatically-create-subscription-preview.md). 

W tym artykule dowiesz się, jak programowo tworzyć subskrypcje za pomocą usługi Azure Resource Manager.

Subskrypcja platformy Azure utworzona programowo podlega warunkom umowy, w ramach której nabyto usługi platformy Azure od firmy Microsoft lub autoryzowanego odsprzedawcy. Aby dowiedzieć się więcej, zobacz [informacje prawne dotyczące platformy Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję, musisz mieć rolę właściciela na koncie rejestracji. Istnieją dwa sposoby, aby uzyskać tę rolę:

* Administrator przedsiębiorstwa Twojej rejestracji może [przyznać Ci rolę właściciela konta](https://ea.azure.com/helpdocs/addNewAccount) (wymagane jest zalogowanie się), dzięki czemu zostaniesz właścicielem konta rejestracji.
* Aktualny właściciel konta rejestracji może [przyznać Ci dostęp](grant-access-to-create-subscription.md). Analogicznie, aby utworzyć subskrypcję umowy EA za pomocą jednostki usługi, musisz [przyznać jednostce usługi możliwość tworzenia subskrypcji](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Odnajdowanie kont, do których masz dostęp

Po dodaniu Cię do konta rejestracji skojarzonego z właścicielem konta platforma Azure na podstawie relacji konto-rejestracja określa, komu wystawić rachunek za opłaty związane z subskrypcją. Wszystkie subskrypcje utworzone w ramach tego konta są rozliczane w ramach rejestracji umowy EA, do której przypisane jest konto. Aby utworzyć subskrypcje, musisz przekazać wartości dotyczące konta rejestracji i jednostki usługi w celu zostania właścicielem subskrypcji.

Aby uruchomić poniższe polecenia, zaloguj się w *katalogu macierzystym* właściciela konta, czyli katalogu, w którym domyślnie tworzone są subskrypcje.

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

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

Zanotuj wartość `id` z jednej z właściwości `enrollmentAccounts`. Jest to zakres rozliczeniowy, w ramach którego jest inicjowane żądanie utworzenia subskrypcji. 

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


<!--
### [Azure CLI](#tab/azure-cli-getEnrollments)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Tworzenie subskrypcji w ramach konkretnego konta rejestracji

W poniższym przykładzie na koncie rejestracji wybranym w poprzednim kroku zostanie utworzona subskrypcja o nazwie *Dev Team Subscription*. 

### <a name="rest"></a>[REST](#tab/rest-EA)

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

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell-EA)

Aby zainstalować najnowszą wersję modułu zawierającego polecenie cmdlet `New-AzSubscriptionAlias`, uruchom polecenie `Install-Module Az.Subscription`. Aby zainstalować najnowszą wersję modułu PowerShellGet, zobacz [Uzyskiwanie modułu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Uruchom następujące polecenie [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription), używając zakresu rozliczeniowego `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
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

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli-EA)

Najpierw zainstaluj to rozszerzenie, uruchamiając polecenia `az extension add --name account` i `az extension add --name alias`.

Uruchom następujące polecenie [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) i dołącz wartości elementów `billing-scope` oraz `id` z jednego z elementów `enrollmentAccounts`. 

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

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ograniczenia interfejsu API tworzenia subskrypcji Enterprise platformy Azure

- Przy użyciu tego interfejsu API można tworzyć tylko subskrypcje Enterprise platformy Azure.
- Obowiązuje limit 2000 subskrypcji na jedno konto rejestracji. Po przekroczeniu tego limitu kolejne subskrypcje dla tego konta można utworzyć wyłącznie w witrynie Azure Portal. Aby utworzyć więcej subskrypcji za pomocą interfejsu API, musisz utworzyć kolejne konto rejestracji. Liczba anulowanych, usuniętych i przesłanych subskrypcji zbliża się do granicy 2000.
- Użytkownicy, którzy nie są właścicielami konta, ale zostali dodani do konta rejestracji przy użyciu systemu RBAC platformy Azure, nie mogą tworzyć subskrypcji w witrynie Azure Portal.
- Nie można wybrać dzierżawy, w której ma zostać utworzona subskrypcja. Subskrypcja jest zawsze tworzona w głównej dzierżawie właściciela konta. Jeśli chcesz przenieść subskrypcję do innej dzierżawy, zobacz [Zmiana dzierżawy subskrypcji](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Następne kroki

* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
