---
title: Wdrażanie zawartości przy użyciu protokołu FTP/S
description: Dowiedz się, jak wdrożyć aplikację w Azure App Service przy użyciu protokołu FTP lub FTPS. Zwiększ bezpieczeństwo witryny sieci Web, wyłączając nieszyfrowany protokół FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045806"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Wdrażanie aplikacji do Azure App Service przy użyciu protokołu FTP/S

W tym artykule pokazano, jak za pomocą protokołu FTP lub FTPS wdrożyć aplikację internetową, zaplecze aplikacji mobilnej lub aplikację interfejsu API w celu [Azure App Service](./overview.md).

Punkt końcowy FTP/S aplikacji jest już aktywny. W celu włączenia wdrożenia protokołu FTP/S nie jest wymagana żadna konfiguracja.

> [!NOTE]
> Strona **centrum deweloperskiego (klasyczna)** w Azure Portal, która jest starym środowiskiem wdrażania, będzie przestarzała w marcu 2021. Ta zmiana nie wpłynie na żadne istniejące ustawienia wdrożenia w aplikacji. można nadal zarządzać wdrażaniem aplikacji na stronie **centrum wdrażania** .

## <a name="get-deployment-credentials"></a>Pobierz poświadczenia wdrażania

1. Postępuj zgodnie z instrukcjami w obszarze [Konfigurowanie poświadczeń wdrażania dla Azure App Service](deploy-configure-credentials.md) , aby skopiować poświadczenia zakresu aplikacji lub ustawić poświadczenia zakresu użytkownika. Możesz połączyć się z punktem końcowym FTP/S aplikacji przy użyciu dowolnego poświadczenia.

1. Utwórz nazwę użytkownika FTP w następującym formacie, w zależności od wybranego zakresu poświadczeń:

    | Zakres aplikacji | Zakres użytkownika |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    W App Service punkt końcowy FTP/S jest współużytkowany między aplikacjami. Ponieważ poświadczenia zakresu użytkownika nie są połączone z określonym zasobem, należy najpierw dołączać nazwę użytkownika zakresu użytkowników z nazwą aplikacji, jak pokazano powyżej.

## <a name="get-ftps-endpoint"></a>Pobierz punkt końcowy FTP/S
    
# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Na tej samej stronie zarządzania dla Twojej aplikacji, w której skopiowano poświadczenia wdrożenia (poświadczenia usługi wdrażania w programie **Deployment Center**  >  ), skopiuj **punkt końcowy FTPS**.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom polecenie [AZ webapp Deployment list-Publishing-profile](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . Poniższy przykład używa [ścieżki JMES](https://jmespath.org/) , aby wyodrębnić punkty końcowe FTP/S z danych wyjściowych.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Każda aplikacja ma dwa punkty końcowe FTP/S, jeden jest do odczytu i zapisu, a drugi jest tylko do odczytu ( `profileName` zawiera `ReadOnly` ) i jest przeznaczony do scenariuszy odzyskiwania danych. Aby wdrożyć pliki przy użyciu protokołu FTP, skopiuj adres URL punktu końcowego do odczytu i zapisu.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uruchom polecenie [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . Poniższy przykład wyodrębnia punkt końcowy FTP/S z danych wyjściowych XML.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Wdrażanie plików na platformie Azure

1. Korzystając z klienta FTP (na przykład [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)lub [WinSCP](https://winscp.net/index.php)), użyj zebranych informacji o połączeniu, aby nawiązać połączenie z Twoją aplikacją.
2. Skopiuj pliki i ich strukturę katalogów do [katalogu **/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) na platformie Azure (lub **/site/wwwroot/App_Data/Jobs/** Directory for WebJobs).
3. Przejdź do adresu URL swojej aplikacji, aby sprawdzić, czy aplikacja działa prawidłowo. 

> [!NOTE] 
> W przeciwieństwie do [wdrożeń opartych na git](deploy-local-git.md) i [wdrożenia zip](deploy-zip.md), wdrożenie FTP nie obsługuje automatyzacji kompilacji, na przykład: 
>
> - Przywracanie zależności (takie jak NuGet, NPM, PIP i Automation Composer)
> - Kompilacja plików binarnych platformy .NET
> - Generowanie web.config (Oto [ przykładNode.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Ręcznie Wygeneruj te wymagane pliki na komputerze lokalnym, a następnie wdróż je razem z aplikacją.
>

## <a name="enforce-ftps"></a>Wymuś FTPS

Aby zapewnić większe bezpieczeństwo, należy zezwalać tylko na protokół FTP przez protokół TLS/SSL. Jeśli nie korzystasz z wdrażania FTP, możesz również wyłączyć zarówno protokół FTP, jak i FTPS.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. Na stronie zasobów aplikacji w obszarze [Azure Portal](https://portal.azure.com)wybierz pozycję **Konfiguracja**  >  **Ogólne ustawienia** w lewym okienku nawigacji.

2. Aby wyłączyć nieszyfrowane FTP, wybierz pozycję **FTPS tylko** w polu **stan FTP**. Aby całkowicie wyłączyć protokół FTP i FTPS, wybierz pozycję **wyłączone**. Po skończeniu kliknij przycisk **Zapisz**. W przypadku używania **tylko FTPS** należy wymusić TLS 1,2 lub nowszy, przechodząc do bloku **Ustawienia protokołu TLS/SSL** w aplikacji sieci Web. Protokoły TLS 1,0 i 1,1 nie są obsługiwane **tylko** w przypadku FTPS.

    ![Wyłącz FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Uruchom polecenie [AZ webapp config Set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) z `--ftps-state` argumentem.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Możliwe wartości `--ftps-state` to `AllAllowed` (FTP i FTPS Enabled), `Disabled` (FTP i FTPS disabled) i `FtpsOnly` (tylko FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uruchom polecenie [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) z `-FtpsState` parametrem.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Możliwe wartości `--ftps-state` to `AllAllowed` (FTP i FTPS Enabled), `Disabled` (FTP i FTPS disabled) i `FtpsOnly` (tylko FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Rozwiązywanie problemów z wdrażaniem FTP

- [Jak rozwiązywać problemy z wdrażaniem FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Nie mogę przeprowadzić FTP i opublikować mojego kodu. Jak mogę rozwiązać ten problem?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak połączyć się z FTP w Azure App Service za pośrednictwem trybu pasywnego?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak rozwiązywać problemy z wdrażaniem FTP?

Pierwszym krokiem w przypadku rozwiązywania problemów z wdrażaniem za pomocą usługi FTP jest izolowanie problemu wdrażania z powodu problemu z aplikacją środowiska uruchomieniowego.

Problem z wdrożeniem zwykle skutkuje brakiem plików lub nieprawidłowymi plikami wdrożonymi w aplikacji. Możesz rozwiązywać problemy, badając wdrożenie FTP lub wybierając alternatywną ścieżkę wdrożenia (np. kontrolę źródła).

Problem z aplikacją środowiska uruchomieniowego zwykle polega na odpowiednim zestawie plików wdrożonym w aplikacji, ale nieprawidłowym zachowaniu aplikacji. Możesz rozwiązywać problemy, koncentrując się na zachowaniu kodu w czasie wykonywania i badając konkretne ścieżki błędów.

Aby określić problem dotyczący wdrożenia lub środowiska uruchomieniowego, zobacz [problemy dotyczące wdrażania i środowiska uruchomieniowego](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I'm not able to FTP and publish my code. Jak mogę rozwiązać ten problem?
Sprawdź, czy wprowadzono poprawną [nazwę hosta](#get-ftps-endpoint) i [poświadczenia](#get-deployment-credentials). Sprawdź również, czy następujące porty FTP na maszynie nie są blokowane przez zaporę:

- Port połączenia sterowania FTP: 21, 990
- Port połączenia danych FTP: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak połączyć się z FTP w Azure App Service za pośrednictwem trybu pasywnego?
Azure App Service obsługuje łączenie za pośrednictwem trybu aktywnego i pasywnego. Tryb pasywny jest preferowany, ponieważ maszyny wdrażania zwykle znajdują się za zaporą (w systemie operacyjnym lub w ramach sieci domowej lub firmowej). Zapoznaj się [z przykładem z dokumentacji WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>Więcej zasobów

* [Lokalne wdrożenie narzędzia Git do Azure App Service](deploy-local-git.md)
* [Poświadczenia wdrażania Azure App Service](deploy-configure-credentials.md)
* [Przykład: Tworzenie aplikacji internetowej i wdrażanie plików za pomocą protokołu FTP (interfejs wiersza polecenia platformy Azure)](./scripts/cli-deploy-ftp.md).
* [Przykład: przekazywanie plików do aplikacji sieci Web przy użyciu protokołu FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).
