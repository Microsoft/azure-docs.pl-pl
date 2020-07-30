---
title: Usługa Azure cache for Redis z linkiem prywatnym platformy Azure (wersja zapoznawcza)
description: Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy prywatnie i bezpiecznie do usługi Azure cache for Redis z obsługą prywatnego linku platformy Azure. W tym artykule przedstawiono sposób tworzenia pamięci podręcznej platformy Azure, sieci wirtualnej platformy Azure i prywatnego punktu końcowego przy użyciu Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 5db756b60330cdac4e43e13bfe29d9397f87af50
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421658"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Usługa Azure cache for Redis z linkiem prywatnym platformy Azure (wersja zapoznawcza)
Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy prywatnie i bezpiecznie do usługi Azure cache for Redis z obsługą prywatnego linku platformy Azure. 

W tym artykule opisano sposób tworzenia pamięci podręcznej platformy Azure, sieci wirtualnej platformy Azure i prywatnego punktu końcowego przy użyciu Azure Portal.  

> [!IMPORTANT]
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure. 
> 

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

> [!NOTE]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej — [skontaktuj się z nami](mailto:azurecache@microsoft.com) , jeśli chcesz.
>

## <a name="create-a-cache"></a>Tworzenie pamięci podręcznej
1. Aby utworzyć pamięć podręczną, zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Wybierz pozycję Utwórz zasób.":::
   
1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Wybierz pozycję Pamięć podręczna platformy Azure dla Redis.":::
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to * \<DNS name> . Redis.cache.Windows.NET*. | 
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Warstwa cenowa** | Lista rozwijana i wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |
   
1. Wybierz pozycję **Utwórz**. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Utwórz pamięć podręczną platformy Azure dla Redis.":::
   
   Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Utworzono pamięć podręczną platformy Azure dla Redis.":::

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. Wybierz pozycję **Utwórz zasób**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Wybierz pozycję Utwórz zasób.":::

2. Na stronie **Nowy** wybierz pozycję **Sieć** , a następnie wybierz pozycję **Sieć wirtualna**.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Utwórz sieć wirtualną.":::

3. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Lista rozwijana i wybierz swoją subskrypcję.                                  |
    | Grupa zasobów   | Lista rozwijana i wybierz grupę zasobów. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wejść**\<virtual-network-name>**                                    |
    | Region           | Zaznaczenia**\<region-name>** |

4. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

5. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresów IPv4 | Wejść**\<IPv4-address-space>** |

6. W obszarze **Nazwa podsieci**wybierz pozycję **domyślny**wyraz.

7. W obszarze **Edytuj podsieć**wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wejść**\<subnet-name>** |
    | Zakres adresów podsieci | Wejść**\<subnet-address-range>**

8. Wybierz pozycję **Zapisz**.

9. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

10. Wybierz pozycję **Utwórz**.


## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego 

W tej sekcji utworzysz prywatny punkt końcowy i nawiążesz połączenie z utworzoną wcześniej pamięcią podręczną.

1. Wyszukaj **link prywatny** i naciśnij klawisz ENTER lub wybierz go z sugestii wyszukiwania.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Wyszukaj link prywatny.":::

2. Po lewej stronie ekranu wybierz pozycję **prywatne punkty końcowe**.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Wybierz łącze prywatne.":::

3. Wybierz przycisk **+ Dodaj** , aby utworzyć prywatny punkt końcowy. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Dodaj prywatny link.":::

4. Na **stronie Tworzenie prywatnego punktu końcowego**Skonfiguruj ustawienia dla prywatnego punktu końcowego.

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Lista rozwijana i wybierz swoją subskrypcję. |
    | Grupa zasobów | Lista rozwijana i wybierz grupę zasobów. |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa |Wprowadź nazwę prywatnego punktu końcowego.  |
    | Region |Lista rozwijana i wybierz lokalizację. |
    |||

5. Wybierz przycisk **Dalej: zasób** w dolnej części strony.

6. Na karcie **zasób** wybierz swoją subskrypcję, wybierz typ zasobu jako Microsoft. cache/Redis, a następnie wybierz pamięć podręczną utworzoną w poprzedniej sekcji.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="Zasoby dla linku prywatnego.":::

7. Wybierz przycisk **Dalej: Konfiguracja** w dolnej części strony.

8. Na karcie **Konfiguracja** wybierz sieć wirtualną i podsieć utworzoną w poprzedniej sekcji.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="Konfiguracja linku prywatnego.":::

9. Wybierz przycisk **Następny: Tagi** w dolnej części strony.

10. Na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz poklasyfikować zasób. Ten krok jest opcjonalny.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="Tagi dla linku prywatnego.":::

11. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przekierowanie do karty **Recenzja + tworzenie**, w   której platforma Azure weryfikuje konfigurację.

12. Po wyświetleniu komunikatu o **przekazaniu** zielonych weryfikacji kliknij przycisk **Utwórz**.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat linku prywatnego, zapoznaj się z [dokumentacją prywatnego linku do platformy Azure](https://docs.microsoft.com/azure/private-link/private-link-overview). 

