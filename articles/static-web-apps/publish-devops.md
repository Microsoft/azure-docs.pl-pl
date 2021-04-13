---
title: 'Samouczek: publikowanie Azure Static Web Apps za pomocą Azure DevOps'
description: Dowiedz się, jak publikować Azure DevOps za pomocą Azure Static Web Apps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: f82ae60ab7f57b20a727deefa6e286d698ee5b6c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365760"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Samouczek: publikowanie Azure Static Web Apps za pomocą Azure DevOps

W tym artykule pokazano, jak wdrożyć usługę [Azure Static Web Apps](./overview.md) przy użyciu [Azure DevOps](https://dev.azure.com/).

Z tego samouczka dowiesz się, jak wykonać następujące czynności:

- Konfigurowanie lokacji Azure Static Web Apps sieci Web
- Tworzenie potoku Azure DevOps do kompilowania i publikowania statycznej aplikacji internetowej

## <a name="prerequisites"></a>Wymagania wstępne

- **Aktywne konto platformy Azure:** Jeśli go nie masz, możesz bezpłatnie [utworzyć konto](https://azure.microsoft.com/free/).
- **Azure DevOps Project:** Jeśli go nie masz, możesz bezpłatnie utworzyć [projekt](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
- **Azure DevOps potoku:** Jeśli potrzebujesz pomocy przy rozpoczynania pracy, zobacz [Tworzenie pierwszego potoku.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline?view=azure-devops&preserve-view=true)

## <a name="create-a-static-web-app-in-an-azure-devops-repository"></a>Tworzenie statycznej aplikacji internetowej w Azure DevOps repozytorium

  > [!NOTE]
  > Jeśli masz istniejącą aplikację w repozytorium, możesz przejść do następnej sekcji.

1. Przejdź do Azure DevOps repozytorium.

1. Wybierz **pozycję Importuj,** aby rozpocząć importowanie przykładowej aplikacji.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps Repo":::

1. W **polu Clone URL (Sklonuj adres URL)** wprowadź `https://github.com/staticwebdev/vanilla-api.git` .

1. Wybierz pozycję **Importuj**.

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

1. Przejdź do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**.

1. Wyszukaj **Static Web Apps**.

1. Wybierz **Static Web Apps (wersja zapoznawcza).**

1. Wybierz przycisk **Utwórz**.

1. W _obszarze Szczegóły wdrożenia_ upewnij się, że wybierzesz pozycję **Inne.** Dzięki temu można używać kodu w repozytorium Azure DevOps repozytorium.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Szczegóły wdrożenia — inne":::

1. Po pomyślnym wdrożeniu przejdź do nowego zasobu Static Web Apps zasobów.

1. Wybierz **pozycję Zarządzaj tokenem wdrożenia.**

1. Skopiuj **token wdrożenia i** wklej go do edytora tekstów do użycia na innym ekranie.

    > [!NOTE]
    > Ta wartość jest na razie zastawiona, ponieważ w kolejnych krokach skopiujesz i wkleisz więcej wartości.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token wdrożenia":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Tworzenie zadania potoku w programie Azure DevOps

1. Przejdź do Azure DevOps repozytorium danych, które zostało utworzone wcześniej.

1. Wybierz **pozycję Skonfiguruj kompilację.**

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Potok kompilacji":::

1. Na *ekranie Konfigurowanie potoku* wybierz pozycję **Potok startowy.**

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Konfigurowanie potoku":::

1. Skopiuj i wklej następujący kod YAML do potoku.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - checkout: self
        submodules: true

      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Jeśli nie używasz przykładowej aplikacji, wartości , i należy zmienić, aby były zgodne `app_location` `api_location` z `output_location` wartościami w aplikacji.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Wartość `azure_static_web_apps_api_token` jest samodzielnie zarządzana i jest konfigurowana ręcznie.

1. Wybierz **pozycję Zmienne**.

1. Utwórz nową zmienną.

1. Nazwij **zmienną deployment_token** (pasującą do nazwy w przepływie pracy).

1. Skopiuj token wdrożenia wklejony wcześniej do edytora tekstów.

1. Wklej token wdrożenia w _polu_ Wartość.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Token zmiennej":::

1. Wybierz pozycję **Zachowaj tę wartość jako tajne.**

1. Wybierz przycisk **OK**.

1. Wybierz **pozycję Zapisz,** aby powrócić do potoku YAML.

1. Wybierz **pozycję Zapisz i uruchom,** aby otworzyć okno dialogowe Zapisywanie i _uruchamianie._

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Potok":::

1. Wybierz **pozycję Zapisz i uruchom,** aby uruchomić potok.

1. Po pomyślnym zakończeniu wdrażania przejdź do Azure Static Web Apps **Przegląd,** który zawiera linki do konfiguracji wdrożenia. Zwróć uwagę, że link _Źródło_ wskazuje teraz gałąź i lokalizację Azure DevOps repozytorium.

1. Wybierz adres **URL,** aby wyświetlić nowo wdrożoną witrynę internetową.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Lokalizacja wdrożenia":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie Azure Static Web Apps](./configuration.md)
