---
title: Informacje o kopii zapasowej udziału plików platformy Azure
description: Dowiedz się, jak utworzyć kopię zapasową udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: e955df7662bd18ed1d1d4ec1f0aa6c9474c5386f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378087"
---
# <a name="about-azure-file-share-backup"></a>Informacje o kopii zapasowej udziału plików platformy Azure

Kopia zapasowa udziału plików platformy Azure to natywne, oparte na chmurze rozwiązanie do tworzenia kopii zapasowych, które chroni dane w chmurze i eliminuje dodatkowe koszty konserwacji związane z lokalnymi rozwiązaniami do tworzenia kopii zapasowych. Usługa Azure Backup zapewnia bezproblemowe integrację z Azure File Sync i pozwala na scentralizowanie danych udziałów plików oraz kopii zapasowych. Takie proste, niezawodne i bezpieczne rozwiązanie umożliwia skonfigurowanie ochrony udziałów plików przedsiębiorstwa w kilku prostych krokach z gwarancją, że możesz odzyskać dane w dowolnym scenariuszu awarii.

## <a name="key-benefits-of-azure-file-share-backup"></a>Najważniejsze zalety tworzenia kopii zapasowych udziałów plików platformy Azure

* **Infrastruktura zerowa**: żadne wdrożenie nie jest konieczne, aby można było skonfigurować ochronę udziałów plików.
* **Zachowanie niestandardowe**: można skonfigurować tworzenie kopii zapasowych codziennie/co tydzień/miesięcznie/rocznie zgodnie z wymaganiami.
* **Wbudowane funkcje zarządzania**: można zaplanować kopie zapasowe i określić żądany okres przechowywania bez dodatkowych nakładów związanych z oczyszczaniem danych.
* **Natychmiastowe przywracanie**: kopia zapasowa udziału plików platformy Azure używa migawek udziałów plików, dzięki czemu możesz wybrać tylko te pliki, które chcesz przywrócić natychmiast.
* **Generowanie alertów i raportowanie**: można skonfigurować alerty dotyczące błędów tworzenia kopii zapasowej i przywracania oraz korzystać z rozwiązania raportowania dostępnego w Azure Backup, aby uzyskiwać wgląd w kopie zapasowe w udziałach plików.
* **Ochrona przed przypadkowym usunięciem udziałów plików**: Azure Backup włącza [funkcję usuwania nietrwałego](../storage/files/storage-files-prevent-file-share-deletion.md) na poziomie konta magazynu z okresem przechowywania wynoszącym 14 dni. Nawet jeśli złośliwy aktor usuwa udział plików, jego zawartość i punkty odzyskiwania (migawki) są zachowywane przez konfigurowalny okres przechowywania, co umożliwia pomyślne i pełne odzyskiwanie zawartości źródłowej i migawek bez utraty danych.

## <a name="architecture"></a>Architektura

![Architektura tworzenia kopii zapasowych udziałów plików platformy Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Jak działa proces tworzenia kopii zapasowej

1. Pierwszym krokiem w konfigurowaniu kopii zapasowej dla udziałów plików platformy Azure jest utworzenie magazynu Recovery Services. Magazyn umożliwia skonsolidowany widok kopii zapasowych skonfigurowanych w różnych obciążeniach.

2. Po utworzeniu magazynu usługa Azure Backup odnajduje konta magazynu, które mogą być zarejestrowane w magazynie. Można wybrać konto magazynu obsługujące udziały plików, które mają być chronione.

3. Po wybraniu konta magazynu usługa Azure Backup Wyświetla zestaw udziałów plików znajdujących się na koncie magazynu i zapisuje ich nazwy w wykazie warstwy zarządzania.

4. Następnie skonfiguruj zasady tworzenia kopii zapasowych (harmonogram i przechowywanie) zgodnie z wymaganiami, a następnie wybierz udziały plików do utworzenia kopii zapasowej. Usługa Azure Backup rejestruje harmonogramy na płaszczyźnie kontroli w celu wykonywania zaplanowanych kopii zapasowych.

5. Na podstawie określonych zasad usługa Azure Backup Scheduler wyzwala kopie zapasowe w zaplanowanym czasie. W ramach tego zadania migawka udziału plików jest tworzona przy użyciu interfejsu API udziału plików. Tylko adres URL migawki jest przechowywany w magazynie metadanych.

    >[!NOTE]
    >Dane udziału plików nie są transferowane do usługi kopii zapasowej, ponieważ usługa Backup tworzy migawki i zarządza nimi, które są częścią Twojego konta magazynu, a kopie zapasowe nie są transferowane do magazynu.

6. Zawartość udziału plików platformy Azure (poszczególne pliki lub pełny udział) można przywrócić z migawek dostępnych w źródłowym udziale plików. Gdy operacja zostanie wyzwolona, adres URL migawki jest pobierany z magazynu metadanych, a dane są wyświetlane i przesyłane z migawki źródłowej do wybranego docelowego udziału plików.

7. Jeśli używasz Azure File Sync, usługa Backup wskazuje usługę Azure File Syncą ścieżki przywracanych plików, a następnie wyzwala proces wykrywania zmian w tle dla tych plików. Wszystkie pliki, które uległy zmianie, są synchronizowane z punktem końcowym serwera. Ten proces odbywa się równolegle z oryginalnym przywracaniem do udziału plików platformy Azure.

8. Dane monitorowania zadania tworzenia kopii zapasowej i przywracania są przekazywane do usługi monitorowania Azure Backup. Pozwala to monitorować kopie zapasowe w chmurze dla udziałów plików na jednym pulpicie nawigacyjnym. Ponadto można również skonfigurować alerty lub powiadomienia e-mail, gdy będzie to miało stan kondycji kopii zapasowej. Wiadomości e-mail są wysyłane za pośrednictwem usługi poczty e-mail platformy Azure.

## <a name="backup-costs"></a>Koszty kopii zapasowych

Istnieją dwa koszty związane z rozwiązaniem kopii zapasowej udziału plików platformy Azure:

1. **Koszt magazynu migawek**: opłaty za magazyn w przypadku migawek są rozliczane wraz z użyciem Azure Files zgodnie z szczegółami cennika wymienionymi [tutaj](https://azure.microsoft.com/pricing/details/storage/files/)

2. **Opłata za chronione wystąpienie**: od 1 września do 2020, klienci będą obciążani opłatą za chronione wystąpienie zgodnie z szczegółami cennika wymienionymi [tutaj](https://azure.microsoft.com/pricing/details/backup/). Opłata za chronione wystąpienie jest zależna od łącznego rozmiaru chronionych udziałów plików na koncie magazynu.

Aby uzyskać szczegółowe oszacowania dotyczące tworzenia kopii zapasowych udziałów plików platformy Azure, możesz pobrać szczegółowe [Azure Backup cenowe](https://aka.ms/AzureBackupCostEstimates).  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć kopię zapasową udziałów plików platformy Azure](backup-afs.md)
* Znajdź odpowiedzi na [pytania dotyczące tworzenia kopii zapasowych Azure Files](backup-azure-files-faq.md)
