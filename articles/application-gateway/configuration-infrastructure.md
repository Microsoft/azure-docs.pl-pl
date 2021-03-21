---
title: Konfiguracja infrastruktury usługi Azure Application Gateway
description: W tym artykule opisano sposób konfigurowania infrastruktury usługi Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397624"
---
# <a name="application-gateway-infrastructure-configuration"></a>Konfiguracja infrastruktury Application Gateway

Infrastruktura bramy aplikacji obejmuje sieć wirtualną, podsieci, sieciowe grupy zabezpieczeń i trasy zdefiniowane przez użytkownika.

## <a name="virtual-network-and-dedicated-subnet"></a>Sieć wirtualna i podsieć dedykowana

Brama aplikacji to dedykowane wdrożenie w sieci wirtualnej. W ramach sieci wirtualnej wymagana jest dedykowana podsieć dla bramy aplikacji. W podsieci można mieć wiele wystąpień danego wdrożenia bramy aplikacji. Możesz również wdrożyć inne bramy aplikacji w podsieci. Nie można jednak wdrożyć żadnego innego zasobu w podsieci bramy aplikacji. Nie można mieszać Standard_v2 i standardowego Application Gateway platformy Azure w tej samej podsieci.

> [!NOTE]
> [Zasady punktu końcowego usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoint-policies-overview.md) nie są obecnie obsługiwane w podsieci Application Gateway.

### <a name="size-of-the-subnet"></a>Rozmiar podsieci

Application Gateway używa jednego prywatnego adresu IP na wystąpienie oraz innego prywatnego adresu IP, jeśli jest skonfigurowany prywatny adres IP frontonu.

Platforma Azure rezerwuje także pięć adresów IP w każdej podsieci do użytku wewnętrznego: pierwsze cztery i ostatnie adresy IP. Rozważmy na przykład 15 wystąpień usługi Application Gateway bez prywatnego adresu IP frontonu. Wymagana jest co najmniej 20 adresów IP dla tej podsieci: pięć do użytku wewnętrznego i 15 dla wystąpień usługi Application Gateway.

Rozważ podsieć zawierającą 27 wystąpień usługi Application Gateway i adres IP dla prywatnego adresu IP frontonu. W takim przypadku wymagane są 33 adresów IP: 27 dla wystąpień usługi Application Gateway, jeden dla zaplecza prywatnego i pięć do użytku wewnętrznego.

Jednostka SKU Application Gateway (standardowa lub WAF) może obsługiwać do 32 wystąpień (32 wystąpień adresów IP + 1 prywatny adres IP frontonu + 5 zarezerwowane platformy Azure) — w związku z tym zaleca się minimalny rozmiar podsieci/26.

Application Gateway (jednostka SKU Standard_v2 lub WAF_v2) może obsługiwać do 125 wystąpień (125 wystąpień adresów IP + 1 prywatny adres IP frontonu + 5 zarezerwowane platformy Azure) — w związku z tym zaleca się minimalny rozmiar podsieci/24.

## <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są obsługiwane w Application Gateway. Istnieją jednak pewne ograniczenia:

- Należy zezwolić na przychodzący ruch internetowy na portach TCP 65503-65534 dla jednostki SKU Application Gateway V1 i portów TCP 65200-65535 dla jednostki SKU v2 z podsiecią docelową jako **dowolny** i źródłowy jako tag usługi **gatewaymanager** . Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure. Te porty są chronione (zablokowane) przez certyfikaty platformy Azure. Jednostki zewnętrzne, w tym klienci tych bram, nie mogą komunikować się z tymi punktami końcowymi.

- Nie można zablokować wychodzącej łączności z Internetem. Domyślne reguły ruchu wychodzącego w sieciowej grupy zabezpieczeń umożliwiają łączność z Internetem. Zalecamy wykonanie następujących czynności:

  - Nie usuwaj domyślnych reguł dla ruchu wychodzącego.
  - Nie należy tworzyć innych reguł wychodzących, które odbraniają łączności wychodzącej.

- Ruch ze znacznika **AzureLoadBalancer** z podsiecią docelową **musi być** dozwolony.

### <a name="allow-access-to-a-few-source-ips"></a>Zezwalaj na dostęp do kilku źródłowych adresów IP

W tym scenariuszu należy użyć sieciowych grup zabezpieczeń w podsieci Application Gateway. W tej kolejności należy umieścić następujące ograniczenia w podsieci:

1. Zezwalaj na ruch przychodzący ze źródłowego adresu IP lub zakresu adresów IP z miejscem docelowym jako cały zakres adresów podsieci Application Gateway i port docelowy jako port dostępu przychodzącego, na przykład port 80 dla dostępu za pośrednictwem protokołu HTTP.
2. Zezwalaj na przychodzące żądania ze źródła jako tag usługi **gatewaymanager** i miejsce docelowe jako **dowolnych** i docelowych portów as 65503-65534 dla jednostki SKU Application Gateway V1 oraz portów 65200-65535 dla wersji 2 jednostki SKU do [komunikacji z stanem kondycji zaplecza](./application-gateway-diagnostics.md). Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure. Te porty są chronione (zablokowane) przez certyfikaty platformy Azure. Bez odpowiednich certyfikatów jednostki zewnętrzne nie mogą inicjować zmian w tych punktach końcowych.
3. Zezwalaj na wychodzące sondy Azure Load Balancer (tag *AzureLoadBalancer* ) i ruchu przychodzącego sieci wirtualnej (tag *VirtualNetwork* ) w [sieciowej grupie zabezpieczeń](../virtual-network/network-security-groups-overview.md).
4. Blokuj cały ruch przychodzący za pomocą reguły Odmów.
5. Zezwalaj na ruch wychodzący do Internetu dla wszystkich miejsc docelowych.

## <a name="supported-user-defined-routes"></a>Obsługiwane trasy zdefiniowane przez użytkownika 

> [!IMPORTANT]
> Użycie UDR w podsieci Application Gateway może spowodować, że stan kondycji w [widoku kondycji wewnętrznej bazy](./application-gateway-diagnostics.md#back-end-health) danych będzie wyglądać jako **nieznany**. Może to spowodować, że generowanie dzienników Application Gateway i metryk kończy się niepowodzeniem. Zalecamy, aby nie używać UDR w podsieci Application Gateway, aby można było wyświetlić kondycję, dzienniki i metryki zaplecza.

- **wersjach**

   W przypadku jednostki SKU w wersji 1 trasy zdefiniowane przez użytkownika (UDR) są obsługiwane w podsieci Application Gateway, o ile nie zmieniają one kompleksowej komunikacji żądania/odpowiedzi. Na przykład można skonfigurować UDR w podsieci Application Gateway, aby wskazać urządzenie zapory na potrzeby inspekcji pakietów. Należy jednak upewnić się, że pakiet może dotrzeć do zamierzonego miejsca docelowego po inspekcji. Niewykonanie tej czynności może spowodować nieprawidłowe działanie sondowania kondycji lub routingu ruchu. Obejmuje to informacje o trasach lub domyślnych trasach 0.0.0.0/0, które są propagowane przez usługi Azure ExpressRoute lub bramy sieci VPN w sieci wirtualnej. Każdy scenariusz, w którym 0.0.0.0/0 musi być przekierowywany lokalnie (Wymuszone tunelowanie), nie jest obsługiwany w wersji 1.

- **v**

   W przypadku jednostki SKU v2 istnieją obsługiwane i nieobsługiwane scenariusze:

   **v2 obsługiwane scenariusze**
   > [!WARNING]
   > Niepoprawna konfiguracja tabeli tras może spowodować, że Routing asymetryczny w Application Gateway v2. Upewnij się, że cały ruch związany z zarządzaniem/płaszczyzną kontroli jest wysyłany bezpośrednio do Internetu, a nie za pomocą urządzenia wirtualnego. Może to również mieć na celu rejestrowanie i metryki.


  **Scenariusz 1**: UDR do wyłączenia propagacji tras Border Gateway Protocol (BGP) do podsieci Application Gateway

   Czasami trasy bramy domyślnej (0.0.0.0/0) są anonsowane za pośrednictwem bram ExpressRoute lub VPN skojarzonych z siecią wirtualną Application Gateway. Ten podział ruchu płaszczyzny zarządzania, który wymaga bezpośredniej ścieżki do Internetu. W takich scenariuszach UDR może służyć do wyłączania propagacji tras BGP. 

   Aby wyłączyć propagację tras BGP, wykonaj następujące czynności:

   1. Utwórz zasób tabeli tras na platformie Azure.
   2. Wyłącz parametr **propagacji trasy bramy sieci wirtualnej** . 
   3. Skojarz tabelę tras z odpowiednią podsiecią. 

   Włączenie UDR w tym scenariuszu nie należy przerywać żadnych istniejących ustawień.

  **Scenariusz 2**: UDR do bezpośredniego 0.0.0.0/0 do Internetu

   Można utworzyć UDR do wysyłania ruchu 0.0.0.0/0 bezpośrednio do Internetu. 

  **Scenariusz 3**: UDR dla usługi Azure Kubernetes z usługą korzystającą wtyczki kubenet

  Jeśli używasz korzystającą wtyczki kubenet z usługą Azure Kubernetes Service (AKS) i Application Gatewaym kontrolerem transferu danych przychodzących (AGIC), będziesz potrzebować tabeli tras, aby zezwolić na ruch wysyłany do tych zasobników z Application Gateway do kierowania do poprawnego węzła. Nie będzie to konieczne, jeśli używasz usługi Azure CNI. 

  Aby użyć tabeli tras, aby zezwolić korzystającą wtyczki kubenet na działanie, wykonaj następujące czynności:

  1. Przejdź do grupy zasobów utworzonej przez AKS (nazwa grupy zasobów powinna zaczynać się od "MC_").
  2. Znajdź tabelę tras utworzoną przez AKS w tej grupie zasobów. Tabelę tras należy wypełnić następującymi informacjami:
     - Prefiks adresu powinien być zakresem adresów IP, w którym chcesz dotrzeć do AKS. 
     - Typ następnego przeskoku powinien być urządzeniem wirtualnym. 
     - Adres następnego przeskoku powinien być adresem IP węzła obsługującym zasobniki.
  3. Skojarz tę tabelę tras z podsiecią Application Gatewayową. 
    
  **v2 nieobsługiwane scenariusze**

  **Scenariusz 1**: UDR dla urządzeń wirtualnych

  Każdy scenariusz, w którym 0.0.0.0/0 musi być przekierowywany za pomocą dowolnego urządzenia wirtualnego, sieci wirtualnej Hub/szprych lub lokalnego (tunelowanie wymuszone) nie jest obsługiwany w wersji 2.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej na temat konfiguracji adresu IP frontonu](configuration-front-end-ip.md).