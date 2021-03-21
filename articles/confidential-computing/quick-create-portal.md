---
title: Szybki Start — tworzenie poufnej maszyny wirtualnej platformy Azure w Azure Portal
description: Rozpocznij pracę z wdrożeniami, zapoznaj się z tematem jak szybko utworzyć poufną maszynę wirtualną obliczeniową w Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: JenCook
ms.openlocfilehash: 3f0984acd66bd5d6c148be8451938d3152fb9ca7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566672"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Szybki Start: Wdrażanie maszyny wirtualnej do obliczania poufnej platformy Azure w Azure Portal

Rozpocznij pracę z usługą Azure poufnego przetwarzania przy użyciu Azure Portal, aby utworzyć maszynę wirtualną (VM), która jest obsługiwana przez platformę Intel SGX. Następnie zainstalujesz otwarty zestaw enklawy Software Development Kit (SDK), aby skonfigurować środowisko deweloperskie. 

Ten samouczek jest zalecany dla Ciebie, Jeśli interesuje Cię wdrożenie poufnej maszyny wirtualnej obliczeniowej z konfiguracją niestandardową. W przeciwnym razie zalecamy wykonanie [czynności związanych z wdrażaniem maszyn wirtualnych w ramach rynku komercyjnego firmy Microsoft](quick-create-marketplace.md).


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnych wersji próbnych nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przeprowadź uaktualnienie do subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W górnej części wybierz pozycję **Utwórz zasób**.

1. W okienku **Marketplace** wybierz pozycję **obliczenia** po lewej stronie.

1. Znajdź i wybierz **maszynę wirtualną**.

    ![Wdrażanie maszyny wirtualnej](media/quick-create-portal/compute-virtual-machine.png)

1. Na stronie miejsce docelowe maszyny wirtualnej wybierz pozycję **Utwórz**.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Konfigurowanie poufnej maszyny wirtualnej

1. Na karcie **Ustawienia podstawowe** wybierz **Subskrypcję** i **Grupę zasobów**.

1. W obszarze **Nazwa maszyny wirtualnej** wprowadź nazwę nowej maszyny wirtualnej.

1. Wpisz lub wybierz poniższe wartości:

   * **Region**: wybierz odpowiedni region platformy Azure.

        > [!NOTE]
        > Poufne maszyny wirtualne obliczeniowe są uruchamiane tylko na wyspecjalizowanym sprzęcie dostępnym w określonych regionach. Najnowsze dostępne regiony dla DCsv2-Series maszyn wirtualnych można znaleźć w temacie [dostępne regiony](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

1. Skonfiguruj obraz systemu operacyjnego, którego chcesz użyć dla maszyny wirtualnej.

    * **Wybierz obraz**: w tym samouczku wybierz pozycję Ubuntu 18,04 LTS. Możesz również wybrać systemy Windows Server 2019, Windows Server 2016 lub i Ubuntu 16,04 LTS. W przypadku wybrania tej opcji zostanie odpowiednio przekierowany w tym samouczku.
    
    * **Przełącz obraz do generacji 2**: poufne maszyny wirtualne obliczeniowe są uruchamiane tylko w obrazach [generacji 2](../virtual-machines/generation-2.md) . Upewnij się, że wybrany obraz jest obrazem generacji 2. Kliknij kartę **Zaawansowane** powyżej miejsca konfiguracji maszyny wirtualnej. Przewiń w dół do momentu znalezienia sekcji "generacja maszyny wirtualnej". Wybierz pozycję Gen 2, a następnie wróć do karty **podstawy** .
    

        ![Karta Zaawansowane](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![Generacja maszyny wirtualnej](media/quick-create-portal/gen2-virtual-machine.png)

    * **Wróć do konfiguracji podstawowej**: Wróć do karty **podstawowe** przy użyciu nawigacji w górnej części strony.

1. Wybierz maszynę wirtualną z poufnymi możliwościami obliczeniowymi w selektorze rozmiarów, wybierając pozycję **Zmień rozmiar**. W selektor rozmiaru maszyny wirtualnej kliknij pozycję **Wyczyść wszystkie filtry**. Wybierz pozycję **Dodaj filtr**, wybierz pozycję **rodzina** dla typu filtru, a następnie wybierz opcję tylko **poufne dane obliczeniowe**.

    ![DCsv2-Series maszyny wirtualne](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Powinny być widoczne rozmiary **DC1s_v2**, **DC2s_v2**, **DC4s_V2** i **DC8_v2**. Są to jedyne rozmiary maszyn wirtualnych, które obecnie obsługują dane poufne. [Dowiedz się więcej](virtual-machine-solutions.md).

1. Wprowadź następujące informacje:

   * **Typ uwierzytelniania**: wybierz opcję **publiczny klucz SSH** , jeśli tworzysz maszynę wirtualną z systemem Linux. 

        > [!NOTE]
        > Do uwierzytelniania możesz używać klucza publicznego SSH lub hasła. Protokół SSH jest bezpieczniejszy. Aby uzyskać instrukcje na temat sposobu generowania klucza SSH, zobacz [Create SSH keys on Linux and Mac for Linux VMs in Azure (Tworzenie kluczy SSH w systemie Linux i na komputerach Mac dla maszyn wirtualnych z systemem Linux na platformie Azure)](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nazwa użytkownika**: Wprowadź nazwę administratora maszyny wirtualnej.

    * **Klucz publiczny SSH**: w razie potrzeby wprowadź klucz publiczny RSA.
    
    * **Hasło**: w razie potrzeby wprowadź hasło do uwierzytelniania.

    * **Publiczne porty przychodzące**: wybierz opcję **Zezwalaj na wybrane porty** i wybierz pozycję **SSH (22)** i **http (80)** na liście **Wybierz publiczne porty wejściowe** . Jeśli wdrażasz maszynę wirtualną z systemem Windows, wybierz pozycję **http (80)** i **protokół RDP (3389)**. W tym przewodniku szybki start ten krok jest niezbędny do nawiązania połączenia z maszyną wirtualną i ukończenia konfiguracji Open enklawy SDK. 

     ![Porty wejściowe](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Wprowadź zmiany na karcie **dyski** .

   * W przypadku wybrania **DC1s_v2**, **DC2s_v2** **DC4s_V2** maszyny wirtualnej, wybierz typ dysku, który jest **SSD w warstwie Standardowa** lub **SSD w warstwie Premium**. 
   * W przypadku wybrania maszyny wirtualnej **DC8_v2** wybierz opcję **SSD w warstwie Standardowa** jako typ dysku.

1. Wprowadź zmiany w ustawieniach na poniższych kartach lub Zachowaj ustawienia domyślne.

    * **Sieć**
    * **Zarządzanie**
    * **Konfiguracja gościa**
    * **Tagi**

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. W okienku **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**.

> [!NOTE]
> Przejdź do następnej sekcji i Kontynuuj pracę z tym samouczkiem, jeśli wdrożono maszynę wirtualną z systemem Linux. W przypadku wdrożenia maszyny wirtualnej z systemem Windows [wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem Windows](../virtual-machines/windows/connect-logon.md) , a następnie [zainstalować zestaw OE SDK w systemie Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a> Łączenie z maszyną wirtualną z systemem Linux

Jeśli już używasz powłoki BASH, połącz się z maszyną wirtualną platformy Azure przy użyciu polecenia **ssh**. W poniższym poleceniu zastąp nazwę użytkownika i adres IP maszyny wirtualnej, aby nawiązać połączenie z maszyną wirtualną z systemem Linux.

```bash
ssh azureadmin@40.55.55.555
```

Publiczny adres IP maszyny wirtualnej można znaleźć w Azure Portal w sekcji Przegląd maszyny wirtualnej.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="Adres IP w witrynie Azure Portal":::

Jeśli używasz systemu Windows i nie masz powłoki BASH, zainstaluj klienta SSH, takiego jak PuTTY.

1. [Pobierz i zainstaluj program PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Uruchom program PuTTY.

1. Na ekranie konfiguracji programu PuTTY wprowadź publiczny adres IP maszyny wirtualnej.

1. Wybierz przycisk **Open** (Otwórz), a następnie po wyświetleniu monitów wprowadź nazwę użytkownika i hasło.

Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszynami wirtualnymi z systemem Linux, zobacz [Create a Linux VM on Azure using the Portal (Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu portalu)](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Jeśli w programie PuTTY zostanie wyświetlony alert dotyczący zabezpieczeń z informacją „server’s host key not cached in the registry” (klucz hosta serwera nie jest buforowany w rejestrze), wybierz jedną z następujących opcji. Jeśli host jest zaufany, wybierz pozycję **Yes** (Tak), aby dodać ten klucz do pamięci podręcznej programu PuTTy i kontynuować nawiązywanie połączenia. Jeśli chcesz nawiązać połączenie tylko raz, bez dodawania klucza do pamięci podręcznej, wybierz pozycję **No** (Nie). Jeśli host nie jest zaufany, wybierz pozycję **Cancel** (Anuluj), aby przerwać nawiązywanie połączenia.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Instalowanie zestawu SDK open enklawy (zestaw SDK programu OE) <a id="Install"></a>

Postępuj zgodnie z instrukcjami krok po kroku, aby zainstalować [zestaw programu OE SDK](https://github.com/openenclave/openenclave) na maszynie wirtualnej DCsv2-Series z Ubuntu 18,04 LTS generacji 2. 

Jeśli maszyna wirtualna jest uruchamiana na Ubuntu 16,04 LTS Gen 2, należy postępować zgodnie z [instrukcjami instalacji dla Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md).

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Skonfiguruj repozytoria Intel i Microsoft APT

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. Zainstaluj sterownik Intel SGX DCAP

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.9/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.36.2.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Użyj najnowszego sterownika Intel SGX DCAP z [witryny SGX firmy Intel](https://01.org/intel-software-guard-extensions/downloads).

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Zainstaluj pakiety i zależności firmy Intel i Otwórz program enklawy

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Ten krok powoduje także zainstalowanie pakietu [AZ-Dcap-Client](https://github.com/microsoft/azure-dcap-client) , który jest niezbędny do wykonania zdalnego zaświadczania na platformie Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Sprawdź instalację zestawu SDK open enklawy**

Zapoznaj się z tematem [Korzystanie z zestawu SDK open enklawy](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) w witrynie GitHub w celu sprawdzenia i użycia zainstalowanego zestawu SDK.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. 

Wybierz grupę zasobów dla maszyny wirtualnej, a następnie wybierz pozycję **Usuń**. Potwierdź nazwę grupy zasobów, aby zakończyć usuwanie zasobów.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono poufną maszynę wirtualną i zainstalowano zestaw SDK open enklawy. Aby uzyskać więcej informacji na temat poufnego przetwarzania maszyn wirtualnych na platformie Azure, zobacz [rozwiązania na Virtual Machines](virtual-machine-solutions.md). 

Dowiedz się, jak tworzyć poufne aplikacje obliczeniowe, kontynuując otwarte przykłady zestawu enklawy SDK w witrynie GitHub. 

> [!div class="nextstepaction"]
> [Kompilowanie przykładów zestawu SDK open enklawy](https://github.com/openenclave/openenclave/blob/master/samples/README.md)