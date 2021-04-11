---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b4701260a7d8da030f9f3019060aaa83e7a3a483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803416"
---
| Zasób | Podstawowa | Standardowa (Standard) | Premium |
|---|---|---|---|
| Uwzględniony magazyn<sup>1</sup> (GIB) | 10 | 100 | 500 |
| Limit magazynowania (TiB) | 20| 20 | 20 |
| Maksymalny rozmiar warstwy obrazu (GiB) | 200 | 200 | 200 |
| ReadOps na minutę<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| WriteOps na minutę<sup>2, 4</sup> | 100 | 500 | 2000 |
| Pobierz przepustowość<sup>2</sup> (MB/s) | 30 | 60 | 100 |
| Przepustowość przekazywania <sup>2</sup> (MB/s) | 10 | 20 | 50 |
| Elementy webhook | 2 | 10 | 500 |
| Replikacja geograficzna | NIE DOTYCZY | NIE DOTYCZY | [Obsługiwane][geo-replication] |
| Strefy dostępności | NIE DOTYCZY | NIE DOTYCZY | [Wersja zapoznawcza][zones] |
| Zaufanie do zawartości | NIE DOTYCZY | NIE DOTYCZY | [Obsługiwane][content-trust] |
| Prywatny link z prywatnymi punktami końcowymi | NIE DOTYCZY | NIE DOTYCZY | [Obsługiwane][plink] |
| &bull; Prywatne punkty końcowe | NIE DOTYCZY | NIE DOTYCZY | 10 |
| Reguły sieci publicznych adresów IP | NIE DOTYCZY | NIE DOTYCZY | 100 |
| Dostęp do sieci wirtualnej punktu końcowego usługi | NIE DOTYCZY | NIE DOTYCZY | [Wersja zapoznawcza][vnet] |
| Klucze zarządzane przez klienta | NIE DOTYCZY | NIE DOTYCZY | [Obsługiwane][cmk] |
| Uprawnienia w zakresie repozytorium | NIE DOTYCZY | NIE DOTYCZY | [Wersja zapoznawcza][token]|
| &bull; Żeton | NIE DOTYCZY | NIE DOTYCZY | 20 000 |
| &bull; Mapy zakresu | NIE DOTYCZY | NIE DOTYCZY | 20 000 |
| &bull; Repozytoria na mapę zakresu | NIE DOTYCZY | NIE DOTYCZY | 500 |


<sup>1</sup> magazyn uwzględniony w dziennej stawce dla każdej warstwy. Można użyć dodatkowego magazynu, do limitu magazynu rejestru, przy dodatkowej dziennej stawce za GiB. Aby uzyskać informacje o stawkach, zobacz [Cennik usługi Azure Container Registry][pricing]. Jeśli potrzebujesz magazynu poza limitem magazynu rejestru, skontaktuj się z pomocą techniczną platformy Azure.

<sup>2</sup>*ReadOps*, *WriteOps* i *przepustowość* są minimalnymi oszacowaniami. Azure Container Registry dąży do poprawy wydajności, ponieważ wymaga użycia.

<sup>3</sup> [Docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) Wykonuje translację na wiele operacji odczytu na podstawie liczby warstw w obrazie oraz pobierania manifestu.

<sup>4</sup> [Wypychanie Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) jest tłumaczone na wiele operacji zapisu na podstawie liczby warstw, które muszą zostać wypchnięte. A `docker push` obejmuje *ReadOps* do pobrania manifestu dla istniejącego obrazu.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md
