---
title: Odnajdowanie i klasyfikacja danych
description: Odnajdowanie danych & klasyfikacji dla usługi Azure SQL Database i usługi Azure Synapse Analytics
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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767004"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Odnajdowanie danych & klasyfikacji dla usługi Azure SQL Database i usługi Azure Synapse Analytics

Odnajdowanie danych & Klasyfikacja jest wbudowany w usługi Azure SQL Database. Zapewnia zaawansowane możliwości wykrywania, klasyfikowania, etykietowania i raportowania poufnych danych w bazach danych.

Twoje najbardziej wrażliwe dane mogą obejmować dane biznesowe, finansowe, służbowe lub osobiste. Odnajdowanie i klasyfikowanie tych danych może odgrywać kluczową rolę w podejściu organizacji do ochrony informacji. Może to stanowić infrastrukturę dla następujących działań:

- Pomoc w spełnianiu standardów dotyczących prywatności danych i wymagań dotyczących zgodności z przepisami.
- Różne scenariusze zabezpieczeń, takie jak monitorowanie (inspekcja) i alerty o nietypowym dostępie do poufnych danych.
- Kontrolowanie dostępu do i wzmacnianie zabezpieczeń baz danych zawierających bardzo poufne dane.

Odnajdowanie danych & Klasyfikacja jest częścią oferty [Zaawansowane zabezpieczenia danych,](sql-database-advanced-data-security.md) która jest ujednoliconym pakietem dla zaawansowanych funkcji zabezpieczeń SQL. Możesz uzyskać dostęp do klasyfikacji & odnajdowania danych i zarządzać nimi za pośrednictwem centralnej sekcji **SQL Advanced Data Security** w witrynie Azure portal.

> [!NOTE]
> Ten artykuł dotyczy usługi Azure SQL Database i usługi Azure Synapse Analytics. Dla uproszczenia używamy *bazy danych SQL* w tym miejscu, aby odwołać się zarówno do bazy danych SQL i usługi Azure Synapse. Aby uzyskać informacje o programie SQL Server (lokalnym), zobacz [Odnajdowanie i klasyfikacja danych SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Co to jest klasyfikacja & odnajdowania danych?

Odnajdowanie danych & Klasyfikacja wprowadza zestaw zaawansowanych usług i nowych możliwości bazy danych SQL. Tworzy nowy paradygmat ochrony informacji dla bazy danych SQL, mające na celu ochronę danych, a nie tylko bazy danych. Paradygmat obejmuje:

- **Odnajdowanie i zalecenia:** Aparat klasyfikacji skanuje bazę danych i identyfikuje kolumny, które zawierają potencjalnie poufne dane. Następnie zapewnia łatwy sposób przeglądania i stosowania zalecanej klasyfikacji za pośrednictwem witryny Azure portal.

- **Etykietowanie:** Etykiety klasyfikacji czułości można stosować uporczywie do kolumn przy użyciu nowych atrybutów metadanych, które zostały dodane do aparatu bazy danych SQL. Te metadane mogą być następnie używane do zaawansowanych scenariuszy inspekcji i ochrony opartych na czułości.

- **Czułość zestawu wyników kwerendy:** Czułość zestawu wyników kwerendy jest obliczana w czasie rzeczywistym do celów inspekcji.

- **Widoczność:** Stan klasyfikacji bazy danych można wyświetlić na szczegółowym pulpicie nawigacyjnym w witrynie Azure portal. Ponadto można pobrać raport w formacie programu Excel do wykorzystania do celów zgodności i inspekcji oraz innych potrzeb.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Odnajdowanie, klasyfikowanie i oznaczanie kolumn poufnych

W tej sekcji opisano kroki dotyczące:

- Odnajdowanie, klasyfikowanie i etykietowanie kolumn zawierających poufne dane w bazie danych.
- Wyświetlanie bieżącego stanu klasyfikacji bazy danych i eksportowanie raportów.

Klasyfikacja zawiera dwa atrybuty metadanych:

- **Etykiety**: Główne atrybuty klasyfikacji, używane do definiowania poziomu czułości danych przechowywanych w kolumnie.  
- **Typy informacji:** Atrybuty, które zapewniają bardziej szczegółowe informacje o typie danych przechowywanych w kolumnie.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definiowanie i dostosowywanie taksonomii klasyfikacji

Odnajdowanie danych & Klasyfikacja jest wyposażony w wbudowany zestaw etykiet czułości i wbudowany zestaw typów informacji i logiki odnajdywania. Teraz można dostosowywać tę taksonomię i definiować zestaw oraz ranking konstrukcji klasyfikacji przeznaczonych dla danego środowiska.

Definiujesz i dostosowujesz taksonomię klasyfikacji w jednym centralnym miejscu dla całej organizacji platformy Azure. Ta lokalizacja znajduje się w [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), w ramach zasad zabezpieczeń. Tylko osoba z uprawnieniami administracyjnymi w głównej grupie zarządzania organizacji może wykonać to zadanie.

W ramach zarządzania zasadami ochrony informacji SQL można definiować etykiety niestandardowe, klasyfikować je i kojarzyć z wybranym zestawem typów informacji. Można również dodać własne typy informacji niestandardowych i skonfigurować je za pomocą wzorców ciągów. Wzorce są dodawane do logiki odnajdywania do identyfikowania tego typu danych w bazach danych.

Dowiedz się więcej o dostosowywaniu zasad i zarządzaniu nimi w [przewodniku po zasadach ochrony informacji SQL](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Po zdefiniowaniu zasad dla całej organizacji można kontynuować klasyfikowanie poszczególnych baz danych przy użyciu niestandardowych zasad.

### <a name="classify-your-sql-database"></a>Klasyfikowanie bazy danych SQL

1. Przejdź do [witryny Azure portal](https://portal.azure.com).

2. Przejdź do **pozycji Zaawansowane zabezpieczenia danych** w nagłówku **Zabezpieczenia** w okienku usługi Azure SQL Database. Wybierz **pozycję Zaawansowane zabezpieczenia danych**, a następnie wybierz kartę **Odnajdowanie danych & Klasyfikacja.**

   ![Okienko Zaawansowane zabezpieczenia danych w witrynie Azure portal](./media/sql-data-discovery-and-classification/data_classification.png)

3. Na **stronie Odnajdowanie danych & klasyfikacji** karta **Przegląd** zawiera podsumowanie bieżącego stanu klasyfikacji bazy danych. Podsumowanie zawiera szczegółową listę wszystkich kolumn niejawnych, które można również filtrować, aby wyświetlić tylko określone części schematu, typy informacji i etykiety. Jeśli nie zaklasyfikowałeś jeszcze żadnych kolumn, [przejdź do kroku 5](#step-5).

   ![Podsumowanie bieżącego stanu klasyfikacji](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Aby pobrać raport w formacie programu Excel, wybierz **pozycję Eksportuj** w górnym menu okienka.

5. <a id="step-5"></a>Aby rozpocząć klasyfikowanie danych, wybierz kartę **Klasyfikacja** na stronie **Odnajdowanie danych & klasyfikacji.**

    Aparat klasyfikacji skanuje bazę danych w poszukiwaniu kolumn zawierających potencjalnie poufne dane i udostępnia listę zalecanych klasyfikacji kolumn.

6. Wyświetlanie i stosowanie zaleceń dotyczących klasyfikacji:

   - Aby wyświetlić listę zalecanych klasyfikacji kolumn, wybierz panel rekomendacji u dołu okienka.

   - Aby zaakceptować zalecenie dla określonej kolumny, zaznacz pole wyboru w lewej kolumnie odpowiedniego wiersza. Aby oznaczyć wszystkie zalecenia jako zaakceptowane, zaznacz pole wyboru po lewej stronie w nagłówku tabeli rekomendacji.

       ![Przejrzyj i wybierz z listy zaleceń dotyczących klasyfikacji](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Aby zastosować wybrane zalecenia, wybierz **pozycję Zaakceptuj wybrane rekomendacje**.

7. Kolumny można również klasyfikować ręcznie, jako alternatywę lub oprócz klasyfikacji opartej na zaleceniach:

   1. Wybierz **pozycję Dodaj klasyfikację** w górnym menu okienka.

   1. W otwartym oknie kontekstu wybierz schemat, tabelę i kolumnę, które chcesz sklasyfikować, oraz etykietę typu informacji i czułości.

   1. Wybierz **pozycję Dodaj klasyfikację** u dołu okna kontekstu.

      ![Wybierz kolumnę do sklasyfikowania](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Aby ukończyć klasyfikację i trwale oznaczyć (oznaczyć) kolumny bazy danych nowymi metadanymi klasyfikacji, wybierz **pozycję Zapisz** w górnym menu okna.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Inspekcja dostępu do poufnych danych

Ważnym aspektem paradygmatu ochrony informacji jest możliwość monitorowania dostępu do poufnych danych. [Usługa Azure SQL Database Auditing](sql-database-auditing.md) została ulepszona, aby `data_sensitivity_information`uwzględnić nowe pole w dzienniku inspekcji o nazwie . To pole rejestruje klasyfikacje czułości (etykiety) danych, które zostały zwrócone przez kwerendę. Oto przykład:

![Dziennik inspekcji](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Uprawnienia

Te wbudowane role mogą odczytywać klasyfikację danych bazy danych SQL platformy Azure:

- Właściciel
- Czytelnik
- Współautor
- Menedżer zabezpieczeń SQL
- Administrator dostępu użytkowników

Te wbudowane role można zmodyfikować klasyfikację danych bazy danych SQL platformy Azure:

- Właściciel
- Współautor
- Menedżer zabezpieczeń SQL

Dowiedz się więcej o uprawnieniach opartych na rolach w [rbac dla zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Zarządzanie klasyfikacjami

Do zarządzania klasyfikacjami można używać funkcji T-SQL, interfejsu API REST lub programu PowerShell.

### <a name="use-t-sql"></a>Korzystanie z funkcji T-SQL

Za pomocą języka T-SQL można dodawać lub usuwać klasyfikacje kolumn oraz pobierać wszystkie klasyfikacje dla całej bazy danych.

> [!NOTE]
> Podczas zarządzania etykietami za pomocą funkcji T-SQL nie ma sprawdzania poprawności, że etykiety dodane do kolumny istnieją w zasadach ochrony informacji organizacji (zestaw etykiet wyświetlanych w zaleceniach portalu). Tak, to do Ciebie, aby to potwierdzić.

Aby uzyskać informacje na temat używania języka T-SQL do klasyfikacji, zobacz następujące odwołania:

- Aby dodać lub zaktualizować klasyfikację jednej lub więcej kolumn: [DODAJ KLASYFIKACJĘ CZUŁOŚCI](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Aby usunąć klasyfikację z jednej lub więcej kolumn: [KLASYFIKACJA WRAŻLIWOŚCI UPUŚCIEŃ](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Aby wyświetlić wszystkie klasyfikacje w bazie danych: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Korzystanie z interfejsu API odpoczynku

Za pomocą interfejsu API REST można programowo zarządzać klasyfikacjami i zaleceniami. Opublikowany interfejs API REST obsługuje następujące operacje:

- [Utwórz lub aktualizuj:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate)Tworzy lub aktualizuje etykietę czułości określonej kolumny.
- [Usuń](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Usuwa etykietę czułości określonej kolumny.
- [Zalecenie wyłączania:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)Wyłącza zalecenia dotyczące wrażliwości w określonej kolumnie.
- [Włącz zalecenie:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)Włącza zalecenia dotyczące czułości w określonej kolumnie. (Zalecenia są domyślnie włączone we wszystkich kolumnach).
- [Pobierz:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get)Pobiera etykietę czułości określonej kolumny.
- [Lista bieżąca według bazy danych:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase)Pobiera bieżące etykiety czułości określonej bazy danych.
- [Lista zalecane przez bazę danych:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)Pobiera zalecane etykiety czułości określonej bazy danych.

### <a name="use-powershell-cmdlets"></a>Korzystanie z poleceń cmdlet programu PowerShell
Program PowerShell służy do zarządzania klasyfikacjami i zaleceniami dla usługi Azure SQL Database i wystąpień zarządzanych.

#### <a name="powershell-cmdlets-for-sql-database"></a>Polecenia cmdlet programu PowerShell dla bazy danych SQL

- [Klasyfikacja jakościOwa Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityKlasyfikacja](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Usuń-AzSqlDatabaseSensitivityKlasyfikacja](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRekomemonia](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityReommendation Enable-AzSqlDatabaSesensitivityReommendation Enable-AzSqlDatabaSesensitivityRecommendation Enable-](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Wyłącz-AzSqlDatabaseSensitivityRekomemagnięć](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Polecenia cmdlet programu PowerShell dla wystąpień zarządzanych

- [Klasyfikacja informacji o adresach dźwiękowych Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Usuń-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityReommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityReommendation Enable-AzSqlInstanceDatabaseSensitivityRecommendation Enable-AzSqlInstanceDatabaseSensitivityRecommendation Enable-](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Wyłącz-AzSqlInstanceDatabaseSensitivityReommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaawansowanym bezpieczeństwie danych](sql-database-advanced-data-security.md).
- Należy rozważyć skonfigurowanie [usługi Azure SQL Database Auditing](sql-database-auditing.md) do monitorowania i inspekcji dostępu do niejawnych poufnych danych.
- Aby uzyskać prezentację zawierającą odnajdowanie i klasyfikację danych, zobacz [Odnajdywanie, klasyfikowanie, etykietowanie & ochrona danych SQL | Ujawniono dane](https://www.youtube.com/watch?v=itVi9bkJUNc).
