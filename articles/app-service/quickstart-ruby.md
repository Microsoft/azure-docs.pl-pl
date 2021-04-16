---
title: 'Szybki start: tworzenie aplikacji ruby'
description: Rozpoczynanie pracy z Azure App Service od wdrożenia pierwszej aplikacji ruby w kontenerze systemu Linux w App Service.
keywords: usługa azure app, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ms.openlocfilehash: dc64dfa277e97fe7487ce91a140e73b9ec44e086
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483541"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Tworzenie aplikacji Ruby on Rails w App Service

[Azure App Service dla systemu Linux](overview.md#app-service-on-linux) zapewnia wysoce skalowalną, samonadajną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym samouczku Szybki start pokazano, jak wdrożyć aplikację ruby on Rails w App Service dla systemu Linux użyciu [Cloud Shell](../cloud-shell/overview.md).

> [!NOTE]
> Obecnie stos programistyczny Ruby obsługuje jedynie aplikację Ruby on Rails. Jeśli chcesz użyć innej platformy, takiej jak Sinatra, lub jeśli chcesz użyć nieobsługiwanej wersji języka Ruby, musisz uruchomić ją w [kontenerze niestandardowym](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Zainstaluj oprogramowanie Ruby 2.6 lub wyższe</a>
* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>

## <a name="download-the-sample"></a>Pobieranie przykładu

W oknie terminala uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na maszynę lokalną:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. Otwórz okno terminala, zmień katalog na `hello-world` i użyj polecenia `rails server`, aby uruchomić serwer.

Pierwszym krokiem jest zainstalowanie wymaganych rozwiązań Gem. W przykładzie znajduje się kod `Gemfile` , więc po prostu uruchom następujące polecenie:

```bash
bundle install
```

Po zainstalowaniu rozwiązań Gem użyjemy do uruchomienia aplikacji programu instalującego niezamówione pakiety oprogramowania:

```bash
bundle exec rails server
```

Przy użyciu przeglądarki internetowej przejdź pod adres `http://localhost:3000`, aby przetestować aplikację lokalnie.

![Skonfigurowana aplikacja Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

Przejdź do aplikacji, aby wyświetlić nowo utworzoną aplikację internetową z wbudowanym obrazem. Zastąp _&lt; nazwę>_ nazwą aplikacji internetowej.

```bash
http://<app_name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja internetowa:

![Strona powitalna](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Wdrażanie aplikacji

Uruchom następujące polecenia, aby wdrożyć aplikację lokalną w aplikacji internetowej platformy Azure:

```bash
git remote add azure <Git deployment URL from above>
git push azure main
```

Upewnij się, że operacje zdalnego wdrażania raportują powodzenie. Polecenia tworzą dane wyjściowe podobne do następującego tekstu:

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  main -> main
```

Po zakończeniu wdrażania poczekaj około 10 sekund na ponowne uruchomienie aplikacji internetowej, a następnie przejdź do aplikacji internetowej i sprawdź wyniki.

```bash
http://<app-name>.azurewebsites.net
```

![uaktualniona aplikacja internetowa](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Podczas ponownego uruchamiania aplikacji możesz obserwować kod stanu HTTP w przeglądarce `Error 503 Server unavailable` lub na `Hey, Ruby developers!` stronie domyślnej. Pełne ponowne uruchomienie aplikacji może potrwać kilka minut.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Ruby on Rails with Postgres (Samouczek: ruby on Rails z usługą Postgres)](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Ruby](configure-language-ruby.md)
