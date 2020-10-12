---
title: Dostęp do sieci wirtualnych platformy Azure
description: Omówienie sposobu, w jaki środowiska usług Integration Service (ISEs) ułatwiają aplikacjom logiki dostęp do sieci wirtualnych platformy Azure (sieci wirtualnych)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 07/05/2020
ms.openlocfilehash: 86d647a79b7babc2780cb0db904e689f3916673f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500389"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów platformy Azure Virtual Network z Azure Logic Apps przy użyciu środowisk usługi integracji (ISEs)

Czasami Aplikacje logiki muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne i inne systemy lub usługi, które znajdują się w [sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md)lub są z nią połączone. Aby skonfigurować ten dostęp, można [utworzyć *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). ISE to wystąpienie usługi Logic Apps, która używa dedykowanych zasobów i jest uruchamiane niezależnie od "globalnej" usługi Logic Apps z wieloma dzierżawcami.

Na przykład niektóre sieci wirtualne platformy Azure używają prywatnych punktów końcowych, które można skonfigurować za pomocą [linku prywatnego platformy Azure](../private-link/private-link-overview.md), aby zapewnić dostęp do usług PaaS platformy Azure, takich jak Azure Storage, Azure Cosmos DB lub Azure SQL Database, usług partnerskich lub usług klienta hostowanych na platformie Azure. Jeśli aplikacje logiki wymagają dostępu do sieci wirtualnych, które używają prywatnych punktów końcowych, należy utworzyć, wdrożyć i uruchomić te aplikacje logiki w ramach ISE.

Po utworzeniu ISE platforma Azure wprowadza lub wdraża te ISE w sieci wirtualnej *platformy Azure.* Następnie można użyć tego ISE jako lokalizacji dla aplikacji logiki i kont integracji, które wymagają dostępu.

![Wybierz środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

To omówienie zawiera więcej informacji na temat tego, [dlaczego chcesz używać ISE](#benefits), [różnic między dedykowaną i wielodostępną usługą Logic Apps](#difference)oraz jak można uzyskać bezpośredni dostęp do zasobów, które znajdują się w sieci wirtualnej platformy Azure lub połączonej z nią.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Dlaczego warto używać ISE?

Uruchamianie aplikacji logiki w osobnym dedykowanym wystąpieniu pozwala ograniczyć wpływ innych dzierżawców platformy Azure na wydajność aplikacji, nazywanych również [efektem "sąsiedzi](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)". ISE zapewnia również następujące korzyści:

* Bezpośredni dostęp do zasobów, które znajdują się wewnątrz lub są połączone z siecią wirtualną

  Aplikacje logiki tworzone i uruchamiane w ISE mogą używać [specjalnie zaprojektowanych łączników, które działają w ISE](../connectors/apis-list.md#ise-connectors). Jeśli łącznik ISE istnieje dla systemu lokalnego lub źródła danych, można połączyć się bezpośrednio bez konieczności korzystania z [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md). Aby uzyskać więcej informacji, zobacz [dedykowany](#difference) i Wielodostępność w [systemach lokalnych](#on-premises) w dalszej części tego tematu.

* Ciągły dostęp do zasobów, które znajdują się poza siecią wirtualną lub nie są z nią połączone

  Aplikacje logiki tworzone i uruchamiane w ISE mogą nadal używać łączników, które działają w usłudze Logic Apps wielodostępnej, gdy łącznik specyficzny dla ISE jest niedostępny. Aby uzyskać więcej informacji, zobacz [dedykowany a Wielodostępność](#difference).

* Własne statyczne adresy IP, które są niezależne od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w usłudze wielu dzierżawców. W celu komunikowania się z systemami docelowymi można także skonfigurować jeden publiczny, statyczny i przewidywalny wychodzący adres IP. Dzięki temu nie trzeba konfigurować dodatkowych otwartych zapór w tych systemach docelowych dla każdego ISEu.

* Zwiększono limity czasu trwania przebiegu, przechowywanie magazynu, przepływność, żądania HTTP i limity czasu odpowiedzi, rozmiary komunikatów i żądania łączników niestandardowych. Aby uzyskać więcej informacji, zobacz [limity i konfiguracja dla Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedykowane w przypadku wielu dzierżawców

Podczas tworzenia i uruchamiania aplikacji logiki w ISE można korzystać z tych samych środowisk użytkownika i podobnych funkcji, co usługa Logic Apps wielodostępna. Można użyć wszystkich wbudowanych wyzwalaczy, akcji i łączników zarządzanych, które są dostępne w usłudze Logic Apps wielodostępnej. Niektóre zarządzane łączniki oferują dodatkowe wersje ISE. Różnica między łącznikami ISE i łącznikami innymi niż ISE istnieją w miejscu, w którym są uruchamiane, oraz etykietami, które znajdują się w Projektancie aplikacji logiki, gdy Pracujesz w ISE.

![Łączniki z etykietami i bez nich w ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Wbudowane wyzwalacze i akcje, takie jak HTTP, wyświetlają **podstawową** etykietę i działają w tym samym ISE, co aplikacja logiki.

* Łączniki zarządzane, które wyświetlają etykietę **ISE** , są specjalnie zaprojektowane dla ISEs i *zawsze uruchamiane w tym samym ISE, jak w aplikacji logiki*. Na przykład Oto kilka [łączników oferujących wersje ISE](../connectors/apis-list.md#ise-connectors):<p>

  * Blob Storage, File Storage i Table Storage platformy Azure
  * Azure Service Bus, Azure Queues, Azure Event Hubs
  * Dzienniki Azure Automation, Azure Key Vault, Azure Event Grid i Azure Monitor
  * FTP, SFTP-SSH, system plików i SMTP
  * SAP, IBM MQ, IBM DB2 i IBM 3270
  * SQL Server, usługa Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 i EDIFACT

  Z rzadkimi wyjątkami, jeśli łącznik ISE jest dostępny dla systemu lokalnego lub źródła danych, można połączyć się bezpośrednio bez korzystania z [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md). Aby uzyskać więcej informacji, zobacz [dostęp do systemów lokalnych](#on-premises) w dalszej części tego tematu.

* Łączniki zarządzane, które nie wyświetlają etykiety **ISE** , nadal działają dla aplikacji LOGIKI wewnątrz ISE. Te łączniki są *zawsze uruchamiane w usłudze Logic Apps z wieloma dzierżawcami*, a nie w ISE.

* Niestandardowe Łączniki tworzone *poza ISE*, niezależnie od tego, czy wymagają one [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md), nadal pracują z aplikacjami logiki wewnątrz ISE. Jednak łączniki niestandardowe tworzone *wewnątrz elementu ISE* nie będą działały z lokalną bramą danych. Aby uzyskać więcej informacji, zobacz [dostęp do systemów lokalnych](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Dostęp do systemów lokalnych

Aplikacje logiki, które działają w ramach usługi ISE, mogą bezpośrednio uzyskiwać dostęp do systemów lokalnych i źródeł danych, które znajdują się wewnątrz sieci wirtualnej platformy Azure lub połączone z tą siecią, przy użyciu następujących elementów:<p>

* Wyzwalacz lub akcja HTTP, która wyświetla **podstawową** etykietę

* Łącznik **ISE** , jeśli jest dostępny dla systemu lokalnego lub źródła danych

  Jeśli łącznik ISE jest dostępny, można bezpośrednio uzyskać dostęp do systemu lub źródła danych bez [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md). Jeśli jednak chcesz uzyskać dostęp do SQL Server z ISE i korzystać z uwierzytelniania systemu Windows, musisz użyć wersji ISE łącznika i lokalnej bramy danych. Wersja ISE łącznika nie obsługuje uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [ISE łączników](../connectors/apis-list.md#ise-connectors) i [nawiązywanie połączenia ze środowiska usługi integracji](../connectors/apis-list.md#integration-service-environment).

* Łącznik niestandardowy

  * Niestandardowe Łączniki tworzone *poza ISE*, niezależnie od tego, czy wymagają one [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md), nadal pracują z aplikacjami logiki wewnątrz ISE.

  * Łączniki niestandardowe tworzone *w ramach ISE* nie działają z lokalną bramą danych. Jednak te łączniki mogą bezpośrednio uzyskiwać dostęp do systemów lokalnych i źródeł danych, które znajdują się wewnątrz lub połączone z siecią wirtualną, która hostuje ISE. Dlatego Aplikacje logiki, które znajdują się w ISE, zwykle nie potrzebują bramy danych podczas uzyskiwania dostępu do tych zasobów.

Aby uzyskać dostęp do systemów lokalnych i źródeł danych, które nie mają łączników ISE, znajdują się poza siecią wirtualną lub nie są połączone z siecią wirtualną, nadal trzeba korzystać z lokalnej bramy danych. Aplikacje logiki w ramach ISE mogą nadal używać łączników, które nie mają etykiety **Core** ani **ISE** . Te łączniki działają tylko w usłudze Logic Apps wielodostępnej, a nie w ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>Jednostki SKU ISE

Po utworzeniu ISE można wybrać jednostkę SKU dla deweloperów lub jednostkę SKU Premium. Poniżej przedstawiono różnice między tymi jednostkami SKU:

* **Deweloper**

  Oferuje tańsze ISE, których można użyć do eksperymentowania, programowania i testowania, ale nie do testowania produkcji ani wydajności. Jednostka SKU dla deweloperów obejmuje wbudowane wyzwalacze i akcje, łączniki standardowe, łączniki przedsiębiorstwa oraz zintegrowane konto integracji z jedną [warstwą](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) dla ustalonej ceny miesięcznej. Jednak ta jednostka SKU nie obejmuje żadnej umowy dotyczącej poziomu usług (SLA), opcji skalowania pojemności lub nadmiarowości podczas odtwarzania, co oznacza, że mogą wystąpić opóźnienia lub przestoje.

* **Premium**

  Oferuje ISE, którego można użyć do produkcji i obejmuje obsługę umów SLA, wbudowane wyzwalacze i akcje, łączniki standardowe, łączniki przedsiębiorstwa, pojedyncze konto integracji [warstwy standardowej](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , opcje skalowania pojemności i nadmiarowości podczas odtwarzania przez ustaloną cenę miesięczną.

> [!IMPORTANT]
> Opcja SKU jest dostępna tylko podczas tworzenia ISE i nie można jej później zmienić.

Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/). Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla usługi ISEs, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Dostęp do punktu końcowego ISE

Po utworzeniu ISE można użyć wewnętrznych lub zewnętrznych punktów końcowych dostępu. Wybór określa, czy wyzwalacze żądania lub elementu webhook w usłudze Logic Apps w ISE mogą odbierać wywołania spoza sieci wirtualnej. Te punkty końcowe wpływają również na sposób dostępu do danych wejściowych i wyjściowych z historii uruchamiania aplikacji logiki.

> [!IMPORTANT]
> Punkt końcowy dostępu można wybrać tylko podczas tworzenia ISE i nie można zmienić tej opcji później.

* **Wewnętrzne**: prywatne punkty końcowe umożliwiają wywoływanie aplikacji LOGIKI w ISE, w którym można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych z historii uruchamiania usługi Logic Apps *tylko z wewnątrz sieci wirtualnej*.

  > [!IMPORTANT]
  > Upewnij się, że masz połączenie sieciowe między prywatnymi punktami końcowymi i komputerem, z którego chcesz uzyskać dostęp do historii uruchamiania. W przeciwnym razie podczas próby wyświetlenia historii uruchamiania aplikacji logiki zostanie wyświetlony komunikat o błędzie "nieoczekiwany błąd. Nie można pobrać ".
  >
  > ![Błąd akcji usługi Azure Storage wynikający z braku możliwości wysyłania ruchu przez zaporę](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Na przykład komputer kliencki może znajdować się w sieci wirtualnej ISE lub wewnątrz sieci wirtualnej, która jest połączona z siecią wirtualną ISE za pomocą komunikacji równorzędnej lub wirtualnej sieci prywatnej. 

* **Zewnętrzne**: publiczne punkty końcowe umożliwiają wywoływanie aplikacji LOGIKI w ISE, w którym można wyświetlać i uzyskiwać dostęp do danych wejściowych i wyjściowych z historii uruchamiania aplikacji logiki *z spoza sieci wirtualnej*. Jeśli używasz sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń), upewnij się, że są one skonfigurowane przy użyciu reguł ruchu przychodzącego, aby zezwolić na dostęp do danych wejściowych i wyjściowych historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Włączanie dostępu do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Aby określić, czy ISE używa wewnętrznego lub zewnętrznego punktu końcowego dostępu, w menu ISE w obszarze **Ustawienia**wybierz pozycję **Właściwości**i Znajdź właściwość **punkt końcowy dostępu** :

![Znajdź punkt końcowy dostępu ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Model cen

Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki działające w ISE używają stałego planu cenowego, który różni się od planu cenowego opartego na zużyciu. Aby uzyskać więcej informacji, zobacz [Logic Apps model cen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Konta integracji z usługą ISE

Konta integracji z aplikacjami logiki można używać w środowisku usługi integracji (ISE). Jednak te konta integracji muszą używać tego *samego ISE* , co połączone Aplikacje logiki. Aplikacje logiki w ISE mogą odwoływać się tylko do tych kont integracji, które znajdują się w tym samym ISE. Podczas tworzenia konta integracji możesz wybrać swój ISE jako lokalizację konta integracji. Aby dowiedzieć się, jak korzystać z cen i rozliczeń dla kont integracji za pomocą ISE, zobacz [model cen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Stawki cenowe znajdują się w temacie [Logic Apps cenniku](https://azure.microsoft.com/pricing/details/logic-apps/). Aby uzyskać informacje o limitach, zobacz [limity kont integracji](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Następne kroki

* [Łączenie z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Dowiedz się więcej o [usłudze Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Informacje o [integracji sieci wirtualnej dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
