---
title: Włącz zamykanie maszyn wirtualnych przy rozłączaniu Azure Lab Services | Microsoft Docs
description: Informacje na temat włączania lub wyłączania automatycznego zamykania maszyn wirtualnych po odłączeniu połączenia z pulpitem zdalnym.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0c5c22d5e4a9d66413e37cce095f5497915bd122
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445716"
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