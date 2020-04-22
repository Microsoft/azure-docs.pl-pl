---
title: 'Samouczek: Wdrażanie Pythona (Django) z postgresem'
description: Dowiedz się, jak utworzyć aplikację języka Python z bazą danych PostgreSQL i wdrożyć ją w usłudze Azure App Service w systemie Linux. Samouczek używa przykładowej aplikacji Django do demonstracji.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 2f6e21f47a021a0aab1d059da2aea2c497acb785
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770711"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Samouczek: Wdrażanie aplikacji sieci Web Języka Python (Django) z postgreSQL w usłudze Azure App Service

W tym samouczku pokazano, jak wdrożyć aplikację internetową Python (Django) opartą na danych w [usłudze Azure App Service](app-service-linux-intro.md) i połączyć ją z bazą danych usługi Azure Database for PostgreSQL. Usługa App Service zapewnia wysoce skalowalną, samoładującą się usługę hostingu.

![Wdrażanie aplikacji internetowej Python Django w usłudze Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych usługi Azure Database dla postgreSQL
> * Wdrażanie kodu w usłudze Azure App Service i łączenie się z usługą Postgres
> * Aktualizowanie kodu i ponowne wdrożenie
> * Wyświetlanie dzienników diagnostycznych
> * Zarządzanie aplikacją sieci Web w witrynie Azure portal

Możesz wykonać kroki opisane w tym artykule w systemach macOS, Linux lub Windows.

## <a name="install-dependencies"></a>Instalowanie zależności

Przed rozpoczęciem tego samouczka:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Zainstaluj [platformę Azure CLI](/cli/azure/install-azure-cli).
- Zainstaluj [Git](https://git-scm.com/).
- Zainstaluj [Pythona 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

W oknie terminala uruchom następujące polecenia, aby sklonować przykładowe repozytorium aplikacji i zmienić katalog główny repozytorium:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

Repozytorium próbek djangoapp zawiera opartą na danych aplikację [ankiety Django,](https://www.djangoproject.com/) którą otrzymujesz, wykonując [pisanie pierwszej aplikacji Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) w dokumentacji Django. Jest tu dla Twojej wygody.

## <a name="prepare-app-for-app-service"></a>Przygotowanie aplikacji do usługi App Service

Podobnie jak wiele struktur internetowych Pythona, Django [wymaga pewnych zmian, zanim będą mogły być uruchamiane na serwerze produkcyjnym](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)i nie inaczej jest w przypadku usługi App Service. Należy zmienić i dodać niektóre ustawienia w domyślnym pliku *azuresite/settings.py,* tak aby aplikacja działała po jej wdrożeniu w usłudze App Service. 

Spójrz na *azuresite/production.py*, co sprawia, że niezbędna konfiguracja dla usługi App Service. Krótko mówiąc, wykonuje następujące czynności:

- Dziedzicz wszystkie ustawienia z *witryny azuresite/settings.py*.
- Dodaj w pełni kwalifikowaną nazwę domeny aplikacji Usługi app service do dozwolonych hostów. 
- Użyj [WhiteNoise,](https://whitenoise.evans.io/en/stable/) aby włączyć wyświetlanie plików statycznych w produkcji, ponieważ Django domyślnie nie obsługuje plików statycznych w produkcji. Pakiet WhiteNoise jest już uwzględniony w pliku *requirements.txt*.
- Dodaj konfigurację bazy danych PostgreSQL. Domyślnie Django używa Sqlite3 jako bazy danych, ale nie nadaje się do aplikacji produkcyjnych. [Pakiet psycopg2-binary](https://pypi.org/project/psycopg2-binary/) jest już zawarty w *requirements.txt*.
- Konfiguracja Postgres używa zmiennych środowiskowych. Później dowiesz się, jak ustawić zmienne środowiskowe w usłudze App Service.

*azuresite/production.py* jest zawarte w repozytorium dla wygody, ale nie jest jeszcze używany przez aplikację. Aby upewnić się, że jego ustawienia są używane w usłudze App Service, należy skonfigurować dwa pliki, *manage.py* i *azuresite/wsgi.py*, aby uzyskać do niego dostęp.

- W *manage.py*zmień następujący wiersz:

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

    Zmienną `DJANGO_ENV` środowiskową ustawisz później podczas konfigurowania aplikacji usługi App Service.

- W *azuresite/wsgi.py*dokonaj tej samej zmiany, co powyżej.

    W usłudze App Service używasz *manage.py* do uruchamiania migracji baz danych, a usługa App Service używa *usługi azuresite/wsgi.py* do uruchamiania aplikacji Django w produkcji. Ta zmiana w obu plikach zapewnia, że ustawienia produkcji są używane w obu przypadkach.

## <a name="sign-in-to-azure-cli"></a>Logowanie do interfejsu wiersza polecenia platformy Azure

Powinieneś mieć już zainstalowany plik CLI platformy Azure. [Narzędzie CLI platformy Azure](/cli/azure/what-is-azure-cli) umożliwia pracę z zasobami platformy Azure z terminala wiersza polecenia. 

Aby zalogować się na [`az login`](/cli/azure/reference-index#az-login) platformie Azure, uruchom polecenie:

```azurecli
az login
```

Postępuj zgodnie z instrukcjami w terminalu, aby zalogować się do konta platformy Azure. Po zakończeniu subskrypcje są wyświetlane w formacie JSON w danych wyjściowych terminala.

## <a name="create-postgres-database-in-azure"></a>Tworzenie bazy danych postgres na platformie Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

W tej sekcji utworzysz usługę Azure Database dla serwera postgreSql i bazy danych. Aby rozpocząć, `db-up` zainstaluj rozszerzenie za pomocą następującego polecenia:

```azurecli
az extension add --name db-up
```

Utwórz bazę danych Postgres na platformie Azure za [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) pomocą polecenia, jak pokazano w poniższym przykładzie. Zastąp * \<postgresql-name>* *unikatową* nazwą (punkt końcowy serwera jest *https://\<postgresql-name>.postgres.database.azure.com*). W * \<* przypadku>>i * \<>hasła administratora *należy określić poświadczenia, aby utworzyć użytkownika administratora dla tego serwera postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

To polecenie może chwilę potrwać, ponieważ wykonuje następujące czynności:

- Tworzy [grupę](../../azure-resource-manager/management/overview.md#terminology) `myResourceGroup`zasobów o nazwie , jeśli nie istnieje. Każdy zasób platformy Azure musi znajdować się w jednym z nich. Parametr `--resource-group` jest opcjonalny.
- Tworzy serwer Postgres z użytkownikiem administracyjnym.
- Tworzy `pollsdb` bazę danych.
- Umożliwia dostęp z lokalnego adresu IP.
- Umożliwia dostęp z usług platformy Azure.
- Utwórz użytkownika bazy danych `pollsdb` z dostępem do bazy danych.

Możesz wykonać wszystkie kroki oddzielnie `az postgres` za `psql`pomocą `az postgres up` innych poleceń i , ale robi je wszystkie w jednym kroku dla Ciebie.

Po zakończeniu polecenia znajdź linie wyjściowe, `Ran Database Query:`które są z . Pokazują one użytkownika bazy danych, który jest `root` tworzony `Pollsdb1`dla Ciebie, z nazwą użytkownika i hasłem . Użyjesz ich później, aby połączyć aplikację z bazą danych.

<!-- not all locations support az postgres up -->
> [!TIP]
> Aby określić lokalizację serwera Postgres, `--location <location-name>`należy `<location_name>` dołączyć argument , gdzie jest jednym z [regionów platformy Azure](https://azure.microsoft.com/global-infrastructure/regions/). Za pomocą [`az account list-locations`](/cli/azure/account#az-account-list-locations) polecenia można uzyskać regiony dostępne dla subskrypcji.

## <a name="deploy-the-app-service-app"></a>Wdrażanie aplikacji usługi app service

W tej sekcji należy utworzyć aplikację usługi App Service. Zostanie to połączenie tej aplikacji z bazą danych Postgres, która została utworzona i wdrożyć kod.

### <a name="create-the-app-service-app"></a>Tworzenie aplikacji usługi App Service

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Upewnij się, że jesteś z powrotem`djangoapp`w katalogu głównym repozytorium ( ), ponieważ aplikacja zostanie wdrożona z tego katalogu.

Utwórz aplikację usługi [`az webapp up`](/cli/azure/webapp#az-webapp-up) App Service za pomocą polecenia, jak pokazano w poniższym przykładzie. * \<Zastąp>nazwy aplikacji* *unikatową* nazwą (punktem końcowym serwera jest *https:// nazwa\<aplikacji>.azurewebsites.net).* Dozwolone znaki * \<dla nazwy aplikacji>* `A` - `Z`to `0` - `9`, `-`i .

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

To polecenie może chwilę potrwać, ponieważ wykonuje następujące czynności:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Automatycznie generuje [grupę zasobów.](../../azure-resource-manager/management/overview.md#terminology)
- Tworzy [plan usługi app service](../overview-hosting-plans.md) *myAppServicePlan* w podstawowej warstwie cenowej (B1), jeśli nie istnieje. `--plan`i `--sku` są opcjonalne.
- Tworzy aplikację usługi App Service, jeśli nie istnieje.
- Włącza domyślne rejestrowanie aplikacji, jeśli nie jest już włączone.
- Przekazuje repozytorium przy użyciu wdrożenia ZIP z włączoną automatyzacją kompilacji.

Po zakończeniu wdrażania zobaczysz dane wyjściowe JSON, takie jak następujące:

<pre>
{
  "URL": "http://<app-name>.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "<app-resource-group>",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Skopiuj wartość * \<>grupy zasobów aplikacji *. Jest to potrzebne do skonfigurowania aplikacji później. 

> [!TIP]
> Można użyć tego samego polecenia później, aby wdrożyć wszelkie zmiany i natychmiast włączyć dzienniki diagnostyczne z:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

Przykładowy kod jest teraz wdrażany, ale aplikacja nie łączy się jeszcze z bazą danych Postgres na platformie Azure. Zrobisz to dalej.

### <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Po uruchomieniu aplikacji lokalnie, można ustawić zmienne środowiskowe w sesji terminala. W usłudze app service, można to zrobić z *ustawieniami aplikacji*, za pomocą [az webapp config appsettings ustawić](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) polecenie.

Uruchom następujące polecenie, aby określić szczegóły połączenia bazy danych jako ustawienia aplikacji. * \<Zastąp>nazwy aplikacji, * * \<>grupy zasobów aplikacji *i * \<>nazw postgresql* własnymi wartościami. Pamiętaj, że poświadczenia `root` `Pollsdb1` użytkownika i zostały `az postgres up`utworzone dla Ciebie przez .

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Aby uzyskać informacje o tym, jak kod uzyskuje dostęp do tych ustawień aplikacji, zobacz [Dostęp do zmiennych środowiskowych](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Uruchamianie migracji bazy danych

Aby uruchomić migracje bazy danych w usłudze App Service, otwórz sesję SSH w przeglądarce, przechodząc do *https:// nazwa\<aplikacji>.scm.azurewebsites.net/webssh/host:*

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

W sesji SSH uruchom następujące polecenia:

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

Przejdź do wdrożonej aplikacji z adresem URL *http:\//\<nazwa aplikacji>.azurewebsites.net* w przeglądarce. Powinien zostać wyświetlony komunikat **Brak dostępnych ankiet.** 

Przejdź do *\//\<http: nazwa aplikacji>.azurewebsites.net/admin* i zaloguj się przy użyciu użytkownika administratora utworzonego w ostatnim kroku. Wybierz **pozycję Dodaj** obok pozycji **Pytania**i utwórz pytanie ankietowe z wybranymi wyborami.

Przejdź do wdrożonej aplikacji z adresem URL *\//\<http: nazwa aplikacji>.azurewebsites.net/admin*i utwórz kilka pytań ankietowych. Pytania można zobaczyć na *stronie\//\<http: app-name>.azurewebsites.net/*. 

![Uruchamianie aplikacji Python Django w usługach app services na platformie Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Przejdź do wdrożonej aplikacji z adresem URL *http:\//\<nazwa aplikacji>.azurewebsites.net* ponownie, aby zobaczyć pytanie ankiety i odpowiedzieć na pytanie.

Usługa App Service wykrywa projekt Django w repozytorium, szukając *pliku wsgi.py* w każdym `manage.py startproject` podkatalogu, który tworzy domyślnie. Gdy usługa App Service znajdzie plik, ładuje aplikację internetową Django. Aby uzyskać więcej informacji na temat sposobu ładowania aplikacji języka Python przez usługę App Service, zobacz [Konfigurowanie wbudowanego obrazu języka Python](how-to-configure-python.md).

**Gratulacje!** Korzystasz z aplikacji sieci Web Python (Django) w usłudze Azure App Service dla systemu Linux.

## <a name="develop-app-locally-and-redeploy"></a>Tworzenie aplikacji lokalnie i ponowne wdrożenie

W tej sekcji programujesz aplikację w środowisku lokalnym i ponownie wdrożyć kod do usługi App Service.

### <a name="set-up-locally-and-run"></a>Konfigurowanie lokalnie i uruchamianie

Aby skonfigurować lokalne środowisko programistyczne i uruchomić przykładową aplikację po raz pierwszy, uruchom następujące polecenia:

# <a name="bash"></a>[Bash](#tab/bash)

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

# <a name="cmd"></a>[Cmd](#tab/cmd)

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

Gdy aplikacja internetowa Django jest w pełni załadowana, zwraca coś następującego:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Przejdź do *\/http: /localhost:8000* w przeglądarce. Powinien zostać wyświetlony komunikat **Brak dostępnych ankiet.** 

Przejdź do *\/http: /localhost:8000/admin* i zaloguj się przy użyciu użytkownika administratora utworzonego w ostatnim kroku. Wybierz **pozycję Dodaj** obok pozycji **Pytania**i utwórz pytanie ankietowe z wybranymi wyborami.

![Uruchamianie aplikacji Python Django w usługach app services lokalnie](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Przejdź do *\/http: /localhost:8000* ponownie, aby zobaczyć pytanie ankiety i odpowiedzieć na pytanie. Lokalna przykładowa aplikacja Django zapisuje i przechowuje dane użytkownika w lokalnej bazie danych Sqlite3, więc nie musisz się martwić o zepsucie produkcyjnej bazy danych. Aby środowisko programistyczne było zgodne ze środowiskiem platformy Azure, należy rozważyć użycie bazy danych Postgres lokalnie.

Aby zatrzymać serwer Django, wpisz Ctrl+C.

### <a name="update-the-app"></a>Aktualizowanie aplikacji

Aby zobaczyć, jak działa aktualizacja aplikacji, `polls/models.py`dokonaj niewielkiej zmiany w . Znajdź linię:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

I zmień go na:

```python
choice_text = models.CharField(max_length=100)
```

Zmieniając model danych, musisz utworzyć nową migrację Django. Wykonaj następujące polecenie:

```
python manage.py makemigrations
```

Zmiany można przetestować lokalnie, uruchamiając migracje, uruchamiając serwer programistyczny i przechodząc do *\/http: /localhost:8000/admin:*

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Ponowne wdrożenie kodu na platformie Azure

Aby ponownie wdrożyć zmiany, uruchom następujące polecenie z katalogu głównego repozytorium:

```azurecli
az webapp up --name <app-name>
```

Usługa App Service wykrywa, że aplikacja istnieje i po prostu wdraża kod.

### <a name="rerun-migrations-in-azure"></a>Ponowne rozpoczynanie migracji na platformie Azure

Ponieważ wprowadzono zmiany w modelu danych, należy ponownie uruchomić migracje bazy danych w usłudze App Service. Otwórz sesję SSH w przeglądarce, przechodząc do *https:// nazwa\<aplikacji>.scm.azurewebsites.net/webssh/host*. Uruchom następujące polecenia:

```
cd site/wwwroot

# Activate the virtual environment
source venv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Przeglądanie aplikacji w produkcji

Przejdź do *\//\<http: nazwa aplikacji>.azurewebsites.net* i zobacz zmiany uruchomione na żywo w produkcji. 

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

Można uzyskać dostęp do dzienników konsoli generowanych z wewnątrz kontenera.

> [!TIP]
> `az webapp up`włącza domyślne rejestrowanie. Ze względu na wydajność to rejestrowanie wyłącza się po pewnym `az webapp up` czasie, ale włącza się za każdym razem, gdy ponownie uruchomisz. Aby włączyć ją ręcznie, uruchom następujące polecenie:
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
> Można również sprawdzić pliki dziennika z `https://<app-name>.scm.azurewebsites.net/api/logs/docker`przeglądarki w .

Aby zatrzymać przesyłanie strumieniowe `Ctrl` + `C`dziennika w dowolnym momencie, wpisz .

## <a name="manage-your-app-in-the-azure-portal"></a>Zarządzanie aplikacją w witrynie Azure portal

W [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz utworzoną aplikację.

![Przejdź do aplikacji Python Django w witrynie Azure portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. W tym miejscu można również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywania, ponownego uruchamiania i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Zarządzanie aplikacją Python Django na stronie Przegląd w witrynie Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie spodziewasz się, że te zasoby będą potrzebne w przyszłości, usuń grupy zasobów, uruchamiając następujące polecenia:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiedziałeś się:

> [!div class="checklist"]
> * Tworzenie bazy danych usługi Azure Database dla postgreSQL
> * Wdrażanie kodu w usłudze Azure App Service i łączenie się z usługą Postgres
> * Aktualizowanie kodu i ponowne wdrożenie
> * Wyświetlanie dzienników diagnostycznych
> * Zarządzanie aplikacją sieci Web w witrynie Azure portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zamapować niestandardową nazwę DNS na aplikację:

> [!div class="nextstepaction"]
> [Samouczek: Mapowanie niestandardowej nazwy DNS do aplikacji](../app-service-web-tutorial-custom-domain.md)

Lub sprawdź inne zasoby:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Języka Python](how-to-configure-python.md)
