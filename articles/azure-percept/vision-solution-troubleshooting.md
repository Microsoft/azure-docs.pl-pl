---
title: Rozwiązywanie problemów z modułami Vision i Visioning platformy Azure Percept
description: Uzyskaj porady dotyczące rozwiązywania problemów w przypadku niektórych typowych problemów występujących w środowiskach tworzenia prototypów systemu AI
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101663517"
---
# <a name="vision-solution-troubleshooting"></a>Rozwiązywanie problemów z rozwiązaniem

Zapoznaj się z poniższymi wskazówkami dotyczącymi rozwiązywania problemów dotyczących rozwiązań niezwiązanych z obsługą kodu w usłudze Azure Percept Studio.

## <a name="delete-a-vision-project"></a>Usuń projekt wizji

1. Przejdź do witryny https://www.customvision.ai/projects.

1. Umieść kursor nad projektem, który chcesz usunąć, a następnie kliknij ikonę kosza, aby usunąć projekt.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Strona projekty w Custom Vision z wyróżnioną ikoną usuwania.":::

## <a name="check-which-modules-are-on-a-device"></a>Sprawdź, które moduły znajdują się na urządzeniu

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Kliknij ikonę **Centrum IoT** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Azure Portal Strona główna z wyróżnioną ikoną Centrum IoT." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Wybierz IoT Hub, z którym jest połączone urządzenie docelowe.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Lista centrów IoT.":::

1. Wybierz pozycję **IoT Edge** i kliknij urządzenie na karcie **Identyfikator urządzenia** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="IoT Edge stronie głównej.":::

1. Moduły urządzenia będą wyświetlane na karcie **moduły** .

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Strona usługi IoT Edge dla wybranego urządzenia z zawartością karty moduły." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Usuwanie urządzenia

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Kliknij ikonę **Centrum IoT** .

1. Wybierz IoT Hub, z którym jest połączone urządzenie docelowe.

1. Wybierz **IoT Edge** i zaznacz pole wyboru obok identyfikatora urządzenia docelowego. Kliknij ikonę kosza, aby usunąć urządzenie.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Ikona usuwania wyróżniona na stronie głównej programu IoT Edge.":::

## <a name="eye-module-troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów z modułem oczu

Jeśli wystąpił problem z **WebStreamModule**, upewnij się, że **azureeyemodule**, który obsługuje model Vision inferencing, jest uruchomiony. Aby sprawdzić stan środowiska uruchomieniowego, przejdź do [Azure Portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) i przejdź do obszaru **wszystkie zasoby**  ->  **\<your IoT hub>**  ->  **IoT Edge**  ->  **\<your device ID>** . Kliknij kartę **moduły** , aby wyświetlić stan środowiska uruchomieniowego wszystkich zainstalowanych modułów.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Ekran stanu środowiska uruchomieniowego modułu urządzenia." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Jeśli stan środowiska uruchomieniowego **azureeyemodule** nie jest wymieniony jako **uruchomiony**, kliknij pozycję **Ustaw moduły**  ->  **azureeyemodule**. Na stronie **Ustawienia modułu** Ustaw **żądany stan** na **uruchomiony** , a następnie kliknij przycisk **Aktualizuj**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Ekran konfiguracji ustawienia modułu.":::

## <a name="view-device-rtsp-video-stream"></a>Wyświetl strumień wideo RTSP urządzenia

Wyświetl strumień wideo RTSP urządzenia w [usłudze Azure Percept Studio](./how-to-view-video-stream.md) lub [VLC Media Player](https://www.videolan.org/vlc/index.html).

Aby otworzyć strumień RTSP w programie VLC Media Player, przejdź do pozycji **multimedia**  ->  **Open Network Stream**  ->  **RTSP://[adres IP urządzenia]/Result**.

## <a name="next-steps"></a>Następne kroki

Zobacz [ogólny przewodnik rozwiązywania problemów](./troubleshoot-dev-kit.md) , aby uzyskać więcej informacji na temat rozwiązywania problemów z platformą Azure Percept DK.