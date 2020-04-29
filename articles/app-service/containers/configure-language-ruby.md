---
title: Konfigurowanie aplikacji Ruby-Azure App Service
description: Dowiedz się, jak skonfigurować wstępnie zbudowany kontener Ruby dla aplikacji. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8daebba840223d050a14b4b99cb6ae15472ee4f5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80046324"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurowanie aplikacji Ruby systemu Linux dla Azure App Service

W tym artykule opisano, jak [Azure App Service](app-service-linux-intro.md) są uruchamiane aplikacje Ruby oraz jak można dostosować zachowanie App Service w razie konieczności. Aplikacje Ruby muszą zostać wdrożone ze wszystkimi wymaganymi [Gems](https://rubygems.org/gems).

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka Ruby, którzy używają wbudowanego kontenera systemu Linux w App Service. Jeśli nie korzystasz z Azure App Service, najpierw należy wykonać samouczka [Ruby](quickstart-ruby.md) i [Ruby with PostgreSQL](tutorial-ruby-postgres-app.md) .

## <a name="show-ruby-version"></a>Pokaż wersję języka Ruby

Aby wyświetlić bieżącą wersję języka Ruby, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje języka Ruby, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Możesz uruchomić nieobsługiwaną wersję języka Ruby, tworząc własny obraz kontenera. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker](tutorial-custom-docker-image.md).

## <a name="set-ruby-version"></a>Ustaw wersję języka Ruby

Uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby ustawić wersję języka Ruby na 2,3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Jeśli podczas wdrażania widoczne są błędy podobne do następujących:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> lub
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Oznacza to, że wersja języka Ruby skonfigurowana w projekcie różni się od wersji zainstalowanej w używanym kontenerze (`2.3.3` w powyższym przykładzie). W powyższym przykładzie Sprawdź wersję *Gemfile* i *. Ruby* i sprawdź, czy wersja języka Ruby nie została ustawiona lub jest ustawiona na wersję zainstalowaną w używanym kontenerze (`2.3.3` w powyższym przykładzie).

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W App Service można [ustawić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodem aplikacji. Następnie możesz uzyskać do nich dostęp przy użyciu standardowego wzorca [ENV\<["path-Name>"]](https://ruby-doc.org/core-2.3.3/ENV.html) . Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `WEBSITE_SITE_NAME`, użyj następującego kodu:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Dostosowywanie wdrożenia

Podczas wdrażania [repozytorium git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)lub [pakietu zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) z procesami kompilacji przełączane, aparat wdrażania (kudu) automatycznie domyślnie uruchamia następujące kroki po wdrożeniu:

1. Sprawdź, czy *Gemfile* istnieje.
1. Uruchom polecenie `bundle clean`. 
1. Uruchom polecenie `bundle install --path "vendor/bundle"`.
1. Uruchom `bundle package` do Gems pakietu w folderze dostawcy/pamięci podręcznej.

### <a name="use---without-flag"></a>Użyj flagi--bez

Aby uruchomić `bundle install` z flagą [--bez](https://bundler.io/man/bundle-install.1.html) , należy ustawić `BUNDLE_WITHOUT` [ustawienie aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na listę grup. Na przykład następujące polecenie ustawia je na `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Jeśli to ustawienie jest zdefiniowane, aparat wdrażania zostanie uruchomiony `bundle install` z. `--without $BUNDLE_WITHOUT`

### <a name="precompile-assets"></a>Wstępne Kompilowanie zasobów

Kroki po wdrożeniu nie domyślnie kompilują zasoby. Aby włączyć prekompilowanie zasobów, ustaw dla `ASSETS_PRECOMPILE` `true` [Ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) wartość. Następnie polecenie `bundle exec rake --trace assets:precompile` jest uruchamiane po zakończeniu kroków po wdrożeniu. Przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Aby uzyskać więcej informacji, zobacz temat [Obsługuj statyczne zasoby](#serve-static-assets).

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślnie kontener Ruby uruchamia serwer szyn w następującej sekwencji (Aby uzyskać więcej informacji, zobacz [skrypt uruchamiania](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Generuj wartość [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) , jeśli jeszcze nie istnieje. Ta wartość jest wymagana, aby aplikacja była uruchamiana w trybie produkcyjnym.
1. Ustaw zmienną `RAILS_ENV` środowiskową na `production`.
1. Usuń dowolny plik *. PID* w katalogu *tmp/PID* , który został pozostawiony przez serwer wcześniej uruchomionych kolejek.
1. Sprawdź, czy wszystkie zależności są zainstalowane. W przeciwnym razie spróbuj zainstalować Gems z katalogu *dostawcy lokalnego/pamięci podręcznej* .
1. Uruchom polecenie `rails server -e $RAILS_ENV`.

Proces uruchamiania można dostosować w następujący sposób:

- [Obsługuj statyczne zasoby](#serve-static-assets)
- [Uruchom w trybie nieprodukcyjnym](#run-in-non-production-mode)
- [Ustaw secret_key_base ręcznie](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Obsługuj statyczne zasoby

Serwer szyn w kontenerze Ruby jest domyślnie uruchamiany w trybie produkcyjnym i zakłada, [że zasoby są wstępnie skompilowane i są obsługiwane przez serwer sieci Web](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Aby zapewnić statyczne zasoby z serwera szyn, należy wykonać dwie czynności:

- **Wstępne Kompilowanie zasobów** - umożliwia wstępne[skompilowanie statycznych zasobów lokalnie](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) i wdrożenie ich ręcznie. Lub pozwól, aby aparat wdrażania go obsłużył go (zobacz [prekompilowanie zasobów](#precompile-assets).
- **Włącz obsługę plików statycznych** — aby obsłużyć statyczne zasoby z kontenera Ruby, [Ustaw `RAILS_SERVE_STATIC_FILES` dla](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) `true`ustawienia aplikację wartość. Przykład:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Uruchom w trybie nieprodukcyjnym

Domyślnie serwer szyn działa w trybie produkcyjnym. Aby uruchomić program w trybie programistycznym, na przykład dla `RAILS_ENV` [Ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) Ustaw `development`wartość.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Jednak to ustawienie powoduje, że serwer szyn będzie uruchamiany w trybie programistycznym, który akceptuje tylko żądania localhost i nie jest dostępny poza kontenerem. Aby zaakceptować żądania klientów zdalnych, należy ustawić `APP_COMMAND_LINE` [ustawienie aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) na `rails server -b 0.0.0.0`. To ustawienie aplikacji umożliwia uruchomienie polecenia niestandardowego w kontenerze Ruby. Przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a>Ustaw secret_key_base ręcznie

Aby użyć własnej `secret_key_base` wartości zamiast zezwalać App Service wygenerować ją dla Ciebie, należy ustawić `SECRET_KEY_BASE` dla [Ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) wartość, którą chcesz. Przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: szyny aplikacji z PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linux — często zadawane pytania](app-service-linux-faq.md)
