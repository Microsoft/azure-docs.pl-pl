---
title: Odzyskiwanie bazy danych Oracle w infrastrukturze Azure BareMetal
description: Dowiedz się, jak odzyskać bazę danych Oracle w infrastrukturze Azure BareMetal.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590276"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Odzyskiwanie bazy danych Oracle w infrastrukturze Azure BareMetal

Chociaż żadna pojedyncza technologia nie chroni przed wszystkimi scenariuszami awarii, łączenie funkcji daje administratorom baz danych możliwość odzyskania bazy danych w niemal każdej sytuacji.

## <a name="causes-of-database-failure"></a>Przyczyny awarii bazy danych

Błędy bazy danych mogą wystąpić z wielu powodów, ale zazwyczaj należą do kilku kategorii:

- Błędy manipulowania danymi.
- Utrata dzienników ponownego śledzenia w trybie online.
- Utrata plików kontroli bazy danych.
- Utrata plików danych bazy danych.
- Uszkodzenie danych fizycznych.

## <a name="choose-your-method-of-recovery"></a>Wybieranie metody odzyskiwania

Typ odzyskiwania zależy od typu błędu. Załóżmy, że obiekt jest porzucony lub dane są niepoprawnie modyfikowane. Następnie najszybszym rozwiązaniem jest zazwyczaj wykonać operację flashback bazy danych. W innych przypadkach odzyskiwanie za pomocą migawki Azure NetApp Files może zapewnić odzyskiwanie, którego potrzebujesz. Drzewo decyzyjne na poniższej ilustracji reprezentuje typowe scenariusze awarii i odzyskiwania, jeśli zaimplementowano wszystkie opcje ochrony danych opisane powyżej.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Diagram drzewa decyzyjnego odzyskiwania bazy danych." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Należy pamiętać, że to przykładowe drzewo decyzyjne jest przeglądane tylko z punktu widzenia administratora bazy danych. Każde wdrożenie może mieć inne wymagania, które mogą zmienić kolejność wyboru. Na przykład przełączenie roli bazy danych do innego regionu za pomocą funkcji Data Guard może mieć niekorzystny wpływ na wydajność aplikacji. Metoda odzyskiwania migawki może mieć niższy czas RTO. Aby zapewnić, że zostaną spełnione wymagania dotyczące celu czasu RTO/celu czasu eksploatacji, zalecamy wykonanie tych operacji i utworzenie udokumentowanych procedur w celu ich wykonania w razie potrzeby.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o infrastrukturze BareMetal:

- [Co to jest infrastruktura BareMetal na platformie Azure?](../../concepts-baremetal-infrastructure-overview.md)
- [Łączenie wystąpień infrastruktury BareMetal na platformie Azure](../../connect-baremetal-infrastructure.md)
