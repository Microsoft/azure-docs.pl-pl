---
title: Omówienie usługi Azure Percept DK
description: Dowiedz się więcej o platformie Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 74e7d1a54b1d760979dbf9833e85ec728b4e5e3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595893"
---
# <a name="azure-percept-dk-overview"></a>Omówienie usługi Azure Percept DK

Azure Percept DK to zestaw "Edge AI i IoT Development Kit" przeznaczony do opracowywania koncepcji wzrokowych i dźwiękowych AI. W połączeniu z usługami [Azure Percept Studio](./overview-azure-percept-studio.md) i [Azure Percept audio](./overview-azure-percept-audio.md)jest to wydajna i prosta platforma służąca do tworzenia rozwiązań dla urządzeń z systemem Edge, obejmująca szeroką gamę aplikacji programistycznych lub audio AI. Jest ona dostępna do zakupu w [Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Kup teraz](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

<!---
:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="Azure Percept DK device.":::
--->
</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="key-features"></a>Najważniejsze funkcje

- **Możliwość uruchamiania AI na krawędzi**. Dzięki wbudowanemu przyspieszeniu sprzętowym można uruchamiać modele Vision AI bez połączenia z chmurą.
- **Wbudowany sprzęt główny zabezpieczeń zaufania**. Aby uzyskać więcej informacji, zobacz ten przegląd [zabezpieczeń usługi Azure Percept](./overview-percept-security.md) .
- **Bezproblemowa integracja z [usługą Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)** i innymi usługami platformy Azure. Na przykład Azure IoT Hub, Azure Cognitive Services i [Analiza filmów wideo na żywo](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview)
- **Bezproblemowa integracja z opcjonalnym użyciem [usługi Azure Percept audio](./overview-azure-percept-audio.md)**
- **Obsługa najważniejszych platform AI**. Takie jak ONNX i TensorFlow.
- **Integracja z systemem kolei 80/20**. Łatwiejsze tworzenie prototypów w środowiskach produkcyjnych. Dowiedz się więcej o [integracji 80/20](./overview-8020-integration.md).

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

> [!div class="nextstepaction"]
> [Kupowanie usługi Azure Percept DK ze sklepu Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
