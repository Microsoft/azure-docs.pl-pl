---
title: Azure Defender for SQL
description: Dowiedz się więcej o funkcji zarządzania lukami w bazie danych i wykrywaniu nietypowych działań, które mogą wskazywać na zagrożenie dla bazy danych w Azure SQL Database, wystąpieniu zarządzanym Azure SQL lub Azure Synapse.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452320"
---
# <a name="azure-defender-for-sql"></a>Azure Defender for SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Usługa Azure Defender for SQL to ujednolicony pakiet na potrzeby zaawansowanych funkcji zabezpieczeń SQL. Usługa Azure Defender jest dostępna do Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics. Obejmuje to funkcję wykrywania i klasyfikowania danych poufnych, uwidacznianie i korygowanie potencjalnych luk w zabezpieczeniach bazy danych oraz wykrywanie nietypowych działań, które mogą wskazywać, że baza danych jest zagrożona. Zapewnia ona pojedynczą lokalizację, w której można włączać te możliwości i zarządzać nimi.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Jakie korzyści zapewnia usługa Azure Defender dla programu SQL?

Usługa Azure Defender oferuje zestaw zaawansowanych funkcji zabezpieczeń SQL, w tym ocenę luk w zabezpieczeniach SQL i zaawansowaną ochronę przed zagrożeniami.
- [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to prosta w konfiguracji usługa, która umożliwia odnajdywanie, śledzenie i rozwiązywanie problemów z potencjalnymi lukami w zabezpieczeniach bazy danych. Zapewnia wgląd w stan zabezpieczeń i obejmuje czynności do wykonania w celu rozwiązywania problemów z zabezpieczeniami i ulepszania bazy danych FORTIFICATIONS.
- Usługa [Advanced Threat Protection](threat-detection-overview.md) wykrywa nietypowe działania wskazujące na nieprawidłowe i potencjalnie szkodliwe próby uzyskania dostępu do bazy danych lub wykorzystania jej. Ciągle monitoruje bazę danych pod kątem podejrzanych działań i zapewnia natychmiastowe alerty zabezpieczeń dotyczące potencjalnych luk w zabezpieczeniach, ataki iniekcji SQL Azure oraz nietypowe wzorce dostępu do bazy danych. Alerty usługi Advanced Threat Protection zawierają szczegółowe informacje o podejrzanych działaniach i zalecane czynności dotyczące sposobu badania i ograniczenia zagrożenia.

Włącz usługę Azure Defender dla SQL raz, aby włączyć wszystkie te funkcje. Po jednym kliknięciu można włączyć usługę Azure Defender dla wszystkich baz danych na [serwerze](logical-servers.md) na platformie Azure lub w wystąpieniu zarządzanym SQL. Włączanie ustawień usługi Azure Defender lub zarządzanie nimi wymaga przynależności do roli [programu SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) lub jednej z ról administratora bazy danych lub serwera.

Aby uzyskać więcej informacji na temat cennika usługi Azure Defender for SQL, zobacz [stronę z cennikiem Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender"></a>Włączanie usługi Azure Defender 
Istnieje wiele sposobów włączania planów usługi Azure Defender. Można ją włączyć na poziomie subskrypcji (**zalecane**) z:

- [Azure Security Center](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [Programowo przy użyciu interfejsu API REST, interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Policy](#enable-azure-defender-plans-programatically)

Alternatywnie można włączyć ją na poziomie zasobu, zgodnie z opisem w temacie [Włączanie usługi Azure Defender dla Azure SQL Database na poziomie zasobu](#enable-azure-defender-for-azure-sql-database-at-the-resource-level)

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Włącz usługę Azure Defender dla Azure SQL Database na poziomie subskrypcji z Azure Security Center
Aby włączyć usługę Azure Defender dla Azure SQL Database na poziomie subskrypcji z poziomu Azure Security Center:

1. W [Azure Portal](https://portal.azure.com)Otwórz **Security Center**.
1. Z menu Security Center wybierz pozycję **Cennik i ustawienia**.
1. Wybierz odpowiednią subskrypcję.
1. Zmień ustawienie planu na **włączone**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Włączanie usługi Azure Defender dla Azure SQL Database na poziomie subskrypcji.":::

1. Wybierz pozycję **Zapisz**.


### <a name="enable-azure-defender-plans-programatically"></a>Włącz programowo planów usługi Azure Defender 

Elastyczność platformy Azure pozwala na wiele metod programistycznych umożliwiających włączenie planów usługi Azure Defender. 

Aby włączyć usługę Azure Defender dla Twojej subskrypcji, użyj dowolnego z następujących narzędzi: 

| Metoda       | Instrukcje                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Interfejs API REST     | [Interfejs API cen](/rest/api/securitycenter/pricings)                                                                                                  |
| Interfejs wiersza polecenia platformy Azure    | [AZ Security Cennik](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Ceny pakietu](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>Włącz usługę Azure Defender dla Azure SQL Database na poziomie zasobu

Zalecamy włączenie planów usługi Azure Defender na poziomie subskrypcji, co może pomóc w tworzeniu niechronionych zasobów. Jeśli jednak masz przyczynę organizacyjną, aby włączyć usługę Azure Defender na poziomie serwera, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)Otwórz serwer lub wystąpienie zarządzane.
1. W obszarze nagłówek **zabezpieczeń** wybierz pozycję **Security Center**.
1. Wybierz pozycję **Włącz usługę Azure Defender dla serwera SQL**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Włącz usługę Azure Defender dla języka SQL z poziomu baz danych Azure SQL Database.":::

> [!NOTE]
> Konto magazynu jest automatycznie tworzone i konfigurowane do przechowywania wyników skanowania **oceny luk w zabezpieczeniach** . Jeśli usługa Azure Defender została już włączona dla innego serwera w tej samej grupie zasobów i regionie, używane jest istniejące konto magazynu.
>
> Koszt usługi Azure Defender jest wyrównany przy Azure Security Center użyciu cen warstwy Standardowa na węzeł, gdzie węzeł jest całym serwerem lub wystąpieniem zarządzanym. W ten sposób płacisz tylko raz na ochronę wszystkich baz danych na serwerze lub w wystąpieniu zarządzanym za pomocą usługi Azure Defender. Możesz wstępnie wypróbować usługę Azure Defender, korzystając z bezpłatnej wersji próbnej.


## <a name="manage-azure-defender-settings"></a>Zarządzanie ustawieniami usługi Azure Defender

Aby wyświetlić ustawienia usługi Azure Defender i zarządzać nimi:

1. W obszarze **zabezpieczenia** serwera lub wystąpienia zarządzanego wybierz pozycję **Security Center**.

    Na tej stronie zostanie wyświetlony stan usługi Azure Defender dla programu SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Sprawdzanie stanu usługi Azure Defender for SQL w bazach danych Azure SQL Database.":::

1. Jeśli usługa Azure Defender dla programu SQL jest włączona, zostanie wyświetlony link **Konfiguruj** , jak pokazano na poprzedniej ilustracji. Aby edytować ustawienia usługi Azure Defender dla programu SQL, wybierz pozycję **Konfiguruj**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Ustawienia usługi Azure Defender dla programu SQL Server.":::

1. Wprowadź niezbędne zmiany i wybierz pozycję **Zapisz**.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [oceny luk w zabezpieczeniach](sql-vulnerability-assessment.md)
- Dowiedz się więcej na temat [zaawansowanej ochrony przed zagrożeniami](threat-detection-configure.md)
- Dowiedz się więcej o [Azure Security Center](../../security-center/security-center-introduction.md)