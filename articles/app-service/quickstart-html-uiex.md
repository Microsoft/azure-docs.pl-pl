---
title: 'Szybki start: tworzenie statycznej aplikacji internetowej HTML'
description: Wd wdrażaj pierwszą aplikację HTML Hello world do Azure App Service w ciągu kilku minut. Wdrażanie przy użyciu usługi Git jest jednym z wielu sposobów wdrażania w App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603d8e642cd2e88beec6ae34094a2c6c43d179ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768914"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Tworzenie statycznej aplikacji internetowej w języku HTML na platformie Azure

W tym przewodniku Szybki start pokazano, jak wdrożyć podstawową witrynę HTML+CSS w <abbr title="Oparta na protokołu HTTP usługa do hostowania aplikacji internetowych, interfejsów API REST i aplikacji mobilnych.">Azure App Service</abbr>. Ten przewodnik Szybki start można wykonać w usłudze [Cloud Shell](../cloud-shell/overview.md), ale można te polecenia uruchomić również lokalnie za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Przygotowywanie środowiska

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

W [Cloud Shell](../cloud-shell/overview.md)utwórz katalog Szybki start, a następnie zmień go na .

```bash
mkdir quickstart

cd $HOME/quickstart
```

Uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Tworzenie aplikacji internetowej

Przejdź do katalogu, który zawiera przykładowy kod, i uruchom polecenie `az webapp up`. **Zamień** `<app-name>` z globalnie unikatową nazwą.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Rozwiązywanie problemów</summary>
<ul>
<li>Jeśli polecenie nie zostanie rozpoznane, upewnij się, że masz zainstalowany interfejs wiersza polecenia platformy Azure zgodnie z opisem <code>az</code> w tesłudze <a href="#1-prepare-your-environment">Prepare your environment (Przygotowywanie środowiska).</a></li>
<li>Zastąp nazwą unikatową na całej <code>&lt;app-name&gt;</code> platformie Azure (prawidłowe <em> znaki to , i <code>a-z</code> <code>0-9</code> <code>-</code> </em> ). Dobrym wzorcem jest użycie kombinacji nazwy firmy i identyfikatora aplikacji.</li>
<li>Argument <code>--sku F1</code> tworzy aplikację internetową w warstwie cenowej Bezpłatna. Pomiń ten argument, aby użyć szybszej warstwy Premium, która wiąże się z godzinowym kosztem.</li>
<li>Argument <code>--html</code> mówi, że cała zawartość folderu jest uważana za zawartość statyczną i wyłącza automatyzację kompilacji.</li>
<li>Opcjonalnie możesz dołączyć argument <code>--location &lt;location-name&gt;</code> , gdzie <code>&lt;location-name&gt;</code> jest dostępnym regionem świadczenia usługi Azure. Listę regionów, które można zezwolić na korzystanie z konta platformy Azure, możesz pobrać, uruchamiając <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> polecenie .</li>
</ul>
</details>

Ukończenie polecenia może potrwać kilka minut. 

<details>
<summary>Co się <code>az webapp up</code> dzieje?</summary>
<p>Polecenie <code>az webapp up</code> wykonuje następujące akcje:</p>
<ul>
<li>Utwórz domyślną grupę zasobów.</li>
<li>Utwórz domyślny App Service planu.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Utwórz aplikację App Service o</a> określonej nazwie.</li>
<li><a href="/azure/app-service/deploy-zip">Spakuj pliki</a> wdrożenia z bieżącego katalogu roboczego do aplikacji.</li>
<li>Podczas działania zapewnia komunikaty dotyczące tworzenia zasobów, rejestrowania i wdrażania pliku ZIP.</li>
</ul>

Po zakończeniu zostaną wyświetlone informacje podobne do następujących:

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

Ta wartość będzie `resourceGroup` potrzebna do [późniejszego oczyszczenia](#6-clean-up-resources) zasobów.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Przejdź do aplikacji

W przeglądarce przejdź do adresu URL aplikacji: `http://<app_name>.azurewebsites.net`.

Strona działa jako aplikacja internetowa usługi Azure App Service.

![Strona główna przykładowej aplikacji](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Aktualizowanie i ponowne wdychaj aplikację

W Cloud Shell **wpisz** , `nano index.html` aby otworzyć edytor tekstów nano. 

W `<h1>` tagu nagłówka zmień "Azure App Service — przykładowa statyczna witryna HTML" na "Azure App Service".

![Plik index.html programu nano](media/quickstart-html/nano-index-html.png)

**Zapisz** zmiany za pomocą polecenia `^O` .

**Zamknij** nano przy użyciu polecenia `^X` .

Ponownie wdychaj aplikację za pomocą `az webapp up` polecenia .

```bash
az webapp up --html
```

Wróć do okna przeglądarki otwartego w kroku **Przejdź do** aplikacji.

**Odśwież** stronę.

![Zaktualizowana strona główna przykładowej aplikacji](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Zarządzanie nową aplikacją platformy Azure

**Przejdź** do [Azure Portal.](https://portal.azure.com) 

**Wyszukaj** i **wybierz pozycję** **App Services**.

![Wybierz App Services w Azure Portal](./media/quickstart-html/portal0.png)

**Wybierz** nazwę aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/quickstart-html/portal1.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Blok usługi App Service w witrynie Azure Portal](./media/quickstart-html/portal2.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji.

<hr/>

## <a name="6-clean-up-resources"></a>6. Czyszczenie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli te zasoby nie będą raczej potrzebne w przyszłości, usuń grupę zasobów, uruchamiając następujące polecenie w usłudze Cloud Shell. Pamiętaj, że nazwa grupy zasobów została wygenerowana automatycznie w kroku [Tworzenie aplikacji internetowej](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Wykonanie tego polecenia może potrwać około minutę.

<hr/>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain-uiex.md)
