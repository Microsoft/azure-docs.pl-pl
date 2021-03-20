---
title: Wyświetl strumień wideo RTSP usługi Azure Percept DK
description: Dowiedz się, jak wyświetlić strumień wideo RTSP z platformy Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5f77e99dc5c34867fef2b0ac47c709824fa4477d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096007"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Wyświetl strumień wideo RTSP usługi Azure Percept DK

Postępuj zgodnie z tym przewodnikiem, aby wyświetlić strumień wideo RTSP z platformy Azure Percept DK w ramach platformy Azure Percept Studio. Inferencing z modeli "Vision AI" wdrożonych na urządzeniu będą widoczne w strumieniu sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Środowisko instalacyjne platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): nawiązano połączenie Devkit z siecią Wi-Fi, utworzono IoT Hub i połączono devkit z IoT Hub

## <a name="view-the-rtsp-video-stream"></a>Wyświetlanie strumienia wideo RTSP

1. Włącz Devkit.

1. Przejdź do [usługi Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Po lewej stronie strony Przegląd kliknij pozycję **urządzenia**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Ekran omówienia usługi Azure Percept Studio." lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Wybierz z listy swój Devkit.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Zrzut ekranu przedstawiający dostępne urządzenia w usłudze Azure Percept Studio.":::

1. Kliknij pozycję **Wyświetl strumień urządzenia**.

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Zrzut ekranu przedstawiający stronę urządzenia z widocznymi akcjami projektu wizji.":::

    Spowoduje to otwarcie oddzielnej karty zawierającej strumień sieci Web na żywo z platformy Azure Percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Zrzut ekranu przedstawiający strumień sieci Web urządzenia.":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wyświetlić dane [telemetryczne w usłudze Azure PERCEPT DK](./how-to-view-telemetry.md).