---
title: Uwierzytelnianie przy użyciu tożsamości zarządzanych
description: Dostęp do zasobów chronionych przez Azure Active Directory bez logowania się przy użyciu poświadczeń lub wpisów tajnych za pomocą tożsamości zarządzanej
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 54880f22fae7f9a193a13745702345f5f7efdc32
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210921"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w programie Azure Logic Apps

Aby łatwo uzyskać dostęp do innych zasobów chronionych przez usługę Azure Active Directory (Azure AD) i uwierzytelnić swoją tożsamość, aplikacja logiki może używać [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) (dawniej tożsamość usługi ZARZĄDZANEJ lub MSI), a nie poświadczeń, wpisów tajnych lub tokenów usługi Azure AD. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć Twoje poświadczenia, ponieważ nie ma potrzeby zarządzania wpisami tajnymi ani bezpośredniego używania tokenów usługi Azure AD.

Azure Logic Apps obsługuje zarządzane tożsamości [*przypisane przez system*](../active-directory/managed-identities-azure-resources/overview.md) i [*przypisane przez użytkownika*](../active-directory/managed-identities-azure-resources/overview.md) . Aplikacja logiki lub poszczególne połączenia mogą korzystać z tożsamości przypisanej do systemu lub *jednej* tożsamości przypisanej do użytkownika, którą można udostępnić w ramach grupy aplikacji logiki, ale nie obu.

<a name="triggers-actions-managed-identity"></a>

## <a name="where-can-logic-apps-use-managed-identities"></a>Gdzie Aplikacje logiki mogą korzystać z zarządzanych tożsamości?

Obecnie tylko [określone wbudowane wyzwalacze i akcje](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) oraz [określone łączniki zarządzane](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) obsługujące usługę Azure AD OAuth mogą używać tożsamości zarządzanej na potrzeby uwierzytelniania. Na przykład:

<a name="built-in-managed-identity"></a>

**Wbudowane wyzwalacze i akcje**

* Usługa Azure API Management
* Azure App Services
* Azure Functions
* HTTP
* Element webhook protokołu HTTP

> [!NOTE]
> Wyzwalacz i akcja protokołu HTTP mogą uwierzytelniać połączenia z kontami usługi Azure Storage za zaporami platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu, ale nie mogą uwierzytelniać tych samych połączeń przy użyciu tożsamości zarządzanej przypisanej przez użytkownika.

<a name="managed-connectors-managed-identity"></a>

**Łączniki zarządzane**

* Azure Automation
* Azure Event Grid
* Azure Key Vault
* Azure Resource Manager
* HTTP z usługą Azure AD

Obsługa łączników zarządzanych jest obecnie w wersji zapoznawczej. Aby uzyskać bieżącą listę, zobacz [typy uwierzytelniania dla wyzwalaczy i akcji, które obsługują uwierzytelnianie](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

W tym artykule przedstawiono sposób konfigurowania obu rodzajów zarządzanych tożsamości dla aplikacji logiki. Więcej informacji można znaleźć w następujących tematach:

* [Wyzwalacze i akcje obsługujące tożsamości zarządzane](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [Limity zarządzanych tożsamości dla aplikacji logiki](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD z tożsamościami zarządzanymi](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Zarówno tożsamość zarządzana, jak i docelowy zasób platformy Azure, do którego potrzebujesz dostępu muszą korzystać z tej samej subskrypcji platformy Azure.

* Aby zapewnić zarządzaną tożsamość dostęp do zasobu platformy Azure, należy dodać rolę do zasobu docelowego dla tej tożsamości. Aby dodać role, musisz mieć [uprawnienia administratora usługi Azure AD](../active-directory/roles/permissions-reference.md) , które mogą przypisywać role do tożsamości w odpowiedniej dzierżawie usługi Azure AD.

* Docelowy zasób platformy Azure, do którego chcesz uzyskać dostęp. Na tym zasobie dodasz rolę dla tożsamości zarządzanej, która pomaga aplikacji logiki uwierzytelniać dostęp do zasobu docelowego.

* Aplikacja logiki, w której chcesz używać [wyzwalacza lub akcji, które obsługują tożsamości zarządzane](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

## <a name="enable-managed-identity"></a>Włączanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzaną, która ma być używana, Użyj linku dla tej tożsamości:

* [Tożsamość przypisana przez system](#system-assigned)
* [Tożsamość przypisana przez użytkownika](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Włącz tożsamość przypisaną przez system

W przeciwieństwie do tożsamości przypisanych przez użytkownika nie trzeba ręcznie tworzyć tożsamości przypisanej do systemu. Aby skonfigurować tożsamość przypisaną przez system dla aplikacji logiki, poniżej przedstawiono opcje, których można użyć:

* [Witryna Azure Portal](#azure-portal-system-logic-app)
* [Szablony usługi Azure Resource Manager](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Włącz tożsamość przypisaną przez system w Azure Portal

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **tożsamość**. Wybierz pozycję **system przypisany**  >  **podczas**  >  **zapisywania**. Gdy na platformie Azure zostanie wyświetlony komunikat z prośbą o potwierdzenie, wybierz pozycję **tak**.

   ![Włączanie tożsamości przypisanej do systemu](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Jeśli wystąpi błąd, który może mieć tylko jedną zarządzaną tożsamość, aplikacja logiki jest już skojarzona z tożsamością przypisaną przez użytkownika. Aby można było dodać tożsamość przypisaną do systemu, należy najpierw *usunąć* tożsamość przypisaną przez użytkownika z aplikacji logiki.

   Aplikacja logiki może teraz korzystać z tożsamości przypisanej do systemu, która jest zarejestrowana w usłudze Azure AD i jest reprezentowana przez identyfikator obiektu.

   ![Identyfikator obiektu dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Identyfikator obiektu** | <*Identyfikator zasobu tożsamości*> | Unikatowy identyfikator globalny (GUID) reprezentujący tożsamość przypisaną przez system dla aplikacji logiki w dzierżawie usługi Azure AD |
   ||||

1. Teraz wykonaj [kroki, które zapewniają tej tożsamości dostęp do zasobu](#access-other-resources) w dalszej części tego tematu.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Włącz tożsamość przypisaną przez system w Azure Resource Manager szablonie

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak Logic Apps, możesz użyć [szablonów Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Aby włączyć zarządzaną przez system tożsamość skojarzoną z aplikacją logiki w szablonie, należy dodać `identity` obiekt i `type` Właściwość podrzędną do definicji zasobu aplikacji logiki w szablonie, na przykład:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Gdy platforma Azure utworzy definicję zasobu aplikacji logiki, `identity` obiekt uzyskuje następujące dodatkowe właściwości:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Właściwość (JSON) | Wartość | Opis |
|-----------------|-------|-------------|
| `principalId` | <*Identyfikator podmiotu zabezpieczeń*> | Unikatowy identyfikator globalny (GUID) obiektu jednostki usługi dla tożsamości zarządzanej, która reprezentuje aplikację logiki w dzierżawie usługi Azure AD. Ten identyfikator GUID czasami pojawia się jako "Identyfikator obiektu" lub `objectID` . |
| `tenantId` | <*Azure-AD-dzierżawca-ID*> | Unikatowy identyfikator globalny (GUID) reprezentujący dzierżawę usługi Azure AD, w której aplikacja logiki jest teraz członkiem. W ramach dzierżawy usługi Azure AD główna nazwa ma taką samą nazwę jak wystąpienie aplikacji logiki. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Włącz tożsamość przypisaną przez użytkownika

Aby skonfigurować tożsamość zarządzaną przez użytkownika dla aplikacji logiki, należy najpierw utworzyć tę tożsamość jako osobny autonomiczny zasób platformy Azure. Poniżej przedstawiono opcje, których można użyć:

* [Witryna Azure Portal](#azure-portal-user-identity)
* [Szablony usługi Azure Resource Manager](#template-user-identity)
* Azure PowerShell
  * [Tworzenie tożsamości przypisanej do użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Dodaj przypisanie roli](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Interfejs wiersza polecenia platformy Azure
  * [Tworzenie tożsamości przypisanej do użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Dodaj przypisanie roli](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Interfejs API REST platformy Azure
  * [Tworzenie tożsamości przypisanej do użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Dodaj przypisanie roli](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Utwórz tożsamość przypisaną przez użytkownika w Azure Portal

1. W [Azure Portal](https://portal.azure.com)w polu wyszukiwania na dowolnej stronie wprowadź `managed identities` i wybierz pozycję **zarządzane tożsamości**.

   ![Znajdź i wybierz pozycję "zarządzane tożsamości"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. W obszarze **zarządzane tożsamości** wybierz pozycję **Dodaj**.

   ![Dodaj nową tożsamość zarządzaną](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Podaj informacje o tożsamości zarządzanej, a następnie wybierz pozycję **Przegląd + Utwórz**, na przykład:

   ![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Tak | <*Azure — nazwa subskrypcji*> | Nazwa subskrypcji platformy Azure do użycia |
   | **Grupa zasobów** | Tak | <*Azure-Resource-Group-Name*> | Nazwa grupy zasobów, która ma zostać użyta. Utwórz nową grupę lub wybierz istniejącą grupę. Ten przykład tworzy nową grupę o nazwie `fabrikam-managed-identities-RG` . |
   | **Region** | Tak | <*Platforma Azure — region*> | Region świadczenia usługi Azure, w którym mają być przechowywane informacje o Twoim zasobie. W tym przykładzie zastosowano "zachodnie stany USA". |
   | **Nazwa** | Tak | <*przypisane przez użytkownika-nazwa tożsamości*> | Nazwa do nadania tożsamości przypisanej do użytkownika. W tym przykładzie użyto wartości `Fabrikam-user-assigned-identity`. |
   |||||

   Po zweryfikowaniu tych szczegółów platforma Azure utworzy zarządzaną tożsamość. Teraz możesz dodać tożsamość przypisaną do użytkownika do aplikacji logiki. Do aplikacji logiki nie można dodać więcej niż jednej tożsamości przypisanej do użytkownika.

1. W Azure Portal Znajdź i Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **tożsamość**, a następnie wybierz pozycję Dodaj przypisane przez **użytkownika**  >  .

   ![Dodawanie tożsamości zarządzanej przypisanej przez użytkownika](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. W okienku **Dodawanie tożsamości zarządzanej przypisanej przez użytkownika** z listy **subskrypcja** wybierz subskrypcję platformy Azure, jeśli nie została jeszcze wybrana. Z listy, która zawiera *wszystkie* zarządzane tożsamości w tej subskrypcji, Znajdź i wybierz żądaną tożsamość przypisaną przez użytkownika. Aby odfiltrować listę, w polu wyszukiwania **tożsamości zarządzane przypisane przez użytkownika** wprowadź nazwę tożsamości lub grupy zasobów. Gdy skończysz, wybierz pozycję **Dodaj**.

   ![Wybierz tożsamość przypisaną przez użytkownika, która ma być używana](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Jeśli wystąpi błąd, który może mieć tylko jedną zarządzaną tożsamość, aplikacja logiki jest już skojarzona z tożsamością przypisaną przez system. Przed dodaniem tożsamości przypisanej do użytkownika należy najpierw wyłączyć tożsamość przypisaną przez system w aplikacji logiki.

   Twoja aplikacja logiki jest teraz skojarzona z tożsamością zarządzaną przez użytkownika.

   ![Skojarzenie z tożsamością przypisaną przez użytkownika](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Teraz wykonaj [kroki, które zapewniają tej tożsamości dostęp do zasobu](#access-other-resources) w dalszej części tego tematu.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Tworzenie tożsamości przypisanej przez użytkownika w szablonie Azure Resource Manager

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak Logic Apps, możesz użyć [szablonów Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), które obsługują [tożsamości przypisane przez użytkownika na potrzeby uwierzytelniania](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). W `resources` sekcji szablonu definicja zasobu aplikacji logiki wymaga następujących elementów:

* `identity`Obiekt z `type` właściwością ustawioną na wartość`UserAssigned`

* Obiekt podrzędny `userAssignedIdentities` określający przypisaną przez użytkownika zasób i nazwę

Ten przykład przedstawia definicję zasobu aplikacji logiki dla żądania HTTP PUT i zawiera niesparametryzowanego `identity` obiektu. Odpowiedź na żądanie PUT i kolejna operacja pobrania również mają ten `identity` obiekt:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Jeśli szablon zawiera również definicję zasobu tożsamości zarządzanej, można Sparametryzuj `identity` obiekt. Ten przykład pokazuje, jak `userAssignedIdentities` obiekt podrzędny odwołuje się do zmiennej zdefiniowanej `userAssignedIdentity` w sekcji szablonu `variables` . Ta zmienna odwołuje się do identyfikatora zasobu dla tożsamości przypisanej do użytkownika.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Przyznaj tożsamości dostęp do zasobów

Aby można było użyć tożsamości zarządzanej aplikacji logiki na potrzeby uwierzytelniania, Skonfiguruj dostęp dla tej tożsamości w zasobie platformy Azure, w którym planujesz używać tożsamości. Aby wykonać to zadanie, przypisz odpowiednią rolę do tej tożsamości w docelowym zasobie platformy Azure. Poniżej przedstawiono opcje, których można użyć:

* [Witryna Azure Portal](#azure-portal-assign-access)
* [Szablon usługi Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) — Aby uzyskać więcej informacji, zobacz [Dodawanie przypisania roli przy użyciu usług Azure RBAC i Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Interfejs wiersza polecenia platformy Azure ([AZ role Create](/cli/azure/role/assignment#az-role-assignment-create)) — Aby uzyskać więcej informacji, zobacz [Dodawanie przypisania roli przy użyciu funkcji Azure RBAC i interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md).
* [Interfejs API REST platformy Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Przypisz dostęp w Azure Portal

W docelowym zasobie platformy Azure, w którym ma być dostępna tożsamość zarządzana, nadaj dostęp do zasobu docelowego opartego na rolach tożsamości.

1. W [Azure Portal](https://portal.azure.com)przejdź do zasobu platformy Azure, do którego Twoja tożsamość zarządza ma mieć dostęp.

1. Z menu zasób wybierz pozycję przypisania roli **Kontrola dostępu (IAM)**, w  >   której można przejrzeć bieżące przypisania ról dla tego zasobu. Na pasku narzędzi wybierz pozycję **Dodaj**  >  **Dodaj przypisanie roli**.

   ![Wybierz pozycję "Dodaj" > "Dodaj przypisanie roli"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Jeśli opcja **Dodaj przypisanie roli** jest wyłączona, prawdopodobnie nie masz uprawnień. Aby uzyskać więcej informacji o uprawnieniach, które umożliwiają zarządzanie rolami dla zasobów, zobacz [uprawnienia roli administrator w Azure Active Directory](../active-directory/roles/permissions-reference.md).

1. W obszarze **Dodaj przypisanie roli** wybierz **rolę** , która zapewnia tożsamości wymagane do uzyskania dostępu do zasobu docelowego.

   Na przykład w tym temacie tożsamość musi mieć [rolę, która może uzyskiwać dostęp do obiektu BLOB w kontenerze usługi Azure Storage](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights), dlatego wybierz rolę **współautor danych obiektów blob magazynu** dla tożsamości zarządzanej.

   ![Wybierz rolę "Współautor danych obiektu blob magazynu"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Wykonaj następujące kroki dla tożsamości zarządzanej:

   * **Tożsamość przypisana przez system**

     1. W polu **Przypisz dostęp do** wybierz pozycję **aplikacja logiki**. Gdy zostanie wyświetlona Właściwość **subskrypcja** , wybierz subskrypcję platformy Azure, która jest skojarzona z Twoją tożsamością.

        ![Wybieranie dostępu dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/assign-access-system.png)

     1. W polu **Wybierz** wybierz swoją aplikację logiki z listy. Jeśli lista jest zbyt długa, użyj pola **wyboru** , aby odfiltrować listę.

        ![Wybierz aplikację logiki dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Tożsamość przypisana przez użytkownika**

     1. W polu **Przypisz dostęp do** wybierz opcję **tożsamość zarządzana przypisana przez użytkownika**. Gdy zostanie wyświetlona Właściwość **subskrypcja** , wybierz subskrypcję platformy Azure, która jest skojarzona z Twoją tożsamością.

        ![Wybieranie dostępu dla tożsamości przypisanej do użytkownika](./media/create-managed-service-identity/assign-access-user.png)

     1. W polu **Wybierz** z listy wybierz swoją tożsamość. Jeśli lista jest zbyt długa, użyj pola **wyboru** , aby odfiltrować listę.

        ![Wybierz swoją tożsamość przypisaną przez użytkownika](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

   Lista przypisań ról zasobu docelowego zawiera teraz wybraną zarządzaną tożsamość i rolę. Ten przykład pokazuje, jak można użyć tożsamości przypisanej do systemu dla jednej aplikacji logiki i przypisanej przez użytkownika tożsamości dla grupy innych aplikacji logiki.

   ![Dodano zarządzane tożsamości i role do zasobu docelowego](./media/create-managed-service-identity/added-roles-for-identities.png)

   Aby uzyskać więcej informacji, [Przypisz zarządzaną tożsamość do zasobu przy użyciu Azure Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Wykonaj kroki w [celu uwierzytelnienia dostępu za pomocą tożsamości](#authenticate-access-with-identity) w wyzwalaczu lub akcji, która obsługuje zarządzane tożsamości.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Uwierzytelnianie dostępu przy użyciu tożsamości zarządzanej

Po [włączeniu zarządzanej tożsamości dla aplikacji logiki](#azure-portal-system-logic-app) i [udzieleniu tej tożsamości dostępu do zasobu lub jednostki docelowej](#access-other-resources)można użyć tej tożsamości w [wyzwalaczach i akcjach, które obsługują tożsamości zarządzane](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

> [!IMPORTANT]
> Jeśli masz funkcję platformy Azure, w której chcesz użyć tożsamości przypisanej do systemu, najpierw [Włącz uwierzytelnianie dla usługi Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

W tych krokach pokazano, jak używać zarządzanej tożsamości z wyzwalaczem lub akcją za pomocą Azure Portal. Aby określić zarządzaną tożsamość w wyzwalaczu lub podstawowej definicji JSON akcji, zobacz [uwierzytelnianie tożsamości zarządzanej](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Jeśli jeszcze tego nie zrobiono, Dodaj [wyzwalacz lub akcję, która obsługuje zarządzane tożsamości](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

   > [!NOTE]
   > Nie wszystkie wyzwalacze i akcje obsługują dodawanie typu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [typy uwierzytelniania dla wyzwalaczy i akcji, które obsługują uwierzytelnianie](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Na dodanym wyzwalaczu lub akcji wykonaj następujące kroki:

   * **Wbudowane wyzwalacze i akcje obsługujące używanie tożsamości zarządzanej**

     1. Dodaj właściwość **Authentication** , jeśli właściwość nie jest już widoczna.

     1. W obszarze **Typ uwierzytelniania** wybierz pozycję **zarządzana tożsamość**.

     Aby uzyskać więcej informacji, zobacz [przykład: uwierzytelnianie wbudowanego wyzwalacza lub akcji przy użyciu tożsamości zarządzanej](#authenticate-built-in-managed-identity).
 
   * **Wyzwalacze zarządzanego łącznika i akcje obsługujące używanie tożsamości zarządzanej**

     1. Na stronie Wybór dzierżawy wybierz pozycję **Połącz z tożsamością zarządzaną**.

     1. Na następnej stronie Podaj nazwę połączenia.

        Domyślnie na liście zarządzanych tożsamości jest wyświetlana tylko obecnie włączona tożsamość zarządzana, ponieważ aplikacja logiki obsługuje jednocześnie Włączanie tylko jednej zarządzanej tożsamości, na przykład:

        ![Zrzut ekranu przedstawiający stronę Nazwa połączenia i wybraną tożsamość zarządzaną.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     Aby uzyskać więcej informacji, zobacz [przykład: uwierzytelnianie wyzwalacza lub akcji łącznika zarządzanego przy użyciu tożsamości zarządzanej](#authenticate-managed-connector-managed-identity).

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Przykład: uwierzytelnianie wbudowanego wyzwalacza lub akcji przy użyciu tożsamości zarządzanej

Wyzwalacz HTTP lub akcja może korzystać z tożsamości przypisanej do systemu, która została włączona dla aplikacji logiki. Na ogół wyzwalacz HTTP lub akcja używa tych właściwości do określenia zasobu lub jednostki, do których chcesz uzyskać dostęp:

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Metoda** | Tak | Metoda HTTP, która jest używana przez operację, którą chcesz uruchomić |
| **URI** | Tak | Adres URL punktu końcowego służący do uzyskiwania dostępu do docelowego zasobu lub jednostki platformy Azure. Składnia identyfikatora URI zwykle zawiera [Identyfikator zasobu](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) dla usługi lub zasobu platformy Azure. |
| **Nagłówki** | Nie | Wszystkie wartości nagłówka, które są potrzebne lub które mają zostać uwzględnione w żądaniu wychodzącym, takie jak typ zawartości. |
| **Zapytania** | Nie | Wszystkie parametry zapytania, które są potrzebne lub które mają zostać uwzględnione w żądaniu, takie jak parametr określonej operacji lub wersja interfejsu API dla operacji, którą chcesz uruchomić |
| **Authentication** | Tak | Typ uwierzytelniania używany do uwierzytelniania dostępu do zasobu lub jednostki docelowej |
||||

Na przykład załóżmy, że chcesz uruchomić [operację tworzenia migawek obiektów](/rest/api/storageservices/snapshot-blob) BLOB na obiekcie BLOB na koncie usługi Azure Storage, na którym wcześniej skonfigurowano dostęp do Twojej tożsamości. Jednak [Łącznik usługi Azure Blob Storage](/connectors/azureblob/) nie oferuje obecnie tej operacji. Zamiast tego można uruchomić tę operację za pomocą [akcji http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) lub innej [operacji interfejsu API REST usługi BLOB Service](/rest/api/storageservices/operations-on-blobs).

> [!IMPORTANT]
> Aby uzyskać dostęp do kont usługi Azure Storage za zaporami przy użyciu żądań HTTP i tożsamości zarządzanych, należy się upewnić, że konto magazynu zostało również skonfigurowane z [wyjątkiem, który zezwala na dostęp za pomocą zaufanych usług firmy Microsoft](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

Aby uruchomić [operację tworzenia migawek obiektów BLOB](/rest/api/storageservices/snapshot-blob), Akcja http określa następujące właściwości:

| Właściwość | Wymagany | Przykładowa wartość | Opis |
|----------|----------|---------------|-------------|
| **Metoda** | Tak | `PUT`| Metoda HTTP, której używa operacja obiektu BLOB Snapshot |
| **URI** | Tak | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Identyfikator zasobu dla pliku Blob Storage platformy Azure w środowisku globalnym (publicznym) platformy Azure, który używa tej składni |
| **Nagłówki** | Dla usługi Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | `x-ms-blob-type` `x-ms-version` Wartości nagłówka, i `x-ms-date` są wymagane dla operacji usługi Azure Storage. <p><p>**Ważne**: w wychodzących wyzwalaczach http i żądaniach akcji dla usługi Azure Storage nagłówek wymaga `x-ms-version` właściwości i wersji interfejsu API dla operacji, która ma zostać uruchomiona. Wartość `x-ms-date` musi być datą bieżącą. W przeciwnym razie aplikacja logiki zakończy się niepowodzeniem z `403 FORBIDDEN` powodu błędu. Aby uzyskać bieżącą datę w wymaganym formacie, można użyć wyrażenia w przykładowej wartości. <p>Więcej informacji można znaleźć w następujących tematach: <p><p>- [Nagłówki żądań — obiekt BLOB migawek](/rest/api/storageservices/snapshot-blob#request) <br>- [Przechowywanie wersji usług Azure Storage](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Zapytania** | Tylko dla operacji migawki obiektu BLOB | `comp` = `snapshot` | Nazwa i wartość parametru zapytania dla operacji. |
|||||

Oto przykładowa akcja HTTP, która wyświetla wszystkie te wartości właściwości:

![Dodawanie akcji HTTP w celu uzyskania dostępu do zasobu platformy Azure](./media/create-managed-service-identity/http-action-example.png)

1. Po dodaniu akcji HTTP Dodaj właściwość **uwierzytelniania** do akcji http. Z listy **Dodaj nowy parametr** wybierz pozycję **uwierzytelnianie**.

   ![Dodaj właściwość "Authentication" do akcji HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nie wszystkie wyzwalacze i akcje obsługują dodawanie typu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [typy uwierzytelniania dla wyzwalaczy i akcji, które obsługują uwierzytelnianie](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions).

1. Z listy **Typ uwierzytelniania** wybierz pozycję **zarządzana tożsamość**.

   ![W obszarze "Uwierzytelnianie" Wybierz pozycję "tożsamość zarządzana"](./media/create-managed-service-identity/select-managed-identity.png)

1. Z listy tożsamość zarządzana wybierz opcję spośród dostępnych opcji w zależności od danego scenariusza.

   * W przypadku skonfigurowania tożsamości przypisanej do systemu wybierz opcję **przypisana przez system tożsamość zarządzana** , jeśli nie została jeszcze wybrana.

     ![Wybierz pozycję "zarządzana tożsamość przypisana przez system"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Jeśli skonfigurujesz tożsamość przypisaną przez użytkownika, wybierz tę tożsamość, jeśli nie została jeszcze wybrana.

     ![Wybierz tożsamość przypisaną przez użytkownika](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Ten przykład jest kontynuowany przez **przypisaną tożsamość zarządzaną przez system**.

1. W przypadku niektórych wyzwalaczy i akcji zostanie również wyświetlona Właściwość **odbiorców** służąca do ustawiania docelowego identyfikatora zasobu. Ustaw właściwość **odbiorców** na [Identyfikator zasobu dla zasobu lub usługi docelowej](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). W przeciwnym razie domyślnie Właściwość **odbiorców** używa `https://management.azure.com/` identyfikatora zasobu, który jest identyfikatorem zasobu dla Azure Resource Manager.

   > [!IMPORTANT]
   > Upewnij się, że identyfikator zasobu docelowego *dokładnie pasuje* do wartości, która oczekuje Azure Active Directory (AD), w tym wszystkich wymaganych końcowych ukośników. Na przykład identyfikator zasobu dla wszystkich kont usługi Azure Blob Storage wymaga końcowego ukośnika. Jednak identyfikator zasobu dla określonego konta magazynu nie wymaga końcowej kreski ułamkowej. Sprawdź [identyfikatory zasobów usług platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Ten przykład ustawia właściwość **odbiorców** `https://storage.azure.com/` tak, aby tokeny dostępu używane na potrzeby uwierzytelniania były prawidłowe dla wszystkich kont magazynu. Można jednak również określić adres URL usługi głównej, `https://fabrikamstorageaccount.blob.core.windows.net` dla określonego konta magazynu.

   ![Ustaw właściwość "odbiorców" na identyfikator zasobu docelowego](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Aby uzyskać więcej informacji na temat autoryzowania dostępu za pomocą usługi Azure AD dla usługi Azure Storage, zobacz następujące tematy:

   * [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autoryzuj dostęp do usługi Azure Storage za pomocą Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Kontynuuj tworzenie aplikacji logiki w żądany sposób.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Przykład: uwierzytelnianie wyzwalacza lub akcji łącznika zarządzanego przy użyciu tożsamości zarządzanej

Akcja Azure Resource Manager, **Odczytaj zasób**, może korzystać z zarządzanej tożsamości włączonej dla aplikacji logiki. Ten przykład pokazuje, jak używać tożsamości zarządzanej przypisanej do systemu.

1. Po dodaniu akcji do przepływu pracy na stronie Wybór dzierżawy wybierz pozycję **Połącz z tożsamością zarządzaną**.

   ![Zrzut ekranu pokazujący akcję Azure Resource Manager i wybraną wartość "Połącz z tożsamością zarządzaną".](./media/create-managed-service-identity/select-connect-managed-identity.png)

   W tej akcji zostanie wyświetlona strona nazwa połączenia z listą zarządzanych tożsamości obejmująca typ tożsamości zarządzanej, który jest obecnie włączony w aplikacji logiki.

1. Na stronie Nazwa połączenia Podaj nazwę połączenia. Z listy zarządzana tożsamość Wybierz zarządzaną tożsamość, która jest **tożsamością zarządzaną przez system** , w tym przykładzie, a następnie wybierz pozycję **Utwórz**. Jeśli została włączona tożsamość zarządzana przypisana przez użytkownika, wybierz tę tożsamość zamiast tego.

   ![Zrzut ekranu pokazujący akcję Azure Resource Manager z wprowadzonymi nazwami połączenia i wybraną opcją "zarządzana przez system skojarzoną tożsamość".](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   Jeśli zarządzana tożsamość nie jest włączona, podczas próby utworzenia połączenia pojawia się następujący błąd:

   *Musisz włączyć zarządzaną tożsamość dla aplikacji logiki, a następnie udzielić wymaganego dostępu do tożsamości w zasobie docelowym.*

   ![Zrzut ekranu pokazujący Azure Resource Manager akcję z błędem, gdy nie jest włączona żadna tożsamość zarządzana.](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. Po pomyślnym utworzeniu połączenia Projektant może pobrać wszystkie wartości dynamiczne, zawartość lub schemat przy użyciu uwierzytelniania tożsamości zarządzanej.

1. Kontynuuj tworzenie aplikacji logiki w żądany sposób.

<a name="logic-app-resource-definition-connection-managed-identity"></a>

### <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity"></a>Definicja zasobu aplikacji logiki i połączenia korzystające z tożsamości zarządzanej

Połączenie, które umożliwia i używa tożsamości zarządzanej, jest specjalnym typem połączenia, który działa tylko z tożsamością zarządzaną. W czasie wykonywania połączenie używa zarządzanej tożsamości, która jest włączona w aplikacji logiki. Ta konfiguracja jest zapisywana w obiekcie definicji zasobu aplikacji logiki `parameters` , który zawiera `$connections` obiekt, który zawiera wskaźniki do identyfikatora zasobu połączenia wraz z identyfikatorem zasobu tożsamości, jeśli jest włączona tożsamość przypisana przez użytkownika.

Ten przykład pokazuje, jak wygląda konfiguracja, gdy aplikacja logiki włącza tożsamość zarządzaną przypisaną przez system:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Ten przykład pokazuje, jak wygląda konfiguracja, gdy aplikacja logiki włącza tożsamość zarządzaną przypisaną przez użytkownika:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Podczas wykonywania usługa Logic Apps sprawdza, czy wszystkie wyzwalacze i akcje zarządzanego łącznika w aplikacji logiki są skonfigurowane do korzystania z tożsamości zarządzanej oraz czy wszystkie wymagane uprawnienia są skonfigurowane do używania tożsamości zarządzanej do uzyskiwania dostępu do zasobów docelowych określonych przez wyzwalacz i akcje. W przypadku powodzenia usługa Logic Apps pobiera token usługi Azure AD skojarzony z zarządzaną tożsamością i używa tej tożsamości do uwierzytelniania dostępu do zasobu docelowego i wykonywania skonfigurowanej operacji w wyzwalaczu i akcjach.

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities"></a>Szablon ARM dla zarządzanych połączeń i tożsamości zarządzanych

W przypadku automatyzowania wdrażania przy użyciu szablonu ARM, gdy aplikacja logiki zawiera wyzwalacz łącznika zarządzanego lub akcję, która używa tożsamości zarządzanej, należy sprawdzić, czy podstawowa definicja zasobu połączenia zawiera `parameterValueType` Właściwość z `Alternative` wartością właściwości. W przeciwnym razie wdrożenie usługi ARM nie skonfiguruje połączenia do korzystania z tożsamości zarządzanej na potrzeby uwierzytelniania, a połączenie nie będzie działało w przepływie pracy aplikacji logiki. To wymaganie dotyczy tylko [określonych wyzwalaczy łączników zarządzanych i akcji](#managed-connectors-managed-identity) , w których wybrano [opcję **Połącz z zarządzaną tożsamością**](#authenticate-managed-connector-managed-identity).

Poniżej przedstawiono na przykład podstawową definicję zasobu połączenia dla akcji Azure Automation, która używa tożsamości zarządzanej, w której definicja zawiera `parameterValueType` Właściwość, która jest ustawiona `Alternative` jako wartość właściwości:

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Wyłącz tożsamość zarządzaną

Aby zatrzymać korzystanie z tożsamości zarządzanej dla aplikacji logiki, możesz skorzystać z następujących opcji:

* [Witryna Azure Portal](#azure-portal-disable)
* [Szablony usługi Azure Resource Manager](#template-disable)
* Azure PowerShell
  * [Usuń przypisanie roli](../role-based-access-control/role-assignments-powershell.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Interfejs wiersza polecenia platformy Azure
  * [Usuń przypisanie roli](../role-based-access-control/role-assignments-cli.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Interfejs API REST platformy Azure
  * [Usuń przypisanie roli](../role-based-access-control/role-assignments-rest.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Jeśli usuniesz aplikację logiki, platforma Azure automatycznie usunie zarządzaną tożsamość z usługi Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Wyłącz tożsamość zarządzaną w Azure Portal

W Azure Portal najpierw usuń dostęp tożsamości do [zasobu docelowego](#disable-identity-target-resource). Następnie wyłącz tożsamość przypisaną do systemu lub Usuń tożsamość przypisaną przez użytkownika z [aplikacji logiki](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Usuń dostęp do tożsamości z zasobów

1. W [Azure Portal](https://portal.azure.com)przejdź do docelowego zasobu platformy Azure, w którym chcesz usunąć dostęp dla tożsamości zarządzanej.

1. Z menu zasobu docelowego wybierz pozycję **Kontrola dostępu (IAM)**. Na pasku narzędzi wybierz pozycję **przypisania ról**.

1. Na liście Role wybierz zarządzane tożsamości, które chcesz usunąć. Na pasku narzędzi wybierz pozycję **Usuń**.

   > [!TIP]
   > Jeśli opcja **Usuń** jest wyłączona, prawdopodobnie nie masz uprawnień. Aby uzyskać więcej informacji o uprawnieniach, które umożliwiają zarządzanie rolami dla zasobów, zobacz [uprawnienia roli administrator w Azure Active Directory](../active-directory/roles/permissions-reference.md).

Zarządzana tożsamość została teraz usunięta i nie ma już dostępu do zasobu docelowego.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Wyłącz tożsamość zarządzaną w aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **tożsamość**, a następnie postępuj zgodnie z instrukcjami dotyczącymi Twojej tożsamości:

   * Wybierz pozycję **system przypisany**  >  **podczas**  >  **zapisywania**. Gdy na platformie Azure zostanie wyświetlony komunikat z prośbą o potwierdzenie, wybierz pozycję **tak**.

     ![Wyłączanie tożsamości przypisanej do systemu](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Wybierz pozycję **przypisane przez użytkownika** i tożsamość zarządzana, a następnie wybierz pozycję **Usuń**. Gdy na platformie Azure zostanie wyświetlony komunikat z prośbą o potwierdzenie, wybierz pozycję **tak**.

     ![Usuwanie tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Zarządzana tożsamość jest teraz wyłączona w aplikacji logiki.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Wyłącz tożsamość zarządzaną w szablonie Azure Resource Manager

Jeśli utworzono tożsamość zarządzaną aplikacji logiki przy użyciu szablonu Azure Resource Manager, ustaw `identity` `type` Właściwość podrzędną obiektu na `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie dostępu i danych w Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
