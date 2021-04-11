---
title: 'Szybki Start: Tworzenie aplikacji w języku Python w Azure Portal'
description: Rozpocznij pracę z Azure App Service, wdrażając pierwszą aplikację w języku Python w kontenerze systemu Linux w App Service przy użyciu Azure Portal.
ms.topic: quickstart
ms.date: 04/01/2021
ms.custom: devx-track-python
robots: noindex
ms.openlocfilehash: 42f29152521da7bf90a550248e8429e51b728b24
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012360"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux-azure-portal"></a>Szybki Start: Tworzenie aplikacji w języku Python przy użyciu Azure App Service w systemie Linux (Azure Portal)

W tym przewodniku szybki start wdrożono aplikację sieci Web w języku Python w celu [App Service w systemie Linux](overview.md#app-service-on-linux)— wysoce skalowalna, samoobsługowa usługa hostingu sieci Web na platformie Azure. Użyj Azure Portal, aby wdrożyć przykład za pomocą kolby lub platform Django. Skonfigurowana aplikacja sieci Web używa warstwy Podstawowa App Service, która wiąże się z niewielkim kosztem subskrypcji platformy Azure.

## <a name="configure-accounts"></a>Konfigurowanie kont

- Jeśli jeszcze nie masz konta platformy Azure z aktywną subskrypcją, [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Jeśli nie masz konta usługi GitHub, odwiedź stronę [GitHub.com](https://github.com) , aby je utworzyć. 

## <a name="fork-the-sample-github-repository"></a>Rozwidlenie przykładowego repozytorium GitHub

1. Otwórz [GitHub.com](https://github.com) i zaloguj się.

1. Przejdź do jednego z następujących przykładowych repozytoriów:
    - [Hello world kolby](https://github.com/Azure-Samples/python-docs-hello-world)
    - [Django Hello world](https://github.com/Azure-Samples/python-docs-hello-django)

1. W prawym górnym rogu strony usługi GitHub wybierz opcję **rozwidlenie** , aby utworzyć kopię repozytorium na swoim koncie usługi GitHub:

    ![Rozwidlenie — polecenie](media/quickstart-python-portal/github-fork-command.png)

    Platforma Azure wymaga dostępu do organizacji GitHub zawierającej repozytorium. Przez rozwidlenie przykładu na własne konto w serwisie GitHub automatycznie masz wymagany dostęp i można również wprowadzić zmiany w kodzie.

## <a name="provision-the-app-service-web-app"></a>Inicjowanie obsługi administracyjnej aplikacji sieci Web App Service

Aplikacja sieci Web App Service to serwer sieci Web, na którym wdrażasz swój kod.

1. W [https://portal.azure.com](https://portal.azure.com) razie konieczności otwórz Azure Portal i zaloguj się.

1. Na pasku wyszukiwania w górnej części Azure Portal wprowadź ciąg "App Service", a następnie wybierz pozycję **App Services**.

    ![Pasek wyszukiwania portalu i wybór App Service](media/quickstart-python-portal/portal-search-bar.png)

1. Na stronie **App Services** wybierz pozycję "**+ Dodaj**:

    ![Dodaj App Service polecenie](media/quickstart-python-portal/add-app-service.png)

1. Na stronie **Tworzenie aplikacji sieci Web** wykonaj następujące czynności:
    
    | Pole | Akcja |
    | --- | --- |
    | Subskrypcja | Wybierz subskrypcję platformy Azure, której chcesz użyć. |
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy** poniżej listy rozwijanej. W oknie podręcznym wprowadź wartość "AppService-PythonQuickstart" i wybierz pozycję "**OK**". |
    | Nazwa | Wprowadź unikatową nazwę dla wszystkich platform Azure, zwykle używając kombinacji nazw prywatnych lub firmowych, takich jak *contoso-TestApp-123*. |
    | Publikowanie | Wybierz pozycję **Kod**. |
    | Stos środowiska uruchomieniowego | Wybierz pozycję **Python 3,8**. |
    | System operacyjny | Wybierz system **Linux** (język Python jest obsługiwany tylko w systemie Linux). |
    | Region (Region) | Wybierz region blisko siebie. |
    | Plan systemu Linux | Wybierz pozycję zamykanie planu App Service lub użyj opcji **Utwórz nowy** , aby utworzyć nowy. Zalecamy użycie planu **Basic B1** . |

    ![Utwórz formularz aplikacji sieci Web na Azure Portal](media/quickstart-python-portal/create-web-app.png)

1. W dolnej części strony wybierz pozycję **Przegląd + Utwórz**, przejrzyj szczegóły, a następnie wybierz pozycję **Utwórz**.

1. Po zakończeniu aprowizacji wybierz pozycję **Przejdź do zasobu** , aby przejść do strony Nowa App Service. Twoja aplikacja sieci Web w tym punkcie zawiera tylko stronę domyślną, więc w następnym kroku zostanie wdrożony przykładowy kod.

Masz problemy? [Daj nam znać](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="deploy-the-sample-code"></a>Wdrażanie przykładowego kodu

1. Na stronie aplikacja sieci Web na Azure Portal wybierz pozycję **centrum wdrażania**:
    
    ![Polecenie centrum wdrażania w menu App Service](media/quickstart-python-portal/deployment-center-command.png)


1. Na stronie **centrum wdrażania** wybierz kartę **Ustawienia** , jeśli nie została jeszcze otwarta:

    ![Karta Ustawienia centrum wdrażania](media/quickstart-python-portal/deployment-center-settings-tab.png)

1. W obszarze **Źródło** wybierz pozycję **GitHub**, a następnie w wyświetlonym formularzu usługi **GitHub** wykonaj następujące czynności:

    | Pole | Akcja |
    | --- | --- |
    | Zalogowano jako | Jeśli nie zalogowano się do usługi GitHub, zaloguj się teraz lub wybierz pozycję **Zmień konto* , jeśli jest to konieczne. |
    | Organizacja | W razie konieczności wybierz organizację usługi GitHub. |
    | Repozytorium | Wybierz nazwę repozytorium przykładowego, które zostało przemieszczone wcześniej, w języku **Python-docs-Hello-World** (w kolbie) lub w języku **Python-docs-Hello-Django** (Django). |
    | Odgałęzienie | Wybierz pozycję **Main**. |

    ![Konfiguracja źródła usługi GitHub centrum wdrażania](media/quickstart-python-portal/deployment-center-configure-github-source.png)

1. W górnej części strony wybierz pozycję **Zapisz** , aby zastosować ustawienia.

    ![Zapisz konfigurację źródłową usługi GitHub w centrum wdrożenia](media/quickstart-python-portal/deployment-center-configure-save.png)

1. Wybierz kartę **dzienniki** , aby wyświetlić stan wdrożenia. Skompilowanie i wdrożenie przykładu może potrwać kilka minut. Po zakończeniu dzienniki powinny odzwierciedlać stan **sukcesu (aktywny)**:

    ![Karta Dzienniki centrum wdrażania](media/quickstart-python-portal/deployment-center-logs.png)

Masz problemy? [Daj nam znać](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

1. Po zakończeniu wdrażania wybierz pozycję **Przegląd** w menu po lewej stronie, aby powrócić do strony głównej aplikacji sieci Web.

1. Wybierz **adres URL** , który zawiera adres aplikacji sieci Web:

    ![Adres URL aplikacji sieci Web na stronie przeglądu](media/quickstart-python-portal/web-app-url.png)

1. Sprawdź, czy dane wyjściowe aplikacji to "Hello, World!":

    ![Aplikacja uruchomiona po początkowym wdrożeniu](media/quickstart-python-portal/web-app-first-deploy-results.png)

Masz problemy? Najpierw zapoznaj się z [przewodnikiem rozwiązywania problemów](configure-language-python.md#troubleshooting), w przeciwnym razie [daj nam znać](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="make-a-change-and-redeploy"></a>Wprowadź zmianę i Wdróż ponownie

Ze względu na to, że połączenie App Service z repozytorium, zmiany wprowadzone do repozytorium źródłowego są automatycznie wdrażane w aplikacji sieci Web.

1. Możesz wprowadzać zmiany bezpośrednio w repozytorium rozwidlenia w usłudze GitHub lub można sklonować repozytorium lokalnie, wprowadzić i zatwierdzić zmiany, a następnie wypchnąć te zmiany do usługi GitHub. Każda z tych metod powoduje zmianę repozytorium, które jest połączone z App Service.

1. **W repozytorium z rozwidleniem** Zmień komunikat aplikacji z "Hello, World!" na "Hello, Azure!" w następujący sposób:
    - Kolba (Python-docs-Hello-World — przykład): Zmień ciąg tekstowy w wierszu 6 pliku *Application.py* .
    - Django (przykład python-docs-Hello-Django): Zmień ciąg tekstowy w wierszu 5 pliku *views.py* w folderze *Hello* .

1. Zatwierdź zmianę w repozytorium.

    Jeśli używasz klonu lokalnego, wypchnij te zmiany do usługi GitHub.

1. Na Azure Portal aplikacji sieci Web Wróć do **centrum wdrażania**, wybierz kartę **dzienniki** i zanotuj nowe działanie wdrażania, które powinno być w toku.

1. Po zakończeniu wdrażania Wróć do strony **Przegląd** aplikacji sieci Web, Otwórz ponownie adres URL aplikacji sieci Web i zaobserwuj zmiany w aplikacji:

    ![Aplikacja uruchomiona po ponownej wdrożeniu](media/quickstart-python-portal/web-app-second-deploy-results.png)

Masz problemy? Najpierw zapoznaj się z [przewodnikiem rozwiązywania problemów](configure-language-python.md#troubleshooting), w przeciwnym razie [daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów o nazwie "AppService-PythonQuickstart", która jest wyświetlana na stronie *przeglądu* aplikacji sieci Web *. Jeśli aplikacja sieci Web jest uruchomiona, zostaną naliczone pewne bieżące koszty (zobacz [cennik App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Jeśli nie chcesz potrzebować tych zasobów w przyszłości, wybierz nazwę grupy zasobów na stronie **Przegląd** aplikacji sieci Web, aby przejść do omówienia grup zasobów. Wybierz pozycję **Usuń grupę zasobów** i postępuj zgodnie z monitami.

![Usuwanie grupy zasobów](media/quickstart-python-portal/delete-resource-group.png)


Masz problemy? [Daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja sieci Web języka Python (Django) z PostgreSQL](/azure/developer/python/tutorial-python-postgresql-app-portal)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Dodawanie logowania użytkownika do aplikacji sieci Web w języku Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie aplikacji języka Python w kontenerze niestandardowym](tutorial-custom-container.md)
