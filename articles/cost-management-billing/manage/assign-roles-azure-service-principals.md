---
title: Przypisywanie ról do nazw głównych usługi Azure Enterprise Agreement
description: Ten artykuł pomaga przypisywać role do nazw głównych usług przy użyciu programu PowerShell i interfejsów API REST.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/05/2021
ms.author: banders
ms.openlocfilehash: d348eeb5cc789665d7e7004523b9feba0ea6e413
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490560"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Przypisywanie ról do nazw głównych usługi Azure Enterprise Agreement

Rejestracją Enterprise Agreement (EA) można zarządzać w [witrynie Azure Enterprise Portal](https://ea.azure.com/). Można tworzyć różne role w celu zarządzania organizacją, wyświetlania kosztów i tworzenia subskrypcji. Ten artykuł ułatwia Automatyzowanie niektórych z tych zadań przy użyciu interfejsów API Azure PowerShell i REST z nazwami głównych usług platformy Azure.

Przed rozpoczęciem upewnij się, że znasz następujące artykuły:

- [Role umów Enterprise Agreement](understand-ea-roles.md)
- [Logowanie się w programie Azure PowerShell](/powershell/azure/authenticate-azureps)
- [Jak wywołać interfejsy API REST za pomocą programu Poster](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Tworzenie i uwierzytelnianie nazwy głównej usługi

Aby zautomatyzować akcje EA przy użyciu nazwy SPN, należy utworzyć aplikację Azure Active Directory (Azure AD). Uwierzytelnianie jest możliwe w zautomatyzowany sposób. Zapoznaj się z następującymi artykułami i wykonaj kroki w nich w celu utworzenia i uwierzytelnienia nazwy głównej usługi.

1. [Tworzenie nazwy głównej usługi](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Pobieranie wartości identyfikatora dzierżawy i aplikacji na potrzeby logowania](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Oto przykład zrzutu ekranu przedstawiającego rejestrację aplikacji.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Zrzut ekranu przedstawiający rejestrowanie aplikacji." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Znajdź nazwę SPN i identyfikator dzierżawy

Wymagany jest również identyfikator obiektu SPN i identyfikator dzierżawy aplikacji. Informacje o operacjach przypisywania uprawnień można uzyskać w kolejnych sekcjach.

Identyfikator dzierżawy aplikacji usługi Azure AD można znaleźć na stronie Przegląd dla aplikacji. Aby go znaleźć w Azure Portal, przejdź do Azure Active Directory i wybierz pozycję **aplikacje dla przedsiębiorstw**. Wyszukaj aplikację.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Zrzut ekranu przedstawiający przykładową aplikację dla przedsiębiorstw." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Wybierz aplikację. Oto przykład wyświetlania identyfikatora aplikacji i identyfikatora obiektu.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Zrzut ekranu przedstawiający identyfikator aplikacji i identyfikator obiektu dla aplikacji przedsiębiorstwa." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

Identyfikator dzierżawy można znaleźć na stronie przeglądu Microsoft Azure AD.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Zrzut ekranu przedstawiający miejsce wyświetlania identyfikatora dzierżawy." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

Identyfikator dzierżawy głównej jest również nazywany IDENTYFIKATORem podmiotu zabezpieczeń, nazwą SPN i IDENTYFIKATORem obiektu w różnych lokalizacjach. Wartość identyfikatora dzierżawy usługi Azure AD wygląda jak identyfikator GUID o następującym formacie: `11111111-1111-1111-1111-111111111111` .

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Uprawnienia, które można przypisać do nazwy SPN

W następnych krokach nadajesz uprawnienie do aplikacji usługi Azure AD w celu wykonywania akcji przy użyciu roli EA. Do nazwy SPN można przypisać tylko następujące role. Identyfikator definicji roli, dokładnie tak jak pokazano, jest używany później w krokach przypisania.

| Rola | Akcje dozwolone | Identyfikator definicji roli |
| --- | --- | --- |
| EnrollmentReader | Może wyświetlać użycie i opłaty dla wszystkich kont i subskrypcji. Może wyświetlać saldo (wcześniej zwane zobowiązanie pieniężne) związane z rejestracją. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| Zakup EA | Kup zamówienia rezerwacji i Wyświetl transakcje rezerwacji. Może wyświetlać użycie i opłaty dla wszystkich kont i subskrypcji. Może wyświetlać saldo (wcześniej zwane zobowiązanie pieniężne) związane z rejestracją. | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | Pobierz szczegóły użycia dla działu, który administruje. Można wyświetlić użycie i opłaty związane z działem. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Utwórz nowe subskrypcje w danym zakresie konta. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Czytelnik rejestracji może być przypisany do nazwy SPN tylko przez użytkownika z rolą składnika zapisywania rejestracji.
- Czytelnik działu może być przypisany do nazwy SPN tylko przez użytkownika, który ma rolę składnika zapisywania rejestracji lub rolę modułu zapisywania.
- Rolę twórcy z subskrypcją można przypisać do nazwy SPN tylko przez użytkownika, który jest właścicielem konta rejestracji. Rola nie jest wyświetlana w portalu EA. Jest on tworzony tylko przez programowy sposób i jest przeznaczony tylko do użytku programistycznego.
- Rola EA Purchase nie jest wyświetlana w portalu EA. Jest on tworzony tylko przez programowy sposób i jest przeznaczony tylko do użytku programistycznego.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Przypisywanie uprawnień roli konta rejestracji do nazwy SPN

Przeczytaj artykuł o [przypisaniach ról — Umieść](/rest/api/billing/2019-10-01-preview/roleassignments/put) interfejs API REST.

Podczas odczytywania artykułu wybierz pozycję **Wypróbuj** , aby rozpocząć korzystanie z nazwy SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Zrzut ekranu przedstawiający opcję Wypróbuj w artykule Put." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Zaloguj się przy użyciu konta w dzierżawie, która ma dostęp do rejestracji, do której chcesz przypisać dostęp.

Podaj następujące parametry w ramach żądania interfejsu API.

**billingAccountName**

Parametr jest IDENTYFIKATORem konta rozliczeniowego. Można go znaleźć w Azure Portal na stronie przeglądu Cost Management i rozliczeń.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Zrzut ekranu przedstawiający identyfikator konta rozliczeniowego." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametr jest unikatowym identyfikatorem GUID, który należy podać. Identyfikator GUID można wygenerować za pomocą polecenia [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid) programu PowerShell.

Możesz również użyć witryny sieci Web [identyfikatora GUID/UUID](https://guidgenerator.com/) w celu wygenerowania unikatowego identyfikatora GUID.

**wersja interfejsu API**

Użyj wersji **2019-10-01-Preview** .

Treść żądania ma kod JSON, którego należy użyć.

Użyj przykładowej treści żądania w [przypisaniach ról — przykłady](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

Istnieją trzy parametry, których należy użyć jako części JSON.

| Parametr | Gdzie można go znaleźć |
| --- | --- |
| Właściwości. principalId | Zobacz [Znajdź nazwę SPN i identyfikator dzierżawy](#find-your-spn-and-tenant-id). |
| Właściwości. principalTenantId | Zobacz [Znajdź nazwę SPN i identyfikator dzierżawy](#find-your-spn-and-tenant-id). |
| Właściwości. zduplikowanych | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

Nazwa konta rozliczeniowego jest tym samym parametrem, który został użyty w parametrach interfejsu API. Jest to identyfikator rejestracji wyświetlany w portalu EA i Azure Portal.

Zwróć uwagę, że `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` jest identyfikatorem definicji roli rozliczenia dla EnrollmentReader.

Wybierz pozycję **Uruchom** , aby uruchomić polecenie.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Zrzut ekranu przedstawiający przykładowe przypisanie roli. Spróbuj użyć przykładu informacji gotowy do uruchomienia." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

`200 OK`Odpowiedź pokazuje, że nazwa SPN została dodana pomyślnie.

Teraz można użyć nazwy SPN (aplikacja usługi Azure AD z IDENTYFIKATORem obiektu), aby uzyskać dostęp do interfejsów API EA w zautomatyzowany sposób. Nazwa SPN ma rolę EnrollmentReader.

## <a name="assign-ea-purchaser-role-permission-to-the-spn"></a>Przypisywanie uprawnień roli zakupu EA do nazwy SPN 

W przypadku roli zakupu EA należy wykonać te same kroki dla czytnika rejestracji. Określ `roleDefinitionId` , przy użyciu poniższego przykładu.

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

 

## <a name="assign-the-department-reader-role-to-the-spn"></a>Przypisywanie roli czytnika działu do nazwy SPN

Przed rozpoczęciem Przeczytaj artykuł interfejs API REST z [przypisaniami ról działu rejestracji](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) .

Podczas odczytywania artykułu wybierz pozycję **Wypróbuj**.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Zrzut ekranu przedstawiający opcję Wypróbuj w przypisaniu roli działu rejestracji." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Zaloguj się przy użyciu konta w dzierżawie, która ma dostęp do rejestracji, do której chcesz przypisać dostęp.

Podaj następujące parametry w ramach żądania interfejsu API.

**billingAccountName**

Jest to identyfikator konta rozliczeniowego. Można go znaleźć w Azure Portal na stronie przeglądu Cost Management i rozliczeń.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Zrzut ekranu przedstawiający identyfikator konta rozliczeniowego." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametr jest unikatowym identyfikatorem GUID, który należy podać. Identyfikator GUID można wygenerować za pomocą polecenia [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid) programu PowerShell.

Możesz również użyć witryny sieci Web [identyfikatora GUID/UUID](https://guidgenerator.com/) w celu wygenerowania unikatowego identyfikatora GUID.

**departmentName**

Jest to identyfikator działu. Identyfikatory działu można zobaczyć w Azure Portal. Przejdź do Cost Management i rozliczeń > **działów**.

W tym przykładzie użyto działu ACE. Identyfikator dla przykładu to `84819` .

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Zrzut ekranu przedstawiający przykładowy identyfikator działu." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**wersja interfejsu API**

Użyj wersji **2019-10-01-Preview** .

Treść żądania ma kod JSON, którego należy użyć.

Użyj przykładu w [przypisaniu roli działu rejestracji — Put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put). Istnieją trzy parametry, których należy użyć jako części JSON.

| Parametr | Gdzie można go znaleźć |
| --- | --- |
| Właściwości. principalId | Zobacz [Znajdź nazwę SPN i identyfikator dzierżawy](#find-your-spn-and-tenant-id). |
| Właściwości. principalTenantId | Zobacz [Znajdź nazwę SPN i identyfikator dzierżawy](#find-your-spn-and-tenant-id). |
| Właściwości. zduplikowanych | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

Nazwa konta rozliczeniowego jest tym samym parametrem, który został użyty w parametrach interfejsu API. Jest to identyfikator rejestracji wyświetlany w portalu EA i Azure Portal.

Identyfikator definicji roli rozliczeń `db609904-a47f-4794-9be8-9bd86fbffd8a` jest przeznaczony dla czytnika działu.

Wybierz pozycję **Uruchom** , aby uruchomić polecenie.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Zrzut ekranu przedstawiający przykład przypisań ról działu rejestracji — w dalszej części spróbuj użyć przykładowych informacji gotowych do uruchomienia." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

`200 OK`Odpowiedź pokazuje, że nazwa SPN została dodana pomyślnie.

Teraz można użyć nazwy SPN (aplikacja usługi Azure AD z IDENTYFIKATORem obiektu), aby uzyskać dostęp do interfejsów API EA w zautomatyzowany sposób. Nazwa SPN ma rolę DepartmentReader.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Przypisywanie roli twórca subskrypcji do nazwy SPN

Odczytywanie [przypisań ról konta rejestracji — umieszczenie](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) artykułu.

Podczas odczytywania, wybierz opcję **Wypróbuj** , aby przypisać rolę twórcy do nazwy SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Zrzut ekranu przedstawiający opcję Wypróbuj w przypadku przypisywania ról konta rejestracji." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Zaloguj się przy użyciu konta w dzierżawie, która ma dostęp do rejestracji, do której chcesz przypisać dostęp.

Podaj następujące parametry w ramach żądania interfejsu API. Zapoznaj się z artykułem po [przypisaniu roli konta rejestracji — parametry Put-URI](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

**billingAccountName**

Parametr jest IDENTYFIKATORem konta rozliczeniowego. Można go znaleźć w Azure Portal na stronie przeglądu Cost Management i rozliczeń.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Zrzut ekranu przedstawiający identyfikator konta rozliczeniowego." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Parametr jest unikatowym identyfikatorem GUID, który należy podać. Identyfikator GUID można wygenerować za pomocą polecenia [New-GUID](/powershell/module/microsoft.powershell.utility/new-guid) programu PowerShell.

Możesz również użyć witryny sieci Web [identyfikatora GUID/UUID](https://guidgenerator.com/) w celu wygenerowania unikatowego identyfikatora GUID.
**enrollmentAccountName**

Parametr jest IDENTYFIKATORem konta. Znajdź identyfikator konta dla nazwy konta w Azure Portal w Cost Management + rozliczanie w zakresie rejestracji i działu.

W tym przykładzie użyto konta testowego GTM. Identyfikator to `196987` .

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Zrzut ekranu przedstawiający identyfikator konta." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**wersja interfejsu API**

Użyj wersji **2019-10-01-Preview** .

Treść żądania ma kod JSON, którego należy użyć.

Użyj przykładu w [przypisaniu roli działu rejestracji — przykłady](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

Istnieją trzy parametry, których należy użyć jako części JSON.

| Parametr | Gdzie można go znaleźć |
| --- | --- |
| Właściwości. principalId | Zobacz [Znajdź nazwę SPN i identyfikator dzierżawy](#find-your-spn-and-tenant-id). |
| Właściwości. principalTenantId | Zobacz [Znajdź nazwę SPN i identyfikator dzierżawy](#find-your-spn-and-tenant-id). |
| Właściwości. zduplikowanych | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

Nazwa konta rozliczeniowego jest tym samym parametrem, który został użyty w parametrach interfejsu API. Jest to identyfikator rejestracji wyświetlany w portalu EA i Azure Portal.

Identyfikator definicji roli rozliczenia `a0bcee42-bf30-4d1b-926a-48d21664ef71` jest przeznaczony dla roli twórca subskrypcji.

Wybierz pozycję **Uruchom** , aby uruchomić polecenie.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Zrzut ekranu przedstawiający opcję Wypróbuj w przypisaniu roli konta rejestracji — umieszczenie artykułu" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

`200 OK`Odpowiedź pokazuje, że nazwa SPN została dodana pomyślnie.

Teraz można użyć nazwy SPN (aplikacja usługi Azure AD z IDENTYFIKATORem obiektu), aby uzyskać dostęp do interfejsów API EA w zautomatyzowany sposób. Nazwa SPN ma rolę SubscriptionCreator.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [administracji portalu Azure EA](ea-portal-administration.md).