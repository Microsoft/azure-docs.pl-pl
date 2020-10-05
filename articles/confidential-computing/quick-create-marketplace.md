---
title: Szybki Start — Tworzenie maszyny wirtualnej do przetwarzania poufnego platformy Azure przy użyciu witryny Marketplace
description: Rozpocznij pracę z wdrożeniami, zapoznaj się z tematem, jak szybko utworzyć poufną maszynę wirtualną obliczeniową w portalu Marketplace.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 82d9c143f84dfced639c928bf12693024079c2ba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91409497"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Szybki Start: Wdrażanie maszyny wirtualnej do obliczania poufnej platformy Azure w portalu Marketplace

Rozpocznij pracę z usługą Azure poufnego przetwarzania przy użyciu witryny Azure Marketplace, aby utworzyć maszynę wirtualną, która jest obsługiwana przez platformę Intel SGX. Następnie zainstalujesz otwarty zestaw enklawy Software Development Kit (SDK), aby skonfigurować środowisko deweloperskie. 

Ten samouczek jest zalecany, jeśli chcesz szybko rozpocząć wdrażanie poufnej maszyny wirtualnej. Maszyny wirtualne są uruchamiane na specjalnym sprzęcie i wymagają określonych danych wejściowych konfiguracji do uruchomienia zgodnie z oczekiwaniami. Oferta portalu Marketplace opisana w tym przewodniku szybki start ułatwia wdrażanie, ograniczając dane wprowadzane przez użytkownika.

Jeśli interesuje się wdrożenie poufnej maszyny wirtualnej obliczeniowej z większą konfiguracją niestandardową, wykonaj [czynności opisane w temacie Azure Portal poufne wdrożenia maszyn wirtualnych](quick-create-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnych wersji próbnych nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przeprowadź uaktualnienie do subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Na początku wpisz dane **poufne platformy Azure** na pasku wyszukiwania.

1. Wybierz pozycję **Azure poufne dane (maszyna wirtualna)** w sekcji **Marketplace** . 

    ![Wybierz witrynę Marketplace](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Na stronie docelowej wdrożenia obliczeń poufnej platformy Azure wybierz pozycję **Utwórz**.
 

## <a name="configure-your-virtual-machine"></a>Konfigurowanie maszyny wirtualnej

1. Na karcie **Ustawienia podstawowe** wybierz **Subskrypcję** i **Grupę zasobów**. Grupa zasobów musi być pusta, aby można było wdrożyć maszynę wirtualną z tego szablonu.

1. Wpisz lub wybierz poniższe wartości:

   * **Region**: wybierz odpowiedni region platformy Azure.

        > [!NOTE]
        > Poufne maszyny wirtualne obliczeniowe są uruchamiane tylko na wyspecjalizowanym sprzęcie dostępnym w określonych regionach. Najnowsze dostępne regiony dla maszyn wirtualnych z serii DCsv2 można znaleźć w temacie [dostępne regiony](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).
    
    * **Wybierz obraz**: zaznacz dowolny obraz. Jeśli chcesz ukończyć ten konkretny samouczek, wybierz pozycję Ubuntu 18,04 (Gen 2). W przeciwnym razie nastąpi przekierowanie do odpowiednich kroków poniżej. 

    * **Nazwa maszyny wirtualnej**, wprowadź nazwę nowej maszyny wirtualnej.

    * **Typ uwierzytelniania**: wybierz opcję **publiczny klucz SSH** , jeśli tworzysz maszynę wirtualną z systemem Linux. 

         > [!NOTE]
         > Do uwierzytelniania możesz używać klucza publicznego SSH lub hasła. Protokół SSH jest bezpieczniejszy. Aby uzyskać instrukcje na temat sposobu generowania klucza SSH, zobacz [Create SSH keys on Linux and Mac for Linux VMs in Azure (Tworzenie kluczy SSH w systemie Linux i na komputerach Mac dla maszyn wirtualnych z systemem Linux na platformie Azure)](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nazwa użytkownika**: Wprowadź nazwę administratora maszyny wirtualnej.

    * **Klucz publiczny SSH**: w razie potrzeby wprowadź klucz publiczny RSA.
    
    * **Hasło**: w razie potrzeby wprowadź hasło do uwierzytelniania.
 
1. Wybierz przycisk **Dalej: ustawienia maszyny wirtualnej** w dolnej części ekranu.

    > [!IMPORTANT]
    > Zaczekaj na zaktualizowanie strony. *Nie powinien zostać* wyświetlony komunikat informujący o tym, że maszyny wirtualne z serii DCsv2 są dostępne w ograniczonej liczbie regionów. " Jeśli ten komunikat będzie nadal występował, Wróć do poprzedniej strony i wybierz dostępny region serii DCsv2.

1. Aby **zmienić rozmiar**, wybierz maszynę wirtualną z możliwością obliczeń poufną w selektorze rozmiarów. 

    > [!TIP]
    > Powinny być widoczne rozmiary **DC1s_v2**, **DC2s_v2**, **DC4s_V2**i **DC8_v2**. Są to jedyne rozmiary maszyn wirtualnych, które obecnie obsługują dane poufne. [Dowiedz się więcej](virtual-machine-solutions.md).

1. W obszarze **typ dysku systemu operacyjnego**wybierz typ dysku.

1. W przypadku **Virtual Network**Utwórz nowy element lub wybierz go z istniejącego zasobu.

1. W obszarze **podsieć**Utwórz nową lub wybierz jeden z istniejących zasobów.

1. W obszarze **Wybierz publiczne porty przychodzące**wybierz pozycję **SSH (Linux)/RDP (Windows)**. W tym przewodniku szybki start ten krok jest niezbędny do nawiązania połączenia z maszyną wirtualną i ukończenia konfiguracji Open enklawy SDK. 

1. W przypadku **diagnostyki rozruchu**pozostaw to pole wyłączone w tym przewodniku Szybki Start. 

1. Wybierz pozycję **Przeglądanie + tworzenie**.

1. W okienku **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**.

> [!NOTE]
> Przejdź do następnej sekcji i Kontynuuj pracę z tym samouczkiem, jeśli wdrożono maszynę wirtualną z systemem Linux. W przypadku wdrożenia maszyny wirtualnej z systemem Windows [wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem Windows](../virtual-machines/windows/connect-logon.md) , a następnie [zainstalować zestaw OE SDK w systemie Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a> Łączenie z maszyną wirtualną z systemem Linux

Jeśli już używasz powłoki BASH, połącz się z maszyną wirtualną platformy Azure przy użyciu polecenia **ssh**. W poniższym poleceniu zastąp nazwę użytkownika i adres IP maszyny wirtualnej, aby nawiązać połączenie z maszyną wirtualną z systemem Linux.

```bash
ssh azureadmin@40.55.55.555
```

Publiczny adres IP maszyny wirtualnej można znaleźć w Azure Portal w sekcji Przegląd maszyny wirtualnej.

![Adres IP w witrynie Azure Portal](media/quick-create-portal/public-ip-virtual-machine.png)

Jeśli używasz systemu Windows i nie masz powłoki BASH, zainstaluj klienta SSH, takiego jak PuTTY.

1. [Pobierz i zainstaluj program PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Uruchom program PuTTY.

1. Na ekranie konfiguracji programu PuTTY wprowadź publiczny adres IP maszyny wirtualnej.

1. Wybierz przycisk **Open** (Otwórz), a następnie po wyświetleniu monitów wprowadź nazwę użytkownika i hasło.

Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszynami wirtualnymi z systemem Linux, zobacz [Create a Linux VM on Azure using the Portal (Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu portalu)](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Jeśli w programie PuTTY zostanie wyświetlony alert dotyczący zabezpieczeń z informacją „server’s host key not cached in the registry” (klucz hosta serwera nie jest buforowany w rejestrze), wybierz jedną z następujących opcji. Jeśli host jest zaufany, wybierz pozycję **Yes** (Tak), aby dodać ten klucz do pamięci podręcznej programu PuTTy i kontynuować nawiązywanie połączenia. Jeśli chcesz nawiązać połączenie tylko raz, bez dodawania klucza do pamięci podręcznej, wybierz pozycję **No** (Nie). Jeśli host nie jest zaufany, wybierz pozycję **Cancel** (Anuluj), aby przerwać nawiązywanie połączenia.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Instalowanie zestawu SDK open enklawy (zestaw SDK programu OE) <a id="Install"></a>

Postępuj zgodnie z instrukcjami krok po kroku, aby zainstalować [zestaw OE SDK](https://github.com/openenclave/openenclave) na maszynie wirtualnej z serii DCsv2, na której działa obraz Ubuntu 18,04 LTS Gen 2. 

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
wget https://download.01.org/intel-sgx/sgx-dcap/1.4/linux/distro/ubuntuServer18.04/sgx_linux_x64_driver_1.21.bin -O sgx_linux_x64_driver.bin
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
