---
title: Uwierzytelnianie przy użyciu tożsamości zarządzanych
description: Uzyskiwanie dostępu do zasobów chronionych przez Azure Active Directory bez logowania się przy użyciu poświadczeń lub wpisów tajnych przy użyciu tożsamości zarządzanej
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 8e081257d70c9bc9c9f75df18b30f8dcf119e48e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763347"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w usłudze Azure Logic Apps

Aby łatwo uzyskać dostęp do innych zasobów chronionych przez usługę Azure Active Directory (Azure AD) i uwierzytelnić tożsamość, aplikacja logiki może używać tożsamości zarządzanej [(dawniej](../active-directory/managed-identities-azure-resources/overview.md) tożsamości usługi zarządzanej lub tożsamości usługi zarządzanej), a nie poświadczeń, wpisów tajnych lub tokenów usługi Azure AD. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć poświadczenia, ponieważ nie trzeba zarządzać wpisami tajnymi ani bezpośrednio używać tokenów usługi Azure AD.

Azure Logic Apps zarówno tożsamości zarządzane przypisane przez [*system,*](../active-directory/managed-identities-azure-resources/overview.md) jak i przypisane [*przez*](../active-directory/managed-identities-azure-resources/overview.md) użytkownika. Aplikacja logiki lub poszczególne połączenia mogą używać tożsamości  przypisanej przez system lub jednej tożsamości przypisanej przez użytkownika, którą można udostępniać w grupie aplikacji logiki, ale nie obu tych tożsamości.

<a name="triggers-actions-managed-identity"></a>

## <a name="where-can-logic-apps-use-managed-identities"></a>Gdzie aplikacje logiki mogą używać tożsamości zarządzanych?

Obecnie tylko [określone wbudowane wyzwalacze](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) i [](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) akcje oraz określone łączniki zarządzane, które obsługują uwierzytelnianie OAuth usługi Azure AD, mogą używać tożsamości zarządzanej do uwierzytelniania. Oto na przykład wybór:

<a name="built-in-managed-identity"></a>

**Wbudowane wyzwalacze i akcje**

* Usługa Azure API Management
* Azure App Services
* Azure Functions
* HTTP
* HTTP i webhook

> [!NOTE]
> Wyzwalacz i akcja HTTP mogą uwierzytelniać połączenia z kontami usługi Azure Storage za zaporami platformy Azure przy użyciu przypisanej przez system tożsamości zarządzanej, ale nie mogą używać tożsamości zarządzanej przypisanej przez użytkownika do uwierzytelniania tych samych połączeń.

<a name="managed-connectors-managed-identity"></a>

**Łączniki zarządzane**

* Azure Automation
* Azure Event Grid
* Azure Key Vault
* Azure Resource Manager
* HTTP z usługą Azure AD

Obsługa łączników zarządzanych jest obecnie dostępna w wersji zapoznawczej. Aby uzyskać bieżącą listę, zobacz [Typy uwierzytelniania dla wyzwalaczy i akcji, które obsługują uwierzytelnianie.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

W tym artykule pokazano, jak skonfigurować oba rodzaje tożsamości zarządzanych dla aplikacji logiki. Więcej informacji można znaleźć w następujących tematach:

* [Wyzwalacze i akcje, które obsługują tożsamości zarządzane](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [Limity tożsamości zarządzanych dla aplikacji logiki](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD przy użyciu tożsamości zarządzanych](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Zarówno tożsamość zarządzana, jak i docelowy zasób platformy Azure, do którego potrzebujesz dostępu, muszą używać tej samej subskrypcji platformy Azure.

* Aby udzielić tożsamości zarządzanej dostępu do zasobu platformy Azure, musisz dodać rolę do zasobu docelowego dla tej tożsamości. Aby dodać role, potrzebne są uprawnienia [administratora usługi Azure AD,](../active-directory/roles/permissions-reference.md) które mogą przypisywać role do tożsamości w odpowiedniej dzierżawie usługi Azure AD.

* Docelowy zasób platformy Azure, do którego chcesz uzyskać dostęp. W tym zasobie dodasz rolę dla tożsamości zarządzanej, która ułatwia aplikacji logiki uwierzytelnianie dostępu do zasobu docelowego.

* Aplikacja logiki, w której chcesz używać [wyzwalacza lub akcji, które obsługują tożsamości zarządzane.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

## <a name="enable-managed-identity"></a>Włączanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzaną, której chcesz użyć, kliknij link dla tej tożsamości:

* [Tożsamość przypisana przez system](#system-assigned)
* [Tożsamość przypisana przez użytkownika](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Włączanie tożsamości przypisanej przez system

W przeciwieństwie do tożsamości przypisanych przez użytkownika nie trzeba ręcznie tworzyć tożsamości przypisanej przez system. Aby skonfigurować tożsamość przypisaną przez system dla aplikacji logiki, możesz użyć następującej opcji:

* [Witryna Azure Portal](#azure-portal-system-logic-app)
* [Szablony usługi Azure Resource Manager](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Włączanie tożsamości przypisanej przez system w Azure Portal

1. W [Azure Portal](https://portal.azure.com)otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Tożsamość**. Wybierz **pozycję System przypisany przy**  >  **zapisywanych**  >  **plikach**. Gdy platforma Azure wyświetli monit o potwierdzenie, wybierz pozycję **Tak.**

   ![Włączanie tożsamości przypisanej przez system](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Jeśli wystąpi błąd, że możesz mieć tylko jedną tożsamość zarządzaną, aplikacja logiki jest już skojarzona z tożsamością przypisaną przez użytkownika. Przed dodaniu tożsamości przypisanej przez system  należy najpierw usunąć tożsamość przypisaną przez użytkownika z aplikacji logiki.

   Aplikacja logiki może teraz używać tożsamości przypisanej przez system, która jest zarejestrowana w usłudze Azure AD i reprezentowana przez identyfikator obiektu.

   ![Identyfikator obiektu dla tożsamości przypisanej przez system](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Identyfikator obiektu** | <*identity-resource-ID*> | Unikatowy identyfikator globalny (GUID) reprezentujący tożsamość przypisaną przez system dla aplikacji logiki w dzierżawie usługi Azure AD |
   ||||

1. Teraz wykonaj [kroki, które zapewniają tej tożsamości dostęp do zasobu w](#access-other-resources) dalszej części tego tematu.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Włączanie tożsamości przypisanej przez system w Azure Resource Manager szablonu

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak aplikacje logiki, można użyć [Azure Resource Manager szablonów.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Aby włączyć tożsamość zarządzaną przypisaną przez system dla aplikacji logiki w szablonie, dodaj obiekt i właściwość podrzędną do definicji zasobu aplikacji logiki w `identity` `type` szablonie, na przykład:

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

Gdy platforma Azure tworzy definicję zasobu aplikacji logiki, `identity` obiekt pobiera następujące dodatkowe właściwości:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Właściwość (JSON) | Wartość | Opis |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | Unikatowy identyfikator globalny (GUID) obiektu jednostki usługi dla tożsamości zarządzanej, która reprezentuje aplikację logiki w dzierżawie usługi Azure AD. Ten identyfikator GUID jest czasami wyświetlany jako "identyfikator obiektu" lub `objectID` . |
| `tenantId` | <*Identyfikator dzierżawy usługi Azure AD*> | Unikatowy identyfikator globalny (GUID) reprezentujący dzierżawę usługi Azure AD, której członkiem jest teraz aplikacja logiki. Wewnątrz dzierżawy usługi Azure AD nazwa główna usługi ma taką samą nazwę jak wystąpienie aplikacji logiki. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Włączanie tożsamości przypisanej przez użytkownika

Aby skonfigurować tożsamość zarządzaną przypisaną przez użytkownika dla aplikacji logiki, musisz najpierw utworzyć tę tożsamość jako oddzielny autonomiczny zasób platformy Azure. Oto opcje, których można użyć:

* [Witryna Azure Portal](#azure-portal-user-identity)
* [Szablony usługi Azure Resource Manager](#template-user-identity)
* Azure PowerShell
  * [Tworzenie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Dodaj przypisanie roli](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Interfejs wiersza polecenia platformy Azure
  * [Tworzenie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Dodaj przypisanie roli](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Interfejs API REST platformy Azure
  * [Tworzenie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Dodaj przypisanie roli](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Tworzenie tożsamości przypisanej przez użytkownika w Azure Portal

1. W polu [Azure Portal](https://portal.azure.com)na dowolnej stronie wpisz , a `managed identities` następnie wybierz pozycję Tożsamości **zarządzane.**

   ![Znajdowanie i wybieranie opcji "Tożsamości zarządzane"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. W **obszarze Tożsamości zarządzane wybierz** pozycję **Dodaj**.

   ![Dodawanie nowej tożsamości zarządzanej](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Podaj informacje o tożsamości zarządzanej, a następnie wybierz **pozycję Przejrzyj i utwórz,** na przykład:

   ![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Subskrypcja** | Tak | <*Nazwa subskrypcji platformy Azure*> | Nazwa subskrypcji platformy Azure do użycia |
   | **Grupa zasobów** | Tak | <*Nazwa grupy zasobów platformy Azure*> | Nazwa grupy zasobów do użycia. Utwórz nową grupę lub wybierz istniejącą grupę. Ten przykład tworzy nową grupę o nazwie `fabrikam-managed-identities-RG` . |
   | **Region** | Tak | <*Region świadczenia usługi Azure*> | Region świadczenia usługi Azure, w którym mają być przechowywane informacje o zasobie. W tym przykładzie użyto "Zachodnie stany USA". |
   | **Nazwa** | Tak | <*nazwa tożsamości przypisanej przez użytkownika*> | Nazwa nadawać tożsamości przypisanej przez użytkownika. W tym przykładzie użyto wartości `Fabrikam-user-assigned-identity`. |
   |||||

   Po weryfikacji tych szczegółów platforma Azure utworzy tożsamość zarządzaną. Teraz możesz dodać tożsamość przypisaną przez użytkownika do aplikacji logiki. Do aplikacji logiki nie można dodać więcej niż jednej tożsamości przypisanej przez użytkownika.

1. W Azure Portal znajdź i otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Tożsamość,** a następnie wybierz pozycję **Przypisano użytkownika**  >  **Dodaj**.

   ![Dodawanie tożsamości zarządzanej przypisanej przez użytkownika](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. W **okienku Dodawanie tożsamości zarządzanej przypisanej** przez użytkownika z listy **Subskrypcja** wybierz swoją subskrypcję platformy Azure, jeśli nie została jeszcze wybrana. Na liście, która zawiera *wszystkie* tożsamości zarządzane w tej subskrypcji, znajdź i wybierz tożsamość przypisaną przez użytkownika. Aby filtrować listę, w polu wyszukiwania **Tożsamości** zarządzane przypisane przez użytkownika wprowadź nazwę tożsamości lub grupy zasobów. Gdy wszystko będzie gotowe, wybierz pozycję **Dodaj**.

   ![Wybierz tożsamość przypisaną przez użytkownika do użycia](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Jeśli wystąpi błąd, że możesz mieć tylko jedną tożsamość zarządzaną, aplikacja logiki jest już skojarzona z tożsamością przypisaną przez system. Aby można było dodać tożsamość przypisaną przez użytkownika, należy najpierw wyłączyć tożsamość przypisaną przez system w aplikacji logiki.

   Aplikacja logiki jest teraz skojarzona z tożsamością zarządzaną przypisaną przez użytkownika.

   ![Skojarzenie z tożsamością przypisaną przez użytkownika](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Teraz wykonaj [kroki, które zapewniają tej tożsamości dostęp do zasobu w](#access-other-resources) dalszej części tego tematu.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Tworzenie tożsamości przypisanej przez użytkownika w szablonie Azure Resource Manager szablonu

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak aplikacje logiki, można użyć szablonów usługi [Azure Resource Manager,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)które obsługują tożsamości przypisane przez [użytkownika do uwierzytelniania.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) W sekcji szablonu definicja zasobu aplikacji `resources` logiki wymaga tych elementów:

* Obiekt `identity` z `type` właściwością ustawioną na `UserAssigned`

* Obiekt `userAssignedIdentities` podrzędny, który określa zasób i nazwę przypisaną przez użytkownika

Ten przykład przedstawia definicję zasobu aplikacji logiki dla żądania HTTP PUT i zawiera obiekt nies `identity` sparametryzowane. Odpowiedź na żądanie PUT i kolejnej operacji GET również mają ten `identity` obiekt:

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

Jeśli szablon zawiera również definicję zasobu tożsamości zarządzanej, możesz sparametryzować `identity` obiekt . W tym przykładzie pokazano, jak obiekt podrzędny odwołuje się do zmiennej, która jest zdefiniowana `userAssignedIdentities` `userAssignedIdentity` w sekcji `variables` szablonu. Ta zmienna odwołuje się do identyfikatora zasobu tożsamości przypisanej przez użytkownika.

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

## <a name="give-identity-access-to-resources"></a>Nadaj tożsamości dostęp do zasobów

Zanim będzie można użyć tożsamości zarządzanej aplikacji logiki do uwierzytelniania, skonfiguruj dostęp dla tej tożsamości w zasobie platformy Azure, w którym planujesz używać tożsamości. Aby wykonać to zadanie, przypisz odpowiednią rolę do tej tożsamości w docelowym zasobie platformy Azure. Oto opcje, których można użyć:

* [Witryna Azure Portal](#azure-portal-assign-access)
* [Szablon usługi Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) — aby uzyskać więcej informacji, zobacz Dodawanie przypisania roli przy użyciu kontroli [RBAC](../role-based-access-control/role-assignments-powershell.md)platformy Azure i Azure PowerShell .
* Interfejs wiersza polecenia platformy Azure[(az role assignment create)](/cli/azure/role/assignment#az_role_assignment_create)— aby uzyskać więcej informacji, zobacz Dodawanie przypisania roli przy użyciu kontroli RBAC platformy [Azure i interfejsu wiersza polecenia platformy Azure.](../role-based-access-control/role-assignments-cli.md)
* [Interfejs API REST platformy Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Przypisywanie dostępu w Azure Portal

W docelowym zasobie platformy Azure, do którego tożsamość zarządzana ma mieć dostęp, nadaj tej tożsamości dostęp oparty na rolach do zasobu docelowego.

1. W [witrynie Azure Portal](https://portal.azure.com)przejdź do zasobu platformy Azure, do którego chcesz, aby tożsamość zarządzana miał dostęp.

1. W menu zasobu wybierz pozycję Przypisania ról kontroli dostępu **(IAM),** w której możesz przejrzeć bieżące przypisania  >   ról dla tego zasobu. Na pasku narzędzi wybierz pozycję **Dodaj**  >  **dodaj przypisanie roli.**

   ![Wybierz pozycję "Dodaj" > "Dodaj przypisanie roli"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Jeśli opcja **Dodaj przypisanie roli** jest wyłączona, najprawdopodobniej nie masz uprawnień. Aby uzyskać więcej informacji na temat uprawnień, które umożliwiają zarządzanie rolami zasobów, zobacz [Uprawnienia roli administratora](../active-directory/roles/permissions-reference.md)w u Azure Active Directory .

1. W **obszarze Dodaj przypisanie roli** wybierz **rolę,** która zapewnia tożsamości niezbędny dostęp do zasobu docelowego.

   Na potrzeby przykładu w tym temacie tożsamość musi mieć rolę, która może uzyskać dostęp do obiektu blob w kontenerze usługi [Azure Storage,](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)dlatego wybierz rolę Współautor danych obiektu blob usługi **Storage** dla tożsamości zarządzanej.

   ![Wybieranie roli "Współautor danych obiektu blob magazynu"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Wykonaj następujące kroki dla tożsamości zarządzanej:

   * **Tożsamość przypisana przez system**

     1. W polu **Przypisz dostęp do** wybierz pozycję **Aplikacja logiki.** Gdy zostanie **wyświetlona** właściwość Subskrypcja, wybierz subskrypcję platformy Azure skojarzoną z Twoją tożsamością.

        ![Wybieranie dostępu dla tożsamości przypisanej przez system](./media/create-managed-service-identity/assign-access-system.png)

     1. W polu **Wybierz** wybierz aplikację logiki z listy. Jeśli lista jest zbyt długa, użyj pola **Wybierz,** aby filtrować listę.

        ![Wybieranie aplikacji logiki dla tożsamości przypisanej przez system](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Tożsamość przypisana przez użytkownika**

     1. W polu **Przypisz dostęp do** wybierz pozycję **Tożsamość zarządzana przypisana przez użytkownika.** Gdy zostanie **wyświetlona** właściwość Subskrypcja, wybierz subskrypcję platformy Azure skojarzoną z Twoją tożsamością.

        ![Wybieranie dostępu dla tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/assign-access-user.png)

     1. W polu **Wybierz** wybierz swoją tożsamość z listy. Jeśli lista jest zbyt długa, użyj pola **Wybierz,** aby filtrować listę.

        ![Wybieranie tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

   Lista przypisań ról zasobu docelowego zawiera teraz wybraną tożsamość zarządzaną i rolę. W tym przykładzie pokazano, jak można użyć tożsamości przypisanej przez system dla jednej aplikacji logiki i tożsamości przypisanej przez użytkownika dla grupy innych aplikacji logiki.

   ![Dodano tożsamości zarządzane i role do zasobu docelowego](./media/create-managed-service-identity/added-roles-for-identities.png)

   Aby uzyskać więcej informacji, [przypisz dostęp tożsamości zarządzanej do zasobu](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)przy użyciu Azure Portal .

1. Teraz wykonaj kroki [uwierzytelniania dostępu przy użyciu tożsamości w](#authenticate-access-with-identity) wyzwalaczu lub akcji, która obsługuje tożsamości zarządzane.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Uwierzytelnianie dostępu za pomocą tożsamości zarządzanej

Po włączeniu tożsamości zarządzanej dla aplikacji [logiki](#azure-portal-system-logic-app) i nadasz tej tożsamości dostęp do docelowego zasobu lub jednostki [,](#access-other-resources)możesz użyć tej tożsamości w wyzwalaczach i akcjach, które obsługują [tożsamości zarządzane.](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

> [!IMPORTANT]
> Jeśli masz funkcję platformy Azure, w której chcesz używać tożsamości przypisanej przez system, najpierw włącz [uwierzytelnianie dla usługi Azure Functions.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions)

Te kroki pokazują, jak używać tożsamości zarządzanej z wyzwalaczem lub akcją za pośrednictwem Azure Portal. Aby określić tożsamość zarządzaną w podstawowej definicji JSON wyzwalacza lub akcji, zobacz [Managed identity authentication (Uwierzytelnianie tożsamości zarządzanej).](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)

1. W [Azure Portal](https://portal.azure.com)otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Jeśli jeszcze tego nie zrobiono, dodaj wyzwalacz lub akcję, [która obsługuje tożsamości zarządzane.](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

   > [!NOTE]
   > Nie wszystkie wyzwalacze i akcje obsługują dodawanie typu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Typy uwierzytelniania dla wyzwalaczy i akcji, które obsługują uwierzytelnianie.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

1. W dodanym wyzwalaczu lub akcji wykonaj następujące kroki:

   * **Wbudowane wyzwalacze i akcje, które obsługują korzystanie z tożsamości zarządzanej**

     1. Dodaj właściwość **Authentication** (Uwierzytelnianie), jeśli właściwość nie jest jeszcze wyświetlana.

     1. W **obszarze Typ uwierzytelniania** wybierz pozycję **Tożsamość zarządzana.**

     Aby uzyskać więcej informacji, zobacz [Przykład: Uwierzytelnianie wbudowanego](#authenticate-built-in-managed-identity)wyzwalacza lub akcji przy użyciu tożsamości zarządzanej.
 
   * **Wyzwalacze i akcje łącznika zarządzanego, które obsługują korzystanie z tożsamości zarządzanej**

     1. Na stronie wyboru dzierżawy wybierz pozycję **Połącz przy użyciu tożsamości zarządzanej.**

     1. Na następnej stronie podaj nazwę połączenia.

        Domyślnie na liście tożsamości zarządzanych jest wyświetlona tylko aktualnie włączona tożsamość zarządzana, ponieważ aplikacja logiki obsługuje włączanie tylko jednej tożsamości zarządzanej na raz, na przykład:

        ![Zrzut ekranu przedstawiający stronę nazwy połączenia i wybraną tożsamość zarządzaną.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     Aby uzyskać więcej informacji, zobacz [Przykład: Uwierzytelnianie wyzwalacza łącznika zarządzanego lub akcji przy użyciu tożsamości zarządzanej.](#authenticate-managed-connector-managed-identity)

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Przykład: Uwierzytelnianie wbudowanego wyzwalacza lub akcji za pomocą tożsamości zarządzanej

Wyzwalacz LUB akcja HTTP może używać tożsamości przypisanej przez system, która została włączona dla aplikacji logiki. Ogólnie rzecz biorąc, wyzwalacz LUB akcja HTTP używa tych właściwości do określenia zasobu lub jednostki, do której chcesz uzyskać dostęp:

| Właściwość | Wymagane | Opis |
|----------|----------|-------------|
| **Metoda** | Tak | Metoda HTTP używana przez operację, którą chcesz uruchomić |
| **URI** | Tak | Adres URL punktu końcowego do uzyskiwania dostępu do docelowego zasobu lub jednostki platformy Azure. Składnia identyfikatora URI zwykle zawiera [identyfikator zasobu](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) dla zasobu lub usługi platformy Azure. |
| **Nagłówki** | Nie | Wszystkie wartości nagłówka, które są potrzebne lub które mają zostać dołączyć do żądania wychodzącego, takie jak typ zawartości |
| **Zapytania** | Nie | Wszelkie parametry zapytania, które należy lub które chcesz uwzględnić w żądaniu, takie jak parametr dla określonej operacji lub wersja interfejsu API dla operacji, którą chcesz uruchomić |
| **Authentication** | Tak | Typ uwierzytelniania do uwierzytelniania dostępu do docelowego zasobu lub jednostki |
||||

W konkretnym przykładzie załóżmy, że chcesz uruchomić operację migawki [obiektu blob](/rest/api/storageservices/snapshot-blob) na obiekcie blob na koncie usługi Azure Storage, na którym wcześniej ustawiono dostęp dla tożsamości. Jednak łącznik [Azure Blob Storage](/connectors/azureblob/) obecnie nie oferuje tej operacji. Zamiast tego można uruchomić tę operację przy użyciu akcji [HTTP lub](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) innej operacji interfejs API REST usługi [blob](/rest/api/storageservices/operations-on-blobs).

> [!IMPORTANT]
> Aby uzyskać dostęp do kont usługi Azure Storage za zaporami przy użyciu żądań HTTP i tożsamości zarządzanych, upewnij się, że konto magazynu jest również ustawione z wyjątkiem zezwalania na dostęp przez zaufane [usługi firmy Microsoft](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

Aby uruchomić [operację migawki obiektu blob,](/rest/api/storageservices/snapshot-blob)akcja HTTP określa następujące właściwości:

| Właściwość | Wymagany | Przykładowa wartość | Opis |
|----------|----------|---------------|-------------|
| **Metoda** | Tak | `PUT`| Metoda HTTP używana przez operację snapshot blob |
| **URI** | Tak | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Identyfikator zasobu dla pliku Azure Blob Storage w globalnym (publicznym) środowisku platformy Azure, w którym jest używana ta składnia |
| **Nagłówki** | W przypadku usługi Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | Wartości `x-ms-blob-type` `x-ms-version` nagłówka , i `x-ms-date` są wymagane dla operacji usługi Azure Storage. <p><p>**Ważne:** W wychodzących żądaniach wyzwalacza HTTP i akcji dla usługi Azure Storage nagłówek wymaga właściwości i wersji interfejsu API dla operacji, `x-ms-version` którą chcesz uruchomić. Musi `x-ms-date` to być bieżąca data. W przeciwnym razie aplikacja logiki zakończy się niepowodzeniem z `403 FORBIDDEN` błędem. Aby uzyskać bieżącą datę w wymaganym formacie, możesz użyć wyrażenia w przykładowej wartości. <p>Więcej informacji można znaleźć w następujących tematach: <p><p>- [Nagłówki żądań — obiekt blob migawki](/rest/api/storageservices/snapshot-blob#request) <br>- [Przechowywanie wersji dla usług Azure Storage](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Zapytania** | Tylko dla operacji migawki obiektu blob | `comp` = `snapshot` | Nazwa i wartość parametru zapytania dla operacji. |
|||||

Oto przykładowa akcja HTTP, która pokazuje wszystkie te wartości właściwości:

![Dodawanie akcji HTTP w celu uzyskania dostępu do zasobu platformy Azure](./media/create-managed-service-identity/http-action-example.png)

1. Po dodaniu akcji HTTP dodaj właściwość **Authentication** (Uwierzytelnianie) do akcji HTTP. Z listy **Dodaj nowy parametr** wybierz pozycję **Uwierzytelnianie.**

   ![Dodawanie właściwości "Authentication" (Uwierzytelnianie) do akcji HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nie wszystkie wyzwalacze i akcje obsługują dodawanie typu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Typy uwierzytelniania dla wyzwalaczy i akcji, które obsługują uwierzytelnianie.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

1. Z listy **Typ uwierzytelniania** wybierz pozycję **Tożsamość zarządzana.**

   ![W przypadku opcji "Uwierzytelnianie" wybierz pozycję "Tożsamość zarządzana"](./media/create-managed-service-identity/select-managed-identity.png)

1. Z listy tożsamości zarządzanych wybierz jedną z dostępnych opcji w zależności od scenariusza.

   * W przypadku skonfigurowania tożsamości przypisanej przez system wybierz pozycję **Tożsamość** zarządzana przypisana przez system, jeśli nie została jeszcze wybrana.

     ![Wybierz pozycję "Tożsamość zarządzana przypisana przez system"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Jeśli skonfigurujesz tożsamość przypisaną przez użytkownika, wybierz tę tożsamość, jeśli nie została jeszcze wybrana.

     ![Wybieranie tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   W tym przykładzie jest kontynuowana tożsamość **zarządzana przypisana przez system**.

1. W przypadku niektórych wyzwalaczy i akcji wyświetlana jest również właściwość **Odbiorców,** która umożliwia ustawienie identyfikatora zasobu docelowego. Ustaw właściwość **Audience** (Odbiorcy) na [identyfikator zasobu docelowego lub usługi](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). W przeciwnym razie domyślnie właściwość **Odbiorców** używa identyfikatora zasobu, który `https://management.azure.com/` jest identyfikatorem zasobu dla Azure Resource Manager.

   > [!IMPORTANT]
   > Upewnij się, że  identyfikator zasobu docelowego jest dokładnie taki sam jak oczekiwana wartość Azure Active Directory (AD), w tym wszystkie wymagane końcowe ukośniki. Na przykład identyfikator zasobu dla wszystkich kont Azure Blob Storage wymaga końcowego ukośnika. Jednak identyfikator zasobu dla określonego konta magazynu nie wymaga końcowego ukośnika. Sprawdź [identyfikatory zasobów dla usług platformy Azure, które obsługują usługę Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

   W tym przykładzie **właściwość Odbiorców** jest ustawiana tak, aby tokeny dostępu używane do uwierzytelniania są prawidłowe dla wszystkich `https://storage.azure.com/` kont magazynu. Można jednak również określić adres URL usługi głównej, `https://fabrikamstorageaccount.blob.core.windows.net` , dla określonego konta magazynu.

   ![Ustaw właściwość "Audience" na docelowy identyfikator zasobu](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Aby uzyskać więcej informacji na temat autoryzowania dostępu za pomocą usługi Azure AD dla usługi Azure Storage, zobacz następujące tematy:

   * [Autoryzowanie dostępu do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autoryzowanie dostępu do usługi Azure Storage za pomocą Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Kontynuuj tworzenie aplikacji logiki w sposób, jaki chcesz.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Przykład: Uwierzytelnianie wyzwalacza lub akcji łącznika zarządzanego przy użyciu tożsamości zarządzanej

Akcja Azure Resource Manager, Odczyt **zasobu,** może używać tożsamości zarządzanej, która została włączona dla aplikacji logiki. W tym przykładzie pokazano, jak używać tożsamości zarządzanej przypisanej przez system.

1. Po dodaniu akcji do przepływu pracy na stronie wyboru dzierżawy wybierz pozycję **Połącz przy użyciu tożsamości zarządzanej.**

   ![Zrzut ekranu przedstawiający Azure Resource Manager i wybraną akcję "Połącz przy użyciu tożsamości zarządzanej".](./media/create-managed-service-identity/select-connect-managed-identity.png)

   Akcja teraz wyświetla stronę nazwy połączenia z listą tożsamości zarządzanych, która zawiera typ tożsamości zarządzanej, który jest obecnie włączony w aplikacji logiki.

1. Na stronie nazwy połączenia podaj nazwę połączenia. Z listy tożsamości zarządzanych wybierz tożsamość  zarządzaną, w tym przykładzie tożsamość zarządzaną przypisaną przez system, a następnie wybierz pozycję **Utwórz**. Jeśli włączono tożsamość zarządzaną przypisaną przez użytkownika, wybierz tę tożsamość.

   ![Zrzut ekranu przedstawiający Azure Resource Manager z wprowadzono nazwę połączenia i wybraną akcję "Tożsamość zarządzana przypisana przez system".](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   Jeśli tożsamość zarządzana nie jest włączona, podczas próby utworzenia połączenia jest wyświetlany następujący błąd:

   *Należy włączyć tożsamość zarządzaną dla aplikacji logiki, a następnie udzielić wymaganego dostępu do tożsamości w zasobie docelowym.*

   ![Zrzut ekranu przedstawiający Azure Resource Manager z błędem, gdy tożsamość zarządzana nie jest włączona.](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. Po pomyślnym utworzeniu połączenia projektant może pobrać dowolne wartości dynamiczne, zawartość lub schemat przy użyciu uwierzytelniania tożsamości zarządzanej.

1. Kontynuuj tworzenie aplikacji logiki w sposób, jaki chcesz.

<a name="logic-app-resource-definition-connection-managed-identity"></a>

### <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity"></a>Definicja zasobu aplikacji logiki i połączenia, które używają tożsamości zarządzanej

Połączenie, które włącza tożsamość zarządzaną i używa jej, to specjalny typ połączenia, który działa tylko z tożsamością zarządzaną. W czasie wykonywania połączenie używa tożsamości zarządzanej włączonej w aplikacji logiki. Ta konfiguracja jest zapisywana w obiekcie definicji zasobu aplikacji logiki, który zawiera obiekt zawierający wskaźniki do identyfikatora zasobu połączenia wraz z identyfikatorem zasobu tożsamości, jeśli tożsamość przypisana przez użytkownika jest `parameters` `$connections` włączona.

W tym przykładzie pokazano, jak wygląda konfiguracja, gdy aplikacja logiki włącza tożsamość zarządzaną przypisaną przez system:

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

W tym przykładzie pokazano, jak wygląda konfiguracja, gdy aplikacja logiki włącza tożsamość zarządzaną przypisaną przez użytkownika:

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

W czasie wykonywania usługa Logic Apps sprawdza, czy dowolny wyzwalacz łącznika zarządzanego i akcje w aplikacji logiki zostały ustawione do używania tożsamości zarządzanej oraz czy wszystkie wymagane uprawnienia są kon setne do używania tożsamości zarządzanej na potrzeby uzyskiwania dostępu do zasobów docelowych określonych przez wyzwalacz i akcje. W przypadku powodzenia usługa Logic Apps pobiera token usługi Azure AD skojarzony z tożsamością zarządzaną i używa tej tożsamości do uwierzytelniania dostępu do zasobu docelowego i wykonywania skonfigurowanej operacji w wyzwalaczu i akcjach.

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities"></a>Szablon usługi ARM dla połączeń zarządzanych i tożsamości zarządzanych

Jeśli automatyzowanie wdrażania przy użyciu szablonu usługi ARM, a aplikacja logiki zawiera wyzwalacz łącznika zarządzanego lub akcję, która używa tożsamości zarządzanej, upewnij się, że podstawowa definicja zasobu połączenia zawiera właściwość z elementem jako `parameterValueType` `Alternative` wartością właściwości. W przeciwnym razie wdrożenie usługi ARM nie skonfiguruje połączenia do używania tożsamości zarządzanej do uwierzytelniania, a połączenie nie będzie działać w przepływie pracy aplikacji logiki. To wymaganie dotyczy tylko określonych wyzwalaczy i akcji łącznika [zarządzanego,](#managed-connectors-managed-identity) w przypadku których wybrano opcję [ **Połącz z tożsamością zarządzaną.**](#authenticate-managed-connector-managed-identity)

Na przykład poniżej znajduje się podstawowa definicja zasobu połączenia dla akcji Azure Automation, która używa tożsamości zarządzanej, w której definicja zawiera właściwość , która jest ustawiona na wartość `parameterValueType` `Alternative` właściwości:

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

## <a name="disable-managed-identity"></a>Wyłączanie tożsamości zarządzanej

Aby zatrzymać korzystanie z tożsamości zarządzanej dla aplikacji logiki, dostępne są następujące opcje:

* [Witryna Azure Portal](#azure-portal-disable)
* [Szablony usługi Azure Resource Manager](#template-disable)
* Azure PowerShell
  * [Usuwanie przypisania roli](../role-based-access-control/role-assignments-powershell.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Interfejs wiersza polecenia platformy Azure
  * [Usuwanie przypisania roli](../role-based-access-control/role-assignments-cli.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Interfejs API REST platformy Azure
  * [Usuwanie przypisania roli](../role-based-access-control/role-assignments-rest.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Jeśli usuniesz aplikację logiki, platforma Azure automatycznie usunie tożsamość zarządzaną z usługi Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Wyłączanie tożsamości zarządzanej w Azure Portal

W Azure Portal najpierw usuń dostęp tożsamości do [zasobu docelowego](#disable-identity-target-resource). Następnie wyłącz tożsamość przypisaną przez system lub usuń tożsamość przypisaną przez użytkownika [z aplikacji logiki.](#disable-identity-logic-app)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Usuwanie dostępu tożsamości z zasobów

1. W [Azure Portal](https://portal.azure.com)przejdź do docelowego zasobu platformy Azure, w którym chcesz usunąć dostęp do tożsamości zarządzanej.

1. Z menu zasobu docelowego wybierz pozycję **Kontrola dostępu (IAM)**. Na pasku narzędzi wybierz pozycję **Przypisania ról.**

1. Na liście ról wybierz tożsamości zarządzane, które chcesz usunąć. Na pasku narzędzi wybierz pozycję **Usuń**.

   > [!TIP]
   > Jeśli opcja **Usuń** jest wyłączona, najprawdopodobniej nie masz uprawnień. Aby uzyskać więcej informacji na temat uprawnień, które umożliwiają zarządzanie rolami zasobów, zobacz Uprawnienia ról administratorów w [programie Azure Active Directory](../active-directory/roles/permissions-reference.md).

Tożsamość zarządzana jest teraz usuwana i nie ma już dostępu do zasobu docelowego.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Wyłączanie tożsamości zarządzanej w aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia** wybierz pozycję **Tożsamość,** a następnie wykonaj kroki dla swojej tożsamości:

   * Wybierz **pozycję Przypisano system przy**  >    >  **zapisywanych plikach**. Gdy platforma Azure wyświetli monit o potwierdzenie, wybierz pozycję **Tak.**

     ![Wyłączanie tożsamości przypisanej przez system](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Wybierz **pozycję Przypisano użytkownika** i tożsamość zarządzaną, a następnie wybierz pozycję **Usuń**. Gdy platforma Azure wyświetli monit o potwierdzenie, wybierz pozycję **Tak.**

     ![Usuwanie tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Tożsamość zarządzana jest teraz wyłączona w aplikacji logiki.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Wyłączanie tożsamości zarządzanej w Azure Resource Manager szablonu

Jeśli tożsamość zarządzana aplikacji logiki została utworzona przy użyciu szablonu Azure Resource Manager, ustaw właściwość `identity` podrzędną `type` obiektu na wartość `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Następne kroki

* [Bezpieczny dostęp i dane w Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
