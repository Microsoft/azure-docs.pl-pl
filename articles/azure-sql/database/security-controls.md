---
title: Mechanizmy zabezpieczeń
description: Lista kontrolna kontroli zabezpieczeń dla oceny Azure SQL Database
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442092"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Kontrolki zabezpieczeń dla Azure SQL Database i wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w Azure SQL Database i wystąpienie zarządzane usługi Azure SQL.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak | Dotyczy tylko [SQL Database](../index.yml) . |
| Obsługa iniekcji Virtual Network platformy Azure| Tak | Dotyczy tylko [wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Izolacja sieci i obsługa zapory| Tak | Zapora zarówno na poziomie bazy danych, jak i na poziomie serwera. Izolacja sieciowa jest tylko dla [wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md) . |
| Obsługa tunelowania wymuszonego| Tak | [Wystąpienie zarządzane SQL](../managed-instance/sql-managed-instance-paas-overview.md) za pośrednictwem sieci VPN [ExpressRoute](../expressroute/../index.yml) . |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure, taka jak Log Analytics lub Application Insights| Tak | SecureSphere rozwiązanie SIEM z Imperva jest również obsługiwane przez integrację z [usługą Azure Event Hubs](../event-hubs/../index.yml) przy użyciu funkcji [inspekcji SQL](../../azure-sql/database/auditing-overview.md). |
| Rejestrowanie i inspekcja w płaszczyźnie kontroli i zarządzania| Tak | Tak tylko w przypadku niektórych zdarzeń |
| Rejestrowanie i inspekcja płaszczyzny danych | Tak | Za pośrednictwem [inspekcji SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak | Azure Active Directory (Azure AD) |
| Autoryzacja| Tak | Brak |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Nazywane "szyfrowaniem w użyciu", zgodnie z opisem w artykule [Always Encrypted](always-encrypted-certificate-store-configure.md). Szyfrowanie po stronie serwera używa [przezroczystego szyfrowania danych](transparent-data-encryption-tde-overview.md).|
| Szyfrowanie podczas przesyłania:<ul><li>Szyfrowanie ExpressRoute platformy Azure</li><li>Szyfrowanie w sieci wirtualnej</li><li>Szyfrowanie między sieciami wirtualnymi</ul>| Tak | Przy użyciu protokołu HTTPS. |
| Obsługa kluczy szyfrowania, taka jak CMK lub BYOK| Tak | Oferowana jest obsługa klucza zarządzanego przez usługę i zarządzane przez klienta. Ten drugi jest oferowany w [Azure Key Vault](../key-vault/../index.yml). |
| Szyfrowanie na poziomie kolumny udostępniane przez usługi danych platformy Azure| Tak | Za [Always Encrypted](always-encrypted-certificate-store-configure.md). |
| Zaszyfrowane wywołania interfejsu API| Tak | Przy użyciu protokołu HTTPS/TLS. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją, taka jak wersja konfiguracji| Nie  | Brak |

## <a name="additional-security-controls-for-sql-database"></a>Dodatkowe opcje zabezpieczeń dla SQL Database

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Prewencyjne: Ocena luk w zabezpieczeniach | Tak | Zapoznaj się z tematem [Usługa oceny luk w zabezpieczeniach w usłudze SQL Database](sql-vulnerability-assessment.md). |
| Prewencyjne: odnajdywanie i Klasyfikacja danych  | Tak | Zapoznaj się z tematem [& klasyfikacji Azure SQL Database i usługi Azure Synapse Analytics Data Discovery](data-discovery-and-classification-overview.md). |
| Wykrywanie: wykrywanie zagrożeń | Tak | Zobacz [zaawansowaną ochronę przed zagrożeniami dla Azure SQL Database](threat-detection-overview.md). |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../../security/fundamentals/security-controls.md).
