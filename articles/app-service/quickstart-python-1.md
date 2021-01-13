---
title: 'Szybki Start: Tworzenie aplikacji w języku Python w systemie Linux'
description: Rozpocznij pracę z Azure App Service, wdrażając aplikację w języku Python w kontenerze systemu Linux w programie App Service.
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python, devx-track-azurecli
zone_pivot_groups: python-frameworks-01
robots: noindex
ms.openlocfilehash: ed2bd3f3bab81f6be56508a203600ec479cc20b6
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134487"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Szybki Start: Tworzenie aplikacji w języku Python w Azure App Service w systemie Linux

W tym przewodniku szybki start wdrożono aplikację sieci Web w języku Python w celu [App Service w systemie Linux](overview.md#app-service-on-linux)— wysoce skalowalna, samoobsługowa usługa hostingu sieci Web na platformie Azure. Za pomocą lokalnego [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/install-azure-cli) na komputerze Mac, Linux lub Windows można wdrożyć przykład za pomocą kolby lub platform Django. Skonfigurowana aplikacja internetowa korzysta z bezpłatnej warstwy App Service, więc nie ponosisz żadnych kosztów w ramach tego artykułu.

**Definicje typowych terminów** można znaleźć w artykule [terminologia platformy Azure](/azure/developer/python/cloud-azure-terminology?toc=/azure/app-service/toc.json).

> [!TIP]
> Jeśli wolisz wdrażać aplikacje za pośrednictwem środowiska IDE, zobacz **[wdrażanie aplikacji w języku Python do App Service z Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01)**.

## <a name="set-up-your-initial-environment"></a>Konfigurowanie środowiska początkowego

1. Posiadanie konta platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Zainstaluj środowisko Python w wersji <a href="https://www.python.org/downloads/" target="_blank">3,6 lub nowszej</a>.
1. Zainstaluj <a href="/cli/azure/install-azure-cli" target="_blank">interfejs wiersza polecenia platformy Azure</a> 2.0.80 lub nowszy, za pomocą którego uruchamiasz polecenia w dowolnej powłoce, aby udostępnić i skonfigurować zasoby platformy Azure.

Otwórz okno terminalu i sprawdź, czy wersja języka Python to 3,6 lub nowszego:

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

Sprawdź, czy wersja interfejsu wiersza polecenia platformy Azure to 2.0.80 lub nowszy:

```azurecli
az --version
```

Następnie zaloguj się do platformy Azure za pomocą interfejsu wiersza polecenia:

```azurecli
az login
```

To polecenie umożliwia otwarcie przeglądarki w celu zebrania poświadczeń. Po zakończeniu wykonywania polecenia zostaną wyświetlone dane wyjściowe JSON zawierające informacje o Twoich subskrypcjach.

Po zalogowaniu możesz uruchamiać polecenia platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, aby pracować z zasobami w ramach subskrypcji.

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clone-the-sample"></a>Klonowanie przykładu

Sklonuj przykładowe repozytorium przy użyciu następującego polecenia i przejdź do przykładowego folderu. ([Zainstaluj program git](https://git-scm.com/downloads) , jeśli nie masz już usługi Git).

::: zone pivot="python-framework-flask"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Następnie przejdź do tego folderu:

```terminal
cd python-docs-hello-world
```
::: zone-end

::: zone pivot="python-framework-django"
```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-django
```

Następnie przejdź do tego folderu:

```terminal
cd python-docs-hello-django
```
::: zone-end

Przykład zawiera kod specyficzny dla platformy, który Azure App Service rozpoznawany podczas uruchamiania aplikacji. Aby uzyskać więcej informacji, zobacz temat [proces uruchamiania kontenera](configure-language-python.md#container-startup-process).

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="deploy-the-sample"></a>Wdróż przykład

Wdróż kod w folderze lokalnym (*Python-docs-Hello-World*) przy użyciu `az webapp up` polecenia:

```azurecli
az webapp up --sku B1 --name <app-name>
```

- Jeśli `az` polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem w artykule [Konfigurowanie początkowego środowiska](#set-up-your-initial-environment).
- Jeśli `webapp` polecenie nie zostanie rozpoznane, ponieważ wersja interfejsu wiersza polecenia platformy Azure to 2.0.80 lub nowszego. Jeśli nie, [Zainstaluj najnowszą wersję](/cli/azure/install-azure-cli).
- Zamień na `<app_name>` nazwę, która jest unikatowa na całym systemie Azure (*prawidłowe znaki to `a-z` , `0-9` i `-`*). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.
- `--sku B1`Argument tworzy aplikację sieci Web w warstwie cenowej Basic, która wiąże się z niewielkim kosztem godzinowym. Pomiń ten argument, aby użyć szybszej warstwy Premium.
- Opcjonalnie możesz dołączyć argument, `--location <location-name>` gdzie `<location_name>` jest dostępny region platformy Azure. Możesz pobrać listę dozwolonych regionów dla Twojego konta platformy Azure, uruchamiając [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations) polecenie.
- Jeśli widzisz błąd, "nie można automatycznie wykryć stosu środowiska uruchomieniowego aplikacji", upewnij się, że uruchomiono polecenie w folderze *Python-docs-Hello-World* (*) lub w folderze *Python-docs-Hello-Django* (Django), który zawiera plik *requirements.txt* . (Zobacz [Rozwiązywanie problemów z wykrywaniem automatyczne przy użyciu AZ webapp up](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md) (GitHub).)

Wykonanie polecenia może potrwać kilka minut. W trakcie korzystania z programu są dostępne komunikaty dotyczące tworzenia grupy zasobów, planu App Service i aplikacji hostingu, konfigurowania rejestrowania, a następnie wykonywania wdrożenia ZIP. Następnie zostanie wyświetlony komunikat "można uruchomić aplikację pod adresem http:// &lt; App-Name &gt; . azurewebsites.NET", który jest adresem URL aplikacji na platformie Azure.

![Przykładowe dane wyjściowe polecenia AZ webapp up](./media/quickstart-python/az-webapp-up-output.png)

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji w przeglądarce sieci Web pod adresem URL `http://<app-name>.azurewebsites.net` . Początkowo uruchamianie aplikacji trwa kilka chwil.

Przykładowy kod w języku Python używa kontenera systemu Linux w App Service przy użyciu wbudowanego obrazu.

![Uruchamianie przykładowej aplikacji w języku Python na platformie Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulacje!** Twoja aplikacja w języku Python została wdrożona w celu App Service.

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej 

::: zone pivot="python-framework-flask"
1. Upewnij się, że jesteś w folderze *Python-docs-Hello-World* . 

1. Utwórz środowisko wirtualne i Zainstaluj zależności:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Jeśli wystąpi "[errno 2] nie ma takiego pliku lub katalogu:" requirements.txt ". Upewnij się, że jesteś w folderze *Python-docs-Hello-World* .

1. Uruchom serwer programistyczny.

    ```terminal  
    flask run
    ```
    
    Domyślnie na serwerze założono, że moduł wprowadzania aplikacji jest w *App.py*, jak użyty w przykładzie. (Jeśli używasz innej nazwy modułu, ustaw `FLASK_APP` zmienną środowiskową na tę nazwę).

1. Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji pod adresem `http://localhost:5000/` . Aplikacja wyświetla komunikat **Hello, World!**.

    ![Uruchamianie przykładowej aplikacji w języku Python lokalnie](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. W oknie terminalu naciśnij klawisz **Ctrl**, + Aby wyjść **z** serwera deweloperskiego.
::: zone-end

::: zone pivot="python-framework-django"
1. Upewnij się, że jesteś w folderze *Python-docs-Hello-Django* . 

1. Utwórz środowisko wirtualne i Zainstaluj zależności:

    [!include [virtual environment setup](../../includes/app-service-quickstart-python-venv.md)]

    Jeśli wystąpi "[errno 2] nie ma takiego pliku lub katalogu:" requirements.txt ". Upewnij się, że jesteś w folderze *Python-docs-Hello-Django* .
    
1. Uruchom serwer programistyczny.

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python3 manage.py runserver
    ```

    # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

    ```powershell
    py -3 manage.py runserver
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -3 manage.py runserver
    ```

    ---    

1. Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji pod adresem `http://localhost:8000/` . Aplikacja wyświetla komunikat **Hello, World!**.

    ![Uruchamianie przykładowej aplikacji w języku Python lokalnie](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)
    
1. W oknie terminalu naciśnij klawisz **Ctrl**, + Aby wyjść **z** serwera deweloperskiego.
::: zone-end

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="redeploy-updates"></a>Wdróż ponownie aktualizacje

W tej sekcji zostanie wprowadzona niewielka zmiana kodu, a następnie ponownie wdrożono kod na platformie Azure. Zmiana kodu zawiera `print` instrukcję generującą dane wyjściowe rejestrowania, z którymi pracujesz w następnej sekcji.

::: zone pivot="python-framework-flask"
Otwórz *App.py* w edytorze i zaktualizuj funkcję, `hello` aby pasowała do poniższego kodu. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello, Azure!"
```
::: zone-end
::: zone pivot="python-framework-django"
Otwórz *powitanie/widoki. PR* w edytorze i zaktualizuj `hello` funkcję w celu dopasowania do poniższego kodu.

```python
def hello(request):
    print("Handling request to home page.")
    return HttpResponse("Hello, Azure!")
```
::: zone-end
    
Zapisz zmiany, a następnie ponownie Wdróż aplikację przy użyciu `az webapp up` polecenia:

```azurecli
az webapp up
```

To polecenie używa wartości, które są buforowane lokalnie w pliku *. Azure/config* , łącznie z nazwą aplikacji, grupą zasobów i planem App Service.

Po zakończeniu wdrażania Przełącz się z powrotem do okna przeglądarki otwartego na `http://<app-name>.azurewebsites.net` . Odśwież stronę, która powinna wyświetlać zmodyfikowany komunikat:

![Uruchamianie zaktualizowanej przykładowej aplikacji w języku Python na platformie Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

> [!TIP]
> Visual Studio Code zapewnia zaawansowane rozszerzenia dla języka Python i Azure App Service, co upraszcza proces wdrażania aplikacji sieci Web w języku Python w App Service. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji w języku Python do App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Można uzyskać dostęp do dzienników konsoli wygenerowanych z wewnątrz aplikacji i kontenera, w którym działa. Dzienniki zawierają wszystkie dane wyjściowe wygenerowane przy użyciu `print` instrukcji.

Aby przesłać strumieniowo dzienniki, uruchom polecenie [AZ webapp log tail](/cli/azure/webapp/log?view=azure-cli-latest&preserve-view=true#az_webapp_log_tail) :

```azurecli
az webapp log tail
```

Możesz również dodać `--logs` parametr za pomocą polecenia, `az webapp up` Aby automatycznie otworzyć strumień dziennika przy wdrożeniu.

Odśwież aplikację w przeglądarce, aby generować dzienniki konsoli, w tym komunikaty opisujące żądania HTTP do aplikacji. Jeśli dane wyjściowe nie pojawiają się natychmiast, spróbuj ponownie za 30 sekund.

Pliki dzienników można także sprawdzać w przeglądarce pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie, naciśnij klawisz **Ctrl** + **C** w terminalu.

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją. Wyszukaj i wybierz **App Services**.

![Przejdź do App Services w Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Wybierz nazwę aplikacji platformy Azure.

![Przejdź do aplikacji w języku Python w App Services w Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Wybranie aplikacji spowoduje otwarcie jej strony **Przegląd** , na której można wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Zarządzaj swoją aplikacją w języku Python na stronie Przegląd w Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Menu App Service zawiera różne strony służące do konfigurowania aplikacji.

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Grupa zasobów ma nazwę "appsvc_rg_Linux_CentralUS" w zależności od lokalizacji. Jeśli aplikacja sieci Web jest uruchomiona, zostaną naliczone pewne bieżące koszty (zobacz [cennik App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów, uruchamiając następujące polecenie:

```azurecli
az group delete --no-wait
```

Polecenie używa nazwy grupy zasobów zapisanej w pamięci podręcznej w pliku *. Azure/config* .

`--no-wait`Argument umożliwia zwrócenie polecenia przed ukończeniem operacji.

[Masz problemy? Daj nam znać.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja sieci Web języka Python (Django) z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Dodawanie logowania użytkownika do aplikacji sieci Web w języku Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie aplikacji języka Python w kontenerze niestandardowym](tutorial-custom-container.md)
