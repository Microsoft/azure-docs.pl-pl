---
title: Usługa Azure cache for Redis z linkiem prywatnym platformy Azure
description: Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy prywatnie i bezpiecznie do usługi Azure cache for Redis z obsługą prywatnego linku platformy Azure. W tym artykule dowiesz się, jak utworzyć pamięć podręczną platformy Azure, Virtual Network platformy Azure i prywatny punkt końcowy przy użyciu Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 3/31/2021
ms.openlocfilehash: 952f708d8f368b63f772e3af35f6fd441d65622d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121663"
---
# <a name="azure-cache-for-redis-with-azure-private-link"></a>Usługa Azure cache for Redis z linkiem prywatnym platformy Azure
W tym artykule dowiesz się, jak utworzyć sieć wirtualną i usługę Azure cache for Redis z prywatnym punktem końcowym przy użyciu Azure Portal. Dowiesz się również, jak dodać prywatny punkt końcowy do istniejącej usługi Azure cache for Redis.

Prywatny punkt końcowy platformy Azure to interfejs sieciowy, który łączy prywatnie i bezpiecznie do usługi Azure cache for Redis z obsługą prywatnego linku platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Obecnie nadmiarowość stref, obsługa konsoli portalu i trwałość do kont magazynu zapory nie są obsługiwane. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Tworzenie prywatnego punktu końcowego przy użyciu nowej usługi Azure cache for Redis 

W tej sekcji utworzysz nowe wystąpienie usługi Azure cache for Redis z prywatnym punktem końcowym.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej 

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Wybierz pozycję Utwórz zasób.":::

2. Na stronie **Nowy** wybierz pozycję **Sieć** , a następnie wybierz pozycję **Sieć wirtualna**.

3. Wybierz pozycję **Dodaj** , aby utworzyć sieć wirtualną.

4. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzona ta sieć wirtualna. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona sieć wirtualna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Nazwa** | Wprowadź nazwę sieci wirtualnej. | Nazwa musi zaczynać się literą lub cyfrą, kończyć literą, cyfrą lub znakiem podkreślenia i może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. | 
   | **Region** | Lista rozwijana i wybierz region. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z sieci wirtualnej. |

5. Wybierz kartę **adresy IP** lub kliknij przycisk **Dalej: adresy IP** w dolnej części strony.

6. Na karcie **adresy IP** Określ **przestrzeń adresową IPv4** jako co najmniej jeden prefiks adresów w notacji CIDR (np. 192.168.1.0/24).

7. W obszarze **Nazwa podsieci** kliknij opcję **domyślne** , aby edytować właściwości podsieci.

8. W okienku **Edytowanie podsieci** Podaj **nazwę podsieci** oraz **zakres adresów podsieci**. Zakres adresów podsieci powinien być w notacji CIDR (np. 192.168.1.0/24). Musi ona być zawarta w przestrzeni adresowej sieci wirtualnej.

9. Wybierz pozycję **Zapisz**.

10. Wybierz kartę **Recenzja + tworzenie** lub kliknij przycisk **Recenzja + tworzenie** .

11. Sprawdź, czy wszystkie informacje są poprawne, a następnie kliknij przycisk **Utwórz** , aby zainicjować obsługę administracyjną sieci wirtualnej.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Tworzenie pamięci podręcznej platformy Azure dla wystąpienia Redis z prywatnym punktem końcowym
Aby utworzyć wystąpienie pamięci podręcznej, wykonaj następujące kroki.

1. Wróć do strony głównej Azure Portal lub Otwórz menu paska bocznego, a następnie wybierz pozycję **Utwórz zasób**. 
   
1. Na stronie **Nowy** wybierz pozycję **bazy danych** , a następnie wybierz pozycję **Azure cache for Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Wybierz pozycję Pamięć podręczna platformy Azure dla Redis.":::
   
1. Na stronie **nowy Redis Cache** Skonfiguruj ustawienia dla nowej pamięci podręcznej.
   
   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nazwa DNS** | Podaj globalnie unikatową nazwę. | Nazwa pamięci podręcznej musi być ciągiem z przedziału od 1 do 63 znaków, który zawiera tylko cyfry, litery lub łączniki. Nazwa musi zaczynać się i kończyć cyfrą lub literą i nie może zawierać następujących po sobie łączników. *Nazwa hosta* wystąpienia pamięci podręcznej to *\<DNS name> . Redis.cache.Windows.NET*. | 
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzone nowe wystąpienie usługi Azure cache for Redis. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona pamięć podręczna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Lokalizacja** | Lista rozwijana i wybierz lokalizację. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z pamięci podręcznej. |
   | **Warstwa cenowa** | Lista rozwijana i wybierz [warstwę cenową](https://azure.microsoft.com/pricing/details/cache/). |  Warstwa cenowa decyduje o rozmiarze, wydajności i funkcjach dostępnych dla pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie pamięci podręcznej Azure Cache for Redis](cache-overview.md). |

1. Wybierz kartę **Sieć** lub kliknij przycisk **sieci** w dolnej części strony.

1. Na karcie **Sieć** wybierz pozycję **prywatny punkt końcowy** dla metody łączności.

1. Kliknij przycisk **Dodaj** , aby utworzyć prywatny punkt końcowy.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="W obszarze Sieć Dodaj prywatny punkt końcowy.":::

1. Na stronie **Tworzenie prywatnego punktu końcowego** Skonfiguruj ustawienia dla prywatnego punktu końcowego, korzystając z sieci wirtualnej i podsieci utworzonej w ostatniej sekcji, a następnie wybierz **przycisk OK**. 

1. Wybierz kartę **Dalej: Zaawansowane** lub kliknij przycisk **Dalej: Zaawansowane** w dolnej części strony.

1. Na karcie **Zaawansowane** dla podstawowego lub standardowego wystąpienia pamięci podręcznej wybierz opcję Włącz przełącznik, jeśli chcesz włączyć port inny niż TLS.

1. Na karcie **Zaawansowane** wystąpienia pamięci podręcznej Premium Skonfiguruj ustawienia dla portu niezwiązanego z protokołem TLS, klastrowanie i trwałość danych.

1. Wybierz kartę **następne: Tagi** lub kliknij przycisk **Dalej: Tagi** w dolnej części strony.

1. Opcjonalnie na karcie **Tagi** wprowadź nazwę i wartość, jeśli chcesz przydzielić zasób. 

1. Wybierz pozycję **Przejrzyj i utwórz**. Nastąpi przekierowanie do karty Recenzja + tworzenie, w której platforma Azure weryfikuje konfigurację.

1. Po wyświetleniu komunikatu o pomyślnym sprawdzeniu poprawności, wybierz pozycję **Utwórz**.

Tworzenie pamięci podręcznej zajmuje trochę czasu. Postęp można monitorować na stronie **Przegląd** usługi Azure cache for Redis. Gdy **stan** jest wyświetlany jako **uruchomiony**, pamięć podręczna jest gotowa do użycia. 
    
> [!IMPORTANT]
> 
> Istnieje `publicNetworkAccess` Flaga, która jest `Disabled` domyślnie. 
> Ta flaga służy do zezwalania na opcjonalne Zezwalanie na dostęp do pamięci podręcznej przez publiczny i prywatny punkt końcowy, jeśli jest ustawiony na `Enabled` . Jeśli jest ustawiona na `Disabled` , zezwala na dostęp tylko do prywatnego punktu końcowego. Możesz ustawić wartość na `Disabled` lub `Enabled` . Aby uzyskać więcej informacji na temat zmiany wartości, zobacz [często zadawane pytania](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
>
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Utwórz prywatny punkt końcowy z istniejącą usługą Azure cache for Redis 

W tej sekcji dodasz prywatny punkt końcowy do istniejącej usługi Azure cache for Redis. 

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej 
Aby utworzyć sieć wirtualną, wykonaj następujące kroki.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Utwórz zasób**.

2. Na stronie **Nowy** wybierz pozycję **Sieć** , a następnie wybierz pozycję **Sieć wirtualna**.

3. Wybierz pozycję **Dodaj** , aby utworzyć sieć wirtualną.

4. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

   | Ustawienie      | Sugerowana wartość  | Opis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Subskrypcja** | Lista rozwijana i wybierz swoją subskrypcję. | Subskrypcja, w ramach której ma zostać utworzona ta sieć wirtualna. | 
   | **Grupa zasobów** | Rozwiń i wybierz grupę zasobów lub wybierz pozycję **Utwórz nową** , a następnie wprowadź nową nazwę grupy zasobów. | Nazwa grupy zasobów, w której ma zostać utworzona sieć wirtualna i inne zasoby. Umieszczenie wszystkich zasobów aplikacji w jednej grupie zasobów pozwala łatwo zarządzać nimi i usuwać je razem. | 
   | **Nazwa** | Wprowadź nazwę sieci wirtualnej. | Nazwa musi zaczynać się literą lub cyfrą, kończyć literą, cyfrą lub znakiem podkreślenia i może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. | 
   | **Region** | Lista rozwijana i wybierz region. | Wybierz [region](https://azure.microsoft.com/regions/) blisko innych usług, które będą korzystać z sieci wirtualnej. |

5. Wybierz kartę **adresy IP** lub kliknij przycisk **Dalej: adresy IP** w dolnej części strony.

6. Na karcie **adresy IP** Określ **przestrzeń adresową IPv4** jako co najmniej jeden prefiks adresów w notacji CIDR (np. 192.168.1.0/24).

7. W obszarze **Nazwa podsieci** kliknij opcję **domyślne** , aby edytować właściwości podsieci.

8. W okienku **Edytowanie podsieci** Podaj **nazwę podsieci** oraz **zakres adresów podsieci**. Zakres adresów podsieci powinien być w notacji CIDR (np. 192.168.1.0/24). Musi ona być zawarta w przestrzeni adresowej sieci wirtualnej.

9. Wybierz pozycję **Zapisz**.

10. Wybierz kartę **Recenzja + tworzenie** lub kliknij przycisk **Recenzja + tworzenie** .

11. Sprawdź, czy wszystkie informacje są poprawne, a następnie kliknij przycisk **Utwórz** , aby zainicjować obsługę administracyjną sieci wirtualnej.

### <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego 

Aby utworzyć prywatny punkt końcowy, wykonaj następujące kroki.

1. W Azure Portal Wyszukaj **usługę Azure cache for Redis** i naciśnij klawisz ENTER lub wybierz ją z sugestii wyszukiwania.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Wyszukaj usługę Azure cache for Redis.":::

2. Wybierz wystąpienie pamięci podręcznej, do którego chcesz dodać prywatny punkt końcowy.

3. Po lewej stronie ekranu wybierz pozycję **prywatny punkt końcowy**.

4. Kliknij przycisk **prywatny punkt końcowy** , aby utworzyć prywatny punkt końcowy.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Dodaj prywatny punkt końcowy.":::

5. Na **stronie Tworzenie prywatnego punktu końcowego** Skonfiguruj ustawienia dla prywatnego punktu końcowego.

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

12. Wybierz pozycję **Przejrzyj i utwórz**. Nastąpi przekierowanie do karty **Recenzja + tworzenie** , w której platforma Azure weryfikuje konfigurację.

13. Po wyświetleniu komunikatu o pomyślnym **sprawdzeniu poprawności** , wybierz pozycję **Utwórz**.

> [!IMPORTANT]
> 
> Istnieje `publicNetworkAccess` Flaga, która jest `Disabled` domyślnie. 
> Ta flaga służy do zezwalania na opcjonalne Zezwalanie na dostęp do pamięci podręcznej przez publiczny i prywatny punkt końcowy, jeśli jest ustawiony na `Enabled` . Jeśli jest ustawiona na `Disabled` , zezwala na dostęp tylko do prywatnego punktu końcowego. Możesz ustawić wartość na `Disabled` lub `Enabled` . Aby uzyskać więcej informacji na temat zmiany wartości, zobacz [często zadawane pytania](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
>
>


## <a name="faq"></a>Często zadawane pytania

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Dlaczego nie mogę nawiązać połączenia z prywatnym punktem końcowym?
Jeśli pamięć podręczna jest już wstrzykiwaną pamięcią podręczną, nie można używać prywatnych punktów końcowych z wystąpieniem pamięci podręcznej. Jeśli wystąpienie pamięci podręcznej używa nieobsługiwanej funkcji (wymienionej poniżej), nie będzie można nawiązać połączenia z wystąpieniem prywatnego punktu końcowego.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Jakie funkcje nie są obsługiwane przez prywatne punkty końcowe?
Obecnie nadmiarowość stref, obsługa konsoli portalu i trwałość do kont magazynu zapory nie są obsługiwane. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Jak mogę zmienić prywatny punkt końcowy, który ma być wyłączony lub włączony z dostępu do sieci publicznej?
Istnieje `publicNetworkAccess` Flaga, która jest `Disabled` domyślnie. Ta flaga służy do zezwalania na opcjonalne Zezwalanie na dostęp do pamięci podręcznej przez publiczny i prywatny punkt końcowy, jeśli jest ustawiony na `Enabled` . Jeśli jest ustawiona na `Disabled` , zezwala na dostęp tylko do prywatnego punktu końcowego. Można ustawić wartość na `Disabled` lub `Enabled` w Azure Portal lub przy użyciu żądania patch API RESTful. 

Aby zmienić wartość w Azure Portal, wykonaj następujące kroki.

1. W Azure Portal Wyszukaj **usługę Azure cache for Redis** i naciśnij klawisz ENTER lub wybierz ją z sugestii wyszukiwania.

2. Wybierz wystąpienie pamięci podręcznej, dla którego chcesz zmienić wartość dostępu do sieci publicznej.

3. Po lewej stronie ekranu wybierz pozycję **prywatny punkt końcowy**.

4. Kliknij przycisk **Włącz dostęp do sieci publicznej** .

Aby zmienić wartość za pomocą żądania PATCH API RESTful, zobacz poniżej i Edytuj wartość w celu odzwierciedlenia flagi dla pamięci podręcznej.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="how-can-i-have-multiple-endpoints-in-different-virtual-networks"></a>Jak mogę korzystać z wielu punktów końcowych w różnych sieciach wirtualnych?
Aby można było korzystać z wielu prywatnych punktów końcowych w różnych sieciach wirtualnych, należy ręcznie skonfigurować prywatną strefę DNS do wielu sieci wirtualnych _przed_ utworzeniem prywatnego punktu końcowego. Aby uzyskać więcej informacji, zobacz [Konfiguracja usługi DNS prywatnego punktu końcowego platformy Azure](../private-link/private-endpoint-dns.md). 

### <a name="what-happens-if-i-delete-all-the-private-endpoints-on-my-cache"></a>Co się stanie w przypadku usunięcia wszystkich prywatnych punktów końcowych w mojej pamięci podręcznej?
Po usunięciu prywatnych punktów końcowych w pamięci podręcznej wystąpienie pamięci podręcznej może stać się nieosiągalne, dopóki nie zostanie jawnie włączony publiczny dostęp do sieci lub zostanie dodany inny prywatny punkt końcowy. Flagę można zmienić `publicNetworkAccess` na Azure Portal lub za pomocą żądania patch API RESTful. Aby uzyskać więcej informacji na temat zmiany wartości, zobacz [często zadawane pytania](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Czy sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń) są włączone dla prywatnych punktów końcowych?
Nie, są one wyłączone dla prywatnych punktów końcowych. W podsieciach zawierających prywatny punkt końcowy może być skojarzonych sieciowej grupy zabezpieczeń, więc reguły nie będą obowiązywać w przypadku ruchu przetwarzanego przez prywatny punkt końcowy. Aby wdrażać prywatne punkty końcowe w podsieci, należy [wyłączyć wymuszanie zasad sieciowych](../private-link/disable-private-endpoint-network-policy.md) . SIECIOWEJ grupy zabezpieczeń jest nadal wymuszane dla innych obciążeń hostowanych w tej samej podsieci. Trasy w dowolnej podsieci klienta będą używać prefiksu/32, zmiana domyślnego zachowania routingu wymaga podobnego UDR. 

Kontroluj ruch przy użyciu reguł sieciowej grupy zabezpieczeń dla ruchu wychodzącego na klientach źródłowych. Wdróż pojedyncze trasy z prefiksem/32, aby przesłonić prywatne trasy punktów końcowych. Dzienniki przepływu sieciowej grupy zabezpieczeń i informacje monitorowania dla połączeń wychodzących są nadal obsługiwane i mogą być używane

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Ponieważ moje prywatne wystąpienie punktu końcowego nie znajduje się w mojej sieci wirtualnej, jak jest ono skojarzone z moją siecią wirtualną?
Jest on połączony tylko z siecią wirtualną. Ponieważ nie znajduje się w sieci wirtualnej, nie trzeba modyfikować reguł sieciowej grupy zabezpieczeń dla zależnych punktów końcowych.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>Jak przeprowadzić migrację z wstrzykniętej pamięci podręcznej do prywatnej pamięci podręcznej punktu końcowego?
Należy usunąć utworzoną pamięć podręczną i utworzyć nowe wystąpienie pamięci podręcznej z prywatnym punktem końcowym. Aby uzyskać więcej informacji, zobacz [Migrowanie do usługi Azure cache for Redis](cache-migration-guide.md)

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o łączu prywatnym platformy Azure, zobacz [dokumentację linku prywatnego platformy Azure](../private-link/private-link-overview.md).
* Aby porównać różne opcje izolacji sieci dla wystąpienia pamięci podręcznej, zobacz [dokumentację opcji izolacji sieci w usłudze Azure cache for Redis](cache-network-isolation.md).
