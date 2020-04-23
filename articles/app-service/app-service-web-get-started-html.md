---
title: 'Szybki start: tworzenie statycznej aplikacji sieci Web HTML'
description: Wdrażanie pierwszego html Hello World do usługi Azure App Service w ciągu kilku minut. Wdrażanie przy użyciu git, który jest jednym z wielu sposobów wdrażania w usłudze App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 04cd28db52630e9de26e30ef4bf35db983f48b50
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086066"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Tworzenie statycznej aplikacji internetowej w języku HTML na platformie Azure

[Usługa Azure App Service](overview.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingu. W tym samouczku Szybki start przedstawiono sposób wdrażania podstawowej witryny HTML+CSS w usłudze Azure App Service. Ten przewodnik Szybki start można wykonać w usłudze [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), ale można te polecenia uruchomić również lokalnie za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

![Strona główna przykładowej aplikacji](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Pobierz przykład

W usłudze Cloud Shell utwórz katalog Szybki start, a następnie przejdź do niego.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Przejdź do katalogu, który zawiera przykładowy kod, i uruchom polecenie `az webapp up`. W poniższym przykładzie zastąp ciąg <nazwa_aplikacji> unikatową nazwą aplikacji. Zawartość statyczna jest oznaczona flagą. `--html`

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

Polecenie `az webapp up` wykonuje następujące akcje:

- Utwórz domyślną grupę zasobów.

- Utwórz domyślny plan usługi aplikacji.

- Utwórz aplikację z określoną nazwą.

- [Spakuj pliki](https://docs.microsoft.com/azure/app-service/deploy-zip) wdrożenia z bieżącego katalogu roboczego do aplikacji internetowej.

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:

<pre>
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
</pre>

Zanotuj wartość parametru `resourceGroup`. Będziesz jej potrzebować w sekcji [Oczyszczanie zasobów](#clean-up-resources).

## <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

W przeglądarce przejdź do adresu URL aplikacji: `http://<app_name>.azurewebsites.net`.

Strona działa jako aplikacja internetowa usługi Azure App Service.

![Strona główna przykładowej aplikacji](media/app-service-web-get-started-html/hello-world-in-browser-az.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku HTML w usłudze App Service.

## <a name="update-and-redeploy-the-app"></a>Aktualizowanie i ponowne wdrażanie aplikacji

W usłudze Cloud Shell wpisz `nano index.html`, aby otworzyć edytor tekstów nano. W tagu nagłówka `<h1>` zmień tekst „Azure App Service - Sample Static HTML Site” na „Azure App Service”, jak pokazano poniżej.

![Plik index.html programu nano](media/app-service-web-get-started-html/nano-index-html.png)

Zapisz zmiany i zamknij program nano. Użyj polecenia `^O` w celu zapisania i polecenia `^X` w celu zamknięcia programu.

Wdrożysz teraz ponownie tę aplikację za pomocą tego samego polecenia `az webapp up`.

```bash
az webapp up --location westeurope --name <app_name> --html
```

Po zakończeniu wdrożenia przejdź z powrotem do okna przeglądarki otwartego w kroku **przechodzenia do aplikacji**, a następnie odśwież stronę.

![Zaktualizowana strona główna przykładowej aplikacji](media/app-service-web-get-started-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Zarządzanie nową aplikacją platformy Azure

Aby zarządzać utworzoną aplikacją internetową, w [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz pozycję **Usługi aplikacji**. 

![Wybieranie usług aplikacji w witrynie Azure portal](./media/app-service-web-get-started-html/portal0.png)

Na stronie **Usługi aplikacji** wybierz nazwę aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-get-started-html/portal1.png)

Zostanie wyświetlona strona Omówienie aplikacji internetowej. Tutaj możesz wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie.

![Blok usługi App Service w witrynie Azure Portal](./media/app-service-web-get-started-html/portal2.png)

Menu po lewej stronie zawiera różne strony służące do konfigurowania aplikacji.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli te zasoby nie będą raczej potrzebne w przyszłości, usuń grupę zasobów, uruchamiając następujące polecenie w usłudze Cloud Shell. Pamiętaj, że nazwa grupy zasobów została wygenerowana automatycznie w kroku [Tworzenie aplikacji internetowej](#create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Wykonanie tego polecenia może potrwać około minutę.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain.md)
