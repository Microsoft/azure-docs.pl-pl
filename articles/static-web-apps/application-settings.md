---
title: Konfigurowanie ustawień aplikacji dla Web Apps statycznej platformy Azure
description: Dowiedz się, jak skonfigurować ustawienia aplikacji dla usługi Azure static Web Apps
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 00502c97e3fc06972c27cedf565d77b52f558795
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844816"
---
# <a name="configure-application-settings-for-azure-static-web-apps-preview"></a>Konfigurowanie ustawień aplikacji dla usługi Azure static Web Apps Preview

Ustawienia aplikacji przechowują ustawienia konfiguracji dla wartości, które mogą się zmieniać, takich jak parametry połączenia bazy danych. Dodanie ustawień aplikacji pozwala modyfikować dane wejściowe konfiguracji w aplikacji bez konieczności zmiany kodu aplikacji.

Ustawienia aplikacji są następujące:

- Zaszyfrowane w spoczynku
- Skopiowane do środowisk [przejściowych](review-publish-pull-requests.md) i produkcyjnych

Ustawienia aplikacji są również czasami określane jako zmienne środowiskowe.

> [!IMPORTANT]
> Ustawienia aplikacji opisane w tym artykule mają zastosowanie tylko do interfejsu API zaplecza statycznej aplikacji sieci Web platformy Azure.
>
> Aby uzyskać informacje na temat używania zmiennych środowiskowych z aplikacją sieci Web frontonu, zapoznaj się z dokumentacją dla [struktury JavaScript](#javascript-frameworks-and-libraries) lub [generatora witryn statycznych](#static-site-generators).

## <a name="prerequisites"></a>Wymagania wstępne

- Statyczna aplikacja Web Apps platformy Azure
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="types-of-application-settings"></a>Typy ustawień aplikacji

Istnieją zwykle dwa aspekty dotyczące statycznej aplikacji Web Apps platformy Azure. Pierwsza to aplikacja internetowa lub zawartość statyczna, która jest reprezentowana przez HTML, CSS, JavaScript i obrazy. Drugim jest interfejs API zaplecza, który jest obsługiwany przez aplikację Azure Functions.

W tym artykule pokazano, jak zarządzać ustawieniami aplikacji dla interfejsu API zaplecza w Azure Functions.

Ustawień aplikacji opisanych w tym artykule nie można używać ani odwołań do statycznych aplikacji sieci Web. Jednak wiele platform frontonu i statycznych generatorów witryn umożliwiają korzystanie z zmiennych środowiskowych podczas opracowywania. W czasie kompilacji te zmienne są zastępowane przez ich wartości w wygenerowanym kodzie HTML lub JavaScript. Ponieważ dane w formacie HTML i JavaScript są łatwo odnajdywane przez odwiedzających witrynę, chcesz uniknąć umieszczania poufnych informacji w aplikacji frontonu. Ustawienia, które przechowują dane poufne, najlepiej znajdują się w części interfejsu API aplikacji.

Aby uzyskać informacje o sposobach używania zmiennych środowiskowych z platformą lub biblioteką JavaScript, zapoznaj się z następującymi artykułami, aby uzyskać więcej szczegółów.

### <a name="javascript-frameworks-and-libraries"></a>Struktury i biblioteki języka JavaScript

- [Angular](https://angular.io/guide/build#configuring-application-environments)
- [React](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Svelte](https://linguinecode.com/post/how-to-add-environment-variables-to-your-svelte-js-app)
- [Vue](https://cli.vuejs.org/guide/mode-and-env.html)

### <a name="static-site-generators"></a>Statyczne generatory lokacji

- [Gatsby](https://www.gatsbyjs.org/docs/environment-variables/)
- [Hugo](https://gohugo.io/getting-started/configuration/)
- [Jekyll](https://jekyllrb.com/docs/configuration/environments/)

## <a name="about-api-app-settings"></a>Informacje o ustawieniach aplikacji interfejsu API

Interfejsy API w usłudze Azure static Web Apps są obsługiwane przez Azure Functions, co pozwala na definiowanie ustawień aplikacji w _local.settings.js_ plik. Ten plik definiuje ustawienia aplikacji we `Values` Właściwości konfiguracji.

Poniższy przykład _local.settings.json_ pokazuje, jak dodać wartość dla `DATABASE_CONNECTION_STRING` .

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
  }
}
```

Ustawienia zdefiniowane we `Values` właściwości mogą być przywoływane z kodu jako zmienne środowiskowe, dostępne w `process.env` obiekcie.

```js
const connectionString = process.env.DATABASE_CONNECTION_STRING;
```

`local.settings.json`Plik nie jest śledzony przez repozytorium GitHub, ponieważ informacje poufne, takie jak parametry połączenia bazy danych, często są zawarte w pliku. Ponieważ ustawienia lokalne pozostają na twoim komputerze, musisz ręcznie przekazać ustawienia na platformę Azure.

Zwykle przekazywanie ustawień jest wykonywane rzadko i nie jest wymagane w przypadku każdej kompilacji.

## <a name="uploading-application-settings"></a>Przekazywanie ustawień aplikacji

Ustawienia aplikacji można skonfigurować za pomocą Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="using-the-azure-portal"></a>Za pomocą witryny Azure Portal

Azure Portal udostępnia interfejs do tworzenia, aktualizowania i usuwania ustawień aplikacji.

1. Przejdź do [Azure Portal](https://portal.azure.com).

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **statyczne Web Apps**.

1. Kliknij opcję **konfiguracji** na pasku bocznym.

1. Wybierz środowisko, do którego chcesz zastosować ustawienia aplikacji. Środowiska tymczasowe są tworzone automatycznie podczas generowania żądania ściągnięcia i są podwyższane do produkcji po scaleniu żądania ściągnięcia. Ustawienia aplikacji można ustawić na środowisko.

1. Kliknij **przycisk Dodaj** , aby dodać nowe ustawienie aplikacji.

    :::image type="content" source="media/application-settings/configuration.png" alt-text="Widok konfiguracji Web Apps statycznej platformy Azure":::

1. Wprowadź **nazwę** i **wartość**.

1. Kliknij przycisk **OK**.

1. Kliknij przycisk **Zapisz**.

### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz użyć polecenia, `az rest` Aby wykonać zbiorcze operacje przekazywania ustawień do platformy Azure. Polecenie akceptuje ustawienia aplikacji jako obiekty JSON w właściwości nadrzędnej o nazwie `properties` .

Najprostszym sposobem utworzenia pliku JSON z odpowiednimi wartościami jest utworzenie zmodyfikowanej wersji _local.settings.js_ pliku.

1. Aby zapewnić, że nowy plik z danymi poufnymi nie jest ujawniany publicznie, Dodaj następujący wpis do pliku _. gitignore_ .

   ```bash
   local.settings*.json
   ```

2. Następnie utwórz kopię _local.settings.js_ pliku i nadaj jej nazwę _local.settings.properties.js_.

3. W nowym pliku Usuń wszystkie inne dane z pliku z wyjątkiem ustawień aplikacji i zmień ich nazwy `Values` na `properties` .

   Plik powinien teraz wyglądać podobnie do poniższego przykładu:

   ```json
   {
     "properties": {
       "DATABASE_CONNECTION_STRING": "<YOUR_DATABASE_CONNECTION_STRING>"
     }
   }
   ```

Polecenie interfejsu wiersza polecenia platformy Azure wymaga określonych wartości dla Twojego konta, aby można było uruchomić przekazywanie. W oknie _Przegląd_ zasobu statycznego Web Apps masz dostęp do następujących informacji:

1. Nazwa lokacji statycznej
2. Nazwa grupy zasobów
3. Identyfikator subskrypcji

:::image type="content" source="media/application-settings/overview.png" alt-text="Azure static Web Apps — Omówienie":::

4. W terminalu lub wierszu polecenia wykonaj następujące polecenie. Pamiętaj, aby zastąpić symbole zastępcze `<YOUR_STATIC_SITE_NAME>` , `<YOUR_RESOURCE_GROUP_NAME>` i `<YOUR_SUBSCRIPTION_ID>` z wartościami z okna _Przegląd_ .

   ```bash
   az rest --method put --headers "Content-Type=application/json" --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/config/functionappsettings?api-version=2019-12-01-preview" --body @local.settings.properties.json
   ```

> [!IMPORTANT]
> Plik "local.settings.properties.json" musi znajdować się w tym samym katalogu, w którym jest uruchamiane to polecenie. Ten plik może mieć nazwę, którą lubisz. Nazwa nie jest istotna.

### <a name="view-application-settings-with-the-azure-cli"></a>Wyświetlanie ustawień aplikacji za pomocą interfejsu wiersza polecenia platformy Azure

Ustawienia aplikacji są dostępne do wyświetlania w interfejsie wiersza polecenia platformy Azure.

- W terminalu lub wierszu polecenia wykonaj następujące polecenie. Pamiętaj, aby zastąpić symbole zastępcze `<YOUR_SUBSCRIPTION_ID>` , `<YOUR_RESOURCE_GROUP_NAME>` `<YOUR_STATIC_SITE_NAME>` z wartościami.

   ```bash
   az rest --method post --uri "/subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/<YOUR_RESOURCE_GROUP_NAME>/providers/Microsoft.Web/staticSites/<YOUR_STATIC_SITE_NAME>/listFunctionAppSettings?api-version=2019-12-01-preview"
   ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie programowania lokalnego](local-development.md)
