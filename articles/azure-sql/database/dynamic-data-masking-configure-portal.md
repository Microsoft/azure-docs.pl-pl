---
title: 'Azure Portal: Dynamiczne maskowanie danych'
description: Jak rozpocząć pracę z Azure SQL Database dynamiczną maskowanie danych w Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 1d4c29cdb9cf24f87df4acc3556b318a1079810a
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443614"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Rozpocznij pracę z SQL Database dynamiczną maskowanie danych przy użyciu Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule pokazano, jak zaimplementować [Dynamiczne maskowanie danych](dynamic-data-masking-overview.md) przy użyciu Azure Portal. Możesz również zaimplementować Dynamiczne maskowanie danych za pomocą [poleceń cmdlet Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/).

> [!NOTE]
> Nie można ustawić tej funkcji przy użyciu portalu dla platformy Azure Synapse (Użyj programu PowerShell lub interfejsu API REST) ani wystąpienia zarządzanego SQL. Aby uzyskać więcej informacji, zobacz [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Skonfiguruj Dynamiczne maskowanie danych dla bazy danych przy użyciu Azure Portal

1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .
2. Przejdź do strony Ustawienia w bazie danych, która zawiera poufne dane, które mają być maskowane.
3. Kliknij blok **Dynamiczne maskowanie danych** w sekcji **zabezpieczenia** bazy danych.

   ![Zrzut ekranu przedstawiający sekcję zabezpieczenia z wyróżnioną dynamiczną maską danych.](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. Na stronie Konfiguracja **dynamicznego maskowania danych** mogą pojawić się pewne kolumny bazy danych, które zostały oflagowane przez aparat zaleceń w celu maskowania. Aby zaakceptować zalecenia, po prostu kliknij pozycję **Dodaj maskę** dla co najmniej jednej kolumny, a na podstawie domyślnego typu dla tej kolumny zostanie utworzona maska. Funkcję maskowania można zmienić, klikając regułę maskowania i edytując format pola maskowania w innym wybranym formacie. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

    ![Zrzut ekranu przedstawiający stronę konfiguracji dynamicznego maskowania danych.](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Aby dodać maskę dla dowolnej kolumny w bazie danych, w górnej części strony Konfiguracja **dynamicznego maskowania danych** kliknij pozycję **Dodaj maskę** , aby otworzyć stronę **Dodawanie konfiguracji reguły maskowania** .

    ![Zrzut ekranu przedstawiający stronę Dodawanie konfiguracji reguły maskowania.](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Wybierz **schemat**, **tabelę** i **kolumnę** , aby zdefiniować wyznaczono pole do maskowania.
7. **Wybierz sposób maskowania** z listy kategorii poufne maskowanie danych.

    ![Zrzut ekranu przedstawiający kategorie z maską danych poufnych w sekcji Wybierz sposób maskowania.](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Kliknij przycisk **Dodaj** na stronie reguły maskowania danych, aby zaktualizować zestaw reguł maskowania w zasadach dynamicznego maskowania danych.
9. Wpisz tożsamości użytkowników SQL lub Azure Active Directory (Azure AD), które mają być wykluczone z maskowania, i uzyskaj dostęp do niemaskowanych danych poufnych. Powinna to być rozdzielana średnikami lista użytkowników. Użytkownicy z uprawnieniami administratora mają zawsze dostęp do pierwotnych niemaskowanych danych.

    ![Okienko nawigacji](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Aby zapewnić, że warstwa aplikacji będzie mogła wyświetlać poufne dane dla użytkowników uprzywilejowanych dla aplikacji, Dodaj użytkownika SQL lub tożsamość usługi Azure AD, która będzie wykorzystywana przez aplikację do wykonywania zapytań w bazie danych. Zdecydowanie zaleca się, aby ta lista zawierała minimalną liczbę użytkowników uprzywilejowanych, aby zminimalizować narażenie poufnych danych.

10. Kliknij przycisk **Zapisz** na stronie Konfiguracja maskowania danych, aby zapisać nowe lub zaktualizowane zasady maskowania.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem dynamicznego maskowania danych, zobacz [Dynamiczne maskowanie danych](dynamic-data-masking-overview.md).
- Możesz również zaimplementować Dynamiczne maskowanie danych za pomocą [poleceń cmdlet Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/).
