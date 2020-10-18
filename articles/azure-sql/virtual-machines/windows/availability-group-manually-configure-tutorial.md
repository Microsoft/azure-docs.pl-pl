---
title: 'Samouczek: Konfigurowanie zawsze włączone grupy dostępności SQL Server'
description: W tym samouczku pokazano SQL Server, jak utworzyć grupę dostępności zawsze włączona na platformie Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: ee249a33187c3f8776cfc8fc750590c58f74579e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168158"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>Samouczek: Ręczne konfigurowanie grupy dostępności (SQL Server na maszynach wirtualnych platformy Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym samouczku pokazano, jak utworzyć grupę dostępności zawsze włączona dla SQL Server w usłudze Azure Virtual Machines. Kompletny samouczek tworzy grupę dostępności z repliką bazy danych na dwóch serwerach SQL.

Chociaż w tym artykule ręcznie konfiguruje się środowisko grupy dostępności, można to również zrobić przy użyciu [Azure Portal](availability-group-azure-portal-configure.md), [programu PowerShell lub interfejsu wiersza polecenia platformy Azure](availability-group-az-commandline-configure.md)lub [szablonów szybkiego startu platformy Azure](availability-group-quickstart-template-configure.md) . 


**Szacowany czas**: trwa około 30 minut, po spełnieniu [wymagań wstępnych](availability-group-manually-configure-prerequisites-tutorial.md) .


## <a name="prerequisites"></a>Wymagania wstępne

W samouczku założono, że masz podstawową wiedzę na temat SQL Server zawsze włączonymi grupami dostępności. Jeśli potrzebujesz więcej informacji, zobacz temat [Omówienie grup dostępności zawsze włączone (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

Przed rozpoczęciem tego samouczka należy [spełnić wymagania wstępne dotyczące tworzenia zawsze dostępnych grup dostępności na platformie Azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md). Jeśli te wymagania wstępne zostały już wykonane, możesz przejść do [tworzenia klastra](#CreateCluster).

W poniższej tabeli wymieniono wymagania wstępne, które należy wykonać przed rozpoczęciem pracy z tym samouczkiem:

| Wymaganie |Opis |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Dwa wystąpienia SQL Server** | — W zestawie dostępności platformy Azure <br/> — W pojedynczej domenie <br/> — Z zainstalowaną funkcją klaster trybu failover |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Udział plików dla monitora klastra |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Konto usługi SQL Server** | Konto domeny |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Konto usługi SQL Server Agent** | Konto domeny |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Otwarte porty zapory** | -SQL Server: **1433** dla domyślnego wystąpienia <br/> -Punkt końcowy dublowania bazy danych: **5022** lub dowolny dostępny port <br/> -Sonda kondycji adresu IP modułu równoważenia obciążenia grupy dostępności: **59999** lub dowolny dostępny port <br/> — Sonda kondycji adresu IP podstawowego modułu równoważenia obciążenia klastra: **58888** lub dowolny dostępny port |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Dodawanie funkcji klaster trybu failover** | Oba wystąpienia SQL Server wymagają tej funkcji |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Konto domeny instalacji** | -Administrator lokalny na każdym SQL Server <br/> -Składowa SQL Server stałej roli serwera sysadmin dla każdego wystąpienia SQL Server  |

>[!NOTE]
> Wiele kroków przedstawionych w tym samouczku można teraz zautomatyzować za pomocą [Azure Portal](availability-group-azure-portal-configure.md), programu PowerShell i [szablonów szybkiego startu](availability-group-quickstart-template-configure.md) [interfejsu wiersza polecenia](availability-group-az-cli-configure.md) platformy Azure.


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Tworzenie klastra

Po zakończeniu wymagań wstępnych pierwszym krokiem jest utworzenie klastra trybu failover systemu Windows Server zawierającego dwa serwery SQL i serwer monitora.

1. Użyj Remote Desktop Protocol (RDP), aby nawiązać połączenie z pierwszym SQL Server. Użyj konta domeny, które jest kontem administratora na serwerach SQL i serwerze monitora.

   >[!TIP]
   >Jeśli zawarto [dokument z wymaganiami wstępnymi](availability-group-manually-configure-prerequisites-tutorial.md), utworzono konto o nazwie **CORP\Install**. Użyj tego konta.

2. Na pulpicie nawigacyjnym **Menedżer serwera** wybierz pozycję **Narzędzia**, a następnie wybierz pozycję **Menedżer klastra trybu failover**.
3. W lewym okienku kliknij prawym przyciskiem myszy pozycję **Menedżer klastra trybu failover**, a następnie wybierz pozycję **Utwórz klaster**.

   ![Tworzenie klastra](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. W Kreatorze tworzenia klastra utwórz klaster z jednym węzłem, przechodząc przez strony z ustawieniami w poniższej tabeli:

   | Strona | Ustawienia |
   | --- | --- |
   | Zanim rozpoczniesz |Użyj domyślnych |
   | Wybierz serwery |Wpisz nazwę pierwszej SQL Server w polu **Wprowadź nazwę serwera** , a następnie wybierz pozycję **Dodaj**. |
   | Ostrzeżenie dotyczące walidacji |Wybierz pozycję **nie. nie wymagaj pomocy technicznej firmy Microsoft dla tego klastra i dlatego nie należy uruchamiać testów weryfikacyjnych. Po wybraniu przycisku Dalej Kontynuuj tworzenie klastra**. |
   | Punkt dostępu do administrowania klastrem |Wpisz nazwę klastra, na przykład **SQLAGCluster1** w polu **Nazwa klastra**.|
   | Potwierdzenie |Użyj wartości domyślnych, chyba że używasz funkcji miejsca do magazynowania. Zapoznaj się z uwagą poniżej tej tabeli. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Ustaw adres IP klastra trybu failover systemu Windows Server

  > [!NOTE]
  > W systemie Windows Server 2019 klaster tworzy **rozproszoną nazwę serwera** zamiast **nazwy sieciowej klastra**. Jeśli używasz systemu Windows Server 2019, Pomiń wszystkie kroki odnoszące się do nazwy podstawowe klastra w tym samouczku. Nazwę sieci klastra można utworzyć przy użyciu [programu PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster). Aby uzyskać więcej informacji, przejrzyj [klaster trybu failover w blogu: cluster network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) . 

1. W **Menedżer klastra trybu failover**przewiń w dół do **zasobów podstawowe klastra** i rozwiń Szczegóły klastra. Powinna zostać wyświetlona **Nazwa** i zasoby **adresów IP** w stanie **Niepowodzenie** . Nie można przełączyć zasobu adresu IP do trybu online, ponieważ klaster ma przypisany ten sam adres IP co komputer, w związku z czym jest to zduplikowany adres.

2. Kliknij prawym przyciskiem myszy zasób niepowodzenie **adresu IP** , a następnie wybierz polecenie **Właściwości**.

   ![Właściwości klastra](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Wybierz pozycję **statyczny adres IP** i określ dostępny adres z tej samej podsieci co maszyny wirtualne.

4. W sekcji **zasoby podstawowe klastra** kliknij prawym przyciskiem myszy pozycję Nazwa klastra i wybierz polecenie **Przełącz do trybu online**. Zaczekaj, aż oba zasoby będą w trybie online. Gdy zasób Nazwa klastra przejdzie w tryb online, aktualizuje serwer kontrolera domeny przy użyciu nowego konta komputera Active Directory (AD). Użyj tego konta usługi AD, aby później uruchomić usługę klastrowaną grupy dostępności.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Dodawanie innych SQL Server do klastra

Dodaj inne SQL Server do klastra.

1. W drzewie przeglądarki kliknij prawym przyciskiem myszy klaster i wybierz polecenie **Dodaj węzeł**.

    ![Dodawanie węzła do klastra](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. W **Kreatorze dodawania węzłów**wybierz pozycję **dalej**. Na stronie **Wybierz serwery** dodaj drugą SQL Server. Wpisz nazwę serwera w polu **Wprowadź nazwę serwera** , a następnie wybierz pozycję **Dodaj**. Gdy skończysz, wybierz pozycję **dalej**.

1. Na stronie **ostrzeżenie dotyczące sprawdzania poprawności** wybierz pozycję **nie** (w scenariuszu produkcyjnym należy wykonać testy weryfikacyjne). Następnie wybierz pozycję **Dalej**.

8. W przypadku korzystania z funkcji miejsca do magazynowania na stronie **potwierdzenie** Usuń zaznaczenie pola wyboru **Dodaj wszystkie odpowiednie magazyny do klastra.**

   ![Potwierdzenie dodawania węzła](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Jeśli używasz funkcji miejsca do magazynowania i nie zaznaczaj **żadnych opcji Dodaj wszystkie odpowiednie magazyny do klastra**, system Windows odłącza dyski wirtualne podczas procesu klastrowania. W związku z tym nie są one wyświetlane w Menedżerze dysków ani w Eksploratorze, dopóki nie zostaną usunięte miejsca do magazynowania z klastra i ponownie dołączone przy użyciu programu PowerShell. Funkcja miejsca do magazynowania grupuje wiele dysków w puli magazynów. Aby uzyskać więcej informacji, zobacz [miejsca do magazynowania](https://technet.microsoft.com/library/hh831739).
   >

1. Wybierz pozycję **Dalej**.

1. Wybierz pozycję **Zakończ**.

   Menedżer klastra trybu failover pokazuje, że klaster ma nowy węzeł i znajduje się w kontenerze **węzły** .

10. Wyloguj się z sesji pulpitu zdalnego.

### <a name="add-a-cluster-quorum-file-share"></a>Dodawanie udziału plików kworum klastra

W tym przykładzie klaster systemu Windows korzysta z udziału plików w celu utworzenia kworum klastra. W tym samouczku jest używana funkcja kworum większość węzłów i udziałów plików. Aby uzyskać więcej informacji, zobacz [Opis konfiguracji kworum w klastrze trybu failover](https://technet.microsoft.com/library/cc731739.aspx).

1. Nawiąż połączenie z serwerem członkowskim monitora udostępniania plików przy użyciu sesji pulpitu zdalnego.

1. Na **Menedżer serwera**wybierz pozycję **Narzędzia**. Otwórz **przystawkę Zarządzanie komputerem**.

1. Wybierz pozycję **Foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy pozycję **udziały**, a następnie wybierz pozycję **Nowy udział.**

   ![Kliknij prawym przyciskiem myszy pozycję udziały i wybierz pozycję Nowy udział](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Użyj **Kreatora tworzenia folderu udostępnionego** , aby utworzyć udział.

1. W polu **ścieżka folderu**wybierz pozycję **Przeglądaj** i zlokalizuj lub Utwórz ścieżkę do folderu udostępnionego. Wybierz pozycję **Dalej**.

1. W polu **Nazwa, opis i ustawienia** Sprawdź nazwę i ścieżkę udziału. Wybierz pozycję **Dalej**.

1. W obszarze **uprawnienia do folderu udostępnionego** Ustaw **uprawnienia do dostosowywania**. Wybierz **niestandardową...**.

1. Na **Dostosuj uprawnienia**, wybierz przycisk **Dodaj...**.

1. Upewnij się, że konto użyte do utworzenia klastra ma pełną kontrolę.

   ![Upewnij się, że konto użyte do utworzenia klastra ma pełną kontrolę](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Wybierz przycisk **OK**.

1. W obszarze **uprawnienia do folderu udostępnionego**wybierz pozycję **Zakończ**. Ponownie wybierz pozycję **Zakończ** .  

1. Wyloguj się z serwera

### <a name="configure-the-cluster-quorum"></a>Konfigurowanie kworum klastra

Następnie skonfiguruj kworum klastra.

1. Połącz się z pierwszym węzłem klastra przy użyciu pulpitu zdalnego.

1. W **Menedżer klastra trybu failover**kliknij prawym przyciskiem myszy klaster, wskaż polecenie **więcej akcji**, a następnie wybierz pozycję **Konfiguruj ustawienia kworum klastra.**

   ![Wybierz pozycję Konfiguruj ustawienia kworum klastra](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. W **Kreatorze konfiguracji kworum klastra**wybierz pozycję **dalej**.

1. W obszarze **wybierz konfigurację kworum**wybierz opcję **Wybierz monitor kworum**, a następnie wybierz przycisk **dalej**.

1. Na **Wybieranie monitora kworum**wybierz opcję **Konfiguruj Monitor udziału plików**.

   >[!TIP]
   >System Windows Server 2016 obsługuje monitor w chmurze. W przypadku wybrania tego typu monitora nie jest potrzebny monitor udostępniania plików. Aby uzyskać więcej informacji, zobacz [wdrażanie monitora chmury dla klastra trybu failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). W tym samouczku jest używany monitor udostępniania plików, który jest obsługiwany przez poprzednie systemy operacyjne.
   >

1. Na **skonfigurować Monitor udostępniania plików**, wpisz ścieżkę dla utworzonego udziału. Wybierz pozycję **Dalej**.

1. Sprawdź ustawienia w obszarze **potwierdzenie**. Wybierz pozycję **Dalej**.

1. Wybierz pozycję **Zakończ**.

Zasoby podstawowe klastra są skonfigurowane za pomocą monitora udziału plików.

## <a name="enable-availability-groups"></a>Włączanie grup dostępności

Następnie Włącz funkcję **zawsze włączonych grup dostępności** . Wykonaj te czynności na obu serwerach SQL.

1. Na ekranie **startowym** uruchom **SQL Server Configuration Manager**.
2. W drzewie przeglądarki wybierz pozycję **usługi SQL Server**, a następnie kliknij prawym przyciskiem myszy usługę **SQL Server (MSSQLSERVER)** i wybierz pozycję **Właściwości**.
3. Wybierz kartę **AlwaysOn o wysokiej dostępności** , a następnie wybierz pozycję **Włącz zawsze włączone grupy dostępności**w następujący sposób:

    ![Włącz zawsze włączone grupy dostępności](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Wybierz przycisk **Zastosuj**. W podręcznym oknie dialogowym wybierz pozycję **OK** .

5. Uruchom ponownie usługę SQL Server.

Powtórz te kroki na drugim SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Utwórz bazę danych na pierwszej SQL Server

1. Uruchom plik RDP na pierwszej SQL Server przy użyciu konta domeny, które jest członkiem stałej roli serwera sysadmin.
1. Otwórz SQL Server Management Studio i Połącz się z pierwszym SQL Server.
7. W **Eksplorator obiektów**kliknij prawym przyciskiem myszy pozycję **bazy danych** , a następnie wybierz pozycję **Nowa baza danych**.
8. W polu **Nazwa bazy danych**wpisz **MyDB1**, a następnie wybierz przycisk **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Utwórz udział kopii zapasowych

1. Na pierwszej SQL Server w **Menedżer serwera**wybierz pozycję **Narzędzia**. Otwórz **przystawkę Zarządzanie komputerem**.

1. Wybierz pozycję **Foldery udostępnione**.

1. Kliknij prawym przyciskiem myszy pozycję **udziały**, a następnie wybierz pozycję **Nowy udział.**

   ![Wybierz pozycję Nowy udział](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Użyj **Kreatora tworzenia folderu udostępnionego** , aby utworzyć udział.

1. W polu **ścieżka folderu**wybierz pozycję **Przeglądaj** i zlokalizuj lub Utwórz ścieżkę do folderu udostępnionego kopii zapasowej bazy danych. Wybierz pozycję **Dalej**.

1. W polu **Nazwa, opis i ustawienia** Sprawdź nazwę i ścieżkę udziału. Wybierz pozycję **Dalej**.

1. W obszarze **uprawnienia do folderu udostępnionego** Ustaw **uprawnienia do dostosowywania**. Wybierz **niestandardową...**.

1. Na **Dostosuj uprawnienia**, wybierz przycisk **Dodaj...**.

1. Upewnij się, że konta usług SQL Server i SQL Server Agent dla obu serwerów mają pełną kontrolę.

   ![Upewnij się, że konta usług SQL Server i SQL Server Agent dla obu serwerów mają pełną kontrolę.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Wybierz przycisk **OK**.

1. W obszarze **uprawnienia do folderu udostępnionego**wybierz pozycję **Zakończ**. Ponownie wybierz pozycję **Zakończ** .  

### <a name="take-a-full-backup-of-the-database"></a>Wykonaj pełną kopię zapasową bazy danych

Należy wykonać kopię zapasową nowej bazy danych w celu zainicjowania łańcucha dzienników. Jeśli nie zostanie podjęta kopia zapasowa nowej bazy danych, nie można jej uwzględnić w grupie dostępności.

1. W **Eksplorator obiektów**kliknij prawym przyciskiem myszy bazę danych, wskaż polecenie **zadania...**, wybierz pozycję **Utwórz kopię zapasową**.

1. Wybierz **przycisk OK** , aby utworzyć pełną kopię zapasową do domyślnej lokalizacji kopii zapasowej.

## <a name="create-the-availability-group"></a>Utwórz grupę dostępności

Teraz można przystąpić do konfigurowania grupy dostępności, wykonując następujące czynności:

* Utwórz bazę danych na pierwszej SQL Server.
* Wykonaj kopię zapasową kopii zapasowej bazy danych i dziennika transakcji.
* Przywróć pełne i Rejestruj kopie zapasowe w drugim SQL Server z opcją **NORECOVERY** .
* Utwórz grupę dostępności (**AG1**) z zatwierdzaniem synchronicznym, automatycznym trybem failover i możliwymi do odczytu replikami pomocniczymi.

### <a name="create-the-availability-group"></a>Utwórz grupę dostępności:

1. W sesji pulpitu zdalnego z pierwszym SQL Server. W **Eksplorator obiektów** w programie SSMS kliknij prawym przyciskiem myszy pozycję **AlwaysOn wysoka dostępność** i wybierz pozycję **Kreator nowej grupy dostępności**.

    ![Uruchom Kreatora nowej grupy dostępności](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. Na stronie **wprowadzenie** wybierz pozycję **dalej**. Na stronie **Określ nazwę grupy dostępności** wpisz nazwę grupy dostępności w polu **Nazwa grupy dostępności**. Na przykład **AG1**. Wybierz pozycję **Dalej**.

    ![Kreator nowej grupy dostępności, Określanie nazwy grupy dostępności](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. Na stronie **wybierz bazy** danych wybierz swoją bazę danych, a następnie wybierz pozycję **dalej**.

   >[!NOTE]
   >Baza danych spełnia wymagania wstępne dla grupy dostępności, ponieważ wykonano co najmniej jedną pełną kopię zapasową w zamierzonej replice podstawowej.
   >

   ![Kreator nowej grupy dostępności, wybieranie baz danych](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. Na stronie **Określanie replik** wybierz pozycję **Dodaj replikę**.

   ![Kreator nowej grupy dostępności, określanie replik](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. Zostanie wyświetlone okno dialogowe **łączenie z serwerem** . Wpisz nazwę drugiego serwera w polu **Nazwa serwera**. Wybierz pozycję **Połącz**.

   Z powrotem na stronie **Określanie replik** powinien zostać wyświetlony drugi serwer wymieniony w obszarze **repliki dostępności**. Skonfiguruj repliki w następujący sposób.

   ![Kreator nowej grupy dostępności, określanie replik (ukończono)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Wybierz **punkty końcowe** , aby wyświetlić punkt końcowy dublowania bazy danych dla tej grupy dostępności. Użyj tego samego portu, który był używany podczas ustawiania [reguły zapory dla punktów końcowych dublowania bazy danych](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

    ![Kreator nowej grupy dostępności, wybieranie początkowej synchronizacji danych](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. Na stronie **Wybierz początkową synchronizację danych** wybierz pozycję **pełna** i określ udostępnioną lokalizację sieciową. Dla lokalizacji Użyj [utworzonego udziału kopii zapasowej](#backupshare). W tym przykładzie ** \\ \\<pierwsze SQL Server \> \Backup \\ **. Wybierz pozycję **Dalej**.

   >[!NOTE]
   >Pełna synchronizacja pobiera pełną kopię zapasową bazy danych przy pierwszym wystąpieniu SQL Server i przywraca ją do drugiego wystąpienia. W przypadku dużych baz danych nie zaleca się stosowania pełnej synchronizacji, ponieważ może to zająć dużo czasu. Możesz skrócić ten czas, ręcznie pobierając kopię zapasową bazy danych i przywracając ją z `NO RECOVERY` . Jeśli baza danych została już przywrócona przy użyciu `NO RECOVERY` programu na drugim SQL Server przed skonfigurowaniem grupy dostępności, wybierz pozycję **tylko Dołącz**. Jeśli chcesz wykonać kopię zapasową po skonfigurowaniu grupy dostępności, wybierz pozycję **Pomiń początkową synchronizację danych**.
   >

   ![Wybierz pomijanie początkowej synchronizacji danych](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. Na stronie **Walidacja** wybierz pozycję **dalej**. Ta strona powinna wyglądać podobnie do poniższej ilustracji:

    ![Kreator nowej grupy dostępności, sprawdzanie poprawności](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >Istnieje ostrzeżenie dotyczące konfiguracji odbiornika, ponieważ nie skonfigurowano odbiornika grupy dostępności. To ostrzeżenie można zignorować, ponieważ na maszynach wirtualnych platformy Azure utworzysz odbiornik po utworzeniu modułu równoważenia obciążenia platformy Azure.

10. Na stronie **Podsumowanie** wybierz pozycję **Zakończ**, a następnie zaczekaj, aż Kreator skonfiguruje nową grupę dostępności. Na stronie **postęp** możesz wybrać **więcej szczegółów** , aby wyświetlić szczegółowy postęp. Po zakończeniu pracy kreatora przejrzyj **wyniki** , aby sprawdzić, czy grupa dostępności została pomyślnie utworzona.

     ![Kreator nowej grupy dostępności, wyniki](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Wybierz pozycję **Zamknij** , aby zakończyć pracę kreatora.

### <a name="check-the-availability-group"></a>Sprawdź grupę dostępności

1. W **Eksplorator obiektów**rozwiń opcję **zawsze włączone wysokiej dostępności**, a następnie rozwiń węzeł **grupy dostępności**. Teraz powinna zostać wyświetlona nowa grupa dostępności w tym kontenerze. Kliknij prawym przyciskiem myszy grupę dostępności i wybierz pozycję **Pokaż pulpit nawigacyjny**.

   ![Pokaż pulpit nawigacyjny grupy dostępności](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   **Zawsze włączony pulpit nawigacyjny** powinien wyglądać podobnie do poniższego zrzutu ekranu:

   ![Pulpit nawigacyjny grupy dostępności](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   Można wyświetlić repliki, tryb trybu failover dla każdej repliki i stan synchronizacji.

2. W **Menedżer klastra trybu failover**wybierz klaster. Wybierz pozycję **role**. Użyta nazwa grupy dostępności jest rolą w klastrze. Ta grupa dostępności nie ma adresu IP dla połączeń klientów, ponieważ nie skonfigurowano odbiornika. Odbiornik zostanie skonfigurowany po utworzeniu modułu równoważenia obciążenia platformy Azure.

   ![Grupa dostępności w Menedżer klastra trybu failover](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Nie należy podejmować próby przełączenia grupy dostępności do trybu failover z Menedżer klastra trybu failover. Wszystkie operacje trybu failover powinny być wykonywane z poziomu **pulpitu nawigacyjnego AlwaysOn** w programie SSMS. Aby uzyskać więcej informacji, zobacz [ograniczenia dotyczące używania Menedżer klastra trybu failover z grupami dostępności](https://msdn.microsoft.com/library/ff929171.aspx).
    >

W tym momencie masz grupę dostępności z replikami w dwóch wystąpieniach SQL Server. Można przenieść grupę dostępności między wystąpieniami. Nie można jeszcze nawiązać połączenia z grupą dostępności, ponieważ nie masz odbiornika. W przypadku maszyn wirtualnych platformy Azure odbiornik wymaga modułu równoważenia obciążenia. Następnym krokiem jest utworzenie modułu równoważenia obciążenia na platformie Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Tworzenie modułu równoważenia obciążenia na platformie Azure

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

W przypadku usługi Azure Virtual Machines Grupa dostępności SQL Server wymaga modułu równoważenia obciążenia. Moduł równoważenia obciążenia przechowuje adresy IP dla odbiorników grupy dostępności i klastra trybu failover systemu Windows Server. Ta sekcja zawiera podsumowanie sposobu tworzenia modułu równoważenia obciążenia w Azure Portal.

Moduł równoważenia obciążenia na platformie Azure może być usługa Load Balancer w warstwie Standardowa lub Load Balancer podstawowym. Usługa Load Balancer w warstwie Standardowa ma więcej funkcji niż Load Balancer podstawowa. W przypadku grupy dostępności usługa Load Balancer w warstwie Standardowa jest wymagana w przypadku używania strefy dostępności (zamiast zestawu dostępności). Aby uzyskać szczegółowe informacje na temat różnic między jednostkami SKU modułu równoważenia obciążenia, zobacz [Load Balancer porównanie jednostek SKU](../../../load-balancer/skus.md).

1. W Azure Portal przejdź do grupy zasobów, w której znajdują się serwery SQL, a następnie wybierz pozycję **+ Dodaj**.
1. Wyszukaj **Load Balancer**. Wybierz usługę równoważenia obciążenia opublikowaną przez firmę Microsoft.

   ![Wybierz usługę równoważenia obciążenia opublikowaną przez firmę Microsoft](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Wybierz pozycję **Utwórz**.
1. Skonfiguruj następujące parametry dla modułu równoważenia obciążenia.

   | Ustawienie | Pole |
   | --- | --- |
   | **Nazwa** |Użyj nazwy tekstowej dla modułu równoważenia obciążenia, na przykład **sqlLB**. |
   | **Typ** |Wewnętrzny |
   | **Sieć wirtualna** |Użyj nazwy sieci wirtualnej platformy Azure. |
   | **Podsieć** |Użyj nazwy podsieci, w której znajduje się maszyna wirtualna.  |
   | **Przypisanie adresu IP** |Static |
   | **Adres IP** |Użyj dostępnego adresu z podsieci. Użyj tego adresu dla odbiornika grupy dostępności. Należy zauważyć, że różni się to od adresu IP klastra.  |
   | **Subskrypcja** |Użyj tej samej subskrypcji co maszyna wirtualna. |
   | **Lokalizacja** |Użyj tej samej lokalizacji co maszyna wirtualna. |

   Blok Azure Portal powinien wyglądać następująco:

   ![Utwórz Load Balancer](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Wybierz pozycję **Utwórz**, aby utworzyć moduł równoważenia obciążenia.

Aby skonfigurować moduł równoważenia obciążenia, należy utworzyć pulę zaplecza, sondę i ustawić reguły równoważenia obciążenia. Zrób to w Azure Portal.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Dodawanie puli zaplecza dla odbiornika grupy dostępności

1. W Azure Portal przejdź do grupy dostępności. Może być konieczne odświeżenie widoku w celu wyświetlenia nowo utworzonego modułu równoważenia obciążenia.

   ![Znajdź Load Balancer w grupie zasobów](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Wybierz moduł równoważenia obciążenia, wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **+ Dodaj**.

1. Wpisz nazwę puli zaplecza.

1. Skojarz pulę zaplecza z zestawem dostępności zawierającym maszyny wirtualne.

1. W obszarze **docelowe konfiguracje IP sieci**Sprawdź **maszynę wirtualną** i wybierz obie maszyny wirtualne, które będą hostować repliki grup dostępności. Nie dołączaj serwera monitora udziału plików.

   >[!NOTE]
   >Jeśli obie maszyny wirtualne nie zostaną określone, połączenia będą kończyły się powodzeniem tylko do repliki podstawowej.

1. Wybierz **przycisk OK** , aby utworzyć pulę zaplecza.

### <a name="set-the-probe"></a>Ustawianie sondy

1. Wybierz moduł równoważenia obciążenia, wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **+ Dodaj**.

1. Ustaw sondę kondycji odbiornika w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | SQLAlwaysOnEndPointProbe |
   | **Protokół** | Wybierz pozycję TCP | TCP |
   | **Port** | Dowolny nieużywany port | 59999 |
   | **Interwał**  | Czas między próbami sondy w sekundach |5 |
   | **Próg złej kondycji** | Liczba kolejnych niepowodzeń sondy, które muszą wystąpić, aby maszyna wirtualna mogła zostać uznana za złą  | 2 |

1. Wybierz **przycisk OK** , aby ustawić sondę kondycji.

### <a name="set-the-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

1. Wybierz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia**i wybierz pozycję **+ Dodaj**.

1. Ustaw następujące reguły równoważenia obciążenia odbiornika.

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | SQLAlwaysOnEndPointListener |
   | **Adres IP frontonu** | Wybierz adres |Użyj adresu utworzonego podczas tworzenia modułu równoważenia obciążenia. |
   | **Protokół** | Wybierz pozycję TCP |TCP |
   | **Port** | Użyj portu dla odbiornika grupy dostępności | 1433 |
   | **Port zaplecza** | To pole nie jest używane, gdy jest ustawiany swobodny adres IP dla bezpośredniego powrotu serwera | 1433 |
   | **Badane** |Nazwa określona dla sondy | SQLAlwaysOnEndPointProbe |
   | **Trwałość sesji** | Lista rozwijana | **Brak** |
   | **Limit czasu bezczynności** | Minuty, aby można było otworzyć połączenie TCP | 4 |
   | **Zmienny adres IP (bezpośredni zwrot serwera)** | |Enabled (Włączony) |

   > [!WARNING]
   > Bezpośredni zwrot serwera jest ustawiany podczas tworzenia. Nie można zmienić tej nazwy.
   >

1. Wybierz **przycisk OK** , aby ustawić reguły równoważenia obciążenia odbiornika.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Dodawanie podstawowego adresu IP klastra dla klastra trybu failover systemu Windows Server (WSFC)

Adres IP usługi WSFC musi również znajdować się w usłudze równoważenia obciążenia.

1. W Azure Portal przejdź do tego samego modułu równoważenia obciążenia platformy Azure. Wybierz pozycję **Konfiguracja adresu IP frontonu** i wybierz pozycję **+ Dodaj**. Użyj adresu IP skonfigurowanego dla usługi WSFC w zasobach podstawowych klastra. Ustaw adres IP jako statyczny.

1. W module równoważenia obciążenia wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **+ Dodaj**.

1. Ustaw sondę kondycji podstawowego adresu IP klastra usługi WSFC w następujący sposób:

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | WSFCEndPointProbe |
   | **Protokół** | Wybierz pozycję TCP | TCP |
   | **Port** | Dowolny nieużywany port | 58888 |
   | **Interwał**  | Czas między próbami sondy w sekundach |5 |
   | **Próg złej kondycji** | Liczba kolejnych niepowodzeń sondy, które muszą wystąpić, aby maszyna wirtualna mogła zostać uznana za złą  | 2 |

1. Wybierz **przycisk OK** , aby ustawić sondę kondycji.

1. Ustaw reguły równoważenia obciążenia. Wybierz pozycję **reguły równoważenia obciążenia**i wybierz pozycję **+ Dodaj**.

1. Ustaw następujące reguły równoważenia obciążenia podstawowego adresu IP klastra.

   | Ustawienie | Opis | Przykład
   | --- | --- |---
   | **Nazwa** | Tekst | WSFCEndPoint |
   | **Adres IP frontonu** | Wybierz adres |Użyj adresu utworzonego podczas konfigurowania adresu IP usługi WSFC. Różni się to od adresu IP odbiornika |
   | **Protokół** | Wybierz pozycję TCP |TCP |
   | **Port** | Użyj portu dla adresu IP klastra. Jest to dostępny port, który nie jest używany przez port sondy odbiornika. | 58888 |
   | **Port zaplecza** | To pole nie jest używane, gdy jest ustawiany swobodny adres IP dla bezpośredniego powrotu serwera | 58888 |
   | **Badane** |Nazwa określona dla sondy | WSFCEndPointProbe |
   | **Trwałość sesji** | Lista rozwijana | **Brak** |
   | **Limit czasu bezczynności** | Minuty, aby można było otworzyć połączenie TCP | 4 |
   | **Zmienny adres IP (bezpośredni zwrot serwera)** | |Enabled (Włączony) |

   > [!WARNING]
   > Bezpośredni zwrot serwera jest ustawiany podczas tworzenia. Nie można zmienić tej nazwy.
   >

1. Wybierz **przycisk OK** , aby ustawić reguły równoważenia obciążenia.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Konfigurowanie odbiornika

Następnym krokiem jest skonfigurowanie odbiornika grupy dostępności w klastrze trybu failover.

> [!NOTE]
> W tym samouczku pokazano, jak utworzyć pojedynczy odbiornik z jednym ILB adresem IP. Aby utworzyć jeden lub więcej odbiorników przy użyciu co najmniej jednego adresu IP, zobacz [Tworzenie odbiornika i równoważenia obciążenia grupy dostępności | Na platformie Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Ustaw port odbiornika

W SQL Server Management Studio Ustaw port odbiornika.

1. Uruchom SQL Server Management Studio i Połącz się z repliką podstawową.

1. Przejdź do funkcji AlwaysOn grup dostępności dla grup dostępności **o wysokiej dostępności**  >  **availability groups**  >  **availability group Listeners**.

1. Powinna zostać wyświetlona nazwa odbiornika utworzona w Menedżer klastra trybu failover. Kliknij prawym przyciskiem myszy nazwę odbiornika i wybierz pozycję **Właściwości**.

1. W polu **port** Określ numer portu dla odbiornika grupy dostępności. 1433 jest wartością domyślną. Wybierz przycisk **OK**.

Masz teraz SQL Server grupę dostępności w usłudze Azure Virtual Machines działającej w trybie Menedżer zasobów.

## <a name="test-connection-to-listener"></a>Test connection do odbiornika

Aby przetestować połączenie:

1. Użyj protokołu RDP, aby nawiązać połączenie z SQL Server, który znajduje się w tej samej sieci wirtualnej, ale nie jest to replika. Możesz użyć innych SQL Server w klastrze.

1. Przetestuj połączenie przy użyciu narzędzia **sqlcmd** . Na przykład poniższy skrypt ustanawia połączenie **sqlcmd** z repliką podstawową za pomocą odbiornika z uwierzytelnianiem systemu Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Jeśli odbiornik używa portu innego niż domyślny port (1433), określ port w parametrach połączenia. Na przykład następujące `sqlcmd` polecenie nawiązuje połączenie z odbiornikiem w porcie 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

Połączenie SQLCMD jest automatycznie nawiązywane z niezależnym wystąpieniem SQL Server hostuje replikę podstawową.

> [!TIP]
> Upewnij się, że określony port jest otwarty na zaporze obu serwerów SQL. Oba serwery wymagają reguły ruchu przychodzącego dla używanego portu TCP. Aby uzyskać więcej informacji, zobacz [Dodawanie lub Edytowanie reguły zapory](https://technet.microsoft.com/library/cc753558.aspx).
>

## <a name="next-steps"></a>Następne kroki

- [Dodaj adres IP do modułu równoważenia obciążenia dla drugiej grupy dostępności](availability-group-listener-powershell-configure.md#Add-IP).
