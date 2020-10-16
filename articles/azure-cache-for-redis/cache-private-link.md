---
title: Usługa Azure cache for Redis z linkiem prywatnym platformy Azure (wersja zapoznawcza)
description: Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy prywatnie i bezpiecznie do usługi Azure cache for Redis z obsługą prywatnego linku platformy Azure. W tym artykule dowiesz się, jak utworzyć pamięć podręczną platformy Azure, Virtual Network platformy Azure i prywatny punkt końcowy przy użyciu Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 93a21b627acfb127c98ead465ebeadc8a472bdfd
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122708"
---
# <a name="azure-cache-for-redis-with-azure-private-link-public-preview"></a>Usługa Azure cache for Redis z linkiem prywatnym platformy Azure (publiczna wersja zapoznawcza)
W tym artykule dowiesz się, jak utworzyć sieć wirtualną i usługę Azure cache for Redis z prywatnym punktem końcowym przy użyciu Azure Portal. Dowiesz się również, jak dodać prywatny punkt końcowy do istniejącej usługi Azure cache for Redis.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy prywatnie i bezpiecznie do usługi Azure cache for Redis z obsługą prywatnego linku platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Aby używać prywatnych punktów końcowych, należy utworzyć wystąpienie usługi Azure cache for Redis po 28 lipca 2020.
> Obecnie replikacja geograficzna, reguły zapory, obsługa konsoli portalu, wiele punktów końcowych dla klastrowanej pamięci podręcznej, trwałość do zapory oraz pamięć podręczna z wstrzykiwanymi sieciami nie są obsługiwane. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Tworzenie prywatnego punktu końcowego przy użyciu nowej usługi Azure cache for Redis 

W tej sekcji utworzysz nowe wystąpienie usługi Azure cache for Redis z prywatnym punktem końcowym.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Wybierz pozycję Utwórz zasób.":::

2. Na stronie **Nowy** wybierz pozycję **Sieć** , a następnie wybierz pozycję **Sieć wirtualna**.

3. Wybierz pozycję **Dodaj** , aby utworzyć sieć wirtualną.

4. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz te informacje na karcie **podstawowe** :

   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzona ta sieć wirtualna. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona sieć wirtualna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Nazwa** | Wprowadź nazwę sieci wirtualnej. | Nazwa musi zaczynać się literą lub cyfrą, kończyć literą, cyfrą lub znakiem podkreślenia i może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. | 
   | **Region** | Lista rozwijana i wybierz region. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z sieci wirtualnej. |

5. Wybierz kartę **adresy IP** lub kliknij przycisk **Dalej: adresy IP** w dolnej części strony.

6. Na karcie **adresy IP** Określ **przestrzeń adresową IPv4** jako co najmniej jeden prefiks adresów w notacji CIDR (np. 192.168.1.0/24).

7. W obszarze **Nazwa podsieci**kliknij opcję **domyślne** , aby edytować właściwości podsieci.

8. W okienku **Edytowanie podsieci** Podaj **nazwę podsieci** oraz **zakres adresów podsieci**. Zakres adresów podsieci powinien być w notacji CIDR (np. 192.168.1.0/24). Musi ona być zawarta w przestrzeni adresowej sieci wirtualnej.

9. Wybierz pozycję **Zapisz**.

10. Wybierz kartę **Recenzja + tworzenie** lub kliknij przycisk **Recenzja + tworzenie** .

11. Sprawdź, czy wszystkie informacje są poprawne, a następnie kliknij przycisk **Utwórz** , aby zainicjować obsługę administracyjną sieci wirtualnej.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Tworzenie pamięci podręcznej platformy Azure dla wystąpienia Redis z prywatnym punktem końcowym
Aby utworzyć wystąpienie pamięci podręcznej, wykonaj następujące kroki.

1. Wróć do strony głównej Azure Portal lub Otwórz menu paska bocznego, a następnie wybierz pozycję **Utwórz zasób**. 
   
1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Wybierz pozycję Utwórz zasób.":::
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to * \<DNS name> . Redis.cache.Windows.NET*. | 
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Warstwa cenowa** | Lista rozwijana i wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |

1. Wybierz kartę **Sieć** lub kliknij przycisk **sieci** w dolnej części strony.

1. Na karcie **Sieć** wybierz pozycję **prywatny punkt końcowy** dla metody łączności.

1. Kliknij przycisk **Dodaj** , aby utworzyć prywatny punkt końcowy.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Wybierz pozycję Utwórz zasób.":::

1. Na stronie **Tworzenie prywatnego punktu końcowego** Skonfiguruj ustawienia dla prywatnego punktu końcowego, korzystając z sieci wirtualnej i podsieci utworzonej w ostatniej sekcji, a następnie wybierz **przycisk OK**. 

1. Wybierz kartę **Dalej: Zaawansowane** lub kliknij przycisk **Dalej: Zaawansowane** w dolnej części strony.

1. Na karcie **Zaawansowane** dla podstawowego lub standardowego wystąpienia pamięci podręcznej wybierz opcję Włącz przełącznik, jeśli chcesz włączyć port inny niż TLS.

1. Na karcie **Zaawansowane** wystąpienia pamięci podręcznej Premium Skonfiguruj ustawienia dla portu niezwiązanego z protokołem TLS, klastrowanie i trwałość danych.


1. Wybierz kartę **następne: Tagi** lub kliknij przycisk **Dalej: Tagi** w dolnej części strony.

1. Opcjonalnie na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz przydzielić zasób. 

1. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przekierowanie do karty Recenzja + tworzenie, w której platforma Azure weryfikuje konfigurację.

1. Po wyświetleniu komunikatu o pomyślnym sprawdzeniu poprawności, wybierz pozycję **Utwórz**.

Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd**usługi Azure cache for Redis   . Gdy **stan**   jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia. 
    
> [!IMPORTANT]
> 
> Istnieje `publicNetworkAccess` Flaga, która jest `Enabled` domyślnie. 
> Ta flaga służy do zezwalania na opcjonalne Zezwalanie na dostęp do pamięci podręcznej przez publiczny i prywatny punkt końcowy, jeśli jest ustawiony na `Enabled` . Jeśli jest ustawiona na `Disabled` , zezwala na dostęp tylko do prywatnego punktu końcowego. Możesz ustawić wartość na `Disabled` za pomocą następującego żądania patch.
> ```http
> PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
> {    "properties": {
>        "publicNetworkAccess":"Disabled"
>    }
> }
> ```
>

> [!IMPORTANT]
> 
> Aby nawiązać połączenie z klastrowaną pamięcią podręczną, należy `publicNetworkAccess` ustawić wartość `Disabled` i mieć tylko jedno połączenie prywatnego punktu końcowego. 
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Utwórz prywatny punkt końcowy z istniejącą usługą Azure cache for Redis 

W tej sekcji dodasz prywatny punkt końcowy do istniejącej usługi Azure cache for Redis. 

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej 
Aby utworzyć sieć wirtualną, wykonaj następujące kroki.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**.

2. Na stronie **Nowy** wybierz pozycję **Sieć** , a następnie wybierz pozycję **Sieć wirtualna**.

3. Wybierz pozycję **Dodaj** , aby utworzyć sieć wirtualną.

4. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz te informacje na karcie **podstawowe** :

   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzona ta sieć wirtualna. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona sieć wirtualna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Nazwa** | Wprowadź nazwę sieci wirtualnej. | Nazwa musi zaczynać się literą lub cyfrą, kończyć literą, cyfrą lub znakiem podkreślenia i może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. | 
   | **Region** | Lista rozwijana i wybierz region. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z sieci wirtualnej. |

5. Wybierz kartę **adresy IP** lub kliknij przycisk **Dalej: adresy IP** w dolnej części strony.

6. Na karcie **adresy IP** Określ **przestrzeń adresową IPv4** jako co najmniej jeden prefiks adresów w notacji CIDR (np. 192.168.1.0/24).

7. W obszarze **Nazwa podsieci**kliknij opcję **domyślne** , aby edytować właściwości podsieci.

8. W okienku **Edytowanie podsieci** Podaj **nazwę podsieci** oraz **zakres adresów podsieci**. Zakres adresów podsieci powinien być w notacji CIDR (np. 192.168.1.0/24). Musi ona być zawarta w przestrzeni adresowej sieci wirtualnej.

9. Wybierz pozycję **Zapisz**.

10. Wybierz kartę **Recenzja + tworzenie** lub kliknij przycisk **Recenzja + tworzenie** .

11. Sprawdź, czy wszystkie informacje są poprawne, a następnie kliknij przycisk **Utwórz** , aby zainicjować obsługę administracyjną sieci wirtualnej.

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego 

Aby utworzyć prywatny punkt końcowy, wykonaj następujące kroki.

1. W Azure Portal Wyszukaj **usługę Azure cache for Redis** i naciśnij klawisz ENTER lub wybierz ją z sugestii wyszukiwania.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Wybierz pozycję Utwórz zasób.":::

2. Wybierz wystąpienie pamięci podręcznej, do którego chcesz dodać prywatny punkt końcowy.

3. Po lewej stronie ekranu wybierz pozycję **(wersja zapoznawcza) prywatny punkt końcowy**.

4. Kliknij przycisk **prywatny punkt końcowy** , aby utworzyć prywatny punkt końcowy.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Wybierz pozycję Utwórz zasób.":::

5. Na **stronie Tworzenie prywatnego punktu końcowego**Skonfiguruj ustawienia dla prywatnego punktu końcowego.

   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzony prywatny punkt końcowy. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzony prywatny punkt końcowy i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Nazwa** | Wprowadź nazwę prywatnego punktu końcowego. | Nazwa musi zaczynać się literą lub cyfrą, kończyć literą, cyfrą lub znakiem podkreślenia i może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. | 
   | **Region** | Lista rozwijana i wybierz region. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystały z prywatnego punktu końcowego. |

6. Kliknij przycisk **Dalej: zasób** w dolnej części strony.

7. Na karcie **zasób** wybierz swoją subskrypcję, wybierz typ zasobu jako `Microsoft.Cache/Redis` , a następnie wybierz pamięć podręczną, do której ma zostać podłączony prywatny punkt końcowy.

8. Kliknij przycisk **Dalej: Konfiguracja** w dolnej części strony.

9. Na karcie **Konfiguracja** wybierz sieć wirtualną i podsieć utworzoną w poprzedniej sekcji.

10. Kliknij przycisk **Dalej: Tagi** w dolnej części strony.

11. Opcjonalnie na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz przydzielić zasób.

12. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przekierowanie do karty **Recenzja + tworzenie**, w   której platforma Azure weryfikuje konfigurację.

13. Po wyświetleniu komunikatu o pomyślnym **sprawdzeniu poprawności** , wybierz pozycję **Utwórz**.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o łączu prywatnym platformy Azure, zobacz [dokumentację linku prywatnego platformy Azure](https://docs.microsoft.com/azure/private-link/private-link-overview). 

