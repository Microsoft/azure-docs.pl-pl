---
title: Wdrażanie zawartości przy użyciu protokołu FTP/S
description: Dowiedz się, jak wdrożyć aplikację w Azure App Service przy użyciu protokołu FTP lub FTPS. Zwiększ bezpieczeństwo witryny sieci Web, wyłączając nieszyfrowany protokół FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9884b109db3f3a34ceb323bef9fba1d5bfc23147
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150263"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Wdrażanie aplikacji do Azure App Service przy użyciu protokołu FTP/S

W tym artykule pokazano, jak za pomocą protokołu FTP lub FTPS wdrożyć aplikację internetową, zaplecze aplikacji mobilnej lub aplikację interfejsu API w celu [Azure App Service](./overview.md).

Punkt końcowy FTP/S aplikacji jest już aktywny. W celu włączenia wdrożenia protokołu FTP/S nie jest wymagana żadna konfiguracja.

## <a name="open-ftp-dashboard"></a>Otwórz pulpit nawigacyjny FTP

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **App Services**.

    ![Wyszukaj usługi App Services.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Wybierz aplikację sieci Web, którą chcesz wdrożyć.

    ![Wybierz aplikację.](media/app-service-continuous-deployment/select-your-app.png)

3. Wybierz **Deployment Center**pozycję  >  **FTP**  >  **pulpit nawigacyjny**FTP centrum wdrażania.

    ![Otwórz pulpit nawigacyjny FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Pobierz informacje o połączeniu FTP

Na pulpicie nawigacyjnym FTP wybierz pozycję **Kopiuj** , aby skopiować FTPS punkt końcowy i poświadczenia aplikacji.

![Kopiuj informacje FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Zalecamy użycie **poświadczeń aplikacji** do wdrożenia aplikacji, ponieważ jest ona unikatowa dla każdej aplikacji. Jeśli jednak klikniesz pozycję **poświadczenia użytkownika**, możesz ustawić poświadczenia na poziomie użytkownika, których można użyć do logowania za pomocą protokołu FTP/S do wszystkich aplikacji App Service w ramach subskrypcji.

> [!NOTE]
> Uwierzytelnianie do punktu końcowego FTP/FTPS przy użyciu poświadczeń na poziomie użytkownika nazwa użytkownika w następującym formacie: 
>
>`<app-name>\<user-name>`
>
> Ponieważ poświadczenia na poziomie użytkownika są połączone z użytkownikiem, a nie konkretnym zasobem, nazwa użytkownika musi być w tym formacie, aby skierować akcję logowania do właściwego punktu końcowego aplikacji.
>

## <a name="deploy-files-to-azure"></a>Wdrażanie plików na platformie Azure

1. Korzystając z klienta FTP (na przykład [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)lub [WinSCP](https://winscp.net/index.php)), użyj zebranych informacji o połączeniu, aby nawiązać połączenie z Twoją aplikacją.
2. Skopiuj pliki i ich strukturę katalogów do [katalogu **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) na platformie Azure (lub **/site/wwwroot/App_Data/Jobs/** Directory for WebJobs).
3. Przejdź do adresu URL swojej aplikacji, aby sprawdzić, czy aplikacja działa prawidłowo. 

> [!NOTE] 
> W przeciwieństwie do [wdrożeń opartych na git](deploy-local-git.md), wdrożenie FTP nie obsługuje następujących automatyzacji wdrożenia: 
>
> - Przywracanie zależności (takie jak NuGet, NPM, PIP i Automation Composer)
> - Kompilacja plików binarnych platformy .NET
> - Generowanie web.config (Oto [ przykładNode.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Ręcznie Wygeneruj te wymagane pliki na komputerze lokalnym, a następnie wdróż je razem z aplikacją.
>

## <a name="enforce-ftps"></a>Wymuś FTPS

Aby zapewnić większe bezpieczeństwo, należy zezwalać tylko na protokół FTP przez protokół TLS/SSL. Jeśli nie korzystasz z wdrażania FTP, możesz również wyłączyć zarówno protokół FTP, jak i FTPS.

Na stronie zasobów aplikacji w obszarze [Azure Portal](https://portal.azure.com)wybierz pozycję **Konfiguracja**  >  **Ogólne ustawienia** w lewym okienku nawigacji.

Aby wyłączyć nieszyfrowane FTP, wybierz pozycję **FTPS tylko** w polu **stan FTP**. Aby całkowicie wyłączyć protokół FTP i FTPS, wybierz pozycję **wyłączone**. Po skończeniu kliknij przycisk **Zapisz**. W przypadku używania **tylko FTPS**należy wymusić TLS 1,2 lub nowszy, przechodząc do bloku **Ustawienia protokołu TLS/SSL** w aplikacji sieci Web. Protokoły TLS 1,0 i 1,1 nie są obsługiwane **tylko**w przypadku FTPS.

![Wyłącz FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatyzowanie przy użyciu skryptów

Aby wdrożyć FTP przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure), zobacz [Tworzenie aplikacji internetowej i wdrażanie plików przy użyciu protokołu FTP (interfejs wiersza polecenia platformy Azure)](./scripts/cli-deploy-ftp.md).

Aby wdrożyć FTP przy użyciu [Azure PowerShell](/cli/azure), zobacz [przekazywanie plików do aplikacji internetowej przy użyciu protokołu FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Rozwiązywanie problemów z wdrażaniem FTP

- [Jak rozwiązywać problemy z wdrażaniem FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Nie mogę przeprowadzić FTP i opublikować mojego kodu. Jak mogę rozwiązać ten problem?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak połączyć się z FTP w Azure App Service za pośrednictwem trybu pasywnego?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak rozwiązywać problemy z wdrażaniem FTP?

Pierwszym krokiem w przypadku rozwiązywania problemów z wdrażaniem za pomocą usługi FTP jest izolowanie problemu wdrażania z powodu problemu z aplikacją środowiska uruchomieniowego.

Problem z wdrożeniem zwykle skutkuje brakiem plików lub nieprawidłowymi plikami wdrożonymi w aplikacji. Możesz rozwiązywać problemy, badając wdrożenie FTP lub wybierając alternatywną ścieżkę wdrożenia (np. kontrolę źródła).

Problem z aplikacją środowiska uruchomieniowego zwykle polega na odpowiednim zestawie plików wdrożonym w aplikacji, ale nieprawidłowym zachowaniu aplikacji. Możesz rozwiązywać problemy, koncentrując się na zachowaniu kodu w czasie wykonywania i badając konkretne ścieżki błędów.

Aby określić problem dotyczący wdrożenia lub środowiska uruchomieniowego, zobacz [problemy dotyczące wdrażania i środowiska uruchomieniowego](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I'm not able to FTP and publish my code. Jak mogę rozwiązać ten problem?
Sprawdź, czy wprowadzono poprawną nazwę hosta i [poświadczenia](#open-ftp-dashboard). Sprawdź również, czy następujące porty FTP na maszynie nie są blokowane przez zaporę:

- Port połączenia sterowania FTP: 21
- Port połączenia danych FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak połączyć się z FTP w Azure App Service za pośrednictwem trybu pasywnego?
Azure App Service obsługuje łączenie za pośrednictwem trybu aktywnego i pasywnego. Tryb pasywny jest preferowany, ponieważ maszyny wdrażania zwykle znajdują się za zaporą (w systemie operacyjnym lub w ramach sieci domowej lub firmowej). Zapoznaj się [z przykładem z dokumentacji WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Następne kroki

W przypadku bardziej zaawansowanych scenariuszy wdrażania spróbuj [wdrożyć platformę Azure za pomocą narzędzia Git](deploy-local-git.md). Wdrożenie oparte na usłudze Git na platformie Azure umożliwia kontrolę wersji, przywracanie pakietów, MSBuild i nie tylko.

## <a name="more-resources"></a>Dodatkowe zasoby

* [Poświadczenia wdrażania Azure App Service](deploy-configure-credentials.md)