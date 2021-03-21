---
title: Przegląd sieci — serwer elastyczny Azure Database for PostgreSQL
description: Informacje o opcjach łączności i sieci w opcji elastyczne wdrożenie serwera dla Azure Database for PostgreSQL
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/21/2021
ms.openlocfilehash: a6f049670a6860bbc195b92458945d1a53029b4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732806"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>Przegląd sieci — serwer elastyczny Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym artykule opisano zagadnienia dotyczące łączności i sieci dla Azure Database for PostgreSQL-elastyczny serwer. 

## <a name="choosing-a-networking-option"></a>Wybieranie opcji sieci
Dostępne są dwie opcje sieciowe dla Azure Database for PostgreSQL-elastyczny serwer. Opcje są **dostęp prywatny (Integracja z siecią wirtualną)** i **dostęp publiczny (dozwolone adresy IP)**. W przypadku tworzenia serwera należy wybrać jedną opcję. 

> [!NOTE]
> Nie można zmienić opcji sieci po utworzeniu serwera. 

* **Dostęp prywatny (Integracja z siecią wirtualną)** — możesz wdrożyć elastyczny serwer do [Virtual Network platformy Azure](../../virtual-network/virtual-networks-overview.md). Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. Zasoby w sieci wirtualnej mogą komunikować się za pomocą prywatnych adresów IP.

   Wybierz opcję Integracja z siecią wirtualną, jeśli chcesz skorzystać z następujących możliwości:
   * Łączenie z zasobów platformy Azure w tej samej sieci wirtualnej z elastycznym serwerem przy użyciu prywatnych adresów IP
   * Używanie sieci VPN lub ExpressRoute do nawiązywania połączeń z zasobów spoza platformy Azure z serwerem elastycznym
   * Elastyczny serwer nie ma publicznego punktu końcowego

* **Dostęp publiczny (dozwolone adresy IP)** — elastyczny serwer jest dostępny za pomocą publicznego punktu końcowego. Publiczny punkt końcowy jest publicznie rozpoznawalnym adresem DNS. Fraza "dozwolone adresy IP" odnosi się do zakresu adresów IP wybranych do udzielenia uprawnienia dostępu do serwera. Te uprawnienia są nazywane **regułami zapory**. 

   Wybierz metodę dostępu publicznego, jeśli chcesz mieć następujące możliwości:
   * Łączenie z zasobów platformy Azure, które nie obsługują sieci wirtualnych
   * Łączenie się z zasobów poza platformą Azure, które nie są połączone za pośrednictwem sieci VPN lub ExpressRoute 
   * Elastyczny serwer ma publiczny punkt końcowy

W przypadku wybrania opcji korzystania z prywatnego dostępu lub publicznego dostępu należy stosować następujące cechy:
* Połączenia z dozwolonych adresów IP muszą być uwierzytelniane na serwerze PostgreSQL z prawidłowymi poświadczeniami
* [Szyfrowanie połączenia](#tls-and-ssl) jest dostępne dla ruchu sieciowego
* Serwer ma w pełni kwalifikowaną nazwę domeny (FQDN). W przypadku właściwości hostname w ciągach połączeń zalecamy użycie nazwy FQDN zamiast adresu IP.
* Obie opcje kontrolują dostęp na poziomie serwera, a nie na poziomie bazy danych lub tabeli. Za pomocą właściwości ról PostgreSQL można kontrolować dostęp do bazy danych, tabeli i innych obiektów.


## <a name="private-access-vnet-integration"></a>Dostęp prywatny (integracja z siecią wirtualną)
Funkcja integracji dostępu prywatnego z siecią wirtualną (VNET) zapewnia prywatną i bezpieczną komunikację z serwerem elastycznym PostgreSQL.

### <a name="virtual-network-concepts"></a>Pojęcia dotyczące sieci wirtualnej
Poniżej przedstawiono kilka koncepcji, które należy znać w przypadku korzystania z sieci wirtualnych z serwerami elastycznymi PostgreSQL.

* **Sieć wirtualna** — usługa Azure Virtual Network (VNET) zawiera prywatną przestrzeń adresową IP skonfigurowaną do użycia. Aby dowiedzieć się więcej o usłudze Azure Virtual Network, przejdź do [omówienia usługi azure Virtual Network](../../virtual-network/virtual-networks-overview.md) .

    Sieć wirtualna musi znajdować się w tym samym regionie świadczenia usługi Azure co serwer elastyczny.


* **Delegowana podsieć** — Sieć wirtualna zawiera podsieci (podsieci). Podsieci umożliwiają segmentację sieci wirtualnej w mniejsze przestrzenie adresowe. Zasoby platformy Azure są wdrażane w określonych podsieciach w obrębie sieci wirtualnej. 

   Serwer elastyczny PostgreSQL musi znajdować się w podsieci **delegowanej** wyłącznie do użycia przez PostgreSQL elastyczny serwer. To delegowanie oznacza, że tylko Azure Database for PostgreSQL elastycznych serwerów może korzystać z tej podsieci. W podsieci delegowanej nie mogą znajdować się żadne inne typy zasobów platformy Azure. Delegowanie podsieci przez przypisanie jej właściwości delegowania jako Microsoft. DBforPostgreSQL/flexibleServers.

   Dodaj `Microsoft.Storage` do punktu końcowego usługi dla podsieci delegowanej do elastycznych serwerów. 

* **Sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń)** Reguły zabezpieczeń w sieciowych grupach zabezpieczeń umożliwiają filtrowanie typu ruchu sieciowego, który może przepływać do i z podsieci sieci wirtualnej i interfejsów sieciowych. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem sieciowej grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md) .


### <a name="unsupported-virtual-network-scenarios"></a>Nieobsługiwane scenariusze sieci wirtualnej
* Publiczny punkt końcowy (lub publiczny adres IP lub DNS) — elastyczny serwer wdrożony w sieci wirtualnej nie może mieć publicznego punktu końcowego
* Po wdrożeniu elastycznego serwera do sieci wirtualnej i podsieci nie można przenieść go do innej sieci wirtualnej lub podsieci. Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji.
* Nie można zwiększyć rozmiaru podsieci (przestrzenie adresowe), gdy zasoby istnieją w podsieci
* Komunikacja równorzędna sieci wirtualnych między regionami nie jest obsługiwana

Dowiedz się, jak utworzyć elastyczny serwer z dostępem prywatnym (Integracja z siecią wirtualną) w [Azure Portal](how-to-manage-virtual-network-portal.md) lub [interfejsie wiersza polecenia platformy Azure](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> W przypadku korzystania z niestandardowego serwera DNS należy użyć usługi przesyłania dalej DNS w celu rozpoznania nazwy FQDN serwera Azure Database for PostgreSQL-elastyczny. Aby dowiedzieć się więcej [, zobacz Rozpoznawanie nazw, które używa własnego serwera DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) .

## <a name="public-access-allowed-ip-addresses"></a>Dostęp publiczny (dozwolone adresy IP)
Właściwości metody dostępu publicznego obejmują:
* Tylko dozwolone adresy IP mają uprawnienia dostępu do serwera elastycznego PostgreSQL. Domyślnie żadne adresy IP nie są dozwolone. Adresy IP można dodać podczas tworzenia serwera lub później.
* Serwer PostgreSQL ma publicznie rozpoznawalną nazwę DNS
* Serwer elastyczny nie znajduje się w jednej z sieci wirtualnych platformy Azure
* Ruch sieciowy do i z serwera nie znajduje się w sieci prywatnej. Ruch używa ogólnych ścieżek Internetu.

### <a name="firewall-rules"></a>Reguły zapory
Przyznanie uprawnienia do adresu IP jest nazywane regułą zapory. Jeśli próba połączenia pochodzi z adresu IP, który nie jest dozwolony, klient inicjujący wyświetli błąd.

Dowiedz się, jak utworzyć elastyczny serwer z dostępem publicznym (dozwolone adresy IP) w [Azure Portal](how-to-manage-firewall-portal.md) lub [interfejsie wiersza polecenia platformy Azure](how-to-manage-firewall-cli.md).


### <a name="allowing-all-azure-ip-addresses"></a>Zezwalanie na wszystkie adresy IP platformy Azure
Jeśli stały wychodzący adres IP nie jest dostępny dla usługi platformy Azure, możesz rozważyć włączenie połączeń ze wszystkich adresów IP centrum danych platformy Azure.

> [!IMPORTANT]
> Opcja **Zezwalaj na publiczny dostęp z usług i zasobów platformy Azure w ramach platformy Azure** umożliwia skonfigurowanie zapory w taki sposób, aby zezwalała na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.

### <a name="troubleshooting-public-access-issues"></a>Rozwiązywanie problemów z dostępem publicznym
Należy wziąć pod uwagę następujące kwestie, gdy dostęp do usługi Microsoft Azure Database for PostgreSQL Server nie zadziała zgodnie z oczekiwaniami:

* **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:** Aby zmiany w konfiguracji zapory serwera Azure Database for PostgreSQL zaczęły obowiązywać, może wystąpić nawet pięć minut.

* **Uwierzytelnianie nie powiodło się:** Jeśli użytkownik nie ma uprawnień na serwerze Azure Database for PostgreSQL lub użyte hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem Azure Database for PostgreSQL. Utworzenie ustawienia zapory zapewnia klientom możliwość próby nawiązania połączenia z serwerem. Każdy klient musi nadal podawać niezbędne poświadczenia zabezpieczeń.

* **Dynamiczny adres IP klienta:** Jeśli masz połączenie internetowe z dynamicznym adresem IP i masz problemy z uzyskaniem przez zaporę, możesz wypróbować jedno z następujących rozwiązań:

   * Poproszenie usługodawcy internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera Azure Database for PostgreSQL, a następnie Dodaj zakres adresów IP jako regułę zapory.
   * Zamiast tego należy pobrać statyczne adresy IP dla komputerów klienckich, a następnie dodać statyczny adres IP jako regułę zapory.

* **Reguła zapory nie jest dostępna dla formatu IPv6:** Reguły zapory muszą być w formacie IPv4. W przypadku określenia reguł zapory w formacie IPv6 zostanie wyświetlony komunikat o błędzie walidacji.

## <a name="hostname"></a>Hostname (Nazwa hosta)
Niezależnie od wybranej opcji sieci zalecamy zawsze używać w pełni kwalifikowanej nazwy domeny (FQDN) jako nazwy hosta podczas łączenia się z serwerem elastycznym. Adres IP serwera nie musi pozostać statyczny. Użycie nazwy FQDN pomoże uniknąć wprowadzania zmian w parametrach połączenia. 

Przykład
* Rekomendowane `hostname = servername.postgres.database.azure.com`
* Jeśli to możliwe, Unikaj używania `hostname = 10.0.0.4` (adresu prywatnego) lub `hostname = 40.2.45.67` (adres publiczny)


## <a name="tls-and-ssl"></a>TLS i SSL
Azure Database for PostgreSQL — elastyczny serwer obsługuje łączenie aplikacji klienckich z usługą PostgreSQL przy użyciu usługi Transport Layer Security (TLS). TLS to standardowy protokół, który zapewnia szyfrowane połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi. Protokół TLS jest zaktualizowanym protokołem SSL (Secure Sockets Layer).

Azure Database for PostgreSQL — serwer elastyczny obsługuje tylko połączenia szyfrowane przy użyciu Transport Layer Security. Wszystkie połączenia przychodzące z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak utworzyć elastyczny serwer z **dostępem prywatnym (Integracja z siecią wirtualną)** w [Azure Portal](how-to-manage-virtual-network-portal.md) lub [interfejsie wiersza polecenia platformy Azure](how-to-manage-virtual-network-cli.md).
* Dowiedz się, jak utworzyć elastyczny serwer z **dostępem publicznym (dozwolone adresy IP)** w [Azure Portal](how-to-manage-firewall-portal.md) lub [interfejsie wiersza polecenia platformy Azure](how-to-manage-firewall-cli.md).
