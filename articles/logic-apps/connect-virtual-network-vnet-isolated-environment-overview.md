---
title: Dostęp do sieci wirtualnych platformy Azure
description: Omówienie sposobu, w jaki środowiska usług integracji (ISE) pomagają aplikacjom logiki uzyskać dostęp do sieci wirtualnych platformy Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 3070083040424b877159955dc2138f15319f05c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766393"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Dostęp do zasobów usługi Azure Virtual Network z Azure Logic Apps przy użyciu środowisk usługi integracji (ISE)

Czasami aplikacje logiki muszą mieć dostęp do zabezpieczonych zasobów, takich jak maszyny wirtualne i inne systemy lub usługi, które znajdują się w sieci wirtualnej platformy Azure lub są z niej [połączone.](../virtual-network/virtual-networks-overview.md) Aby skonfigurować ten dostęp, możesz utworzyć [środowisko *usługi integracji* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) IsE to wystąpienie usługi Logic Apps, która używa dedykowanych zasobów i działa niezależnie od "globalnej" wielodostępnej Logic Apps usługi. Dane w programie ISE pozostają w tym samym regionie, w [którym tworzysz i wdrażasz ten program.](https://azure.microsoft.com/global-infrastructure/data-residency/)

Na przykład niektóre sieci wirtualne platformy Azure używają prywatnych punktów końcowych, które można skonfigurować za pośrednictwem usługi [Azure Private Link,](../private-link/private-link-overview.md)w celu zapewnienia dostępu do usług PaaS platformy Azure, takich jak Azure Storage, Azure Cosmos DB lub Azure SQL Database, usługi partnerskie lub usługi klienta hostowane na platformie Azure. Jeśli aplikacje logiki wymagają dostępu do sieci wirtualnych, które korzystają z prywatnych punktów końcowych, musisz tworzyć, wdrażać i uruchamiać te aplikacje logiki w środowisku ISE.

Podczas tworzenia środowiska ISE platforma *Azure* wprowadza lub wdraża to urządzenie w sieci wirtualnej platformy Azure. Następnie możesz użyć tego środowiska ISE jako lokalizacji dla aplikacji logiki i kont integracji, które wymagają dostępu.

![Wybieranie środowiska usługi integracji](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

To omówienie zawiera więcej informacji o tym, dlaczego warto używać środowiska [ISE,](#benefits)różnic między dedykowaną i wielodostępną usługą [Logic Apps oraz](#difference)o tym, jak można bezpośrednio uzyskać dostęp do zasobów, które znajdują się w sieci wirtualnej platformy Azure lub są połączone z tą siecią wirtualną.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Dlaczego warto używać ise?

Uruchamianie aplikacji logiki we własnym dedykowanym wystąpieniu pomaga zmniejszyć wpływ innych dzierżaw platformy Azure na wydajność aplikacji, znany również jako efekt "hałaśliwych [sąsiadów".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Ponadto środowiska ISE zapewniają następujące korzyści:

* Bezpośredni dostęp do zasobów, które znajdują się w sieci wirtualnej lub są z nim połączone

  Aplikacje logiki, które tworzysz i uruchamiasz w programie ISE, mogą używać specjalnie zaprojektowanych łączników [uruchamianych na tym komputerze.](../connectors/managed.md#ise-connectors) Jeśli łącznik ISE istnieje dla systemu lokalnego lub źródła danych, możesz nawiązać bezpośrednie połączenie bez konieczności używania lokalnej [bramy danych.](../logic-apps/logic-apps-gateway-connection.md) Aby uzyskać więcej informacji, zobacz [tematy Dedykowane i wielodostępne](#difference) oraz Dostęp do [systemów](#on-premises) lokalnych w dalszej części tego tematu.

* Dalszy dostęp do zasobów, które znajdują się poza siecią wirtualną lub nie są z nim połączone

  Aplikacje logiki, które tworzysz i uruchamiasz w programie ISE, nadal mogą używać łączników uruchamianych w wielodostępnej usłudze Logic Apps, gdy łącznik specyficzny dla programu ISE jest niedostępny. Aby uzyskać więcej informacji, zobacz [Dedykowane i wielodostępne](#difference).

* Twoje własne statyczne adresy IP, które są oddzielone od statycznych adresów IP udostępnianych przez aplikacje logiki w usłudze wielodostępnej. Można również skonfigurować jeden publiczny, statyczny i przewidywalny wychodzący adres IP do komunikowania się z systemami docelowymi. Dzięki temu nie trzeba skonfigurować dodatkowych otworów zapory w tych systemach docelowych dla każdego programu ISE.

* Zwiększone limity czasu trwania działania, przechowywania magazynu, przepływności, limitów czasu żądań HTTP i odpowiedzi, rozmiarów komunikatów i niestandardowych żądań łącznika. Aby uzyskać więcej informacji, zobacz [Limits and configuration for Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedykowane a wielodostępne

Podczas tworzenia i uruchamiania aplikacji logiki w programie ISE masz takie same środowisko użytkownika i podobne możliwości jak wielodostępna Logic Apps usługi. Możesz użyć tych samych wbudowanych wyzwalaczy, akcji i zarządzanych łączników, które są dostępne w usłudze zarządzania Logic Apps dzierżawy. Niektóre łączniki zarządzane oferują dodatkowe wersje ise. Różnica między łącznikami ISE i łącznikami innym niż łączniki ISE występuje w miejscu ich uruchamiania, a etykietami, które mają w Projektancie aplikacji logiki podczas pracy w ramach programu ISE.

![Łączniki z etykietami i bez etykiet w isE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Wbudowane wyzwalacze i akcje, takie jak HTTP, wyświetlają etykietę **CORE** i działają w tym samym programie ISE co aplikacja logiki.

* Zarządzane łączniki, które wyświetlają **etykietę ISE,** są specjalnie zaprojektowane dla platform ISE i zawsze działają w tym samym programie ISE co *aplikacja logiki.* Na przykład poniżej znajdują się niektóre [łączniki, które oferują wersje ise:](../connectors/managed.md#ise-connectors)<p>

  * Azure Blob Storage, File Storage i Table Storage
  * Azure Service Bus, Azure Queues, Azure Event Hubs
  * Azure Automation, Azure Key Vault, Azure Event Grid i Azure Monitor dzienniki
  * FTP, SFTP-SSH, System plików i SMTP
  * SAP, IBM MQ, IBM DB2 i IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 i EDIFACT

  Z rzadkimi wyjątkami, jeśli łącznik ISE jest dostępny dla systemu lokalnego lub źródła danych, możesz nawiązać połączenie bezpośrednio bez używania [lokalnej bramy danych.](../logic-apps/logic-apps-gateway-connection.md) Aby uzyskać więcej informacji, zobacz [Dostęp do systemów lokalnych w](#on-premises) dalszej części tego tematu.

* Łączniki zarządzane, które nie wyświetlają **etykiety ISE,** nadal działają w przypadku aplikacji logiki w ise. Te *łączniki są zawsze uruchamiane w wielodostępnej Logic Apps ,* a nie w programie ISE.

* Łączniki niestandardowe, które tworzysz poza środowisku *ISE,* niezależnie od tego, czy wymagają lokalnej bramy [danych,](../logic-apps/logic-apps-gateway-connection.md)nadal działają dla aplikacji logiki w środowisku ISE. Jednak łączniki niestandardowe, które tworzysz *w środowisku ISE,* nie będą działać z lokalną bramą danych. Aby uzyskać więcej informacji, [zobacz Access to on-premises systems](#on-premises)(Dostęp do systemów lokalnych).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Dostęp do systemów lokalnych

Aplikacje logiki uruchamiane w środowisku ISE mogą bezpośrednio uzyskać dostęp do systemów lokalnych i źródeł danych, które znajdują się w sieci wirtualnej platformy Azure lub są z nim połączone, przy użyciu tych elementów:<p>

* Wyzwalacz LUB akcja HTTP, która wyświetla **etykietę CORE**

* Łącznik **ISE,** jeśli jest dostępny, dla systemu lokalnego lub źródła danych

  Jeśli łącznik ise jest dostępny, możesz bezpośrednio uzyskać dostęp do systemu lub źródła danych bez lokalnej [bramy danych.](../logic-apps/logic-apps-gateway-connection.md) Jeśli jednak musisz uzyskać dostęp do usługi SQL Server z programu ISE i korzystać z uwierzytelniania systemu Windows, musisz użyć wersji łącznika bez programu ISE i lokalnej bramy danych. Wersja ise łącznika nie obsługuje uwierzytelniania systemu Windows. Aby uzyskać więcej informacji, zobacz [Łączniki ISE i](../connectors/managed.md#ise-connectors) [Nawiązywanie połączenia ze środowiska usługi integracji](../connectors/managed.md#integration-account-connectors).

* Łącznik niestandardowy

  * Łączniki niestandardowe, które tworzysz poza środowisku *ISE*, niezależnie od tego, czy wymagają lokalnej bramy [danych,](../logic-apps/logic-apps-gateway-connection.md)nadal działają dla aplikacji logiki w środowisku ISE.

  * Łączniki niestandardowe, które *tworzysz w środowisku ISE,* nie działają z lokalną bramą danych. Jednak te łączniki mogą bezpośrednio uzyskać dostęp do systemów lokalnych i źródeł danych, które znajdują się w sieci wirtualnej hostowania środowiska ISE lub są z nim połączone. Dlatego aplikacje logiki, które znajdują się w programie ISE, zwykle nie potrzebują bramy danych podczas uzyskiwania dostępu do tych zasobów.

Aby uzyskać dostęp do systemów lokalnych i źródeł danych, które nie mają łączników środowiska ISE, znajdują się poza siecią wirtualną lub nie są połączone z siecią wirtualną, nadal musisz używać lokalnej bramy danych. Aplikacje logiki w ramach programu ISE mogą nadal używać łączników, które nie mają **etykiety CORE** **ani ISE.** Te łączniki działają w wielodostępnej usłudze Logic Apps, a nie w programie ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>Jednostki SKU ISE

Podczas tworzenia własnego oprogramowania ISE możesz wybrać pozycję Developer SKU lub Premium SKU. Ta opcja SKU jest dostępna tylko podczas tworzenia ise i nie można jej zmienić później. Poniżej podano różnice między tymi jednostkami SKU:

* **Deweloper**

  Zapewnia tańszą platformę ISE, która umożliwia eksplorację, eksperymenty, opracowywanie i testowanie, ale nie do testowania produkcyjnego lub wydajnościowego. Jednostka SKU dewelopera obejmuje wbudowane wyzwalacze i akcje, łączniki [](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) w warstwie Standardowa, łączniki dla przedsiębiorstw i jedno konto integracji w warstwie Bezpłatna w stałej cenie [miesięcznej.](https://azure.microsoft.com/pricing/details/logic-apps) 

  > [!IMPORTANT]
  > Ta sku nie ma umowy dotyczącej poziomu usług (SLA), możliwości skalowania w górę ani nadmiarowości podczas przetwarzania, co oznacza, że mogą wystąpić opóźnienia lub przestoje. Aktualizacje zaplecza mogą sporadycznie przerywać działanie usługi.

  Aby uzyskać informacje o pojemności i limitach, zobacz [Limity ise w Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Aby dowiedzieć się, jak działa rozliczanie dla platform ISE, zobacz [Logic Apps cennika](../logic-apps/logic-apps-pricing.md#fixed-pricing).

* **Premium**

  Zapewnia platformę ISE, która umożliwia testowanie produkcyjne i wydajnościowe. Jednostka SKU w warstwie Premium obejmuje obsługę umowy SLA, wbudowane wyzwalacze i akcje, łączniki w warstwie Standardowa, łączniki dla przedsiębiorstw, jedno konto integracji w warstwie [Standardowa,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) możliwość skalowania w górę i nadmiarowość podczas ponownego przetwarzania za stałą miesięczną [cenę.](https://azure.microsoft.com/pricing/details/logic-apps)

  Aby uzyskać informacje o pojemności i limitach, zobacz [Limity ise w Azure Logic Apps](logic-apps-limits-and-config.md#integration-service-environment-ise). Aby dowiedzieć się, jak działa rozliczanie dla platform ISE, zobacz [Logic Apps cennika](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Dostęp do punktu końcowego ise

Podczas tworzenia programu ISE można użyć wewnętrznych lub zewnętrznych punktów końcowych dostępu. Wybór określa, czy wyzwalacze żądania lub webhook w aplikacjach logiki w środowisku ISE mogą odbierać wywołania spoza sieci wirtualnej. Te punkty końcowe mają również wpływ na sposób uzyskiwania dostępu do danych wejściowych i wyjściowych z historii przebiegów aplikacji logiki.

> [!IMPORTANT]
> Punkt końcowy dostępu można wybrać tylko podczas tworzenia programu ISE i nie można później zmienić tej opcji.

* **Wewnętrzne:** prywatne punkty końcowe zezwalają na wywołania aplikacji logiki w środowisku ISE, w których można wyświetlać dane wejściowe i wyjściowe z historii przebiegów aplikacji logiki i uzyskać do nich dostęp tylko z wewnątrz sieci *wirtualnej.*

  > [!IMPORTANT]
  > Jeśli musisz użyć tych wyzwalaczy opartych na webhook, użyj zewnętrznych *punktów* końcowych, a nie wewnętrznych punktów końcowych, podczas tworzenia własnego programu ISE:
  > 
  > * Azure DevOps
  > * Azure Event Grid
  > * Common Data Service
  > * Office 365
  > * SAP (wersja ISE)
  > 
  > Upewnij się również, że masz łączność sieciową między prywatnymi punktami końcowymi a komputerem, z którego chcesz uzyskać dostęp do historii uruchamiania. W przeciwnym razie podczas próby wyświetlenia historii uruchamiania aplikacji logiki zostanie wyświetlany komunikat o błędzie "Nieoczekiwany błąd. Nie można pobrać".
  >
  > ![Błąd akcji usługi Azure Storage wynikowy z powodu błędu wysyłania ruchu przez zaporę](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Na przykład komputer kliencki może istnieć wewnątrz sieci wirtualnej środowiska ISE lub wewnątrz sieci wirtualnej, która jest połączona z siecią wirtualną środowiska ISE za pośrednictwem komunikacji równorzędnej lub wirtualnej sieci prywatnej. 

* **Zewnętrzne:** publiczne punkty końcowe zezwalają na wywołania aplikacji logiki w środowisku ISE, w których można wyświetlać dane wejściowe i wyjściowe z historii przebiegów aplikacji logiki spoza sieci wirtualnej oraz uzyskać do *nich dostęp.* Jeśli używasz sieciowych grup zabezpieczeń, upewnij się, że zostały one ustawione przy użyciu reguł ruchu przychodzącego, aby zezwolić na dostęp do danych wejściowych i wyjściowych historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Włączanie dostępu dla programu ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

Aby **określić,** czy twoje oprogramowanie ISE używa wewnętrznego, czy zewnętrznego punktu końcowego dostępu, w menu aparatu ISE w obszarze Ustawienia wybierz pozycję Właściwości **i** znajdź właściwość Punkt końcowy programu **Access:**

![Znajdowanie punktu końcowego dostępu ise](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Model cen

Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki uruchamiane w programie ISE używają stałego planu cenowego, który różni się od planu cenowego opartego na zużyciu. Aby uzyskać więcej informacji, [zobacz Logic Apps cennika](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać informacje o cenach, [zobacz Logic Apps cennik.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Konta integracji z programem ISE

Kont integracji można używać z aplikacjami logiki w środowisku usługi integracji (ISE). Jednak te konta integracji muszą używać tego samego środowiska *ISE* co połączone aplikacje logiki. Aplikacje logiki w programie ISE mogą odwoływać się tylko do kont integracji, które znajdują się w tym samym programie ISE. Podczas tworzenia konta integracji można wybrać platformę ISE jako lokalizację konta integracji. Aby dowiedzieć się, jak działają ceny i rozliczenia dla kont integracji ze środowiskami ISE, zobacz [Logic Apps model cen.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Aby uzyskać informacje o cenach, [zobacz Logic Apps cennika.](https://azure.microsoft.com/pricing/details/logic-apps/) Aby uzyskać informacje o limitach, zobacz [Integration account limits (Limity konta integracji).](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="next-steps"></a>Następne kroki

* [Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Dowiedz się więcej o [usłudze Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Dowiedz się więcej [o integracji z siecią wirtualną dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
