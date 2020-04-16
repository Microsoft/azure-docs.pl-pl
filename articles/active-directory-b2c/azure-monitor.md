---
title: Monitoruj usługę Azure AD B2C za pomocą usługi Azure Monitor
titleSuffix: Azure AD B2C
description: Dowiedz się, jak rejestrować zdarzenia usługi Azure AD B2C za pomocą usługi Azure Monitor przy użyciu zarządzania zasobami delegowanymi.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 99e04c95156e40eed8c2b9aa88a2bee6f39e90c9
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392892"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitoruj usługę Azure AD B2C za pomocą usługi Azure Monitor

Użyj usługi Azure Monitor do kierowania dzienników logowania i [inspekcji](view-audit-logs.md) usługi Azure Active Directory B2C (Azure AD B2C) do różnych rozwiązań monitorowania. Można zachować dzienniki do długotrwałego użytku lub zintegrować z innymi firmami informacji o zabezpieczeniach i narzędzi do zarządzania zdarzeniami (SIEM), aby uzyskać wgląd w środowisko.

Zdarzenia dziennika można kierować do:

* Konto [magazynu](../storage/blobs/storage-blobs-introduction.md)platformy Azure .
* Centrum [zdarzeń](../event-hubs/event-hubs-about.md) platformy Azure (i zintegrować z wystąpieniami Splunk i Sumo Logic).
* [Obszar roboczy usługi Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (do analizowania danych, tworzenia pulpitów nawigacyjnych i alertów o określonych zdarzeniach).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, należy wdrożyć szablon usługi Azure Resource Manager przy użyciu modułu programu Azure PowerShell.

* [Moduł programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) w wersji 6.13.1 lub nowszej

Można również użyć [usługi Azure Cloud Shell](https://shell.azure.com), która zawiera najnowszą wersję modułu programu Azure PowerShell.

## <a name="delegated-resource-management"></a>Zarządzanie zasobami delegowanymi

Usługa Azure AD B2C korzysta z [monitorowania usługi Azure Active Directory.](../active-directory/reports-monitoring/overview-monitoring.md) Aby włączyć *ustawienia diagnostyczne* w usłudze Azure Active Directory w dzierżawie usługi Azure AD B2C, należy użyć [zarządzania zasobami delegowanymi](../lighthouse/concepts/azure-delegated-resource-management.md).

Autoryzujesz użytkownika lub grupę w katalogu usługi Azure AD B2C **(usługodawca)** w celu skonfigurowania wystąpienia usługi Azure Monitor w obrębie dzierżawy zawierającej subskrypcję platformy Azure **(klienta).** Aby utworzyć autoryzację, należy wdrożyć szablon [usługi Azure Resource Manager](../azure-resource-manager/index.yml) do dzierżawy usługi Azure AD zawierającej subskrypcję. W poniższych sekcjach przebiega proces.

## <a name="create-or-choose-resource-group"></a>Tworzenie lub wybieranie grupy zasobów

Jest to grupa zasobów zawierająca docelowe konto magazynu platformy Azure, centrum zdarzeń lub obszar roboczy usługi Log Analytics do odbierania danych z usługi Azure Monitor. Podczas wdrażania szablonu usługi Azure Resource Manager należy określić nazwę grupy zasobów.

[Utwórz grupę zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) lub wybierz istniejącą w dzierżawie usługi Azure Active Directory (Azure AD), która zawiera subskrypcję platformy Azure, a *nie* katalog zawierający dzierżawę usługi Azure AD B2C.

W tym przykładzie użyto grupy zasobów o nazwie *azure-ad-b2c-monitor* w regionie *Środkowe stany USA.*

## <a name="delegate-resource-management"></a>Delegowanie zarządzania zasobami

Następnie zbierz następujące informacje:

**Identyfikator katalogu** usługi Azure AD B2C (znany również jako identyfikator dzierżawy).

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako użytkownik z roli *administratora użytkownika* (lub wyższej).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. Wybierz **pozycję Azure Active Directory**, wybierz pozycję **Właściwości**.
1. Nagraj identyfikator **katalogu**.

**Identyfikator obiektu** grupy usługi Azure AD B2C lub użytkownika, który ma nadać *współautorowi* uprawnienia do grupy zasobów utworzonej wcześniej w katalogu zawierającym subskrypcję.

Aby ułatwić zarządzanie, zaleca się używanie *grup* użytkowników usługi Azure AD dla każdej roli, co pozwala na dodawanie lub usuwanie poszczególnych użytkowników do grupy, zamiast przypisywania uprawnień bezpośrednio do tego użytkownika. W tym instruktażu należy dodać użytkownika.

1. Gdy **usługa Azure Active Directory** jest nadal zaznaczona w witrynie Azure portal, wybierz pozycję **Użytkownicy**, a następnie wybierz użytkownika.
1. Nagraj **identyfikator obiektu**użytkownika .

### <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager

Aby dołączać dzierżawę usługi Azure AD **(klienta),** utwórz [szablon usługi Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) dla oferty z następującymi informacjami. I `mspOfferName` `mspOfferDescription` wartości są widoczne podczas wyświetlania szczegółów oferty na [stronie dostawcy usług](../lighthouse/how-to/view-manage-service-providers.md) w witrynie Azure portal.

| Pole   | Definicja |
|---------|------------|
| `mspOfferName`                     | Nazwa opisująca tę definicję. Na przykład *usługi zarządzane usługi Azure AD B2C*. Ta wartość jest wyświetlana klientowi jako tytuł oferty. |
| `mspOfferDescription`              | Krótki opis twojej oferty. Na przykład *włącza usługę Azure Monitor w usłudze Azure AD B2C*.|
| `rgName`                           | Nazwa grupy zasobów utworzonej wcześniej w dzierżawie usługi Azure AD. Na przykład *azure-ad-b2c-monitor*. |
| `managedByTenantId`                | **Identyfikator katalogu dzierżawy** usługi Azure AD B2C (znany również jako identyfikator dzierżawy). |
| `authorizations.value.principalId` | **Identyfikator obiektu** grupy B2C lub użytkownika, który będzie miał dostęp do zasobów w tej subskrypcji platformy Azure. W tym instruktażu określ identyfikator obiektu użytkownika, który został nagrany wcześniej. |

Pobierz szablon i pliki parametrów usługi Azure Resource Manager:

- [rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Następnie zaktualizuj plik parametrów o wartości zarejestrowane wcześniej. Poniższy fragment kodu JSON zawiera przykład pliku parametrów szablonu usługi Azure Resource Manager. Dla `authorizations.value.roleDefinitionId`, użyj [wbudowanej](../role-based-access-control/built-in-roles.md) wartości roli dla `b24988ac-6180-42a0-ab88-20f7382dd24c`roli *Współautor*, .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Wdrażanie szablonów usługi Azure Resource Manager

Po zaktualizowaniu pliku parametrów wdrożyć szablon usługi Azure Resource Manager w dzierżawie platformy Azure jako wdrożenie na poziomie subskrypcji. Ponieważ jest to wdrożenie na poziomie subskrypcji, nie można go zainicjować w witrynie Azure portal. Można wdrożyć przy użyciu modułu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Metoda programu Azure PowerShell jest pokazana poniżej.

Zaloguj się do katalogu zawierającego subskrypcję przy użyciu [connect-azaccount](/powershell/azure/authenticate-azureps). Użyj `-tenant` flagi, aby wymusić uwierzytelnianie do prawidłowego katalogu.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Polecenie cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) służy do listy subskrypcji, do których konto bieżące może uzyskiwać dostęp w ramach dzierżawy usługi Azure AD. Zarejestruj identyfikator subskrypcji, którą chcesz zaprojektować w dzierżawie usługi Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Następnie przełącz się do subskrypcji, którą chcesz zaprojektować w dzierżawie usługi Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Na koniec należy wdrożyć pliki szablonów i parametrów usługi Azure Resource Manager, które zostały pobrane i zaktualizowane wcześniej. `Location`Wymień `TemplateFile`, `TemplateParameterFile` i odpowiednio wartości.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

Pomyślne wdrożenie szablonu daje dane wyjściowe podobne do następujących (dane wyjściowe obcięty dla zwięzłości):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

Po wdrożeniu szablonu może upłynąć kilka minut, aby projekcja zasobów została ukończona. Może być konieczne odczekanie kilku minut (zazwyczaj nie więcej niż pięć) przed przejściem do następnej sekcji, aby wybrać subskrypcję.

## <a name="select-your-subscription"></a>Wybierz swoją subskrypcję

Po wdrożeniu szablonu i odczekaniu kilku minut na ukończenie projekcji zasobów skojarz subskrypcję z katalogiem usługi Azure AD B2C z następującymi krokami.

1. **Wyloguj się** z witryny Azure portal, jeśli jesteś aktualnie zalogowany. Ten i następujący krok są wykonywane, aby odświeżyć poświadczenia w sesji portalu.
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta administracyjnego usługi Azure AD B2C.
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu.
1. Wybierz katalog zawierający subskrypcję.

    ![Przełącz katalog](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Sprawdź, czy wybrano poprawny katalog i subskrypcję. W tym przykładzie są zaznaczone wszystkie katalogi i subskrypcje.

    ![Wszystkie katalogi wybrane w filtrze Subskrypcja & katalogów](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Ustawienia diagnostyczne definiują, gdzie powinny być wysyłane dzienniki i metryki dla zasobu. Możliwe miejsca docelowe to:

- [Konto magazynu platformy Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Rozwiązania centrów zdarzeń.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Obszar roboczy usługi Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

Jeśli jeszcze tego nie zrobiłeś, utwórz wystąpienie wybranego typu docelowego w grupie zasobów określonej w [szablonie usługi Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Tworzenie ustawień diagnostycznych

Możesz utworzyć [ustawienia diagnostyczne](../active-directory/reports-monitoring/overview-monitoring.md) w witrynie Azure portal.

Aby skonfigurować ustawienia monitorowania dzienników aktywności usługi Azure AD B2C:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. Wybieranie **usługi Azure Active Directory**
1. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
1. Jeśli w zasobie istnieją ustawienia, zostanie wyświetlona lista ustawień już skonfigurowanych. Wybierz pozycję **Dodaj ustawienie diagnostyczne,** aby dodać nowe ustawienie, lub ustawienie **Edytuj,** aby edytować istniejące. Każde ustawienie może mieć nie więcej niż jeden z każdego typu docelowego..

    ![Okienko ustawień diagnostyki w witrynie Azure portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Nadaj ustawieniu nazwę, jeśli jeszcze jej nie ma.
1. Zaznacz pole wyboru dla każdego miejsca docelowego, aby wysłać dzienniki. Wybierz **pozycję Konfiguruj,** aby określić ich ustawienia zgodnie z opisem w poniższej tabeli.

    | Ustawienie | Opis |
    |:---|:---|
    | Zarchiwizuj na koncie magazynu | Nazwa konta magazynu. |
    | Przesyłaj strumieniowo do centrum zdarzeń | Obszar nazw, w którym jest tworzony centrum zdarzeń (jeśli jest to po raz pierwszy dzienniki przesyłania strumieniowego) lub przesyłane strumieniowo do (jeśli istnieją już zasoby, które są przesyłane strumieniowo tej kategorii dziennika do tego obszaru nazw).
    | Wysyłanie do usługi Log Analytics | Nazwa obszaru roboczego. |

1. Wybierz **pozycję AuditLogs** i **SignInLogs**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat dodawania i konfigurowania ustawień diagnostycznych w usłudze Azure Monitor, zobacz [Samouczek: Zbieranie i analizowanie dzienników zasobów z zasobu platformy Azure](../azure-monitor/insights/monitor-azure-resource.md).

Aby uzyskać informacje dotyczące przesyłania strumieniowego dzienników usługi Azure AD do centrum zdarzeń, zobacz [Samouczek: Strumieniowanie dzienników usługi Azure Active Directory do centrum zdarzeń platformy Azure.](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
