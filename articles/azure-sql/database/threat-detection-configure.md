---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych w Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321550"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Zaawansowana ochrona przed zagrożeniami](threat-detection-overview.md) dla Azure SQL Database wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Zaawansowana ochrona przed zagrożeniami może identyfikować **potencjalną iniekcję kodu SQL**, **uzyskiwać dostęp z nietypowej lokalizacji lub centrum danych**, **uzyskiwać dostęp z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwej aplikacji**oraz **wymuszać poświadczenia SQL** — Zobacz więcej szczegółów w temacie [Advanced Threat Protection](threat-detection-overview.md#alerts)

Powiadomienia dotyczące wykrytych zagrożeń można odbierać za pośrednictwem [powiadomień e-mail](threat-detection-overview.md#explore-detection-of-a-suspicious-event) lub [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

Zaawansowana [Ochrona przed zagrożeniami](threat-detection-overview.md) jest częścią [zaawansowanej oferty zabezpieczeń danych](advanced-data-security.md) , która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem centralnego portalu SQL Advanced Data Security.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami w Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera, który ma być chroniony. W obszarze Ustawienia zabezpieczeń wybierz pozycję **Zaawansowane zabezpieczenia danych**.
3. Na stronie Konfiguracja **zabezpieczeń danych zaawansowanych** :

   - Włącz zaawansowane zabezpieczenia danych na serwerze.
   - W obszarze **Ustawienia zaawansowanej ochrony przed zagrożeniami**w polu tekstowym **Wyślij alerty do** wprowadź listę wiadomości e-mail, które mają otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
  
   ![Skonfiguruj zaawansowaną ochronę przed zagrożeniami](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurowanie usługi Advanced Threat Protection przy użyciu programu PowerShell

Aby zapoznać się z przykładem skryptu, zobacz [Konfigurowanie inspekcji i zaawansowanej ochrony przed zagrożeniami przy użyciu programu PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](threat-detection-overview.md).
- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami w wystąpieniu zarządzanym SQL](../managed-instance/threat-detection-configure.md).  
- Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych](advanced-data-security.md).
- Dowiedz się więcej na temat [inspekcji](../../azure-sql/database/auditing-overview.md)
- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .  
