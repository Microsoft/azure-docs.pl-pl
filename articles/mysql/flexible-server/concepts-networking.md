---
title: Omówienie sieci — Azure Database for MySQL elastyczny serwer
description: Informacje o opcjach łączności i sieci w opcji elastyczne wdrożenie serwera dla Azure Database for MySQL
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 48265856e5e745e05f6625766f9cd0c9b15c90a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708666"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Pojęcia dotyczące łączności i sieci dla Azure Database for MySQL-elastyczny serwer (wersja zapoznawcza)

W tym artykule opisano opcje łączności publicznej i prywatnej dla Twojego serwera. Zapoznaj się z szczegółowymi pojęciami sieciowymi dotyczącymi Azure Database for MySQL elastyczny serwer, aby bezpiecznie utworzyć serwer na platformie Azure.

> [!IMPORTANT]
> Azure Database for MySQL — serwer elastyczny jest w wersji zapoznawczej.

## <a name="choosing-a-networking-option"></a>Wybieranie opcji sieci
Dostępne są dwie opcje sieciowe dla Azure Database for MySQL elastycznego serwera. Opcje są **dostęp prywatny (Integracja z siecią wirtualną)** i **dostęp publiczny (dozwolone adresy IP)**. W przypadku tworzenia serwera należy wybrać jedną opcję. 

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
* Połączenia z dozwolonych adresów IP muszą uwierzytelniać się na serwerze MySQL przy użyciu prawidłowych poświadczeń
* [Szyfrowanie połączenia](#tls-and-ssl) jest dostępne dla ruchu sieciowego
* Serwer ma w pełni kwalifikowaną nazwę domeny (FQDN). W przypadku właściwości hostname w ciągach połączeń zalecamy użycie nazwy FQDN zamiast adresu IP.
* Obie opcje kontrolują dostęp na poziomie serwera, a nie na poziomie bazy danych lub tabeli. Za pomocą właściwości ról MySQL można kontrolować dostęp do bazy danych, tabeli i innego obiektu.


## <a name="private-access-vnet-integration"></a>Dostęp prywatny (integracja z siecią wirtualną)
Funkcja integracji dostępu prywatnego z siecią wirtualną (VNET) zapewnia prywatną i bezpieczną komunikację z serwerem elastycznym MySQL.

### <a name="virtual-network-concepts"></a>Pojęcia dotyczące sieci wirtualnej
Poniżej przedstawiono kilka koncepcji, które należy znać w przypadku używania sieci wirtualnych z elastycznymi serwerami MySQL.

* **Sieć wirtualna** — usługa Azure Virtual Network (VNET) zawiera prywatną przestrzeń adresową IP skonfigurowaną do użycia. Aby dowiedzieć się więcej o usłudze Azure Virtual Network, przejdź do [omówienia usługi azure Virtual Network](../../virtual-network/virtual-networks-overview.md) .

    Sieć wirtualna musi znajdować się w tym samym regionie świadczenia usługi Azure co serwer elastyczny.


* **Delegowana podsieć** — Sieć wirtualna zawiera podsieci (podsieci). Podsieci umożliwiają segmentację sieci wirtualnej w mniejsze przestrzenie adresowe. Zasoby platformy Azure są wdrażane w określonych podsieciach w obrębie sieci wirtualnej. 

   Serwer elastyczny MySQL musi znajdować się w podsieci **delegowanej** tylko do użytku elastycznego serwera MySQL. Ta delegacja oznacza, że tylko usługa Azure Database for MySQL — elastyczny serwer może korzystać z tej podsieci. W podsieci delegowanej nie mogą znajdować się żadne inne typy zasobów platformy Azure. Delegowanie podsieci przez przypisanie jej właściwości delegowania jako Microsoft. DBforMySQL/flexibleServers.

* **Sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń)** Reguły zabezpieczeń w sieciowych grupach zabezpieczeń umożliwiają filtrowanie typu ruchu sieciowego, który może przepływać do i z podsieci sieci wirtualnej i interfejsów sieciowych. Aby uzyskać więcej informacji, zapoznaj się z [omówieniem sieciowej grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md) .


### <a name="unsupported-virtual-network-scenarios"></a>Nieobsługiwane scenariusze sieci wirtualnej
* Publiczny punkt końcowy (lub publiczny adres IP lub DNS) — elastyczny serwer wdrożony w sieci wirtualnej nie może mieć publicznego punktu końcowego
* Po wdrożeniu elastycznego serwera do sieci wirtualnej i podsieci nie można przenieść go do innej sieci wirtualnej lub podsieci. Nie można przenieść sieci wirtualnej do innej grupy zasobów lub subskrypcji.
* Nie można zwiększyć rozmiaru podsieci (przestrzenie adresowe), gdy zasoby istnieją w podsieci
* Komunikacja równorzędna sieci wirtualnych między regionami nie jest obsługiwana

Dowiedz się, jak włączyć dostęp prywatny (Integracja z siecią wirtualną) za pomocą [Azure Portal](how-to-manage-virtual-network-portal.md) lub [interfejsu wiersza polecenia platformy Azure](how-to-manage-virtual-network-cli.md).


## <a name="public-access-allowed-ip-addresses"></a>Dostęp publiczny (dozwolone adresy IP)
Właściwości metody dostępu publicznego obejmują:
* Tylko dozwolone adresy IP mają uprawnienia dostępu do serwera elastycznego MySQL. Domyślnie żadne adresy IP nie są dozwolone. Adresy IP można dodać podczas tworzenia serwera lub później.
* Serwer MySQL ma publicznie rozpoznawalną nazwę DNS
* Serwer elastyczny nie znajduje się w jednej z sieci wirtualnych platformy Azure
* Ruch sieciowy do i z serwera nie znajduje się w sieci prywatnej. Ruch używa ogólnych ścieżek Internetu.

### <a name="firewall-rules"></a>Reguły zapory
Przyznanie uprawnienia do adresu IP jest nazywane regułą zapory. Jeśli próba połączenia pochodzi z adresu IP, który nie jest dozwolony, klient inicjujący wyświetli błąd.


### <a name="allowing-all-azure-ip-addresses"></a>Zezwalanie na wszystkie adresy IP platformy Azure
Jeśli stały wychodzący adres IP nie jest dostępny dla usługi platformy Azure, możesz rozważyć włączenie połączeń ze wszystkich adresów IP centrum danych platformy Azure.

> [!IMPORTANT]
> Opcja **Zezwalaj na publiczny dostęp z usług i zasobów platformy Azure w ramach platformy Azure** umożliwia skonfigurowanie zapory w taki sposób, aby zezwalała na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.

Dowiedz się, jak włączyć publiczny dostęp (dozwolone adresy IP) i zarządzać [Azure portal](how-to-manage-firewall-portal.md) nim przy użyciu [interfejsu wiersza polecenia Azure Portal lub platformy Azure](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Rozwiązywanie problemów z dostępem publicznym
Podczas uzyskiwania dostępu do bazy danych programu Microsoft Azure dla usługi serwera MySQL należy wziąć pod uwagę następujące kwestie:

* **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:** Aby zmiany w konfiguracji zapory serwera Azure Database for MySQL zaczęły obowiązywać, może wystąpić nawet pięć minut.

* **Uwierzytelnianie nie powiodło się:** Jeśli użytkownik nie ma uprawnień na serwerze Azure Database for MySQL lub użyte hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem Azure Database for MySQL. Utworzenie ustawienia zapory zapewnia klientom możliwość próby nawiązania połączenia z serwerem. Każdy klient musi nadal podawać niezbędne poświadczenia zabezpieczeń.

* **Dynamiczny adres IP klienta:** Jeśli masz połączenie internetowe z dynamicznym adresem IP i masz problemy z uzyskaniem przez zaporę, możesz wypróbować jedno z następujących rozwiązań:

   * Poproszenie usługodawcy internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera Azure Database for MySQL, a następnie Dodaj zakres adresów IP jako regułę zapory.
   * Zamiast tego należy pobrać statyczne adresy IP dla komputerów klienckich, a następnie dodać statyczny adres IP jako regułę zapory.


## <a name="hostname"></a>Hostname (Nazwa hosta)
Niezależnie od wybranej opcji sieci zalecamy zawsze używać w pełni kwalifikowanej nazwy domeny (FQDN) jako nazwy hosta podczas łączenia się z serwerem elastycznym. Adres IP serwera nie musi pozostać statyczny. Użycie nazwy FQDN pomoże uniknąć wprowadzania zmian w parametrach połączenia. 

Przykład
* Rekomendowane `hostname = servername.mysql.database.azure.com`
* Tam, gdzie to możliwe, Unikaj używania `hostname = 10.0.0.4` (adresu prywatnego) lub `hostname = 40.2.45.67` (publiczny adres IP)



## <a name="tls-and-ssl"></a>TLS i SSL
Azure Database for MySQL elastyczny serwer obsługuje łączenie aplikacji klienckich z usługą MySQL przy użyciu protokołu Transport Layer Security (TLS). TLS to standardowy protokół, który zapewnia szyfrowane połączenia sieciowe między serwerem bazy danych i aplikacjami klienckimi. Protokół TLS jest zaktualizowanym protokołem SSL (SSL).

Azure Database for MySQL elastyczny serwer obsługuje tylko połączenia szyfrowane przy użyciu Transport Layer Security (TLS 1,2). Wszystkie połączenia przychodzące z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone. Nie można wyłączyć ani zmienić wersji protokołu TLS w celu nawiązania połączenia z serwerem elastycznym Azure Database for MySQL.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak włączyć dostęp prywatny (integracja z siecią wirtualną) przy użyciu witryny [Azure Portal](how-to-manage-virtual-network-portal.md) lub [interfejsu wiersza polecenia platformy Azure](how-to-manage-virtual-network-cli.md)
* Dowiedz się, jak włączyć dostęp publiczny (dozwolone adresy IP) za pomocą witryny [Azure Portal](how-to-manage-firewall-portal.md) lub [interfejsu wiersza polecenia platformy Azure](how-to-manage-firewall-cli.md)
* Dowiedz się, jak [korzystać z protokołu TLS](how-to-connect-tls-ssl.md)
