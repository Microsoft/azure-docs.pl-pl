---
title: Wdrażanie zawartości przy użyciu protokołu FTP/S
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service przy użyciu protokołu FTP lub FTPS. Zwiększ bezpieczeństwo witryny sieci Web, wyłączając niezaszyfrowany protokół FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fcc7c5b8fa182cace6e3dae0b1cae4cd41c5dcb9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532597"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Wdrażanie aplikacji w usłudze Azure App Service przy użyciu protokołu FTP/S

W tym artykule pokazano, jak za pomocą protokołu FTP lub FTPS wdrożyć aplikację sieci web, zaplecze aplikacji mobilnej lub aplikację interfejsu API w [usłudze Azure App Service.](https://go.microsoft.com/fwlink/?LinkId=529714)

Punkt końcowy FTP/S dla aplikacji jest już aktywny. Do włączenia wdrażania ftp/s nie jest konieczna żadna konfiguracja.

## <a name="open-ftp-dashboard"></a>Otwórz pulpit nawigacyjny FTP

1. W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz **pozycję Usługi aplikacji**.

    ![Wyszukaj usługi aplikacji.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Wybierz aplikację sieci Web, którą chcesz wdrożyć.

    ![Wybierz aplikację.](media/app-service-continuous-deployment/select-your-app.png)

3. Wybierz **pozycję Centrum** > wdrażania**ftp** > **dashboard**.

    ![Otwórz pulpit nawigacyjny FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Uzyskaj informacje o połączeniu FTP

Na pulpicie nawigacyjnym FTP wybierz pozycję **Kopiuj,** aby skopiować punkt końcowy FTPS i poświadczenia aplikacji.

![Kopiowanie informacji FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Zaleca się, aby użyć **poświadczeń aplikacji** do wdrożenia w aplikacji, ponieważ jest unikatowa dla każdej aplikacji. Jeśli jednak klikniesz **pozycję Poświadczenia użytkownika,** można ustawić poświadczenia na poziomie użytkownika, których można użyć do logowania FTP/S do wszystkich aplikacji usługi App Service w ramach subskrypcji.

> [!NOTE]
> Uwierzytelnianie w punkcie końcowym FTP/FTPS przy użyciu numerów poświadczeń na poziomie użytkownika wymaga nazwy użytkownika w następującym formacie: 
>
>`<app-name>\<user-name>`
>
> Ponieważ poświadczenia na poziomie użytkownika są połączone z użytkownikiem, a nie z określonym zasobem, nazwa użytkownika musi być w tym formacie, aby skierować akcję logowania do odpowiedniego punktu końcowego aplikacji.
>

## <a name="deploy-files-to-azure"></a>Wdrażanie plików na platformie Azure

1. Z klienta FTP (na przykład [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)lub [WinSCP](https://winscp.net/index.php)) użyj informacji o połączeniu zebranych w celu połączenia się z aplikacją.
2. Skopiuj pliki i ich odpowiednią strukturę katalogów do katalogu [ **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) na platformie Azure (lub **katalogu /site/wwwroot/App_Data/Jobs/** dla webjobs).
3. Przejdź do adresu URL aplikacji, aby sprawdzić, czy aplikacja działa prawidłowo. 

> [!NOTE] 
> W przeciwieństwie do [wdrożeń opartych na git,](deploy-local-git.md)wdrożenie FTP nie obsługuje następujących automatyzacji wdrażania: 
>
> - przywraca zależności (takich jak NuGet, NPM, PIP i automatyzacji kompozytora)
> - kompilacja plików binarnych .NET
> - generowanie web.config (oto [przykład Node.js)](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Wygeneruj te niezbędne pliki ręcznie na komputerze lokalnym, a następnie wdrożyć je razem z aplikacją.
>

## <a name="enforce-ftps"></a>Wymuszanie FTPS

Aby zwiększyć bezpieczeństwo, należy zezwolić tylko na protokół FTP za tl/SSL. Można również wyłączyć zarówno FTP, jak i FTPS, jeśli nie używasz wdrożenia FTP.

Na stronie zasobów aplikacji w [witrynie Azure portal](https://portal.azure.com)wybierz**pozycję Ustawienia ogólne** **konfiguracji** > z lewej strony nawigacji.

Aby wyłączyć niezaszyfrowany protokół FTP, wybierz pozycję **FTPS Only** w **stanie FTP**. Aby całkowicie wyłączyć ftp i FTPS, wybierz **wyłączone**. Po skończeniu kliknij przycisk **Zapisz**. Jeśli **używasz tylko protokołu FTPS,** należy wymusić protokół TLS 1.2 lub nowszą, przechodząc do bloku **ustawień TLS/SSL** aplikacji sieci web. Protokół TLS 1.0 i 1.1 nie są obsługiwane tylko przez **protokół FTPS**.

![Wyłączanie ftp/s](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

W przypadku wdrażania protokołu FTP przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure)zobacz Tworzenie aplikacji sieci web i [wdrażanie plików za pomocą protokołu FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

W przypadku wdrażania protokołu FTP przy użyciu [programu Azure PowerShell](/cli/azure)zobacz [Przekazywanie plików do aplikacji sieci Web przy użyciu protokołu FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Rozwiązywanie problemów z wdrażaniem ftp

- [Jak rozwiązać problem z wdrożeniem ftp?](#how-can-i-troubleshoot-ftp-deployment)
- [Nie jestem w stanie FTP i opublikować mój kod. Jak rozwiązać ten problem?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak połączyć się z ftp w usłudze Azure App Service w trybie pasywnym?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak rozwiązać problem z wdrożeniem ftp?

Pierwszym krokiem rozwiązywania problemów z wdrażaniem protokołu FTP jest izolowanie problemu z wdrażaniem z powodu problemu z aplikacją środowiska wykonawczego.

Problem z wdrażaniem zazwyczaj powoduje, że żadne pliki lub nieprawidłowe pliki wdrożone w aplikacji. Rozwiązywanie problemów można rozwiązać, badając wdrożenie FTP lub wybierając alternatywną ścieżkę wdrażania (taką jak kontrola źródła).

Problem z aplikacją środowiska wykonawczego zazwyczaj powoduje odpowiedni zestaw plików wdrożonych w aplikacji, ale nieprawidłowe zachowanie aplikacji. Można rozwiązać problem, koncentrując się na zachowanie kodu w czasie wykonywania i badania określonych ścieżek błędów.

Aby określić problem z wdrożeniem lub czasem wykonywania, zobacz [Problemy z wdrażaniem a czasem wykonywania](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I'm not able to FTP and publish my code. Jak rozwiązać ten problem?
Sprawdź, czy wprowadzono poprawną nazwa hosta i [poświadczenia](#open-ftp-dashboard). Sprawdź również, czy następujące porty FTP na komputerze nie są blokowane przez zaporę:

- Port połączenia sterowania FTP: 21
- Port połączenia danych FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak połączyć się z ftp w usłudze Azure App Service w trybie pasywnym?
Usługa Azure App Service obsługuje łączenie zarówno w trybie aktywnym, jak i pasywnym. Tryb pasywny jest preferowany, ponieważ maszyny rozmieszczenia znajdują się zwykle za zaporą (w systemie operacyjnym lub jako część sieci domowej lub firmowej). Zobacz [przykład z dokumentacji WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać bardziej zaawansowane scenariusze wdrażania, spróbuj [wdrożyć na platformie Azure za pomocą git](deploy-local-git.md). Wdrożenie oparte na git na platformie Azure umożliwia kontrolę wersji, przywracanie pakietów, MSBuild i inne.

## <a name="more-resources"></a>Więcej zasobów

* [Poświadczenia wdrażania usługi Azure App Service](deploy-configure-credentials.md)
