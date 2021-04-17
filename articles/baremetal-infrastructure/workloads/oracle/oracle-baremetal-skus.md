---
title: Jednostki SKU BareMetal dla obciążeń Oracle
description: Dowiedz się więcej na temat jednostki SKU dla obciążeń infrastruktury Oracle BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 42ff26b9ea9bcc022f1ddbf3dddcb041b2cea3a2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588857"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>Jednostki SKU BareMetal dla obciążeń Oracle

W tym artykule przyjrzymy się wyspecjalizowanym jednostkom SKU infrastruktury BareMetal dla obciążeń Oracle.

Infrastruktura BareMetal dla jednostki SKU Oracle obejmuje zakres od dwóch gniazd do czterech gniazd. Możesz również wybrać spośród różnych rozmiarów pamięci i rdzeni procesora CPU, aby spełnić wymagania obciążenia. Oto tabela podsumowująca funkcje dostępnych jednostki SKU.
 
| **Certyfikowany sprzęt firmy Oracle**   | **Model** | **Łączna ilość pamięci** | **Storage** | **Dostępność** |
| --- | --- | --- | --- | --- |
| TAK | SAP HANA na platformie Azure S32m— 2 wątki procesora Intel® Xeon® I6234 i 16 rdzeni procesora CPU oraz 32 wątki procesora CPU | 1,5 TB | --- | Dostępne |
| TAK | SAP HANA usłudze Azure S64m — 4 wątki procesora Intel® Xeon® I6234 z 32 rdzeniami procesora CPU i 64 wątkami procesora CPU | 3,0 TB | --- | Dostępne |
| TAK | SAP HANA na platformie Azure S96 — 2 rdzenie procesora Intel® Xeon® E7-8890 v4, 48 rdzeni procesora CPU i 96 wątków procesora CPU | 768 GB | 3,0 TB | Dostępne |
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
