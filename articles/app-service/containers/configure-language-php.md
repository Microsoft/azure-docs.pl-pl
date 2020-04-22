---
title: Konfigurowanie aplikacji PHP
description: Dowiedz się, jak skonfigurować wstępnie utworzony kontener PHP dla aplikacji. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758879"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurowanie aplikacji PHP systemu Linux dla usługi Azure App Service

W tym przewodniku pokazano, jak skonfigurować wbudowany czas pracy PHP dla aplikacji sieci web, tylnych linii mobilnych i aplikacji interfejsu API w usłudze Azure App Service.

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla programistów PHP, którzy używają wbudowanego kontenera Linuksa w usłudze App Service. Jeśli nigdy nie korzystałeś z usługi Azure App Service, wykonaj najpierw [szybki start PHP](quickstart-php.md) i [PHP za pomocą samouczka MySQL.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>Pokaż wersję PHP

Aby wyświetlić bieżącą wersję PHP, uruchom następujące polecenie w [przyłówku Chmury:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje PHP, uruchom następujące polecenie w [usłudze Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Ustawianie wersji PHP

Uruchom następujące polecenie w [usłudze Cloud Shell,](https://shell.azure.com) aby ustawić wersję PHP na 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Dostosowywanie automatyzacji kompilacji

Jeśli wdrożysz aplikację przy użyciu pakietów Git lub zip z włączoną automatyzacją kompilacji, kroki automatyzacji kompilacji usługi App Service w następującej kolejności:

1. Uruchom skrypt niestandardowy, jeśli jest określony przez `PRE_BUILD_SCRIPT_PATH`plik .
1. Uruchom polecenie `php composer.phar install`.
1. Uruchom skrypt niestandardowy, jeśli jest określony przez `POST_BUILD_SCRIPT_PATH`plik .

`PRE_BUILD_COMMAND`i `POST_BUILD_COMMAND` są zmienne środowiskowe, które są domyślnie puste. Aby uruchomić polecenia przed kompilacją, zdefiniuj `PRE_BUILD_COMMAND`plik . Aby uruchomić polecenia po kompilacji, zdefiniuj `POST_BUILD_COMMAND`plik .

Poniższy przykład określa dwie zmienne do serii poleceń, oddzielone przecinkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Aby uzyskać dodatkowe zmienne środowiskowe w celu dostosowania automatyzacji kompilacji, zobacz [Konfiguracja Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Aby uzyskać więcej informacji na temat sposobu działania i tworzenia aplikacji PHP w systemie Linux, zobacz [dokumentację Oryx: Jak aplikacje PHP są wykrywane i budowane.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślnie wbudowany kontener PHP uruchamia serwer Apache. Przy uruchomieniu działa `apache2ctl -D FOREGROUND"`. Jeśli chcesz, możesz uruchomić inne polecenie podczas uruchamiania, uruchamiając następujące polecenie w [usłudze Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service możesz [ustawić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodem aplikacji. Następnie można uzyskać do nich dostęp za pomocą standardowego wzorca [getenv().](https://secure.php.net/manual/function.getenv.php) Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `DB_HOST`, użyj następującego kodu:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Zmienianie katalogu głównego witryny

Wybrane przez użytkownika struktury sieci Web mogą używać podkatalogu jako katalogu głównego witryny. Na przykład [Laravel](https://laravel.com/), `public/` używa podkatalogu jako katalogu głównego witryny.

Domyślny obraz PHP dla usługi App Service używa Apache i nie pozwala dostosować katalogu głównego witryny dla aplikacji. Aby obejść to ograniczenie, dodaj plik *.htaccess* do katalogu głównego repozytorium z następującą zawartością:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Jeśli nie chcesz używać ponownego zapisywania pliku *.htaccess*, zamiast tego możesz wdrożyć aplikację platformy Laravel przy użyciu [niestandardowego obrazu platformy Docker](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. Na platformie [CodeIgniter](https://codeigniter.com/) element [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) domyślnie sprawdza wartość `X_FORWARDED_PROTO`.

## <a name="customize-phpini-settings"></a>Dostosowywanie ustawień php.ini

Jeśli chcesz wprowadzić zmiany w instalacji PHP, możesz zmienić dowolną z [dyrektyw php.ini,](https://www.php.net/manual/ini.list.php) wykonując następujące kroki.

> [!NOTE]
> Najlepszym sposobem, aby zobaczyć wersję PHP i aktualną konfigurację *php.ini* jest [wywołanie phpinfo()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Dostosowywanie dyrektyw PHP_INI_SYSTEM innych niż PHP_INI_SYSTEM

Aby dostosować PHP_INI_USER, PHP_INI_PERDIR i PHP_INI_ALL (zobacz [dyrektywy php.ini](https://www.php.net/manual/ini.list.php)), dodaj plik *.htaccess* do katalogu głównego aplikacji.

W pliku *.htaccess* dodaj dyrektywy przy `php_value <directive-name> <value>` użyciu składni. Przykład:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ponownie rozmieszcz aplikację ze zmianami i uruchom ją ponownie. Jeśli wdrożysz go z Kudu (na przykład za pomocą [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), zostanie automatycznie uruchomiony ponownie po wdrożeniu.

Jako alternatywę dla korzystania *z .htaccess*, można użyć [ini_set()](https://www.php.net/manual/function.ini-set.php) w aplikacji, aby dostosować te dyrektywy PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Dostosowywanie dyrektyw PHP_INI_SYSTEM

Aby dostosować PHP_INI_SYSTEM dyrektyw (patrz [php.ini dyrektyw),](https://www.php.net/manual/ini.list.php)nie można użyć *.htaccess* podejście. Usługa App Service udostępnia `PHP_INI_SCAN_DIR` oddzielny mechanizm przy użyciu ustawienia aplikacji.

Najpierw uruchom następujące polecenie w [aplikacji Cloud Shell,](https://shell.azure.com) aby dodać ustawienie aplikacji o nazwie: `PHP_INI_SCAN_DIR`

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`jest domyślnym katalogiem, w którym istnieje *php.ini.* `/home/site/ini`to katalog niestandardowy, w którym dodasz niestandardowy plik *.ini.* Oddzielasz wartości za `:`pomocą pliku .

Przejdź do sesji SSH w sieci`https://<app-name>.scm.azurewebsites.net/webssh/host`Web za pomocą kontenera Linuksa ( ).

Utwórz katalog `/home/site` w `ini`wywoływanym , a następnie `/home/site/ini` utwórz plik *.ini* w katalogu (na przykład *settings.ini)* z dyrektywami, które chcesz dostosować. Użyj tej samej składni, której użyjesz w pliku *php.ini.* 

> [!TIP]
> We wbudowanych kontenerach systemu Linux w usłudze App Service */home* jest używany jako utrwalony udostępniony magazyn. 
>

Na przykład, aby zmienić wartość [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) uruchomić następujące polecenia:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby zmiany zostały wprowadzone, uruchom ponownie aplikację.

## <a name="enable-php-extensions"></a>Włączanie rozszerzeń PHP

Wbudowane instalacje PHP zawierają najczęściej używane rozszerzenia. Można włączyć dodatkowe rozszerzenia w taki sam sposób, w jaki [można dostosować dyrektywy php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Najlepszym sposobem, aby zobaczyć wersję PHP i aktualną konfigurację *php.ini* jest [wywołanie phpinfo()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

Aby włączyć dodatkowe rozszerzenia, wykonaj następujące kroki:

Dodaj `bin` katalog do katalogu głównego aplikacji i umieść `.so` w nim pliki rozszerzeń (na przykład *mongodb.so*). Upewnij się, że rozszerzenia są zgodne z wersją PHP na platformie Azure i są zgodne z VC9 i niebezgłębowymi (nts).

Wdrażaj zmiany.

Wykonaj kroki opisane w [obszarze Dostosuj PHP_INI_SYSTEM dyrektyw](#customize-php_ini_system-directives), dodaj rozszerzenia do niestandardowego pliku *.ini* z dyrektywami [rozszerzenia](https://www.php.net/manual/ini.core.php#ini.extension) lub [zend_extension.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Aby zmiany zostały wprowadzone, uruchom ponownie aplikację.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Gdy działająca aplikacja PHP zachowuje się inaczej w usłudze App Service lub ma błędy, spróbuj wykonać następujące czynności:

- [Dostęp do strumienia dziennika](#access-diagnostic-logs).
- Przetestuj aplikację lokalnie w trybie produkcyjnym. Usługa App Service uruchamia aplikacje Node.js w trybie produkcyjnym, dlatego należy upewnić się, że projekt działa zgodnie z oczekiwaniami w trybie produkcji lokalnie. Przykład:
    - W zależności od *pliku composer.json,* różne pakiety mogą być instalowane w trybie produkcji (w`require` porównaniu z. `require-dev`).
    - Niektóre struktury sieci web mogą inaczej wdrażać pliki statyczne w trybie produkcyjnym.
    - Niektóre struktury sieci web mogą używać niestandardowych skryptów startowych podczas pracy w trybie produkcyjnym.
- Uruchom aplikację w usłudze App Service w trybie debugowania. Na przykład w [laravel](https://meanjs.org/), można skonfigurować aplikację do wyprowadzania komunikatów debugowania w [ `APP_DEBUG` środowisku produkcyjnym, ustawiając ustawienie aplikacji na `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja PHP z MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Często zadawane pytania dotyczące usługi aplikacji Linux](app-service-linux-faq.md)
