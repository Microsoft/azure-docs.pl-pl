---
title: Tworzenie kopii zapasowej farmy programu SharePoint na platformie Azure za pomocą usługi serwera usługi MAB
description: Użyj Azure Backup Server, aby utworzyć kopię zapasową i przywrócić dane programu SharePoint. Ten artykuł zawiera informacje dotyczące konfigurowania farmy programu SharePoint w taki sposób, aby wymagane dane mogły być przechowywane na platformie Azure. Chronione dane programu SharePoint można przywrócić z dysku lub z platformy Azure.
ms.topic: conceptual
ms.date: 04/26/2020
ms.openlocfilehash: 837aabf739431eebaa6406770620329fe6345eb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89375401"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>Tworzenie kopii zapasowej farmy programu SharePoint na platformie Azure za pomocą usługi serwera usługi MAB

Można utworzyć kopię zapasową farmy programu SharePoint w celu Microsoft Azure przy użyciu programu Microsoft Azure Backup Server (serwera usługi MAB) w taki sam sposób, jak w przypadku tworzenia kopii zapasowych innych źródeł danych. Azure Backup zapewnia elastyczność harmonogramu tworzenia kopii zapasowych w celu tworzenia codziennych, cotygodniowych, comiesięcznych i corocznych punktów kopii zapasowych oraz zapewnia opcje zasad przechowywania dla różnych punktów kopii zapasowych. Usługa serwera usługi MAB oferuje możliwość przechowywania kopii dysków lokalnych w celu uzyskania szybkich celów odzyskiwania (RTO) oraz do przechowywania kopii na platformie Azure w celu zapewnienia ekonomicznego i długoterminowego przechowywania.

Tworzenie kopii zapasowej programu SharePoint na platformie Azure za pomocą serwera usługi MAB jest podobnym procesem tworzenia kopii zapasowej programu SharePoint w programie DPM (Data Protection Manager) lokalnie. Szczególne zagadnienia dotyczące platformy Azure zostaną wymienione w tym artykule.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Obsługiwane wersje programu SharePoint i powiązane z nimi scenariusze ochrony

Aby uzyskać listę obsługiwanych wersji programu SharePoint i wersji serwera usługi MAB wymaganych do tworzenia ich kopii zapasowych [, zobacz macierz ochrony serwera usługi MAB](./backup-mabs-protection-matrix.md)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed utworzeniem kopii zapasowej farmy programu SharePoint na platformie Azure istnieje kilka rzeczy, które należy potwierdzić.

### <a name="whats-not-supported"></a>Jakie operacje nie są obsługiwane

* SERWERA usługi MAB chroniące farmy programu SharePoint nie chroni indeksów wyszukiwania ani baz danych usług aplikacji. Należy osobno skonfigurować ochronę tych baz danych.

* SERWERA usługi MAB nie zapewnia kopii zapasowych baz danych programu SharePoint SQL Server hostowanych w udziałach serwera plików skalowalnego w poziomie (SOFS).

### <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że zostały spełnione wszystkie [wymagania wstępne dotyczące używania Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) do ochrony obciążeń. Niektóre zadania związane z wymaganiami wstępnymi obejmują: Tworzenie magazynu kopii zapasowych, pobieranie poświadczeń magazynu, instalowanie Azure Backup agenta i rejestrowanie Azure Backup Server z magazynem.

Dodatkowe wymagania wstępne i ograniczenia:

* Domyślnie w przypadku ochrony programu SharePoint będą chronione wszystkie bazy danych zawartości (oraz bazy danych SharePoint_Config i SharePoint_AdminContent *). Jeśli chcesz dodać dostosowania, takie jak indeksy wyszukiwania, usługa profilu użytkownika, szablony lub bazy danych usługi aplikacji, musisz oddzielnie skonfigurować je pod kątem ochrony. Upewnij się, że ochrona została włączona dla wszystkich folderów zawierających te typy funkcji lub pliki dostosowań.

* Nie można włączyć ochrony baz danych programu SharePoint jako źródła danych programu SQL Server. Z kopii zapasowej farmy można odzyskiwać pojedyncze bazy danych.

* Należy pamiętać, że serwera usługi MAB jest uruchamiany jako **System lokalny**i aby utworzyć kopię zapasową baz danych SQL Server, potrzebne są uprawnienia sysadmin dla tego konta dla programu SQL Server. Na SQL Server chcesz utworzyć kopię zapasową, ustaw NT NT\SYSTEM na **sysadmin**.

* Dla każdego 10 000 000 elementów w farmie musi znajdować się co najmniej 2 GB miejsca na woluminie, na którym znajduje się folder serwera usługi MAB. Miejsce to jest wymagane na potrzeby generowania wykazów. Aby umożliwić użycie serwera usługi MAB do wykonania określonego odzyskania elementów (zbiory witryn, witryny, listy, biblioteki dokumentów, foldery, pojedyncze dokumenty i elementy listy), generacja wykazu tworzy listę adresów URL zawartych w każdej bazie danych zawartości. Listę adresów URL można wyświetlić w okienku element możliwy do odzyskania w obszarze zadań odzyskiwanie w konsola administratora serwera usługi MAB.

* W farmie programu SharePoint, jeśli masz SQL Server baz danych, które są skonfigurowane przy użyciu aliasów SQL Server, Zainstaluj składniki klienta SQL Server na serwerze frontonu sieci Web, który będzie chroniony przez serwera usługi MAB.

* Ochrona elementów sklepu z aplikacjami nie jest obsługiwana w programie SharePoint 2013.

* SERWERA usługi MAB nie obsługuje ochrony zdalnego strumienia FILESTREAM. Katalog FILESTREAM powinien być częścią bazy danych.

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Aby utworzyć kopię zapasową farmy programu SharePoint, skonfiguruj ochronę programu SharePoint przy użyciu ConfigureSharePoint.exe a następnie utwórz grupę ochrony w serwera usługi MAB.

1. **Uruchom narzędzie ConfigureSharePoint.exe** — służy ono do konfigurowania usługi składnika zapisywania usługi VSS dla programu SharePoint \(WSS\) i udostępnia agentowi ochrony poświadczenia dla farmy programu SharePoint. Po wdrożeniu agenta ochrony plik ConfigureSharePoint.exe można znaleźć w `<MABS Installation Path\>\bin` folderze na serwerze frontonu \- sieci Web.  Jeśli masz wiele serwerów WFE, musisz zainstalować je tylko na jednym z nich. Postępuj w następujący sposób:

    * Na serwerze WFE, w wierszu polecenia przejdź do `\<MABS installation location\>\\bin\\` i uruchom `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` , gdzie:

        * **EnableSharePointProtection** umożliwia ochronę farmy programu SharePoint, włącza składnik zapisywania usługi VSS i rejestruje tożsamość aplikacji DCOM WssCmdletsWrapper do uruchamiania jako użytkownik, którego poświadczenia wprowadzono przy użyciu tej opcji. To konto powinno należeć do grupy administratorów farmy oraz administratorów lokalnych serwera frontonu sieci Web.

        * **EnableSPSearchProtection** włącza ochronę usługi WSS 3.0 SP Search przy użyciu klucza rejestru SharePointSearchEnumerationEnabled w gałęzi HKLM\\Software\\Microsoft\\Microsoft Data Protection Manager\\Agent\\2.0\\ z przodu\- na serwerze frontonu sieci Web i rejestruje tożsamość aplikacji DCOM WssCmdletsWrapper w celu uruchomienia jej jako użytkownik, którego poświadczenia wprowadzono przy użyciu tej opcji. To konto powinno należeć do grupy administratorów farmy oraz administratorów lokalnych serwera frontonu sieci Web.

        * **ResolveAllSQLAliases** wyświetla wszystkie aliasy raportowane przez składnik zapisywania usługi VSS programu SharePoint i rozpoznaje je jako odpowiednie serwery programu SQL Server. Wyświetla również rozpoznane nazwy wystąpień. Serwery zdublowane również zostaną wyświetlone. Raportuje wszystkie aliasy, które nie są rozpoznawane do SQL Server.

        * **SetTempPath** ustawia zmienne środowiskowe TEMP i TMP na określoną ścieżkę. Odzyskiwanie na poziomie elementu kończy się niepowodzeniem, jeśli jest odzyskiwana duża kolekcja witryn, witryna, lista lub element, a w folderze tymczasowym administratora farmy jest za mało miejsca. Ta opcja pozwala zmienić ścieżkę folderu plików tymczasowych na wolumin, który ma wystarczająco dużo miejsca, aby zmieścić odzyskiwany zbiór witryn lub witrynę.

    * Wprowadź poświadczenia administratora farmy. To konto musi należeć do lokalnej grupy administratorów na serwerze WFE. Jeśli administrator farmy nie jest administratorem lokalnym, przyznaj następujące uprawnienia na serwerze WFE:

        * Przyznaj grupie **WSS_Admin_WPG** pełną kontrolę do folderu serwera usługi MAB ( `%Program Files%\Data Protection Manager\DPM\` ).

        * Przyznaj grupie **WSS_Admin_WPG** dostęp do odczytu do klucza rejestru serwera usługi MAB ( `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager` ).

        Po uruchomieniu ConfigureSharePoint.exe należy uruchomić go ponownie w przypadku zmiany poświadczeń administratora farmy programu SharePoint.

1. Aby utworzyć grupę ochrony, wybierz pozycję **Protection**  >  **Akcje**ochrony  >  **Utwórz grupę ochrony** , aby otworzyć kreatora **tworzenia nowej grupy ochrony** w konsoli serwera usługi MAB.

1. W obszarze **Wybierz typ grupy ochrony** wybierz pozycję **Serwery**.

1. W obszarze **Wybierz członków grupy**rozwiń serwer, który zawiera rolę WFE. Jeśli istnieje więcej niż jeden serwer WFE, wybierz ten, na którym zainstalowano ConfigureSharePoint.exe.

    Po rozszerzeniu programu SharePoint Server serwera usługi MAB zapytania usługi VSS, aby zobaczyć, jakie dane mogą być chronione przez serwera usługi MAB.  Jeśli baza danych programu SharePoint jest zdalna, serwera usługi MAB łączy się z nią. Jeśli źródła danych programu SharePoint nie są wyświetlane, sprawdź, czy składnik zapisywania usługi VSS jest uruchomiony na serwerze programu SharePoint i wszystkich SQL Server zdalnych, a następnie upewnij się, że agent serwera usługi MAB jest zainstalowany na serwerze programu SharePoint i SQL Server zdalnym. Ponadto upewnij się, że bazy danych programu SharePoint nie są chronione w innym miejscu jako SQL Server bazy danych.

1. W obszarze **Wybierz metodę ochrony danych**Określ, jak mają być obsługiwane krótkoterminowe i długoterminowe \- kopie zapasowe. Krótkoterminowe \- Tworzenie kopii zapasowych zawsze odbywa się na dysku, z opcją tworzenia kopii zapasowych na dysku w chmurze platformy Azure z Azure Backupm \( krótkim lub długim \- okresem \) .

1. W obszarze **Wybierz \- cele krótkoterminowe**Określ sposób tworzenia kopii zapasowej do krótkoterminowego \- przechowywania na dysku.   W obszarze **Zakres przechowywania** Określ, jak długo chcesz przechowywać dane na dysku. W obszarze **częstotliwość synchronizacji**Określ, jak często mają być uruchamiane przyrostowe kopie zapasowe na dysku. Jeśli nie chcesz ustawiać interwału kopii zapasowych, możesz sprawdzić tuż przed punktem odzyskiwania, aby program serwera usługi MAB uruchomił ekspresową pełną kopię zapasową tuż przed zaplanowaniem każdego punktu odzyskiwania.

1. Na stronie Przejrzyj przydział dysku Przejrzyj miejsce na dysku w puli magazynów przydzielone danej grupie ochrony.

    **Łączny rozmiar danych** to rozmiar danych, których kopia zapasowa ma zostać utworzona, oraz miejsce na dysku, które ma **zostać zainicjowane w systemie serwera usługi MAB** , to miejsce, które serwera usługi MAB zaleca dla grupy ochrony. SERWERA usługi MAB wybiera idealny wolumin kopii zapasowej na podstawie ustawień. Opcje wielkości kopii zapasowej można jednak edytować w obszarze **Szczegóły przydziału dysku**. W przypadku obciążeń wybierz z menu rozwijanego preferowany magazyn. Edycja zmienia wartości pozycji **Całkowita ilość miejsca dla magazynu** i **Wolne miejsce w magazynie** w okienku **Dostępny magazyn dyskowy**. Zajęte miejsce to ilość serwera usługi MAB magazynu sugerująca dodanie do woluminu, co pozwala na płynne wykonywanie kopii zapasowych w przyszłości.

1. W obszarze **Wybierz metodę tworzenia repliki**wybierz, jak chcesz obsługiwać początkową pełną replikację danych.  W przypadku wybrania replikacji przez sieć zalecamy wybranie czasu poza godzinami szczytu. W przypadku dużych ilości danych lub nieoptymalnych warunków sieciowych należy wziąć pod uwagę replikację danych w trybie offline za pomocą nośników wymiennych.

1. W polu **Wybierz opcje sprawdzania spójności** wybierz sposób automatyzacji sprawdzania spójności. Można włączyć uruchamianie sprawdzania tylko wtedy, gdy dane są niespójne, lub zgodnie z harmonogramem. Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, w dowolnym momencie możesz uruchomić sprawdzanie ręczne, klikając prawym przyciskiem myszy grupę ochrony w obszarze **Ochrona** w konsoli programu serwera usługi MAB, a następnie wybierając polecenie **Przeprowadź sprawdzanie spójności**.

1. Jeśli wybrano opcję tworzenia kopii zapasowych w chmurze przy użyciu usługi Azure Backup, sprawdź, czy na stronie **Określanie danych chronionych w trybie online** zaznaczono obciążenia, dla których ma być tworzona kopia zapasowa na platformie Azure.

1. W obszarze **Określ harmonogram kopii zapasowych online**Określ, jak często mają być wykonywane przyrostowe kopie zapasowe na platformie Azure. Wykonywanie kopii zapasowych można zaplanować na każdy dzień/tydzień/miesiąc/rok, natomiast godziny/daty pozwalają ustalić konkretny czas uruchomienia wykonywania kopii zapasowych. Kopie zapasowe mogą być tworzone maksymalnie dwa razy dziennie. Za każdym razem, gdy wykonywana jest kopia zapasowa, punkt odzyskiwania danych jest tworzony na platformie Azure na podstawie kopii kopii zapasowej danych przechowywanych na dysku serwera usługi MAB.

1. W obszarze **Określ zasady przechowywania danych online**możesz określić sposób zachowywania punktów odzyskiwania tworzonych na podstawie kopii zapasowych na dzień/tydzień/miesiąc/rok.

1. W obszarze **Wybierz replikację online**Określ, jak będzie wykonywana początkowa pełna replikacja danych. Możesz wykonywać replikację za pośrednictwem sieci lub tworzyć kopie zapasowe offline (rozmieszczanie offline). W przypadku kopii zapasowych offline używana jest funkcja Azure Import. [Przeczytaj więcej](./backup-azure-backup-import-export.md).

1. Na stronie **Podsumowanie** przejrzyj ustawienia. Po wybraniu opcji **Utwórz grupę**następuje Replikacja początkowa danych. Po zakończeniu stan grupy ochrony zostanie wyświetlony na stronie **stan** jako **OK** . Następnie odbywa się tworzenie kopii zapasowych zgodnie z ustawieniami grupy ochrony.

## <a name="monitoring"></a>Monitorowanie

Po utworzeniu grupy ochrony następuje Replikacja początkowa i serwera usługi MAB rozpoczyna wykonywanie kopii zapasowych i synchronizowanie danych programu SharePoint. SERWERA usługi MAB monitoruje początkową synchronizację i kolejne kopie zapasowe.  Możesz monitorować dane programu SharePoint na kilka sposobów:

* Za pomocą domyślnego monitorowania serwera usługi MAB można skonfigurować powiadomienia na potrzeby aktywnego monitorowania przez publikowanie alertów i Konfigurowanie powiadomień. Powiadomienia mogą być przesyłane pocztą e-mail w przypadku alertów krytycznych, ostrzeżeń lub informacji oraz w przypadku informacji o stanie wystąpień procesu odzyskiwania.

* Jeśli używasz Operations Manager, możesz centralnie publikować alerty.

### <a name="set-up-monitoring-notifications"></a>Konfigurowanie powiadomień dotyczących monitorowania

1. W Konsola administratora serwera usługi MAB wybierz pozycję **monitorowanie**  >  **Action**  >  **Opcje**akcji.

2. Wybierz pozycję **serwer SMTP**, wpisz nazwę serwera, port i adres e-mail, z którego będą wysyłane powiadomienia. Adres musi być prawidłowy.

3. W obszarze **uwierzytelniony serwer SMTP**wpisz nazwę użytkownika i hasło. Nazwa użytkownika i hasło muszą być nazwą konta domeny osoby, której adres "od" został opisany w poprzednim kroku. W przeciwnym razie dostarczanie powiadomień nie powiedzie się.

4. Aby przetestować ustawienia serwera SMTP, wybierz pozycję **Wyślij testową wiadomość e-mail**, wpisz adres e-mail, na który chcesz wysłać wiadomość testową, a następnie wybierz przycisk **OK**. Wybierz pozycję **Opcje**  >  **powiadomienia** , a następnie wybierz typy alertów, o których odbiorcy chcą być powiadamiani. W polu **odbiorcy** wpisz adres e-mail każdego odbiorcy, do którego chcesz serwera usługi MAB wysyłać kopie powiadomień.

### <a name="publish-operations-manager-alerts"></a>Publikowanie alertów dotyczących programu Operations Manager

1. W Konsola administratora serwera usługi MAB wybierz pozycję **monitorowanie**  >  **Action**  >  **Opcje**akcji  >  **Publikowanie alertów**  >  **Publikuj aktywne alerty**

2. Po włączeniu **publikowania alertów**wszystkie istniejące alerty serwera usługi MAB, które mogą wymagać akcji użytkownika, są publikowane w dzienniku zdarzeń **alertów serwera usługi MAB** . Agent Operations Manager zainstalowany na serwerze serwera usługi MAB następnie publikuje te alerty w Operations Manager i aktualizuje konsolę w miarę generowania nowych alertów.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Przywracanie elementu programu SharePoint z dysku przy użyciu serwera usługi MAB

W poniższym przykładzie *element odzyskiwania programu SharePoint* został przypadkowo usunięty i należy go odzyskać.
![SERWERA usługi MAB SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otwórz **Konsola administratora serwera usługi MAB**. Wszystkie farmy programu SharePoint, które są chronione przez serwera usługi MAB, są wyświetlane na karcie **Ochrona** .

    ![SERWERA usługi MAB SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Aby rozpocząć odzyskiwanie elementu, wybierz kartę **odzyskiwanie** .

    ![SERWERA usługi MAB SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Możesz wyszukać program SharePoint pod kątem *odzyskiwania elementu programu SharePoint* , używając wyszukiwania opartego na symbolu wieloznacznego w zakresie punktów odzyskiwania.

    ![SERWERA usługi MAB SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Wybierz odpowiedni punkt odzyskiwania z wyników wyszukiwania, kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj**.
5. Można także przeglądać różne punkty odzyskiwania i wybierać bazę danych lub element do odzyskania. Wybierz **datę > czas odzyskiwania**, a następnie wybierz prawidłową **bazę danych > farmy programu SharePoint > > elementu punktu odzyskiwania**.

    ![SERWERA usługi MAB SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj** , aby otworzyć **Kreatora odzyskiwania**. Wybierz opcję **Dalej**.

    ![Przegląd wyboru odzyskiwania](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Wybierz typ odzyskiwania, który chcesz wykonać, a następnie wybierz przycisk **dalej**.

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

    SERWERA usługi MAB dołącza bazę danych zawartości, która hostuje element programu SharePoint, do wystąpienia tymczasowego SQL Server. Z bazy danych zawartości odzyskuje element i umieszcza go w lokalizacji pliku przemieszczania w systemie serwera usługi MAB. Odzyskany element, który znajduje się w lokalizacji tymczasowej, musi zostać wyeksportowany do lokalizacji tymczasowej w farmie programu SharePoint.

    ![Location2 przemieszczania](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Wybierz opcję **Określ opcje odzyskiwania**i Zastosuj ustawienia zabezpieczeń do farmy programu SharePoint lub Zastosuj ustawienia zabezpieczeń punktu odzyskiwania. Wybierz opcję **Dalej**.

    ![Opcje odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Możesz wybrać opcję ograniczenia użycia przepustowości sieci. Pozwala to zminimalizować wpływ na serwer produkcyjny w godzinach produkcyjnych.
    >
    >
11. Przejrzyj informacje podsumowujące, a następnie wybierz pozycję **Odzyskaj** , aby rozpocząć odzyskiwanie pliku.

    ![Podsumowanie odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Teraz wybierz kartę **monitorowanie** w **Konsola administratora serwera usługi MAB** , aby wyświetlić **stan** odzyskiwania.

    ![Stan odzyskiwania](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Plik zostanie przywrócony. Można odświeżyć witrynę programu SharePoint, aby sprawdzić przywrócony plik.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>Przywracanie bazy danych programu SharePoint z platformy Azure przy użyciu serwera usługi MAB

1. Aby odzyskać bazę danych zawartości programu SharePoint, Przejrzyj różne punkty odzyskiwania (jak pokazano wcześniej) i wybierz punkt odzyskiwania, który chcesz przywrócić.

    ![SERWERA usługi MAB SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Kliknij dwukrotnie punkt odzyskiwania programu SharePoint, aby wyświetlić dostępne informacje dotyczące katalogu programu SharePoint.

   > [!NOTE]
   > Ponieważ farma programu SharePoint jest chroniona do długoterminowego przechowywania na platformie Azure, na serwerze serwera usługi MAB nie są dostępne żadne informacje o katalogu (metadane). W związku z tym zawsze, gdy baza danych zawartości programu SharePoint do punktu w czasie musi zostać odzyskana, należy ponownie wykazać farmy programu SharePoint.
   >
   >
3. Wybierz pozycję **ponownie katalog**.

    ![SERWERA usługi MAB SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Zostanie otwarte okno stanu usługi **Cloud Catalog** .

    ![SERWERA usługi MAB SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po zakończeniu wykazania stan zmieni się na *powodzenie*. Wybierz pozycję **Close** (Zamknij).

    ![SERWERA usługi MAB SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Wybierz obiekt programu SharePoint wyświetlany na karcie **odzyskiwanie** serwera usługi MAB, aby uzyskać strukturę bazy danych zawartości. Kliknij prawym przyciskiem myszy element, a następnie wybierz polecenie **Odzyskaj**.

    ![SERWERA usługi MAB SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. W tym momencie wykonaj kroki odzyskiwania opisane wcześniej w tym artykule, aby odzyskać bazę danych zawartości programu SharePoint z dysku.

## <a name="switching-the-front-end-web-server"></a>Przełączanie serwera sieci Web Front-End

Jeśli masz więcej niż jeden serwer frontonu sieci Web i chcesz przełączyć serwer, którego serwera usługi MAB używa do ochrony farmy, postępuj zgodnie z instrukcjami:

Poniższa procedura używa przykładu farmy serwerów z dwoma serwerami frontonu sieci Web, *serwer1* i *Serwer2*. SERWERA usługi MAB używa *serwer1* do ochrony farmy. Zmień serwer frontonu sieci Web, który serwera usługi MAB używa do *Serwer2* , aby można było usunąć *serwer1* z farmy.

> [!NOTE]
> Jeśli serwer frontonu sieci Web używany przez serwera usługi MAB do ochrony farmy jest niedostępny, użyj poniższej procedury, aby zmienić serwer frontonu sieci Web, rozpoczynając od kroku 4.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>Aby zmienić serwer frontonu sieci Web, którego serwera usługi MAB używa do ochrony farmy

1. Zatrzymaj usługę składnika zapisywania usługi VSS programu SharePoint na *serwerze Serwer1* , uruchamiając następujące polecenie w wierszu polecenia:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. Na *serwerze Serwer1*Otwórz Edytor rejestru i przejdź do następującego klucza:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. Zaznacz wszystkie wartości wymienione w podkluczu VssAccessControl. Jeśli dowolny wpis ma dane wartości 0 i inny składnik zapisywania usługi VSS jest uruchomiony w ramach poświadczeń skojarzonego konta, Zmień dane wartości na 1.

1. Zainstaluj agenta ochrony na serwerze *Serwer2*.

   > [!WARNING]
   > Można przełączyć tylko serwery frontonu sieci Web, jeśli oba serwery znajdują się w tej samej domenie.

1. Na serwerze *Serwer2*w wierszu polecenia Zmień katalog na `_MABS installation location_\bin\` i uruchom **ConfigureSharepoint**. Aby uzyskać więcej informacji na temat ConfigureSharePoint, zobacz [Konfigurowanie kopii zapasowej](#configure-backup).

1. Wybierz grupę ochrony, do której należy farma serwerów, a następnie wybierz pozycję **Modyfikuj grupę ochrony**.

1. W Kreatorze modyfikowania grupy na stronie **Wybierz członków grupy** rozwiń węzeł *Serwer2* i wybierz farmę serwerów, a następnie Ukończ pracę kreatora.

   Zostanie uruchomione sprawdzanie spójności.

1. Jeśli wykonano krok 6, można teraz usunąć wolumin z grupy ochrony.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem [Tworzenie kopii zapasowej programu Exchange Server](backup-azure-exchange-mabs.md) .
Zapoznaj się z artykułem [Tworzenie kopii zapasowej SQL Server](backup-azure-sql-mabs.md) .
