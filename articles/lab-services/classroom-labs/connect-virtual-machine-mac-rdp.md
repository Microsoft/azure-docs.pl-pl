---
title: Jak nawiązać połączenie z maszyną wirtualną Azure Lab Services z komputera Mac | Microsoft Docs
description: Ten artykuł
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503090"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac
W tej sekcji pokazano, jak student może połączyć się z maszyną wirtualną laboratorium z systemem Mac przy użyciu protokołu RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instalowanie Pulpit zdalny Microsoft na komputerze Mac
1. Otwórz sklep App Store na komputerze Mac, a następnie wyszukaj **pulpit zdalny Microsoft**.

    ![Pulpit zdalny firmy Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Zainstaluj najnowszą wersję programu Pulpit zdalny Microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Uzyskiwanie dostępu do maszyny wirtualnej z komputera Mac przy użyciu protokołu RDP
1. Otwórz plik **RDP** pobrany na komputerze z zainstalowanym **pulpit zdalny Microsoft** . Należy rozpocząć łączenie się z maszyną wirtualną. 

    ![Łączenie z maszyną wirtualną](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Wybierz pozycję **Kontynuuj** , jeśli zostanie wyświetlone następujące ostrzeżenie. 

    ![Ostrzeżenie dotyczące certyfikatu](../media/how-to-use-classroom-lab/certificate-error.png)
1. Powinna zostać wyświetlona maszyna wirtualna. 

    > [!NOTE]
    > Poniższy przykład dotyczy maszyny wirtualnej z systemem CentOS Linux. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak nawiązać połączenie z maszynami [wirtualnymi](how-to-use-remote-desktop-linux-student.md) z systemem Linux przy użyciu protokołu RDP, zobacz


