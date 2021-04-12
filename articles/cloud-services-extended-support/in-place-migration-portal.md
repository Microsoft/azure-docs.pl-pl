---
title: Jak przeprowadzić migrację — Portal
description: Jak przeprowadzić migrację do Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286954"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Migrowanie do Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal

W tym artykule pokazano, jak za pomocą Azure Portal przeprowadzić migrację z [Cloud Services (klasycznego)](../cloud-services/cloud-services-choose-me.md) do [Cloud Services (obsługa rozszerzona)](overview.md).

> [!IMPORTANT]
> Migrowanie z Cloud Services (klasycznego) do Cloud Services (obsługa rozszerzona) za pomocą narzędzia migracji jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

**Upewnij się, że jesteś administratorem subskrypcji.**

Aby przeprowadzić migrację, należy dodać jako administratora dla subskrypcji w [Azure Portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu **centrum** wybierz pozycję **subskrypcja**. Jeśli go nie widzisz, wybierz pozycję **wszystkie usługi**.
3. Znajdź odpowiedni wpis subskrypcji, a następnie poszukaj pola **My role** . W przypadku współadministratora wartość powinna być *administratorem konta*.

Jeśli nie możesz dodać współadministratora, skontaktuj się z administratorem usługi lub [współadministratorem](../role-based-access-control/classic-administrators.md) , aby uzyskać subskrypcję, która ma zostać dodana.

**Zarejestruj się w celu uzyskania dostawcy zasobów migracji**

1. Zarejestruj się u dostawcy zasobów migracji `Microsoft.ClassicInfrastructureMigrate` i funkcji wersji zapoznawczej `Cloud Services` w obszarze Przestrzeń nazw Microsoft. COMPUTE przy użyciu [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1).  
1. Zaczekaj pięć minut na zakończenie rejestracji, a następnie sprawdź stan zatwierdzenia. 

## <a name="migrate-your-cloud-service-resources"></a>Migrowanie zasobów usługi w chmurze

1. Przejdź do [bloku portalu Cloud Services (klasycznego)](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames). 
2. Wybierz usługę w chmurze, którą chcesz zmigrować.
3. Wybierz blok **Migruj do ARM** .

    > [!NOTE]
    > W przypadku migrowania Cloud Services (klasycznego) znajdującego się w sieci wirtualnej (klasycznej) zostanie wyświetlony komunikat z prośbą o przeniesienie bloku Sieć wirtualna (klasyczna).
    > Nastąpi przełączenie do bloku Sieć wirtualna (klasyczna) w celu ukończenia migracji sieci wirtualnej (klasycznej) i Cloud Services (klasycznych).
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Obraz przedstawia przemieszczenie klasycznej sieci wirtualnej w Azure Portal.":::
 

4. Sprawdź poprawność migracji. 

    Jeśli sprawdzanie poprawności zakończy się pomyślnie, wszystkie wdrożenia są obsługiwane i gotowe do przygotowania.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Obraz przedstawia blok Migrowanie do ARM w Azure Portal.":::

    Jeśli sprawdzanie poprawności nie powiedzie się, zostanie wyświetlona lista nieobsługiwanych scenariuszy i trzeba będzie naprawić ją przed kontynuowaniem migracji. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="Obraz przedstawia błąd walidacji w Azure Portal.":::

5. Przygotuj się do migracji.

    Jeśli przygotowywanie zakończyło się pomyślnie, migracja jest gotowa do zatwierdzenia.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="Obraz przedstawia przebieg walidacji w Azure Portal.":::

    Jeśli przygotowanie nie powiedzie się, zapoznaj się z błędem, Rozwiąż wszelkie problemy i ponów próbę przygotowania. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="Obraz przedstawia błąd walidacji.":::

      Po przygotowaniu wszystkie Cloud Services w sieci wirtualnej są dostępne dla operacji odczytu przy użyciu bloku Azure Portal Cloud Services (klasyczny) i Cloud Services (obsługa rozszerzona). Wdrożenie usługi w chmurze (obsługa rozszerzona) można teraz przetestować, aby zapewnić prawidłowe działanie przed finalizowaniem migracji. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="Obraz przedstawia testowanie interfejsów API w bloku portalu.":::

6.  **(Opcjonalnie)** Przerwij migrację. 
    
    W przypadku wybrania opcji zaprzestania migracji Użyj przycisku **Przerwij** , aby wycofać poprzednie kroki. Wdrożenie Cloud Services (klasycznego) jest odblokowane dla wszystkich operacji CRUD.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="Obraz przedstawia przekazywanie walidacji.":::

    Jeśli przerwanie nie powiedzie się, wybierz pozycję **Ponów przerwanie**. Ponowienie próby powinno rozwiązać problem. Jeśli nie, skontaktuj się z pomocą techniczną. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="Obraz przedstawia komunikat o błędzie weryfikacji błędu.":::

7.  Zatwierdź migrację.

    >[!IMPORTANT]
    > Gdy zatwierdzisz migrację, nie ma możliwości wycofania. 
    
    Wpisz "yes" (tak), aby potwierdzić i zatwierdzić migrację. Migracja została zakończona. Zmigrowane wdrożenie Cloud Services (obsługa rozszerzona) jest odblokowane dla wszystkich operacji ". 

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z sekcją [zmiany po migracji](in-place-migration-overview.md#post-migration-changes) , aby zobaczyć zmiany w plikach wdrożenia, automatyzacji i innych atrybutach nowego wdrożenia Cloud Services (obsługa rozszerzona). 
