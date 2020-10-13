---
title: Lista kontrolna przed wdrożeniem rozwiązań VMware platformy Azure
description: Użyj tej listy kontrolnej w ramach procesu planowania przed wdrożeniem.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91580023"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Lista kontrolna przed wdrożeniem rozwiązań VMware platformy Azure
Ta lista kontrolna przed wdrożeniem zostanie użyta podczas [fazy planowania](production-ready-deployment-steps.md).

## <a name="success-criteria"></a>Kryteria sukcesu
Zdefiniuj testy, które chcesz uruchomić, oraz oczekiwany wynik.

| Podstawowe informacje niezbędne | Twoja odpowiedź |
| ----------- | ------------- |
| Wdróż SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Tworzenie sieci wirtualnej | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Utwórz pole skoku | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Utwórz bramę sieci wirtualnej | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Połącz ExpressRoute Global Reach | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Logowanie do programu NSX Manager i programu vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Podstawowe informacje są zbędne | Twoja odpowiedź |
| --------| --------------|
| Utwórz serwer DHCP | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Utwórz segmenty sieci | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Skalowanie (Dodawanie lub usuwanie węzłów) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Wdrażanie programu VMware HCX | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Tworzenie maszyn wirtualnych | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Włącz Internet | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Migracja maszyny wirtualnej z chmury lokalnej do prywatnej | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Dodatkowe informacje są zbędne | Twoja odpowiedź |
| --------| --------------|
| Operacje migawek maszyn wirtualnych | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Wdróż moduł równoważenia obciążenia NSX-T | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Integracja z Azure<br><ul><li>Udostępniona biblioteka zawartości</li><li>Integracja zabezpieczeń</li><li>Przekaż plik ISO</li><li>Kompiluj z obrazu ISO</li><li>Azure Backup</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Micro segmentacja | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Routing | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Informacje o rozwiązaniu VMware platformy Azure

#### <a name="azure-subscription"></a>Subskrypcja platformy Azure
Podaj nazwę subskrypcji i Identyfikator subskrypcji rozwiązania Azure VMware. Subskrypcja może być nową lub istniejącą subskrypcją. Nie używaj subskrypcji deweloperskiej/testowej.

| Informacje wymagające  | Twoja odpowiedź |
| ------------| ------------- |
| Subskrypcja i identyfikator | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Subskrypcja z umową EA | ☐ Tak &nbsp; &nbsp; ☐ nie  |
| Nazwa grupy zasobów | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Lokalizacja | ☒ Wschodnie stany USA |
| Administrator platformy Azure<br><br>Podaj nazwę i kontakt z administratorem<br>przypisane do włączenia usługi z witryny Marketplace.<br>Współautor jest minimalnym rolą wymaganym przez <br>[zarejestruj dostawcę zasobów rozwiązań VMware platformy Azure](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>Informacje o lokalnym oprogramowaniu VMware

| Informacje wymagające  | Twoja odpowiedź |
| ------------| --------------|
| wersja vSphere | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| wersja programu vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Administrator vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Rozszerzenie L2<br><br>Jeśli rozszerzasz sieci L2 przy użyciu oprogramowania VMware HCX,<br>Podaj sieć lokalną, która zostanie rozszerzona. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| przełącznika wirtualnego<br><br>Podaj typ przełącznika vSwitch używanego w całym środowisku. | ☐ ☐ W warstwie Standardowa &nbsp; &nbsp;<br><br>W przypadku korzystania ze standardu program VMware HCX jest niedostępny. |
| DNS i DHCP<br><br>Wymagana jest właściwa infrastruktura DNS i DHCP. <br>Zaleca się używanie usługi DHCP wbudowanej w <br>NSX lub Użyj lokalnego serwera DHCP w chmurze prywatnej <br>zamiast routingu ruch DHCP emitowany przez <br>Z powrotem do sieci lokalnej. Aby uzyskać więcej informacji, <br>Zobacz [, jak utworzyć protokół DHCP i zarządzać nim w rozwiązaniu VMware platformy Azure](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Sieć — infrastruktura rozwiązań VMware platformy Azure 
Potrzebne dane mają na celu ułatwienie spełnienia wymagań w zakresie sieci rozwiązań VMware platformy Azure w celu rozłożenia i wstępnego testowania sieci. 

| Informacje wymagające | Twoja odpowiedź |
| ----------- | ------------- |
|CIDR rozwiązań VMware platformy Azure<br><br>Wymagane dla hostów vSphere, sieci vSAN i zarządzania <br>sieci w rozwiązaniu VMware platformy Azure. Aby uzyskać więcej informacji <br>informacje znajdują się w temacie [zagadnienia dotyczące routingu i podsieci](tutorial-network-checklist.md#routing-and-subnet-considerations). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Routing CIDR rozwiązań VMware platformy Azure (opcjonalnie)<br><br>Przypisywanie sieci do użycia w programie VMware platformy Azure<br>Rozwiązanie do wstępnego testowania. Maszyny wirtualne<br>zostanie wdrożone w celu zweryfikowania łączności sieciowej <br>Rozwiązanie z platformy Azure VMware. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Sieć — łączenie rozwiązań VMware platformy Azure z platformą Azure Virtual Network
Dane, które są niezbędne dla klastra rozwiązań VMware platformy Azure, są postawiliśmy, mogą być połączone z platformą Azure za pośrednictwem obwodu usługi ExpressRoute, który jest częścią usług Azure VMware Solution Service.

| Informacje wymagające | Twoja odpowiedź |
| ------------------ | ------------- |
| Pole skoku — system Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Tworzenie sieci wirtualnej | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Utwórz podsieć bramy | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Utwórz bramę sieci wirtualnej<br><br>Aby uzyskać więcej informacji, zobacz [Tworzenie bramy sieci wirtualnej](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Sieć — łączenie rozwiązania VMware z platformą Azure z lokalnym centrum danych

| Informacje wymagające | Twoja odpowiedź |
| ------------------ | ------------- |
| ExpressRoute komunikacji równorzędnej<br><br>`/29`Blok adresów CIDR. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute klucz autoryzacji i identyfikator zasobu<br><br>Podaj klucz autoryzacji i identyfikator zasobu, <br>który jest generowany na podstawie bieżącego ExpressRoute <br>Obwód, który łączy się z lokalnym centrum danych.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Kierunek trasy domyślnej<br><br>Czy maszyny wirtualne w rozwiązaniu VMware platformy Azure <br>dostęp do Internetu za pośrednictwem rozwiązania Azure VMware <br>Internet lub Wróć przez obwód ExpressRoute do <br>Czy w środowisku lokalnym dla trasy domyślnej? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Porty sieciowe wymagane do komunikowania się z usługą<br><br>Aby uzyskać więcej informacji, zobacz [wymagane porty sieciowe](tutorial-network-checklist.md#required-network-ports). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Sieć — VMware HCX

| Informacje wymagające | Twoja odpowiedź |
| ------------------ | ------------- |
| Porty sieciowe<br><br>Jeśli istnieje Zapora, upewnij się, że wymagane porty sieciowe <br>są otwierane między środowiskiem lokalnym i rozwiązaniem VMware platformy Azure. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>Aby uzyskać informacje na temat konfigurowania systemu DNS, <br>Zobacz [Sieć — infrastruktura rozwiązań VMware platformy Azure](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| HCXy CIDR VMware<br><br>Wymagane są dwa `/29` bloki CIDR, które są używane przez program <br>lokalne składniki infrastruktury VMware HCX.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

