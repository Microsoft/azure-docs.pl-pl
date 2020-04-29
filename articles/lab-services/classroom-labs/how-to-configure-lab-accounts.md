---
title: Konfigurowanie kont laboratorium w Azure Lab Services | Microsoft Docs
description: W tym artykule opisano sposób tworzenia konta laboratorium, wyświetlania wszystkich kont laboratorium lub usuwania konta laboratorium w Azure Lab Services.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79284306"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurowanie kont laboratorium w Azure Lab Services 
W Azure Lab Services konto laboratorium jest kontenerem dla zarządzanych typów laboratorium, takich jak pracownie. Administrator konfiguruje konto laboratorium przy użyciu Azure Lab Services i zapewnia dostęp do właścicieli laboratorium, którzy mogą tworzyć laboratoria na koncie. 

W tym artykule przedstawiono sposób wykonywania następujących zadań: 

- Określ zakres adresów dla maszyn wirtualnych w laboratorium
- Skonfiguruj automatyczne zamykanie maszyn wirtualnych przy rozłączaniu

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Określ zakres adresów dla maszyn wirtualnych w laboratorium
Poniższa procedura zawiera kroki umożliwiające określenie zakresu adresów dla maszyn wirtualnych w laboratorium. W przypadku aktualizacji zakresu, który został wcześniej określony, zmodyfikowany zakres adresów ma zastosowanie tylko do maszyn wirtualnych, które zostały utworzone po wprowadzeniu zmiany. 

Poniżej przedstawiono niektóre ograniczenia dotyczące określania zakresu adresów, który należy zachować na uwadze. 

- Prefiks musi być mniejszy lub równy 23. 
- Jeśli sieć wirtualna jest połączona z kontem laboratorium, podany zakres adresów nie może pokrywać się z zakresem adresów z równorzędnej sieci wirtualnej.

1. Na stronie **konto laboratorium** wybierz pozycję **Ustawienia laboratoria** w menu po lewej stronie.
2. Dla pola **zakres adresów** Określ zakres adresów dla maszyn wirtualnych, które zostaną utworzone w laboratorium. Zakres adresów powinien znajdować się w notacji Classless Inter-Domain Routing (CIDR) (przykład: 10.20.0.0/23). Maszyny wirtualne w laboratorium zostaną utworzone w tym zakresie adresów.
3. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Konfiguruj zakres adresów](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatyczne zamykanie maszyn wirtualnych przy rozłączaniu
Można włączyć lub wyłączyć automatyczne zamykanie maszyn wirtualnych laboratorium systemu Windows (szablonu lub ucznia) po odłączeniu połączenia z pulpitem zdalnym. Możesz również określić, jak długo maszyny wirtualne powinny czekać, aby użytkownik mógł ponownie nawiązać połączenie przed automatycznym zamknięciem.

![Ustawienie automatycznego zamykania na koncie laboratorium](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

To ustawienie ma zastosowanie do wszystkich laboratoriów utworzonych w ramach konta laboratorium. Właściciel laboratorium może zastąpić to ustawienie na poziomie laboratorium. Zmiana tego ustawienia na koncie laboratorium będzie miała wpływ tylko na laboratoria, które są tworzone po wprowadzeniu zmiany.

Aby dowiedzieć się, jak właściciel laboratorium może skonfigurować to ustawienie na poziomie laboratorium, zobacz [ten artykuł](how-to-enable-shutdown-disconnect.md) .

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Zezwalanie twórcy laboratorium na wybieranie lokalizacji laboratorium](allow-lab-creator-pick-lab-location.md)
- [Łączenie sieci laboratorium z równorzędną siecią wirtualną](how-to-connect-peer-virtual-network.md)
- [Dołączanie galerii obrazów udostępnionych do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
