---
title: Monitorowanie i obsługa kopii zapasowych w centrum kopii zapasowych
description: W tym artykule wyjaśniono, jak monitorować i obsługiwać kopie zapasowe na dużą skalę przy użyciu centrum kopii zapasowych
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506129"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Monitorowanie i wykonywanie kopii zapasowych przy użyciu centrum kopii zapasowych

Jako administrator kopii zapasowej można użyć centrum kopii zapasowych jako jednego okienka Glass do monitorowania zadań i spisu kopii zapasowych w codziennym okresie. Centrum kopii zapasowych umożliwia również wykonywanie zwykłych operacji, takich jak reagowanie na żądania kopii zapasowych na żądanie, przywracanie kopii zapasowych, tworzenie zasad tworzenia kopii zapasowych i tak dalej.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

* Centrum kopii zapasowych jest obecnie obsługiwane w przypadku kopii zapasowych maszyny wirtualnej platformy Azure, bazy danych SQL w usłudze Kopia zapasowa maszyny wirtualnej platformy Azure, SAP HANA w ramach kopii zapasowej maszyny wirtualnej platformy Azure, Azure Files tworzenia kopii zapasowych, tworzenia kopii zapasowych Azure Database for PostgreSQL Managed Disks Azure
* Zapoznaj się z [matrycą pomocy technicznej](backup-center-support-matrix.md) , aby uzyskać szczegółową listę obsługiwanych i nieobsługiwanych scenariuszy.

## <a name="backup-instances"></a>Wystąpienia kopii zapasowej

Centrum kopii zapasowych umożliwia łatwe wyszukiwanie i odnajdowanie wystąpień kopii zapasowych w ramach funkcji tworzenia kopii zapasowych.

Wybranie karty **wystąpienia kopii zapasowej** w centrum kopii zapasowych umożliwia wyświetlenie szczegółowych informacji o wszystkich wystąpieniach kopii zapasowych, do których masz dostęp.

 Można wyświetlić następujące informacje dotyczące poszczególnych wystąpień kopii zapasowych:

* Subskrypcja źródła danych
* Grupa zasobów źródła danych
* Najnowszy punkt odzyskiwania
* Magazyn skojarzony z wystąpieniem kopii zapasowej
* Stan ochrony

 Można również filtrować listę wystąpień kopii zapasowych na następujących parametrach:

* Subskrypcja źródła danych
* Grupa zasobów źródła danych
* Lokalizacja źródła danych
* Typ źródła danych
* Magazyn
* Stan ochrony
* Tagi DataSource

Kliknięcie prawym przyciskiem myszy dowolnego elementu w siatce umożliwia wykonywanie akcji na danym wystąpieniu kopii zapasowej, takich jak przechodzenie do zasobu, wyzwalanie kopii zapasowych na żądanie oraz przywracanie lub zatrzymywanie kopii zapasowej.

![Centrum kopii zapasowych — wystąpienia](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Zadania tworzenia kopii zapasowej

Centrum kopii zapasowych umożliwia wyświetlenie szczegółowych informacji na temat wszystkich zadań, które zostały utworzone w ramach tworzenia kopii zapasowej, i podjęcia odpowiedniej akcji dla zadań zakończonych niepowodzeniem.

Wybranie elementu menu **zadania tworzenia kopii zapasowej** w centrum kopii zapasowych umożliwia wyświetlenie wszystkich zadań. Każde zadanie zawiera następujące informacje:

* Wystąpienie kopii zapasowej skojarzone z zadaniem
* Subskrypcja źródła danych
* Grupa zasobów źródła danych
* Lokalizacja źródła danych
* Operacja zadania
* Stan zadania
* Godzina rozpoczęcia zadania
* Czas trwania zadania

Wybranie elementu w siatce pozwala wyświetlić więcej szczegółów dotyczących danego zadania. Kliknięcie prawym przyciskiem myszy elementu ułatwia przejście do zasobu w celu podjęcia odpowiedniej akcji.

![Centrum kopii zapasowych — zadania](./media/backup-center-monitor-operate/backup-center-jobs.png)

Za pomocą karty **zadania tworzenia kopii zapasowej** można wyświetlać zadania w ciągu ostatnich siedmiu dni. Aby wyświetlić starsze zadania, użyj [raportów kopii zapasowych](backup-center-obtain-insights.md).

## <a name="vaults"></a>Magazyny

Wybranie elementu menu **magazyny** w centrum kopii zapasowych umożliwia wyświetlenie listy wszystkich [magazynów Recovery Services](backup-azure-recovery-services-vault-overview.md) i [magazynów kopii zapasowych](backup-vault-overview.md) , do których masz dostęp. Listę można filtrować przy użyciu następujących parametrów:

* Subskrypcja magazynu
* Grupa zasobów magazynu
* Nazwa magazynu
* Typ źródła danych skojarzony z zasadami

Wybranie dowolnego elementu na liście umożliwia przejście do danego magazynu.

![Centrum kopii zapasowych — magazyny](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Zasady tworzenia kopii zapasowych

Centrum kopii zapasowych umożliwia wyświetlanie i edytowanie kluczowych informacji dotyczących dowolnych zasad tworzenia kopii zapasowych.

Wybranie elementu menu **zasady tworzenia kopii zapasowych** umożliwia wyświetlenie wszystkich zasad, które zostały utworzone w ramach danej kopii zapasowej. Listę można filtrować według subskrypcji magazynu, grupy zasobów, typu źródła danych i magazynu. Kliknięcie prawym przyciskiem myszy elementu w siatce pozwala wyświetlić skojarzone elementy dla tych zasad, edytować zasady lub nawet usunąć je w razie potrzeby.

![Centrum kopii zapasowych — zasady](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie brakiem kopii zapasowej](backup-center-govern-environment.md)
* [Wykonywanie akcji przy użyciu centrum kopii zapasowych](backup-center-actions.md)
* [Uzyskaj wgląd w kopie zapasowe](backup-center-obtain-insights.md)
