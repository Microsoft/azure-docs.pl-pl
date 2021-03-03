---
title: Wprowadzenie do usługi Azure Percept audio
description: Dowiedz się, jak podłączyć urządzenie audio usługi Azure Percept do platformy Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665500"
---
# <a name="azure-percept-audio-setup"></a>Konfiguracja audio usługi Azure Percept

Usługa Azure Percept audio działa od pudełka z platformą Azure Percept DK. Nie jest wymagana żadna unikatowa konfiguracja.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- Azure Percept — dźwięk
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Środowisko instalacyjne platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): nawiązano połączenie Devkit z siecią Wi-Fi, utworzono IoT Hub i połączono devkit z IoT Hub

## <a name="connecting-your-devices"></a>Łączenie urządzeń

1. Podłącz urządzenie audio usługi Azure Percept do tablicy nośnej Azure Percept DK z dołączonym mikrotypem typu USB-B do typu USB-kabla. Podłącz mikrotype-B koniec kabla do modelu SoM audio i typu-A końca do tablicy nośnej Percept.

1. Włącz Devkit.

    - Dioda LED L01 w modelu SoM audio zmieni się na Solid zielony, aby wskazać, że urządzenie zostało włączone.
    - L02 diody LED zmieni się na miganie, aby wskazać, że model SoM audio jest uwierzytelniany.

1. Poczekaj na zakończenie procesu uwierzytelniania — może to potrwać do 3 minut.

1. Możesz rozpocząć tworzenie prototypów, gdy zobaczysz jedną z następujących czynności:

    - Dioda LED L01 wyłącza się, a L02 wyłącza kolor biały. Oznacza to, że uwierzytelnianie jest ukończone i Devkit nie został jeszcze skonfigurowany za pomocą słowa kluczowego.
    - Wszystkie trzy diody LED zmienią kolor na niebieski. Oznacza to, że uwierzytelnianie jest ukończone i Devkit jest skonfigurowany za pomocą słowa kluczowego.

    > [!NOTE]
    > Skontaktuj się z pomocą techniczną, aby dowiedzieć się, czy Devkit nie uwierzytelnia się.

## <a name="next-steps"></a>Następne kroki

Utwórz [rozwiązanie do obsługi mowy bez kodu](./tutorial-no-code-speech.md).