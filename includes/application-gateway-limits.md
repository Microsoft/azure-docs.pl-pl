---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 1166585c7291c4fe0d78cbc9540e3f08f985db6c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107589954"
---
| Zasób | Limit | Uwaga |
| --- | --- | --- |
| Azure Application Gateway |1000 na subskrypcję | |
| Konfiguracje adresów IP frontony |2 |1 publiczna i 1 prywatna |
| Porty frontony |100<sup>1</sup> | |
| Pule adresów back-end |100<sup>1</sup> | |
| Serwery back-end na pulę |1200 | |
| Odbiorniki HTTP |200<sup>1</sup> |Ograniczona do 100 aktywnych odbiorników, które rozsyłają ruch. Odbiorniki aktywne = łączna liczba odbiorników — odbiorniki nie są aktywne.<br>Jeśli konfiguracja domyślna wewnątrz reguły rozsyłania jest ustawiona na kierowanie ruchu (na przykład ma odbiornik, pulę zaplecza i ustawienia protokołu HTTP), będzie ona również liczyła się jako odbiornik.|
| Reguły równoważenia obciążenia HTTP |100<sup>1</sup> | |
| Ustawienia protokołu HTTP w zadomowiu |100<sup>1</sup> | |
| Wystąpienia na bramę |Wersja 1 SKU — 32<br>V2 SKU — 125 | |
| Certyfikaty SSL |100<sup>1</sup> |1 na odbiornik HTTP |
| Maksymalny rozmiar certyfikatu SSL |V1 SKU — 10 KB<br>V2 SKU — 16 KB| |
| Certyfikaty uwierzytelniania |100 | |
| Zaufane certyfikaty główne |100 | |
| Minimalny limit czasu żądania |1 sekunda | |
| Maksymalny limit czasu żądania |24 godziny | |
| Liczba witryn |100<sup>1</sup> |1 na odbiornik HTTP |
| Mapy adresów URL na odbiornik |1 | |
| Maksymalna liczba reguł opartych na ścieżkach na mapę adresów URL|100||
| Konfiguracje przekierowania |100<sup>1</sup>| |
| Liczba zestawów reguł ponownego pisali |400| |
| Liczba konfiguracji nagłówka lub adresu URL na zestaw reguł ponownego pisali|40| |
| Liczba warunków na zestaw reguł ponownego przepisywania|40| |
| Współbieżne połączenia websocket |Średnie bramy 20 000<sup>2</sup><br> Duże bramy 50 000<sup>2</sup>| |
| Maksymalna długość adresu URL|32 KB| |
| Maksymalny rozmiar nagłówka dla protokołu HTTP/2 |16 KB| |
| Maksymalny rozmiar przekazywania plików, Standardowa |2 GB | |
| Maksymalny rozmiar przekazywania plików WAF |Średnia brama WAF w wersji 1, 100 MB<br>Duże bramy WAF w wersji 1, 500 MB<br>V2 WAF, 750 MB| |
| Limit rozmiaru treści aplikacji sieci Windows bez plików|128 KB||
| Maksymalna liczba reguł niestandardowych WAF|100||
| Maksymalna liczba wykluczeń WAF na Application Gateway|40||

<sup>1</sup> W przypadku jednostki SKU z włączoną WAF należy ograniczyć liczbę zasobów do 40.

<sup>2</sup> Limit nie jest Application Gateway na Application Gateway zasobów.
