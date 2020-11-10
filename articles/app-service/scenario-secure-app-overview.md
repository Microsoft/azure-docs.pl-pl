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
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428929"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Samouczek: Włączanie uwierzytelniania w App Service i dostęp do magazynu i Microsoft Graph

W tym samouczku opisano typowy scenariusz aplikacji:

- [(A) Skonfiguruj uwierzytelnianie dla aplikacji sieci Web](scenario-secure-app-authentication-app-service.md) i Ogranicz dostęp do użytkowników w organizacji.
- [(B) bezpieczny dostęp do usługi Azure Storage](scenario-secure-app-access-storage.md) w imieniu aplikacji sieci Web przy użyciu tożsamości zarządzanych.
- (C) dostęp do danych w Microsoft Graph [w imieniu zalogowanego użytkownika](scenario-secure-app-access-microsoft-graph-as-user.md) lub [w imieniu aplikacji sieci Web](scenario-secure-app-access-microsoft-graph-as-app.md) przy użyciu tożsamości zarządzanych.
- [Wyczyść zasoby](scenario-secure-app-clean-up-resources.md) utworzone w ramach tego samouczka.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Scenariusze aplikacji na platformie tożsamości firmy Microsoft" border="false":::

Aby rozpocząć, Dowiedz się, jak włączyć uwierzytelnianie dla aplikacji sieci Web.

> [!div class="nextstepaction"]
> [Konfigurowanie uwierzytelniania dla aplikacji sieci Web](scenario-secure-app-authentication-app-service.md)
