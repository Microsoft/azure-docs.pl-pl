---
title: Informacje o kopii zapasowej udziału plików platformy Azure
description: Dowiedz się, jak utworzyć kopię zapasową udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 40cb9ca0bd34fd65ab1983af6384d617db26e996
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539095"
---
# <a name="about-azure-file-share-backup"></a>Informacje o kopii zapasowej udziału plików platformy Azure

Kopia zapasowa udziału plików platformy Azure to natywne, oparte na chmurze rozwiązanie do tworzenia kopii zapasowych, które chroni dane w chmurze i eliminuje dodatkowe koszty konserwacji związane z lokalnymi rozwiązaniami do tworzenia kopii zapasowych. Usługa Azure Backup zapewnia bezproblemowe integrację z usługą Azure File Sync i umożliwia scentralizowanie danych udziałów plików oraz kopii zapasowych. Takie proste, niezawodne i bezpieczne rozwiązanie umożliwia skonfigurowanie ochrony udziałów plików przedsiębiorstwa w kilku prostych krokach z gwarancją, że możesz odzyskać dane na wypadek awarii.

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

1. Pierwszym krokiem w konfigurowaniu kopii zapasowych dla udziałów plików platformy Azure jest utworzenie magazynu usługi Recovery Services. Magazyn umożliwia skonsolidowany widok kopii zapasowych skonfigurowanych w różnych obciążeniach.

2. Po utworzeniu magazynu usługa Azure Backup odnajduje konta magazynu, które mogą być zarejestrowane w magazynie. Można wybrać konto magazynu obsługujące udziały plików, które mają być chronione.

3. Po wybraniu konta magazynu usługa Azure Backup Wyświetla zestaw udziałów plików znajdujących się na koncie magazynu i zapisuje ich nazwy w wykazie warstwy zarządzania.

4. Następnie skonfiguruj zasady tworzenia kopii zapasowych (harmonogram i przechowywanie) zgodnie z wymaganiami, a następnie wybierz udziały plików do utworzenia kopii zapasowej. Usługa Azure Backup rejestruje harmonogramy na płaszczyźnie kontroli w celu wykonywania zaplanowanych kopii zapasowych.

5. Na podstawie określonych zasad usługa Azure Backup Scheduler wyzwala kopie zapasowe w zaplanowanym czasie. W ramach tego zadania migawka udziału plików jest tworzona przy użyciu interfejsu API udziału plików. Tylko adres URL migawki jest przechowywany w magazynie metadanych.

    >[!NOTE]
    >Dane udziału plików nie są przenoszone do usługi kopii zapasowej, ponieważ usługa Backup tworzy migawki i zarządza nimi, które są częścią Twojego konta magazynu, a kopie zapasowe nie są przenoszone do magazynu.

6. Zawartość udziału plików platformy Azure (poszczególne pliki lub pełny udział) można przywrócić z migawek dostępnych w źródłowym udziale plików. Gdy operacja zostanie wyzwolona, adres URL migawki jest pobierany z magazynu metadanych, a dane są wyświetlane i przesyłane z migawki źródłowej do wybranego docelowego udziału plików.

7. Dane monitorowania zadania tworzenia kopii zapasowej i przywracania są przekazywane do usługi monitorowania Azure Backup. Pozwala to monitorować kopie zapasowe w chmurze dla udziałów plików na jednym pulpicie nawigacyjnym. Ponadto można również skonfigurować alerty lub powiadomienia e-mail, gdy będzie to miało stan kondycji kopii zapasowej. Wiadomości e-mail są wysyłane za pośrednictwem usługi poczty e-mail platformy Azure.

## <a name="backup-costs"></a>Koszty kopii zapasowych

Obecnie opłata zostanie naliczona tylko za migawki, ponieważ kopia zapasowa udziału plików platformy Azure jest rozwiązaniem opartym na migawce. Opłaty za magazyn dla migawek są rozliczane wraz z użyciem Azure Files zgodnie z informacjami o cenach opisanymi [tutaj](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć kopię zapasową udziałów plików platformy Azure](backup-afs.md)
* Znajdź odpowiedzi na [pytania dotyczące tworzenia kopii zapasowych Azure Files](backup-azure-files-faq.md)
