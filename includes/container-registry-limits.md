---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79117142"
---
| Zasób | Podstawowy | Standardowa | Premium |
|---|---|---|---|
| Magazyn<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| Maksymalny rozmiar warstwy obrazu | 200 GiB | 200 GiB | 200 GiB |
| ReadOps na minutę<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| WriteOps na minutę<sup>2, 4</sup> | 100 | 500 | 2000 |
| Pobierz przepustowość<sup>2</sup> MB/s | 30 | 60 | 100 |
| Przepustowość przekazywania<sup>2</sup> MB/s | 10 | 20 | 50 |
| Elementy webhook | 2 | 10 | 500 |
| Replikacja geograficzna | Brak | Brak | [Obsługiwane][geo-replication] |
| Zaufanie do zawartości | Brak | Brak | [Obsługiwane][content-trust] |
| Dostęp do sieci wirtualnej | Brak | Brak | [Wersja zapoznawcza][vnet] |
| Integracja z linkiem prywatnym | Brak | Brak | [Wersja zapoznawcza][plink] |
| Klucze zarządzane przez klienta | Brak | Brak | [Wersja zapoznawcza][cmk] |
| Uprawnienia w zakresie repozytorium | Brak | Brak | [Wersja zapoznawcza][token]|
| &bull;Żeton | Brak | Brak | 20 000 |
| &bull;Mapy zakresu | Brak | Brak | 20 000 |
| &bull;Repozytoria na mapę zakresu | Brak | Brak | 500 |


<sup>1</sup> Określone limity magazynu to ilość *dołączonego* magazynu dla każdej warstwy. Opłata jest naliczana za dodatkową dzienną stawkę za GiB dla magazynu obrazów powyżej tych limitów. Aby uzyskać informacje o stawkach, zobacz [Cennik usługi Azure Container Registry][pricing].

<sup>2</sup>*ReadOps*, *WriteOps*i *przepustowość* są minimalnymi oszacowaniami. Azure Container Registry dąży do poprawy wydajności, ponieważ wymaga użycia.

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