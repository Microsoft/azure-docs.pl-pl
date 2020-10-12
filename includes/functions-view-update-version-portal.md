---
title: plik dołączany
description: plik dołączany
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83343358"
---
Poniższa procedura umożliwia wyświetlanie i aktualizowanie wersji środowiska uruchomieniowego używanego aktualnie przez aplikację funkcji.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojej aplikacji funkcji.

1. W obszarze **Ustawienia**wybierz pozycję **Konfiguracja**. Na karcie **Ustawienia środowiska uruchomieniowego funkcji** Znajdź **wersję środowiska uruchomieniowego**. Zanotuj konkretną wersję środowiska uruchomieniowego. W poniższym przykładzie wersja jest ustawiona na `~3`.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Wyświetl wersję środowiska uruchomieniowego." border="true":::

1. Aby przypiąć aplikację funkcji do środowiska uruchomieniowego w wersji 1.x, wybierz pozycję **~1** w obszarze **Wersja środowiska uruchomieniowego**. Ten przełącznik jest wyłączony, gdy masz funkcje w aplikacji.

1. Po zmianie wersji środowiska uruchomieniowego wróć na kartę **Omówienie** i wybierz pozycję **Uruchom ponownie**, aby ponownie uruchomić aplikację.  Aplikacja funkcji uruchamia się ponownie w środowisku uruchomieniowym w wersji 1.x, a podczas tworzenia funkcji są używane szablony w wersji 1.x.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Wyświetl wersję środowiska uruchomieniowego." border="true":::
