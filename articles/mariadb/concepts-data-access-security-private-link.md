---
title: Prywatny link — Azure Database for MariaDB
description: Dowiedz się, jak działa łącze prywatne dla Azure Database for MariaDB.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: a1b97586eeeab1d5d1917f2d1cec9e0f71e9e329
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867461"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Prywatny link do Azure Database for MariaDB

Link prywatny umożliwia tworzenie prywatnych punktów końcowych dla Azure Database for MariaDB, co umożliwia korzystanie z usług platformy Azure w ramach prywatnego Virtual Network (VNet). Prywatny punkt końcowy uwidacznia prywatny adres IP, którego można użyć do nawiązania połączenia z serwerem bazy danych Azure Database for MariaDB tak samo jak w przypadku każdego innego zasobu w sieci wirtualnej.

Aby uzyskać listę PaaS usług, które obsługują funkcję linku prywatnego, zapoznaj się z [dokumentacją](../private-link/index.yml)linku prywatnego. Prywatny punkt końcowy to prywatny adres IP w obrębie określonej sieci [wirtualnej](../virtual-network/virtual-networks-overview.md) i podsieci.

> [!NOTE]
> Funkcja Link prywatny jest dostępna tylko dla serwerów Azure Database for MariaDB w warstwach cenowych Ogólnego przeznaczenia lub zoptymalizowanych pod kątem pamięci. Upewnij się, że serwer bazy danych znajduje się w jednej z tych warstw cenowych.

## <a name="data-exfiltration-prevention"></a>Data exfiltration prevention (Zapobieganie eksfiltracji danych)

Filtrowanie danych w Azure Database for MariaDB jest możliwe, gdy autoryzowany użytkownik, taki jak administrator bazy danych, może wyodrębnić dane z jednego systemu i przenieść je do innej lokalizacji lub systemu poza organizacją. Na przykład użytkownik przenosi dane na konto magazynu należące do innej firmy.

Rozważmy scenariusz z użytkownikiem z systemem MariaDB Workbench wewnątrz maszyny wirtualnej platformy Azure łączącej się z wystąpieniem Azure Database for MariaDB. To wystąpienie MariaDB znajduje się w centrum danych w zachodnich stanach USA. W poniższym przykładzie pokazano, jak ograniczyć dostęp za pomocą publicznych punktów końcowych w Azure Database for MariaDB przy użyciu funkcji kontroli dostępu do sieci.

* Wyłącz cały ruch usługi platformy Azure do Azure Database for MariaDB za pośrednictwem publicznego punktu końcowego przez ustawienie opcji Zezwalaj na wyłączanie usług platformy Azure. Upewnij się, że żadne adresy IP lub zakresy nie mogą uzyskać dostępu do serwera za pośrednictwem [reguł zapory](concepts-firewall-rules.md) lub [punktów końcowych usługi sieci wirtualnej](concepts-data-access-security-vnet.md).

* Zezwalaj tylko na ruch do Azure Database for MariaDB przy użyciu prywatnego adresu IP maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz artykuły na temat [punktów końcowych usługi](concepts-data-access-security-vnet.md) i [reguł zapory sieci wirtualnej](howto-manage-vnet-portal.md).

* Na maszynie wirtualnej platformy Azure Zawęź zakres połączenia wychodzącego za pomocą sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń) i Tagi usług w następujący sposób:

    * Określ regułę sieciowej grupy zabezpieczeń, aby zezwolić na ruch dla programu Service Tag = SQL. Zachodnie — Zezwalanie na połączenie tylko Azure Database for MariaDB w regionie zachodnie stany USA
    * Określ regułę sieciowej grupy zabezpieczeń (z wyższym priorytetem), aby odmówić ruchu dla programu Service Tag = z odmową połączeń z bazą danych MariaDB we wszystkich regionach</br></br>

Po zakończeniu tej instalacji maszyna wirtualna platformy Azure może łączyć się tylko z Azure Database for MariaDB w regionie zachodnie stany USA. Łączność nie jest jednak ograniczona do jednego Azure Database for MariaDB. Maszyna wirtualna może nadal łączyć się z dowolnymi Azure Database for MariaDB w regionie zachodnie stany USA, w tym z bazami danych, które nie są częścią subskrypcji. Chociaż eksfiltracji zakres danych w powyższym scenariuszu do określonego regionu, nie został on całkowicie wyeliminowany.</br>

Za pomocą linku prywatnego można teraz skonfigurować mechanizmy kontroli dostępu do sieci, takie jak sieciowych grup zabezpieczeń, aby ograniczyć dostęp do prywatnego punktu końcowego. Poszczególne zasoby usługi Azure PaaS są następnie mapowane na określone prywatne punkty końcowe. Złośliwy tester może uzyskać dostęp tylko do zamapowanego zasobu PaaS (na przykład Azure Database for MariaDB) i bez innego zasobu.

## <a name="on-premises-connectivity-over-private-peering"></a>Łączność lokalna za pośrednictwem prywatnej komunikacji równorzędnej

Po nawiązaniu połączenia z publicznym punktem końcowym z maszyn lokalnych należy dodać adres IP do zapory opartej na protokole IP przy użyciu reguły zapory na poziomie serwera. Chociaż ten model działa dobrze, aby umożliwić dostęp do poszczególnych maszyn na potrzeby obciążeń deweloperskich lub testowych, trudno jest zarządzać w środowisku produkcyjnym.

Za pomocą linku prywatnego można włączyć dostęp między lokalizacjami do prywatnego punktu końcowego za pomocą usługi [Express Route](https://azure.microsoft.com/services/expressroute/) (er), prywatnej komunikacji równorzędnej lub [tunelu VPN](../vpn-gateway/index.yml). Mogą oni następnie wyłączyć dostęp za pośrednictwem publicznego punktu końcowego i nie używać zapory opartej na protokole IP.

> [!NOTE]
> W niektórych przypadkach Azure Database for MariaDB i podsieć wirtualna znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
> - Upewnij się, że w subskrypcji jest zarejestrowany dostawca zasobów **Microsoft. DBforMariaDB** . Aby uzyskać więcej informacji, zobacz temat [Resource-Manager-Registration][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Skonfiguruj prywatny link dla Azure Database for MariaDB

### <a name="creation-process"></a>Proces tworzenia

Prywatne punkty końcowe są wymagane do włączenia prywatnego linku. Można to zrobić, korzystając z poniższych przewodników.

* [Witryna Azure Portal](howto-configure-privatelink-portal.md)
* [Interfejs wiersza polecenia](howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Proces zatwierdzania

Po utworzeniu przez administratora sieci prywatnego punktu końcowego (PE) administrator może zarządzać połączeniem prywatnego punktu końcowego (PEC) do Azure Database for MariaDB. Takie Rozdzielenie obowiązków między administratorem sieci a usługą DBA jest pomocne w zarządzaniu Azure Database for MariaDB łącznością. 

* Przejdź do zasobu serwera Azure Database for MariaDB w Azure Portal. 
    * Wybierz połączenia prywatnego punktu końcowego w lewym okienku
    * Pokazuje listę wszystkich połączeń prywatnych punktów końcowych (PECs)
    * Odpowiedni prywatny punkt końcowy (PE) został utworzony

![Wybierz Portal prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Wybierz z listy indywidualny element PEC, wybierając go.

![Wybierz prywatny punkt końcowy oczekujący na zatwierdzenie](media/concepts-data-access-and-security-private-link/select-private-link.png)

* Administrator serwera MariaDB może zdecydować się na zatwierdzenie lub odrzucenie PEC i opcjonalnie dodać odpowiedź krótką tekstu.

![Wybierz komunikat dotyczący prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Po zatwierdzeniu lub odrzuceniu lista będzie odzwierciedlać odpowiedni stan wraz z tekstem odpowiedzi

![Wybierz stan końcowy prywatnego punktu końcowego](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Przypadki użycia prywatnego linku do Azure Database for MariaDB


Klienci mogą łączyć się z prywatnym punktem końcowym z tej samej sieci wirtualnej, [równorzędnej](../virtual-network/virtual-network-peering-overview.md) podsieci w tym samym regionie lub w różnych regionach albo za pośrednictwem [połączenia](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) między różnymi regionami. Ponadto klienci mogą łączyć się z lokalnego przy użyciu ExpressRoute, prywatnej komunikacji równorzędnej lub tunelowania sieci VPN. Poniżej znajduje się uproszczony diagram przedstawiający typowe przypadki użycia.

![Wybieranie prywatnego punktu końcowego — Omówienie](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Łączenie z maszyny wirtualnej platformy Azure w Virtual Network komunikacji równorzędnej (VNet)
Skonfiguruj [komunikację równorzędną sieci](../virtual-network/tutorial-connect-virtual-networks-powershell.md) wirtualnych w celu nawiązania połączenia z Azure Database for MariaDB z maszyny wirtualnej platformy Azure w równorzędnej sieci wirtualnej.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Nawiązywanie połączenia z maszyny wirtualnej platformy Azure w środowisku sieci wirtualnej między sieciami wirtualnymi
Skonfiguruj [połączenie z bramą VPN między sieciami wirtualnymi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) w celu nawiązania połączenia z Azure Database for MariaDBą z maszyny wirtualnej platformy Azure w innym regionie lub w ramach subskrypcji.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Łączenie ze środowiska lokalnego za pośrednictwem sieci VPN
Aby nawiązać połączenie ze środowiskiem lokalnym z Azure Database for MariaDB, wybierz i zaimplementuj jedną z opcji:

* [Połączenie punkt-lokacja](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Połączenie sieci VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Obwód ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Link prywatny połączony z regułami zapory

W przypadku korzystania z prywatnego linku w połączeniu z regułami zapory są możliwe następujące sytuacje i wyniki:

* Jeśli nie skonfigurujesz żadnych reguł zapory, domyślnie żaden ruch nie będzie mógł uzyskać dostępu do Azure Database for MariaDB.

* W przypadku skonfigurowania ruchu publicznego lub punktu końcowego usługi i utworzenia prywatnych punktów końcowych różne typy ruchu przychodzącego są autoryzowane przez odpowiedni typ reguły zapory.

* Jeśli nie skonfigurowano żadnego ruchu publicznego lub punktu końcowego usługi i utworzysz prywatne punkty końcowe, Azure Database for MariaDB będzie dostępny tylko za pomocą prywatnych punktów końcowych. Jeśli nie skonfigurujesz ruchu publicznego lub punktu końcowego usługi, po odrzuceniu lub usunięciu wszystkich zatwierdzonych prywatnych punktów końcowych żaden ruch nie będzie w stanie uzyskać dostępu do Azure Database for MariaDB.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Odmów dostępu publicznego dla Azure Database for MariaDB

Jeśli chcesz całkowicie używać prywatnych punktów końcowych do uzyskiwania dostępu do swoich Azure Database for MariaDB, możesz wyłączyć ustawienie wszystkie publiczne punkty końcowe ([reguły zapory](concepts-firewall-rules.md) i [punkty końcowe usługi sieci wirtualnej](concepts-data-access-security-vnet.md)), ustawiając opcję **Odmów dostępu** do poczty publicznej na serwerze bazy danych. 

Jeśli to ustawienie ma wartość *tak*, do Azure Database for MariaDB mogą być używane tylko połączenia za pośrednictwem prywatnych punktów końcowych. Jeśli to ustawienie ma wartość *nie*, klienci mogą łączyć się z Azure Database for MariaDB na podstawie ustawień zapory lub punktu końcowego usługi sieci wirtualnej. Ponadto po ustawieniu wartości dostępu do sieci prywatnej klienci nie mogą dodawać i/lub aktualizować istniejących reguł zapory i zasad punktu końcowego usługi sieci wirtualnej.

> [!Note]
> Ta funkcja jest dostępna we wszystkich regionach świadczenia usługi Azure, w których Azure Database for PostgreSQL — jeden serwer obsługuje warstwy cenowe Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.
>
> To ustawienie nie ma żadnego wpływu na konfiguracje protokołów SSL i TLS dla Azure Database for MariaDB.

Aby dowiedzieć się, jak ustawić opcję **Odmów dostępu do sieci publicznej** dla Azure Database for MariaDB z Azure Portal, zobacz [jak skonfigurować odmowę dostępu do sieci publicznej](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat funkcji zabezpieczeń Azure Database for MariaDB, zobacz następujące artykuły:

* Aby skonfigurować zaporę dla Azure Database for MariaDB, zobacz [Obsługa zapory](concepts-firewall-rules.md).

* Aby dowiedzieć się, jak skonfigurować punkt końcowy usługi sieci wirtualnej dla Azure Database for MariaDB, zobacz [Konfigurowanie dostępu z sieci wirtualnych](concepts-data-access-security-vnet.md).

* Aby zapoznać się z omówieniem Azure Database for MariaDB łączności, zobacz [Azure Database for MariaDB architektura łączności](concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
