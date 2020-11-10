---
title: Samouczek — Oczyszczanie zasobów | Azure
description: W tym samouczku dowiesz się, jak wyczyścić zasoby platformy Azure przydzieloną podczas tworzenia aplikacji sieci Web.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ab91ea7aa6e621dabc5cac83fe818dbf175214b6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428904"
---
# <a name="tutorial-clean-up-resources"></a>Samouczek: czyszczenie zasobów

Jeśli wykonano wszystkie kroki tego samouczka wieloczęściowego, utworzono usługę App Service, plan hostingu usługi App Service i konto magazynu w grupie zasobów.  Możesz również utworzyć rejestrację aplikacji w usłudze Azure AD.  Gdy nie będzie już potrzebny, usuń te zasoby i rejestrację aplikacji, aby nie naliczać opłat.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Usuń zasoby platformy Azure utworzone w ramach samouczka

## <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów
W [Azure Portal](https://portal.azure.com)wybierz pozycję **grupy zasobów** z menu Portal i wybierz grupę zasobów zawierającą usługę App Service i plan usługi App Service.

Wybierz pozycję **Usuń grupę zasobów** , aby usunąć grupę zasobów i wszystkie zasoby.

:::image type="content" alt-text="Usuwanie grupy zasobów" source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Uruchomienie tego polecenia może potrwać kilka minut.

## <a name="delete-the-app-registration"></a>Usuń rejestrację aplikacji
W menu portalu wybierz pozycję **Azure Active Directory** , a następnie pozycję **rejestracje aplikacji** , a następnie utworzoną aplikację.
:::image type="content" alt-text="Wybierz rejestrację aplikacji" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

W przegląd rejestracji aplikacji wybierz pozycję **Usuń**.
:::image type="content" alt-text="Usuń rejestrację aplikacji" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Usuń zasoby platformy Azure utworzone w ramach samouczka

Dowiedz się, jak połączyć [aplikację platformy .NET Core](tutorial-dotnetcore-sqldb-app.md), aplikację [języka](tutorial-java-spring-cosmosdb.md) [Python](tutorial-python-postgresql-app.md), aplikację Java lub [Node.js aplikację](tutorial-nodejs-mongodb-app.md) z bazą danych.