---
title: 'Szybki Start: Tworzenie Node.js aplikacji sieci Web w systemie Linux'
description: Wdróż swoje pierwsze Node.js Hello world do Azure App Service w ciągu kilku minut.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a6c580f7d6bc03f298621b1a33fcb9f3f461e802
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748611"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. Wdróż do Azure App Service z Visual Studio Code

1. Otwórz folder aplikacji w Visual Studio Code.

    ```bash
    code .
    ```

1. W Eksploratorze **usługi Azure App Service** wybierz pozycję **Zaloguj się do platformy Azure...** i postępuj zgodnie z instrukcjami. Po zalogowaniu się w Eksploratorze powinien zostać wyświetlona nazwa subskrypcji platformy Azure.

    ![Zaloguj się do platformy Azure](../media/quickstart-nodejs/sign-in.png)
    <br>
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

1. Wybierz pozycję **Utwórz nową aplikację sieci Web**, która domyślnie wdraża App Service w systemie Linux.

1. Wpisz globalnie unikatowy <abbr title="Prawidłowymi znakami dla nazwy aplikacji są "a-z", "0-9" i "-".">name</abbr> dla aplikacji sieci Web i naciśnij klawisz **Enter**. 

1. Wybierz **wersjęNode.js**, zalecamy LTS.

    Kanał powiadomień przedstawia zasoby platformy Azure, które są tworzone dla aplikacji.

1. Wybierz opcję **tak** po wyświetleniu monitu, aby zaktualizować konfigurację do uruchomienia `npm install` na serwerze docelowym. Aplikacja zostanie wdrożona.

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="Zrzut ekranu przedstawiający monit o zaktualizowanie konfiguracji na serwerze docelowym z wybranym przyciskiem tak.":::

1. Wybierz opcję **tak** , gdy zostanie wyświetlony monit o zaktualizowanie obszaru roboczego, tak aby późniejsze wdrożenia były automatycznie ukierunkowane na tę samą App Service aplikację sieci Web. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Zrzut ekranu przedstawiający monit o zaktualizowanie obszaru roboczego z wybranym przyciskiem tak.":::




1. Po zakończeniu wdrażania wybierz pozycję **Przeglądaj witrynę sieci** Web w wierszu polecenia, aby wyświetlić świeżą wdrożoną aplikację sieci Web.

<br>
<details>
<summary>Rozwiązywanie problemów</summary>
* Upewnij się, że aplikacja nasłuchuje na porcie dostarczonym przez zmienną środowiskową portów: `process.env.PORT` .
* Jeśli zostanie wyświetlony komunikat o błędzie **"nie masz uprawnień do wyświetlenia tego katalogu lub strony"**, uruchomienie aplikacji prawdopodobnie nie powiodło się. Przejrzyj dane wyjściowe dziennika, aby znaleźć i naprawić błąd. 

</details>

<br>

[zgłaszanie problemu](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>


