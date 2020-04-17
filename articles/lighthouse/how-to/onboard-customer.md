---
title: Dołączanie klienta do zarządzania zasobami delegowanymi na platformie Azure
description: Dowiedz się, jak przywdzielić klienta do zarządzania zasobami delegowanymi platformy Azure, umożliwiając dostęp do jego zasobów i zarządzanie nimi za pośrednictwem własnej dzierżawy.
ms.date: 04/16/2020
ms.topic: conceptual
ms.openlocfilehash: 22c96d43f3d5f284c2cba995eb33f5f8cd238659
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481689"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Dołączanie klienta do zarządzania zasobami delegowanymi na platformie Azure

W tym artykule wyjaśniono, jak jako dostawca usług możesz dołączać klienta do zarządzania zasobami delegowanymi platformy Azure, umożliwiając dostęp do delegowanych zasobów (subskrypcji i/lub grup zasobów) i zarządzanie nimi za pośrednictwem własnej dzierżawy usługi Azure Active Directory (Azure AD). W tym miejscu będziemy odnosić się do dostawców usług i klientów, [przedsiębiorstwa zarządzające wieloma dzierżawcami](../concepts/enterprise.md) mogą używać tego samego procesu do konsolidacji środowiska zarządzania.

Możesz powtórzyć ten proces, jeśli zarządzasz zasobami dla wielu klientów. Następnie, gdy autoryzowany użytkownik loguje się do dzierżawy, ten użytkownik może być autoryzowany w zakresach dzierżawy klienta do wykonywania operacji zarządzania bez konieczności logowania się do każdej dzierżawy klienta.

Aby śledzić wpływ na zaangażowanie klientów i otrzymywać uznanie, skojarz identyfikator Microsoft Partner Network (MPN) z co najmniej jednym kontem użytkownika, które ma dostęp do każdej z wbudowanych subskrypcji. Należy zauważyć, że należy wykonać to skojarzenie w dzierżawie dostawcy usług. Dla uproszczenia zaleca się utworzenie konta jednostki usługi w dzierżawie, która jest skojarzona z identyfikatorem MPN i przyznanie mu dostępu do czytnika dla każdego klienta, którego jesteś na pokładzie. Aby uzyskać więcej informacji, zobacz [Łączenie identyfikatora partnera z kontami platformy Azure.](../../billing/billing-partner-admin-link-started.md) 

> [!NOTE]
> Klienci mogą być również dołączani podczas zakupu oferty usług zarządzanych (publicznych lub prywatnych), która została opublikowana w portalu Azure Marketplace. Aby uzyskać więcej informacji, zobacz [Publikowanie ofert usług zarządzanych w portalu Azure Marketplace](publish-managed-services-offers.md). Można również użyć procesu dołączania opisanego w tym miejscu wraz z ofertą opublikowaną w portalu Azure Marketplace.

Proces dołączania wymaga akcji, które mają być podejmowane zarówno z wewnątrz dzierżawy dostawcy usług, jak i od dzierżawy klienta. Wszystkie te kroki są opisane w tym artykule.

## <a name="gather-tenant-and-subscription-details"></a>Zbieranie informacji o dzierżawie i subskrypcji

Aby mieć wbudowaną dzierżawę klienta, musi mieć aktywną subskrypcję platformy Azure. Musisz znać następujące kwestie:

- Identyfikator dzierżawy dzierżawy usługodawcy (gdzie będziesz zarządzać zasobami klienta)
- Identyfikator dzierżawy dzierżawy klienta (który będzie miał zasoby zarządzane przez dostawcę usług)
- Identyfikatory subskrypcji dla każdej określonej subskrypcji w dzierżawie klienta, które będą zarządzane przez dostawcę usług (lub który zawiera grupy zasobów, które będą zarządzane przez dostawcę usług).

> [!NOTE]
> Nawet jeśli chcesz mieć dostęp tylko do jednej lub więcej grup zasobów w ramach subskrypcji, wdrożenie musi być wykonane na poziomie subskrypcji, więc będziesz potrzebować identyfikatora subskrypcji.

Jeśli nie masz już tych wartości identyfikatora, możesz je pobrać w jeden z następujących sposobów. Upewnij się i użyj tych dokładnych wartości we wdrożeniu.

### <a name="azure-portal"></a>Azure Portal

Identyfikator dzierżawy można zobaczyć, najeżdżając kursorem na nazwę konta w prawym górnym rogu witryny Azure portal lub wybierając **pozycję Przełącz katalog**. Aby wybrać i skopiować identyfikator **dzierżawy,** wyszukaj "Usługa Azure Active Directory" z poziomu portalu, a następnie wybierz **pozycję Właściwości** i skopiuj wartość wyświetlaną w polu Identyfikator katalogu. Aby znaleźć identyfikator subskrypcji w dzierżawie klienta, wyszukaj hasło "Subskrypcje", a następnie wybierz odpowiedni identyfikator subskrypcji.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Podczas dołączania subskrypcji (lub co najmniej jednej grupy zasobów w ramach subskrypcji) przy użyciu procesu opisanego w tym miejscu, dostawca zasobów **Microsoft.ManagedServices** zostanie zarejestrowany dla tej subskrypcji.

## <a name="define-roles-and-permissions"></a>Definiowanie ról i uprawnień

Jako dostawca usług można wykonać wiele zadań dla jednego klienta, wymagające innego dostępu dla różnych zakresów. Można zdefiniować dowolną liczbę autoryzacji, ile potrzeba do przypisania [wbudowanych ról kontroli dostępu opartej na rolach (RBAC)](../../role-based-access-control/built-in-roles.md) do użytkowników w dzierżawie.

Aby ułatwić zarządzanie, zaleca się używanie grup użytkowników usługi Azure AD dla każdej roli, co pozwala na dodawanie lub usuwanie poszczególnych użytkowników do grupy, zamiast przypisywania uprawnień bezpośrednio do tego użytkownika. Można również przypisać role do jednostki usługi. Pamiętaj, aby przestrzegać zasady najmniejszych uprawnień, aby użytkownicy mieli tylko uprawnienia potrzebne do ukończenia zadania. Aby uzyskać zalecenia i informacje na temat obsługiwanych ról, zobacz [Dzierżawcy, użytkownicy i role w scenariuszach latarni morskiej platformy Azure.](../concepts/tenants-users-roles.md)

> [!IMPORTANT]
> Aby dodać uprawnienia dla grupy usługi Azure AD, **typem grupy** musi być **security,** a nie **Office 365**. Ta opcja jest zaznaczona podczas tworzenia grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Aby zdefiniować autoryzacje, musisz znać wartości identyfikatorów dla każdego użytkownika, grupy użytkowników lub jednostki usługi w dzierżawie dostawcy usług, do której chcesz udzielić dostępu. Identyfikator definicji roli będzie również potrzebny dla każdej wbudowanej roli, którą chcesz przypisać. Jeśli nie masz ich jeszcze, można je pobrać, uruchamiając poniższe polecenia z poziomu dzierżawy dostawcy usług.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Zaleca się przypisanie [roli usuwania przypisania przydziału rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) podczas dołączania do klienta, aby użytkownicy w dzierżawie mogli w razie potrzeby [usunąć dostęp do delegacji.](#remove-access-to-a-delegation) Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

## <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager

Aby mieć klienta na pokładzie, musisz utworzyć szablon [usługi Azure Resource Manager](../../azure-resource-manager/index.yml) dla oferty z następującymi informacjami. Wartości **mspOfferName** i **mspOfferDescription** będą widoczne dla klienta podczas wyświetlania szczegółów oferty na [stronie Dostawcy usług](view-manage-service-providers.md) w witrynie Azure portal.

|Pole  |Definicja  |
|---------|---------|
|**nazwa mspOffer**     |Nazwa opisująca tę definicję. Ta wartość jest wyświetlana klientowi jako tytuł oferty.         |
|**mspOfferDescription (Opis)**     |Krótki opis oferty (na przykład "Oferta zarządzania maszynami wirtualnymi Contoso").      |
|**managedByTenantId**     |Identyfikator dzierżawy.          |
|**Zezwoleń**     |PrincipalId wartości dla użytkowników/grup/SPN z dzierżawy, każdy z **principalIdDisplayName,** aby pomóc klientowi zrozumieć cel autoryzacji i mapowane do wbudowanej **wartości roleDefinitionId,** aby określić poziom dostępu. **principalId**      |

Proces dołączania wymaga szablonu usługi Azure Resource Manager (dostępnego w naszym [przykładowym repozytorium)](https://github.com/Azure/Azure-Lighthouse-samples/)i odpowiedniego pliku parametrów, który można zmodyfikować, aby dopasować konfigurację i zdefiniować autoryzacje.

Szablon, który wybierzesz, zależy od tego, czy dołączasz całą subskrypcję, grupę zasobów, czy wiele grup zasobów w ramach subskrypcji. Udostępniamy również szablon, który może służyć klientom, którzy zakupili ofertę usługi zarządzanej opublikowaną w portalu Azure Marketplace, jeśli wolisz w ten sposób dołączać ich subskrypcje.

|Aby włączyć tę  |Użyj tego szablonu usługi Azure Resource Manager  |I zmodyfikuj ten plik parametru |
|---------|---------|---------|
|Subskrypcja   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Grupa zasobów   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Wiele grup zasobów w ramach subskrypcji   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Subskrypcja (przy użyciu oferty opublikowanej w portalu Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Proces opisany w tym miejscu wymaga wdrożenia na poziomie oddzielnej subskrypcji dla każdej subskrypcji jest dołączany, nawet jeśli są dołączanie subskrypcji w tej samej dzierżawy klienta. Oddzielne wdrożenia są również wymagane, jeśli są dołączanie wielu grup zasobów w ramach różnych subskrypcji w tej samej dzierżawy klienta. Jednak dołączanie wielu grup zasobów w ramach jednej subskrypcji można wykonać w jednym wdrożeniu na poziomie subskrypcji.
>
> Oddzielne wdrożenia są również wymagane dla wielu ofert stosowanych do tej samej subskrypcji (lub grup zasobów w ramach subskrypcji). Każda zastosowana oferta musi używać innego **mspOfferName**.

W poniższym przykładzie przedstawiono zmodyfikowany **plik delegatedResourceManagement.parameters.json,** który może służyć do dołączania subskrypcji. Pliki parametrów grupy zasobów (znajdujące się w folderze [zarządzania zasobami delegowanymi rg)](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) są podobne, ale zawierają również parametr **rgName** w celu zidentyfikowania określonych grup zasobów, które mają być wbudowane.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

Ostatnia autoryzacja w powyższym przykładzie dodaje **identyfikator główny** z rolą administratora dostępu użytkownika (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Podczas przypisywania tej roli należy dołączyć właściwość **deledRoleDefinitionIds** i jedną lub więcej wbudowanych ról. Użytkownik utworzony w tej autoryzacji będzie mógł przypisać te wbudowane role do [zarządzanych tożsamości](../../active-directory/managed-identities-azure-resources/overview.md) w dzierżawie klienta, co jest wymagane w celu [wdrożenia zasad, które można naprawić.](deploy-policy-remediation.md) Żadne inne uprawnienia zwykle skojarzone z rolą Administratora dostępu użytkownika nie będą miały zastosowania do tego użytkownika.

## <a name="deploy-the-azure-resource-manager-templates"></a>Wdrażanie szablonów usługi Azure Resource Manager

Po zaktualizowaniu pliku parametrów użytkownik w dzierżawie klienta musi wdrożyć szablon usługi Azure Resource Manager w ramach dzierżawy jako wdrożenie na poziomie subskrypcji. Oddzielne wdrożenie jest potrzebne dla każdej subskrypcji, która ma być owana na platformie Azure do zarządzania zasobami delegowanymi platformy Azure (lub dla każdej subskrypcji zawierającej grupy zasobów, które mają być dołączane).

Ponieważ jest to wdrożenie na poziomie subskrypcji, nie można go zainicjować w witrynie Azure portal. Wdrożenie można wykonać przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, jak pokazano poniżej.

> [!IMPORTANT]
> To wdrożenie na poziomie subskrypcji musi być wykonywane przez konto niebędące gośćm w dzierżawie klienta, który ma [wbudowaną rolę Właściciela](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) dla wbudowanej subskrypcji (lub która zawiera grupy zasobów, które są dołączane). Aby wyświetlić wszystkich użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie klienta może wybrać subskrypcję w portalu Azure, otworzyć **formant dostępu (IAM)** i [wyświetlić wszystkich użytkowników z rolą Właściciel](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Potwierdzanie pomyślnego dołączania

Gdy subskrypcja klienta została pomyślnie przyporządkowana do zarządzania zasobami delegowanymi platformy Azure, użytkownicy w dzierżawie dostawcy usług będą mogli zobaczyć subskrypcję i jej zasoby (jeśli przyznano im dostęp do niej za pośrednictwem powyższego procesu, indywidualnie lub jako członek grupy usługi Azure AD z odpowiednimi uprawnieniami). Aby to potwierdzić, sprawdź, czy subskrypcja jest wyświetlana w jeden z następujących sposobów.  

### <a name="azure-portal"></a>Azure Portal

W dzierżawie usługodawcy:

1. Przejdź do [strony Moi klienci](view-manage-customers.md).
2. Wybierz **pozycję Klienci**.
3. Upewnij się, że możesz zobaczyć subskrypcje o nazwie oferty podanej w szablonie Menedżera zasobów.

> [!IMPORTANT]
> Aby wyświetlić delegowaną subskrypcję w [obszarze Moi klienci,](view-manage-customers.md)użytkownicy w dzierżawie dostawcy usług muszą otrzymać rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) (lub inną wbudowaną rolę, która obejmuje dostęp do czytnika), gdy subskrypcja była dołączana do zarządzania zasobami delegowanymi platformy Azure.

W dzierżawie klienta:

1. Przejdź do [strony Dostawcy usług](view-manage-service-providers.md).
2. Wybierz **ofertę usługodawcy**.
3. Upewnij się, że możesz zobaczyć subskrypcje o nazwie oferty podanej w szablonie Menedżera zasobów.

> [!NOTE]
> Może upłynąć kilka minut po zakończeniu wdrażania, zanim aktualizacje zostaną odzwierciedlone w witrynie Azure portal.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Usuwanie dostępu do delegacji

Domyślnie użytkownicy w dzierżawie klienta, którzy mają odpowiednie uprawnienia, mogą usunąć dostęp dostawcy usług do delegowanych zasobów na [stronie Dostawcy usług](view-manage-service-providers.md#add-or-remove-service-provider-offers) w witrynie Azure portal. Gdy to zrobią, żaden użytkownik w dzierżawie dostawcy usług nie będzie mógł uzyskać dostępu do zasobów, które zostały wcześniej delegowane.

Jeśli masz wbudowanych użytkowników z [zadaniami usuwania przydziału rejestracji usług zarządzanych](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) podczas dołączania klienta do zarządzania zasobami delegowanymi platformy Azure, ci użytkownicy będą również mogli usunąć delegowanie.

W poniższym przykładzie przedstawiono przydział przyznający **rolę usuwania przydziału rejestracji usług zarządzanych,** która może zostać uwzględniona w pliku parametru:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Użytkownik z tym uprawnieniem może usunąć delegowanie w jeden z następujących sposobów.

### <a name="azure-portal"></a>Azure Portal

1. Przejdź do [strony Moi klienci](view-manage-customers.md).
2. Wybierz **opcję Delegacje**.
3. Znajdź delegowanie, które chcesz usunąć, a następnie wybierz ikonę kosza, która pojawi się w jego wierszu.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- [Wyświetlanie klientów i zarządzanie nimi,](view-manage-customers.md) przechodząc do **witryny Moi klienci** w witrynie Azure portal.
