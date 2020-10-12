---
title: plik dołączany
description: plik dołączany
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 361fb18574e90fc46e45bff8914c51ee1afa2f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89411737"
---
| Zasób | Limit |
| --- | --- |
| Zasoby platformy Azure z przodu na subskrypcję | 100 |
| Hosty frontonu, w tym domeny niestandardowe na zasób | 500 |
| Reguły routingu na zasób | 500 |
| Pule zaplecza na zasób | 50 |
| Zaplecza na pulę zaplecza | 100 |
| Wzorce ścieżek zgodne z regułą routingu | 25 |
| Adresy URL w ramach pojedynczego wywołania przeczyszczania pamięci podręcznej | 100 |
| Niestandardowe reguły zapory aplikacji sieci Web według zasad | 100 |
| Zasady zapory aplikacji sieci Web na subskrypcję | 100 |
| Warunki dopasowania zapory aplikacji sieci Web na regułę niestandardową | 10 |
| Zakresy adresów IP zapory aplikacji sieci Web na warunek dopasowania | 600 |
| Wartości parametrów zapory aplikacji sieci Web na warunek dopasowania | 10 |
| Długość wartości ciągu zapory aplikacji sieci Web | 256 |
| Długość nazwy parametru treści wpisu zapory aplikacji sieci Web | 256 |
| Długość nazwy nagłówka HTTP zapory aplikacji sieci Web | 256 |
| Długość nazwy pliku cookie zapory aplikacji sieci Web | 256 |
| Inspekcja rozmiaru treści żądania HTTP zapory aplikacji sieci Web | 128 KB |
| Długość treści niestandardowej odpowiedzi zapory aplikacji sieci Web | 2 KB |

### <a name="timeout-values"></a>Wartości limitu czasu
#### <a name="client-to-front-door"></a>Klient do przednich drzwi
* Drzwi z przodu mają limit czasu bezczynności połączenia TCP wynoszący 61 sekund.

#### <a name="front-door-to-application-back-end"></a>Tylne drzwi do zaplecza aplikacji
* Jeśli odpowiedź jest rozsegmentową odpowiedzią, zwracany jest 200, jeśli lub kiedy zostanie odebrany pierwszy fragment.
* Gdy żądanie HTTP zostanie przekazane do zaplecza, przód czeka przez 30 sekund na pierwszy pakiet od zaplecza. Następnie zwraca błąd 503 do klienta. Tę wartość można skonfigurować za pomocą pola sendRecvTimeoutSeconds w interfejsie API.
    * W przypadku scenariuszy pamięci podręcznej ten limit czasu nie jest konfigurowany i dlatego, jeśli żądanie jest buforowane i trwa dłużej niż 30 sekund dla pierwszego pakietu z przodu lub z zaplecza, do klienta jest zwracany błąd 504. 
* Po odebraniu pierwszego pakietu od zaplecza drzwi z przodu czekają na 30 sekund w przekroczeniu limitu czasu bezczynności. Następnie zwraca błąd 503 do klienta. Nie można skonfigurować tej wartości limitu czasu.
* Od przodu do limitu czasu sesji TCP zaplecza jest 90 sekund.

### <a name="upload-and-download-data-limit"></a>Limit przekazywania i pobierania danych

|  | Z kodowaniem transferu fragmentarycznego (CTE) | Bez fragmentacji HTTP |
| ---- | ------- | ------- |
| **Pobieranie** | Nie ma żadnego limitu rozmiaru pobierania. | Nie ma żadnego limitu rozmiaru pobierania. |
| **Przekaż** |    Nie ma żadnego limitu, o ile każde przesłanie CTE jest mniejsze niż 2 GB. | Rozmiar nie może być większy niż 2 GB. |

### <a name="other-limits"></a>Inne limity
* Maksymalny rozmiar adresu URL — 8 192 bajtów — określa maksymalną długość nieprzetworzonego adresu URL (schemat + nazwa hosta + port + ścieżka + ciąg zapytania w adresie URL)
* Maksymalny rozmiar ciągu zapytania — 4 096 bajtów — określa maksymalną długość ciągu zapytania w bajtach.
* Maksymalny rozmiar nagłówka odpowiedzi HTTP z adresu URL sondy kondycji — 4 096 bajtów — określono maksymalną długość wszystkich nagłówków odpowiedzi sond kondycji. 
