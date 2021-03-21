---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009685"
---
> [!NOTE]
> Aplikacja internetowa może przekroczyć limit czasu po 20 minutach braku aktywności. tylko żądania do rzeczywistej aplikacji sieci Web mogą zresetować czasomierz. Wyświetlanie konfiguracji aplikacji w Azure Portal lub wykonywanie żądań do zaawansowanej witryny narzędzi ( `https://<app_name>.scm.azurewebsites.net` ) nie powoduje resetowania czasomierza. Jeśli ustawisz aplikację sieci Web tak, aby uruchamiała Zadania WebJob ciągła lub zaplanowana (czasomierz-wyzwalacz), Włącz ustawienie **zawsze** włączone na stronie **konfiguracji** platformy Azure aplikacji sieci Web, aby upewnić się, że Zadania WebJob działają niezawodnie. Ta funkcja jest dostępna tylko w [warstwach cenowych](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)podstawowa, standardowa i Premium.
