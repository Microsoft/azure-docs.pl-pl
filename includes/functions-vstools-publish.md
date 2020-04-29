---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 8a4fbb3803f2eed4f961792a29a6bf8a327ea208
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80657538"
---
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

2. W obszarze **Wybieranie elementu docelowego publikowania**Użyj opcji publikowania określonych w poniższej tabeli: 

    | Opcja      | Opis                                |
    | ------------ |  -------------------------------------------------- |
    | **aplikacja funkcji platformy Azure** | Utwórz aplikację funkcji w środowisku chmury platformy Azure. | 
    | **Utwórz nowy** | Nowa aplikacja funkcji z powiązanymi zasobami jest tworzona na platformie Azure. <br/>Jeśli wybierzesz **pozycję Wybierz istniejące**, wszystkie pliki w istniejącej aplikacji funkcji na platformie Azure są zastępowane przez pliki z projektu lokalnego. Tej opcji należy używać tylko w przypadku ponownego publikowania aktualizacji w istniejącej aplikacji funkcji. |
    | **Uruchom z pliku pakietu** | Aplikacja funkcji jest wdrażana przy użyciu narzędzia [zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) z włączonym trybem [uruchamiania z pakietu](../articles/azure-functions/run-functions-from-deployment-package.md) . To wdrożenie, które skutkuje lepszą wydajnością, jest zalecanym sposobem uruchamiania funkcji. <br/>Jeśli nie używasz tej opcji, pamiętaj, aby zatrzymać uruchamianie projektu aplikacji funkcji lokalnie przed opublikowaniem na platformie Azure. |

    ![Wybieranie miejsca docelowego publikacji](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)


3. Wybierz pozycję **Publikuj**. Jeśli jeszcze nie zalogowano się na koncie platformy Azure z poziomu programu Visual Studio, wybierz pozycję **Zaloguj**. Możesz również utworzyć bezpłatne konto platformy Azure.

4. W **Azure App Service: Utwórz nowy**, użyj wartości określonych w poniższej tabeli:

    | Ustawienie      | Wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. Zaakceptuj tę nazwę lub wprowadź nową nazwę. Prawidłowe znaki to: `a-z`, `0-9`, i `-`. |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja platformy Azure, która ma być używana. Zaakceptuj tę subskrypcję lub wybierz nową z listy rozwijanej. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** | Nazwa grupy zasobów |  Grupa zasobów, w której ma zostać utworzona aplikacja funkcji. Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Nowa** , aby utworzyć nową grupę zasobów.|
    | **[Plan hostingu](../articles/azure-functions/functions-scale.md)** | Nazwa planu hostingu | Wybierz pozycję **Nowy** , aby skonfigurować plan bezserwerowy. Upewnij się, że wybrano **użycie** w obszarze **rozmiar**. Po opublikowaniu projektu w aplikacji funkcji, która jest uruchamiana w [planie zużycia](../articles/azure-functions/functions-scale.md#consumption-plan), płacisz tylko za wykonywanie aplikacji funkcji. Inne plany hostingu wiążą się z wyższymi kosztami. Jeśli uruchamiasz program w planie innym niż **użycie**, musisz zarządzać [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md). Wybierz **lokalizację** w [regionie](https://azure.microsoft.com/regions/) blisko siebie lub innych usług, do których dostęp ma funkcja.  |
    | **[Usługa Azure Storage](../articles/storage/common/storage-account-create.md)** | Konto magazynu ogólnego przeznaczenia | Konto usługi Azure Storage jest wymagane przez środowisko uruchomieniowe funkcji. Wybierz pozycję **Nowy** , aby skonfigurować konto magazynu ogólnego przeznaczenia. Możesz również wybrać istniejące konto spełniające [wymagania dotyczące konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Okno dialogowe Tworzenie usługi App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

5. Wybierz pozycję **Utwórz** , aby utworzyć aplikację funkcji i powiązane z nią zasoby na platformie Azure z tymi ustawieniami i wdrożyć kod projektu funkcji. 

6. Wybierz pozycję Publikuj i po zakończeniu wdrożenia Zanotuj wartość **adresu URL witryny** , która jest adresem aplikacji funkcji na platformie Azure.

    ![Komunikat o powodzeniu publikowania](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
