---
title: Konfigurowanie tłumaczenia
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób konfigurowania różnych opcji tłumaczenia.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 0a6ed6ecea216a36cfc9da4ef36a2bddc69cc6a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633148"
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