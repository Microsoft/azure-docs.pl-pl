---
title: Programowe tworzenie subskrypcji platformy Azure
description: Dowiedz się, jak programowo utworzyć dodatkowe subskrypcje platformy Azure.
author: amberbhargava
ms.topic: conceptual
ms.date: 03/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 33f1d154f438b917b79cd299e81c9078e2f2e81d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460402"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Programowe tworzenie subskrypcji platformy Azure (wersja zapoznawcza)

Klienci platformy Azure z kontem rozliczania [Umowa Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [Microsoft Customer Agreement (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) lub [Microsoft Partner Agreement (MPa)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) mogą programowo tworzyć subskrypcje. W tym artykule dowiesz się, jak tworzyć subskrypcje programowo przy użyciu Azure Resource Manager.

Gdy tworzysz subskrypcję platformy Azure programowo, ta subskrypcja podlega postanowieniom umowy, na podstawie której uzyskano usługi platformy Azure od firmy Microsoft lub autoryzowanego odsprzedawcy. Aby dowiedzieć się więcej, zobacz [Microsoft Azure informacje prawne](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Tworzenie subskrypcji dla konta rozliczania EA

Skorzystaj z informacji podanych w poniższych sekcjach, aby utworzyć subskrypcje EA.

### <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję, musisz mieć rolę właściciela na koncie rejestracji. Istnieją dwa sposoby uzyskania roli:

* Administrator przedsiębiorstwa rejestracji może należeć do [właściciela konta](https://ea.azure.com/helpdocs/addNewAccount) (wymagane jest zalogowanie), co umożliwia właścicielowi konta rejestracji.

* Istniejący właściciel konta rejestracji może [udzielić Ci dostępu](grant-access-to-create-subscription.md). Podobnie, jeśli chcesz użyć nazwy głównej usługi do utworzenia subskrypcji umowy EA, należy [przyznać tej jednostce usługi możliwość tworzenia subskrypcji](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Znajdź konta, do których masz dostęp

Po dodaniu do konta rejestracji skojarzonego z właścicielem konta platforma Azure korzysta z relacji konto-do-Rejestracja, aby określić, gdzie należy rozliczać opłaty za subskrypcję. Wszystkie subskrypcje utworzone w ramach konta są rozliczane w ramach rejestracji w ramach umowy EA, w której znajduje się konto. Aby utworzyć subskrypcje, należy przekazać wartości z konta rejestracji i podmiotów zabezpieczeń użytkowników do subskrypcji.

Aby uruchomić następujące polecenia, należy zalogować się do *katalogu macierzystego*właściciela konta, który jest katalogiem, w którym domyślnie są tworzone subskrypcje.

### <a name="rest"></a>[REST](#tab/rest)

Żądaj, aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Odpowiedź interfejsu API zawiera listę wszystkich kont rejestracji, do których masz dostęp:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Użyj `principalName` właściwości, aby zidentyfikować konto, na które mają być naliczane opłaty. Skopiuj to `name` konto. Jeśli na przykład chcesz utworzyć subskrypcje na koncie SignUpEngineering@contoso.com rejestracji, skopiujesz. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Ten identyfikator jest IDENTYFIKATORem obiektu konta rejestracji. Wklej tę wartość w miejscu, aby można było użyć jej w następnym kroku jako `enrollmentAccountObjectId`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Otwórz [Azure Cloud Shell](https://shell.azure.com/) i wybierz pozycję PowerShell.

Użyj polecenia cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) , aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Platforma Azure reaguje na listę kont rejestracji, do których masz dostęp:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Użyj `principalName` właściwości, aby zidentyfikować konto, na które mają być naliczane opłaty. Skopiuj to `ObjectId` konto. Jeśli na przykład chcesz utworzyć subskrypcje na koncie SignUpEngineering@contoso.com rejestracji, skopiujesz. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Wklej ten identyfikator obiektu w miejscu, aby można było go użyć w następnym kroku jako `enrollmentAccountObjectId`.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić listę wszystkich kont rejestracji, do których masz dostęp, użyj polecenia [AZ Rozlicz rejestrację konta](https://aka.ms/EASubCreationPublicPreviewCLI) .

```azurecli-interactive
az billing enrollment-account list
```

Platforma Azure reaguje na listę kont rejestracji, do których masz dostęp:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

Użyj `principalName` właściwości, aby zidentyfikować konto, na które mają być naliczane opłaty. Skopiuj to `name` konto. Jeśli na przykład chcesz utworzyć subskrypcje na koncie SignUpEngineering@contoso.com rejestracji, skopiujesz. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Ten identyfikator jest IDENTYFIKATORem obiektu konta rejestracji. Wklej tę wartość w miejscu, aby można było użyć jej w następnym kroku jako `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Tworzenie subskrypcji przy użyciu określonego konta rejestracji

Poniższy przykład tworzy subskrypcję o nazwie *Dev Team Subscription* na koncie rejestracji wybranym w poprzednim kroku. Oferta subskrypcji to *MS-AZR-0017P* (zwykła Umowa Enterprise firmy Microsoft). Opcjonalnie dodatkowo dodaje dwóch użytkowników jako właścicieli RBAC dla subskrypcji.

### <a name="rest"></a>[REST](#tab/rest)

Wykonaj następujące żądanie, zastępując wartość `<enrollmentAccountObjectId>` wartością `name` skopiowaną z pierwszego kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Jeśli chcesz określić właścicieli, Dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nie      | String | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, zostanie ona ustawiona na nazwę oferty, np. "Microsoft Azure Enterprise".                                 |
| `offerType`   | Tak      | String | Oferta subskrypcji. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Product Use) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muszą być [włączone przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nie       | String | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia.  |

W odpowiedzi otrzymujesz `subscriptionOperation` obiekt do monitorowania. Po zakończeniu tworzenia subskrypcji `subscriptionOperation` obiekt zwróci `subscriptionLink` obiekt, który ma identyfikator subskrypcji.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Najpierw zainstaluj ten moduł w wersji zapoznawczej, uruchamiając `Install-Module Az.Subscription -AllowPrerelease`polecenie. Aby upewnić `-AllowPrerelease` się, że działa, zainstaluj najnowszą wersję programu PowerShellGet z [modułu Get PowerShellGet](/powershell/scripting/gallery/installing-psget).

Uruchom polecenie [New-AzSubscription](/powershell/module/az.subscription) poniżej, zastępując `<enrollmentAccountObjectId>` je `ObjectId` zebranym w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Jeśli chcesz określić właścicieli, Dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nie      | String | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, zostanie ona ustawiona na nazwę oferty, np. "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Tak      | String | Oferta subskrypcji. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Product Use) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muszą być [włączone przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Tak       | String | Identyfikator obiektu konta rejestracji, w ramach którego utworzono subskrypcję. Ta wartość jest identyfikatorem GUID, z `Get-AzEnrollmentAccount`którego otrzymujesz. |
| `OwnerObjectId`      | Nie       | String | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia.  |
| `OwnerSignInName`    | Nie       | String | Adres e-mail dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru można użyć zamiast `OwnerObjectId`.|
| `OwnerApplicationId` | Nie       | String | Identyfikator aplikacji dowolnej jednostki usługi, która ma zostać dodana jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru można użyć zamiast `OwnerObjectId`. W przypadku korzystania z tego parametru jednostka usługi musi mieć [dostęp do odczytu do katalogu](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Aby wyświetlić pełną listę wszystkich parametrów, zobacz polecenie [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw zainstaluj to rozszerzenie w wersji zapoznawczej, uruchamiając `az extension add --name subscription`polecenie.

Uruchom polecenie [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) poniżej, zastępując `<enrollmentAccountObjectId>` je `name` skopiowanymi w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Jeśli chcesz określić właścicieli, Dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nie      | String | Nazwa wyświetlana subskrypcji. Jeśli nie zostanie określony, zostanie ona ustawiona na nazwę oferty, np. "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Tak      | String | Oferta subskrypcji. Dwie opcje dla EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (Product Use) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (Dev/Test, muszą być [włączone przy użyciu portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Tak       | String | Identyfikator obiektu konta rejestracji, w ramach którego utworzono subskrypcję. Ta wartość jest identyfikatorem GUID, z `az billing enrollment-account list`którego otrzymujesz. |
| `owner-object-id`      | Nie       | String | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia.  |
| `owner-upn`    | Nie       | String | Adres e-mail dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru można użyć zamiast `owner-object-id`.|
| `owner-spn` | Nie       | String | Identyfikator aplikacji dowolnej jednostki usługi, która ma zostać dodana jako właściciel RBAC w ramach subskrypcji podczas jej tworzenia. Tego parametru można użyć zamiast `owner-object-id`. W przypadku korzystania z tego parametru jednostka usługi musi mieć [dostęp do odczytu do katalogu](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).|

Aby wyświetlić pełną listę wszystkich parametrów, zobacz [AZ Account Create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ograniczenia interfejsu API tworzenia subskrypcji przedsiębiorstwa platformy Azure

- Za pomocą tego interfejsu API można tworzyć tylko subskrypcje platformy Azure Enterprise.
- Istnieje limit 500 subskrypcji na konto rejestracji. Następnie więcej subskrypcji dla konta można utworzyć tylko w Azure Portal. Jeśli chcesz utworzyć więcej subskrypcji za pomocą interfejsu API, Utwórz inne konto rejestracji.
- Użytkownicy, którzy nie są właścicielami kont, ale zostali dodani do konta rejestracji za pośrednictwem RBAC, nie mogą tworzyć subskrypcji w Azure Portal.
- Nie można wybrać dzierżawy, w której ma zostać utworzona subskrypcja. Subskrypcja jest zawsze tworzona w dzierżawie głównej właściciela konta. Aby przenieść subskrypcję do innej dzierżawy, zobacz [zmiana subskrypcji dzierżawy](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Tworzenie subskrypcji dla konta MCA

### <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcje, należy mieć rolę twórca, współautor lub administrator subskrypcji platformy Azure w sekcji faktury lub roli współautor lub współpracownika w profilu rozliczeń lub koncie rozliczeniowym. Aby uzyskać więcej informacji, zobacz [Role rozliczeniowe i zadania w subskrypcji](../../cost-management-billing/manage/understand-mca-roles.md#subscription-billing-roles-and-tasks).

W poniższym przykładzie przedstawiono użycie interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Znajdź konta rozliczeniowe, do których masz dostęp

Wprowadź poniższe żądanie, aby wyświetlić listę wszystkich kont rozliczeń.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę kont rozliczeń, do których masz dostęp.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Użyj `displayName` właściwości, aby zidentyfikować konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że agreeementType konta to *MicrosoftCustomerAgreement*. `name` Skopiuj konto.  Jeśli na przykład chcesz utworzyć subskrypcję dla konta `Contoso` rozliczeniowego, skopiujesz. `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` Przeklej gdzieś tę wartość, aby można z niej było skorzystać w następnym kroku.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Znajdź sekcje faktur, aby utworzyć subskrypcje

Opłaty za subskrypcję są wyświetlane w sekcji faktury profilu rozliczania. Użyj poniższego interfejsu API, aby uzyskać listę sekcji faktur i profilów rozliczeń, na których masz uprawnienia do tworzenia subskrypcji platformy Azure.

Wykonaj następujące żądanie, zastępując wartość `<billingAccountName>` wartością `name` skopiowaną z pierwszego kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę wszystkich sekcji faktur i ich profilów rozliczeń, w których masz dostęp do tworzenia subskrypcji:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Użyj `invoiceSectionDisplayName` właściwości, aby zidentyfikować sekcję faktury, dla której chcesz utworzyć subskrypcje. `invoiceSectionId`Skopiuj `billingProfileId` i jedną z sekcji `skuId` dla faktury. Jeśli na przykład chcesz utworzyć `Microsoft Azure plan` subskrypcję typu dla `Development` faktury, skopiuj `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` i. `0001` Wklej te wartości w tym miejscu, aby można było ich używać w następnym kroku.

### <a name="create-a-subscription-for-an-invoice-section"></a>Tworzenie subskrypcji dla sekcji faktury

Poniższy przykład tworzy subskrypcję o nazwie *Dev Team Subscription subskrypcji* typu *Microsoft Azure plan* dla sekcji faktury *deweloperskiej* . Opłata zostanie naliczona za profil rozliczania *finansów firmy Contoso* i pojawi się w sekcji *opracowywanie* faktury.

Wykonaj następujące żądanie, zastępując `<invoiceSectionId>` je `invoiceSectionId` skopiowanymi z drugiego kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Należy przekazać `billingProfileId` i `skuId` skopiować z drugiego kroku parametry żądania interfejsu API. Jeśli chcesz określić właścicieli, Dowiedz się, [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Tak      | String | Nazwa wyświetlana subskrypcji.|
| `billingProfileId`   | Tak      | String | Identyfikator profilu rozliczeń, który będzie obciążany opłatami za subskrypcję.  |
| `skuId` | Tak      | String | Identyfikator jednostki SKU, który określa typ planu platformy Azure. |
| `owners`      | Nie       | String | Identyfikator obiektu dowolnego użytkownika lub jednostki usługi, który chcesz dodać jako właściciela RBAC w ramach subskrypcji, gdy zostanie ona utworzona.  |
| `costCenter` | Nie      | String | Centrum kosztów skojarzone z subskrypcją. Zostanie ona wyświetlona w pliku CSV użycia. |
| `managementGroupId` | Nie      | String | Identyfikator grupy zarządzania, do której zostanie dodana subskrypcja. Aby uzyskać listę grup zarządzania, zobacz [grupy zarządzania-list API](/rest/api/resources/managementgroups/list). Użyj identyfikatora grupy zarządzania z interfejsu API. |

W odpowiedzi otrzymujesz `subscriptionCreationResult` obiekt do monitorowania. Po zakończeniu tworzenia subskrypcji `subscriptionCreationResult` obiekt zwróci `subscriptionLink` obiekt, który ma identyfikator subskrypcji.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Tworzenie subskrypcji dla konta rozliczania MPA

### <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję konta rozliczeniowego, musisz mieć rolę administratora globalnego lub agenta administracyjnego w ramach konta dostawcy rozwiązań w chmurze w organizacji. Aby uzyskać więcej informacji, zobacz [Centrum partnerskie — Przypisywanie ról i uprawnień użytkowników](https://docs.microsoft.com/partner-center/permissions-overview).

W poniższym przykładzie przedstawiono użycie interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Znajdź konta rozliczeń, do których masz dostęp

Wprowadź poniższe żądanie, aby wyświetlić listę wszystkich kont rozliczeniowych, do których masz dostęp.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API wyświetla listę kont rozliczeń.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Użyj `displayName` właściwości, aby zidentyfikować konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że agreeementType konta to *MicrosoftPartnerAgreement*. Skopiuj `name` dla konta. Jeśli na przykład chcesz utworzyć subskrypcję dla konta `Contoso` rozliczeniowego, skopiujesz. `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` Przeklej gdzieś tę wartość, aby można z niej było skorzystać w następnym kroku.

### <a name="find-customers-that-have-azure-plans"></a>Znajdź klientów, którzy mają plany platformy Azure

Wykonaj następujące żądanie, zastępując `<billingAccountName>` je `name` skopiowanym z pierwszego kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```), aby wyświetlić listę wszystkich klientów na koncie rozliczeniowym, dla którego można tworzyć subskrypcje platformy Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę klientów na koncie rozliczeniowym z planami platformy Azure. Możesz tworzyć subskrypcje dla tych klientów.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Użyj `displayName` właściwości, aby zidentyfikować klienta, dla którego chcesz utworzyć subskrypcje. Skopiuj `id` dla klienta. Na przykład jeśli chcesz utworzyć subskrypcję dla `Fabrikam toys`, skopiujesz. `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Wklej tę wartość w miejscu, aby użyć jej w kolejnych krokach.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcjonalne dla dostawców pośrednich: Uzyskaj odsprzedawcy dla klienta

Jeśli jesteś pośrednim dostawcą w modelu dwuwarstwowym dostawcy CSP, możesz określić Odsprzedawcę podczas tworzenia subskrypcji dla klientów.

Wykonaj następujące żądanie, zastępując `<customerId>` je `id` skopiowanymi z drugiego kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```), aby wyświetlić listę wszystkich odsprzedawcaów dostępnych dla klienta.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
Odpowiedź interfejsu API zawiera listę odsprzedawcaów dla klienta:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Użyj właściwości `description` , aby zidentyfikować odsprzedawcy, który zostanie skojarzony z subskrypcją. Skopiuj `resellerId` do odsprzedawcy. Na przykład jeśli chcesz skojarzyć `Wingtip`, skopiujesz. `3xxxxx` Przeklej gdzieś tę wartość, aby można z niej było skorzystać w następnym kroku.

### <a name="create-a-subscription-for-a-customer"></a>Tworzenie subskrypcji dla klienta

Poniższy przykład tworzy subskrypcję o nazwie *Dev Team Subscription* for *Fabrikam zabawki* i kojarzy odsprzedawcy *Wingtip* z subskrypcją. T

Wykonaj następujące żądanie, zastępując `<customerId>` je `id` skopiowanymi z drugiego kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Przekaż opcjonalną *resellerId* skopiowaną z drugiego kroku w parametrach żądania interfejsu API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nazwa elementu  | Wymagany | Typ   | Opis                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Tak      | String | Nazwa wyświetlana subskrypcji.|
| `skuId` | Tak      | String | Identyfikator jednostki SKU planu platformy Azure. Użyj *0001* dla subskrypcji typu Microsoft Azure plan |
| `resellerId`      | Nie       | String | IDENTYFIKATOR MPN odsprzedawcy, który zostanie skojarzony z subskrypcją.  |

W odpowiedzi otrzymujesz `subscriptionCreationResult` obiekt do monitorowania. Po zakończeniu tworzenia subskrypcji `subscriptionCreationResult` obiekt zwróci `subscriptionLink` obiekt, który ma identyfikator subskrypcji.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładem tworzenia subskrypcji Umowa Enterprise (EA) przy użyciu platformy .NET, zobacz [przykładowy kod w witrynie GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i podmiotom usługi. Aby uzyskać więcej informacji, zobacz [udzielanie dostępu do tworzenia subskrypcji platformy Azure Enterprise (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji przy użyciu grup zarządzania, zobacz [organizowanie zasobów za pomocą grup zarządzania platformy Azure](../../governance/management-groups/overview.md) .
