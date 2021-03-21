---
title: Wdrażanie modelu programu Vision AI na platformie Azure Percept DK
description: Dowiedz się, jak wdrożyć model Vision AI na platformie Azure Percept DK z usługi Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 01bd3709050d8a2b57c1bf51920308188546fb31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035487"
---
# <a name="deploy-a-vision-ai-model-to-your-azure-percept-dk"></a>Wdrażanie modelu programu Vision AI na platformie Azure Percept DK

Postępuj zgodnie z tym przewodnikiem, aby wdrożyć model Vision AI na platformie Azure Percept DK z poziomu platformy Azure Percept Studio.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Środowisko instalacyjne platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): nawiązano połączenie Devkit z siecią Wi-Fi, utworzono IoT Hub i połączono devkit z IoT Hub

## <a name="model-deployment"></a>Wdrożenie modelu

1. Włącz Devkit.

1. Przejdź do [usługi Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Po lewej stronie strony Przegląd kliknij pozycję **urządzenia**.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Ekran omówienia usługi Azure Percept Studio." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Wybierz z listy swój Devkit.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Lista urządzeń Percept.":::

1. Na następnej stronie kliknij pozycję **Wdróż przykładowy model** , jeśli chcesz wdrożyć jeden z wstępnie przeszkolonych modeli wizji. Jeśli chcesz wdrożyć istniejące [niestandardowe rozwiązanie bez obsługi kodu](./tutorial-nocode-vision.md), kliknij przycisk **Wdróż projekt Custom Vision**.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Wybór modelu dla wdrożenia.":::

1. Jeśli wybrano wdrożenie rozwiązania bez obsługi kodu, wybierz projekt i preferowaną iterację modelu, a następnie kliknij pozycję **Wdróż**.

1. W przypadku wybrania opcji wdrożenia przykładowego modelu wybierz model, a następnie kliknij pozycję **Wdróż na urządzeniu**.

1. Po pomyślnym wdrożeniu modelu w prawym górnym rogu ekranu zostanie wyświetlony komunikat o stanie. Aby wyświetlić inferencing modelu w akcji, kliknij link **Wyświetl strumień** w komunikacie o stanie, aby zobaczyć strumień wideo RTSP z modelu "Vision Modeling" Devkit.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wyświetlić dane [telemetryczne w usłudze Azure PERCEPT DK](how-to-view-telemetry.md).