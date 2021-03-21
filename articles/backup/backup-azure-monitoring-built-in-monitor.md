---
title: Monitorowanie Azure Backup chronionych obciążeń
description: W tym artykule omówiono możliwości monitorowania i powiadamiania dotyczące obciążeń Azure Backup przy użyciu Azure Portal.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 2273b66be88cb22a15d0779ed2918ba3d94da1ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713375"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorowanie obciążeń Azure Backup

Azure Backup oferuje wiele rozwiązań do tworzenia kopii zapasowych opartych na wymaganiach dotyczących kopii zapasowych i topologii infrastruktury (lokalnie i na platformie Azure). Każdy użytkownik kopii zapasowej lub administrator powinien zobaczyć, co się dzieje we wszystkich rozwiązaniach, i może zostać powiadomiony w ważnych scenariuszach. W tym artykule opisano możliwości monitorowania i powiadamiania udostępniane przez usługę Azure Backup.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Elementy kopii zapasowej w magazynie Recovery Services

Możesz monitorować wszystkie elementy kopii zapasowej za pośrednictwem magazynu Recovery Servicesowego. Przechodzenie do sekcji **elementy kopii zapasowej** w magazynie otwiera widok, który zapewnia liczbę elementów kopii zapasowej każdego typu obciążenia skojarzonego z magazynem. Kliknięcie dowolnego wiersza powoduje otwarcie widoku szczegółowego zawierającego listę wszystkich elementów kopii zapasowej danego typu obciążenia, z informacjami na temat ostatniego stanu kopii zapasowej dla każdego elementu, dostępnego najnowszego punktu przywracania i tak dalej.

![Elementy kopii zapasowej magazynu RS](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> W przypadku elementów, których kopia zapasowa jest wykonywana na platformie Azure przy użyciu programu DPM, na liście zostaną wyświetlone wszystkie źródła danych chronione (zarówno na dysku, jak i w trybie online) przy użyciu serwera DPM Jeśli ochrona źródła danych z zachowaną kopią zapasową jest zatrzymana, źródło danych będzie nadal wyświetlane w portalu. Możesz przejść do szczegółów źródła danych, aby sprawdzić, czy punkty odzyskiwania znajdują się na dysku, w trybie online, czy w obu. Ponadto źródła danych, dla których ochrona online jest zatrzymana, ale dane są zachowywane, rozliczenia punktów odzyskiwania online są kontynuowane do momentu całkowitego usunięcia danych.
>
> Wersja programu DPM musi mieć wartość DPM 1807 (5.1.378.0) lub DPM 2019 (wersja 10.19.58.0 lub nowsza), aby elementy kopii zapasowej były widoczne w portalu magazynu Recovery Services.

## <a name="backup-jobs-in-recovery-services-vault"></a>Zadania tworzenia kopii zapasowej w magazynie Recovery Services

Azure Backup zapewnia wbudowane funkcje monitorowania i alertów dla obciążeń chronionych przez Azure Backup. W ustawieniach magazynu Recovery Services sekcja **monitorowanie** zawiera wbudowane zadania i alerty.

![Niezbudowane monitorowanie magazynu RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Zadania są generowane, gdy wykonywane są operacje, takie jak konfigurowanie kopii zapasowej, wykonywanie kopii zapasowej, przywracanie, Usuwanie kopii zapasowej itd.

Poniżej przedstawiono zadania z następujących rozwiązań Azure Backup:

- Kopia zapasowa maszyny wirtualnej platformy Azure
- Kopia zapasowa plików platformy Azure
- Tworzenie kopii zapasowych platformy Azure, takich jak SQL i SAP HANA
- Agent Microsoft Azure Recovery Services (MARS)

Nie są wyświetlane zadania z programu System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (serwera usługi MAB).

> [!NOTE]
> Obciążenia platformy Azure, takie jak SQL i SAP HANA Backup na maszynach wirtualnych platformy Azure, mają ogromną liczbę zadań tworzenia kopii zapasowych. Na przykład kopie zapasowe dzienników można uruchamiać co 15 minut. W przypadku takich obciążeń bazy danych wyświetlane są tylko operacje wyzwalane przez użytkownika. Zaplanowane operacje tworzenia kopii zapasowej nie są wyświetlane.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alerty kopii zapasowych w magazynie Recovery Services

> [!NOTE]
> Wyświetlanie alertów w magazynach nie jest obecnie obsługiwane w centrum kopii zapasowych. Aby wyświetlić alerty dla tego magazynu, należy przejść do poszczególnych magazynów.

Alerty są szczególnie sytuacje, w których użytkownicy są powiadamiani, aby mogli podejmować odpowiednie działania. W sekcji **alerty kopii zapasowej** znajdują się alerty wygenerowane przez usługę Azure Backup. Te alerty są definiowane przez usługę i użytkownik nie może tworzyć alertów niestandardowych.

### <a name="alert-scenarios"></a>Scenariusze alertów

Poniższe scenariusze są definiowane przez usługę jako scenariusze z alertami.

- Niepowodzenia wykonywania kopii zapasowej/przywracania
- Tworzenie kopii zapasowej zakończyło się pomyślnie z ostrzeżeniami dla agenta Microsoft Azure Recovery Services (MARS)
- Zatrzymaj ochronę z zachowaniem Zachowaj dane/Zatrzymaj ochronę przy użyciu danych usuwania

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Alerty z następujących rozwiązań Azure Backup są wyświetlane w tym miejscu

- Kopie zapasowe maszyn wirtualnych platformy Azure
- Kopie zapasowe plików platformy Azure
- Kopie zapasowe obciążeń platformy Azure, takie jak SQL, SAP HANA
- Agent Microsoft Azure Recovery Services (MARS)

> [!NOTE]
> Alerty z programu System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (serwera usługi MAB) nie są wyświetlane w tym miejscu.

### <a name="consolidated-alerts"></a>Skonsolidowane alerty

W przypadku rozwiązań do tworzenia kopii zapasowych platformy Azure, takich jak SQL i SAP HANA, kopie zapasowe dzienników można generować bardzo często (do co 15 minut zgodnie z zasadami). Istnieje również możliwość, że błędy kopii zapasowej dziennika są również bardzo częste (do co 15 minut). W tym scenariuszu użytkownik końcowy zostanie przesunięty w przypadku zgłoszenia alertu dla każdego wystąpienia błędu. W związku z tym alert jest wysyłany dla pierwszego wystąpienia i jeśli późniejsze błędy są spowodowane tą samą główną przyczyną, kolejne alerty nie są generowane. Pierwszy alert jest aktualizowany z liczbą błędów. Jeśli jednak alert zostanie zdezaktywowany przez użytkownika, następne wystąpienie wywoła kolejny alert i będzie traktowane jako pierwszy alert dla tego wystąpienia. Poniżej przedstawiono sposób, w jaki Azure Backup wykonuje konsolidację alertów dla kopii zapasowych SQL i SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Wyjątki w przypadku niezgłoszenia alertu

Istnieje kilka wyjątków, gdy alert nie zostanie zgłoszony w przypadku awarii. Są to:

- Użytkownik jawnie anulował uruchomione zadanie
- Zadanie nie powiodło się, ponieważ trwa inne zadanie tworzenia kopii zapasowej (nic nie działa, ponieważ oczekujemy na zakończenie poprzedniego zadania)
- Zadanie tworzenia kopii zapasowej maszyny wirtualnej nie powiodło się, ponieważ kopia zapasowa maszyny wirtualnej platformy Azure już nie istnieje
- [Skonsolidowane alerty](#consolidated-alerts)

Powyższe wyjątki zostały zaprojektowane z myślą o tym, że wynik tych operacji (przede wszystkim jest wyzwalany przez użytkownika) jest natychmiast wyświetlany na klientach Portal/PS/CLI. Użytkownik jest natychmiast świadomy i nie potrzebuje powiadomienia.

### <a name="alert-types"></a>Typy alertów

W oparciu o ważność alertu alerty można definiować w trzech typach:

- **Krytyczny**: w zasadzie wszystkie błędy tworzenia kopii zapasowej lub odzyskiwania (zaplanowane lub wywołane przez użytkownika) spowodują wygenerowanie alertu i będą wyświetlane jako alert krytyczny, a także operacje niszczące, takie jak usuwanie kopii zapasowej.
- **Ostrzeżenie**: Jeśli operacja tworzenia kopii zapasowej zakończy się pomyślnie, ale z kilkoma ostrzeżeniami, są one wyświetlane jako alerty ostrzegawcze. Alerty ostrzegawcze są obecnie dostępne tylko dla kopii zapasowych agenta Azure Backup.
- **Informacyjny**: obecnie nie jest generowany alert informacyjny przez usługę Azure Backup.

## <a name="notification-for-backup-alerts"></a>Powiadomienia o alertach dotyczących kopii zapasowych

> [!NOTE]
> Konfigurację powiadomień można wykonać tylko za pomocą Azure Portal. Obsługa szablonu Azure Resource Manager interfejsu API (PS/CLI/REST) nie jest obsługiwana.

Po podniesieniu alertu użytkownicy będą powiadamiani. Azure Backup zapewnia wbudowany mechanizm powiadomień za pośrednictwem poczty e-mail. Jeden z nich może określać poszczególne adresy e-mail lub listy dystrybucyjne do powiadomienia o wygenerowaniu alertu. Możesz również wybrać, czy otrzymywać powiadomienia o każdym indywidualnym alercie, czy grupować je w co godzinę, a następnie otrzymywać powiadomienia.

![Powiadomienie e-mail dotyczące nieskompilowanego magazynu RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Po skonfigurowaniu powiadomienia otrzymasz powitalną lub wprowadzającą wiadomość e-mail. Spowoduje to potwierdzenie, że Azure Backup mogą wysyłać wiadomości e-mail na te adresy, gdy zostanie zgłoszony alert.<br>

Jeśli częstotliwość została ustawiona na podsumowanie godzinowe, a alert został zgłoszony i rozwiązany w ciągu godziny, nie będzie częścią nadchodzącego podsumowania godzinowego.

> [!NOTE]
>
> - W przypadku wykonania operacji niszczącej, takiej jak **zatrzymanie ochrony z usuwaniem danych** , zostanie zgłoszony alert, a wiadomość e-mail zostanie wysłana do właścicieli subskrypcji, administratorów i współadministratorów, nawet jeśli nie skonfigurowano powiadomień dla magazynu Recovery Services.
> - Aby skonfigurować powiadomienie dla zadań zakończonych powodzeniem, użyj [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Dezaktywacja alertów

Aby dezaktywować/rozwiązywać aktywny alert, możesz wybrać element listy odpowiadający Alertowi, który ma zostać zdezaktywowany. Spowoduje to otwarcie ekranu wyświetlającego szczegółowe informacje o alercie, z przyciskiem **Dezaktywuj** u góry. Wybranie tego przycisku spowoduje zmianę stanu alertu na **nieaktywny**. Możesz również dezaktywować alert, klikając prawym przyciskiem myszy element listy odpowiadający temu Alertowi i wybierając pozycję **Dezaktywuj**.

![Dezaktywacja alertu dotyczącego magazynu RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Monitor alertów dla Azure Backup (wersja zapoznawcza)

Azure Backup udostępnia również alerty za pośrednictwem Azure Monitor, aby umożliwić użytkownikom spójne środowisko zarządzania alertami w różnych usługach platformy Azure, łącznie z tworzeniem kopii zapasowych. Za pomocą alertów Azure Monitor można kierować alerty do dowolnego kanału powiadomień obsługiwanego przez Azure Backup, takich jak wiadomości e-mail, narzędzia ITSM, webhook, Logic App i tak dalej.

Obecnie ta funkcja jest dostępna dla baz danych platformy Azure dla serwera PostgreSQL, obiektów blob platformy Azure i platformy Azure Managed Disks. Alerty są generowane dla następujących scenariuszy i można uzyskać do nich dostęp, przechodząc do magazynu kopii zapasowych i klikając element menu **alerty** :

- Usuń dane kopii zapasowej
- Niepowodzenie kopii zapasowej (aby otrzymywać alerty dotyczące błędu kopii zapasowej, należy zarejestrować flagę AFEC o nazwie **EnableAzureBackupJobFailureAlertsToAzureMonitor** za pośrednictwem portalu w wersji zapoznawczej)
- Niepowodzenie przywracania (aby otrzymywać alerty dotyczące błędów przywracania, należy zarejestrować flagę AFEC o nazwie **EnableAzureBackupJobFailureAlertsToAzureMonitor** za pośrednictwem portalu w wersji zapoznawczej)

Aby uzyskać więcej informacji na temat alertów Azure Monitor, zobacz [Omówienie alertów na platformie Azure](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-overview).

## <a name="next-steps"></a>Następne kroki

[Monitorowanie obciążeń Azure Backup przy użyciu Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
