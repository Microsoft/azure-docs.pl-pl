---
title: Jednostki SKU BareMetal dla obciążeń Oracle
description: Dowiedz się więcej na temat jednostki SKU dla obciążeń infrastruktury Oracle BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 2482f8ed682a982ee3c8c4907e21b4e6c6ebbb4f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559091"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>Jednostki SKU BareMetal dla obciążeń Oracle

W tym artykule przyjrzymy się wyspecjalizowanym jednostkom SKU infrastruktury BareMetal dla obciążeń Oracle.

Infrastruktura BareMetal dla jednostki SKU Oracle obejmuje zakres od dwóch gniazd do czterech gniazd. Możesz również wybrać spośród różnych rozmiarów pamięci i rdzeni procesora CPU, aby spełnić wymagania obciążenia. Oto tabela podsumowująca funkcje dostępnych jednostki SKU.
 
| **Certyfikowany sprzęt firmy Oracle**   | **Model** | **Łączna ilość pamięci** | **Storage** | **Dostępność** |
| --- | --- | --- | --- | --- |
| TAK | SAP HANA na platformie Azure S32m— 2 rdzenie procesora Intel® Xeon® cpu I623416 i 32 wątki procesora CPU | 1,5 TB | --- | Dostępne |
| TAK | SAP HANA na platformie Azure S64m — 4 rdzenie procesora Intel® Xeon® cpu I623432 i 64 wątki procesora CPU | 3,0 TB | --- | Dostępne |
| TAK | SAP HANA na platformie Azure S96 — 2 rdzenie procesora Intel® Xeon® E7-8890 v448 i 96 wątków procesora CPU | 768 GB | 3,0 TB | Dostępne |
| TAK | SAP HANA na platformie Azure S224 — 4 procesory Intel® Xeon® i 112 rdzeni procesora CPU oraz 224 wątki procesora CPU | 3,0 TB | 6,3 TB | Dostępne |
| TAK | SAP HANA usłudze Azure S224m — 4 procesory Intel® Xeon® Cpu Cpu 8276 z 112 rdzeniami i 224 wątkami procesora CPU | 6,0 TB | 10,5 TB | Dostępne |

- Rdzenie procesora CPU = suma niewątkowych rdzeni procesora CPU (łączna liczba procesorów fizycznych) jednostki serwera. 
- Wątki procesora CPU = suma wątków obliczeniowych dostarczanych przez rdzenie procesora CPU z hiperwątkami (łączna liczba procesorów logicznych) jednostki serwera. Większość jednostek jest konfigurowana domyślnie do używania Hyper-Threading Technologii.
- Serwery są przeznaczone dla klientów.
- Klient ma dostęp główny (brak funkcji hypervisor).
- Serwery nie są bezpośrednio na sieciach wirtualnych platformy Azure.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o magazynie oferowanym przez usługę BareMetal Infrastructure for Oracle.

> [!div class="nextstepaction"]
> [Magazyn na platformie BareMetal dla obciążeń Oracle](oracle-baremetal-storage.md)
