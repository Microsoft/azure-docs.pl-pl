---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami
titleSuffix: Azure SQL Managed Instance
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych w wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: ceb6285448df2a5d87dfa87ab249c99bf22c9928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84686328"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami w wystąpieniu zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Zaawansowana ochrona przed zagrożeniami](../database/threat-detection-overview.md) dla [wystąpienia zarządzanego usługi Azure SQL](sql-managed-instance-paas-overview.md) wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Zaawansowana ochrona przed zagrożeniami może identyfikować **potencjalną iniekcję kodu SQL**, **uzyskiwać dostęp z nietypowej lokalizacji lub centrum danych**, **uzyskiwać dostęp z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwej aplikacji**oraz **wymuszać poświadczenia SQL** — Zobacz więcej szczegółów w temacie [Advanced Threat Protection](../database/threat-detection-overview.md#alerts)

Powiadomienia dotyczące wykrytych zagrożeń można odbierać za pośrednictwem [powiadomień e-mail](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) lub [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

Zaawansowana [Ochrona przed zagrożeniami](../database/threat-detection-overview.md) jest częścią [zaawansowanej oferty zabezpieczeń danych](../database/advanced-data-security.md) , która jest ujednoliconym pakietem zaawansowanych funkcji zabezpieczeń SQL. Aby uzyskiwać dostęp do funkcji Advanced Threat Protection i zarządzać nią, można korzystać centralnego portalu pakietu SQL ADS.

##  <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
2. Przejdź do strony Konfiguracja wystąpienia wystąpienia zarządzanego SQL, które chcesz chronić. Na stronie **Ustawienia** wybierz pozycję **Zaawansowane zabezpieczenia danych**.
3. Na stronie Konfiguracja zabezpieczeń danych zaawansowanych
   - Włącz **Zaawansowane** zabezpieczenia danych.
   - Skonfiguruj **listę wiadomości e-mail** , aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
   - Wybierz **konto usługi Azure Storage** , w którym są zapisywane rekordy nietypowych zagrożeń inspekcji.
   - Wybierz **typy zaawansowanej ochrony przed zagrożeniami** , które chcesz skonfigurować. Dowiedz się więcej o [alertach dotyczących zaawansowanej ochrony przed zagrożeniami](../database/threat-detection-overview.md).
4. Kliknij przycisk **Zapisz** , aby zapisać nowe lub zaktualizowane zasady zaawansowanej ochrony danych.

   ![Zaawansowana ochrona przed zagrożeniami](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](../database/threat-detection-overview.md).
- Dowiedz się więcej o wystąpieniach zarządzanych, zobacz [co to jest wystąpienie zarządzane usługi Azure SQL](sql-managed-instance-paas-overview.md).
- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami dla Azure SQL Database](../database/threat-detection-configure.md).
- Dowiedz się więcej na temat [inspekcji wystąpienia zarządzanego SQL](https://go.microsoft.com/fwlink/?linkid=869430).
- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
