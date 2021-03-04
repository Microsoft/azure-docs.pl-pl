---
title: Wyświetlanie danych telemetrycznych o wnioskach o modelu w usłudze Azure Percept DK
description: Dowiedz się, jak wyświetlić dane telemetryczne dotyczące modelowania Percept w wersji zapoznawczej platformy Azure w programie Azure IoT Explorer
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 07abbc5f5e85c75b73774d11b6b81dd2085735b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095327"
---
# <a name="view-your-azure-percept-dks-model-inference-telemetry"></a>Wyświetlanie danych telemetrycznych o wnioskach o modelu w usłudze Azure Percept DK

Postępuj zgodnie z tym przewodnikiem, aby wyświetlić dane telemetryczne dotyczące modelowania Microsoft Azure Percept w wersji zapoznawczej w [Eksploratorze usługi Azure IoT](https://github.com/Azure/azure-iot-explorer/releases).

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Środowisko instalacyjne platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): nawiązano połączenie Devkit z siecią Wi-Fi, utworzono IoT Hub i połączono devkit z IoT Hub
- [Model Vision AI został wdrożony na platformie Azure Percept DK](./how-to-deploy-model.md)

## <a name="view-telemetry"></a>Wyświetlanie danych telemetrycznych

1. Włącz Devkit.

1. Pobierz i zainstaluj program [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases). Jeśli jesteś użytkownikiem systemu Windows, wybierz plik msi.

    :::image type="content" source="./media/how-to-view-telemetry/azure-iot-explorer-download.png" alt-text="Ekran pobierania dla programu Azure IoT Explorer.":::

1. Połącz IoT Hub z Eksploratorem usługi Azure IoT:

    1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

    1. Wybierz pozycję **Wszystkie zasoby**.

        :::image type="content" source="./media/how-to-view-telemetry/azure-portal.png" alt-text="Azure Portal stronie głównej.":::

    1. Wybierz IoT Hub, z którym jest połączona usługa Azure Percept.

        :::image type="content" source="./media/how-to-view-telemetry/iot-hub.png" alt-text="Lista IoT Hub w Azure Portal.":::

    1. Po lewej stronie IoT Hub wybierz pozycję **zasady dostępu współdzielonego**.

        :::image type="content" source="./media/how-to-view-telemetry/shared-access-policies.png" alt-text="IoT Hub Strona zawierająca zasady dostępu współdzielonego.":::

    1. Kliknij pozycję **iothubowner**.

        :::image type="content" source="./media/how-to-view-telemetry/iothubowner.png" alt-text="Ekran zasady dostępu współdzielonego z wyróżnioną pozycją iothubowner.":::

    1. Kliknij ikonę niebieskiego kopiowania obok pozycji **Parametry połączenia — klucz podstawowy**.

        :::image type="content" source="./media/how-to-view-telemetry/connection-string.png" alt-text="okno iothubowner z wyróżnionym przyciskiem kopiowania parametrów połączenia.":::

    1. Otwórz program Azure IoT Explorer i kliknij pozycję **+ Dodaj połączenie**.

    1. Wklej parametry połączenia w polu **Parametry połączenia** w oknie **Dodawanie parametrów połączenia** i kliknij przycisk **Zapisz**.

        :::image type="content" source="./media/how-to-view-telemetry/add-connection-string.png" alt-text="Okno programu Azure IoT Explorer z polem wklejania parametrów połączenia do programu.":::

    1. Wskaż element SoM "Vision" w obiekcie dla modelu inferencing.

    1. Wybierz pozycję **Telemetria**.

    1. Kliknij przycisk **Uruchom** , aby wyświetlić zdarzenia telemetrii z urządzenia.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak wyświetlić [strumień wideo dotyczący usługi Azure PERCEPT DK](./how-to-view-video-stream.md).