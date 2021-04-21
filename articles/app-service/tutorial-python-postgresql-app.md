---
title: 'Samouczek: wdrażanie aplikacji Django w języku Python przy użyciu bazy danych Postgres'
description: Tworzenie aplikacji internetowej w języku Python z bazą danych PostgreSQL i wdrażanie jej na platformie Azure. W samouczku jest używana framework Django, a aplikacja jest hostowana na Azure App Service dla systemu Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 02/02/2021
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 882a9fb0f8d528ca21cdc8149c60b9d5bdaf1723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767099"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Samouczek: wdrażanie aplikacji internetowej Django za pomocą bazy danych PostgreSQL na platformie Azure App Service

W tym samouczku przedstawiono sposób wdrażania opartej na danych aplikacji internetowej [Python Django](https://www.djangoproject.com/) w celu Azure App Service [i](overview.md) łączenia jej z bazą danych usługi Azure Database for Postgres. App Service zapewnia wysoce skalowalną i samonachowalną usługę hostingu w Internecie.

W tym samouczku użyjemy interfejsu wiersza polecenia platformy Azure do wykonania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie początkowego środowiska przy użyciu języka Python i interfejsu wiersza polecenia platformy Azure
> * Tworzenie bazy Azure Database for PostgreSQL danych
> * Wdrażanie kodu w Azure App Service i nawiązywanie połączenia z usługą PostgreSQL
> * Aktualizowanie kodu i ponowne wdychaj
> * Wyświetlanie dzienników diagnostycznych
> * Zarządzanie aplikacją internetową w Azure Portal

Możesz również użyć Azure Portal [tego samouczka.](/azure/developer/python/tutorial-python-postgresql-app-portal)


## <a name="1-set-up-your-initial-environment"></a>1. Konfigurowanie początkowego środowiska

1. Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Zainstaluj <a href="https://www.python.org/downloads/" target="_blank">język Python w wersji 3.6 lub wyższej.</a>
1. Zainstaluj interfejs <a href="/cli/azure/install-azure-cli" target="_blank">wiersza polecenia</a> platformy Azure w wersji 2.18.0 lub wyższej, za pomocą którego uruchamiasz polecenia w dowolnej powłoki w celu aprowizowania i konfigurowania zasobów platformy Azure.

Otwórz okno terminalu i sprawdź, czy wersja języka Python to 3.6 lub nowsza:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Sprawdź, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.18.0 lub wyższej:

```azurecli
az --version
```

Jeśli musisz wykonać uaktualnienie, wypróbuj polecenie `az upgrade` (wymaga wersji 2.11 lub wersji lub wersji) lub zobacz <a href="/cli/azure/install-azure-cli" target="_blank">Instalowanie interfejsu wiersza polecenia platformy Azure.</a>

Następnie zaloguj się do platformy Azure za pomocą interfejsu wiersza polecenia:

```azurecli
az login
```

To polecenie powoduje otwarcie przeglądarki w celu zebrania poświadczeń. Po zakończeniu polecenie wyświetla dane wyjściowe JSON zawierające informacje o subskrypcjach.

Po zalogowaniu można uruchamiać polecenia platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, aby pracować z zasobami w ramach subskrypcji.

Masz problemy? [Daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

## <a name="2-clone-or-download-the-sample-app"></a>2. Klonowanie lub pobieranie przykładowej aplikacji

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

Odwiedź [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) stronę , wybierz pozycję Clone **(Klonuj),** a następnie wybierz pozycję Download **ZIP (Pobierz plik ZIP).** 

Rozpakuj plik ZIP do folderu o nazwie *djangoapp*. 

Następnie otwórz okno terminalu w tym *folderze djangoapp.*

---

Przykład djangoapp zawiera aplikację do sondowania Django opartego na danych, która jest pisania pierwszej aplikacji [Django](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) w dokumentacji django. Ukończona aplikacja jest tutaj dostarczana dla Twojej wygody.

Przykład jest również modyfikowany w celu uruchamiania w środowisku produkcyjnym, na przykład App Service:

- Ustawienia produkcyjne znajdują się w *pliku azuresite/production.py.* Ustawienia dewelopera znajdują się w *witrynie azuresite/settings.py.*
- Aplikacja używa ustawień produkcyjnych, gdy `WEBSITE_HOSTNAME` zmienna środowiskowa jest ustawiona. Azure App Service automatycznie ustawia tę zmienną na adres URL aplikacji internetowej, taki jak `msdocs-django.azurewebsites.net` .

Ustawienia produkcyjne są specyficzne dla konfigurowania środowiska Django do uruchamiania w dowolnym środowisku produkcyjnym i nie są specyficzne dla App Service. Aby uzyskać więcej informacji, zobacz listę [kontrolną wdrażania Django](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/). Aby uzyskać szczegółowe informacje na temat niektórych zmian, zobacz Ustawienia produkcyjne platformy Django na platformie [Azure.](configure-language-python.md#production-settings-for-django-apps)

Masz problemy? [Daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

## <a name="3-create-postgres-database-in-azure"></a>3. Tworzenie bazy danych Postgres na platformie Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Zainstaluj rozszerzenie `db-up` dla interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name db-up
```

Jeśli polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem w tesłudze `az` [Konfigurowanie początkowego środowiska](#1-set-up-your-initial-environment).

Następnie utwórz bazę danych Postgres na platformie Azure za pomocą [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) polecenia :

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Zamień** *\<postgres-server-name>* o nazwie, która jest unikatowa **na całej platformie Azure** (punkt końcowy serwera staje się `https://<postgres-server-name>.postgres.database.azure.com` ). Dobrym wzorcem jest użycie kombinacji nazwy firmy i innej unikatowej wartości.
- W *\<admin-username>* przypadku serwerów i określ *\<admin-password>* poświadczenia, aby utworzyć użytkownika administratora dla tego serwera Postgres. Nazwa użytkownika administratora nie może być azure_superuser *,* *azure_pg_admin,* *admin,* *administrator,* *root,* *guest* ani *public*. Nie można rozpocząć od *pg_*. Hasło musi zawierać od 8 do **128** znaków z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (od 0 do 9) i znaki inne niż alfanumeryczne (na przykład !, #, %). Hasło nie może zawierać nazwy użytkownika.
- Nie używaj znaku `$` w nazwie użytkownika lub hasła. Później utworzysz zmienne środowiskowe z tymi wartościami, gdzie znak ma specjalne znaczenie w kontenerze `$` systemu Linux używanym do uruchamiania aplikacji języka Python.
- Warstwa cenowa B_Gen5_1 (Podstawowa, 5. generacji, 1 [rdzeń)](../postgresql/concepts-pricing-tiers.md) jest najdroższa. W przypadku produkcyjnych baz danych pomiń argument , aby zamiast GP_Gen5_2 `--sku-name` (Ogólnego przeznaczenia, 5. generacji, 2 rdzenie).

To polecenie wykonuje następujące akcje, które mogą potrwać kilka minut:

- Utwórz [grupę zasobów](../azure-resource-manager/management/overview.md#terminology) o `DjangoPostgres-tutorial-rg` nazwie , jeśli jeszcze nie istnieje.
- Utwórz serwer Postgres o nazwie według `--server-name` argumentu .
- Utwórz konto administratora przy użyciu `--admin-user` argumentów `--admin-password` i . Możesz pominąć te argumenty, aby umożliwić poleceniem wygenerowanie unikatowych poświadczeń.
- Utwórz bazę `pollsdb` danych o nazwie według `--database-name` argumentu .
- Włącz dostęp z lokalnego adresu IP.
- Włącz dostęp z usług platformy Azure.
- Utwórz użytkownika bazy danych z dostępem do `pollsdb` bazy danych.

Wszystkie kroki można wykonać oddzielnie za pomocą innych `az postgres` poleceń i , ale wszystkie kroki są `psql` `az postgres up` wspólnie.

Po zakończeniu działania polecenia zostanie wygenerowany obiekt JSON zawierający różne parametry połączenia dla bazy danych wraz z adresem URL serwera, wygenerowaną nazwą użytkownika (taką jak " ") i hasłem joyfulKoala@msdocs-djangodb-12345 GUID. **Skopiuj nazwę użytkownika i hasło do tymczasowego pliku tekstowego,** ponieważ będą potrzebne w dalszej części tego samouczka.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`Wartość można ustawić na dowolny z regionów [świadczenia usługi Azure.](https://azure.microsoft.com/global-infrastructure/regions/) Regiony dostępne dla subskrypcji możesz uzyskać za pomocą [`az account list-locations`](/cli/azure/account#az_account_list_locations) polecenia . W przypadku aplikacji produkcyjnych umieść bazę danych i aplikację w tej samej lokalizacji.

Masz problemy? [Daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. Wdrażanie kodu w Azure App Service

W tej sekcji utworzysz hosta aplikacji w App Service, połączysz tę aplikację z bazą danych Postgres, a następnie wdrożysz kod na tym hoście.

### <a name="41-create-the-app-service-app"></a>4.1. Tworzenie App Service aplikacji

W terminalu upewnij się, że jesteś w *folderze repozytorium djangoapp* zawierającym kod aplikacji.

Utwórz App Service aplikacji (proces hosta) za pomocą [`az webapp up`](/cli/azure/webapp#az_webapp_up) polecenia :

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Dla `--location` argumentu użyj tej samej lokalizacji co w przypadku bazy danych w poprzedniej sekcji.
- **Zamień** *\<app-name>* o unikatowej nazwie na całej platformie Azure (punkt końcowy serwera to `https://<app-name>.azurewebsites.net` ). Dozwolone znaki dla *\<app-name>* to `A` - `Z` , `0` - `9` i `-` . Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.

To polecenie wykonuje następujące akcje, które mogą potrwać kilka minut:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Utwórz [grupę zasobów,](../azure-resource-manager/management/overview.md#terminology) jeśli jeszcze nie istnieje. (W tym poleceniu użyj tej samej grupy zasobów, w której wcześniej utworzono bazę danych).
- Utwórz plan [App Service](overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* w warstwie cenowej Podstawowa (B1), jeśli nie istnieje. `--plan` i `--sku` są opcjonalne.
- Utwórz aplikację App Service, jeśli nie istnieje.
- Włącz domyślne rejestrowanie dla aplikacji, jeśli jeszcze nie zostało włączone.
- Przekaż repozytorium przy użyciu wdrożenia pliku ZIP z włączoną automatyzacją kompilacji.
- Buforuj typowe parametry, takie jak nazwa grupy zasobów i plan App Service, do pliku *.azure/config.* W związku z tym nie trzeba określać tego samego parametru za pomocą późniejszych poleceń. Aby na przykład ponownie wdać aplikację po w wprowadzenie zmian, możesz po prostu uruchomić `az webapp up` ją ponownie bez żadnych parametrów. Polecenia, które pochodzą z rozszerzeń interfejsu wiersza polecenia, takie jak , nie używają obecnie pamięci podręcznej, dlatego trzeba było określić grupę zasobów i lokalizację w tym miejscu przy początkowym użyciu `az postgres up` polecenia `az webapp up` .

Po pomyślnym wdrożeniu polecenie generuje dane wyjściowe JSON, jak w poniższym przykładzie:

![Przykładowe dane wyjściowe polecenia az webapp up](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

Masz problemy? W pierwszej kolejności zapoznaj się [z przewodnikiem rozwiązywania](configure-language-python.md#troubleshooting)problemów. W przeciwnym razie [daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2. Konfigurowanie zmiennych środowiskowych w celu połączenia z bazą danych

Po wdrożeniu kodu w usłudze App Service następnym krokiem jest połączenie aplikacji z bazą danych Postgres na platformie Azure.

Kod aplikacji oczekuje znalezienia informacji o bazie danych w czterech zmiennych środowiskowych o nazwach `DBHOST` , `DBNAME` , i `DBUSER` `DBPASS` .

Aby ustawić zmienne środowiskowe w App Service, utwórz "ustawienia aplikacji" za pomocą następującego polecenia [az webapp config appsettings set.](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set)

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Zastąp *\<postgres-server-name>* nazwą używaną wcześniej za pomocą `az postgres up` polecenia . Kod w pliku *azuresite/production.py* jest dołączany automatycznie w celu utworzenia pełnego adresu `.postgres.database.azure.com` URL serwera Postgres.
- Zastąp *\<username>* *\<password>* i poświadczeniami administratora, które były używane z wcześniejszym poleceniem `az postgres up` lub `az postgres up` wygenerowanymi automatycznie. Kod w *witrynie azuresite/production.py* automatycznie tworzy pełną nazwę użytkownika postgres z i , więc nie `DBUSER` `DBHOST` dołączaj `@server` części . (Ponadto, jak wspomniano wcześniej, nie należy używać znaku w jednej z wartości, ponieważ ma ona specjalne znaczenie `$` dla zmiennych środowiskowych systemu Linux).
- Nazwy grup zasobów i aplikacji są rysowane na podstawie buforowanych wartości w *pliku .azure/config.*

W kodzie języka Python uzyskujesz dostęp do tych ustawień jako zmiennych środowiskowych za pomocą instrukcji takich jak `os.environ.get('DBHOST')` . Aby uzyskać więcej informacji, zobacz [Access environment variables (Uzyskiwanie dostępu do zmiennych środowiskowych).](configure-language-python.md#access-environment-variables)

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania](configure-language-python.md#troubleshooting)problemów. W przeciwnym razie [daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4.3. Uruchamianie migracji bazy danych Django

Migracje baz danych Django zapewniają, że schemat w bazie danych PostgreSQL na platformie Azure jest taki, jak opisany w kodzie.

1. Otwórz sesję SSH **w** przeglądarce, przechodząc do następującego adresu URL i logując się przy użyciu poświadczeń konta platformy Azure (a nie poświadczeń serwera bazy danych).

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    Zastąp `<app-name>` nazwą używaną wcześniej w `az webapp up` poleceniu .

    Możesz też nawiązać połączenie z sesją SSH za pomocą [`az webapp ssh`](/cli/azure/webapp#az_webapp_ssh) polecenia . W systemie Windows to polecenie wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.18.0 lub wyższej.

    Jeśli nie możesz nawiązać połączenia z sesją SSH, uruchomienie samej aplikacji nie powiodło się. [Sprawdź dzienniki diagnostyczne, aby](#6-stream-diagnostic-logs) uzyskać szczegółowe informacje. Jeśli na przykład nie utworzono niezbędnych ustawień aplikacji w poprzedniej sekcji, dzienniki będą wskazywać `KeyError: 'DBNAME'` .

1. W sesji SSH uruchom następujące polecenia (możesz wkleić polecenia za pomocą **klawisza Ctrl** + **Shift** + **V):**

    ```bash
    # Change to the app folder
    cd $APP_PATH
    
    # Activate the venv
    source /antenv/bin/activate

    # Install requirements
    pip install -r requirements.txt

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Jeśli wystąpią błędy związane z nawiązywanie połączenia z bazą danych, sprawdź wartości ustawień aplikacji utworzonych w poprzedniej sekcji.

1. Polecenie `createsuperuser` wyświetli monit o poświadczenia superużytkownika. Na potrzeby tego samouczka użyj domyślnej nazwy użytkownika , naciśnij klawisz Enter dla adresu e-mail, aby pozostawić to pole puste, a następnie wprowadź wartość `root` jako  `Pollsdb1` hasło.

1. Jeśli zostanie wyświetlony błąd z komunikatem o zablokowaniu bazy danych, upewnij się, że w `az webapp settings` poprzedniej sekcji uruchomiono polecenie . Bez tych ustawień polecenie migracji nie może komunikować się z bazą danych, co spowoduje błąd.

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania problemów.](configure-language-python.md#troubleshooting)W przeciwnym razie [daj nam znać](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4. Tworzenie pytania ankiety w aplikacji

1. W przeglądarce otwórz adres URL `http://<app-name>.azurewebsites.net` . Aplikacja powinna wyświetlić komunikat "Aplikacja sondowania" i "Nie są dostępne żadne sondowania", ponieważ nie ma jeszcze żadnych konkretnych sond w bazie danych.

    Jeśli zostanie wyświetlony komunikat "Błąd aplikacji", prawdopodobnie w poprzednim kroku nie utworzyliśmy wymaganych [ustawień,](#42-configure-environment-variables-to-connect-the-database)skonfiguruj zmienne środowiskowe w celu połączenia z bazą danych lub że te wartości zawierają błędy. Uruchom polecenie , `az webapp config appsettings list` aby sprawdzić ustawienia. Możesz również sprawdzić [dzienniki diagnostyczne, aby](#6-stream-diagnostic-logs) zobaczyć konkretne błędy podczas uruchamiania aplikacji. Jeśli na przykład nie utworzysz ustawień, w dziennikach będzie wyświetlany błąd `KeyError: 'DBNAME'` .

    Po zaktualizowaniu ustawień w celu poprawienia błędów należy dać aplikacji minutę na ponowne uruchomienie, a następnie odśwież przeglądarkę.

1. Przejdź na stronę `http://<app-name>.azurewebsites.net/admin`. Zaloguj się przy użyciu poświadczeń superużytkownika Django z poprzedniej sekcji ( `root` i `Pollsdb1` ). W **obszarze Sondy** wybierz pozycję **Dodaj** obok pola **Pytania** i utwórz pytanie ankiety z pewnymi wyborami.

1. Ponownie przejdź do strony , `http://<app-name>.azurewebsites.net` aby potwierdzić, że pytania są teraz prezentowane użytkownikowi. Odpowiedz na pytania, jednak chcesz wygenerować dane w bazie danych.

**Gratulacje!** Uruchamiasz aplikację internetową Django w języku Python na platformie Azure App Service dla systemu Linux z aktywną bazą danych Postgres.

Masz problemy? [Daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> App Service projekt Django, szukając pliku *wsgi.py* w każdym podfolderze, który domyślnie `manage.py startproject` tworzy. Gdy App Service ten plik, ładuje aplikację internetową Django. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wbudowanego obrazu języka Python.](configure-language-python.md)

## <a name="5-make-code-changes-and-redeploy"></a>5. Wprowadzaj zmiany w kodzie i wdowuj ponownie

W tej sekcji wprowadzasz lokalne zmiany w aplikacji i ponownie wdajemy kod w App Service. W trakcie tego procesu skonfigurujemy środowisko wirtualne języka Python, które będzie wspierać bieżącą pracę.

### <a name="51-run-the-app-locally"></a>5.1 Uruchamianie aplikacji lokalnie

W oknie terminalu uruchom następujące polecenia. Pamiętaj, aby postępować zgodnie z monitami podczas tworzenia superużytkownika:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install dependencies
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install dependencies
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Po pełnym załadowaniu aplikacji internetowej serwer projektowy Django udostępnia lokalny adres URL aplikacji w komunikacie "Starting development server at http://127.0.0.1:8000/ . Zamknij serwer za pomocą klawiszy CTRL-BREAK".

![Przykład danych wyjściowych serwera dewelopera Django](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Przetestuj aplikację lokalnie, wykonać następujące czynności:

1. Przejdź do `http://localhost:8000` strony w przeglądarce, która powinna wyświetlić komunikat "No polls are available" (Nie są dostępne żadne sondowania). 

1. Przejdź do `http:///localhost:8000/admin` strony i zaloguj się przy użyciu utworzonego wcześniej użytkownika administratora. W **obszarze Sondy** ponownie wybierz pozycję **Dodaj** obok pola **Pytania** i utwórz pytanie ankiety z pewnymi wyborami. 

1. Ponownie *przejdź do adresu http: \/ /localhost:8000* i odpowiedz na pytanie, aby przetestować aplikację. 

1. Zatrzymaj serwer Django, naciskając **klawisze Ctrl** + **C**.

W przypadku uruchamiania lokalnego aplikacja używa lokalnej bazy danych Sqlite3 i nie zakłóca działania produkcyjnej bazy danych. W razie potrzeby możesz również użyć lokalnej bazy danych PostgreSQL, aby lepiej symulować środowisko produkcyjne.

Masz problemy? [Daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

### <a name="52-update-the-app"></a>5.2 Aktualizowanie aplikacji

W lokalizacji znajdź wiersz, który zaczyna się od ciągu `polls/models.py` i zmień parametr na `choice_text` `max_length` 100:

```python
# Find this line of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Ponieważ model danych został zmieniony, utwórz nową migrację Django i zmigruj bazę danych:

```
python manage.py makemigrations
python manage.py migrate
```

Uruchom ponownie serwer projektowy za pomocą i przetestuj aplikację pod adresem `python manage.py runserver` *http: \/ /localhost:8000/admin*:

Zatrzymaj ponownie serwer internetowy Django za pomocą **klawisza Ctrl** + **C.**

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania problemów.](configure-language-python.md#troubleshooting)W przeciwnym razie [daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

### <a name="53-redeploy-the-code-to-azure"></a>5.3. Ponownie wdychaj kod na platformie Azure

Uruchom następujące polecenie w katalogu głównym repozytorium:

```azurecli
az webapp up
```

To polecenie używa parametrów buforowanych w *pliku .azure/config.* Ponieważ App Service aplikacja już istnieje, po prostu ponownie wdepnie kod.

Masz problemy? W pierwszej kolejności zapoznaj się [z przewodnikiem rozwiązywania](configure-language-python.md#troubleshooting)problemów. W przeciwnym razie [daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

### <a name="54-rerun-migrations-in-azure"></a>5.4 Ponowne uruchomić migracje na platformie Azure

Ze względu na wprowadzone zmiany w modelu danych należy ponownie uruchomić migracje baz danych w App Service.

Otwórz ponownie sesję SSH w przeglądarce, przechodząc do strony `https://<app-name>.scm.azurewebsites.net/webssh/host` . Następnie uruchom następujące polecenia:

```
cd $APP_PATH
source /antenv/bin/activate
pip install -r requirements.txt
python manage.py migrate
```

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania](configure-language-python.md#troubleshooting)problemów. W przeciwnym razie [daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

### <a name="55-review-app-in-production"></a>5.5 Przeglądanie aplikacji w środowisku produkcyjnym

Przejdź do `http://<app-name>.azurewebsites.net` strony i ponownie przetestuj aplikację w środowisku produkcyjnym. (Ponieważ zmieniono tylko długość pola bazy danych, zmiana jest zauważalna tylko wtedy, gdy spróbujesz wprowadzić dłuższą odpowiedź podczas tworzenia pytania).

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania](configure-language-python.md#troubleshooting)problemów. W przeciwnym razie [daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

## <a name="6-stream-diagnostic-logs"></a>6. Przesyłanie strumieniowe dzienników diagnostycznych

Możesz uzyskać dostęp do dzienników konsoli wygenerowanych z wewnątrz kontenera, który hostuje aplikację na platformie Azure.

Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby wyświetlić strumień dzienników. To polecenie używa parametrów buforowanych w *pliku .azure/config.*

```azurecli
az webapp log tail
```

Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie, wpisz **Ctrl** + **C**.

Masz problemy? [Daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

> [!NOTE]
> Pliki dzienników można także sprawdzać w przeglądarce pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
>
> `az webapp up` Włącza domyślne rejestrowanie. Ze względu na wydajność to rejestrowanie wyłącza się po pewnym czasie, ale jest ponownie włączane przy każdym `az webapp up` uruchomieniu ponownie. Aby włączyć ją ręcznie, uruchom następujące polecenie:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="7-manage-your-app-in-the-azure-portal"></a>7. Zarządzanie aplikacją w Azure Portal

W [Azure Portal](https://portal.azure.com)wyszukaj nazwę aplikacji i wybierz aplikację w wynikach.

![Przejdź do aplikacji Django w języku Python w Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Domyślnie w portalu jest wyświetlana  strona Przegląd aplikacji, która zapewnia ogólny widok wydajności. W tym miejscu można również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Zarządzanie aplikacją Django w języku Python na stronie Przegląd w Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania problemów.](configure-language-python.md#troubleshooting)W przeciwnym razie [daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

## <a name="8-clean-up-resources"></a>8. Czyszczenie zasobów

Jeśli chcesz zachować aplikację lub przejść do dodatkowych samouczków, przejdź do [następnego kroku.](#next-steps) W przeciwnym razie, aby uniknąć naliczenie bieżących opłat, możesz usunąć grupę zasobów tworzyć w tym samouczku:

```azurecli
az group delete --no-wait
```

Polecenie używa nazwy grupy zasobów buforowanej w *pliku .azure/config.* Usunięcie grupy zasobów powoduje również cofanie alokacji i usuwanie wszystkich zawartych w niej zasobów.

Usunięcie wszystkich zasobów może trochę potrwać. Argument `--no-wait` umożliwia natychmiastowe zwrócenie polecenia.

Masz problemy? [Daj nam znać](https://aka.ms/DjangoCLITutorialHelp).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zamapować niestandardową nazwę DNS na aplikację:

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](app-service-web-tutorial-custom-domain.md)

Dowiedz się, App Service uruchamia aplikację w języku Python:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji w języku Python](configure-language-python.md)
