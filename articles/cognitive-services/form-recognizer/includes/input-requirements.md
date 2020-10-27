---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: b3d24ce4dd42ea8122610943379160ca93406c21
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548060"
---
Aparat rozpoznawania formularzy działa na dokumentach wejściowych, które spełniają następujące wymagania:

* Wymagany format to JPG, PNG, PDF (tekst lub zeskanowany) lub TIFF. Pliki PDF z tekstem osadzonym są najlepszym rozwiązaniem, ponieważ nie ma możliwości błędu podczas wyodrębniania znaków i lokalizacji.
* Rozmiar pliku musi być mniejszy niż 50 MB.
* Wymiary obrazu muszą zawierać się w przedziale od 50 x 50 pikseli i 10000 x 10000 pikseli.
* Wymiary PDF muszą mieć co najwyżej 17 x 17 cali, odpowiadające rozmiarowi papieru legalnego lub A3 i mniejszemu.
* W przypadku plików PDF i TIFF tylko pierwsze 200 stron jest przetwarzanych (z subskrypcją warstwy Bezpłatna, są przetwarzane tylko dwie pierwsze strony).
* Łączny rozmiar zestawu danych szkoleniowych musi być 500 stron lub mniej.
* Jeśli pliki PDF są zablokowane hasłem, należy usunąć blokadę przed przesłaniem.
* W przypadku skanowania z dokumentów papierowych formularze powinny być skanów o wysokiej jakości.
* Tekst musi używać alfabetu łacińskiego (znaki angielskie).
* W przypadku nienadzorowanej uczenia (bez etykiet danych) dane muszą zawierać klucze i wartości.
* W przypadku nienadzorowanej uczenia (bez etykiet danych) klucze muszą znajdować się powyżej lub z lewej strony wartości; nie mogą występować poniżej ani w prawo.

Aparat rozpoznawania formularzy obecnie nie obsługuje następujących typów danych wejściowych:

* Tabele złożone (tabele zagnieżdżone, scalone nagłówki lub komórki itd.).
* Pola wyboru lub przyciski radiowe.
