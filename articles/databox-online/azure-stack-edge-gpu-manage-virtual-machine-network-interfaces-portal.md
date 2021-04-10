---
title: Jak zarządzać interfejsami sieciowymi maszyn wirtualnych na Azure Stack brzeg Pro za pośrednictwem Azure Portal
description: Dowiedz się, jak zarządzać interfejsami sieciowymi na maszynach wirtualnych, które są wdrożone w Azure Stack Edge w procesorze GPU przy użyciu Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage network interfaces on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 84077f174fabd02afcd5171e8d365e8cbd3a52c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027737"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Użyj Azure Portal do zarządzania interfejsami sieciowymi na maszynach wirtualnych na urządzeniach z systemem Azure Stack EDGE Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Możesz tworzyć maszyny wirtualne i zarządzać nimi na Azure Stack urządzeniu brzegowym przy użyciu Azure Portal, szablonów, poleceń cmdlet Azure PowerShell i za pośrednictwem skryptów interfejsu wiersza polecenia platformy Azure/Python. W tym artykule opisano sposób zarządzania interfejsami sieciowymi na maszynie wirtualnej działającej na urządzeniu brzegowym Azure Stack przy użyciu Azure Portal. 

Podczas tworzenia maszyny wirtualnej należy określić jeden interfejs sieci wirtualnej, który ma zostać utworzony. Po utworzeniu maszyny wirtualnej można dodać do niej co najmniej jeden interfejs sieciowy. Możesz również zmienić domyślne ustawienia interfejsu sieciowego dla istniejącego interfejsu sieciowego.

W tym artykule wyjaśniono, jak dodać interfejs sieciowy do istniejącej maszyny wirtualnej, zmienić istniejące ustawienia, takie jak typ IP (statyczny a dynamiczny), a wreszcie usunąć lub odłączyć istniejący interfejs. 

        
## <a name="about-network-interfaces-on-vms"></a>Informacje o interfejsach sieciowych na maszynach wirtualnych

Interfejs sieciowy włącza maszynę wirtualną (VM) działającą na urządzeniu Azure Stack EDGE Pro do komunikowania się z zasobami platformy Azure i lokalnymi. Po włączeniu portu dla sieci obliczeniowej na urządzeniu zostanie utworzony przełącznik wirtualny w tym interfejsie sieciowym. Ten przełącznik wirtualny służy do wdrażania obciążeń obliczeniowych, takich jak maszyny wirtualne lub aplikacje zwirtualizowane na urządzeniu. 

Urządzenie obsługuje tylko jeden przełącznik wirtualny, ale wiele interfejsów sieci wirtualnej. Każdy interfejs sieciowy na maszynie wirtualnej ma przypisany statyczny lub dynamiczny adres IP. W przypadku adresów IP przypisanych do wielu interfejsów sieciowych na maszynie wirtualnej niektóre możliwości są włączane na maszynie wirtualnej. Na przykład maszyna wirtualna może hostować wiele witryn sieci Web lub usług z różnymi adresami IP i certyfikatami SSL na jednym serwerze. Maszyna wirtualna na urządzeniu może działać jako sieciowe urządzenie wirtualne, takie jak zapora lub moduł równoważenia obciążenia. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem zarządzania maszynami wirtualnymi na urządzeniu za pośrednictwem Azure Portal upewnij się, że:

1. Włączono interfejs sieciowy do obliczeń na urządzeniu. Ta akcja powoduje utworzenie przełącznika wirtualnego dla tego interfejsu sieciowego na maszynie wirtualnej. 
    1. W lokalnym interfejsie użytkownika urządzenia przejdź do pozycji **obliczenia**. Wybierz interfejs sieciowy, który zostanie użyty do utworzenia przełącznika wirtualnego.

        > [!IMPORTANT] 
        > Dla obliczeń można skonfigurować tylko jeden port.

    1. Włącz obliczenia w interfejsie sieciowym. Azure Stack Edge — procesor GPU tworzy przełącznik wirtualny odpowiadający temu interfejsowi sieciowemu i zarządza nim.

1. Na urządzeniu zainstalowano co najmniej jedną maszynę wirtualną. Aby utworzyć tę maszynę wirtualną, zapoznaj się z instrukcjami w temacie [Wdrażanie maszyny wirtualnej na Azure Stack brzegowej Pro za pośrednictwem Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Maszyna wirtualna powinna być w stanie **zatrzymania** . Aby zatrzymać MASZYNę wirtualną, przejdź do pozycji **maszyny wirtualne > przegląd** i wybierz maszynę wirtualną, która ma zostać zatrzymana. Na stronie właściwości maszyny wirtualnej wybierz pozycję **Zatrzymaj** , a następnie wybierz pozycję **tak** po wyświetleniu monitu o potwierdzenie. Przed dodaniem, edycją lub usunięciem interfejsów sieciowych należy zatrzymać maszynę wirtualną.

    ![Strona właściwości zatrzymywania maszyny wirtualnej na maszynie wirtualnej](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Dodawanie interfejsu sieciowego

Wykonaj następujące kroki, aby dodać interfejs sieciowy do maszyny wirtualnej wdrożonej na urządzeniu. 

1. Przejdź do zatrzymanej maszyny wirtualnej, a następnie przejdź do strony **właściwości maszyny wirtualnej** . Wybierz pozycję **Sieć**.
    
    ![Strona właściwości sieci maszyny wirtualnej](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. W bloku **Sieć** na pasku poleceń wybierz pozycję **+ Dodaj interfejs sieciowy**.

    ![Wybierz pozycję Dodaj interfejs sieciowy](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. W bloku **Dodaj interfejs sieciowy** wprowadź następujące parametry:

    
    |Column1  |Kolumna2  |
    |---------|---------|
    |Nazwa     | Unikatowa nazwa w obrębie grupy zasobów. Nie można zmienić nazwy po utworzeniu interfejsu sieciowego. Aby łatwo zarządzać wieloma interfejsami sieciowymi, Skorzystaj z sugestii podanych w [konwencjach nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming).     |
    |Sieć wirtualna| Sieć wirtualna skojarzona z przełącznikiem wirtualnym utworzonym na urządzeniu po włączeniu obliczeń w interfejsie sieciowym. Z urządzeniem jest skojarzona tylko jedna sieć wirtualna.         |         
    |Podsieć     | Podsieć w ramach wybranej sieci wirtualnej. To pole jest wypełniane automatycznie z podsiecią skojarzoną z interfejsem sieciowym, w którym włączono obliczenia.         |       
    |Przypisanie adresu IP   | Statyczny lub dynamiczny adres IP dla interfejsu sieciowego. Statyczny adres IP powinien być dostępny, bezpłatny adres IP z określonego zakresu podsieci. Wybierz opcję dynamiczny, jeśli w środowisku istnieje serwer DHCP.        | 

    ![Dodawanie bloku interfejsu sieciowego](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Zobaczysz powiadomienie o tym, że tworzenie interfejsu sieciowego jest w toku.

    ![Powiadomienie po utworzeniu interfejsu sieciowego](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Po pomyślnym utworzeniu interfejsu sieciowego zostanie wyświetlona lista interfejsów sieciowych w celu wyświetlenia nowo utworzonego interfejsu.

    ![Zaktualizowana lista interfejsów sieciowych](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Edytowanie interfejsu sieciowego

Wykonaj następujące kroki, aby edytować interfejs sieciowy skojarzony z maszyną wirtualną wdrożoną na urządzeniu.

1. Przejdź do zatrzymanej maszyny wirtualnej i przejdź do strony **właściwości maszyny wirtualnej** . Wybierz pozycję **Sieć**.

1. Na liście interfejsów sieciowych wybierz interfejs, który chcesz edytować. Z prawej strony wybranego interfejsu sieciowego wybierz ikonę edycji (ołówek).  

    ![Wybierz interfejs sieciowy do edycji](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. W bloku **Edytowanie interfejsu sieciowego** można zmienić tylko przypisanie adresu IP interfejsu sieciowego. Po utworzeniu nie można zmienić nazwy, sieci wirtualnej i podsieci skojarzonej z interfejsem sieciowym. Zmień **przypisanie adresu IP** na static i Zapisz zmiany.

    ![Zmień przypisanie adresu IP dla interfejsu sieciowego](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. Lista odświeża interfejs sieciowy w celu wyświetlenia zaktualizowanego interfejsu sieciowego.


## <a name="detach-a-network-interface"></a>Odłączanie interfejsu sieciowego

Wykonaj następujące kroki, aby odłączyć lub usunąć interfejs sieciowy skojarzony z maszyną wirtualną wdrożoną na urządzeniu.

1. Przejdź do zatrzymanej maszyny wirtualnej i przejdź do strony **właściwości maszyny wirtualnej** . Wybierz pozycję **Sieć**.

1. Na liście interfejsów sieciowych wybierz interfejs, który chcesz edytować. Z prawej strony wybranego interfejsu sieciowego wybierz ikonę odłączania (Odłącz).  

    ![Wybierz interfejs sieciowy do odłączenia](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Po całkowitym odłączeniu interfejsu lista interfejsów sieciowych jest odświeżana, aby wyświetlić pozostałe interfejsy.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak wdrażać maszyny wirtualne na urządzeniu Azure Stack EDGE Pro, zobacz [wdrażanie maszyn wirtualnych za pośrednictwem Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
