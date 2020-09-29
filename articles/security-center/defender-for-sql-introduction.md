---
title: Azure Defender for SQL — korzyści i funkcje
description: Dowiedz się więcej o zaletach i funkcjach usługi Azure Defender dla programu SQL Server.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 328a565dbb22a13c71a7001d43941e7be062dff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449053"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Wprowadzenie do usługi Azure Defender dla programu SQL

Usługa Azure Defender dla programu SQL obejmuje dwa plany usługi Azure Defender, które rozszerzają [pakiet zabezpieczeń danych](../azure-sql/database/advanced-data-security.md) Azure Security Center, aby zabezpieczyć bazy danych i ich dane w dowolnym miejscu. 

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|**Usługa Azure Defender dla serwerów usługi Azure SQL Database** — ogólnie dostępna (ga)<br>**Usługa Azure Defender dla serwerów SQL na komputerach** — wersja zapoznawcza|
|Wpisaną|Na [stronie cennika](security-center-pricing.md) są naliczane opłaty za dwa plany tworzące **usługę Azure Defender for SQL** .|
|Chronione wersje programu SQL:|Azure SQL Database <br>Wystąpienie zarządzane Azure SQL<br>Azure Synapse Analytics (dawniej SQL DW)<br>SQL Server (wszystkie obsługiwane wersje)|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) US Gov<br>![Nie](./media/icons/no-icon.png) Chiny gov, inne gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Co to jest usługa Azure Defender dla ochrony SQL?

**Usługa Azure Defender dla programu SQL** obejmuje dwa oddzielne plany usługi Azure Defender:

- **Usługa Azure Defender dla serwerów usługi Azure SQL Database** chroni:
  - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)
  - [Wystąpienie zarządzane Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- **Usługa Azure Defender dla serwerów SQL na maszynach (wersja zapoznawcza)** rozszerza ochronę natywnych serwerów SQL na platformie Azure w celu zapewnienia pełnej obsługi środowisk hybrydowych i ochrony serwerów SQL (wszystkie obsługiwane wersje) hostowanych na platformie Azure, w innych środowiskach w chmurze, a nawet na maszynach lokalnych


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Jakie korzyści zapewnia usługa Azure Defender dla programu SQL?

Te dwa plany obejmują funkcje służące do identyfikowania i ograniczania potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywania nietypowych działań, które mogą wskazywać na zagrożenia dla baz danych:

- [Ocena luk w zabezpieczeniach](../azure-sql/database/sql-vulnerability-assessment.md) — Usługa skanowania w celu odnajdywania, śledzenia i rozwiązywania problemów dotyczących potencjalnych luk w zabezpieczeniach bazy danych. Przeglądy oceny zawierają omówienie stanu zabezpieczeń maszyn SQL i szczegółowe informacje o wszelkich wynikach związanych z bezpieczeństwem.

- [Zaawansowana ochrona przed zagrożeniami](../azure-sql/database/threat-detection-overview.md) — usługa wykrywania, która stale monitoruje serwery SQL pod kątem zagrożeń, takich jak iniekcja SQL, ataki z wymuszeniem i nadużycie uprawnień. Ta usługa udostępnia alerty zabezpieczeń zorientowane na działania w Azure Security Center z szczegółowymi informacjami o podejrzanych działaniach, wskazówkami dotyczącymi sposobu łagodzenia zagrożeń oraz opcjami do kontynuowania badań za pomocą platformy Azure.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Jakiego rodzaju alerty zapewniają usługa Azure Defender dla programu SQL?

Alerty zabezpieczeń są wyzwalane w przypadku:

- **Potencjalne ataki z iniekcją SQL** — w tym luki wykryte w przypadku wygenerowania błędnej instrukcji SQL w bazie danych
- **Nietypowego dostępu do bazy danych i wzorców zapytań** — na przykład nietypowo duża liczba nieudanych prób logowania z różnymi poświadczeniami (próba nałożenia siły)
- **Podejrzana aktywność bazy danych** — na przykład zmiana w docelowym magazynie eksportu dla operacji importu i eksportu SQL

Alerty obejmują szczegółowe informacje o zdarzeniu, które je wywołały, a także zalecenia dotyczące sposobu badania i korygowania zagrożeń.



## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o usłudze Azure Defender dla programu SQL Server.

W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami: 

- [Jak włączyć usługę Azure Defender dla serwerów SQL na maszynach](defender-for-sql-usage.md)
- [Jak włączyć usługę Azure Defender dla serwerów baz danych SQL](../azure-sql/database/advanced-data-security.md)
- [Lista alertów usługi Azure Defender dla programu SQL](alerts-reference.md#alerts-sql-db-and-warehouse)