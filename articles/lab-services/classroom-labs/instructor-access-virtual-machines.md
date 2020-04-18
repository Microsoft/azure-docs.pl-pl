---
title: Instruktor uzyskujący dostęp do maszyn wirtualnych w usługach Azure Lab Services
description: W tym artykule pokazano, jak instruktorzy mogą uzyskiwać dostęp do swoich maszyn wirtualnych uczniów z widoku instruktora. Na przykład asystent nauczyciela może być instruktorem dla jednej klasy, ale uczniem dla innych klas.
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
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641942"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Dostęp do maszyn wirtualnych jako uczeń z widoku instruktora
W tym artykule pokazano, jak instruktorzy mogą uzyskać dostęp do swoich maszyn wirtualnych dla klas, które uczestniczą jako uczniowie. 

Oto scenariusz, w którym ta funkcja pomoże. Asystent nauczyciela jest instruktorem dla jednej klasy, ale uczeń w innych klasach. Instruktor nauczania chce wyświetlać maszyny wirtualne uczniów i uzyskiwać do nich dostęp z widoku instruktora, który pokazuje laboratoria, których są właścicielami. 

## <a name="access-vms-from-instructor-view"></a>Dostęp do maszyn wirtualnych z widoku instruktora

1. Zaloguj się w [witrynie azure lab services](https://labs.azure.com). Widzisz laboratoria, które posiadasz. Te laboratoria mogą być laboratoriami utworzonymi samodzielnie lub laboratoriami, które administrator przypisany do Ciebie jako właściciela. Aby uzyskać więcej informacji, zobacz [Jak dodać dodatkowych właścicieli do istniejącego laboratorium](how-to-add-user-lab-owner.md)
2. Aby uzyskać dostęp do maszyn wirtualnych dla klas, które uczęszczasz jako uczeń, wybierz ikonę komputera w prawym górnym rogu. Upewnij się, że widzisz maszyny wirtualne, do których możesz uzyskać dostęp jako student. W poniższym przykładzie użytkownik jest asystentem nauczania w laboratorium Języka Python, ale studentem laboratorium Java. Tak, użytkownik widzi maszyny Wirtualnej z laboratorium Java na liście rozwijanej. Użytkownik może uruchomić maszynę wirtualną i połączyć się z nią. 
    
    ![Dostęp do maszyn wirtualnych dla uczniów](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Łączenie z maszyną wirtualną](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Łączenie się z maszyną wirtualną przy użyciu protokołu RDP na komputerze Mac](connect-virtual-machine-mac-rdp.md)
- [Używanie pulpitu zdalnego dla maszyn wirtualnych systemu Linux](how-to-use-remote-desktop-linux-student.md)
