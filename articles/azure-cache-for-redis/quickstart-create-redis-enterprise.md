---
title: 'Szybki Start: tworzenie pamięci podręcznej Redis przedsiębiorstwa'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć wystąpienie usługi Azure cache for Redis w warstwach przedsiębiorstwa
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8b450ddff0952777652a957ba2ed554a4bc6497d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584826"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>Szybki Start: tworzenie pamięci podręcznej Redis przedsiębiorstwa

Usługa Azure cache for Redis "warstwy przedsiębiorstwa zapewniają w pełni zintegrowane i zarządzane [Redis przedsiębiorstwa](https://redislabs.com/redis-enterprise/) na platformie Azure. Te nowe warstwy są następujące:
* Enterprise, która używa pamięci lotnej (DRAM) na maszynie wirtualnej do przechowywania danych
* Program Enterprise Flash, który używa zarówno pamięci nietrwałej, jak i nielotnej (interfejsu NVMe lub SSD) do przechowywania danych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem musisz mieć subskrypcję platformy Azure. Jeśli go nie masz, Utwórz [konto](https://azure.microsoft.com/). Aby uzyskać więcej informacji, zobacz [zagadnienia specjalne dotyczące warstw przedsiębiorstwa](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
1. Aby utworzyć pamięć podręczną, zaloguj się do Azure Portal i wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Wybieranie usługi Azure cache for Redis":::
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to *\<DNS name> . <Azure region> . redisenterprise.cache.azure.net*. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Warstwy przedsiębiorstwa są dostępne w wybranych regionach świadczenia usługi Azure. |
   | **Typ pamięci podręcznej** | Lista rozwijana i wybierz warstwę *Enterprise* lub *Enterprise Flash* i rozmiar. |  Warstwa określa rozmiar, wydajność i funkcje, które są dostępne dla pamięci podręcznej. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Karta podstawy warstwy przedsiębiorstwa":::

   > [!NOTE] 
   > Pamiętaj, aby zaznaczyć pole wyboru "warunki" przed kontynuowaniem.
   >

1. Wybierz pozycję **Dalej: sieci** i Pomiń.

1. Wybierz pozycję **Dalej: Zaawansowane** i ustaw **zasady klastrowania** dla **przedsiębiorstwa**. Włącz **dostęp bez protokołu TLS tylko** wtedy, gdy planujesz nawiązać połączenie z nową pamięcią podręczną bez użycia protokołu TLS. Nie jest to jednak zalecane.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Karta Zaawansowane warstwy przedsiębiorstwa":::

1. Wybierz pozycję **Dalej: Tagi** i Pomiń.

1. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Recenzja warstwy korporacyjnej + Utwórz kartę":::

1. Przejrzyj ustawienia i kliknij przycisk **Utwórz**.
   
   Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia wystąpienia warstwy przedsiębiorstwa usługi Azure cache for Redis.

> [!div class="nextstepaction"]
> [Tworzenie na platformie ASP.NET aplikacji internetowej, która korzysta z usługi Microsoft Azure Cache for Redis.](./cache-web-app-howto.md)

