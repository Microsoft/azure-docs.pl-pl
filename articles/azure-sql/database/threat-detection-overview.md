---
title: Zaawansowana ochrona przed zagrożeniami
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych, które wskazują potencjalne zagrożenia bezpieczeństwa w Azure SQL Database, wystąpieniu zarządzanym usługi Azure SQL i usługą Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 27d44ba8be56839f248312e1bdb4b0e4b7761110
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047020"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance--azure-synapse"></a>Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database i wystąpienia zarządzanego SQL & Azure Synapse
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Zaawansowana ochrona przed zagrożeniami dla [Azure SQL Database](sql-database-paas-overview.md), [wystąpienie zarządzane SQL Azure](../managed-instance/sql-managed-instance-paas-overview.md) i [usługa Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) wykrywają anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby dostępu do baz danych lub korzystania z nich.

Zaawansowana ochrona przed zagrożeniami jest częścią [zaawansowanej oferty zabezpieczeń danych](advanced-data-security.md) , która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Aby uzyskiwać dostęp do funkcji Advanced Threat Protection i zarządzać nią, można korzystać centralnego portalu pakietu SQL ADS.

## <a name="overview"></a>Omówienie

Zaawansowana ochrona przed zagrożeniami zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku nietypowych działań. Użytkownicy otrzymują alerty o podejrzanych działaniach związanych z bazą danych, potencjalnych luk w zabezpieczeniach i atakach iniekcji SQL, a także o nietypowych wzorcach dostępu do bazy danych i zapytań. Zaawansowana ochrona przed zagrożeniami integruje alerty z [Azure Security Center](https://azure.microsoft.com/services/security-center/), które zawierają szczegółowe informacje o podejrzanych działaniach i zalecaną akcję dotyczącą badania i łagodzenia zagrożeń. Zaawansowana ochrona przed zagrożeniami pozwala łatwo rozwiązywać potencjalne zagrożenia dla bazy danych bez konieczności być specjalistą ds. zabezpieczeń ani zarządzać zaawansowanymi systemami monitorowania zabezpieczeń.

Aby przeprowadzić pełne badanie, zaleca się włączenie inspekcji, która zapisuje zdarzenia bazy danych w dzienniku inspekcji na koncie usługi Azure Storage.  Aby włączyć inspekcję, zobacz [Inspekcja dla Azure SQL Database i Azure Synapse](../../azure-sql/database/auditing-overview.md) lub [inspekcji dla wystąpienia zarządzanego Azure SQL](../managed-instance/auditing-configure.md).

## <a name="alerts"></a>Alerty

Zaawansowana ochrona przed zagrożeniami dla Azure SQL Database wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Aby uzyskać listę alertów dla Azure SQL Database, zobacz [alerty dotyczące SQL Database i SQL Data Warehouse w Azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse).

## <a name="explore-detection-of-a-suspicious-event"></a>Eksplorowanie wykrywania podejrzanego zdarzenia

Po wykryciu nietypowych działań bazy danych otrzymasz powiadomienie e-mail. Wiadomość e-mail zawiera informacje dotyczące podejrzanego zdarzenia zabezpieczeń, w tym charakter nietypowych działań, nazwę bazy danych, nazwę serwera, nazwę aplikacji i czas zdarzenia. Ponadto wiadomość e-mail zawiera informacje o możliwych przyczynach i zalecanych działaniach związanych z badaniem i łagodzeniem potencjalnego zagrożenia dla bazy danych.

![Raport o nietypowych działaniach](./media/threat-detection-overview/anomalous_activity_report.png)

1. Kliknij link **Wyświetl Ostatnie alerty SQL** w wiadomości e-mail, aby uruchomić Azure Portal i wyświetlić stronę Azure Security Center alertów, która zawiera przegląd aktywnych zagrożeń wykrytych w bazie danych.

   ![Zagrożenia aktywności](./media/threat-detection-overview/active_threats.png)

2. Kliknij konkretny alert, aby uzyskać dodatkowe szczegóły i akcje związane z badaniem tego zagrożenia i korygowaniem przyszłe zagrożenia.

   Na przykład iniekcja SQL to jeden z najczęstszych problemów z zabezpieczeniami aplikacji sieci Web w Internecie, który jest używany do ataku na aplikacje oparte na danych. Osoby atakujące wykorzystują luki w zabezpieczeniach, aby wstrzyknąć złośliwe instrukcje SQL do pól wprowadzania aplikacji, naruszania lub modyfikowania danych w bazie danych. W przypadku alertów o iniekcji SQL szczegóły alertu obejmują wykorzystaną przez lukę instrukcję SQL.

   ![Konkretny alert](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-azure-portal"></a>Eksplorowanie alertów w Azure Portal

Zaawansowana ochrona przed zagrożeniami integruje swoje alerty z [usługą Azure Security Center](https://azure.microsoft.com/services/security-center/). Kafelki SQL Advanced Threat Protection na żywo w ramach bazy danych i bloki usługi SQL AD w Azure Portal śledzą stan aktywnych zagrożeń.

Kliknij **alert zaawansowanej ochrony przed zagrożeniami** , aby uruchomić stronę alerty Azure Security Center i zapoznać się z omówieniem aktywnych zagrożeń SQL wykrytych w bazie danych.

   ![Alert dotyczący zaawansowanej ochrony przed zagrożeniami](./media/threat-detection-overview/threat_detection_alert.png)

   ![Zaawansowana ochrona przed zagrożeniami Alert2](./media/threat-detection-overview/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami w Azure SQL Database & Azure Synapse](threat-detection-configure.md).
- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami w wystąpieniu zarządzanym usługi Azure SQL](../managed-instance/threat-detection-configure.md).
- Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych](advanced-data-security.md).
- Dowiedz się więcej na temat [inspekcji Azure SQL Database](../../azure-sql/database/auditing-overview.md)
- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
