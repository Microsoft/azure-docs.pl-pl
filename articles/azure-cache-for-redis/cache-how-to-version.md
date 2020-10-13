---
title: Ustaw wersję Redis dla usługi Azure cache for Redis (wersja zapoznawcza)
description: Dowiedz się, jak skonfigurować wersję Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ed0f486afe466d31388fa99b4ce5f5754210533f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91571504"
---
# <a name="set-redis-version-for-azure-cache-for-redis-preview"></a>Ustaw wersję Redis dla usługi Azure cache for Redis (wersja zapoznawcza)
W tym artykule dowiesz się, jak skonfigurować wersję oprogramowania Redis do użycia z wystąpieniem pamięci podręcznej. Usługa Azure cache for Redis oferuje najnowszą wersję główną Redis i co najmniej jedną poprzednią wersję. Aktualizuje te wersje regularnie, gdy zostanie wydane nowsze oprogramowanie Redis. Można wybrać jedną z dwóch dostępnych wersji. Należy pamiętać, że pamięć podręczna zostanie uaktualniona do następnej wersji automatycznie, jeśli aktualnie używana wersja nie jest już obsługiwana.

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

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
    | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to * \<DNS name> . Redis.cache.Windows.NET*. | 
    | **Lokalizacja** | Wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
    | **Typ pamięci podręcznej** | Wybierz [warstwę i rozmiar pamięci podręcznej](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |
   
1. Na stronie **Zaawansowane** wybierz wersję programu Redis, która ma zostać użyta.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Wybierz pozycję Pamięć podręczna platformy Azure dla Redis.":::

1. Kliknij przycisk **Utwórz**. 
   
    Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia.

    > [!NOTE]
    > W tej chwili nie można zmienić wersji Redis po utworzeniu pamięci podręcznej.
    >

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

> [!div class="nextstepaction"]
> [Pamięć podręczna systemu Azure dla warstw usługi Redis Premium](cache-overview.md#service-tiers)
