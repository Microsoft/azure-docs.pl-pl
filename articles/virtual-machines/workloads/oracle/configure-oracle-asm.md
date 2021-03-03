---
title: Konfigurowanie programu Oracle ASM na maszynie wirtualnej platformy Azure z systemem Linux | Microsoft Docs
description: Szybko Uzyskaj oprogramowanie Oracle ASM w środowisku platformy Azure.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: b180a0f148db5222a288958724ad248acd18b565
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673861"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Konfigurowanie programu Oracle ASM na maszynie wirtualnej platformy Azure z systemem Linux  

Maszyny wirtualne platformy Azure oferują w pełni konfigurowalne i elastyczne środowiska obliczeniowe. W tym samouczku opisano podstawowe wdrożenie maszyn wirtualnych platformy Azure połączone z instalacją i konfiguracją zautomatyzowanego zarządzania magazynem (ASM) firmy Oracle.  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej Oracle Database i nawiązywanie z nią połączenia
> * Instalowanie i Konfigurowanie zautomatyzowanego zarządzania magazynem firmy Oracle
> * Instalowanie i Konfigurowanie infrastruktury siatki Oracle
> * Inicjowanie instalacji programu Oracle ASM
> * Tworzenie Oracle DB zarządzane przez ASM


Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć grupę zasobów, użyj polecenia [AZ Group Create](/cli/azure/group) . Grupa zasobów platformy Azure to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure. W tym przykładzie grupa zasobów o nazwie Moja *zasobów* w regionie *wschodnim* .

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną na podstawie obrazu Oracle Database i skonfigurować ją do korzystania z programu Oracle ASM, użyj polecenia [AZ VM Create](/cli/azure/vm) . 

Poniższy przykład tworzy maszynę wirtualną o nazwie myVM, która ma rozmiar Standard_DS2_v2 z czterema dołączonymi dyskami danych wynoszącymi 50 GB każdego. Jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza, tworzy również klucze SSH.  Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Po utworzeniu maszyny wirtualnej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do poniższego przykładu. Zwróć uwagę na wartość parametru `publicIpAddress` . Ten adres jest używany do uzyskiwania dostępu do maszyny wirtualnej.

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Aby utworzyć sesję SSH z maszyną wirtualną i skonfigurować dodatkowe ustawienia, użyj następującego polecenia. Zastąp adres IP `publicIpAddress` wartością dla maszyny wirtualnej.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalowanie programu Oracle ASM

Aby zainstalować Oracle ASM, wykonaj następujące czynności. 

Aby uzyskać więcej informacji na temat instalowania oprogramowania Oracle ASM, zobacz artykuł [pobieranie z programu Oracle ASMLib dla Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Aby kontynuować instalację ASM, należy zalogować się jako katalog główny:

   ```bash
   sudo su -
   ```
   
2. Uruchom te dodatkowe polecenia, aby zainstalować składniki programu Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Sprawdź, czy jest zainstalowany Oracle ASM:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Dane wyjściowe tego polecenia powinny wyświetlać następujące składniki:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. Funkcja ASM wymaga określonych użytkowników i ról w celu poprawnego działania. Następujące polecenia tworzą wstępnie wymagane konta i grupy użytkowników: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Weryfikowanie, czy pomyślnie utworzono użytkowników i grupy:

   ```bash
   id grid
   ```

    Dane wyjściowe tego polecenia powinny wyświetlać listę następujących użytkowników i grup:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Utwórz folder dla *siatki* użytkowników i Zmień właściciela:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Konfigurowanie programu Oracle ASM

W tym samouczku domyślny użytkownik jest *siatką* , a domyślną grupą jest *asmadmin*. Upewnij się, że użytkownik *Oracle* jest częścią grupy asmadmin. Aby skonfigurować instalację programu Oracle ASM, wykonaj następujące czynności:

1. Ustawienie sterownika biblioteki ASM firmy Oracle obejmuje zdefiniowanie domyślnego użytkownika (siatki) i grupy domyślnej (asmadmin), a także skonfigurowanie dysku do uruchamiania przy rozruchu (wybierz y) i przeskanowanie w poszukiwaniu dysków przy rozruchu (wybierz y). Musisz odpowiedzieć na pytania przy użyciu następującego polecenia:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Dane wyjściowe tego polecenia powinny wyglądać podobnie do poniższego, zatrzymywać z monitami o odpowiedź.

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Wyświetl konfigurację dysku:

   ```bash
   cat /proc/partitions
   ```

   Dane wyjściowe tego polecenia powinny wyglądać podobnie do poniższej listy dostępnych dysków

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Sformatuj dysk */dev/SDC* , uruchamiając następujące polecenie i udzielając odpowiedzi na pytania:
   - *n* dla nowej partycji
   - *p* dla partycji podstawowej
   - *1* , aby wybrać pierwszą partycję
   - Naciśnij klawisz `enter` , aby uzyskać domyślną pierwszą cylinder
   - Naciśnij klawisz `enter` , aby uzyskać domyślną ostatnią cylinder
   - Naciśnij klawisz *w* , aby zapisać zmiany w tabeli partycji  

   ```bash
   fdisk /dev/sdc
   ```
   
   Korzystając z odpowiedzi podanych powyżej, dane wyjściowe `fdisk` polecenia powinny wyglądać następująco:

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Powtórz poprzednie `fdisk` polecenie dla `/dev/sdd` , `/dev/sde` , i `/dev/sdf` .

5. Sprawdź konfigurację dysku:

   ```bash
   cat /proc/partitions
   ```

   Dane wyjściowe polecenia powinny wyglądać następująco:

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Sprawdź stan usługi Oracle ASM i uruchom usługę Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Dane wyjściowe polecenia powinny wyglądać następująco:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Utwórz dyski programu Oracle ASM:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   Dane wyjściowe polecenia powinny wyglądać następująco:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Wyświetl listę dysków bazy danych ASM firmy Oracle:

   ```bash
   service oracleasm listdisks
   ```

   Dane wyjściowe polecenia powinny wystawiać następujące dyski systemu Oracle ASM:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Zmień hasła dla użytkowników root, Oracle i Grid. **Zanotuj te nowe hasła** , ponieważ są one używane później podczas instalacji.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Zmień uprawnienie do folderu:

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Pobieranie i przygotowanie infrastruktury siatki Oracle

Aby pobrać i przygotować oprogramowanie infrastruktury siatki Oracle, wykonaj następujące czynności:

1. Pobierz infrastrukturę usługi Oracle Grid ze [strony pobierania programu Oracle ASM](https://www.oracle.com/database/technologies/oracle19c-linux-downloads.html). 

   W obszarze Pobieranie zatytułowane **Oracle Database 12C wersja 1 Grid Infrastructure (12.1.0.2.0) dla systemu Linux x86-64** Pobierz dwa pliki. zip.

2. Po pobraniu plików zip na komputer kliencki można użyć protokołu Secure Copy Protocol (SCP) do skopiowania plików na maszynę wirtualną:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. Połącz się z powrotem z maszyną wirtualną Oracle na platformie Azure, aby przenieść pliki zip do folderu/opt. Następnie Zmień właściciela plików:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Rozpakuj pliki. (Zainstaluj narzędzie rozpakować systemu Linux, jeśli nie jest jeszcze zainstalowane).

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Zmień uprawnienie:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Aktualizuj skonfigurowany obszar wymiany. Składniki siatki Oracle wymagają co najmniej 6,8 GB miejsca do wymiany w celu zainstalowania siatki. Domyślny rozmiar pliku wymiany dla obrazów Oracle Linux na platformie Azure jest tylko 2048 MB pamięci. Aby `ResourceDisk.SwapSizeMB` `/etc/waagent.conf` zaktualizować ustawienia zaczęły obowiązywać, należy zwiększyć plik i ponownie uruchomić usługę WALinuxAgent. Ponieważ jest to plik tylko do odczytu, należy zmienić uprawnienia do pliku, aby umożliwić dostęp do zapisu.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Wyszukaj `ResourceDisk.SwapSizeMB` i zmień wartość na **8192**. Należy nacisnąć klawisz `insert` , aby wprowadzić tryb wstawiania, wpisz wartość **8192** , a następnie naciśnij klawisz `esc` , aby powrócić do trybu polecenia. Aby zapisać zmiany i zamknąć plik, wpisz `:wq` i naciśnij klawisz `enter` .
   
   > [!NOTE]
   > Zdecydowanie zalecamy, aby `WALinuxAgent` skonfigurować przestrzeń wymiany tak, aby była zawsze tworzona na lokalnym dysku tymczasowym (dysku tymczasowego) w celu uzyskania najlepszej wydajności. Aby uzyskać więcej informacji na temat, zobacz [jak dodać plik wymiany na maszynach wirtualnych systemu Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Przygotuj lokalnego klienta i maszynę wirtualną do uruchomienia X11
Skonfigurowanie systemu Oracle ASM wymaga interfejsu graficznego do ukończenia instalacji i konfiguracji. W celu ułatwienia tej instalacji korzystamy z protokołu X11. W przypadku korzystania z systemu klienckiego (Mac lub Linux), który ma już włączone i skonfigurowane możliwości X11, można pominąć tę konfigurację i konfigurację wyłącznie na maszynach z systemem Windows. 

1. [Pobierz](https://www.putty.org/) i [Pobierz Xming](https://xming.en.softonic.com/) na komputer z systemem Windows. Przed kontynuowaniem należy ukończyć instalację obu tych aplikacji z wartościami domyślnymi.

2. Po zainstalowaniu programu, Otwórz wiersz polecenia, przejdź do folderu podano (na przykład C:\Program Files\PuTTY) i uruchom polecenie `puttygen.exe` w celu wygenerowania klucza.

3. W oknie generator kluczy:
   
   1. Wygeneruj klucz, wybierając `Generate` przycisk.
   2. Skopiuj zawartość klucza (Ctrl + C).
   3. Wybierz przycisk `Save private key`.
   4. Zignoruj ostrzeżenie o zabezpieczaniu klucza hasłem, a następnie wybierz opcję `OK` .

   ![Zrzut ekranu przedstawiający generator kluczy](./media/oracle-asm/puttykeygen.png)

4. Na maszynie wirtualnej Uruchom następujące polecenia:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Utwórz plik o nazwie `authorized_keys`. Wklej zawartość klucza w tym pliku, a następnie Zapisz plik.

   > [!NOTE]
   > Klucz musi zawierać ciąg `ssh-rsa` . Ponadto zawartość klucza musi być pojedynczym wierszem tekstu.
   >  

6. Na komputerze klienckim uruchom polecenie wylogowania. W okienku **Kategoria** przejdź do pozycji **połączenie**  >  **SSH**  >  **AUTH**. W polu **plik klucza prywatnego dla uwierzytelniania** przejdź do wygenerowanego wcześniej klucza.

   ![Zrzut ekranu opcji uwierzytelniania SSH](./media/oracle-asm/setprivatekey.png)

7. W okienku **Kategoria** przejdź do pozycji **połączenie**  >  **SSH**  >  **X11**. Zaznacz pole wyboru **Włącz przekazywanie X11** .

   ![Zrzut ekranu przedstawiający opcje przekazywania SSH X11](./media/oracle-asm/enablex11.png)

8. W okienku **Kategoria** przejdź do **sesji**. W oknie dialogowym Nazwa hosta wprowadź swoją maszynę wirtualną Oracle ASM `<publicIPaddress>` , Wypełnij nową `Saved Session` nazwę, a następnie kliknij przycisk Włącz `Save` .  Po zapisaniu kliknij przycisk Włącz, `open` Aby nawiązać połączenie z maszyną wirtualną programu Oracle ASM.  Przy pierwszym połączeniu zostanie wyświetlone ostrzeżenie, że system zdalny nie jest buforowany w rejestrze. Kliknij pozycję Włącz `yes` , aby ją dodać i kontynuować.

   ![Zrzut ekranu przedstawiający opcje sesji](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalowanie infrastruktury siatki Oracle

Aby zainstalować infrastrukturę sieci Oracle, wykonaj następujące czynności:

1. Zaloguj się jako **Siatka**. (Powinno być możliwe zalogowanie się bez monitowania o hasło). 

   > [!NOTE]
   > Jeśli używasz systemu Windows, przed rozpoczęciem instalacji upewnij się, że uruchomiono Xming.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Zostanie otwarty Instalator programu Oracle Grid Infrastructure 12c w wersji 1. (Uruchomienie Instalatora może potrwać kilka minut).

2. Na stronie **Wybierz opcję instalacji** wybierz opcję **Zainstaluj i skonfiguruj infrastrukturę usługi Oracle Grid dla serwera autonomicznego**.

   ![Zrzut ekranu przedstawiający stronę opcji instalacji w instalatorze](./media/oracle-asm/install01.png)

3. Na stronie **Wybierz Języki produktu** upewnij się, że wybrany język to **angielski** lub żądany  Kliknij pozycję `next` (Dalej).

4. Na stronie **Tworzenie grupy dysków ASM** :
   - Wprowadź nazwę grupy dysków.
   - W obszarze **nadmiarowość** wybierz pozycję **zewnętrzny**.
   - W obszarze **rozmiar jednostki alokacji** wybierz pozycję **4**.
   - W obszarze **Dodawanie dysków** wybierz pozycję **ORCLASMSP**.
   - Kliknij pozycję `next` (Dalej).

5. Na stronie **Określanie hasła ASM** wybierz opcję **Użyj tych samych haseł dla tych kont** , a następnie wprowadź hasło.

   ![Zrzut ekranu przedstawiający stronę Określanie hasła ASM Instalatora](./media/oracle-asm/install04.png)

6. Na stronie **Określanie opcji zarządzania** dostępna jest opcja KONFIGUROWANIA usługi em Cloud Control. Pomijamy tę opcję — kliknij `next` , aby kontynuować. 

7. Na stronie **uprzywilejowane grupy systemu operacyjnego** Użyj ustawień domyślnych. Kliknij `next` , aby kontynuować.

8. Na stronie **Określ lokalizację instalacji** Użyj ustawień domyślnych. Kliknij `next` , aby kontynuować.

9. Na stronie **Tworzenie spisu** Zmień katalog spisu na `/u01/app/grid/oraInventory` . Kliknij `next` , aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę tworzenia spisu przez Instalatora](./media/oracle-asm/install08.png)

10. Na stronie **Konfiguracja wykonywania skryptu głównego** zaznacz pole wyboru **automatycznie Uruchom skrypty konfiguracyjne** . Następnie wybierz opcję **Użyj poświadczeń użytkownika root** , a następnie wprowadź hasło użytkownika root.

    ![Zrzut ekranu przedstawiający stronę konfiguracji wykonywania skryptu głównego Instalatora](./media/oracle-asm/install09.png)

11. Na stronie **Sprawdzanie wymagań wstępnych** Bieżąca konfiguracja zakończy się niepowodzeniem z błędami. Jest to oczekiwane zachowanie. Wybierz pozycję `Fix & Check Again`.

12. W oknie dialogowym **skrypt korekty** kliknij pozycję `OK` .

13. Na stronie **Podsumowanie** Przejrzyj wybrane ustawienia, a następnie kliknij przycisk `Install` .

    ![Zrzut ekranu przedstawiający stronę podsumowania Instalatora](./media/oracle-asm/install12.png)

14. Wyświetlane jest okno dialogowe ostrzeżenia z informacją o tym, że skrypty konfiguracyjne muszą być uruchamiane jako użytkownik uprzywilejowany. Kliknij `Yes` , aby kontynuować.

15. Na stronie **zakończenie** kliknij przycisk, `Close` Aby zakończyć instalację.

## <a name="set-up-your-oracle-asm-installation"></a>Konfigurowanie instalacji programu Oracle ASM

Aby skonfigurować instalację programu Oracle ASM, wykonaj następujące czynności:

1. Upewnij się, że nadal zalogowano się jako **Siatka**, z sesji X11. Może być konieczne przekroczenie `enter` , aby przywrócić Terminal. Następnie Uruchom Asystenta konfiguracji zautomatyzowanego zarządzania magazynem firmy Oracle:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Zostanie otwarty Asystent konfiguracji programu Oracle ASM.

2. W oknie dialogowym **Konfigurowanie grup ASM: dysków** kliknij `Create` przycisk, a następnie kliknij przycisk `Show Advanced Options` .

3. W oknie dialogowym **Tworzenie grupy dysków** :

   - Wprowadź **dane** w polu Nazwa grupy dysków.
   - W obszarze **Wybierz dyski Członkowskie** wybierz pozycję **ORCL_DATA** i **ORCL_DATA1**.
   - W obszarze **rozmiar jednostki alokacji** wybierz pozycję **4**.
   - Kliknij, `ok` Aby utworzyć grupę dysków.
   - Kliknij, `ok` Aby zamknąć okno potwierdzenia.

   ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie grupy dysków](./media/oracle-asm/asm02.png)

4. W oknie dialogowym **Konfigurowanie grup ASM: dysków** kliknij `Create` przycisk, a następnie kliknij przycisk `Show Advanced Options` .

5. W oknie dialogowym **Tworzenie grupy dysków** :

   - Wprowadź nazwę grupy dysków **FRA**.
   - W obszarze **nadmiarowość** wybierz pozycję **zewnętrzny (brak)**.
   - W obszarze **Wybierz dyski Członkowskie** wybierz pozycję **ORCL_FRA**.
   - W obszarze **rozmiar jednostki alokacji** wybierz pozycję **4**.
   - Kliknij, `ok` Aby utworzyć grupę dysków.
   - Kliknij, `ok` Aby zamknąć okno potwierdzenia.

   ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie grupy dysków i wyróżnienie opcji zewnętrzny (brak).](./media/oracle-asm/asm04.png)

6. Wybierz pozycję **Zakończ** , aby zamknąć Asystenta konfiguracji ASM.

   ![Zrzut ekranu przedstawiający okno dialogowe Konfiguracja ASM: grupy dysków z przyciskiem Zakończ](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Tworzenie bazy danych

Oprogramowanie bazy danych Oracle jest już zainstalowane w obrazie portalu Azure Marketplace. Aby utworzyć bazę danych, wykonaj następujące czynności:

1. Przełącz użytkowników do programu Oracle administratora, a następnie zainicjuj odbiornik do rejestrowania:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Zostanie otwarty Asystent konfiguracji bazy danych.

2. Na stronie **operacja bazy danych** kliknij pozycję `Create Database` .

3. Na stronie **tryb tworzenia** :

   - Wprowadź nazwę bazy danych.
   - W przypadku **typu magazynu** upewnij się, że jest wybrana **Funkcja automatycznego zarządzania magazynem (ASM)** .
   - W polu **Lokalizacja plików bazy danych** Użyj domyślnej sugerowanej lokalizacji ASM.
   - W przypadku **obszaru szybkiego odzyskiwania** Użyj domyślnej sugerowanej lokalizacji ASM.
   - Wpisz **hasło administracyjne** i **Potwierdź hasło**.
   - Upewnij `create as container database` się, że wybrano.
   - Wpisz `pluggable database name` wartość.

4. Na stronie **Podsumowanie** Przejrzyj wybrane ustawienia, a następnie kliknij, `Finish` Aby utworzyć bazę danych.

   ![Zrzut ekranu przedstawiający stronę Podsumowanie](./media/oracle-asm/createdb03.png)

5. Baza danych została utworzona. Na stronie **zakończenie** można odblokować dodatkowe konta, aby użyć tej bazy danych i zmienić hasła. Jeśli chcesz to zrobić, wybierz pozycję **Zarządzanie hasłami** — w przeciwnym razie kliknij pozycję włączone `close` .

## <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Pomyślnie skonfigurowano automatyczne zarządzanie magazynem danych Oracle na obrazie Oracle DB w portalu Azure Marketplace.  Gdy ta maszyna wirtualna nie jest już potrzebna, możesz użyć następującego polecenia, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

[Samouczek: Konfigurowanie bazy danych Oracle DataGuard](configure-oracle-dataguard.md)

[Samouczek: Konfigurowanie programu Oracle GoldenGate](Configure-oracle-golden-gate.md)

Przeglądanie [Oracle DB architekta](oracle-design.md)
