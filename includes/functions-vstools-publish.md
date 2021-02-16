---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5b537b88052ce4042e346732f3dc63aaec6621cc
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100529689"
---
1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Publikuj** i w obszarze **docelowym**, wybierz pozycję **Azure** następnie **dalej**.

1. Dla **konkretnego obiektu docelowego** wybierz pozycję **Azure aplikacja funkcji (Windows)**, która tworzy aplikację funkcji działającą w systemie Windows.

1. W obszarze **Funkcja** wybierz pozycję **Utwórz nową funkcję platformy Azure...** 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="Utwórz nowe wystąpienie aplikacji funkcji":::

1. Utwórz nowe wystąpienie przy użyciu wartości określonych w poniższej tabeli:

    | Ustawienie      | Wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. Zaakceptuj tę nazwę lub wprowadź nową nazwę. Prawidłowe znaki to: `a-z` , `0-9` , i `-` . |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja platformy Azure, która ma być używana. Zaakceptuj tę subskrypcję lub wybierz nową z listy rozwijanej. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** | Nazwa grupy zasobów |  Grupa zasobów, w której ma zostać utworzona aplikacja funkcji. Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Nowa** , aby utworzyć nową grupę zasobów.|
    | **[Typ planu](../articles/azure-functions/functions-scale.md)** | Zużycie | Po opublikowaniu projektu w aplikacji funkcji, która jest uruchamiana w [planie zużycia](../articles/azure-functions/consumption-plan.md), płacisz tylko za wykonywanie aplikacji funkcji. Inne plany hostingu wiążą się z wyższymi kosztami. |
    | **Lokalizacja** | Lokalizacja usługi App Service | Wybierz **lokalizację** w [regionie](https://azure.microsoft.com/regions/) blisko siebie lub innych usług, do których dostęp ma funkcja. |
    | **[Azure Storage](../articles/azure-functions/storage-considerations.md)** | Konto magazynu ogólnego przeznaczenia | Konto usługi Azure Storage jest wymagane przez środowisko uruchomieniowe funkcji. Wybierz pozycję **Nowy** , aby skonfigurować konto magazynu ogólnego przeznaczenia. Możesz również wybrać istniejące konto spełniające [wymagania dotyczące konta magazynu](../articles/azure-functions/storage-considerations.md#storage-account-requirements).  |

    ![Okno dialogowe Tworzenie usługi App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć aplikację funkcji i powiązane z nią zasoby na platformie Azure. Stan tworzenia zasobów znajduje się w lewym dolnym rogu okna. 

1. Z powrotem w **wystąpieniu funkcji** upewnij się, że jest zaznaczone pole wyboru **Uruchom z pliku pakietu** . Aplikacja funkcji jest wdrażana przy użyciu narzędzia [zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) z włączonym trybem [uruchamiania z pakietu](../articles/azure-functions/run-functions-from-deployment-package.md) . Jest to zalecana metoda wdrażania dla projektu usługi Functions, ponieważ powoduje to lepszą wydajność. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Zakończ tworzenie profilu":::

1. Wybierz pozycję **Zakończ**, a na stronie publikowanie wybierz pozycję **Publikuj** , aby wdrożyć pakiet zawierający pliki projektu w nowej aplikacji funkcji na platformie Azure. 

    Po zakończeniu wdrożenia na karcie **Publikuj** zostanie wyświetlony główny adres URL aplikacji funkcji na platformie Azure. 
    
1.  Na karcie publikowanie wybierz pozycję **Zarządzaj w programie Cloud Explorer**. Spowoduje to otwarcie nowego zasobu funkcji usługa Azure App w programie Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Komunikat o powodzeniu publikowania":::
    
    W programie Cloud Explorer można używać programu Visual Studio do wyświetlania zawartości witryny, uruchamiania i zatrzymywania aplikacji funkcji oraz przeglądania bezpośrednio do zasobów aplikacji na platformie Azure i w Azure Portal. 
