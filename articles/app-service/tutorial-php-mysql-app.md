---
title: 'Samouczek: aplikacja PHP z bazą danych MySQL'
description: Dowiedz się, jak uruchomić aplikację języka PHP na platformie Azure z użyciem połączenia z bazą danych MySQL na platformie Azure. Platformy laravel jest używany w samouczku.
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/15/2020
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: aaef3f28ea9b7e41bce35661e9515f6efcc9ade5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184444"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure-app-service"></a>Samouczek: Tworzenie aplikacji PHP i MySQL w Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w Internecie przy użyciu systemu operacyjnego Windows. W tym samouczku pokazano, jak utworzyć aplikację języka PHP na platformie Azure i połączyć ją z bazą danych MySQL. Po zakończeniu będziesz mieć aplikację [platformy laravel](https://laravel.com/) działającą na Azure App Service w systemie Windows.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym samouczku pokazano, jak utworzyć aplikację języka PHP na platformie Azure i połączyć ją z bazą danych MySQL. Po zakończeniu będziesz mieć aplikację [platformy laravel](https://laravel.com/) działającą na Azure App Service w systemie Linux.

::: zone-end

:::image type="content" source="./media/tutorial-php-mysql-app/complete-checkbox-published.png" alt-text="Zrzut ekranu przykładu aplikacji PHP zatytułowany Lista zadań.":::

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych MySQL na platformie Azure
> * Łączenie aplikacji PHP z bazą danych MySQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- [Zainstaluj oprogramowanie Git](https://git-scm.com/)
- [Zainstaluj środowisko PHP w wersji 5.6.4 lub nowszej](https://php.net/downloads.php)
- [Zainstaluj oprogramowanie Composer](https://getcomposer.org/doc/00-intro.md)
- Włącz następujące rozszerzenia PHP wymagane przez platformę Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer i XML
- [Instalowanie i uruchamianie MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)] 

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

W oknie terminalu dodaj element `cd` do katalogu roboczego.

Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium.

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

W katalogu głównym repozytorium utwórz plik o nazwie *env*. Skopiuj poniższe zmienne do pliku *env*. Zastąp symbol zastępczy _&lt;>root_password_ przy użyciu hasła użytkownika root MySQL.

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

![Pomyślne połączenie środowiska PHP z serwerem MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Aby zatrzymać środowisko PHP, naciśnij w terminalu klawisze `Ctrl + C`.

## <a name="create-mysql-in-azure"></a>Tworzenie bazy danych MySQL na platformie Azure

W tym kroku utworzysz bazę danych MySQL w usłudze [Azure Database for MySQL](../mysql/index.yml). Następnie skonfigurujesz aplikację PHP i połączysz ją z tą bazą danych.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Tworzenie serwera MySQL

W Cloud Shell Utwórz serwer w Azure Database for MySQL za pomocą [`az mysql server create`](/cli/azure/mysql/server#az-mysql-server-create) polecenia.

W poniższym poleceniu Zastąp unikatową nazwę serwera dla *\<mysql-server-name>* symbolu zastępczego, nazwę użytkownika *\<admin-user>* i hasło dla *\<admin-password>*  symbolu zastępczego. Ta nazwa serwera jest używana jako część punktu końcowego bazy danych MySQL (`https://<mysql-server-name>.mysql.database.azure.com`), więc nazwa musi być unikatowa na wszystkich serwerach platformy Azure. Aby uzyskać szczegółowe informacje na temat wybierania jednostki SKU bazy danych MySQL, zobacz [Tworzenie serwera Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md#create-an-azure-database-for-mysql-server).

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

Po utworzeniu serwera MySQL w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

<pre>
{
  "administratorLogin": "&lt;admin-user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
  -  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>Konfigurowanie zapory serwera

W Cloud Shell Utwórz regułę zapory dla serwera MySQL, aby zezwolić na połączenia klientów przy użyciu [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) polecenia. Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](overview-inbound-outbound-ips.md#find-outbound-ips).
>

W Cloud Shell ponownie uruchom polecenie, aby zezwolić na dostęp z komputera lokalnego przez zastąpienie *\<your-ip-address>* [lokalnego adresu IP IPv4](https://www.whatsmyip.org/).

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Nawiązywanie połączenia z serwerem produkcyjnym MySQL lokalnie

W oknie terminala lokalnego nawiąż połączenie z serwerem MySQL na platformie Azure. Użyj podanej wcześniej wartości dla _&lt; administratora-użytkownika>_ i _&lt; mysql-Server-Name>_. Gdy zostanie wyświetlone pytanie o hasło, podaj hasło określone podczas tworzenia bazy danych na platformie Azure.

```bash
mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Tworzenie produkcyjnej bazy danych

Utwórz bazę danych w wierszu polecenia `mysql`.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Tworzenie użytkownika z uprawnieniami

Utwórz użytkownika bazy danych o nazwie _phpappuser_ i nadaj mu wszystkie uprawnienia w bazie danych `sampledb`. Aby prostota samouczka, użyj _MySQLAzure2017_ jako hasła.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Zakończ połączenie z serwerem, wpisując polecenie `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Łączenie aplikacji z bazą danych Azure MySQL

W tym kroku połączysz aplikację PHP z bazą danych MySQL utworzoną w usłudze Azure Database for MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Konfigurowanie połączenia z bazą danych

W katalogu głównym repozytorium utwórz plik _env.production_ i skopiuj do niego poniższe zmienne. Zastąp &lt; ciąg placeholder_ MySQL-Server-name>_ zarówno w *db_host* , jak i *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Zapisz zmiany.

> [!TIP]
> Aby zabezpieczyć informacje o połączeniu MySQL, ten plik jest już wykluczony z repozytorium Git (zobacz plik _gitignore_ w katalogu głównym repozytorium). W dalszej części dowiesz się, jak skonfigurować zmienne środowiskowe w usłudze App Service, aby nawiązać połączenie z bazą danych w usłudze Azure Database for MySQL. Zmienne środowiskowe nie wymagają pliku *env* w usłudze App Service.
>

### <a name="configure-tlsssl-certificate"></a>Konfigurowanie certyfikatu TLS/SSL

Domyślnie Azure Database for MySQL wymusza połączenia TLS z klientów. Aby nawiązać połączenie z bazą danych MySQL na platformie Azure, należy użyć certyfikatu [_pem_ dostarczonego przez usługę Azure Database for MySQL](../mysql/howto-configure-ssl.md).

Otwórz plik _config/database.php_, a następnie dodaj parametry `sslmode` i `options` do elementu `connections.mysql`, jak pokazano w poniższym kodzie.

::: zone pivot="platform-windows"  

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

::: zone-end

::: zone pivot="platform-linux"

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

::: zone-end

Dla wygody w tym samouczku certyfikat `BaltimoreCyberTrustRoot.crt.pem` znajduje się w repozytorium. 

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

![Pomyślne połączenie środowiska PHP z usługą Azure Database for MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

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

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

::: zone-end

### <a name="configure-database-settings"></a>Konfigurowanie ustawień bazy danych

W usłudze App Service zmienne środowiskowe są ustawiane jako _ustawienia aplikacji_ za pomocą polecenia [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Następujące polecenie konfiguruje ustawienia aplikacji `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` i `DB_PASSWORD`. Zastąp symbole zastępcze _&lt; App-Name>_ i _&lt; mysql-Server-Name>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

W Cloud Shell Ustaw klucz aplikacji w aplikacji App Service za pomocą [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) polecenia. Zastąp symbole zastępcze _&lt; App-Name>_ i _&lt; outputofphpartisankey: Generate>_.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

Właściwość `APP_DEBUG="true"` informuje platformę Laravel, aby zwróciła informacje debugowania, gdy wdrożona aplikacja napotka błędy. Podczas uruchamiania aplikacji produkcyjnej ustaw ją na wartość `false`, co zapewnia większe bezpieczeństwo.

### <a name="set-the-virtual-application-path"></a>Ustawianie wirtualnej ścieżki aplikacji

::: zone pivot="platform-windows"  

Ustaw wirtualną ścieżkę aplikacji dla aplikacji. Ten krok jest wymagany, ponieważ [cykl życia aplikacji Laravel](https://laravel.com/docs/5.4/lifecycle) rozpoczyna się w katalogu _publicznym_, a nie w katalogu głównym aplikacji. Inne platformy PHP, których cykl życia rozpoczyna się w katalogu głównym, mogą działać bez ręcznej konfiguracji wirtualnej ścieżki aplikacji.

W Cloud Shell Ustaw ścieżkę aplikacji wirtualnej przy użyciu [`az resource update`](/cli/azure/resource#az-resource-update) polecenia. Zastąp symbol zastępczy _&lt;>nazwa aplikacji_ .

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Domyślnie Azure App Service punkty głównej ścieżki aplikacji wirtualnej ( _/_ ) do katalogu głównego wdrożonych plików aplikacji (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

[Cykl życia aplikacji platformy laravel](https://laravel.com/docs/5.4/lifecycle) rozpoczyna się w katalogu _publicznym_ , a nie w katalogu głównym aplikacji. Domyślny obraz platformy Docker w środowisku PHP dla usługi App Service używa platformy Apache i nie pozwala na dostosowywanie elementu `DocumentRoot` dla platformy Laravel. Za pomocą pliku `.htaccess` możesz jednak ponownie zapisać wszystkie żądania tak, aby wskazywały katalog _/public_ zamiast katalogu głównego. W tym celu do katalogu głównego repozytorium dodano plik `.htaccess`. Dzięki niemu aplikacja platformy Laravel jest gotowa do wdrożenia.

Aby uzyskać więcej informacji, zobacz [Zmienianie katalogu głównego witryny](configure-language-php.md#change-site-root).

::: zone-end

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

::: zone pivot="platform-windows"  

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

> [!NOTE]
> Możesz zauważyć, że w procesie wdrażania pakiety [Composer](https://getcomposer.org/) są instalowane na końcu. Usługa App Service nie uruchamia tych automatyzacji podczas wdrażania domyślnego, dlatego w katalogu głównym tego przykładowego repozytorium znajdują się 3 dodatkowe pliki włączające tę funkcję:
>
> - `.deployment` — ten plik informuje usługę App Service, aby uruchomiła skrypt `bash deploy.sh` jako skrypt wdrożenia niestandardowego.
> - `deploy.sh` — skrypt wdrożenia niestandardowego. Jeśli przejrzysz plik, zauważysz, że uruchamia on polecenie `php composer.phar install` po poleceniu `npm install`.
> - `composer.phar` — menedżer pakietów Composer.
>
> Korzystając z tego podejścia, możesz dodać dowolny krok do opartego na usłudze Git wdrożenia do usługi App Service. Aby uzyskać więcej informacji, zobacz [Skrypt wdrożenia niestandardowego](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

::: zone-end

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do adresu `http://<app-name>.azurewebsites.net` i dodaj kilka zadań do listy.

:::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="Zrzut ekranu przedstawiający przykład aplikacji platformy Azure zatytułowany Lista zadań, w którym dodano nowe zadania.":::

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

![Dodanie pola wyboru do zadania](./media/tutorial-php-mysql-app/complete-checkbox.png)

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

![Zmiany w modelu i bazie danych opublikowane na platformie Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Jeśli dodano jakiekolwiek zadania, zostaną one zachowane w bazie danych. Aktualizacje schematu danych pozostawiają dane bez zmian.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

::: zone pivot="platform-windows"  

Gdy aplikacja PHP działa w usłudze Azure App Service, dzienniki konsoli można przesłać potokiem do terminala. W ten sposób można użyć komunikatów diagnostycznych w celu ułatwienia debugowania błędów aplikacji.

Aby rozpocząć przesyłanie strumieniowe dzienników, użyj [`az webapp log tail`](/cli/azure/webapp/log#az-webapp-log-tail) polecenia znajdującego się w Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Po rozpoczęciu przesyłania strumieniowego dzienników odśwież aplikację platformy Azure w przeglądarce, aby wywołać pewien ruch w Internecie. Teraz można zobaczyć dzienniki konsoli przesłane potokiem do terminala. Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

Aby w dowolnym momencie zatrzymać przesyłanie strumieniowe dzienników, naciśnij kombinację klawiszy `Ctrl`+`C`.

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

> [!TIP]
> Aplikacja PHP może użyć standardowej instrukcji [error_log()](https://php.net/manual/function.error-log.php) w celu przesłania danych wyjściowych do konsoli. Przykładowa aplikacja używa tego podejścia w pliku _app/Http/routes.php_.
>
> Będące platformą internetową rozwiązanie [Laravel używa produktu Monolog](https://laravel.com/docs/5.4/errors) jako dostawcy logowania. Aby zobaczyć, jak ustawić rozwiązanie Monolog tak, aby wysyłało komunikaty wyjściowe do konsoli, zobacz [PHP: How to use monolog to log to console (php://out) (PHP: Jak używać rozwiązania Monolog w przypadku logowania do konsoli (php://out))](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby zarządzać utworzoną aplikacją.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/tutorial-php-mysql-app/access-portal.png)

Zostanie wyświetlona strona omówienia aplikacji. W tym miejscu możesz wykonywać podstawowe zadania zarządzania, takie jak zatrzymywanie, uruchamianie, ponowne uruchamianie, przeglądanie i usuwanie.

Menu po lewej stronie zawiera strony służące do konfigurowania aplikacji.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych MySQL na platformie Azure
> * Łączenie aplikacji PHP z bazą danych MySQL
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji PHP](configure-language-php.md)
