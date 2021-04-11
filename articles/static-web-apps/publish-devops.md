---
title: 'Samouczek: publikowanie Web Apps statycznej platformy Azure za pomocą usługi Azure DevOps'
description: Dowiedz się, jak opublikować Web Apps statycznej platformy Azure za pomocą usługi Azure DevOps.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 472cf7b69078b3247c393ff65139bc29e5683a32
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639380"
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

1. Wybierz pozycję **Importuj** , aby rozpocząć importowanie przykładowej aplikacji.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="Repozytorium DevOps":::

1. W polu **sklonowany adres URL** wprowadź `https://github.com/staticwebdev/vanilla-api.git` .

1. Wybierz pozycję **Importuj**.

## <a name="create-a-static-web-app"></a>Tworzenie statycznej aplikacji internetowej

1. Przejdź do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**.

1. Wyszukaj **statyczne Web Apps**.

1. Wybierz pozycję **statyczne Web Apps (wersja zapoznawcza)**.

1. Wybierz przycisk **Utwórz**.

1. W obszarze _Szczegóły wdrożenia_ upewnij się, że wybrano pozycję **inne**. Pozwala to na użycie kodu w repozytorium usługi Azure DevOps.

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="Szczegóły wdrożenia — inne":::

1. Po pomyślnym wdrożeniu przejdź do nowego zasobu statycznego Web Apps.

1. Wybierz pozycję **Zarządzaj tokenem wdrożenia**.

1. Skopiuj **token wdrożenia** i wklej go do edytora tekstu, aby użyć go na innym ekranie.

    > [!NOTE]
    > Ta wartość jest teraz dostępna, ponieważ skopiujesz i wkleisz więcej wartości w kolejnych krokach.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Token wdrożenia":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Tworzenie zadania potoku na platformie Azure DevOps

1. Przejdź do repozytorium Azure DevOps, które zostało utworzone wcześniej.

1. Wybierz pozycję **Konfiguruj kompilację**.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Potok kompilacji":::

1. Na ekranie *Konfigurowanie potoku* wybierz pozycję **potok początkowy**.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Konfiguruj potok":::

1. Skopiuj i wklej następujący YAML do potoku.

    ```yaml
    trigger:
      - main
    
    pool:
      vmImage: ubuntu-latest
    
    steps:
      - task: AzureStaticWebApp@0
        inputs:
          app_location: "/" 
          api_location: "api"
          output_location: ""
        env:
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Jeśli nie korzystasz z przykładowej aplikacji, wartości dla `app_location` , `api_location` i `output_location` muszą ulec zmianie, aby odpowiadały wartościom w aplikacji.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token`Wartość jest samodzielnie zarządzana i jest konfigurowana ręcznie.

1. Wybierz **zmienne**.

1. Utwórz nową zmienną.

1. Nazwij zmienną **deployment_token** (zgodną z nazwą w przepływie pracy).

1. Skopiuj token wdrożenia, który został wcześniej wklejony do edytora tekstu.

1. Wklej w tokenie wdrożenia w polu _wartość_ .

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Token zmiennej":::

1. Wybierz opcję **Zachowaj ten wpis tajny wartości**.

1. Wybierz przycisk **OK**.

1. Wybierz pozycję **Zapisz** , aby powrócić do YAML potoku.

1. Wybierz pozycję **Zapisz i uruchom** , aby otworzyć okno dialogowe _Zapisywanie i uruchamianie_ .

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Potok":::

1. Wybierz pozycję **Zapisz i uruchom** , aby uruchomić potok.

1. Po pomyślnym wdrożeniu przejdź do **omówienia** usługi Azure static Web Apps, który zawiera linki do konfiguracji wdrożenia. Zwróć uwagę, jak link _źródłowy_ teraz wskazuje gałąź i lokalizację repozytorium usługi Azure DevOps.

1. Wybierz **adres URL** , aby wyświetlić nowo wdrożoną witrynę sieci Web.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Lokalizacja wdrożenia":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Konfigurowanie Web Apps statycznej platformy Azure](./configuration.md)
