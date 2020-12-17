---
title: 'Szybki Start: kompilowanie pierwszej witryny statycznej przy użyciu statycznego Web Apps platformy Azure za pomocą interfejsu wiersza polecenia'
description: Dowiedz się, jak wdrożyć lokację statyczną w usłudze Azure static Web Apps przy użyciu interfejsu wiersza polecenia platformy Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 00892b61cd23ee38ff3d63f8b61391ff1bffdc90
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616409"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Szybki Start: Tworzenie pierwszej witryny statycznej przy użyciu interfejsu wiersza polecenia platformy Azure

Usługa Azure static Web Apps publikuje witrynę sieci Web w środowisku produkcyjnym, tworząc aplikacje z repozytorium GitHub. W tym przewodniku szybki start wdrożono aplikację sieci Web do statycznych aplikacji sieci Web platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

- Konto usługi [GitHub](https://github.com)
- [Osobisty token dostępu usługi GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- Konto [platformy Azure](https://portal.azure.com)
- Zainstalowano [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) (w wersji 2.8.0 lub nowszej)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Następnie przejdź do nowego folderu przy użyciu poniższego polecenia.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

Po utworzeniu repozytorium możesz utworzyć statyczną aplikację sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Upewnij się, że używasz folderu _My-First-static-Web-App_ w terminalu.

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure przy użyciu poniższe polecenie.

    ```bash
    az login
    ```

1. Utwórz nową statyczną aplikację sieci Web z repozytorium.

    # <a name="no-framework"></a>[Brak struktury](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`: Zastąp tę wartość nazwą istniejącej grupy zasobów platformy Azure.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Zastąp tę wartość nazwą użytkownika usługi GitHub.

    - `<LOCATION>`: Zastąp tę wartość najbliższej lokalizacji. Dostępne opcje to: _środkowe_, _EastAsia_, _EastUS2_, _WestEurope_ i _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Zastąp tę wartość [tokenem dostępu osobistego usługi GitHub](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) , który został wcześniej wygenerowany.

    Teraz możesz wyświetlić utworzoną aplikację na platformie Azure.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

1. Wyszukaj **aplikację My-First-Web-static-App** na górnym pasku wyszukiwania.

1. Wybierz pozycję **moja-First-Web-static-App**.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, możesz usunąć wystąpienie Web Apps statycznej platformy Azure, uruchamiając następujące polecenie:

```bash
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie interfejsu API](add-api.md)
