---
title: Skonfiguruj publiczny adres IP wychodzący dla ISEs
description: Dowiedz się, jak skonfigurować jeden publiczny adres IP dla środowisk usług integracji (ISEs) w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e88c4bf05d88007a6e19b568f1bc1085e24b0325
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211060"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Skonfiguruj pojedynczy adres IP dla co najmniej jednego środowiska usługi integracji w Azure Logic Apps

Podczas pracy z Azure Logic Apps można skonfigurować [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) do hostowania aplikacji logiki, które wymagają dostępu do zasobów w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md). Jeśli masz wiele wystąpień ISE, które wymagają dostępu do innych punktów końcowych, które mają ograniczenia adresów IP, wdróż [zaporę platformy Azure](../firewall/overview.md) lub [sieciowe urządzenie wirtualne](../virtual-network/virtual-networks-overview.md#filter-network-traffic) w sieci wirtualnej i Roześlij ruch wychodzący za pomocą tej zapory lub wirtualnego urządzenia sieciowego. Następnie można utworzyć wszystkie wystąpienia ISE w sieci wirtualnej z użyciem jednego, publicznego, statycznego i przewidywalnego adresu IP do komunikacji z żądanymi systemami docelowymi. Dzięki temu nie trzeba konfigurować dodatkowych otwartych zapór w systemach docelowych dla każdego ISEu.

W tym temacie pokazano, jak skierować ruch wychodzący za pośrednictwem zapory platformy Azure, ale możesz zastosować podobne koncepcje do sieciowego urządzenia wirtualnego, takiego jak zapora innej firmy z portalu Azure Marketplace. Ten temat koncentruje się na konfiguracji wielu wystąpień ISE, ale można również użyć tej metody dla jednego ISEu, gdy scenariusz wymaga ograniczenia liczby adresów IP, które wymagają dostępu. Należy rozważyć, czy dodatkowe koszty zapory lub urządzenia sieci wirtualnej mają sens dla danego scenariusza. Dowiedz się więcej o [cenach zapory platformy Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Wymagania wstępne

* Zapora platformy Azure działająca w tej samej sieci wirtualnej co ISE. Jeśli nie masz zapory, najpierw [Dodaj podsieć](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) o nazwie `AzureFirewallSubnet` do sieci wirtualnej. Następnie możesz [utworzyć i wdrożyć zaporę](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) w sieci wirtualnej.

* [Tabela tras](../virtual-network/manage-route-table.md)platformy Azure. Jeśli nie masz takiego, należy najpierw [utworzyć tabelę tras](../virtual-network/manage-route-table.md#create-a-route-table). Aby uzyskać więcej informacji na temat routingu, zobacz [routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Konfigurowanie tabeli tras

1. W [Azure Portal](https://portal.azure.com)wybierz tabelę tras, na przykład:

   ![Wybierz tabelę tras z regułą dla ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Aby [dodać nową trasę](../virtual-network/manage-route-table.md#create-a-route), w menu tabela tras wybierz pozycję **trasy**  >  **Dodaj**.

   ![Dodawanie trasy do kierowania ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. W okienku **Dodawanie trasy** [Skonfiguruj nową trasę](../virtual-network/manage-route-table.md#create-a-route) z regułą określającą, że cały ruch wychodzący do systemu docelowego jest zgodny z tym zachowaniem:

   * Używa [**urządzenia wirtualnego**](../virtual-network/virtual-networks-udr-overview.md#user-defined) jako typu następnego przeskoku.

   * Przechodzi do prywatnego adresu IP dla wystąpienia zapory jako adres następnego przeskoku.

     Aby znaleźć ten adres IP, w menu zapory wybierz pozycję **Przegląd**, Znajdź adres w obszarze **prywatny adres IP**, na przykład:

     ![Znajdowanie prywatnego adresu IP zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Oto przykład, który pokazuje, jak taka reguła może wyglądać:

   ![Konfigurowanie reguły kierowania ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa trasy** | <*Unikatowa nazwa trasy*> | Unikatowa nazwa trasy w tabeli tras |
   | **Prefiks adresu** | <*adres docelowy*> | Prefiks adresu dla systemu docelowego, do którego ma nastąpić ruch wychodzący. Upewnij się, że dla tego adresu jest używana [notacja CIDR (classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . W tym przykładzie ten prefiks adresu jest przeznaczony dla serwera SFTP, który jest opisany w sekcji [Konfigurowanie reguły sieci](#set-up-network-rule). |
   | **Typ następnego przeskoku** | **Urządzenie wirtualne** | [Typ przeskoku](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) używany przez ruch wychodzący |
   | **Adres następnego skoku** | <*Zapora-Private-IP-Address*> | Prywatny adres IP dla zapory |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Konfigurowanie reguły sieci

1. W Azure Portal Znajdź i wybierz zaporę. W menu Zapora w obszarze **Ustawienia** wybierz pozycję **reguły**. W okienku reguły wybierz pozycję **Kolekcja reguł sieciowych**  >  **Dodaj kolekcję reguł sieci**.

   ![Dodawanie kolekcji reguł sieci do zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. W kolekcji Dodaj regułę zezwalającą na ruch do systemu docelowego.

   Załóżmy na przykład, że masz aplikację logiki, która działa w ISE i musi komunikować się z serwerem SFTP. Tworzysz kolekcję reguł sieci o nazwie `LogicApp_ISE_SFTP_Outbound` , która zawiera regułę sieci o nazwie `ISE_SFTP_Outbound` . Ta zasada zezwala na ruch z adresu IP każdej podsieci, w której ISE działa w sieci wirtualnej na docelowy serwer SFTP przy użyciu prywatnego adresu IP zapory.

   ![Konfigurowanie reguły sieci dla zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Właściwości kolekcji reguł sieci**

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*Sieć-reguła-kolekcja-nazwa*> | Nazwa kolekcji reguł sieci |
   | **Priority** | <*priorytet — poziom*> | Kolejność, w której ma zostać uruchomiona Kolekcja reguł. Aby uzyskać więcej informacji, zobacz [co to są niektóre zagadnienia dotyczące zapory platformy Azure](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)? |
   | **Akcja** | **Zezwalaj** | Typ akcji do wykonania dla tej reguły |
   |||

   **Właściwości reguły sieci**

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*Nazwa reguły sieci*> | Nazwa reguły sieci |
   | **Protokół** | <*połączenia — protokoły*> | Protokoły połączeń do użycia. Na przykład jeśli korzystasz z reguł sieciowej grupy zabezpieczeń, wybierz zarówno **TCP** , jak i **UDP**, a nie tylko **TCP**. |
   | **Adresy źródłowe** | <*ISE — adresy podsieci*> | Adresy IP podsieci, w których są uruchamiane ISE oraz miejsce, w którym pochodzą ruch z aplikacji logiki |
   | **Adresy docelowe** | <*Destination-IP-Address*> | Adres IP systemu docelowego, do którego ma nastąpić ruch wychodzący. W tym przykładzie ten adres IP jest przeznaczony dla serwera SFTP. |
   | **Porty docelowe** | <*porty docelowe*> | Wszystkie porty używane przez system docelowy na potrzeby komunikacji przychodzącej |
   |||

   Aby uzyskać więcej informacji na temat reguł sieci, zobacz następujące artykuły:

   * [Konfigurowanie reguły sieci](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logika przetwarzania reguł usługi Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Zapora platformy Azure — często zadawane pytania](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Interfejs wiersza polecenia platformy Azure: AZ Network firewall Network-Rule](/cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Następne kroki

* [Łączenie z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
