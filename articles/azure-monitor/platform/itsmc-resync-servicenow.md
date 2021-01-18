---
title: Ręczne rozwiązywanie problemów z synchronizacją usługi ServiceNow
description: Zresetuj połączenie do usługi ServiceNow, aby alerty w Microsoft Azure mogły ponownie wywołać usługi ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: aede7e3dec886d6a6213c64b386cacd725dd74f5
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562798"
---
# <a name="how-to-manually-fix-sync-problems"></a>Ręczne rozwiązywanie problemów z synchronizacją

Azure Monitor mogą łączyć się z dostawcami zarządzania usługami IT innych firm (narzędzia ITSM). Usługi ServiceNow jest jednym z tych dostawców.

Ze względów bezpieczeństwa może być konieczne odświeżenie tokenu uwierzytelniania używanego dla połączenia z usługą usługi ServiceNow.
Aby ponownie uaktywnić połączenie i odświeżyć token, użyj następującego procesu synchronizacji:

1. Wyszukaj rozwiązanie na banerze z góry wyszukiwania, a następnie wybierz odpowiednie rozwiązania

    ![Zrzut ekranu pokazujący górny baner wyszukiwania i miejsce, w którym należy wybrać odpowiednie rozwiązania.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na ekranie rozwiązania wybierz opcję "Zaznacz wszystko" w filtrze subskrypcji, a następnie Przefiltruj według "Servicedesk"

    ![Zrzut ekranu pokazujący, gdzie wybrać opcję Zaznacz wszystko i miejsce, w którym ma zostać przefiltrowane według Servicedesk.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Wybierz rozwiązanie połączenia usługi narzędzia ITSM.
1. Wybierz pozycję połączenie narzędzia ITSM na lewym transparencie.

    ![Zrzut ekranu pokazujący lokalizację wybierania połączeń narzędzia ITSM.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Wybierz każdy łącznik z listy. 
    1. Kliknij nazwę łącznika, aby go skonfigurować
    1. Usuń wszystkie łączniki, które nie są już używane

    1. Aktualizowanie pól zgodnie z [tymi definicjami](./itsmc-connections.md) na podstawie typu partnera

    1. Kliknij pozycję Synchronizuj.

       ![Zrzut ekranu, który podświetla przycisk Synchronizuj.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Kliknij pozycję Zapisz

        ![Nowe połączenie](media/itsmc-resync-servicenow/save-8bit.png)

f.    Przejrzyj powiadomienia, aby sprawdzić, czy proces został uruchomiony.
