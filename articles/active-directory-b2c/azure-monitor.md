---
title: Monitoruj Azure AD B2C z Azure Monitor
titleSuffix: Azure AD B2C
description: Dowiedz się, jak rejestrować Azure AD B2C zdarzenia za pomocą Azure Monitor przy użyciu delegowanego zarządzania zasobami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 3106e5a640ed66828558078e6986979ad7195450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85386219"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitoruj Azure AD B2C z Azure Monitor

Użyj Azure Monitor, aby kierować dzienniki logowania i [inspekcji](view-audit-logs.md) w usłudze Azure Active Directory B2C (Azure AD B2C) do różnych rozwiązań monitorowania. Dzienniki można zachować do długoterminowego użytku lub zintegrować z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń innych firm (SIEM), aby uzyskać wgląd w środowisko.

Zdarzenia dziennika można kierować do:

* Konto usługi Azure [Storage](../storage/blobs/storage-blobs-introduction.md).
* [Obszar roboczy log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (do analizowania danych, tworzenia pulpitów nawigacyjnych i alertów dotyczących określonych zdarzeń).
* [Centrum zdarzeń](../event-hubs/event-hubs-about.md) platformy Azure (i integracja z wystąpieniami logiki Splunk i Sumo).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, należy wdrożyć szablon Azure Resource Manager przy użyciu modułu Azure PowerShell.

* [Moduł Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) w wersji 6.13.1 lub nowszej

Można również użyć [Azure Cloud Shell](https://shell.azure.com), która zawiera najnowszą wersję modułu Azure PowerShell.

## <a name="delegated-resource-management"></a>Delegowane zarządzanie zasobami

Azure AD B2C wykorzystuje [monitorowanie Azure Active Directory](../active-directory/reports-monitoring/overview-monitoring.md). Aby włączyć *Ustawienia diagnostyczne* w Azure Active Directory w dzierżawie Azure AD B2C, należy użyć [delegowanego zarządzania zasobami](../lighthouse/concepts/azure-delegated-resource-management.md).

Użytkownik lub grupa jest autoryzowana w katalogu Azure AD B2C ( **Dostawca usługi**), aby skonfigurować wystąpienie Azure monitor w ramach dzierżawy, które zawiera subskrypcję platformy Azure ( **klienta**). Aby utworzyć autoryzację, należy wdrożyć szablon [Azure Resource Manager](../azure-resource-manager/index.yml) w dzierżawie usługi Azure AD zawierającym subskrypcję programu. W poniższych sekcjach omówiono proces.

## <a name="create-or-choose-resource-group"></a>Utwórz lub wybierz grupę zasobów

Jest to grupa zasobów zawierająca docelowe konto usługi Azure Storage, centrum zdarzeń lub Log Analytics obszar roboczy do odbierania danych z Azure Monitor. Podczas wdrażania szablonu Azure Resource Manager należy określić nazwę grupy zasobów.

[Utwórz grupę zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) lub wybierz istniejącą w dzierżawie usługi Azure Active Directory (Azure AD), która zawiera subskrypcję platformy Azure, a *nie* katalog zawierający dzierżawę Azure AD B2C.

Ten przykład używa grupy zasobów o nazwie *Azure-AD-B2C-monitor* w regionie *środkowe stany USA* .

## <a name="delegate-resource-management"></a>Delegowanie zarządzania zasobami

Następnie Zbierz następujące informacje:

**Identyfikator katalogu** katalogu Azure AD B2C (znany również jako identyfikator dzierżawy).

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako użytkownik z rolą *administratora użytkownika* (lub wyższą).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Właściwości**.
1. Zapisz **Identyfikator katalogu**.

**Identyfikator obiektu** grupy Azure AD B2C lub użytkownika, któremu chcesz nadać uprawnienia *współautora* do grupy zasobów utworzonej wcześniej w katalogu zawierającym twoją subskrypcję.

Aby ułatwić zarządzanie, zalecamy korzystanie z *grup* użytkowników usługi Azure AD dla każdej roli, co pozwala na dodawanie lub usuwanie poszczególnych użytkowników do grupy zamiast przypisywania uprawnień bezpośrednio do tego użytkownika. W tym instruktażu zostanie dodany użytkownik.

1. Po wybraniu **Azure Active Directory** w Azure Portal wybierz pozycję **Użytkownicy**, a następnie wybierz użytkownika.
1. Zapisz **Identyfikator obiektu**użytkownika.

### <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu Azure Resource Manager

Aby dołączyć dzierżawę usługi Azure AD ( **klienta**), utwórz [szablon Azure Resource Manager](../lighthouse/how-to/onboard-customer.md) na potrzeby oferty, korzystając z poniższych informacji. `mspOfferName`Wartości i `mspOfferDescription` są widoczne podczas wyświetlania szczegółów oferty na [stronie dostawcy usług](../lighthouse/how-to/view-manage-service-providers.md) Azure Portal.

| Pole   | Definicja |
|---------|------------|
| `mspOfferName`                     | Nazwa opisująca tę definicję. Na przykład *Azure AD B2C usługi zarządzane*. Ta wartość jest wyświetlana klientowi jako tytuł oferty. |
| `mspOfferDescription`              | Krótki opis oferty. Na przykład program *włącza Azure monitor w Azure AD B2C*.|
| `rgName`                           | Nazwa grupy zasobów utworzonej wcześniej w dzierżawie usługi Azure AD. Na przykład *usługa Azure-AD-B2C-monitor*. |
| `managedByTenantId`                | **Identyfikator katalogu** dzierżawy Azure AD B2C (znany również jako identyfikator dzierżawy). |
| `authorizations.value.principalId` | **Identyfikator obiektu** grupy B2C lub użytkownika, który będzie miał dostęp do zasobów w ramach tej subskrypcji platformy Azure. W tym instruktażu Określ identyfikator obiektu użytkownika, który został zarejestrowany wcześniej. |

Pobierz Azure Resource Manager szablonu i plików parametrów:

- [rgDelegatedResourceManagement.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.jsna](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Następnie zaktualizuj plik parametrów przy użyciu zarejestrowanych wcześniej wartości. Poniższy fragment kodu JSON przedstawia przykład pliku parametrów szablonu Azure Resource Manager. W przypadku programu `authorizations.value.roleDefinitionId` należy użyć [wbudowanej wartości roli](../role-based-access-control/built-in-roles.md) *współautor* `b24988ac-6180-42a0-ab88-20f7382dd24c` .

```json
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

### <a name="deploy-the-azure-resource-manager-templates"></a>Wdrażanie szablonów Azure Resource Manager

Po zaktualizowaniu pliku parametrów Wdróż szablon Azure Resource Manager w dzierżawie platformy Azure jako wdrożenie na poziomie subskrypcji. Ponieważ jest to wdrożenie na poziomie subskrypcji, nie można go zainicjować w Azure Portal. Program można wdrożyć przy użyciu modułu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Poniżej przedstawiono metodę Azure PowerShell.

Zaloguj się do katalogu zawierającego swoją subskrypcję za pomocą polecenia [Connect-AzAccount](/powershell/azure/authenticate-azureps). Użyj `-tenant` flagi, aby wymusić uwierzytelnienie w poprawnym katalogu.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Użyj polecenia cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) , aby wyświetlić listę subskrypcji, do których bieżące konto może uzyskać dostęp w ramach dzierżawy usługi Azure AD. Zapisz identyfikator subskrypcji, która ma być projektem w dzierżawie Azure AD B2C.

```PowerShell
Get-AzSubscription
```

Następnie przejdź do subskrypcji, którą chcesz umieścić w projekcie w dzierżawie Azure AD B2C:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Na koniec Wdróż pobrane i zaktualizowane wcześniej pliki szablonu Azure Resource Manager i parametrów. Zastąp `Location` `TemplateFile` `TemplateParameterFile` odpowiednio wartości, i.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

Pomyślne wdrożenie szablonu generuje dane wyjściowe podobne do następujących (dane wyjściowe zostały obcięte dla zwięzłości):

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

Po wdrożeniu szablonu może upłynąć kilka minut, zanim będzie można wykonać projekcję zasobu. Może być konieczne odczekanie kilku minut (zazwyczaj nie więcej niż pięć) przed przejściem do następnej sekcji, aby wybrać subskrypcję.

## <a name="select-your-subscription"></a>Wybierz swoją subskrypcję

Po wdrożeniu szablonu i poczekaj kilka minut na ukończenie projekcji zasobów Skojarz swoją subskrypcję z katalogiem Azure AD B2C, wykonując poniższe kroki.

1. **Wyloguj się** z Azure Portal, jeśli użytkownik jest obecnie zalogowany. W tym celu należy wykonać następujące czynności, aby odświeżyć poświadczenia w sesji portalu.
1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu Azure AD B2C konta administracyjnego.
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** .
1. Wybierz katalog, który zawiera twoją subskrypcję.

    ![Przełącz katalog](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Sprawdź, czy wybrano prawidłowy katalog i subskrypcję. W tym przykładzie wybrano wszystkie katalogi i subskrypcje.

    ![Wszystkie katalogi wybrane w & filtr subskrypcji usługi katalogowej](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Ustawienia diagnostyczne definiują, gdzie należy wysyłać dzienniki i metryki dla zasobu. Możliwe miejsca docelowe to:

- [Konto usługi Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md)
- Rozwiązania [centrów zdarzeń](../azure-monitor/platform/resource-logs-stream-event-hubs.md) .
- [Log Analytics obszar roboczy](../azure-monitor/platform/resource-logs-collect-workspace.md)

Jeśli jeszcze tego nie zrobiono, Utwórz wystąpienie wybranego typu docelowego w grupie zasobów określonej w [szablonie Azure Resource Manager](#create-an-azure-resource-manager-template).

### <a name="create-diagnostic-settings"></a>Tworzenie ustawień diagnostycznych

Możesz przystąpić do [tworzenia ustawień diagnostycznych](../active-directory/reports-monitoring/overview-monitoring.md) w Azure Portal.

Aby skonfigurować ustawienia monitorowania dla Azure AD B2C dzienników aktywności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. Wybierz **Azure Active Directory**
1. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.
1. Jeśli w zasobie istnieją istniejące ustawienia, zostanie wyświetlona lista ustawień, które zostały już skonfigurowane. Wybierz opcję **Dodaj ustawienie diagnostyczne** , aby dodać nowe ustawienie, lub **Edytuj** ustawienie, aby edytować istniejące. Każde ustawienie nie może mieć więcej niż jednego z typów docelowych...

    ![Okienko ustawień diagnostycznych w Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Nadaj ustawieniu nazwę, jeśli jeszcze jej nie ma.
1. Zaznacz pole wyboru dla każdego miejsca docelowego, aby wysłać dzienniki. Wybierz pozycję **Konfiguruj** , aby określić swoje ustawienia zgodnie z opisem w poniższej tabeli.

    | Ustawienie | Opis |
    |:---|:---|
    | Zarchiwizuj na koncie magazynu | Nazwa konta magazynu. |
    | Przesyłaj strumieniowo do centrum zdarzeń | Przestrzeń nazw, w której jest tworzony centrum zdarzeń (jeśli jest to pierwsze dzienniki przesyłania strumieniowego) lub przesyłane strumieniowo do programu (jeśli istnieją już zasoby, które są przesyłane strumieniowo do tej przestrzeni nazw).
    | Wysyłanie do usługi Log Analytics | Nazwa obszaru roboczego. |

1. Wybierz pozycję **AuditLogs** i **SignInLogs**.
1. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat dodawania i konfigurowania ustawień diagnostycznych w Azure Monitor, zobacz [Samouczek: zbieranie i analizowanie dzienników zasobów z zasobów platformy Azure](../azure-monitor/insights/monitor-azure-resource.md).

Aby uzyskać informacje o przesyłaniu strumieniowym dzienników usługi Azure AD do centrum zdarzeń, zobacz [Samouczek: przesyłanie strumieniowe dzienników Azure Active Directory do centrum zdarzeń platformy Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
