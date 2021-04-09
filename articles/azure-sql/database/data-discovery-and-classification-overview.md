---
title: Odnajdywanie i klasyfikacja danych
description: Funkcja odnajdywania danych & Klasyfikacja dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i usługi Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
tags: azure-synapse
ms.openlocfilehash: f47303a953dbb06b4c9efdfbbae41715cad81d2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690808"
---
# <a name="data-discovery--classification"></a>Odnajdywanie i klasyfikacja danych
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Odnajdywanie i klasyfikacja danych jest wbudowane w usługi Azure SQL Database, Azure SQL Managed Instance i Azure Synapse Analytics. Funkcja zapewnia podstawowe możliwości w zakresie odnajdywania, klasyfikowania, etykietowania i raportowania danych poufnych w bazach danych.

Najważniejsze dane mogą obejmować działalność, finanse, opiekę lub dane osobowe. Odnajdywanie i Klasyfikowanie tych danych może odgrywać rolę przestawną w podejściu do ochrony informacji w organizacji. Może to stanowić infrastrukturę dla następujących działań:

- Pomoc w spełnianiu standardów dotyczących prywatności danych i wymagań dotyczących zgodności z przepisami.
- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja), dostęp do poufnych danych.
- Kontrolowanie dostępu do i ograniczanie bezpieczeństwa baz danych zawierających wysoce poufne dane.

> [!NOTE]
> Aby uzyskać informacje na temat SQL Server lokalnych, zobacz temat [klasyfikacja & SQL Data Discovery](/sql/relational-databases/security/sql-data-discovery-and-classification).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Co to jest funkcja odnajdywania danych & Klasyfikacja?

Funkcja odnajdywania danych & Klasyfikacja zawiera zestaw podstawowych usług i nowych możliwości platformy Azure. Jest to nowy model ochrony informacji, który SQL Database, wystąpienia zarządzane SQL i Azure Synapse, na potrzeby ochrony danych, a nie tylko bazy danych. Model zawiera:

- **Odnajdywanie i zalecenia:** Aparat klasyfikacji skanuje bazę danych i identyfikuje kolumny zawierające potencjalnie wrażliwe dane. Dzięki temu można łatwo przejrzeć i zastosować zalecaną klasyfikację za pośrednictwem Azure Portal.

- **Etykietowanie:** Etykiety klasyfikacji czułości można stosować trwale do kolumn przy użyciu nowych atrybutów metadanych, które zostały dodane do aparatu bazy danych SQL Server. Te metadane mogą być następnie używane w scenariuszach inspekcji i ochrony opartej na czułości.

- **Czułość zestawu wyników zapytania:** Czułość zestawu wyników zapytania jest obliczana w czasie rzeczywistym na potrzeby inspekcji.

- **Widoczność:** Można wyświetlić stan klasyfikacji bazy danych na szczegółowym pulpicie nawigacyjnym w Azure Portal. Ponadto można pobrać raport w formacie programu Excel, który będzie używany na potrzeby zgodności i inspekcji oraz innych potrzeb.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Odnajdź, klasyfikuj i Oznacz kolumny jako poufne

W tej sekcji opisano kroki dla programu:

- Odnajdywanie, klasyfikowanie i etykietowanie kolumn zawierających dane poufne w bazie danych.
- Wyświetlanie bieżącego stanu klasyfikacji bazy danych i Eksportowanie raportów.

Klasyfikacja obejmuje dwa atrybuty metadanych:

- **Etykiety**: główne atrybuty klasyfikacji używane do definiowania poziomu czułości danych przechowywanych w kolumnie.  
- **Typy informacji**: atrybuty, które dostarczają bardziej szczegółowych informacji na temat typu danych przechowywanych w kolumnie.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definiowanie i dostosowywanie taksonomii klasyfikacji

Funkcja odnajdywania danych & Klasyfikacja zawiera wbudowany zestaw etykiet czułości i wbudowany zestaw typów informacji oraz logiki odnajdywania. Teraz można dostosowywać tę taksonomię i definiować zestaw oraz ranking konstrukcji klasyfikacji przeznaczonych dla danego środowiska.

Możesz definiować i dostosowywać taksonomię klasyfikacji w jednym centralnym miejscu dla całej organizacji platformy Azure. Ta lokalizacja znajduje się w [Azure Security Center](../../security-center/security-center-introduction.md)w ramach zasad zabezpieczeń. To zadanie może wykonać tylko osoba z uprawnieniami administracyjnymi w głównej grupie zarządzania w organizacji.

W ramach zarządzania zasadami dotyczącymi ochrony informacji można definiować etykiety niestandardowe, klasyfikować je i kojarzyć z wybranym zestawem typów informacji. Możesz również dodać własne typy informacji niestandardowych i skonfigurować je za pomocą wzorców ciągów. Wzorce są dodawane do logiki odnajdywania w celu identyfikowania tego typu danych w bazach danych.

Aby uzyskać więcej informacji, zobacz [Dostosowywanie zasad usługi SQL Information Protection w Azure Security Center (wersja zapoznawcza)](../../security-center/security-center-info-protection-policy.md).

Po zdefiniowaniu zasad w całej organizacji można kontynuować klasyfikowanie poszczególnych baz danych przy użyciu zasad niestandardowych.

### <a name="classify-your-database"></a>Klasyfikowanie bazy danych

> [!NOTE]
> Poniższy przykład używa Azure SQL Database, ale należy wybrać odpowiedni produkt, dla którego chcesz skonfigurować klasyfikację & odnajdywania danych.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).

1. Przejdź do obszaru **odnajdywanie danych & Klasyfikacja** pod nagłówkiem **zabezpieczenia** w okienku Azure SQL Database. Karta przegląd zawiera podsumowanie bieżącego stanu klasyfikacji bazy danych. Podsumowanie zawiera szczegółową listę wszystkich sklasyfikowanych kolumn, które można również filtrować w celu wyświetlania tylko określonych części schematu, typów informacji i etykiet. Jeśli nie zostały jeszcze sklasyfikowane żadne kolumny, [Przejdź do kroku 4](#step-4).

    ![Omówienie](./media/data-discovery-and-classification-overview/data-discovery-and-classification.png)

1. Aby pobrać raport w formacie programu Excel, wybierz opcję **Eksportuj** w górnym menu okienka.

1. <a id="step-4"></a>Aby rozpocząć klasyfikowanie danych, wybierz kartę **Klasyfikacja** na stronie **Klasyfikacja & odnajdywania danych** .

    Aparat klasyfikacji skanuje bazę danych pod kątem kolumn zawierających potencjalnie wrażliwe dane i zawiera listę zalecanych klasyfikacji kolumn.

1. Wyświetl i Zastosuj zalecenia dotyczące klasyfikacji:

   - Aby wyświetlić listę zalecanych klasyfikacji kolumn, wybierz Panel zalecenia w dolnej części okienka.

   - Aby zaakceptować zalecenie dotyczące konkretnej kolumny, zaznacz pole wyboru w lewej kolumnie odpowiedniego wiersza. Aby oznaczyć wszystkie rekomendacje jako akceptowane, zaznacz pole wyboru z lewej strony w nagłówku tabeli rekomendacji.

   - Aby zastosować wybrane zalecenia, wybierz pozycję **Zaakceptuj wybrane rekomendacje**.

   ![Zalecenia dotyczące klasyfikacji](./media/data-discovery-and-classification-overview/recommendation.png)

1. Można również klasyfikować kolumny ręcznie, jako alternatywę lub jako uzupełnienie klasyfikacji opartej na rekomendacji:

   1. Wybierz pozycję **Dodaj klasyfikację** w górnym menu okienka.

   1. W otwartym oknie kontekstu wybierz schemat, tabelę i kolumnę, które chcesz sklasyfikować, a następnie typ informacji i etykieta czułości.

   1. Wybierz pozycję **Dodaj klasyfikację** u dołu okna kontekstu.

   ![Ręcznie Dodaj klasyfikację](./media/data-discovery-and-classification-overview/manually-add-classification.png)


1. Aby dokończyć swoją klasyfikację i trwale oznaczyć (tag) kolumnami bazy danych nowymi metadanymi klasyfikacji, wybierz pozycję **Zapisz** na stronie **Klasyfikacja** .

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Inspekcja dostępu do poufnych danych

Ważnym aspektem modelu ochrony informacji jest możliwość monitorowania dostępu do poufnych danych. [Inspekcja usługi Azure SQL](../../azure-sql/database/auditing-overview.md) została ulepszona w celu uwzględnienia nowego pola w dzienniku inspekcji o nazwie `data_sensitivity_information` . To pole służy do rejestrowania klasyfikacji czułości (etykiet) danych zwróconych przez zapytanie. Oto przykład:

![Dziennik inspekcji](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Uprawnienia

Te wbudowane role mogą odczytywać klasyfikację danych bazy danych:

- Właściciel
- Czytelnik
- Współautor
- Menedżer zabezpieczeń SQL
- Administrator dostępu użytkowników

Te wbudowane role mogą modyfikować klasyfikację danych bazy danych:

- Właściciel
- Współautor
- Menedżer zabezpieczeń SQL

Dowiedz się więcej na temat uprawnień opartych na rolach w [usłudze Azure RBAC](../../role-based-access-control/overview.md).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Klasyfikacje zarządzania

Aby zarządzać klasyfikacjami, można użyć języka T-SQL, interfejsu API REST lub programu PowerShell.

### <a name="use-t-sql"></a>Korzystanie z języka T-SQL

Możesz użyć T-SQL, aby dodać lub usunąć klasyfikacje kolumn i pobrać wszystkie klasyfikacje dla całej bazy danych.

> [!NOTE]
> W przypadku zarządzania etykietami przy użyciu języka T-SQL nie jest sprawdzana żadna weryfikacja, że etykiety dodawane do kolumny istnieją w zasadach ochrony informacji w organizacji (zestaw etykiet, które są wyświetlane w zaleceniach portalu). W ten sposób można sprawdzić poprawność tego elementu.

Aby uzyskać informacje na temat używania języka T-SQL dla klasyfikacji, zobacz następujące odwołania:

- Aby dodać lub zaktualizować klasyfikację jednej lub kilku kolumn: [Dodaj klasyfikację czułości](/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Aby usunąć klasyfikację z jednej lub kilku kolumn: [Klasyfikacja czułości](/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Aby wyświetlić wszystkie klasyfikacje w bazie danych: [sys.sensitivity_classifications](/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>Korzystanie z poleceń cmdlet programu PowerShell
Zarządzaj klasyfikacjami i zaleceniami dotyczącymi Azure SQL Database i wystąpienia zarządzanego Azure SQL przy użyciu programu PowerShell.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Polecenia cmdlet programu PowerShell dla Azure SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Polecenia cmdlet programu PowerShell dla wystąpienia zarządzanego Azure SQL

- [Get-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Korzystanie z interfejsu API REST

Za pomocą interfejsu API REST można programowo zarządzać klasyfikacjami i zaleceniami. Opublikowany interfejs API REST obsługuje następujące operacje:

- [Utwórz lub zaktualizuj](/rest/api/sql/sensitivitylabels/createorupdate): tworzy lub aktualizuje etykietę czułości dla określonej kolumny.
- [Usuń](/rest/api/sql/sensitivitylabels/delete): usuwa etykietę czułości dla określonej kolumny.
- [Wyłącz rekomendacje](/rest/api/sql/sensitivitylabels/disablerecommendation): wyłącza zalecenia dotyczące czułości w określonej kolumnie.
- [Włącz rekomendacje](/rest/api/sql/sensitivitylabels/enablerecommendation): włącza zalecenia dotyczące czułości w określonej kolumnie. (Zalecenia są domyślnie włączone dla wszystkich kolumn).
- [Get](/rest/api/sql/sensitivitylabels/get): Pobiera etykietę czułości dla określonej kolumny.
- [Lista bieżąca według bazy danych](/rest/api/sql/sensitivitylabels/listcurrentbydatabase): pobiera bieżące etykiety czułości określonej bazy danych.
- [Lista zalecana przez bazę danych](/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Pobiera zalecane etykiety czułości określonej bazy danych.


## <a name="faq---advanced-classification-capabilities"></a>Często zadawane pytania — zaawansowane funkcje klasyfikacji

**Pytanie**: czy [usługa Azure kontrolą](../../purview/overview.md) zastąpi &ą klasyfikację i czy zostanie wycofana funkcja odnajdywania danych SQL & Klasyfikacja?
**Odpowiedź**: nadal obsługujemy funkcję klasyfikacji & danych SQL i zachęcamy do wdrażania [usługi Azure kontrolą](../../purview/overview.md) , która ma bogatsze możliwości w zakresie możliwości zaawansowanej klasyfikacji i zarządzania danymi. Jeśli zdecydujemy się wycofać jakąkolwiek usługę, funkcję, interfejs API lub jednostkę SKU, otrzymasz informacje z wyprzedzeniem wraz ze ścieżką migracji lub przejścia. Więcej informacji na temat zasad cyklu życia firmy Microsoft znajdziesz tutaj.


## <a name="next-steps"></a><a id="next-steps"></a>Następne kroki

- Należy rozważyć skonfigurowanie [inspekcji usługi Azure SQL](../../azure-sql/database/auditing-overview.md) na potrzeby monitorowania i inspekcji dostępu do danych poufnych.
- Aby poznać prezentację obejmującą &ą klasyfikację, zobacz [odnajdywanie, klasyfikowanie, etykietowanie & Ochrona danych SQL | Dane uwidocznione](https://www.youtube.com/watch?v=itVi9bkJUNc).
- Aby sklasyfikować bazy danych Azure SQL Database i analizy Synapse Azure za pomocą etykiet usługi Azure kontrolą przy użyciu poleceń języka T-SQL, zobacz artykuł [klasyfikowanie danych usługi Azure SQL przy użyciu etykiet kontrolą na platformie Azure](../../sql-database/scripts/sql-database-import-purview-labels.md).