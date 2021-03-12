---
title: Używanie portalu do powiadomień o konserwacji
description: Wyświetlaj powiadomienia dotyczące konserwacji maszyn wirtualnych działających na platformie Azure i uruchamiaj samoobsługową konserwację przy użyciu portalu.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: a13d79a28ac07d736b1eaf0d0e6b7f7b1ba4a4ec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557713"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Obsługa powiadomień dotyczących planowanej konserwacji przy użyciu portalu

**Ten artykuł ma zastosowanie do maszyn wirtualnych z systemami Linux i Windows.**

Po zaplanowaniu [planowanej fazy konserwacji](maintenance-notifications.md) możesz sprawdzić listę maszyn wirtualnych, których dotyczy problem. 

Możesz użyć Azure Portal i wyszukać maszyny wirtualne zaplanowane do konserwacji.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W lewym okienku nawigacji kliknij pozycję **Virtual Machines**.

3. W okienku Virtual Machines wybierz przycisk **Edytuj kolumny** , aby otworzyć listę dostępnych kolumn.

4. Wybierz i Dodaj następujące kolumny:

   **Stan konserwacji**: pokazuje stan konserwacji maszyny wirtualnej. Oto potencjalne wartości:
      
    | Wartość | Opis |
    |-------|-------------|
    | Rozpocznij teraz | Maszyna wirtualna znajduje się w oknie obsługi samoobsługowej, która umożliwia samodzielne zainicjowanie obsługi. Zobacz poniżej, jak uruchomić konserwację na maszynie wirtualnej. | 
    | Zaplanowana | Zaplanowano konserwację maszyny wirtualnej bez opcji inicjowania obsługi. Aby dowiedzieć się więcej o oknie obsługi, wybierz okno konserwacji zaplanowane w tym widoku lub kliknij maszynę wirtualną. | 
    | Już Zaktualizowano | Maszyna wirtualna została już zaktualizowana i w tej chwili nie są wymagane żadne dalsze akcje. | 
    | Spróbuj ponownie później | Inicjowanie obsługi nie powiodło się. Będzie można użyć opcji konserwacji samoobsługowej w późniejszym czasie. | 
    | Ponów próbę teraz | Można ponowić próbę wykonania wcześniej nieudanej konserwacji. | 
    | - | Maszyna wirtualna nie jest częścią planowanej fazy obsługi. |

   **Konserwacja — okno** samoobsługowe: pokazuje przedział czasu, w którym można uruchomić konserwację na maszynach wirtualnych.
   
   **Konserwacja — okno zaplanowane**: pokazuje przedział czasu, w którym platforma Azure będzie obsługiwać maszynę wirtualną w celu ukończenia konserwacji. 



## <a name="notification-and-alerts-in-the-portal"></a>Powiadomienia i alerty w portalu

Platforma Azure komunikuje harmonogram planowanej konserwacji, wysyłając wiadomość e-mail do właściciela subskrypcji i grupy współwłaścicieli. Możesz dodać kolejnych adresatów i kanałów do tej komunikacji, tworząc alerty dzienników aktywności platformy Azure. Aby uzyskać więcej informacji, zobacz [tworzenie alertów dziennika aktywności w powiadomieniach usługi](../service-health/alerts-activity-log-service-notifications-portal.md).

Upewnij się, że **Typ zdarzenia** jest ustawiony **jako planowana konserwacja** i **usługi** jako **Virtual Machine Scale Sets** i/lub **Virtual Machines**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Rozpocznij konserwację na maszynie wirtualnej z poziomu portalu

Podczas przeglądania szczegółowych informacji o maszynie wirtualnej będzie można zobaczyć więcej szczegółów związanych z konserwacją.  
W górnej części widoku Szczegóły maszyny wirtualnej zostanie dodana nowa wstążka powiadomień, jeśli maszyna wirtualna zostanie uwzględniona w planowanej fazie konserwacji. Ponadto nowa opcja jest dodawana, aby rozpocząć konserwację, jeśli jest to możliwe. 


Kliknij powiadomienie o konserwacji, aby wyświetlić stronę obsługa z dodatkowymi szczegółami dotyczącymi planowanej konserwacji. Z tego miejsca będzie można **rozpocząć konserwację** na maszynie wirtualnej.

Po rozpoczęciu konserwacji maszyna wirtualna zostanie utrzymana, a stan konserwacji zostanie zaktualizowany w celu odzwierciedlenia wyniku w ciągu kilku minut.

Jeśli pominięto okno samoobsługowe, nadal będzie można zobaczyć okno, gdy maszyna wirtualna będzie utrzymywana na platformie Azure. 


## <a name="next-steps"></a>Następne kroki

Możesz również obsługiwać planowaną konserwację przy użyciu [interfejsu wiersza polecenia platformy Azure](maintenance-notifications-cli.md) lub [programu PowerShell](maintenance-notifications-powershell.md).
