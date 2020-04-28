---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych w pojedynczej bazie danych lub puli elastycznej.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 8eb8e4fccc17fe31def671cf6e8edb19d867b244
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822506"
---
# <a name="azure-sql-database-advanced-threat-protection-for-single-or-pooled-databases"></a>Azure SQL Database zaawansowaną ochronę przed zagrożeniami dla jednej lub puli baz danych

[Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) dla baz danych o pojedynczej i puli wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Zaawansowana ochrona przed zagrożeniami może identyfikować **potencjalną iniekcję kodu SQL**, **uzyskiwać dostęp z nietypowej lokalizacji lub centrum danych**, **uzyskiwać dostęp z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwej aplikacji**oraz **wymuszać poświadczenia SQL** — Zobacz więcej szczegółów w temacie [Advanced Threat Protection](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)

Powiadomienia dotyczące wykrytych zagrożeń można odbierać za pośrednictwem [powiadomień e-mail](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) lub [Azure Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)

[Zaawansowana ochrona przed zagrożeniami](sql-database-threat-detection-overview.md) jest częścią oferty [Advanced Data Security](sql-database-advanced-data-security.md) (ADS), która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Aby uzyskiwać dostęp do funkcji Advanced Threat Protection i zarządzać nią, można korzystać centralnego portalu pakietu SQL ADS.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami w Azure Portal

1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera Azure SQL Database, który ma być chroniony. W obszarze Ustawienia zabezpieczeń wybierz pozycję **Zaawansowane zabezpieczenia danych**.
3. Na stronie Konfiguracja **zabezpieczeń danych zaawansowanych** :

   - Włącz zaawansowane zabezpieczenia danych na serwerze.
   - W obszarze **Ustawienia zaawansowanej ochrony przed zagrożeniami**w polu tekstowym **Wyślij alerty do** wprowadź listę wiadomości e-mail, które mają otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Skonfiguruj zaawansowaną ochronę przed zagrożeniami](./media/sql-database-threat-detection/set_up_threat_detection.png)

   > [!NOTE]
   > Ceny na zrzutach ekranu nie zawsze odzwierciedlają bieżącą cenę i są przykładem.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurowanie usługi Advanced Threat Protection przy użyciu programu PowerShell

Aby zapoznać się z przykładem skryptu, zobacz [Konfigurowanie inspekcji i zaawansowanej ochrony przed zagrożeniami przy użyciu programu PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](sql-database-threat-detection-overview.md).
- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami w wystąpieniu zarządzanym](sql-database-managed-instance-threat-detection.md).  
- Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych](sql-database-advanced-data-security.md).
- Dowiedz się więcej na temat [inspekcji](sql-database-auditing.md)
- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
