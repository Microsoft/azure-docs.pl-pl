---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: dfd2b32094aae06675ea8ee9157370f9d2833e91
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518232"
---
Rozpoznawanie formularzy działa na dokumentach wejściowych, które spełniają następujące wymagania:

* Format musi mieć format JPG, PNG, PDF (tekst lub zeskanowany) lub TIFF. Pliki PDF osadzone tekstem są najlepsze, ponieważ nie ma możliwości wystąpienia błędu podczas wyodrębniania i lokalizacji znaków.
* Rozmiar pliku musi być mniejszy niż 50 MB.
* Wymiary obrazu muszą mieć rozmiar od 50 x 50 pikseli do 10000 x 10000 pikseli.
* Wymiary pdf muszą mieć co najwyżej 17 x 17 cali, co odpowiada rozmiarom dokumentów prawnych lub A3 i mniejszym.
* W przypadku plików PDF i TIFF przetwarzane jest tylko 200 pierwszych stron (w przypadku subskrypcji w warstwie Bezpłatna przetwarzane są tylko dwie pierwsze strony).
* Łączny rozmiar zestawu danych treningowych musi mieć nie więcej niż 500 stron.
* Jeśli pliki PDF są zablokowane hasłem, przed przesłaniem należy usunąć blokadę.
* W przypadku skanowania z dokumentów papierowych formularze powinny być skanowaniami wysokiej jakości.
* W przypadku uczenia bez nadzoru (bez danych oznaczonych etykietami) dane muszą zawierać klucze i wartości.
* W przypadku uczenia bez nadzoru (bez danych oznaczonych etykietami) klucze muszą znajdować się powyżej lub z lewej strony wartości; Nie mogą być wyświetlane poniżej ani po prawej stronie.
