---
title: 'Szybki Start: tworzenie pamięci podręcznej warstwy przedsiębiorstwa'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć wystąpienie pamięci podręcznej platformy Azure dla warstwy przedsiębiorstwa Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 95f6e2cf7803ff0b152f33c08f170725b5a0e94e
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95795668"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Szybki Start: tworzenie pamięci podręcznej warstwy przedsiębiorstwa (wersja zapoznawcza)

Usługa Azure cache for Redis "warstwy przedsiębiorstwa zapewniają w pełni zintegrowane i zarządzane [Redis przedsiębiorstwa](https://redislabs.com/redis-enterprise/) na platformie Azure. Są one obecnie dostępne jako wersja zapoznawcza. W tej wersji zapoznawczej są dostępne dwie nowe warstwy:
* Enterprise, która używa pamięci lotnej (DRAM) na maszynie wirtualnej do przechowywania danych
* Program Enterprise Flash, który używa zarówno pamięci nietrwałej, jak i nielotnej (interfejsu NVMe lub SSD) do przechowywania danych.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem musisz mieć subskrypcję platformy Azure. Jeśli go nie masz, Utwórz [konto](https://azure.microsoft.com/). Aby uzyskać więcej informacji, zobacz [wymagania dotyczące warstwy przedsiębiorstwa](cache-overview.md#enterprise-tier-requirements).

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
1. Aby utworzyć pamięć podręczną, zaloguj się do Azure Portal przy użyciu linku w zaproszeniu do wersji zapoznawczej i wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Wybieranie usługi Azure cache for Redis":::
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to *\<DNS name> . <Azure region> . redisenterprise.cache.azure.net*. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Warstwy przedsiębiorstwa są dostępne w ograniczonych regionach świadczenia usługi Azure w wersji zapoznawczej. |
   | **Typ pamięci podręcznej** | Lista rozwijana i wybierz warstwę *Enterprise* lub *Enterprise Flash* i rozmiar. |  Warstwa określa rozmiar, wydajność i funkcje, które są dostępne dla pamięci podręcznej. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Podstawowe informacje o warstwie korporacyjnej":::

   > [!NOTE] 
   > Pamiętaj, aby zaznaczyć pole wyboru "warunki" przed kontynuowaniem.
   >

1. Wybierz pozycję **Dalej: sieci** i Pomiń.

   > [!NOTE] 
   > Opcja Link prywatny jest pobierana i może nie być dostępna natychmiast w Twoim regionie.
   >

1. Wybierz pozycję **Dalej: Zaawansowane** i ustaw **zasady klastrowania** dla **przedsiębiorstwa**.
   
   Można zachować ustawienia domyślne lub zmienić je odpowiednio do potrzeb. Włączenie opcji **Zezwalaj na dostęp tylko za pośrednictwem protokołu TLS** wymaga użycia protokołu TLS w celu uzyskania dostępu do nowej pamięci podręcznej z poziomu aplikacji.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Warstwa przedsiębiorstwa — zaawansowane":::

   > [!NOTE] 
   > Moduły Redis nie są jeszcze obsługiwane w warstwie programu Enterprise Flash. Jeśli planujesz użycie modułu Redis, pamiętaj o wybraniu pamięci podręcznej warstwy przedsiębiorstwa.
   >
   
1. Wybierz pozycję **Dalej: Tagi** i Pomiń.

1. Wybierz pozycję **Dalej: Przeglądanie i tworzenie**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Podsumowanie warstwy przedsiębiorstwa":::

1. Przejrzyj ustawienia i kliknij przycisk **Utwórz**.
   
   Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia wystąpienia warstwy przedsiębiorstwa usługi Azure cache for Redis.

> [!div class="nextstepaction"]
> [Tworzenie na platformie ASP.NET aplikacji internetowej, która korzysta z usługi Microsoft Azure Cache for Redis.](./cache-web-app-howto.md)

