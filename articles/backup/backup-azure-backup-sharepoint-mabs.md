---
title: Tworzenie kopii zapasowej farmy programu SharePoint na platformie Azure za pomocą usługi serwera usługi MAB
description: Użyj Azure Backup Server, aby utworzyć kopię zapasową i przywrócić dane programu SharePoint. Ten artykuł zawiera informacje dotyczące konfigurowania farmy programu SharePoint w taki sposób, aby wymagane dane mogły być przechowywane na platformie Azure. Chronione dane programu SharePoint można przywrócić z dysku lub z platformy Azure.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 441a896f2faa67a1380007ebb9474d7c311a4842
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673141"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>Tworzenie kopii zapasowej farmy programu SharePoint na platformie Azure za pomocą usługi serwera usługi MAB

Można utworzyć kopię zapasową farmy programu SharePoint w celu Microsoft Azure przy użyciu programu Microsoft Azure Backup Server (serwera usługi MAB) w taki sam sposób, jak w przypadku tworzenia kopii zapasowych innych źródeł danych. Azure Backup zapewnia elastyczność harmonogramu tworzenia kopii zapasowych w celu tworzenia codziennych, cotygodniowych, comiesięcznych i corocznych punktów kopii zapasowych oraz zapewnia opcje zasad przechowywania dla różnych punktów kopii zapasowych. Oferuje również możliwość przechowywania kopii dysków lokalnych w celu szybkiego zamierzania czasu odzyskiwania (RTO) oraz przechowywania kopii na platformie Azure w celu zapewnienia ekonomicznego i długoterminowego przechowywania.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint i powiązane z nimi scenariusze ochrony

Azure Backup programu DPM obsługuje następujące scenariusze:

| Obciążenie | Wersja | Wdrożenie programu SharePoint | Ochrona i odzyskiwanie |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3,0 |Program SharePoint wdrożony jako serwer fizyczny lub maszyna wirtualna z funkcją Hyper-V/VMware <br> -------------- <br> Program SQL AlwaysOn | Ochrona farmy programu SharePoint opcje odzyskiwania: Farma odzyskiwania, baza danych i plik lub element listy z punktów odzyskiwania dysku.  Odzyskiwanie farmy i bazy danych z punktów odzyskiwania platformy Azure. |

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed utworzeniem kopii zapasowej farmy programu SharePoint na platformie Azure istnieje kilka rzeczy, które należy potwierdzić.

### <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że [zainstalowano i przygotowano Azure Backup Server](backup-azure-microsoft-azure-backup.md) do ochrony obciążeń.

### <a name="protection-agent"></a>Agent ochrony

Agent Azure Backup musi być zainstalowany na serwerze, na którym działa program SharePoint, serwery z systemem SQL Server i wszystkie inne serwery, które są częścią farmy programu SharePoint. Więcej informacji o sposobie konfigurowania agenta ochrony znajduje się w temacie [Setup Protection Agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).  Jedynym wyjątkiem jest zainstalowanie agenta tylko na jednym serwerze frontonu sieci Web (WFE). Azure Backup Server potrzebuje agenta na jednym serwerze WFE tylko jako punktu wejścia do ochrony.

### <a name="sharepoint-farm"></a>Farma programu SharePoint

Dla każdego 10 000 000 elementów w farmie musi znajdować się co najmniej 2 GB miejsca na woluminie, na którym znajduje się folder serwera usługi MAB. Miejsce to jest wymagane na potrzeby generowania wykazów. Aby program serwera usługi MAB odzyskał określone elementy (zbiory witryn, witryny, listy, biblioteki dokumentów, foldery, pojedyncze dokumenty i elementy listy), generacja wykazu tworzy listę adresów URL zawartych w poszczególnych bazach danych zawartości. Listę adresów URL można wyświetlić w okienku element możliwy do odzyskania w obszarze zadania **odzyskiwania** serwera usługi MAB Konsola administratora.

### <a name="sql-server"></a>SQL Server

Azure Backup Server jest uruchamiany jako konto LocalSystem. Aby utworzyć kopię zapasową baz danych SQL Server, serwera usługi MAB musi mieć uprawnienia sysadmin na tym koncie dla serwera, na którym działa program SQL Server. Przed utworzeniem kopii zapasowej należy ustawić NT *NT\SYSTEM na serwerze, na którym* działa SQL Server.

Jeśli farma programu SharePoint ma SQL Server baz danych, które są skonfigurowane przy użyciu aliasów SQL Server, Zainstaluj składniki klienta SQL Server na serwerze frontonu sieci Web, który będzie chroniony przez serwera usługi MAB.

### <a name="sharepoint-server"></a>Oprogramowanie SharePoint Server

Chociaż wydajność jest zależna od wielu czynników, takich jak rozmiar farmy programu SharePoint, ponieważ ogólne wskazówki serwera usługi MAB mogą chronić witrynę programu SharePoint z 25 TB.

### <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane

* SERWERA usługi MAB chroniące farmę programu SharePoint nie chroni indeksów wyszukiwania lub baz danych usług aplikacji. Należy osobno skonfigurować ochronę tych baz danych.
* SERWERA usługi MAB nie zapewnia kopii zapasowych baz danych programu SharePoint SQL Server hostowanych w udziałach serwera plików skalowalnego w poziomie (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurowanie ochrony programu SharePoint

Przed użyciem serwera usługi MAB do ochrony programu SharePoint należy skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint (usługę składnika zapisywania usługi WSS) przy użyciu programu **ConfigureSharePoint. exe**.

**ConfigureSharePoint. exe** można znaleźć w folderze [serwera usługi MAB Installation path] \Bin na serwerze frontonu sieci Web. To narzędzie udostępnia agentowi ochrony poświadczenia farmy programu SharePoint. Jest on uruchamiany na jednym serwerze WFE. Jeśli masz wiele serwerów WFE, wybierz opcję tylko jeden podczas konfigurowania grupy ochrony.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Aby skonfigurować usługę składnika zapisywania usługi VSS programu SharePoint

1. Na serwerze WFE, w wierszu polecenia, przejdź do lokalizacji [serwera usługi MAB Installation Location] \Bin\
2. Wprowadź ConfigureSharePoint-EnableSharePointProtection.
3. Wprowadź poświadczenia administratora farmy. To konto musi należeć do lokalnej grupy administratorów na serwerze WFE. Jeśli administrator farmy nie jest administratorem lokalnym, przyznaj następujące uprawnienia na serwerze WFE:
   * Przyznaj grupie WSS_Admin_WPG pełną kontrolę do folderu programu DPM (% Program Files%\Microsoft Azure Backup\DPM).
   * Przyznaj grupie WSS_Admin_WPG dostęp do odczytu do klucza rejestru programu DPM (HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Po zmianie poświadczeń administratora farmy programu SharePoint należy ponownie uruchomić program ConfigureSharePoint. exe.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Tworzenie kopii zapasowej farmy programu SharePoint za pomocą serwera usługi MAB

Po skonfigurowaniu serwera usługi MAB i farmy programu SharePoint jako wyjaśnionej wcześniej program SharePoint może być chroniony przez serwera usługi MAB.

### <a name="to-protect-a-sharepoint-farm"></a>Aby chronić farmę programu SharePoint

1. Na karcie **ochrona** Konsola administratora serwera usługi MAB kliknij pozycję **Nowy**.
    ![Karta Nowa ochrona](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na stronie **Wybierz typ grupy ochrony** w kreatorze **tworzenia nowej grupy ochrony** wybierz pozycję **serwery**, a następnie kliknij przycisk **dalej**.

    ![Wybierz typ grupy ochrony](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na ekranie **Wybieranie członków grupy** zaznacz pole wyboru dla serwera programu SharePoint, który ma być chroniony, a następnie kliknij przycisk **dalej**.

    ![Wybierz członków grupy](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Po zainstalowaniu agenta ochrony można zobaczyć serwer w kreatorze. SERWERA usługi MAB również pokazuje swoją strukturę. Ponieważ uruchomiono ConfigureSharePoint. exe, serwera usługi MAB komunikuje się z usługą składnika zapisywania usługi VSS programu SharePoint i odpowiadającymi jej SQL Server bazami danych i rozpoznaje strukturę farmy programu SharePoint, skojarzone bazy danych zawartości i wszelkie odpowiadające im elementy.
   >
   >
4. Na stronie **Wybierz metodę ochrony danych** wprowadź nazwę **grupy ochrony**i wybierz preferowane *metody ochrony*. Kliknij przycisk **Dalej**.

    ![Wybieranie metody ochrony danych](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Metoda ochrony dysku pomaga spełnić krótkie cele czasu odzyskiwania.
   >
   >
5. Na stronie **Określ cele krótkoterminowe** wybierz swój preferowany **Zakres przechowywania** i określ, kiedy mają być wykonywane kopie zapasowe.

    ![Określ cele krótkoterminowe](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Ponieważ odzyskiwanie jest najczęściej wymagane w przypadku danych, które są starsze niż pięć dni, w tym przykładzie wybrano zakres przechowywania równy pięć dni i upewnił się, że kopia zapasowa występuje w godzinach nieprodukcyjnych.
   >
   >
6. Przejrzyj miejsce na dysku w puli magazynów przydzieloną dla grupy ochrony, a następnie kliknij przycisk **dalej**.
7. Dla każdej grupy ochrony serwera usługi MAB przydziela miejsce na dysku do przechowywania replik i zarządzania nimi. W tym momencie serwera usługi MAB musi utworzyć kopię wybranych danych. Wybierz, jak i Kiedy chcesz utworzyć replikę, a następnie kliknij przycisk **dalej**.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Aby upewnić się, że ruch sieciowy nie jest zastosowany, wybierz czas poza godzinami produkcji.
   >
   >
8. SERWERA usługi MAB zapewnia integralność danych przez wykonywanie kontroli spójności repliki. Dostępne są dwie opcje. Można zdefiniować harmonogram uruchamiania kontroli spójności lub uruchomić testy spójności automatycznie w replice, gdy stanie się niespójna. Wybierz preferowaną opcję, a następnie kliknij przycisk **dalej**.

    ![Sprawdzanie spójności](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na stronie **Określ dane ochrony w trybie online** wybierz farmę programu SharePoint, którą chcesz chronić, a następnie kliknij przycisk **dalej**.

    ![Protection1 SharePoint programu DPM](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych online** wybierz preferowany harmonogram, a następnie kliknij przycisk **dalej**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > Program serwera usługi MAB zapewnia maksymalnie dwa codzienne kopie zapasowe na platformie Azure, które są dostępne dla najnowszego punktu kopii zapasowej dysku. Azure Backup może również kontrolować ilość przepustowości sieci WAN, która może być używana w przypadku kopii zapasowych w godzinach szczytu i poza szczytem przy użyciu funkcji [ograniczania przepustowości sieci Azure Backup](backup-windows-with-mars-agent.md#enable-network-throttling).
    >
    >
11. W zależności od wybranego harmonogramu tworzenia kopii zapasowych na stronie **Określanie zasad przechowywania danych online** wybierz zasady przechowywania codziennie, co tydzień, co miesiąc i roczne punkty kopii zapasowej.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > SERWERA usługi MAB używa schematu przechowywania z dziadkiem-ojciec-syn, w którym można wybrać różne zasady przechowywania dla różnych punktów kopii zapasowych.
    >
    >
12. Podobnie jak w przypadku dysku, należy utworzyć początkową replikę punktu odniesienia na platformie Azure. Wybierz preferowaną opcję, aby utworzyć początkową kopię zapasową na platformie Azure, a następnie kliknij przycisk **dalej**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Przejrzyj wybrane ustawienia na stronie **Podsumowanie** , a następnie kliknij przycisk **Utwórz grupę**. Po utworzeniu grupy ochrony zostanie wyświetlony komunikat o powodzeniu.

    ![Podsumowanie](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Przywracanie elementu programu SharePoint z dysku przy użyciu serwera usługi MAB

W poniższym przykładzie *element odzyskiwania programu SharePoint* został przypadkowo usunięty i należy go odzyskać.
![SERWERA usługi MAB SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otwórz **Konsola administratora programu DPM**. Wszystkie farmy programu SharePoint, które są chronione przez program DPM, są wyświetlane na karcie **Ochrona** .

    ![SERWERA usługi MAB SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Aby rozpocząć odzyskiwanie elementu, wybierz kartę **odzyskiwanie** .

    ![SERWERA usługi MAB SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Możesz wyszukać program SharePoint pod kątem *odzyskiwania elementu programu SharePoint* , używając wyszukiwania opartego na symbolu wieloznacznego w zakresie punktów odzyskiwania.

    ![SERWERA usługi MAB SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wybierz odpowiedni punkt odzyskiwania z wyników wyszukiwania, kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj**.
5. Można także przeglądać różne punkty odzyskiwania i wybierać bazę danych lub element do odzyskania. Wybierz **datę > czas odzyskiwania**, a następnie wybierz prawidłową **bazę danych > farmy programu SharePoint > > elementu punktu odzyskiwania**.

    ![SERWERA usługi MAB SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj** , aby otworzyć **Kreatora odzyskiwania**. Kliknij przycisk **Dalej**.

    ![Przegląd wyboru odzyskiwania](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Wybierz typ odzyskiwania, który chcesz wykonać, a następnie kliknij przycisk **dalej**.

    ![Typ odzyskiwania](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Wybór opcji **Odzyskaj do oryginału** w przykładzie odzyskuje element do oryginalnej witryny programu SharePoint.
   >
   >
8. Wybierz **proces odzyskiwania** , którego chcesz użyć.

   * Wybierz opcję **Odzyskaj bez używania farmy odzyskiwania** , jeśli farma programu SharePoint nie uległa zmianie i jest taka sama jak przywracany punkt odzyskiwania.
   * Wybierz opcję **Odzyskaj przy użyciu farmy odzyskiwania** , jeśli farma programu SharePoint została zmieniona od czasu utworzenia punktu odzyskiwania.

     ![Proces odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Podaj lokalizację wystąpienia SQL Server przemieszczania w celu tymczasowej odzyskania bazy danych, a następnie Udostępnij udział plików przemieszczania na serwera usługi MAB oraz na serwerze, na którym działa program SharePoint w celu odzyskania elementu.

    ![Location1 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location1.png)

    SERWERA usługi MAB dołącza bazę danych zawartości, która hostuje element programu SharePoint do wystąpienia tymczasowego SQL Server. Z bazy danych zawartości odzyskuje element i umieszcza go w lokalizacji pliku przemieszczania w systemie serwera usługi MAB. Odzyskany element, który znajduje się w lokalizacji tymczasowej, musi zostać wyeksportowany do lokalizacji tymczasowej w farmie programu SharePoint.

    ![Location2 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wybierz opcję **Określ opcje odzyskiwania**i Zastosuj ustawienia zabezpieczeń do farmy programu SharePoint lub Zastosuj ustawienia zabezpieczeń punktu odzyskiwania. Kliknij przycisk **Dalej**.

    ![Opcje odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Możesz wybrać opcję ograniczenia użycia przepustowości sieci. Pozwala to zminimalizować wpływ na serwer produkcyjny w godzinach produkcyjnych.
    >
    >
11. Przejrzyj informacje podsumowujące, a następnie kliknij przycisk **Odzyskaj** , aby rozpocząć odzyskiwanie pliku.

    ![Podsumowanie odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teraz wybierz kartę **monitorowanie** w **Konsola administratora serwera usługi MAB** , aby wyświetlić **stan** odzyskiwania.

    ![Stan odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Plik zostanie przywrócony. Można odświeżyć witrynę programu SharePoint, aby sprawdzić przywrócony plik.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Przywracanie bazy danych programu SharePoint z platformy Azure przy użyciu programu DPM

1. Aby odzyskać bazę danych zawartości programu SharePoint, Przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej) i wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![SERWERA usługi MAB SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania programu SharePoint, aby wyświetlić dostępne informacje dotyczące katalogu programu SharePoint.

   > [!NOTE]
   > Ponieważ farma programu SharePoint jest chroniona do długoterminowego przechowywania na platformie Azure, żadne informacje o katalogu (metadane) nie są dostępne w witrynie serwera usługi MAB. W związku z tym zawsze, gdy baza danych zawartości programu SharePoint do punktu w czasie musi zostać odzyskana, należy ponownie wykazać farmy programu SharePoint.
   >
   >
3. Kliknij pozycję **ponownie katalog**.

    ![SERWERA usługi MAB SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Zostanie otwarte okno stanu usługi **Cloud Catalog** .

    ![SERWERA usługi MAB SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu wykazania stan zmieni się na *powodzenie*. Kliknij przycisk **Zamknij**.

    ![SERWERA usługi MAB SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknij obiekt programu SharePoint wyświetlany na karcie **odzyskiwanie** serwera usługi MAB, aby uzyskać strukturę bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następnie kliknij polecenie **Odzyskaj**.

    ![SERWERA usługi MAB SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonaj kroki odzyskiwania opisane wcześniej w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [Tworzenie kopii zapasowej programu Exchange Server](backup-azure-exchange-mabs.md) .
Zapoznaj się z artykułem [Tworzenie kopii zapasowej SQL Server](backup-azure-sql-mabs.md) .
