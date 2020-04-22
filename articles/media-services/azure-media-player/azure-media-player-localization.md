---
title: Lokalizacja programu Azure Media Player
description: Obsługa wielu języków dla użytkowników ustawień regionalnych innych niż angielski.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: 34805c4eaae5d969fc2338c24f9f92404e065d15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727179"
---
# <a name="localization"></a>Lokalizacja #

Obsługa wielu języków umożliwia użytkownikom ustawień regionalnych innych niż angielskie na natywną interakcję z odtwarzaczem. Usługa Azure Media Player będzie tworzenie wystąpienia z globalnego słownika języków, który będzie lokalizować komunikaty o błędach na podstawie języka strony. Deweloper może również utworzyć wystąpienie gracza z wymuszonym językiem zestawu. Domyślnym językiem jest angielski (pl).

> [!NOTE]
> Ta funkcja jest nadal przechodzi niektóre testy i jako takie podlega błędom.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Usługa Azure Media Player obsługuje obecnie następujące języki z odpowiednimi kodami języków:

| Język            | Code | Język                | Code   | Język                | Code         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| angielski {default}   | pl   | Chorwacki                | Kadry     | Rumuński                | Ro           |
| Arabski              | Ar   | węgierski               | Hu     | Słowacki                  | Sk           |
| Bułgarski           | Bg   | Indonezyjski              | id     | Słoweński                 | Sl           |
| Kataloński             | Ca   | Islandzki               | is     | Serbski - cyrylica      | sr-cyrl-cs   |
| Czeski               | Cs   | Włoski                 | it     | Serbski - Łaciński         | sr-latn-rs   |
| duński              | da   | japoński                | ja     | Rosyjski                 | ru           |
| niemiecki              | de   | Kazachski                  | Kk     | szwedzki                 | sv           |
| grecki               | El   | koreański                  | Ko     | Tajski                    | Th           |
| Hiszpański             | Tak   | Litewski              | lt     | Tagalog                 | Tl           |
| Estoński            | Et   | Łotewski                 | Lv     | Turecki                 | Tr           |
| Baskijski              | Ue   | Malezji               | Pani     | Ukraiński               | Uk           |
| Farsi               | Fa   | Norweski - BokmÃ¥l     | Nb     | Urdu                    | twój           |
| fiński             | fi   | Niderlandzki                   | nl     | Wietnamski              | Vi           |
| Francuski              | fr   | Norweski - Nynorsk     | nn     | Chiński - uproszczony    | zh-hans      |
| Galicyjski            | Gl   | Polski                  | Pl     | Chiński - tradycyjny   | zh-hant      |
| Hebrajski              | On   | Portugalski (Brazylia)     | pt-br  |                         |              |
| Hindi               | Hu   | Portugalski (Portugalia)   | pt-pt  |                         |              |


> [!NOTE]
> Jeśli nie chcesz, aby wystąpiła żadna lokalizacja, musisz wymusić język na angielski

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)