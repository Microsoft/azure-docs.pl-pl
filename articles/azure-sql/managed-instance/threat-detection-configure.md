---
title: Konfigurowanie zaawansowanej ochrony przed zagrożeniami
titleSuffix: Azure SQL Managed Instance
description: Zaawansowana ochrona przed zagrożeniami wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych w wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 12/01/2020
ms.openlocfilehash: 69bebcf872f55055117acf5cef410d1f89eafe34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96446922"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami w wystąpieniu zarządzanym usługi Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Zaawansowana ochrona przed zagrożeniami](../database/threat-detection-overview.md) dla [wystąpienia zarządzanego usługi Azure SQL](sql-managed-instance-paas-overview.md) wykrywa nietypowe działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Zaawansowana ochrona przed zagrożeniami może identyfikować **potencjalną iniekcję kodu SQL**, **uzyskiwać dostęp z nietypowej lokalizacji lub centrum danych**, **uzyskiwać dostęp z nieznanego podmiotu zabezpieczeń lub potencjalnie szkodliwej aplikacji** oraz **wymuszać poświadczenia SQL** — Zobacz więcej szczegółów w temacie [Advanced Threat Protection](../database/threat-detection-overview.md#alerts)

Powiadomienia dotyczące wykrytych zagrożeń można odbierać za pośrednictwem [powiadomień e-mail](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event) lub [Azure Portal](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)

[Zaawansowana ochrona przed zagrożeniami](../database/threat-detection-overview.md) jest częścią [usługi Azure Defender dla oferty SQL](../database/azure-defender-for-sql.md)  , która jest ujednoliconym pakietem dla zaawansowanych możliwości zabezpieczeń SQL. Dostęp do zaawansowanej ochrony przed zagrożeniami można uzyskać i zarządzać nimi za pośrednictwem centralnej usługi Azure Defender dla programu SQL Portal.

##  <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do  [Azure Portal](https://portal.azure.com). 
2. Przejdź do strony Konfiguracja wystąpienia wystąpienia zarządzanego SQL, które chcesz chronić. W obszarze **zabezpieczenia** wybierz pozycję **Security Center**.
3. Na stronie Konfiguracja usługi Azure Defender dla programu SQL Server
   - Włącz **usługę Azure Defender** dla języka SQL.
   - Skonfiguruj **wysyłanie alertów na** adres e-mail, aby otrzymywać alerty zabezpieczeń po wykryciu nietypowych działań bazy danych.
   - Wybierz **konto usługi Azure Storage** , w którym są zapisywane rekordy nietypowych zagrożeń inspekcji.
   - Wybierz **typy zaawansowanej ochrony przed zagrożeniami** , które chcesz skonfigurować. Dowiedz się więcej o [alertach dotyczących zaawansowanej ochrony przed zagrożeniami](../database/threat-detection-overview.md).
4. Kliknij przycisk **Zapisz** , aby zapisać nowe lub zaktualizowane zasady usługi Azure Defender dla programu SQL Server.

   :::image type="content" source="../database/media/azure-defender-for-sql/set-up-advanced-threat-protection-mi.png" alt-text="Skonfiguruj zaawansowaną ochronę przed zagrożeniami":::

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](../database/threat-detection-overview.md).
- Dowiedz się więcej o wystąpieniach zarządzanych, zobacz [co to jest wystąpienie zarządzane usługi Azure SQL](sql-managed-instance-paas-overview.md).
- Dowiedz się więcej [na temat zaawansowanej ochrony przed zagrożeniami dla Azure SQL Database](../database/threat-detection-configure.md).
- Dowiedz się więcej na temat [inspekcji wystąpienia zarządzanego SQL](./auditing-configure.md).
- Dowiedz się więcej o [usłudze Azure Security Center](../../security-center/security-center-introduction.md).