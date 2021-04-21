---
title: Dodawanie interfejsów sieciowych do maszyn wirtualnych platformy Azure lub ich usuwanie
description: Dowiedz się, jak dodawać interfejsy sieciowe do maszyn wirtualnych lub usuwać je z tych interfejsów.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 847f8dbd2d8f4064f12333348a4f03e5c5fcc611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774273"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Dodawanie interfejsów sieciowych do maszyn wirtualnych lub ich usuwanie

Dowiedz się, jak dodać istniejący interfejs sieciowy podczas tworzenia maszyny wirtualnej platformy Azure. Dowiedz się również, jak dodawać lub usuwać interfejsy sieciowe z istniejącej maszyny wirtualnej w stanie zatrzymanym (cofniesz jej alokację). Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure komunikowanie się z Internetem, platformą Azure i zasobami lokalnymi. Maszyna wirtualna ma co najmniej jeden interfejs sieciowy. 

Jeśli musisz dodać, zmienić lub usunąć adresy IP dla interfejsu sieciowego, zobacz [Zarządzanie adresami IP interfejsu sieciowego.](virtual-network-network-interface-addresses.md) Aby tworzyć, zmieniać lub usuwać interfejsy sieciowe, zobacz [Zarządzanie interfejsami sieciowym](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Przed rozpoczęciem dalszej części tego artykułu wykonaj jedno z tych zadań:

- **Użytkownicy portalu:** zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

- **Użytkownicy programu PowerShell:** uruchom polecenia w Azure Cloud Shell [lub](https://shell.azure.com/powershell)uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Azure Cloud Shell przeglądarki znajdź  listę rozwijaną Wybierz środowisko, a następnie wybierz program **PowerShell,** jeśli nie został jeszcze wybrany.

    Jeśli używasz programu PowerShell lokalnie, użyj modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure:** uruchamiaj polecenia w Azure Cloud Shell [lub](https://shell.azure.com/bash)uruchamiaj interfejs wiersza polecenia na komputerze. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.26 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom polecenie `az login`, aby utworzyć połączenia z platformą Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Dodawanie istniejących interfejsów sieciowych do nowej maszyny wirtualnej

Podczas tworzenia maszyny wirtualnej za pośrednictwem portalu portal tworzy interfejs sieciowy z ustawieniami domyślnymi i dołącza interfejs sieciowy do maszyny wirtualnej. Nie można używać portalu do dodawania istniejących interfejsów sieciowych do nowej maszyny wirtualnej ani do tworzenia maszyny wirtualnej z wieloma interfejsami sieciowym. Możesz to zrobić za pomocą interfejsu wiersza polecenia lub programu PowerShell. Pamiętaj, aby zapoznać się z [ograniczeniami](#constraints). Jeśli tworzysz maszynę wirtualną z wieloma interfejsami sieciowym, musisz również skonfigurować system operacyjny do prawidłowego używania ich po utworzeniu maszyny wirtualnej. Dowiedz się, jak skonfigurować [system Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [lub Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) dla wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia

Przed utworzeniem maszyny wirtualnej [utwórz interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface).

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Dodawanie interfejsu sieciowego do istniejącej maszyny wirtualnej

Aby dodać interfejs sieciowy do maszyny wirtualnej:

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz **pozycję Maszyny wirtualne.**

2. Wybierz nazwę maszyny wirtualnej. Maszyna wirtualna musi obsługiwać liczbę interfejsów sieciowych, które chcesz dodać. Aby dowiedzieć się, ile interfejsów sieciowych obsługuje każdy rozmiar maszyny wirtualnej, zobacz rozmiary na platformie Azure dla maszyn wirtualnych z systemem [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [lub maszyn wirtualnych z systemem Windows.](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

3. Na pasku poleceń maszyny wirtualnej wybierz pozycję **Zatrzymaj**, a następnie **kliknij przycisk OK** w oknie dialogowym potwierdzenia. Następnie poczekaj, aż **stan** maszyny wirtualnej zmieni się na **Zatrzymana (cofniesz alokację).**

4. Na pasku menu maszyny wirtualnej wybierz pozycję **Sieć Dołącz**  >  **interfejs sieciowy**. Następnie w **łączeniu istniejącego interfejsu** sieciowego wybierz interfejs sieciowy, który chcesz dołączyć, i wybierz przycisk **OK.**

    >[!NOTE]
    >Interfejs sieciowy, który wybierzesz, nie może mieć włączonej przyspieszonej sieci, nie może mieć przypisanego adresu IPv6 i musi istnieć w tej samej sieci wirtualnej z interfejsem sieciowym aktualnie dołączonym do maszyny wirtualnej.

    Jeśli nie masz istniejącego interfejsu sieciowego, musisz go najpierw utworzyć. W tym celu wybierz pozycję **Utwórz interfejs sieciowy.** Aby dowiedzieć się więcej na temat tworzenia interfejsu sieciowego, zobacz [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface). Aby dowiedzieć się więcej o dodatkowych ograniczeniach podczas dodawania interfejsów sieciowych do maszyn wirtualnych, zobacz [Ograniczenia](#constraints).

5. Na pasku menu maszyny wirtualnej wybierz pozycję **Przegląd**  >  **Rozpocznij,** aby ponownie uruchomić maszynę wirtualną.

Teraz możesz skonfigurować system operacyjny maszyny wirtualnej tak, aby prawidłowo używać wielu interfejsów sieciowych. Dowiedz się, jak skonfigurować [system Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [lub Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) dla wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (odwołanie); [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie); [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Wyświetl interfejsy sieciowe dla maszyny wirtualnej

Możesz wyświetlić interfejsy sieciowe aktualnie dołączone do maszyny wirtualnej, aby dowiedzieć się więcej o konfiguracji poszczególnych interfejsów sieciowych i adresach IP przypisanych do każdego interfejsu sieciowego. 

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz **pozycję Maszyny wirtualne.**

    >[!NOTE]
    >Zaloguj się przy użyciu konta, które ma przypisaną rolę Właściciel, Współautor lub Współautor sieci dla Twojej subskrypcji. Aby dowiedzieć się więcej na temat przypisywania ról do kont, zobacz Wbudowane role kontroli dostępu opartej na rolach [platformy Azure.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

2. Wybierz nazwę maszyny wirtualnej, dla której chcesz wyświetlić dołączone interfejsy sieciowe.

3. Na pasku menu maszyny wirtualnej wybierz pozycję **Sieć.**

Aby dowiedzieć się więcej na temat ustawień interfejsu sieciowego i sposobu ich zmieniania, zobacz [Zarządzanie interfejsami sieciowym.](virtual-network-network-interface.md) Aby dowiedzieć się, jak dodawać, zmieniać lub usuwać adresy IP przypisane do interfejsu sieciowego, zobacz Zarządzanie [adresami IP interfejsu sieciowego.](virtual-network-network-interface-addresses.md)

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Usuwanie interfejsu sieciowego z maszyny wirtualnej

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz **pozycję Maszyny wirtualne.**

2. Wybierz nazwę maszyny wirtualnej, dla której chcesz wyświetlić dołączone interfejsy sieciowe.

3. Na pasku narzędzi maszyny wirtualnej wybierz przycisk **Zatrzymaj.**

4. Poczekaj, aż **stan maszyny** wirtualnej zmieni się na **Zatrzymana (cofniesz jej alokację).**

5. Na pasku menu maszyny wirtualnej wybierz pozycję **Sieć** Odłącz  >  **interfejs sieciowy.**

6. W **oknie dialogowym** Odłącz interfejs sieciowy wybierz interfejs sieciowy, który chcesz odłączyć. Następnie wybierz przycisk **OK**.

    >[!NOTE]
    >Jeśli na liście znajduje się tylko jeden interfejs sieciowy, nie można go odłączyć, ponieważ maszyna wirtualna musi zawsze mieć dołączony co najmniej jeden interfejs sieciowy.

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (odwołanie); [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie); [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Ograniczenia

- Do maszyny wirtualnej musi być dołączony co najmniej jeden interfejs sieciowy.

- Maszyna wirtualna może mieć tylko tyle interfejsów sieciowych, ile obsługuje rozmiar maszyny wirtualnej. Aby dowiedzieć się więcej o tym, ile interfejsów sieciowych obsługuje każdy rozmiar maszyny wirtualnej, zobacz rozmiary na platformie Azure dla maszyn wirtualnych z systemem [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [lub maszyn wirtualnych z systemem Windows.](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Wszystkie rozmiary obsługują co najmniej dwa interfejsy sieciowe.

- Interfejsów sieciowych, które dodajesz do maszyny wirtualnej, nie można obecnie dołączyć do innej maszyny wirtualnej. Aby dowiedzieć się więcej na temat tworzenia interfejsów sieciowych, zobacz [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface).

- W przeszłości interfejsy sieciowe można było dodawać tylko do maszyn wirtualnych, które obsługuje wiele interfejsów sieciowych i zostały utworzone przy użyciu co najmniej dwóch interfejsów sieciowych. Nie można dodać interfejsu sieciowego do maszyny wirtualnej, która została utworzona przy użyciu jednego interfejsu sieciowego, nawet jeśli rozmiar maszyny wirtualnej obsługuje więcej niż jeden interfejs sieciowy. Z drugiej strony można usunąć tylko interfejsy sieciowe z maszyny wirtualnej z co najmniej trzema interfejsami sieciowym, ponieważ maszyny wirtualne utworzone przy użyciu co najmniej dwóch interfejsów sieciowych zawsze muszą mieć co najmniej dwa interfejsy sieciowe. Te ograniczenia nie mają już zastosowania. Teraz możesz utworzyć maszynę wirtualną z dowolną liczbą interfejsów sieciowych (do liczby obsługiwanej przez rozmiar maszyny wirtualnej).

- Domyślnie pierwszym interfejsem sieciowym dołączonym do maszyny wirtualnej jest *podstawowy interfejs* sieciowy. Wszystkie inne interfejsy sieciowe na maszynie wirtualnej są *pomocniczym interfejsem* sieciowym.

- Możesz kontrolować, do którego interfejsu sieciowego jest przesyłany ruch wychodzący. Jednak domyślnie maszyna wirtualna wysyła cały ruch wychodzący na adres IP przypisany do podstawowej konfiguracji adresu IP podstawowego interfejsu sieciowego.

- W przeszłości wszystkie maszyny wirtualne w tym samym zestawie dostępności miały jeden lub wiele interfejsów sieciowych. Maszyny wirtualne z dowolną liczbą interfejsów sieciowych mogą teraz istnieć w tym samym zestawie dostępności do liczby obsługiwanej przez rozmiar maszyny wirtualnej. Maszynę wirtualną można dodać do zestawu dostępności tylko po jej utworzeniu. Aby dowiedzieć się więcej na temat zestawów dostępności, zobacz Manage the availability of VMs in Azure (Zarządzanie [dostępnością maszyn wirtualnych na platformie Azure).](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

- Interfejsy sieciowe na tej samej maszynie wirtualnej można połączyć z różnymi podsieciami w sieci wirtualnej. Jednak wszystkie interfejsy sieciowe muszą być podłączone do tej samej sieci wirtualnej.

- Dowolny adres IP dla dowolnej konfiguracji adresu IP dowolnego podstawowego lub pomocniczego interfejsu sieciowego można dodać do Azure Load Balancer puli. W przeszłości do puli back-end można było dodać tylko podstawowy adres IP podstawowego interfejsu sieciowego. Aby dowiedzieć się więcej na temat adresów IP i konfiguracji, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP.](virtual-network-network-interface-addresses.md)

- Usunięcie maszyny wirtualnej nie powoduje usunięcia dołączonych do niej interfejsów sieciowych. Po usunięciu maszyny wirtualnej interfejsy sieciowe są odłączane od maszyny wirtualnej. Możesz dodać te interfejsy sieciowe do różnych maszyn wirtualnych lub usunąć je.

- Uzyskanie udokumentowanej optymalnej wydajności wymaga przyspieszonej sieci. W niektórych przypadkach należy jawnie włączyć przyspieszoną sieć dla maszyn wirtualnych z systemem [Windows](create-vm-accelerated-networking-powershell.md) [lub Linux.](create-vm-accelerated-networking-cli.md)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="next-steps"></a>Następne kroki

Aby utworzyć maszynę wirtualną z wieloma interfejsami sieciowym lub adresami IP, zobacz:

|Zadanie|Narzędzie|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi|[Interfejs wiersza polecenia,](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [program PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z wieloma adresami IPv4|[Interfejs wiersza polecenia,](virtual-network-multiple-ip-addresses-cli.md) [program PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z prywatnym adresem IPv6 (za Azure Load Balancer)|[Interfejs wiersza](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)polecenia, [program PowerShell,](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Azure Resource Manager szablonu](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|