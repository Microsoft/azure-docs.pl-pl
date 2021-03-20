---
title: Łączenie się z maszynami wirtualnymi z systemem Linux w Azure DevTest Labs
description: Dowiedz się, jak nawiązać połączenie z maszyną wirtualną z systemem Linux w środowisku laboratoryjnym (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86532189"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux w laboratorium (Azure DevTest Labs)
W tym artykule opisano sposób nawiązywania połączenia z maszyną wirtualną z systemem Linux w środowisku laboratoryjnym. 

## <a name="connect-to-a-linux-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Linux
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **DevTest Labs**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Wyszukaj i wybierz pozycję DevTest Labs":::    
1. Z listy laboratoriów wybierz **laboratorium**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Wybierz laboratorium":::            
1. Na stronie głównej laboratorium wybierz MASZYNę wirtualną z systemem Linux z listy **moje maszyny wirtualne** . 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Wybierz maszynę wirtualną z systemem Linux":::        
5. Na stronie **Przegląd** można zobaczyć w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP maszyny wirtualnej. Możesz również zobaczyć port, jak pokazano na poniższej ilustracji.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="W pełni kwalifikowana nazwa domeny dla maszyny wirtualnej":::    

    Zwróć uwagę, że przycisk **Połącz** jest wyszarzony, nawet jeśli maszyna wirtualna jest uruchomiona. Jest to zaprojektowane.
6.  Połącz się z maszyną wirtualną z systemem Linux przy użyciu protokołu SSH. Poniższy przykład nawiązuje połączenie z maszyną wirtualną za pomocą nazwy FQDN z `mydtl07172452621450000.eastus.cloudapp.azure.com` nazwą użytkownika `vmuser` i portem `51637` . Wprowadź hasło użytkownika, aby połączyć się z maszyną wirtualną. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    Do nawiązania połączenia z maszyną wirtualną można używać narzędzi takich jak podano [lub dowolny](https://www.putty.org/) klient SSH. 

    Po nawiązaniu połączenia przy użyciu protokołu SSH można zainstalować i skonfigurować środowisko pulpitu ([pulpit Xfce](https://www.xfce.org)) i pulpit zdalny ([xrdp](http://xrdp.org)).  Aby uzyskać szczegółowe informacje, zobacz [Instalowanie i konfigurowanie pulpit zdalny do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Następne kroki
[Instrukcje: Nawiązywanie połączenia z maszyną wirtualną z systemem Windows](connect-windows-virtual-machine.md)
