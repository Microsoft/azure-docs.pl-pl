---
title: Często zadawane pytania dotyczące maszyn wirtualnych systemu Windows na platformie Azure
description: Zawiera odpowiedzi na niektóre z typowych pytań dotyczących maszyn wirtualnych systemu Windows utworzonych za pomocą modelu Menedżera zasobów.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 33d530181f4cc389f81ea83dc42b774ccfc8fd85
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869618"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Często zadawane pytania dotyczące maszyn wirtualnych systemu Windows
Ten artykuł dotyczy niektórych typowych pytań dotyczących maszyn wirtualnych systemu Windows utworzonych na platformie Azure przy użyciu modelu wdrażania Usługi Resource Manager. Zobacz też: Często zadawane [pytania dotyczące maszyn wirtualnych systemu Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Aby uzyskać informacje na temat zasad pomocy technicznej dotyczących uruchamiania oprogramowania serwera firmy Microsoft na platformie Azure, zobacz [Pomoc techniczna dotycząca oprogramowania serwera firmy Microsoft dla maszyn wirtualnych platformy Azure](https://support.microsoft.com/kb/2721672).

Niektóre wersje systemów Windows 7, Windows 8.1 i Windows 10 są dostępne dla subskrybentów platformy MSDN Azure oraz subskrybentów MSDN Dev i Test Pay-As-You-Go do zadań programistycznych i testowych. Aby uzyskać szczegółowe informacje, łącznie z instrukcjami i ograniczeniami, zobacz [Windows Client images for MSDN subscribers](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) (Obrazy klienta systemu Windows dla subskrybentów MSDN). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk danych może mieć maksymalnie 32 767 GiB. Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Rozmiary maszyn wirtualnych).

Dyski zarządzane platformy Azure to zalecane oferty magazynu dysków do użytku z maszynami wirtualnymi platformy Azure do trwałego przechowywania danych. Z każdej maszyną wirtualną można używać wielu dysków zarządzanych. Dyski zarządzane oferują dwa typy opcji trwałego przechowywania: dyski zarządzane w wersji Premium i standardowe. Aby uzyskać informacje o cenach, zobacz [Ceny dysków zarządzanych](https://azure.microsoft.com/pricing/details/managed-disks).

Konta magazynu platformy Azure mogą również zapewnić magazyn dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak uzyskać dostęp do mojej maszyny wirtualnej?
Ustanawianie połączenia zdalnego przy użyciu usługi Podłączanie pulpitu zdalnego (RDP) dla maszyny Wirtualnej systemu Windows. Aby uzyskać instrukcje, zobacz [Jak połączyć się i zalogować się na maszynie wirtualnej platformy Azure z systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Obsługiwane są maksymalnie dwa połączenia równoczesne, chyba że serwer jest skonfigurowany jako host sesji usług pulpitu zdalnego.  

Jeśli masz problemy z pulpitem zdalnym, zobacz [Rozwiązywanie problemów z połączeniami pulpitu zdalnego z maszyną wirtualną platformy Azure z systemem Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Jeśli znasz program Hyper-V, być może szukasz narzędzia podobnego do VMConnect. Platforma Azure nie oferuje podobnego narzędzia, ponieważ dostęp konsoli do maszyny wirtualnej nie jest obsługiwany.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Czy mogę używać dysku tymczasowego (domyślnie dysku D:) do przechowywania danych?
Nie używaj dysku tymczasowego do przechowywania danych. Jest to tylko tymczasowe przechowywanie, więc można ryzykować utratę danych, które nie mogą być odzyskane. Utrata danych może wystąpić, gdy maszyna wirtualna przenosi się do innego hosta. Zmiana rozmiaru maszyny wirtualnej, aktualizowanie hosta lub awaria sprzętu na hoście to kilka przyczyn, które mogą spowodować przeniesienie maszyny wirtualnej.

Jeśli masz aplikację, która musi używać litery dysku D:, możesz ponownie przypisać litery dysków, aby dysk tymczasowy używał czegoś innego niż D:. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak mogę zmienić literę dysku tymczasowego?
Możesz zmienić literę dysku, przesuwając plik strony i ponownie przypisując litery dysków, ale musisz upewnić się, że wykonasz czynności w określonej kolejności. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Czy mogę dodać istniejącą maszynę wirtualną do zestawu dostępności?
Nie. Jeśli chcesz, aby maszyna wirtualna była częścią zestawu dostępności, musisz utworzyć maszynę wirtualną w zestawie. Obecnie nie ma sposobu, aby dodać maszynę wirtualną do zestawu dostępności po jej utworzeniu.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Czy mogę przekazać maszynę wirtualną na platformę Azure?
Tak. Aby uzyskać instrukcje, zobacz [Migrowanie lokalnych maszyn wirtualnych na platformę Azure.](on-prem-to-azure.md)

## <a name="can-i-resize-the-os-disk"></a>Czy można zmienić rozmiar dysku systemu operacyjnego?
Tak. Aby uzyskać instrukcje, zobacz [Jak rozwinąć dysk systemu operacyjnego maszyny wirtualnej w grupie zasobów platformy Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Czy można skopiować lub sklonować istniejącą maszynę wirtualną platformy Azure?
Tak. Za pomocą obrazów zarządzanych można utworzyć obraz maszyny wirtualnej, a następnie użyć obrazu do tworzenia wielu nowych maszyn wirtualnych. Aby uzyskać instrukcje, zobacz [Tworzenie niestandardowego obrazu maszyny Wirtualnej](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę regionów Kanada Środkowa i Kanada Wschodnia za pośrednictwem usługi Azure Resource Manager?

Dwa nowe regiony Kanada Central i Kanada Wschód nie są automatycznie rejestrowane do tworzenia maszyn wirtualnych dla istniejących subskrypcji platformy Azure. Ta rejestracja jest wykonywana automatycznie, gdy maszyna wirtualna jest wdrażana za pośrednictwem witryny Azure portal do dowolnego innego regionu przy użyciu usługi Azure Resource Manager. Po wdrożeniu maszyny wirtualnej w dowolnym innym regionie platformy Azure nowe regiony powinny być dostępne dla kolejnych maszyn wirtualnych.

## <a name="does-azure-support-linux-vms"></a>Czy platforma Azure obsługuje maszyny wirtualne z systemem Linux?
Tak. Aby szybko utworzyć maszynę wirtualną z systemem Linux do wypróbowania, zobacz [Tworzenie maszyny Wirtualnej z systemem Linux na platformie Azure przy użyciu portalu](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Czy mogę dodać kartę sieciową do maszyny Wirtualnej po jej utworzeniu?
Tak, jest to teraz możliwe. Maszyna wirtualna najpierw musi zostać zatrzymana cofnięto alokację. Następnie można dodać lub usunąć kartę sieciową (chyba że jest to ostatnia karta sieciowa na maszynie Wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy są jakieś wymagania dotyczące nazwy komputera?
Tak. Nazwa komputera może mieć maksymalnie 15 znaków długości. Zobacz [Reguły i ograniczenia konwencji nazewnictwa, aby](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) uzyskać więcej informacji na temat nazywania zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy są jakieś wymagania dotyczące nazwy grupy zasobów?
Tak. Nazwa grupy zasobów może mieć maksymalnie 90 znaków. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Reguły i ograniczenia konwencji nazewnictwa.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny Wirtualnej?

Nazwy użytkowników mogą mieć maksymalnie 20 znaków i nie mogą kończyć się kropką ("."). 

Następujące nazwy użytkowników nie są dozwolone:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła podczas tworzenia maszyny Wirtualnej?

Istnieją różne wymagania dotyczące długości hasła, w zależności od używanego narzędzia:
 - Portal - od 12 do 72 znaków
 - PowerShell - od 8 do 123 znaków
 - CLI - między 12 - 123

* Musi zawierać małe litery
* Musi zawierać wielkie litery
* Musi zawierać cyfrę
* Musi zawierać znak specjalny (zgodność wyrażenia regularnego [\W_])

Następujące hasła nie są dozwolone:

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Hasło!</td>
        <td>Hasło1</td>
        <td>Hasło22</td>
    </tr>
</table>