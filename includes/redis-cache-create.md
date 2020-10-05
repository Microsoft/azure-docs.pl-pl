---
title: plik dołączania
description: plik dołączania
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e0557f8eb2fd120baeebee96acb4ef539344e3e7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87528835"
---
1. Aby utworzyć pamięć podręczną, zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**. 
   
   ![Wybierz pozycję Utwórz zasób](media/redis-cache-create/create-a-resource.png)
   
1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.
   
   ![Wybieranie usługi Azure cache for Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to * \<DNS name> . Redis.cache.Windows.NET*. | 
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Warstwa cenowa** | Lista rozwijana i wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Wybierz przycisk **Utwórz**. 
   
   ![ Tworzenie pamięci podręcznej Azure Cache for Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   > [!NOTE]
   > Możesz oczekiwać, że inicjowanie obsługi pamięci podręcznej zajmie około 15 do 20 minut.   
   
   Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia.
   
   ![Utworzono pamięć podręczną platformy Azure dla Redis](media/redis-cache-create/redis-cache-cache-created.png)

