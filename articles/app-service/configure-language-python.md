---
title: Konfigurowanie aplikacji systemu Linux Python
description: Informacje o konfigurowaniu kontenera języka Python, w którym są uruchamiane aplikacje sieci Web, przy użyciu zarówno Azure Portal, jak i interfejsu wiersza polecenia platformy Azure.
ms.topic: quickstart
ms.date: 10/06/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python
ms.openlocfilehash: df4b94702d14278a3279c504f52f46b922859db8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822817"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurowanie aplikacji systemu Linux w języku Python dla Azure App Service

W tym artykule opisano, jak usługa [Azure App Service](overview.md) uruchamia aplikacje języka Python i jak można dostosować zachowanie usługi App Service w razie potrzeby. Aplikacje języka Python muszą zostać wdrożone ze wszystkimi wymaganymi modułami [PIP](https://pypi.org/project/pip/) .

Aparat wdrażania App Service automatycznie aktywuje środowisko wirtualne i jest uruchamiany `pip install -r requirements.txt` podczas wdrażania [repozytorium git](deploy-local-git.md)lub [pakietu zip](deploy-zip.md).

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka Python, którzy używają wbudowanego kontenera systemu Linux w App Service. Jeśli nigdy nie korzystasz z Azure App Service, najpierw postępuj zgodnie z samouczkiem [przewodnika Szybki Start](quickstart-python.md) i [Python z PostgreSQL](tutorial-python-postgresql-app.md).

Do konfiguracji można użyć [Azure Portal](https://portal.azure.com) lub interfejsu wiersza polecenia platformy Azure:

- **Azure Portal**Użyj **Settings**  >  strony**konfiguracji** ustawienia aplikacji zgodnie z opisem w temacie [Konfigurowanie aplikacji App Service w Azure Portal](configure-common.md).

- **Interfejs wiersza polecenia platformy Azure**: masz dwie opcje.

    - Uruchom polecenia w [Azure Cloud Shell](../cloud-shell/overview.md), które można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom polecenia lokalnie, instalując najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), a następnie zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login).
    
> [!NOTE]
> System Linux jest obecnie zalecaną opcją uruchamiania aplikacji w języku Python w App Service. Aby uzyskać informacje na temat opcji systemu Windows, zobacz [Python w systemie windows App Service](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Konfigurowanie wersji języka Python

- **Azure Portal**: Użyj karty **Ustawienia ogólne** na stronie **Konfiguracja** , zgodnie z opisem w temacie [Konfigurowanie ustawień ogólnych](configure-common.md#configure-general-settings) dla kontenerów systemu Linux.

- **Interfejs wiersza polecenia platformy Azure**:

    -  Pokaż bieżącą wersję języka Python za pomocą [AZ webapp config show](/cli/azure/webapp/config#az_webapp_config_show):
    
        ```azurecli-interactive
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Zamień `<resource-group-name>` i na `<app-name>` nazwy odpowiednie dla aplikacji sieci Web.
    
    - Ustaw wersję języka Python za pomocą [AZ webapp config Set](/cli/azure/webapp/config#az_webapp_config_set)
        
        ```azurecli-interactive
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Pokaż wszystkie wersje języka Python, które są obsługiwane w Azure App Service za pomocą [AZ webapp list-Runtimes](/cli/azure/webapp#az_webapp_list_runtimes):
    
        ```azurecli-interactive
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Możesz uruchomić nieobsługiwaną wersję języka Python, kompilując w zamian własny obraz kontenera. Aby uzyskać więcej informacji, zobacz [Używanie niestandardowego obrazu platformy Docker](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Dostosuj automatyzację kompilacji

System kompilacji App Service o nazwie Oryx, podczas wdrażania aplikacji przy użyciu pakietów Git lub zip wykonuje następujące czynności:

1. Uruchom niestandardowy skrypt przed kompilacją, jeśli został określony przez `PRE_BUILD_COMMAND` ustawienie.
1. Uruchom polecenie `pip install -r requirements.txt`. Plik *requirements.txt* musi znajdować się w folderze głównym projektu. W przeciwnym razie proces kompilacji zgłosi błąd: "nie można znaleźć setup.py lub requirements.txt; Nie uruchomiono instalacji PIP ".
1. Jeśli *manage.py* znajduje się w katalogu głównym repozytorium (wskazującym aplikację Django), uruchom *manage.py collectstatic*. Jeśli jednak `DISABLE_COLLECTSTATIC` ustawienie ma wartość `true` , ten krok zostanie pominięty.
1. Uruchom niestandardowy skrypt po kompilacji, jeśli został określony przez `POST_BUILD_COMMAND` ustawienie.

Domyślnie `PRE_BUILD_COMMAND` `POST_BUILD_COMMAND` Ustawienia, i `DISABLE_COLLECTSTATIC` są puste. 

- Aby wyłączyć uruchamianie collectstatic podczas kompilowania aplikacji Django, ustaw `DISABLE_COLLECTSTATIC` dla ustawienia wartość true.

- Aby uruchomić polecenia przed kompilacją, należy ustawić `PRE_BUILD_COMMAND` opcję, która będzie zawierać polecenie, takie jak `echo Pre-build command` , lub ścieżkę do pliku skryptu względem katalogu głównego projektu, na przykład `scripts/prebuild.sh` . Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

- Aby uruchomić polecenia po kompilacji, należy ustawić `POST_BUILD_COMMAND` opcję, która będzie zawierać polecenie, takie jak `echo Post-build command` , lub ścieżkę do pliku skryptu względem katalogu głównego projektu, na przykład `scripts/postbuild.sh` . Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

Aby uzyskać dodatkowe ustawienia, które dostosowują automatyzację kompilacji, zobacz [Konfiguracja Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

Aby uzyskać więcej informacji na temat sposobu uruchamiania App Service i tworzenia aplikacji w języku Python w systemie Linux, zobacz [jak Oryx wykrywa i tworzy aplikacje języka Python](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

> [!NOTE]
> `PRE_BUILD_SCRIPT_PATH`Ustawienia i `POST_BUILD_SCRIPT_PATH` są takie same jak `PRE_BUILD_COMMAND` i `POST_BUILD_COMMAND` i są obsługiwane w starszych celach.
> 
> Ustawienie o nazwie `SCM_DO_BUILD_DURING_DEPLOYMENT` , jeśli zawiera `true` lub 1, wyzwala kompilację Oryx podczas wdrażania. Ustawienie ma wartość true w przypadku wdrażania przy użyciu narzędzia Git, polecenia interfejsu CLI platformy Azure `az webapp up` i Visual Studio Code.

> [!NOTE]
> Zawsze używaj ścieżek względnych we wszystkich skryptach przed i po kompilacji, ponieważ kontener kompilacji, w którym działa Oryx, różni się od kontenera środowiska uruchomieniowego, w którym działa aplikacja. Nigdy nie należy polegać na dokładnym umieszczeniu folderu projektu aplikacji w kontenerze (na przykład, że znajduje się on w obszarze *site/wwwroot*).

## <a name="container-characteristics"></a>Właściwości kontenera

Po wdrożeniu w celu App Service aplikacje języka Python działają w kontenerze platformy Docker systemu Linux, który jest zdefiniowany w [repozytorium usługi GitHub App Service Python](https://github.com/Azure-App-Service/python). Konfiguracje obrazów można znaleźć w katalogach specyficznych dla danej wersji.

Ten kontener ma następujące cechy:

- Aplikacje są uruchamiane przy użyciu [serwera HTTP Gunicorn WSGI](https://gunicorn.org/) z dodatkowymi argumentami `--bind=0.0.0.0 --timeout 600`.
    - Można podać ustawienia konfiguracji Gunicorn za pomocą pliku *gunicorn.conf.py* w katalogu głównym projektu, zgodnie z opisem w temacie [konfiguracja Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org). Alternatywnie można [dostosować polecenie uruchamiania](#customize-startup-command).

    - Aby chronić aplikację sieci Web przed przypadkowymi lub świadomymi atakami DDOS, Gunicorn jest uruchamiany za pomocą zwrotnego serwera proxy Nginx, zgodnie z opisem w temacie [wdrażanie Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org).

- Domyślnie obraz kontenera Base zawiera tylko platformę sieci Web, ale kontener obsługuje inne platformy, które są zgodne z WSGI i są zgodne z językiem Python 3.6 +, takimi jak Django.

- Aby zainstalować dodatkowe pakiety, takie jak Django, Utwórz plik [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) w folderze głównym projektu, który określa zależności bezpośrednie. App Service następnie automatycznie zainstaluje te zależności podczas wdrażania projektu.

    Aby można było zainstalować zależności, plik *requirements.txt* *musi* znajdować się w katalogu głównym projektu. W przeciwnym razie proces kompilacji zgłosi błąd: "nie można znaleźć setup.py lub requirements.txt; Nie uruchomiono instalacji PIP ". Jeśli wystąpi ten błąd, Sprawdź lokalizację pliku wymagań.

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

Jeśli potrzebujesz bardziej szczegółowej kontroli nad poleceniem uruchamiania, użyj [niestandardowego polecenia uruchamiania](#customize-startup-command), Zastąp ciąg `<module>` nazwą folderu zawierającego *WSGI.py*i Dodaj argument, `--chdir` Jeśli ten moduł nie znajduje się w katalogu głównym projektu. Na przykład jeśli *WSGI.py* znajduje się w obszarze *knboard/zaplecza/config* z katalogu głównego projektu, użyj argumentów `--chdir knboard/backend config.wsgi` .

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

Jeśli usługa App Service nie znajdzie polecenia niestandardowego, aplikacji Django ani aplikacji Flask, uruchomi domyślną aplikację dostępną tylko do odczytu, która znajduje się w folderze _opt/defaultsite_. Aplikacja domyślna wygląda następująco:

![Domyślna strona internetowa usługi App Service w systemie Linux](media/configure-language-python/default-python-app.png)

## <a name="customize-startup-command"></a>Dostosowywanie polecenia uruchamiania

Jak wspomniano wcześniej w tym artykule, można podać ustawienia konfiguracji Gunicorn za pomocą pliku *gunicorn.conf.py* w katalogu głównym projektu, zgodnie z opisem w temacie [Omówienie konfiguracji Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file).

Jeśli taka konfiguracja nie jest wystarczająca, można kontrolować zachowanie uruchamiania kontenera, dostarczając niestandardowe polecenie uruchamiania lub wiele poleceń w pliku polecenia uruchamiania. Plik poleceń uruchamiania może korzystać z dowolnej wybranej nazwy, takiej jak *Startup.sh*, *Startup. cmd*, *startup.txt*i tak dalej.

Wszystkie polecenia muszą używać ścieżek względnych do folderu głównego projektu.

Aby określić polecenie uruchamiania lub plik poleceń:

- **Azure Portal**: Wybierz stronę **konfiguracji** aplikacji, a następnie wybierz pozycję **Ustawienia ogólne**. W polu **polecenie uruchamiania** Umieść pełny tekst polecenia uruchamiania lub nazwę pliku poleceń uruchomieniowych. Następnie wybierz pozycję **Zapisz** , aby zastosować zmiany. Zobacz [Konfigurowanie ustawień ogólnych](configure-common.md#configure-general-settings) dla kontenerów systemu Linux.

- **Interfejs wiersza polecenia platformy Azure**: Aby ustawić polecenie lub plik, użyj [AZ webapp config Set](/cli/azure/webapp/config#az_webapp_config_set) polecenie z `--startup-file` parametrem:

    ```azurecli-interactive
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

## <a name="open-ssh-session-in-browser"></a>Otwieranie sesji SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- **Po wdrożeniu własnego kodu aplikacji wyświetlana jest aplikacja domyślna.** Aplikacja domyślna jest wyświetlana dlatego, że kod Twojej aplikacji nie został wdrożony w usłudze App Service albo usługa App Service nie znalazła kodu aplikacji i zamiast niej uruchomiła aplikację domyślną.

    - Uruchom ponownie usługę App Service, poczekaj 15–20 sekund i sprawdź ponownie aplikację.
    
    - Upewnij się, że używasz usługi App Service dla systemu Linux, a nie wystąpienia opartego na systemie Windows. W interfejsie wiersza polecenia platformy Azure uruchom polecenie `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind`, zastępując zmienne `<resource-group-name>` i `<app-service-name>` odpowiednimi wartościami. Powinny zostać wyświetlone dane wyjściowe `app,linux`. W przeciwnym razie ponownie utwórz usługę App Service i wybierz system Linux.
    
    - Połącz się bezpośrednio z usługą App Service przy użyciu konsoli SSH lub Kudu, a następnie sprawdź, czy Twoje pliki znajdują się w katalogu *site/wwwroot*. Jeśli pliki nie istnieją, sprawdź proces wdrażania i ponownie wdróż aplikację.
    
    - Jeśli pliki istnieją, oznacza to, że usługa App Service nie mogła zidentyfikować określonego pliku startowego. Sprawdź, czy aplikacja ma strukturę, App Service oczekuje na [Django](#django-app) lub [kolbę](#flask-app), lub Użyj [niestandardowego polecenia uruchamiania](#customize-startup-command).

- **W przeglądarce jest wyświetlany komunikat „Usługa niedostępna”.** W przeglądarce upłynął limit czasu oczekiwania na odpowiedź usługi App Service, co wskazuje, że usługa App Service uruchomiła serwer Gunicorn, ale argumenty określające kod aplikacji są niepoprawne.

    - Odśwież okno przeglądarki, zwłaszcza jeśli korzystasz z niższych warstw cenowych w planie usługi App Service. Na przykład podczas korzystania z warstw bezpłatnych aplikacja może być uruchamiana dłużej i zacznie odpowiadać po odświeżeniu okna przeglądarki.

    - Sprawdź, czy aplikacja ma strukturę, App Service oczekuje na [Django](#django-app) lub [kolbę](#flask-app), lub Użyj [niestandardowego polecenia uruchamiania](#customize-startup-command).

    - Przejrzyj [strumień dziennika](#access-diagnostic-logs) pod kątem wszystkich komunikatów o błędach.

- **Strumień dziennika pokazuje, że nie można znaleźć Setup.py lub requirements.txt; Nie uruchomiono instalacji PIP. "**: proces kompilacji Oryx nie może odnaleźć pliku *requirements.txt* .

    - Użyj protokołu SSH lub konsoli kudu, aby połączyć się bezpośrednio z App Service i sprawdź, czy *requirements.txt* istnieje bezpośrednio w obszarze *site/wwwroot*. Jeśli nie istnieje, Utwórz lokację w repozytorium i jest ona uwzględniona w Twoim wdrożeniu. Jeśli istnieje w oddzielnym folderze, przenieś go do katalogu głównego.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja w języku Python z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie z repozytorium kontenera prywatnego](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [App Service dla systemu Linux — często zadawane pytania](faq-app-service-linux.md)
