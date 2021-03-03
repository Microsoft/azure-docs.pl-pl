---
title: Informacje o urządzeniu audio usługi Azure Percept
description: Dowiedz się więcej o usłudze Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663685"
---
# <a name="introduction-to-azure-percept-audio"></a>Wprowadzenie do usługi Azure Percept audio

Usługa Azure Percept audio to urządzenie do akcesoriów, które dodaje możliwości mowy do usługi Azure Percept DK. Zawiera wstępnie skonfigurowany akcelerator typu Speech AI oraz tablicę liniową z czterema mikrofonami, umożliwiając stosowanie poleceń niestandardowych, słów kluczowych wykrywania trendów i daleko pól do miejscowego nasłuchiwania. Usługa Azure Percept audio umożliwia producentom urządzeń rozbudowanie możliwości programu Azure Percept DK Vision do nowych, inteligentnych urządzeń aktywowanych głosowo. Jest ona zintegrowana z platformą Azure Percept DK, Azure Percept Studio i innymi usługami Azure Edge Management. Jest ona dostępna do zakupu w [Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

![Urządzenie audio usługi Azure Percept.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Składniki audio usługi Azure Percept

Usługa Azure Percept audio zawiera następujące główne składniki:

- Gotowe do produkcji urządzenie audio usługi Azure Percept (SoM) z tablicą liniową z czterema mikrofonami
- Tablica deweloperów (w tym 2 przyciski, 3. diody LED, złącze USB Micro i 3,5 mm audio)
- Wymagane kable: kabel Flex, USB Micro Type-B do USB-A
- Karta powitalna
- Płyta montażowa mechaniczna z integracją instalacji z serii 80/20 1010


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>Wprowadzenie

- [Złóż platformę Azure Percept DK](./quickstart-percept-dk-unboxing.md)
- [Ukończ działanie Instalatora platformy Azure Percept DK](./quickstart-percept-dk-set-up.md)
- [Łączenie urządzenia audio usługi Azure Percept z Devkit](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>Kompilowanie prototypu bez kodu

Twórz [rozwiązanie do obsługi mowy bez kodu](./tutorial-no-code-speech.md) przy użyciu szablonów asystentów głosowych usługi Azure Percept dla scenariuszy Hotelarstwo, opieki zdrowotnej, spisu i sieci motoryzacyjnej.

## <a name="additional-technical-information"></a>Dodatkowe informacje techniczne

- [Arkusz danych audio usługi Azure Percept](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Następne kroki

Zamów urządzenie audio usługi Azure Percept w [Sklepie Microsoft Online](https://go.microsoft.com/fwlink/p/?LinkId=2155270).