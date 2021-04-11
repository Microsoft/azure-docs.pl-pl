---
title: Konfigurowanie aplikacji systemu Linux Python
description: Informacje o konfigurowaniu kontenera języka Python, w którym są uruchamiane aplikacje sieci Web, przy użyciu zarówno Azure Portal, jak i interfejsu wiersza polecenia platformy Azure.
ms.topic: quickstart
ms.date: 03/16/2021
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 094755ed6c018b3ac82d6f62a43f17e2536bbd9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953514"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurowanie aplikacji systemu Linux w języku Python dla Azure App Service

W tym artykule opisano, jak [Azure App Service](overview.md) są uruchamiane aplikacje Python, jak można migrować istniejące aplikacje na platformę Azure oraz jak dostosować zachowanie App Service, gdy jest to konieczne. Aplikacje języka Python muszą zostać wdrożone ze wszystkimi wymaganymi modułami [PIP](https://pypi.org/project/pip/) .

Aparat wdrażania App Service automatycznie aktywuje środowisko wirtualne i jest uruchamiany `pip install -r requirements.txt` podczas wdrażania [repozytorium git](deploy-local-git.md)lub [pakietu zip](deploy-zip.md).

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka Python, którzy używają wbudowanego kontenera systemu Linux w App Service. Jeśli nigdy nie korzystasz z Azure App Service, najpierw postępuj zgodnie z samouczkiem [przewodnika Szybki Start](quickstart-python.md) i [Python z PostgreSQL](tutorial-python-postgresql-app.md).

Do konfiguracji można użyć [Azure Portal](https://portal.azure.com) lub interfejsu wiersza polecenia platformy Azure:

- **Azure Portal** Użyj   >  strony **konfiguracji** ustawienia aplikacji zgodnie z opisem w temacie [Konfigurowanie aplikacji App Service w Azure Portal](configure-common.md).

- **Interfejs wiersza polecenia platformy Azure**: masz dwie opcje.

    - Uruchom polecenia w [Azure Cloud Shell](../cloud-shell/overview.md).
    - Uruchom polecenia lokalnie, instalując najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), a następnie zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login).
    
> [!NOTE]
> System Linux jest obecnie zalecaną opcją uruchamiania aplikacji w języku Python w App Service. Aby uzyskać informacje na temat opcji systemu Windows, zobacz [Python w systemie windows App Service](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Konfigurowanie wersji języka Python

- **Azure Portal**: Użyj karty **Ustawienia ogólne** na stronie **Konfiguracja** , zgodnie z opisem w temacie [Konfigurowanie ustawień ogólnych](configure-common.md#configure-general-settings) dla kontenerów systemu Linux.

- **Interfejs wiersza polecenia platformy Azure**:

    -  Pokaż bieżącą wersję języka Python za pomocą [AZ webapp config show](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Zamień `<resource-group-name>` i na `<app-name>` nazwy odpowiednie dla aplikacji sieci Web.
    
    - Ustaw wersję języka Python za pomocą [AZ webapp config Set](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Pokaż wszystkie wersje języka Python, które są obsługiwane w Azure App Service za pomocą [AZ webapp list-Runtimes](/cli/azure/webapp#az_webapp_list_runtimes):
    
        ```azurecli
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Możesz uruchomić nieobsługiwaną wersję języka Python, kompilując w zamian własny obraz kontenera. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Dostosuj automatyzację kompilacji

System kompilacji App Service o nazwie Oryx, podczas wdrażania aplikacji przy użyciu pakietów Git lub zip wykonuje następujące czynności:

1. Uruchom niestandardowy skrypt przed kompilacją, jeśli został określony przez `PRE_BUILD_COMMAND` ustawienie. (Skrypt może sama uruchamiać inne skrypty języka Python i Node.js, polecenia PIP i npm oraz narzędzia oparte na węzłach, takie jak przędza, na przykład `yarn install` i `yarn build` .)

1. Uruchom polecenie `pip install -r requirements.txt`. Plik *requirements.txt* musi znajdować się w folderze głównym projektu. W przeciwnym razie proces kompilacji zgłosi błąd: "nie można znaleźć setup.py lub requirements.txt; Nie uruchomiono instalacji PIP ".

1. Jeśli *manage.py* znajduje się w katalogu głównym repozytorium (wskazującym aplikację Django), uruchom *manage.py collectstatic*. Jeśli jednak `DISABLE_COLLECTSTATIC` ustawienie ma wartość `true` , ten krok zostanie pominięty.

1. Uruchom niestandardowy skrypt po kompilacji, jeśli został określony przez `POST_BUILD_COMMAND` ustawienie. (Ponownie skrypt może uruchamiać inne skrypty języka Python i Node.js, polecenia PIP i npm oraz narzędzia oparte na węzłach).

Domyślnie `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` Ustawienia, i `DISABLE_COLLECTSTATIC` są puste. 

- Aby wyłączyć uruchamianie collectstatic podczas kompilowania aplikacji Django, ustaw `DISABLE_COLLECTSTATIC` dla ustawienia wartość true.

- Aby uruchomić polecenia przed kompilacją, należy ustawić `PRE_BUILD_COMMAND` opcję, która będzie zawierać polecenie, takie jak `echo Pre-build command` , lub ścieżkę do pliku skryptu względem katalogu głównego projektu, na przykład `scripts/prebuild.sh` . Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

- Aby uruchomić polecenia po kompilacji, należy ustawić `POST_BUILD_COMMAND` opcję, która będzie zawierać polecenie, takie jak `echo Post-build command` , lub ścieżkę do pliku skryptu względem katalogu głównego projektu, na przykład `scripts/postbuild.sh` . Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

Aby uzyskać dodatkowe ustawienia, które dostosowują automatyzację kompilacji, zobacz [Konfiguracja Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

Aby uzyskać dostęp do dzienników kompilacji i wdrażania, zobacz [dzienniki wdrażania programu Access](#access-deployment-logs).

Aby uzyskać więcej informacji na temat sposobu uruchamiania App Service i tworzenia aplikacji w języku Python w systemie Linux, zobacz [jak Oryx wykrywa i tworzy aplikacje języka Python](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

> [!NOTE]
> `PRE_BUILD_SCRIPT_PATH`Ustawienia i `POST_BUILD_SCRIPT_PATH` są takie same jak `PRE_BUILD_COMMAND` i `POST_BUILD_COMMAND` i są obsługiwane w starszych celach.
> 
> Ustawienie o nazwie `SCM_DO_BUILD_DURING_DEPLOYMENT` , jeśli zawiera `true` lub 1, wyzwala kompilację Oryx podczas wdrażania. Ustawienie ma wartość true w przypadku wdrażania przy użyciu narzędzia Git, polecenia interfejsu CLI platformy Azure `az webapp up` i Visual Studio Code.

> [!NOTE]
> Zawsze używaj ścieżek względnych we wszystkich skryptach przed i po kompilacji, ponieważ kontener kompilacji, w którym działa Oryx, różni się od kontenera środowiska uruchomieniowego, w którym działa aplikacja. Nigdy nie należy polegać na dokładnym umieszczeniu folderu projektu aplikacji w kontenerze (na przykład, że znajduje się on w obszarze *site/wwwroot*).

## <a name="migrate-existing-applications-to-azure"></a>Migrowanie istniejących aplikacji na platformę Azure

Istniejące aplikacje sieci Web można ponownie wdrożyć na platformie Azure w następujący sposób:

1. **Repozytorium źródłowe**: utrzymuje kod źródłowy w odpowiednim repozytorium, takim jak GitHub, co umożliwia skonfigurowanie ciągłego wdrażania w dalszej części tego procesu.
    1. Plik *requirements.txt* musi znajdować się w katalogu głównym repozytorium, aby App Service automatycznie instalować wymagane pakiety.    

1. **Baza danych**: Jeśli aplikacja jest zależna od bazy danych, należy również udostępnić wymagane zasoby na platformie Azure. Zobacz [Samouczek: wdrażanie aplikacji sieci Web Django za pomocą PostgreSQL — Tworzenie bazy danych](tutorial-python-postgresql-app.md#3-create-postgres-database-in-azure) na przykład.

1. **Zasoby usługi App Service**: Utwórz grupę zasobów, plan App Service i App Service aplikację sieci Web do hostowania aplikacji. Można to zrobić, wykonując początkowe wdrożenie kodu za pomocą polecenia platformy Azure `az webapp up` , jak pokazano na [samouczku: wdrażanie aplikacji sieci Web Django za pomocą PostgreSQL — Wdróż kod](tutorial-python-postgresql-app.md#4-deploy-the-code-to-azure-app-service). Zastąp nazwy grupy zasobów, planu App Service i aplikacji sieci Web, aby były bardziej odpowiednie dla aplikacji.

1. **Zmienne środowiskowe**: Jeśli aplikacja wymaga żadnych zmiennych środowiskowych, Utwórz równoważne [Ustawienia aplikacji App Service](configure-common.md#configure-app-settings). Te ustawienia App Service są wyświetlane jako zmienne środowiskowe, zgodnie z opisem w temacie [zmienne środowiskowe dostępu](#access-app-settings-as-environment-variables).
    - Połączenia z bazą danych, na przykład, są często zarządzane za pomocą takich ustawień, jak pokazano w [samouczku: wdrażanie aplikacji sieci Web Django za pomocą PostgreSQL — Konfiguruj zmienne w celu połączenia bazy danych](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database).
    - Zobacz [Ustawienia produkcyjne dla aplikacji Django](#production-settings-for-django-apps) dla określonych ustawień typowych aplikacji Django.

1. **Uruchamianie aplikacji**: Zapoznaj się z sekcją [proces uruchamiania kontenera](#container-startup-process) w dalszej części tego artykułu, aby dowiedzieć się, jak App Service próbuje uruchomić aplikację. App Service domyślnie używa serwera sieci Web Gunicorn, który musi być w stanie znaleźć obiekt aplikacji lub folder *WSGI.py* . W razie konieczności można [dostosować polecenie uruchamiania](#customize-startup-command).

1. **Ciągłe wdrażanie**: Skonfiguruj ciągłe wdrażanie, zgodnie z opisem w temacie [ciągłe wdrażanie, aby Azure App Service](deploy-continuous-deployment.md) w przypadku używania Azure Pipelines lub wdrożenia kudu, lub [Wdróż w App Service przy użyciu akcji](./deploy-continuous-deployment.md) GitHub w przypadku korzystania z akcji usługi GitHub.

1. **Akcje niestandardowe**: Aby wykonać akcje w kontenerze App Service, który hostuje aplikację, taką jak migracje bazy danych Django, można połączyć się z [kontenerem za pośrednictwem protokołu SSH](configure-linux-open-ssh-session.md). Aby zapoznać się z przykładem uruchamiania migracji bazy danych Django, zobacz [Samouczek: wdrażanie aplikacji sieci Web Django z PostgreSQL-Run Migration Database](tutorial-python-postgresql-app.md#43-run-django-database-migrations).
    - W przypadku korzystania z ciągłego wdrażania można wykonać te akcje przy użyciu poleceń po kompilacji, jak opisano wcześniej w temacie [Dostosowywanie automatyzacji kompilacji](#customize-build-automation).

Po wykonaniu tych kroków należy mieć możliwość zatwierdzania zmian w repozytorium źródłowym i automatycznego wdrażania tych aktualizacji do App Service.

### <a name="production-settings-for-django-apps"></a>Ustawienia produkcyjne dla aplikacji Django

Dla środowiska produkcyjnego, takiego jak Azure App Service, aplikacje Django powinny postępować zgodnie z [listą kontrolną wdrożenia](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) Django (djangoproject.com).

W poniższej tabeli opisano ustawienia produkcyjne odpowiednie dla platformy Azure. Te ustawienia są zdefiniowane w pliku *Setting.py* aplikacji.

| Ustawienie Django | Instrukcje dotyczące platformy Azure |
| --- | --- |
| `SECRET_KEY` | Zapisz wartość w ustawieniu App Service zgodnie z opisem w temacie [dostęp do ustawień aplikacji jako zmiennych środowiskowych](#access-app-settings-as-environment-variables). Możesz również [przechowywać wartości jako "tajne" w Azure Key Vault](../key-vault/secrets/quick-create-python.md). |
| `DEBUG` | Utwórz `DEBUG` ustawienie na App Service z wartością 0 (false), a następnie załaduj wartość jako zmienną środowiskową. W środowisku deweloperskim Utwórz `DEBUG` zmienną środowiskową o wartości 1 (true). |
| `ALLOWED_HOSTS` | W środowisku produkcyjnym Django wymaga dołączenia adresu URL aplikacji w `ALLOWED_HOSTS` tablicy *Settings.py*. Ten adres URL można pobrać w czasie wykonywania przy użyciu kodu, `os.environ['WEBSITE_HOSTNAME']` . App Service automatycznie ustawia `WEBSITE_HOSTNAME` zmienną środowiskową na adres URL aplikacji. |
| `DATABASES` | Zdefiniuj ustawienia w App Service dla połączenia z bazą danych i załaduj je jako zmienne środowiskowe w celu wypełnienia [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES) słownika. Można na przykład zapisywać wartości (zwłaszcza nazwę użytkownika i hasło) jako Azure Key Vault wpisy [tajne](../key-vault/secrets/quick-create-python.md). |

## <a name="serve-static-files-for-django-apps"></a>Obsługiwanie plików statycznych dla aplikacji Django

Jeśli Twoja aplikacja sieci Web Django zawiera statyczne pliki frontonu, najpierw postępuj zgodnie z instrukcjami dotyczącymi [zarządzania plikami statycznymi](https://docs.djangoproject.com/en/3.1/howto/static-files/) w dokumentacji Django.

W przypadku App Service następnie wprowadź następujące modyfikacje:

1. Rozważ użycie zmiennych środowiskowych (do lokalnego tworzenia) i ustawień aplikacji (podczas wdrażania w chmurze), aby dynamicznie ustawiać Django `STATIC_URL` i `STATIC_ROOT` zmienne. Na przykład:    

    ```python
    STATIC_URL = os.environ.get("DJANGO_STATIC_URL", "/static/")
    STATIC_ROOT = os.environ.get("DJANGO_STATIC_ROOT", "./static/")    
    ```

    `DJANGO_STATIC_URL` i `DJANGO_STATIC_ROOT` można je zmienić w razie potrzeby w środowiskach lokalnych i w chmurze. Na przykład jeśli proces kompilacji dla plików statycznych umieszcza je w folderze o nazwie `django-static` , można ustawić, `DJANGO_STATIC_URL` Aby `/django-static/` uniknąć używania wartości domyślnej.

1. Jeśli masz skrypt przed kompilacją, który generuje pliki statyczne w innym folderze, należy uwzględnić ten folder w zmiennej Django, `STATICFILES_DIRS` tak aby `collectstatic` proces Django go znalazł. Na przykład w przypadku uruchamiania `yarn build` w folderze frontonu, a przędza generuje `build/static` folder zawierający pliki statyczne, a następnie dołącza go w następujący sposób:

    ```python
    FRONTEND_DIR = "path-to-frontend-folder" 
    STATICFILES_DIRS = [os.path.join(FRONTEND_DIR, 'build', 'static')]    
    ```

    Tutaj, `FRONTEND_DIR` Aby utworzyć ścieżkę do miejsca, w którym jest uruchamiane narzędzie kompilacji, takie jak przędza. W razie potrzeby można ponownie użyć ustawienia zmienna środowiskowa i aplikacja.

1. Dodaj `whitenoise` do pliku *requirements.txt* . [Whitenoise](http://whitenoise.evans.io/en/stable/) (whitenoise.Evans.IO) to pakiet języka Python, który ułatwia aplikacjom Django produkcyjnych tworzenie własnych plików statycznych. Whitenoise w pełni obsługuje te pliki, które znajdują się w folderze określonym przez `STATIC_ROOT` zmienną Django.

1. W pliku *Settings.py* Dodaj następujący wiersz dla whitenoise:

    ```python
    STATICFILES_STORAGE = ('whitenoise.storage.CompressedManifestStaticFilesStorage')
    ```

1. Zmodyfikuj również `MIDDLEWARE` listy i, `INSTALLED_APPS` Aby uwzględnić whitenoise:

    ```python
    MIDDLEWARE = [
        "whitenoise.middleware.WhiteNoiseMiddleware",
        # Other values follow
    ]

    INSTALLED_APPS = [
        "whitenoise.runserver_nostatic",
        # Other values follow
    ]
    ```

## <a name="container-characteristics"></a>Właściwości kontenera

Po wdrożeniu w celu App Service aplikacje języka Python działają w kontenerze platformy Docker systemu Linux, który jest zdefiniowany w [repozytorium usługi GitHub App Service Python](https://github.com/Azure-App-Service/python). Konfiguracje obrazów można znaleźć w katalogach specyficznych dla danej wersji.

Ten kontener ma następujące cechy:

- Aplikacje są uruchamiane przy użyciu [serwera HTTP Gunicorn WSGI](https://gunicorn.org/) z dodatkowymi argumentami `--bind=0.0.0.0 --timeout 600`.
    - Można podać ustawienia konfiguracji Gunicorn za pomocą pliku *gunicorn.conf.py* w katalogu głównym projektu, zgodnie z opisem w temacie [konfiguracja Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org). Alternatywnie można [dostosować polecenie uruchamiania](#customize-startup-command).

    - Aby chronić aplikację sieci Web przed przypadkowymi lub świadomymi atakami DDOS, Gunicorn jest uruchamiany za pomocą zwrotnego serwera proxy Nginx, zgodnie z opisem w temacie [wdrażanie Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org).

- Domyślnie obraz kontenera Base zawiera tylko platformę sieci Web, ale kontener obsługuje inne platformy, które są zgodne z WSGI i są zgodne z językiem Python 3.6 +, takimi jak Django.

- Aby zainstalować dodatkowe pakiety, takie jak Django, Utwórz plik [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) w folderze głównym projektu, który określa zależności bezpośrednie. App Service następnie automatycznie zainstaluje te zależności podczas wdrażania projektu.

    Aby można było zainstalować zależności, plik *requirements.txt* *musi* znajdować się w katalogu głównym projektu. W przeciwnym razie proces kompilacji zgłosi błąd: "nie można znaleźć setup.py lub requirements.txt; Nie uruchomiono instalacji PIP ". Jeśli wystąpi ten błąd, Sprawdź lokalizację pliku wymagań.

- App Service automatycznie definiuje zmienną środowiskową o nazwie `WEBSITE_HOSTNAME` przy użyciu adresu URL aplikacji sieci Web, na przykład `msdocs-hello-world.azurewebsites.net` . Definiuje również `WEBSITE_SITE_NAME` nazwę aplikacji, na przykład `msdocs-hello-world` . 
   
- npm i Node.js są instalowane w kontenerze, dzięki czemu można uruchamiać narzędzia do kompilacji oparte na węzłach, takie jak przędza.

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

Jeśli potrzebujesz bardziej szczegółowej kontroli nad poleceniem uruchamiania, użyj [niestandardowego polecenia uruchamiania](#customize-startup-command), Zastąp ciąg `<module>` nazwą folderu zawierającego *WSGI.py* i Dodaj argument, `--chdir` Jeśli ten moduł nie znajduje się w katalogu głównym projektu. Na przykład jeśli *WSGI.py* znajduje się w obszarze *knboard/zaplecza/config* z katalogu głównego projektu, użyj argumentów `--chdir knboard/backend config.wsgi` .

Aby włączyć rejestrowanie produkcji, należy dodać `--access-logfile` Parametry i, `--error-logfile` jak pokazano w przykładach dla [poleceń uruchamiania niestandardowego](#customize-startup-command).

### <a name="flask-app"></a>Aplikacja Flask

W przypadku aplikacji Flask usługa App Service szuka pliku o nazwie *application.py* lub *app.py* i uruchamia serwer Gunicorn w następujący sposób:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Jeśli główny moduł aplikacji jest zawarty w innym pliku, użyj innej nazwy dla obiektu aplikacji lub chcesz podać dodatkowe argumenty Gunicorn, użyj [niestandardowego polecenia uruchamiania](#customize-startup-command).

### <a name="default-behavior"></a>Zachowanie domyślne

Jeśli App Service nie znajdzie polecenia niestandardowego, aplikacji Django lub aplikacji do przełączenia, zostanie uruchomiona domyślna aplikacja tylko do odczytu znajdująca się w folderze _opt/Defaultsite_ i pokazana na poniższej ilustracji.

Jeśli wdrożono kod i nadal widzisz aplikację domyślną, zobacz [Rozwiązywanie problemów — aplikacja nie jest wyświetlana](#app-doesnt-appear).

[![Domyślna strona internetowa usługi App Service w systemie Linux](media/configure-language-python/default-python-app.png)](#app-doesnt-appear)

Jeśli chcesz zobaczyć wdrożoną aplikację zamiast domyślnej aplikacji, zobacz [Rozwiązywanie problemów — aplikacja nie jest wyświetlana](#app-doesnt-appear).

## <a name="customize-startup-command"></a>Dostosowywanie polecenia uruchamiania

Jak wspomniano wcześniej w tym artykule, można podać ustawienia konfiguracji Gunicorn za pomocą pliku *gunicorn.conf.py* w katalogu głównym projektu, zgodnie z opisem w temacie [Omówienie konfiguracji Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file).

Jeśli taka konfiguracja nie jest wystarczająca, można kontrolować zachowanie uruchamiania kontenera, dostarczając niestandardowe polecenie uruchamiania lub wiele poleceń w pliku polecenia uruchamiania. Plik poleceń uruchamiania może korzystać z dowolnej wybranej nazwy, takiej jak *Startup.sh*, *Startup. cmd*, *startup.txt* i tak dalej.

Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

Aby określić polecenie uruchamiania lub plik poleceń:

- **Azure Portal**: Wybierz stronę **konfiguracji** aplikacji, a następnie wybierz pozycję **Ustawienia ogólne**. W polu **polecenie uruchamiania** Umieść pełny tekst polecenia uruchamiania lub nazwę pliku poleceń uruchomieniowych. Następnie wybierz pozycję **Zapisz** , aby zastosować zmiany. Zobacz [Konfigurowanie ustawień ogólnych](configure-common.md#configure-general-settings) dla kontenerów systemu Linux.

- **Interfejs wiersza polecenia platformy Azure**: Aby ustawić polecenie lub plik, użyj [AZ webapp config Set](/cli/azure/webapp/config#az_webapp_config_set) polecenie z `--startup-file` parametrem:

    ```azurecli
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Zamień na `<custom-command>` pełny tekst polecenia uruchamiania lub nazwę pliku polecenia uruchomieniowego.
        
App Service ignoruje wszelkie błędy występujące podczas przetwarzania niestandardowego polecenia uruchamiania lub pliku, a następnie kontynuuje proces uruchamiania, szukając aplikacji Django i kolb. Jeśli nie widzisz oczekiwanego zachowania, sprawdź, czy polecenie uruchamiania lub plik jest bezpłatny i czy plik poleceń uruchamiania jest wdrażany w App Service wraz z kodem aplikacji. Aby uzyskać dodatkowe informacje, można także sprawdzić [dzienniki diagnostyczne](#access-diagnostic-logs) . Sprawdź również stronę **diagnozowanie i rozwiązywanie problemów** aplikacji na [Azure Portal](https://portal.azure.com).

### <a name="example-startup-commands"></a>Przykładowe polecenia uruchamiania

- **Dodano argumenty Gunicorn**: Poniższy przykład dodaje `--workers=4` do Gunicorn wiersza polecenia, aby uruchomić aplikację Django: 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Aby uzyskać więcej informacji, zobacz [Running Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (Uruchamianie serwera Gunicorn) (docs.gunicorn.org).

- **Włącz rejestrowanie produkcyjne dla Django**: Dodaj `--access-logfile '-'` argumenty i `--error-logfile '-'` do wiersza polecenia:

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Te dzienniki będą widoczne w [strumieniu dziennika App Service](#access-diagnostic-logs).

    Aby uzyskać więcej informacji, zobacz [Gunicorn Logging](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Moduł główny kolby niestandardowej**: domyślnie App Service zakłada, że moduł główny aplikacji kolby to *Application.py* lub *App.py*. Jeśli Twój główny moduł używa innej nazwy, należy dostosować polecenie uruchamiania. Na przykład YF masz aplikację z kolbą, której moduł główny to *Hello.py* , a obiekt aplikacji do przeszukania w tym pliku jest `myapp` następujący:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Jeśli moduł główny znajduje się w podfolderze, takim jak `website`, określ ten folder za pomocą argumentu `--chdir`:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Użyj serwera innego niż Gunicorn**: Aby użyć innego serwera sieci Web, takiego jak [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), użyj odpowiedniego polecenia jako polecenia uruchamiania lub pliku polecenia uruchamiania:

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Dostęp do ustawień aplikacji jako zmiennych środowiskowych

Ustawienia aplikacji to wartości przechowywane w chmurze przeznaczone dla aplikacji zgodnie z opisem w temacie [Konfigurowanie ustawień aplikacji](configure-common.md#configure-app-settings). Te ustawienia są dostępne dla kodu aplikacji jako zmienne środowiskowe i dostępne za pomocą wzorca standardowego [systemu operacyjnego. environ —](https://docs.python.org/3/library/os.html#os.environ) .

Na przykład jeśli utworzono ustawienie aplikacji o nazwie `DATABASE_SERVER` , poniższy kod pobiera wartość tego ustawienia:

```python
db_server = os.environ['DATABASE_SERVER']
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W App Service na usługi równoważenia obciążenia sieciowego następuje [zakończenie protokołu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) (Wikipedia.org), więc wszystkie żądania HTTPS docierają do aplikacji jako nieszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. Na platformie [CodeIgniter](https://codeigniter.com/) element [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) domyślnie sprawdza wartość `X_FORWARDED_PROTO`.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Aby uzyskać dostęp do dzienników za pomocą Azure Portal, wybierz pozycję **monitorowanie**  >  **strumienia dzienników** w menu po lewej stronie dla aplikacji.

## <a name="access-deployment-logs"></a>Dzienniki wdrażania dostępu

Podczas wdrażania kodu, App Service wykonuje proces kompilacji opisany wcześniej w sekcji [Dostosowywanie automatyzacji kompilacji](#customize-build-automation). Ponieważ kompilacja jest uruchamiana we własnym kontenerze, dzienniki kompilacji są przechowywane niezależnie od dzienników diagnostycznych aplikacji.

Wykonaj następujące kroki, aby uzyskać dostęp do dzienników wdrożenia:

1. Na Azure Portal aplikacji sieci Web wybierz pozycję **Deployment**  >  **Deployment Center (wersja zapoznawcza)** w menu po lewej stronie.
1. Na karcie **dzienniki** wybierz **Identyfikator zatwierdzenia** dla ostatniego zatwierdzenia.
1. Na wyświetlonej stronie **szczegóły dziennika** wybierz link **Pokaż dzienniki...** , który pojawia się obok pozycji "Uruchamianie kompilacji Oryx...".

W tych dziennikach będą wyświetlane problemy z kompilacją, takie jak nieprawidłowe zależności w *requirements.txt* i błędy w skryptach pre-lub po kompilacji. Błędy są również wyświetlane, jeśli plik wymagań nie jest dokładnie nazwany *requirements.txt* lub nie jest wyświetlany w folderze głównym projektu.

## <a name="open-ssh-session-in-browser"></a>Otwieranie sesji SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

Po pomyślnym nawiązaniu połączenia z sesją SSH powinien zostać wyświetlony komunikat "NAWIĄZANO połączenie SSH" w dolnej części okna. Jeśli zostaną wyświetlone błędy, takie jak "SSH_CONNECTION_CLOSED" lub komunikat informujący o ponownym uruchomieniu kontenera, błąd może uniemożliwiać uruchomienie kontenera aplikacji. Aby zbadać możliwe problemy, zobacz [Rozwiązywanie problemów](#troubleshooting) .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ogólnie rzecz biorąc, pierwszy krok rozwiązywania problemów polega na użyciu diagnostyki App Service:

1. Na Azure Portal aplikacji sieci Web wybierz opcję **Diagnozuj i rozwiąż problemy** z menu po lewej stronie.
1. Wybierz pozycję **dostępność i wydajność**.
1. Zapoznaj się z informacjami w opcjach **Dzienniki aplikacji**, **awaria kontenera** i **problemy z kontenerem** , w których pojawią się najczęstsze problemy.

Następnie przejrzyj [dzienniki wdrożenia](#access-deployment-logs) i [Dzienniki aplikacji](#access-diagnostic-logs) w poszukiwaniu wszelkich komunikatów o błędach. Te dzienniki często identyfikują konkretne problemy, które mogą uniemożliwić rozmieszczenie aplikacji lub uruchamianie aplikacji. Na przykład kompilacja może zakończyć się niepowodzeniem, jeśli plik *requirements.txt* ma nieprawidłową nazwę pliku lub nie jest obecny w folderze głównym projektu.

Poniższe sekcje zawierają dodatkowe wskazówki dotyczące konkretnych problemów.

- [Aplikacja nie jest wyświetlana — domyślne wyświetlanie aplikacji](#app-doesnt-appear)
- [Aplikacja nie jest wyświetlana — komunikat "Usługa niedostępna"](#service-unavailable)
- [Nie można znaleźć setup.py lub requirements.txt](#could-not-find-setuppy-or-requirementstxt)
- [ModuleNotFoundError przy uruchamianiu](#modulenotfounderror-when-app-starts)
- [Baza danych jest zablokowana](#database-is-locked)
- [Hasła nie są wyświetlane w sesji SSH po wpisaniu](#other-issues)
- [Polecenia w sesji SSH prawdopodobnie są obcinane](#other-issues)
- [Statyczne zasoby nie są wyświetlane w aplikacji Django](#other-issues)
- [Wymagane jest krytyczne połączenie SSL](#other-issues)

#### <a name="app-doesnt-appear"></a>Aplikacja nie jest wyświetlana

- **Po wdrożeniu własnego kodu aplikacji wyświetlana jest aplikacja domyślna.** [Aplikacja domyślna](#default-behavior) zostanie wyświetlona, ponieważ nie wdrożono kodu aplikacji do App Service lub App Service nie można znaleźć kodu aplikacji i zamiast tego uruchomił aplikację domyślną.

    - Uruchom ponownie usługę App Service, poczekaj 15–20 sekund i sprawdź ponownie aplikację.
    
    - Upewnij się, że używasz usługi App Service dla systemu Linux, a nie wystąpienia opartego na systemie Windows. W interfejsie wiersza polecenia platformy Azure uruchom polecenie `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind`, zastępując zmienne `<resource-group-name>` i `<app-name>` odpowiednimi wartościami. Powinny zostać wyświetlone dane wyjściowe `app,linux`. W przeciwnym razie ponownie utwórz usługę App Service i wybierz system Linux.
    
    - Za pomocą protokołu [SSH](#open-ssh-session-in-browser) Połącz się bezpośrednio z kontenerem App Service i sprawdź, czy pliki znajdują się w obszarze *site/wwwroot*. Jeśli pliki nie istnieją, wykonaj następujące czynności:
      1. Utwórz ustawienie aplikacji o nazwie `SCM_DO_BUILD_DURING_DEPLOYMENT` z wartością 1, ponownie Wdróż kod, odczekaj kilka minut, a następnie spróbuj uzyskać dostęp do aplikacji. Aby uzyskać więcej informacji na temat tworzenia ustawień aplikacji, zobacz [Konfigurowanie aplikacji App Service w Azure Portal](configure-common.md).
      1. Przejrzyj proces wdrażania, [Sprawdź dzienniki wdrażania](#access-deployment-logs), Napraw wszystkie błędy i ponownie Wdróż aplikację.
    
    - Jeśli pliki istnieją, oznacza to, że usługa App Service nie mogła zidentyfikować określonego pliku startowego. Sprawdź, czy aplikacja ma strukturę, App Service oczekuje na [Django](#django-app) lub [kolbę](#flask-app), lub Użyj [niestandardowego polecenia uruchamiania](#customize-startup-command).

- <a name="service-unavailable"></a>**W przeglądarce zostanie wyświetlony komunikat "Usługa niedostępna".** Przekroczono limit czasu oczekiwania przeglądarki na odpowiedź z App Service, co oznacza, że App Service uruchomiono serwer Gunicorn, ale sama aplikacja nie została uruchomiona. Ten stan może wskazywać, że argumenty Gunicorn są niepoprawne lub wystąpiły błędy w kodzie aplikacji.

    - Odśwież okno przeglądarki, zwłaszcza jeśli korzystasz z niższych warstw cenowych w planie usługi App Service. Na przykład podczas korzystania z warstw bezpłatnych aplikacja może być uruchamiana dłużej i zacznie odpowiadać po odświeżeniu okna przeglądarki.

    - Sprawdź, czy aplikacja ma strukturę, App Service oczekuje na [Django](#django-app) lub [kolbę](#flask-app), lub Użyj [niestandardowego polecenia uruchamiania](#customize-startup-command).

    - Przejrzyj [strumień dziennika aplikacji](#access-diagnostic-logs) pod kątem wszystkich komunikatów o błędach. W dziennikach zostaną wyświetlone wszystkie błędy w kodzie aplikacji.

#### <a name="could-not-find-setuppy-or-requirementstxt"></a>Nie można znaleźć setup.py lub requirements.txt

- **Strumień dziennika pokazuje, że nie można znaleźć Setup.py lub requirements.txt; Nie uruchomiono instalacji PIP. "**: proces kompilacji Oryx nie może odnaleźć pliku *requirements.txt* .

    - Połącz się z kontenerem aplikacji sieci Web za pośrednictwem protokołu [SSH](#open-ssh-session-in-browser) i sprawdź, czy *requirements.txt* ma nazwę poprawnie i czy istnieje bezpośrednio w obszarze *site/wwwroot*. Jeśli nie istnieje, Utwórz lokację w repozytorium i jest ona uwzględniona w Twoim wdrożeniu. Jeśli istnieje w oddzielnym folderze, przenieś go do katalogu głównego.

#### <a name="modulenotfounderror-when-app-starts"></a>ModuleNotFoundError podczas uruchamiania aplikacji

Jeśli zobaczysz błąd podobny do `ModuleNotFoundError: No module named 'example'` tego, oznacza to, że język Python nie mógł znaleźć co najmniej jednego modułu podczas uruchamiania aplikacji. Najczęściej zdarza się to w przypadku wdrożenia środowiska wirtualnego przy użyciu kodu. Środowiska wirtualne nie są przenośne, dlatego nie należy wdrażać środowiska wirtualnego przy użyciu kodu aplikacji. Zamiast tego pozwól Oryx na utworzenie środowiska wirtualnego i instalowanie pakietów w aplikacji sieci Web, tworząc ustawienie aplikacji `SCM_DO_BUILD_DURING_DEPLOYMENT` i ustawiając je na `1` . Spowoduje to wymuszenie instalacji pakietów przez Oryx przy każdym wdrożeniu programu do App Service. Aby uzyskać więcej informacji, zobacz [ten artykuł dotyczący przenośności środowiska wirtualnego](https://azure.github.io/AppService/2020/12/11/cicd-for-python-apps.html).

### <a name="database-is-locked"></a>Baza danych jest zablokowana

Podczas próby uruchomienia migracji bazy danych za pomocą aplikacji Django może być widoczna wartość "sqlite3. OperationalError: baza danych jest zablokowana. " Ten błąd oznacza, że aplikacja korzysta z bazy danych programu SQLite, dla której Django jest domyślnie konfigurowana, zamiast korzystać z bazy danych w chmurze, takiej jak PostgreSQL dla platformy Azure.

Sprawdź `DATABASES` zmienną w pliku *Settings.py* aplikacji, aby upewnić się, że aplikacja korzysta z bazy danych w chmurze zamiast programu SQLite.

Jeśli napotkasz ten błąd w [samouczku przykładowym: Wdróż aplikację sieci Web Django za pomocą PostgreSQL](tutorial-python-postgresql-app.md), sprawdź, czy zostały wykonane kroki opisane w temacie [Konfigurowanie zmiennych środowiskowych w celu połączenia bazy danych](tutorial-python-postgresql-app.md#42-configure-environment-variables-to-connect-the-database).

#### <a name="other-issues"></a>Inne problemy

- **Hasła nie pojawiają się w sesji SSH po wpisaniu**: ze względów bezpieczeństwa sesja SSH utrzymuje hasło ukryte podczas wpisywania. Znaki są rejestrowane, jednak wpisz hasło w zwykły sposób i naciśnij klawisz **Enter** po zakończeniu.

- **Polecenia w sesji SSH prawdopodobnie są obcinane**: Edytor nie może być poleceniami zawijania słów, ale powinny nadal działać poprawnie.

- **Statyczne zasoby nie są wyświetlane w aplikacji Django**: Upewnij się, że włączono [moduł whitenoise](http://whitenoise.evans.io/en/stable/django.html)

- **Zobaczysz komunikat "wymagane jest krytyczne połączenie SSL"**: Sprawdź wszystkie nazwy użytkowników i hasła używane do uzyskiwania dostępu do zasobów (takich jak bazy danych) w aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja w języku Python z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie z repozytorium kontenera prywatnego](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](faq-app-service-linux.md)