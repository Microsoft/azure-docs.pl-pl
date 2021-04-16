---
title: Dołączanie klienta do usługi Azure Lighthouse
description: Dowiedz się, jak dołączać klienta do zarządzania Azure Lighthouse, co umożliwia dostęp do jego zasobów i zarządzanie nimi za pośrednictwem własnej dzierżawy przy użyciu funkcji zarządzania delegowanymi zasobami platformy Azure.
ms.date: 03/29/2021
ms.topic: how-to
ms.openlocfilehash: d8ad448ac022b07ecdea6b68c4544b8c955814b1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497969"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Dołączanie klienta do usługi Azure Lighthouse

W tym artykule wyjaśniono, jak jako dostawca usług możesz do dołączać klienta do Azure Lighthouse. Gdy to zrobisz, delegowane zasoby (subskrypcje i/lub grupy zasobów) w dzierżawie usługi Azure Active Directory klienta (Azure AD) mogą być zarządzane za pośrednictwem własnej dzierżawy przy użyciu zarządzania delegowanymi zasobami platformy [Azure.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> Chociaż w tym temacie odnosimy się do dostawców usług i [klientów,](../concepts/enterprise.md) przedsiębiorstwa zarządzające wieloma dzierżawami mogą używać tego samego procesu do Azure Lighthouse i konsolidowania swoich funkcji zarządzania.

Proces dołączania można powtórzyć dla wielu klientów. Gdy użytkownik z odpowiednimi uprawnieniami zaloguje się do dzierżawy zarządzającej, może być autoryzowany w zakresach dzierżawy klienta do wykonywania operacji zarządzania bez konieczności logowania się do każdej dzierżawy klienta.

Aby śledzić wpływ na zaangażowanie klientów i odbierać rozpoznawanie, skojarz swój identyfikator Microsoft Partner Network (MPN) z co najmniej jednym kontem użytkownika, które ma dostęp do każdej z dołączanych subskrypcji. To skojarzenie należy wykonać w dzierżawie dostawcy usług. Zalecamy utworzenie w dzierżawie konta jednostki usługi skojarzonego z Twoim identyfikatorem MPN, a następnie dołączanie tej jednostki usługi za każdym razem, gdy dołączasz klienta. Aby uzyskać więcej informacji, zobacz [Łączenie identyfikatora partnera, aby włączyć środki uzyskane przez partnera dla delegowanych zasobów.](partner-earned-credit.md)

> [!NOTE]
> Klienci mogą być również dołączani do usługi Azure Lighthouse gdy kupują ofertę usługi zarządzanej (publiczną lub prywatną), która jest publikowana w [Azure Marketplace](publish-managed-services-offers.md). Możesz również użyć opisanego tutaj procesu dołączania wraz z ofertami opublikowanymi w Azure Marketplace.

Proces dołączania wymaga, aby akcje były podejmowane zarówno z dzierżawy dostawcy usług, jak i z dzierżawy klienta. Wszystkie te kroki zostały opisane w tym artykule.

## <a name="gather-tenant-and-subscription-details"></a>Zbieranie szczegółów dzierżawy i subskrypcji

Aby do dołączać dzierżawę klienta, musi ona mieć aktywną subskrypcję platformy Azure. Musisz znać następujące informacje:

- Identyfikator dzierżawy dostawcy usług (w którym będziesz zarządzać zasobami klienta)
- Identyfikator dzierżawy klienta (która będzie mieć zasoby zarządzane przez dostawcę usług)
- Identyfikatory subskrypcji dla każdej konkretnej subskrypcji w dzierżawie klienta, która będzie zarządzana przez dostawcę usług (lub która zawiera grupy zasobów, które będą zarządzane przez dostawcę usług).

Jeśli nie masz jeszcze tych wartości identyfikatorów, możesz je pobrać na jeden z następujących sposobów. Upewnij się, że używasz tych dokładnych wartości we wdrożeniu.

### <a name="azure-portal"></a>Azure Portal

Identyfikator dzierżawy można zobaczyć, umieszczając kursor na nazwie konta w prawym górnym rogu strony Azure Portal lub wybierając pozycję Przełącz **katalog**. Aby wybrać i skopiować identyfikator dzierżawy, wyszukaj "Azure Active Directory" w  portalu, a następnie wybierz pozycję Właściwości i skopiuj wartość wyświetlaną w **polu Identyfikator** katalogu. Aby znaleźć identyfikator subskrypcji w dzierżawie klienta, wyszukaj frazę „Subskrypcje”, a następnie wybierz odpowiedni identyfikator subskrypcji.

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
> W przypadku dołączania subskrypcji (lub co najmniej jednej grupy zasobów w ramach subskrypcji) przy użyciu procesu opisanego tutaj dostawca zasobów **Microsoft.ManagedServices** zostanie zarejestrowany dla tej subskrypcji.

## <a name="define-roles-and-permissions"></a>Definiowanie ról i uprawnień

Jako dostawca usług możesz chcieć wykonać wiele zadań dla jednego klienta, wymagając różnego dostępu dla różnych zakresów. Możesz zdefiniować tyle autoryzacji, ile potrzebujesz, aby przypisać odpowiednie wbudowane [role platformy Azure.](../../role-based-access-control/built-in-roles.md) Każda autoryzacja zawiera **identyfikator principalId,** który odwołuje się do użytkownika, grupy lub jednostki usługi usługi Azure AD w dzierżawie zarządzającej.

> [!NOTE]
> O ile nie zostanie to jawnie określone, odwołania do "użytkownika" w dokumentacji usługi Azure Lighthouse mogą być stosowane do użytkownika, grupy lub jednostki usługi usługi Azure AD w ramach autoryzacji.

Aby ułatwić zarządzanie, zalecamy używanie grup użytkowników usługi Azure AD dla każdej roli zawsze, gdy jest to możliwe, a nie dla poszczególnych użytkowników. Zapewnia to elastyczność dodawania lub usuwania poszczególnych użytkowników do grupy, która ma dostęp, dzięki czemu nie trzeba powtarzać procesu dołączania w celu zmiany użytkownika. Można również przypisać role do jednostki usługi, co może być przydatne w scenariuszach automatyzacji.

> [!IMPORTANT]
> Aby dodać uprawnienia dla grupy usługi Azure AD, dla ustawienia **Typ** grupy należy ustawić wartość **Zabezpieczenia.** Ta opcja jest wybierana podczas tworzenia grupy. Aby uzyskać więcej informacji, zobacz [Tworzenie podstawowej grupy i dodawanie członków w usłudze Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Podczas definiowania autoryzacji należy postępować zgodnie z zasadą najmniejszych uprawnień, aby użytkownicy mieli tylko uprawnienia wymagane do ukończenia zadania. Aby uzyskać informacje o obsługiwanych rolach i najlepszych rozwiązaniach, zobacz Dzierżawy, użytkownicy i role w [Azure Lighthouse scenariuszach.](../concepts/tenants-users-roles.md)

Aby zdefiniować autoryzacje, musisz znać wartości identyfikatorów dla każdego użytkownika, grupy użytkowników lub jednostki usługi w dzierżawie dostawcy usług, do której chcesz udzielić dostępu. Będziesz również potrzebować identyfikatora definicji roli dla każdej wbudowanej roli, którą chcesz przypisać. Jeśli nie masz ich jeszcze, możesz je pobrać, uruchamiając poniższe polecenia z poziomu dzierżawy dostawcy usług.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

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
> Zalecamy przypisanie [](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) roli usuwania przypisania rejestracji usług zarządzanych podczas dołączania klienta, [](remove-delegation.md) aby użytkownicy w dzierżawie mogą później usunąć dostęp do delegowania w razie potrzeby. Jeśli ta rola nie jest przypisana, delegowane zasoby mogą zostać usunięte tylko przez użytkownika w dzierżawie klienta.

## <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu Azure Resource Manager szablonu

Aby dołączyć klienta, należy utworzyć szablon usługi [Azure Resource Manager](../../azure-resource-manager/index.yml) dla oferty, zawierający następujące informacje. Wartości **mspOfferName** i **mspOfferDescription** będą widoczne dla klienta na stronie [Dostawcy](view-manage-service-providers.md) usług Azure Portal.

|Pole  |Definicja  |
|---------|---------|
|**mspOfferName**     |Nazwa opisująca tę definicję. Ta wartość jest wyświetlana klientowi jako tytuł oferty i musi być unikatową wartością.        |
|**mspOfferDescription**     |Krótki opis oferty (na przykład "Oferta zarządzania maszynami wirtualnymi firmy Contoso").      |
|**managedByTenantId**     |Identyfikator dzierżawy.          |
|**Zezwoleń**     |Wartości **principalId** dla użytkowników/grup/nazw SPN z dzierżawy, z których każda ma wartość **principalIdDisplayName,** która pomaga klientowi zrozumieć cel autoryzacji, i mapowana na wbudowaną wartość **roleDefinitionId** w celu określenia poziomu dostępu.      |

Proces dołączania wymaga szablonu Azure Resource Manager (dostarczonego w naszym przykładowym [repocie](https://github.com/Azure/Azure-Lighthouse-samples/)) i odpowiedniego pliku parametrów, który można zmodyfikować w celu dopasowania do konfiguracji i zdefiniowania autoryzacji.

> [!IMPORTANT]
> Opisany tutaj proces wymaga oddzielnego wdrożenia dla każdej dołączanych subskrypcji, nawet jeśli dołączasz subskrypcje w tej samej dzierżawie klienta. Oddzielne wdrożenia są również wymagane w przypadku dołączania wielu grup zasobów w ramach różnych subskrypcji w tej samej dzierżawie klienta. Jednak dołączanie wielu grup zasobów w ramach jednej subskrypcji można wykonać w jednym wdrożeniu.
>
> Oddzielne wdrożenia są również wymagane w przypadku wielu ofert stosowanych do tej samej subskrypcji (lub grup zasobów w ramach subskrypcji). Każda zastosowana oferta musi używać innego **parametru mspOfferName.**

Wybór szablonu będzie zależeć od tego, czy dołączasz całą subskrypcję, grupę zasobów, czy wiele grup zasobów w ramach subskrypcji. Oferujemy również szablon, który może być używany dla klientów, którzy kupili ofertę usługi zarządzanej opublikowaną w usłudze Azure Marketplace, jeśli wolisz dołączać ich subskrypcje w ten sposób.

|Aby doć to  |Korzystanie z tego Azure Resource Manager szablonu  |Zmodyfikuj ten plik parametrów |
|---------|---------|---------|
|Subskrypcja   |[delegatedResourceManagement.jswł.](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.jswł.](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Grupa zasobów   |[rgDelegatedResourceManagement.jswł.](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.jswł.](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Wiele grup zasobów w ramach subskrypcji   |[multipleRgDelegatedResourceManagement.jswł.](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.jswł.](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Subskrypcja (w przypadku korzystania z oferty opublikowanej w Azure Marketplace)   |[marketplaceDelegatedResourceManagement.jswł.](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.jswł.](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> Nie można do dołączać całej grupy zarządzania w jednym wdrożeniu, ale można wdrożyć zasady [na poziomie grupy zarządzania](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups). Zasady wykorzystują efekt [deployIfNotExists,](../../governance/policy/concepts/effects.md#deployifnotexists) aby sprawdzić, czy każda subskrypcja w grupie zarządzania została delegowana do określonej dzierżawy zarządzającej, a jeśli nie, utworzy przypisanie na podstawie podanych wartości. Następnie będziesz mieć dostęp do wszystkich subskrypcji w grupie zarządzania, mimo że musisz pracować z nimi jako poszczególnymi subskrypcjami (zamiast podejmowania działań na całej grupie zarządzania).

W poniższym przykładzie pokazanodelegatedResourceManagement.parameters.js **pliku,** który może służyć do dołączania subskrypcji. Pliki parametrów grupy zasobów (znajdujące się w folderze [rg-delegated-resource-management)](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) są podobne, ale zawierają również parametr **rgName** w celu zidentyfikowania określonych grup zasobów, które mają zostać dołączyć.

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

Ostatnia autoryzacja w powyższym przykładzie dodaje **identyfikator principalId** z rolą administratora dostępu użytkowników (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Podczas przypisywania tej roli należy uwzględnić właściwość **delegatedRoleDefinitionIds** i co najmniej jedną obsługiwaną wbudowaną rolę platformy Azure. Użytkownik utworzony w tej autoryzacji będzie mógł [](../../active-directory/managed-identities-azure-resources/overview.md) przypisać te role do tożsamości zarządzanych w dzierżawie klienta, co jest wymagane do wdrożenia zasad, które można [skorygować.](deploy-policy-remediation.md)  Użytkownik może również tworzyć zdarzenia pomocy technicznej. Żadne inne uprawnienia normalnie skojarzone z rolą administratora dostępu użytkowników nie będą mieć zastosowania do tego **principalId.**

## <a name="deploy-the-azure-resource-manager-templates"></a>Wdrażanie szablonów Azure Resource Manager szablonów

Po zaktualizowania pliku parametrów użytkownik w dzierżawie klienta musi wdrożyć szablon Azure Resource Manager w ramach swojej dzierżawy. Oddzielne wdrożenie jest wymagane dla każdej subskrypcji, która ma zostać do dosłanana (lub dla każdej subskrypcji zawierającej grupy zasobów, które chcesz do niej dodać).

> [!IMPORTANT]
> To wdrożenie musi być wykonywane przez konto inne niż gość w dzierżawie klienta, które ma rolę z uprawnieniami, taką jak Właściciel , dla dołączanych subskrypcji (lub która zawiera grupy zasobów, które są `Microsoft.Authorization/roleAssignments/write` dołączane). [](../../role-based-access-control/built-in-roles.md#owner) Aby znaleźć użytkowników, którzy mogą delegować subskrypcję, użytkownik w dzierżawie klienta może wybrać subskrypcję w skrypcie Azure Portal, otworzyć okno Kontrola dostępu **(IAM)** i wyświetlić wszystkich użytkowników z rolą [właściciela.](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription) 
>
> Jeśli subskrypcja została utworzona za pomocą [programu Dostawca rozwiązań w chmurze (CSP),](../concepts/cloud-solution-provider.md) [](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) wdrożenie może wykonać każdy użytkownik, który ma rolę agenta administracyjnego w dzierżawie dostawcy usług.

Wdrożenie można wykonać w witrynie Azure Portal przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure, jak pokazano poniżej.

### <a name="azure-portal"></a>Azure Portal

1. W naszym [repozytorium GitHub](https://github.com/Azure/Azure-Lighthouse-samples/)wybierz przycisk Deploy to Azure (Wd wdrażaj na platformie **Azure)** widoczny obok szablonu, którego chcesz użyć. Szablon zostanie otwarty w witrynie Azure Portal.
1. Wprowadź wartości w polach Nazwa oferty **Msp,** Opis oferty **Msp,** **Zarządzane przez identyfikator dzierżawy** i **Autoryzacje.** Jeśli wolisz, możesz wybrać pozycję **Edytuj parametry,** aby wprowadzić wartości `mspOfferName` , , i bezpośrednio w pliku `mspOfferDescription` `managedbyTenantId` `authorizations` parametrów. Pamiętaj, aby zaktualizować te wartości zamiast używać wartości domyślnych z szablonu.
1. Wybierz **pozycję Przejrzyj i utwórz,** a następnie wybierz pozycję **Utwórz.**

Po kilku minutach powinno zostać wyświetlony komunikat z powiadomieniem, że wdrożenie zostało ukończone.

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
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-file <pathToTemplateFile> \
                         --parameters <parameters/parameterFile> \
                         --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment sub create --name <deploymentName> \
                         --location <AzureRegion> \
                         --template-uri <templateUri> \
                         --parameters <parameterFile> \
                         --verbose
```

## <a name="confirm-successful-onboarding"></a>Potwierdzanie pomyślnego do dołączania

Po pomyślnym dojechania subskrypcji klienta do usługi Azure Lighthouse użytkownicy w dzierżawie dostawcy usług będą mogli zobaczyć subskrypcję i jej zasoby (jeśli przyznano im dostęp za pośrednictwem powyższego procesu, indywidualnie lub jako członek grupy usługi Azure AD z odpowiednimi uprawnieniami). Aby to potwierdzić, upewnij się, że subskrypcja jest wyświetlana na jeden z następujących sposobów.  

### <a name="azure-portal"></a>Azure Portal

W dzierżawie dostawcy usług:

1. Przejdź do [strony Moi klienci.](view-manage-customers.md)
2. Wybierz pozycję **Klienci**.
3. Upewnij się, że widzisz subskrypcje z nazwą oferty podaną w szablonie Resource Manager szablonu.

> [!IMPORTANT]
> Aby wyświetlić delegowaną subskrypcję w skrypcie Moi [klienci,](view-manage-customers.md)użytkownicy w [](../../role-based-access-control/built-in-roles.md#reader) dzierżawie dostawcy usług muszą mieć przyznaną rolę Czytelnik (lub inną wbudowaną rolę, która obejmuje dostęp czytelnika) podczas dołączania subskrypcji.

W dzierżawie klienta:

1. Przejdź do [strony Dostawcy usług.](view-manage-service-providers.md)
2. Wybierz pozycję **Oferty dostawców usług**.
3. Upewnij się, że widzisz subskrypcje z nazwą oferty podaną w szablonie Resource Manager szablonu.

> [!NOTE]
> Po ukończeniu wdrożenia może upłynieć do 15 minut, zanim aktualizacje zostaną odzwierciedlone w Azure Portal. Aktualizacje mogą być dostępne wcześniej, jeśli zaktualizujemy token Azure Resource Manager odświeżenie przeglądarki, zalogowanie się i wylogowanie lub zażądanie nowego tokenu.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext

# Confirm successful onboarding for Azure Lighthouse

Get-AzManagedServicesDefinition
Get-AzManagedServicesAssignment
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list

# Confirm successful onboarding for Azure Lighthouse

az managedservices definition list
az managedservices assignment list
```

Jeśli musisz wprowadzić zmiany po doeniu klienta, możesz [zaktualizować delegowanie](update-delegation.md). Możesz również [całkowicie usunąć dostęp do delegowania.](remove-delegation.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie możesz pomyślnie dopiąć klienta lub jeśli użytkownicy mają problemy z dostępem do delegowanych zasobów, zapoznaj się z poniższymi poradami i wymaganiami i spróbuj ponownie.

- Wartość `managedbyTenantId` nie może być taka sama jak identyfikator dzierżawy dla dołączanej subskrypcji.
- Nie można mieć wielu przypisań w tym samym zakresie z tym `mspOfferName` samym .
- Dostawca **zasobów Microsoft.ManagedServices** musi być zarejestrowany dla subskrypcji delegowane. Powinno to nastąpić automatycznie podczas wdrażania, ale jeśli nie, można [zarejestrować go ręcznie.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)
- Autoryzacje nie mogą obejmować [](../../role-based-access-control/built-in-roles.md#owner) żadnych użytkowników z wbudowaną rolą Właściciel ani żadnych wbudowanych ról z [obiektem DataActions.](../../role-based-access-control/role-definitions.md#dataactions)
- Grupy muszą być tworzone z [**typem grupy ustawionym**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md#group-types) na **Zabezpieczenia,** a nie **Microsoft 365**.
- Może wystąpić dodatkowe opóźnienie przed włączeniu dostępu dla [grup zagnieżdżonych.](../..//active-directory/fundamentals/active-directory-groups-membership-azure-portal.md)
- Użytkownicy, którzy muszą wyświetlać zasoby w Azure Portal [](../../role-based-access-control/built-in-roles.md#reader) muszą mieć rolę Czytelnik (lub inną wbudowaną rolę, która obejmuje dostęp czytelnika).
- Wbudowane [role platformy Azure, które](../../role-based-access-control/built-in-roles.md) są dołączane do autoryzacji, nie mogą zawierać żadnych przestarzałych ról. Jeśli wbudowana rola platformy Azure stanie się przestarzała, żaden użytkownik, który został do tej roli, utraci dostęp i nie będzie można do niego do dołączać dodatkowych delegowania. Aby rozwiązać ten problem, zaktualizuj szablon tak, aby używać tylko obsługiwanych ról wbudowanych, a następnie wykonaj nowe wdrożenie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat obsługi zarządzania między dzierżawami.](../concepts/cross-tenant-management-experience.md)
- [Wyświetl klientów i zarządzaj nimi,](view-manage-customers.md) przechodząc do strony **Moi klienci** w Azure Portal.
- Dowiedz się, [jak zaktualizować](update-delegation.md) [lub usunąć](remove-delegation.md) delegowanie.
