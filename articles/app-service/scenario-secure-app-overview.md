---
title: Samouczek — tworzenie bezpiecznej aplikacji sieci Web na Azure App Service | Azure
description: W tym samouczku dowiesz się, jak utworzyć aplikację internetową przy użyciu Azure App Service, włączyć uwierzytelnianie, wywołać usługę Azure Storage i Microsoft Graph wywołania.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 17931ee92072059d94cd950e9ce8ef6588f7e891
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905506"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Samouczek: Włączanie uwierzytelniania w App Service i dostęp do magazynu i Microsoft Graph

W tym samouczku opisano typowy scenariusz aplikacji, w którym można dowiedzieć się, jak:

- [Skonfiguruj uwierzytelnianie dla aplikacji sieci Web](scenario-secure-app-authentication-app-service.md) i Ogranicz dostęp do użytkowników w organizacji. Zobacz na diagramie.
- [Bezpieczny dostęp do usługi Azure Storage](scenario-secure-app-access-storage.md) dla aplikacji sieci Web przy użyciu tożsamości zarządzanych. Zobacz B na diagramie.
- Dostęp do danych w Microsoft Graph [dla zalogowanego użytkownika](scenario-secure-app-access-microsoft-graph-as-user.md) lub [dla aplikacji sieci Web](scenario-secure-app-access-microsoft-graph-as-app.md) przy użyciu tożsamości zarządzanych. Zobacz C na diagramie.
- [Wyczyść zasoby](scenario-secure-app-clean-up-resources.md) utworzone w ramach tego samouczka.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagram przedstawiający scenariusze aplikacji na platformie Microsoft Identity platform." border="false":::

Aby rozpocząć, Dowiedz się, jak włączyć uwierzytelnianie dla aplikacji sieci Web.

> [!div class="nextstepaction"]
> [Konfigurowanie uwierzytelniania dla aplikacji sieci Web](scenario-secure-app-authentication-app-service.md)
