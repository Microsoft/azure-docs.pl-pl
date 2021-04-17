---
title: Szybki start — tworzenie maszyny wirtualnej poufnego przetwarzania na platformie Azure w Azure Portal
description: Rozpoczynanie pracy z wdrożeniami przez szybkie tworzenie poufnej maszyny wirtualnej obliczeniowej w Azure Portal.
author: JBCook
ms.author: JenCook
ms.date: 04/23/2020
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.custom:
- mode-portal
ms.openlocfilehash: f43229570f6bab942cc57a2ea3be163d37f02f89
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536182"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Szybki start: wdrażanie maszyny wirtualnej poufnego przetwarzania na platformie Azure w Azure Portal

Rozpoczynanie pracy z poufnym przetwarzaniem na platformie Azure przy Azure Portal tworzenia maszyny wirtualnej z użyciem rozwiązania Intel SGX. Następnie zainstalujesz zestaw SDK (Open Enclave Software Development Kit) w celu skonfigurowania środowiska deweloperskiego. 

Ten samouczek jest zalecany, jeśli interesuje Cię wdrożenie poufnej obliczeniowej maszyny wirtualnej z konfiguracją niestandardową. W przeciwnym razie zalecamy, aby wykonać kroki [poufnego wdrażania maszyny wirtualnej przetwarzania dla komercyjnej platformy handlowej firmy Microsoft.](quick-create-marketplace.md)


## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnej wersji próbnej nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przechodz do subskrypcji z płatnością zgodnie z platformą.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. U góry wybierz pozycję **Utwórz zasób.**

1. W **okienku Marketplace** wybierz pozycję **Obliczenia** po lewej stronie.

1. Znajdź i wybierz pozycję **Maszyna wirtualna.**

    ![Wdrażanie maszyny wirtualnej](media/quick-create-portal/compute-virtual-machine.png)

1. Na stronie docelowej Maszyna wirtualna wybierz pozycję **Utwórz.**


## <a name="configure-a-confidential-computing-virtual-machine"></a>Konfigurowanie maszyny wirtualnej poufnego przetwarzania

1. Na karcie **Ustawienia podstawowe** wybierz **Subskrypcję** i **Grupę zasobów**.

1. W **polach Nazwa maszyny** wirtualnej wprowadź nazwę nowej maszyny wirtualnej.

1. Wpisz lub wybierz poniższe wartości:

   * **Region:** wybierz odpowiedni dla Ciebie region świadczenia usługi Azure.

        > [!NOTE]
        > Poufne obliczeniowe maszyny wirtualne działają tylko na wyspecjalizowanym sprzęcie dostępnym w określonych regionach. Aby uzyskać informacje o najnowszych dostępnych regionach DCsv2-Series wirtualnych, zobacz [dostępne regiony.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)

1. Skonfiguruj obraz systemu operacyjnego, który chcesz użyć dla maszyny wirtualnej.

    * **Wybierz pozycję Obraz:** w tym samouczku wybierz pozycję Ubuntu 18.04 LTS. Możesz również wybrać systemy Windows Server 2019, Windows Server 2016 lub Ubuntu 16.04 LTS. Jeśli to zrobisz, nastąpi odpowiednie przekierowanie w tym samouczku.
    
    * **Przełącz obraz dla generacji 2:** poufne obliczeniowe maszyny wirtualne działają tylko na [obrazach generacji 2.](../virtual-machines/generation-2.md) Upewnij się, że wybierany obraz jest obrazem 2. generacji. Kliknij **kartę** Zaawansowane powyżej, na której konfigurujesz maszynę wirtualną. Przewiń w dół, aż znajdziesz sekcję z etykietą "Vm Generation" (Generowanie maszyny wirtualnej). Wybierz pozycję Gen 2, a następnie wróć do **karty Podstawy.**
    

        ![Karta Zaawansowane](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![Generacja maszyny wirtualnej](media/quick-create-portal/gen2-virtual-machine.png)

    * **Wróć do konfiguracji podstawowej:** Wstecz na **kartę** Podstawowe, korzystając z nawigacji u góry.

1. Wybierz maszynę wirtualną z poufnymi możliwościami obliczeniowymi w selektorze rozmiaru, wybierając **pozycję Zmień rozmiar.** W selektorze rozmiaru maszyny wirtualnej kliknij pozycję **Wyczyść wszystkie filtry.** Wybierz **pozycję Dodaj filtr,** wybierz **pozycję Rodzina** jako typ filtru, a następnie wybierz tylko pozycję Poufne **obliczenia**.

    ![DCsv2-Series wirtualne](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Powinny zostać wyświetlony **rozmiary DC1s_v2**, **DC2s_v2**, **DC4s_V2** i **DC8_v2**. Są to jedyne rozmiary maszyn wirtualnych, które obecnie obsługują poufne przetwarzanie. [Dowiedz się więcej](virtual-machine-solutions.md).

1. Wprowadź następujące informacje:

   * **Typ uwierzytelniania:** wybierz **klucz publiczny SSH,** jeśli tworzysz maszynę wirtualną z systemem Linux. 

        > [!NOTE]
        > Do uwierzytelniania możesz używać klucza publicznego SSH lub hasła. Protokół SSH jest bezpieczniejszy. Aby uzyskać instrukcje na temat sposobu generowania klucza SSH, zobacz [Create SSH keys on Linux and Mac for Linux VMs in Azure (Tworzenie kluczy SSH w systemie Linux i na komputerach Mac dla maszyn wirtualnych z systemem Linux na platformie Azure)](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nazwa** użytkownika: wprowadź nazwę administratora maszyny wirtualnej.

    * **Klucz publiczny SSH:** jeśli ma to zastosowanie, wprowadź klucz publiczny RSA.
    
    * **Hasło:** Jeśli ma to zastosowanie, wprowadź hasło do uwierzytelniania.

    * **Publiczne porty przychodzące:** wybierz pozycję Zezwalaj na wybrane porty i wybierz pozycję **SSH (22)** i HTTP **(80)** na liście **Wybierz publiczne porty przychodzące.**  Jeśli wdrażasz maszynę wirtualną z systemem Windows, wybierz pozycję **HTTP (80)** i **RDP (3389).** W tym przewodniku Szybki start ten krok jest niezbędny do nawiązania połączenia z maszyną wirtualną i ukończenia konfiguracji zestawu SDK Open Enclave. 

     ![Porty wejściowe](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Dokonaj zmian na **karcie Dyski.**

   * W przypadku wybrania maszyny **DC1s_v2** **,** **DC2s_v2** DC4s_V2 wirtualnej wybierz typ dysku, który  jest SSD w warstwie Standardowa **lub SSD w warstwie Premium**. 
   * W przypadku wybrania **DC8_v2** wirtualnej wybierz **SSD w warstwie Standardowa** jako typ dysku.

1. W ustawieniach na poniższych kartach należy wprowadzić dowolne zmiany lub zachować ustawienia domyślne.

    * **Sieć**
    * **Zarządzanie**
    * **Konfiguracja gościa**
    * **Tagi**

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. W okienku **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**.

> [!NOTE]
> Przejdź do następnej sekcji i kontynuuj pracę z tym samouczkiem, jeśli wdrożono maszynę wirtualną z systemem Linux. Jeśli wdrożono maszynę wirtualną z systemem Windows, wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem [Windows,](../virtual-machines/windows/connect-logon.md) a następnie zainstaluj zestaw [OE SDK w systemie Windows.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)


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

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Instalowanie zestawu SDK open enclave (OE SDK) <a id="Install"></a>

Postępuj zgodnie z instrukcjami krok po kroku, aby zainstalować zestaw [SDK OE](https://github.com/openenclave/openenclave) na maszynie wirtualnej z systemem DCsv2-Series systemem Ubuntu 18.04 LTS Gen 2. 

Jeśli maszyna wirtualna działa w systemie Ubuntu 18.04 LTS Gen 2, należy wykonać instrukcje instalacji dla systemu [Ubuntu 18.04.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Konfigurowanie repozytoriów Intel i Microsoft APT

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
> Użyj najnowszego sterownika Intel SGX DCAP z [witryny SGX firmy Intel.](https://01.org/intel-software-guard-extensions/downloads)

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Instalowanie pakietów Intel i Open Enclave oraz zależności

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> W tym kroku instalowany jest również [pakiet az-dcap-client,](https://github.com/microsoft/azure-dcap-client) który jest niezbędny do wykonywania zdalnego zaświadczenia na platformie Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Weryfikowanie instalacji zestawu SDK open enclave**

Aby sprawdzić i użyć zainstalowanego zestawu SDK, zobacz Using [the Open Enclave SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) on GitHub (Używanie zestawu SDK open enclave w usłudze GitHub).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. 

Wybierz grupę zasobów dla maszyny wirtualnej, a następnie wybierz pozycję **Usuń**. Potwierdź nazwę grupy zasobów, aby zakończyć usuwanie zasobów.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono maszynę wirtualną poufnego przetwarzania i zainstalowano zestaw SDK Open Enclave. Aby uzyskać więcej informacji na temat poufnych maszyn wirtualnych do przetwarzania na platformie Azure, zobacz [Solutions on Virtual Machines (Rozwiązania na platformie Virtual Machines).](virtual-machine-solutions.md) 

Dowiedz się, jak tworzyć poufne aplikacje obliczeniowe, korzystając z przykładów zestawu OPEN Enclave SDK w witrynie GitHub. 

> [!div class="nextstepaction"]
> [Tworzenie przykładów zestawu OPEN Enclave SDK](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
