---
title: Ręczne rozwiązywanie problemów z synchronizacją usługi ServiceNow
description: Zresetuj połączenie do usługi ServiceNow, aby alerty w Microsoft Azure mogły ponownie wywołać usługi ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f7d69c4e112ded678c70a516202492d37ee8f60a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436865"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Rozwiązywanie problemów z łącznik ITSM

## <a name="how-to-manually-fix-servicenow-sync-problems"></a>Ręczne rozwiązywanie problemów z synchronizacją usługi ServiceNow

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

f.    Przejrzyj powiadomienia, aby sprawdzić, czy proces zakończy się pomyślnie 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [połączeń zarządzania usługami IT](itsmc-connections.md)
