---
title: 'Szybki Start: Tworzenie aplikacji Ruby'
description: Rozpocznij pracę z Azure App Service, wdrażając pierwszą aplikację Ruby w kontenerze systemu Linux w App Service.
keywords: usługa azure app, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 49f2100386af21cee8f76403d7a2d2e4ac6b8f63
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88961341"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>Tworzenie aplikacji w języku Ruby on-Szyns w App Service

[Azure App Service w systemie Linux](overview.md#app-service-on-linux) oferuje wysoce skalowalną, samoobsługową usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. Ten samouczek szybki start przedstawia sposób wdrażania aplikacji języka Ruby on-Szyns w celu App Service w systemie Linux przy użyciu [Cloud Shell](../cloud-shell/overview.md).

> [!NOTE]
> Obecnie stos programistyczny Ruby obsługuje jedynie aplikację Ruby on Rails. Jeśli chcesz użyć innej platformy, takiej jak Sinatra, lub jeśli chcesz użyć nieobsługiwanej wersji języka Ruby, musisz [uruchomić ją w kontenerze niestandardowym](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Zainstaluj język Ruby 2,6 lub nowszy</a>
* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>

## <a name="download-the-sample"></a>Pobieranie przykładu

W oknie terminala uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na maszynę lokalną:

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>Lokalne uruchamianie aplikacji

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. Otwórz okno terminala, zmień katalog na `hello-world` i użyj polecenia `rails server`, aby uruchomić serwer.

Pierwszym krokiem jest zainstalowanie wymaganych rozwiązań Gem. Znajduje się `Gemfile` w próbce, więc po prostu uruchom następujące polecenie:

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

Przejdź do aplikacji, aby zobaczyć nowo utworzoną aplikację internetową z wbudowanym obrazem. Zastąp _ &lt; nazwę aplikacji>_ nazwą aplikacji sieci Web.

```bash
http://<app_name>.azurewebsites.net
```

Tak powinna wyglądać nowa aplikacja internetowa:

![Strona powitalna](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>Wdrażanie aplikacji

Uruchom następujące polecenia, aby wdrożyć aplikację lokalną w aplikacji sieci Web platformy Azure:

```bash
git remote add azure <Git deployment URL from above>
git push azure master
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
   a6e73a2..ae34be9  master -> master
```

Po zakończeniu wdrożenia Poczekaj około 10 sekund na ponowne uruchomienie aplikacji sieci Web, a następnie przejdź do aplikacji sieci Web i sprawdź wyniki.

```bash
http://<app-name>.azurewebsites.net
```

![uaktualniona aplikacja internetowa](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> Po ponownym uruchomieniu aplikacji można obserwować kod stanu HTTP `Error 503 Server unavailable` w przeglądarce lub na `Hey, Ruby developers!` stronie domyślnej. Ponowne uruchomienie aplikacji może potrwać kilka minut.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: język Ruby na szynach z Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Ruby](configure-language-ruby.md)