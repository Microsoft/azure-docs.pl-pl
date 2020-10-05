---
title: Tworzenie sieci wirtualnej — Szybki start — Azure Portal
titleSuffix: Azure Virtual Network
description: 'Szybki Start: tworzenie sieci wirtualnej w Azure Portal. Te sieci pozwalają na zasoby platformy Azure, takie jak maszyny wirtualne, bezpiecznie komunikować się ze sobą i z Internetem.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: dfeb9c6bc3dbe33881fc51ccb08a776b827dfe9c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "86082441"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu witryny Azure Portal

W tym przewodniku szybki start dowiesz się, jak utworzyć sieć wirtualną przy użyciu Azure Portal. Wdrażane są dwie maszyny wirtualne. Następnie można bezpiecznie komunikować się między maszynami wirtualnymi i łączyć się z maszynami wirtualnymi z Internetu. Sieć wirtualna jest podstawowym blokiem konstrukcyjnym sieci prywatnej na platformie Azure. Pozwala ona na bezpieczne komunikowanie się ze sobą i z Internetem za pomocą zasobów platformy Azure, takich jak maszyny wirtualne.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. W portalu Azure Marketplace wybierz pozycję **Sieć**  >  **Sieć wirtualna**.

1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Nazwa | Wpisz *myVirtualNetwork*. |
    | Lokalizacja | Wybierz pozycję **Wschodnie stany USA**.|

1. Wybierz pozycję **Dalej: adresy IP**, a dla **przestrzeni adresów IPv4**wprowadź *10.1.0.0/16*.

1. Wybierz pozycję **Dodaj podsieć**, a następnie wprowadź *MyVirtualSubnet* dla **nazwy podsieci** i *10.1.0.0/24* dla **zakresu adresów podsieci**.

1. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Przegląd + Utwórz**. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.

1. W obszarze **Utwórz sieć wirtualną**wybierz pozycję **Utwórz**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne:

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**.

1. W portalu Azure Marketplace wybierz pozycję **COMPUTE**  >  **Windows Server 2019 Datacenter**. Wybierz przycisk **Utwórz**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Ta grupa zasobów została utworzona w poprzedniej sekcji. |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź nazwę *myVm1*. |
    | Region | Wybierz pozycję **Wschodnie stany USA**. |
    | Opcje dostępności | Domyślnie **nie jest wymagana żadna nadmiarowość infrastruktury**. |
    | Image (Obraz) | Domyślnie dla **systemu Windows Server 2019 Datacenter**. |
    | Rozmiar | Domyślnie **standardowa DS1 v2**. |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Wprowadź ponownie hasło. |
    | **Reguły portów ruchu przychodzącego** |  |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wprowadź *protokół http (80)* i *protokół RDP (3389)*. |
    | **Oszczędność pieniędzy** |  |
    | Masz już licencję systemu Windows? | Domyślnie wartość **nie**. |

1. Wybierz pozycję **Dalej: dyski**.

1. W obszarze **Utwórz maszynę wirtualną**Zachowaj ustawienia domyślne i wybierz pozycję **Dalej: sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Wartość domyślna to **myVirtualNetwork**. |
    | Podsieć | Domyślnie **myVirtualSubnet (10.1.0.0/24)**. |
    | Publiczny adres IP | Wartość domyślna to **(New) myVm-IP**. |
    | Grupa zabezpieczeń sieci karty sieciowej | Wartość domyślna to **podstawowa**. |
    | Publiczne porty wejściowe | Domyślnie **zezwala na wybrane porty**. |
    | Wybierz porty wejściowe | Domyślnie dla **protokołów HTTP** i **RDP**.

1. Wybierz pozycję **Dalej: Zarządzanie**.

1. W obszarze **Tworzenie maszyny wirtualnej — zarządzanie** dla opcji **Konto magazynu diagnostyki** wybierz ustawienie **Utwórz nowe**.

1. W obszarze **Tworzenie konta magazynu** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź nazwę *myvmstorageaccount*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
    | Rodzaj konta | Domyślne do **magazynu (ogólnego przeznaczenia w wersji 1)**. |
    | Wydajność | Domyślnie **standardowa**. |
    | Replikacja | Domyślnie **Magazyn lokalnie nadmiarowy (LRS)**. |

1. Wybierz pozycję **OK**, a następnie wybierz pozycję **Przegląd + Utwórz**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

1. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Powtórz procedurę opisaną w poprzedniej sekcji, aby utworzyć inną maszynę wirtualną.

> [!IMPORTANT]
> W polu **Nazwa maszyny wirtualnej**wprowadź *myVm2*.
>
> W przypadku **konta magazynu diagnostyki**upewnij się, że wybrano opcję **myvmstorageaccount**, a nie utwórz ją.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Po utworzeniu *myVm1*Połącz się z Internetem.

1. W Azure Portal Wyszukaj i wybierz pozycję *myVm1*.

1. Wybierz pozycję **Połącz**, a następnie **protokół RDP**.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/quick-create-portal/connect-to-virtual-machine.png)

    Zostanie otwarta strona **łączenie** .

1. Wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy plik Remote Desktop Protocol (*RDP*) i pobiera go na komputer.

1. Otwórz plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.

1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

    > [!NOTE]
    > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz przycisk **OK**.

1. Po zalogowaniu się może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. Na pulpicie zdalnym maszyny *myVm1* otwórz program PowerShell.

1. Wprowadź `ping myVm2`.

    Zostanie wyświetlony komunikat podobny do tego wyjściowego:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Polecenie `ping` kończy się niepowodzeniem, ponieważ polecenie `ping` używa protokołu ICMP. Domyślnie protokół ICMP jest blokowany przez zaporę Windows.

1. Aby umożliwić wykonanie polecenia ping na maszynie *myVm2* w celu komunikacji z maszyną *myVm1* w późniejszym kroku, wprowadź następujące polecenie:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    To polecenie zezwala na ruch przychodzący protokołu ICMP przez zaporę systemu Windows:

1. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm1*.

1. Ponownie wykonaj kroki opisane w sekcji [Nawiązywanie połączenia z maszyną wirtualną z Internetu](#connect-to-a-vm-from-the-internet), ale tym razem nawiąż połączenie z maszyną wirtualną *myVm2*.

1. W wierszu polecenia wprowadź polecenie `ping myvm1`.

    Zostanie wyświetlony komunikat podobny do następującego:

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Otrzymujesz odpowiedzi z *myVm1*, ponieważ zezwolono na ICMP przez zaporę systemu Windows na maszynie wirtualnej *myVm1* w kroku 3.

1. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm2*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym przewodniku Szybki start utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. Nawiązano połączenie z jedną maszyną wirtualną z Internetu i bezpiecznie komunikuje się między tymi dwiema maszynami wirtualnymi.

Gdy skończysz korzystać z sieci wirtualnej i maszyn wirtualnych, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wyszukaj i wybierz pozycję Moja *resourceName*.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat ustawień sieci wirtualnej, zobacz [Tworzenie, zmienianie lub usuwanie sieci wirtualnej](manage-virtual-network.md).

Domyślnie platforma Azure umożliwia bezpieczną komunikację między maszynami wirtualnymi. Na platformie Azure dozwolone są tylko przychodzące połączenia pulpitu zdalnego z maszynami wirtualnymi z systemem Windows z Internetu. Aby dowiedzieć się więcej o typach komunikacji sieciowej maszyny wirtualnej, zobacz [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md).

> [!NOTE] 
> Opłaty za usługi platformy Azure. Azure Cost Management ułatwia ustawianie budżetów i Konfigurowanie alertów w celu utrzymywania wydatków pod kontrolą. Analizowanie i optymalizowanie kosztów platformy Azure oraz zarządzanie nimi za pomocą Cost Management. Aby dowiedzieć się więcej, zobacz [Przewodnik Szybki Start dotyczący analizowania kosztów](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).