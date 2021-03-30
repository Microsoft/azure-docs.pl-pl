---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "66244613"
---
| Jednostka                                       | Konwencje   |
|----------------------------------------------|-----------------------------------------------------|
| Nazwy kontenerów dla blokowych obiektów blob i stronicowych obiektów BLOB | Musi być prawidłową nazwą DNS o długości od 3 do 63 znaków. <br>  Musi zaczynać się literą lub cyfrą. <br> Może zawierać tylko małe litery, cyfry i łącznik (-). <br> Bezpośrednio przed łącznikiem (-) i bezpośrednio po nim musi znajdować się cyfra lub litera. <br> Kolejne łączniki nie są dozwolone w nazwach. |
| Nazwy udziałów dla plików platformy Azure                  | Jak wyżej                                          |
| Nazwy katalogów i plików dla usługi Azure Files     |<li> Zachowywanie wielkości liter, bez uwzględniania wielkości liter i nie może przekraczać 255 znaków. </li><li> Nie może kończyć się ukośnikiem (/). </li><li>Jeśli jest podany, zostanie automatycznie usunięta. </li><li> Następujące znaki nie są dozwolone: <code>" \\ / : \| < > * ?</code></li><li> Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li> Niedozwolone znaki ścieżki URL są niedozwolone. Punkty kodu, takie jak \\ uE000, nie są prawidłowymi znakami Unicode. Niektóre znaki ASCII lub Unicode, takie jak znaki sterujące (0x00 do 0x1F, \\ u0081 itp.), również są niedozwolone. Aby uzyskać reguły dotyczące ciągów Unicode w protokole HTTP/1.1, zobacz RFC 2616, sekcja 2,2: Basic rules i RFC 3987. </li><li> Następujące nazwy plików są niedozwolone: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, znak kropki (.) i dwa znaki kropki (..).</li>|
| Nazwy blokowych i stronicowych obiektów blob      | </li><li>W nazwach obiektów blob jest rozróżniana wielkość liter. Mogą zawierać dowolną kombinację znaków. </li><li>Nazwa obiektu blob musi zawierać od 1 do 1024 znaków. </li><li>Zastrzeżone znaki adresów URL muszą być poprzedzone odpowiednim znakiem ucieczki. </li><li>Liczba segmentów ścieżki w nazwie obiektu blob nie może przekraczać 254. Segment ścieżki to ciąg znajdujący się pomiędzy następującymi po sobie znakami ogranicznika (na przykład ukośnikami „/”), co odpowiada nazwie katalogu wirtualnego.</li> |
