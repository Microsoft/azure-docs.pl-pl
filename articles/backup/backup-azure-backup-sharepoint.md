---
title: Tworzenie kopii zapasowej farmy programu SharePoint na platformie Azure przy użyciu programu DPM
description: Ten artykuł zawiera omówienie ochrony programu DPM/Azure Backup serwera w farmie programu SharePoint na platformie Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: b326ce15cd605f4f768a03b95337bbc5bd6aaabc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032888"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Tworzenie kopii zapasowej farmy programu SharePoint na platformie Azure przy użyciu programu DPM

Można utworzyć kopię zapasową farmy programu SharePoint w celu Microsoft Azure przy użyciu programu System Center Data Protection Manager (DPM) w taki sam sposób, jak w przypadku tworzenia kopii zapasowych innych źródeł danych. Azure Backup zapewnia elastyczność harmonogramu tworzenia kopii zapasowych w celu tworzenia codziennych, cotygodniowych, comiesięcznych i corocznych punktów kopii zapasowych oraz zapewnia opcje zasad przechowywania dla różnych punktów kopii zapasowych. Program DPM oferuje możliwość przechowywania kopii dysków lokalnych w celu uzyskania krótkich celów związanych z odzyskiwaniem (RTO) oraz przechowywania kopii na platformie Azure w celu zapewnienia ekonomicznego i długoterminowego przechowywania.

Tworzenie kopii zapasowej programu SharePoint na platformie Azure przy użyciu programu DPM to bardzo podobny proces tworzenia kopii zapasowej programu SharePoint w programie DPM lokalnie. Szczególne zagadnienia dotyczące platformy Azure zostaną wymienione w tym artykule.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint i powiązane z nimi scenariusze ochrony

Aby uzyskać listę obsługiwanych wersji programu SharePoint i wersji programu DPM wymaganych do wykonywania kopii zapasowych poszczególnych wersji programu SharePoint, zobacz artykuł [What can DPM back up?](/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup) (Kopie zapasowe wykonywane przez program DPM).

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed utworzeniem kopii zapasowej farmy programu SharePoint na platformie Azure istnieje kilka rzeczy, które należy potwierdzić.

### <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że zostały spełnione wszystkie [wymagania wstępne dotyczące używania Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) w celu ochrony obciążeń. Niektóre zadania związane z wymaganiami wstępnymi obejmują: Tworzenie magazynu kopii zapasowych, pobieranie poświadczeń magazynu, instalowanie Azure Backup agenta i rejestrowanie programu DPM/Azure Backup Server z magazynem.

Dodatkowe wymagania wstępne i ograniczenia można znaleźć w artykule [Tworzenie kopii zapasowej programu SharePoint przy użyciu programu DPM](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations) .

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Aby utworzyć kopię zapasową farmy programu SharePoint, skonfiguruj ochronę programu SharePoint przy użyciu pliku ConfigureSharePoint.exe, a następnie utwórz grupę ochrony w programie DPM. Aby uzyskać instrukcje, zobacz [Konfigurowanie kopii zapasowej](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) w dokumentacji programu DPM.

## <a name="monitoring"></a>Monitorowanie

Aby monitorować zadanie tworzenia kopii zapasowej, postępuj zgodnie z instrukcjami w temacie [monitorowanie kopii zapasowej programu DPM](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring) .

## <a name="restore-sharepoint-data"></a>Przywracanie danych programu SharePoint

Aby dowiedzieć się, jak przywrócić element programu SharePoint z dysku za pomocą programu DPM, zobacz [przywracanie danych programu SharePoint](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywracanie bazy danych programu SharePoint z platformy Azure przy użyciu programu DPM

1. Aby odzyskać bazę danych zawartości programu SharePoint, Przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej) i wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![Protection8 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania programu SharePoint, aby wyświetlić dostępne informacje dotyczące katalogu programu SharePoint.

   > [!NOTE]
   > Ponieważ farma programu SharePoint jest chroniona do długoterminowego przechowywania na platformie Azure, na serwerze DPM nie są dostępne żadne informacje o katalogu (metadane). W związku z tym zawsze, gdy baza danych zawartości programu SharePoint do punktu w czasie musi zostać odzyskana, należy ponownie wykazać farmy programu SharePoint.
   >
   >
3. Kliknij pozycję **ponownie katalog**.

    ![Protection10 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Zostanie otwarte okno stanu usługi **Cloud Catalog** .

    ![Protection11 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu wykazania stan zmieni się na *powodzenie*. Kliknij przycisk **Zamknij**.

    ![Protection12 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknij obiekt programu SharePoint wyświetlony na karcie **odzyskiwanie** programu DPM, aby uzyskać strukturę bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następnie kliknij polecenie **Odzyskaj**.

    ![Protection13 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonaj kroki odzyskiwania opisane wcześniej w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="switching-the-front-end-web-server"></a>Przełączanie serwera frontonu sieci Web

Jeśli masz więcej niż jeden serwer frontonu sieci Web i chcesz przełączyć serwer, którego program DPM używa do ochrony farmy, postępuj zgodnie z instrukcjami w temacie [przełączanie serwera frontonu sieci Web](/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server).

## <a name="next-steps"></a>Następne kroki

* [Azure Backup Server i DPM — często zadawane pytania](backup-azure-dpm-azure-server-faq.md)
* [Rozwiązywanie problemów z programem System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
