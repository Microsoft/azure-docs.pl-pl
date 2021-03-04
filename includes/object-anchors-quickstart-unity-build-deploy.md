---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044670"
---
### <a name="build-and-deploy-the-app"></a>Kompilowanie i wdrażanie aplikacji

Otwórz `.sln` plik wygenerowany przez środowisko Unity. Zmień konfigurację kompilacji w następujący sposób.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="Konfiguracja kompilacji":::

Następnie należy skonfigurować **adres IP maszyny zdalnej** w celu wdrożenia i debugowania aplikacji.

Kliknij prawym przyciskiem myszy projekt aplikacji i wybierz polecenie **Właściwości**. Na stronie właściwości wybierz pozycję **Właściwości konfiguracji — debugowanie >**. Zmień wartość **Nazwa komputera** na adres IP urządzenia Hololens, a następnie kliknij przycisk **Zastosuj**.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="debugowanie zdalne":::

Zamknij stronę właściwości. Kliknij pozycję **maszyna zdalna**. Aplikacja powinna rozpocząć Kompilowanie i wdrażanie na urządzeniu zdalnym. Upewnij się, że urządzenie jest aktywne.
