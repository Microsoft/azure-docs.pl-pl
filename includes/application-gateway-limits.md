---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: c98d88d66e89df3efe186212d8c004ec2a65158d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106450483"
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
| Liczba zestawów reguł ponownego zapisywania |400| |
| Liczba konfiguracji nagłówka lub adresu URL dla zestawu reguł ponownego zapisywania|40| |
| Liczba warunków według zestawu reguł ponownego zapisu|40| |
| Współbieżne połączenia protokołu WebSocket |Średnia Brama 20 000<sup>2</sup><br> Duże bramy 50 000<sup>2</sup>| |
| Maksymalna długość adresu URL|32 KB| |
| Maksymalny rozmiar nagłówka dla protokołu HTTP/2 |4 KB| |
| Maksymalny rozmiar przekazywania pliku, standardowa |2 GB | |
| Maksymalny rozmiar przekazywania pliku WAF |Bramy WAF o rozmiarze V1, 100 MB<br>Duże bramy WAF w wersji 1, 500 MB<br>V2 WAF, 750 MB| |
| Limit rozmiaru treści WAF, bez plików|128 KB||
| Maksymalna liczba reguł niestandardowych WAF|100||
| Maksymalna liczba wykluczeń WAF na Application Gateway|40||

<sup>1</sup> w przypadku jednostek SKU z obsługą WAF należy ograniczyć liczbę zasobów do 40.

<sup>2</sup> limit jest przypadany na wystąpienie Application Gateway, a nie na Application Gateway zasób.
