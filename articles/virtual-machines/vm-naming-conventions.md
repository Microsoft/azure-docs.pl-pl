---
title: Konwencje nazewnictwa maszyn wirtualnych platformy Azure
description: Wyjaśnia konwencje nazewnictwa używane dla rozmiarów maszyn wirtualnych platformy Azure
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131722"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Konwencje nazewnictwa maszyn wirtualnych platformy Azure

Ta strona zawiera opis konwencji nazewnictwa używanych dla maszyn wirtualnych platformy Azure. Maszyny wirtualne korzystają z tych konwencji nazewnictwa, aby zauważyć różne funkcje i specyfikacje.

## <a name="naming-convention-explanation"></a>Wyjaśnienie konwencji nazewnictwa

**[Rodzina]**  +  **[Podrodzina *]**  +  **[# z procesorów wirtualnych vCPU]**  +  **[Funkcje dodatków]**  +  **[Typ akceleratora *]**  +  **[Wersja]**

|Wartość | Wyjaśnienie|
|---|---|
| Standard, Basic lub eksperymentalny | "Standardowa" jest wartością domyślną przypisaną dla wszystkich rozmiarów maszyn wirtualnych | 
| Family | Wskazuje serię rodzin maszyn wirtualnych| 
| * Podrodzina | Używany tylko do celów specjalnych odróżniania maszyn wirtualnych|
| Liczba procesorów wirtualnych vCPU| Wskazuje liczbę procesorów wirtualnych vCPU maszyny wirtualnej |
| Funkcje dodatków | Co najmniej jedna małe litery oznacza funkcje dodatków, takie jak: <br> a = procesor oparty na procesorze AMD <br> d = dysk (lokalny dysk tymczasowy jest obecny); dotyczy to nowszych maszyn wirtualnych platformy Azure, zobacz [Ddv4 i Ddsv4 — seria](./ddv4-ddsv4-series.md) <br> h = możliwość hibernacji <br> i = izolowany <br> l = mało pamięci <br> m = intensywność pamięci <br> t = mała ilość pamięci <br> r = RDMA <br> s = Premium Storage możliwości, w tym możliwe użycie [SSD w warstwie Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) (Uwaga: Niektóre nowsze rozmiary bez atrybutu s nadal mogą obsługiwać Premium Storage np. M128, M64 itd.)<br> |
| * — Typ akceleratora | Wskazuje typ akceleratora sprzętowego w jednostkach JSZ wyspecjalizowanych/GPU. Tylko nowe jednostki SKU wyspecjalizowanych/GPU uruchomione z kwartału Q3 2020 będą miały akcelerator sprzętu w nazwie. |
| Wersja | Oznacza wersję serii rodziny maszyn wirtualnych |

## <a name="example-breakdown"></a>Przykład podziału

**[Rodzina]**  +  **[Podrodzina *]**  +  **[# z procesorów wirtualnych vCPU]**  +  **[Funkcje dodatków]**  +  **[Typ akceleratora *]**  +  **[Wersja]**

### <a name="example-1-m416ms_v2"></a>Przykład 1: M416ms_v2

|Wartość | Wyjaśnienie|
|---|---|
| Family | M | 
| Liczba procesorów wirtualnych vCPU | 416 |
| Funkcje dodatków | m = intensywność pamięci <br> s = Premium Storage może |
| Wersja | v2 |

### <a name="example-2-nv16as_v4"></a>Przykład 2: NV16as_v4

|Wartość | Wyjaśnienie|
|---|---|
| Family | N | 
| Podrodzina | V |
| Liczba procesorów wirtualnych vCPU | 16 |
| Funkcje dodatków | a = procesor oparty na procesorze AMD <br> s = Premium Storage może |
| Wersja | 4 |

### <a name="example-3-nc4as_t4_v3"></a>Przykład 3: NC4as_T4_v3

|Wartość | Wyjaśnienie|
|---|---|
| Family | N | 
| Podrodzina | C |
| Liczba procesorów wirtualnych vCPU | 4 |
| Funkcje dodatków | a = procesor oparty na procesorze AMD <br> s = Premium Storage może |
| Typ akceleratora | T4 |
| Wersja | v3 |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat dostępnych [rozmiarów maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) na platformie Azure. 
