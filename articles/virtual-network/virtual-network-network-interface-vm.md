---
title: Dodawanie interfejsów sieciowych do maszyn wirtualnych platformy Azure lub usuwanie ich z niej
description: Dowiedz się, jak dodać interfejsy sieciowe do lub usunąć interfejsy sieciowe z maszyn wirtualnych.
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
ms.openlocfilehash: 2b45bd2e2b9ad971891cbcb944286f40b4f66883
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216975"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Dodawanie interfejsów sieciowych do maszyn wirtualnych lub ich usuwanie

Dowiedz się, jak dodać istniejący interfejs sieciowy podczas tworzenia maszyny wirtualnej platformy Azure. Dowiedz się również, jak dodać lub usunąć interfejsy sieciowe z istniejącej maszyny wirtualnej w stanie zatrzymania (bez przydziału). Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure komunikowanie się z Internetem, platformą Azure i zasobami lokalnymi. Maszyna wirtualna ma co najmniej jeden interfejs sieciowy. 

Jeśli musisz dodać, zmienić lub usunąć adresy IP dla interfejsu sieciowego, zobacz [Zarządzanie adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md). Aby tworzyć, zmieniać lub usuwać interfejsy sieciowe, zobacz [Zarządzanie interfejsami sieciowymi](virtual-network-network-interface.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Wykonaj jedno z tych zadań przed rozpoczęciem pozostałej części tego artykułu:

- **Użytkownicy portalu**: Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

- **Użytkownicy programu PowerShell**: Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub Uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Azure Cloud Shell Browser Znajdź listę rozwijaną **Wybierz środowisko** , a następnie wybierz opcję **PowerShell** , jeśli nie została jeszcze wybrana.

    Jeśli używasz programu PowerShell lokalnie, użyj Azure PowerShell module w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure (CLI)**: uruchamiają polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiają interfejs CLI z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.26 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom polecenie `az login`, aby utworzyć połączenia z platformą Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Dodawanie istniejących interfejsów sieciowych do nowej maszyny wirtualnej

Gdy tworzysz maszynę wirtualną za pomocą portalu, Portal tworzy interfejs sieciowy z ustawieniami domyślnymi i dołącza interfejs sieciowy do maszyny wirtualnej. Nie można używać portalu do dodawania istniejących interfejsów sieciowych do nowej maszyny wirtualnej lub do tworzenia maszyny wirtualnej z wieloma interfejsami sieciowymi. Można to zrobić przy użyciu interfejsu wiersza polecenia lub programu PowerShell. Pamiętaj, aby zaznajomić się z [ograniczeniami](#constraints). W przypadku tworzenia maszyny wirtualnej z wieloma interfejsami sieciowymi należy również skonfigurować system operacyjny, aby korzystał z nich prawidłowo po utworzeniu maszyny wirtualnej. Dowiedz się, jak skonfigurować [system](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub Windows dla wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia

Przed utworzeniem maszyny wirtualnej [Utwórz interfejs sieciowy](virtual-network-network-interface.md#create-a-network-interface).

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Dodawanie interfejsu sieciowego do istniejącej maszyny wirtualnej

Aby dodać interfejs sieciowy do maszyny wirtualnej:

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

2. Wybierz nazwę maszyny wirtualnej. Maszyna wirtualna musi obsługiwać liczbę interfejsów sieciowych, które chcesz dodać. Aby dowiedzieć się, ile interfejsów sieciowych obsługuje każdy rozmiar maszyny wirtualnej, zobacz rozmiary na platformie Azure dla [maszyn wirtualnych systemu Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [maszyn wirtualnych z systemem Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Na pasku poleceń maszyny wirtualnej wybierz pozycję **Zatrzymaj**, a następnie kliknij przycisk **OK** w oknie dialogowym potwierdzenia. Następnie poczekaj, aż **stan** maszyny wirtualnej zmieni się na **zatrzymano (cofnięto przydział)**.

4. Na pasku menu maszyny wirtualnej wybierz pozycję **Sieć**  >  **Dołącz interfejs sieciowy**. Następnie w polu **Dołącz istniejący interfejs sieciowy** wybierz interfejs sieciowy, który chcesz dołączyć, a następnie wybierz **przycisk OK**.

    >[!NOTE]
    >Wybrany interfejs sieciowy nie może mieć włączonej przyspieszonej sieci, nie może mieć przypisanego adresu IPv6 i musi znajdować się w tej samej sieci wirtualnej, w której interfejs sieciowy jest obecnie dołączony do maszyny wirtualnej.

    Jeśli nie masz istniejącego interfejsu sieciowego, musisz go najpierw utworzyć. W tym celu wybierz pozycję **Utwórz interfejs sieciowy**. Aby dowiedzieć się więcej na temat tworzenia interfejsu sieciowego, zobacz [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface). Aby dowiedzieć się więcej na temat dodatkowych ograniczeń podczas dodawania interfejsów sieciowych do maszyn wirtualnych, zobacz [ograniczenia](#constraints).

5. Na pasku menu maszyny wirtualnej wybierz pozycję **Przegląd**  >  **Rozpocznij** , aby ponownie uruchomić maszynę wirtualną.

Teraz można skonfigurować system operacyjny maszyny wirtualnej tak, aby używał wielu interfejsów sieciowych prawidłowo. Dowiedz się, jak skonfigurować [system](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) lub Windows dla wielu interfejsów sieciowych.

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ VM nic Add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (odwołanie); [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie); [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Wyświetl interfejsy sieciowe dla maszyny wirtualnej

Możesz wyświetlić interfejsy sieciowe aktualnie dołączone do maszyny wirtualnej, aby dowiedzieć się więcej na temat konfiguracji poszczególnych interfejsów sieciowych i adresów IP przypisanych do każdego interfejsu sieciowego. 

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

    >[!NOTE]
    >Zaloguj się przy użyciu konta, któremu przypisano rolę współautor właściciela, współautora lub sieci dla subskrypcji. Aby dowiedzieć się więcej o sposobie przypisywania ról do kont, zobacz [wbudowane role dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).

2. Wybierz nazwę maszyny wirtualnej, dla której chcesz wyświetlić dołączone interfejsy sieciowe.

3. Na pasku menu maszyny wirtualnej wybierz pozycję **Sieć**.

Aby dowiedzieć się więcej o ustawieniach interfejsu sieciowego i sposobach ich zmiany, zobacz [Zarządzanie interfejsami sieciowymi](virtual-network-network-interface.md). Aby dowiedzieć się, jak dodawać, zmieniać lub usuwać adresy IP przypisane do interfejsu sieciowego, zobacz [Zarządzanie adresami IP interfejsu sieciowego](virtual-network-network-interface-addresses.md).

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ VM nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Usuwanie interfejsu sieciowego z maszyny wirtualnej

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby znaleźć istniejącą maszynę wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

2. Wybierz nazwę maszyny wirtualnej, dla której chcesz wyświetlić dołączone interfejsy sieciowe.

3. Na pasku narzędzi maszyny wirtualnej wybierz pozycję **Zatrzymaj**.

4. Zaczekaj, aż **stan** maszyny wirtualnej zmieni się na **zatrzymane (cofnięto przydział)**.

5. Na pasku menu maszyny wirtualnej wybierz kolejno opcje **Sieć**  >  **Odłącz interfejs sieciowy**.

6. W oknie dialogowym **Odłącz interfejs sieciowy** wybierz interfejs sieciowy, który chcesz odłączyć. Następnie wybierz przycisk **OK**.

    >[!NOTE]
    >Jeśli na liście znajduje się tylko jeden interfejs sieciowy, nie można go odłączyć, ponieważ maszyna wirtualna musi mieć dołączony co najmniej jeden interfejs sieciowy.

### <a name="commands"></a>Polecenia

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ VM nic Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (odwołanie); [szczegółowe kroki](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (odwołanie); [szczegółowe kroki](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Ograniczenia

- Do maszyny wirtualnej musi być dołączony co najmniej jeden interfejs sieciowy.

- Maszyna wirtualna może mieć tylko tyle interfejsów sieciowych dołączonych do niej, ponieważ rozmiar maszyny wirtualnej obsługuje. Aby dowiedzieć się więcej o liczbie interfejsów sieciowych obsługiwanych przez poszczególne rozmiary maszyn wirtualnych, zobacz rozmiary [maszyn wirtualnych](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) na platformie Azure lub [maszyn wirtualnych z systemem Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wszystkie rozmiary obsługują co najmniej dwa interfejsy sieciowe.

- Interfejsy sieciowe dodawane do maszyny wirtualnej nie mogą być obecnie dołączone do innej maszyny wirtualnej. Aby dowiedzieć się więcej na temat tworzenia interfejsów sieciowych, zobacz [Tworzenie interfejsu sieciowego](virtual-network-network-interface.md#create-a-network-interface).

- W przeszłości można dodać interfejsy sieciowe tylko do maszyn wirtualnych, które obsługują wiele interfejsów sieciowych i zostały utworzone przy użyciu co najmniej dwóch interfejsów sieciowych. Nie można dodać interfejsu sieciowego do maszyny wirtualnej, która została utworzona przy użyciu jednego interfejsu sieciowego, nawet jeśli rozmiar maszyny wirtualnej obsługuje więcej niż jeden interfejs sieciowy. Z kolei można usunąć tylko interfejsy sieciowe z maszyny wirtualnej z co najmniej trzema interfejsami sieciowymi, ponieważ maszyny wirtualne utworzone przy użyciu co najmniej dwóch interfejsów sieciowych zawsze musiały mieć co najmniej dwa interfejsy sieciowe. Te ograniczenia nie mają już zastosowania. Teraz można utworzyć maszynę wirtualną z dowolną liczbą interfejsów sieciowych (do liczby obsługiwanej przez rozmiar maszyny wirtualnej).

- Domyślnie pierwszy interfejs sieciowy dołączony do maszyny wirtualnej jest *głównym* interfejsem sieciowym. Wszystkie inne interfejsy sieciowe w maszynie wirtualnej są *dodatkowymi* interfejsami sieciowymi.

- Można kontrolować, do którego interfejsu sieciowego wysyłany jest ruch wychodzący. Jednak maszyna wirtualna domyślnie wysyła cały ruch wychodzący do adresu IP przypisanego do podstawowej konfiguracji adresu IP podstawowego interfejsu sieciowego.

- W przeszłości wszystkie maszyny wirtualne w tym samym zestawie dostępności musiały mieć jeden lub wiele interfejsów sieciowych. Maszyny wirtualne z dowolną liczbą interfejsów sieciowych mogą teraz istnieć w tym samym zestawie dostępności, do liczby obsługiwanej przez rozmiar maszyny wirtualnej. Możesz dodać maszynę wirtualną do zestawu dostępności, gdy zostanie on utworzony. Aby dowiedzieć się więcej o zestawach dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych na platformie Azure](../virtual-machines/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

- Interfejsy sieciowe można podłączyć do różnych podsieci w sieci wirtualnej. Jednak wszystkie interfejsy sieciowe muszą być połączone z tą samą siecią wirtualną.

- Można dodać dowolny adres IP dla dowolnej konfiguracji protokołu IP dowolnego podstawowego lub pomocniczego interfejsu sieciowego do puli zaplecza Azure Load Balancer. W przeszłości tylko podstawowy adres IP dla podstawowego interfejsu sieciowego można dodać do puli zaplecza. Aby dowiedzieć się więcej o adresach IP i konfiguracjach, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md).

- Usunięcie maszyny wirtualnej nie powoduje usunięcia podłączonych do niej interfejsów sieciowych. Po usunięciu maszyny wirtualnej interfejsy sieciowe są odłączone od maszyny wirtualnej. Możesz dodać te interfejsy sieciowe do różnych maszyn wirtualnych lub je usunąć.

- Osiągnięcie optymalnej wydajności wymaga użycia przyspieszonej sieci. W niektórych przypadkach należy jawnie włączyć przyspieszone sieci dla maszyn wirtualnych z [systemem Windows](create-vm-accelerated-networking-powershell.md) lub [Linux](create-vm-accelerated-networking-cli.md) .

## <a name="next-steps"></a>Następne kroki

Aby utworzyć maszynę wirtualną z wieloma interfejsami sieciowymi lub adresami IP, zobacz:

|Zadanie|Narzędzie|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi|[Interfejs wiersza polecenia](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), program [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z wieloma adresami IPv4|[Interfejs wiersza polecenia](virtual-network-multiple-ip-addresses-cli.md), program [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Utwórz pojedynczą maszynę wirtualną kart sieciowych z prywatnym adresem IPv6 (za Azure Load Balancer)|[Interfejs wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), program [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [szablon Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|