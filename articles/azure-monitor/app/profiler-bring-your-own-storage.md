---
title: Konfigurowanie BYOS (przenoszenie własnego magazynu) dla & profilera Snapshot Debugger
description: Konfigurowanie BYOS (przenoszenie własnego magazynu) dla & profilera Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589670"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Skonfiguruj własny magazyn (BYOS) dla Application Insights Profiler i Snapshot Debugger

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>Co to jest Twój własny magazyn (BYOS) i dlaczego warto go potrzebować? 
W przypadku używania Application Insights Profiler lub Snapshot Debugger artefakty generowane przez aplikację są przekazywane do kont usługi Azure Storage za pośrednictwem publicznego Internetu. Te konta są płatne i kontrolowane przez firmę Microsoft w celu przetworzenia i analizy. Firma Microsoft kontroluje zasady zarządzania szyfrowaniem w czasie spoczynku i okres istnienia dla tych artefaktów.

W przypadku przenoszenia własnego magazynu te artefakty są przekazywane do konta magazynu, które kontrolujesz. Oznacza to, że steruje zasadami szyfrowania w czasie spoczynku, zasadami zarządzania okresem istnienia i dostępem do sieci. Użytkownik będzie jednak odpowiedzialny za koszty związane z tym kontem magazynu.

> [!NOTE]
> W przypadku włączenia linku prywatnego należy wprowadzić własny magazyn. Aby uzyskać więcej informacji na temat prywatnego linku do Application Insights, [Zobacz dokumentację.](../logs/private-link-security.md)
>
> W przypadku włączenia kluczy Customer-Managed należy wprowadzić własny magazyn. Aby uzyskać więcej informacji na temat Customer-Managed kluczy dla Application Insights, [zapoznaj się z dokumentacją.](../logs/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>Jak będzie uzyskiwany dostęp do konta magazynu?
1. Agenci działający w Virtual Machines lub App Service będą przekazywać artefakty (profile, migawki i symbole) do kontenerów obiektów BLOB na Twoim koncie. Ten proces obejmuje kontaktowanie się z usługą Application Insights Profiler lub Snapshot Debugger w celu uzyskania tokenu sygnatury dostępu współdzielonego do nowego obiektu BLOB na koncie magazynu.
1. Usługa Application Insights Profiler lub Snapshot Debugger przeanalizuje przychodzący obiekt BLOB i zapisze wyniki analizy i pliki dzienników w usłudze BLOB Storage. W zależności od dostępnej pojemności obliczeniowej ten proces może wystąpić w dowolnym momencie po załadowaniu.
1. Podczas wyświetlania śladów profilera lub analizy debugera migawek usługa pobierze wyniki analizy z magazynu obiektów BLOB.

## <a name="prerequisites"></a>Wymagania wstępne
* Upewnij się, że konto magazynu zostało utworzone w tej samej lokalizacji co zasób Application Insights. Np. Jeśli zasób Application Insights jest w regionie zachodnie stany USA 2, konto magazynu musi być również w regionie zachodnie stany USA 2. 
* Udziel roli "Współautor danych obiektów blob magazynu" do aplikacji usługi AAD "dostęp do zaufanego magazynu usług diagnostycznych" na koncie magazynu za pośrednictwem interfejsu użytkownika Access Control (IAM).
* Jeśli link prywatny jest włączony, skonfiguruj dodatkowe ustawienie, aby zezwolić na połączenie z naszą zaufaną usługą firmy Microsoft z Virtual Network. 

## <a name="how-to-enable-byos"></a>Jak włączyć BYOS

### <a name="create-storage-account"></a>Tworzenie konta magazynu
Utwórz nowe konto magazynu (jeśli nie masz go) w tej samej lokalizacji, w której znajduje się zasób Application Insights.
Jeśli zasób Application Insights jest włączony `West US 2` , konto magazynu musi znajdować się w `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>Udzielanie dostępu do usług diagnostycznych do konta magazynu
Konto magazynu BYOS zostanie połączone z zasobem Application Insights. Może istnieć tylko jedno konto magazynu dla zasobu Application Insights i oba muszą znajdować się w tej samej lokalizacji. Możesz użyć tego samego konta magazynu z więcej niż jednym zasobem Application Insights.

Najpierw Application Insights Profiler i usługa Snapshot Debugger muszą otrzymać dostęp do konta magazynu. Aby udzielić dostępu, Dodaj rolę `Storage Blob Data Contributor` do aplikacji usługi AAD o nazwie `Diagnostic Services Trusted Storage Access` za pośrednictwem strony Access Control (IAM) na koncie magazynu, jak pokazano na rysunku 1,0. 

Kroki: 
1. Kliknij przycisk "Dodaj" w sekcji "Dodawanie przypisania roli" 
1. Wybierz rolę "Współautor danych obiektu blob magazynu" 
1. Wybierz pozycję "użytkownik usługi Azure AD, Grupa lub główna usługa" w sekcji "Przypisywanie dostępu do" 
1. Wyszukaj & wybierz aplikację "usługa diagnostyczna zaufany dostęp do magazynu" 
1. Zapisz zmiany

_![ Rysunek 1,0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _rysunek 1,0_ 

Po dodaniu roli zostanie ona wyświetlona w sekcji "przydziały ról", jak pokazano na poniższym rysunku 1,1. 
_![ Rysunek 1,1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _rysunek 1,1_ 

Jeśli używasz również linku prywatnego, musisz mieć jedną dodatkową konfigurację, aby zezwolić na połączenie z naszą zaufaną usługą firmy Microsoft z Virtual Network. Zapoznaj się z [dokumentacją zabezpieczeń sieci magazynu](../../storage/common/storage-network-security.md#trusted-microsoft-services).

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>Połącz swoje konto magazynu z zasobem Application Insights
Aby skonfigurować BYOS do diagnostyki na poziomie kodu (Profiler/debuger), dostępne są trzy opcje:

* Używanie Azure PowerShell poleceń cmdlet
* Korzystanie z interfejsu wiersza polecenia platformy Azure (CLI)
* Korzystanie z szablonu usługi Azure Resource Manager

#### <a name="configure-using-azure-powershell-cmdlets"></a>Konfigurowanie przy użyciu Azure PowerShell poleceń cmdlet

1. Upewnij się, że zainstalowano AZ PowerShell 4.2.0 lub nowszy.

    Aby zainstalować Azure PowerShell, zapoznaj się z [oficjalną dokumentacją Azure PowerShell](/powershell/azure/install-az-ps).

1. Zainstaluj rozszerzenie programu Application Insights PowerShell.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Zaloguj się przy użyciu konta platformy Azure
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    Aby uzyskać więcej informacji na temat sposobu logowania, zapoznaj się z [dokumentacją Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Usuń poprzednie konto magazynu połączone z zasobem Application Insights.

    Znaczne
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Przykład:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Połącz swoje konto magazynu z zasobem Application Insights.
    
    Znaczne
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Przykład:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Konfigurowanie przy użyciu interfejsu wiersza polecenia platformy Azure

1. Upewnij się, że zainstalowano interfejs wiersza polecenia platformy Azure.

    Aby zainstalować interfejs wiersza polecenia platformy Azure, zapoznaj się z [oficjalną dokumentacją interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

1. Zainstaluj rozszerzenie interfejsu wiersza polecenia Application Insights.
    ```powershell
    az extension add -n application-insights
    ```

1. Połącz swoje konto magazynu z zasobem Application Insights.

    Znaczne
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Przykład:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Oczekiwane dane wyjściowe:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Aby przeprowadzić aktualizacje na połączonych kontach magazynu do zasobu Application Insights, zapoznaj się z [dokumentacją interfejsu wiersza polecenia Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage).

#### <a name="configure-using-azure-resource-manager-template"></a>Konfigurowanie przy użyciu szablonu Azure Resource Manager

1. Utwórz plik szablonu Azure Resource Manager o następującej zawartości (byos.template.jswłączony).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Uruchom następujące polecenie programu PowerShell, aby wdrożyć poprzedni szablon (utworzyć konto połączonego magazynu).

    Znaczne
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Przykład:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. Po wyświetleniu monitu w konsoli programu PowerShell podaj następujące parametry:
    
    |           Parametr           |                                Opis                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | Nazwa zasobu Application Insights, aby włączyć BYOS.            |
    | storage_account_name          | Nazwa zasobu konta magazynu, który będzie używany jako BYOS. |
    
    Oczekiwane dane wyjściowe:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Włącz diagnostykę na poziomie kodu (Profiler/debuger) na podstawie obciążeń związanych z Azure Portal. (App Service > Application Insights) _![ Rysunek 2,0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _rysunek 2,0_

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="template-schema-schema_uri-isnt-supported"></a>Schemat szablonu "{schema_uri}" nie jest obsługiwany.
* Upewnij się, że `$schema` Właściwość szablonu jest prawidłowa. Musi być zgodna z następującym wzorcem: `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* Upewnij się, że `schema_version` szablon znajduje się w prawidłowych wartościach: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Komunikat o błędzie:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>Nie znaleziono żadnego zarejestrowanego dostawcy zasobów dla lokalizacji "{Location}".
* Upewnij się, że `apiVersion` zasób `microsoft.insights/components` jest `2015-05-01` .
* Upewnij się, że `apiVersion` zasób `linkedStorageAccount` jest `2020-03-01-preview` .
    Komunikat o błędzie:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>Lokalizacja konta magazynu powinna być zgodna z lokalizacją składnika AI.
* Upewnij się, że lokalizacja zasobu Application Insights jest taka sama jak konto magazynu.
    Komunikat o błędzie:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

Ogólne rozwiązywanie problemów z narzędziem Profiler można znaleźć w dokumentacji dotyczącej [rozwiązywania problemów profilera](profiler-troubleshooting.md).

Ogólne Snapshot Debugger Rozwiązywanie problemów można znaleźć w [dokumentacji Snapshot Debugger rozwiązywania problemów](snapshot-debugger-troubleshoot.md). 

## <a name="faqs"></a>Często zadawane pytania
* Jeśli mam program Profiler lub migawkę, a następnie BYOS, czy moje dane zostaną zmigrowane na konto magazynu?
    _Nie._

* Czy BYOS będzie działał przy użyciu szyfrowania w czasie spoczynku i Customer-Managed?
    _Tak, aby była precyzyjna, BYOS jest wymagana, aby Profiler/debuger był włączony z kluczami Customer-Manager._

* Czy BYOS będzie działała w środowisku odizolowanym od Internetu?
    _Opcję. W rzeczywistości BYOS jest wymaganiem dla izolowanych scenariuszy sieci._

* BYOS będzie działała po włączeniu obu, Customer-Managed kluczy i linku prywatnego? 
    _Tak, może być możliwe._

* Czy po włączeniu usługi BYOS można korzystać z kont magazynu usług diagnostycznych w celu przechowywania zebranych danych? 
    _Tak, możesz, ale teraz nie obsługujemy migracji danych z BYOS._

* Czy po włączeniu BYOS przeniesiesz wszystkie powiązane koszty, które są magazynem i siecią? 
    _Tak_
