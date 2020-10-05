---
title: 'Szybki Start: tworzenie pamięci podręcznej warstwy przedsiębiorstwa'
description: W tym przewodniku szybki start dowiesz się, jak utworzyć wystąpienie pamięci podręcznej platformy Azure dla warstwy przedsiębiorstwa Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/12/2020
ms.openlocfilehash: 3df6cb1afe8a6249eadbbd3f61619e66be2d2478
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "83402491"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Szybki Start: tworzenie pamięci podręcznej warstwy przedsiębiorstwa (wersja zapoznawcza)

Usługa Azure cache for Redis "warstwy przedsiębiorstwa zapewniają w pełni zintegrowane i zarządzane [Redis przedsiębiorstwa](https://redislabs.com/redis-enterprise/) na platformie Azure. Są one obecnie dostępne jako wersja zapoznawcza. W tej wersji zapoznawczej są dostępne dwie nowe warstwy:
* Enterprise, która używa pamięci lotnej (DRAM) na maszynie wirtualnej do przechowywania danych
* Dysk SSD przedsiębiorstwa, który używa pamięci nietrwałej i nieulotnej (NVMe) do przechowywania danych.

Nie ma żadnego kosztu dołączenia do wersji zapoznawczej. Zarejestruj **się** w [portalu Azure Marketplace](https://aka.ms/redispreviewsignup/) , jeśli interesują Cię zainteresowania. Mamy bardzo ograniczoną liczbę elementów uczestników i nie gwarantujemy, że użytkownik zostanie zaakceptowany do wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem musisz mieć subskrypcję platformy Azure. Jeśli go nie masz, Utwórz najpierw [bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
1. Aby utworzyć pamięć podręczną, zaloguj się do Azure Portal przy użyciu linku w zaproszeniu do wersji zapoznawczej i wybierz pozycję **Utwórz zasób**.

   > [!IMPORTANT] 
   > Nie Subskrybuj *usługi Azure cache for Redis, warstwy przedsiębiorstwa* w portalu Marketplace bezpośrednio.
   > Ten krok jest wykonywany przez interfejs użytkownika portalu usługi Azure cache for Redis automatycznie.
   >
   
1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.
   
   ![Wybieranie usługi Azure cache for Redis](media/cache-create/new-cache-menu.png)
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to * \<DNS name> . <Azure region> . redisenterprise.cache.azure.net*. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Warstwy przedsiębiorstwa są dostępne w regionach zachodnie stany USA, Wschodnie stany USA 2 i Europa Zachodnia. |
   | **Warstwa pamięci podręcznej** | Rozwiń i wybierz warstwę pamięci *SSD* *przedsiębiorstwa* , a także rozmiar. |  Warstwa określa rozmiar, wydajność i funkcje, które są dostępne dla pamięci podręcznej. |
   
   ![Podstawowe informacje o warstwie korporacyjnej](media/cache-create/enterprise-tier-basics.png) 

1. Wybierz pozycję **Dalej: sieci** i Pomiń.

   > [!NOTE] 
   > Obsługa linku prywatnego będzie późniejsza.
   >

1. Wybierz pozycję **Dalej: Zaawansowane**.
   
   Można zachować ustawienia domyślne lub zmienić je odpowiednio do potrzeb. Włączenie opcji **Zezwalaj na dostęp tylko za pośrednictwem protokołu TLS**wymaga użycia protokołu TLS w celu uzyskania dostępu do nowej pamięci podręcznej z poziomu aplikacji.

   ![Warstwa przedsiębiorstwa — zaawansowane](media/cache-create/enterprise-tier-advanced.png) 

   > [!NOTE] 
   > Moduły Redis nie są jeszcze obsługiwane w warstwie dysków SSD przedsiębiorstwa. Jeśli planujesz użycie modułu Redis, pamiętaj o wybraniu pamięci podręcznej warstwy przedsiębiorstwa.
   >
   
1. Wybierz pozycję **Dalej: Tagi** i Pomiń.

1. Wybierz pozycję **Dalej: Podsumowanie**.

   ![Podsumowanie warstwy przedsiębiorstwa](media/cache-create/enterprise-tier-summary.png) 

1. Kliknij pole wyboru w obszarze **warunki**, przejrzyj ustawienia, a następnie wybierz pozycję **Przegląd + Utwórz**.
   
   Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia.

   > [!NOTE] 
   > Po utworzeniu pamięci podręcznej warstwy przedsiębiorstwa otrzymasz wiadomość e-mail z **wymaganą akcją** z witryny Azure Marketplace, aby skonfigurować *usługę Azure cache for Redis, warstwy przedsiębiorstwa*. Ta akcja nie jest wymagana. Możesz bezpiecznie zignorować tę wiadomość e-mail.
   >

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia wystąpienia warstwy przedsiębiorstwa usługi Azure cache for Redis.

> [!div class="nextstepaction"]
> [Tworzenie na platformie ASP.NET aplikacji internetowej, która korzysta z usługi Microsoft Azure Cache for Redis.](./cache-web-app-howto.md)

