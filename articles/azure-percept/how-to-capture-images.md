---
title: Wdrażanie modelu programu Vision AI na platformie Azure Percept DK
description: Dowiedz się, jak wdrożyć model Vision AI na platformie Azure Percept DK z usługi Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5ad263f5ae3b0b8e91de30b620002e33086608b4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662856"
---
# <a name="capture-images-for-a-vision-project"></a>Przechwyć obrazy dla projektu programu Vision

Postępuj zgodnie z tym przewodnikiem, aby przechwycić obrazy przy użyciu modelu programu Visioning Percept Jeśli projekt programu Vision nie został jeszcze utworzony, zapoznaj się z [samouczkiem bez obsługi kodu](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Środowisko instalacyjne platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): nawiązano połączenie Devkit z siecią Wi-Fi, utworzono IoT Hub i połączono devkit z IoT Hub
- [Projekt bez obsługi kodu](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Przechwytywanie obrazów

1. Włącz Devkit.

1. Przejdź do [usługi Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Po lewej stronie strony Przegląd kliknij pozycję **urządzenia**.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Ekran omówienia usługi Azure Percept Studio." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Wybierz z listy swój Devkit.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Lista urządzeń Percept.":::

1. Na stronie urządzenie kliknij pozycję **Przechwyć obrazy dla projektu**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Strona urządzenia Percept z dostępnymi akcjami.":::

1. W oknie **Przechwytywanie obrazu** wykonaj następujące czynności:

    1. W menu rozwijanym **projekt** wybierz projekt wizji, dla którego chcesz zebrać obrazy.

    1. Kliknij pozycję **Wyświetl strumień urządzenia** , aby upewnić się, że aparat modelu "Visioning" został poprawnie umieszczony.

    1. Kliknij pozycję **Zrób zdjęcie** , aby przechwycić obraz.

    1. Możesz również zaznaczyć pole wyboru obok pozycji **automatyczny przechwycenie obrazu** , aby skonfigurować czasomierz przechwytywania obrazu:

        1. Wybierz preferowaną szybkość przetwarzania obrazu w obszarze **szybkość przechwytywania**.
        1. Wybierz łączną liczbę obrazów, które mają być zbierane w obszarze **cel**.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Strona urządzenia Percept z dostępnymi akcjami.":::

Wszystkie obrazy będą dostępne w [Custom Vision](https://www.customvision.ai/).

## <a name="next-steps"></a>Następne kroki

[Testowanie i ponowne uczenie modelu wizji bez kodu](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model).