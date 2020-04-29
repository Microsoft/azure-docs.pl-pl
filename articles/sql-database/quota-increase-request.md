---
title: Poproś o zwiększenie limitu przydziału
description: Na tej stronie opisano, jak utworzyć żądanie pomocy technicznej w celu zwiększenia limitów przydziału dla Azure SQL Database pojedynczych baz danych, serwerów i wystąpień zarządzanych.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586159"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Wzrost przydziału żądań dla Azure SQL Database

W tym artykule wyjaśniono, jak zażądać zwiększenia limitu przydziału dla Azure SQL Database pojedynczych baz danych, serwerów i wystąpień zarządzanych. Wyjaśniono także, jak włączyć dostęp do subskrypcji do regionu.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Utwórz nowe żądanie obsługi

Wykonaj następujące kroki, aby utworzyć nowe żądanie obsługi z Azure Portal SQL Database.

1. W menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Łącze Pomoc i obsługa techniczna](./media/quota-increase-request/help-plus-support.png)

1. W oknie **Pomoc i obsługa techniczna**wybierz pozycję **nowe żądanie obsługi**.

    ![Utwórz nowe żądanie obsługi](./media/quota-increase-request/new-support-request.png)

1. W obszarze **typ problemu**wybierz pozycję **usługi i limity subskrypcji (przydziały)**.

   ![Wybierz typ problemu](./media/quota-increase-request/select-quota-issue-type.png)

1. W obszarze **subskrypcja**wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję dla zwiększonych limitów przydziału](./media/quota-increase-request/select-subscription-support-request.png)

1. W **polu Typ limitu przydziału**wybierz jeden z następujących typów przydziałów:

   - **SQL Database** w przypadku przydziałów dla pojedynczej bazy danych i puli elastycznej.
   - **SQL Database wystąpienia zarządzanego** dla wystąpień zarządzanych.

   Następnie wybierz pozycję **Dalej: rozwiązania >>**.

   ![Wybierz typ limitu przydziału](./media/quota-increase-request/select-quota-type.png)

1. W oknie **szczegóły** wybierz pozycję **Podaj szczegóły** , aby wprowadzić dodatkowe informacje.

   ![Link "Podaj szczegóły"](./media/quota-increase-request/provide-details-link.png)

Kliknięcie pozycji **Podaj szczegóły** powoduje wyświetlenie okna **Szczegóły przydziału** umożliwiającego dodanie dodatkowych informacji. W poniższych sekcjach opisano różne opcje **SQL Database** i SQL Database typy przydziałów **wystąpienia zarządzanego** .

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>SQL Database typy przydziałów

W poniższych sekcjach opisano trzy opcje wzrostu przydziału dla **SQL Database** typów przydziałów:

- Jednostki transakcji bazy danych (DTU) na serwer
- Serwery na subskrypcję
- Włączanie dostępu do subskrypcji do regionu

### <a name="database-transaction-units-dtus-per-server"></a>Jednostki transakcji bazy danych (DTU) na serwer

Wykonaj następujące kroki, aby zażądać wzrostu DTU na serwer.

1. Wybierz **jednostki transakcji bazy danych (DTU) dla każdego** typu przydziału serwera.

1. Na liście **zasobów** wybierz zasób docelowy.

1. W polu **nowy przydział** wprowadź nowy limit liczby jednostek DTU, który ma być żądany.

   ![Szczegóły limitu przydziału jednostek DTU](./media/quota-increase-request/quota-details-dtus.png)

Aby uzyskać więcej informacji, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](sql-database-dtu-resource-limits-single-databases.md) i [limitów zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Serwery na subskrypcję

Wykonaj następujące kroki, aby zażądać zwiększenia liczby serwerów na subskrypcję.

1. Wybierz typ przydziału **serwery na subskrypcję** .

1. Na liście **Lokalizacja** wybierz region platformy Azure, który ma być używany. Przydział jest przypadany na subskrypcję w każdym regionie.

1. W polu **nowy limit przydziału** wprowadź swój wniosek dotyczący maksymalnej liczby serwerów w tym regionie.

   ![Szczegóły limitu przydziału serwerów](./media/quota-increase-request/quota-details-servers.png)

Aby uzyskać więcej informacji, zobacz [SQL Database limity zasobów i zarządzanie zasobami](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Włączanie dostępu do subskrypcji do regionu

Niektóre typy ofert nie są dostępne w każdym regionie. Może zostać wyświetlony następujący błąd:

`This location is not available for subscription`

Jeśli Twoja subskrypcja wymaga dostępu w określonym regionie, użyj **innej opcji żądania przydziału** , aby zażądać dostępu. W żądaniu Określ szczegóły oferty i jednostki SKU, które chcesz włączyć dla regionu. Aby poznać opcje oferty i jednostki SKU, zobacz [cennik Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Inne szczegóły przydziału](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Typ przydziału wystąpienia zarządzanego

W przypadku typu przydziału **wystąpienia zarządzanego SQL Server** wykonaj następujące czynności:

1. Na liście **region** wybierz region platformy Azure, który ma być celem.

1. Wprowadź nowe limity, które są żądane dla **podsieci** i **rdzeń wirtualny**.

   ![Szczegóły przydziału wystąpienia zarządzanego](./media/quota-increase-request/quota-details-managed-instance.png)

Aby uzyskać więcej informacji, zobacz [przegląd Azure SQL Database limitów zasobów wystąpienia zarządzanego](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Prześlij żądanie

Ostatnim krokiem jest wypełnienie pozostałych szczegółów żądania limitu przydziału SQL Database. Następnie wybierz kolejno pozycje **Dalej: przegląd + utwórz>>** i po przejrzeniu szczegółów żądania kliknij pozycję **Utwórz** , aby przesłać żądanie.

## <a name="next-steps"></a>Następne kroki

Po przesłaniu żądania zostanie ono zweryfikowane. Nastąpi skontaktowanie z odpowiedzią na podstawie informacji podanych w formularzu.

Aby uzyskać więcej informacji na temat innych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md).
