---
title: Konfigurowanie programu Oracle ASM na maszynie wirtualnej systemu Azure Linux | Dokumenty firmy Microsoft
description: Szybkie uruchamianie rozwiązania Oracle ASM w środowisku platformy Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: aa65b789d02c60ef6042aa62e1c138c0e1bd7224
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676895"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Konfigurowanie programu Oracle ASM na maszynie wirtualnej systemu Azure Linux  

Maszyny wirtualne platformy Azure oferują w pełni konfigurowalne i elastyczne środowiska obliczeniowe. W tym samouczku opisano podstawowe wdrażanie maszyny wirtualnej platformy Azure w połączeniu z instalacją i konfiguracją oracle automated storage management (ASM).  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie maszyny Wirtualnej bazy danych Oracle Database i łączenie się z nią
> * Instalowanie i konfigurowanie automatycznego zarządzania pamięcią masową Oracle
> * Instalowanie i konfigurowanie infrastruktury Oracle Grid
> * Inicjowanie instalacji Oracle ASM
> * Tworzenie bazy danych Oracle DB zarządzanej przez ASM


Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby utworzyć grupę zasobów, użyj polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to kontener logiczny, w którym zasoby platformy Azure są wdrażane i zarządzane. W tym przykładzie grupa zasobów o nazwie *myResourceGroup* w regionie *eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Aby utworzyć maszynę wirtualną na podstawie obrazu bazy danych Oracle Database i skonfigurować ją do używania programu Oracle ASM, należy użyć polecenia [az vm create.](/cli/azure/vm) 

Poniższy przykład tworzy maszynę wirtualną o nazwie myVM, która jest Standard_DS2_v2 rozmiar z czterech dołączonych dysków danych 50 GB każdy. Jeśli jeszcze nie istnieją w domyślnej lokalizacji klucza, tworzy również klucze SSH.  Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

Po utworzeniu maszyny Wirtualnej platformy Azure CLI wyświetla informacje podobne do poniższego przykładu. Zanotuj `publicIpAddress`wartość dla . Ten adres służy do uzyskiwania dostępu do maszyny Wirtualnej.

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

Aby utworzyć sesję SSH z maszyną wirtualną i skonfigurować dodatkowe ustawienia, należy użyć następującego polecenia. Zastąp adres `publicIpAddress` IP wartością maszyny Wirtualnej.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Instalowanie oracle ASM

Aby zainstalować oracle ASM, wykonaj następujące kroki. 

Aby uzyskać więcej informacji na temat instalowania programu Oracle ASM, zobacz [Oracle ASMLib Downloads for Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Musisz zalogować się jako root, aby kontynuować instalację ASM:

   ```bash
   sudo su -
   ```
   
2. Uruchom te dodatkowe polecenia, aby zainstalować składniki Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Sprawdź, czy oracle ASM jest zainstalowany:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Dane wyjściowe tego polecenia powinny być zawierane z następującą listą następujących składników:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM wymaga określonych użytkowników i ról, aby działać poprawnie. Następujące polecenia tworzą wstępnie wymagane konta użytkowników i grupy: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Sprawdź, czy użytkownicy i grupy zostały utworzone poprawnie:

   ```bash
   id grid
   ```

    Dane wyjściowe tego polecenia powinny być zawierane z następującą listą następujących użytkowników i grup:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Utwórz folder dla *siatki* użytkownika i zmień właściciela:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Konfigurowanie oracle ASM

W tym samouczku domyślnym użytkownikiem jest *siatka,* a domyślną grupą jest *asmadmin*. Upewnij się, że użytkownik *oracle* jest częścią grupy asmadmin. Aby skonfigurować instalację Oracle ASM, wykonaj następujące czynności:

1. Konfigurowanie sterownika biblioteki Oracle ASM polega na zdefiniowaniu domyślnego użytkownika (siatki) i domyślnej grupy (asmadmin), a także skonfigurowaniu dysku do uruchamiania przy rozruchu (wybierz y) i skanowania w poszukiwaniu dysków przy rozruchu (wybierz y). Należy odpowiedzieć na monity z następującego polecenia:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Dane wyjściowe tego polecenia powinny wyglądać podobnie do następującego, zatrzymując się z monitami, które mają zostać odebrane.

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

3. Formatuj dysk */dev/sdc,* uruchamiając następujące polecenie i odpowiadając na monity za pomocą:
   - *n* dla nowej partycji
   - *p* dla partycji podstawowej
   - *1,* aby wybrać pierwszą partycję
   - naciśnij `enter` domyślny pierwszy cylinder
   - naciśnij `enter` domyślny ostatni cylinder
   - naciśnij *w,* aby zapisać zmiany w tabeli partycji  

   ```bash
   fdisk /dev/sdc
   ```
   
   Korzystając z odpowiedzi podanych powyżej, `fdisk` dane wyjściowe dla polecenia powinny wyglądać następująco:

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

4. Powtórz poprzednie `fdisk` polecenie `/dev/sdd`dla `/dev/sde`, `/dev/sdf`i .

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

7. Tworzenie dysków Oracle ASM:

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

8. Wymień dyski Oracle ASM:

   ```bash
   service oracleasm listdisks
   ```

   Dane wyjściowe polecenia powinny być zawierane z następującą gamą dysków ASM Oracle:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Zmień hasła dla użytkowników root, oracle i grid. **Zanotuj te nowe hasła** podczas ich późniejszego użycia podczas instalacji.

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Pobieranie i przygotowywanie infrastruktury Oracle Grid

Aby pobrać i przygotować oprogramowanie Oracle Grid Infrastructure, wykonaj następujące czynności:

1. Pobierz oracle grid infrastructure ze [strony pobierania Oracle ASM](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   W ramach pobierania zatytułowanej **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) dla Linuksa x86-64**pobierz dwa pliki .zip.

2. Po pobraniu plików zip na komputer kliencki można skopiować pliki na maszynę wirtualną za pomocą protokołu Secure Copy Protocol(SCP):

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH z powrotem do oracle maszyny Wirtualnej na platformie Azure w celu przeniesienia plików .zip do folderu /opt. Następnie zmień właściciela plików:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Rozpaj pliki. (Zainstaluj narzędzie rozpakować Linuksa, jeśli nie jest jeszcze zainstalowany.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Zmień uprawnienie:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Zaktualizuj skonfigurowane miejsce wymiany. Składniki Oracle Grid potrzebują co najmniej 6,8 GB miejsca wymiany, aby zainstalować siatkę. Domyślny rozmiar pliku wymiany obrazów Oracle Linux na platformie Azure wynosi tylko 2048 MB. Musisz zwiększyć `ResourceDisk.SwapSizeMB` `/etc/waagent.conf` plik i ponownie uruchomić usługę WALinuxAgent, aby zaktualizowane ustawienia zostały zastosowane. Ponieważ jest to plik tylko do odczytu, należy zmienić uprawnienia do plików, aby włączyć dostęp do zapisu.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Wyszukaj `ResourceDisk.SwapSizeMB` i zmień wartość na **8192**. Aby przejść do `insert` trybu wstawiania, należy nacisnąć wartość **8192,** a następnie nacisnąć, `esc` aby powrócić do trybu polecenia. Aby zapisać zmiany i zamknąć `:wq` plik, wpisz i naciśnij klawisz `enter`.
   
   > [!NOTE]
   > Zdecydowanie zaleca się, `WALinuxAgent` aby zawsze używać do konfigurowania miejsca wymiany, tak aby był zawsze tworzony na lokalnym dysku tymczasowym (dysku tymczasowym) dla najlepszej wydajności. Aby uzyskać więcej informacji na ten temat, zobacz [Jak dodać plik wymiany na maszynach wirtualnych platformy Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Przygotowanie klienta lokalnego i maszyny Wirtualnej do uruchomienia x11
Konfigurowanie oracle ASM wymaga interfejsu graficznego, aby zakończyć instalację i konfigurację. Używamy protokołu x11, aby ułatwić tę instalację. Jeśli używasz systemu klienckiego (Mac lub Linux), który ma już włączone i skonfigurowane funkcje X11 - możesz pominąć tę konfigurację i skonfigurować ją wyłącznie na komputerach z systemem Windows. 

1. [Pobierz PuTTY](https://www.putty.org/) i [pobierz Xming](https://xming.en.softonic.com/) na komputer z systemem Windows. Przed kontynuowaniem należy ukończyć instalację obu tych aplikacji z wartościami domyślnymi.

2. Po zainstalowaniu putty otwórz wiersz polecenia, zmień folder PuTTY (na przykład C:\Program Files\PuTTY) i uruchom `puttygen.exe` w celu wygenerowania klucza.

3. W PuTTY Key Generator:
   
   1. Wygeneruj klawisz, `Generate` wybierając przycisk.
   2. Skopiuj zawartość klucza (Ctrl+C).
   3. Wybierz przycisk `Save private key`.
   4. Zignoruj ostrzeżenie dotyczące zabezpieczania klucza `OK`za pomocą hasła, a następnie wybierz opcję .

   ![Zrzut ekranu przedstawiający generator kluczy PuTTY](./media/oracle-asm/puttykeygen.png)

4. Na maszynie wirtualnej uruchom następujące polecenia:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Utwórz plik o nazwie `authorized_keys`. Wklej zawartość klucza w tym pliku, a następnie zapisz plik.

   > [!NOTE]
   > Klucz musi zawierać `ssh-rsa`ciąg . Ponadto zawartość klucza musi być pojedynczym wierszem tekstu.
   >  

6. W systemie klienta uruchom PuTTY. W **okienku Kategoria** przejdź do **strony Podłączanie** > **Auth****SSH** > . W **polu Plik klucza prywatnego do uwierzytelniania** przejdź do klucza wygenerowanego wcześniej.

   ![Zrzut ekranu przedstawiający opcje uwierzytelniania SSH](./media/oracle-asm/setprivatekey.png)

7. W okienku **Kategoria** przejdź do **strony Połączenie** > **SSH** > **X11**. Zaznacz pole wyboru **Włącz przekazywanie X11.**

   ![Zrzut ekranu przedstawiający opcje przekazywania ssh X11](./media/oracle-asm/enablex11.png)

8. W okienku **Kategoria** przejdź do **strony Sesja**. Wprowadź maszynę wirtualną `<publicIPaddress>` Oracle ASM w oknie dialogowym nazwa hosta, wprowadź nową `Saved Session` nazwę, a następnie kliknij przycisk `Save`.  Po zapisaniu `open` kliknij, aby połączyć się z maszyną wirtualną Oracle ASM.  Przy pierwszym połączeniu zostanie wyświetlone ostrzeżenie, że system zdalny nie jest buforowany w rejestrze. Kliknij, `yes` aby dodać go i kontynuować.

   ![Zrzut ekranu przedstawiający opcje sesji PuTTY](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Instalowanie infrastruktury Oracle Grid

Aby zainstalować oracle grid infrastructure, wykonaj następujące kroki:

1. Zaloguj się jako **siatka**. (Powinieneś być w stanie zalogować się bez monitu o podanie hasła). 

   > [!NOTE]
   > Jeśli korzystasz z systemu Windows, upewnij się, że program Xming został uruchomiony przed rozpoczęciem instalacji.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Otwiera się Instalator Oracle Grid Infrastructure 12c Release 1. (Uruchomienie instalatora może potrwać kilka minut).

2. Na stronie **Wybierz opcję instalacji** wybierz pozycję **Zainstaluj i skonfiguruj infrastrukturę Oracle Grid dla serwera autonomicznego**.

   ![Zrzut ekranu przedstawiający stronę Opcji wyboru instalacji instalatora](./media/oracle-asm/install01.png)

3. Na stronie **Wybierz języki produktu** upewnij się, że jest zaznaczony język **angielski** lub wybrany odpowiedni język.  Kliknij pozycję `next` (Dalej).

4. Na stronie **Tworzenie grupy dysków ASM:**
   - Wprowadź nazwę grupy dysków.
   - W obszarze **Nadmiarowość**wybierz pozycję **Zewnętrzne**.
   - W obszarze **Wielkość jednostki alokacji**wybierz **4**.
   - W obszarze **Dodaj dyski**wybierz pozycję **ORCLASMSP**.
   - Kliknij pozycję `next` (Dalej).

5. Na stronie **Określanie hasła ASM** wybierz opcję **Użyj tych samych haseł dla tych kont** i wprowadź hasło.

   ![Zrzut ekranu przedstawiający stronę Określ hasło ASM instalatora](./media/oracle-asm/install04.png)

6. Na stronie **Określ opcje zarządzania** masz możliwość skonfigurowania em cloud control. Pomijamy tę opcję - `next` kliknij, aby kontynuować. 

7. Na stronie **Grupy uprzywilejowanych systemów operacyjnych** użyj ustawień domyślnych. Kliknij, `next` aby kontynuować.

8. Na stronie **Określanie lokalizacji instalacji** użyj ustawień domyślnych. Kliknij, `next` aby kontynuować.

9. Na stronie **Tworzenie zapasów** zmień katalog `/u01/app/grid/oraInventory`zapasów na . Kliknij, `next` aby kontynuować.

   ![Zrzut ekranu przedstawiający stronę Utwórz zapasy instalatora](./media/oracle-asm/install08.png)

10. Na stronie **Konfiguracja wykonywania skryptu głównego** zaznacz pole wyboru **Automatycznie uruchamiaj skrypty konfiguracji.** Następnie wybierz opcję **Użyj poświadczeń użytkownika "root"** i wprowadź hasło użytkownika głównego.

    ![Zrzut ekranu przedstawiający stronę konfiguracji wykonania skryptu głównego instalatora](./media/oracle-asm/install09.png)

11. Na stronie Wykonywanie sprawdzania pod warunkiem **wstępnym** bieżąca konfiguracja zakończy się niepowodzeniem z błędami. Jest to oczekiwane zachowanie. Wybierz pozycję `Fix & Check Again`.

12. W oknie dialogowym Skrypt `OK` **korekty** kliknij pozycję .

13. Na stronie **Podsumowanie** przejrzyj wybrane `Install`ustawienia, a następnie kliknij przycisk .

    ![Zrzut ekranu przedstawiający stronę Podsumowanie instalatora](./media/oracle-asm/install12.png)

14. Zostanie wyświetlone okno dialogowe z ostrzeżeniem informujące o konieczności uruchomienia skryptów konfiguracyjnych jako uprzywilejowanego użytkownika. Kliknij, `Yes` aby kontynuować.

15. Na stronie **Zakończenie** `Close` kliknij, aby zakończyć instalację.

## <a name="set-up-your-oracle-asm-installation"></a>Konfigurowanie instalacji Oracle ASM

Aby skonfigurować instalację Oracle ASM, wykonaj następujące czynności:

1. Upewnij się, że nadal jesteś zalogowany jako **siatka**, z sesji X11. Może być konieczne `enter` trafienie, aby ożywić terminal. Następnie uruchom Asystenta konfiguracji oracle automated storage management:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Zostanie otwarty Asystent konfiguracji Oracle ASM.

2. W oknie dialogowym **Konfigurowanie grup dysków ASM:** Kliknij `Create` przycisk, a następnie kliknij przycisk `Show Advanced Options`.

3. W oknie dialogowym **Tworzenie grupy dysków:**

   - Wprowadź nazwę grupy dysków **DATA**.
   - W obszarze **Wybierz dyski członkowskie**wybierz **ORCL_DATA** i **ORCL_DATA1**.
   - W obszarze **Wielkość jednostki alokacji**wybierz **4**.
   - Kliknij, `ok` aby utworzyć grupę dysków.
   - Kliknij, `ok` aby zamknąć okno potwierdzenia.

   ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie grupy dysków](./media/oracle-asm/asm02.png)

4. W oknie dialogowym **Konfigurowanie grup dysków ASM:** Kliknij `Create` przycisk, a następnie kliknij przycisk `Show Advanced Options`.

5. W oknie dialogowym **Tworzenie grupy dysków:**

   - Wprowadź nazwę grupy dysków **FRA**.
   - W obszarze **Nadmiarowość**wybierz **opcję Zewnętrzne (brak)**.
   - W obszarze **Wybierz dyski członkowskie**wybierz **ORCL_FRA**.
   - W obszarze **Wielkość jednostki alokacji**wybierz **4**.
   - Kliknij, `ok` aby utworzyć grupę dysków.
   - Kliknij, `ok` aby zamknąć okno potwierdzenia.

   ![Zrzut ekranu przedstawiający okno dialogowe Tworzenie grupy dysków](./media/oracle-asm/asm04.png)

6. Wybierz **pozycję Zakończ,** aby zamknąć Asystenta konfiguracji ASM.

   ![Zrzut ekranu przedstawiający okno dialogowe Konfigurowanie asm: grupy dysków z przyciskiem Zakończ](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Tworzenie bazy danych

Oprogramowanie bazy danych Oracle jest już zainstalowane na obrazie portalu Azure Marketplace. Aby utworzyć bazę danych, wykonaj następujące czynności:

1. Przełącz użytkowników do superużytka Oracle, a następnie zainicjuj odbiornik do rejestrowania:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Zostanie otwarty Asystent konfiguracji bazy danych.

2. Na stronie Operacja bazy `Create Database` **danych** kliknij pozycję .

3. Na stronie **Tryb tworzenia:**

   - Wprowadź nazwę bazy danych.
   - W przypadku **typu magazynu**upewnij się, że wybrano **opcję Automatycznego zarządzania pamięcią masową (ASM).**
   - W przypadku **lokalizacji plików bazy danych**należy użyć domyślnej sugerowanej lokalizacji ASM.
   - W obszarze **szybkiego odzyskiwania**użyj domyślnej sugerowanej lokalizacji ASM.
   - wpisz **hasło administracyjne** i **potwierdź hasło**.
   - upewnij `create as container database` się, że jest wybrany.
   - wpisać `pluggable database name` wartość.

4. Na stronie **Podsumowanie** przejrzyj wybrane `Finish` ustawienia, a następnie kliknij, aby utworzyć bazę danych.

   ![Zrzut ekranu przedstawiający stronę Podsumowanie](./media/oracle-asm/createdb03.png)

5. Baza danych została utworzona. Na stronie **Zakończ** możesz odblokować dodatkowe konta, aby korzystać z tej bazy danych i zmieniać hasła. Jeśli chcesz to zrobić, wybierz **Zarządzanie hasłami** - w przeciwnym razie kliknij na `close`.

## <a name="delete-the-vm"></a>Usuwanie maszyny wirtualnej

Pomyślnie skonfigurowano oracle automated storage management na obrazie Oracle DB z portalu Azure Marketplace.  Jeśli ta maszyna wirtualna nie jest już potrzebna, można użyć następującego polecenia, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

[Samouczek: Konfigurowanie oracle dataguard](configure-oracle-dataguard.md)

[Samouczek: Konfigurowanie Oracle GoldenGate](Configure-oracle-golden-gate.md)

Przegląd [Architekt Oracle DB](oracle-design.md)
