---
title: Omówienie przykładu strategii obciążenia środowiska ASE/bazy danych SQL ISO 27001
description: Omówienie i architektura przykładu strategii obciążenia środowiska App Service Environment/bazy danych SQL ISO 27001.
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 6f6cb50450e461d87fdbb3c71fe6273c43c3122e
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930411"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Przegląd przykładowej strategii obciążenia środowiska App Service Environment/bazy danych SQL ISO 27001

Przykład strategii obciążenia środowiska App Service Environment/bazy danych SQL ISO 27001 udostępnia dodatkową infrastrukturę przykładu strategii [usług udostępnionych ISO 27001](../iso27001-shared/index.md).
Ta strategia pomaga klientom wdrażać architektury chmurowe, które oferują rozwiązania dla scenariuszy z wymaganiami w zakresie akredytacji lub zgodności.

Istnieją dwa przykłady strategii ISO 27001: ten przykład i przykład strategii [usług udostępnionych ISO 27001](../iso27001-shared/index.md).

> [!IMPORTANT]
> Ten przykład jest zależny od infrastruktury wdrożonej przez przykład strategii [usług udostępnionych ISO 27001](../iso27001-shared/index.md). Najpierw trzeba go wdrożyć.

## <a name="architecture"></a>Architektura

Przykład strategii obciążenia środowiska App Service Environment/bazy danych SQL ISO 27001 pozwala wdrożyć platformę jako środowisko internetowe oparte na usługach. Środowisko umożliwia hostowanie wielu aplikacji internetowych, internetowych interfejsów API i wystąpień bazy danych SQL zgodnych ze standardami ISO 27001. Ten przykład strategii zależy od przykładu strategii [usług udostępnionych ISO 27001](../iso27001-shared/index.md).

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="Przykładowy projekt strategii obciążenia środowiska ASE/bazy danych SQL ISO 27001" border="false":::

To środowisko składa się z kilku usług platformy Azure, które udostępniają bezpieczną, w pełni monitorowaną infrastrukturę obciążeń z obsługą przedsiębiorstw zgodną ze standardami ISO 27001. To środowisko zawiera następujące składniki:

- [Rola platformy Azure](../../../../role-based-access-control/overview.md) o nazwie DevOps, która ma uprawnienia do wdrażania zasobów i zarządzania nimi w [środowiskach Azure App Service Environment](../../../../app-service/environment/intro.md) wdrożonych przez przykładową strategię
- Definicje usługi [Azure Policy](../../../policy/overview.md) blokujące usługi, które można wdrażać w środowisku, i odmawiające utworzenia zasobu publicznego adresu IP
- Sieć wirtualna zawierająca jedną podsieć i połączona równorzędnie z istniejącym wcześniej środowiskiem [usług udostępnionych](../iso27001-shared/index.md), która wymusza przekazywanie całego ruchu przez zaporę [usług udostępnionych](../iso27001-shared/index.md). Sieć wirtualna hostuje następujące zasoby:
  - [Środowiska Azure App Service Environment](../../../../app-service/environment/intro.md), które umożliwiają hostowanie aplikacji internetowych, internetowych interfejsów API lub funkcji
  - Wystąpienie usługi [Azure Key Vault](../../../../key-vault/general/overview.md) używające punktu końcowego usługi sieci wirtualnej do przechowywania wpisów tajnych używanych przez aplikacje działające w środowisku obciążenia
  - Wystąpienie usługi [Azure SQL Database](../../../../azure-sql/database/sql-database-paas-overview.md) używające punktu końcowego usługi sieci wirtualnej do hostowania baz danych używanych na potrzeby aplikacji w środowisku obciążenia

## <a name="next-steps"></a>Następne kroki

Zapoznano się z omówieniem i architekturą przykładu strategii obciążenia środowiska App Service Environment/bazy danych SQL ISO 27001. Następnie przeczytaj następujące artykuły, aby poznać mapowanie kontrolek i sposób wdrażania tego przykładu:

> [!div class="nextstepaction"]
> [Strategia obciążenia środowiska App Service Environment/bazy danych SQL ISO 27001 — mapowanie kontrolek](./control-mapping.md)
> [Strategia obciążenia środowiska App Service Environment/bazy danych SQL ISO 27001 — procedura wdrażania](./deploy.md)

Dodatkowe artykuły na temat strategii i sposobu ich używania:

- Uzyskaj informacje na temat [cyklu życia strategii](../../concepts/lifecycle.md).
- Dowiedz się, jak używać [parametrów statycznych i dynamicznych](../../concepts/parameters.md).
- Dowiedz się, jak dostosować [kolejność sekwencjonowania strategii](../../concepts/sequencing-order.md).
- Dowiedz się, jak używać [blokowania zasobów strategii](../../concepts/resource-locking.md).
- Dowiedz się, jak [zaktualizować istniejące przypisania](../../how-to/update-existing-assignments.md).
