---
title: Konfigurowanie usługi Azure Percept audio
description: Dowiedz się, jak podłączyć urządzenie audio usługi Azure Percept do platformy Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605794"
---
# <a name="azure-percept-audio-setup"></a>Konfiguracja audio usługi Azure Percept

Usługa Azure Percept audio działa od pudełka z platformą Azure Percept DK. Nie jest wymagana żadna unikatowa konfiguracja.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- Azure Percept — dźwięk
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Środowisko instalacyjne platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): nawiązano połączenie Devkit z siecią Wi-Fi, utworzono IoT Hub i połączono devkit z IoT Hub
- Głośnik lub słuchawki, które mogą łączyć się z gniazdem audio 3,5 mm (opcjonalnie)

## <a name="connecting-your-devices"></a>Łączenie urządzeń

1. Podłącz urządzenie audio usługi Azure Percept do tablicy nośnej Azure Percept DK z dołączonym mikroUSB do typu USB — kablem. Podłącz końcówkę mikrousba kabla do tablicy międzyetapowej audio (deweloper) i typu-A końca do tablicy nośnej Percept.

1. (Opcjonalnie) Połącz prelegenta lub słuchawkę z urządzeniem audio Percept na platformie Azure za pośrednictwem gniazda audio oznaczonego jako "line out". Umożliwi to słuchanie odpowiedzi na dźwięk.

1. Włącz Devkit. Dioda LED L02 na płycie wieloetapowej audio zmieni się na miganie, aby wskazać, że urządzenie zostało włączone i że model SoM audio jest uwierzytelniany.

1. Poczekaj na zakończenie procesu uwierzytelniania — może to potrwać do 3 minut.

1. Możesz rozpocząć tworzenie prototypów, gdy zobaczysz jedną z następujących czynności:

    - L02 LED zmieni się na pełny biały: oznacza to, że uwierzytelnianie jest ukończone, a Devkit nie został jeszcze skonfigurowany za pomocą słowa kluczowego.
    - Wszystkie trzy diody LED zmienią się na niebieską: oznacza to, że uwierzytelnianie jest ukończone i Devkit jest skonfigurowany za pomocą słowa kluczowego.

## <a name="next-steps"></a>Następne kroki

Utwórz [rozwiązanie mowy bez kodu](./tutorial-no-code-speech.md) w [usłudze Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
