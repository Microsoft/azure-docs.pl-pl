---
title: Włącz połączenie przeglądarki na maszynach wirtualnych Azure DevTest Labs | Microsoft Docs
description: DevTest Labs teraz integrują się z usługą Azure bastionu, jako właściciel laboratorium można włączyć dostęp do wszystkich maszyn wirtualnych laboratorium za pomocą przeglądarki.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 074d512767769fe434cd4a6d4f4e8e6a88f7ed2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549118"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Włącz połączenie przeglądarki na maszynach wirtualnych laboratorium 
DevTest Labs integrują się z [usługą Azure bastionu](https://docs.microsoft.com/azure/bastion/), która umożliwia łączenie się z maszynami wirtualnymi za pomocą przeglądarki. Najpierw należy włączyć połączenie przeglądarki na maszynach wirtualnych laboratorium.

Jako właściciel laboratorium można włączyć dostęp do wszystkich maszyn wirtualnych laboratorium za pomocą przeglądarki. Nie jest potrzebny dodatkowy klient, agent ani oprogramowanie. Usługa Azure bastionu zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi bezpośrednio w Azure Portal za pośrednictwem protokołu TLS. Po nawiązaniu połączenia za pośrednictwem usługi Azure bastionu maszyny wirtualne nie potrzebują publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure bastionu?](../bastion/bastion-overview.md)


W tym artykule przedstawiono sposób włączania połączenia przeglądarki na maszynach wirtualnych laboratorium.

## <a name="prerequisites"></a>Wymagania wstępne 
Wdróż hosta bastionu w istniejącej sieci wirtualnej laboratorium **(lub)** Połącz laboratorium ze skonfigurowaną siecią wirtualną bastionu. 

Aby dowiedzieć się, jak wdrożyć hosta bastionu w sieci wirtualnej, zobacz [Tworzenie hosta usługi Azure bastionu](../bastion/bastion-create-host-portal.md). Podczas tworzenia hosta bastionu wybierz sieć wirtualną laboratorium. 

Najpierw należy utworzyć drugą podsieć w sieci wirtualnej bastionu, ponieważ AzureBastionSubnet nie zezwala na tworzenie zasobów innych niż bastionu. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Utwórz drugą podsieć w sieci wirtualnej bastionu
Nie można tworzyć maszyn wirtualnych laboratorium w podsieci usługi Azure bastionu. Utwórz inną podsieć w sieci wirtualnej bastionu, jak pokazano na poniższej ilustracji:

![Druga podsieć w sieci wirtualnej Azure bastionu](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Włącz tworzenie maszyn wirtualnych w podsieci
Teraz Włącz tworzenie maszyn wirtualnych w tej podsieci, wykonując następujące czynności: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **wszystkie usługi** w menu nawigacji po lewej stronie. 
1. Wybierz z listy pozycję **DevTest Labs**. 
1. Z listy laboratoriów wybierz *laboratorium*. 

    > [!NOTE]
    > Usługa Azure bastionu jest teraz ogólnie dostępna w następujących regionach: zachodnie stany USA, Wschodnie stany USA, Europa Zachodnia, Południowo-środkowe stany USA, Australia Wschodnia i Japonia Wschodnia. Utwórz laboratorium w jednym z tych regionów, jeśli laboratorium nie znajduje się w jednym z nich. 
    
1. Wybierz pozycję **Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie. 
1. Wybierz pozycję **sieci wirtualne**.
1. Wybierz pozycję **Dodaj** z paska narzędzi. 
1. Wybierz **sieć wirtualną** , dla której wdrożono hosta bastionu. 
1. Wybierz podsieć dla maszyn wirtualnych, a nie **AzureBastionSubnet**, inną utworzoną wcześniej. Zamknij stronę i otwórz ją ponownie, jeśli podsieć nie jest widoczna na liście u dołu. 

    ![Włącz tworzenie maszyn wirtualnych w podsieci](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Wybierz opcję **Użyj podczas tworzenia maszyny wirtualnej** . 
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 
1. Jeśli masz starą sieć wirtualną dla laboratorium, usuń ją, wybierając pozycję **...*  i **Usuń**. 

## <a name="enable-browser-connection"></a>Włącz połączenie przeglądarki 

Gdy bastionu sieć wirtualną skonfigurowaną w laboratorium jako właściciel laboratorium, możesz włączyć program Browser Connect na maszynach wirtualnych laboratorium.

Aby włączyć program Browser Connect na maszynach wirtualnych laboratorium, wykonaj następujące kroki:

1. W Azure Portal przejdź do *laboratorium*.
1. Wybierz pozycję **Konfiguracja i zasady**.
1. W obszarze **Ustawienia**wybierz pozycję **przeglądarka Połącz**. Jeśli ta opcja nie jest widoczna, zamknij stronę **zasady konfiguracji** i otwórz ją ponownie. 

    ![Włącz połączenie przeglądarki](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższym artykułem, aby dowiedzieć się, jak nawiązać połączenie z maszynami wirtualnymi przy użyciu przeglądarki: łączenie się z [maszyną wirtualną za pomocą przeglądarki](connect-virtual-machine-through-browser.md)
