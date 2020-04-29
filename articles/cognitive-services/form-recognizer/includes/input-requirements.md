---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75379444"
---
Aparat rozpoznawania formularzy działa na dokumentach wejściowych, które spełniają następujące wymagania:

* Wymagany format to JPG, PNG, PDF (tekst lub zeskanowany) lub TIFF. Pliki PDF z tekstem osadzonym są najlepszym rozwiązaniem, ponieważ nie ma możliwości błędu podczas wyodrębniania znaków i lokalizacji.
* Jeśli pliki PDF są zablokowane hasłem, należy usunąć blokadę przed przesłaniem.
* Dokumenty PDF i TIFF muszą mieć 200 stron lub mniej, a łączny rozmiar zestawu danych szkoleniowych musi mieć 500 stron lub mniej.
* W przypadku obrazów wymiary muszą zawierać się w przedziale od 600 x 100 pikseli i 4200 x 4200 pikseli.
* W przypadku skanowania z dokumentów papierowych formularze powinny być skanów o wysokiej jakości.
* Tekst musi używać alfabetu łacińskiego (znaki angielskie).
* W przypadku nienadzorowanej uczenia (bez etykiet danych) dane muszą zawierać klucze i wartości.
* W przypadku nienadzorowanej uczenia (bez etykiet danych) klucze muszą znajdować się powyżej lub z lewej strony wartości; nie mogą występować poniżej ani w prawo.

Aparat rozpoznawania formularzy obecnie nie obsługuje następujących typów danych wejściowych:

* Tabele złożone (tabele zagnieżdżone, scalone nagłówki lub komórki itd.).
* Pola wyboru lub przyciski radiowe.
