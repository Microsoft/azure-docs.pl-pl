---
title: Informacje o kopii zapasowej udziału plików platformy Azure
description: Dowiedz się, jak kopię zapasową udziałów plików platformy Azure w magazynie usługi Recovery Services
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c4f9dd816ace2c9aec8f48207fbce88acf34e24a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516819"
---
# <a name="about-azure-file-share-backup"></a>Informacje o kopii zapasowej udziału plików platformy Azure

Kopia zapasowa udziału plików platformy Azure to natywne, oparte na chmurze rozwiązanie do tworzenia kopii zapasowych, które chroni dane w chmurze i eliminuje dodatkowe koszty konserwacji związane z lokalnymi rozwiązaniami do tworzenia kopii zapasowych. Usługa Azure Backup bezproblemowo integruje się z Azure File Sync i umożliwia scentralizowanie danych udziałów plików oraz kopii zapasowych. To proste, niezawodne i bezpieczne rozwiązanie umożliwia skonfigurowanie ochrony udziałów plików przedsiębiorstwa w kilku prostych krokach z gwarancją, że dane można odzyskać w dowolnym scenariuszu awarii.

## <a name="key-benefits-of-azure-file-share-backup"></a>Najważniejsze korzyści wynikające z tworzenia kopii zapasowej udziału plików platformy Azure

* **Infrastruktura zero:** do skonfigurowania ochrony udziałów plików nie jest wymagane żadne wdrożenie.
* **Przechowywanie dostosowane:** możesz skonfigurować kopie zapasowe z przechowywaniem codziennie/co tydzień/co miesiąc/co rok zgodnie z wymaganiami.
* **Wbudowane funkcje zarządzania:** możesz zaplanować tworzenie kopii zapasowych i określić żądany okres przechowywania bez dodatkowego obciążenia związanego z przycinaniem danych.
* **Natychmiastowe przywracanie:** kopia zapasowa udziału plików platformy Azure używa migawek udziałów plików, dzięki czemu możesz natychmiast wybrać tylko pliki, które chcesz przywrócić.
* **Alerty i** raportowanie: możesz skonfigurować alerty dotyczące niepowodzeń tworzenia kopii zapasowych i przywracania oraz korzystać z rozwiązania do raportowania udostępnianego przez usługę Azure Backup w celu uzyskania szczegółowych informacji na temat kopii zapasowych w udziałach plików.
* **Ochrona przed przypadkowym usunięciem udziałów** plików: [](../storage/files/storage-files-prevent-file-share-deletion.md) Azure Backup funkcję usuwania nie soft na poziomie konta magazynu z okresem przechowywania 14 dni. Nawet jeśli złośliwy aktor usunie udział plików, zawartość i punkty odzyskiwania udziału plików (migawki) są zachowywane przez konfigurowalny okres przechowywania, co umożliwia pomyślne i pełne odzyskiwanie zawartości źródłowej i migawek bez utraty danych.

## <a name="architecture"></a>Architektura

![Architektura kopii zapasowej udziału plików platformy Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Jak działa proces tworzenia kopii zapasowej

1. Pierwszym krokiem konfigurowania kopii zapasowej udziałów plików platformy Azure jest utworzenie magazynu usługi Recovery Services. Magazyn zapewnia skonsolidowany widok kopii zapasowych skonfigurowanych dla różnych obciążeń.

2. Po utworzeniu magazynu usługa Azure Backup odnajduje konta magazynu, które można zarejestrować w magazynie. Możesz wybrać konto magazynu hostowania udziałów plików, które chcesz chronić.

3. Po wybraniu konta magazynu usługa Azure Backup wyświetla zestaw udziałów plików obecnych na koncie magazynu i przechowuje ich nazwy w katalogu warstwy zarządzania.

4. Następnie skonfiguruj zasady tworzenia kopii zapasowej (harmonogram i przechowywanie) zgodnie z wymaganiami i wybierz udziały plików do tworzenia kopii zapasowej. Usługa Azure Backup rejestruje harmonogramy na płaszczyźnie sterowania w celu wykonywania zaplanowanych kopii zapasowych.

5. Na podstawie określonych zasad harmonogram Azure Backup kopie zapasowe w zaplanowanym czasie. W ramach tego zadania migawka udziału plików jest tworzona przy użyciu interfejsu API udziału plików. Tylko adres URL migawki jest przechowywany w magazynie metadanych.

    >[!NOTE]
    >Dane udziału plików nie są przesyłane do usługi Backup, ponieważ usługa Backup tworzy migawki, które są częścią konta magazynu, i zarządza nimi, a kopie zapasowe nie są przenoszone do magazynu.

6. Zawartość udziału plików platformy Azure (pojedyncze pliki lub pełny udział) można przywrócić z migawek dostępnych w źródłowym udziałze plików. Po wyzwoleniu operacji adres URL migawki jest pobierany z magazynu metadanych, a dane są wyświetlane na liście i przesyłane z migawki źródłowej do wybranego docelowego udziału plików.

7. Jeśli używasz usługi Azure File Sync, usługa Backup wskazuje usłudze Azure File Sync ścieżki przywracanych plików, co następnie wyzwala proces wykrywania zmian w tle dla tych plików. Wszystkie zmienione pliki są synchronizowane z punktem końcowym serwera. Ten proces odbywa się równolegle z oryginalnym przywróceniem do udziału plików platformy Azure.

8. Dane monitorowania zadania tworzenia kopii zapasowej i przywracania są wypychane do Azure Backup monitorowania. Dzięki temu można monitorować kopie zapasowe udziałów plików w chmurze na jednym pulpicie nawigacyjnym. Ponadto można również skonfigurować alerty lub powiadomienia e-mail w przypadku wpływu na kondycję kopii zapasowej. Wiadomości e-mail są wysyłane za pośrednictwem usługi poczty e-mail platformy Azure.

## <a name="backup-costs"></a>Koszty tworzenia kopii zapasowych

Rozwiązanie do tworzenia kopii zapasowych udziałów plików platformy Azure wiąże się z dwoma kosztami:

1. **Koszt magazynu migawek:** opłaty za magazyn naliczane za migawki są rozliczane razem z użyciem Azure Files zgodnie ze szczegółami cen wymienionymi [tutaj](https://azure.microsoft.com/pricing/details/storage/files/)

2. **Opłata za wystąpienie chronione:** od 1 września 2020 r. klienci będą obciążani opłatą za wystąpienie chronione zgodnie ze szczegółami cen wymienionymi [tutaj.](https://azure.microsoft.com/pricing/details/backup/) Opłata za chronione wystąpienie zależy od całkowitego rozmiaru chronionych udziałów plików na koncie magazynu.

Aby uzyskać szczegółowe oszacowania dotyczące kopii zapasowej udziałów plików platformy Azure, możesz pobrać szczegółowy [Azure Backup do szacowania cen.](https://aka.ms/AzureBackupCostEstimates)  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [wrócić do kopii zapasowej udziałów plików platformy Azure](backup-afs.md)
* Znajdź odpowiedzi na [pytania dotyczące kopii zapasowej Azure Files](backup-azure-files-faq.yml)
