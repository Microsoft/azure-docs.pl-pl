---
title: Wdrażanie zawartości przy użyciu protokołu FTP/S
description: Dowiedz się, jak wdrożyć aplikację w Azure App Service ftp lub FTPS. Zwiększ bezpieczeństwo witryny sieci Web, wyłączając niezaszyfrowany protokół FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: fd856ee47c1100292f7558bb0fa2a1772951a3cb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832373"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Wdrażanie aplikacji w witrynie Azure App Service użyciu protokołu FTP/S

W tym artykule pokazano, jak używać protokołu FTP lub FTPS do wdrażania aplikacji internetowej, zaplecza aplikacji mobilnej lub aplikacji interfejsu API do [Azure App Service](./overview.md).

Punkt końcowy FTP/S aplikacji jest już aktywny. Do włączenia wdrożenia protokołu FTP/S nie jest konieczna żadna konfiguracja.

> [!NOTE]
> Strona **Centrum programowa (klasyczna)** w Azure Portal, która jest starym środowiskom wdrażania, zostanie wycofana w marcu 2021 r. Ta zmiana nie wpłynie na żadne ustawienia wdrażania istniejące w aplikacji i będzie można nadal zarządzać wdrażaniem aplikacji na **stronie Centrum** wdrażania.

## <a name="get-deployment-credentials"></a>Uzyskiwanie poświadczeń wdrożenia

1. Postępuj zgodnie z instrukcjami w te tematu Konfigurowanie poświadczeń wdrożenia dla [Azure App Service,](deploy-configure-credentials.md) aby skopiować poświadczenia zakresu aplikacji lub ustawić poświadczenia zakresu użytkownika. Możesz nawiązać połączenie z punktem końcowym FTP/S aplikacji przy użyciu obu poświadczeń.

1. W zależności od wybranego zakresu poświadczeń należy tworzyć nazwę użytkownika FTP w następującym formacie:

    | Zakres aplikacji | Zakres użytkownika |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    W App Service punkt końcowy FTP/S jest udostępniany między aplikacjami. Ponieważ poświadczenia zakresu użytkownika nie są połączone z określonym zasobem, musisz dowiązać nazwę użytkownika w zakresie użytkownika nazwą aplikacji, jak pokazano powyżej.

## <a name="get-ftps-endpoint"></a>Uzyskiwanie punktu końcowego FTP/S
    
# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Na tej samej stronie zarządzania dla aplikacji, na której skopiowano poświadczenia wdrożenia **(poświadczenia** FTP Centrum wdrażania), skopiuj  >  punkt **końcowy FTPS.**

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom polecenie [az webapp deployment list-publishing-profiles.](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) W poniższym przykładzie użyto [ścieżki JMES do](https://jmespath.org/) wyodrębnienia punktów końcowych FTP/S z danych wyjściowych.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Każda aplikacja ma dwa punkty końcowe FTP/S, jeden jest do odczytu i zapisu, a drugi jest tylko do odczytu (zawiera ) i jest dla `profileName` `ReadOnly` scenariuszy odzyskiwania danych. Aby wdrożyć pliki za pomocą protokołu FTP, skopiuj adres URL punktu końcowego odczytu i zapisu.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uruchom polecenie [Get-AzWebAppPublishingProfile.](/powershell/module/az.websites/get-azwebapppublishingprofile) Poniższy przykład wyodrębnia punkt końcowy FTP/S z danych wyjściowych XML.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Wdrażanie plików na platformie Azure

1. Z klienta FTP (na przykład Visual Studio [,](https://www.visualstudio.com/vs/community/) [Cyberduck](https://cyberduck.io/)lub [WinSCP](https://winscp.net/index.php)) użyj zebranych informacji o połączeniu w celu nawiązania połączenia z aplikacją.
2. Skopiuj pliki i ich odpowiednią strukturę katalogów do [ **katalogu /site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) na platformie Azure (lub katalogu **/site/wwwroot/App_Data/Jobs/** dla zadań WebJob).
3. Przejdź do adresu URL aplikacji, aby sprawdzić, czy aplikacja działa prawidłowo. 

> [!NOTE] 
> W [przeciwieństwie do wdrożeń opartych na](deploy-local-git.md) usłudze Git i wdrożenia zip wdrożenie FTP nie obsługuje automatyzacji kompilacji, na przykład: [](deploy-zip.md) 
>
> - przywracanie zależności (na przykład automatyzacje NuGet, NPM, PIP i Composer)
> - kompilacja plików binarnych .NET
> - generowanie web.config (oto przykład [Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Wygeneruj te niezbędne pliki ręcznie na komputerze lokalnym, a następnie wd wdrażaj je razem z aplikacją.
>

## <a name="enforce-ftps"></a>Wymuszanie protokołu FTPS

W celu zwiększenia bezpieczeństwa należy zezwolić tylko na połączenia FTP za pośrednictwem protokołu TLS/SSL. Możesz również wyłączyć zarówno protokół FTP, jak i FTPS, jeśli nie korzystasz z wdrożenia ftp.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. Na stronie zasobów aplikacji w witrynie [Azure Portal](https://portal.azure.com)wybierz pozycję **Ustawienia**  >  **ogólne konfiguracji w** lewym panelu nawigacyjnym.

2. Aby wyłączyć nieszyfrowany protokół FTP, wybierz pozycję **FTPS Only** in FTP state (Tylko FTP w **stanie FTP).** Aby całkowicie wyłączyć zarówno protokół FTP, jak i FTPS, wybierz pozycję **Wyłączone.** Po skończeniu kliknij przycisk **Zapisz**. Jeśli używasz **tylko protokołu FTPS,** musisz wymusić protokół TLS 1.2 lub wyższy, przechodząc do bloku ustawień **protokołu TLS/SSL** aplikacji internetowej. Protokoły TLS 1.0 i 1.1 nie są obsługiwane tylko **przez protokół FTPS.**

    ![Wyłączanie protokołu FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom polecenie [az webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) z `--ftps-state` argumentem .

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Możliwe wartości dla `--ftps-state` są `AllAllowed` (FTP i FTPS włączone), `Disabled` (FTP i FTPs wyłączone) i `FtpsOnly` (tylko FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uruchom polecenie [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) z `-FtpsState` parametrem .

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Możliwe wartości dla `--ftps-state` są `AllAllowed` (FTP i FTPS włączone), `Disabled` (FTP i FTPs wyłączone) i `FtpsOnly` (tylko FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Rozwiązywanie problemów z wdrażaniem protokołu FTP

- [Jak rozwiązywać problemy z wdrażaniem protokołu FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Nie mogę na przykład korzystać z protokołu FTP i publikować kodu. Jak mogę rozwiązać ten problem?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak połączyć się z protokołem FTP w trybie Azure App Service trybie pasywnym?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak rozwiązywać problemy z wdrażaniem protokołu FTP?

Pierwszym krokiem rozwiązywania problemów z wdrożeniem protokołu FTP jest odizolowanie problemu z wdrożeniem od problemu z aplikacją środowiska uruchomieniowego.

Problem z wdrażaniem zwykle powoduje brak plików lub nieprawidłowe pliki wdrożone w aplikacji. Możesz rozwiązywać problemy, badając wdrożenie ftp lub wybierając alternatywną ścieżkę wdrożenia (na przykład kontrolę źródła).

Problem z aplikacją środowiska uruchomieniowego zwykle skutkuje odpowiednim zestawem plików wdrożonych w aplikacji, ale nieprawidłowym zachowaniem aplikacji. Rozwiązywanie problemów można rozwiązać, koncentrując się na zachowaniu kodu w czasie wykonywania i badając konkretne ścieżki błędów.

Aby ustalić problem z wdrożeniem lub środowiskiem uruchomieniowym, zobacz [Deployment vs. runtime issues (Problemy ze wdrożeniem a środowiskiem uruchomieniowym).](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I'm not able to FTP and publish my code. Jak mogę rozwiązać ten problem?
Sprawdź, czy wprowadzono poprawną [nazwę hosta](#get-ftps-endpoint) i [poświadczenia](#get-deployment-credentials). Sprawdź również, czy następujące porty FTP na maszynie nie są blokowane przez zaporę:

- Port połączenia sterowania FTP: 21, 990
- Port połączenia danych FTP: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak połączyć się z protokołem FTP w trybie Azure App Service trybie pasywnym?
Azure App Service połączenie za pośrednictwem trybu aktywnego i pasywnego. Preferowany jest tryb pasywny, ponieważ maszyny wdrażania znajdują się zwykle za zaporą (w systemie operacyjnym lub jako część sieci domowej lub biznesowej). Zobacz przykład [z dokumentacji winSCP.](https://winscp.net/docs/ui_login_connection) 

## <a name="more-resources"></a>Więcej zasobów

* [Lokalne wdrożenie usługi Git w Azure App Service](deploy-local-git.md)
* [Azure App Service wdrażania](deploy-configure-credentials.md)
* [Przykład: tworzenie aplikacji internetowej i wdrażanie plików przy użyciu protokołu FTP (interfejs wiersza polecenia platformy Azure).](./scripts/cli-deploy-ftp.md)
* [Przykład: przekazywanie plików do aplikacji internetowej przy użyciu protokołu FTP (PowerShell).](./scripts/powershell-deploy-ftp.md)
