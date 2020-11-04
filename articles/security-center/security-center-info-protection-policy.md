---
title: Zasady dotyczące programu SQL Information Protection w Azure Security Center
description: Dowiedz się, jak dostosować zasady ochrony informacji w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: 0a487f778693e87e680033edd0d80c55d1a85f66
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348631"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Zasady dotyczące programu SQL Information Protection w Azure Security Center
 
[Mechanizm odnajdywania i klasyfikowania danych](../azure-sql/database/data-discovery-and-classification-overview.md) usługi SQL Information Protection zapewnia zaawansowane funkcje odnajdywania, klasyfikowania, etykietowania i raportowania poufnych danych w bazach danych. Jest ona wbudowana w [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md), [wystąpienia zarządzanego Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)i [usługi Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Mechanizm klasyfikacji jest oparty na następujących dwóch elementach:

- **Etykiety** — główne atrybuty klasyfikacji używane do definiowania *poziomu czułości danych* przechowywanych w kolumnie. 
- **Typy informacji** — zapewnia dodatkowy poziom szczegółowości dla *typu danych* przechowywanych w kolumnie.

Opcje zasad usługi Information Protection w Security Center udostępniają wstępnie zdefiniowany zestaw etykiet i typów informacji, które są używane jako wartości domyślne dla aparatu klasyfikacji. Zasady można dostosować zgodnie z potrzebami organizacji, zgodnie z poniższym opisem.

> [!IMPORTANT]
> Aby dostosować zasady ochrony informacji dla dzierżawy platformy Azure, musisz mieć uprawnienia administracyjne w głównej grupie zarządzania dzierżawcy. Dowiedz się więcej, [Aby uzyskać wgląd w całą dzierżawę Azure Security Center](security-center-management-groups.md).

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="Strona przedstawiająca zasady dotyczące programu SQL Information Protection":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Jak mogę uzyskać dostęp do zasad ochrony informacji SQL?

Istnieją trzy sposoby uzyskiwania dostępu do zasad ochrony informacji:

- **(Zalecane)** Na stronie cennika i ustawienia Security Center
- Ze względów bezpieczeństwa "dane poufne w bazach danych SQL należy klasyfikować"
- Na stronie odnajdywania danych usługi Azure SQL DB

Każda z nich jest wyświetlana na odpowiedniej karcie poniżej.



### <a name="from-security-centers-settings"></a>[**Z ustawień Security Center**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Uzyskiwanie dostępu do zasad na stronie cennika i ustawienia Security Center <a name="sqlip-tenant"></a>

Na stronie **cennika i ustawienia** usługi Security Center wybierz pozycję **SQL Information Protection**.

> [!NOTE]
> Ta opcja jest wyświetlana tylko dla użytkowników z uprawnieniami na poziomie dzierżawy. 

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Uzyskiwanie dostępu do zasad usługi SQL Information Protection ze strony cen i ustawień Azure Security Center":::



### <a name="from-security-centers-recommendation"></a>[**Zalecenie dotyczące Security Center**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Uzyskaj dostęp do zasad z rekomendacji Security Center <a name="sqlip-db"></a>

Użyj zalecenia Security Center, "poufne dane w bazach danych SQL powinny być sklasyfikowane", aby wyświetlić stronę odnajdywania i klasyfikacji danych dla bazy danych. Zobaczysz również, że odnalezione kolumny zawierają informacje, które zalecamy klasyfikowanie.

1. Na stronie **zalecenia dotyczące** Security Center należy zaklasyfikować odpowiednie dane dotyczące rekomendacji **w bazach danych SQL**.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Znajdowanie zalecenia zapewniającego dostęp do zasad ochrony informacji SQL":::

1. Na stronie Szczegóły zalecenia wybierz bazę danych z kart **kondycja** lub **zła kondycja** .

1. Zostanie otwarta strona **klasyfikacji & odnajdywania danych** . Wybierz pozycję **Konfiguruj**.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Otwieranie zasad usługi SQL Information Protection z odpowiedniego zalecenia w Azure Security Center":::



### <a name="from-azure-sql"></a>[**Z usługi Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Uzyskiwanie dostępu do zasad z poziomu usługi Azure SQL <a name="sqlip-azuresql"></a>

1. W Azure Portal Otwórz usługę Azure SQL.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Otwieranie usługi Azure SQL z poziomu Azure Portal":::

1. Wybierz dowolną bazę danych.

1. W obszarze **zabezpieczenia** w menu Otwórz stronę **klasyfikacji & odnajdywania danych** (1) i wybierz pozycję **Konfiguruj** (2).

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Otwieranie zasad usługi SQL Information Protection w usłudze Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Dostosowywanie typów informacji

Aby zarządzać typami informacji i je dostosowywać:

1. Wybierz pozycję **Zarządzaj typami informacji**.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Zarządzanie typami informacji w ramach zasad ochrony informacji":::

1. Aby dodać nowy typ, wybierz opcję **Utwórz typ informacji**. Można skonfigurować nazwę, opis i ciągi wzorców wyszukiwania dla typu informacji. Ciągi wzorców wyszukiwania mogą opcjonalnie używać słów kluczowych z symbolami wieloznacznymi (za pomocą znaku "%"), który jest używany przez aparat automatycznego odnajdywania do identyfikowania poufnych danych w bazach danych na podstawie metadanych kolumn.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Skonfiguruj nowy typ informacji dla zasad usługi Information Protection":::

1. Możesz również zmodyfikować wbudowane typy przez dodanie dodatkowych ciągów wzorca wyszukiwania, wyłączenie niektórych istniejących ciągów lub zmianę opisu. 

    > [!TIP]
    > Nie można usunąć typów wbudowanych ani zmienić ich nazw. 

1. **Typy informacji** są wymienione w kolejności rosnącej klasyfikacji odnajdywania, co oznacza, że typy wyższe na liście będą podejmować próby dopasowania najpierw. Aby zmienić klasyfikację między typami informacji, przeciągnij typy do prawego miejsca w tabeli lub użyj przycisków **Przenieś w górę** i **Przenieś w dół** , aby zmienić kolejność. 

1. Po zakończeniu wybierz **przycisk OK** .

1. Po zakończeniu zarządzania typami informacji Pamiętaj o skojarzeniu odpowiednich typów z odpowiednimi etykietami, klikając opcję **Konfiguruj** dla określonej etykiety i dodając lub usuwając typy informacji zgodnie z potrzebami.

1. Aby zastosować zmiany, wybierz pozycję **Zapisz** na stronie **etykiet** głównych.
 

## <a name="exporting-and-importing-a-policy"></a>Eksportowanie i Importowanie zasad

Możesz pobrać plik JSON ze zdefiniowanymi etykietami i typami informacji, edytować plik w wybranym edytorze, a następnie zaimportować zaktualizowany plik. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Eksportowanie i Importowanie zasad ochrony informacji":::

> [!NOTE]
> Aby zaimportować plik zasad, musisz mieć uprawnienia na poziomie dzierżawy. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Zarządzanie usługą SQL Information Protection przy użyciu Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): Pobiera obowiązujące zasady ochrony informacji SQL dla dzierżawy.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): ustawia obowiązujące dzierżawy zasad ochrony informacji SQL.
 

## <a name="next-steps"></a>Następne kroki
 
W tym artykule przedstawiono informacje dotyczące definiowania zasad ochrony informacji w programie Azure Security Center. Aby dowiedzieć się więcej o używaniu programu SQL Information Protection do klasyfikowania i ochrony poufnych danych w bazach danych SQL, zobacz [Azure SQL Database odnajdywania i klasyfikowania danych](../azure-sql/database/data-discovery-and-classification-overview.md).

Aby uzyskać więcej informacji na temat zasad zabezpieczeń i zabezpieczeń danych w Security Center, zobacz następujące artykuły:
 
- [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md): informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure
- [Azure Security Center zabezpieczenia danych](security-center-data-security.md): Dowiedz się, jak Security Center zarządza danymi i zabezpiecza je
