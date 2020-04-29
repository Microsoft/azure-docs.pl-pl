---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67183880"
---
Poniższa procedura umożliwia wyświetlanie i aktualizowanie wersji środowiska uruchomieniowego używanego aktualnie przez aplikację funkcji.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojej aplikacji funkcji.

1. W obszarze **Skonfigurowane funkcje** wybierz pozycję **Ustawienia aplikacji funkcji**.

    ![Wybieranie ustawień aplikacji funkcji](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Na karcie **Ustawienia aplikacji funkcji** znajdź pozycję **Wersja środowiska uruchomieniowego**. Zwróć uwagę na określoną wersję środowiska uruchomieniowego i żądaną wersję główną. W poniższym przykładzie wersja jest ustawiona na `~2`.

   ![Wybieranie ustawień aplikacji funkcji](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Aby przypiąć aplikację funkcji do środowiska uruchomieniowego w wersji 1.x, wybierz pozycję **~1** w obszarze **Wersja środowiska uruchomieniowego**. Ten przełącznik jest wyłączony, gdy masz funkcje w aplikacji.

1. Po zmianie wersji środowiska uruchomieniowego wróć na kartę **Omówienie** i wybierz pozycję **Uruchom ponownie**, aby ponownie uruchomić aplikację.  Aplikacja funkcji uruchamia się ponownie w środowisku uruchomieniowym w wersji 1.x, a podczas tworzenia funkcji są używane szablony w wersji 1.x.