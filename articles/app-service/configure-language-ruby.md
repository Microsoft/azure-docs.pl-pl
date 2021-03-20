---
title: Konfigurowanie aplikacji języka Ruby — Azure App Service
description: Dowiedz się, jak skonfigurować wstępnie skompilowany kontener języka Ruby dla aplikacji. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 038d62573b491325adc60647debf17fa87e06cfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92743692"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Konfigurowanie aplikacji Ruby systemu Linux dla usługi Azure App Service

W tym artykule opisano, jak usługa [Azure App Service](overview.md) uruchamia aplikacje języka Ruby w kontenerze systemu Linux i jak można dostosować zachowanie usługi App Service w razie potrzeby. Aplikacje Ruby należy wdrażać ze wszystkimi wymaganymi [rozwiązaniami Gem](https://rubygems.org/gems).

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka Ruby, którzy używają wbudowanego kontenera systemu Linux w usłudze App Service. Jeśli korzystasz z usługi Azure App Service po raz pierwszy, najpierw musisz skorzystać z [podręcznika Ruby — Szybki start](quickstart-ruby.md) i [samouczka języka Ruby w usłudze PostgreSQL](tutorial-ruby-postgres-app.md).

## <a name="show-ruby-version"></a>Wyświetlanie wersji języka Ruby

Aby wyświetlić bieżącą wersję języka Ruby, uruchom następujące polecenie w usłudze [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje języka Ruby, uruchom następujące polecenie w usłudze [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

Możesz uruchomić nieobsługiwaną wersję języka Ruby, kompilując w zamian własny obraz kontenera. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker](tutorial-custom-container.md?pivots=container-linux).

## <a name="set-ruby-version"></a>Ustawianie wersji języka Ruby

Uruchom następujące polecenie w usłudze [Cloud Shell](https://shell.azure.com), aby ustawić środowisko Ruby na wersję 2.3:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> Jeśli w czasie wdrażania widzisz błędy podobne do następujących:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> lub
> ```
> rbenv: version `2.3.1' is not installed
> ```
> Oznacza to, że wersja języka Ruby skonfigurowana w projekcie różni się od wersji zainstalowanej w uruchomionym kontenerze (w powyższym przykładzie `2.3.3`). W powyższym przykładzie sprawdź ustawienia *Gemfile* i *.ruby-version* oraz upewnij się, że wersja języka Ruby nie została ustawiona lub została ustawiona na wersję zainstalowaną w uruchomionym kontenerze (w powyższym przykładzie `2.3.3`).

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service można [określić ustawienia aplikacji](configure-common.md#configure-app-settings) poza kodem aplikacji. Następnie można uzyskiwać do nich dostęp przy użyciu standardowego wzorca [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html). Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `WEBSITE_SITE_NAME`, użyj następującego kodu:

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>Dostosowywanie wdrożenia

Gdy wdrażasz [repozytorium Git](deploy-local-git.md) lub [pakiet ZIP](deploy-zip.md) z włączonymi procesami kompilacji, aparat wdrażania (Kudu) automatycznie wykonuje domyślnie następujące kroki po wdrożeniu:

1. Sprawdź, czy istnieje plik *Gemfile*.
1. Uruchom polecenie `bundle clean`. 
1. Uruchom polecenie `bundle install --path "vendor/bundle"`.
1. Uruchom polecenie `bundle package`, aby spakować rozwiązania Gem do folderu vendor/cache.

### <a name="use---without-flag"></a>Używanie flagi --without

Aby uruchomić polecenie `bundle install` z flagą [--without](https://bundler.io/man/bundle-install.1.html), określ [ustawienie aplikacji](configure-common.md#configure-app-settings) `BUNDLE_WITHOUT` jako listę grup rozdzielaną przecinkami. Na przykład poniższe polecenie określa je jako `development,test`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

Jeśli to ustawienie jest zdefiniowane, aparat wdrażania uruchamia polecenie `bundle install` z parametrem `--without $BUNDLE_WITHOUT`.

### <a name="precompile-assets"></a>Kompilacja wstępna zasobów

Kroki po wdrożeniu domyślnie nie obejmują kompilacji wstępnej zasobów. Aby włączyć kompilację wstępną zasobów, określ [ustawienie aplikacji](configure-common.md#configure-app-settings) `ASSETS_PRECOMPILE` jako `true`. Na zakończenie wykonywania kroków po wdrożeniu będzie wówczas uruchamiane polecenie `bundle exec rake --trace assets:precompile`. Na przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

Aby uzyskać więcej informacji, zobacz [Obsługa zasobów statycznych](#serve-static-assets).

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślnie kontener języka Ruby uruchamia serwer Rails w następującej kolejności (aby uzyskać więcej informacji, zobacz [skrypt uruchamiania](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)):

1. Wygeneruj wartość [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security), jeśli jeszcze nie istnieje. Ta wartość jest wymagana, aby można było uruchomić aplikację w trybie produkcyjnym.
1. Ustaw zmienną środowiskową `RAILS_ENV` na `production`.
1. W katalogu *tmp/pids* usuń każdy plik *PID* pozostawiony przez wcześniej uruchomiony serwer Rails.
1. Sprawdź, czy są zainstalowane wszystkie zależności. W przeciwnym razie spróbuj zainstalować rozwiązania Gem z katalogu lokalnego *vendor/cache*.
1. Uruchom polecenie `rails server -e $RAILS_ENV`.

Proces uruchamiania można dostosować w następujący sposób:

- [Obsługa zasobów statycznych](#serve-static-assets)
- [Uruchamianie w trybie nieprodukcyjnym](#run-in-non-production-mode)
- [Ustawianie wartości secret_key_base ręcznie](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>Obsługa zasobów statycznych

Serwer Rails w kontenerze języka Ruby jest domyślnie uruchamiany w trybie produkcyjnym i [zakłada, że zasoby są wstępnie skompilowane oraz obsługiwane przez serwer internetowy](https://guides.rubyonrails.org/asset_pipeline.html#in-production). W celu obsługi zasobów statycznych z serwera Rails musisz wykonać dwie czynności:

- **Wstępnie skompilować zasoby** - [Skompiluj wstępnie zasoby statyczne w środowisku lokalnym](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation) i wdróż je ręcznie. Możesz również pozwolić, aby obsługiwał je aparat wdrażania (zobacz [Kompilacja wstępna zasobów](#precompile-assets).
- **Włączyć obsługę plików statycznych** — Aby obsługiwać zasoby statyczne z kontenera języka Ruby, [określ ustawienie aplikacji `RAILS_SERVE_STATIC_FILES`](configure-common.md#configure-app-settings) jako `true`. Na przykład:

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>Uruchamianie w trybie nieprodukcyjnym

Domyślnie serwer Rails jest uruchamiany w trybie produkcyjnym. Aby uruchomić go na przykład w trybie projektowania, określ [ustawienie aplikacji](configure-common.md#configure-app-settings) `RAILS_ENV` jako `development`.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

Jednak to ustawienie oddzielnie powoduje uruchamianie serwera Rails w trybie projektowania, który akceptuje tylko żądania hosta lokalnego i nie jest dostępny poza kontenerem. Aby akceptować żądania klientów zdalnych, określ [ustawienie aplikacji](configure-common.md#configure-app-settings) `APP_COMMAND_LINE` jako `rails server -b 0.0.0.0`. To ustawienie aplikacji umożliwia uruchomienie polecenia niestandardowego w kontenerze języka Ruby. Na przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a> Ustawianie wartości secret_key_base ręcznie

Aby używać własnej wartości `secret_key_base`, zamiast pozwalać na jej generowanie przez usługę App Service, określ [ustawienie aplikacji](configure-common.md#configure-app-settings) `SECRET_KEY_BASE`, używając wybranej wartości. Na przykład:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwieranie sesji SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Aplikacja Rails z usługą PostgreSQL](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](faq-app-service-linux.md)
