---
title: Konfigurowanie publicznego wychodzącego adresu IP dla platform ISE
description: Dowiedz się, jak skonfigurować jeden publiczny wychodzący adres IP dla środowisk usług integracji (ISE) w Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7ec495dd52607f2f65e0bef50489dd182c2a3253
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874193"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Skonfiguruj jeden adres IP dla co najmniej jednego środowiska usługi integracji w Azure Logic Apps

Podczas pracy z usługą Azure Logic Apps można skonfigurować środowisko usługi integracji [  (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) do hostowania aplikacji logiki, które wymagają dostępu do zasobów w sieci [wirtualnej platformy Azure.](../virtual-network/virtual-networks-overview.md) W przypadku wielu wystąpień środowiska ISE, które wymagają dostępu do innych punktów końcowych [](../virtual-network/virtual-networks-overview.md#filter-network-traffic) z ograniczeniami adresów IP, należy wdrożyć urządzenie [wirtualne](../firewall/overview.md) Azure Firewall lub sieciowe w sieci wirtualnej i przekierowyć ruch wychodzący przez zaporę lub sieciowe urządzenie wirtualne. Następnie wszystkie wystąpienia środowiska ISE w sieci wirtualnej mogą używać jednego publicznego, statycznego i przewidywalnego adresu IP do komunikowania się z systemami docelowymi, które mają być. Dzięki temu nie trzeba skonfigurować dodatkowych otworów zapory w systemach docelowych dla każdego programu ISE.

W tym temacie przedstawiono sposób rozsyłania ruchu wychodzącego przez usługę Azure Firewall, ale podobne koncepcje można zastosować do sieciowego urządzenia wirtualnego, takiego jak zapora innej firmy z Azure Marketplace. Chociaż ten temat koncentruje się na konfiguracji dla wielu wystąpień ise, można również użyć tego podejścia dla jednego rozwiązania ISE, gdy scenariusz wymaga ograniczenia liczby adresów IP, które wymagają dostępu. Zastanów się, czy dodatkowe koszty zapory lub wirtualnego urządzenia sieciowego mają sens dla twojego scenariusza. Dowiedz się więcej o [Azure Firewall usługi .](https://azure.microsoft.com/pricing/details/azure-firewall/)

## <a name="prerequisites"></a>Wymagania wstępne

* Zapora platformy Azure, która działa w tej samej sieci wirtualnej co twoja platforma ISE. Jeśli nie masz zapory, najpierw [](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) dodaj podsieć o nazwie `AzureFirewallSubnet` do sieci wirtualnej. Następnie możesz [utworzyć i wdrożyć zaporę w](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) sieci wirtualnej.

* Tabela tras [platformy](../virtual-network/manage-route-table.md)Azure. Jeśli jej nie masz, najpierw [utwórz tabelę tras](../virtual-network/manage-route-table.md#create-a-route-table). Aby uzyskać więcej informacji na temat routingu, zobacz [Routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Konfigurowanie tabeli tras

1. W [Azure Portal](https://portal.azure.com)wybierz tabelę tras, na przykład:

   ![Wybieranie tabeli tras z regułą kierowania ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Aby [dodać nową trasę](../virtual-network/manage-route-table.md#create-a-route), w menu tabeli tras wybierz pozycję **Trasy**  >  **Dodaj**.

   ![Dodawanie trasy do kierowania ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. W **okienku Dodawanie** trasy skonfiguruj [nową](../virtual-network/manage-route-table.md#create-a-route) trasę przy użyciu reguły, która określa, że cały ruch wychodzący do systemu docelowego jest zgodny z tym zachowaniem:

   * Używa urządzenia [**wirtualnego**](../virtual-network/virtual-networks-udr-overview.md#user-defined) jako typu następnego przeskoku.

   * Przechodzi do prywatnego adresu IP wystąpienia zapory jako adres następnego przeskoku.

     Aby znaleźć ten adres IP, w menu zapory wybierz pozycję **Przegląd**, znajdź adres w obszarze **Prywatny adres IP,** na przykład:

     ![Znajdowanie prywatnego adresu IP zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Oto przykład, który pokazuje, jak może wyglądać taka reguła:

   ![Konfigurowanie reguły kierowania ruchu wychodzącego](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa trasy** | <*unique-route-name*> | Unikatowa nazwa trasy w tabeli tras |
   | **Prefiks adresu** | <*adres docelowy*> | Prefiks adresu dla systemu docelowego, w którym ma odbywać się ruch wychodzący. Upewnij się, że dla tego adresu [używasz notacji CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) W tym przykładzie ten prefiks adresu dotyczy serwera SFTP, co opisano w sekcji [Konfigurowanie reguły sieci](#set-up-network-rule). |
   | **Typ następnego przeskoku** | **Urządzenie wirtualne** | Typ [przeskoku](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) używany przez ruch wychodzący |
   | **Adres następnego skoku** | <*firewall-private-IP-address*> | Prywatny adres IP zapory |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Konfigurowanie reguły sieci

1. W Azure Portal znajdź i wybierz zaporę. W menu zapory w obszarze **Ustawienia wybierz** pozycję **Reguły.** W okienku reguł wybierz pozycję **Kolekcja reguł sieci** Dodaj  >  **kolekcję reguł sieci.**

   ![Dodawanie kolekcji reguł sieci do zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. W kolekcji dodaj regułę, która zezwala na ruch do systemu docelowego.

   Załóżmy na przykład, że masz aplikację logiki, która działa na platformie ISE i musi komunikować się z serwerem SFTP. Utworzysz kolekcję reguł sieci o nazwie `LogicApp_ISE_SFTP_Outbound` , która zawiera regułę sieci o nazwie `ISE_SFTP_Outbound` . Ta reguła zezwala na ruch z adresu IP dowolnej podsieci, w której jest uruchomiony program ISE w sieci wirtualnej, do docelowego serwera SFTP przy użyciu prywatnego adresu IP zapory.

   ![Konfigurowanie reguły sieci dla zapory](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Właściwości kolekcji reguł sieci**

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*nazwa-kolekcji-reguł sieci*> | Nazwa kolekcji reguł sieci |
   | **Priority** | <*poziom priorytetu*> | Kolejność priorytetów do użycia podczas uruchamiania kolekcji reguł. Aby uzyskać więcej informacji, zobacz What are some some Azure Firewall concepts ? [(Jakie są Azure Firewall pojęcia?](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts) |
   | **Akcja** | **Zezwalaj** | Typ akcji do wykonania dla tej reguły |
   |||

   **Właściwości reguły sieci**

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*nazwa-reguły-sieci*> | Nazwa reguły sieci |
   | **Protokół** | <*protokoły połączeń*> | Protokoły połączenia do użycia. Jeśli na przykład używasz reguł sieciowej organizacji, wybierz zarówno **protokół TCP,** jak i **UDP,** a nie tylko **protokół TCP.** |
   | **Adresy źródłowe** | <*Adresy podsieci ISE*> | Adresy IP podsieci, w których działa twoje urządzenie ISE i skąd pochodzi ruch z aplikacji logiki |
   | **Adresy docelowe** | <*docelowy adres IP*> | Adres IP systemu docelowego, do którego ma trafiać ruch wychodzący. W tym przykładzie ten adres IP jest dla serwera SFTP. |
   | **Porty docelowe** | <*porty docelowe*> | Wszystkie porty używane przez system docelowy do komunikacji przychodzącej |
   |||

   Aby uzyskać więcej informacji na temat reguł sieci, zobacz następujące artykuły:

   * [Konfigurowanie reguły sieci](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logika przetwarzania reguł usługi Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Zapora platformy Azure — często zadawane pytania](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Interfejs wiersza polecenia platformy Azure: az network firewall network-rule](/cli/azure/network/firewall/network-rule#az_network_firewall_network_rule_create)

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
