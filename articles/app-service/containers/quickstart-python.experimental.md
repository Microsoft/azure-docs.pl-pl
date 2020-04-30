---
title: 'Szybki Start: Tworzenie aplikacji w języku Python w systemie Linux — Azure App Service'
description: Wdróż swoją pierwszą aplikację Hello World w języku Python w usłudze Azure App Service w systemie Linux w ciągu kilku minut.
services: app-service\web
documentationcenter: ''
author: msangapu-msft
ms.topic: quickstart
ms.date: 1/14/2020
ms.author: msangapu
ms.custom: seo-python-october2019
experimental: false
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 68dc36ce96737fe8395280c3a833e359084d2fee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78246854"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Szybki Start: Tworzenie aplikacji w języku Python w Azure App Service w systemie Linux

W tym przewodniku szybki start wdrożono aplikację sieci Web w języku Python w celu [App Service w systemie Linux](app-service-linux-intro.md)— wysoce skalowalna, samoobsługowa usługa hostingu sieci Web na platformie Azure. Używasz lokalnego [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/install-azure-cli) na komputerze Mac, Linux lub Windows. Skonfigurowana aplikacja internetowa korzysta z bezpłatnej warstwy App Service, więc nie ponosisz żadnych kosztów w ramach tego artykułu.

Jeśli wolisz wdrażać aplikacje za pośrednictwem środowiska IDE, zobacz [wdrażanie aplikacji w języku Python do App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (obsługiwane jest również środowisko Python 3,6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Interfejs wiersza polecenia platformy Azure</a>

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminalu uruchom następujące polecenie, aby sklonować przykładową aplikację na komputer lokalny. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Następnie przejdź do tego folderu:

```terminal
cd python-docs-hello-world
```

Repozytorium zawiera plik *Application.py* , który informuje App Service, że kod zawiera aplikację z kolbą. Aby uzyskać więcej informacji, zobacz [Proces uruchamiania kontenera i dostosowania](how-to-configure-python.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Interfejs wiersza polecenia platformy Azure udostępnia wiele wygodnych poleceń, które są używane z lokalnego terminalu do aprowizacji zasobów platformy Azure i zarządzania nimi z wiersza poleceń. Polecenia służą do wykonywania tych samych zadań, które można wykonać za pomocą Azure Portal w przeglądarce. Do automatyzowania procesów zarządzania można także używać poleceń interfejsu wiersza polecenia w skryptach.

Aby uruchomić polecenia platformy Azure w interfejsie wiersza polecenia platformy Azure, musisz najpierw zalogować się przy użyciu `az login` polecenia. To polecenie umożliwia otwarcie przeglądarki w celu zebrania poświadczeń.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Wdróż przykład

[`az webapp up`](/cli/azure/webapp#az-webapp-up) Polecenie tworzy aplikację sieci web na App Service i wdraża swój kod.

W folderze *Python-docs-Hello-World* , który zawiera przykładowy kod, uruchom następujące `az webapp up` polecenie. Zamień `<app-name>` na globalnie unikatową nazwę aplikacji (*prawidłowe znaki to `a-z`, `0-9`i `-` *). Zastąp `<location-name>` również regionem platformy Azure, takim jak **środkowe**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**i tak dalej. (Możesz pobrać listę dozwolonych regionów dla Twojego konta platformy Azure, uruchamiając [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) polecenie).


```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji w przeglądarce sieci Web pod adresem URL `http://<app-name>.azurewebsites.net`.

Przykładowy kod w języku Python używa kontenera systemu Linux w App Service przy użyciu wbudowanego obrazu.

![Uruchamianie przykładowej aplikacji w języku Python na platformie Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulacje!** Aplikacja w języku Python została wdrożona w celu App Service w systemie Linux.

## <a name="run-the-sample-app-locally"></a>Uruchamianie przykładowej aplikacji lokalnie

W oknie terminalu Użyj poleceń poniżej (odpowiednio dla danego systemu operacyjnego), aby zainstalować wymagane zależności i uruchomić wbudowany serwer programistyczny. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji pod adresem `http://localhost:5000/`. Aplikacja wyświetli komunikat **Hello World!**.

![Uruchamianie przykładowej aplikacji w języku Python lokalnie](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

W oknie terminalu naciśnij klawisz **Ctrl**+**, aby wyjść z serwera** sieci Web.

## <a name="redeploy-updates"></a>Wdróż ponownie aktualizacje

W ulubionym edytorze kodu Otwórz *Application.py* i Zmień `return` instrukcję w ostatnim wierszu w celu dopasowania do poniższego kodu. Ta `print` instrukcja jest uwzględniona w tym miejscu w celu wygenerowania danych wyjściowych rejestrowania, które są używane w następnej sekcji. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Zapisz zmiany i zamknij edytor. 

Wdróż ponownie aplikację przy użyciu następującego `az webapp up` polecenia, używając tego samego polecenia, które zostało użyte do wdrożenia aplikacji po raz pierwszy, zastępując `<app-name>` i `<location-name>` przy użyciu tych samych nazw, które były używane wcześniej. 

```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Po zakończeniu wdrażania Przełącz się z powrotem do okna przeglądarki Otwórz `http://<app-name>.azurewebsites.net` i Odśwież stronę, która powinna wyświetlać zmodyfikowany komunikat:

![Uruchamianie zaktualizowanej przykładowej aplikacji w języku Python na platformie Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code zapewnia zaawansowane rozszerzenia dla języka Python i Azure App Service, co upraszcza proces wdrażania aplikacji sieci Web w języku Python w App Service. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji w języku Python do App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Można uzyskać dostęp do dzienników konsoli wygenerowanych z wewnątrz aplikacji i kontenera, w którym działa. Dzienniki zawierają wszystkie dane wyjściowe wygenerowane przy użyciu `print` instrukcji.

Najpierw Włącz rejestrowanie kontenerów, uruchamiając następujące polecenie w terminalu, zastępując `<app-name>` je nazwą aplikacji i `<resource-group-name>` nazwą grupy zasobów wyświetlaną w danych wyjściowych użytego `az webapp up` polecenia (na przykład "appsvc_rg_Linux_centralus"):

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Po włączeniu rejestrowania kontenera Uruchom następujące polecenie, aby wyświetlić strumień dziennika:

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Odśwież aplikację w przeglądarce, aby generować dzienniki konsoli, które powinny zawierać wiersze podobne do poniższego tekstu. Jeśli dane wyjściowe nie są widoczne natychmiast, spróbuj ponownie za 30 sekund.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Możesz również sprawdzić pliki dziennika z przeglądarki pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie `Ctrl` + `C`, wpisz polecenie.

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją. Wyszukaj i wybierz **App Services**.

![Przejdź do App Services w Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Wybierz nazwę aplikacji platformy Azure.

![Przejdź do aplikacji w języku Python w App Services w Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Zostanie wyświetlona strona omówienia aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Zarządzaj swoją aplikacją w języku Python na stronie Przegląd w Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Menu App Service zawiera różne strony służące do konfigurowania aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Grupa zasobów ma nazwę "appsvc_rg_Linux_CentralUS" w zależności od lokalizacji. Jeśli używasz jednostki SKU App Service innej niż bezpłatna warstwa F1, te zasoby spowodują naliczanie bieżących kosztów.

Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów, uruchamiając następujące polecenie, zastępując `<resource-group-name>` ją grupą zasobów pokazaną w danych wyjściowych `az webapp up` polecenia, na przykład "appsvc_rg_Linux_centralus". Wykonanie polecenia może potrwać minutę.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja sieci Web języka Python (Django) z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie aplikacji języka Python w kontenerze niestandardowym](tutorial-custom-docker-image.md)
