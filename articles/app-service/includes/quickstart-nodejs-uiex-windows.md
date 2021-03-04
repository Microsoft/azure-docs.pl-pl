---
title: 'Szybki Start: Tworzenie Node.js aplikacji sieci Web — Windows'
description: Wdróż swoje pierwsze Node.js Hello world do Azure App Service w ciągu kilku minut dla platformy systemu Windows.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 324919c1d085302e675e0bc9a370ce35f52a0f1d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109038"
---
<!-- advanced for windows -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. Wdróż do Azure App Service z Visual Studio Code

1. Otwórz folder aplikacji w Visual Studio Code.

    ```bash
    code .
    ```

1. W Eksploratorze **usługi Azure App Service** wybierz pozycję **Zaloguj się do platformy Azure...** i postępuj zgodnie z instrukcjami. Po zalogowaniu się w Eksploratorze powinien zostać wyświetlona nazwa subskrypcji platformy Azure.

    ![Logowanie do platformy Azure](../media/quickstart-nodejs/sign-in.png)

    <details>
    <summary>Rozwiązywanie problemów z logowaniem do platformy Azure</summary>
    
    Jeśli zostanie wyświetlony komunikat o błędzie **"nie można odnaleźć subskrypcji o nazwie [Identyfikator subskrypcji]"** podczas logowania do platformy Azure, może to być spowodowane tym, że jesteś za serwerem proxy i nie można uzyskać dostępu do interfejsu API platformy Azure. Skonfiguruj `HTTP_PROXY` i `HTTPS_PROXY` zmienne środowiskowe za pomocą informacji o serwerze proxy w terminalu przy użyciu `export` .
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [zgłaszanie problemu](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. W Eksploratorze **usługi Azure App Service** wybierz ikonę strzałki w górę, aby wdrożyć aplikację na platformie Azure. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Zrzut ekranu usługi Azure App Service w VS Code z wybraną ikoną niebieską strzałką.":::

1. Wybierz katalog, który jest aktualnie otwarty `nodejs-docs-hello-world` .

1. Wybierz pozycję **Utwórz nową aplikację sieci Web... Zaawansowane**, aby wdrożyć program w celu App Service w systemie Windows.

1. Wpisz globalnie unikatowy <abbr title="Prawidłowymi znakami dla nazwy aplikacji są "a-z", "0-9" i "-".">name</abbr> dla aplikacji sieci Web i naciśnij klawisz **Enter**. 
1. Wybierz pozycję **Utwórz nową grupę zasobów**, a następnie wprowadź nazwę grupy zasobów, na przykład `AppServiceQS-rg` .
1. Wybierz **wersjęNode.js**, zalecamy LTS.

    Kanał powiadomień przedstawia zasoby platformy Azure, które są tworzone dla aplikacji.
1. Wybierz **system Windows** dla systemu operacyjnego.
1. Wybierz pozycję **Utwórz nowy plan App Service**, a następnie wprowadź nazwę planu (na przykład `AppServiceQS-plan` ), a następnie wybierz pozycję **F1 bezpłatnie** dla warstwy cenowej.
1. Wybierz pozycję **Pomiń teraz** po wyświetleniu monitu o Application Insights.
1. Wybierz region w sąsiedztwie lub blisko zasobów, do których chcesz uzyskać dostęp.

1. Wybierz opcję **tak** , gdy zostanie wyświetlony monit o zaktualizowanie obszaru roboczego, tak aby późniejsze wdrożenia były automatycznie ukierunkowane na tę samą App Service aplikację sieci Web. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Zrzut ekranu przedstawiający monit o zaktualizowanie obszaru roboczego z wybranym przyciskiem tak.":::

1. Kliknij prawym przyciskiem myszy węzeł usługi App Service, a następnie wybierz pozycję **Wdróż w aplikacji sieci Web**.

1. Kliknij prawym przyciskiem myszy węzeł usługi App Service, a następnie wybierz pozycję **Przeglądaj witrynę sieci Web**.

    [zgłaszanie problemu](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

1. Po zakończeniu wdrażania wybierz pozycję **Przeglądaj witrynę sieci** Web w wierszu polecenia, aby wyświetlić świeżą wdrożoną aplikację sieci Web.

<br/>
<details>
<summary><strong>Rozwiązywanie problemów</strong></summary>

Jeśli nie możesz wykonać następujących kroków, sprawdź następujące warunki:

* Upewnij się, że aplikacja nasłuchuje na porcie dostarczonym przez zmienną środowiskową portów: `process.env.PORT` .

* Jeśli zostanie wyświetlony komunikat o błędzie **"nie masz uprawnień do wyświetlenia tego katalogu lub strony"**, uruchomienie aplikacji prawdopodobnie nie powiodło się. Przejrzyj dane wyjściowe dziennika, aby znaleźć i naprawić błąd. 

</details>

<br>

[zgłaszanie problemu](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>
