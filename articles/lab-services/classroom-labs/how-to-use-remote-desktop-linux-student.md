---
title: Korzystanie z pulpitu zdalnego dla systemu Linux w Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak korzystać z usług pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium w Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 8ccad0698ea6560dd183cacc71f5f3a644e8220c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588109"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Korzystanie z usług pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium z klasą Azure Lab Services
W tym artykule opisano, jak uczniowie mogą łączyć się z maszyną wirtualną z systemem Linux w laboratorium przy użyciu protokołu RDP/SSH. 

Instruktor musi włączyć funkcję Podłączanie pulpitu zdalnego, zanim uczniowie będą mogli połączyć się z maszyną wirtualną laboratorium klasy. Aby uzyskać instrukcje dotyczące włączania funkcji Podłączanie pulpitu zdalnego przez instruktora, zobacz [Włączanie usług pulpitu zdalnego dla maszyn wirtualnych z systemem Linux](how-to-enable-remote-desktop-linux.md).

> [!IMPORTANT] 
> Włączenie usługi **Podłączanie pulpitu zdalnego** otwiera tylko port **RDP** na maszynach z systemem Linux. Instruktor może nawiązać połączenie z maszyną z systemem Linux przy pierwszym użyciu protokołu SSH, a także zainstalować pakiety RDP i GUI, aby można było połączyć się z maszyną z systemem Linux później przy użyciu protokołu RDP. 

## <a name="connect-to-the-student-vm"></a>Nawiązywanie połączenia z maszyną wirtualną ucznia
Studenci mogą włączyć protokół RDP na maszynach wirtualnych z systemem Linux po **opublikowaniu** przez właściciela laboratorium (nauczycieli) szablonu maszyny wirtualnej z pakietami RDP i interfejsem GUI zainstalowanymi na komputerze. Oto odpowiednie kroki: 

1. Gdy student loguje się bezpośrednio do portalu Labs ( `https://labs.azure.com` ) lub przy użyciu linku do rejestracji ( `https://labs.azure.com/register/<registrationCode>` ), zostanie wyświetlony kafelek dla każdego laboratorium, do którego student ma dostęp. 
2. Na kafelku Przełącz przycisk, aby uruchomić maszynę wirtualną, jeśli jest ona w stanie zatrzymania. 
3. Wybierz przycisk **Połącz**. Zobaczysz dwie opcje połączenia z maszyną wirtualną: **SSH** i **pulpit zdalny**.

    ![Maszyna wirtualna ucznia — opcje połączeń](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Nawiązywanie połączenia przy użyciu protokołu SSH lub RDP
W przypadku wybrania opcji **SSH** zostanie wyświetlone okno dialogowe **Połącz z maszyną wirtualną** :  

![Parametry połączenia SSH](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Wybierz przycisk **Kopiuj** obok pola tekstowego, aby skopiować go do Schowka. Zapisz parametry połączenia SSH. Użyj tych parametrów połączenia z terminalu SSH [(jak tutaj](https://www.putty.org/)), aby nawiązać połączenie z maszyną wirtualną.

W przypadku wybrania opcji **RDP** na komputerze zostanie pobrany plik RDP. Zapisz go i Otwórz, aby nawiązać połączenie z maszyną. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak włączyć funkcję Podłączanie pulpitu zdalnego dla maszyn wirtualnych z systemem Linux w laboratorium z klasą, zobacz temat [Włączanie usług pulpitu zdalnego dla komputerów z systemem Linux](how-to-enable-remote-desktop-linux.md). 

