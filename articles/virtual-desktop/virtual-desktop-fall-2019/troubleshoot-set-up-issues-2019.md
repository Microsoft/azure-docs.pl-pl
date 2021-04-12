---
title: Tworzenie puli hostów dzierżawy usług pulpitu wirtualnego systemu Windows (klasycznej) — Azure
description: Jak rozwiązywać problemy z dzierżawą i pulą hostów podczas instalacji środowiska dzierżawy pulpitu wirtualnego systemu Windows (klasycznego).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 12be4611f0583c6da630b9e5a0e4b5a82ebfa650
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444279"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Tworzenie dzierżawy i puli hostów w systemie Windows Virtual Desktop (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../troubleshoot-set-up-issues.md).

W tym artykule opisano problemy podczas wstępnej instalacji dzierżawy usług pulpitu wirtualnego systemu Windows i infrastruktury puli powiązanych hostów sesji.

## <a name="provide-feedback"></a>Wyraź opinię

Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Uzyskiwanie obrazu wielosesyjnego dla systemu Windows 10 Enterprise

Aby użyć obrazu wielosesyjnego dla systemu Windows 10 Enterprise, przejdź do witryny Azure Marketplace, wybierz pozycję **wprowadzenie**  >  **Microsoft Windows 10** > i [Windows 10 Enterprise dla pulpitów wirtualnych w wersji 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający wybór systemu Windows 10 Enterprise dla pulpitów wirtualnych, wersja 1809.](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant&quot;></a>Tworzenie dzierżawy pulpitu wirtualnego systemu Windows

W tej sekcji omówiono potencjalne problemy podczas tworzenia dzierżawy pulpitu wirtualnego systemu Windows.

### <a name=&quot;error-aadsts650052-the-app-needs-access-to-a-service&quot;></a>Błąd: AADSTS650052 aplikacja musi mieć dostęp do usługi.

Przykład nieprzetworzonego błędu:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**Przyczyna:** Nie udzielono zgody na pulpit wirtualny systemu Windows w wystąpieniu usługi Azure Active Directory.

**Poprawka:** [postępuj zgodnie z tym przewodnikiem](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) , aby udzielić zgody.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu okna programu PowerShell, w którym użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania.](../media/UserNotAuthorizedNewTenant.png)

Przykład nieprzetworzonego błędu:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Przyczyna:** Zalogowany użytkownik nie ma przypisanej roli TenantCreator w Azure Active Directory.

**Poprawka:** Postępuj zgodnie z instrukcjami w temacie [Przypisywanie roli aplikacji TenantCreator do użytkownika w dzierżawie Azure Active Directory](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Po wykonaniu instrukcji uzyskasz użytkownikowi przypisaną rolę TenantCreator.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający przypisaną rolę TenantCreator.](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Tworzenie maszyn wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows

Maszyny wirtualne hosta sesji można tworzyć na kilka sposobów, ale zespół pulpitów wirtualnych systemu Windows obsługuje tylko problemy z obsługą maszyn wirtualnych powiązane z ofertą [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) . Aby uzyskać więcej informacji, zobacz [problemy z używaniem pulpitu wirtualnego systemu Windows — Inicjowanie obsługi puli hostów Azure Marketplace oferta](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemy z używaniem pulpitu wirtualnego systemu Windows — Inicjowanie obsługi puli hostów Azure Marketplace oferta

Pulpit wirtualny systemu Windows — Inicjowanie obsługi administracyjnej szablonu puli hostów jest dostępny w portalu Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Błąd: w przypadku korzystania z linku z usługi GitHub zostanie wyświetlony komunikat "Tworzenie bezpłatnego konta"

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, aby utworzyć bezpłatne konto.](../media/be615904ace9832754f0669de28abd94.png)

**Przyczyna 1:** W ramach konta użytego do zalogowania się na platformie Azure nie ma aktywnych subskrypcji lub użyte konto nie ma uprawnień do wyświetlania subskrypcji.

**Poprawka 1:** Zaloguj się przy użyciu konta, które ma dostęp współautora (co najmniej) do subskrypcji, w której zostaną wdrożone maszyny wirtualne hosta sesji.

**Przyczyna 2:** Używana subskrypcja jest częścią dzierżawy dostawcy usług Microsoft Cloud.

**Poprawka 2:** Przejdź do lokalizacji usługi GitHub, aby **utworzyć nową pulę hostów usług pulpitu wirtualnego systemu Windows i udostępnić** ją, a następnie wykonaj następujące instrukcje:

1. Kliknij prawym przyciskiem myszy pozycję **Wdróż na platformie Azure** i wybierz pozycję **Kopiuj adres łącza**.
2. Otwórz **Notatnik** i wklej link.
3. Przed znakiem # Wstaw nazwę dzierżawcy klienta końcowego dostawcy CSP.
4. Otwórz nowy link w przeglądarce, a Azure Portal załaduje szablon.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Błąd: Wystąpił błąd "wdrożenie szablonu jest nieprawidłowe"

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający "Wdrażanie szablonu... nieprawidłowy "błąd"](../media/troubleshooting-marketplace-validation-error-generic.png)

Przed podjęciem określonego działania należy sprawdzić dziennik aktywności, aby zobaczyć szczegółowy błąd sprawdzania poprawności wdrożenia zakończonego niepowodzeniem.

Aby wyświetlić błąd w dzienniku aktywności:

1. Wyjdź z bieżącej oferty wdrażania w portalu Azure Marketplace.
2. Na górnym pasku wyszukiwania Wyszukaj i wybierz pozycję **Dziennik aktywności**.
3. Znajdź działanie o nazwie **Weryfikuj wdrożenie** o stanie **Niepowodzenie** i wybierz działanie.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający działanie pojedynczego * * Weryfikuj wdrożenie * * aktywności z * * niepowodzeniem * * status](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Wybierz pozycję JSON, a następnie przewiń w dół do dolnej części ekranu, dopóki nie zobaczysz pola "statusMessage".

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu działania zakończonego niepowodzeniem z czerwonym prostokątem wokół właściwości statusMessage tekstu JSON.](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

Jeśli szablon operacji przekracza limit przydziału, możesz wykonać jedną z następujących czynności, aby rozwiązać ten problem:

 - Uruchom witrynę Azure Marketplace z parametrami, które zostały użyte po raz pierwszy, ale ten czas wykorzystuje mniej maszyn wirtualnych i rdzeni maszyn wirtualnych.
 - Otwórz link widoczny w polu **statusMessage** w przeglądarce, aby przesłać żądanie zwiększenia przydziału subskrypcji platformy Azure dla określonej jednostki SKU maszyny wirtualnej.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager szablonu i błędów konfiguracji żądanego stanu (DSC) programu PowerShell

Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązywać problemy z niepowodzeniem wdrożenia szablonów Azure Resource Manager i DSC programu PowerShell.

1. Przejrzyj błędy we wdrożeniu, korzystając [z opcji Wyświetl operacje wdrażania z Azure Resource Manager](../../azure-resource-manager/templates/deployment-history.md).
2. Jeśli wdrożenie nie zawiera żadnych błędów, Przejrzyj błędy w dzienniku aktywności, korzystając [z widoku dzienniki aktywności do inspekcji akcji na zasobach](../../azure-resource-manager/management/view-activity-logs.md).
3. Po zidentyfikowaniu tego błędu Użyj komunikatu o błędzie i zasobów w [rozwiązywaniu typowych błędów wdrażania platformy Azure z Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) , aby rozwiązać problem.
4. Usuń wszystkie zasoby utworzone w poprzednim wdrożeniu i ponów próbę wdrożenia szablonu.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Błąd: wdrożenie nie powiodło się.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu wdrożenia nie powiódł się.](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Przykład nieprzetworzonego błędu:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Przyczyna 1:** Poświadczenia podane w celu dołączania maszyn wirtualnych do domeny są nieprawidłowe.

**Poprawka 1:** Zobacz błąd "Nieprawidłowe poświadczenia" dla maszyn wirtualnych nie są przyłączone do domeny w [konfiguracji maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration-2019.md).

**Przyczyna 2:** Nie rozpoznano nazwy domeny.

**Poprawka 2:** Zobacz [błąd: nazwa domeny nie jest rozpoznawana](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) w [konfiguracji maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration-2019.md).

**Przyczyna 3:** Konfiguracja DNS sieci wirtualnej (VNET) jest ustawiona na **wartość domyślna**.

Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Otwórz witrynę Azure Portal i przejdź do karty **sieci wirtualne** .
2. Znajdź sieć wirtualną, a następnie wybierz pozycję **serwery DNS**.
3. Menu serwery DNS powinno pojawić się po prawej stronie ekranu. W tym menu wybierz pozycję **niestandardowy**.
4. Upewnij się, że serwery DNS wymienione w obszarze niestandardowe są zgodne z kontrolerem domeny lub Active Directory domeny. Jeśli nie widzisz serwera DNS, możesz go dodać, wprowadzając jego wartość w polu **Dodaj serwer DNS** .

### <a name="error-your-deployment-failedunauthorized"></a>Błąd: Wdrożenie nie powiodło się...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Przyczyna:** Używana subskrypcja jest typem, który nie może uzyskać dostępu do wymaganych funkcji w regionie, w którym klient próbuje wykonać wdrożenie. Na przykład w przypadku subskrypcji MSDN, bezpłatnych lub edukacyjnych może zostać wyświetlony ten błąd.

**Poprawka:** Zmień typ subskrypcji lub region na taki, który będzie mógł uzyskać dostęp do wymaganych funkcji.

### <a name="error-vmextensionprovisioningerror"></a>Błąd: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu wdrożenia nie powiódł się z powodu niepowodzenia inicjowania obsługi administracyjnej.](../media/7aaf15615309c18a984673be73ac969a.png)

**Przyczyna 1:** Błąd przejściowy ze środowiskiem pulpitu wirtualnego systemu Windows.

**Przyczyna 2:** Błąd przejściowy z połączeniem.

**Poprawka:** Potwierdź, że środowisko pulpitu wirtualnego systemu Windows jest w dobrej kondycji, logując się przy użyciu programu PowerShell. Ręcznie Zakończ rejestrację maszyny wirtualnej w obszarze [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell-2019.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Błąd: Określona nazwa użytkownika administratora jest niedozwolona

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu wdrożenia nie powiódł się, gdy określony administrator nie jest dozwolony.](../media/f2b3d3700e9517463ef88fa41875bac9.png)

Przykład nieprzetworzonego błędu:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Przyczyna:** Podane hasło zawiera zabronione podciągi (administrator, administrator, root).

**Poprawka:** Zaktualizuj nazwę użytkownika lub Użyj różnych użytkowników.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Błąd: Maszyna wirtualna zgłosiła błąd podczas przetwarzania rozszerzenia

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający operację zasobu zakończony ze stanem inicjowania obsługi terminalu we wdrożeniu nie powiódł się.](../media/49c4a1836a55d91cd65125cf227f411f.png)

Przykład nieprzetworzonego błędu:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Przyczyna:** Rozszerzenie DSC programu PowerShell nie mogło uzyskać dostępu administratora na maszynie wirtualnej.

**Poprawka:** Upewnij się, że nazwa użytkownika i hasło mają dostęp administracyjny do maszyny wirtualnej, a następnie ponownie uruchom szablon Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Błąd: DeploymentFailed — Konfiguracja DSC programu PowerShell "FirstSessionHost" została ukończona z błędami

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu wdrożenia nie powiodło się, ponieważ Konfiguracja DSC programu PowerShell "FirstSessionHost" została ukończona z błędami.](../media/64870370bcbe1286906f34cf0a8646ab.png)

Przykład nieprzetworzonego błędu:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Przyczyna:** Rozszerzenie DSC programu PowerShell nie mogło uzyskać dostępu administratora na maszynie wirtualnej.

**Poprawka:** Upewnij się, że podana nazwa użytkownika i hasło mają dostęp administracyjny do maszyny wirtualnej, a następnie ponownie uruchom szablon Azure Resource Manager.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Błąd: DeploymentFailed – InvalidResourceReference

Przykład nieprzetworzonego błędu:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Przyczyna:** Część nazwy grupy zasobów jest używana w przypadku niektórych zasobów tworzonych przez szablon. Ze względu na nazwę zgodną z istniejącymi zasobami szablon może wybrać istniejący zasób z innej grupy.

**Poprawka:** Podczas uruchamiania szablonu Azure Resource Manager, aby wdrożyć maszyny wirtualne hosta sesji, należy utworzyć pierwsze dwa znaki jako unikatowe dla nazwy grupy zasobów subskrypcji.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Błąd: DeploymentFailed – InvalidResourceReference

Przykład nieprzetworzonego błędu:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Przyczyna:** Ten błąd jest spowodowany tym, że karta sieciowa utworzona przy użyciu szablonu Azure Resource Manager ma taką samą nazwę, jak inna karta sieciowa już w sieci wirtualnej.

**Poprawka:** Użyj innego prefiksu hosta.

### <a name="error-deploymentfailed--error-downloading"></a>Błąd: DeploymentFailed — pobieranie błędu

Przykład nieprzetworzonego błędu:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Przyczyna:** Ten błąd jest spowodowany trasą statyczną, regułą zapory lub sieciowej grupy zabezpieczeń, która blokuje pobieranie pliku zip powiązanego z szablonem Azure Resource Manager.

**Poprawka:** Usuń blokowanie trasy statycznej, reguły zapory lub sieciowej grupy zabezpieczeń. Opcjonalnie możesz otworzyć plik JSON szablonu Azure Resource Manager w edytorze tekstów, pobrać link do pliku zip i pobrać zasób do dozwolonej lokalizacji.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Błąd: Użytkownik nie ma uprawnień do wysyłania zapytań do usługi zarządzania

Przykład nieprzetworzonego błędu:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Przyczyna:** Określony Administrator dzierżawy pulpitu wirtualnego systemu Windows nie ma prawidłowego przypisania roli.

**Poprawka:** Użytkownik, który utworzył dzierżawcę pulpitów wirtualnych systemu Windows, musi zalogować się do programu PowerShell Virtual Desktop systemu Windows i przypisać użytkownikowi, który podjął próbę przypisania roli. Jeśli uruchamiasz parametry szablonu Azure Resource Manager GitHub, postępuj zgodnie z tymi instrukcjami przy użyciu poleceń programu PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Błąd: użytkownik wymaga usługi Azure AD Multi-Factor Authentication (MFA)

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu wdrożenia nie powiódł się z powodu braku Multi-Factor Authentication (MFA)](../media/MFARequiredError.png)

Przykład nieprzetworzonego błędu:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Przyczyna:** Określony Administrator dzierżawy usług pulpitu wirtualnego systemu Windows wymaga zalogowania się do usługi Azure AD Multi-Factor Authentication (MFA).

**Poprawka:** Utwórz jednostkę usługi i przypisz ją do swojej dzierżawy pulpitu wirtualnego systemu Windows, wykonując kroki opisane w [samouczku: tworzenie jednostek usługi i przypisań ról przy użyciu programu PowerShell](create-service-principal-role-powershell.md). Po sprawdzeniu, czy możesz zalogować się do pulpitu wirtualnego systemu Windows przy użyciu nazwy głównej usługi, uruchom ponownie ofertę witryny Azure Marketplace lub szablon Azure Resource Manager GitHub, w zależności od używanej metody. Postępuj zgodnie z poniższymi instrukcjami, aby wprowadzić poprawne parametry dla metody.

W przypadku korzystania z oferty portalu Azure Marketplace podaj wartości następujących parametrów, aby prawidłowo uwierzytelniać się na pulpicie wirtualnym systemu Windows:

- Windows Virtual Desktop dzierżawca RDS właściciel: główna usługi
- Identyfikator aplikacji: Identyfikacja aplikacji nowo utworzonej jednostki usługi
- Hasło/Potwierdź hasło: klucz tajny hasła wygenerowany dla jednostki usługi
- Identyfikator dzierżawy usługi Azure AD: Identyfikator dzierżawy usługi Azure AD, który został utworzony

Jeśli uruchamiasz szablon Azure Resource Manager GitHub, podaj wartości następujących parametrów, aby prawidłowo uwierzytelniać się na pulpicie wirtualnym systemu Windows:

- Główna nazwa użytkownika (UPN) administratora dzierżawy lub identyfikator aplikacji: Identyfikator aplikacji utworzonej nowej jednostki usługi
- Hasło administratora dzierżawy: hasło wygenerowane dla jednostki usługi
- IsServicePrincipal: **true**
- AadTenantId: Identyfikator dzierżawy usługi Azure AD, która została utworzona przez użytkownika

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Błąd: podsieć vmSubnet jest niedostępna podczas konfigurowania sieci wirtualnych

**Przyczyna:** W szablonie portalu Windows Virtual Desktop Marketplace interfejs użytkownika wyświetla tylko podsieci, które mają co najmniej tyle adresów IP jako łączną liczbę maszyn wirtualnych określonych w szablonie. Rzeczywista liczba dostępnych adresów IP w podsieci musi być równa liczbie wdrożonych nowych maszyn wirtualnych, ale nie może ona być obliczana przez bieżący interfejs użytkownika.

**Poprawka:** Można określić podsieć z co najmniej tylu adresami IP dostępnymi jako liczba maszyn wirtualnych, które są dodawane przy użyciu interfejsu użytkownika portalu Marketplace, można to zrobić przez określenie nazwy podsieci w parametrze "**existingSubnetName**" podczas [ponownego wdrażania istniejącego wdrożenia](expand-existing-host-pool-2019.md#redeploy-from-azure) lub [wdrożenia przy użyciu podstawowego szablonu ARM z usługi GitHub](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool).

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview-2019.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration-2019.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection-2019.md).
- Aby rozwiązać problemy z Pulpit zdalny klientami, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](../troubleshoot-client.md)
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup-2019.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../../azure-resource-manager/templates/deployment-history.md).