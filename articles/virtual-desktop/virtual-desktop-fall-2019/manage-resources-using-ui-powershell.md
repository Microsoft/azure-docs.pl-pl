---
title: Wdrażanie narzędzia do zarządzania dla pulpitu wirtualnego systemu Windows (klasycznego) przy użyciu nazwy głównej usługi platformy Azure
description: Jak wdrożyć narzędzie do zarządzania dla pulpitu wirtualnego systemu Windows (klasyczny) przy użyciu programu PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: lizross
ms.openlocfilehash: d7219751d584eb458cded9f4e30cccb1439dfa1b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89069021"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>Wdrażanie narzędzia do zarządzania pulpitem wirtualnym systemu Windows (klasycznego) przy użyciu programu PowerShell

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows.

W tym artykule przedstawiono sposób wdrażania narzędzia do zarządzania przy użyciu programu PowerShell.

## <a name="important-considerations"></a>Istotne zagadnienia

Każda subskrypcja dzierżawy usługi Azure Active Directory (Azure AD) wymaga oddzielnego wdrożenia narzędzia do zarządzania. To narzędzie nie obsługuje scenariuszy usługi Azure AD Business-to-Business (B2B).

To narzędzie do zarządzania jest przykładem. Firma Microsoft zapewni ważne aktualizacje zabezpieczeń i ich jakości. [Kod źródłowy jest dostępny w](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)serwisie GitHub. Bez względu na to, czy jesteś klientem, czy partnerem, zachęcamy do dostosowania narzędzia, aby zaspokoić potrzeby biznesowe.

Następujące przeglądarki są zgodne z narzędziem do zarządzania:

- Google Chrome 68 lub nowsza
- Microsoft Edge 40,15063 lub nowsza
- Mozilla Firefox 52,0 lub nowsza
- Przeglądarka Safari 10 lub nowsza (tylko macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Co jest potrzebne do wdrożenia narzędzia do zarządzania

Przed wdrożeniem narzędzia do zarządzania musisz mieć Azure Active Directory użytkownika (Azure AD), aby utworzyć rejestrację aplikacji i wdrożyć interfejs użytkownika zarządzania. Ten użytkownik musi:

- Masz uprawnienia do tworzenia zasobów w ramach subskrypcji platformy Azure
- Masz uprawnienia do tworzenia aplikacji usługi Azure AD. Wykonaj następujące kroki, aby sprawdzić, czy użytkownik ma wymagane uprawnienia, postępując zgodnie z instrukcjami w obszarze [wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Po wdrożeniu i skonfigurowaniu narzędzia do zarządzania zalecamy poproszenie użytkownika o uruchomienie interfejsu użytkownika zarządzania, aby upewnić się, że wszystko działa. Użytkownik, który uruchamia interfejs użytkownika zarządzania, musi mieć przypisanie roli, które umożliwia im wyświetlanie lub edytowanie dzierżawy pulpitów wirtualnych systemu Windows.

## <a name="set-up-powershell"></a>Konfigurowanie programu PowerShell

Zacznij od zalogowania się do modułów AZ i Azure AD PowerShell. Oto jak się zalogować:

1. Otwórz program PowerShell jako administrator i przejdź do katalogu, w którym zapisano skrypty programu PowerShell.
2. Zaloguj się do platformy Azure przy użyciu konta, które ma uprawnienia właściciela lub współautora w ramach subskrypcji platformy Azure, która ma zostać użyta do utworzenia narzędzia do zarządzania, uruchamiając następujące polecenie cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Uruchom następujące polecenie cmdlet, aby zalogować się do usługi Azure AD przy użyciu tego samego konta, którego użyto w module AZ PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. Następnie przejdź do folderu, w którym zapisano dwa skrypty programu PowerShell z repozytorium usługi GitHub RDS-Templates.

Pozostaw okno programu PowerShell użyte do logowania się w celu uruchomienia dodatkowych poleceń cmdlet programu PowerShell podczas logowania.

## <a name="create-an-azure-active-directory-app-registration"></a>Tworzenie Azure Active Directory rejestracji aplikacji

W celu pomyślnego wdrożenia i skonfigurowania narzędzia do zarządzania należy najpierw pobrać następujące skrypty programu PowerShell z [repozytorium RDS-templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

Uruchom następujące polecenia, aby utworzyć rejestrację aplikacji z wymaganymi uprawnieniami interfejsu API:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Po zakończeniu rejestracji aplikacji usługi Azure AD można wdrożyć narzędzie do zarządzania programu.

## <a name="deploy-the-management-tool"></a>Wdrażanie narzędzia do zarządzania

Uruchom następujące polecenia programu PowerShell, aby wdrożyć narzędzie do zarządzania i skojarzyć je z właśnie utworzoną jednostką usługi:

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Po utworzeniu aplikacji sieci Web należy dodać identyfikator URI przekierowania do aplikacji usługi Azure AD w celu pomyślnego zalogowania użytkowników.

## <a name="set-the-redirect-uri"></a>Ustaw identyfikator URI przekierowania

Uruchom następujące polecenia programu PowerShell, aby pobrać adres URL aplikacji sieci Web i ustawić go jako identyfikator URI przekierowania uwierzytelniania (nazywany również adresem URL odpowiedzi):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

Teraz, po dodaniu identyfikatora URI przekierowania, należy najpierw zaktualizować adres URL interfejsu API, dzięki czemu narzędzie do zarządzania może współdziałać z usługą zaplecza API.

## <a name="update-the-api-url-for-the-web-application"></a>Zaktualizuj adres URL interfejsu API dla aplikacji sieci Web

Uruchom następujący skrypt, aby zaktualizować konfigurację adresu URL interfejsu API w frontonie aplikacji sieci Web:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Teraz, gdy aplikacja internetowa narzędzia do zarządzania została w pełni skonfigurowana, czas na zweryfikowanie aplikacji usługi Azure AD i zapewnienie zgody.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Weryfikowanie aplikacji usługi Azure AD i wyrażanie zgody

Aby sprawdzić konfigurację aplikacji usługi Azure AD i zapewnić zgodę:

1. Otwórz przeglądarkę internetową i zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administracyjnego.
2. Na pasku wyszukiwania w górnej części Azure Portal Wyszukaj pozycję **rejestracje aplikacji** i wybierz element w obszarze **usługi**.
3. Wybierz pozycję **wszystkie aplikacje** i Wyszukaj unikatową nazwę aplikacji podaną dla skryptu programu PowerShell w temacie [Tworzenie Azure Active Directory rejestracji aplikacji](#create-an-azure-active-directory-app-registration).
4. W panelu po lewej stronie przeglądarki wybierz pozycję **uwierzytelnianie** i upewnij się, że identyfikator URI przekierowania jest taki sam jak adres URL aplikacji sieci Web dla narzędzia do zarządzania, jak pokazano na poniższej ilustracji.

   [![Strona uwierzytelnianie z wprowadzonym identyfikatorem URI ](../media/management-ui-redirect-uri-inline.png) przekierowania](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. W lewym panelu wybierz pozycję **uprawnienia interfejsu API** , aby potwierdzić, że uprawnienia zostały dodane. Jeśli jesteś administratorem globalnym, wybierz przycisk **Udziel zgody przez administratora dla `tenantname`** przycisku i postępuj zgodnie z instrukcjami wyświetlanymi w oknie dialogowym, aby zapewnić zgodę administratora na organizację.

    [![Strona ](../media/management-ui-permissions-inline.png) uprawnień interfejsu API](../media/management-ui-permissions-expanded.png#lightbox)

Teraz możesz zacząć korzystać z narzędzia do zarządzania.

## <a name="use-the-management-tool"></a>Korzystanie z narzędzia do zarządzania

Teraz, po skonfigurowaniu narzędzia do zarządzania w dowolnym momencie, możesz je uruchomić w dowolnym miejscu. Poniżej przedstawiono sposób uruchomienia narzędzia:

1. Otwórz adres URL aplikacji sieci Web w przeglądarce internetowej. Jeśli nie pamiętasz adresu URL, możesz zalogować się do platformy Azure, znaleźć usługę App Service wdrożoną dla narzędzia do zarządzania, a następnie wybrać adres URL.
2. Zaloguj się przy użyciu poświadczeń pulpitu wirtualnego systemu Windows.

   > [!NOTE]
   > Jeśli nie przyznano zgody administratora podczas konfigurowania narzędzia do zarządzania, każdy użytkownik, który się zaloguje, będzie musiał podać własną zgodę użytkownika w celu korzystania z tego narzędzia.

3. Po wyświetleniu monitu o wybranie grupy dzierżawców wybierz z listy rozwijanej pozycję **Domyślna grupa dzierżawców** .
4. Po wybraniu **domyślnej grupy dzierżawców** menu powinno być wyświetlane po lewej stronie okna. W tym menu Znajdź nazwę grupy dzierżawców i wybierz ją.

   > [!NOTE]
   > Jeśli masz niestandardową grupę dzierżawców, wprowadź nazwę ręcznie zamiast wybierać ją z listy rozwijanej.

## <a name="report-issues"></a>Zgłaszanie problemów

Jeśli występują problemy z narzędziem do zarządzania lub innymi narzędziami pulpitu wirtualnego systemu Windows, postępuj zgodnie z instrukcjami w temacie [Azure Resource Manager templates for usługi pulpitu zdalnego](https://github.com/Azure/RDS-Templates/blob/master/README.md) , aby zgłosić je w serwisie GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wdrożyć narzędzie do zarządzania i połączyć się z nim, możesz dowiedzieć się, jak używać Azure Service Health do monitorowania problemów z usługami i klasyfikatorów kondycji. Aby dowiedzieć się więcej, zobacz [Samouczek dotyczący konfigurowania alertów usługi](set-up-service-alerts-2019.md).
