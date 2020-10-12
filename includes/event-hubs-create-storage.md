---
title: plik dołączany
description: plik dołączany
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75692552"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Tworzenie konta magazynu dla hosta procesora zdarzeń
Host procesora zdarzeń to inteligentny agent, który upraszcza odbieranie zdarzeń z usługi Event Hubs przez zarządzanie trwałymi punktami kontrolnymi i równoległymi odbiorami. W przypadku obsługi punktów kontrolnych host procesora zdarzeń wymaga konta magazynu. Poniższy przykład pokazuje, jak utworzyć konto magazynu oraz uzyskać jego klucze na potrzeby dostępu:

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**.

    ![Utwórz element menu zasobu, Microsoft Azure Portal](./media/event-hubs-create-storage/create-resource.png)

2. Wybierz pozycję **Magazyn** > **Konto magazynu**.
   
    ![Wybierz pozycję konto magazynu, Microsoft Azure Portal](./media/event-hubs-create-storage/select-storage-account.png)

3. Na stronie **Utwórz konto magazynu** wykonaj następujące kroki: 

   1. Wprowadź **nazwę konta magazynu**.
   2. Wybierz **subskrypcję** platformy Azure, która zawiera centrum zdarzeń.
   3. Wybierz lub Utwórz **grupę zasobów** , w której znajduje się centrum zdarzeń.
   4. Wybierz **lokalizację** , w której chcesz utworzyć zasób. 
   5. Wybierz pozycję **Przeglądanie + tworzenie**.
   
        ![Przejrzyj + Utwórz, Utwórz konto magazynu, Microsoft Azure Portal](./media/event-hubs-create-storage/review-create.png)

4. Na stronie **Przeglądanie + tworzenie** sprawdź wartości i wybierz pozycję **Utwórz**. 

    ![Przejrzyj ustawienia konta magazynu i Utwórz, Microsoft Azure Portal](./media/event-hubs-create-storage/create-storage-account.png)
5. Po wyświetleniu komunikatu **wdrożenia zakończone powodzeniem** w powiadomieniach wybierz pozycję **Przejdź do zasobu** , aby otworzyć stronę konto magazynu. Alternatywnie możesz rozwinąć **Szczegóły wdrożenia** , a następnie wybrać nowy zasób z listy zasobów.  

    ![Przejdź do zasobów, wdrożenie konta magazynu, Microsoft Azure Portal](./media/event-hubs-create-storage/go-to-resource.png) 
6. Wybierz **kontenery**.

    ![Wybierz usługę kontenera obiektów blob, konta magazynu, Microsoft Azure Portal](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Wybierz pozycję **+ kontener** u góry, wprowadź **nazwę** kontenera, a następnie wybierz **przycisk OK**. 

    ![Tworzenie nowego kontenera obiektów blob, kont magazynu Microsoft Azure Portal](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Wybierz pozycję **klucze dostępu** z menu strony **konta magazynu** i skopiuj wartość **Klucz1**.

    Zapisz następujące wartości w Notatniku lub innej lokalizacji tymczasowej.
    - Nazwa konta magazynu
    - Klucz dostępu dla konta magazynu
    - Nazwa kontenera
