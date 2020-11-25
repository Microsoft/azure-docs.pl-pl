---
title: Programowe tworzenie subskrypcji platformy Azure za pomocą interfejsów API w wersji zapoznawczej
description: Dowiedz się, jak programowo tworzyć dodatkowe subskrypcje platformy Azure przy użyciu wersji zapoznawczych interfejsu API REST, interfejsu wiersza polecenia platformy Azure i usługi Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 68d890386d53b4115c773b128f8678bac9579e53
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844339"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Programowe tworzenie subskrypcji platformy Azure za pomocą interfejsów API w wersji zapoznawczej

Ten artykuł ułatwia programowe tworzenie subskrypcji platformy Azure przy użyciu starszego interfejsu API w wersji zapoznawczej. W tym artykule dowiesz się, jak programowo tworzyć subskrypcje za pomocą usługi Azure Resource Manager.

Mamy nowe artykuły dotyczące najnowszej wersji interfejsu API dotyczące różnego typu subskrypcji umów platformy Azure:

- [Programowe tworzenie subskrypcji umowy EA za pomocą najnowszego interfejsu API](programmatically-create-subscription-enterprise-agreement.md)
- [Programowe tworzenie subskrypcji Umowy z Klientem Microsoft za pomocą najnowszego interfejsu API](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Programowe tworzenie subskrypcji umowy MPA za pomocą najnowszego interfejsu API](Programmatically-create-subscription-microsoft-customer-agreement.md)

Nadal możesz jednak korzystać z informacji znajdujących się w tym artykule, jeśli nie chcesz korzystać z najnowszej wersji interfejsu API.

Programowe tworzenie subskrypcji jest dostępne dla klientów platformy Azure z kontem rozliczeniowym dla następujących typów umów:

- Enterprise Agreement
- Umowa klienta firmy Microsoft (MCA)
- Umowa partnerska firmy Microsoft

Subskrypcja platformy Azure utworzona programowo podlega warunkom umowy, w ramach której nabyto usługi platformy Azure od firmy Microsoft lub autoryzowanego odsprzedawcy. Aby dowiedzieć się więcej, zobacz [informacje prawne dotyczące platformy Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Tworzenie subskrypcji dla konta rozliczeniowego umowy EA

W poniższych sekcjach przedstawiono informacje pomocne w tworzeniu subskrypcji umowy EA.

### <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję, musisz mieć rolę właściciela na koncie rejestracji. Istnieją dwa sposoby, aby uzyskać tę rolę:

* Administrator przedsiębiorstwa Twojej rejestracji może [przyznać Ci rolę właściciela konta](https://ea.azure.com/helpdocs/addNewAccount) (wymagane jest zalogowanie się), dzięki czemu zostaniesz właścicielem konta rejestracji.
* Aktualny właściciel konta rejestracji może [przyznać Ci dostęp](grant-access-to-create-subscription.md). Analogicznie, aby utworzyć subskrypcję umowy EA za pomocą jednostki usługi, musisz [przyznać jednostce usługi możliwość tworzenia subskrypcji](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Odnajdowanie kont, do których masz dostęp

Po dodaniu Cię do konta rejestracji skojarzonego z właścicielem konta platforma Azure na podstawie relacji konto-rejestracja określa, komu wystawić rachunek za opłaty związane z subskrypcją. Wszystkie subskrypcje utworzone w ramach tego konta są rozliczane w ramach rejestracji umowy EA, do której przypisane jest konto. Aby utworzyć subskrypcje, musisz przekazać wartości dotyczące konta rejestracji i jednostki usługi w celu zostania właścicielem subskrypcji.

Aby uruchomić poniższe polecenia, zaloguj się w *katalogu macierzystym* właściciela konta, czyli katalogu, w którym domyślnie tworzone są subskrypcje.

### <a name="rest"></a>[REST](#tab/rest)

Wykonaj żądanie wyświetlenia listy wszystkich kont rejestracji, do których masz dostęp:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Odpowiedź interfejsu API będzie zawierała listę wszystkich kont rejestracji, do których masz dostęp:

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

Użyj właściwości `principalName`, aby określić konto, w ramach którego będą rozliczane subskrypcje. Skopiuj właściwość `name` tego konta. Przykładowo aby utworzyć subskrypcje w ramach konta rejestracji SignUpEngineering@contoso.com, skopiuj wartość ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku jako właściwości `enrollmentAccountObjectId`.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Otwórz usługę [Azure Cloud Shell](https://shell.azure.com/) i wybierz program PowerShell.

Użyj polecenia cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), aby wyświetlić wszystkie konta rejestracji, do których masz dostęp.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Platforma Azure w odpowiedzi zwróci listę kont rejestracji, do których masz dostęp:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Użyj właściwości `principalName`, aby określić konto, w ramach którego będą rozliczane subskrypcje. Skopiuj właściwość `ObjectId` tego konta. Przykładowo aby utworzyć subskrypcje w ramach konta rejestracji SignUpEngineering@contoso.com, skopiuj wartość ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Wklej ten identyfikator obiektu w dogodnym miejscu, aby można było go użyć w następnym kroku jako właściwości `enrollmentAccountObjectId`.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [az billing enrollment-account list](/cli/azure/billing), aby wyświetlić wszystkie konta rejestracji, do których masz dostęp.

```azurecli-interactive
az billing enrollment-account list
```

Platforma Azure w odpowiedzi zwróci listę kont rejestracji, do których masz dostęp:

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

Użyj właściwości `principalName`, aby określić konto, w ramach którego będą rozliczane subskrypcje. Skopiuj właściwość `name` tego konta. Przykładowo aby utworzyć subskrypcje w ramach konta rejestracji SignUpEngineering@contoso.com, skopiuj wartość ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Jest to identyfikator obiektu konta rejestracji. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku jako właściwości `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Tworzenie subskrypcji w ramach konkretnego konta rejestracji

W poniższym przykładzie na koncie rejestracji wybranym w poprzednim kroku zostanie utworzona subskrypcja o nazwie *Dev Team Subscription*. Oferta subskrypcji to *MS-AZR-0017P* (zwykła umowa Microsoft Enterprise Agreement). Umożliwia ona opcjonalne dodanie dwóch użytkowników jako właścicieli RBAC platformy Azure w subskrypcji.

### <a name="rest"></a>[REST](#tab/rest)

Wykonaj następujące żądanie, zastępując wartość `<enrollmentAccountObjectId>` wartością `name` skopiowaną z pierwszego kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Aby określić właścicieli, zobacz [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

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
| `displayName` | Nie      | Ciąg | Wyświetlana nazwa subskrypcji. Jeśli jej nie określono, będzie nią nazwa oferty, na przykład „Microsoft Azure Enterprise”.                                 |
| `offerType`   | Tak      | Ciąg | Oferta subskrypcji. Dwie opcje dla umów EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (w środowisku produkcyjnym) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (w środowisku deweloperskim/testowym; wymaga [włączenia w portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nie       | Ciąg | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC platformy Azure subskrypcji po jej utworzeniu.  |

W nagłówku `Location` odpowiedzi otrzymasz adres url, którego możesz użyć, aby wykonać zapytanie dotyczące stanu operacji tworzenia subskrypcji. Po zakończeniu tworzenia subskrypcji żądanie GET dla adresu url `Location` zwróci obiekt `subscriptionLink` z identyfikatorem subskrypcji. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API subskrypcji](/rest/api/subscription/).

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby zainstalować najnowszą wersję modułu zawierającego polecenie cmdlet `New-AzSubscription`, uruchom polecenie `Install-Module Az.Subscription`. Aby zainstalować najnowszą wersję modułu PowerShellGet, zobacz [Uzyskiwanie modułu PowerShellGet](/powershell/scripting/gallery/installing-psget).

Uruchom następujące polecenie [New-AzSubscription](/powershell/module/az.subscription), zastępując wartość `<enrollmentAccountObjectId>` wartością `ObjectId` uzyskaną w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Aby określić właścicieli, zobacz [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nazwa elementu  | Wymagany | Typ   | Opis |
|---------------|----------|--------|----|
| `Name` | Nie      | Ciąg | Wyświetlana nazwa subskrypcji. Jeśli jej nie określono, będzie nią nazwa oferty, na przykład *Microsoft Azure Enterprise*. |
| `OfferType`   | Tak      | Ciąg | Oferta subskrypcji. Dwie opcje dla umów EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (w środowisku produkcyjnym) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (w środowisku deweloperskim/testowym; wymaga [włączenia w portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Tak       | Ciąg | Identyfikator obiektu konta rejestracji, w ramach którego tworzona i rozliczana jest subskrypcja. Ta wartość to identyfikator GUID otrzymany w odpowiedzi na polecenie `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Nie       | Ciąg | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC platformy Azure subskrypcji po jej utworzeniu.  |
| `OwnerSignInName`    | Nie       | Ciąg | Adres e-mail dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC platformy Azure subskrypcji po jej utworzeniu. Można użyć tego parametru zamiast parametru `OwnerObjectId`.|
| `OwnerApplicationId` | Nie       | Ciąg | Identyfikator aplikacji dowolnej jednostki usługi, która ma zostać dodana jako właściciel RBAC platformy Azure subskrypcji po jej utworzeniu. Można użyć tego parametru zamiast parametru `OwnerObjectId`. Jeśli chcesz użyć tego parametru, jednostka usługi musi mieć [dostęp do odczytu tego katalogu](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Aby wyświetlić pełną listę parametrów, zobacz [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription).

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Najpierw zainstaluj to rozszerzenie w wersji zapoznawczej, uruchamiając polecenie `az extension add --name subscription`.

Uruchom następujące polecenie [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true), zastępując wartość `<enrollmentAccountObjectId>` wartością `name` skopiowaną w pierwszym kroku (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Aby określić właścicieli, zobacz [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nazwa elementu  | Wymagany | Typ   | Opis |
|---------------|----------|--------|------------|
| `display-name` | Nie      | Ciąg | Wyświetlana nazwa subskrypcji. Jeśli jej nie określono, będzie nią nazwa oferty, na przykład *Microsoft Azure Enterprise*.|
| `offer-type`   | Tak      | Ciąg | Oferta subskrypcji. Dwie opcje dla umów EA to [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (w środowisku produkcyjnym) i [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (w środowisku deweloperskim/testowym; wymaga [włączenia w portalu EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Tak       | Ciąg | Identyfikator obiektu konta rejestracji, w ramach którego tworzona i rozliczana jest subskrypcja. Ta wartość to identyfikator GUID otrzymany w odpowiedzi na polecenie `az billing enrollment-account list`. |
| `owner-object-id`      | Nie       | Ciąg | Identyfikator obiektu dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC platformy Azure subskrypcji po jej utworzeniu.  |
| `owner-upn`    | Nie       | Ciąg | Adres e-mail dowolnego użytkownika, który ma zostać dodany jako właściciel RBAC platformy Azure subskrypcji po jej utworzeniu. Można użyć tego parametru zamiast parametru `owner-object-id`.|
| `owner-spn` | Nie       | Ciąg | Identyfikator aplikacji dowolnej jednostki usługi, która ma zostać dodana jako właściciel RBAC platformy Azure subskrypcji po jej utworzeniu. Można użyć tego parametru zamiast parametru `owner-object-id`. Jeśli chcesz użyć tego parametru, jednostka usługi musi mieć [dostęp do odczytu tego katalogu](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Aby wyświetlić pełną listę parametrów, zobacz [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Ograniczenia interfejsu API tworzenia subskrypcji Enterprise platformy Azure

- Przy użyciu tego interfejsu API można tworzyć tylko subskrypcje Enterprise platformy Azure.
- Obowiązuje limit 2000 subskrypcji na jedno konto rejestracji. Po przekroczeniu tego limitu kolejne subskrypcje dla tego konta można utworzyć wyłącznie w witrynie Azure Portal. Jeśli chcesz utworzyć więcej subskrypcji za pomocą interfejsu API, musisz utworzyć kolejne konto rejestracji. Liczba anulowanych, usuniętych i przesłanych subskrypcji zbliża się do granicy 2000.
- Użytkownicy, którzy nie są właścicielami konta, ale zostali dodani do konta rejestracji przy użyciu systemu RBAC platformy Azure, nie mogą tworzyć subskrypcji w witrynie Azure Portal.
- Nie można wybrać dzierżawy, w której ma zostać utworzona subskrypcja. Subskrypcja jest zawsze tworzona w głównej dzierżawie właściciela konta. Jeśli chcesz przenieść subskrypcję do innej dzierżawy, zobacz [Zmiana dzierżawy subskrypcji](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Tworzenie subskrypcji dla konta MCA

W poniższych sekcjach przedstawiono informacje pomocne w tworzeniu subskrypcji dla konta MCA.

### <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć subskrypcje, musisz mieć rolę właściciela, współautora lub twórcy subskrypcji platformy Azure w sekcji faktury albo rolę właściciela lub współautora w profilu rozliczeniowym lub na koncie rozliczeniowym. Aby uzyskać więcej informacji, zobacz [Role rozliczeniowe i zadania w subskrypcji](understand-mca-roles.md#subscription-billing-roles-and-tasks).

W poniższych przykładach użyto interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Odnajdowanie kont rozliczeniowych, do których masz dostęp

Wykonaj następujące żądanie, aby wyświetlić listę wszystkich kont rozliczeniowych.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API będzie zawierała listę wszystkich kont rozliczeniowych, do których masz dostęp.

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
Użyj właściwości `displayName`, aby określić konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że parametr agreeementType tego konta to *MicrosoftCustomerAgreement*. Skopiuj wartość `name` tego konta.  Przykładowo aby utworzyć subskrypcję dla konta rozliczeniowego `Contoso`, skopiuj wartość `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Odnajdowanie sekcji faktury w celu utworzenia subskrypcji

Opłaty za subskrypcję są wyświetlane w sekcji faktury profilu rozliczeniowego. Użyj poniższego interfejsu API, aby uzyskać listę sekcji faktury i profilów rozliczeniowych, w ramach których masz uprawnienie do tworzenia subskrypcji platformy Azure.

Wykonaj następujące żądanie, zastępując wartość `<billingAccountName>` wartością `name` skopiowaną z pierwszego kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

Odpowiedź interfejsu API będzie zawierała listę wszystkich sekcji faktur i ich profilów rozliczeniowych, w ramach których masz dostęp do tworzenia subskrypcji:

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

Użyj właściwości `invoiceSectionDisplayName`, aby określić sekcję faktury, dla której chcesz utworzyć subskrypcje. Skopiuj wartość `invoiceSectionId`, `billingProfileId` i jedną z wartości `skuId` sekcji faktury. Przykładowo aby utworzyć subskrypcję typu `Microsoft Azure plan` dla sekcji faktury `Development`, skopiuj wartości `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` i `0001`. Wklej te wartości w dogodnym miejscu, aby można było użyć ich w następnym kroku.

### <a name="create-a-subscription-for-an-invoice-section"></a>Tworzenie subskrypcji dla sekcji faktury

W poniższym przykładzie zostanie utworzona subskrypcja o nazwie *Dev Team Subscription* typu *Microsoft Azure Plan* dla sekcji faktury *Development*. Ta subskrypcja jest rozliczana w profilu rozliczeniowym *Contoso finance* i pojawi się w sekcji *Development* na tej fakturze.

Wykonaj następujące żądanie, zastępując wartość `<invoiceSectionId>` wartością `invoiceSectionId` skopiowaną w drugim kroku (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Przekaż wartości `billingProfileId` i `skuId` skopiowane w drugim kroku w parametrach żądania interfejsu API. Aby określić właścicieli, zobacz [jak uzyskać identyfikatory obiektów użytkowników](grant-access-to-create-subscription.md#userObjectId).

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
| `displayName` | Tak      | Ciąg | Wyświetlana nazwa subskrypcji.|
| `billingProfileId`   | Tak      | Ciąg | Wybierz identyfikator profilu rozliczeniowego, dla którego są naliczane opłaty za subskrypcję.  |
| `skuId` | Tak      | Ciąg | Identyfikator jednostki SKU, który określa typ planu platformy Azure. |
| `owners`      | Nie       | Ciąg | Identyfikator obiektu dowolnego użytkownika lub jednostki usługi, który ma zostać dodany jako właściciel RBAC platformy Azure subskrypcji po jej utworzeniu.  |
| `costCenter` | Nie      | Ciąg | Centrum kosztów skojarzone z subskrypcją. Zostanie ono wyświetlone w pliku CSV dotyczącym użycia. |
| `managementGroupId` | Nie      | Ciąg | Identyfikator grupy zarządzania, do której zostanie dodana subskrypcja. Jeśli chcesz uzyskać listę grup zarządzania, zobacz [Grupy zarządzania — interfejs API listy](/rest/api/resources/managementgroups/list). Użyj identyfikatora grupy zarządzania uzyskanego za pomocą interfejsu API. |

W odpowiedzi uzyskasz obiekt `subscriptionCreationResult` na potrzeby monitorowania. Po zakończeniu tworzenia subskrypcji obiekt `subscriptionCreationResult` zwraca obiekt `subscriptionLink` z identyfikatorem subskrypcji.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Tworzenie subskrypcji dla konta rozliczeniowego umowy MPA

W poniższych sekcjach przedstawiono informacje pomocne w tworzeniu subskrypcji dla konta rozliczeniowego MPA.

### <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć subskrypcję dla konta rozliczeniowego, musisz mieć rolę globalnego administratora lub agenta administratora na koncie dostawcy rozwiązań w chmurze w swojej organizacji. Aby dowiedzieć się więcej, zobacz [Centrum partnerskie — przypisywanie ról i uprawnień użytkowników](/partner-center/permissions-overview).

W poniższych przykładach użyto interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Odnajdowanie kont rozliczeniowych, do których masz dostęp

Wykonaj następujące żądanie, aby wyświetlić listę wszystkich kont rozliczeniowych, do których masz dostęp.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

Odpowiedź interfejsu API zawiera listę wszystkich kont rozliczeniowych.

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
Użyj właściwości `displayName`, aby określić konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że parametr agreementType tego konta to *MicrosoftPartnerAgreement*. Skopiuj wartość `name` tego konta. Przykładowo aby utworzyć subskrypcję dla konta rozliczeniowego `Contoso`, skopiuj wartość `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku.

### <a name="find-customers-that-have-azure-plans"></a>Odnajdowanie klientów z planami platformy Azure

Wykonaj następujące żądanie, zastępując wartość `<billingAccountName>` wartością `name`skopiowaną w pierwszym kroku (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```), aby uzyskać listę wszystkich klientów na koncie rozliczeniowym, dla których możesz utworzyć subskrypcje platformy Azure.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Odpowiedź interfejsu API będzie zawierała listę klientów z planami platformy Azure na koncie rozliczeniowym. Dla tych klientów można utworzyć subskrypcje.

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

Użyj właściwości `displayName`, aby określić klienta, dla którego chcesz utworzyć subskrypcje. Skopiuj wartość `id` klienta. Przykładowo aby utworzyć subskrypcję dla konta `Fabrikam toys`, skopiuj wartość `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Wklej tę wartość w dogodnym miejscu, aby można było z niej skorzystać w kolejnych krokach.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Opcjonalne dla dostawców pośrednich: Uzyskiwanie odsprzedawców dla klienta

Jeśli jesteś dostawcą pośrednim w modelu dwuwarstwowym dostawców usług w chmurze, możesz określić odsprzedawcę podczas tworzenia subskrypcji dla klientów.

Wykonaj następujące żądanie, zastępując wartość `<customerId>` wartością `id` skopiowaną w drugim kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```), aby uzyskać listę wszystkich odsprzedawców dostępnych dla klienta.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
Odpowiedź interfejsu API będzie zawierała listę odsprzedawców dla klienta:

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

Użyj właściwości `description`, aby określić odsprzedawcę, który zostanie skojarzony z subskrypcją. Skopiuj wartość `resellerId` odsprzedawcy. Przykładowo aby skojarzyć odsprzedawcę `Wingtip`, skopiuj wartość `3xxxxx`. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku.

### <a name="create-a-subscription-for-a-customer"></a>Tworzenie subskrypcji dla klienta

W poniższym przykładzie zostanie utworzona subskrypcja o nazwie *Dev Team subscription* dla klienta *Fabrikam toys* ze skojarzonym odsprzedawcą *Wingtip*. 

Wykonaj następujące żądanie, zastępując wartość `<customerId>` wartością `id` skopiowaną w drugim kroku (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Przekaż opcjonalną wartość *resellerId* skopiowaną w drugim kroku w parametrach żądania interfejsu API.

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
| `displayName` | Tak      | Ciąg | Wyświetlana nazwa subskrypcji.|
| `skuId` | Tak      | Ciąg | Identyfikator jednostki SKU planu platformy Azure. Użyj ciągu *0001* dla subskrypcji typu Microsoft Azure Plan. |
| `resellerId`      | Nie       | Ciąg | Identyfikator MPN odsprzedawcy, który będzie skojarzony z subskrypcją.  |

W odpowiedzi uzyskasz obiekt `subscriptionCreationResult` na potrzeby monitorowania. Po zakończeniu tworzenia subskrypcji obiekt `subscriptionCreationResult` zwraca obiekt `subscriptionLink`. Zawiera on identyfikator subskrypcji.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje dotyczące tworzenia subskrypcji Enterprise Agreement (EA) przy użyciu platformy .NET, zobacz [kod przykładowy w usłudze GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
