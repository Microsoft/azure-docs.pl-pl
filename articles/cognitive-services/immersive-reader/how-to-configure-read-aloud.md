---
title: Konfigurowanie czytania na głos
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób konfigurowania różnych opcji odczytywania na głos.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 8b45fe07b4bd42059199197bc5b99f20f6b2a8cf
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608720"
---
# <a name="how-to-configure-read-aloud"></a>Jak skonfigurować odczytywanie na głos

W tym artykule pokazano, jak skonfigurować różne opcje odczytu na głos w czytniku immersyjny.

## <a name="automatically-start-read-aloud"></a>Automatycznie Rozpocznij odczytywanie na głos

`options`Parametr zawiera wszystkie flagi, których można użyć do skonfigurowania odczytywania na głos. Ustaw `autoplay` , aby `true` włączyć automatyczne Rozpoczynanie odczytywania na głos po uruchomieniu czytnika immersyjny.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> Ze względu na ograniczenia przeglądarki przeglądarka Safari nie obsługuje automatycznego odtwarzania.

## <a name="configure-the-voice"></a>Konfigurowanie głosu

Ustaw wartość `voice` `male` lub `female` . Nie wszystkie języki obsługują oba głosy. Aby uzyskać więcej informacji, zobacz stronę [Obsługa języka](./language-support.md) .

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>Konfigurowanie szybkości odtwarzania

Ustaw `speed` liczbę z zakresu od `0.5` (50%) i `2.5` (250%) Pobiera. Wartości spoza tego zakresu zostaną załączone do 0,5 lub 2,5.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [Kompendium zestawu SDK czytnika immersyjny](./reference.md)