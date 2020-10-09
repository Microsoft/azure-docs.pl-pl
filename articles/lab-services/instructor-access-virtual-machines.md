---
title: Nauczycieli dostępu do maszyn wirtualnych w Azure Lab Services
description: W tym artykule przedstawiono sposób, w jaki nauczyciele mogą uzyskać dostęp do swoich maszyn wirtualnych w widoku nauczycieli. Na przykład asystent nauczania może być nauczycieli dla jednej klasy, ale student dla innych klas.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad4f9cfd11b372e5c6da5c17eaeba82b0cd8a91f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445104"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>Dostęp do maszyn wirtualnych jako uczniów z widoku nauczycieli
W tym artykule przedstawiono sposób, w jaki wykładowcy mogą uzyskać dostęp do swoich maszyn wirtualnych dla klas, które uczestniczyły jako uczniowie. 

Poniżej przedstawiono scenariusz, w którym ta funkcja pomoże. Asystent nauczania to nauczycieli dla jednej klasy, ale student w innych klasach. Ponadto asystent nauczania chce wyświetlać i uzyskiwać dostęp do maszyn wirtualnych uczniów z widoku nauczycieli, który pokazuje laboratoria, których są właścicielami. 

## <a name="access-vms-from-educator-view"></a>Dostęp do maszyn wirtualnych z widoku nauczycieli

1. Zaloguj się do [witryny sieci web Azure Lab Services](https://labs.azure.com). Zobaczysz odpowiednie laboratoria. Te laboratoria mogą być laboratoriami utworzonymi przez siebie lub w laboratoriach przypisanych do Ciebie jako właściciel. Aby uzyskać więcej informacji, zobacz [jak dodać dodatkowych właścicieli do istniejącego laboratorium](how-to-add-user-lab-owner.md)
2. Aby uzyskać dostęp do maszyn wirtualnych dla klas, które można wziąć jako student, wybierz ikonę komputera w prawym górnym rogu. Upewnij się, że są widoczne maszyny wirtualne, do których możesz uzyskać dostęp jako student. W poniższym przykładzie użytkownik jest asystentem nauczania dla laboratorium języka Python, ale jest studentem laboratorium Java. Dlatego użytkownik widzi maszynę wirtualną z laboratorium Java na liście rozwijanej. Użytkownik może uruchomić maszynę wirtualną i nawiązać z nią połączenie. 
    
    ![Dostęp do maszyn wirtualnych uczniów](./media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Łączenie z maszyną wirtualną](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP na Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)
- [Korzystanie z usług pulpitu zdalnego dla maszyn wirtualnych z systemem Linux](how-to-use-remote-desktop-linux-student.md)
