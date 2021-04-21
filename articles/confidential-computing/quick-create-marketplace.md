---
title: Szybki start — tworzenie maszyny wirtualnej do poufnego przetwarzania na platformie Azure przy użyciu witryny Marketplace
description: Rozpoczynanie pracy z wdrożeniami przez uczenie się, jak szybko utworzyć maszynę wirtualną poufnego przetwarzania w witrynie Marketplace.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: be935dbd7e4559bcad8c5cf78622a5c63810f54c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812394"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Szybki start: wdrażanie maszyny wirtualnej poufnego przetwarzania na platformie Azure w witrynie Marketplace

Rozpoczynanie pracy z poufnym przetwarzaniem na platformie Azure przy użyciu Azure Marketplace tworzenia maszyny wirtualnej z technologią Intel SGX. Następnie zainstalujesz zestaw Open Enclave Software Development Kit (SDK) w celu skonfigurowania środowiska deweloperskiego. 

Ten samouczek jest zalecany, jeśli chcesz szybko rozpocząć wdrażanie poufnej maszyny wirtualnej obliczeniowej. Maszyny wirtualne są uruchamiane na sprzęcie specjalistycznym i wymagają określonych danych wejściowych konfiguracji, aby działały zgodnie z przeznaczeniem. Oferta platformy handlowej opisana w tym przewodniku Szybki start ułatwia wdrażanie dzięki ograniczeniu danych wejściowych użytkownika.

Jeśli interesuje Cię wdrożenie poufnej obliczeniowej maszyny wirtualnej z bardziej niestandardową konfiguracją, wykonaj kroki wdrażania maszyny wirtualnej poufnej Azure Portal [obliczeniowej.](quick-create-portal.md)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> Konta bezpłatnej wersji próbnej nie mają dostępu do maszyn wirtualnych używanych w tym samouczku. Przechodz do subskrypcji z płatnością zgodnie z platformą.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W górnej części wpisz **Poufne przetwarzanie na** platformie Azure na pasku wyszukiwania.

1. Wybierz **pozycję Poufne przetwarzanie na platformie Azure (maszyna wirtualna)** w sekcji **Marketplace.** 

    ![Wybieranie witryny Marketplace](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Na stronie docelowej poufnego wdrożenia obliczeniowego platformy Azure wybierz pozycję **Utwórz**.
 

## <a name="configure-your-virtual-machine"></a>Konfigurowanie maszyny wirtualnej

1. Na karcie **Ustawienia podstawowe** wybierz **Subskrypcję** i **Grupę zasobów**. Grupa zasobów musi być pusta, aby wdrożyć w nim maszynę wirtualną z tego szablonu.

1. Wpisz lub wybierz poniższe wartości:

   * **Region:** wybierz odpowiedni dla Ciebie region świadczenia usługi Azure.

        > [!NOTE]
        > Poufne obliczeniowe maszyny wirtualne działają tylko na wyspecjalizowanym sprzęcie dostępnym w określonych regionach. Aby uzyskać informacje o najnowszych dostępnych regionach DCsv2-Series wirtualnych, zobacz [dostępne regiony.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)
    
    * **Wybierz pozycję Obraz:** wybierz dowolny obraz. Jeśli chcesz ukończyć ten konkretny samouczek, wybierz pozycję Ubuntu 18.04 (Gen 2). W przeciwnym razie nastąpi przekierowanie w odpowiednich krokach poniżej. 

    * **Nazwa maszyny wirtualnej** wprowadź nazwę nowej maszyny wirtualnej.

    * **Typ uwierzytelniania:** wybierz **pozycję Klucz publiczny SSH,** jeśli tworzysz maszynę wirtualną z systemem Linux. 

         > [!NOTE]
         > Do uwierzytelniania możesz używać klucza publicznego SSH lub hasła. Protokół SSH jest bezpieczniejszy. Aby uzyskać instrukcje na temat sposobu generowania klucza SSH, zobacz [Create SSH keys on Linux and Mac for Linux VMs in Azure (Tworzenie kluczy SSH w systemie Linux i na komputerach Mac dla maszyn wirtualnych z systemem Linux na platformie Azure)](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Nazwa** użytkownika: wprowadź nazwę administratora maszyny wirtualnej.

    * **Klucz publiczny SSH:** jeśli ma to zastosowanie, wprowadź klucz publiczny RSA.
    
    * **Hasło:** Jeśli ma to zastosowanie, wprowadź hasło do uwierzytelniania.
 
1. Wybierz przycisk **Dalej: Ustawienia maszyny wirtualnej** w dolnej części ekranu.

    > [!IMPORTANT]
    > Poczekaj na aktualizację strony. Nie *powinien być wyświetlany* komunikat "Poufne przetwarzanie maszyn wirtualnych serii DCsv2 są dostępne w ograniczonej liczbie regionów". Jeśli ten komunikat będzie się powtarzać, wróć do poprzedniej strony i wybierz dostępny DCsv2-Series region.

1. W **przypadku zmiany rozmiaru** wybierz maszynę wirtualną z poufnymi możliwościami obliczeniowymi w selektorze rozmiaru. 

    > [!TIP]
    > Powinny zostać wyświetlony **rozmiary DC1s_v2**, **DC2s_v2**, **DC4s_V2** i **DC8_v2**. Są to jedyne rozmiary maszyn wirtualnych, które obecnie obsługują poufne przetwarzanie. [Dowiedz się więcej](virtual-machine-solutions.md).

1. W **przypadku opcji Typ dysku systemu** operacyjnego wybierz typ dysku.

1. Na **Virtual Network** utwórz nowy lub wybierz istniejący zasób.

1. W **przypadku podsieci** utwórz nową lub wybierz istniejący zasób.

1. W **przypadku opcji Wybierz publiczne porty przychodzące** wybierz pozycję **SSH(Linux)/RDP(Windows).** W tym przewodniku Szybki start ten krok jest niezbędny do nawiązania połączenia z maszyną wirtualną i ukończenia konfiguracji zestawu SDK Open Enclave. 

1. W **przypadku diagnostyki** rozruchu pozostaw ją wyłączona na potrzeby tego przewodnika Szybki start. 

1. Wybierz pozycję **Przejrzyj i utwórz**.

1. W okienku **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**.

> [!NOTE]
> Przejdź do następnej sekcji i kontynuuj pracę z tym samouczkiem, jeśli wdrożono maszynę wirtualną z systemem Linux. Jeśli wdrożono maszynę wirtualną z systemem Windows, wykonaj następujące kroki, aby nawiązać połączenie z maszyną wirtualną z systemem [Windows,](../virtual-machines/windows/connect-logon.md) a następnie zainstaluj zestaw [SDK OE w systemie Windows.](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)


## <a name="connect-to-the-linux-vm"></a> Łączenie z maszyną wirtualną z systemem Linux

Jeśli już używasz powłoki BASH, połącz się z maszyną wirtualną platformy Azure przy użyciu polecenia **ssh**. W poniższym poleceniu zastąp nazwę użytkownika i adres IP maszyny wirtualnej, aby nawiązać połączenie z maszyną wirtualną z systemem Linux.

```bash
ssh azureadmin@40.55.55.555
```

Publiczny adres IP maszyny wirtualnej można znaleźć w Azure Portal, w sekcji Przegląd maszyny wirtualnej.

![Adres IP w witrynie Azure Portal](media/quick-create-portal/public-ip-virtual-machine.png)

Jeśli używasz systemu Windows i nie masz powłoki BASH, zainstaluj klienta SSH, takiego jak PuTTY.

1. [Pobierz i zainstaluj program PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Uruchom program PuTTY.

1. Na ekranie konfiguracji programu PuTTY wprowadź publiczny adres IP maszyny wirtualnej.

1. Wybierz przycisk **Open** (Otwórz), a następnie po wyświetleniu monitów wprowadź nazwę użytkownika i hasło.

Aby uzyskać więcej informacji na temat nawiązywania połączenia z maszynami wirtualnymi z systemem Linux, zobacz [Create a Linux VM on Azure using the Portal (Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu portalu)](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Jeśli w programie PuTTY zostanie wyświetlony alert dotyczący zabezpieczeń z informacją „server’s host key not cached in the registry” (klucz hosta serwera nie jest buforowany w rejestrze), wybierz jedną z następujących opcji. Jeśli host jest zaufany, wybierz pozycję **Yes** (Tak), aby dodać ten klucz do pamięci podręcznej programu PuTTy i kontynuować nawiązywanie połączenia. Jeśli chcesz nawiązać połączenie tylko raz, bez dodawania klucza do pamięci podręcznej, wybierz pozycję **No** (Nie). Jeśli host nie jest zaufany, wybierz pozycję **Cancel** (Anuluj), aby przerwać nawiązywanie połączenia.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>Instalowanie zestawu OPEN Enclave SDK (OE SDK) <a id="Install"></a>

Postępuj zgodnie z instrukcjami krok po kroku, aby zainstalować zestaw [SDK OE](https://github.com/openenclave/openenclave) na maszynie DCsv2-Series wirtualnej z obrazem systemu Ubuntu 18.04 LTS Gen 2. 

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
Niektóre wersje systemu Ubuntu mogą już mieć zainstalowany sterownik Intel SGX. Sprawdź przy użyciu następującego polecenia: 

```bash
dmesg | grep -i sgx
[  106.775199] sgx: intel_sgx: Intel SGX DCAP Driver {version}
``` 
Jeśli dane wyjściowe są puste, zainstaluj sterownik: 

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.7/linux/distro/ubuntu18.04-server/sgx_linux_x64_driver_1.35.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Użyj najnowszego sterownika Intel SGX DCAP z witryny [SGX firmy Intel.](https://01.org/intel-software-guard-extensions/downloads)

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Instalowanie pakietów Intel i Open Enclave oraz zależności

```bash
sudo apt -y install clang-8 libssl-dev gdb libsgx-enclave-common libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> W tym kroku instalowany jest również [pakiet az-dcap-client,](https://github.com/microsoft/azure-dcap-client) który jest niezbędny do wykonywania zdalnego zaświadczenia na platformie Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Weryfikowanie instalacji zestawu SDK open enclave**

Aby sprawdzić i użyć zainstalowanego zestawu SDK, zobacz Using [the Open Enclave SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) on GitHub (Używanie zestawu SDK open enclave w usłudze GitHub).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. 

Wybierz grupę zasobów dla maszyny wirtualnej, a następnie wybierz pozycję **Usuń**. Potwierdź nazwę grupy zasobów, aby zakończyć usuwanie zasobów.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono maszynę wirtualną poufnego przetwarzania i zainstalowano zestaw SDK Open Enclave. Aby uzyskać więcej informacji na temat poufnych maszyn wirtualnych do przetwarzania na platformie Azure, zobacz [Rozwiązania na Virtual Machines](virtual-machine-solutions.md). 

Dowiedz się, jak tworzyć poufne aplikacje obliczeniowe, korzystając z przykładów zestawu OPEN Enclave SDK w witrynie GitHub. 

> [!div class="nextstepaction"]
> [Przykłady budowania zestawu SDK open enclave](https://github.com/openenclave/openenclave/blob/master/samples/README.md)
