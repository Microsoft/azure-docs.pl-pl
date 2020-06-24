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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: a617bdc8e6fcb4588b26f898f437dc7bba3c2f59
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84895892"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Włącz automatyczne zamykanie maszyn wirtualnych przy rozłączaniu
W tym artykule opisano sposób włączania lub wyłączania automatycznego zamykania maszyn wirtualnych z **systemem Windows 10** Lab (szablonu lub ucznia) po odłączeniu połączenia z pulpitem zdalnym. Możesz również określić, jak długo maszyny wirtualne powinny czekać, aby użytkownik mógł ponownie nawiązać połączenie przed automatycznym zamknięciem.

Administrator konta laboratorium może skonfigurować to ustawienie dla konta laboratorium, w którym tworzysz laboratoria. Aby uzyskać więcej informacji, zobacz [Konfigurowanie automatycznego zamykania maszyn wirtualnych przy rozłączaniu dla konta laboratorium](how-to-configure-lab-accounts.md). Jako właściciel laboratorium możesz zastąpić to ustawienie podczas tworzenia laboratorium lub po utworzeniu laboratorium. 

## <a name="configure-when-creating-a-lab"></a>Konfiguruj podczas tworzenia laboratorium
Na stronie krok 3 Kreatora tworzenia laboratorium można włączyć lub wyłączyć tę funkcję, a także określić, jak długo maszyna wirtualna ma czekać na ponowne połączenie przed automatycznym zamknięciem. 

![Konfiguruj podczas tworzenia laboratorium](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Konfiguruj po utworzeniu laboratorium
To ustawienie można skonfigurować na stronie **Ustawienia** , jak pokazano na poniższej ilustracji: 

![Konfiguruj po utworzeniu laboratorium](./media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Jeśli system operacyjny Windows zostanie zamknięty na maszynie wirtualnej przed rozłączeniem sesji RDP z maszyną wirtualną, funkcja Autozamykania nie będzie działać prawidłowo.  

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Pulpit nawigacyjny dla laboratoriów zajęć](use-dashboard.md)