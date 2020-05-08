---
title: Lokalizacja Azure Media Player
description: Obsługa wielu języków dla użytkowników w językach innych niż angielski.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: ca4dc888af414ede270118eff72652f098d3306c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779046"
---
# <a name="localization"></a>Lokalizacja #

Obsługa wielu języków umożliwia użytkownikom w językach innych niż angielski interaktywne korzystanie z odtwarzacza. Azure Media Player będzie tworzyć wystąpienia z słownikiem globalnym języków, który lokalizuje komunikaty o błędach na podstawie języka strony. Deweloper może również utworzyć wystąpienie odtwarzacza z wymuszonym ustawionym językiem. Język domyślny to angielski (EN).

> [!NOTE]
> Ta funkcja nadal przeprowadzi proces niektórych testów i podlega błędów.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player obecnie obsługuje następujące języki z odpowiednimi kodami języka:

| Język            | Code | Język                | Code   | Język                | Code         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Angielski {domyślnie}   | pl   | Chorwacki                | Kadry     | Rumuński                | ro           |
| Arabski              | ty   | węgierski               | Węgry     | Słowacki                  | SK           |
| Bułgarski           | BG   | Indonezyjski              | id     | Języka                 | SL           |
| Kataloński             | urzędu certyfikacji   | Islandzki               | is     | Serbski — Cyrylica      | Wirtualizacja SR-Cyrl-CS   |
| Czeski               | Rejestr   | Włoski                 | it     | Serbski (łaciński)         | sr-latn-rs   |
| duński              | da   | japoński                | ja     | Rosyjski                 | ru           |
| niemiecki              | de   | Kazachski                  | kk     | szwedzki                 | sv           |
| grecki               | Colon   | koreański                  | Ko     | Tajski                    | th           |
| Hiszpański             | Tak   | Litewski              | lt     | Tagalski                 | Przełącznik           |
| Estoński            | staw   | Łotewski                 | LV     | Turecki                 | zdawczy           |
| Baskijski              | Europejska   | Malezji               | Arial     | Ukraiński               | Południowe Zjednoczone Królestwo           |
| Perski               | FA   | Norweski — BokmÃ ¥ l     | sesja     | Urdu                    | publikacj           |
| fiński             | fi   | Niderlandzki                   | nl     | Wietnamski              | VI           |
| Francuski              | fr   | Norweski — nynorsk     | nn     | Chiński (uproszczony)    | zh-Hans      |
| Galicyjski            | GL   | Polski                  | zysków     | Chiński (tradycyjny)   | zh-Hant      |
| Hebrajski              | Przewodniczący   | Portugalski (Brazylia)     | pt-br  |                         |              |
| Hindi               | Cześć   | Portugalski (Portugalia)   | pt-pt  |                         |              |


> [!NOTE]
> Jeśli nie chcesz mieć żadnej lokalizacji, musisz wymusić, aby język był w języku angielskim

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
