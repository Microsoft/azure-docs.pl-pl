---
title: 'Samouczek: tworzenie aplikacji PHP (Laravel) za pomocą Azure Database for MySQL Serwera elastycznego'
description: W tym samouczku wyjaśniono, jak utworzyć aplikację PHP przy użyciu serwera elastycznego.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bb38b72af6e7c649c0904c41d3052b15a4c36955
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770061"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Samouczek: tworzenie aplikacji PHP (Laravel) i MySQL Flexible Server (wersja zapoznawcza) w Azure App Service


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Aplikacja internetowa PHP na platformie Azure z serwerem elastycznym":::

[Azure App Service](../../app-service/overview.md) zapewnia wysoce skalowalną i samonadajną usługę hostingu w Internecie korzystającą z systemu operacyjnego Linux. W tym samouczku pokazano, jak utworzyć aplikację języka PHP na platformie Azure i połączyć ją z bazą danych MySQL. Po zakończeniu aplikacja [laravel](https://laravel.com/) będzie działać na platformie Azure App Service dla systemu Linux.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie aplikacji PHP (Laravel) przy użyciu lokalnego programu MySQL
> * Tworzenie serwera elastycznego MySQL (wersja zapoznawcza)
> * Łączenie aplikacji PHP z serwerem elastycznym MySQL (wersja zapoznawcza)
> * Wdrażanie aplikacji w Azure App Service
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Zarządzanie aplikacją w witrynie Azure Portal

Jeśli nie masz subskrypcji platformy [Azure,](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto.

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Zainstaluj środowisko PHP w wersji 5.6.4 lub nowszej](https://php.net/downloads.php)
3. [Zainstaluj oprogramowanie Composer](https://getcomposer.org/doc/00-intro.md)
4. Włącz następujące rozszerzenia PHP wymagane przez platformę Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer i XML
5. [Zainstaluj i uruchom oprogramowanie MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

## <a name="prepare-local-mysql"></a>Przygotowywanie lokalnego środowiska MySQL

W tym kroku utworzysz bazę danych na lokalnym serwerze MySQL przeznaczoną do użytku w tym samouczku.

### <a name="connect-to-local-mysql-server"></a>Nawiązywanie połączenia z lokalnym serwerem MySQL

W oknie terminala nawiąż połączenie z lokalnym serwerem MySQL. W tym oknie terminala możesz uruchamiać wszystkie polecenia z tego samouczka.

```bash
mysql -u root -p
```

Jeśli zostanie wyświetlony monit o hasło, wprowadź hasło do konta `root`. Jeśli nie pamiętasz hasła do konta root, zobacz [MySQL: How to Reset the Root Password (MySQL: Jak zresetować hasło konta root)](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Jeśli polecenie zostanie pomyślnie uruchomione, oznacza to, że serwer MySQL działa. Jeśli nie, upewnij się, że lokalny serwer MySQL został uruchomiony, postępując zgodnie z [procedurą poinstalacyjną MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Tworzenie bazy danych lokalnie

Utwórz bazę danych w wierszu polecenia `mysql`.

```sql
CREATE DATABASE sampledb;
```

Zakończ połączenie z serwerem, wpisując polecenie `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Tworzenie aplikacji PHP lokalnie
W tym kroku otrzymasz przykładową aplikację platformy Laravel, skonfigurujesz jej połączenie z bazą danych i uruchomisz ją lokalnie.

### <a name="clone-the-sample"></a>Klonowanie przykładu

W oknie terminalu przejdź do pustego katalogu, w którym możesz sklonować przykładową aplikację.  Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

Przy użyciu polecenia `cd` przejdź do sklonowanego katalogu.
Zainstaluj wymagane pakiety.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Konfigurowanie połączenia z serwerem MySQL

W katalogu głównym repozytorium utwórz plik o nazwie *env*. Skopiuj poniższe zmienne do pliku *env*. Zastąp _&lt; symbol root_password>_ zastępczy hasłem użytkownika głównego mySQL.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Aby uzyskać informacje o tym, jak platforma Laravel używa pliku _env_, zobacz [Laravel Environment Configuration (Konfiguracja środowiska Laravel)](https://laravel.com/docs/5.4/configuration#environment-configuration).

### <a name="run-the-sample-locally"></a>Uruchamianie przykładu lokalnie

Uruchom [migracje bazy danych platformy Laravel](https://laravel.com/docs/5.4/migrations), aby utworzyć tabele wymagane przez aplikację. Aby zobaczyć, które tabele zostały utworzone w migracjach, zajrzyj do katalogu _database/migrations_ w repozytorium Git.

```bash
php artisan migrate
```

Wygeneruj nowy klucz aplikacji platformy Laravel.

```bash
php artisan key:generate
```

Uruchom aplikację.

```bash
php artisan serve
```

W przeglądarce przejdź do adresu `http://localhost:8000`. Dodaj na stronie kilka zadań.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="Pomyślne połączenie środowiska PHP z serwerem MySQL":::

Aby zatrzymać środowisko PHP, naciśnij w terminalu klawisze `Ctrl + C`.

## <a name="create-a-mysql-flexible-server-preview"></a>Tworzenie serwera elastycznego MySQL (wersja zapoznawcza)
W tym kroku utworzysz bazę danych MySQL na serwerze [Azure Database for MySQL,](../index.yml) który jest w publicznej wersji zapoznawczej. Następnie skonfigurujesz aplikację PHP i połączysz ją z tą bazą danych. W [Azure Cloud Shell](../../cloud-shell/overview.md)utwórz serwer w programie za pomocą [`az flexible-server create`](/cli/azure/mysql/server#az_mysql_flexible_server_create) polecenia .

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Zanotuj nazwę **serwera** i **ciąg** połączenia, aby użyć ich w następnym kroku do nawiązania połączenia i uruchomienia migracji danych laravel.
> - W **przypadku argumentu Adres**  IP podaj adres IP maszyny klienckiej. Serwer jest blokowany podczas tworzenia i musisz zezwolić na dostęp do maszyny klienckiej, aby zarządzać serwerem lokalnie.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Konfigurowanie zapory serwera w celu umożliwienia aplikacji internetowej nawiązania połączenia z serwerem

W Cloud Shell utwórz regułę zapory dla serwera MySQL, aby zezwolić na połączenia klienckie, używając polecenia az mysql server firewall-rule create. Jeśli początkowy i końcowy adres IP są ustawione na wartość , zapora jest otwierana tylko dla innych usług platformy Azure, które nie mają statycznego adresu IP do łączenia ```0.0.0.0``` się z serwerem.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Nawiązywanie połączenia z serwerem produkcyjnym MySQL lokalnie

W oknie terminala lokalnego nawiąż połączenie z serwerem MySQL na platformie Azure. Użyj określonej wcześniej wartości dla ```<admin-user>``` i ```<mysql-server-name>``` . Gdy zostanie wyświetlone pytanie o hasło, podaj hasło określone podczas tworzenia bazy danych na platformie Azure.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Tworzenie produkcyjnej bazy danych

Utwórz bazę danych w wierszu polecenia `mysql`.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Tworzenie użytkownika z uprawnieniami

Utwórz użytkownika bazy danych o nazwie _phpappuser_ i nadaj mu wszystkie uprawnienia w bazie danych `sampledb`. Dla uproszczenia samouczka użyj hasła _MySQLAzure2020._

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Zakończ połączenie z serwerem, wpisując polecenie `quit`.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Łączenie aplikacji z elastycznym serwerem MySQL

W tym kroku połączysz aplikację PHP z bazą danych MySQL utworzoną w usłudze Azure Database for MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurowanie połączenia z bazą danych

W katalogu głównym repozytorium utwórz plik _env.production_ i skopiuj do niego poniższe zmienne. Zastąp symbol zastępczy _&lt; mysql-server-name>_ w obu *DB_HOST* i *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Zapisz zmiany.

> [!TIP]
> Aby zabezpieczyć informacje o połączeniu MySQL, ten plik jest już wykluczony z repozytorium Git (zobacz plik _gitignore_ w katalogu głównym repozytorium). W dalszej części dowiesz się, jak skonfigurować zmienne środowiskowe w usłudze App Service, aby nawiązać połączenie z bazą danych w usłudze Azure Database for MySQL. Zmienne środowiskowe nie wymagają pliku *env* w usłudze App Service.
>

### <a name="configure-tlsssl-certificate"></a>Konfigurowanie certyfikatu TLS/SSL

Domyślnie serwer elastyczny MySQL wymusza połączenia TLS od klientów. Aby nawiązać połączenie z bazą danych MySQL na platformie Azure, musisz użyć certyfikatu [ _PEM_](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)dostarczonego przez Azure Database for MySQL Elastyczny serwer. Pobierz [ten certyfikat](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)) i umieść go w folderze **SSL** w lokalnej kopii repozytorium przykładowej aplikacji.

Otwórz plik _config/database.php_, a następnie dodaj parametry `sslmode` i `options` do elementu `connections.mysql`, jak pokazano w poniższym kodzie.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

### <a name="test-the-application-locally"></a>Testowanie aplikacji w środowisku lokalnym

Uruchom migracje baz danych platformy Laravel z plikiem _env.production_ jako plikiem środowiska, aby utworzyć tabele w bazie danych MySQL w usłudze Azure Database for MySQL. Pamiętaj, że plik _env.production_ zawiera informacje o połączeniu z bazą danych MySQL na platformie Azure.

```bash
php artisan migrate --env=production --force
```

Plik _env.production_ nie zawiera jeszcze prawidłowego klucza aplikacji. Wygeneruj dla niego nowy klucz w terminalu.

```bash
php artisan key:generate --env=production --force
```

Uruchom przykładową aplikację z plikiem _env.production_ jako plikiem środowiska.

```bash
php artisan serve --env=production
```

Przejdź do adresu `http://localhost:8000`. Jeśli strona ładuje się bez błędów, oznacza to, że aplikacja PHP nawiązuje połączenie z bazą danych MySQL na platformie Azure.

Dodaj na stronie kilka zadań.

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="Pomyślne połączenie środowiska PHP z usługą Azure Database for MySQL":::

Aby zatrzymać środowisko PHP, naciśnij w terminalu klawisze `Ctrl + C`.

### <a name="commit-your-changes"></a>Zatwierdzanie zmian

Aby zatwierdzić zmiany, uruchom następujące polecenia Git:

```bash
git add .
git commit -m "database.php updates"
```

Aplikacja jest gotowa do wdrożenia.

## <a name="deploy-to-azure"></a>Wdróż na platformie Azure

W tym kroku wdrożysz aplikację PHP połączoną z bazą danych MySQL w usłudze Azure App Service.

### <a name="configure-a-deployment-user"></a>Konfigurowanie użytkownika wdrożenia

Protokół FTP i lokalne narzędzie Git można wdrożyć w aplikacji internetowej platformy Azure przy użyciu użytkownika wdrożenia. Po skonfigurowaniu użytkownika wdrożenia można go używać we wszystkich wdrożeniach platformy Azure. Nazwa użytkownika i hasło wdrożenia na poziomie konta różnią się od poświadczeń subskrypcji platformy Azure.

Aby skonfigurować użytkownika wdrożenia, uruchom [polecenie az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) w Azure Cloud Shell. Zastąp _&lt; nazwę>_ _&lt; i hasło>_ nazwą użytkownika wdrożenia i hasłem.

Nazwa użytkownika musi być unikatowa w obrębie platformy Azure, a w przypadku lokalnych wypchnięć Git nie może zawierać symbolu "@".
Hasło musi mieć co najmniej osiem znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

Dane wyjściowe JSON pokazują hasło jako null. Jeśli występuje konflikt. Szczegóły: Błąd 409. Zmień nazwę użytkownika. Jeśli otrzymasz "Złe żądanie". Szczegóły: błąd 400, użyj silniejszego hasła. **Zanotuj nazwę użytkownika i hasło, które będą stosowane do wdrażania aplikacji internetowych.**

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

W Cloud Shell utwórz plan App Service grupie zasobów za pomocą polecenia [az appservice plan create.](/cli/azure/appservice/plan#az_appservice_plan_create) Poniższy przykład tworzy plan usługi App Service o nazwie myAppServicePlan w warstwie cenowej Bezpłatna (--sku F1) i w kontenerze systemu Linux (--is-linux).

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Utwórz aplikację [internetową w](../../app-service/overview.md#app-service-on-linux) planie App Service myAppServicePlan.

W Cloud Shell możesz użyć polecenia [az webapp create.](/cli/azure/webapp#az_webapp_create) W poniższym przykładzie zastąp nazwę _&lt; aplikacji_ nazwą>globalnie unikatową nazwą aplikacji (prawidłowe znaki to `a-z` , i `0-9` `-` ). Środowisko uruchomieniowe ma ustawioną wartość `PHP|7.0`. Aby wyświetlić wszystkie obsługiwane środowiska uruchomieniowe, uruchom [az webapp list-runtimes --linux.](/cli/azure/webapp#az_webapp_list_runtimes)

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Utworzona została nowa pusta aplikacja internetowa z włączonym wdrażaniem git.

> [!NOTE]
> Adres URL zdalnego repozytorium Git jest wyświetlany we właściwości deploymentLocalGitUrl w formacie https:// <username> @<app-name>.scm.azurewebsites.net/<app-name>.git. Zapisz ten adres URL, ponieważ będzie on potrzebny później.

### <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych

W usłudze App Service zmienne środowiskowe są ustawiane jako _ustawienia aplikacji_ za pomocą polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

Następujące polecenie konfiguruje ustawienia aplikacji `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` i `DB_PASSWORD`. Zastąp symbole zastępcze _&lt; app-name>_ i _&lt; mysql-server-name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

Dostęp do tych ustawień możesz uzyskać za pomocą metody [getenv](https://www.php.net/manual/en/function.getenv.php) języka PHP. W kodzie platformy Laravel otoka [env](https://laravel.com/docs/5.4/helpers#method-env) opakowuje metodę `getenv` języka PHP. Na przykład konfiguracja bazy danych MySQL w pliku _config/database.php_ wygląda jak poniższy kod:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Konfigurowanie zmiennych środowiskowych platformy Laravel

Platforma Laravel wymaga klucza aplikacji w usłudze App Service. Możesz go skonfigurować za pomocą ustawień aplikacji.

W oknie terminala lokalnego przy użyciu polecenia `php artisan` wygeneruj nowy klucz aplikacji bez zapisywania go w pliku _env_.

```bash
php artisan key:generate --show
```

W Cloud Shell ustaw klucz aplikacji w aplikacji App Service za pomocą [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) polecenia . Zastąp symbole zastępcze _&lt; app-name>_ _&lt; i outputofphpartisankey:generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

Właściwość `APP_DEBUG="true"` informuje platformę Laravel, aby zwróciła informacje debugowania, gdy wdrożona aplikacja napotka błędy. Podczas uruchamiania aplikacji produkcyjnej ustaw ją na wartość `false`, co zapewnia większe bezpieczeństwo.

### <a name="set-the-virtual-application-path"></a>Ustawianie wirtualnej ścieżki aplikacji

[Cykl życia aplikacji laravel](https://laravel.com/docs/5.4/lifecycle) rozpoczyna się _w katalogu publicznym,_ a nie w katalogu głównym aplikacji. Domyślny obraz platformy Docker w środowisku PHP dla usługi App Service używa platformy Apache i nie pozwala na dostosowywanie elementu `DocumentRoot` dla platformy Laravel. Za pomocą pliku `.htaccess` możesz jednak ponownie zapisać wszystkie żądania tak, aby wskazywały katalog _/public_ zamiast katalogu głównego. W tym celu do katalogu głównego repozytorium dodano plik `.htaccess`. Dzięki niemu aplikacja platformy Laravel jest gotowa do wdrożenia.

Aby uzyskać więcej informacji, zobacz [Zmienianie katalogu głównego lokacji](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root).

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

W lokalnym oknie terminala dodaj zdalną platformę Azure do lokalnego repozytorium Git. Zastąp _&lt; adres deploymentLocalGitUrl-from-create-step>_ adresem URL zdalnego repozytorium Git zapisanym w kroku Tworzenie aplikacji [internetowej.](#create-a-web-app)

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy usługa Git Menedżer poświadczeń wyświetli monit o podanie poświadczeń, upewnij się, że wprowadzasz poświadczenia utworzone podczas konfigurowania użytkownika wdrożenia, **a** nie poświadczenia używane do logowania się do Azure Portal.

```bash
git push azure main
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do adresu `http://<app-name>.azurewebsites.net` i dodaj kilka zadań do listy.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="Aplikacja internetowa w języku PHP na platformie Azure":::

Gratulacje! Masz uruchomioną opartą na danych aplikację PHP w usłudze Azure App Service.

## <a name="update-model-locally-and-redeploy"></a>Lokalne aktualizowanie modelu i ponowne wdrażanie

W tym kroku wprowadzisz prostą zmianę w modelu danych `task` i aplikacji internetowej, a następnie opublikujesz tę aktualizację na platformie Azure.

W scenariuszu tego zadania zmodyfikujesz aplikację tak, aby można było oznaczyć zadanie jako zakończone.

### <a name="add-a-column"></a>Dodawanie kolumny

W oknie terminala lokalnego przejdź do katalogu głównego repozytorium Git.

Wygeneruj nową migrację bazy danych dla tabeli `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

To polecenie wyświetla nazwę wygenerowanego pliku migracji. Znajdź ten plik w katalogu _database/migrations_ i otwórz go.

Zastąp metodę `up` poniższym kodem:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Poprzedzający kod dodaje kolumnę logiczną w tabeli `tasks` o nazwie `complete`.

Zastąp metodę `down` poniższym kodem dla akcji wycofania:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

W lokalnym oknie terminala uruchom migracje baz danych platformy Laravel, aby wprowadzić zmianę w lokalnej bazie danych.

```bash
php artisan migrate
```

Zgodnie z [konwencją nazewnictwa platformy Laravel](https://laravel.com/docs/5.4/eloquent#defining-models) model `Task` (zobacz plik _app/Task.php_) jest domyślnie mapowany na tabelę `tasks`.

### <a name="update-application-logic"></a>Aktualizowanie logiki aplikacji

Otwórz plik *routes/web.php*. W tym miejscu aplikacja określa swoje trasy i logikę biznesową.

Na końcu pliku dodaj trasę z następującym kodem:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Poprzedzający kod wprowadza prostą aktualizację do modelu danych, przełączając wartość kolumny `complete`.

### <a name="update-the-view"></a>Aktualizowanie widoku

Otwórz plik *resources/views/tasks.blade.php*. Znajdź tag otwierający `<tr>` i zastąp go następującym kodem:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Poprzedzający kod zmienia kolor wiersza w zależności od tego, czy zadanie zostało ukończone.

W następnym wierszu znajduje się następujący kod:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Zastąp cały wiersz poniższym kodem:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Poprzedzający kod dodaje przycisk przesyłania, który odwołuje się do określonej wcześniej trasy.

### <a name="test-the-changes-locally"></a>Lokalne testowanie zmian

W oknie terminala lokalnego uruchom serwer projektowy z poziomu katalogu głównego repozytorium Git.

```bash
php artisan serve
```

Aby zobaczyć zmianę stanu zadania, przejdź do adresu `http://localhost:8000` i zaznacz pole wyboru.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="Dodanie pola wyboru do zadania":::

Aby zatrzymać środowisko PHP, naciśnij w terminalu klawisze `Ctrl + C`.

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

W oknie terminala lokalnego uruchom migracje baz danych platformy Laravel przy użyciu parametrów połączenia, aby wprowadzić zmianę w bazie danych platformy Azure.

```bash
php artisan migrate --env=production --force
```

Zatwierdź wszystkie zmiany w narzędziu Git, a następnie wypchnij zmiany kodu na platformę Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure main
```

Po zakończeniu wykonywania polecenia `git push` przejdź do aplikacji platformy Azure i przetestuj nowe funkcje.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Zmiany w modelu i bazie danych opublikowane na platformie Azure":::

Jeśli dodano jakiekolwiek zadania, zostaną one zachowane w bazie danych. Aktualizacje schematu danych pozostawiają dane bez zmian.

## <a name="clean-up-resources"></a>Czyszczenie zasobów
W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli te zasoby nie będą raczej potrzebne w przyszłości, usuń grupę zasobów, uruchamiając następujące polecenie w usłudze Cloud Shell:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Jak zarządzać zasobami w programie Azure Portal](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [Jak zarządzać serwerem](how-to-manage-server-cli.md)
