---
title: Jak nawiązać połączenie z maszyną wirtualną Azure Lab Services z komputera Mac | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z komputera Mac z maszyną wirtualną w Azure Lab Services.
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
ms.openlocfilehash: 091acbab20723ec33d52085a717d23adf261254e
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897383"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu Remote Desktop Protocol na komputerze Mac
W tej sekcji pokazano, jak student może połączyć się z maszyną wirtualną laboratorium z systemem Mac przy użyciu protokołu RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instalowanie Pulpit zdalny Microsoft na komputerze Mac
1. Otwórz sklep App Store na komputerze Mac, a następnie wyszukaj **pulpit zdalny Microsoft**.

    ![Pulpit zdalny firmy Microsoft](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Zainstaluj najnowszą wersję programu Pulpit zdalny Microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Uzyskiwanie dostępu do maszyny wirtualnej z komputera Mac przy użyciu protokołu RDP
1. Otwórz plik **RDP** pobrany na komputerze z zainstalowanym **pulpit zdalny Microsoft** . Należy rozpocząć łączenie się z maszyną wirtualną. 

    ![Łączenie z maszyną wirtualną](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Wybierz pozycję **Kontynuuj** , jeśli zostanie wyświetlone następujące ostrzeżenie. 

    ![Ostrzeżenie dotyczące certyfikatu](./media/how-to-use-classroom-lab/certificate-error.png)
1. Powinna zostać wyświetlona maszyna wirtualna. 

    > [!NOTE]
    > Poniższy przykład dotyczy maszyny wirtualnej z systemem CentOS Linux. 

    ![VM](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak nawiązać połączenie z maszynami [wirtualnymi](how-to-use-remote-desktop-linux-student.md) z systemem Linux przy użyciu protokołu RDP, zobacz


