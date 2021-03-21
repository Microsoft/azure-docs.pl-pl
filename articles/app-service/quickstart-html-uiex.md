---
title: 'Szybki Start: Tworzenie statycznej aplikacji sieci Web w formacie HTML'
description: Wdróż swoje pierwsze Hello world HTML do Azure App Service w ciągu kilku minut. Wdrażasz program przy użyciu narzędzia Git, który jest jednym z wielu sposobów wdrażania w App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 538d414ce606b944fcea7adbb1c817386e13090e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178579"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Tworzenie statycznej aplikacji internetowej w języku HTML na platformie Azure

Ten przewodnik Szybki Start przedstawia sposób wdrażania podstawowej witryny HTML + CSS w programie <abbr title="Usługa oparta na protokole HTTP do hostowania aplikacji sieci Web, interfejsów API REST i aplikacji zaplecza mobilnego.">Azure App Service</abbr>. Ten przewodnik Szybki start można wykonać w usłudze [Cloud Shell](../cloud-shell/overview.md), ale można te polecenia uruchomić również lokalnie za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

W [Cloud Shell](../cloud-shell/overview.md)Utwórz katalog szybkiego startu, a następnie przejdź do niego.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Tworzenie aplikacji sieci Web

Przejdź do katalogu, który zawiera przykładowy kod, i uruchom polecenie `az webapp up`. **Zamień** `<app-name>` z globalnie unikatową nazwą.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Rozwiązywanie problemów</summary>
<ul>
<li>Jeśli <code>az</code> polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem w artykule <a href="#1-prepare-your-environment">Przygotowywanie środowiska</a>.</li>
<li>Zamień na <code>&lt;app-name&gt;</code> nazwę, która jest unikatowa na całym systemie Azure ( <em> prawidłowe znaki to <code>a-z</code> , <code>0-9</code> i <code>-</code> </em> ). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.</li>
<li><code>--sku F1</code>Argument tworzy aplikację sieci Web w warstwie cenowej bezpłatna. Pomiń ten argument, aby użyć szybszej warstwy Premium, która wiąże się z godziną.</li>
<li><code>--html</code>Argument mówi, że cała zawartość folderu jest traktowana jako zawartość statyczna i wyłączyć automatyzację kompilacji.</li>
<li>Opcjonalnie możesz dołączyć argument, <code>--location &lt;location-name&gt;</code> gdzie <code>&lt;location-name&gt;</code> jest dostępny region platformy Azure. Możesz pobrać listę dozwolonych regionów dla Twojego konta platformy Azure, uruchamiając <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> polecenie.</li>
</ul>
</details>

Wykonanie polecenia może potrwać kilka minut. 

<details>
<summary>Co <code>az webapp up</code> robi?</summary>
<p>Polecenie <code>az webapp up</code> wykonuje następujące akcje:</p>
<ul>
<li>Utwórz domyślną grupę zasobów.</li>
<li>Utwórz domyślny plan App Service.</li>
<li><a href="/cli/azure/webapp#az-webapp-create">Utwórz aplikację App Service</a> o podanej nazwie.</li>
<li><a href="/azure/app-service/deploy-zip">Spakuj pliki</a> wdrożenia z bieżącego katalogu roboczego do aplikacji.</li>
<li>W trakcie działania są dostępne komunikaty dotyczące tworzenia zasobów, rejestrowania i wdrażania pliku ZIP.</li>
</ul>

Po zakończeniu wyświetla informacje podobne do następującego przykładu:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

Będzie potrzebna `resourceGroup` wartość w celu późniejszego [oczyszczenia zasobów](#6-clean-up-resources) .

<hr/>

## <a name="3-browse-to-the-app"></a>3. Przejdź do aplikacji

W przeglądarce przejdź do adresu URL aplikacji: `http://<app_name>.azurewebsites.net`.

Strona działa jako aplikacja internetowa usługi Azure App Service.

![Strona główna przykładowej aplikacji](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Zaktualizuj i Wdróż ponownie aplikację

W Cloud Shell **wpisz** polecenie, `nano index.html` Aby otworzyć Edytor tekstu nano. 

W `<h1>` znaczniku nagłówka Zmień "Azure App Service przykładową statyczną witrynę HTML" na "Azure App Service".

![Plik index.html programu nano](media/quickstart-html/nano-index-html.png)

**Zapisz** zmiany za pomocą polecenia `^O` .

**Wyjdź** z systemu nano przy użyciu polecenia `^X` .

Ponownie Wdróż aplikację za pomocą `az webapp up` polecenia.

```bash
az webapp up --html
```

Przełącz się z powrotem do okna przeglądarki otwartego w kroku **Przejdź do aplikacji** .

**Odśwież** stronę.

![Zaktualizowana strona główna przykładowej aplikacji](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Zarządzanie nową aplikacją platformy Azure

**Przejdź** do [Azure Portal](https://portal.azure.com)., 

**Wyszukaj** i **Wybierz** **App Services**.

![Wybierz App Services w Azure Portal](./media/quickstart-html/portal0.png)

**Wybierz** nazwę aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/quickstart-html/portal1.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Blok usługi App Service w witrynie Azure Portal](./media/quickstart-html/portal2.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji.

<hr/>

## <a name="6-clean-up-resources"></a>6. Wyczyść zasoby

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli te zasoby nie będą raczej potrzebne w przyszłości, usuń grupę zasobów, uruchamiając następujące polecenie w usłudze Cloud Shell. Pamiętaj, że nazwa grupy zasobów została wygenerowana automatycznie w kroku [Tworzenie aplikacji internetowej](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Wykonanie tego polecenia może potrwać około minutę.

<hr/>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain-uiex.md)
