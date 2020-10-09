---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 92ec7c0a1469c9f02855cd6191faa8514e54c8f0
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828966"
---
| Zasób | Limit | Uwaga |
| --- | --- | --- |
| Azure Application Gateway |1 000 na subskrypcję | |
| Konfiguracje adresów IP frontonu |2 |1 publiczna i 1 prywatna |
| Porty frontonu |100<sup>1</sup> | |
| Pule adresów zaplecza |100<sup>1</sup> | |
| Serwery zaplecza na pulę |1200 | |
| Odbiorniki HTTP |200<sup>1</sup> |Ograniczone do 100 aktywnych odbiorników, które są ruchem routingu. Aktywne odbiorniki = całkowita liczba odbiorników nieaktywnych.<br>Jeśli domyślna konfiguracja wewnątrz reguły routingu jest ustawiona na kierowanie ruchu (na przykład ma odbiornik, pulę zaplecza i ustawienia HTTP), które również liczą się jako odbiornik.|
| Reguły równoważenia obciążenia HTTP |100<sup>1</sup> | |
| Ustawienia protokołu HTTP zaplecza |100<sup>1</sup> | |
| Wystąpienia na bramę |JEDNOSTKA SKU V1 — 32<br>JEDNOSTKA SKU V2 — 125 | |
| Certyfikaty SSL |100<sup>1</sup> |1 na odbiornik HTTP |
| Maksymalny rozmiar certyfikatu SSL |JEDNOSTKA SKU V1 — 10 KB<br>WERSJA 2 SKU — 16 KB| |
| Certyfikaty uwierzytelniania |100 | |
| Zaufane certyfikaty główne |100 | |
| Minimalny limit czasu żądania |1 sekunda | |
| Maksymalny limit czasu żądania |24 godziny | |
| Liczba witryn |100<sup>1</sup> |1 na odbiornik HTTP |
| Mapowania adresów URL na odbiornik |1 | |
| Maksymalna liczba reguł opartych na ścieżce na mapę URL|100||
| Konfiguracja przekierowania |100<sup>1</sup>| |
| Współbieżne połączenia protokołu WebSocket |Średnie bramy 20 000<br> Duże bramy 50 000| |
| Maksymalna długość adresu URL|32 KB| |
| Maksymalny rozmiar nagłówka dla protokołu HTTP/2 |4 KB| |
| Maksymalny rozmiar przekazywania pliku, standardowa |2 GB | |
| Maksymalny rozmiar przekazywania pliku WAF |Bramy WAF o rozmiarze V1, 100 MB<br>Duże bramy WAF w wersji 1, 500 MB<br>V2 WAF, 750 MB| |
| Limit rozmiaru treści WAF, bez plików|128 KB||
| Maksymalna liczba reguł niestandardowych WAF|100||
| Maksymalne wykluczenia WAF|100||

<sup>1</sup> w przypadku jednostek SKU z obsługą WAF należy ograniczyć liczbę zasobów do 40.
