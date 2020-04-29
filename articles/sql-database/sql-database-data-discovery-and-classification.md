---
title: Odnajdowanie i klasyfikacja danych
description: Klasyfikacja & odnajdywania danych dla Azure SQL Database i usługi Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: f05b4d4fec99aaa2fb79da46e2167d883d1f15ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767004"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Klasyfikacja & odnajdywania danych dla Azure SQL Database i usługi Azure Synapse Analytics

Klasyfikacja & odnajdywania danych jest wbudowana w Azure SQL Database. Zapewnia zaawansowane funkcje odnajdywania, klasyfikowania, etykietowania i raportowania poufnych danych w bazach danych.

Najważniejsze dane mogą obejmować działalność, finanse, opiekę lub dane osobowe. Odnajdywanie i Klasyfikowanie tych danych może odgrywać rolę przestawną w podejściu do ochrony informacji w organizacji. Może to stanowić infrastrukturę dla następujących działań:

- Pomoc w spełnianiu standardów dotyczących prywatności danych i wymagań dotyczących zgodności z przepisami.
- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i alerty dotyczące nietypowego dostępu do poufnych danych.
- Kontrolowanie dostępu do i ograniczanie bezpieczeństwa baz danych zawierających wysoce poufne dane.

Funkcja odnajdywania danych & jest częścią [zaawansowanej oferty zabezpieczeń danych](sql-database-advanced-data-security.md) , która stanowi ujednolicony pakiet zaawansowanych funkcji zabezpieczeń SQL. Możesz uzyskać dostęp do klasyfikacji & odnajdywania danych i zarządzać nią za pośrednictwem centralnej sekcji **zaawansowanego dostępu do danych SQL** w Azure Portal.

> [!NOTE]
> Ten artykuł odnosi się do Azure SQL Database i analizy Synapse Azure. Dla uproszczenia użyjemy *SQL Database* tutaj, aby odwołać się do SQL Database i usługi Azure Synapse. Aby uzyskać informacje na temat SQL Server (lokalnie), zobacz [odnajdywanie i Klasyfikacja danych SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Co to jest funkcja odnajdywania danych & Klasyfikacja?

Funkcja odnajdywania danych & Klasyfikacja zawiera zestaw zaawansowanych usług i nowych możliwości SQL Database. Stanowi ona nowy model ochrony informacji, który jest przeznaczony dla SQL Database, na przykład w celu ochrony danych, a nie tylko bazy danych. Model zawiera:

- **Odnajdywanie i zalecenia:** Aparat klasyfikacji skanuje bazę danych i identyfikuje kolumny zawierające potencjalnie wrażliwe dane. Dzięki temu można łatwo przejrzeć i zastosować zalecaną klasyfikację za pośrednictwem Azure Portal.

- **Etykietowanie:** Etykiety klasyfikacji czułości można stosować trwale do kolumn przy użyciu nowych atrybutów metadanych, które zostały dodane do aparatu bazy danych SQL. Te metadane mogą następnie służyć do zaawansowanego, opartego na czułości scenariuszy inspekcji i ochrony.

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

Możesz definiować i dostosowywać taksonomię klasyfikacji w jednym centralnym miejscu dla całej organizacji platformy Azure. Ta lokalizacja znajduje się w [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)w ramach zasad zabezpieczeń. To zadanie może wykonać tylko osoba z uprawnieniami administracyjnymi w głównej grupie zarządzania w organizacji.

W ramach zarządzania zasadami dotyczącymi usługi SQL Information Protection można definiować etykiety niestandardowe, klasyfikować je i kojarzyć z wybranym zestawem typów informacji. Możesz również dodać własne typy informacji niestandardowych i skonfigurować je za pomocą wzorców ciągów. Wzorce są dodawane do logiki odnajdywania w celu identyfikowania tego typu danych w bazach danych.

Dowiedz się więcej na temat dostosowywania zasad i zarządzania nimi za pomocą [zasad programu SQL Information Protection](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po zdefiniowaniu zasad w całej organizacji można kontynuować klasyfikowanie poszczególnych baz danych przy użyciu zasad niestandardowych.

### <a name="classify-your-sql-database"></a>Klasyfikowanie bazy danych SQL

1. Przejdź do [Azure Portal](https://portal.azure.com).

2. Przejdź do pozycji **Zaawansowane zabezpieczenia danych** w obszarze nagłówek **zabezpieczenia** w okienku Azure SQL Database. Wybierz pozycję **Zaawansowane zabezpieczenia danych**, a następnie wybierz kartę **Klasyfikacja danych &** .

   ![Zaawansowane okienko zabezpieczenia danych w Azure Portal](./media/sql-data-discovery-and-classification/data_classification.png)

3. Na stronie **klasyfikacja & odnajdywania danych** karta **Przegląd** zawiera podsumowanie bieżącego stanu klasyfikacji bazy danych. Podsumowanie zawiera szczegółową listę wszystkich sklasyfikowanych kolumn, które można również filtrować w celu wyświetlania tylko określonych części schematu, typów informacji i etykiet. Jeśli nie zostały jeszcze sklasyfikowane żadne kolumny, [Przejdź do kroku 5](#step-5).

   ![Podsumowanie bieżącego stanu klasyfikacji](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Aby pobrać raport w formacie programu Excel, wybierz opcję **Eksportuj** w górnym menu okienka.

5. <a id="step-5"></a>Aby rozpocząć klasyfikowanie danych, wybierz kartę **Klasyfikacja** na stronie **Klasyfikacja & odnajdywania danych** .

    Aparat klasyfikacji skanuje bazę danych pod kątem kolumn zawierających potencjalnie wrażliwe dane i zawiera listę zalecanych klasyfikacji kolumn.

6. Wyświetl i Zastosuj zalecenia dotyczące klasyfikacji:

   - Aby wyświetlić listę zalecanych klasyfikacji kolumn, wybierz Panel zalecenia w dolnej części okienka.

   - Aby zaakceptować zalecenie dotyczące konkretnej kolumny, zaznacz pole wyboru w lewej kolumnie odpowiedniego wiersza. Aby oznaczyć wszystkie rekomendacje jako akceptowane, zaznacz pole wyboru z lewej strony w nagłówku tabeli rekomendacji.

       ![Przejrzyj i wybierz z listy zaleceń klasyfikacji](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Aby zastosować wybrane zalecenia, wybierz pozycję **Zaakceptuj wybrane rekomendacje**.

7. Można również klasyfikować kolumny ręcznie, jako alternatywę lub jako uzupełnienie klasyfikacji opartej na rekomendacji:

   1. Wybierz pozycję **Dodaj klasyfikację** w górnym menu okienka.

   1. W otwartym oknie kontekstu wybierz schemat, tabelę i kolumnę, które chcesz sklasyfikować, a następnie typ informacji i etykieta czułości.

   1. Wybierz pozycję **Dodaj klasyfikację** u dołu okna kontekstu.

      ![Wybierz kolumnę do klasyfikowania](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Aby dokończyć swoją klasyfikację i trwale oznaczyć (tag) kolumnami bazy danych nowymi metadanymi klasyfikacji, wybierz pozycję **Zapisz** w górnym menu okna.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Inspekcja dostępu do poufnych danych

Ważnym aspektem modelu ochrony informacji jest możliwość monitorowania dostępu do poufnych danych. [Inspekcja Azure SQL Database](sql-database-auditing.md) została ulepszona w celu uwzględnienia nowego pola w dzienniku inspekcji `data_sensitivity_information`o nazwie. To pole służy do rejestrowania klasyfikacji czułości (etykiet) danych zwróconych przez zapytanie. Przykład:

![Dziennik inspekcji](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Uprawnienia

Te wbudowane role mogą odczytywać klasyfikację danych usługi Azure SQL Database:

- Właściciel
- Czytelnik
- Współautor
- Menedżer zabezpieczeń SQL
- Administrator dostępu użytkowników

Te wbudowane role mogą modyfikować klasyfikację danych usługi Azure SQL Database:

- Właściciel
- Współautor
- Menedżer zabezpieczeń SQL

Dowiedz się więcej o uprawnieniach opartych na rolach w [ramach RBAC dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Zarządzaj klasyfikacjami

Aby zarządzać klasyfikacjami, można użyć języka T-SQL, interfejsu API REST lub programu PowerShell.

### <a name="use-t-sql"></a>Korzystanie z języka T-SQL

Możesz użyć T-SQL, aby dodać lub usunąć klasyfikacje kolumn i pobrać wszystkie klasyfikacje dla całej bazy danych.

> [!NOTE]
> W przypadku zarządzania etykietami przy użyciu języka T-SQL nie jest sprawdzana żadna weryfikacja, że etykiety dodawane do kolumny istnieją w zasadach ochrony informacji w organizacji (zestaw etykiet, które są wyświetlane w zaleceniach portalu). W ten sposób można sprawdzić poprawność tego elementu.

Aby uzyskać informacje na temat używania języka T-SQL dla klasyfikacji, zobacz następujące odwołania:

- Aby dodać lub zaktualizować klasyfikację jednej lub kilku kolumn: [Dodaj klasyfikację czułości](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Aby usunąć klasyfikację z jednej lub kilku kolumn: [Klasyfikacja czułości](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Aby wyświetlić wszystkie klasyfikacje w bazie danych: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Korzystanie z interfejsu API REST

Za pomocą interfejsu API REST można programowo zarządzać klasyfikacjami i zaleceniami. Opublikowany interfejs API REST obsługuje następujące operacje:

- [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): tworzy lub aktualizuje etykietę czułości dla określonej kolumny.
- [Usuń](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): usuwa etykietę czułości dla określonej kolumny.
- [Wyłącz rekomendacje](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): wyłącza zalecenia dotyczące czułości w określonej kolumnie.
- [Włącz rekomendacje](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): włącza zalecenia dotyczące czułości w określonej kolumnie. (Zalecenia są domyślnie włączone dla wszystkich kolumn).
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Pobiera etykietę czułości dla określonej kolumny.
- [Lista bieżąca według bazy danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): pobiera bieżące etykiety czułości określonej bazy danych.
- [Lista zalecana przez bazę danych](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): Pobiera zalecane etykiety czułości określonej bazy danych.

### <a name="use-powershell-cmdlets"></a>Korzystanie z poleceń cmdlet programu PowerShell
Za pomocą programu PowerShell można zarządzać klasyfikacjami i zaleceniami dotyczącymi Azure SQL Database i wystąpieniami zarządzanymi.

#### <a name="powershell-cmdlets-for-sql-database"></a>Polecenia cmdlet programu PowerShell dla SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Polecenia cmdlet programu PowerShell dla wystąpień zarządzanych

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaawansowanych zabezpieczeniach danych](sql-database-advanced-data-security.md).
- Należy rozważyć skonfigurowanie [inspekcji Azure SQL Database](sql-database-auditing.md) na potrzeby monitorowania i inspekcji dostępu do danych poufnych.
- Aby poznać prezentację obejmującą odnajdywanie i klasyfikację danych, zobacz [odnajdywanie, klasyfikowanie, etykietowanie & ochrony danych SQL | Dane uwidocznione](https://www.youtube.com/watch?v=itVi9bkJUNc).
