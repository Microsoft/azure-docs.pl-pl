---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych w Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: 68ab5c820f3a67a7fd332557d47918d2a7aa4b62
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789424"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Zaawansowana ochrona przed zagrożeniami](threat-detection-overview.md) dla Azure SQL Database wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Zaawansowana ochrona przed zagrożeniami może identyfikować **potencjalną iniekcję kodu SQL** , **uzyskiwać dostęp z nietypowej lokalizacji lub centrum danych** , **uzyskiwać dostęp z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwej aplikacji** oraz **wymuszać poświadczenia SQL** — Zobacz więcej szczegółów w temacie [Advanced Threat Protection](threat-detection-overview.md#alerts)

Powiadomienia dotyczące wykrytych zagrożeń można odbierać za pośrednictwem [powiadomień e-mail](threat-detection-overview.md#explore-detection-of-a-suspicious-event) lub [Azure Portal](threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Zaawansowana ochrona przed zagrożeniami](threat-detection-overview.md) jest częścią [usługi Azure Defender dla oferty SQL](azure-defender-for-sql.md) , która jest ujednoliconym pakietem dla zaawansowanych możliwości zabezpieczeń SQL. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem centralnej usługi Azure Defender dla programu SQL Portal.

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami w Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do strony konfiguracji serwera, który ma być chroniony. W ustawieniach zabezpieczeń wybierz pozycję **Security Center** .
3. Na stronie Konfiguracja **usługi Azure Defender** :

   - Włącz usługę Azure Defender na serwerze.
   - W obszarze **Ustawienia zaawansowanej ochrony przed zagrożeniami** w polu tekstowym **Wyślij alerty do** wprowadź listę wiadomości e-mail, które mają otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.

## <a name="set-up-advanced-threat-protection-using-powershell"></a>Konfigurowanie usługi Advanced Threat Protection przy użyciu programu PowerShell

Aby zapoznać się z przykładem skryptu, zobacz [Konfigurowanie inspekcji i zaawansowanej ochrony przed zagrożeniami przy użyciu programu PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](threat-detection-overview.md).
- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami w wystąpieniu zarządzanym SQL](../managed-instance/threat-detection-configure.md).  
- Dowiedz się więcej o [usłudze Azure Defender dla języka SQL](azure-defender-for-sql.md).
- Dowiedz się więcej na temat [inspekcji](../../azure-sql/database/auditing-overview.md)
- Dowiedz się więcej o [usłudze Azure Security Center](../../security-center/security-center-introduction.md)
- Aby uzyskać więcej informacji na temat cen, zobacz [stronę z cennikiem SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) .