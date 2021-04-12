---
title: 'Samouczek: wdrażanie aplikacji Django przy użyciu App Service i Azure Database for PostgreSQL — serwer elastyczny (wersja zapoznawcza) w sieci wirtualnej'
description: Wdrażanie aplikacji Django przy użyciu aplikacji usługi i Azure Database for PostgreSQL-elastyczny serwer (wersja zapoznawcza) w sieci wirtualnej
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bcc9ca0175e0e03c62c2ce2b91d8ec337756a3cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490105"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>Samouczek: wdrażanie aplikacji Django przy użyciu App Service i Azure Database for PostgreSQL — serwer elastyczny (wersja zapoznawcza)

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym samouczku dowiesz się, jak wdrożyć aplikację Django na platformie Azure przy użyciu App Services i Azure Database for PostgreSQL — elastyczny serwer w sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2,0 lub nowszej. Aby sprawdzić zainstalowaną wersję, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Musisz zalogować się na swoje konto za pomocą polecenia [AZ login](/cli/azure/authenticate-azure-cli) . Zanotuj właściwość **id** z danych wyjściowych polecenia dla odpowiedniej nazwy subskrypcji.

```azurecli
az login
```

Jeśli masz wiele subskrypcji, wybierz odpowiednią subskrypcję, w ramach której powinny być naliczane opłaty za ten zasób. Wybierz określony identyfikator subskrypcji na Twoim koncie za pomocą polecenia [az account set](/cli/azure/account). Zastąp wartość właściwości **Identyfikator subskrypcji** w polu **AZ login** Output for the Subscription do symbolu zastępczego identyfikatora subskrypcji.

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>Klonowanie lub pobieranie przykładowej aplikacji

# <a name="git-clone"></a>[Klonowanie git](#tab/clone)

Klonuj repozytorium przykładowe:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Następnie przejdź do tego folderu:

```terminal
cd djangoapp
```

# <a name="download"></a>[Pobieranie](#tab/download)

Odwiedź stronę [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , wybierz pozycję **Klonuj**, a następnie wybierz pozycję **Pobierz plik zip**.

Rozpakuj plik ZIP do folderu o nazwie *djangoapp*.

Następnie otwórz okno terminalu w tym folderze *djangoapp* .

---

Przykład djangoapp zawiera opartą na danych aplikację do sondowania Django, którą uzyskasz, wykonując [pisanie pierwszej aplikacji Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) w dokumentacji Django. Ukończona aplikacja jest udostępniana w tym miejscu dla wygody użytkownika.

Przykład jest również modyfikowany do uruchamiania w środowisku produkcyjnym, takim jak App Service:

- Ustawienia produkcyjne znajdują się w pliku *azuresite/Production. PR* . Szczegóły dotyczące programowania znajdują się w *azuresite/Settings. PR*.
- Aplikacja używa ustawień produkcyjnych, gdy `DJANGO_ENV` zmienna środowiskowa jest ustawiona na "produkcja". Tę zmienną środowiskową utworzysz w dalszej części tego samouczka wraz z innymi osobami używanymi do konfiguracji bazy danych PostgreSQL.

Te zmiany są specyficzne dla konfigurowania Django do uruchamiania w dowolnym środowisku produkcyjnym i nie są szczególnie App Service. Aby uzyskać więcej informacji, zobacz [Lista kontrolna wdrożenia Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Utwórz serwer elastyczny PostgreSQL w nowej sieci wirtualnej

Utwórz prywatny, elastyczny serwer i bazę danych w sieci wirtualnej (VNET) przy użyciu następującego polecenia:
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

To polecenie wykonuje następujące akcje, co może potrwać kilka minut:

- Utwórz grupę zasobów, jeśli jeszcze nie istnieje.
- Generuje nazwę serwera, jeśli nie została dostarczona.
- Utwórz nową sieć wirtualną dla nowego serwera postgreSQL. **Zanotuj nazwę sieci wirtualnej i nazwę podsieci** utworzoną dla serwera, ponieważ musisz dodać aplikację sieci Web do tej samej sieci wirtualnej.
- Nie można utworzyć nazwy użytkownika administratora, hasła dla serwera. **Zanotuj nazwę użytkownika i hasło** , które mają być używane w następnym kroku.
- Utwórz bazę danych ```postgres``` , która może być używana do programowania. Można uruchomić [ **PSQL** w celu nawiązania połączenia z bazą danych,](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) aby utworzyć inną bazę danych.

> [!NOTE]
> Zanotuj hasło, które zostanie wygenerowane, jeśli nie zostanie podane. Jeśli zapomnisz hasła, należy zresetować hasło przy użyciu ``` az postgres flexible-server update``` polecenia


## <a name="deploy-the-code-to-azure-app-service"></a>Wdróż kod w Azure App Service

W tej sekcji utworzysz hosta aplikacji w aplikacji App Service, Połącz tę aplikację z bazą danych Postgres, a następnie wdróż swój kod na tym hoście.


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>Tworzenie aplikacji sieci Web App Service w sieci wirtualnej

Upewnij się, że jesteś w katalogu głównym repozytorium ( `djangoapp` ), który zawiera kod aplikacji.

Utwórz aplikację App Service (proces hosta) za pomocą [`az webapp up`](/cli/azure/webapp#az-webapp-up) polecenia:

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
- Dla `--location` argumentu Użyj tej samej lokalizacji co w przypadku bazy danych w poprzedniej sekcji.
- Zamień na *\<app-name>* unikatową nazwę na całym systemie Azure (punkt końcowy serwera to `https://\<app-name>.azurewebsites.net` ). Dozwolone znaki dla *\<app-name>* są `A` - `Z` , `0` - `9` , i `-` . Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
- Utwórz plan [App Service](../../app-service/overview-hosting-plans.md) *DjangoPostgres — samouczek — plan* w warstwie cenowej Basic (B1), jeśli nie istnieje. `--plan` i `--sku` są opcjonalne.
- Utwórz aplikację App Service, jeśli nie istnieje.
- Włącz domyślne rejestrowanie dla aplikacji, jeśli nie została jeszcze włączona.
- Przekaż repozytorium przy użyciu wdrożenia ZIP z włączonym automatyzacją kompilacji.
- polecenie **AZ webapp VNET-Integration** dodaje aplikację sieci Web w tej samej sieci wirtualnej co serwer Postgres.
- Kod aplikacji oczekuje na znalezienie informacji o bazie danych w wielu zmiennych środowiskowych. Aby ustawić zmienne środowiskowe w App Service, należy utworzyć "Ustawienia aplikacji" przy użyciu polecenia [AZ webapp config AppSettings Set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

> [!TIP]
> Wiele poleceń interfejsu wiersza polecenia platformy Azure buforuje typowe parametry, takie jak nazwa grupy zasobów i plan App Service, do pliku *. Azure/config*. W związku z tym nie trzeba określać wszystkich parametrów z nowszymi poleceniami. Na przykład aby ponownie wdrożyć aplikację po wprowadzeniu zmian, można po prostu uruchomić polecenie `az webapp up` bez żadnych parametrów.

### <a name="run-django-database-migrations"></a>Uruchom migracje bazy danych Django

Migracja baz danych Django upewnij się, że schemat w PostgreSQL w bazie danych platformy Azure jest zgodny z tymi opisanymi w kodzie.

1. Otwórz sesję SSH w przeglądarce, przechodząc do *https:// \<app-name> . SCM.azurewebsites.NET/webssh/Host* i zaloguj się przy użyciu poświadczeń konta platformy Azure (nie poświadczeń serwera bazy danych).

1. W sesji SSH Uruchom następujące polecenia (można wkleić polecenia za pomocą **klawiszy CTRL** + **SHIFT** + **V**):

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

1. `createsuperuser`Polecenie prosi o poświadczenia administratora. Na potrzeby tego samouczka Użyj domyślnej nazwy użytkownika `root` , naciśnij klawisz **Enter** , aby adres e-mail pozostawić puste, a następnie wprowadź `postgres1` hasło.

### <a name="create-a-poll-question-in-the-app"></a>Tworzenie pytania dotyczącego ankiety w aplikacji

1. W przeglądarce Otwórz adres URL *http: \/ / \<app-name> . azurewebsites.NET*. Aplikacja powinna wyświetlić komunikat "Brak dostępnych sondowań", ponieważ w bazie danych nie ma jeszcze określonych sond.

1. Przejdź do *protokołu http: \/ / \<app-name> . azurewebsites.NET/admin*. Zaloguj się przy użyciu poświadczeń administratora z poprzedniej sekcji ( `root` i `postgres1` ). W obszarze **sondy** wybierz pozycję **Dodaj** obok pozycji **pytania** i Utwórz pytanie dotyczące ankiety z wybranymi opcjami.

1. Przejdź ponownie do *protokołu http: \/ / \<app-name> . azurewebsites.NET/* , aby potwierdzić, że pytania są teraz prezentowane użytkownikowi. Odpowiedz na pytania, na które chcesz wygenerować niektóre dane w bazie danych.

**Gratulacje!** Korzystasz z aplikacji sieci Web w języku Python Django w Azure App Service dla systemu Linux z aktywną bazą danych Postgres.

> [!NOTE]
> App Service wykrywa projekt Django, szukając pliku *WSGI.py* w każdym podfolderze, który `manage.py startproject` tworzy domyślnie. Gdy App Service odnajdzie ten plik, ładuje aplikację sieci Web Django. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wbudowanego obrazu w języku Python](../../app-service/configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Wprowadzanie zmian w kodzie i ponowne wdrażanie

W tej sekcji wprowadzasz lokalne zmiany w aplikacji i ponownie Wdróż kod w App Service. W procesie należy skonfigurować środowisko wirtualne języka Python, które obsługuje bieżące prace.

### <a name="run-the-app-locally"></a>Lokalne uruchamianie aplikacji

W oknie terminalu uruchom następujące polecenia. Pamiętaj, aby podczas tworzenia administratora postępuj zgodnie z monitami:

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
Po całkowitym załadowaniu aplikacji sieci Web serwer Django Development udostępnia adres URL aplikacji lokalnej w komunikacie "Uruchamianie serwera deweloperskiego pod adresem http://127.0.0.1:8000/ . Zamknij serwer przy użyciu kombinacji klawiszy CTRL-BREAK.

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="Przykład Django serwera deweloperskiego":::

Przetestuj aplikację lokalnie, wykonując następujące czynności:

1. Przejdź do *protokołu http: \/ /localhost: 8000* w przeglądarce, która powinna wyświetlać komunikat "Brak dostępnych sondowań".

1. Przejdź do *protokołu http: \/ /localhost: 8000/admin* i zaloguj się przy użyciu utworzonego wcześniej konta administratora. W obszarze **sondy** ponownie wybierz pozycję **Dodaj** obok pozycji **pytania** i Utwórz pytanie dotyczące ankiety z wybranymi opcjami.

1. Przejdź ponownie do *protokołu http: \/ /localhost: 8000* i Odpowiedz na pytanie, aby przetestować aplikację.

1. Zatrzymaj serwer Django, naciskając klawisz **Ctrl** + **C**.

W przypadku uruchamiania lokalnego aplikacja korzysta z lokalnej bazy danych Sqlite3 i nie zakłóca pracy w produkcyjnej bazie danych. W razie potrzeby można również użyć lokalnej bazy danych PostgreSQL, aby lepiej symulować środowisko produkcyjne.



### <a name="update-the-app"></a>Aktualizowanie aplikacji

W programie `polls/models.py` Znajdź wiersz zaczynający się od `choice_text` i Zmień `max_length` parametr na 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Ponieważ został zmieniony model danych, Utwórz nową migrację Django i Przeprowadź migrację bazy danych:

```
python manage.py makemigrations
python manage.py migrate
```

Uruchom ponownie serwer programistyczny `python manage.py runserver` i przetestuj aplikację pod adresem *http: \/ /localhost: 8000/admin*:

Zatrzymaj ponownie serwer sieci Web Django za pomocą **klawisza Ctrl** + **C**.


### <a name="redeploy-the-code-to-azure"></a>Ponowne wdrażanie kodu na platformie Azure

Uruchom następujące polecenie w katalogu głównym repozytorium:

```azurecli
az webapp up
```

To polecenie używa parametrów w pamięci podręcznej w pliku *. Azure/config* . Ponieważ App Service wykryje, że aplikacja już istnieje, po prostu ponownie wdraża kod.



### <a name="rerun-migrations-in-azure"></a>Ponowne uruchamianie migracji na platformie Azure

Ze względu na to, że wprowadzono zmiany w modelu danych, należy ponownie uruchomić migracje bazy danych w App Service.

Otwórz ponownie sesję SSH w przeglądarce, przechodząc do *https:// \<app-name> . SCM.azurewebsites.NET/webssh/Host*. Następnie uruchom następujące polecenia:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Przeglądanie aplikacji w środowisku produkcyjnym

Przejdź do *protokołu http: \/ / \<app-name> . azurewebsites.NET* i przetestuj aplikację ponownie w środowisku produkcyjnym. (Ponieważ zmieniono tylko długość pola bazy danych, zmiana jest zauważalna tylko wtedy, gdy użytkownik spróbuje wprowadzić dłuższy czas odpowiedzi podczas tworzenia pytania).

> [!TIP]
> [Magazynu Django](https://django-storages.readthedocs.io/en/latest/backends/azure.html) można użyć do przechowywania statycznych zasobów multimedialnych & w usłudze Azure Storage. Azure CDN dla plików statycznych można użyć do gzipping.


## <a name="manage-your-app-in-the-azure-portal"></a>Zarządzanie aplikacją w Azure Portal

W [Azure Portal](https://portal.azure.com)Wyszukaj nazwę aplikacji i wybierz aplikację w wynikach.

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="Przejdź do aplikacji w języku Python Django w Azure Portal":::

Domyślnie w portalu jest wyświetlana strona **omówienia** aplikacji, która zawiera ogólny widok wydajności. W tym miejscu można również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="Zarządzanie aplikacją Django języka Python na stronie Przegląd w Azure Portal":::


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz zachować aplikację lub przejść do następnego samouczka, przejdź do [sekcji Następne kroki](#next-steps). W przeciwnym razie, aby uniknąć ponoszenia opłat, możesz usunąć grupę zasobów utworzoną dla tego samouczka:

```azurecli
az group delete -g myresourcegroup
```

Polecenie używa nazwy grupy zasobów zapisanej w pamięci podręcznej w pliku *. Azure/config* . Usunięcie grupy zasobów spowoduje również cofnięcie alokacji i usunięcie wszystkich znajdujących się w niej zasobów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zmapować niestandardową nazwę DNS na aplikację:

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](../../app-service/app-service-web-tutorial-custom-domain.md)

Dowiedz się, jak App Service uruchamia aplikację w języku Python:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](../../app-service/configure-language-python.md)
