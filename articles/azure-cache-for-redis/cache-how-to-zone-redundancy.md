---
title: Włącz nadmiarowość strefy dla usługi Azure cache for Redis (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować nadmiarowość strefy dla pamięci podręcznej platformy Azure w warstwie Premium i Enterprise dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 0623f47528d0530838f62c28cf5546e1e66c187b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508269"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Włącz nadmiarowość strefy dla usługi Azure cache for Redis (wersja zapoznawcza)
W tym artykule dowiesz się, jak skonfigurować strefowo nadmiarowe wystąpienie pamięci podręcznej platformy Azure przy użyciu Azure Portal.

Usługa Azure cache for Redis Standard, Premium i warstwy przedsiębiorstwa zapewnia wbudowaną nadmiarowość, ponieważ hostuje każdą pamięć podręczną na dwóch dedykowanych maszynach wirtualnych. Mimo że te maszyny wirtualne znajdują się w oddzielnych [domenach błędów i aktualizacji platformy Azure](../virtual-machines/availability.md) oraz o wysokiej dostępności, są podatne na awarie na poziomie centrum danych. Usługa Azure cache for Redis obsługuje również nadmiarowość stref w warstwach Premium i Enterprise. Pamięć podręczna nadmiarowa strefy jest uruchamiana na maszynach wirtualnych w wielu [strefach dostępności](../availability-zones/az-overview.md). Zapewnia wyższy poziom odporności i dostępności.

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

> [!NOTE]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej — [skontaktuj się z nami](mailto:azurecache@microsoft.com) , jeśli chcesz.
>

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
Aby utworzyć pamięć podręczną, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**.
  
1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Wybierz pozycję Pamięć podręczna platformy Azure dla Redis.":::
   
1. Na stronie **podstawowe** Skonfiguruj ustawienia nowej pamięci podręcznej.
   
    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Subskrypcja** | Wybierz subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
    | **Grupa zasobów** | Wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
    | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to *\<DNS name> . Redis.cache.Windows.NET*. | 
    | **Lokalizacja** | Wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
    | **Typ pamięci podręcznej** | Wybierz pamięć podręczną [warstwy Premium lub Enterprise](https://azure.microsoft.com/pricing/details/cache/) . |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |
   
1. Na stronie **Zaawansowane** dla pamięci podręcznej warstwy Premium wybierz pozycję **Liczba replik**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Liczba replik":::

1. Wybierz pozycję **strefy dostępności**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Strefy dostępności":::

1. Zostaw inne opcje w ustawieniach domyślnych. 

    > [!NOTE]
    > Obsługa nadmiarowości stref działa tylko z pamięciami podręcznymi nieklastrowanymi i replikowanymi geograficznie. Ponadto nie obsługuje prywatnego linku, skalowania, trwałości danych ani importowania/eksportowania.
    >

1. Kliknij pozycję **Utwórz**. 
   
    Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia.
   
    > [!NOTE]
    > Stref dostępności nie można zmienić po utworzeniu pamięci podręcznej.
    >

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

> [!div class="nextstepaction"]
> [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)