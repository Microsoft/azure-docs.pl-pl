---
title: Omówienie usługi Azure Percept DK
description: Dowiedz się więcej o platformie Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 4fd0a7cb575a109d1393527b48de3fa4e3446167
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663469"
---
# <a name="azure-percept-dk-overview"></a>Omówienie usługi Azure Percept DK

Azure Percept DK to zestaw Development Kit przeznaczony do opracowywania koncepcji programu Vision AI. W połączeniu z [usługą Azure Percept Studio](./overview-azure-percept-studio.md)stał się to wydajną, łatwą w użyciu platformą do tworzenia rozwiązań dla systemu. Jest ona dostępna do zakupu w [Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="Image.":::

## <a name="key-features"></a>Najważniejsze funkcje

- **Możliwość uruchamiania AI na krawędzi**. Dzięki wbudowanemu przyspieszeniu sprzętowym można uruchamiać modele Vision AI bez połączenia z chmurą.
- **Wbudowany sprzęt główny zabezpieczeń zaufania**. Aby uzyskać więcej informacji, zobacz ten przegląd [zabezpieczeń usługi Azure Percept](./overview-percept-security.md) .
- **Bezproblemowa integracja z [usługą Azure Percept Studio](./overview-azure-percept-studio.md)** i innymi usługami platformy Azure. Na przykład Azure IoT Hub, Azure Cognitive Services i [Analiza filmów wideo na żywo](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **Obsługa najważniejszych platform AI**. Takie jak ONNX i TensorFlow.
- **Integracja z systemem kolei 80/20**. Łatwiejsze tworzenie prototypów w środowiskach produkcyjnych. Dowiedz się więcej o [integracji 8/20](./overview-8020-integration.md).

## <a name="hardware-components"></a>Składniki sprzętowe

- Tablica przewoźnika usługi Azure Percept DK
    - Procesor NXP iMX8m
    - Trusted Platform Module (TPM), wersja 2,0
    - Łączność Wi-Fi i Bluetooth
    - Zobacz pełny [Arkusz danych](./azure-percept-dk-datasheet.md)
- System Vision Percept na platformie Azure w module (SoM)
    - Jednostka przetwarzania wzrokowego Intel Movidius wyposażono X (MA2085) (VPU)
    - Czujnik kamery RGB z możliwością dodania sekundy
    - Zobacz pełny [Arkusz danych](./azure-percept-vision-datasheet.md)

## <a name="get-started-with-the-azure-percept-dk"></a>Wprowadzenie do usługi Azure Percept DK

- Wykonaj te Przewodniki Szybki Start
    - [Unbox i złóż platformę Azure Percept DK](./quickstart-percept-dk-unboxing.md)
    - [Skonfiguruj platformę Azure Percept DK i Uruchom swój pierwszy model systemu AI](./quickstart-percept-dk-set-up.md)
- Zacznij tworzyć potwierdzenia koncepcji za pomocą tych samouczków
    - [Tworzenie rozwiązania "bez obsługi kodu" w usłudze Azure Percept Studio](./tutorial-nocode-vision.md)
    - [Tworzenie asystenta głosowego w usłudze Azure Percept Studio](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>Następne kroki

Zamów usługę Azure Percept DK w [Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270).