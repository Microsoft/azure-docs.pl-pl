---
title: Tworzenie puli hostów środowiska pulpitu wirtualnego systemu Windows — Azure
description: Rozwiązywanie problemów i rozwiązywanie problemów z pulą dzierżaw i hostów podczas instalacji środowiska pulpitu wirtualnego systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c31a32b32a685087c53198ec52af1188d0071cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652449"
---
# <a name="host-pool-creation"></a>Tworzenie puli hostów

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md).

W tym artykule opisano problemy podczas wstępnej instalacji dzierżawy usług pulpitu wirtualnego systemu Windows i infrastruktury puli powiązanych hostów sesji.

## <a name="provide-feedback"></a>Wyraź opinię

Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Uzyskiwanie obrazu wielosesyjnego dla systemu Windows 10 Enterprise

Aby użyć obrazu wielosesyjnego dla systemu Windows 10 Enterprise, przejdź do witryny Azure Marketplace, wybierz pozycję **wprowadzenie**  >  **Microsoft Windows 10** > i [wielosesyjne Windows 10 Enterprise, wersja 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Problemy z używaniem Azure Portal do tworzenia pul hostów

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Błąd: "utwórz bezpłatne konto" pojawia się podczas uzyskiwania dostępu do usługi

> [!div class="mx-imgBorder"]
> ![Obraz przedstawiający Azure Portal wyświetlania komunikatu "Tworzenie bezpłatnego konta"](media/create-new-account.png)

**Przyczyna**: nie ma aktywnych subskrypcji na koncie, które zostało zalogowane na platformie Azure za pomocą usługi, lub że konto nie ma uprawnień do wyświetlania subskrypcji.

**Poprawka**: Zaloguj się do subskrypcji, w której zostaną wdrożone maszyny wirtualne hosta sesji przy użyciu konta, które ma co najmniej dostęp na poziomie współautora.

### <a name="error-exceeding-quota-limit"></a>Błąd: "przekraczanie limitu przydziału"

Jeśli operacja przekracza limit przydziału, można wykonać jedną z następujących czynności:

- Utwórz nową pulę hostów z takimi samymi parametrami, ale mniejszą liczbę rdzeni maszyn wirtualnych i maszyn wirtualnych.

- Otwórz link widoczny w polu statusMessage w przeglądarce, aby przesłać żądanie zwiększenia przydziału subskrypcji platformy Azure dla określonej jednostki SKU maszyny wirtualnej.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Błąd: nie można wyświetlić przypisań użytkowników w grupach aplikacji.

**Przyczyna**: ten błąd występuje zwykle po przeniesieniu subskrypcji z 1 Azure Active Directory (AD) dzierżawy do innej. Jeśli stare przypisania są nadal powiązane ze starą dzierżawą usługi Azure AD, Azure Portal utraci ich śledzenie.

**Poprawka**: należy ponownie przypisać użytkowników do grup aplikacji.

### <a name="i-only-see-us-when-setting-the-location-for-my-service-objects"></a>Po ustawieniu lokalizacji dla obiektów usługi są wyświetlane tylko stany USA

**Przyczyna**: platforma Azure nie obsługuje obecnie tego regionu dla usługi pulpitu wirtualnego systemu Windows. Aby dowiedzieć się, które lokalizacje geograficzne obsługuje, sprawdź [lokalizacje danych](data-locations.md). Jeśli pulpit wirtualny systemu Windows obsługuje lokalizację, ale nadal nie jest wyświetlany podczas próby wybrania lokalizacji, oznacza to, że dostawca zasobów nie został jeszcze zaktualizowany.

**Poprawka**: Aby uzyskać najnowszą listę regionów, należy ponownie zarejestrować dostawcę zasobów:

1. Przejdź do pozycji **subskrypcje** i wybierz odpowiednią subskrypcję.
2. Wybierz pozycję **dostawca zasobów**.
3. Wybierz pozycję **Microsoft. DesktopVirtualization**, a następnie wybierz pozycję **zarejestruj ponownie** z menu Akcja.

Po ponownym zarejestrowaniu dostawcy zasobów nie będą wyświetlane żadne informacje zwrotne interfejsu użytkownika ani aktualizacje. Proces ponownej rejestracji nie zakłóca również istniejących środowisk.

## <a name="azure-resource-manager-template-errors"></a>Błędy szablonu Azure Resource Manager

Postępuj zgodnie z poniższymi instrukcjami, aby rozwiązywać problemy z niepowodzeniem wdrożenia szablonów Azure Resource Manager i DSC programu PowerShell.

1. Przejrzyj błędy we wdrożeniu, korzystając [z opcji Wyświetl operacje wdrażania z Azure Resource Manager](../azure-resource-manager/templates/deployment-history.md).
2. Jeśli wdrożenie nie zawiera żadnych błędów, Przejrzyj błędy w dzienniku aktywności, korzystając [z widoku dzienniki aktywności do inspekcji akcji na zasobach](../azure-resource-manager/management/view-activity-logs.md).
3. Po zidentyfikowaniu tego błędu Użyj komunikatu o błędzie i zasobów w [rozwiązywaniu typowych błędów wdrażania platformy Azure z Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md) , aby rozwiązać problem.
4. Usuń wszystkie zasoby utworzone w poprzednim wdrożeniu i ponów próbę wdrożenia szablonu.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Błąd: wdrożenie nie powiodło się.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu wdrożenia nie powiódł się.](media/failure-joindomain.png)

Przykład nieprzetworzonego błędu:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Przyczyna 1:** Poświadczenia podane w celu dołączania maszyn wirtualnych do domeny są nieprawidłowe.

**Poprawka 1:** Zobacz błąd "Nieprawidłowe poświadczenia" dla maszyn wirtualnych nie są przyłączone do domeny w [konfiguracji maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).

**Przyczyna 2:** Nie rozpoznano nazwy domeny.

**Poprawka 2:** Zobacz [błąd: nazwa domeny nie jest rozpoznawana](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) w [konfiguracji maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).

**Przyczyna 3:** Konfiguracja DNS sieci wirtualnej (VNET) jest ustawiona na **wartość domyślna**.

Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Otwórz Azure Portal i przejdź do karty **sieci wirtualne** .
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
> ![Zrzut ekranu wdrożenia nie powiódł się z powodu niepowodzenia inicjowania obsługi administracyjnej.](media/failure-vmextensionprovisioning.png)

**Przyczyna 1:** Błąd przejściowy ze środowiskiem pulpitu wirtualnego systemu Windows.

**Przyczyna 2:** Błąd przejściowy z połączeniem.

**Poprawka:** Potwierdź, że środowisko pulpitu wirtualnego systemu Windows jest w dobrej kondycji, logując się przy użyciu programu PowerShell. Ręcznie Zakończ rejestrację maszyny wirtualnej w obszarze [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Błąd: Określona nazwa użytkownika administratora jest niedozwolona

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu wdrożenia nie powiódł się, gdy określony administrator nie jest dozwolony.](media/failure-username.png)

Przykład nieprzetworzonego błędu:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Przyczyna:** Podane hasło zawiera zabronione podciągi (administrator, administrator, root).

**Poprawka:** Zaktualizuj nazwę użytkownika lub Użyj różnych użytkowników.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Błąd: Maszyna wirtualna zgłosiła błąd podczas przetwarzania rozszerzenia

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający operację zasobu zakończony ze stanem inicjowania obsługi terminalu we wdrożeniu nie powiódł się.](media/failure-processing.png)

Przykład nieprzetworzonego błędu:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Przyczyna:** Rozszerzenie DSC programu PowerShell nie mogło uzyskać dostępu administratora na maszynie wirtualnej.

**Poprawka:** Upewnij się, że nazwa użytkownika i hasło mają dostęp administracyjny do maszyny wirtualnej, a następnie ponownie uruchom szablon Azure Resource Manager.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Błąd: DeploymentFailed — Konfiguracja DSC programu PowerShell "FirstSessionHost" została ukończona z błędami

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu wdrożenia nie powiodło się, ponieważ Konfiguracja DSC programu PowerShell "FirstSessionHost" została ukończona z błędami.](media/failure-dsc.png)

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

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Błąd: nie można usunąć hosta sesji z puli hostów po usunięciu maszyny wirtualnej

**Przyczyna:** Należy usunąć hosta sesji przed usunięciem maszyny wirtualnej.

**Poprawka:** Umieść hosta sesji w trybie opróżniania, wyloguj wszystkich użytkowników z hosta sesji, a następnie Usuń hosta.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Rozwiązywanie problemów związanych z agentem pulpitu wirtualnego systemu Windows lub łącznością sesji można znaleźć w temacie [Rozwiązywanie typowych problemów z systemem Windows Virtual Desktop Agent](troubleshoot-agent.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z Pulpit zdalny klientami, zobacz [Rozwiązywanie problemów z klientem pulpit zdalny](troubleshoot-client.md)
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
