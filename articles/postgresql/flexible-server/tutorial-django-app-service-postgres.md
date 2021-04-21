---
title: 'Samouczek: wdrażanie aplikacji Django za App Service i Azure Database for PostgreSQL — elastyczny serwer (wersja zapoznawcza) w sieci wirtualnej'
description: Wdrażanie aplikacji Django z usługą App Serice i Azure Database for PostgreSQL — elastyczny serwer (wersja zapoznawcza) w sieci wirtualnej
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 84fd0c368bcf39af1bae90dc83336ffdb8489768
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791521"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Samouczek: wdrażanie aplikacji Django za App Service i Azure Database for PostgreSQL — elastyczny serwer (wersja zapoznawcza)

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Z tego samouczka dowiesz się, jak wdrożyć aplikację Django na platformie Azure przy użyciu usługi App Services i Azure Database for PostgreSQL — elastyczny serwer w sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej lokalnie. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się do swojego konta przy użyciu [polecenia az login.](/cli/azure/authenticate-azure-cli) Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp właściwość **subscription ID** z danych **wyjściowych az login** subskrypcji symbolem zastępczym identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>Klonowanie lub pobieranie przykładowej aplikacji

# <a name="git-clone"></a>[Klon git](#tab/clone)

Sklonuj przykładowe repozytorium:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Następnie przejdź do tego folderu:

```terminal
cd djangoapp
```

# <a name="download"></a>[Pobieranie](#tab/download)

Odwiedź [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) stronę , wybierz pozycję Clone **(Sklonuj),** a następnie wybierz pozycję Download ZIP **(Pobierz plik ZIP).**

Rozpakuj plik ZIP do folderu o nazwie *djangoapp*.

Następnie otwórz okno terminalu w tym *folderze djangoapp.*

---

Przykład djangoapp zawiera aplikację sondowania Django opartego na danych, która jest pisania pierwszej aplikacji [Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) w dokumentacji django. Ukończona aplikacja jest dostarczana tutaj dla Twojej wygody.

Przykład jest również modyfikowany w celu uruchamiania w środowisku produkcyjnym, na przykład App Service:

- Ustawienia produkcyjne znajdują się w *pliku azuresite/production.py.* Szczegóły projektowania znajdują się w *witrynie azuresite/settings.py.*
- Aplikacja używa ustawień produkcyjnych, gdy `DJANGO_ENV` zmienna środowiskowa jest ustawiona na wartość "production". Tę zmienną środowiskową utworzysz w dalszej części tego samouczka wraz z innymi używanymi do konfiguracji bazy danych PostgreSQL.

Te zmiany są specyficzne dla konfigurowania django do uruchamiania w dowolnym środowisku produkcyjnym i nie są specyficzne dla App Service. Aby uzyskać więcej informacji, zobacz listę [kontrolną wdrażania Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Tworzenie serwera elastycznego PostgreSQL w nowej sieci wirtualnej

Utwórz prywatny serwer elastyczny i bazę danych w sieci wirtualnej za pomocą następującego polecenia:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

To polecenie wykonuje następujące akcje, które mogą potrwać kilka minut:

- Utwórz grupę zasobów, jeśli jeszcze nie istnieje.
- Generuje nazwę serwera, jeśli nie zostanie podany.
- Utwórz nową sieć wirtualną dla nowego serwera postgreSQL. **Zanotuj nazwę sieci wirtualnej** i nazwę podsieci utworzone dla serwera, ponieważ musisz dodać aplikację internetową do tej samej sieci wirtualnej.
- Tworzy nazwę użytkownika administratora , hasło dla serwera, jeśli nie zostanie podane. **Zanotuj nazwę użytkownika i hasło do** użycia w następnym kroku.
- Utwórz bazę ```postgres``` danych, która może służyć do tworzenia aplikacji. Możesz uruchomić program [ **psql,** aby nawiązać połączenie z bazą danych w](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) celu utworzenia innej bazy danych.

> [!NOTE]
> Zanotuj hasło, które zostanie wygenerowane automatycznie, jeśli nie zostanie podane. Jeśli zapomnisz hasła, musisz zresetować hasło za pomocą ``` az postgres flexible-server update``` polecenia


## <a name="deploy-the-code-to-azure-app-service"></a>Wdrażanie kodu w Azure App Service

W tej sekcji utworzysz hosta aplikacji w App Service, połączysz tę aplikację z bazą danych Postgres, a następnie wdrożysz kod na tym hoście.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Tworzenie App Service internetowej w sieci wirtualnej

W terminalu upewnij się, że jesteś w katalogu głównym repozytorium ( `djangoapp` ), który zawiera kod aplikacji.

Utwórz App Service aplikacji (proces hosta) za pomocą [`az webapp up`](/cli/azure/webapp#az_webapp_up) polecenia :

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- Dla `--location` argumentu użyj tej samej lokalizacji co w przypadku bazy danych w poprzedniej sekcji.
- Zastąp *\<app-name>* unikatową nazwą na całej platformie Azure (punkt końcowy serwera to `https://\<app-name>.azurewebsites.net` ). Dozwolone znaki dla *\<app-name>* to `A` - `Z` , `0` - `9` i `-` . Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
- Utwórz plan [App Service](../../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* w warstwie cenowej Podstawowa (B1), jeśli nie istnieje. `--plan` i `--sku` są opcjonalne.
- Utwórz aplikację App Service, jeśli nie istnieje.
- Włącz domyślne rejestrowanie dla aplikacji, jeśli jeszcze nie zostało włączone.
- Przekaż repozytorium przy użyciu wdrożenia pliku ZIP z włączoną automatyzacją kompilacji.
- **Polecenie az webapp vnet-integration** dodaje aplikację internetową w tej samej sieci wirtualnej co serwer postgres.
- Kod aplikacji oczekuje znalezienia informacji o bazie danych w wielu zmiennych środowiskowych. Aby ustawić zmienne środowiskowe w App Service, należy utworzyć "ustawienia aplikacji" za pomocą polecenia [az webapp config appsettings set.](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set)

> [!TIP]
> Wiele poleceń interfejsu wiersza polecenia platformy Azure buforuje typowe parametry, takie jak nazwa grupy zasobów i plan App Service, do pliku *.azure/config.* W związku z tym nie trzeba określać tego samego parametru za pomocą późniejszych poleceń. Aby na przykład ponownie wdać aplikację po w wprowadzenie zmian, możesz po prostu uruchomić `az webapp up` ją ponownie bez żadnych parametrów.

### <a name="run-django-database-migrations"></a>Uruchamianie migracji bazy danych Django

Migracje baz danych Django zapewniają, że schemat w bazie danych PostgreSQL na platformie Azure jest taki, jak opisany w kodzie.

1. Otwórz sesję SSH w przeglądarce, przechodząc do witryny *https:// \<app-name> .scm.azurewebsites.net/webssh/host* i logując się przy użyciu poświadczeń konta platformy Azure (a nie poświadczeń serwera bazy danych).

1. W sesji SSH uruchom następujące polecenia (możesz wkleić polecenia za pomocą **klawisza Ctrl** + **Shift** + **V):**

    ```bash
    cd site/wwwroot

    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

1. Polecenie `createsuperuser` wyświetli monit o poświadczenia superużytkownika. Na potrzeby tego samouczka użyj domyślnej nazwy użytkownika , naciśnij klawisz Enter dla adresu e-mail, aby pozostawić to pole puste, a następnie wprowadź wartość `root` jako  `postgres1` hasło.

### <a name="create-a-poll-question-in-the-app"></a>Tworzenie pytania ankiety w aplikacji

1. W przeglądarce otwórz adres URL *http: \/ / \<app-name> .azurewebsites.net*. Aplikacja powinna wyświetlić komunikat "No polls are available" (Nie są dostępne żadne sondowania), ponieważ nie ma jeszcze żadnych konkretnych sond w bazie danych.

1. Przejdź do *adresu http: \/ / \<app-name> .azurewebsites.net/admin*. Zaloguj się przy użyciu poświadczeń superużytkownika z poprzedniej sekcji ( `root` i `postgres1` ). W **obszarze Sondy** wybierz pozycję **Dodaj** obok pola **Pytania** i utwórz pytanie ankiety z pewnymi wyborami.

1. Przejdź ponownie do *adresu http: \/ / \<app-name> azurewebsites.net/,* aby potwierdzić, że pytania są teraz prezentowane użytkownikowi. Odpowiedz na pytania, jeśli chcesz wygenerować dane w bazie danych.

**Gratulacje!** Uruchamiasz aplikację internetową Django w języku Python na platformie Azure App Service dla systemu Linux z aktywną bazą danych Postgres.

> [!NOTE]
> App Service projekt Django, szukając pliku *wsgi.py* w każdym podfolderze, który `manage.py startproject` domyślnie tworzy plik . Gdy App Service ten plik, ładuje aplikację internetową Django. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wbudowanego obrazu języka Python.](../../app-service/configure-language-python.md)

## <a name="make-code-changes-and-redeploy"></a>Wprowadzaj zmiany w kodzie i wdowuj ponownie

W tej sekcji wprowadzasz lokalne zmiany w aplikacji i ponownie wdajemy kod w App Service. W trakcie tego procesu skonfigurujemy środowisko wirtualne języka Python, które obsługuje bieżącą pracę.

### <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

W oknie terminalu uruchom następujące polecenia. Pamiętaj, aby postępować zgodnie z monitami podczas tworzenia superużytkownika:

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
Po pełnym załadowaniu aplikacji internetowej serwer projektowy Django udostępnia lokalny adres URL aplikacji w komunikacie "Starting development server at http://127.0.0.1:8000/ . Zamknij serwer za pomocą klawiszy CTRL-BREAK".

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Przykładowe dane wyjściowe serwera projektowego Django":::

Przetestuj aplikację lokalnie, aby wykonać następujące czynności:

1. Przejdź do *adresu http: \/ /localhost:8000* w przeglądarce. Powinien zostać wyświetlony komunikat "No polls are available" (Nie są dostępne żadne sondowania).

1. Przejdź do *adresu http: \/ /localhost:8000/admin* i zaloguj się przy użyciu utworzonego wcześniej administratora. W **obszarze Sondy** ponownie wybierz pozycję **Dodaj** obok pola **Pytania** i utwórz pytanie ankiety z pewnymi wyborami.

1. Ponownie przejdź *do adresu http: \/ /localhost:8000* i odpowiedz na pytanie, aby przetestować aplikację.

1. Zatrzymaj serwer Django, naciskając **klawisze Ctrl** + **C**.

W przypadku uruchamiania lokalnego aplikacja używa lokalnej bazy danych Sqlite3 i nie zakłóca działania produkcyjnej bazy danych. W razie potrzeby możesz również użyć lokalnej bazy danych PostgreSQL, aby lepiej symulować środowisko produkcyjne.



### <a name="update-the-app"></a>Aktualizowanie aplikacji

W `polls/models.py` programie znajdź wiersz, który zaczyna się od i `choice_text` zmień parametr na `max_length` 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Ponieważ model danych został zmieniony, utwórz nową migrację Django i zmigruj bazę danych:

```
python manage.py makemigrations
python manage.py migrate
```

Uruchom ponownie serwer projektowy za pomocą i `python manage.py runserver` przetestuj aplikację pod adresem *http: \/ /localhost:8000/admin*:

Zatrzymaj ponownie serwer internetowy Django za pomocą **klawisza Ctrl** + **C.**


### <a name="redeploy-the-code-to-azure"></a>Ponowne wdychaj kod na platformie Azure

Uruchom następujące polecenie w katalogu głównym repozytorium:

```azurecli
az webapp up
```

To polecenie używa parametrów buforowanych w *pliku .azure/config.* Ponieważ App Service aplikacja już istnieje, po prostu ponownie wdepnie kod.



### <a name="rerun-migrations-in-azure"></a>Ponowne uruchomić migracje na platformie Azure

Ze względu na wprowadzone zmiany w modelu danych należy ponownie uruchomić migracje baz danych w App Service.

Otwórz ponownie sesję SSH w przeglądarce, przechodząc do https:// *\<app-name> .scm.azurewebsites.net/webssh/host.* Następnie uruchom następujące polecenia:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Przeglądanie aplikacji w środowisku produkcyjnym

Przejdź do *adresu http: \/ / \<app-name> .azurewebsites.net* i ponownie przetestuj aplikację w środowisku produkcyjnym. (Ponieważ zmieniono tylko długość pola bazy danych, zmiana jest zauważalna tylko wtedy, gdy spróbujesz wprowadzić dłuższą odpowiedź podczas tworzenia pytania).

> [!TIP]
> Za pomocą magazynu [django-storage można przechowywać](https://django-storages.readthedocs.io/en/latest/backends/azure.html) statyczne zasoby & w usłudze Azure Storage. Możesz użyć Azure CDN do gzippingu dla plików statycznych.


## <a name="manage-your-app-in-the-azure-portal"></a>Zarządzanie aplikacją w Azure Portal

W [Azure Portal](https://portal.azure.com)wyszukaj nazwę aplikacji i wybierz aplikację w wynikach.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Przejdź do aplikacji Django w języku Python w Azure Portal":::

Domyślnie w portalu jest wyświetlana  strona Przegląd aplikacji, która zapewnia ogólny widok wydajności. W tym miejscu można również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Zarządzanie aplikacją Django w języku Python na stronie Przegląd w Azure Portal":::


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz zachować aplikację lub przejść do następnego samouczka, przejdź do [następnego kroku.](#next-steps) W przeciwnym razie, aby uniknąć naliczenie bieżących opłat, możesz usunąć grupę zasobów tworzyć w tym samouczku:

```azurecli
az group delete -g myresourcegroup
```

Polecenie używa nazwy grupy zasobów buforowanej w *pliku .azure/config.* Usuwając grupę zasobów, cofniesz przydział i usuniesz wszystkie zawarte w niej zasoby.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zamapować niestandardową nazwę DNS na aplikację:

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](../../app-service/app-service-web-tutorial-custom-domain.md)

Dowiedz się, App Service uruchamia aplikację w języku Python:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji w języku Python](../../app-service/configure-language-python.md)
