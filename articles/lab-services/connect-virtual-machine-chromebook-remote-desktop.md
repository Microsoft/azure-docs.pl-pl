---
title: Jak nawiązać połączenie z maszyną wirtualną Azure Lab Services z poziomu usługi Chromebook | Microsoft Docs
description: Dowiedz się, jak nawiązać połączenie z Chromebook z maszyną wirtualną w Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: d2a936a6acb6491c57cb1460c83ec0253561b102
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445665"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu Remote Desktop Protocol na Chromebook
W tej sekcji pokazano, jak student może połączyć się z maszyną wirtualną laboratorium z Chromebook za pomocą protokołu RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Instalowanie Pulpit zdalny Microsoft na Chromebook
1. Otwórz sklep App Store w Chromebook i Wyszukaj **pulpit zdalny Microsoft**.

    ![Pulpit zdalny firmy Microsoft](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. Zainstaluj najnowszą wersję programu Pulpit zdalny Microsoft. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Uzyskiwanie dostępu do maszyny wirtualnej z Chromebook przy użyciu protokołu RDP
1. Otwórz plik **RDP** pobrany na komputerze z zainstalowanym **pulpit zdalny Microsoft** . Należy rozpocząć łączenie się z maszyną wirtualną. 

    ![Łączenie z maszyną wirtualną](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Po wyświetleniu monitu wprowadź hasło.
    ![Łączenie z maszyną wirtualną](./media/how-to-use-classroom-lab/password-chromebook.png)


1. Wybierz pozycję **Kontynuuj** , jeśli zostanie wyświetlone następujące ostrzeżenie. 

    ![Ostrzeżenie dotyczące certyfikatu](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Powinien zostać wyświetlony pulpit maszyny wirtualnej, z którą nawiązujesz połączenie.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat nawiązywania połączenia z maszynami wirtualnymi z systemem Linux, zobacz [łączenie z maszynami](how-to-use-remote-desktop-linux-student.md)


