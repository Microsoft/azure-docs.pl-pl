---
title: Wprowadzenie do usługi Azure Percept audio
description: Dowiedz się, jak podłączyć urządzenie audio usługi Azure Percept do platformy Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179276"
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

1. Podłącz urządzenie audio usługi Azure Percept do tablicy nośnej Azure Percept DK z dołączonym mikroUSB do typu USB — kablem. Podłącz końcówkę mikrousba kabla do tablicy międzyskładnikowej (deweloper) i typu-A końca do tablicy nośnej Percept.
1. (Opcjonalnie) Połącz prelegenta lub słuchawkę z Perceptem audio platformy Azure za pośrednictwem gniazda audio, które jest oznaczone jako "line out". Pozwoli to na słuchanie odpowiedzi dźwiękowych asystenta głosowego. Jeśli nie połączysz prelegenta lub słuchawki, nadal będzie można zobaczyć odpowiedzi jako tekst w oknie demonstracji. 

1. Włącz Devkit. Dioda LED L02 na tablicy międzyłożonej zmieni się na miganie bieli, aby wskazać, że urządzenie zostało włączone i że jest uwierzytelniany model audio SoM.

1. Poczekaj na zakończenie procesu uwierzytelniania — może to potrwać do 3 minut.

1. Możesz rozpocząć tworzenie prototypów, gdy zobaczysz jedną z następujących czynności:

    - L02 LED zmieni się na pełny biały. Oznacza to, że uwierzytelnianie jest ukończone i Devkit nie został jeszcze skonfigurowany za pomocą słowa kluczowego.
    - Wszystkie trzy diody LED zmienią kolor na niebieski. Oznacza to, że uwierzytelnianie jest ukończone i Devkit jest skonfigurowany za pomocą słowa kluczowego.

## <a name="next-steps"></a>Następne kroki

Utwórz [rozwiązanie mowy bez kodu](./tutorial-no-code-speech.md) w [usłudze Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
