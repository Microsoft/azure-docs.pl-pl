---
title: Dostosowywanie przycisku czytnika immersyjnego
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób dostosowywania przycisku uruchamiającego czytnik immersyjny.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: b24a9dcb7a369708b1374f367186dc4d9a6ce475
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146879"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Jak dostosować przycisk czytnika immersyjny

W tym artykule pokazano, jak dostosować przycisk, który uruchamia czytnik immersyjny w celu dopasowania do potrzeb aplikacji.

## <a name="add-the-immersive-reader-button"></a>Dodaj przycisk czytnika immersyjny

Zestaw SDK czytnika immersyjny zawiera domyślne style dla przycisku uruchamiającego czytnik immersyjny. Użyj `immersive-reader-button` atrybutu Class, aby włączyć ten styl.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Dostosuj styl przycisku

Użyj `data-button-style` atrybutu, aby ustawić styl przycisku. Dozwolone wartości to `icon` , `text` , i `iconAndText` . Wartość domyślna to `icon`.

### <a name="icon-button"></a>Przycisk ikony

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Renderuje następujące elementy:

![Jest to renderowany przycisk tekstu](./media/button-icon.png)

### <a name="text-button"></a>Przycisk tekstu

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Renderuje następujące elementy:

![Jest to renderowany przycisk czytnika immersyjny.](./media/button-text.png)

### <a name="icon-and-text-button"></a>Przycisk ikony i tekstu

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Renderuje następujące elementy:

![Przycisk ikony](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Dostosuj tekst przycisku

Skonfiguruj język i tekst alternatywny dla przycisku przy użyciu `data-locale` atrybutu. Językiem domyślnym jest język angielski.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Dostosuj rozmiar ikony

Rozmiar ikony czytnika immersyjny można skonfigurować przy użyciu `data-icon-px-size` atrybutu. Ustawia rozmiar ikony w pikselach. Domyślny rozmiar to 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [Kompendium zestawu SDK czytnika immersyjny](./reference.md)