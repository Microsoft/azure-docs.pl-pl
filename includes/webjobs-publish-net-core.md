---
title: plik dołączany
description: plik dołączany
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e208b52c67f173bd0d289715b63562df656b1ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009792"
---
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W oknie dialogowym **Publikowanie** wybierz pozycję **Azure** for **Target**, a następnie wybierz pozycję **dalej**. 

1. Wybierz **Azure WebJobs** dla **określonego celu**, a następnie wybierz przycisk **dalej**.

1. Wybierz pozycję **Utwórz nowe zadanie WebJob platformy Azure**.

   ![Wybierz cel publikowania](./media/webjobs-publish-netcore/pick-publish-target.png)

1. W oknie dialogowym **App Service (system Windows)** Użyj ustawień hostingu w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Unikatowa nazwa identyfikująca nową aplikację funkcji. |
    | **Subskrypcja** | Wybierz subskrypcję | Subskrypcja platformy Azure, która ma być używana. |
    | **[Grupa zasobów](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Nazwa grupy zasobów, w której ma zostać utworzona aplikacja funkcji. Wybierz pozycję **Nowa**, aby utworzyć nową grupę zasobów.|
    | **[Plan hostingu](../articles/app-service/overview-hosting-plans.md)** | Plan usługi App Service | [Plan usługi App Service](../articles/app-service/overview-hosting-plans.md) określa lokalizację, rozmiar i funkcje farmy serwerów sieci Web, która jest hostem aplikacji. Hostując wiele aplikacji, można zmniejszyć koszty przez skonfigurowanie aplikacji internetowych do korzystania z jednego planu usługi App Service. Plany App Service określają region, rozmiar wystąpienia, liczbę skalowania i jednostkę SKU (bezpłatna, współdzielona, podstawowa, standardowa lub Premium). Wybierz pozycję **Nowy** , aby utworzyć nowy plan App Service. |

    ![Okno dialogowe Create App Service (Tworzenie usługi App Service)](./media/webjobs-publish-netcore/app-service-dialog.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć zadanie WebJob i powiązane zasoby na platformie Azure przy użyciu tych ustawień i wdrożyć kod projektu.