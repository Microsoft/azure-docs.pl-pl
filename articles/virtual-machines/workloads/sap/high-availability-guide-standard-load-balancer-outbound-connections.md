---
title: Publiczna łączność z punktem końcowym dla maszyn wirtualnych platformy Azure&Standard ILB w scenariuszach dotyczących oprogramowania SAP HA
description: Publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/16/2020
ms.author: radeltch
ms.openlocfilehash: a6b62e9c894c25b2c3cd064524881ae5db51ec5a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968540"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP

Zakres tego artykułu opisuje konfiguracje, które umożliwią łączność wychodzącą z publicznymi punktami końcowymi. Konfiguracje są głównie w kontekście wysokiej dostępności z Pacemaker dla SUSE/RHEL.  

Jeśli używasz Pacemaker z agentem usługi Azure ogrodzeniem w rozwiązaniu wysokiej dostępności, maszyny wirtualne muszą mieć łączność wychodzącą z interfejsem API zarządzania platformy Azure.  
Artykuł zawiera kilka opcji umożliwiających wybranie opcji najlepiej dopasowanej do danego scenariusza.  

## <a name="overview"></a>Omówienie

W przypadku implementowania wysokiej dostępności dla rozwiązań SAP za pośrednictwem klastrowania jeden z niezbędnych składników jest [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Platforma Azure oferuje dwie jednostki SKU modułu równoważenia obciążenia: standardowa i podstawowa.

Moduł równoważenia obciążenia w warstwie Standardowa platformy Azure oferuje pewne korzyści w stosunku do podstawowego modułu równoważenia obciążenia. Na przykład działa w strefach dostępności platformy Azure, oferuje lepsze możliwości monitorowania i rejestrowania w celu łatwiejszego rozwiązywania problemów i skrócenia opóźnień. Funkcja "porty HA" obejmuje wszystkie porty, czyli nie jest już konieczne wyświetlanie listy wszystkich poszczególnych portów.  

Istnieją pewne istotne różnice między podstawową i standardową jednostką SKU modułu równoważenia obciążenia platformy Azure. Jednym z nich jest obsługa ruchu wychodzącego do publicznego punktu końcowego. Aby zapoznać się z pełnym porównaniem modułu równoważenia obciążenia z podstawową jednostką SKU, zobacz [Load Balancer porównanie jednostek SKU](../../../load-balancer/load-balancer-overview.md).  
 
Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie istnieje łączność wychodząca z publicznymi punktami końcowymi, chyba że dodatkowa konfiguracja zostanie ukończona.  

Jeśli do maszyny wirtualnej jest przypisany publiczny adres IP lub maszyna wirtualna znajduje się w puli zaplecza modułu równoważenia obciążenia z publicznym adresem IP, będzie mieć łączność wychodzącą z publicznymi punktami końcowymi.  

Systemy SAP często zawierają poufne dane biznesowe. Są one rzadko akceptowane w przypadku maszyn wirtualnych obsługujących systemy SAP w celu posiadania publicznych adresów IP. W tym samym czasie istnieją scenariusze, które wymagają połączenia wychodzącego z maszyny wirtualnej z publicznymi punktami końcowymi.  

Przykłady scenariuszy, które wymagają dostępu do publicznego punktu końcowego platformy Azure, to:  
- Używanie agenta usługi Azure ogrodzenia jako mechanizmu ogrodzenia w klastrach Pacemaker
- Azure Backup
- Azure Site Recovery  
- Używanie repozytorium publicznego na potrzeby stosowania poprawek do systemu operacyjnego
- Przepływ danych aplikacji SAP może wymagać łączności wychodzącej z publicznym punktem końcowym

Jeśli wdrożenie SAP nie wymaga łączności wychodzącej z publicznymi punktami końcowymi, nie trzeba implementować dodatkowej konfiguracji. Jest to wystarczające do utworzenia wewnętrznej standardowej Azure Load Balancer jednostki SKU dla scenariusza wysokiej dostępności, przy założeniu, że nie ma także potrzeby łączności przychodzącej z publicznych punktów końcowych.  

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych.  
>Jeśli maszyny wirtualne mają publiczne adresy IP lub znajdują się już w puli zaplecza modułu równoważenia obciążenia platformy Azure z publicznym adresem IP, maszyna wirtualna będzie miała już łączność wychodzącą z publicznymi punktami końcowymi.


Przeczytaj najpierw następujące dokumenty:

* usługa Load Balancer w warstwie Standardowa platformy Azure
  * [Omówienie usługi azure usługa Load Balancer w warstwie Standardowa](../../../load-balancer/load-balancer-overview.md) — kompleksowy przegląd modułu równoważenia obciążenia systemu Azure w warstwie Standardowa, ważnych zasad, koncepcji i samouczków 
  * [Połączenia wychodzące na platformie Azure](../../../load-balancer/load-balancer-outbound-connections.md#scenarios) — scenariusze dotyczące uzyskiwania łączności wychodzącej na platformie Azure
  * [Reguły ruchu wychodzącego modułu równoważenia obciążenia](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules)— wyjaśnia koncepcje reguł ruchu wychodzącego modułu równoważenia obciążenia oraz sposób tworzenia reguł wychodzących
* Azure Firewall
  * [Omówienie Zapory platformy Azure](../../../firewall/overview.md)— Omówienie Zapory platformy Azure
  * [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure](../../../firewall/tutorial-firewall-deploy-portal.md) — instrukcje dotyczące konfigurowania zapory platformy Azure za pośrednictwem Azure Portal
* [Sieci wirtualne — reguły zdefiniowane przez użytkownika](../../../virtual-network/virtual-networks-udr-overview.md#user-defined) — koncepcje i reguły routingu platformy Azure  
* [Tagi usługi grup zabezpieczeń](../../../virtual-network/network-security-groups-overview.md#service-tags) — jak uprościć sieciowe grupy zabezpieczeń i konfigurację zapory za pomocą tagów usługi

## <a name="additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Dodatkowe zewnętrzne usługa Load Balancer w warstwie Standardowa platformy Azure dla połączeń wychodzących z Internetem

Jedną z opcji zapewnienia łączności wychodzącej z publicznymi punktami końcowymi, bez zezwalania na połączenia przychodzące z maszyną wirtualną z publicznego punktu końcowego, jest utworzenie drugiego modułu równoważenia obciążenia z publicznym adresem IP, dodanie maszyn wirtualnych do puli zaplecza drugiego modułu równoważenia obciążenia i zdefiniowanie tylko [reguł ruchu wychodzącego](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules).  
Używaj [sieciowych grup zabezpieczeń](../../../virtual-network/network-security-groups-overview.md) do kontrolowania publicznych punktów końcowych, które są dostępne dla wywołań wychodzących z maszyny wirtualnej.  
Aby uzyskać więcej informacji, zobacz scenariusz 2 w dokumencie [połączenia wychodzące](../../../load-balancer/load-balancer-outbound-connections.md#scenarios).  
Konfiguracja będzie wyglądać następująco:  

![Kontrola łączności z publicznymi punktami końcowymi przy użyciu sieciowych grup zabezpieczeń](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Istotne zagadnienia

- Możesz użyć jednej z dodatkowych Load Balancer publicznych dla wielu maszyn wirtualnych w tej samej podsieci, aby uzyskać łączność wychodzącą z publicznym punktem końcowym i zoptymalizować koszty  
- Używaj [sieciowych grup zabezpieczeń](../../../virtual-network/network-security-groups-overview.md) do kontrolowania, które publiczne punkty końcowe są dostępne z maszyn wirtualnych. Można przypisać sieciową grupę zabezpieczeń do podsieci lub do każdej maszyny wirtualnej. Jeśli to możliwe, użyj [tagów usługi](../../../virtual-network/network-security-groups-overview.md#service-tags) , aby zmniejszyć złożoność reguł zabezpieczeń.  
- Moduł równoważenia obciążenia w warstwie Standardowa platformy Azure z publicznym adresem IP i regułami ruchu wychodzącego umożliwia bezpośredni dostęp do publicznego punktu końcowego. Jeśli istnieją wymagania dotyczące zabezpieczeń firmy, aby cały ruch wychodzący był realizowany za pośrednictwem scentralizowanego rozwiązania firmy na potrzeby inspekcji i rejestrowania, może nie być możliwe spełnienie wymagań w tym scenariuszu.  

>[!TIP]
>Jeśli to możliwe, użyj [tagów usługi](../../../virtual-network/network-security-groups-overview.md#service-tags) , aby zmniejszyć złożoność sieciowej grupy zabezpieczeń. 

### <a name="deployment-steps"></a>Kroki wdrażania

1. Utwórz Load Balancer  
   1. W [Azure Portal](https://portal.azure.com) kliknij pozycję wszystkie zasoby, Dodaj, a następnie wyszukaj **Load Balancer**  
   1. Kliknij pozycję **Utwórz** 
   1. Nazwa Load Balancer **MyPublicILB**  
   1. Wybierz opcję **Public** as Type, **Standard** as SKU  
   1. Wybierz pozycję **Utwórz publiczny adres IP** i określ jako nazwę **MyPublicILBFrondEndIP**  
   1. Wybierz strefę **nadmiarową** jako strefa dostępności  
   1. Kliknij przycisk Przeglądaj i Utwórz, a następnie kliknij przycisk Utwórz.  
2. Utwórz **MyBackendPoolOfPublicILB** puli zaplecza i Dodaj maszyny wirtualne.  
   1. Wybierz sieć wirtualną  
   1. Wybierz Maszyny wirtualne i ich adresy IP, a następnie dodaj je do puli zaplecza  
3. [Utwórz reguły ruchu wychodzącego](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration). Obecnie nie można tworzyć reguł wychodzących na podstawie Azure Portal. Reguły ruchu wychodzącego można tworzyć przy użyciu [interfejsu wiersza polecenia platformy Azure](../../../cloud-shell/overview.md?view=azure-cli-latest).  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Utwórz reguły sieciowej grupy zabezpieczeń, aby ograniczyć dostęp do określonych publicznych punktów końcowych. Jeśli istnieje sieciowa Grupa zabezpieczeń, możesz ją dostosować. W poniższym przykładzie pokazano, jak włączyć dostęp do interfejsu API zarządzania platformy Azure: 
   1. Przejdź do sieciowej grupy zabezpieczeń
   1. Kliknij pozycję Reguły zabezpieczeń dla ruchu wychodzącego
   1. Dodaj regułę, aby **odmówić** wszystkim dostępowi wychodzącemu do **Internetu**.
   1. Dodaj regułę, aby **zezwolić** na dostęp do **AzureCloud**, z priorytetem niższym od priorytetu reguły, aby odmówić dostępu do Internetu.


   Reguły zabezpieczeń dla ruchu wychodzącego będą wyglądać następująco: 

   ![Połączenie wychodzące z drugim Load Balancer z publicznym adresem IP](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Aby uzyskać więcej informacji na temat grup zabezpieczeń sieci platformy Azure, zobacz [grupy zabezpieczeń ](../../../virtual-network/network-security-groups-overview.md). 

## <a name="azure-firewall-for-outbound-connections-to-internet"></a>Zapora platformy Azure dla połączeń wychodzących z Internetem

Kolejną opcją zapewnienia łączności wychodzącej z publicznymi punktami końcowymi, bez zezwalania na połączenia przychodzące z maszyną wirtualną z publicznych punktów końcowych, jest Zapora platformy Azure. Zapora systemu Azure to zarządzana usługa z wbudowaną wysoką dostępnością i może obejmować wiele Strefy dostępności.  
Konieczne będzie również wdrożenie [trasy zdefiniowanej przez użytkownika](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes)skojarzonej z podsiecią, w której wdrożono maszyny wirtualne i moduł równoważenia obciążenia platformy Azure, wskazując na zaporę platformy Azure, aby kierować ruchem za pośrednictwem zapory platformy Azure.  
Aby uzyskać szczegółowe informacje na temat wdrażania zapory platformy Azure, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure](../../../firewall/tutorial-firewall-deploy-portal.md).  

Architektura będzie wyglądać następująco:

![Połączenie wychodzące z zaporą platformy Azure](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Istotne zagadnienia

- Zapora systemu Azure to usługa natywna w chmurze z wbudowaną wysoką dostępnością i obsługuje wdrożenie strefowe.
- Wymaga dodatkowej podsieci, która musi mieć nazwę AzureFirewallSubnet. 
- W przypadku przenoszenia dużych zestawów danych wychodzących z sieci wirtualnej, w której znajdują się maszyny wirtualne SAP, do maszyny wirtualnej w innej sieci wirtualnej lub do publicznego punktu końcowego, może to nie być ekonomiczne rozwiązanie. Jeden z takich przykładów kopiuje duże kopie zapasowe w sieciach wirtualnych. Aby uzyskać szczegółowe informacje, zobacz cennik zapory platformy Azure.  
- Jeśli firmowe rozwiązanie zapory nie jest zaporą platformy Azure i masz wymagania dotyczące zabezpieczeń, aby cały ruch wychodzący był przekazywany przez centralne rozwiązanie korporacyjne, rozwiązanie to może nie być praktyczne.  

>[!TIP]
>Jeśli to możliwe, użyj [tagów usługi](../../../virtual-network/network-security-groups-overview.md#service-tags) , aby zmniejszyć złożoność reguł zapory platformy Azure.  

### <a name="deployment-steps"></a>Kroki wdrażania

1. W ramach kroków wdrożenia przyjęto, że masz już zdefiniowaną sieć wirtualną i podsieć dla maszyn wirtualnych.  
2. Utwórz podsieć **AzureFirewallSubnet** w tym samym Virtual Network, w którym są WDROŻONE maszyny wirtualne i usługa Load Balancer w warstwie Standardowa.  
   1. W Azure Portal przejdź do Virtual Network: kliknij pozycję wszystkie zasoby, Wyszukaj Virtual Network, kliknij Virtual Network, wybierz pozycję podsieci.  
   1. Kliknij przycisk Dodaj podsieć. Wprowadź **AzureFirewallSubnet** jako nazwę. Wprowadź odpowiedni zakres adresów. Zapisz zmiany.  
3. Utwórz zaporę platformy Azure.  
   1. W Azure Portal zaznacz wszystkie zasoby, kliknij przycisk Dodaj, Zapora, Utwórz. Wybierz grupę zasobów (Wybierz tę samą grupę zasobów, w której znajduje się Virtual Network).  
   1. Wprowadź nazwę zasobu zapory platformy Azure. Na przykład **MyAzureFirewall**.  
   1. Wybierz opcję Region i wybierz co najmniej dwie strefy dostępności wyrównane z strefami dostępności, w których wdrożono maszyny wirtualne.  
   1. Wybierz Virtual Network, w którym są wdrażane maszyny wirtualne SAP i usługa Azure Standard load module.  
   1. Publiczny adres IP: kliknij przycisk Utwórz, a następnie wprowadź nazwę. Na przykład **MyFirewallPublicIP**.  
4. Utwórz regułę zapory platformy Azure, aby zezwolić na połączenia wychodzące z określonymi publicznymi punktami końcowymi. W przykładzie pokazano, jak zezwolić na dostęp do publicznego punktu końcowego interfejsu API zarządzania platformy Azure.  
   1. Wybierz pozycję reguły, Kolekcja reguł sieci, a następnie kliknij pozycję Dodaj kolekcję reguł sieci.  
   1. Nazwa: **MyOutboundRule**, wprowadź priorytet, wybierz pozycję Akcja **Zezwalaj**.  
   1. Usługa: nazwa **ToAzureAPI**.  Protokół: Wybierz **dowolny**. Adres źródłowy: Wprowadź zakres podsieci, w której wdrożono maszyny wirtualne i usługa Load Balancer w warstwie Standardowa na przykład: **11.97.0.0/24**. Porty docelowe: wprowadź <b>*</b> .  
   1. Zapisz
   1. Ponieważ nadal umieszczasz w zaporze platformy Azure, wybierz pozycję przegląd. Zanotuj prywatny adres IP zapory platformy Azure.  
5. Tworzenie trasy do zapory platformy Azure  
   1. W Azure Portal wybierz pozycję wszystkie zasoby, a następnie kliknij pozycję Dodaj, Roześlij tabelę, Utwórz.  
   1. Wprowadź nazwę Moje trasy, wybierz opcję subskrypcja, Grupa zasobów i lokalizacja (zgodna z lokalizacją sieci wirtualnej i zapory).  
   1. Zapisz  

   Reguła zapory będzie wyglądać następująco: ![ diagram pokazujący, jak będzie wyglądać Zapora.](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Utwórz trasę zdefiniowaną przez użytkownika z podsieci maszyn wirtualnych do prywatnego adresu IP **MyAzureFirewall**.
   1. Po umieszczeniu w tabeli tras kliknij pozycję trasy. Wybierz pozycję Dodaj. 
   1. Nazwa trasy: ToMyAzureFirewall, prefiks adresu: **0.0.0.0/0**. Typ następnego przeskoku: wybierz pozycję urządzenie wirtualne. Adres następnego przeskoku: wprowadź prywatny adres IP skonfigurowanej zapory: **11.97.1.4**.  
   1. Zapisz

## <a name="using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Używanie serwera proxy dla wywołań Pacemaker do interfejsu API zarządzania platformy Azure

Serwera proxy można użyć do zezwalania na wywołania Pacemaker do publicznego punktu końcowego interfejsu API zarządzania platformy Azure.  

### <a name="important-considerations"></a>Istotne zagadnienia

  - Jeśli istnieje już firmowy serwer proxy, możesz kierować wywołania wychodzące do publicznych punktów końcowych. Wywołania wychodzące do publicznych punktów końcowych przechodzą przez punkt kontroli firmowej.  
  - Upewnij się, że konfiguracja serwera proxy umożliwia łączność wychodzącą z interfejsem API zarządzania platformy Azure: `https://management.azure.com` i `https://login.microsoftonline.com`  
  - Upewnij się, że istnieje trasa z maszyn wirtualnych do serwera proxy  
  - Serwer proxy będzie obsługiwał tylko wywołania HTTP/HTTPS. Jeśli istnieje dodatkowe zapotrzebowanie na wywołania wychodzące do publicznego punktu końcowego przez różne protokoły (takie jak RFC), wymagane jest alternatywne rozwiązanie  
  - Rozwiązanie serwera proxy musi być wysoce dostępne, aby uniknąć niestabilności w klastrze Pacemaker  
  - W zależności od lokalizacji serwera proxy może nawiązać dodatkowe opóźnienie wywołań z agenta usługi Azure ogrodzenia do interfejsu API zarządzania platformy Azure. Jeśli Twój firmowy serwer proxy nadal znajduje się w środowisku lokalnym, podczas gdy klaster Pacemaker jest na platformie Azure, Zmierz opóźnienie i rozważ, jeśli to rozwiązanie jest odpowiednie dla Ciebie  
  - Jeśli na miejscu nie ma jeszcze firmowej serwera proxy o wysokiej dostępności, nie zalecamy tej opcji, ponieważ klient będzie ponoszenia dodatkowych kosztów i złożoności. Niemniej jednak w przypadku podjęcia decyzji o wdrożeniu dodatkowego rozwiązania serwera proxy na potrzeby zezwalania na połączenia wychodzące z usługi Pacemaker do publicznego interfejsu API zarządzania Azure upewnij się, że serwer proxy ma wysoką dostępność, a opóźnienie z maszyn wirtualnych do serwera proxy jest niskie.  

### <a name="pacemaker-configuration-with-proxy"></a>Konfiguracja Pacemaker z serwerem proxy 

W branży dostępnych jest wiele różnych opcji serwera proxy. Instrukcje krok po kroku dotyczące wdrażania serwera proxy wykraczają poza zakres tego dokumentu. W poniższym przykładzie przyjęto założenie, że serwer proxy odpowiada na **MyProxyService** i nasłuchuje na porcie **MyProxyPort**.  
Aby umożliwić usłudze Pacemaker komunikowanie się z interfejsem API zarządzania platformy Azure, wykonaj następujące czynności na wszystkich węzłach klastra:  

1. Edytuj plik konfiguracji Pacemaker/etc/sysconfig/Pacemaker i Dodaj następujące wiersze (wszystkie węzły klastra):

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Uruchom ponownie usługę Pacemaker na **wszystkich** węzłach klastra.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-solutions"></a>Inne rozwiązania

Jeśli ruch wychodzący jest kierowany za pośrednictwem zapory innej firmy:

- w przypadku korzystania z agenta usługi Azure ogrodzenia upewnij się, że konfiguracja zapory zezwala na łączność wychodzącą z interfejsem API zarządzania platformy Azure: `https://management.azure.com` i `https://login.microsoftonline.com`   
- Jeśli korzystasz z infrastruktury aktualizacji chmury publicznej platformy Azure do stosowania aktualizacji i poprawek, zobacz [infrastruktura aktualizacji chmury publicznej platformy azure 101](https://suse.com/c/azure-public-cloud-update-infrastructure-101/)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak skonfigurować Pacemaker na SUSE na platformie Azure](./high-availability-guide-suse-pacemaker.md)
* [Dowiedz się, jak skonfigurować Pacemaker na Red Hat na platformie Azure](./high-availability-guide-rhel-pacemaker.md)