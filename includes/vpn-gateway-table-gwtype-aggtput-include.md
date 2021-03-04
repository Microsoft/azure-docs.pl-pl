---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010827"
---
|**<br>Generowanie bramy sieci VPN <br>** |**SKU**   | **Połączenia typu lokacja-lokacja/Połączenia między sieciami wirtualnymi<br>Tunele** | **Połączenia typu punkt-lokacja<br> Połączenia SSTP** | **P2S <br> połączenia IKEv2/OpenVPN** | **Test porównawczy<br>agregowanej przepływności** | **BGP** | **Strefa nadmiarowa** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Podstawowe**   | Maksymalnie z 10    | Maksymalnie z 128  | Nieobsługiwane  | 100 Mb/s  | Nieobsługiwane| Nie |
|**Generation1**|**VpnGw1**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane | Nie |
|**Generation1**|**VpnGw2**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane | Nie |
|**Generation1**|**VpnGw3**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane | Nie |
|**Generation1**|**VpnGw1AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane | Tak |
|**Generation1**|**VpnGw2AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane | Tak |
|**Generation1**|**VpnGw3AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane | Tak |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1,25 Gb/s | Obsługiwane | Nie |
|**Generation2**|**VpnGw3**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 2,5 Gb/s  | Obsługiwane | Nie |
|**Generation2**|**VpnGw4**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 5000      | 5 Gb/s    | Obsługiwane | Nie |
|**Generation2**|**VpnGw5**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 10 000      | 10 Gb/s   | Obsługiwane | Nie |
|**Generation2**|**VpnGw2AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1,25 Gb/s | Obsługiwane | Tak |
|**Generation2**|**VpnGw3AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 2,5 Gb/s  | Obsługiwane | Tak |
|**Generation2**|**VpnGw4AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 5000      | 5 Gb/s    | Obsługiwane | Tak |
|**Generation2**|**VpnGw5AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 10 000      | 10 Gb/s   | Obsługiwane | Tak |

(*) Jeśli potrzebujesz więcej niż 30 tuneli sieci VPN S2S, skorzystaj z usługi [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Zmiany rozmiarów jednostek SKU VpnGw są dozwolone w ramach tej samej generacji, z wyjątkiem zmiany rozmiarów podstawowej jednostki SKU. Podstawowa jednostka SKU jest starszą wersją SKU i ma ograniczenia dotyczące funkcji. Aby przejść z warstwy Podstawowa do innej jednostki SKU VpnGw, należy usunąć podstawową jednostkę SKU sieci VPN i utworzyć nową bramę z kombinacją żądanej generacji i rozmiaru jednostki SKU.

* Te limity połączeń są niezależne. Przykładowo dla jednostki SKU VpnGw1 można mieć 128 połączeń SSTP, a oprócz tego 250 połączeń IKEv2.

* Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informacje na temat umowy SLA (Service Level Agreement) można znaleźć na stronie [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Na pojedynczym tunelu można osiągnąć maksymalnie 1 GB/s przepustowości. Wzorzec zagregowanej przepływności w powyższej tabeli opiera się na pomiarach wielu tuneli zagregowanych za pośrednictwem jednej bramy. Test porównawczy agregowanej przepływności dla bramy sieci VPN bazuje na sumie wartości dla połączeń typu lokacja-lokacja i punkt-lokacja. **Posiadanie dużej liczby połączeń typu punkt-lokacja może negatywnie wpływać na połączenie typu lokacja-lokacja z powodu ograniczeń przepustowości.** Ze względu na warunki ruchu internetowego i zachowania aplikacji test porównawczy agregowanej przepływności nie pokazuje przepływności gwarantowanej.

Aby pomóc naszym klientom zrozumieć względną wydajność jednostek SKU przy użyciu różnych algorytmów, do mierzenia wydajności używamy publicznie dostępnych narzędzi iPerf i CTSTraffic. W poniższej tabeli przedstawiono wyniki testów wydajności dla generacji 1, VpnGw jednostek SKU. Jak widać, najlepszą wydajność uzyskuje się w przypadku użycia algorytmu GCMAES256 w przypadku szyfrowania i integralności protokołu IPsec. Średnia wydajność podczas korzystania z AES256 na potrzeby szyfrowania IPsec i SHA256 w celu zapewnienia integralności. W przypadku użycia DES3 na potrzeby szyfrowania IPsec i SHA256 w celu zapewnienia integralności mamy najniższą wydajność.

|**Generowanie**|**SKU**   | **Używane algorytmy <br>** | **Zaobserwowana przepływność <br>** | **Poobserwowane pakiety na sekundę <br>** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 MB/s   | 58 000<br>50 000<br>50 000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 MB/s | 90 000<br>80 000<br>55 000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 MB/s<br>120 MB/s | 105 000<br>90 000<br>60 000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 MB/s   | 58 000<br>50 000<br>50 000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 MB/s | 90 000<br>80 000<br>55 000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 MB/s<br>120 MB/s | 105 000<br>90 000<br>60 000|
