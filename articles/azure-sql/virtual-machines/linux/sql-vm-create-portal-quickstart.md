---
title: 'Szybki Start: Tworzenie maszyny wirtualnej z systemem Linux SQL Server na platformie Azure'
description: W tym samouczku pokazano sposób tworzenia maszyny wirtualnej z programem SQL Server 2017 i systemem Linux na platformie Azure.
services: virtual-machines-linux
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fd5c289f2b441b5862d863d9a390a1cd054acbfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790155"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem Linux SQL Server w Azure Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

W tym samouczku Szybki start użyjesz witryny Azure Portal do utworzenia maszyny wirtualnej z systemem Linux z zainstalowanym programem SQL Server 2017. Poznasz następujące informacje: 


* [Tworzenie maszyny wirtualnej z systemem Linux z SQL Server z galerii](#create)
* [Łączenie z nową maszyną wirtualną przy użyciu protokołu SSH](#connect)
* [Zmienianie hasła administratora systemu](#password)
* [Konfiguruj dla połączeń zdalnych](#remote)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a> Tworzenie maszyny wirtualnej z systemem Linux i zainstalowanym programem SQL Server

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

1. W okienku **Tworzenie zasobu** wybierz pozycję **Obliczenia**.

1. Wybierz przycisk **Zobacz wszystko** obok nagłówka **Polecane**.

   ![Wyświetlanie wszystkich obrazów maszyn wirtualnych](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. W polu wyszukiwania wpisz **SQL Server 2019**, a następnie wybierz **klawisz ENTER** , aby rozpocząć wyszukiwanie.

1. Ogranicz wyniki wyszukiwania, wybierając pozycję **system operacyjny**  >  **RedHat**.

    ![Filtr wyszukiwania dla obrazów maszyn wirtualnych z SQL Server 2019](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Wybierz obraz systemu Linux w systemie SQL Server 2019 z wyników wyszukiwania. Ten samouczek używa **SQL Server 2019 w RHEL74**.

   > [!TIP]
   > Wersja Developer umożliwia testowanie lub programowanie przy użyciu funkcji wersji Enterprise, ale bez kosztów licencjonowania programu SQL Server. Ponosisz tylko koszty obsługi maszyny wirtualnej z systemem Linux.

1. Wybierz przycisk **Utwórz**. 


### <a name="set-up-your-linux-vm"></a>Konfigurowanie maszyny wirtualnej z systemem Linux

1. Na karcie **Ustawienia podstawowe** wybierz **Subskrypcję** i **Grupę zasobów**. 

    ![Okno Podstawowe informacje](./media/sql-vm-create-portal-quickstart/basics.png)

1. W polu **Nazwa maszyny wirtualnej** wprowadź nazwę nowej maszyny wirtualnej z systemem Linux.
1. Następnie wpisz lub wybierz następujące wartości:
   * **Region**: wybierz odpowiedni region platformy Azure.
   * **Opcje dostępności**: wybierz opcję dostępność i nadmiarowość, która jest Najlepsza dla aplikacji i danych.
   * **Zmień rozmiar**: Wybierz tę opcję, aby wybrać rozmiar maszyny, a następnie wybierz **pozycję Wybierz**. Aby uzyskać więcej informacji o rozmiarach maszyn wirtualnych, zobacz [rozmiary maszyn](../../../virtual-machines/sizes.md)wirtualnych.

     ![Wybieranie rozmiaru maszyny wirtualnej](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > Do programowania i testowania funkcjonalnego wybierz rozmiar maszyny wirtualnej **DS2** lub większy. Do testowania wydajnościowego należy użyć rozmiaru **DS13** lub większego.

   * **Typ uwierzytelniania**: wybierz opcję **klucz publiczny SSH**.

     > [!Note]
     > Do uwierzytelniania możesz używać klucza publicznego SSH lub hasła. Protokół SSH jest bezpieczniejszy. Aby uzyskać instrukcje na temat sposobu generowania klucza SSH, zobacz [Create SSH keys on Linux and Mac for Linux VMs in Azure (Tworzenie kluczy SSH w systemie Linux i na komputerach Mac dla maszyn wirtualnych z systemem Linux na platformie Azure)](../../../virtual-machines/linux/mac-create-ssh-keys.md).

   * **Nazwa użytkownika**: Wprowadź nazwę administratora maszyny wirtualnej.
   * **Klucz publiczny SSH**: Wprowadź klucz publiczny RSA.
   * **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty** i wybierz port **SSH (22)** na liście **Wybierz publiczne porty przychodzące** . W tym przewodniku Szybki start ten krok jest konieczny do połączenia z programem SQL Server i ukończenia jego konfiguracji. Jeśli chcesz zdalnie nawiązać połączenie z usługą SQL Server, musisz ręcznie zezwolić na ruch przychodzący do portu domyślnego (1433), który będzie używany przez Microsoft SQL Server dla połączeń przez Internet po utworzeniu maszyny wirtualnej.

     ![Porty wejściowe](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Wprowadź wszelkie wymagane zmiany ustawień na następujących kartach dodatkowych lub zachowaj ustawienia domyślne.
    * **Dyski**
    * **Sieć**
    * **Zarządzanie**
    * **Konfiguracja gościa**
    * **Tagi**

1. Wybierz pozycję **Przejrzyj i utwórz**.
1. W okienku **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> Łączenie z maszyną wirtualną z systemem Linux

Jeśli już używasz powłoki BASH, połącz się z maszyną wirtualną platformy Azure przy użyciu polecenia **ssh**. W poniższym poleceniu zastąp nazwę użytkownika i adres IP maszyny wirtualnej, aby nawiązać połączenie z maszyną wirtualną z systemem Linux.

```bash
ssh azureadmin@40.55.55.555
```

Adres IP swojej maszyny wirtualnej możesz znaleźć w witrynie Azure Portal.

![Adres IP w witrynie Azure Portal](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Jeśli używasz systemu Windows i nie masz powłoki BASH, zainstaluj klienta SSH, takiego jak PuTTY.

1. [Pobierz i zainstaluj program PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Uruchom program PuTTY.

1. Na ekranie konfiguracji programu PuTTY wprowadź publiczny adres IP maszyny wirtualnej.

1. Wybierz przycisk **Open** (Otwórz), a następnie po wyświetleniu monitów wprowadź nazwę użytkownika i hasło.

Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszynami wirtualnymi z systemem Linux, zobacz [Create a Linux VM on Azure using the Portal (Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu portalu)](../../../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Jeśli w programie PuTTY zostanie wyświetlony alert dotyczący zabezpieczeń z informacją „server’s host key not cached in the registry” (klucz hosta serwera nie jest buforowany w rejestrze), wybierz jedną z następujących opcji. Jeśli host jest zaufany, wybierz pozycję **Yes** (Tak), aby dodać ten klucz do pamięci podręcznej programu PuTTy i kontynuować nawiązywanie połączenia. Jeśli chcesz nawiązać połączenie tylko raz, bez dodawania klucza do pamięci podręcznej, wybierz pozycję **No** (Nie). Jeśli host nie jest zaufany, wybierz pozycję **Cancel** (Anuluj), aby przerwać nawiązywanie połączenia.

## <a name="change-the-sa-password"></a><a id="password"></a> Zmienianie hasła administratora systemu

Nowa maszyna wirtualna instaluje program SQL Server z losowym hasłem administratora systemu. Zresetuj to hasło, zanim połączysz się z programem SQL Server za pomocą danych logowania administratora systemu.

1. Po połączeniu ze swoją maszyną wirtualną z systemem Linux otwórz nowy terminal poleceń.

1. Zmień hasło administratora systemu za pomocą następujących poleceń:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Wprowadź nowe hasło administratora systemu i potwierdź hasło po wyświetleniu monitu.

1. Uruchom ponownie usługę SQL Server.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Dodawanie narzędzi do swojej ścieżki (opcjonalne)

Kilka [pakietów](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) programu SQL Server zostanie domyślnie zainstalowanych, w tym pakiet narzędzi wiersza polecenia programu SQL Server. Pakiet narzędzi zawiera narzędzia **sqlcmd** i **bcp**. Dla wygody możesz opcjonalnie dodać ścieżkę narzędzi, `/opt/mssql-tools/bin/`, do swojej zmiennej środowiskowej **PATH**.

1. Uruchom następujące polecenia, aby zmodyfikować zmienną **PATH** zarówno dla sesji logowania, jak i sesji interaktywnych/bez logowania:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> Konfigurowanie pod kątem połączeń zdalnych

Jeśli potrzebujesz zdalnie połączyć się z programem SQL Server na maszynie wirtualnej platformy Azure, musisz skonfigurować regułę ruchu przychodzącego w sieciowej grupie zabezpieczeń. Reguła zezwala na ruch przez port, na którym nasłuchuje program SQL Server (domyślnie 1433). Poniższe kroki pokazują, jak korzystać z witryny Azure Portal w tym kroku.

> [!TIP]
> Jeśli podczas inicjowania obsługi w oknie Ustawienia wybrano port wejściowy **MS SQL (1433)**, te zmiany zostały wprowadzone automatycznie. Możesz przejść do kolejnej sekcji, która dotyczy konfigurowania zapory.

1. W portalu wybierz pozycję **Maszyny wirtualne**, a następnie wybierz swoją maszynę wirtualną z programem SQL Server.
1. W okienku nawigacji po lewej stronie w obszarze **Wstawienia** wybierz pozycję **Sieć**.
1. W oknie Sieć wybierz przycisk **Dodaj port wejściowy** w obszarze **Reguły portów wejściowych**.

   ![Reguły portów wejściowych](./media/sql-vm-create-portal-quickstart/networking.png)

1. Na liście **Usługa** wybierz pozycję **MS SQL**.

    ![Reguła grupy zabezpieczeń MS SQL](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. Kliknij przycisk **OK**, aby zapisać regułę dla swojej maszyny wirtualnej.

### <a name="open-the-firewall-on-rhel"></a>Otwieranie zapory w systemie RHEL

Ten samouczek pomógł utworzyć maszynę wirtualną z systemem Red Hat Enterprise Linux (RHEL). Jeśli chcesz łączyć się zdalnie z maszynami wirtualnymi z systemem RHEL, musisz też otworzyć port 1433 w zaporze systemu Linux.

1. [Połącz się](#connect) ze swoją maszyną wirtualną z systemem RHEL.

1. W powłoce BASH uruchom następujące polecenia:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz maszynę wirtualną z programem SQL Server 2017 na platformie Azure, możesz się połączyć lokalnie z narzędziem **sqlcmd**, aby uruchamiać zapytania Transact-SQL.

Jeśli maszynę wirtualną platformy Azure skonfigurowano pod kątem połączeń zdalnych programu SQL Server, powinno być możliwe zdalne łączenie się z tą maszyną. Aby zapoznać się z przykładem, jak połączyć się zdalnie z programem SQL Server w systemie Linux z systemu Windows, zobacz [Use SSMS on Windows to connect to SQL Server on Linux (Używanie aplikacji SSMS w systemie Windows, aby połączyć się z programem SQL Server w systemie Linux)](/sql/linux/sql-server-linux-develop-use-ssms). Aby połączyć się z programem Visual Studio Code, zobacz [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server (Używanie programu Visual Studio Code do tworzenia i uruchamiania skryptów języka Transact-SQL dla programu SQL Server)](/sql/linux/sql-server-linux-develop-use-vscode)

Aby uzyskać więcej ogólnych informacji na temat programu SQL Server w systemie Linux, zobacz [Overview of SQL Server 2017 on Linux (Omówienie programu SQL Server 2017 w systemie Linux)](/sql/linux/sql-server-linux-overview). Aby uzyskać więcej informacji o używaniu maszyn wirtualnych z programem SQL Server 2017 i systemem Linux, zobacz [Overview of SQL Server 2017 virtual machines on Azure (Omówienie maszyn wirtualnych z programem SQL Server 2017 na platformie Azure)](sql-server-on-linux-vm-what-is-iaas-overview.md).