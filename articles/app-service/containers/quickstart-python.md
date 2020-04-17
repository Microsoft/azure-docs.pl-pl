---
title: 'Szybki start: tworzenie aplikacji Python linux'
description: Rozpocznij pracę z aplikacjami systemu Linux w usłudze Azure App Service, wdrażając pierwszą aplikację języka Python w kontenerze systemu Linux w usłudze App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: ea5df6a2d4a972638b52587f9edc868f65e25df5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532752"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Szybki start: tworzenie aplikacji języka Python w usłudze Azure App Service w systemie Linux

W tym przewodniku Szybki start można wdrożyć aplikację sieci Web języka Python do [usługi App Service w systemie Linux](app-service-linux-intro.md), wysoce skalowalna, samoładująca się usługa hostingu azure. Lokalny interfejs [wiersza polecenia platformy Azure (CLI)](/cli/azure/install-azure-cli) jest używany na komputerze Mac, Linux lub Windows. Skonfigurowana aplikacja sieci web używa bezpłatnej warstwy usługi App Service, więc w trakcie tego artykułu nie ponosisz żadnych kosztów.

Jeśli wolisz wdrażać aplikacje za pośrednictwem ide, zobacz [Wdrażanie aplikacji języka Python w usłudze App Service z programu Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 jest również obsługiwany)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Narzędzie CLI platformy Azure</a> 2.0.80 lub nowsze. Uruchom polecenie `az --version`, aby sprawdzić wersję.

## <a name="download-the-sample"></a>Pobierz przykład

W oknie terminala uruchom następujące polecenie, aby sklonować przykładową aplikację na komputerze lokalnym. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Następnie przejdź do tego folderu:

```terminal
cd python-docs-hello-world
```

Repozytorium zawiera plik *application.py,* który informuje app service, że kod zawiera aplikację Flask. Aby uzyskać więcej informacji, zobacz [Proces uruchamiania kontenera i dostosowania](how-to-configure-python.md).

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

W oknie terminala użyj poniższych poleceń (odpowiednio dla systemu operacyjnego), aby zainstalować wymagane zależności i uruchomić wbudowany serwer dewelopera. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
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

Otwórz przeglądarkę internetową i przejdź do `http://localhost:5000/`przykładowej aplikacji pod adresem . Aplikacja wyświetla komunikat **Hello World!**.

![Lokalne uruchamianie przykładowej aplikacji języka Python](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

W oknie terminala naciśnij klawisz **Ctrl**+**C,** aby wyjść z serwera sieci Web.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Narzędzie wiersza polecenia platformy Azure zapewnia wiele wygodnych poleceń używanych z terminala lokalnego do inicjowania obsługi administracyjnej zasobów platformy Azure i zarządzania nimi z wiersza polecenia. Polecenia służy do wykonywania tych samych zadań, które można za pośrednictwem witryny Azure portal w przeglądarce. Można również użyć poleceń interfejsu wiersza polecenia w skryptach do automatyzacji procesów zarządzania.

Aby uruchomić polecenia platformy Azure w platformie Azure `az login` CLI, należy najpierw zalogować się przy użyciu polecenia. To polecenie otwiera przeglądarkę w celu zebrania poświadczeń.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Wdrażanie próbki

Polecenie [`az webapp up`](/cli/azure/webapp#az-webapp-up) tworzy aplikację sieci web w usłudze App Service i wdraża kod.

W folderze *python-docs-hello-world* zawierającym przykładowy `az webapp up` kod uruchom następujące polecenie. Zamień `<app-name>` na globalnie unikatową nazwę aplikacji *(prawidłowe znaki to `a-z`, `0-9`i `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```

Argument `--sku F1` tworzy aplikację sieci web w warstwie Bezpłatne ceny. Można pominąć ten argument, aby zamiast tego użyć warstwy premium, która wiąże się z kosztem godzinowym.

Opcjonalnie można dołączyć `-l <location-name>` argument, gdzie `<location_name>` jest region azure, takich jak **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**, i tak dalej. Listę dozwolonych regionów dla konta platformy Azure można pobrać, uruchamiając [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) polecenie.

Całkowite `az webapp up` uruchomienie polecenia może potrwać kilka minut. Podczas pracy wyświetla informacje podobne do poniższego przykładu, gdzie `<app_name>` będzie nazwa podana wcześniej:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://<app-name>.azurewebsites.net
{
  "URL": "http://<app-name>.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "FREE",
  "src_path": "D:\\Examples\\python-docs-hello-world"
}
</pre>

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji w przeglądarce `http://<app-name>.azurewebsites.net`internetowej pod adresem URL .

Przykładowy kod języka Python jest uruchomiony kontener systemu Linux w usłudze App Service przy użyciu wbudowanego obrazu.

![Uruchamianie przykładowej aplikacji języka Python na platformie Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulacje!** Aplikacja Języka Python została wdrożona w usłudze App Service w systemie Linux.

## <a name="redeploy-updates"></a>Ponowne wdrożenie aktualizacji

W ulubionym edytorze kodu otwórz *application.py* i `hello` zaktualizuj tę funkcję w następujący sposób. Ta zmiana `print` dodaje instrukcję do generowania danych wyjściowych rejestrowania, które można pracować z w następnej sekcji. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Zapisz zmiany i zamknij edytor. 

Ponownie rozesz się `az webapp up` ponownie aplikacji za pomocą polecenia ponownie:

```azurecli
az webapp up
```

To polecenie używa wartości, które są buforowane w pliku *.azure/config,* w tym nazwę aplikacji, grupę zasobów i plan usługi App Service.

Po zakończeniu wdrażania przełącz się z `http://<app-name>.azurewebsites.net` powrotem do otwartego okna przeglądarki i odśwież stronę, która powinna wyświetlić zmodyfikowany komunikat:

![Uruchamianie zaktualizowanej przykładowej aplikacji języka Python na platformie Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code udostępnia zaawansowane rozszerzenia dla języka Python i usługi Azure App Service, które upraszczają proces wdrażania aplikacji internetowych języka Python w usłudze App Service. Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji języka Python w usłudze App Service z programu Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Można uzyskać dostęp do dzienników konsoli generowanych z wewnątrz aplikacji i kontenera, w którym działa. Dzienniki obejmują wszelkie `print` dane wyjściowe generowane przy użyciu instrukcji.

Aby przesyłać strumieniowo dzienniki, uruchom następujące polecenie:

```azurecli
az webapp log tail
```

Odśwież aplikację w przeglądarce, aby wygenerować dzienniki konsoli, które powinny zawierać wiersze podobne do następującego tekstu. Jeśli nie widzisz danych wyjściowych od razu, spróbuj ponownie w ciągu 30 sekund.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

Można również sprawdzić pliki dziennika z `https://<app-name>.scm.azurewebsites.net/api/logs/docker`przeglądarki w .

Aby zatrzymać przesyłanie strumieniowe `Ctrl` + `C`dziennika w dowolnym momencie, wpisz .

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją. Wyszukaj i wybierz **pozycję Usługi aplikacji**.

![Przechodzenie do usług aplikacji w witrynie Azure portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Wybierz nazwę aplikacji platformy Azure.

![Przechodzenie do aplikacji języka Python w usługach app services w witrynie Azure portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Zostanie wyświetlona strona omówienia aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Zarządzanie aplikacją języka Python na stronie Przegląd w witrynie Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Menu Usługi aplikacji zawiera różne strony do konfigurowania aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Grupa zasobów ma nazwę taką jak "appsvc_rg_Linux_CentralUS" w zależności od lokalizacji. Jeśli używasz jednostki SKU usługi app service innej niż bezpłatna warstwa F1, te zasoby ponoszą bieżące koszty (zobacz [cennik usługi App Service).](https://azure.microsoft.com/pricing/details/app-service/linux/)

Jeśli nie spodziewasz się, że te zasoby będą potrzebne w przyszłości, `<resource-group-name>` usuń grupę zasobów, uruchamiając `az webapp up` następujące polecenie, zastępując grupę zasobów pokazaną w danych wyjściowych polecenia, taką jak "appsvc_rg_Linux_centralus". Polecenie może potrwać minutę.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Python (Django) aplikacja internetowa z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Dodawanie logowania użytkownika do aplikacji sieci Web języka Python](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Języka Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Samouczek: Uruchamianie aplikacji Python w kontenerze niestandardowym](tutorial-custom-docker-image.md)
