---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 02/19/2021
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: ebaca1f39b16e4a06b5dcaa4e5f1de07122c6c89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622282"
---
| Zasób | Limit |
| ---------------------------------------------------------------------- | -------------------------- |
| Maksymalna liczba jednostek skalowania | 10 na region<sup>1</sup> |
| Rozmiar pamięci podręcznej | 5 GiB na jednostkę<sup>2</sup> |
| Współbieżne połączenia zaplecza<sup>3</sup> na urząd http | 2 048 na jednostkę<sup>4</sup> |
| Maksymalny rozmiar buforowanej odpowiedzi | 2 MiB |
| Maksymalny rozmiar dokumentu zasad | 256 KiB<sup>5</sup> |
| Maksymalna liczba niestandardowych domen bramy na wystąpienie usługi<sup>6</sup> | 20 |
| Maksymalna liczba certyfikatów urzędu certyfikacji na wystąpienie usługi<sup>7</sup> | 10 |
| Maksymalna liczba wystąpień usługi na subskrypcję<sup>8</sup> | 20 |
| Maksymalna liczba subskrypcji na wystąpienie usługi<sup>8</sup> | 500 |
| Maksymalna liczba certyfikatów klienta na wystąpienie usługi<sup>8</sup> | 50 |
| Maksymalna liczba interfejsów API na wystąpienie usługi<sup>8</sup> | 50 |
| Maksymalna liczba operacji interfejsu API na wystąpienie usługi<sup>8</sup> | 1000 |
| Maksymalny łączny czas trwania żądania<sup>8</sup> | 30 sekund |
| Maksymalny rozmiar zbuforowanego ładunku<sup>8</sup> | 2 MiB |
| Maksymalny rozmiar adresu URL żądania<sup>9</sup> | 4096 bajtów |
| Maksymalna długość segmentu ścieżki adresu URL<sup>10</sup> | 260 znaków |
| Maksymalny rozmiar schematu interfejsu API używany przez [zasady sprawdzania poprawności](../articles/api-management/validation-policies.md)<sup>10</sup> | 4 MB |
| Maksymalny rozmiar żądania lub treści odpowiedzi w ramach [walidacji — zasady zawartości](../articles/api-management/validation-policies.md#validate-content) | 100 KB |
| Maksymalna liczba bram hostowanych przez siebie<sup>11</sup> | 25 |

<sup>1</sup> Limity skalowania zależą od warstwy cenowej. Aby uzyskać szczegółowe informacje dotyczące warstw cenowych i ich limitów skalowania, zobacz [Cennik usługi API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Rozmiar pamięci podręcznej na jednostkę zależy od warstwy cenowej. Aby sprawdzić warstwy cenowe i ich limity skalowania, zobacz [cennik API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> Połączenia są w puli i ponownie używane, chyba że zostały jawnie zamknięte przez zaplecze.<br/>
<sup>4</sup> Ten limit jest na jednostkę warstw Podstawowa, standardowa i Premium. Warstwa dewelopera jest ograniczona do 1 024. Ten limit nie dotyczy warstwy zużycia.<br/>
<sup>5</sup> Ten limit dotyczy warstw Podstawowa, standardowa i Premium. W warstwie zużycia rozmiar dokumentu zasad jest ograniczony do 16 KiB.<br/>
<sup>6</sup> Wiele domen niestandardowych jest obsługiwanych tylko w warstwach deweloper i Premium.<br/>
<sup>7</sup> Certyfikaty urzędu certyfikacji nie są obsługiwane w warstwie zużycia.<br/>
<sup>8</sup> Ten limit dotyczy tylko warstwy zużycia. W tych kategoriach nie ma ograniczeń dotyczących innych warstw.<br/>
<sup>9</sup> Dotyczy tylko warstwy zużycia. Zawiera ciąg zapytania o długości do 2048 bajtów.<br/>
<sup>10</sup> aby zwiększyć ten limit, skontaktuj się z [pomocą techniczną](https://azure.microsoft.com/support/options/).<br/>
<sup>11</sup> Bramy samoobsługowe są obsługiwane tylko w warstwach deweloper i Premium. Limit ma zastosowanie do liczby [zasobów bramy samoobsługowej](/rest/api/apimanagement/2019-12-01/gateway). Aby zgłosić ten limit, skontaktuj się z [pomocą techniczną](https://azure.microsoft.com/support/options/). Należy zauważyć, że liczba węzłów (lub replik) skojarzonych z nieobsługiwanym zasobem bramy jest nieograniczona w warstwie Premium i ograniczona do jednego węzła w warstwie dewelopera.