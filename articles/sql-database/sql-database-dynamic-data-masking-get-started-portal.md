---
title: 'Azure Portal: Dynamiczne maskowanie danych'
description: Jak rozpocząć pracę z SQL Database dynamiczną maskowanie danych w Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76722122"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Rozpocznij pracę z SQL Database dynamiczną maskowanie danych przy użyciu Azure Portal

W tym artykule pokazano, jak zaimplementować [Dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md) przy użyciu Azure Portal. Możesz również zaimplementować Dynamiczne maskowanie danych za pomocą [poleceń cmdlet Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Skonfiguruj Dynamiczne maskowanie danych dla bazy danych przy użyciu Azure Portal

1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Przejdź do strony Ustawienia w bazie danych, która zawiera poufne dane, które mają być maskowane.
3. Kliknij kafelek **Dynamiczne maskowanie danych** , który uruchamia stronę konfiguracji **dynamicznego maskowania danych** .

   * Alternatywnie możesz przewinąć w dół do sekcji **operacje** , a następnie kliknąć pozycję **Dynamiczne maskowanie danych**.

     ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Na stronie Konfiguracja **dynamicznego maskowania danych** mogą pojawić się pewne kolumny bazy danych, które zostały oflagowane przez aparat zaleceń w celu maskowania. Aby zaakceptować zalecenia, po prostu kliknij pozycję **Dodaj maskę** dla co najmniej jednej kolumny, a na podstawie domyślnego typu dla tej kolumny zostanie utworzona maska. Funkcję maskowania można zmienić, klikając regułę maskowania i edytując format pola maskowania w innym wybranym formacie. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Aby dodać maskę dla dowolnej kolumny w bazie danych, w górnej części strony Konfiguracja **dynamicznego maskowania danych** kliknij pozycję **Dodaj maskę** , aby otworzyć stronę **Dodawanie konfiguracji reguły maskowania** .

    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Wybierz **schemat**, **tabelę** i **kolumnę** , aby zdefiniować wyznaczono pole do maskowania.
7. Wybierz **Format pola maskowania** z listy kategorii poufne maskowanie danych.

    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Kliknij przycisk **Zapisz** na stronie reguły maskowania danych, aby zaktualizować zestaw reguł maskowania w zasadach dynamicznego maskowania danych.
9. Wpisz tożsamości użytkowników programu SQL lub usługi AAD, które mają być wykluczone z maskowania, i uzyskaj dostęp do niemaskowanych danych poufnych. Powinna to być rozdzielana średnikami lista użytkowników. Użytkownicy z uprawnieniami administratora mają zawsze dostęp do pierwotnych niemaskowanych danych.

    ![Okienko nawigacji](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Aby zapewnić, że warstwa aplikacji będzie mogła wyświetlać poufne dane dla użytkowników uprzywilejowanych aplikacji, Dodaj użytkownika SQL lub tożsamość usługi AAD używaną przez aplikację do wykonywania zapytań względem bazy danych. Zdecydowanie zaleca się, aby ta lista zawierała minimalną liczbę użytkowników uprzywilejowanych, aby zminimalizować narażenie poufnych danych.

10. Kliknij przycisk **Zapisz** na stronie Konfiguracja maskowania danych, aby zapisać nowe lub zaktualizowane zasady maskowania.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z omówieniem dynamicznego maskowania danych, zobacz [Dynamiczne maskowanie danych](sql-database-dynamic-data-masking-get-started.md).
* Możesz również zaimplementować Dynamiczne maskowanie danych za pomocą [poleceń cmdlet Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) lub [interfejsu API REST](https://docs.microsoft.com/rest/api/sql/).
