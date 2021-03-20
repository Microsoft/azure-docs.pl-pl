---
title: Dodawanie replik do usługi Azure cache for Redis (wersja zapoznawcza)
description: Dowiedz się, jak dodać więcej replik do pamięci podręcznej platformy Azure w warstwie Premium dla wystąpień Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 87b5ec5eb13f2bc53bdf993547ce3da1c74404bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91566792"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Dodawanie replik do usługi Azure cache for Redis (wersja zapoznawcza)
W tym artykule dowiesz się, jak skonfigurować wystąpienie usługi Azure cache z dodatkowymi replikami przy użyciu Azure Portal.

Usługa Azure cache for Redis w warstwach Standardowa i Premium oferuje nadmiarowość przez hosting każdej pamięci podręcznej na dwóch dedykowanych maszynach wirtualnych. Te maszyny wirtualne są skonfigurowane jako podstawowe i replika. Gdy podstawowa maszyna wirtualna staje się niedostępna, replika wykryje i przejdzie automatycznie do nowej głównej. Teraz można zwiększyć liczbę replik w pamięci podręcznej Premium do trzech, co zapewnia całkowitą liczbę czterech maszyn wirtualnych tworzących pamięć podręczną. Posiadanie wielu replik powoduje zwiększenie odporności niż to, co może zapewnić jedna replika.

> [!IMPORTANT]
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure. 
> 

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
    | **Typ pamięci podręcznej** | Wybierz pamięć podręczną [warstwy Premium](https://azure.microsoft.com/pricing/details/cache/) . |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |
   
1. Na stronie **Zaawansowane** wybierz pozycję **Liczba replik**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Liczba replik.":::

1. Zostaw inne opcje w ustawieniach domyślnych. 

    > [!NOTE]
    > Obsługa wiele replik działa tylko z nieklastrowanymi pamięciami podręcznymi.
    >

1. Kliknij pozycję **Utwórz**.
   
    Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia.

    > [!NOTE]
    > Nie można zmienić liczby replik w pamięci podręcznej po jej utworzeniu.
    >

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

> [!div class="nextstepaction"]
> [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)
