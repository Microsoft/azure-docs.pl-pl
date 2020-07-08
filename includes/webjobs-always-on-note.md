---
title: dołączanie pliku
description: dołączanie pliku
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "68424167"
---
> [!NOTE]
> Aplikacja internetowa może przekroczyć limit czasu po 20 minutach braku aktywności. Czasomierz może zresetować tylko żądania do rzeczywistej aplikacji sieci Web. Wyświetlanie konfiguracji aplikacji w Azure Portal lub wykonywanie żądań do zaawansowanej witryny narzędzi ( `https://<app_name>.scm.azurewebsites.net` ) nie Zresetuj czasomierza. Jeśli aplikacja uruchamia zadania WebJob w trybie ciągłym lub zaplanowanym (wyzwalacz czasomierza), Włącz opcję **zawsze** włączone, aby upewnić się, że Zadania WebJob działają niezawodnie. Ta funkcja jest dostępna tylko w [warstwach cenowych](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)podstawowa, standardowa i Premium.
