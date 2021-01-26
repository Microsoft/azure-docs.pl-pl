---
title: Azure Lab Services — przekazywanie obrazu niestandardowego do galerii obrazów udostępnionych
description: Opisuje sposób przekazywania obrazu niestandardowego do galerii obrazów udostępnionych. W szkołach działów IT mogą znaleźć się szczególnie przydatne obrazy.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 3e2f38b0cab87eab27181ddef79d0c02bd8c9bdb
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787167"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Przekazywanie obrazu niestandardowego do usługi Shared Image Gallery

Udostępniona Galeria obrazów jest dostępna do importowania własnych niestandardowych obrazów do tworzenia laboratoriów w Azure Lab Services. Uniwersyteckie działy IT będą wyszukiwać obrazy, które są szczególnie korzystne z następujących powodów: 

* Nie musisz tworzyć ręcznie obrazów przy użyciu szablonu maszyny wirtualnej laboratorium.
* Można przekazać obrazy utworzone przy użyciu innych narzędzi, takich jak SCCM, Menedżer punktów końcowych itd.

W tym artykule opisano czynności, które można wykonać w celu przeprowadzenia niestandardowego obrazu i użycia go w Azure Lab Services. 

> [!IMPORTANT]
> Po przeniesieniu obrazów z fizycznego środowiska laboratoryjnego do AZ Labs należy zmienić ich strukturę appropriatly. Nie należy po prostu ponownie używać istniejących obrazów z poziomu laboratoriów fizycznych. <br/>Aby uzyskać szczegółowe informacje, zobacz artykuł [dotyczący przechodzenia z laboratorium fizycznego do Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) wpis w blogu.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Przenoszenie obrazu niestandardowego z fizycznego środowiska laboratoryjnego

W poniższych krokach przedstawiono sposób importowania obrazu niestandardowego, który jest uruchamiany z fizycznego środowiska laboratoryjnego. Wirtualny dysk twardy zostanie utworzony na podstawie tego środowiska i zaimportowany do udostępnionej galerii obrazów na platformie Azure, aby można go było używać w Azure Lab Services.

Istnieje wiele opcji tworzenia wirtualnego dysku twardego w środowisku laboratoryjnym. Poniższe kroki pokazują, jak utworzyć dysk VHD z poziomu maszyny wirtualnej funkcji Hyper-V systemu Windows:

1. Rozpocznij pracę z maszyną wirtualną funkcji Hyper-V w środowisku środowiska laboratoryjnego, która została utworzona na podstawie obrazu.
    1. Maszyna wirtualna musi zostać utworzona jako maszyna wirtualna generacji 1.
    1. Maszyna wirtualna musi używać stałego rozmiaru dysku. Możesz również określić rozmiar dysku w tym oknie. Rozmiar dysku nie może być większy niż 128 GB.<br/>    
    Obrazy o rozmiarze dysku > 128 GB nie są obsługiwane przez Azure Lab Services. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Łączenie wirtualnego dysku twardego":::   
    1. Obraz maszyny wirtualnej w normalny sposób.
1. [Nawiąż połączenie z maszyną wirtualną i przygotuj ją na platformę Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Ustawianie konfiguracji systemu Windows na platformie Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)
    1. [Sprawdź usługi systemu Windows, które są minimalnymi wymaganiami, aby zapewnić łączność z maszyną wirtualną](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [Aktualizowanie ustawień rejestru usług pulpitu zdalnego](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
    1. [Konfigurowanie reguł zapory systemu Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)
    1. Zainstaluj aktualizacje systemu Windows
    1. [Zainstaluj agenta maszyny wirtualnej platformy Azure i dodatkową konfigurację, jak pokazano tutaj](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
    
    Powyższe kroki spowodują utworzenie wyspecjalizowanego obrazu. W przypadku tworzenia uogólnionego obrazu należy również uruchomić program [Sysprep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep). <br/>
        Należy utworzyć wyspecjalizowany obraz, jeśli chcesz zachować katalog użytkownika (może zawierać pliki, informacje o koncie użytkownika itp.), które są wymagane przez oprogramowanie zawarte w obrazie.
1. Ponieważ **Funkcja Hyper-V** domyślnie tworzy plik **VHDX** , należy go przekonwertować na plik VHD.
    1. Przejdź do **akcji Menedżera funkcji Hyper-V**  ->    ->  **Edytuj dysk**.
    1. W tym miejscu będziesz mieć możliwość **przekonwertowania** dysku z pliku VHDX na dysk VHD.
    1. Przy próbie zwiększenia rozmiaru dysku upewnij się, że nie przekroczy 128 GB.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Wybieranie akcji":::   
1. Przekaż plik VHD na platformę Azure, aby utworzyć dysk zarządzany.
    1. Możesz użyć albo Eksplorator usługi Storage lub AzCopy z wiersza polecenia, zgodnie z opisem w artykule [przekazywanie wirtualnego dysku twardego na platformę Azure lub skopiowanie dysku zarządzanego do innego regionu](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md).        
    Jeśli maszyna przechodzi do trybu uśpienia lub blokady, proces przekazywania może zostać przerwany i niepowodzenie.
    1. W wyniku tego kroku masz teraz dysk zarządzany, który można zobaczyć w Azure Portal. 
        Aby wybrać rozmiar dysku, możesz użyć karty "Size\Performance" Azure Portal. Jak wspomniano wcześniej, rozmiar nie może być > 128 GB.
1. Utwórz migawkę dysku zarządzanego.
    Można to zrobić przy użyciu programu PowerShell, Azure Portal lub z poziomu Eksplorator usługi Storage, zgodnie z opisem w temacie [Tworzenie migawki przy użyciu portalu lub programu PowerShell](../virtual-machines/windows/snapshot-copy-managed-disk.md).
1. W galerii obrazów udostępnionych Utwórz definicję i wersję obrazu:
    1. [Utwórz definicję obrazu](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition).
    1. Należy również określić, czy tworzysz obraz wyspecjalizowany/uogólniony.
1. Utwórz laboratorium w Azure Lab Services i wybierz obraz niestandardowy z galerii obrazów udostępnionych.

    Jeśli dysk został rozwinięty po zainstalowaniu systemu operacyjnego na oryginalnej maszynie wirtualnej funkcji Hyper-V, należy również rozszerzyć dysk C w systemie Windows, aby użyć przydzieloną przestrzeni dyskowej. W tym celu zaloguj się do szablonu maszyny wirtualnej po utworzeniu laboratorium, a następnie postępuj zgodnie z instrukcjami podanymi w sekcji [Zwiększ wolumin podstawowy](/windows-server/storage/disk-management/extend-a-basic-volume). Można to zrobić za pośrednictwem interfejsu użytkownika, a także przy użyciu programu PowerShell.

## <a name="next-steps"></a>Następne kroki

* [Omówienie galerii obrazów udostępnionych](../virtual-machines/shared-image-galleries.md)
* [Jak korzystać z galerii obrazów udostępnionych](how-to-use-shared-image-gallery.md)