---
title: Skonfiguruj automatyczne zamykanie maszyn wirtualnych w Azure Lab Services
description: W tym artykule opisano sposób konfigurowania automatycznego zamykania maszyn wirtualnych na koncie laboratorium.
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
ms.openlocfilehash: afe74e5c3aec2519ec22eee9573ae7b47c1c73f8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588211"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Skonfiguruj automatyczne zamykanie maszyn wirtualnych w przypadku ustawienia odłączania dla konta laboratorium
Można włączyć lub wyłączyć automatyczne zamykanie maszyn wirtualnych laboratorium systemu Windows (szablonu lub ucznia) po odłączeniu połączenia z pulpitem zdalnym. Możesz również określić, jak długo usługi laboratoryjne powinny czekać, aż użytkownik ponownie nawiąże połączenie przed automatycznym zamknięciem.

## <a name="enable-automatic-shutdown"></a>Włącz automatyczne zamykanie

1. Na stronie **konto laboratorium** wybierz pozycję **Ustawienia laboratoria** w menu po lewej stronie.
2. Wybierz opcję **automatycznie zamykaj maszyny wirtualne podczas rozłączania użytkowników** .
3. Określ, jak długo usługi laboratoryjne powinny czekać, zanim użytkownik będzie mógł ponownie nawiązać połączenie przed automatycznym zamknięciem maszyn wirtualnych.

    ![Ustawienie automatycznego zamykania na koncie laboratorium](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    To ustawienie ma zastosowanie do wszystkich laboratoriów utworzonych w ramach konta laboratorium. Twórca laboratorium (nauczycieli) może zastąpić to ustawienie na poziomie laboratorium. Zmiana tego ustawienia na koncie laboratorium będzie miała wpływ tylko na laboratoria, które są tworzone po wprowadzeniu zmiany.

    Aby wyłączyć to ustawienie, usuń zaznaczenie pola wyboru dla **maszyn wirtualnych, które są rozłączane automatycznie po rozłączeniu** na tej stronie. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak właściciel laboratorium może skonfigurować lub zastąpić to ustawienie na poziomie laboratorium, zobacz [ten artykuł](how-to-enable-shutdown-disconnect.md) .
