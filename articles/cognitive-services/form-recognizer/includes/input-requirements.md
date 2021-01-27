---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 7cf3d86eeea9d1b0f5fcbb757d3597e21cbcc369
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901109"
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
