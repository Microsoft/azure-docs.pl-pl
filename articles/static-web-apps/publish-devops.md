---
title: 'Samouczek: publikowanie Web Apps statycznej platformy Azure za pomocą usługi Azure DevOps'
description: Dowiedz się, jak opublikować Web Apps statycznej platformy Azure za pomocą usługi Azure DevOps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: af359734ff5bfe90dedbb7f8389aecdc6e056654
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543566"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Samouczek: publikowanie Web Apps statycznej platformy Azure za pomocą usługi Azure DevOps

W tym artykule przedstawiono sposób wdrażania w usłudze Azure [Static Web Apps](./overview.md) przy użyciu [usługi Azure DevOps](https://dev.azure.com/).

Z tego samouczka dowiesz się, jak wykonać następujące czynności:

- Konfigurowanie lokacji statycznej Web Apps platformy Azure
- Tworzenie potoku usługi Azure DevOps w celu kompilowania i publikowania statycznej aplikacji sieci Web

## <a name="prerequisites"></a>Wymagania wstępne

- **Aktywne konto platformy Azure:** Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://azure.microsoft.com/free/).
- **Projekt usługi Azure DevOps:** Jeśli go nie masz, możesz [utworzyć projekt bezpłatnie](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Potok Azure DevOps:** Jeśli potrzebujesz pomocy przy rozpoczynaniu pracy, zobacz [Tworzenie pierwszego potoku](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true).

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Tworzenie statycznej aplikacji sieci Web w repozytorium usługi Azure DevOps

  > [!NOTE]
  > Jeśli masz istniejącą aplikację w repozytorium, możesz przejść do następnej sekcji.

1. Przejdź do repozytorium usługi Azure DevOps.

1. Użyj istniejącego repozytorium lub _zaimportuj repozytorium_ , jak pokazano poniżej.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Repozytorium DevOps":::

1. Utwórz nowy plik dla aplikacji frontonu sieci Web.

1. Skopiuj i wklej następujący znacznik HTML do nowego pliku:

    ```html
    <!DOCTYPE html>
    <html lang="en">
  
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <link rel="stylesheet" href="styles.css">
      <title>Hello World!</title>
    </head>
  
    <body>
      <main>
        <h1>Hello World!</h1>
      </main>
    </body>
  
    </html>
    ```

1. Zapisz plik.

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

1. Przejdź do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**.

1. Wyszukaj **statyczne Web Apps**.

1. Wybierz pozycję **statyczne Web Apps (wersja zapoznawcza)**.

1. Wybierz przycisk **Utwórz**.

1. W obszarze _Szczegóły wdrożenia_ upewnij się, że wybrano pozycję **inne**. Pozwala to na użycie kodu w repozytorium usługi Azure DevOps.

    > [!NOTE]
    > Funkcja wybierania _innych_ jest obecnie wdrażana i może nie być jeszcze dostępna we wszystkich subskrypcjach platformy Azure.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Szczegóły wdrożenia — inne":::

1. Po pomyślnym wdrożeniu wybierz pozycję **Zarządzaj tokenem wdrożenia**.

1. Skopiuj **token wdrożenia** i wklej go do edytora tekstu, aby użyć go na innym ekranie.

    > [!NOTE]
    > Ta wartość jest teraz dostępna, ponieważ skopiujesz i wkleisz więcej wartości w kolejnych krokach.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token wdrożenia":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Tworzenie zadania potoku na platformie Azure DevOps

1. Przejdź do projektu usługi Azure DevOps, który został utworzony wcześniej.

2. Utwórz nowy **potok kompilacji** i wybierz pozycję **Skonfiguruj kompilację**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Potok kompilacji":::

3. Skopiuj i wklej następujący YAML do potoku.

    > [!NOTE]
    > Dla aplikacji należy zmodyfikować wartości wprowadzone dla _app_location_,_api_location_ i _output_location_ .  

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: frontend 
          api_location: api
          output_location: build
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    Skonfiguruj dane wejściowe statycznej aplikacji internetowej platformy Azure zgodnie ze strukturą folderów aplikacji.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token`Wartość jest samodzielnie zarządzana i jest konfigurowana ręcznie.

4. Wybierz **zmienne**.

5. Utwórz nową zmienną.

6. Nazwij zmienną **deployment_token** (zgodną z nazwą w przepływie pracy).

7. Skopiuj token wdrożenia, który został wcześniej wklejony do edytora tekstu.

8. Wklej w tokenie wdrożenia w polu _wartość_ .

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Token zmiennej":::

9. Wybierz przycisk **OK**.

10. Wybierz pozycję **Zapisz i uruchom** potok.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Potok":::

11. Po pomyślnym wdrożeniu przejdź do **omówienia** usługi Azure static Web Apps, który zawiera linki do konfiguracji wdrożenia.

12. Wybierz **adres URL** , aby wyświetlić nowo wdrożoną witrynę sieci Web. Zwróć uwagę, jak link _źródłowy_ teraz wskazuje gałąź i lokalizację repozytorium usługi Azure DevOps.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Lokalizacja wdrożenia":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie Web Apps statycznej platformy Azure](./configuration.md)
