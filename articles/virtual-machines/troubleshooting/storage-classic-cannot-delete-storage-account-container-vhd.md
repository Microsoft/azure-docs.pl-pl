---
title: Rozwiązywanie problemów podczas usuwania klasycznego konta magazynu Azure, kontenerów lub wirtualnych dysków twardych | Microsoft Docs
description: Jak rozwiązywać problemy podczas usuwania zasobów magazynu zawierających dołączone wirtualne dyski twarde.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: c74f2ef9eed25719e722970671406c850b6a59b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361861"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Rozwiązywanie problemów z błędami usuwania zasobów klasycznego magazynu
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów w przypadku wystąpienia jednego z następujących błędów podczas próby usunięcia klasycznego konta magazynu, kontenera lub pliku obiektu blob platformy Azure. 


W tym artykule omówiono tylko problemy z klasycznymi zasobami magazynu. Jeśli użytkownik usunie klasyczną maszynę wirtualną przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia, dyski nie zostaną automatycznie usunięte. Użytkownik pobiera opcję usunięcia zasobu "dysk". Jeśli opcja nie jest zaznaczona, zasób "dysk" uniemożliwi usunięcie konta magazynu, kontenera i rzeczywistego pliku BLOB strony.

Więcej informacji o dyskach platformy Azure można znaleźć [tutaj](../../virtual-machines/managed-disks-overview.md). Platforma Azure uniemożliwia usunięcie dysku dołączonego do maszyny wirtualnej w celu zapobieżenia uszkodzeniu. Zapobiega to również usunięciu kontenerów i kont magazynu, które mają stronicowy obiekt BLOB dołączony do maszyny wirtualnej. 

## <a name="what-is-a-disk"></a>Co to jest "dysk"?
Zasób "dysk" służy do instalowania pliku BLOB stronicowania *. VHD na maszynę wirtualną, jako dysk systemu operacyjnego lub dysk danych. Zasób dysku systemu operacyjnego lub dysku danych, dopóki nie zostanie usunięty, będzie nadal miał dzierżawę w pliku *. VHD. Nie można usunąć dowolnego zasobu magazynu znajdującego się w ścieżce pokazanej poniżej obrazu, jeśli do niego odwołuje się zasób "dysk".

![Zrzut ekranu przedstawiający Portal z otwartym okienkiem "Właściwość" dysku (klasycznego)](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Kroki podczas usuwania klasycznej maszyny wirtualnej 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Usuń klasyczną maszynę wirtualną.
2. Jeśli zaznaczone jest pole wyboru "dyski", **dzierżawa dysku** (pokazana w powyższym obrazie) skojarzona ze stroną obiektów BLOB *. VHD jest uszkodzona. Rzeczywisty plik obiektu BLOB na stronie *. VHD nadal będzie istnieć na koncie magazynu.
![Zrzut ekranu przedstawia okno dialogowe, w którym można potwierdzić usunięcie maszyny wirtualnej.](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Gdy dzierżawa dysku zostanie przerwana, można usunąć elementy BLOB strony. Konto magazynu lub kontener można usunąć, gdy wszystkie znajdujące się w nich zasoby "dysk" zostaną usunięte.

>[!NOTE] 
>Jeśli użytkownik usunie maszynę wirtualną, ale nie dysk VHD, opłaty za magazyn będą nadal naliczane w pliku na stronie BLOB *. VHD. Opłaty będą naliczane zgodnie z typem konta magazynu, aby uzyskać więcej informacji, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/) . Jeśli użytkownik nie zamierza już korzystać z wirtualnych dysków twardych, należy je usunąć, aby uniknąć przyszłych opłat. 

## <a name="unable-to-delete-storage-account"></a>Nie można usunąć konta magazynu 

Gdy użytkownik próbuje usunąć klasyczne konto magazynu, które nie jest już potrzebne, użytkownik może zobaczyć następujące zachowanie.

#### <a name="azure-portal"></a>Azure Portal 
Użytkownik przechodzi do klasycznego konta magazynu na [Azure Portal](https://portal.azure.com) i klika pozycję **Usuń**. użytkownik zobaczy następujący komunikat: 

Z dyskami "dołączonymi" do maszyny wirtualnej

![Zrzut ekranu przedstawia komunikat wyjaśniający, dlaczego nie można usunąć konta magazynu.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Z dyskami "niedołączone" do maszyny wirtualnej

![Zrzut ekranu przedstawiający Portal z otwartym okienkiem "Usuń" maszynę wirtualną](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Użytkownik próbuje usunąć konto magazynu, które nie jest już używane, za pomocą klasycznych poleceń cmdlet programu PowerShell. Użytkownik zobaczy następujący komunikat:

> <span style="color:cyan">**Remove-AzureStorageAccount-StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount: nieprawidłowego żądania: konto magazynu myclassicaccount zawiera aktywne obrazy i/lub dyski, np.  
> myclassicaccount. Upewnij się, że te obrazy i/lub dyski zostały usunięte przed usunięciem tego konta magazynu.</span>

## <a name="unable-to-delete-storage-container"></a>Nie można usunąć kontenera magazynu

Gdy użytkownik próbuje usunąć kontener klasycznego obiektu blob magazynu, który nie jest już wymagany, użytkownik może zobaczyć następujące zachowanie.

#### <a name="azure-portal"></a>Azure Portal 
Azure Portal nie zezwoli użytkownikowi na usunięcie kontenera, jeśli istnieje dzierżawa "dyskowa", wskazująca plik obiektu BLOB *. VHD w kontenerze. Jest to projekt, aby zapobiec przypadkowemu usunięciu pliku VHD z dyskami z dzierżawą. 

![Zrzut ekranu przedstawiający Portal z otwartym okienkiem kontenera magazynu "list"](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Jeśli użytkownik zdecyduje się na usunięcie przy użyciu programu PowerShell, spowoduje to wyświetlenie następującego błędu. 

> <span style="color:cyan">**Remove-AzureStorageContainer-Context $context-Name VHD**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: serwer zdalny zwrócił błąd: (412) obecnie istnieje dzierżawa w kontenerze i w żądaniu nie określono identyfikatora dzierżawy. Kod stanu HTTP: 412 — komunikat o błędzie HTTP: obecnie istnieje dzierżawa w kontenerze i w żądaniu nie określono identyfikatora dzierżawy.</span>

## <a name="unable-to-delete-a-vhd"></a>Nie można usunąć dysku VHD 

Po usunięciu maszyny wirtualnej platformy Azure użytkownik próbuje usunąć plik VHD (Page BLOB) i odebrać następujący komunikat:

#### <a name="azure-portal"></a>Azure Portal 
W portalu mogą znajdować się dwa środowiska w zależności od listy obiektów BLOB wybranych do usunięcia.

1. Jeśli wybrano tylko "dzierżawione" obiekty blob, przycisk Usuń nie jest wyświetlany.
![Zrzut ekranu portalu z otwartym okienkiem listy obiektów BLOB kontenera i wybraną opcją tylko dzierżawione obiekty blob.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Jeśli wybrano kombinację "dzierżawionych" i "dostępne" obiektów blob, zostanie wyświetlony przycisk "Usuń". Natomiast operacja "Delete" pozostawi za stronicowymi obiektami BLOB, które mają na nich dzierżawę. 
![Zrzut ekranu przedstawiający Portal z otwartym okienkiem listy obiektów BLOB kontenera i zaznaczonymi obiektami BLOB, które są dzierżawione i dostępne. ](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
 ![ Zrzut ekranu portalu z otwartym okienkiem "Usuń" wybranego obiektu BLOB](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Jeśli użytkownik zdecyduje się na usunięcie przy użyciu programu PowerShell, spowoduje to wyświetlenie następującego błędu. 

> <span style="color:cyan">**Remove-AzureStorageBlob-Context $context-Container VHDs-BLOB "classicvm-OS-8698. VHD"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob: serwer zdalny zwrócił błąd: (412) obecnie istnieje dzierżawa obiektu BLOB i w żądaniu nie określono identyfikatora dzierżawy. Kod stanu HTTP: 412 — komunikat o błędzie HTTP: obecnie istnieje dzierżawa obiektu BLOB i w żądaniu nie określono identyfikatora dzierżawy.</span>


## <a name="resolution-steps"></a>Kroki rozwiązania

### <a name="to-remove-classic-disks"></a>Aby usunąć dyski klasyczne
Wykonaj następujące kroki na Azure Portal:
1.  Przejdź do [Azure Portal](https://portal.azure.com).
2.  Przejdź do dysków (klasyczne). 
3.  Kliknij kartę dyski. ![ Zrzut ekranu przedstawia wybrane Azure Portal z dyskami (klasycznymi) oraz klasycznej nazwy dysku i konta magazynu.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Wybierz dysk danych, a następnie kliknij polecenie Usuń dysk.
 ![Zrzut ekranu przedstawia wybrane Azure Portal z dyskami (klasyczny) z wybranym dyskiem danych i opcją usunięcia.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Spróbuj ponownie wykonać operację usuwania, która zakończyła się niepowodzeniem.
6.  Nie można usunąć konta magazynu lub kontenera, o ile ma on jeden dysk.

### <a name="to-remove-classic-images"></a>Aby usunąć obrazy klasyczne   
Wykonaj następujące kroki na Azure Portal:
1.  Przejdź do [Azure Portal](https://portal.azure.com).
2.  Przejdź do obrazów systemu operacyjnego (klasycznego).
3.  Usuń obraz.
4.  Spróbuj ponownie wykonać operację usuwania, która zakończyła się niepowodzeniem.
5.  Nie można usunąć konta magazynu lub kontenera, o ile ma on pojedynczy obraz.
