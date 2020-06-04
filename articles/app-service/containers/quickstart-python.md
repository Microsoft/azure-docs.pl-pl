---
title: 'Szybki Start: Tworzenie aplikacji w języku Python dla systemu Linux'
description: Zacznij korzystać z aplikacji systemu Linux na Azure App Service, wdrażając pierwszą aplikację w języku Python w kontenerze systemu Linux w App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
ms.openlocfilehash: 023bd8a3d53665a797848c6a9053ccd26f6015ce
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344734"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Szybki Start: Tworzenie aplikacji w języku Python w Azure App Service w systemie Linux

W tym przewodniku szybki start wdrożono aplikację sieci Web w języku Python w celu [App Service w systemie Linux](app-service-linux-intro.md)— wysoce skalowalna, samoobsługowa usługa hostingu sieci Web na platformie Azure. Używasz lokalnego [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/install-azure-cli) na komputerze Mac, Linux lub Windows. Skonfigurowana aplikacja internetowa korzysta z bezpłatnej warstwy App Service, więc nie ponosisz żadnych kosztów w ramach tego artykułu.

Jeśli wolisz wdrażać aplikacje za pośrednictwem środowiska IDE, zobacz [wdrażanie aplikacji w języku Python do App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (obsługiwane jest również środowisko Python 3,6)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Interfejs wiersza polecenia platformy Azure</a> 2.0.80 lub wyższy. Uruchom polecenie `az --version`, aby sprawdzić wersję.

## <a name="download-the-sample"></a>Pobieranie przykładu

W oknie terminalu uruchom następujące polecenie, aby sklonować przykładową aplikację na komputer lokalny. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Następnie przejdź do tego folderu:

```terminal
cd python-docs-hello-world
```

Repozytorium zawiera plik *Application.py* , który informuje App Service, że kod zawiera aplikację z kolbą. Aby uzyskać więcej informacji, zobacz [Proces uruchamiania kontenera i dostosowania](how-to-configure-python.md).

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

W oknie terminalu Użyj poleceń poniżej (odpowiednio dla danego systemu operacyjnego), aby zainstalować wymagane zależności i uruchomić wbudowany serwer programistyczny. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

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

Otwórz przeglądarkę internetową i przejdź do przykładowej aplikacji pod adresem `http://localhost:5000/` . Aplikacja wyświetli komunikat **Hello World!**.

![Uruchamianie przykładowej aplikacji w języku Python lokalnie](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

W oknie terminalu naciśnij klawisz **Ctrl**, + Aby wyjść**z** serwera sieci Web.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Interfejs wiersza polecenia platformy Azure udostępnia wiele wygodnych poleceń, które są używane z lokalnego terminalu do aprowizacji zasobów platformy Azure i zarządzania nimi z wiersza poleceń. Polecenia służą do wykonywania tych samych zadań, które można wykonać za pomocą Azure Portal w przeglądarce. Do automatyzowania procesów zarządzania można także używać poleceń interfejsu wiersza polecenia w skryptach.

Aby uruchomić polecenia platformy Azure w interfejsie wiersza polecenia platformy Azure, musisz najpierw zalogować się przy użyciu `az login` polecenia. To polecenie umożliwia otwarcie przeglądarki w celu zebrania poświadczeń.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Wdróż przykład

[`az webapp up`](/cli/azure/webapp#az-webapp-up)Polecenie tworzy aplikację sieci Web na App Service i wdraża swój kod.

W folderze *Python-docs-Hello-World* , który zawiera przykładowy kod, uruchom następujące `az webapp up` polecenie. Zamień na `<app-name>` globalnie unikatową nazwę aplikacji (*prawidłowe znaki to `a-z` , `0-9` i `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```

`--sku F1`Argument tworzy aplikację sieci Web w warstwie cenowej bezpłatna. Możesz pominąć ten argument, aby zamiast tego użyć warstwy Premium, która ponosi koszt godzinowy.

Opcjonalnie możesz dołączyć argument `-l <location-name>` `<location_name>` , gdzie jest regionem świadczenia usługi Azure, takim jak **środkowe**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**i tak dalej. Możesz pobrać listę dozwolonych regionów dla Twojego konta platformy Azure, uruchamiając [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) polecenie.

`az webapp up`Wykonanie polecenia może potrwać kilka minut. W trakcie działania są wyświetlane informacje podobne do poniższego przykładu, gdzie `<app-name>` zostanie podana wcześniej Nazwa:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '&lt;app-name&gt;' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name&gt;.azurewebsites.net
{
  "URL": "http://&lt;app-name&gt;.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "&lt;app-name&gt;",
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

Przejdź do wdrożonej aplikacji w przeglądarce sieci Web pod adresem URL `http://<app-name>.azurewebsites.net` .

Przykładowy kod w języku Python używa kontenera systemu Linux w App Service przy użyciu wbudowanego obrazu.

![Uruchamianie przykładowej aplikacji w języku Python na platformie Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Gratulacje!** Aplikacja w języku Python została wdrożona w celu App Service w systemie Linux.

## <a name="redeploy-updates"></a>Wdróż ponownie aktualizacje

W ulubionym edytorze kodu Otwórz *Application.py* i zaktualizuj `hello` funkcję w następujący sposób. Ta zmiana powoduje dodanie `print` instrukcji w celu wygenerowania danych wyjściowych rejestrowania, które są używane w następnej sekcji. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Zapisz zmiany i zamknij edytor. 

Ponownie Wdróż aplikację przy użyciu `az webapp up` polecenia:

```azurecli
az webapp up
```

To polecenie używa wartości przechowywanych w pamięci podręcznej w pliku *. Azure/config* , łącznie z nazwą aplikacji, grupą zasobów i planem App Service.

Po zakończeniu wdrażania Przełącz się z powrotem do okna przeglądarki Otwórz `http://<app-name>.azurewebsites.net` i Odśwież stronę, która powinna wyświetlać zmodyfikowany komunikat:

![Uruchamianie zaktualizowanej przykładowej aplikacji w języku Python na platformie Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code zapewnia zaawansowane rozszerzenia dla języka Python i Azure App Service, co upraszcza proces wdrażania aplikacji sieci Web w języku Python w App Service. Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji w języku Python do App Service z Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strumieniowe przesyłanie dzienników

Można uzyskać dostęp do dzienników konsoli wygenerowanych z wewnątrz aplikacji i kontenera, w którym działa. Dzienniki zawierają wszystkie dane wyjściowe wygenerowane przy użyciu `print` instrukcji.

Aby przesłać strumieniowo dzienniki, uruchom następujące polecenie:

```azurecli
az webapp log tail
```

Odśwież aplikację w przeglądarce, aby generować dzienniki konsoli, które powinny zawierać wiersze podobne do poniższego tekstu. Jeśli dane wyjściowe nie są widoczne natychmiast, spróbuj ponownie za 30 sekund.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

Możesz również sprawdzić pliki dziennika z przeglądarki pod adresem `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Aby zatrzymać przesyłanie strumieniowe dzienników w dowolnym momencie, wpisz polecenie `Ctrl` + `C` .

## <a name="manage-the-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, aby zarządzać utworzoną aplikacją. Wyszukaj i wybierz **App Services**.

![Przejdź do App Services w Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Wybierz nazwę aplikacji platformy Azure.

![Przejdź do aplikacji w języku Python w App Services w Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Zostanie wyświetlona strona omówienia aplikacji. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Zarządzaj swoją aplikacją w języku Python na stronie Przegląd w Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

Menu App Service zawiera różne strony służące do konfigurowania aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Grupa zasobów ma nazwę "appsvc_rg_Linux_CentralUS" w zależności od lokalizacji. Jeśli używasz jednostki SKU App Service innej niż bezpłatna warstwa F1, te zasoby ponoszą bieżące koszty (zobacz [App Service Cennik](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów, uruchamiając następujące polecenie, zastępując `<resource-group-name>` ją grupą zasobów pokazaną w danych wyjściowych `az webapp up` polecenia, na przykład "appsvc_rg_Linux_centralus". Wykonanie polecenia może potrwać minutę.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja sieci Web języka Python (Django) z PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Dodawanie logowania użytkownika do aplikacji sieci Web w języku Python](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji języka Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Samouczek: uruchamianie aplikacji języka Python w kontenerze niestandardowym](tutorial-custom-docker-image.md)
