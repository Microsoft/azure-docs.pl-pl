---
title: Kopii zapasowej farmy programu SharePoint na platformie Azure za pomocą programu DPM
description: Ten artykuł zawiera omówienie ochrony serwera programu DPM/Azure Backup farmy programu SharePoint na platformie Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 3524107b545c151fcf931b89c629a61d83f47ace
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515162"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Kopii zapasowej farmy programu SharePoint na platformie Azure za pomocą programu DPM

Kopię zapasową farmy programu SharePoint można Microsoft Azure za pomocą programu System Center Data Protection Manager (DPM) w taki sam sposób, jak w przypadku innych źródeł danych. Azure Backup zapewnia elastyczność w harmonogramie tworzenia kopii zapasowych w celu tworzenia codziennych, cotygodniowych, comiesięcznych lub rocznie punktów kopii zapasowych oraz udostępnia opcje zasad przechowywania dla różnych punktów kopii zapasowej. Program DPM umożliwia przechowywanie kopii dysków lokalnych w celu osiągnięcia celów szybkiego czasu odzyskiwania (RTO) oraz przechowywanie kopii na platformie Azure w celu ekonomicznego i długoterminowego przechowywania.

Proces tworzenia kopii zapasowej programu SharePoint na platformie Azure za pomocą programu DPM jest bardzo podobny do tworzenia kopii zapasowej programu SharePoint w programie DPM lokalnie. W tym artykule opisano konkretne zagadnienia dotyczące platformy Azure.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint i powiązane scenariusze ochrony

Aby uzyskać listę obsługiwanych wersji programu SharePoint i wersji programu DPM wymaganych do wykonywania kopii zapasowych poszczególnych wersji programu SharePoint, zobacz artykuł [What can DPM back up?](/system-center/dpm/dpm-protection-matrix#applications-backup) (Kopie zapasowe wykonywane przez program DPM).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem pracy z farmą programu SharePoint na platformie Azure należy potwierdzić kilka rzeczy.

### <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że zostały spełnione wszystkie wymagania wstępne dotyczące używania Microsoft Azure Backup [do](backup-azure-dpm-introduction.md#prerequisites-and-limitations) ochrony obciążeń. Niektóre zadania dotyczące wymagań wstępnych obejmują tworzenie magazynu kopii zapasowych, pobieranie poświadczeń magazynu, instalowanie agenta Azure Backup i rejestrowanie programu DPM/Azure Backup Server w magazynie.

Dodatkowe wymagania wstępne i ograniczenia można znaleźć w artykule Back up SharePoint with DPM (Back up SharePoint with DPM (Back [up SharePoint with DPM ) (Back up SharePoint with DPM (Back up SharePoint with DPM) (Dodatkowe](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations) wymagania wstępne i ograniczenia można znaleźć w

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Aby utworzyć kopię zapasową farmy programu SharePoint, skonfiguruj ochronę programu SharePoint przy użyciu pliku ConfigureSharePoint.exe, a następnie utwórz grupę ochrony w programie DPM. Aby uzyskać instrukcje, [zobacz Konfigurowanie kopii zapasowej](/system-center/dpm/back-up-sharepoint#configure-backup) w dokumentacji programu DPM.

## <a name="monitoring"></a>Monitorowanie

Aby monitorować zadanie tworzenia kopii zapasowej, wykonaj instrukcje z [tematu Monitorowanie kopii zapasowej programu DPM](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>Przywracanie danych programu SharePoint

Aby dowiedzieć się, jak przywrócić element programu SharePoint z dysku za pomocą programu DPM, zobacz [Przywracanie danych programu SharePoint.](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywracanie bazy danych programu SharePoint z platformy Azure przy użyciu programu DPM

1. Aby odzyskać bazę danych zawartości programu SharePoint, przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej) i wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania programu SharePoint, aby wyświetlić dostępne informacje o wykazie programu SharePoint.

   > [!NOTE]
   > Farma programu SharePoint jest chroniona na potrzeby długoterminowego przechowywania na platformie Azure, dlatego na serwerze programu DPM nie są dostępne żadne informacje o wykazie (metadane). W związku z tym za każdym razem, gdy trzeba odzyskać bazę danych zawartości programu SharePoint w punkcie w czasie, należy ponownie skatalogować farmę programu SharePoint.
   >
   >
3. Wybierz **pozycję Re-catalog (Ponownie wyekstuj).**

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Zostanie **otwarte okno Stan recatalogu** chmury.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu katalogowania stan zmieni się na *Powodzenie.* Wybierz pozycję **Zamknij**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Wybierz obiekt programu SharePoint wyświetlany na karcie Odzyskiwanie programu **DPM,** aby uzyskać strukturę bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj.**

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Na tym etapie wykonaj kroki odzyskiwania opisane wcześniej w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="switching-the-front-end-web-server"></a>Przełączanie serwera Front-End sieci Web

Jeśli masz więcej niż jeden serwer frontony sieci Web i chcesz przełączyć serwer używany przez program DPM do ochrony farmy, postępuj zgodnie z instrukcjami w te Front-End [serwer sieci Web](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server).

## <a name="next-steps"></a>Następne kroki

* [Azure Backup Server i program DPM — często zadawane pytania](backup-azure-dpm-azure-server-faq.yml)
* [Rozwiązywanie problemów z programem System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
