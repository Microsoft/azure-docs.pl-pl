---
title: Instalowanie adaptera StorSimple dla programu SharePoint | Microsoft Docs
description: Opisuje sposób instalowania i konfigurowania lub usuwania adaptera StorSimple dla programu SharePoint w farmie serwerów programu SharePoint.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: alkohli
ms.openlocfilehash: 639efab3a9eaef400b3fbe3d0b134d02f458b865
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966296"
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instalowanie i Konfigurowanie adaptera StorSimple dla programu SharePoint
## <a name="overview"></a>Omówienie
Adapter StorSimple dla programu SharePoint to składnik umożliwiający zapewnienie Microsoft Azure StorSimple elastycznego magazynu i ochrony danych w farmach serwerów programu SharePoint. Za pomocą adaptera można przenieść zawartość obiektów binarnych (BLOB) z SQL Serverch baz danych zawartości do Microsoft Azure StorSimple hybrydowego urządzenia magazynującego w chmurze.

Adapter StorSimple dla programu SharePoint działa jako dostawca zdalnego magazynu obiektów BLOB (SPZ) i używa SQL Server zdalnej usługi BLOB Storage do przechowywania niestrukturalnej zawartości programu SharePoint (w formie obiektów BLOB) na serwerze plików, który jest obsługiwany przez urządzenie StorSimple.

> [!NOTE]
> Adapter StorSimple dla programu SharePoint obsługuje program SharePoint Server 2010 Remote BLOB Storage (SPZ). Nie obsługuje on zewnętrznego magazynu obiektów BLOB programu SharePoint Server 2010 (EBS).


* Aby pobrać adapter StorSimple dla programu SharePoint, przejdź do [karty StorSimple dla programu SharePoint][1] w centrum pobierania Microsoft.
* Informacje o planowaniu ograniczeń SPZ i SPZ można znaleźć w temacie [Decydowanie o użyciu SPZ w SharePoint 2013][2] lub [Plan for SPZ (SharePoint Server 2010)][3].

W pozostałej części tego omówienia krótko opisano rolę karty StorSimple dla programu SharePoint oraz limity pojemności i wydajności programu SharePoint, których należy wiedzieć przed zainstalowaniem i skonfigurowaniem adaptera. Po przejrzeniu tych informacji przejdź do pozycji [StorSimple adapter for SharePoint Installation](#storsimple-adapter-for-sharepoint-installation) , aby rozpocząć konfigurowanie karty.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>StorSimple adapter for SharePoint
W witrynie programu SharePoint zawartość jest przechowywana jako dane obiektów BLOB bez struktury w co najmniej jednej bazie danych zawartości. Domyślnie te bazy danych są hostowane na komputerach z systemem SQL Server i znajdują się w farmie serwerów programu SharePoint. Obiekty blob mogą szybko zwiększyć rozmiar i zużywać duże ilości magazynu lokalnego. Z tego powodu warto znaleźć inne, tańsze rozwiązanie do magazynowania. SQL Server zapewnia technologię o nazwie Remote Blob Storage (SPZ), która umożliwia przechowywanie zawartości obiektów BLOB w systemie plików poza bazą danych SQL Server. W przypadku SPZ obiekty blob mogą znajdować się w systemie plików na komputerze z uruchomionym SQL Server lub mogą być przechowywane w systemie plików na innym komputerze serwera.

SPZ wymaga użycia dostawcy SPZ, takiego jak adapter StorSimple dla programu SharePoint, aby włączyć strukturę SPZ w programie SharePoint. Adapter StorSimple dla programu SharePoint współpracuje z SPZ, umożliwiając przenoszenie obiektów BLOB do serwera, którego kopia zapasowa jest wykonywana przez system Microsoft Azure StorSimple. Microsoft Azure StorSimple następnie przechowuje dane obiektów BLOB lokalnie lub w chmurze na podstawie użycia. Obiekty blob, które są bardzo aktywne (zwykle nazywane warstwą 1 lub gorącą), znajdują się lokalnie. Mniej aktywne dane i dane archiwalne znajdują się w chmurze. Po włączeniu SPZ w bazie danych zawartości każda nowa zawartość obiektu BLOB utworzona w programie SharePoint będzie przechowywana na urządzeniu StorSimple, a nie w bazie danych zawartości.

Microsoft Azure StorSimple implementacja SPZ zapewnia następujące korzyści:

* Przenosząc zawartość obiektu BLOB na oddzielny serwer, można zmniejszyć obciążenie zapytania na SQL Server, co może poprawić czas reakcji SQL Server. 
* Usługa Azure StorSimple używa deduplikacji i kompresji w celu zmniejszenia rozmiaru danych.
* Usługa Azure StorSimple zapewnia ochronę danych w postaci migawek lokalnych i w chmurze. Ponadto w przypadku umieszczenia samej bazy danych na urządzeniu StorSimple można utworzyć kopię zapasową bazy danych zawartości i obiektów BLOB w spójny sposób. (Przeniesienie bazy danych zawartości na urządzenie jest obsługiwane tylko dla urządzenia z serii StorSimple 8000). Ta funkcja nie jest obsługiwana w przypadku serii 5000 lub 7000.
* Usługa Azure StorSimple obejmuje funkcje odzyskiwania po awarii, w tym tryb failover, odzyskiwanie plików i woluminów (w tym odzyskiwanie testowe) oraz szybkie przywracanie danych.
* Korzystając z oprogramowania do odzyskiwania danych, takiego jak Kroll Ontrack PowerControls, można użyć migawek StorSimple danych obiektów BLOB w celu przeprowadzenia odzyskiwania zawartości programu SharePoint na poziomie elementu. (To oprogramowanie do odzyskiwania danych jest osobnym zakupem).
* Adapter StorSimple dla wtyczki programu SharePoint w portalu administracji centralnej programu SharePoint, który umożliwia zarządzanie całym rozwiązaniem programu SharePoint z centralnej lokalizacji.

Przeniesienie zawartości obiektu BLOB do systemu plików może zapewnić inne oszczędności i korzyści. Na przykład użycie SPZ może ograniczyć potrzebę oszczędności magazynu warstwy 1 i, ponieważ zmniejsza ona bazę danych zawartości, SPZ może zmniejszyć liczbę baz danych wymaganych w farmie serwerów programu SharePoint. Jednak inne czynniki, takie jak limity rozmiaru bazy danych i ilość zawartości spoza SPZ, mogą również mieć wpływ na wymagania dotyczące magazynu. Aby uzyskać więcej informacji o kosztach i korzyściach wynikających z używania SPZ, zobacz [Plan for SPZ (SharePoint Foundation 2010)][4] i [Decydowanie o użyciu SPZ w programie SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Limity pojemności i wydajności
Przed rozpoczęciem korzystania z SPZ w rozwiązaniu programu SharePoint należy pamiętać o przetestowanych limitach wydajności i pojemności programu SharePoint Server 2010 i SharePoint Server 2013 oraz o tym, jak te limity odnoszą się do akceptowalnej wydajności. Aby uzyskać więcej informacji, zobacz [granice oprogramowania i limity dla programu SharePoint 2013](/SharePoint/install/software-boundaries-and-limits-0).

Przed skonfigurowaniem SPZ należy przejrzeć następujące elementy:

* Upewnij się, że całkowity rozmiar zawartości (rozmiar bazy danych zawartości oraz rozmiar wszystkich skojarzonych zewnętrznych obiektów BLOB) nie przekracza limitu rozmiaru SPZ obsługiwanego przez program SharePoint. Ten limit wynosi 200 GB. 
  
    **Aby zmierzyć bazę danych zawartości i rozmiar obiektu BLOB**
  
  1. Uruchom to zapytanie w WFE administracji centralnej. Uruchom powłokę zarządzania programu SharePoint, a następnie wprowadź następujące polecenie programu Windows PowerShell, aby uzyskać rozmiar baz danych zawartości:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Ten krok umożliwia pobranie rozmiaru bazy danych zawartości na dysku.
  2. Uruchom jedno z następujących zapytań SQL w programie SQL Management Studio w polu programu SQL Server w każdej bazie danych zawartości, a następnie dodaj wynik do liczby uzyskanej w kroku 1.
     
     W przypadku baz danych zawartości programu SharePoint 2013 wprowadź:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     W przypadku baz danych zawartości programu SharePoint 2010 wprowadź:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Ten krok umożliwia pobranie rozmiaru obiektów blob, które zostały zewnętrzne.
* Zalecamy przechowywanie wszystkich zawartości obiektów BLOB i bazy danych lokalnie na urządzeniu StorSimple. Urządzenie StorSimple to klaster z dwoma węzłami w celu zapewnienia wysokiej dostępności. Umieszczenie baz danych zawartości i obiektów BLOB na urządzeniu StorSimple zapewnia wysoką dostępność.
  
    Aby przenieść bazę danych zawartości na urządzenie StorSimple, użyj tradycyjnych najlepszych rozwiązań dotyczących migracji SQL Server. Przenieś bazę danych dopiero po przeniesieniu całej zawartości obiektu BLOB z bazy danych do udziału plików za pomocą SPZ. Jeśli zdecydujesz się przenieść bazę danych zawartości na urządzenie StorSimple, zalecamy skonfigurowanie magazynu bazy danych zawartości na urządzeniu jako woluminu podstawowego.
* W Microsoft Azure StorSimple, jeśli używane są woluminy warstwowe, nie ma możliwości zagwarantowania, że zawartość przechowywana lokalnie na urządzeniu StorSimple nie będzie warstwą Microsoft Azure magazynu w chmurze. W związku z tym zalecamy używanie woluminów przypiętych lokalnie StorSimple w połączeniu z SPZ programu SharePoint. Dzięki temu cała zawartość obiektu BLOB będzie pozostawać lokalnie na urządzeniu StorSimple i nie będzie przenoszona do Microsoft Azure.
* Jeśli nie przechowujesz baz danych zawartości na urządzeniu StorSimple, użyj SQL Server tradycyjnych najlepszych rozwiązań o wysokiej dostępności, które obsługują SPZ. SQL Server klastrowanie obsługuje strukturę SPZ, podczas gdy SQL Server dublowania nie. 

> [!WARNING]
> Jeśli nie włączono SPZ, nie zalecamy przeniesienia bazy danych zawartości na urządzenie StorSimple. To jest Nietestowana konfiguracja.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Instalacja adaptera StorSimple dla programu SharePoint
Przed zainstalowaniem adaptera StorSimple dla programu SharePoint należy skonfigurować urządzenie StorSimple i upewnić się, że w farmie serwerów programu SharePoint i wystąpieniu SQL Server są spełnione wszystkie wymagania wstępne. W tym samouczku opisano wymagania dotyczące konfiguracji, a także procedury instalowania i uaktualniania adaptera StorSimple dla programu SharePoint.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych
Przed zainstalowaniem adaptera StorSimple dla programu SharePoint upewnij się, że na urządzeniu StorSimple, farmie serwerów programu SharePoint i wystąpieniu SQL Server są spełnione następujące wymagania wstępne.

### <a name="system-requirements"></a>Wymagania systemowe
Adapter StorSimple dla programu SharePoint współpracuje z następującym sprzętem i oprogramowaniem:

* Obsługiwany system operacyjny — Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2012 lub Windows Server 2012 R2
* Obsługiwane wersje programu SharePoint — SharePoint Server 2010 lub SharePoint Server 2013
* Obsługiwane SQL Server wersje — SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition lub SQL Server 2012 Enterprise Edition
* Obsługiwane urządzenia StorSimple — seria StorSimple 8000, Seria StorSimple 7000 lub StorSimple 5000 Series.

### <a name="storsimple-device-configuration-prerequisites"></a>Wymagania wstępne dotyczące konfiguracji urządzenia StorSimple
Urządzenie StorSimple jest urządzeniem blokującym i wymaga serwera plików, na którym mogą być hostowane dane. Zalecamy używanie osobnego serwera, a nie istniejącego serwera z farmy programu SharePoint. Ten serwer plików musi znajdować się w tej samej sieci lokalnej (LAN) co komputer SQL Server, który obsługuje bazy danych zawartości.

> [!TIP]
> * W przypadku skonfigurowania farmy programu SharePoint pod kątem wysokiej dostępności należy również wdrożyć serwer plików pod kątem wysokiej dostępności.
> * Jeśli nie przechowujesz bazy danych zawartości na urządzeniu StorSimple, użyj tradycyjnych najlepszych rozwiązań o wysokiej dostępności, które obsługują SPZ. SQL Server klastrowanie obsługuje strukturę SPZ, podczas gdy SQL Server dublowania nie. 


Upewnij się, że urządzenie StorSimple jest prawidłowo skonfigurowane i że odpowiednie woluminy do obsługi wdrożenia programu SharePoint są skonfigurowane i dostępne z komputera SQL Server. Przejdź do pozycji [Wdróż lokalne urządzenie StorSimple](storsimple-8000-deployment-walkthrough-u2.md) , jeśli jeszcze nie wdrożono i nie skonfigurowano urządzenia StorSimple. Zanotuj adres IP urządzenia StorSimple; będzie on potrzebny podczas instalacji programu SharePoint adaptera StorSimple.

Ponadto upewnij się, że wolumin, który ma być używany na potrzeby obiektu BLOB externalization, spełnia następujące wymagania:

* Wolumin musi być sformatowany przy użyciu rozmiaru jednostki alokacji 64 KB.
* Fronton sieci Web (WFE) i serwery aplikacji muszą mieć możliwość uzyskania dostępu do woluminu za pośrednictwem ścieżki Universal Naming Convention (UNC).
* Farma serwerów programu SharePoint musi być skonfigurowana do zapisu na woluminie.

> [!NOTE]
> Po zainstalowaniu i skonfigurowaniu karty wszystkie obiekty BLOB externalization muszą przechodzić przez urządzenie StorSimple (urządzenie będzie prezentować woluminy do SQL Server i zarządzania warstwami magazynowania). Nie można używać żadnych innych elementów docelowych dla obiektu BLOB externalization.


Jeśli planujesz używać Snapshot Manager StorSimple do tworzenia migawek obiektów BLOB i danych bazy danych, należy zainstalować StorSimple Snapshot Manager na serwerze bazy danych, aby można było użyć usługi składnika zapisywania SQL w celu zaimplementowania Usługa kopiowania woluminów w tle systemu Windows (VSS).

> [!IMPORTANT]
> StorSimple Snapshot Manager nie obsługuje składnika zapisywania usługi VSS programu SharePoint i nie może przyjmować migawek spójnych z aplikacjami dla danych programu SharePoint. W scenariuszu programu SharePoint StorSimple Snapshot Manager zapewnia tylko kopie zapasowe spójne na poziomie awarii.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Wymagania wstępne konfiguracji farmy programu SharePoint
Upewnij się, że farma serwerów programu SharePoint została prawidłowo skonfigurowana, w następujący sposób:

* Sprawdź, czy farma serwerów programu SharePoint jest w dobrej kondycji i sprawdź następujące kwestie:
* Wszystkie serwery i aplikacje programu SharePoint WFE zarejestrowane w farmie działają i można je wysyłać za pomocą polecenia ping z serwera, na którym zostanie zainstalowana karta StorSimple dla programu SharePoint.
* Usługa czasomierza programu SharePoint (SPTimerV3 lub SPTimerV4) jest uruchomiona na każdym serwerze WFE i na serwerze aplikacji.
* Zarówno usługa czasomierza programu SharePoint, jak i Pula aplikacji usług IIS, w której jest uruchomiona Witryna administracji centralnej programu SharePoint, ma uprawnienia administracyjne.
* Upewnij się, że kontekst rozszerzonych zabezpieczeń programu Internet Explorer (IE ESC) jest wyłączony. Wykonaj następujące kroki, aby wyłączyć program IE ESC:
  
  1. Zamknij wszystkie wystąpienia programu Internet Explorer.
  2. Uruchom Menedżer serwera.
  3. W lewym okienku kliknij pozycję **serwer lokalny**.
  4. W prawym okienku obok pozycji **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** kliknij pozycję **włączone**.
  5. W obszarze **administratorzy** kliknij pozycję **wyłączone**.
  6. Kliknij przycisk **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Wymagania wstępne dotyczące zdalnego magazynu obiektów BLOB (SPZ)
Upewnij się, że używasz obsługiwanej wersji programu SQL Server. Tylko następujące wersje są obsługiwane i mogą korzystać z SPZ:

* SQL Server 2008 Enterprise Edition
* SQL Server 2008 R2 Enterprise Edition
* SQL Server 2012 Enterprise Edition

Obiekty blob mogą być zewnętrzne tylko na woluminach, które przedstawia SQL Server urządzenie StorSimple. Nie są obsługiwane żadne inne elementy docelowe dla externalization obiektów BLOB.

Po zakończeniu wszystkich kroków konfiguracji wymagań wstępnych przejdź do sekcji [Instalowanie adaptera StorSimple dla programu SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalowanie adaptera StorSimple dla programu SharePoint
Wykonaj następujące kroki, aby zainstalować adapter StorSimple dla programu SharePoint. W przypadku ponownej instalacji oprogramowania należy zapoznać [się z tematem Upgrade lub Install the StorSimple adapter for SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Czas wymagany do instalacji zależy od łącznej liczby baz danych programu SharePoint w farmie serwerów programu SharePoint.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Konfigurowanie SPZ
Po zainstalowaniu adaptera StorSimple dla programu SharePoint Skonfiguruj strukturę SPZ zgodnie z opisem w poniższej procedurze.

> [!TIP]
> Adapter StorSimple dla wtyczki programu SharePoint na stronie Administracja centralna programu SharePoint, która umożliwia włączenie lub wyłączenie SPZ dla każdej bazy danych zawartości w farmie programu SharePoint. Włączenie lub wyłączenie SPZ w bazie danych zawartości powoduje jednak Resetowanie usług IIS, które w zależności od konfiguracji farmy może chwilowo zakłócać dostępność frontonu sieci Web programu SharePoint (WFE). (Takie czynniki jak użycie modułu równoważenia obciążenia frontonu, bieżącego obciążenia serwera i tak dalej, mogą ograniczać lub eliminować zakłócenia). Aby chronić użytkowników przed zakłóceniami, zalecamy włączenie lub wyłączenie funkcji SPZ tylko w ramach zaplanowanego okna obsługi.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Konfigurowanie wyrzucania elementów bezużytecznych
Po usunięciu obiektów z witryny programu SharePoint nie są one automatycznie usuwane z woluminu magazynu SPZ. Zamiast tego, asynchroniczny program konserwacji w tle usuwa oddzielone obiekty blob z magazynu plików. Administratorzy systemu mogą zaplanować okresowe uruchamianie tego procesu lub uruchamiać je w razie potrzeby.

Ten program obsługi (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) jest automatycznie instalowany na wszystkich serwerach i serwerach aplikacji programu SharePoint WFE po włączeniu SPZ. Program jest instalowany w następującej lokalizacji: *dysk rozruchowy*: \Program Files\Microsoft SQL Remote BLOB Storage 10.50 \ utrzymujący \

Aby uzyskać informacje o konfigurowaniu i korzystaniu z programu obsługi, zobacz temat [konserwowanie SPZ w programie SharePoint Server 2013][8].

> [!IMPORTANT]
> Program utrzymujący struktury SPZ jest intensywnie obciążający zasoby. Należy zaplanować jej uruchamianie tylko podczas okresów działania światła w farmie programu SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Natychmiastowe usuwanie oddzielonych obiektów BLOB
Jeśli musisz natychmiast usunąć oddzielone obiekty blob, możesz użyć poniższych instrukcji. Należy zauważyć, że te instrukcje są przykładem tego, jak można to zrobić w środowisku programu SharePoint 2013 z następującymi składnikami:

* Nazwa bazy danych zawartości jest WSS_Content.
* Nazwa SQL Server to SHRPT13-SQL12\SHRPT13.
* Nazwa aplikacji sieci Web to SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Uaktualnianie lub ponowne instalowanie adaptera StorSimple dla programu SharePoint
Użyj następującej procedury, aby uaktualnić program SharePoint Server, a następnie ponownie zainstaluj adapter StorSimple dla programu SharePoint lub po prostu Uaktualnij lub ponownie zainstaluj kartę w istniejącej farmie serwerów programu SharePoint.

> [!IMPORTANT]
> Przed podjęciem próby uaktualnienia oprogramowania programu SharePoint i/lub uaktualnienia lub ponownej instalacji adaptera StorSimple dla programu SharePoint zapoznaj się z poniższymi informacjami:
> 
> * Wszystkie pliki, które zostały wcześniej przeniesione do magazynu zewnętrznego za pośrednictwem SPZ, nie będą dostępne do momentu zakończenia ponownej instalacji i włączenia funkcji SPZ. Aby ograniczyć wpływ na użytkowników, należy przeprowadzić uaktualnienie lub ponowną instalację podczas planowanego okna obsługi.
> * Czas wymagany na uaktualnienie/ponowną instalację może się różnić w zależności od łącznej liczby baz danych programu SharePoint w farmie serwerów programu SharePoint.
> * Po zakończeniu uaktualniania/ponownej instalacji należy włączyć opcję SPZ dla baz danych zawartości. Aby uzyskać więcej informacji, zobacz [Konfigurowanie SPZ](#configure-rbs) .
> * W przypadku konfigurowania struktury SPZ dla farmy programu SharePoint, która ma bardzo dużą liczbę baz danych (ponad 200), Strona **administracji centralnej programu SharePoint** może przekroczyć limit czasu. W takim przypadku należy odświeżyć stronę. Nie ma to wpływu na proces konfiguracji.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Usuwanie adaptera StorSimple dla programu SharePoint
W poniższych procedurach opisano sposób przenoszenia obiektów blob z powrotem do baz danych zawartości SQL Server, a następnie odinstalowania adaptera StorSimple dla programu SharePoint. 

> [!IMPORTANT]
> Przed odinstalowaniem oprogramowania karty obiekty blob należy przenieść z powrotem do baz danych zawartości.


### <a name="before-you-begin"></a>Zanim rozpoczniesz
Przed przeniesieniem danych z powrotem do baz danych zawartości SQL Server należy zebrać następujące informacje i rozpocząć proces usuwania karty:

* Nazwy wszystkich baz danych, dla których jest włączona funkcja SPZ
* Ścieżka UNC skonfigurowanego magazynu obiektów BLOB

### <a name="move-the-blobs-back-to-the-content-databases"></a>Przenoszenie obiektów blob z powrotem do baz danych zawartości
Przed odinstalowaniem adaptera StorSimple dla oprogramowania SharePoint należy zmigrować wszystkie obiekty blob, które zostały zewnętrzne z powrotem do baz danych zawartości SQL Server. Jeśli podjęto próbę odinstalowania adaptera StorSimple dla programu SharePoint przed przeniesieniem wszystkich obiektów blob z powrotem do baz danych zawartości, zostanie wyświetlony następujący komunikat ostrzegawczy.

![Komunikat ostrzegawczy](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Aby przenieść obiekty blob z powrotem do baz danych zawartości
1. Pobierz wszystkie obiekty zewnętrzne.
2. Otwórz stronę **administracji centralnej programu SharePoint** i przejdź do **ustawień systemowych**.
3. W obszarze **Azure StorSimple** kliknij pozycję **Konfiguruj StorSimple adapter**.
4. Na stronie **Konfigurowanie karty StorSimple** kliknij przycisk **Wyłącz** poniżej każdej bazy danych zawartości, która ma zostać usunięta z zewnętrznego magazynu obiektów BLOB. 
5. Usuń obiekty z programu SharePoint, a następnie Przekaż je ponownie.

Alternatywnie możesz użyć polecenia cmdlet programu Microsoft `RBS Migrate()` PowerShell dołączonego do programu SharePoint. Aby uzyskać więcej informacji, zobacz [Migrowanie zawartości do lub z SPZ](/previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)).

Po przeniesieniu obiektów blob z powrotem do bazy danych zawartości przejdź do następnego kroku: [Odinstaluj kartę](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Odinstalowywanie adaptera
Po przeniesieniu obiektów blob z powrotem do SQL Server baz danych zawartości Użyj jednej z następujących opcji, aby odinstalować adapter StorSimple dla programu SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Aby użyć programu instalacyjnego do odinstalowania karty
1. Zaloguj się na serwerze frontonu sieci Web (WFE) przy użyciu konta z uprawnieniami administratora.
2. Kliknij dwukrotnie kartę StorSimple dla Instalatora programu SharePoint. Zostanie uruchomiony Kreator instalacji.
   
    ![Kreator instalacji](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Kliknij przycisk **Dalej**. Zostanie wyświetlona następująca strona.
   
    ![Usuwanie strony przez Kreatora instalacji](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Kliknij przycisk **Usuń** , aby wybrać proces usuwania. Zostanie wyświetlona następująca strona.
   
    ![Strona potwierdzenia kreatora konfiguracji](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Kliknij przycisk **Usuń** , aby potwierdzić usunięcie. Zostanie wyświetlona następująca strona postępu.
   
    ![Strona postępu Kreatora konfiguracji](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Po zakończeniu usuwania zostanie wyświetlona strona Zakończ. Kliknij przycisk **Zakończ** , aby zamknąć kreatora instalacji.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Aby odinstalować kartę przy użyciu panelu sterowania
1. Otwórz Panel sterowania, a następnie kliknij pozycję **programy i funkcje**.
2. Wybierz pozycję **StorSimple adapter dla programu SharePoint**, a następnie kliknij przycisk **Odinstaluj**.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: /SharePoint/administration/rbs-planning
[3]: /previous-versions/office/sharepoint-server-2010/ff628583(v=office.14)
[4]: /previous-versions/office/sharepoint-foundation-2010/ff628569(v=office.14)
[5]: /SharePoint/administration/rbs-planning
[8]: /SharePoint/administration/maintain-rbs