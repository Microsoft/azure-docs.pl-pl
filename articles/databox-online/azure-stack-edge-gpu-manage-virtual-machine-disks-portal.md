---
title: Zarządzanie dyskami maszyn wirtualnych w Azure Stack Edge — procesor GPU, Pro R, mini R za pośrednictwem Azure Portal
description: Dowiedz się, jak zarządzać dyskami, w tym dodawać lub odłączać dysk danych na maszynach wirtualnych wdrożonych w Azure Portal ramach procesora GPU w Azure Stack Edge, Azure Stack EDGE Pro R i Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080556"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Użyj Azure Portal do zarządzania dyskami na maszynach wirtualnych na Azure Stack

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Dyski można udostępniać na maszynach wirtualnych wdrożonych na urządzeniu Azure Stack EDGE Pro przy użyciu Azure Portal. Dyski są obsługiwane na urządzeniu za pośrednictwem Azure Resource Manager lokalnego i zużywają pojemność urządzenia. Operacje, takie jak dodawanie dysku, Odłączanie dysku można wykonać za pomocą Azure Portal, co z kolei umożliwia nawiązywanie połączeń z lokalnym Azure Resource Manager w celu aprowizacji magazynu. 

W tym artykule wyjaśniono, jak dodać dysk danych do istniejącej maszyny wirtualnej, odłączyć dysk danych i ostatecznie zmienić rozmiar maszyny wirtualnej za pomocą Azure Portal. 

        
## <a name="about-disks-on-vms"></a>Informacje o dyskach na maszynach wirtualnych

Maszyna wirtualna może mieć dysk systemu operacyjnego i dysk danych. Każda maszyna wirtualna wdrożona na urządzeniu ma jeden dołączony dysk systemu operacyjnego. Ten dysk systemu operacyjnego ma wstępnie zainstalowany system operacyjny, który został wybrany podczas tworzenia maszyny wirtualnej. Ten dysk zawiera wolumin rozruchowy.

> [!NOTE]
> Nie można zmienić rozmiaru dysku systemu operacyjnego dla maszyny wirtualnej na urządzeniu. Rozmiar dysku systemu operacyjnego jest określany na podstawie wybranego rozmiaru maszyny wirtualnej. 


Dysk danych z drugiej strony jest dyskiem zarządzanym podłączonym do maszyny wirtualnej działającej na urządzeniu. Dysk danych służy do przechowywania danych aplikacji. Dyski danych są zazwyczaj dyskami SCSI. Rozmiar maszyny wirtualnej określa liczbę dysków z danymi, które można dołączyć do maszyny wirtualnej. Usługa Premium Storage jest domyślnie używana do hostowania dysków.

Maszyna wirtualna wdrożona na urządzeniu może czasami zawierać dysk tymczasowy. Dysk tymczasowy zapewnia krótkoterminowy magazyn dla aplikacji i procesów, który jest przeznaczony tylko do przechowywania danych, takich jak pliki stron lub plików wymiany. Dane na dysku tymczasowym mogą zostać utracone podczas zdarzenia konserwacji lub po ponownym wdrożeniu maszyny wirtualnej. Po pomyślnym przeprowadzeniu standardowego ponownego uruchomienia maszyny wirtualnej dane na dysku tymczasowym będą utrwalane. 


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem zarządzania dyskami na maszynach wirtualnych działających na urządzeniu za pośrednictwem Azure Portal upewnij się, że:


1. Masz dostęp do aktywowanego urządzenia GPU z usługą Azure Stack Edge. Włączono również interfejs sieciowy do obliczeń na urządzeniu. Ta akcja powoduje utworzenie przełącznika wirtualnego dla tego interfejsu sieciowego na maszynie wirtualnej. 
    1. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **obliczenia**. Wybierz interfejs sieciowy, który zostanie użyty do utworzenia przełącznika wirtualnego.

        > [!IMPORTANT] 
        > Dla obliczeń można skonfigurować tylko jeden port.

    1. Włącz obliczenia w interfejsie sieciowym. Azure Stack Edge — procesor GPU tworzy przełącznik wirtualny odpowiadający temu interfejsowi sieciowemu i zarządza nim.

1. Na urządzeniu została wdrożona co najmniej jedna maszyna wirtualna. Aby utworzyć tę maszynę wirtualną, zapoznaj się z instrukcjami w temacie [Wdrażanie maszyny wirtualnej na Azure Stack brzegowej Pro za pośrednictwem Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).



## <a name="add-a-data-disk"></a>Dodawanie dysku z danymi

Wykonaj następujące kroki, aby dodać dysk do maszyny wirtualnej wdrożonej na urządzeniu. 

1. Przejdź do maszyny wirtualnej, do której chcesz dodać dysk danych, a następnie przejdź do strony **Przegląd** . Wybierz pozycję **Dyski**.
    
    ![Wybieranie dysków na stronie Przegląd](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. W bloku **dyski** w obszarze **dyski danych** wybierz pozycję **Utwórz i Dołącz nowy dysk**.

    ![Tworzenie i dołączanie nowego dysku](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. W bloku **Utwórz nowy dysk** wprowadź następujące parametry:

    
    |Pole  |Opis  |
    |---------|---------|
    |Nazwa     | Unikatowa nazwa w obrębie grupy zasobów. Nie można zmienić nazwy po utworzeniu dysku z danymi.     |
    |Rozmiar| Rozmiar dysku danych w GiB. Maksymalny rozmiar dysku z danymi zależy od wybranego rozmiaru maszyny wirtualnej. Podczas aprowizacji dysku należy również rozważyć rzeczywistą ilość miejsca na urządzeniu oraz inne obciążenia maszyn wirtualnych, które używają pojemności.  |         

    ![Utwórz nowy blok dysku](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Wybierz **przycisk OK** i wykonaj.

1. Na stronie **Przegląd** w obszarze **dyski** zobaczysz wpis odpowiadający nowemu dyskowi. Zaakceptuj domyślny lub Przypisz prawidłowy numer jednostki logicznej (LUN) do dysku, a następnie wybierz pozycję **Zapisz**. Jednostka LUN jest unikatowym identyfikatorem dysku SCSI. Aby uzyskać więcej informacji, zobacz [co to jest jednostka LUN?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Nowy dysk na stronie Przegląd](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Zobaczysz powiadomienie, że tworzenie dysku jest w toku. Po pomyślnym utworzeniu dysku maszyna wirtualna zostanie zaktualizowana. 

    ![Powiadomienie o tworzeniu dysku](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Przejdź z powrotem do strony **Przegląd** . Lista dysków jest aktualizowana w celu wyświetlenia nowo utworzonego dysku z danymi.

    ![Zaktualizowana lista dysków z danymi](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Zmiana dysku z danymi

Wykonaj następujące kroki, aby zmienić dysk skojarzony z maszyną wirtualną wdrożoną na urządzeniu.

1. Przejdź do maszyny wirtualnej, która zawiera dysk danych do zmiany, i przejdź do strony **Przegląd** . Wybierz pozycję **Dyski**.

1. Na liście dysków z danymi wybierz dysk, który chcesz zmienić. Po prawej stronie wybranego dysku wybierz ikonę edycji (ołówek).  

    ![Wybierz dysk do zmiany](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. W bloku **Zmień dysk** można zmienić tylko rozmiar dysku. Nie można zmienić nazwy skojarzonej z dyskiem, gdy zostanie on utworzony. Zmień **rozmiar** i Zapisz zmiany.

    ![Zmień rozmiar dysku z danymi](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Dysk danych można rozszerzyć tylko, ale nie można go zmniejszyć.

1. Na stronie **Przegląd** znajduje się lista dysków odświeżanych w celu wyświetlenia zaktualizowanego dysku.


## <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku

Wykonaj następujące kroki, aby dołączyć istniejący dysk do maszyny wirtualnej wdrożonej na urządzeniu.

1. Przejdź do maszyny wirtualnej, do której chcesz dołączyć istniejący dysk, a następnie przejdź do strony **Przegląd** . Wybierz pozycję **Dyski**.
    
    ![Wybierz dyski ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. W bloku **dyski** w obszarze **dyski danych** wybierz pozycję **Dołącz istniejący dysk**.

    ![Wybierz opcję Dołącz istniejący dysk](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Zaakceptuj domyślną jednostkę LUN lub Przypisz prawidłową jednostkę LUN. Wybierz z listy rozwijanej istniejący dysk z danymi. Wybierz pozycję Zapisz.

    ![Wybierz istniejący dysk](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Wybierz pozycję **Zapisz** i wykonaj.

1. Zobaczysz powiadomienie o zaktualizowaniu maszyny wirtualnej. Po zaktualizowaniu maszyny wirtualnej przejdź z powrotem do strony **Przegląd** . Odśwież stronę, aby wyświetlić nowo dołączony dysk na liście dysków z danymi.

    ![Wyświetlanie zaktualizowanej listy dysków danych na stronie Przegląd](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Odłączanie dysku danych

Wykonaj następujące kroki, aby odłączyć lub usunąć dysk danych skojarzony z maszyną wirtualną wdrożoną na urządzeniu.

> [!NOTE]
> - Dysk danych można usunąć, gdy maszyna wirtualna jest uruchomiona. Upewnij się, że nic nie korzysta aktywnie z dysku przed odłączeniem go od maszyny wirtualnej.
> - Jeśli dysk zostanie odłączony, nie zostanie automatycznie usunięty.

1. Przejdź do maszyny wirtualnej, z której chcesz odłączyć dysk z danymi, i przejdź do strony **Przegląd** . Wybierz pozycję **Dyski**.

    ![Wybierz dyski](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Na liście dysków wybierz dysk, który chcesz odłączyć. Po prawej stronie wybranego dysku wybierz ikonę Odłącz (krzyżowo). Wybrany wpis zostanie odłączony. Wybierz pozycję **Zapisz**. 

    ![Wybierz dysk do odłączenia](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Po odłączeniu dysku maszyna wirtualna zostanie zaktualizowana. Odśwież stronę **przeglądu** , aby wyświetlić zaktualizowaną listę dysków z danymi.

    ![Wybierz pozycję Zapisz](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrażać maszyny wirtualne na urządzeniu Azure Stack EDGE Pro, zobacz [wdrażanie maszyn wirtualnych za pośrednictwem Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
