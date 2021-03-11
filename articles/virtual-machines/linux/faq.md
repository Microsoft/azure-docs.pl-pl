---
title: Często zadawane pytania dotyczące maszyn wirtualnych z systemem Linux na platformie Azure
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące maszyn wirtualnych z systemem Linux utworzonych przy użyciu modelu Menedżer zasobów.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 22be45403a7863328c5f6f2c883886296b734914
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549094"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Często zadawane pytania dotyczące Linux Virtual Machines
W tym artykule opisano niektóre często zadawane pytania dotyczące maszyn wirtualnych z systemem Linux utworzonych na platformie Azure przy użyciu modelu wdrażania Menedżer zasobów. Aby uzyskać informacje na temat wersji systemu Windows w tym temacie, zobacz [często zadawane pytania dotyczące Windows Virtual Machines](../windows/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [Linux na Azure-Endorsed dystrybucji](endorsed-distros.md)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk z danymi może mieć do 32 767 GiB. Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](../sizes.md) (Rozmiary maszyn wirtualnych).

Usługa Azure Managed Disks to zalecane oferty magazynu dyskowego do użycia z usługą Azure Virtual Machines na potrzeby trwałego magazynowania danych. Do każdej maszyny wirtualnej można użyć wielu Managed Disks. Managed Disks oferuje dwa typy opcji trwałego magazynu: Managed Disks Premium i standardowa. Aby uzyskać informacje o cenach, zobacz [Cennik usługi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks).

Konta usługi Azure Storage mogą również udostępniać magazyn dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak mogę uzyskać dostęp do mojej maszyny wirtualnej?
Nawiąż połączenie zdalne, aby zalogować się do maszyny wirtualnej przy użyciu Secure Shell (SSH). Zapoznaj się z instrukcjami dotyczącymi sposobu nawiązywania połączenia [z systemem Windows](ssh-from-windows.md) lub [z systemów Linux i Mac](mac-create-ssh-keys.md). Domyślnie protokół SSH umożliwia maksymalnie 10 równoczesnych połączeń. Możesz zwiększyć tę liczbę, edytując plik konfiguracji.

Jeśli masz problemy, sprawdź [Rozwiązywanie problemów z połączeniami Secure Shell (SSH)](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Czy można używać dysku tymczasowego (/dev/sdb1) do przechowywania danych?
Nie używaj dysku tymczasowego (/dev/sdb1) do przechowywania danych. Tylko w przypadku magazynu tymczasowego. Ryzyko utraty danych, których nie można odzyskać.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Czy mogę skopiować lub sklonować istniejącą maszynę wirtualną platformy Azure?
Tak. Aby uzyskać instrukcje, zobacz [jak utworzyć kopię maszyny wirtualnej z systemem Linux w modelu wdrażania Menedżer zasobów](copy-vm.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę regionów Kanady środkowe i Kanada Wschodnia za poorednictwem Azure Resource Manager?
Dwa nowe regiony Kanady środkowe i Kanada Wschodnia nie są automatycznie rejestrowane na potrzeby tworzenia maszyn wirtualnych dla istniejących subskrypcji platformy Azure. Ta rejestracja jest wykonywana automatycznie, gdy maszyna wirtualna jest wdrażana za pośrednictwem Azure Portal w innym regionie przy użyciu Azure Resource Manager. Po wdrożeniu maszyny wirtualnej w innym regionie świadczenia usługi Azure nowe regiony powinny być dostępne dla kolejnych maszyn wirtualnych.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Czy mogę dodać kartę sieciową do mojej maszyny wirtualnej po jej utworzeniu?
Tak. teraz jest to możliwe. Najpierw należy zatrzymać cofniętą alokację maszyny wirtualnej. Następnie można dodać lub usunąć kartę sieciową (chyba że jest to ostatnia karta sieciowa na maszynie wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy istnieją jakieś wymagania dotyczące nazw komputerów?
Tak. Nazwa komputera może zawierać maksymalnie 64 znaków. Zobacz [reguły nazewnictwa i ograniczenia,](/azure/architecture/best-practices/resource-naming) Aby uzyskać więcej informacji na temat nazywania zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy istnieją jakieś wymagania dotyczące nazw grup zasobów?
Tak. Nazwa grupy zasobów może zawierać maksymalnie 90 znaków. Aby uzyskać więcej informacji na temat grup zasobów [, zobacz Reguły nazewnictwa i ograniczenia](/azure/architecture/best-practices/resource-naming) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny wirtualnej?

Nazwy użytkowników powinny mieć długość 1-32 znaków.

Następujące nazwy użytkowników są niedozwolone:

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła podczas tworzenia maszyny wirtualnej?

Istnieją różne wymagania dotyczące długości hasła, w zależności od używanego narzędzia:
 - Portal — od 12-72 znaków
 - PowerShell — od 8-123 znaków
 - Interfejs wiersza polecenia — od 12-123 znaków
 - Szablony Azure Resource Manager (ARM) — znaki o długości 12-72 i znaki kontrolne są niedozwolone
 

Hasła muszą również spełniać 3 z następujących wymagań dotyczących złożoności:

* Musi zawierać małe litery
* Musi zawierać wielkie litery
* Musi zawierać cyfrę
* Musi zawierać znak specjalny (zgodność wyrażenia regularnego [\W_])

Następujące hasła są niedozwolone:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@ $ $w 0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA $ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Hasło!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
