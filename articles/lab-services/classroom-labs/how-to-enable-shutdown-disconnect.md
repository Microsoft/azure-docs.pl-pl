---
title: Włącz zamykanie maszyn wirtualnych przy rozłączaniu Azure Lab Services | Microsoft Docs
description: Informacje na temat włączania lub wyłączania automatycznego zamykania maszyn wirtualnych po odłączeniu połączenia z pulpitem zdalnym.
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
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77117136"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Włącz automatyczne zamykanie maszyn wirtualnych przy rozłączaniu
W tym artykule opisano sposób włączania lub wyłączania automatycznego zamykania maszyn wirtualnych z **systemem Windows 10** Lab (szablonu lub ucznia) po odłączeniu połączenia z pulpitem zdalnym. Możesz również określić, jak długo maszyny wirtualne powinny czekać, aby użytkownik mógł ponownie nawiązać połączenie przed automatycznym zamknięciem.

Administrator konta laboratorium może skonfigurować to ustawienie dla konta laboratorium, w którym tworzysz laboratoria. Aby uzyskać więcej informacji, zobacz [Konfigurowanie automatycznego zamykania maszyn wirtualnych przy rozłączaniu dla konta laboratorium](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect). Jako właściciel laboratorium możesz zastąpić to ustawienie podczas tworzenia laboratorium lub po utworzeniu laboratorium. 

## <a name="configure-when-creating-a-lab"></a>Konfiguruj podczas tworzenia laboratorium
Na stronie krok 3 Kreatora tworzenia laboratorium można włączyć lub wyłączyć tę funkcję, a także określić, jak długo maszyna wirtualna ma czekać na ponowne połączenie przed automatycznym zamknięciem. 

![Konfiguruj podczas tworzenia laboratorium](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfiguruj po utworzeniu laboratorium
To ustawienie można skonfigurować na stronie **Ustawienia** , jak pokazano na poniższej ilustracji: 

![Konfiguruj po utworzeniu laboratorium](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Jeśli system operacyjny Windows zostanie zamknięty na maszynie wirtualnej przed rozłączeniem sesji RDP z maszyną wirtualną, funkcja Autozamykania nie będzie działać prawidłowo.  

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Pulpit nawigacyjny dla laboratoriów zajęć](use-dashboard.md)