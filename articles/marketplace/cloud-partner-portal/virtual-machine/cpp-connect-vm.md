---
title: Nawiązywanie połączenia z maszyną wirtualną opartą na Microsoft Azure | Portal Azure Marketplace
description: Wyjaśnia, jak nawiązać połączenie z nową maszyną wirtualną utworzoną na platformie Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 4aea624c2127c9b0a61d72b8d14929ce6f47df24
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142487"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną opartą na platformie Azure

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie zasobów technicznych maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) , aby zarządzać migrowanymi ofertami.

W tym artykule wyjaśniono, jak nawiązać połączenie z maszynami wirtualnymi utworzonymi na platformie Azure i zalogować się do nich.  Po pomyślnym nawiązaniu połączenia możesz współpracować z maszyną wirtualną tak, jakby była ona zalogowana lokalnie na serwerze hosta.

## <a name="connect-to-a-windows-based-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows

Klient usług pulpitu zdalnego będzie używany do nawiązywania połączenia z maszyną wirtualną opartą na systemie Windows hostowaną na platformie Azure.  Większość wersji systemu Windows natywnie zawiera obsługę protokołu RDP (Remote Desktop Protocol).  W przypadku innych maszyn można znaleźć więcej informacji o klientach w [pulpit zdalny klientach](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

W poniższym artykule szczegółowo opisano sposób używania wbudowanej obsługi protokołu RDP systemu Windows do nawiązywania połączenia z maszyną wirtualną: [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Podczas procesu mogą pojawić się ostrzeżenia dotyczące zabezpieczeń, na przykład że plik RDP pochodzi od nieznanego wydawcy lub nie można zweryfikować poświadczeń użytkownika.  Można bezpiecznie zignorować te ostrzeżenia.


## <a name="connect-to-a-linux-based-vm"></a>Nawiązywanie połączenia z maszyną wirtualną opartą na systemie Linux

Aby połączyć maszynę wirtualną z systemem Linux, musisz mieć klienta Secure Shell Protocol (SSH).  W tej dyskusji zostanie [użyty bezpłatny prywatnego](https://www.ssh.com/ssh/putty/) terminala.

1. Przejdź do [Azure Portal](https://ms.portal.azure.com). Wyszukaj i wybierz pozycję **maszyny wirtualne**. 
2. Wybierz maszynę wirtualną, z którą chcesz nawiązać połączenie.  
3. **Uruchom** maszynę wirtualną, jeśli jeszcze nie działa.
4. Kliknij nazwę maszyny wirtualnej, aby otworzyć jej stronę **przeglądu** .
5. Zanotuj publiczny adres IP i nazwę DNS maszyny wirtualnej.  (Jeśli te wartości nie są ustawione, należy [utworzyć interfejs sieciowy](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Ustawienia przeglądu maszyny wirtualnej](./media/publishvm_019.png)
 
6. Otwórz aplikację.  
7. W oknie dialogowym Konfiguracja konfiguracji wprowadź adres IP lub nazwę DNS maszyny wirtualnej. 

   ![Podano ustawienia terminalu](./media/publishvm_020.png)
 
8. Kliknij przycisk **Otwórz** , aby otworzyć wystawcy Terminal.  
9. Po wyświetleniu monitu wprowadź nazwę konta i hasło do konta maszyny wirtualnej z systemem Linux. 

Jeśli występują problemy z połączeniem, zapoznaj się z dokumentacją klienta SSH, na przykład [rozdział 10: typowe komunikaty o błędach](https://www.ssh.com/ssh/putty/putty-manuals).

Aby uzyskać więcej informacji, w tym o sposobach dodawania pulpitu do maszyny wirtualnej z systemem Linux z obsługą administracyjną, zobacz [Instalowanie i konfigurowanie pulpit zdalny do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Zatrzymaj nieużywane maszyny wirtualne
Weksle platformy Azure dla hostingu maszyn wirtualnych, gdy maszyna wirtualna jest uruchomiona lub jest w *stanie bezczynności*.  W związku z tym najlepszym rozwiązaniem jest zatrzymanie maszyn wirtualnych, które nie są obecnie używane.  Na przykład, test, kopia zapasowa lub wycofane maszyny wirtualne są kandydatami do zamknięcia. Aby zamknąć maszynę wirtualną, wykonaj następujące czynności:

1. W bloku **maszyny wirtualne** wybierz maszynę wirtualną, która ma zostać zatrzymana. 
2. Na pasku narzędzi u góry strony kliknij przycisk **Zatrzymaj** .

   ![Zatrzymywanie maszyny wirtualnej](./media/publishvm_018.png)

Platforma Azure szybko zatrzyma maszynę wirtualną w procesie nazywanym *cofnięciem alokacji*, który nie tylko zamknie system operacyjny na maszynie wirtualnej, ale również zwalnia zasoby sprzętowe i sieciowe, które wcześniej zainicjowano dla niego.

Jeśli chcesz później ponownie uaktywnić zatrzymaną maszynę wirtualną, zaznacz ją i kliknij przycisk **Uruchom** .


## <a name="next-steps"></a>Następne kroki

Po nawiązaniu połączenia zdalnego możesz [skonfigurować maszynę wirtualną](./cpp-configure-vm.md).
