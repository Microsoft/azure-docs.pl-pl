---
title: Azure Defender for SQL — korzyści i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla programu SQL Server.
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 81ae46a7b6c19557eeadd2d5b28ad2fa46e77115
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881371"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Wprowadzenie do usługi Azure Defender dla programu SQL

Usługa Azure Defender dla programu SQL obejmuje dwa plany usługi Azure Defender, które rozszerzają [pakiet zabezpieczeń danych](../azure-sql/database/azure-defender-for-sql.md) Azure Security Center, aby zabezpieczyć bazy danych i ich dane w dowolnym miejscu. 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|**Usługa Azure Defender dla serwerów usługi Azure SQL Database** — ogólnie dostępna (ga)<br>**Usługa Azure Defender dla serwerów SQL na maszynach** — ogólnie dostępna (ga) |
|Wpisaną|Na [stronie cennika](security-center-pricing.md) są naliczane opłaty za dwa plany tworzące **usługę Azure Defender for SQL** .|
|Chronione wersje programu SQL:|[SQL na maszynach wirtualnych platformy Azure](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[Serwery SQL z włączonym usługą Azure Arc](/sql/sql-server/azure-arc/overview)<br>Lokalne serwery SQL na maszynach z systemem Windows bez usługi Azure Arc<br>[Pojedyncze bazy danych](../azure-sql/database/single-database-overview.md) SQL Azure i [Pule elastyczne](../azure-sql/database/elastic-pool-overview.md)<br>[Wystąpienie zarządzane Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Dedykowana Pula SQL usługi Azure Synapse Analytics (dawniej SQL DW)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|Połączeń|![Tak ](./media/icons/yes-icon.png) chmury komercyjne<br>![Tak ](./media/icons/yes-icon.png) US gov<br>![Tak ](./media/icons/yes-icon.png) — Chiny gov (**części**: podzbiór alertów i Ocena luk w zabezpieczeniach dla serwerów SQL. Ochrona przed zagrożeniami nie jest dostępna.|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Co to jest usługa Azure Defender dla ochrony SQL?

**Usługa Azure Defender dla programu SQL** obejmuje dwa oddzielne plany usługi Azure Defender:

- **Usługa Azure Defender dla serwerów usługi Azure SQL Database** chroni:
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
    - [Wystąpienie zarządzane Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Dedykowana Pula SQL na platformie Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Usługa Azure Defender dla serwerów SQL na maszynach** rozszerza ochronę natywnych serwerów SQL w celu zapewnienia pełnej obsługi środowisk hybrydowych i ochrony serwerów SQL (wszystkie obsługiwane wersje) hostowanych na platformie Azure, w innych środowiskach w chmurze, a nawet na maszynach lokalnych:
    - [Program SQL Server w usłudze Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - Lokalne serwery SQL:
        - [Program SQL Server z obsługą usługi Azure Arc (wersja zapoznawcza)](/sql/sql-server/azure-arc/overview)
        - [SQL Server uruchomione na maszynach z systemem Windows bez usługi Azure Arc](../azure-monitor/platform/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Jakie korzyści zapewnia usługa Azure Defender dla programu SQL?

Te dwa plany obejmują funkcje służące do identyfikowania i ograniczania potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenia dla baz danych:

- [Ocena luk w zabezpieczeniach](../azure-sql/database/sql-vulnerability-assessment.md) — Usługa skanowania w celu odnajdywania, śledzenia i rozwiązywania problemów dotyczących potencjalnych luk w zabezpieczeniach bazy danych. Przeglądy oceny zawierają omówienie stanu zabezpieczeń maszyn SQL i szczegółowe informacje o wszelkich wynikach związanych z bezpieczeństwem.

- [Zaawansowana ochrona przed zagrożeniami](../azure-sql/database/threat-detection-overview.md) — usługa wykrywania, która stale monitoruje serwery SQL pod kątem zagrożeń, takich jak iniekcja SQL, ataki z wymuszeniem i nadużycie uprawnień. Ta usługa udostępnia alerty zabezpieczeń zorientowane na działania w Azure Security Center z szczegółowymi informacjami o podejrzanych działaniach, wskazówkami dotyczącymi sposobu łagodzenia zagrożeń oraz opcjami do kontynuowania badań za pomocą platformy Azure. 
    > [!TIP]
    > Wyświetl listę alertów zabezpieczeń dla serwerów SQL [na stronie Dokumentacja dotycząca alertów](alerts-reference.md#alerts-sql-db-and-warehouse).


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Jakiego rodzaju alerty zapewniają usługa Azure Defender dla programu SQL?

Alerty zabezpieczeń wzbogacone przez analizę zagrożeń są wyzwalane w przypadku:

- **Potencjalne ataki z iniekcją SQL** — w tym luki wykryte w przypadku wygenerowania błędnej instrukcji SQL w bazie danych
- **Nietypowego dostępu do bazy danych i wzorców zapytań** — na przykład nietypowo duża liczba nieudanych prób logowania z różnymi poświadczeniami (próba nałożenia siły)
- **Podejrzana aktywność bazy danych** — na przykład uprawniony użytkownik uzyskujący dostęp do SQL Server z naruszenia komputera, który komunikuje się z serwerem C&c

Alerty obejmują szczegółowe informacje o zdarzeniu, które je wywołały, a także zalecenia dotyczące sposobu badania i korygowania zagrożeń.



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla programu SQL Server.

> [!div class="nextstepaction"]
> [Skanowanie serwerów SQL pod kątem luk w zabezpieczeniach za pomocą usługi Azure Defender](defender-for-sql-usage.md)