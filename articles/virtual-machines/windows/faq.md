---
title: Często zadawane pytania dotyczące maszyn wirtualnych z systemem Windows na platformie Azure
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące maszyn wirtualnych z systemem Windows utworzonych przy użyciu modelu Menedżer zasobów.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: d5dda567d0cfa89863731a3e8e651aede4ab5957
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87825195"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Często zadawane pytania dotyczące Windows Virtual Machines
W tym artykule opisano niektóre często zadawane pytania dotyczące maszyn wirtualnych z systemem Windows utworzonych na platformie Azure przy użyciu modelu wdrażania Menedżer zasobów. Aby uzyskać informacje na temat wersji systemu Linux tego tematu, zobacz [często zadawane pytania dotyczące Linux Virtual Machines](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Co mogę uruchomić na maszynie wirtualnej platformy Azure?
Wszyscy subskrybenci mogą uruchomić oprogramowanie serwerowe na maszynie wirtualnej platformy Azure. Aby uzyskać informacje na temat zasad pomocy technicznej dotyczących uruchamiania oprogramowania serwera firmy Microsoft na platformie Azure, zobacz [Microsoft Server Software Support for Azure Virtual Machines](https://support.microsoft.com/kb/2721672).

Niektóre wersje systemów Windows 7, Windows 8.1 i Windows 10 są dostępne dla subskrybentów korzyści MSDN platformy Azure oraz MSDN — Tworzenie i testowanie — płatność zgodnie z rzeczywistym użyciem. Aby uzyskać szczegółowe informacje, łącznie z instrukcjami i ograniczeniami, zobacz [Windows Client images for MSDN subscribers](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/) (Obrazy klienta systemu Windows dla subskrybentów MSDN). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Ile pamięci masowej mogę użyć w maszynie wirtualnej?
Każdy dysk z danymi może mieć do 32 767 GiB. Liczba dysków danych, których możesz użyć, zależy od rozmiaru maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Sizes for virtual machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Rozmiary maszyn wirtualnych).

Usługa Azure Managed Disks to zalecane oferty magazynu dyskowego do użycia z usługą Azure Virtual Machines na potrzeby trwałego magazynowania danych. Do każdej maszyny wirtualnej można użyć wielu Managed Disks. Managed Disks oferuje dwa typy opcji trwałego magazynu: Managed Disks Premium i standardowa. Aby uzyskać informacje o cenach, zobacz [Cennik usługi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks).

Konta usługi Azure Storage mogą również udostępniać magazyn dla dysku systemu operacyjnego i dysków z danymi. Każdy dysk jest plikiem VHD przechowywanym jako stronicowy obiekt blob. Aby uzyskać szczegółowe informacje o cenach, zobacz [Szczegóły cennika magazynu](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Jak mogę uzyskać dostęp do mojej maszyny wirtualnej?
Nawiąż połączenie zdalne przy użyciu Podłączanie pulpitu zdalnego (RDP) dla maszyny wirtualnej z systemem Windows. Aby uzyskać instrukcje, zobacz [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Obsługiwane są maksymalnie dwa współbieżne połączenia, chyba że serwer jest skonfigurowany jako Host sesji Usługi pulpitu zdalnego.  

Jeśli masz problemy z Pulpit zdalny, zobacz [Rozwiązywanie problemów pulpit zdalny połączeń z maszyną wirtualną platformy Azure](../troubleshooting/troubleshoot-rdp-connection.md?toc=/azure/virtual-machines/windows/toc.json)z systemem Windows. 

Użytkownicy znający funkcję Hyper-V mogą szukać narzędzia podobnego do VMConnect. Platforma Azure nie oferuje podobnego narzędzia, ponieważ dostęp z konsoli do maszyny wirtualnej nie jest obsługiwany.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Czy można używać dysku tymczasowego (domyślnie dysku D:) do przechowywania danych?
Nie używaj dysku tymczasowego do przechowywania danych. Jest to tylko magazyn tymczasowy, więc ryzyko utraty danych, których nie można odzyskać. Utrata danych może wystąpić, gdy maszyna wirtualna zostanie przeniesiona do innego hosta. Zmiana rozmiaru maszyny wirtualnej, aktualizowanie hosta lub awaria sprzętu na hoście to kilka przyczyn, które mogą spowodować przeniesienie maszyny wirtualnej.

Jeśli masz aplikację, która musi korzystać z litery D: dysku, możesz ponownie przypisać litery dysku, aby dysk tymczasowy używał czegoś innego niż D:. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Jak mogę zmienić literę dysku tymczasowego?
Można zmienić literę dysku, przenosząc plik stronicowania i ponownie przypisując litery dysku, ale należy upewnić się, że kroki są wykonywane w określonej kolejności. Aby uzyskać instrukcje, zobacz [Change the drive letter of the Windows temporary disk](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Zmiana litery dysku tymczasowego w systemie Windows).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Czy mogę dodać istniejącą maszynę wirtualną do zestawu dostępności?
Nie. Jeśli chcesz, aby maszyna wirtualna była częścią zestawu dostępności, musisz utworzyć maszynę wirtualną w zestawie. Obecnie nie ma możliwości dodania maszyny wirtualnej do zestawu dostępności po jego utworzeniu.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Czy mogę przekazać maszynę wirtualną na platformę Azure?
Tak. Aby uzyskać instrukcje, zobacz [Migrowanie lokalnych maszyn wirtualnych na platformę Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Czy mogę zmienić rozmiar dysku systemu operacyjnego?
Tak. Aby uzyskać instrukcje, zobacz [jak rozszerzyć dysk systemu operacyjnego maszyny wirtualnej w grupie zasobów platformy Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Czy mogę skopiować lub sklonować istniejącą maszynę wirtualną platformy Azure?
Tak. Przy użyciu obrazów zarządzanych można utworzyć obraz maszyny wirtualnej, a następnie użyć obrazu, aby skompilować wiele nowych maszyn wirtualnych. Aby uzyskać instrukcje, zobacz [Tworzenie niestandardowego obrazu maszyny wirtualnej](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Dlaczego nie widzę regionów Kanady środkowe i Kanada Wschodnia za poorednictwem Azure Resource Manager?

Dwa nowe regiony Kanady środkowe i Kanada Wschodnia nie są automatycznie rejestrowane na potrzeby tworzenia maszyn wirtualnych dla istniejących subskrypcji platformy Azure. Ta rejestracja jest wykonywana automatycznie, gdy maszyna wirtualna jest wdrażana za pośrednictwem Azure Portal w innym regionie przy użyciu Azure Resource Manager. Po wdrożeniu maszyny wirtualnej w innym regionie świadczenia usługi Azure nowe regiony powinny być dostępne dla kolejnych maszyn wirtualnych.

## <a name="does-azure-support-linux-vms"></a>Czy platforma Azure obsługuje maszyny wirtualne z systemem Linux?
Tak. Aby szybko utworzyć maszynę wirtualną z systemem Linux do wypróbowania, zobacz temat [Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure przy użyciu portalu](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Czy mogę dodać kartę sieciową do mojej maszyny wirtualnej po jej utworzeniu?
Tak. teraz jest to możliwe. Najpierw należy zatrzymać cofniętą alokację maszyny wirtualnej. Następnie można dodać lub usunąć kartę sieciową (chyba że jest to ostatnia karta sieciowa na maszynie wirtualnej). 

## <a name="are-there-any-computer-name-requirements"></a>Czy istnieją jakieś wymagania dotyczące nazw komputerów?
Tak. Nazwa komputera może zawierać maksymalnie 15 znaków. Zobacz [reguły nazewnictwa i ograniczenia,](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) Aby uzyskać więcej informacji na temat nazywania zasobów.

## <a name="are-there-any-resource-group-name-requirements"></a>Czy istnieją jakieś wymagania dotyczące nazw grup zasobów?
Tak. Nazwa grupy zasobów może zawierać maksymalnie 90 znaków. Aby uzyskać więcej informacji na temat grup zasobów [, zobacz Reguły nazewnictwa i ograniczenia](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące nazwy użytkownika podczas tworzenia maszyny wirtualnej?

Nazwy użytkowników mogą zawierać maksymalnie 20 znaków i nie mogą kończyć się kropką ("."). 

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

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Jakie są wymagania dotyczące hasła podczas tworzenia maszyny wirtualnej?

Istnieją różne wymagania dotyczące długości hasła, w zależności od używanego narzędzia:
 - Portal — od 12-72 znaków
 - PowerShell — od 8-123 znaków
 - Interfejs wiersza polecenia — między 12-123

* Musi zawierać małe litery
* Musi zawierać wielkie litery
* Musi zawierać cyfrę
* Musi zawierać znak specjalny (zgodność wyrażenia regularnego [\W_])

Następujące hasła są niedozwolone:

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@ $ $w 0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>PA $ $word</td>
        <td>pass@word1</td>
        <td>Hasło!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>