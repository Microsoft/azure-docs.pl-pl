---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183262"
---
| **Identyfikator limitu** | **Granice** | **Komentarze** |
| --- | --- | --- |
| Całkowita pojemność (łącznie z chmurą) |Do 64 TB na urządzenie wirtualne |Można przełączyć pełną tablicę wirtualną StorSimple do innej pustej tablicy. Jeśli próbujesz przywrócić to samo urządzenie, upewnij się, że masz wystarczająco dużo miejsca na urządzeniu, aby ukończyć tę operację. Po przekroczeniu 32 TB nie można przywrócić do tego samego urządzenia. |
| Maksymalna liczba poświadczeń konta magazynu na urządzenie |1 | |
| Maksymalna liczba woluminów/udziałów |16 | |
| Minimalny rozmiar udziału warstwowego |500 GB | |
| Minimalny rozmiar woluminu warstwowego |500 GB | |
| Maksymalny rozmiar udziału warstwowego |20 TB | |
| Maksymalny rozmiar woluminu warstwowego |5 TB | |
| Minimalny rozmiar udziału przypiętego lokalnie |50 GB | |
| Minimalny rozmiar woluminu przypiętego lokalnie |50 GB | |
| Maksymalny rozmiar udziału przypiętego lokalnie |2 TB | |
| Maksymalny rozmiar woluminu przypiętego lokalnie |200 GB | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzenie |64 | |
| Maksymalna liczba kopii zapasowych przechowywanych przez urządzenie wirtualne w folderze *.* backups dla serwera plików |5 |Obejmuje to najnowsze zaplanowane (generowane przez domyślne zasady tworzenia kopii zapasowych) oraz ręczne tworzenie kopii zapasowych. |
| Maksymalna liczba zaplanowanych kopii zapasowych przechowywanych przez urządzenie |55 |30 codziennych kopii zapasowych<br>12 comiesięcznych kopii zapasowych<br>13-roczne kopie zapasowe |
| Maksymalna liczba ręcznych kopii zapasowych przechowywanych przez urządzenie |45 | |
| Maksymalna liczba plików na udział serwera plików |1 mln |Podczas przywracania urządzenia czasy przywracania są proporcjonalne do liczby plików we wszystkich udziałach na urządzeniu. |
| Maksymalna liczba plików na wolumin dla serwera iSCSI |1 mln | |
| Maksymalna liczba plików na tablicę wirtualną |4 000 000 | |
| Przywróć czas odzyskiwania |Szybkie przywracanie |Przywracanie jest oparte na mapie cieplnej i zależy od rozmiaru woluminu.<br>Operacje tworzenia kopii zapasowej mogą wystąpić, gdy operacja przywracania jest w toku. |

