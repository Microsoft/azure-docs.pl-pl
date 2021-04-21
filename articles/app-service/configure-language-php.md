---
title: Konfigurowanie aplikacji PHP
description: Dowiedz się, jak skonfigurować aplikację PHP w natywnych wystąpieniach systemu Windows lub we wstępnie skonfigurowym kontenerze PHP w Azure App Service. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 94cbe0fa6669546cee8e989a6db2fcbb428cb9d0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829444"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Konfigurowanie aplikacji PHP dla Azure App Service

W tym przewodniku pokazano, jak skonfigurować aplikacje internetowe PHP, zaimki mobilne i aplikacje interfejsu API w Azure App Service.

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla deweloperów języka PHP, którzy wdrażają aplikacje w App Service. Jeśli nigdy wcześniej nie używaliśmy języka Azure App Service, najpierw postępuj zgodnie z samouczkiem [PHP Quickstart](quickstart-php.md) and PHP with MySQL (Szybki start dla języka PHP i [php z programem MySQL).](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>Wyświetlanie wersji języka PHP

::: zone pivot="platform-windows"  

Aby wyświetlić bieżącą wersję języka PHP, uruchom następujące polecenie w Cloud Shell [:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

> [!NOTE]
> Aby rozwiązać ten temat, dołącz parametr `--slot` , po którym następuje nazwa miejsca.

Aby wyświetlić wszystkie obsługiwane wersje języka PHP, uruchom następujące polecenie w Cloud Shell [:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Aby wyświetlić bieżącą wersję języka PHP, uruchom następujące polecenie w Cloud Shell [:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

> [!NOTE]
> Aby rozwiązać ten temat, dołącz parametr `--slot` , po którym następuje nazwa miejsca.

Aby wyświetlić wszystkie obsługiwane wersje języka PHP, uruchom następujące polecenie w Cloud Shell [:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>Ustawianie wersji języka PHP

::: zone pivot="platform-windows"  

Uruchom następujące polecenie w [skrypcie Cloud Shell,](https://shell.azure.com) aby ustawić wersję php na 7.4:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Uruchom następujące polecenie w [skrypcie Cloud Shell,](https://shell.azure.com) aby ustawić wersję php na 7.2:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Uruchamianie aplikacji Composer

Jeśli chcesz, App Service [program Composer](https://getcomposer.org/) podczas wdrażania, najprostszym sposobem jest dołącznie do repozytorium programu Composer.

W lokalnym oknie terminalu zmień katalog na katalog główny repozytorium i postępuj zgodnie z instrukcjami na stronie [download Composer](https://getcomposer.org/download/) (Composer), aby pobrać *plik composer.phar* do katalogu głównego.

Uruchom następujące polecenia (musisz zainstalować [program npm):](https://www.npmjs.com/get-npm)

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Katalog główny repozytorium zawiera teraz dwa dodatkowe pliki: *.deployment* *i deploy.sh*.

Otwórz *deploy.sh* i znajdź `Deployment` sekcję , która wygląda następująco:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Na końcu sekcji dodaj sekcję kodu, która będzie potrzebna do uruchomienia *wymaganego* `Deployment` narzędzia:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Zat zatwierdzanie wszystkich zmian i wdrażanie kodu przy użyciu narzędzia Git lub wdrażanie pliku zip z włączoną automatyzacją kompilacji. Program Composer powinien teraz działać w ramach automatyzacji wdrażania.

## <a name="run-gruntbowergulp"></a>Uruchamianie Grunt/Bower/Gulp

Jeśli chcesz, App Service automatyzacji popularne narzędzia automatyzacji w czasie wdrażania, takie jak Grunt, Bower lub Gulp, musisz podać niestandardowy [skrypt wdrożenia](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). App Service uruchamia ten skrypt podczas wdrażania za [](deploy-zip.md) pomocą narzędzia Git lub z włączonym wdrożeniem zip z włączoną automatyzacją kompilacji. 

Aby umożliwić repozytorium uruchamianie tych narzędzi, należy dodać je do zależności wpackage.js *na.* Na przykład:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

W lokalnym oknie terminalu zmień katalog na katalog główny repozytorium i uruchom następujące polecenia [(musisz zainstalować program npm):](https://www.npmjs.com/get-npm)

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Katalog główny repozytorium zawiera teraz dwa dodatkowe pliki: *.deployment* *i deploy.sh*.

Otwórz *deploy.sh* i znajdź `Deployment` sekcję , która wygląda następująco:

```bash
##################################################################################################################################
# Deployment
# ----------
```

W tej sekcji kończy się uruchomieniem . `npm install --production` Na końcu sekcji dodaj sekcję kodu, która będzie potrzebna do uruchomienia *wymaganego* `Deployment` narzędzia:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Zobacz przykład [w przykładzie MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), w którym skrypt wdrażania uruchamia również polecenie `npm install` niestandardowe.

### <a name="bower"></a>Bower

Ten fragment kodu `bower install` uruchamia .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ten fragment kodu `gulp imagemin` uruchamia .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ten fragment kodu `grunt` uruchamia .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Dostosowywanie automatyzacji kompilacji

W przypadku wdrażania aplikacji przy użyciu pakietów Git lub zip z włączona automatyzacją kompilacji App Service kroki automatyzacji kompilacji w następującej kolejności:

1. Uruchom skrypt niestandardowy, jeśli jest określony przez `PRE_BUILD_SCRIPT_PATH` .
1. Uruchom polecenie `php composer.phar install`.
1. Uruchom skrypt niestandardowy, jeśli jest określony przez `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` i `POST_BUILD_COMMAND` to zmienne środowiskowe, które są domyślnie puste. Aby uruchamiać polecenia przed kompilacją, zdefiniuj `PRE_BUILD_COMMAND` . Aby uruchamiać polecenia po kompilacji, zdefiniuj `POST_BUILD_COMMAND` polecenie .

W poniższym przykładzie określono dwie zmienne dla serii poleceń rozdzielonych przecinkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Aby uzyskać dodatkowe zmienne środowiskowe w celu dostosowania automatyzacji kompilacji, zobacz [Oryx configuration (Konfiguracja narzędzia Oryx).](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)

Aby uzyskać więcej informacji na temat sposobu App Service i kompilowania aplikacji PHP w systemie Linux, zobacz dokumentację systemu [Oryx: Jak](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)są wykrywane i kompilowane aplikacje PHP.

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślnie wbudowany kontener PHP uruchamia serwer Apache. Podczas uruchamiania jest uruchamiany program `apache2ctl -D FOREGROUND"` . Jeśli chcesz, możesz uruchomić inne polecenie podczas uruchamiania, uruchamiając następujące polecenie w Cloud Shell [:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service można [określić ustawienia aplikacji](configure-common.md#configure-app-settings) poza kodem aplikacji. Następnie można uzyskać do nich dostęp przy użyciu standardowego [wzorca getenv().](https://secure.php.net/manual/function.getenv.php) Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `DB_HOST`, użyj następującego kodu:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Zmienianie katalogu głównego lokacji

::: zone pivot="platform-windows"  

Wybranej struktury sieci Web można użyć podkatalogu jako katalogu głównego witryny. Na przykład [laravel](https://laravel.com/)używa podkatalogu *public/jako* katalogu głównego witryny.

Aby dostosować katalog główny witryny, ustaw ścieżkę aplikacji wirtualnej dla aplikacji za pomocą [`az resource update`](/cli/azure/resource#az_resource_update) polecenia . Poniższy przykład ustawia katalog główny  witryny na publiczny/podkatalog w repozytorium. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Domyślnie program Azure App Service główną ścieżkę aplikacji wirtualnej ( ) do katalogu głównego wdrożonych plików aplikacji _/_ (_sites\wwwroot)._

::: zone-end

::: zone pivot="platform-linux"

Wybranej struktury sieci Web można użyć podkatalogu jako katalogu głównego witryny. Na przykład [laravel](https://laravel.com/)używa `public/` podkatalogu jako katalogu głównego witryny.

Domyślny obraz PHP dla App Service korzysta z serwera Apache i nie pozwala na dostosowywanie katalogu głównego witryny dla aplikacji. Aby ominą to ograniczenie, dodaj plik *htaccess* do katalogu głównego repozytorium o następującej zawartości:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Jeśli nie chcesz używać ponownego zapisywania pliku *.htaccess*, zamiast tego możesz wdrożyć aplikację platformy Laravel przy użyciu [niestandardowego obrazu platformy Docker](quickstart-custom-container.md).

::: zone-end

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. Na platformie [CodeIgniter](https://codeigniter.com/) element [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) domyślnie sprawdza wartość `X_FORWARDED_PROTO`.

## <a name="customize-phpini-settings"></a>Dostosowywanie php.ini ustawień

Jeśli musisz wprowadzić zmiany w instalacji języka PHP, możesz zmienić dowolną dyrektywę [php.ini,](https://www.php.net/manual/ini.list.php) korzystając z poniższych kroków.

> [!NOTE]
> Najlepszym sposobem na zobaczenie wersji php i bieżącej konfiguracji *php.ini* jest wywołanie [funkcji phpinfo()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Dostosowywanie dyrektyw innych PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Aby dostosować PHP_INI_USER, PHP_INI_PERDIR i PHP_INI_ALL (zobacz [ dyrektywyphp.ini](https://www.php.net/manual/ini.list.php)), dodaj plik do katalogu głównego `.user.ini` aplikacji.

Dodaj ustawienia konfiguracji do `.user.ini` pliku przy użyciu tej samej składni, co w `php.ini` pliku. Jeśli na przykład chcesz włączyć to ustawienie i ustawić ustawienie na `display_errors` `upload_max_filesize` 10 mln, plik `.user.ini` będzie zawierać ten tekst:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Ponownie wdoń aplikację ze zmianami i uruchom ją ponownie.

Alternatywą dla korzystania z pliku jest użycie metody `.user.ini` [ini_set()](https://www.php.net/manual/function.ini-set.php) w aplikacji w celu dostosowania tych dyrektyw PHP_INI_SYSTEM innych niż .

::: zone-end

::: zone pivot="platform-linux"

Aby dostosować PHP_INI_USER, PHP_INI_PERDIR i PHP_INI_ALL (zobacz dyrektywy [php.ini](https://www.php.net/manual/ini.list.php)), dodaj plik *htaccess* do katalogu głównego aplikacji.

W *pliku htaccess* dodaj dyrektywy przy użyciu `php_value <directive-name> <value>` składni . Na przykład:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ponownie wdoń aplikację ze zmianami i uruchom ją ponownie. Jeśli wdrożysz go za pomocą usługi Kudu (na przykład przy użyciu usługi [Git),](deploy-local-git.md)zostanie on automatycznie uruchomiony ponownie po wdrożeniu.

Alternatywą dla korzystania z *.htaccess* jest użycie metody [ini_set()](https://www.php.net/manual/function.ini-set.php) w aplikacji w celu dostosowania tych dyrektyw PHP_INI_SYSTEM innych niż .

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Dostosowywanie dyrektyw PHP_INI_SYSTEM danych

::: zone pivot="platform-windows"  

Aby dostosować PHP_INI_SYSTEM (zobacz [php.ini dyrektywy](https://www.php.net/manual/ini.list.php)), nie można użyć metody *.htaccess.* App Service udostępnia oddzielny mechanizm przy użyciu `PHP_INI_SCAN_DIR` ustawienia aplikacji.

Najpierw uruchom następujące polecenie [](https://shell.azure.com) w Cloud Shell, aby dodać ustawienie aplikacji o nazwie `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Przejdź do konsoli Kudu ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) i przejdź do . `d:\home\site`

Utwórz katalog w pliku o nazwie , a następnie utwórz plik ini w katalogu (na przykładsettings.ini) z dyrektywami, `d:\home\site` `ini` które chcesz  `d:\home\site\ini` dostosować.  Użyj tej samej składni, która byłabyphp.ini *pliku.* 

Aby na przykład zmienić wartość expose_php [uruchom](https://php.net/manual/ini.core.php#ini.expose-php) następujące polecenia:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby zmiany weszły w życie, uruchom ponownie aplikację.

::: zone-end

::: zone pivot="platform-linux"

Aby dostosować PHP_INI_SYSTEM (zobacz [php.ini dyrektywy](https://www.php.net/manual/ini.list.php)), nie można użyć *metody .htaccess.* App Service udostępnia oddzielny mechanizm przy użyciu `PHP_INI_SCAN_DIR` ustawienia aplikacji.

Najpierw uruchom następujące polecenie [](https://shell.azure.com) w Cloud Shell, aby dodać ustawienie aplikacji o nazwie `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` jest katalogiem domyślnym, *wphp.ini* istnieje. `/home/site/ini`to katalog niestandardowy, w którym dodasz niestandardowy plik *ini.* Wartości należy oddzielić za `:` pomocą .

Przejdź do internetowej sesji SSH z kontenerem systemu Linux ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Utwórz katalog w pliku o nazwie , a następnie utwórz plik ini w katalogu (na przykładsettings.ini) z dyrektywami, `/home/site` `ini` które chcesz  `/home/site/ini` dostosować.  Użyj tej samej składni, która byłabyphp.ini *pliku.* 

> [!TIP]
> We wbudowanych kontenerach systemu Linux w App Service */home* jest używany jako trwały magazyn udostępniony. 
>

Aby na przykład zmienić wartość expose_php [uruchom](https://php.net/manual/ini.core.php#ini.expose-php) następujące polecenia:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby zmiany weszły w życie, uruchom ponownie aplikację.

::: zone-end

## <a name="enable-php-extensions"></a>Włączanie rozszerzeń PHP

::: zone pivot="platform-windows"  

Wbudowane instalacje php zawierają najczęściej używane rozszerzenia. Dodatkowe rozszerzenia można włączyć w taki sam sposób, jak w przypadku [dostosowywania php.ini dyrektywy](#customize-php_ini_system-directives).

> [!NOTE]
> Najlepszym sposobem na zobaczenie wersji php i bieżącej konfiguracji *php.ini* jest wywołanie [funkcji phpinfo()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

Aby włączyć dodatkowe rozszerzenia, należy wykonać następujące kroki:

Dodaj katalog do katalogu głównego aplikacji i umieść w nim pliki rozszerzeń (na przykład `bin` `.dll` *mongodb.dll*). Upewnij się, że rozszerzenia są zgodne z wersją php na platformie Azure i są zgodne ze standardem VC9 i niebędące wątkami (nts).

Wdrażanie zmian.

Wykonaj kroki opisane w [te PHP_INI_SYSTEM dyrektywy](#customize-php_ini_system-directives), dodaj rozszerzenia do niestandardowego [](https://www.php.net/manual/ini.core.php#ini.extension) pliku *ini* z rozszerzeniem lub zend_extension [dyrektywy.](https://www.php.net/manual/ini.core.php#ini.zend-extension)

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Aby zmiany zaszły, uruchom ponownie aplikację.

::: zone-end

::: zone pivot="platform-linux"

Wbudowane instalacje języka PHP zawierają najczęściej używane rozszerzenia. Dodatkowe rozszerzenia można włączyć w taki sam sposób, jak w przypadku [dostosowywania php.ini dyrektywy](#customize-php_ini_system-directives).

> [!NOTE]
> Najlepszym sposobem na zobaczenie wersji php i bieżącej konfiguracji *php.ini* jest wywołanie [funkcji phpinfo()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

Aby włączyć dodatkowe rozszerzenia, należy wykonać następujące kroki:

Dodaj katalog do katalogu głównego aplikacji i umieść w nim pliki rozszerzeń (na przykład `bin` `.so` *mongodb.so*). Upewnij się, że rozszerzenia są zgodne z wersją php na platformie Azure i są zgodne ze standardem VC9 i niebędące wątkami (nts).

Wdrażanie zmian.

Wykonaj kroki opisane w [te PHP_INI_SYSTEM dyrektywy](#customize-php_ini_system-directives), dodaj rozszerzenia do niestandardowego [](https://www.php.net/manual/ini.core.php#ini.extension) pliku *ini* z rozszerzeniem lub [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) dyrektywy.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Aby zmiany zaszły, uruchom ponownie aplikację.

::: zone-end

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

::: zone pivot="platform-windows"  

Użyj standardowego [error_log(),](https://php.net/manual/function.error-log.php) aby dzienniki diagnostyczne pokazywały się w Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli robocza aplikacja PHP zachowuje się inaczej w App Service lub ma błędy, spróbuj wykonać następujące czynności:

- [Uzyskaj dostęp do strumienia dzienników](#access-diagnostic-logs).
- Testowanie aplikacji lokalnie w trybie produkcyjnym. App Service aplikację w trybie produkcyjnym, dlatego musisz upewnić się, że projekt działa zgodnie z oczekiwaniami lokalnie w trybie produkcyjnym. Na przykład:
    - W zależności *odcomposer.jsw programie* mogą być instalowane różne pakiety dla trybu produkcyjnego ( `require` vs. `require-dev` ).
    - Niektóre struktury internetowe mogą wdrażać pliki statyczne inaczej w trybie produkcyjnym.
    - Niektóre struktury internetowe mogą używać niestandardowych skryptów uruchamiania podczas uruchamiania w trybie produkcyjnym.
- Uruchom aplikację w trybie App Service debugowania. Na przykład na [platformie Laravel](https://meanjs.org/)możesz skonfigurować aplikację do wyprowadzania komunikatów debugowania w środowisku produkcyjnym, ustawiając [ustawienie aplikacji `APP_DEBUG` na `true` ](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja PHP z mySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](faq-app-service-linux.md)

::: zone-end
