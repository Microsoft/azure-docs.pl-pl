---
title: Konfigurowanie aplikacji języka Python dla systemu Linux
description: Dowiedz się, jak skonfigurować kontener języka Python, w którym są uruchamiane aplikacje internetowe, przy użyciu interfejsu Azure Portal i interfejsu wiersza polecenia platformy Azure.
ms.topic: quickstart
ms.date: 03/16/2021
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 605d1e0f67ac959d2c7325e04e2fd10d9d2419be
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829498"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurowanie aplikacji języka Python dla systemu Linux dla Azure App Service

W tym artykule [opisano, Azure App Service](overview.md) uruchamiać aplikacje języka Python, jak migrować istniejące aplikacje na platformę Azure oraz jak dostosować zachowanie aplikacji App Service w razie potrzeby. Aplikacje języka Python muszą być wdrażane ze wszystkimi wymaganymi [modułami pip.](https://pypi.org/project/pip/)

Aparat App Service wdrażania automatycznie aktywuje środowisko wirtualne i jest uruchamiany automatycznie podczas wdrażania repozytorium Git lub `pip install -r requirements.txt` pakietu [zip.](deploy-zip.md) [](deploy-local-git.md)

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dla deweloperów języka Python, którzy używają wbudowanego kontenera systemu Linux w App Service. Jeśli nigdy wcześniej nie używaliśmy języka Azure App Service, najpierw postępuj zgodnie z samouczkiem Python [Quickstart](quickstart-python.md) and Python with PostgreSQL (Język Python z [programem PostgreSQL).](tutorial-python-postgresql-app.md)

Do konfiguracji możesz użyć [interfejsu Azure Portal](https://portal.azure.com) lub interfejsu wiersza polecenia platformy Azure:

- **Azure Portal** użyj strony Konfiguracja ustawień aplikacji zgodnie z opisem w te App Service  >   [aplikacji w](configure-common.md)Azure Portal .

- **Interfejs wiersza polecenia** platformy Azure: dostępne są dwie opcje.

    - Uruchom polecenia w [Azure Cloud Shell](../cloud-shell/overview.md).
    - Uruchom polecenia lokalnie, instalując najnowszą wersję interfejsu wiersza polecenia platformy [Azure,](/cli/azure/install-azure-cli)a następnie zaloguj się do platformy Azure przy użyciu [polecenia az login](/cli/azure/reference-index#az_login).
    
> [!NOTE]
> System Linux jest obecnie zalecaną opcją uruchamiania aplikacji w języku Python w App Service. Aby uzyskać informacje na temat opcji systemu Windows, zobacz [Python on the Windows flavor of App Service](/visualstudio/python/managing-python-on-azure-app-service)(Język Python w wersji systemu Windows dla App Service ).

## <a name="configure-python-version"></a>Konfigurowanie wersji języka Python

- **Azure Portal:** użyj karty **Ustawienia ogólne** na stronie **Konfiguracja** zgodnie z opisem w tece Configure general settings for Linux containers (Konfigurowanie ustawień ogólnych [dla](configure-common.md#configure-general-settings) kontenerów systemu Linux).

- **Interfejs wiersza polecenia platformy Azure:**

    -  Pokaż bieżącą wersję języka Python za pomocą [az webapp config show](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Zastąp `<resource-group-name>` i `<app-name>` nazwami odpowiednimi dla Twojej aplikacji internetowej.
    
    - Ustawianie wersji języka Python za pomocą [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Pokaż wszystkie wersje języka Python obsługiwane w programie Azure App Service za pomocą [az webapp list-runtimes:](/cli/azure/webapp#az_webapp_list_runtimes)
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Możesz uruchomić nieobsługiwaną wersję języka Python, kompilując w zamian własny obraz kontenera. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Dostosowywanie automatyzacji kompilacji

App Service systemu kompilacji, o nazwie Oryx, wykonuje następujące kroki podczas wdrażania aplikacji przy użyciu usługi Git lub pakietów zip:

1. Uruchom niestandardowy skrypt przed kompilacją, jeśli jest określony przez `PRE_BUILD_COMMAND` ustawienie . (Skrypt sam może uruchamiać inne skrypty języka Python Node.js skrypty, polecenia pip i npm oraz narzędzia oparte na węzłach, takie jak yarn, `yarn install` na przykład `yarn build` i ).

1. Uruchom polecenie `pip install -r requirements.txt`. Plik *requirements.txt* musi znajdować się w folderze głównym projektu. W przeciwnym razie proces kompilacji zgłasza błąd: "Nie można odnaleźć setup.py lub requirements.txt; Nie uruchamiaj instalacji pip".

1. Jeśli *manage.py* w katalogu głównym repozytorium (wskazującym aplikację Django), uruchom manage.py *collectstatic*. Jeśli jednak ustawienie `DISABLE_COLLECTSTATIC` ma ustawienie , ten krok zostanie `true` pominięty.

1. Uruchom niestandardowy skrypt po kompilacji, jeśli jest określony przez `POST_BUILD_COMMAND` ustawienie. (Ponownie skrypt może uruchamiać inne skrypty języka Python i Node.js skrypty, polecenia pip i npm oraz narzędzia oparte na węzłach).

Domyślnie ustawienia `PRE_BUILD_COMMAND` , `POST_BUILD_COMMAND` i są `DISABLE_COLLECTSTATIC` puste. 

- Aby wyłączyć uruchamianie kolekcji collectstatic podczas tworzenia aplikacji Django, `DISABLE_COLLECTSTATIC` ustaw ustawienie na wartość true.

- Aby uruchomić polecenia przed kompilacją, ustaw ustawienie tak, aby zawierało polecenie , takie jak , lub ścieżkę do pliku skryptu względem katalogu głównego `PRE_BUILD_COMMAND` `echo Pre-build command` projektu, takiego jak `scripts/prebuild.sh` . Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

- Aby uruchamiać polecenia po kompilacji, ustaw ustawienie tak, aby zawierało polecenie , takie jak , lub ścieżkę do pliku skryptu względem katalogu głównego `POST_BUILD_COMMAND` `echo Post-build command` projektu, takiego jak `scripts/postbuild.sh` . Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

Aby uzyskać dodatkowe ustawienia, które dostosują automatyzację kompilacji, zobacz [Oryx configuration (Konfiguracja narzędzia Oryx).](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md) 

Aby uzyskać dostęp do dzienników kompilacji i wdrażania, zobacz [Access deployment logs (Uzyskiwanie dostępu do dzienników wdrażania).](#access-deployment-logs)

Aby uzyskać więcej informacji na temat App Service i kompilowania aplikacji języka Python w systemie Linux, zobacz [How Oryx detects and builds Python apps](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md)(Jak oryx wykrywa i tworzy aplikacje w języku Python).

> [!NOTE]
> Ustawienia `PRE_BUILD_SCRIPT_PATH` i są identyczne z `POST_BUILD_SCRIPT_PATH` `PRE_BUILD_COMMAND` ustawieniami i i są obsługiwane w starszych `POST_BUILD_COMMAND` wersjach.
> 
> Ustawienie o nazwie , jeśli zawiera lub 1, wyzwala kompilację `SCM_DO_BUILD_DURING_DEPLOYMENT` `true` Oryx podczas wdrażania. To ustawienie ma wartość true w przypadku wdrażania przy użyciu narzędzia git, polecenia interfejsu wiersza polecenia platformy Azure `az webapp up` Visual Studio Code.

> [!NOTE]
> Zawsze używaj ścieżek względnych we wszystkich skryptach przed kompilacją i po kompilacji, ponieważ kontener kompilacji, w którym działa program Oryx, różni się od kontenera środowiska uruchomieniowego, w którym jest uruchamiana aplikacja. Nigdy nie polegaj na dokładnym umieszczeniu folderu projektu aplikacji w kontenerze (na przykład że znajduje się on w folderze *site/wwwroot).*

## <a name="migrate-existing-applications-to-azure"></a>Migrowanie istniejących aplikacji na platformę Azure

Istniejące aplikacje internetowe można ponownie wdać na platformie Azure w następujący sposób:

1. **Repozytorium źródłowe:** utrzymuj kod źródłowy w odpowiednim repozytorium, takim jak GitHub, co umożliwia skonfigurowanie ciągłego wdrażania w dalszej części tego procesu.
    1. Plik *requirements.txt* musi znajdować się w katalogu głównym repozytorium, aby program App Service automatycznie instalować niezbędne pakiety.    

1. **Baza** danych: jeśli aplikacja zależy od bazy danych, aprowizuj również niezbędne zasoby na platformie Azure. Zobacz [Samouczek: wdrażanie aplikacji internetowej Django za pomocą bazy danych PostgreSQL —](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure) na przykład utwórz bazę danych.

1. **Zasoby usługi App Service:** utwórz grupę zasobów, App Service plan i App Service internetową do hostowania aplikacji. Można to łatwo zrobić, wykonując początkowe wdrożenie kodu za pomocą polecenia interfejsu wiersza polecenia platformy Azure , jak pokazano w tece Samouczek: wdrażanie aplikacji internetowej Django za pomocą bazy danych `az webapp up` [PostgreSQL](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service)— wdrażanie kodu . Zastąp nazwy grupy zasobów, planu App Service i aplikacji internetowej, aby bardziej odpowiednie dla Twojej aplikacji.

1. **Zmienne środowiskowe:** jeśli aplikacja wymaga żadnych zmiennych środowiskowych, utwórz równoważne [App Service aplikacji.](configure-common.md#configure-app-settings) Te App Service są wyświetlane w kodzie jako zmienne środowiskowe zgodnie z opisem w te tematu Access environment variables (Uzyskiwanie dostępu [do zmiennych środowiskowych).](#access-app-settings-as-environment-variables)
    - Na przykład połączeniami z bazą danych często zarządza się za pomocą takich ustawień, jak pokazano w teście Samouczek: wdrażanie aplikacji internetowej Django za pomocą [programu PostgreSQL](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database)— konfigurowanie zmiennych w celu połączenia bazy danych .
    - Zobacz [Ustawienia produkcyjne dla aplikacji Django,](#production-settings-for-django-apps) aby uzyskać szczegółowe ustawienia dla typowych aplikacji Django.

1. **Uruchamianie aplikacji:** przejrzyj [](#container-startup-process) sekcję Proces uruchamiania kontenera w dalszej części tego artykułu, aby zrozumieć, App Service próbuje uruchomić aplikację. App Service domyślnie używa serwera internetowego Gunicorn, który musi być w stanie znaleźć obiekt aplikacji *lub wsgi.py* folderze. W razie potrzeby możesz dostosować [polecenie uruchamiania](#customize-startup-command).

1. **Ciągłe wdrażanie:** skonfiguruj ciągłe wdrażanie zgodnie z opisem w tesłudze Continuous [deployment to Azure App Service](deploy-continuous-deployment.md) if using Azure Pipelines or Kudu deployment (Ciągłe wdrażanie w usłudze Azure App Service lub Kudu) albo Deploy to App Service using GitHub Actions using GitHub Actions (Wdrażanie w usłudze App Service przy użyciu funkcji [GitHub Actions](./deploy-continuous-deployment.md) w przypadku korzystania z funkcji GitHub Actions).

1. **Akcje niestandardowe:** aby wykonać akcje w kontenerze App Service, który hostuje aplikację, takie jak migracje bazy danych Django, możesz nawiązać połączenie z kontenerem za [pośrednictwem połączenia SSH.](configure-linux-open-ssh-session.md) Aby uzyskać przykład uruchamiania migracji baz danych Django, zobacz [Tutorial: Deploy a Django web app with PostgreSQL - run database migrations](tutorial-python-postgresql-app.md#43-run-django-database-migrations)(Samouczek: wdrażanie aplikacji internetowej Django za pomocą bazy danych PostgreSQL — uruchamianie migracji bazy danych).
    - W przypadku korzystania z ciągłego wdrażania można wykonać te akcje przy użyciu poleceń po kompilacji, jak opisano wcześniej w obszarze [Dostosowywanie automatyzacji kompilacji.](#customize-build-automation)

Po ukończeniu tych kroków powinno być możliwe zatwierdzanie zmian w repozytorium źródłowym i automatyczne wdrażanie tych aktualizacji w App Service.

### <a name="production-settings-for-django-apps"></a>Ustawienia produkcyjne dla aplikacji Django

W przypadku środowiska produkcyjnego, takiego Azure App Service, aplikacje Django powinny być zgodne z listą kontrolną wdrażania [django](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) (djangoproject.com).

W poniższej tabeli opisano ustawienia produkcyjne, które są istotne dla platformy Azure. Te ustawienia są definiowane w *pliku* setting.py aplikacji.

| Ustawienie Django | Instrukcje dotyczące platformy Azure |
| --- | --- |
| `SECRET_KEY` | Przechowuj wartość w ustawieniach App Service zgodnie z opisem w tece Access app settings as environment variables (Uzyskiwanie dostępu do ustawień aplikacji [jako zmiennych środowiskowych).](#access-app-settings-as-environment-variables) Możesz też przechowywać [wartość jako "klucz tajny"](../key-vault/secrets/quick-create-python.md)w Azure Key Vault . |
| `DEBUG` | Utwórz ustawienie na App Service o wartości 0 (false), a następnie załaduj `DEBUG` wartość jako zmienną środowiskową. W środowisku projektowym utwórz `DEBUG` zmienną środowiskową o wartości 1 (true). |
| `ALLOWED_HOSTS` | W środowisku produkcyjnym django wymaga, aby adres URL aplikacji był uwzględniany `ALLOWED_HOSTS` w tablicy *settings.py*. Ten adres URL można pobrać w czasie wykonywania przy użyciu kodu `os.environ['WEBSITE_HOSTNAME']` . App Service automatycznie ustawia `WEBSITE_HOSTNAME` zmienną środowiskową na adres URL aplikacji. |
| `DATABASES` | Zdefiniuj ustawienia App Service dla połączenia z bazą danych i załaduj je jako zmienne środowiskowe w celu wypełnienia [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) słownika. Możesz też przechowywać wartości (szczególnie nazwę użytkownika i hasło) jako wpisy [Azure Key Vault tajne.](../key-vault/secrets/quick-create-python.md) |

## <a name="serve-static-files-for-django-apps"></a>Serve static files for Django apps (Obsługiwać pliki statyczne dla aplikacji Django)

Jeśli aplikacja internetowa Django zawiera statyczne pliki frontonia, najpierw postępuj zgodnie z instrukcjami dotyczącymi zarządzania plikami statycznych [w](https://docs.djangoproject.com/en/3.1/howto/static-files/) dokumentacji django.

Na App Service następnie wprowadzić następujące zmiany:

1. Rozważ użycie zmiennych środowiskowych (w przypadku tworzenia lokalnego) i ustawień aplikacji (podczas wdrażania w chmurze) w celu dynamicznego ustawienia django `STATIC_URL` `STATIC_ROOT` i zmiennych. Na przykład:    

    ```python
    STATIC_URL = os.environ.get("DJANGO_STATIC_URL", "/static/")
    STATIC_ROOT = os.environ.get("DJANGO_STATIC_ROOT", "./static/")    
    ```

    `DJANGO_STATIC_URL` I `DJANGO_STATIC_ROOT` można zmieniać w razie potrzeby w środowiskach lokalnych i w chmurze. Jeśli na przykład proces kompilacji dla plików statycznych umieszcza je w folderze o nazwie , można ustawić wartość , aby `django-static` `DJANGO_STATIC_URL` uniknąć `/django-static/` używania wartości domyślnej.

1. Jeśli masz skrypt przed kompilacją, który generuje pliki statyczne w innym folderze, dołącz ten folder do zmiennej Django, aby proces `STATICFILES_DIRS` Django `collectstatic` je znalazł. Jeśli na przykład uruchamiasz plik `yarn build` w folderze frontonia, a yarn wygeneruje folder zawierający pliki statyczne, dołącz `build/static` ten folder w następujący sposób:

    ```python
    FRONTEND_DIR = "path-to-frontend-folder" 
    STATICFILES_DIRS = [os.path.join(FRONTEND_DIR, 'build', 'static')]    
    ```

    W tym `FRONTEND_DIR` miejscu , aby utworzyć ścieżkę do miejsca, w którym jest uruchamiane narzędzie do kompilacji, takie jak yarn. Możesz ponownie użyć zmiennej środowiskowej i ustawienia aplikacji zgodnie z potrzebami.

1. Dodaj `whitenoise` plik *requirements.txt* pliku. [Whitenoise](http://whitenoise.evans.io/en/stable/) (whitenoise.evans.io) to pakiet języka Python, który ułatwia produkcyjnej aplikacji Django obsługa własnych plików statycznych. Whitenoise w szczególności obsługuje pliki, które znajdują się w folderze określonym przez zmienną `STATIC_ROOT` Django.

1. W pliku *settings.py* dodaj następujący wiersz dla whitenoise:

    ```python
    STATICFILES_STORAGE = ('whitenoise.storage.CompressedManifestStaticFilesStorage')
    ```

1. Zmodyfikuj `MIDDLEWARE` również listy i , aby uwzględnić `INSTALLED_APPS` whitenoise:

    ```python
    MIDDLEWARE = [                                                                   
        'django.middleware.security.SecurityMiddleware',
        # Add whitenoise middleware after the security middleware                             
        'whitenoise.middleware.WhiteNoiseMiddleware',
        # Other values follow
    ]

    INSTALLED_APPS = [
        "whitenoise.runserver_nostatic",
        # Other values follow
    ]
    ```

## <a name="container-characteristics"></a>Właściwości kontenera

Po wdrożeniu w usłudze App Service python aplikacje działają w kontenerze platformy Docker systemu Linux zdefiniowanym w [repozytorium GitHub](https://github.com/Azure-App-Service/python)języka Python App Service python. Konfiguracje obrazów można znaleźć w katalogach specyficznych dla wersji.

Ten kontener ma następujące cechy:

- Aplikacje są uruchamiane przy użyciu [serwera HTTP Gunicorn WSGI](https://gunicorn.org/) z dodatkowymi argumentami `--bind=0.0.0.0 --timeout 600`.
    - Ustawienia konfiguracji serwera Gunicorn można podać za pośrednictwem *pliku gunicorn.conf.py* w katalogu głównym projektu, zgodnie z opisem w tece Omówienie konfiguracji serwera [Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org). Możesz też dostosować [polecenie uruchamiania](#customize-startup-command).

    - Aby chronić aplikację internetową przed przypadkowymi lub celowym atakami DDOS, serwer Gunicorn jest uruchamiany za zwrotnym serwerem proxy serwera Nginx zgodnie z opisem w temacie Wdrażanie serwera [Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org).

- Domyślnie podstawowy obraz kontenera zawiera tylko platformę internetową Flask, ale kontener obsługuje inne struktury zgodne ze standardem WSGI i zgodne z platformą Python 3.6+, np. Django.

- Aby zainstalować dodatkowe pakiety, takie jak Django,requirements.txt [*pliku*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) w katalogu głównym projektu, który określa bezpośrednie zależności. App Service następnie automatycznie zainstaluje te zależności podczas wdrażania projektu.

    Plik *requirements.txt* musi *znajdować* się w katalogu głównym projektu, aby można było zainstalować zależności. W przeciwnym razie proces kompilacji zgłasza błąd: "Nie można odnaleźć setup.py lub requirements.txt; Nie uruchamiaj instalacji pip". Jeśli wystąpi ten błąd, sprawdź lokalizację pliku wymagań.

- App Service automatycznie definiuje zmienną środowiskową o nazwie z adresem URL aplikacji `WEBSITE_HOSTNAME` internetowej, na przykład `msdocs-hello-world.azurewebsites.net` . Definiuje również `WEBSITE_SITE_NAME` nazwę aplikacji, taką jak `msdocs-hello-world` . 
   
- Narzędzia npm i Node.js są zainstalowane w kontenerze, aby można było uruchamiać narzędzia kompilacji oparte na węźle, takie jak yarn.

## <a name="container-startup-process"></a>Proces uruchamiania kontenera

Podczas uruchamiania kontener usługi App Service w systemie Linux wykonuje następujące kroki:

1. Użyj [niestandardowego polecenia uruchomienia](#customize-startup-command), jeśli zostało udostępnione.
2. Sprawdź istnienie [aplikacji Django](#django-app), a następnie uruchom dla niej serwer Gunicorn, jeśli zostanie wykryta.
3. Sprawdź istnienie [aplikacji Flask](#flask-app), a następnie uruchom dla niej serwer Gunicorn, jeśli zostanie wykryta.
4. Jeśli nie została znaleziona żadna inna aplikacja, uruchomienie domyślnej aplikacji wbudowanej w kontener.

Poniższe sekcje zawierają dodatkowe szczegóły dla każdej z tych opcji.

### <a name="django-app"></a>Aplikacja platformy Django

W przypadku aplikacji Django usługa App Service szuka pliku o nazwie `wsgi.py` w kodzie aplikacji, a następnie uruchamia serwer Gunicorn przy użyciu następującego polecenia:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Jeśli chcesz mieć bardziej konkretną kontrolę nad poleceniem uruchamiania, użyj niestandardowego polecenia [uruchamiania,](#customize-startup-command)zastąp nazwą folderu zawierającego wsgi.py i dodaj argument, jeśli ten moduł nie znajduje się w katalogu głównym `<module>`  `--chdir` projektu. Jeśli na przykład plik *wsgi.py* w katalogu głównym projektu w obszarze *knboard/backend/config,* użyj argumentów `--chdir knboard/backend config.wsgi` .

Aby włączyć rejestrowanie w środowisku produkcyjnym, dodaj parametry i , jak pokazano w `--access-logfile` `--error-logfile` przykładach dla [niestandardowych poleceń uruchamiania](#customize-startup-command).

### <a name="flask-app"></a>Aplikacja Flask

W przypadku aplikacji Flask usługa App Service szuka pliku o nazwie *application.py* lub *app.py* i uruchamia serwer Gunicorn w następujący sposób:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Jeśli główny moduł aplikacji znajduje się w innym pliku, użyj innej nazwy dla obiektu aplikacji lub chcesz podać dodatkowe argumenty dla serwera Gunicorn, użyj niestandardowego [polecenia uruchamiania](#customize-startup-command).

### <a name="default-behavior"></a>Zachowanie domyślne

Jeśli aplikacja App Service nie znajdzie polecenia niestandardowego, aplikacji Django ani aplikacji Flask, uruchomi domyślną aplikację tylko do odczytu znajdującą się w folderze _opt/defaultsite_ i pokazaną na poniższej ilustracji.

Jeśli wdrożono kod i nadal jest wyświetlana aplikacja domyślna, zobacz Rozwiązywanie problemów [— aplikacja nie jest wyświetlana.](#app-doesnt-appear)

[![Domyślna strona internetowa usługi App Service w systemie Linux](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Jeśli spodziewasz się, że wdrożona aplikacja będzie wyświetlana zamiast aplikacji domyślnej, zobacz Rozwiązywanie problemów [— aplikacja nie jest wyświetlana.](#app-doesnt-appear)

## <a name="customize-startup-command"></a>Dostosowywanie polecenia uruchamiania

Jak wspomniano wcześniej w tym artykule, ustawienia konfiguracji serwera Gunicorn można podać za pośrednictwem pliku *gunicorn.conf.py* w katalogu głównym projektu, zgodnie z opisem w artykule Omówienie konfiguracji serwera [Gunicorn.](https://docs.gunicorn.org/en/stable/configure.html#configuration-file)

Jeśli taka konfiguracja nie jest wystarczająca, możesz kontrolować zachowanie podczas uruchamiania kontenera, podając niestandardowe polecenie uruchamiania lub wiele poleceń w pliku polecenia uruchamiania. Plik polecenia uruchamiania może używać dowolnej nazwy, takiej jak *startup.sh*, *startup.cmd,* *startup.txt* i tak dalej.

Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

Aby określić polecenie uruchamiania lub plik polecenia:

- **Azure Portal:** wybierz stronę Konfiguracja  aplikacji, a następnie wybierz **pozycję Ustawienia ogólne.** W polu **Polecenie uruchamiania** umieść pełny tekst polecenia uruchamiania lub nazwę pliku polecenia uruchamiania. Następnie wybierz **pozycję Zapisz,** aby zastosować zmiany. Zobacz [Konfigurowanie ustawień ogólnych dla](configure-common.md#configure-general-settings) kontenerów systemu Linux.

- **Interfejs wiersza polecenia** platformy Azure: użyj [polecenia az webapp config set z](/cli/azure/webapp/config#az_webapp_config_set) parametrem , aby ustawić polecenie lub plik `--startup-file` uruchamiania:

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Zastąp tekst pełnym tekstem polecenia uruchamiania lub `<custom-command>` nazwą pliku polecenia uruchamiania.
        
App Service ignoruje wszelkie błędy występujące podczas przetwarzania niestandardowego polecenia uruchamiania lub pliku, a następnie kontynuuje proces uruchamiania, szukając aplikacji Django i Flask. Jeśli nie widzisz spodziewanych zachowań, sprawdź, czy polecenie uruchamiania lub plik jest wolny od błędów i czy plik polecenia uruchamiania jest wdrożony w u App Service wraz z kodem aplikacji. Możesz również sprawdzić dzienniki [diagnostyczne, aby](#access-diagnostic-logs) uzyskać dodatkowe informacje. Sprawdź również stronę **Diagnozowanie** i rozwiązywanie problemów aplikacji na [stronie Azure Portal.](https://portal.azure.com)

### <a name="example-startup-commands"></a>Przykładowe polecenia uruchamiania

- **Dodano argumenty gunicorn:** W poniższym przykładzie dodano polecenie do wiersza polecenia `--workers=4` Gunicorn w celu uruchamiania aplikacji Django: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Aby uzyskać więcej informacji, zobacz [Running Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (Uruchamianie serwera Gunicorn) (docs.gunicorn.org).

- **Włącz rejestrowanie produkcyjne dla django:** dodaj `--access-logfile '-'` argumenty i `--error-logfile '-'` do wiersza polecenia:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Te dzienniki będą wyświetlane w [strumieniu App Service dziennika.](#access-diagnostic-logs)

    Aby uzyskać więcej informacji, zobacz [Rejestrowanie w gunicorn](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Moduł główny niestandardowej** aplikacji Flask: domyślnie App Service, że główny moduł  aplikacji Flask jest application.py lub *app.py*. Jeśli moduł główny używa innej nazwy, należy dostosować polecenie uruchamiania. Na przykład masz aplikację Flask, której głównym modułem jest *hello.py,* a obiekt aplikacji Flask w tym pliku ma nazwę , a następnie polecenie `myapp` jest następujące:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Jeśli moduł główny znajduje się w podfolderze, takim jak `website`, określ ten folder za pomocą argumentu `--chdir`:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- Użyj serwera innego niż **Gunicorn:** aby użyć innego serwera internetowego, takiego jak [aiohttp,](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)użyj odpowiedniego polecenia jako polecenia uruchamiania lub w pliku polecenia uruchamiania:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Uzyskiwanie dostępu do ustawień aplikacji jako zmiennych środowiskowych

Ustawienia aplikacji to wartości przechowywane w chmurze przeznaczone specjalnie dla Twojej aplikacji, zgodnie z opisem w [te tematu Configure app settings (Konfigurowanie ustawień aplikacji).](configure-common.md#configure-app-settings) Te ustawienia są dostępne dla kodu aplikacji jako zmienne środowiskowe i dostępne przy użyciu standardowego wzorca [os.environ.](https://docs.python.org/3/library/os.html#os.environ)

Jeśli na przykład utworzono ustawienie aplikacji o nazwie , następujący kod pobiera `DATABASE_SERVER` wartość tego ustawienia:

```python
db_server = os.environ['DATABASE_SERVER']
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W App Service ssl [termination](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) odbywa się w usługach równoważenia obciążenia sieciowego, więc wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. Na platformie [CodeIgniter](https://codeigniter.com/) element [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) domyślnie sprawdza wartość `X_FORWARDED_PROTO`.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Aby uzyskać dostęp do dzienników za pośrednictwem Azure Portal, wybierz **pozycję Monitorowanie**  >  **strumienia dzienników** w menu po lewej stronie aplikacji.

## <a name="access-deployment-logs"></a>Uzyskiwanie dostępu do dzienników wdrażania

Podczas wdrażania kodu program App Service proces kompilacji opisany wcześniej w sekcji [Dostosowywanie automatyzacji kompilacji.](#customize-build-automation) Ponieważ kompilacja działa we własnym kontenerze, dzienniki kompilacji są przechowywane niezależnie od dzienników diagnostycznych aplikacji.

Aby uzyskać dostęp do dzienników wdrażania, należy wykonać następujące czynności:

1. Na stronie Azure Portal aplikacji internetowej wybierz pozycję **Centrum** wdrażania  >  **(wersja zapoznawcza)** w menu po lewej stronie.
1. Na karcie **Dzienniki** wybierz identyfikator **zatwierdzenia** dla ostatniego zatwierdzenia.
1. Na **wyświetlonej stronie Szczegóły** dziennika wybierz link **Pokaż dzienniki...** wyświetlany obok opcji "Uruchomiona kompilacja oryx...".

W tych dziennikach będą wyświetlane problemy z *kompilacją,* takie jak nieprawidłowe zależności wrequirements.txti błędy skryptów przed kompilacją lub po kompilacji. Błędy są również wyświetlane, jeśli plik wymagań nie *ma* dokładnierequirements.txtlub nie pojawia się w folderze głównym projektu.

## <a name="open-ssh-session-in-browser"></a>Otwieranie sesji SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

Po pomyślnym nawiązaniu połączenia z sesją SSH w dolnej części okna powinien zostać wyświetlony komunikat "NAWIĄZYWANO POŁĄCZENIE SSH". Jeśli wystąpią błędy, takie jak "SSH_CONNECTION_CLOSED" lub komunikat o ponownym uruchomieniu kontenera, może wystąpić błąd uniemożliwiający uruchomienie kontenera aplikacji. Zobacz [Rozwiązywanie problemów,](#troubleshooting) aby uzyskać instrukcje badania możliwych problemów.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ogólnie rzecz biorąc, pierwszym krokiem rozwiązywania problemów jest użycie App Service diagnostyki:

1. Na Azure Portal aplikacji internetowej wybierz pozycję Diagnozowanie i **rozwiązywanie problemów** z menu po lewej stronie.
1. Wybierz **pozycję Dostępność i wydajność.**
1. Sprawdź informacje w opcjach **Dzienniki aplikacji,**  **Awaria kontenera** i Problemy z kontenerem, gdzie pojawią się najbardziej typowe problemy.

Następnie sprawdź, czy w [dziennikach wdrażania](#access-deployment-logs) i [w dziennikach aplikacji](#access-diagnostic-logs) nie ma żadnych komunikatów o błędach. Te dzienniki często identyfikują konkretne problemy, które mogą uniemożliwić wdrożenie aplikacji lub uruchomienie aplikacji. Na przykład kompilacja może się nie *powieść,requirements.txt* plik ma nieprawidłową nazwę pliku lub nie znajduje się w folderze głównym projektu.

Poniższe sekcje zawierają dodatkowe wskazówki dotyczące konkretnych problemów.

- [Aplikacja nie jest wyświetlana — jest wyświetlana aplikacja domyślna](#app-doesnt-appear)
- [Aplikacja nie jest wyświetlana — komunikat "usługa niedostępna"](#service-unavailable)
- [Nie można odnaleźć setup.py lub requirements.txt](#could-not-find-setuppy-or-requirementstxt)
- [ModuleNotFoundError podczas uruchamiania](#modulenotfounderror-when-app-starts)
- [Baza danych jest zablokowana](#database-is-locked)
- [Hasła nie są wyświetlane w sesji SSH po wpisaniu](#other-issues)
- [Polecenia w sesji SSH wydają się być odcięte](#other-issues)
- [Zasoby statyczne nie są wyświetlane w aplikacji Django](#other-issues)
- [Wymagane jest krytyczne połączenie SSL](#other-issues)

#### <a name="app-doesnt-appear"></a>Aplikacja nie jest wyświetlana

- **Po wdrożeniu własnego kodu aplikacji wyświetlana jest aplikacja domyślna.** Aplikacja [domyślna jest](#default-behavior) wyświetlana, ponieważ nie wdrożono kodu aplikacji w u App Service lub App Service nie można odnaleźć kodu aplikacji i zamiast tego uruchomiono aplikację domyślną.

    - Uruchom ponownie usługę App Service, poczekaj 15–20 sekund i sprawdź ponownie aplikację.
    
    - Upewnij się, że używasz usługi App Service dla systemu Linux, a nie wystąpienia opartego na systemie Windows. W interfejsie wiersza polecenia platformy Azure uruchom polecenie `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind`, zastępując zmienne `<resource-group-name>` i `<app-name>` odpowiednimi wartościami. Powinny zostać wyświetlone dane wyjściowe `app,linux`. W przeciwnym razie ponownie utwórz usługę App Service i wybierz system Linux.
    
    - Użyj [połączenia SSH,](#open-ssh-session-in-browser) aby połączyć się bezpośrednio z kontenerem App Service i sprawdzić, czy pliki istnieją w *folderze site/wwwroot.* Jeśli pliki nie istnieją, użyj następujących kroków:
      1. Utwórz ustawienie aplikacji o nazwie o wartości 1, ponownie wdychuj kod, odczekaj kilka minut, a następnie spróbuj ponownie uzyskać `SCM_DO_BUILD_DURING_DEPLOYMENT` dostęp do aplikacji. Aby uzyskać więcej informacji na temat tworzenia ustawień aplikacji, zobacz [Konfigurowanie App Service aplikacji w Azure Portal](configure-common.md).
      1. Przejrzyj proces wdrażania, [sprawdź dzienniki wdrażania,](#access-deployment-logs)popraw wszelkie błędy i ponownie wdaj aplikację.
    
    - Jeśli pliki istnieją, oznacza to, że usługa App Service nie mogła zidentyfikować określonego pliku startowego. Sprawdź, czy aplikacja ma strukturę zgodnie [](#django-app) z oczekiwaniami App Service django lub [Flask,](#flask-app)lub użyj [niestandardowego polecenia uruchamiania](#customize-startup-command).

- <a name="service-unavailable"></a>**W przeglądarce zostanie wyświetlony komunikat "Usługa niedostępna".** Uchylił czas oczekiwania na odpowiedź z usługi App Service, co oznacza, że App Service serwera Gunicorn, ale sama aplikacja nie została uruchomiona. Ten warunek może wskazywać, że argumenty serwera Gunicorn są nieprawidłowe lub że w kodzie aplikacji wystąpił błąd.

    - Odśwież okno przeglądarki, zwłaszcza jeśli korzystasz z niższych warstw cenowych w planie usługi App Service. Na przykład podczas korzystania z warstw bezpłatnych aplikacja może być uruchamiana dłużej i zacznie odpowiadać po odświeżeniu okna przeglądarki.

    - Sprawdź, czy aplikacja ma strukturę zgodnie [](#django-app) z oczekiwaniami App Service django lub [Flask,](#flask-app)lub użyj [niestandardowego polecenia uruchamiania](#customize-startup-command).

    - Sprawdź, [czy w strumieniu dziennika aplikacji](#access-diagnostic-logs) nie ma żadnych komunikatów o błędach. Dzienniki będą pokazywać wszelkie błędy w kodzie aplikacji.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>Nie można odnaleźć setup.py lub requirements.txt

- **Strumień dziennika zawiera informacje "Nie można odnaleźć setup.py lub requirements.txt; Nie uruchamiaj instalacji pip."**: Proces kompilacji Oryx nie może odnaleźć *requirements.txt* pliku.

    - Połącz się z kontenerem aplikacji internetowej za pośrednictwem [połączenia SSH](#open-ssh-session-in-browser) i sprawdź, *czy* nazwarequirements.txtjest poprawnie i istnieje bezpośrednio w obszarze *site/wwwroot.* Jeśli nie istnieje, upewnij się, że lokacja pliku istnieje w repozytorium i jest uwzględniona we wdrożeniu. Jeśli istnieje w oddzielnym folderze, przenieś go do katalogu głównego.

#### <a name="modulenotfounderror-when-app-starts"></a>ModuleNotFoundError podczas uruchamiania aplikacji

Jeśli zostanie wyświetlony błąd, taki jak , oznacza to, że język Python nie może znaleźć co najmniej jednego z modułów `ModuleNotFoundError: No module named 'example'` podczas rozpoczynania pracy aplikacji. Dzieje się tak najczęściej w przypadku wdrażania środowiska wirtualnego przy użyciu kodu. Środowiska wirtualne nie są przenośne, dlatego nie należy wdrażać środowiska wirtualnego przy użyciu kodu aplikacji. Zamiast tego pozwól programowi Oryx utworzyć środowisko wirtualne i zainstalować pakiety w aplikacji internetowej, tworząc ustawienie aplikacji `SCM_DO_BUILD_DURING_DEPLOYMENT` i ustawiając je na `1` . Spowoduje to wymusze zainstalowanie pakietów w programie Oryx przy każdym wdrożeniu w App Service. Aby uzyskać więcej informacji, zobacz [ten artykuł na temat przenośności środowiska wirtualnego.](https://azure.github.io/AppService/2020/12/11/cicd-for-python-apps.html)

### <a name="database-is-locked"></a>Baza danych jest zablokowana

Podczas próby uruchomienia migracji bazy danych za pomocą aplikacji Django może zostać wyświetlony błąd "sqlite3. OperationalError: baza danych jest zablokowana". Błąd wskazuje, że aplikacja używa bazy danych SQLite, dla której platforma Django jest domyślnie skonfigurowana, a nie bazy danych w chmurze, takiej jak PostgreSQL dla platformy Azure.

Sprawdź zmienną w pliku settings.py aplikacji, aby upewnić się, że aplikacja używa bazy danych w chmurze zamiast `DATABASES` sqlite. 

Jeśli wystąpi ten błąd w przykładzie z samouczka: wdrażanie aplikacji internetowej Django za pomocą bazy danych [PostgreSQL,](tutorial-python-postgresql-app.md)sprawdź, czy zostały wykonane kroki opisane w teście Konfigurowanie zmiennych środowiskowych w celu połączenia z bazą [danych](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database).

#### <a name="other-issues"></a>Inne problemy

- **Hasła nie są wyświetlane w** sesji SSH po wpisaniu : ze względów bezpieczeństwa sesja SSH przechowuje hasło ukryte podczas wpisywania. Znaki są jednak rejestrowane, więc wpisz hasło w zwykły sposób i naciśnij klawisz **Enter,** gdy wszystko będzie gotowe.

- **Polecenia w sesji SSH** wydają się być odcięte: edytor może nie być poleceniami zawijania wyrazów, ale nadal powinny działać prawidłowo.

- **Zasoby statyczne nie są wyświetlane w aplikacji Django:** Upewnij się, że włączono [moduł whitenoise](http://whitenoise.evans.io/en/stable/django.html)

- **Zostanie wyświetlony komunikat "Krytyczne** połączenie SSL jest wymagane": Sprawdź nazwy użytkowników i hasła używane do uzyskiwania dostępu do zasobów (takich jak bazy danych) z poziomu aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja w języku Python z postgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie z prywatnego repozytorium kontenerów](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](faq-app-service-linux.md)
