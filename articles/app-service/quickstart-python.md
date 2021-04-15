---
title: 'Szybki start: tworzenie aplikacji w języku Python'
description: Rozpoczynanie pracy z Azure App Service od wdrożenia pierwszej aplikacji w języku Python w kontenerze systemu Linux w App Service.
ms.topic: quickstart
ms.date: 11/10/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
zone_pivot_groups: python-frameworks-01
adobe-target: true
adobe-target-activity: DocsExp–393165–A/B–Docs/PythonQuickstart–CLIvsPortal–FY21Q4
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-python-portal
ms.openlocfilehash: d8af7a9744fe136f482fc007dbd0e51a680df932
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379489"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux"></a>Szybki start: tworzenie aplikacji w języku Python przy użyciu Azure App Service dla systemu Linux

W tym przewodniku Szybki start wdrożysz aplikację internetową w języku Python App Service dla systemu Linux [—](overview.md#app-service-on-linux)wysoce skalowalnej i samonadajnej usłudze hostingu w Internecie na platformie Azure. Użyj lokalnego interfejsu wiersza polecenia [platformy Azure na](/cli/azure/install-azure-cli) komputerze Mac, Linux lub Windows, aby wdrożyć przykład za pomocą platformy Flask lub Django. Skonfigurowana aplikacja internetowa używa podstawowej warstwy App Service, która wiąże się z niewielkimi kosztami w ramach subskrypcji platformy Azure.

## <a name="set-up-your-initial-environment"></a>Konfigurowanie początkowego środowiska

1. Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Zainstaluj <a href="https://www.python.org/downloads/" target="_blank">język Python w wersji 3.6 lub wyższej.</a>
1. Zainstaluj interfejs <a href="/cli/azure/install-azure-cli" target="_blank">wiersza polecenia</a> platformy Azure w wersji 2.0.80 lub wyższej, za pomocą którego uruchamiasz polecenia w dowolnej powłoki w celu aprowizowania i konfigurowania zasobów platformy Azure.

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

Sprawdź, czy interfejs wiersza polecenia platformy Azure jest w wersji 2.0.80 lub wyższej:

```azurecli
az --version
```

Następnie zaloguj się do platformy Azure za pomocą interfejsu wiersza polecenia:

```azurecli
az login
```

To polecenie powoduje otwarcie przeglądarki w celu zebrania poświadczeń. Po zakończeniu polecenie wyświetla dane wyjściowe JSON zawierające informacje o twoich subskrypcjach.

Po zalogowaniu można uruchamiać polecenia platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, aby pracować z zasobami w ramach subskrypcji.

Masz problemy? [Daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clone-the-sample"></a>Klonowanie przykładu

Sklonuj przykładowe repozytorium przy użyciu następującego polecenia i przejdź do folderu sample. (Zainstaluj[repozytorium git,](https://git-scm.com/downloads) jeśli nie masz jeszcze narzędzia git).

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```
::: zone-end

Przykład zawiera kod specyficzny dla struktury, który Azure App Service rozpoznawczy podczas uruchamiania aplikacji. Aby uzyskać więcej informacji, zobacz [Proces uruchamiania kontenera](configure-language-python.md#container-startup-process).

Masz problemy? [Daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

::: zone pivot="python-framework-flask"
1. Przejdź do folderu *python-docs-hello-world:*

    ```terminal
    cd python-docs-hello-world
    ```

1. Utwórz środowisko wirtualne i zainstaluj zależności:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Jeśli napotkasz komunikat "[Errno 2] No such file or directory: 'requirements.txt'".", upewnij się, że jesteś w folderze *python-docs-hello-world.*

1. Uruchom serwer dewelopera.

    ```terminal  
    flask run
    ```
    
    Domyślnie serwer zakłada, że moduł wprowadzania aplikacji znajduje się w app.py *,* jak w przykładzie.

    Jeśli używasz innej nazwy modułu, ustaw zmienną `FLASK_APP` środowiskową na wartość tej nazwy.

    Jeśli wystąpi błąd "Nie można zlokalizować aplikacji Flask. Zmienna środowiskowa "FLASK_APP" nie została udostępniona, a moduł "wsgi.py" lub "app.py" nie został znaleziony w bieżącym katalogu". Upewnij się, że jesteś w folderze zawierającym `python-docs-hello-world` przykład.

1. Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji na stronie `http://localhost:5000/` . W aplikacji zostanie wyświetlony komunikat **Hello, World!**.

    ![Uruchamianie przykładowej aplikacji w języku Python lokalnie](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. W oknie terminalu naciśnij **klawisze Ctrl** + **C,** aby zamknąć serwer dewelopera.
::: zone-end

::: zone pivot="python-framework-django"
1. Przejdź do *folderu python-docs-hello-django:*

    ```terminal
    cd python-docs-hello-django
    ```

1. Utwórz środowisko wirtualne i zainstaluj zależności:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Jeśli napotkasz błąd "[Errno 2] No such file or directory: 'requirements.txt'".", upewnij się, że jesteś w folderze *python-docs-hello-django.*
    
1. Uruchom serwer dewelopera.

    ```terminal
    python manage.py runserver
    ```

1. Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji na stronie `http://localhost:8000/` . W aplikacji zostanie wyświetlony komunikat **Hello, World!**.

    ![Uruchamianie przykładowej aplikacji w języku Python lokalnie](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. W oknie terminalu naciśnij **klawisze Ctrl** + **C,** aby zamknąć serwer projektowy.
::: zone-end

Masz problemy? [Daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="deploy-the-sample"></a>Wdrażanie przykładu

Wd wdrażaj kod w folderze lokalnym *(python-docs-hello-world)* przy użyciu `az webapp up` polecenia :

```azurecli
az webapp up --sku B1 --name <app-name>
```

- Jeśli polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem w tesłudze `az` Set up your initial environment (Konfigurowanie [początkowego środowiska).](#set-up-your-initial-environment)
- Jeśli polecenie nie zostanie rozpoznane, ponieważ interfejs wiersza polecenia platformy Azure ma `webapp` wersję 2.0.80 lub nowszą. Jeśli nie, [zainstaluj najnowszą wersję](/cli/azure/install-azure-cli)programu .
- Zastąp `<app_name>` nazwą unikatową na całej platformie Azure (prawidłowe *znaki to , `a-z` `0-9` i `-`*). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
- Argument `--sku B1` tworzy aplikację internetową w warstwie cenowej Podstawowa, co powoduje naliczanie małego kosztu godzinowego. Pomiń ten argument, aby użyć szybszej warstwy Premium.
- Opcjonalnie możesz dołączyć argument `--location <location-name>` , gdzie `<location_name>` jest dostępnym regionem świadczenia usługi Azure. Listę regionów, w których można zezwolić na korzystanie z konta platformy Azure, możesz pobrać, uruchamiając [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) polecenie .
- Jeśli zostanie wyświetlony błąd "Nie można automatycznie wykryć stosu środowiska uruchomieniowego aplikacji", upewnij się, że uruchamiasz polecenie w folderze *python-docs-hello-world* (Flask) lub w folderze *python-docs-hello-django* (Django), który zawiera *plikrequirements.txt.* (Zobacz [Troubleshooting auto-detect issues with az webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub) (Rozwiązywanie problemów z automatycznym wykrywaniem problemów z usługą az webapp up (GitHub).)

Ukończenie polecenia może potrwać kilka minut. Podczas działania program udostępnia komunikaty dotyczące tworzenia grupy zasobów, planu App Service hostowania aplikacji, konfigurowania rejestrowania, a następnie wdrażania pliku ZIP. Następnie jest wyświetlany komunikat "Możesz uruchomić aplikację pod nazwą http:// .azurewebsites.net", który jest adresem URL aplikacji na &lt; &gt; platformie Azure.

![Przykładowe dane wyjściowe polecenia az webapp up](./media/quickstart-python/az-webapp-up-output.png)

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania](configure-language-python.md#troubleshooting)problemów. W przeciwnym razie [daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji w przeglądarce internetowej pod adresem URL `http://<app-name>.azurewebsites.net` . Uruchomienie aplikacji może potrwać minutę lub dwie, więc jeśli zobaczysz domyślną stronę aplikacji, zaczekaj chwilę i odśwież przeglądarkę.

Przykładowy kod w języku Python uruchamia kontener systemu Linux App Service użyciu wbudowanego obrazu.

![Uruchamianie przykładowej aplikacji w języku Python na platformie Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulacje!** Aplikacja w języku Python została wdrożona do App Service.

Masz problemy? W pierwszej kolejności zapoznaj się [z przewodnikiem rozwiązywania](configure-language-python.md#troubleshooting)problemów. W przeciwnym razie [daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="redeploy-updates"></a>Ponowne wdychaj aktualizacje

W tej sekcji dokonasz niewielkiej zmiany kodu, a następnie ponownie wdepniesz kod na platformie Azure. Zmiana kodu zawiera instrukcje służące do generowania danych wyjściowych rejestrowania, z których będziesz `print` pracować w następnej sekcji.

::: zone pivot="python-framework-flask"
Otwórz *app.py* w edytorze i zaktualizuj `hello` funkcję, aby dopasować go do poniższego kodu. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Otwórz *w edytorze element hello/views.py* i zaktualizuj funkcję tak, `hello` aby dopasować go do poniższego kodu.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Zapisz zmiany, a następnie ponownie wdeń aplikację przy użyciu `az webapp up` polecenia :

```azurecli
az webapp up
```

To polecenie używa wartości buforowanych lokalnie w pliku *.azure/config,* w tym nazwy aplikacji, grupy zasobów i App Service planu.

Po zakończeniu wdrażania wróć do okna przeglądarki otwartego `http://<app-name>.azurewebsites.net` na . Odśwież stronę, na której powinien zostać wyświetlony zmodyfikowany komunikat:

![Uruchamianie zaktualizowanej przykładowej aplikacji w języku Python na platformie Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania problemów.](configure-language-python.md#troubleshooting)W przeciwnym razie [daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

> [!TIP]
> Visual Studio Code zapewnia zaawansowane rozszerzenia dla języka Python i Azure App Service, które upraszczają proces wdrażania aplikacji internetowych w języku Python w App Service. Aby uzyskać więcej informacji, zobacz [Deploy Python apps to App Service from Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01)(Wdrażanie aplikacji w języku Python App Service z Visual Studio Code .

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Możesz uzyskać dostęp do dzienników konsoli wygenerowanych wewnątrz aplikacji i kontenera, w którym jest uruchamiana. Dzienniki zawierają wszystkie dane wyjściowe wygenerowane przy użyciu `print` instrukcji .

Aby przesyłać strumieniowo dzienniki, uruchom [polecenie az webapp log tail:](/cli/azure/webapp/log#az_webapp_log_tail)

```azurecli
az webapp log tail
```

Możesz również dołączyć parametr `--logs` z `az webapp up` poleceniem , aby automatycznie otworzyć strumień dzienników podczas wdrażania.

Odśwież aplikację w przeglądarce, aby wygenerować dzienniki konsoli, które zawierają komunikaty opisujące żądania HTTP wysyłane do aplikacji. Jeśli dane wyjściowe nie pojawiają się natychmiast, spróbuj ponownie za 30 sekund.

Pliki dzienników można także sprawdzać w przeglądarce pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Aby w dowolnym momencie zatrzymać przesyłanie strumieniowe dzienników, naciśnij **klawisze Ctrl** + **C** w terminalu.

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania problemów.](configure-language-python.md#troubleshooting)W przeciwnym razie [daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją. Wyszukaj i wybierz **App Services**.

![Przejdź do App Services w Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Wybierz nazwę aplikacji platformy Azure.

![Przejdź do aplikacji w języku Python w App Services w Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Wybranie aplikacji powoduje otwarcie jej **strony Przegląd,** na której można wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Zarządzanie aplikacją w języku Python na stronie Przegląd w Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Menu App Service zawiera różne strony służące do konfigurowania aplikacji.

Masz problemy? Zapoznaj się najpierw z [przewodnikiem rozwiązywania problemów.](configure-language-python.md#troubleshooting)W przeciwnym razie [daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Grupa zasobów ma nazwę, na przykład "appsvc_rg_Linux_CentralUS" w zależności od lokalizacji. Jeśli aplikacja internetowa będzie nadal działać, poniesiesz pewne bieżące koszty (zobacz [cennik usługi App Service ).](https://azure.microsoft.com/pricing/details/app-service/linux/)

Jeśli te zasoby nie będą potrzebne w przyszłości, usuń grupę zasobów, uruchamiając następujące polecenie:

```azurecli
az group delete --no-wait
```

Polecenie używa nazwy grupy zasobów buforowanej w *pliku .azure/config.*

Argument `--no-wait` umożliwia zwrócenie polecenia przed ukończeniem operacji.

Masz problemy? [Daj nam znać](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja internetowa Python (Django) z usługą PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji w języku Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Dodawanie logowania użytkownika do aplikacji internetowej w języku Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie aplikacji w języku Python w kontenerze niestandardowym](tutorial-custom-container.md)
