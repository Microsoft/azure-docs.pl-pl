---
title: Używanie zapory platformy Azure do sprawdzania ruchu kierowanego do prywatnego punktu końcowego
titleSuffix: Azure Private Link
description: Dowiedz się, jak kontrolować ruch kierowany do prywatnego punktu końcowego za pomocą zapory platformy Azure.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 3ed349616ae6456913c19bb073f6e9ea28e7d549
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575119"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Używanie zapory platformy Azure do sprawdzania ruchu kierowanego do prywatnego punktu końcowego

Prywatny punkt końcowy platformy Azure to podstawowy blok konstrukcyjny dla prywatnego linku platformy Azure. Prywatne punkty końcowe umożliwiają zasobom platformy Azure wdrożonym w sieci wirtualnej komunikowanie się z prywatnymi zasobami linków.

Prywatne punkty końcowe umożliwiają dostęp do zasobów do usługi link prywatny wdrożonej w sieci wirtualnej. Dostęp do prywatnego punktu końcowego za pośrednictwem komunikacji równorzędnej sieci wirtualnej i lokalnych połączeń sieciowych zwiększają łączność.

Może być konieczne sprawdzenie lub zablokowanie ruchu od klientów do usług udostępnianych za pośrednictwem prywatnych punktów końcowych. Wykonaj tę inspekcję, używając [zapory platformy Azure](../firewall/overview.md) lub zewnętrznego urządzenia sieciowego.

Obowiązują następujące ograniczenia:

* Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) nie mają zastosowania do prywatnych punktów końcowych
* Trasy zdefiniowane przez użytkownika (UDR) nie mają zastosowania do prywatnych punktów końcowych
* Pojedyncza tabela tras może być dołączona do podsieci
* Tabela tras obsługuje do 400 tras

Zapora platformy Azure filtruje ruch przy użyciu:

* [Nazwa FQDN w regułach sieciowych](../firewall/fqdn-filtering-network-rules.md) dla protokołów TCP i UDP
* [Nazwa FQDN w regułach aplikacji](../firewall/features.md#application-fqdn-filtering-rules) dla protokołów HTTP, https i MSSQL. 

Większość usług narażonych na prywatne punkty końcowe korzysta z protokołu HTTPS. Użycie reguł aplikacji względem reguł sieci jest zalecane w przypadku korzystania z usługi Azure SQL.

> [!NOTE]
> Filtrowanie nazwy FQDN SQL jest obsługiwane tylko w [trybie proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). Tryb **serwera proxy** może powodować większe opóźnienia w porównaniu do *przekierowania*. Jeśli chcesz kontynuować korzystanie z trybu przekierowania, który jest domyślnym ustawieniem dla klientów łączących się na platformie Azure, możesz filtrować dostęp przy użyciu nazwy FQDN w regułach sieci zapory.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Scenariusz 1: architektura Hub i szprych — dedykowana Sieć wirtualna dla prywatnych punktów końcowych

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Dedykowane Virtual Network dla prywatnych punktów końcowych" border="true":::

Ten scenariusz to najbardziej rozszerzalna architektura umożliwiająca nawiązywanie połączeń prywatnych z wieloma usługami platformy Azure przy użyciu prywatnych punktów końcowych. Zostanie utworzona trasa wskazująca na przestrzeń adresową sieci, w której są wdrożone prywatne punkty końcowe. Ta konfiguracja zmniejsza obciążenie administracyjne i uniemożliwia działanie w limicie 400 tras.

Połączenia z sieci wirtualnej klienta z zaporą platformy Azure w sieci wirtualnej centrum będą powodować naliczanie opłat, gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej.

Aby uzyskać więcej informacji na temat opłat związanych z połączeniami z równorzędnymi sieciami wirtualnymi, zapoznaj się z sekcją często zadawanych pytań na stronie [Cennik](https://azure.microsoft.com/pricing/details/private-link/) .

>[!NOTE]
> Ten scenariusz można zaimplementować przy użyciu dowolnych reguł sieci urządzenie WUS lub zapory platformy Azure zamiast reguł aplikacji.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Scenariusz 2: architektura Hub i szprych — udostępniona Sieć wirtualna dla prywatnych punktów końcowych i maszyn wirtualnych

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Prywatne punkty końcowe i Virtual Machines w tym samym Virtual Network" border="true":::

Ten scenariusz jest implementowany, gdy:

* Nie jest możliwe posiadanie dedykowanej sieci wirtualnej dla prywatnych punktów końcowych

* Gdy w sieci wirtualnej są widoczne tylko niektóre usługi korzystające z prywatnych punktów końcowych

Maszyny wirtualne będą zawierać trasy systemu/32 wskazujące każdy prywatny punkt końcowy. Jedna trasa na prywatny punkt końcowy jest skonfigurowana do kierowania ruchem przez zaporę platformy Azure. 

Koszty administracyjne związane z utrzymywaniem tabeli tras rosną w miarę jak usługi są ujawniane w sieci wirtualnej. Możliwość osiągnięcia limitu trasy również wzrasta.

W zależności od ogólnej architektury można korzystać z limitu 400 tras. Zaleca się używanie scenariusza 1 wszędzie tam, gdzie to możliwe.

Połączenia z sieci wirtualnej klienta z zaporą platformy Azure w sieci wirtualnej centrum będą powodować naliczanie opłat, gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej.

Aby uzyskać więcej informacji na temat opłat związanych z połączeniami z równorzędnymi sieciami wirtualnymi, zapoznaj się z sekcją często zadawanych pytań na stronie [Cennik](https://azure.microsoft.com/pricing/details/private-link/) .

>[!NOTE]
> Ten scenariusz można zaimplementować przy użyciu dowolnych reguł sieci urządzenie WUS lub zapory platformy Azure zamiast reguł aplikacji.

## <a name="scenario-3-single-virtual-network"></a>Scenariusz 3: pojedyncza Sieć wirtualna

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Pojedyncza Sieć wirtualna" border="true":::

Istnieją pewne ograniczenia dotyczące implementacji: migracja do architektury gwiazdy i szprych nie jest możliwa. Zastosowanie mają takie same zagadnienia jak w przypadku scenariusza 2. W tym scenariuszu opłaty za komunikację równorzędną sieci wirtualnych nie mają zastosowania.

>[!NOTE]
> Jeśli chcesz zaimplementować ten scenariusz przy użyciu urządzenie WUS innej firmy lub zapory platformy Azure, reguły sieciowe zamiast reguł aplikacji są wymagane do ruchu w celu kierowania do prywatnych punktów końcowych. Komunikacja między maszynami wirtualnymi i prywatnymi punktami końcowymi zakończy się niepowodzeniem.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Scenariusz 4: ruch lokalny do prywatnych punktów końcowych

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Ruch lokalny do prywatnych punktów końcowych" border="true":::

Tę architekturę można zaimplementować, jeśli skonfigurowano łączność z siecią lokalną przy użyciu: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [Sieć VPN między lokacjami](../vpn-gateway/tutorial-site-to-site-portal.md) 

Jeśli wymagania dotyczące zabezpieczeń wymagają ruchu klienckiego do usług ujawnianych za pośrednictwem prywatnych punktów końcowych, należy wdrożyć ten scenariusz.

Należy zastosować takie same kwestie jak w scenariuszu 2 powyżej. W tym scenariuszu nie są naliczane opłaty za komunikację równorzędną sieci wirtualnej. Aby uzyskać więcej informacji o sposobie konfigurowania serwerów DNS w celu zezwalania lokalnym obciążeniom na dostęp do prywatnych punktów końcowych, zobacz [obciążenia lokalne przy użyciu usługi przesyłania dalej DNS](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

>[!NOTE]
> Jeśli chcesz zaimplementować ten scenariusz przy użyciu urządzenie WUS innej firmy lub zapory platformy Azure, reguły sieciowe zamiast reguł aplikacji są wymagane do ruchu w celu kierowania do prywatnych punktów końcowych. Komunikacja między maszynami wirtualnymi i prywatnymi punktami końcowymi zakończy się niepowodzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure.
* Obszar roboczy usługi Log Analytics.  

Zobacz temat [Tworzenie obszaru roboczego log Analytics w Azure Portal](../azure-monitor/logs/quick-create-workspace.md) , aby utworzyć obszar roboczy, jeśli nie znajduje się on w subskrypcji.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania maszyny wirtualnej używanej do uzyskiwania dostępu do prywatnego zasobu linku. Baza danych SQL Azure jest używana jako Przykładowa usługa.

## <a name="virtual-networks-and-parameters"></a>Sieci wirtualne i parametry

Utwórz trzy sieci wirtualne i odpowiadające im podsieci, aby:

* Zawiera zaporę platformy Azure używaną do ograniczania komunikacji między maszyną wirtualną i prywatnym punktem końcowym.
* Hostowanie maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku.
* Hostowanie prywatnego punktu końcowego.

Zastąp następujące parametry w krokach poniższymi informacjami:

### <a name="azure-firewall-network"></a>Sieć zapory platformy Azure
| Parametr                   | Wartość                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | South Central US      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Sieć maszyn wirtualnych
| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | South Central US      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | Podsieci      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Sieć prywatna punktu końcowego
| Parametr                   | Wartość                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | South Central US      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet    |        |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Powtórz kroki od 1 do 9, aby utworzyć sieci wirtualne do hostowania maszyn wirtualnych i prywatnych zasobów punktów końcowych.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**.

2. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Ta grupa zasobów została utworzona w poprzedniej sekcji.  |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM**. |
    | Region (Region) | Wybierz pozycję **(USA) Południowo-środkowe stany USA**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Obraz | Wybierz pozycję **Ubuntu Server 18,04 LTS-Gen1**. |
    | Rozmiar | Wybierz **Standard_B2s**. |
    | **Konto administratora** |  |
    | Typ uwierzytelniania | Wybierz pozycję **hasło**. |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **Reguły portów ruchu przychodzącego** |  |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
    |||

3. Wybierz pozycję **Dalej: dyski**.

4. W obszarze **Tworzenie maszyny wirtualnej — dyski** pozostaw wartości domyślne i wybierz przycisk **Dalej: Sieć**.

5. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Wybierz pozycję **myVMVNet**.  |
    | Podsieć | Wybierz pozycję **VMSubnet (10.1.0.0/24)**.|
    | Publiczny adres IP | Pozostaw wartość domyślną **(New) myVm-IP**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycję **SSH**.|
    ||

6. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

7. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

## <a name="deploy-the-firewall"></a>Wdrażanie zapory

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania wpisz **zaporę** i naciśnij klawisz **Enter**.

3. Wybierz opcję **Zapora** , a następnie wybierz pozycję **Utwórz**.

4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**.  |
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź **myAzureFirewall**. |
    | Region (Region) | Wybierz pozycję **Południowo-środkowe stany USA**. |
    | Strefa dostępności | Pozostaw wartość domyślną **Brak**. |
    | Wybieranie sieci wirtualnej    |    Wybierz pozycję **Użyj istniejącej**.    |
    | Sieć wirtualna    |    Wybierz pozycję **myAzFwVNet**.    |
    | Publiczny adres IP    |    Wybierz pozycję **Dodaj nowe** i w polu Nazwa wprowadź nazwę moja **Zapora-IP**.    |
    | Wymuszone tunelowanie    | Pozostaw ustawienie domyślne **wyłączone**.    |
    |||
5. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

6. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

## <a name="enable-firewall-logs"></a>Włącz Dzienniki zapory

W tej sekcji można włączyć Dzienniki zapory.

1. W Azure Portal wybierz pozycję **wszystkie zasoby** w menu po lewej stronie.

2. Wybierz **myAzureFirewall** zapory na liście zasobów.

3. W obszarze **monitorowanie** w ustawieniach zapory wybierz pozycję **Ustawienia diagnostyczne** .

4. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne** w ustawieniach diagnostycznych.

5. W polu **Ustawienia diagnostyczne** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa ustawienia diagnostycznego | Wprowadź **myDiagSetting**. |
    | Szczegóły kategorii | |
    | Dziennik | Wybierz pozycję **AzureFirewallApplicationRule** i **AzureFirewallNetworkRule**. |
    | Szczegóły miejsca docelowego | Wybierz pozycję **Wyślij do log Analytics**. |
    | Subskrypcja | Wybierz subskrypcję. |
    | Obszar roboczy usługi Log Analytics | Wybierz obszar roboczy Log Analytics. |

6. Wybierz pozycję **Zapisz**.

## <a name="create-azure-sql-database"></a>Tworzenie bazy danych Azure SQL Database

W tej sekcji utworzysz prywatny SQL Database.

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz**  >  **bazę danych** zasobów  >  **SQL Database**.

2. W obszarze **tworzenie SQL Database — podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Ta grupa zasobów została utworzona w poprzedniej sekcji.|
    | **Szczegóły bazy danych** |  |
    | Nazwa bazy danych  | Wprowadź **bazę danych**.  |
    | Serwer | Wybierz pozycję **Utwórz nową** , a następnie wprowadź poniższe informacje.    |
    | Nazwa serwera | Wprowadź **MójSerwerBD**. Jeśli ta nazwa jest wykonywana, wprowadź unikatową nazwę.   |
    | Identyfikator logowania administratora serwera | Wprowadź wybraną nazwę. |
    | Hasło    |    Wprowadź wybrane hasło.    |
    | Potwierdź hasło | Ponownie wprowadź hasło    |
    | Lokalizacja    | Wybierz pozycję **(USA) Południowo-środkowe stany USA**.    |
    | Chcesz użyć elastycznej puli SQL    | Pozostaw wartość domyślną **nie**. |
    | Obliczenia i magazyn | Pozostaw wartość domyślną **ogólnego przeznaczenia 5 rdzeń, 2 rdzeni wirtualnych, 32 GB magazynu**. |
    |||

3. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

4. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

## <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

W tej sekcji utworzysz prywatny punkt końcowy dla bazy danych Azure SQL Database w poprzedniej sekcji.

1. W Azure Portal wybierz pozycję **wszystkie zasoby** w menu po lewej stronie.

2. Wybierz pozycję Azure SQL Server **MójSerwerBD** na liście usług.  Jeśli użyto innej nazwy serwera, wybierz tę nazwę.

3. W obszarze Ustawienia serwera wybierz pozycję **połączenia prywatnego punktu końcowego** w obszarze **zabezpieczenia**.

4. Wybierz pozycję **+ prywatny punkt końcowy**.

5. W obszarze **Utwórz prywatny punkt końcowy** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. |
    | **Szczegóły wystąpienia** | |
    | Nazwa | Wprowadź **SQLPrivateEndpoint**. |
    | Region (Region) | Wybierz pozycję **(USA) Południowo-środkowe stany USA.** |

6. Wybierz kartę **zasób** lub wybierz pozycję **Dalej: zasób** w dolnej części strony.

7. Na karcie **zasób** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Metoda połączenia | Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**. |
    | Subskrypcja | Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. SQL/Server**. |
    | Zasób | Wybierz **MójSerwerBD** lub nazwę serwera, który został utworzony w poprzednim kroku.
    | Docelowy zasób podrzędny | Wybierz pozycję **sqlServer**. |

8. Wybierz kartę **Konfiguracja** lub wybierz pozycję **Dalej: Konfiguracja** w dolnej części strony.

9. Na karcie **Konfiguracja** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Sieć** | |
    | Sieć wirtualna | Wybierz pozycję **myPEVnet**. |
    | Podsieć | Wybierz pozycję **PrivateEndpointSubnet**. |
    | **Integracja Prywatna strefa DNS** | |
    | Integruj z prywatną strefą DNS | Wybierz pozycję **Tak**. |
    | Subskrypcja | Wybierz subskrypcję. |
    | Prywatne strefy DNS | Pozostaw wartość domyślną **privatelink.Database.Windows.NET**. |

10. Wybierz kartę **Recenzja + tworzenie** lub wybierz pozycję **Przegląd + Utwórz** w dolnej części strony.

11. Wybierz przycisk **Utwórz**.

12. Po utworzeniu punktu końcowego wybierz pozycję **zapory i sieci wirtualne** w obszarze **zabezpieczenia**.

13. W obszarze **zapory i sieci wirtualne** wybierz pozycję **tak** dalej, aby **zezwolić usługom i zasobom platformy Azure na dostęp do tego serwera**.

14. Wybierz pozycję **Zapisz**.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Łączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej

W tej sekcji połączymy sieci wirtualne **myVMVNet** i **MyPEVNet** z usługą **myAzFwVNet** przy użyciu komunikacji równorzędnej. Nie ma bezpośredniej łączności między **myVMVNet** i **myPEVNet**.

1. Na pasku wyszukiwania portalu wprowadź **myAzFwVNet**.

2. Wybierz pozycję **Komunikacja równorzędna** w obszarze menu **Ustawienia** , a następnie wybierz pozycję **+ Dodaj**.

3. W obszarze **Dodaj komunikację równorzędną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa komunikacji równorzędnej z myAzFwVNet do zdalnej sieci wirtualnej | Wprowadź **myAzFwVNet-to-myVMVNet**. |
    | **Szczegóły elementu równorzędnego** |  |
    | Model wdrażania sieci wirtualnej  | Pozostaw domyślne **Menedżer zasobów**.  |
    | Wiemy o IDENTYFIKATORze zasobu | Pozostaw niezaznaczone.    |
    | Subskrypcja | Wybierz subskrypcję.    |
    | Sieć wirtualna | Wybierz pozycję **myVMVNet**. |
    | Nazwa komunikacji równorzędnej ze zdalnej sieci wirtualnej do myAzFwVNet    |    Wprowadź **myVMVNet-to-myAzFwVNet**.    |
    | **Konfiguracja** | |
    | **Konfigurowanie ustawień dostępu do sieci wirtualnej** | |
    | Zezwalaj na dostęp do sieci wirtualnej z myAzFwVNet do zdalnej sieci wirtualnej | Pozostaw wartość domyślną **Włączone**.    |
    | Zezwalaj na dostęp do sieci wirtualnej ze zdalnej sieci wirtualnej do myAzFwVNet    | Pozostaw wartość domyślną **Włączone**.    |
    | **Konfigurowanie ustawień przekazywania ruchu** | |
    | Zezwalaj na ruch przesłany dalej ze zdalnej sieci wirtualnej do myAzFwVNet    | Wybierz pozycję **Włączone**. |
    | Zezwalaj na ruch przesłany z myAzFwVNet do zdalnej sieci wirtualnej | Wybierz pozycję **Włączone**. |
    | **Konfigurowanie ustawień tranzytu bramy** | |
    | Zezwalaj na tranzyt bramy | Pozostaw niezaznaczone |
    |||

4. Wybierz przycisk **OK**.

5. Wybierz pozycję **+ Dodaj**.

6. W obszarze **Dodaj komunikację równorzędną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa komunikacji równorzędnej z myAzFwVNet do zdalnej sieci wirtualnej | Wprowadź **myAzFwVNet-to-myPEVNet**. |
    | **Szczegóły elementu równorzędnego** |  |
    | Model wdrażania sieci wirtualnej  | Pozostaw domyślne **Menedżer zasobów**.  |
    | Wiemy o IDENTYFIKATORze zasobu | Pozostaw niezaznaczone.    |
    | Subskrypcja | Wybierz subskrypcję.    |
    | Sieć wirtualna | Wybierz pozycję **myPEVNet**. |
    | Nazwa komunikacji równorzędnej ze zdalnej sieci wirtualnej do myAzFwVNet    |    Wprowadź **myPEVNet-to-myAzFwVNet**.    |
    | **Konfiguracja** | |
    | **Konfigurowanie ustawień dostępu do sieci wirtualnej** | |
    | Zezwalaj na dostęp do sieci wirtualnej z myAzFwVNet do zdalnej sieci wirtualnej | Pozostaw wartość domyślną **Włączone**.    |
    | Zezwalaj na dostęp do sieci wirtualnej ze zdalnej sieci wirtualnej do myAzFwVNet    | Pozostaw wartość domyślną **Włączone**.    |
    | **Konfigurowanie ustawień przekazywania ruchu** | |
    | Zezwalaj na ruch przesłany dalej ze zdalnej sieci wirtualnej do myAzFwVNet    | Wybierz pozycję **Włączone**. |
    | Zezwalaj na ruch przesłany z myAzFwVNet do zdalnej sieci wirtualnej | Wybierz pozycję **Włączone**. |
    | **Konfigurowanie ustawień tranzytu bramy** | |
    | Zezwalaj na tranzyt bramy | Pozostaw niezaznaczone |

7. Wybierz przycisk **OK**.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Łączenie sieci wirtualnych z prywatną strefą DNS

W tej sekcji połączymy sieci wirtualne **myVMVNet** i **myAzFwVNet** z prywatną strefą DNS **privatelink.Database.Windows.NET** . Ta strefa została utworzona podczas tworzenia prywatnego punktu końcowego. 

Łącze jest wymagane dla maszyny wirtualnej i zapory, aby można było rozpoznać nazwę FQDN bazy danych jako adres prywatnego punktu końcowego. **MyPEVNet** sieci wirtualnej został automatycznie połączony po utworzeniu prywatnego punktu końcowego.

>[!NOTE]
>Jeśli nie dołączysz maszyny wirtualnej i sieci wirtualnych zapory do prywatnej strefy DNS, maszyna wirtualna i Zapora nadal będą mogli rozpoznać SQL Server FQDN. Zostaną one rozpoznane jako publiczny adres IP.

1. Na pasku wyszukiwania portalu wprowadź **privatelink. Database**.

2. W wynikach wyszukiwania wybierz pozycję **privatelink.Database.Windows.NET** .

3. W obszarze **Ustawienia** wybierz pozycję **linki sieci wirtualnej** .

4. Wybierz pozycję **+ Dodaj**

5. W obszarze **Dodawanie linku do sieci wirtualnej** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa łącza | Wprowadź **link do myVMVNet**. |
    | **Szczegóły sieci wirtualnej** |  |
    | Wiemy identyfikator zasobu sieci wirtualnej  | Pozostaw niezaznaczone.  |
    | Subskrypcja | Wybierz subskrypcję.    |
    | Sieć wirtualna | Wybierz pozycję **myVMVNet**. |
    | **SKONFIGUROWAĆ** | |
    | Włącz rejestrację autorejestrowania | Pozostaw niezaznaczone.    |


6. Wybierz przycisk **OK**.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Konfigurowanie reguły aplikacji przy użyciu nazwy FQDN programu SQL w zaporze platformy Azure

W tej sekcji Skonfiguruj regułę aplikacji, aby umożliwić komunikację między **myVM** i prywatnym punktem końcowym SQL Server **mydbserver.Database.Windows.NET**. 

Ta reguła umożliwia komunikację przez zaporę utworzoną w poprzednich krokach.

1. Na pasku wyszukiwania portalu wprowadź **myAzureFirewall**.

2. W wynikach wyszukiwania wybierz pozycję **myAzureFirewall** .

3. Wybierz pozycję **reguły** w obszarze **Ustawienia** w przeglądzie **myAzureFirewall** .

4. Wybierz kartę **Kolekcja reguł aplikacji** .

5. Wybierz pozycję **+ Dodaj kolekcję reguł aplikacji**.

6. W obszarze **Dodaj kolekcję reguł aplikacji** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **SQLPrivateEndpoint**. |
    | Priorytet | Wprowadź wartość **100**. |
    | Akcja | Wprowadź **Zezwalaj**. |
    | **Reguły** |  |
    | **Tagi w pełni kwalifikowanych nazw domen** | |
    | Nazwa  | Pozostaw to pole puste.  |
    | Typ źródła | Pozostaw domyślny **adres IP**.    |
    | Element źródłowy | Pozostaw to pole puste. |
    | Tagi w pełni kwalifikowanych nazw domen | Pozostaw **wybraną** wartość domyślną 0. |
    | **Docelowe nazwy FQDN** | |
    | Nazwa | Wprowadź **SQLPrivateEndpoint**.    |
    | Typ źródła | Pozostaw domyślny **adres IP**. |
    | Element źródłowy | Wprowadź adres **10.1.0.0/16**. |
    | Protokół: Port | Wprowadź **MSSQL: 1433**. |
    | Docelowe nazwy FQDN | Wprowadź **mydbserver.Database.Windows.NET**. |
    |||

7. Wybierz pozycję **Dodaj**.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Kierowanie ruchu między maszyną wirtualną i prywatnym punktem końcowym za pomocą zapory platformy Azure

Nie utworzono komunikacji równorzędnej sieci wirtualnej bezpośrednio między sieciami wirtualnymi **myVMVNet** i **myPEVNet**. **MyVM** maszyny wirtualnej nie ma trasy do prywatnego punktu końcowego, który został utworzony. 

W tej sekcji utworzymy tabelę tras z trasą niestandardową. 

Trasa wysyła ruch z podsieci **myVM** do przestrzeni adresowej sieci wirtualnej **myPEVNet** za pośrednictwem zapory platformy Azure.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania wpisz **tabelę tras** i naciśnij klawisz **Enter**.

3. Wybierz pozycję **tabela tras** , a następnie wybierz pozycję **Utwórz**.

4. Na stronie **Tworzenie tabeli tras** Skorzystaj z poniższej tabeli, aby skonfigurować tabelę tras:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**.  |
    | **Szczegóły wystąpienia** |  |
    | Region (Region) | Wybierz pozycję **Południowo-środkowe stany USA**. |
    | Nazwa | Wprowadź **podsieć VMsubnet do AzureFirewall**. |
    | Propaguj trasy bramy | Wybierz pozycję **Nie**. |

5. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

6. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

7. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**.

8. Wybierz pozycję **trasy** w obszarze **Ustawienia**.

9. Wybierz pozycję **+ Dodaj**.

10. Na stronie **Dodawanie trasy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa trasy | Wprowadź **myVMsubnet-to-privateendpoint**. |
    | Prefiks adresu | Wprowadź **10.2.0.0/16**.  |
    | Typ następnego przeskoku | Wybierz pozycję **Urządzenie wirtualne**. |
    | Adres następnego skoku | Wprowadź **10.0.0.4**. |

11. Wybierz przycisk **OK**.

12. W obszarze **Ustawienia** wybierz pozycję **podsieci** .

13. Wybierz pozycję **+ Skojarz**.

14. Na stronie **Skojarz podsieć** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Wybierz pozycję **myVMVNet**. |
    | Podsieć | Wybierz pozycję **podsieć VMSubnet**.  |

15. Wybierz przycisk **OK**.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Nawiązywanie połączenia z maszyną wirtualną z komputera klienckiego

Połącz się z maszyną wirtualną **myVm** z Internetu w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź wartość **myVm-IP**.

2. W wynikach wyszukiwania wybierz pozycję **myVM-IP** .

3. Skopiuj lub Zapisz wartość w obszarze **adres IP**.

4. Jeśli używasz systemu Windows 10, uruchom następujące polecenie przy użyciu programu PowerShell. W przypadku innych wersji klienta systemu Windows Użyj klienta SSH, [takiego jak](https://www.putty.org/)wyglądająco:

* Zastąp **nazwę username** nazwą użytkownika wprowadzoną podczas tworzenia maszyny wirtualnej.

* Zastąp element **IPAddress** adresem IP z poprzedniego kroku.

    ```bash
    ssh username@IPaddress
    ```

5. Wprowadź hasło zdefiniowane podczas tworzenia **myVm**

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Dostęp SQL Server prywatnie z poziomu maszyny wirtualnej

W tej sekcji połączysz się prywatnie z SQL Database przy użyciu prywatnego punktu końcowego.

1. Wprowadź wartość `nslookup mydbserver.database.windows.net`
    
    Zostanie wyświetlony komunikat podobny do poniższego:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Zainstaluj [SQL Server narzędzia wiersza polecenia](/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15#tools).

3. Uruchom następujące polecenie, aby połączyć się z SQL Server. Użyj administratora serwera i hasła zdefiniowanego podczas tworzenia SQL Server w poprzednich krokach.

* Zastąp **\<ServerAdmin>** wartość nazwą użytkownika administratora wprowadzoną podczas tworzenia programu SQL Server.

* Zamień na **\<YourPassword>** hasło administratora wprowadzone podczas tworzenia programu SQL Server.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. W przypadku pomyślnego logowania zostanie wyświetlony wiersz polecenia SQL. Wprowadź **Exit** , aby zamknąć narzędzie **sqlcmd** .

5. Zamknij połączenie z usługą **myVM** , wprowadzając **wyjście**.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Sprawdzanie poprawności ruchu w dziennikach zapory platformy Azure

1. W Azure Portal wybierz pozycję **wszystkie zasoby** , a następnie wybierz obszar roboczy log Analytics.

2. Wybierz pozycję **dzienniki** w obszarze **Ogólne** na stronie obszaru roboczego log Analytics.

3. Wybierz przycisk " **Rozpocznij** niebiesko".

4. W oknie **przykładowe zapytania** wybierz pozycję **zapory** w obszarze **wszystkie zapytania**.

5. Wybierz przycisk **Uruchom** w obszarze **dane dziennika reguły aplikacji**.

6. W danych wyjściowych zapytania dziennika Sprawdź, czy **mydbserver.Database.Windows.NET** jest wymieniony w obszarze **nazwy FQDN** , a **SQLPrivateEndpoint** jest wymieniony w obszarze **RuleCollection**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy skończysz korzystać z zasobów, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:

1. Wprowadź w polu **wyszukiwania** w górnej części portalu **i wybierz pozycję** **moja zasobów z** wyników wyszukiwania.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę **myResourceGroup**, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono różne scenariusze, których można użyć w celu ograniczenia ruchu między maszyną wirtualną a prywatnym punktem końcowym przy użyciu zapory platformy Azure. 

Nawiązano połączenie z maszyną wirtualną i bezpieczną komunikację z bazą danych za pośrednictwem zapory platformy Azure przy użyciu linku prywatnego.

Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure?](private-endpoint-overview.md).