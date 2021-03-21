---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: da36cb5c5d2db20b89f80d381f48632c7528c193
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002469"
---
1. Aby utworzyć pamięć podręczną, zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="W okienku nawigacji po lewej stronie zostanie wyróżniony zasób.":::

   
1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="W obszarze nowe są wyróżnione bazy danych, a usługa Azure cache for Redis jest wyróżniona.":::
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to *\<DNS name> . Redis.cache.Windows.NET*. | 
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Warstwa cenowa** | Lista rozwijana i wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md). |

1. Wybierz kartę **Sieć** lub kliknij przycisk **sieci** w dolnej części strony.

1. Na karcie **Sieć** wybierz metodę łączności.

1. Wybierz kartę **Dalej: Zaawansowane** lub kliknij przycisk **Dalej: Zaawansowane** w dolnej części strony.

1. Na karcie **Zaawansowane** dla podstawowego lub standardowego wystąpienia pamięci podręcznej wybierz opcję Włącz przełącznik, jeśli chcesz włączyć port inny niż TLS. Możesz również wybrać, która wersja Redis ma być używana, 4 lub (wersja zapoznawcza) 6.

    :::image type="content" source="media/redis-cache-create/redis-version.png" alt-text="Redis w wersji 4 lub 6.":::

1. Na karcie **Zaawansowane** wystąpienia pamięci podręcznej Premium Skonfiguruj ustawienia dla portu niezwiązanego z protokołem TLS, klastrowanie i trwałość danych. Możesz również wybrać, która wersja Redis ma być używana, 4 lub (wersja zapoznawcza) 6. 

1. Wybierz kartę **następne: Tagi** lub kliknij przycisk **Dalej: Tagi** w dolnej części strony.

1. Opcjonalnie na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz przydzielić zasób. 

1. Wybierz pozycję **Przejrzyj i utwórz**. Nastąpi przekierowanie do karty Recenzja + tworzenie, w której platforma Azure weryfikuje konfigurację.

1. Po wyświetleniu komunikatu o pomyślnym sprawdzeniu poprawności, wybierz pozycję **Utwórz**.

Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia. 