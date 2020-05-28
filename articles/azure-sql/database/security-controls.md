---
title: Mechanizmy zabezpieczeń
description: Lista kontrolna kontroli zabezpieczeń dla oceny Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 09045e01ad4d40ab770dd6203f2dd4b299317a55
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050009"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Kontrolki zabezpieczeń dla Azure SQL Database i wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Yes | Dotyczy tylko [SQL Database](../index.yml) . |
| Obsługa iniekcji Virtual Network platformy Azure| Yes | Dotyczy tylko [wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Izolacja sieci i obsługa zapory| Yes | Zapora zarówno na poziomie bazy danych, jak i na poziomie serwera. Izolacja sieciowa jest tylko dla [wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Obsługa tunelowania wymuszonego| Yes | [Wystąpienie zarządzane SQL](../managed-instance/sql-managed-instance-paas-overview.md) za pośrednictwem sieci VPN [ExpressRoute](../expressroute/../index.yml) . |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure, taka jak Log Analytics lub Application Insights| Yes | SecureSphere rozwiązanie SIEM z Imperva jest również obsługiwane przez integrację z [usługą Azure Event Hubs](../event-hubs/../index.yml) przy użyciu funkcji [inspekcji SQL](../../azure-sql/database/auditing-overview.md). |
| Rejestrowanie i inspekcja w płaszczyźnie kontroli i zarządzania| Yes | Tak tylko w przypadku niektórych zdarzeń |
| Rejestrowanie i inspekcja płaszczyzny danych | Yes | Za pośrednictwem [inspekcji SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Azure Active Directory (Azure AD) |
| Autoryzacja| Yes | Brak |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Yes | Nazywane "szyfrowaniem w użyciu", zgodnie z opisem w artykule [Always Encrypted](always-encrypted-certificate-store-configure.md). Szyfrowanie po stronie serwera używa [przezroczystego szyfrowania danych](transparent-data-encryption-tde-overview.md).|
| Szyfrowanie podczas przesyłania:<ul><li>Szyfrowanie ExpressRoute platformy Azure</li><li>Szyfrowanie w sieci wirtualnej</li><li>Szyfrowanie między sieciami wirtualnymi</ul>| Yes | Przy użyciu protokołu HTTPS. |
| Obsługa kluczy szyfrowania, taka jak CMK lub BYOK| Yes | Oferowana jest obsługa klucza zarządzanego przez usługę i zarządzane przez klienta. Ten drugi jest oferowany w [Azure Key Vault](../key-vault/../index.yml). |
| Szyfrowanie na poziomie kolumny udostępniane przez usługi danych platformy Azure| Yes | Za [Always Encrypted](always-encrypted-certificate-store-configure.md). |
| Zaszyfrowane wywołania interfejsu API| Yes | Przy użyciu protokołu HTTPS/TLS. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją, taka jak wersja konfiguracji| Nie  | Brak |

## <a name="additional-security-controls-for-sql-database"></a>Dodatkowe opcje zabezpieczeń dla SQL Database

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Prewencyjne: Ocena luk w zabezpieczeniach | Yes | Zapoznaj się z tematem [Usługa oceny luk w zabezpieczeniach w usłudze SQL Database](sql-vulnerability-assessment.md). |
| Prewencyjne: odnajdywanie i Klasyfikacja danych  | Yes | Zobacz [Azure SQL Database i SQL Data Warehouse odnajdywania danych & klasyfikację](data-discovery-and-classification-overview.md). |
| Wykrywanie: wykrywanie zagrożeń | Yes | Zobacz [zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database](threat-detection-overview.md). |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../../security/fundamentals/security-controls.md).
