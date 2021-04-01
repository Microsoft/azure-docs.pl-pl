---
title: Tworzenie harmonogramów Azure Lab Services w zespołach
description: Dowiedz się, jak tworzyć harmonogramy usług Lab w ramach zespołów.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92042341"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Tworzenie harmonogramów usług Lab i zarządzanie nimi w ramach zespołów

Harmonogramy umożliwiają skonfigurowanie laboratorium klasy w taki sposób, aby maszyny wirtualne w laboratorium były automatycznie uruchamiane i zamykane w określonym czasie. Można zdefiniować harmonogram jednorazowy lub cykliczny. Poniższe procedury umożliwiają tworzenie harmonogramów dla laboratorium zajęć i zarządzanie nimi: 

Oto, jak harmonogramy mają wpływ na maszyny wirtualne laboratorium: 

- Maszyna wirtualna szablonu nie jest dołączona do harmonogramów. 
- Uruchomiono tylko przypisane maszyny wirtualne. Oznacza to, że jeśli komputer nie zostanie przejęty przez użytkownika końcowego (student), maszyna nie zostanie uruchomiona w zaplanowanych godzinach. 
- Wszystkie maszyny wirtualne (bez względu na to, czy zostały przejęte przez użytkownika) są zatrzymane na podstawie harmonogramu laboratorium. 

> [!IMPORTANT]
> Zaplanowany czas działania maszyn wirtualnych nie jest uwzględniany w stosunku do przydziału przydzielonego dla użytkownika. Limit przydziału jest przeznaczony dla czasu poza godzinami harmonogramu, które student spędza na maszynach wirtualnych. 

Użytkownicy mogą tworzyć, edytować i usuwać harmonogramy laboratorium w ramach zespołów tak samo jak w [witrynie sieci Web laboratoriów](https://labs.azure.com). Zapoznaj się z artykułem dotyczącym [tworzenia harmonogramów i zarządzania nimi](how-to-create-schedules-within-teams.md).

## <a name="automatic-shutdown-and-disconnect-settings"></a>Ustawienia automatycznego zamykania i rozłączania

Możesz włączyć kilka funkcji kontroli kosztów automatycznego zamykania, aby aktywnie uniknąć dodatkowych kosztów, gdy maszyny wirtualne nie są aktywnie używane. Kombinacja następujących trzech funkcji automatycznego zamykania i rozłączania przechwytuje większość przypadków, w których użytkownicy przypadkowo opuszczają maszyny wirtualne z systemem:
 
- Automatyczne rozłączanie użytkowników z maszyn wirtualnych uznawanych za bezczynne przez system operacyjny.
- Automatycznie zamykaj maszyny wirtualne podczas odłączania użytkowników.
- Automatycznie Zamykaj uruchomione maszyny wirtualne, ale użytkownicy nie łączą się.

Aby uzyskać więcej informacji, zapoznaj się z artykułem dotyczącym [konfigurowania ustawień automatycznego zamykania dla laboratorium](how-to-enable-shutdown-disconnect.md).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły:

- [Korzystanie z Azure Lab Services w programie Teams — Omówienie](lab-services-within-teams-overview.md)
- [Rozpocznij pracę i Utwórz laboratorium w zespołach](how-to-get-started-create-lab-within-teams.md)
- [Zarządzanie listami użytkowników laboratorium w zespołach](how-to-manage-user-lists-within-teams.md)
- [Zarządzaj pulą maszyn wirtualnych laboratorium w zespołach](how-to-manage-vm-pool-within-teams.md)
- [Dostęp do maszyny wirtualnej w programie Teams — widok ucznia](how-to-access-vm-for-students-within-teams.md)
