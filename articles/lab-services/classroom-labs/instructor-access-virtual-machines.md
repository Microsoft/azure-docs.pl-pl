---
title: Instruktor uzyskuje dostęp do maszyn wirtualnych w Azure Lab Services
description: W tym artykule przedstawiono sposób, w jaki instruktorzy mogą uzyskać dostęp do maszyn wirtualnych uczniów z widoku instruktora. Na przykład asystent nauczania może być instruktorem dla jednej klasy, ale student dla innych klas.
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641942"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Dostęp do maszyn wirtualnych jako uczniów z widoku instruktora
W tym artykule przedstawiono sposób, w jaki instruktorzy mogą uzyskać dostęp do swoich maszyn wirtualnych dla klas, które uczestniczyły jako studenci. 

Poniżej przedstawiono scenariusz, w którym ta funkcja pomoże. Asystent nauczania jest instruktorem dla jednej klasy, ale studenta w innych klasach. Ponadto instruktorzy nauczania chcą wyświetlać i uzyskiwać dostęp do maszyn wirtualnych uczniów z widoku instruktora, który pokazuje laboratoria, których są właścicielami. 

## <a name="access-vms-from-instructor-view"></a>Dostęp do maszyn wirtualnych z widoku instruktora

1. Zaloguj się do [witryny sieci web Azure Lab Services](https://labs.azure.com). Zobaczysz odpowiednie laboratoria. Te laboratoria mogą być laboratoriami utworzonymi przez siebie lub w laboratoriach przypisanych do Ciebie jako właściciel. Aby uzyskać więcej informacji, zobacz [jak dodać dodatkowych właścicieli do istniejącego laboratorium](how-to-add-user-lab-owner.md)
2. Aby uzyskać dostęp do maszyn wirtualnych dla klas, które można wziąć jako student, wybierz ikonę komputera w prawym górnym rogu. Upewnij się, że są widoczne maszyny wirtualne, do których możesz uzyskać dostęp jako student. W poniższym przykładzie użytkownik jest asystentem nauczania dla laboratorium języka Python, ale jest studentem laboratorium Java. Dlatego użytkownik widzi maszynę wirtualną z laboratorium Java na liście rozwijanej. Użytkownik może uruchomić maszynę wirtualną i nawiązać z nią połączenie. 
    
    ![Dostęp do maszyn wirtualnych uczniów](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Łączenie z maszyną wirtualną](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Nawiązywanie połączenia z maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac](connect-virtual-machine-mac-rdp.md)
- [Korzystanie z usług pulpitu zdalnego dla maszyn wirtualnych z systemem Linux](how-to-use-remote-desktop-linux-student.md)
