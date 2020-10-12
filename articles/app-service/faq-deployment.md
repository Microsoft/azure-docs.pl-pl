---
title: Często zadawane pytania dotyczące wdrażania — Azure App Service | Microsoft Docs
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące wdrażania Web Apps funkcji Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 163a6940e50d1f8beacc23855fd1e6f9daad0085
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080477"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące aplikacji internetowych na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące problemów z wdrażaniem [Web Apps funkcji Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Rozpoczynam pracę z App Service aplikacjami sieci Web. Jak mogę opublikować mój kod?

Oto kilka opcji publikowania kodu aplikacji sieci Web:

*   Wdrażanie przy użyciu programu Visual Studio. Jeśli masz rozwiązanie Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji sieci Web, a następnie wybierz polecenie **Publikuj**.
*   Wdróż program przy użyciu klienta FTP. W Azure Portal Pobierz profil publikowania dla aplikacji sieci Web, w której chcesz wdrożyć swój kod. Następnie Przekaż pliki do \site\wwwroot przy użyciu tych samych poświadczeń FTP profilu publikowania.

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji do App Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Pojawia się komunikat o błędzie podczas próby wdrożenia z programu Visual Studio. Jak mogę rozwiązać ten błąd?

Jeśli zobaczysz następujący komunikat, być może używasz starszej wersji zestawu SDK: "błąd podczas wdrażania zasobu" YourResourceName "w grupie zasobów" YourResourceGroup ": MissingRegistrationForLocation: subskrypcja nie jest zarejestrowana dla typu zasobu" Components "w lokalizacji" środkowe stany USA ". Zarejestruj ponownie dla tego dostawcy w celu uzyskania dostępu do tej lokalizacji. 

Aby rozwiązać ten problem, należy przeprowadzić uaktualnienie do [najnowszego zestawu SDK](https://azure.microsoft.com/downloads/). Jeśli zobaczysz ten komunikat i masz najnowszy zestaw SDK, Prześlij żądanie pomocy technicznej.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Jak mogę wdrożyć aplikację ASP.NET z poziomu programu Visual Studio w celu App Service?
<a id="deployasp"></a>

Samouczek [Tworzenie pierwszej aplikacji sieci web ASP.NET na platformie Azure w ciągu pięciu minut](quickstart-dotnetcore.md) pokazuje, jak wdrożyć aplikację sieci Web ASP.NET w aplikacji sieci web w App Service przy użyciu programu Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Jakie są różne typy poświadczeń wdrażania?

App Service obsługuje dwa typy poświadczeń dla lokalnego wdrożenia git i wdrożenia FTP/S. Więcej informacji o sposobie konfigurowania poświadczeń wdrożenia znajduje się w temacie [Konfigurowanie poświadczeń wdrażania dla App Service](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Co to jest struktura plików lub katalogów aplikacji sieci Web App Service?

Aby uzyskać informacje o strukturze plików aplikacji App Service, zobacz [Struktura plików na platformie Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Jak mogę rozwiązać problem "Błąd FTP 550 — za mało miejsca na dysku" podczas próby FTP moje pliki?

Jeśli zobaczysz ten komunikat, prawdopodobnie korzystasz z przydziału dysku w planie usługi dla aplikacji sieci Web. Może być konieczne skalowanie w górę do wyższej warstwy usług w zależności od potrzeb dotyczących miejsca na dysku. Aby uzyskać więcej informacji na temat planów cenowych i limitów zasobów, zobacz [App Service Cennik](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Jak mogę skonfigurować ciągłe wdrażanie dla aplikacji internetowej App Service?

Można skonfigurować ciągłe wdrażanie z kilku zasobów, w tym Azure DevOps, OneDrive, GitHub, BitBucket, Dropbox i innych repozytoriów Git. Te opcje są dostępne w portalu. [Ciągłe wdrażanie do App Service](deploy-continuous-deployment.md) to przydatny samouczek, w którym wyjaśniono, jak skonfigurować ciągłe wdrażanie.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Jak mogę rozwiązywania problemów z ciągłym wdrażaniem z usługi GitHub i Bitbucket?

Aby uzyskać pomoc w prowadzeniu problemów z ciągłym wdrażaniem z usługi GitHub lub BitBucket, zobacz [Badanie ciągłego wdrażania](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Nie mogę przeprowadzić połączenia FTP z moją witryną i opublikować mój kod. Jak mogę rozwiązać ten problem?

Aby rozwiązać problemy z usługą FTP:

1. Sprawdź, czy wprowadzasz poprawną nazwę hosta i poświadczenia. Aby uzyskać szczegółowe informacje o różnych typach poświadczeń i sposobach ich użycia, zobacz [poświadczenia wdrożenia](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Upewnij się, że porty FTP nie są blokowane przez zaporę. Porty powinny mieć następujące ustawienia:
    * Port połączenia sterowania FTP: 21
    * Port połączenia danych FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Jak mogę opublikować mój kod, aby App Service?

Przewodnik Szybki Start platformy Azure został zaprojektowany z założeniami, aby ułatwić wdrożenie aplikacji za pomocą wybranego stosu i metody wdrażania. Aby skorzystać z przewodnika Szybki Start, w Azure Portal przejdź do usługi App Service, w obszarze **Deployment (wdrażanie**) wybierz pozycję **Szybki Start**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Dlaczego moja aplikacja czasami jest uruchamiana ponownie po wdrożeniu do App Service?

Aby dowiedzieć się, jak sytuacje, w których wdrożenie aplikacji może spowodować ponowne uruchomienie, zobacz [problemy dotyczące wdrażania i środowiska uruchomieniowego](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Zgodnie z opisem w artykule App Service wdraża pliki w folderze wwwroot. Nigdy nie jest bezpośrednio ponownie uruchamiana aplikacja.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Jak zintegrować kod usługi Azure DevOps z usługą App Service?

Dostępne są dwie opcje korzystania z ciągłego wdrażania w usłudze Azure DevOps:

*   Użyj projektu git. Połącz się za pośrednictwem App Service przy użyciu centrum wdrażania.
*   Użyj projektu Kontrola wersji serwera Team Foundation (TFVC). Wdróż program przy użyciu agenta kompilacji dla App Service.

Ciągłe wdrażanie kodu dla obu tych opcji zależy od istniejących przepływów pracy dla deweloperów i procedur zaewidencjonowania. Więcej informacji można znaleźć w następujących artykułach: 

*   [Implementowanie ciągłego wdrażania aplikacji w witrynie sieci Web platformy Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Skonfiguruj organizację usługi Azure DevOps, aby umożliwić wdrażanie jej w aplikacji sieci Web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Jak mogę użyć usługi FTP lub FTPS do wdrożenia mojej aplikacji do App Service?

Aby uzyskać informacje na temat używania protokołu FTP lub FTPS do wdrażania aplikacji sieci Web w celu App Service, zobacz [wdrażanie aplikacji do App Service przy użyciu protokołu FTP/S](deploy-ftp.md).
