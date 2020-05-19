---
title: Włączanie pulpitu zdalnego dla systemu Linux w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak włączyć pulpit zdalny dla maszyn wirtualnych z systemem Linux w laboratorium w Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a40575340fc5b1c202be6b001807085954439f03
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588160"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Włącz pulpit zdalny dla maszyn wirtualnych z systemem Linux w laboratorium w Azure Lab Services
W tym artykule przedstawiono sposób wykonywania następujących zadań:

- Włączanie pulpitu zdalnego dla maszyny wirtualnej z systemem Linux
- Jak nauczycieli może nawiązać połączenie z maszyną wirtualną z szablonem za pośrednictwem Podłączanie pulpitu zdalnego (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Włączanie pulpitu zdalnego dla maszyny wirtualnej z systemem Linux
Podczas tworzenia laboratorium wykładowcy mogą włączyć Podłączanie **pulpitu zdalnego** dla obrazów systemu **Linux** . Opcja **włącz Podłączanie pulpitu zdalnego** jest wyświetlana, gdy dla szablonu wybrano obraz systemu Linux. Gdy ta opcja jest włączona, nauczyciele mogą łączyć się z szablonami maszyn wirtualnych z MASZYNami wirtualnymi i uczniami za pośrednictwem protokołu RDP (Pulpit zdalny). 

![Włączanie połączenia pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

W oknie dialogowym **Włączanie komunikatu Podłączanie pulpitu zdalnego** wybierz pozycję **Kontynuuj z pulpit zdalny**. 

![Włączanie połączenia pulpitu zdalnego dla obrazu systemu Linux](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Włączenie usługi **Podłączanie pulpitu zdalnego** otwiera tylko port **RDP** na maszynach z systemem Linux. Jeśli protokół RDP jest już zainstalowany i skonfigurowany w obrazie maszyny wirtualnej, możesz połączyć się z maszynami wirtualnymi za pośrednictwem protokołu RDP, nie wykonując żadnych dodatkowych kroków.
> 
> Jeśli na obrazie maszyny wirtualnej nie zainstalowano i skonfigurowano protokół RDP, należy połączyć się z maszyną z systemem Linux przy użyciu protokołu SSH po raz pierwszy, a następnie zainstalować pakiety RDP i GUI, aby umożliwić uczniom łączenie się z maszyną z systemem Linux przy użyciu protokołu RDP. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie pulpit zdalny do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure](../../virtual-machines/linux/use-remote-desktop.md). Następnie opublikujesz obraz tak, aby studenci mogli na maszynach wirtualnych z systemem Linux mieć protokół RDP. 

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Obecnie jest obsługiwane Podłączanie pulpitu zdalnego dla następujących systemów operacyjnych:

- openSUSE przestępnie 42,3
- CentOS — based 7,5
- Debian 9 "rozciąganie"
- Ubuntu Server 16,04 LTS

## <a name="connect-to-the-template-vm"></a>Łączenie z maszyną wirtualną szablonu 
Usługi wykładowcy muszą najpierw połączyć się z szablonową maszyną wirtualną przy użyciu protokołu SSH, a następnie zainstalować na niej pakiety RDP i GUI. Następnie wykładowcy mogą połączyć się z szablonową maszyną wirtualną przy użyciu protokołu RDP: 

1. Jeśli widzisz przycisk **Dostosuj szablon** na pasku narzędzi, wybierz go. Następnie wybierz pozycję **Kontynuuj** w oknie dialogowym **Dostosuj szablon** . Ta akcja uruchamia szablon maszyny wirtualnej.  

    ![Dostosuj szablon](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Po rozpoczęciu pracy szablonu można wybrać pozycję **Połącz szablon** , a następnie **nawiązać połączenie za pośrednictwem protokołu SSH** na pasku narzędzi. 

    ![Nawiązywanie połączenia z szablonem za pośrednictwem protokołu RDP po utworzeniu laboratorium](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. Zostanie wyświetlone okno dialogowe **Połącz z maszyną wirtualną** . Wybierz przycisk **Kopiuj** obok pola tekstowego, aby skopiować go do Schowka. Zapisz parametry połączenia SSH. Użyj tych parametrów połączenia z terminalu SSH [(jak tutaj](https://www.putty.org/)), aby nawiązać połączenie z maszyną wirtualną.
 
    ![Parametry połączenia SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Zainstaluj pakiety RDP i GUI, aby umożliwić uczniom łączenie się z maszyną z systemem Linux przy użyciu protokołu RDP w późniejszym czasie. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie pulpit zdalny do nawiązywania połączenia z maszyną wirtualną z systemem Linux na platformie Azure](../../virtual-machines/linux/use-remote-desktop.md). Następnie opublikujesz obraz tak, aby studenci mogli na maszynach wirtualnych z systemem Linux mieć protokół RDP.
5. Po zainstalowaniu tych pakietów można użyć **szablonu Połącz z** na pasku narzędzi, a następnie wybrać opcję **Połącz za pośrednictwem protokołu RDP** , aby połączyć się z szablonową maszyną wirtualną za pośrednictwem protokołu RDP. Zapisz plik RDP i użyj go do nawiązania połączenia z szablonową maszyną wirtualną za pośrednictwem protokołu RDP. 

## <a name="next-steps"></a>Następne kroki
Po włączeniu funkcji Podłączanie pulpitu zdalnego przez instruktora studenci mogą łączyć się z maszynami wirtualnymi za pośrednictwem protokołu RDP/SSH. Aby uzyskać więcej informacji, zobacz [Korzystanie z pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium z klasą](how-to-use-remote-desktop-linux-student.md). 