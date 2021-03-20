---
title: Konfigurowanie aplikacji PHP
description: Dowiedz się, jak skonfigurować aplikację PHP w natywnych wystąpieniach systemu Windows lub we wstępnie skompilowanym kontenerze PHP w Azure App Service. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: afac8273b5729bcf5470be471145214426dc7dab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90055303"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Konfigurowanie aplikacji PHP dla Azure App Service

W tym przewodniku pokazano, jak skonfigurować aplikacje sieci Web w języku PHP, zaplecza mobilne i aplikacje interfejsu API w Azure App Service.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka PHP, którzy wdrażają aplikacje do App Service. Jeśli nie korzystasz z Azure App Service, najpierw postępuj zgodnie [z samouczkiem dotyczącym](tutorial-php-mysql-app.md) języka [php](quickstart-php.md) i środowiska php z programem MySQL.

## <a name="show-php-version"></a>Pokaż wersję języka PHP

::: zone pivot="platform-windows"  

Aby wyświetlić bieżącą wersję PHP, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Aby wyświetlić wszystkie obsługiwane wersje języka PHP, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Aby wyświetlić bieżącą wersję PHP, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje języka PHP, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>Ustaw wersję języka PHP

::: zone pivot="platform-windows"  

Uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby ustawić wersję PHP na 7,4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby ustawić wersję PHP na 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Uruchom układacz

Jeśli chcesz, aby App Service uruchamiać [układacz](https://getcomposer.org/) w czasie wdrażania, najprostszym sposobem jest uwzględnienie kompozytora w repozytorium.

W lokalnym oknie terminalu Zmień katalog na katalog główny repozytorium i postępuj zgodnie z instrukcjami w obszarze [pobieranie](https://getcomposer.org/download/) pakietu, aby pobrać *Composer. PHAR* do katalogu głównego katalogów.

Uruchom następujące polecenia (wymagane jest zainstalowanie [npm](https://www.npmjs.com/get-npm) ):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Katalog główny repozytorium ma teraz dwa dodatkowe pliki: *. Deployment* i *Deploy.sh*.

Otwórz *Deploy.sh* i Znajdź `Deployment` sekcję, która wygląda następująco:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Dodaj sekcję kodu, która będzie potrzebna do uruchomienia wymaganego narzędzia *na końcu* `Deployment` sekcji:

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

Zatwierdź wszystkie zmiany i Wdróż swój kod przy użyciu narzędzia Git lub Zastąp polecenie zip Deploy with Build Automation Enabled. Układacz powinien teraz działać w ramach automatyzacji wdrażania.

## <a name="run-gruntbowergulp"></a>Uruchom grunt/Bower/Gulp

Jeśli chcesz, aby App Service uruchamiać popularne narzędzia automatyzacji w czasie wdrażania, takie jak grunt, Bower lub Gulp, musisz podać [niestandardowy skrypt wdrożenia](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). App Service uruchamia ten skrypt podczas wdrażania przy użyciu usługi Git lub z włączonym [wdrożeniem zip](deploy-zip.md) z włączoną automatyzacją kompilacji. 

Aby umożliwić repozytorium uruchamianie tych narzędzi, należy dodać je do zależności w *package.js.* Na przykład:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

W oknie terminalu lokalnego Zmień katalog na katalog główny repozytorium i uruchom następujące polecenia (wymagane jest zainstalowanie [npm](https://www.npmjs.com/get-npm) ):

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Katalog główny repozytorium ma teraz dwa dodatkowe pliki: *. Deployment* i *Deploy.sh*.

Otwórz *Deploy.sh* i Znajdź `Deployment` sekcję, która wygląda następująco:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ta sekcja kończy się z uruchomionym programem `npm install --production` . Dodaj sekcję kodu, która będzie potrzebna do uruchomienia wymaganego narzędzia *na końcu* `Deployment` sekcji:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Zapoznaj się z [przykładem w przykładzie MEAN.js, w](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)którym skrypt wdrażania również uruchamia `npm install` polecenie niestandardowe.

### <a name="bower"></a>Bower

Ten fragment kodu jest uruchamiany `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ten fragment kodu jest uruchamiany `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ten fragment kodu jest uruchamiany `grunt` .

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

## <a name="customize-build-automation"></a>Dostosuj automatyzację kompilacji

Jeśli aplikacja zostanie wdrożona za pomocą usługi Git lub zip z włączonym automatyzacją kompilacji, App Service kroki automatyzacji kompilacji w następującej kolejności:

1. Uruchom skrypt niestandardowy, jeśli został określony przez `PRE_BUILD_SCRIPT_PATH` .
1. Uruchom polecenie `php composer.phar install`.
1. Uruchom skrypt niestandardowy, jeśli został określony przez `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` i `POST_BUILD_COMMAND` są zmiennymi środowiskowymi, które są domyślnie puste. Aby uruchomić polecenia przed kompilacją, zdefiniuj `PRE_BUILD_COMMAND` . Aby uruchomić polecenia po kompilacji, zdefiniuj `POST_BUILD_COMMAND` .

W poniższym przykładzie określono dwie zmienne do szeregu poleceń, oddzielone przecinkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Aby uzyskać dodatkowe zmienne środowiskowe umożliwiające dostosowanie automatyzacji kompilacji, zobacz [Konfiguracja Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Aby uzyskać więcej informacji na temat sposobu uruchamiania i kompilowania aplikacji PHP w systemie Linux przez App Service, zobacz [dokumentację Oryx: jak wykrywane są i kompilowane aplikacje PHP](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślnie wbudowany kontener PHP uruchamia serwer Apache. Po uruchomieniu działa `apache2ctl -D FOREGROUND"` . Jeśli chcesz, możesz uruchomić inne polecenie podczas uruchamiania, uruchamiając następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W usłudze App Service można [określić ustawienia aplikacji](configure-common.md#configure-app-settings) poza kodem aplikacji. Następnie możesz uzyskać do nich dostęp przy użyciu standardowego wzorca [getenv ()](https://secure.php.net/manual/function.getenv.php) . Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `DB_HOST`, użyj następującego kodu:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Zmień katalog główny witryny

::: zone pivot="platform-windows"  

Wybrana platforma sieci Web może używać podkatalogu jako katalogu głównego witryny. Na przykład [platformy laravel](https://laravel.com/)używa *publicznego/* podkatalogu jako katalogu głównego lokacji.

Aby dostosować katalog główny witryny, Ustaw ścieżkę aplikacji wirtualnej dla aplikacji za pomocą [`az resource update`](/cli/azure/resource#az-resource-update) polecenia. Poniższy przykład ustawia katalog główny witryny jako *publiczny/* podkatalog w repozytorium. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Domyślnie Azure App Service punkty głównej ścieżki aplikacji wirtualnej ( _/_ ) do katalogu głównego wdrożonych plików aplikacji (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

Wybrana platforma sieci Web może używać podkatalogu jako katalogu głównego witryny. Na przykład [platformy laravel](https://laravel.com/)używa `public/` podkatalogu jako katalogu głównego witryny.

Domyślny obraz PHP dla App Service korzysta z platformy Apache i nie pozwala na dostosowywanie katalogu głównego witryny dla aplikacji. Aby obejść to ograniczenie, należy dodać plik *. htaccess* do katalogu głównego repozytorium o następującej zawartości:

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

## <a name="customize-phpini-settings"></a>Dostosuj ustawienia php.ini

Jeśli musisz wprowadzić zmiany w instalacji PHP, możesz zmienić dowolne z [php.ini dyrektyw](https://www.php.net/manual/ini.list.php) , wykonując następujące kroki.

> [!NOTE]
> Najlepszym sposobem na wyświetlenie wersji języka PHP i bieżącej konfiguracji *php.ini* jest wywołanie [phpinfo ()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Dostosowywanie — dyrektywy inne niż PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Aby dostosować dyrektywy PHP_INI_USER, PHP_INI_PERDIR i PHP_INI_ALL (patrz [ dyrektywyphp.ini](https://www.php.net/manual/ini.list.php)), Dodaj `.user.ini` plik do katalogu głównego aplikacji.

Dodaj ustawienia konfiguracji do `.user.ini` pliku, używając tej samej składni, która będzie używana w `php.ini` pliku. Jeśli na przykład chcesz włączyć `display_errors` ustawienie i ustawić wartość `upload_max_filesize` 10 mln, `.user.ini` plik będzie zawierał następujący tekst:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Ponownie Wdróż aplikację ze zmianami, a następnie uruchom ją.

Alternatywą dla użycia pliku jest `.user.ini` użycie [ini_set ()](https://www.php.net/manual/function.ini-set.php) w aplikacji, aby dostosować te dyrektywy nie PHP_INI_SYSTEM.

::: zone-end

::: zone pivot="platform-linux"

Aby dostosować dyrektywy PHP_INI_USER, PHP_INI_PERDIR i PHP_INI_ALL (patrz [ dyrektywyphp.ini](https://www.php.net/manual/ini.list.php)), Dodaj plik *. htaccess* do katalogu głównego aplikacji.

W pliku *. htaccess* Dodaj dyrektywy przy użyciu `php_value <directive-name> <value>` składni. Na przykład:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ponownie Wdróż aplikację ze zmianami, a następnie uruchom ją. Jeśli zostanie wdrożony za pomocą kudu (na przykład przy użyciu usługi [git](deploy-local-git.md)), zostanie on automatycznie uruchomiony ponownie po wdrożeniu.

Jako alternatywę dla korzystania z *. htaccess* można użyć [ini_set ()](https://www.php.net/manual/function.ini-set.php) w aplikacji, aby dostosować te dyrektywy niePHP_INI_SYSTEMowe.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Dostosuj dyrektywy PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Aby dostosować dyrektywy PHP_INI_SYSTEM (patrz [php.ini dyrektyw](https://www.php.net/manual/ini.list.php)), nie można użyć metody *. htaccess* . App Service zapewnia oddzielny mechanizm przy użyciu `PHP_INI_SCAN_DIR` Ustawienia aplikacji.

Najpierw uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby dodać ustawienie aplikacji o nazwie `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Przejdź do konsoli kudu ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) i przejdź do `d:\home\site` .

Utwórz katalog w `d:\home\site` nazwie `ini` , a następnie utwórz plik *. ini* w `d:\home\site\ini` katalogu (na przykład *settings.ini)* z dyrektywami, które chcesz dostosować. Użyj tej samej składni, która będzie używana w pliku *php.ini* . 

Na przykład aby zmienić wartość [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) Uruchom następujące polecenia:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić aplikację.

::: zone-end

::: zone pivot="platform-linux"

Aby dostosować dyrektywy PHP_INI_SYSTEM (patrz [php.ini dyrektyw](https://www.php.net/manual/ini.list.php)), nie można użyć metody *. htaccess* . App Service zapewnia oddzielny mechanizm przy użyciu `PHP_INI_SCAN_DIR` Ustawienia aplikacji.

Najpierw uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby dodać ustawienie aplikacji o nazwie `PHP_INI_SCAN_DIR` :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` jest domyślnym katalogiem, w którym istnieje *php.ini* . `/home/site/ini` jest katalogiem niestandardowym, w którym zostanie dodany niestandardowy plik *. ini* . Wartości należy rozdzielić `:` .

Przejdź do sesji protokołu SSH sieci Web przy użyciu kontenera systemu Linux ( `https://<app-name>.scm.azurewebsites.net/webssh/host` ).

Utwórz katalog w `/home/site` nazwie `ini` , a następnie utwórz plik *. ini* w `/home/site/ini` katalogu (na przykład *settings.ini)* z dyrektywami, które chcesz dostosować. Użyj tej samej składni, która będzie używana w pliku *php.ini* . 

> [!TIP]
> W wbudowanych kontenerach systemu Linux w App Service */Home* jest używany jako utrwalony magazyn udostępniony. 
>

Na przykład aby zmienić wartość [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) Uruchom następujące polecenia:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić aplikację.

::: zone-end

## <a name="enable-php-extensions"></a>Włącz rozszerzenia PHP

::: zone pivot="platform-windows"  

Wbudowane instalacje języka PHP zawierają najczęściej używane rozszerzenia. Dodatkowe rozszerzenia można włączyć w taki sam sposób, w jaki [dostosowuje się php.ini dyrektyw](#customize-php_ini_system-directives).

> [!NOTE]
> Najlepszym sposobem na wyświetlenie wersji języka PHP i bieżącej konfiguracji *php.ini* jest wywołanie [phpinfo ()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

Aby włączyć dodatkowe rozszerzenia, wykonaj następujące czynności:

Dodaj `bin` katalog do katalogu głównego aplikacji i umieść `.dll` w nim pliki rozszerzeń (na przykład *mongodb.dll*). Upewnij się, że rozszerzenia są zgodne z wersją języka PHP na platformie Azure i są zgodne z VC9 i niebezpiecznym wątkem (nkty przerwania).

Wdróż zmiany.

Wykonaj kroki opisane w sekcji [dostosowywanie PHP_INI_SYSTEM dyrektyw](#customize-php_ini_system-directives), Dodaj rozszerzenia do pliku Custom *. ini* z [rozszerzeniem](https://www.php.net/manual/ini.core.php#ini.extension) lub [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) dyrektyw.

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić aplikację.

::: zone-end

::: zone pivot="platform-linux"

Wbudowane instalacje języka PHP zawierają najczęściej używane rozszerzenia. Dodatkowe rozszerzenia można włączyć w taki sam sposób, w jaki [dostosowuje się php.ini dyrektyw](#customize-php_ini_system-directives).

> [!NOTE]
> Najlepszym sposobem na wyświetlenie wersji języka PHP i bieżącej konfiguracji *php.ini* jest wywołanie [phpinfo ()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

Aby włączyć dodatkowe rozszerzenia, wykonaj następujące czynności:

Dodaj `bin` katalog do katalogu głównego aplikacji i umieść `.so` w nim pliki rozszerzeń (na przykład *MongoDB.so*). Upewnij się, że rozszerzenia są zgodne z wersją języka PHP na platformie Azure i są zgodne z VC9 i niebezpiecznym wątkem (nkty przerwania).

Wdróż zmiany.

Wykonaj kroki opisane w sekcji [dostosowywanie PHP_INI_SYSTEM dyrektyw](#customize-php_ini_system-directives), Dodaj rozszerzenia do pliku Custom *. ini* z [rozszerzeniem](https://www.php.net/manual/ini.core.php#ini.extension) lub [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) dyrektyw.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić aplikację.

::: zone-end

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

::: zone pivot="platform-windows"  

Użyj narzędzia standardowego [error_log ()](https://php.net/manual/function.error-log.php) , aby dzienniki diagnostyczne były wyświetlane w Azure App Service.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Gdy działająca aplikacja PHP działa inaczej w App Service lub zawiera błędy, spróbuj wykonać następujące czynności:

- [Dostęp do strumienia dzienników](#access-diagnostic-logs).
- Przetestuj aplikację lokalnie w trybie produkcyjnym. App Service uruchamia aplikację w trybie produkcyjnym, dlatego należy się upewnić, że projekt działa zgodnie z oczekiwaniami w trybie produkcyjnym lokalnie. Na przykład:
    - W zależności od *composer.jsw systemie* można zainstalować różne pakiety dla trybu produkcyjnego ( `require` vs. `require-dev` ).
    - Niektóre platformy sieci Web mogą wdrażać pliki statyczne inaczej w trybie produkcyjnym.
    - Niektóre platformy sieci Web mogą używać niestandardowych skryptów uruchamiania podczas pracy w trybie produkcyjnym.
- Uruchom aplikację w App Service w trybie debugowania. Na przykład w [platformy laravel](https://meanjs.org/)można skonfigurować aplikację do wyprowadzania komunikatów debugowania w środowisku produkcyjnym, [ustawiając `APP_DEBUG` ustawienie aplikacji na `true` ](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja PHP z bazą danych MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](faq-app-service-linux.md)

::: zone-end

