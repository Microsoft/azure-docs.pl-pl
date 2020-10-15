---
title: Dodawanie strony do interfejsu użytkownika rozwiązania do monitorowania zdalnego — Azure | Microsoft Docs
description: W tym artykule opisano sposób dodawania nowej strony do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 2e54cd0e3c5b9499a3bc1a499ae4cef62c73160e
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070711"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie strony niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego

W tym artykule opisano sposób dodawania nowej strony do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego. W tym artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nową stronę do interfejsu użytkownika sieci Web.

Inne Przewodniki krok po kroku zwiększają ten scenariusz, aby dodać więcej funkcji do dodawanej strony.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz zainstalować następujące oprogramowanie na lokalnym komputerze deweloperskim:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Przygotuj lokalne środowisko programistyczne dla interfejsu użytkownika

Kod interfejsu użytkownika akceleratora rozwiązania do monitorowania zdalnego jest implementowany przy użyciu platformy JavaScript do [reagowania](https://reactjs.org/) . Kod źródłowy można znaleźć w repozytorium do [zdalnego monitorowania WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) .

Aby wprowadzić i przetestować zmiany w interfejsie użytkownika, można uruchomić je na lokalnym komputerze deweloperskim. Opcjonalnie kopia lokalna może połączyć się ze wdrożonym wystąpieniem akceleratora rozwiązania, aby umożliwić mu współpracujące z rzeczywistymi lub symulowanymi urządzeniami.

Aby przygotować lokalne środowisko programistyczne, użyj narzędzia Git, aby sklonować repozytorium [zdalnego monitorowania WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) na maszynę lokalną:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Dodawanie strony

Aby dodać stronę do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe, które definiują stronę, i zmodyfikować niektóre istniejące pliki, aby interfejs użytkownika sieci Web miał świadomość nowej strony.

### <a name="add-the-new-files-that-define-the-page"></a>Dodaj nowe pliki, które definiują stronę

Aby rozpocząć pracę, folder **src/Przewodnik/składniki/strony/od basicpage** zawiera cztery pliki, które definiują prostą stronę:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**Od basicpage. SCSS**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Utwórz nowy folder **src/Components/Pages/example** i skopiuj do niego cztery pliki.

### <a name="add-the-new-page-to-the-web-ui"></a>Dodaj nową stronę do interfejsu użytkownika sieci Web

Aby dodać nową stronę do interfejsu użytkownika sieci Web, wprowadź następujące zmiany w istniejących plikach:

1. Dodaj kontener nowej strony do pliku **src/Components/Pages/index.js** :

    ```js
    export * from './example/basicPage.container';
    ```

1. Obowiązkowe  Dodaj ikonę SVG dla nowej strony. Aby uzyskać więcej informacji, zobacz [WebUI/src/Utilities/Readme. MD](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Możesz użyć istniejącego pliku SVG.

1. Dodaj nazwę strony do pliku tłumaczenia, **Public/Locals/pl/translations.json**. Interfejs użytkownika sieci Web używa [i18next](https://www.i18next.com/) na potrzeby funkcji wielojęzycznych.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Otwórz plik **src/Components/app.js** , który definiuje stronę aplikacji najwyższego poziomu. Dodaj nową stronę do listy importów:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. W tym samym pliku Dodaj nową stronę do `pagesConfig` tablicy. Ustaw `to` adres dla trasy, odwołując się do ikony SVG i wcześniej dodanych tłumaczeń i ustaw `component` dla kontenera strony:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Dodaj nową tablicę struktury nawigacyjnej do `crumbsConfig` tablicy:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Ta przykładowa strona ma tylko jeden pasek nawigacyjny, ale niektóre strony mogą mieć więcej informacji.

Zapisz wszystkie zmiany. Wszystko jest gotowe do uruchomienia internetowego interfejsu użytkownika z dodaną nową stroną.

### <a name="test-the-new-page"></a>Testowanie nowej strony

W wierszu polecenia przejdź do katalogu głównego lokalnej kopii repozytorium i uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić lokalny interfejs użytkownika sieci Web:

```cmd/sh
npm install
npm start
```

Poprzednie polecenie uruchamia interfejs użytkownika lokalnie w `http://localhost:3000/dashboard` .

Bez łączenia lokalnego wystąpienia interfejsu użytkownika sieci Web ze wdrożonym wystąpieniem akceleratora rozwiązania zobaczysz błędy na pulpicie nawigacyjnym. Te błędy nie wpływają na zdolność do testowania nowej strony.

Teraz można edytować kod, gdy lokacja jest uruchamiana lokalnie i automatycznie wyświetlać aktualizację interfejsu użytkownika sieci Web.

## <a name="optional-connect-to-deployed-instance"></a>Obowiązkowe Nawiązywanie połączenia ze wdrożonym wystąpieniem

Opcjonalnie możesz połączyć lokalną kopię interfejsu użytkownika sieci Web z akceleratorem rozwiązania do zdalnego monitorowania w chmurze:

1. Wdróż **podstawowe** wystąpienie akceleratora rozwiązania przy użyciu interfejsu wiersza polecenia **komputerów** . Zanotuj nazwę wdrożenia i poświadczenia podane dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [wdrażanie przy użyciu interfejsu wiersza polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Użyj Azure Portal lub [AZ CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) , aby włączyć dostęp SSH do maszyny wirtualnej, która hostuje mikrousługi w rozwiązaniu. Na przykład:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Dostęp SSH należy włączyć tylko podczas testowania i programowania. W przypadku włączenia protokołu SSH należy [go ponownie wyłączyć najszybciej, jak to możliwe](../security/fundamentals/network-best-practices.md).

1. Użyj Azure Portal lub [AZ CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) , aby znaleźć nazwę i publiczny adres IP maszyny wirtualnej. Na przykład:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Użyj protokołu SSH do nawiązania połączenia z maszyną wirtualną przy użyciu adresu IP z poprzedniego kroku oraz poświadczeń podanych podczas uruchamiania **komputerów** w celu wdrożenia rozwiązania.

1. Aby zezwolić na połączenie lokalnego środowiska użytkownika, uruchom następujące polecenia w powłoce bash na maszynie wirtualnej:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po wyświetleniu polecenia i uruchomieniu witryny sieci Web można rozłączyć się z maszyną wirtualną.

1. W lokalnej kopii repozytorium [WebUI zdalnego monitorowania](https://github.com/Azure/pcs-remote-monitoring-webui) Edytuj plik **ENV** , aby dodać adres URL wdrożonego rozwiązania:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o dostępnych zasobach, które ułatwiają Dostosowywanie interfejsu użytkownika sieci Web w akceleratorze rozwiązania do monitorowania zdalnego.

Po zdefiniowaniu strony, następnym krokiem jest [dodanie usługi niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-service.md) , który pobiera dane do wyświetlenia w interfejsie użytkownika.

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).