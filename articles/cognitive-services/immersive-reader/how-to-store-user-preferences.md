---
title: Zachowaj preferencje użytkownika
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób przechowywania preferencji użytkownika.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486923"
---
# <a name="how-to-store-user-preferences"></a>Jak przechowywać preferencje użytkownika

W tym artykule przedstawiono sposób przechowywania preferencji użytkownika. Ta funkcja jest przeznaczona jako alternatywna metoda przechowywania preferencji użytkownika w przypadku, gdy używanie plików cookie nie jest pożądane lub możliwe.

## <a name="how-to-enable-storing-user-preferences"></a>Jak włączyć przechowywanie preferencji użytkownika

`options`Parametr zawiera `onPreferencesChanged` wywołanie zwrotne. Ta funkcja będzie wywoływana w dowolnym momencie, gdy użytkownik zmieni swoje preferencje (na przykład zmieni rozmiar tekstu, kolor motywu, czcionkę itd.). `value`Parametr zawiera ciąg, który reprezentuje bieżące preferencje użytkownika. Ten ciąg może być przechowywany przy użyciu dowolnego preferowanego mechanizmu.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Jak załadować preferencje użytkownika do czytnika immersyjny

Przekaż preferencje użytkownika do czytnika immersyjny przy użyciu `preferences` parametru. Oto uproszczony przykład przechowywania i ładowania preferencji użytkownika:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [Kompendium zestawu SDK czytnika immersyjny](./reference.md)