---
title: plik dołączany
description: plik dołączany
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84231577"
---
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W obszarze **Wybieranie elementu docelowego publikowania**Użyj opcji publikowania określonych w poniższej tabeli: 

    | Opcja      | Opis                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions plan zużycia** | Utwórz aplikację funkcji w środowisku chmury platformy Azure, która jest uruchamiana w [planie zużycia](../articles/azure-functions/functions-scale.md#consumption-plan). Gdy używasz planu zużycia, płacisz tylko za wykonywanie aplikacji funkcji. Inne plany hostingu wiążą się z wyższymi kosztami. Jeśli uruchamiasz program w planie innym niż plan zużycia, musisz zarządzać [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md).| 
    | **Utwórz nowy** | Nowa aplikacja funkcji z powiązanymi zasobami jest tworzona na platformie Azure. <br/>Jeśli wybierzesz **pozycję Wybierz istniejące**, wszystkie pliki w istniejącej aplikacji funkcji na platformie Azure są zastępowane przez pliki z projektu lokalnego. Tej opcji należy używać tylko w przypadku ponownego publikowania aktualizacji w istniejącej aplikacji funkcji. |
    | **Uruchom z pliku pakietu** | Aplikacja funkcji jest wdrażana przy użyciu narzędzia [zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) z włączonym trybem [uruchamiania z pakietu](../articles/azure-functions/run-functions-from-deployment-package.md) . To wdrożenie, które skutkuje lepszą wydajnością, jest zalecanym sposobem uruchamiania funkcji. <br/>Jeśli nie używasz tej opcji, pamiętaj, aby zatrzymać uruchamianie projektu aplikacji funkcji lokalnie przed opublikowaniem na platformie Azure. |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="Tworzenie profilu publikowania":::


1. Wybierz pozycję **Utwórz profil**. Jeśli jeszcze nie zalogowano się na koncie platformy Azure z poziomu programu Visual Studio, wybierz pozycję **Zaloguj**. Możesz również utworzyć bezpłatne konto platformy Azure.

1. W **App Service: Utwórz nowy**, użyj wartości określonych w poniższej tabeli:

    | Ustawienie      | Wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. Zaakceptuj tę nazwę lub wprowadź nową nazwę. Prawidłowe znaki to: `a-z` , `0-9` , i `-` . |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja platformy Azure, która ma być używana. Zaakceptuj tę subskrypcję lub wybierz nową z listy rozwijanej. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** | Nazwa grupy zasobów |  Grupa zasobów, w której ma zostać utworzona aplikacja funkcji. Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz pozycję **Nowa** , aby utworzyć nową grupę zasobów.|
    | **[Plan hostingu](../articles/azure-functions/functions-scale.md)** | Nazwa planu hostingu | Wybierz pozycję **Nowy** , aby skonfigurować plan bezserwerowy. Upewnij się, że wybrano **użycie** w obszarze **rozmiar**. Po opublikowaniu projektu w aplikacji funkcji, która jest uruchamiana w [planie zużycia](../articles/azure-functions/functions-scale.md#consumption-plan), płacisz tylko za wykonywanie aplikacji funkcji. Inne plany hostingu wiążą się z wyższymi kosztami. Jeśli uruchamiasz program w planie innym niż **użycie**, musisz zarządzać [skalowaniem aplikacji funkcji](../articles/azure-functions/functions-scale.md).  |
    | **Lokalizacja** | Lokalizacja usługi App Service | Wybierz **lokalizację** w [regionie](https://azure.microsoft.com/regions/) blisko siebie lub innych usług, do których dostęp ma funkcja. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Konto magazynu ogólnego przeznaczenia | Konto usługi Azure Storage jest wymagane przez środowisko uruchomieniowe funkcji. Wybierz pozycję **Nowy** , aby skonfigurować konto magazynu ogólnego przeznaczenia. Możesz również wybrać istniejące konto spełniające [wymagania dotyczące konta magazynu](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Okno dialogowe Tworzenie usługi App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć aplikację funkcji i powiązane z nią zasoby na platformie Azure z tymi ustawieniami i wdrożyć kod projektu funkcji. 

1. Wybierz pozycję **Publikuj** i poczekaj na zakończenie wdrożenia. 

    Po zakończeniu wdrożenia na karcie **Publikuj** zostanie wyświetlony główny adres URL aplikacji funkcji na platformie Azure. 
    
1.  Na karcie publikowanie wybierz pozycję **Zarządzaj w programie Cloud Explorer**. Spowoduje to otwarcie nowego zasobu funkcji usługa Azure App w programie Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Tworzenie profilu publikowania":::
    
    W programie Cloud Explorer można używać programu Visual Studio do wyświetlania zawartości witryny, uruchamiania i zatrzymywania aplikacji funkcji oraz przeglądania bezpośrednio do zasobów aplikacji na platformie Azure i w Azure Portal. 