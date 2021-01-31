---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/27/2021
ms.author: rogarana
ms.openlocfilehash: 7da7c2fbb49a9dd936762b23f3c251d2142c52fd
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221771"
---
| Zasób | Standardowe udziały plików\* | Udziały plików w warstwie Premium |
|----------|---------------|------------------------------------------|
| Minimalny rozmiar udziału plików | Brak wartości minimalnej; płatność zgodnie z rzeczywistym użyciem | 100 GiB; aprowizowane |
| Maksymalny rozmiar udziału plików | 100 TiB \* \* , 5 TIB | 100 TiB |
| Maksymalny rozmiar pliku w udziale plików | 4 TiB | 4 TiB |
| Maksymalna liczba plików w udziale plików | Bez ograniczeń | Bez ograniczeń |
| Maksymalna liczba operacji we/wy na udział | 10 000 operacji we/wy \* \* , 1 000 iops lub 100 żądań w 100 MS | LICZBA OPERACJI WE/WY 100 000 |
| Maksymalna liczba przechowywanych zasad dostępu na udział plików | 5 | 5 |
| Docelowa przepływność dla pojedynczego udziału plików | do 300 MiB/s \* \* , do 60 MIB/s,  | Zobacz wartości dotyczące transferu plików w warstwie Premium i ruchu wychodzącego|
| Maksymalna liczba ruchu wychodzącego dla pojedynczego udziału plików | Zobacz standardową przepływność udziału plików | Do 6 204 MiB/s |
| Maksymalna liczba danych wejściowych dla pojedynczego udziału plików | Zobacz standardową przepływność udziału plików | Do 4 136 MiB/s |
| Maksymalna liczba otwartych dojść na plik lub katalog | 2 000 otwartych dojść | 2 000 otwartych dojść |
| Maksymalna liczba migawek udziałów | 200 migawek udziałów | 200 migawek udziałów |
| Maksymalna długość nazwy obiektu (katalogów i plików) | 2 048 znaków | 2 048 znaków |
| Maksymalna część ścieżki składnika (w ścieżce \A\B\C\D każda litera jest składnikiem) | 255 znaków | 255 znaków |
| Limit linków twardych (tylko system plików NFS) | Nie dotyczy | 178 |
| Maksymalna liczba kanałów wielokanałowe protokołu SMB | Nie dotyczy | 4 |

\* Limity dla standardowych udziałów plików mają zastosowanie do wszystkich trzech warstw dostępnych dla standardowych udziałów plików: transakcja zoptymalizowana, gorąca i chłodna.

\*\* Domyślnie w standardowych udziałach plików jest 5 TiB, zobacz temat [Włączanie i tworzenie dużych udziałów plików](../articles/storage/files/storage-files-how-to-create-large-file-share.md) , aby uzyskać szczegółowe informacje na temat zwiększania rozmiaru standardowych udziałów plików do 100 TIB.
