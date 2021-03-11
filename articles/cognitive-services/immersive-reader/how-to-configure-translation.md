---
title: Konfigurowanie tłumaczenia
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób konfigurowania różnych opcji tłumaczenia.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: bb90cb3a86acb6a94fa92c33d78ec596659e105f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608703"
---
# <a name="how-to-configure-translation"></a>Jak skonfigurować tłumaczenie

W tym artykule pokazano, jak skonfigurować różne opcje tłumaczenia w czytniku immersyjny.

## <a name="configure-translation-language"></a>Konfigurowanie języka tłumaczenia

`options`Parametr zawiera wszystkie flagi, których można użyć do skonfigurowania tłumaczenia. Ustaw `language` parametr na język, który chcesz przetłumaczyć. Zobacz [obsługę języka](./language-support.md) , aby zapoznać się z pełną listą obsługiwanych języków.

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Automatycznie Przetłumacz dokument przy ładowaniu

Ustaw `autoEnableDocumentTranslation` na `true` , aby włączyć automatyczne tłumaczenie całego dokumentu podczas ładowania czytnika immersyjny.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Automatyczne włączanie tłumaczenia słów

Ustaw `autoEnableWordTranslation` , aby `true` włączyć tłumaczenie pojedynczego słowa.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [Kompendium zestawu SDK czytnika immersyjny](./reference.md)