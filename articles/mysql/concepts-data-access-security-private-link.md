---
title: Prywatny link — Azure Database for MySQL
description: Dowiedz się, jak działa łącze prywatne dla Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: e239b6b00c5a5e993834a10fca30de02b9f715ff
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065518"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Usługa Private Link dla usługi Azure Database for MySQL

Usługa Private Link umożliwia łączenie z różnymi usługami PaaS na platformie Azure za pośrednictwem prywatnego punktu końcowego. Usługa Azure Private Link zasadniczo łączy usługi platformy Azure z Twoją prywatną siecią wirtualną. Dostęp do zasobów PaaS można uzyskać przy użyciu prywatnego adresu IP, podobnie jak w przypadku każdego innego zasobu w sieci wirtualnej.

Aby uzyskać listę PaaS usług, które obsługują funkcję linku prywatnego, zapoznaj się z [dokumentacją](../private-link/index.yml)linku prywatnego. Prywatny punkt końcowy to prywatny adres IP w obrębie określonej sieci [wirtualnej](../virtual-network/virtual-networks-overview.md) i podsieci.

> [!NOTE]
> Funkcja Link prywatny jest dostępna tylko dla serwerów Azure Database for MySQL w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer bazy danych znajduje się w jednej z tych warstw cenowych.

## <a name="data-exfiltration-prevention"></a>Data exfiltration prevention (Zapobieganie eksfiltracji danych)

Filtrowanie danych w Azure Database for MySQL jest możliwe, gdy autoryzowany użytkownik, taki jak administrator bazy danych, może wyodrębnić dane z jednego systemu i przenieść je do innej lokalizacji lub systemu poza organizacją. Na przykład użytkownik przenosi dane na konto magazynu należące do innej firmy.

Rozważmy scenariusz z użytkownikiem z uruchomioną funkcją MySQL Workbench na maszynie wirtualnej platformy Azure, która nawiązuje połączenie z serwerem Azure Database for MySQLm, który jest inicjowany w regionie zachodnie stany USA. W poniższym przykładzie pokazano, jak ograniczyć dostęp za pomocą publicznych punktów końcowych w Azure Database for MySQL przy użyciu funkcji kontroli dostępu do sieci.

* Wyłącz cały ruch usługi platformy Azure do Azure Database for MySQL za pośrednictwem publicznego punktu końcowego przez ustawienie opcji Zezwalaj na wyłączanie *usług platformy Azure* . Upewnij się, że żadne adresy IP lub zakresy nie mogą uzyskać dostępu do serwera za pośrednictwem [reguł zapory](./concepts-firewall-rules.md) lub [punktów końcowych usługi sieci wirtualnej](./concepts-data-access-and-security-vnet.md).

* Zezwalaj tylko na ruch do Azure Database for MySQL przy użyciu prywatnego adresu IP maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz artykuły na temat [punktów końcowych usługi](concepts-data-access-and-security-vnet.md) i [reguł zapory sieci wirtualnej](howto-manage-vnet-using-portal.md).

* Na maszynie wirtualnej platformy Azure Zawęź zakres połączenia wychodzącego za pomocą sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i tagów usługi w następujący sposób:

    * Określ regułę sieciowej grupy zabezpieczeń, aby zezwolić na ruch dla *programu Service Tag = SQL. Zachodnie* — Zezwalanie na połączenie tylko Azure Database for MySQL w regionie zachodnie stany USA
    * Określ regułę sieciowej grupy zabezpieczeń (z wyższym priorytetem), aby odmówić ruchu przez Azure Database for MySQL *tag usługi*</br></br>

Po zakończeniu tej instalacji maszyna wirtualna platformy Azure może łączyć się tylko z Azure Database for MySQL w regionie zachodnie stany USA. Łączność nie jest jednak ograniczona do jednego Azure Database for MySQL. Maszyna wirtualna może nadal łączyć się z dowolnymi Azure Database for MySQL w regionie zachodnie stany USA, w tym z bazami danych, które nie są częścią subskrypcji. Chociaż eksfiltracji zakres danych w powyższym scenariuszu do określonego regionu, nie został on całkowicie wyeliminowany.</br>

Za pomocą linku prywatnego można teraz skonfigurować mechanizmy kontroli dostępu do sieci, takie jak sieciowych grup zabezpieczeń, aby ograniczyć dostęp do prywatnego punktu końcowego. Poszczególne zasoby usługi Azure PaaS są następnie mapowane na określone prywatne punkty końcowe. Złośliwy tester może uzyskać dostęp tylko do zamapowanego zasobu PaaS (na przykład Azure Database for MySQL) i bez innego zasobu.

## <a name="on-premises-connectivity-over-private-peering"></a>Łączność lokalna za pośrednictwem prywatnej komunikacji równorzędnej

Po nawiązaniu połączenia z publicznym punktem końcowym z maszyn lokalnych należy dodać adres IP do zapory opartej na protokole IP przy użyciu reguły zapory na poziomie serwera. Chociaż ten model działa dobrze, aby umożliwić dostęp do poszczególnych maszyn na potrzeby obciążeń deweloperskich lub testowych, trudno jest zarządzać w środowisku produkcyjnym.

Za pomocą linku prywatnego można włączyć dostęp między lokalizacjami do prywatnego punktu końcowego za pomocą usługi [Express Route](https://azure.microsoft.com/services/expressroute/) (er), prywatnej komunikacji równorzędnej lub [tunelu VPN](../vpn-gateway/index.yml). Mogą oni następnie wyłączyć dostęp za pośrednictwem publicznego punktu końcowego i nie używać zapory opartej na protokole IP.

> [!NOTE]
> W niektórych przypadkach Azure Database for MySQL i podsieć wirtualna znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
> - Upewnij się, że w subskrypcji jest zarejestrowany dostawca zasobów **Microsoft. DBforMySQL** . Aby uzyskać więcej informacji, zobacz temat [Resource-Manager-Registration][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Skonfiguruj prywatny link dla Azure Database for MySQL

### <a name="creation-process"></a>Proces tworzenia

Prywatne punkty końcowe są wymagane do włączenia prywatnego linku. Można to zrobić, korzystając z poniższych przewodników.

* [Witryna Azure Portal](./howto-configure-privatelink-portal.md)
* [Interfejs wiersza polecenia](./howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Proces zatwierdzania
Po utworzeniu przez administratora sieci prywatnego punktu końcowego (PE) administrator MySQL może zarządzać połączeniem prywatnego punktu końcowego (PEC) do Azure Database for MySQL. Takie Rozdzielenie obowiązków między administratorem sieci a usługą DBA jest pomocne w zarządzaniu Azure Database for MySQL łącznością. 

* Przejdź do zasobu serwera Azure Database for MySQL w Azure Portal. 
    * Wybierz połączenia prywatnego punktu końcowego w lewym okienku
    * Pokazuje listę wszystkich połączeń prywatnych punktów końcowych (PECs)
    * Odpowiedni prywatny punkt końcowy (PE) został utworzony

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-portal.png" alt-text="Wybierz Portal prywatnego punktu końcowego":::

* Wybierz z listy indywidualny element PEC, wybierając go.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link.png" alt-text="Wybierz prywatny punkt końcowy oczekujący na zatwierdzenie":::

* Administrator serwera MySQL może zatwierdzić lub odrzucić PEC i opcjonalnie dodać odpowiedź krótką tekstu.

:::image type="content" source="media/concepts-data-access-and-security-private-link/select-private-link-message.png" alt-text="Wybierz komunikat dotyczący prywatnego punktu końcowego":::

* Po zatwierdzeniu lub odrzuceniu lista będzie odzwierciedlać odpowiedni stan wraz z tekstem odpowiedzi

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png" alt-text="Wybierz stan końcowy prywatnego punktu końcowego":::

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Przypadki użycia prywatnego linku do Azure Database for MySQL

Klienci mogą łączyć się z prywatnym punktem końcowym z tej samej sieci wirtualnej, [równorzędnej](../virtual-network/virtual-network-peering-overview.md) podsieci w tym samym regionie lub w różnych regionach albo za pośrednictwem [połączenia](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) między różnymi regionami. Ponadto klienci mogą łączyć się z lokalnego przy użyciu ExpressRoute, prywatnej komunikacji równorzędnej lub tunelowania sieci VPN. Poniżej znajduje się uproszczony diagram przedstawiający typowe przypadki użycia.

:::image type="content" source="media/concepts-data-access-and-security-private-link/show-private-link-overview.png" alt-text="Wybieranie prywatnego punktu końcowego — Omówienie":::

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Łączenie z maszyny wirtualnej platformy Azure w Virtual Network komunikacji równorzędnej (VNet)
Skonfiguruj [komunikację równorzędną sieci](../virtual-network/tutorial-connect-virtual-networks-powershell.md) wirtualnych w celu nawiązania połączenia z Azure Database for MySQL z maszyny wirtualnej platformy Azure w równorzędnej sieci wirtualnej.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Nawiązywanie połączenia z maszyny wirtualnej platformy Azure w środowisku sieci wirtualnej między sieciami wirtualnymi
Skonfiguruj [połączenie z bramą VPN między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) w celu nawiązania połączenia z Azure Database for MySQLą z maszyny wirtualnej platformy Azure w innym regionie lub w ramach subskrypcji.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Łączenie ze środowiska lokalnego za pośrednictwem sieci VPN
Aby nawiązać połączenie ze środowiskiem lokalnym z Azure Database for MySQL, wybierz i zaimplementuj jedną z opcji:

* [Połączenie punkt-lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Połączenie sieci VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Obwód ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Link prywatny połączony z regułami zapory

W przypadku korzystania z prywatnego linku w połączeniu z regułami zapory są możliwe następujące sytuacje i wyniki:

* Jeśli nie skonfigurujesz żadnych reguł zapory, domyślnie żaden ruch nie będzie mógł uzyskać dostępu do Azure Database for MySQL.

* W przypadku skonfigurowania ruchu publicznego lub punktu końcowego usługi i utworzenia prywatnych punktów końcowych różne typy ruchu przychodzącego są autoryzowane przez odpowiedni typ reguły zapory.

* Jeśli nie skonfigurowano żadnego ruchu publicznego lub punktu końcowego usługi i utworzysz prywatne punkty końcowe, Azure Database for MySQL będzie dostępny tylko za pomocą prywatnych punktów końcowych. Jeśli nie skonfigurujesz ruchu publicznego lub punktu końcowego usługi, po odrzuceniu lub usunięciu wszystkich zatwierdzonych prywatnych punktów końcowych żaden ruch nie będzie w stanie uzyskać dostępu do Azure Database for MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Odmów dostępu publicznego dla Azure Database for MySQL

Jeśli chcesz używać tylko prywatnych punktów końcowych do uzyskiwania dostępu do swoich Azure Database for MySQL, możesz wyłączyć ustawienie wszystkie publiczne punkty końcowe (tj. [reguły zapory](concepts-firewall-rules.md) i [punkty końcowe usługi sieci wirtualnej](concepts-data-access-and-security-vnet.md)), ustawiając opcję **Odmów dostępu** do poczty publicznej na serwerze bazy danych. 

Jeśli to ustawienie ma wartość *tak*, do Azure Database for MySQL mogą być używane tylko połączenia za pośrednictwem prywatnych punktów końcowych. Jeśli to ustawienie ma wartość *nie*, klienci mogą łączyć się z Azure Database for MySQL na podstawie ustawień zapory lub punktu końcowego usługi sieci wirtualnej. Ponadto po ustawieniu wartości dostępu do sieci prywatnej klienci nie mogą dodawać i/lub aktualizować istniejących reguł zapory i zasad punktu końcowego usługi sieci wirtualnej.

> [!Note]
> Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w których Azure Database for MySQL — jeden serwer obsługuje warstwy cenowe Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.
>
> To ustawienie nie ma żadnego wpływu na konfiguracje protokołów SSL i TLS dla Azure Database for MySQL.

Aby dowiedzieć się, jak ustawić opcję **Odmów dostępu do sieci publicznej** dla Azure Database for MySQL z Azure Portal, zobacz [jak skonfigurować odmowę dostępu do sieci publicznej](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat funkcji zabezpieczeń Azure Database for MySQL, zobacz następujące artykuły:

* Aby skonfigurować zaporę dla Azure Database for MySQL, zobacz [Obsługa zapory](./concepts-firewall-rules.md).

* Aby dowiedzieć się, jak skonfigurować punkt końcowy usługi sieci wirtualnej dla Azure Database for MySQL, zobacz [Konfigurowanie dostępu z sieci wirtualnych](./concepts-data-access-and-security-vnet.md).

* Aby zapoznać się z omówieniem Azure Database for MySQL łączności, zobacz [Azure Database for MySQL architektura łączności](./concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
