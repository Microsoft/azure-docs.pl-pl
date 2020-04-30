---
title: 'Samouczek: wdrażanie języka Python (Django) z Postgres'
description: Dowiedz się, jak utworzyć aplikację w języku Python przy użyciu bazy danych PostgreSQL i wdrożyć ją w systemie Linux w Azure App Service. Samouczek używa przykładowej aplikacji Django do pokazania.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085760"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Samouczek: wdrażanie aplikacji sieci Web w języku Python (Django) za pomocą PostgreSQL w Azure App Service

W tym samouczku przedstawiono sposób wdrażania aplikacji sieci Web opartej na danych w języku Python (Django) w celu [Azure App Service](app-service-linux-intro.md) i połączenia jej z bazą danych Azure Database for PostgreSQL. App Service zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web.

![Wdróż aplikację sieci Web w języku Python Django do Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych Azure Database for PostgreSQL
> * Wdróż kod do Azure App Service i Połącz się z usługą Postgres
> * Zaktualizuj kod i Wdróż ponownie
> * Wyświetlanie dzienników diagnostycznych
> * Zarządzanie aplikacją internetową w Azure Portal

Kroki opisane w tym artykule można wykonać w systemie macOS, Linux lub Windows.

## <a name="install-dependencies"></a>Instalowanie zależności

Przed rozpoczęciem pracy z tym samouczkiem:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
- Zainstaluj narzędzie [git](https://git-scm.com/).
- Zainstaluj język [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

W oknie terminalu uruchom następujące polecenia, aby sklonować repozytorium przykładowej aplikacji, a następnie przejdź do katalogu głównego repozytorium:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

Przykładowe repozytorium djangoapp zawiera aplikację do sondowania [Django](https://www.djangoproject.com/) opartego na danych, którą uzyskasz, wykonując od [zapisania pierwszej aplikacji Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) w dokumentacji Django. Jest on dostępny w tym miejscu dla wygody użytkownika.

## <a name="prepare-app-for-app-service"></a>Przygotowywanie aplikacji do App Service

Podobnie jak w przypadku wielu platform sieci Web w języku Python, Django [wymaga pewnych zmian, zanim będą mogły zostać uruchomione na serwerze produkcyjnym](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)i nie różnią się App Service. Należy zmienić i dodać niektóre ustawienia w domyślnym pliku *azuresite/Settings. PR* , aby aplikacja działała po wdrożeniu do App Service. 

Zapoznaj się z tematem *azuresite/Product. PR*, który wprowadza konfigurację niezbędną dla App Service. Na krótko wykonujemy następujące czynności:

- Dziedzicz wszystkie ustawienia z *azuresite/Settings. PR*.
- Dodaj w pełni kwalifikowaną nazwę domeny aplikacji App Service na dozwolonych hostach. 
- Użyj [WhiteNoise](https://whitenoise.evans.io/en/stable/) , aby włączyć obsługę plików statycznych w środowisku produkcyjnym, ponieważ domyślnie Django nie obsługuje plików statycznych w środowisku produkcyjnym. Pakiet WhiteNoise jest już uwzględniony w pliku *requirements.txt*.
- Dodaj konfigurację dla bazy danych PostgreSQL. Domyślnie Django używa Sqlite3 jako bazy danych, ale nie jest to odpowiednie dla aplikacji produkcyjnych. Pakiet [psycopg2-Binary](https://pypi.org/project/psycopg2-binary/) został już uwzględniony w pliku *Requirements. txt*.
- Konfiguracja Postgres używa zmiennych środowiskowych. Później dowiesz się, jak ustawić zmienne środowiskowe w App Service.

*azuresite/Product. PR* jest dołączona do repozytorium dla wygody, ale nie jest jeszcze używana przez aplikację. Aby upewnić się, że jego ustawienia są używane w App Service, należy skonfigurować dwa pliki, *manage.py* i *azuresite/WSGI. PR*, aby uzyskać do nich dostęp.

- W *manage.py*Zmień następujący wiersz:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Do następującego kodu:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Zmienna `DJANGO_ENV` środowiskowa zostanie ustawiona później podczas konfigurowania aplikacji App Service.

- W *azuresite/WSGI. PR*wprowadź tę samą zmianę jak powyżej.

    W App Service można używać *manage.py* do uruchamiania migracji bazy danych, a App Service do uruchamiania aplikacji Django w środowisku produkcyjnym używa *azuresite/WSGI. PR* . Ta zmiana w obu plikach gwarantuje, że ustawienia produkcyjne są używane w obu przypadkach.

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Powinien już być zainstalowany interfejs wiersza polecenia platformy Azure. [Interfejs wiersza polecenia platformy Azure](/cli/azure/what-is-azure-cli) umożliwia korzystanie z zasobów platformy Azure z poziomu terminalu z wierszem poleceń. 

Aby zalogować się do platformy Azure, [`az login`](/cli/azure/reference-index#az-login) Uruchom polecenie:

```azurecli
az login
```

Postępuj zgodnie z instrukcjami w terminalu, aby zalogować się na koncie platformy Azure. Po zakończeniu subskrypcje są wyświetlane w formacie JSON w danych wyjściowych terminalu.

## <a name="create-postgres-database-in-azure"></a>Tworzenie bazy danych Postgres na platformie Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

W tej sekcji utworzysz serwer Azure Database for PostgreSQL i bazę danych. Aby rozpocząć, zainstaluj `db-up` rozszerzenie za pomocą następującego polecenia:

```azurecli
az extension add --name db-up
```

Utwórz bazę danych Postgres na platformie Azure przy [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) użyciu polecenia, jak pokazano w poniższym przykładzie. Zastąp * \<PostgreSQL-Name>* *unikatową* nazwą (punkt końcowy serwera to *https://\<PostgreSQL-Name>. Postgres.Database.Azure.com*). W * \<obszarze Nazwa użytkownika admin>* i * \<administrator-hasło>* określ poświadczenia, aby utworzyć użytkownika administratora dla tego serwera Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

To polecenie może chwilę potrwać, ponieważ wykonuje następujące czynności:

- Tworzy [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) o nazwie `myResourceGroup`, jeśli nie istnieje. Każdy zasób platformy Azure musi znajdować się w jednym z nich. Parametr `--resource-group` jest opcjonalny.
- Tworzy serwer Postgres z użytkownikiem administracyjnym.
- Tworzy `pollsdb` bazę danych.
- Zezwala na dostęp z lokalnego adresu IP.
- Zezwala na dostęp z usług platformy Azure.
- Utwórz użytkownika bazy danych z dostępem do `pollsdb` bazy danych.

Wszystkie kroki można wykonać oddzielnie z innymi `az postgres` poleceniami `psql`, ale `az postgres up` wszystkie są wykonywane w jednym kroku.

Po zakończeniu wykonywania polecenia Znajdź linie wyjściowe, z `Ran Database Query:`którymi się znajdują. Pokażą użytkownikowi bazy danych, który został utworzony dla Ciebie, przy użyciu `root` nazwy użytkownika `Pollsdb1`i hasła. Będziesz ich używać później, aby połączyć aplikację z bazą danych.

<!-- not all locations support az postgres up -->
> [!TIP]
> Aby określić lokalizację serwera Postgres, należy uwzględnić argument `--location <location-name>`, gdzie `<location_name>` jest jednym z regionów świadczenia [usługi Azure](https://azure.microsoft.com/global-infrastructure/regions/). Dostępne regiony można pobrać za pomocą [`az account list-locations`](/cli/azure/account#az-account-list-locations) polecenia.

## <a name="deploy-the-app-service-app"></a>Wdrażanie aplikacji App Service

W tej sekcji utworzysz aplikację App Service. Ta aplikacja zostanie podłączona do utworzonej i wdrożonej bazy danych Postgres.

### <a name="create-the-app-service-app"></a>Tworzenie aplikacji App Service

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Upewnij się, że jesteś z powrotem w katalogu głównym`djangoapp`repozytorium (), ponieważ aplikacja zostanie wdrożona z tego katalogu.

Utwórz aplikację App Service za pomocą [`az webapp up`](/cli/azure/webapp#az-webapp-up) polecenia, jak pokazano w poniższym przykładzie. Zastąp * \<ciąg App-Name>* *unikatową* nazwą (punkt końcowy serwera *to\<https://App-Name>. azurewebsites.NET*). Dozwolone znaki dla * \<nazwy aplikacji>* to `A` - `Z`, `0` - `9`, i `-`.

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

To polecenie może chwilę potrwać, ponieważ wykonuje następujące czynności:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Automatycznie generuje [grupę zasobów](../../azure-resource-manager/management/overview.md#terminology) .
- Tworzy [App Service planu](../overview-hosting-plans.md) *myAppServicePlan* w warstwie cenowej Basic (B1), jeśli nie istnieje. `--plan`i `--sku` są opcjonalne.
- Tworzy aplikację App Service, jeśli nie istnieje.
- Włącza domyślne rejestrowanie dla aplikacji, jeśli nie została jeszcze włączona.
- Przekazuje repozytorium przy użyciu wdrożenia ZIP z włączoną automatyzacją kompilacji.

Po zakończeniu wdrażania zobaczysz dane wyjściowe JSON podobne do następujących:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Skopiuj wartość * \<>App-Resource-Group *. Aplikacja będzie potrzebna później. 

> [!TIP]
> Możesz użyć tego samego polecenia później do wdrożenia wszelkich zmian i natychmiast włączyć dzienniki diagnostyczne przy użyciu:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

Przykładowy kod jest teraz wdrożony, ale aplikacja nie łączy się jeszcze z bazą danych Postgres na platformie Azure. W tym celu należy wykonać poniższe czynności.

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Po uruchomieniu aplikacji lokalnie można ustawić zmienne środowiskowe w sesji terminala. W App Service można to zrobić za pomocą *ustawień aplikacji*, korzystając z polecenia [AZ webapp config AppSettings Set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

Uruchom następujące polecenie, aby określić szczegóły połączenia z bazą danych jako ustawienia aplikacji. Zastąp * \<nazwę App-Name>*, * \<App-Resource-Group>* i * \<PostgreSQL-Name>* własnymi wartościami. Należy pamiętać, że poświadczenia `root` użytkownika `Pollsdb1` i zostały utworzone przez `az postgres up`program.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Aby uzyskać informacje o tym, jak kod uzyskuje dostęp do tych ustawień aplikacji, zobacz [dostęp do zmiennych środowiskowych](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Uruchom migracje bazy danych

Aby uruchomić migracje bazy danych w App Service, Otwórz sesję SSH w przeglądarce, przechodząc do *https://\<App-name>. SCM.azurewebsites.NET/webssh/Host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

W sesji SSH Uruchom następujące polecenia:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do wdrożonej aplikacji z adresem URL *http\//\<: App-Name>. azurewebsites.NET* w przeglądarce. Powinien zostać wyświetlony komunikat **Brak dostępnych sondowań**. 

Przejdź do *protokołu HTTP\//\<: App-Name>. azurewebsites.NET/admin* i zaloguj się przy użyciu administratora utworzonego w ostatnim kroku. Wybierz pozycję **Dodaj** obok pozycji **pytania**i Utwórz pytanie dotyczące ankiety z wybranymi opcjami.

Przejdź do wdrożonej aplikacji z adresem URL *http\//\<: App-Name>. azurewebsites.NET/admin*i Utwórz kilka pytań dotyczących sondowania. Pytania można zobaczyć na stronie *http:\//\<App-Name>. azurewebsites.NET/*. 

![Uruchamianie aplikacji Django języka Python w App Services na platformie Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Przejdź do wdrożonej aplikacji przy użyciu adresu URL *http:\//\<App-Name>. azurewebsites.NET* ponownie, aby zobaczyć pytanie dotyczące sondy i odpowiedzieć na pytanie.

App Service wykrywa projekt Django w repozytorium, szukając pliku *WSGI.py* w każdym podkatalogu, który `manage.py startproject` tworzy domyślnie. Gdy App Service odnajdzie plik, ładuje aplikację sieci Web Django. Aby uzyskać więcej informacji na temat sposobu ładowania aplikacji języka Python przez usługę App Service, zobacz [Konfigurowanie wbudowanego obrazu języka Python](how-to-configure-python.md).

**Gratulacje!** Korzystasz z aplikacji sieci Web języka Python (Django) w Azure App Service dla systemu Linux.

## <a name="develop-app-locally-and-redeploy"></a>Opracowywanie aplikacji lokalnie i ponowne wdrażanie

W tej sekcji utworzysz swoją aplikację w środowisku lokalnym i ponownie Wdróż swój kod w App Service.

### <a name="set-up-locally-and-run"></a>Skonfiguruj lokalnie i uruchom

Aby skonfigurować lokalne środowisko programistyczne i uruchomić przykładową aplikację po raz pierwszy, uruchom następujące polecenia:

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Gdy aplikacja sieci Web Django jest w pełni załadowana, zwraca komunikat podobny do następującego:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Przejdź do *protokołu HTTP\/:/localhost: 8000* w przeglądarce. Powinien zostać wyświetlony komunikat **Brak dostępnych sondowań**. 

Przejdź do *protokołu HTTP\/:/localhost: 8000/admin* i zaloguj się przy użyciu administratora utworzonego w ostatnim kroku. Wybierz pozycję **Dodaj** obok pozycji **pytania**i Utwórz pytanie dotyczące ankiety z wybranymi opcjami.

![Uruchamianie aplikacji Django języka Python w App Services lokalnie](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Przejdź ponownie do *protokołu\/http:/localhost: 8000* , aby zobaczyć pytanie dotyczące sondy i odpowiedzieć na pytanie. Lokalna aplikacja Przykładowa Django zapisuje i zapisuje dane użytkownika w lokalnej bazie danych Sqlite3, dzięki czemu nie trzeba martwić się o wygenerowanie produkcyjnej bazy danych. Aby środowisko programistyczne było zgodne ze środowiskiem platformy Azure, zamiast tego Rozważ użycie bazy danych Postgres lokalnie.

Aby zatrzymać serwer Django, wpisz Ctrl + C.

### <a name="update-the-app"></a>Aktualizowanie aplikacji

Aby zobaczyć, jak działają aktualizacje aplikacji, wprowadź niewielką zmianę w `polls/models.py`artykule. Znajdź wiersz:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

I zmień go na:

```python
choice_text = models.CharField(max_length=100)
```

Zmieniając model danych, należy utworzyć nową migrację Django. Zrób to za pomocą następującego polecenia:

```
python manage.py makemigrations
```

Zmiany można testować lokalnie, uruchamiając migracje, uruchamiając serwer programistyczny i przechodząc do *protokołu HTTP\/:/localhost: 8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Ponowne wdrażanie kodu na platformie Azure

Aby ponownie wdrożyć zmiany, uruchom następujące polecenie w katalogu głównym repozytorium:

```azurecli
az webapp up --name <app-name>
```

App Service wykryje, że aplikacja istnieje i po prostu wdraża kod.

### <a name="rerun-migrations-in-azure"></a>Ponowne uruchamianie migracji na platformie Azure

Ze względu na to, że wprowadzono zmiany w modelu danych, należy ponownie uruchomić migracje bazy danych w App Service. Otwórz sesję SSH w przeglądarce, przechodząc do *https://\<app-name>. SCM.azurewebsites.NET/webssh/Host*. Uruchom następujące polecenia:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Przeglądanie aplikacji w środowisku produkcyjnym

Przejdź do *protokołu HTTP\//\<: App-Name>. azurewebsites.NET* i zobacz zmiany działające na żywo w środowisku produkcyjnym. 

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

Możesz uzyskać dostęp do dzienników konsoli wygenerowanych z wewnątrz kontenera.

> [!TIP]
> `az webapp up`włącza opcję rejestrowania domyślnego. Ze względu na wydajność to rejestrowanie wyłącza się po pewnym czasie, ale włącza się ponownie przy każdym uruchomieniu `az webapp up` . Aby włączyć tę funkcję ręcznie, uruchom następujące polecenie:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby wyświetlić strumień dziennika:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

> [!NOTE]
> Możesz również sprawdzić pliki dziennika z przeglądarki pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie `Ctrl` + `C`, wpisz polecenie.

## <a name="manage-your-app-in-the-azure-portal"></a>Zarządzanie aplikacją w Azure Portal

W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz utworzoną aplikację.

![Przejdź do aplikacji w języku Python Django w Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. W tym miejscu można również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Zarządzanie aplikacją Django języka Python na stronie Przegląd w Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupy zasobów, uruchamiając następujące polecenia:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Tworzenie bazy danych Azure Database for PostgreSQL
> * Wdróż kod do Azure App Service i Połącz się z usługą Postgres
> * Zaktualizuj kod i Wdróż ponownie
> * Wyświetlanie dzienników diagnostycznych
> * Zarządzanie aplikacją internetową w Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację:

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](../app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](how-to-configure-python.md)
