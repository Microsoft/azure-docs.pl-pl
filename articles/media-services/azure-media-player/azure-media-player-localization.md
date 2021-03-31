---
title: Lokalizacja Azure Media Player
description: Obsługa wielu języków dla użytkowników w językach innych niż angielski.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 8a459d1fba4353b4b3e092e83a759314cc455ead
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87087292"
---
# <a name="azure-media-player-localization"></a>Lokalizacja Azure Media Player #

Obsługa wielu języków umożliwia użytkownikom w językach innych niż angielski interaktywne korzystanie z odtwarzacza. Azure Media Player będzie tworzyć wystąpienia z słownikiem globalnym języków, który lokalizuje komunikaty o błędach na podstawie języka strony. Deweloper może również utworzyć wystąpienie odtwarzacza z wymuszonym ustawionym językiem. Język domyślny to angielski (EN).

> [!NOTE]
> Ta funkcja nadal przeprowadzi proces niektórych testów i podlega błędów.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player obecnie obsługuje następujące języki z odpowiednimi kodami języka:

| Język            | Kod | Język                | Kod   | Język                | Kod         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Angielski {domyślnie}   | en   | Chorwacki                | godz.     | Rumuński                | ro           |
| Arabski              | ty   | Węgierski               | Węgry     | Słowacki                  | SK           |
| Bułgarski           | BG   | Indonezyjski              | identyfikator     | Języka                 | SL           |
| Kataloński             | urzędu certyfikacji   | Islandzki               | is     | Serbski – cyrylica      | Wirtualizacja SR-Cyrl-CS   |
| Czeski               | Rejestr   | Włoski                 | it     | Serbski – łaciński         | sr-latn-rs   |
| Duński              | da   | japoński                | ja     | Rosyjski                 | ru           |
| Niemiecki              | de   | Kazachski                  | kk     | Szwedzki                 | sv           |
| Grecki               | Colon   | Koreański                  | Ko     | Tajlandzki                    | th           |
| Hiszpański             | es   | Litewski              | lt     | Tagalski                 | Przełącznik           |
| Estoński            | staw   | Łotewski                 | LV     | Turecki                 | zdawczy           |
| Baskijski              | Europejska   | Malezji               | Arial     | Ukraiński               | Południowe Zjednoczone Królestwo           |
| Perski               | FA   | Norweski — BokmÃ ¥ l     | sesja     | Urdu                    | publikacj           |
| Fiński             | fi   | Niderlandzki                   | nl     | Wietnamski              | VI           |
| Francuski              | fr   | Norweski — nynorsk     | nn     | Chiński (uproszczony)    | zh-Hans      |
| Galicyjski            | GL   | Polski                  | zysków     | Chiński (tradycyjny)   | zh-Hant      |
| Hebrajski              | Przewodniczący   | Portugalski (Brazylia)     | pt-br  |                         |              |
| Hindi               | Cześć   | Portugalski (Portugalia)   | pt-pt  |                         |              |


> [!NOTE]
> Jeśli nie chcesz mieć żadnej lokalizacji, musisz wymusić, aby język był w języku angielskim

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
