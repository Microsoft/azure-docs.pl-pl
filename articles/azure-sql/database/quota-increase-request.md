---
title: Poproś o zwiększenie limitu przydziału
description: Na tej stronie opisano, jak utworzyć żądanie pomocy technicznej w celu zwiększenia limitów przydziału dla Azure SQL Database i wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 06/04/2020
ms.openlocfilehash: 27719663acfbdbcd7293defc4b746153359adb61
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251860"
---
# <a name="request-quota-increases-for-azure-sql-database-and-sql-managed-instance"></a>Wzrost przydziału żądań dla Azure SQL Database i wystąpienia zarządzanego SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym artykule wyjaśniono, jak zażądać zwiększenia przydziału dla Azure SQL Database i wystąpienia zarządzanego Azure SQL. Wyjaśniono także, jak włączyć dostęp do subskrypcji do regionu oraz jak zażądać włączenia określonego sprzętu w regionie.

## <a name="create-a-new-support-request"></a><a id="newquota"></a> Utwórz nowe żądanie obsługi

Wykonaj następujące kroki, aby utworzyć nowe żądanie obsługi z Azure Portal SQL Database.

1. W menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Łącze Pomoc i obsługa techniczna](./media/quota-increase-request/help-plus-support.png)

1. W oknie **Pomoc i obsługa techniczna** wybierz pozycję **nowe żądanie obsługi**.

    ![Utwórz nowe żądanie obsługi](./media/quota-increase-request/new-support-request.png)

1. W obszarze **typ problemu** wybierz pozycję **usługi i limity subskrypcji (przydziały)**.

   ![Wybierz typ problemu](./media/quota-increase-request/select-quota-issue-type.png)

1. W obszarze **subskrypcja** wybierz subskrypcję, której przydział chcesz zwiększyć.

   ![Wybierz subskrypcję dla zwiększonych limitów przydziału](./media/quota-increase-request/select-subscription-support-request.png)

1. W **polu Typ limitu przydziału** wybierz jeden z następujących typów przydziałów:

   - **SQL Database** w przypadku przydziałów dla pojedynczej bazy danych i puli elastycznej.
   - **SQL Database wystąpienia zarządzanego** dla wystąpień zarządzanych.

   Następnie wybierz pozycję **Dalej: rozwiązania >>**.

   ![Wybierz typ limitu przydziału](./media/quota-increase-request/select-quota-type.png)

1. W oknie **szczegóły** wybierz pozycję **Wprowadź szczegóły** , aby wprowadzić dodatkowe informacje.

   ![Wprowadź link do szczegółów](./media/quota-increase-request/provide-details-link.png)

Kliknięcie przycisku **Wprowadź szczegóły** powoduje wyświetlenie okna **Szczegóły przydziału** , które umożliwia dodanie dodatkowych informacji. W poniższych sekcjach opisano różne opcje **SQL Database** i SQL Database typy przydziałów **wystąpienia zarządzanego** .

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a> SQL Database typy przydziałów

W poniższych sekcjach opisano opcje zwiększania limitu przydziału dla **SQL Database** typów przydziałów:

- Jednostki transakcji bazy danych (DTU) na serwer
- Serwery na subskrypcję
- Dostęp do regionu dla subskrypcji lub określonego sprzętu

### <a name="database-transaction-units-dtus-per-server"></a>Jednostki transakcji bazy danych (DTU) na serwer

Wykonaj następujące kroki, aby zażądać wzrostu DTU na serwer.

1. Wybierz **jednostki transakcji bazy danych (DTU) dla każdego** typu przydziału serwera.

1. Na liście **zasobów** wybierz zasób docelowy.

1. W polu **nowy przydział** wprowadź nowy limit liczby jednostek DTU, który ma być żądany.

   ![Szczegóły limitu przydziału jednostek DTU](./media/quota-increase-request/quota-details-dtus.png)

Aby uzyskać więcej informacji, zobacz [limity zasobów dla pojedynczych baz danych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-single-databases.md) i [limitów zasobów dla pul elastycznych przy użyciu modelu zakupu jednostek DTU](resource-limits-dtu-elastic-pools.md).

### <a name="servers-per-subscription"></a>Serwery na subskrypcję

Wykonaj następujące kroki, aby zażądać zwiększenia liczby serwerów na subskrypcję.

1. Wybierz typ przydziału **serwery na subskrypcję** .

1. Na liście **Lokalizacja** wybierz region platformy Azure, który ma być używany. Przydział jest przypadany na subskrypcję w każdym regionie.

1. W polu **nowy limit przydziału** wprowadź swój wniosek dotyczący maksymalnej liczby serwerów w tym regionie.

   ![Szczegóły limitu przydziału serwerów](./media/quota-increase-request/quota-details-servers.png)

Aby uzyskać więcej informacji, zobacz [SQL Database limity zasobów i zarządzanie zasobami](resource-limits-logical-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="region"></a> Włączanie dostępu do subskrypcji do regionu

Niektóre typy ofert nie są dostępne w każdym regionie. Może zostać wyświetlony następujący błąd:

`Your subscription does not have access to create a server in the selected region. For the latest information about region availability for your subscription, go to aka.ms/sqlcapacity. Please try another region or create a support ticket to request access.`

Jeśli Twoja subskrypcja wymaga dostępu w określonym regionie, wybierz opcję **dostęp do regionu** . W żądaniu Określ szczegóły oferty i jednostki SKU, które chcesz włączyć dla regionu. Aby poznać opcje oferty i jednostki SKU, zobacz [cennik Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

1. Wybierz typ limitu przydziału **dostępu do regionu** .

1. Z listy **Wybierz lokalizację** wybierz region platformy Azure, który ma być używany. Przydział jest przypadany na subskrypcję w każdym regionie.

1. Wprowadź **model zakupu** oraz szczegóły **oczekiwanego zużycia** .

   ![Dostęp do regionu żądania](./media/quota-increase-request/quota-request.png)

### <a name="request-enabling-specific-hardware-in-a-region"></a>Żądanie włączenia określonego sprzętu w regionie

Jeśli [generacja sprzętowa](service-tiers-vcore.md#hardware-generations) , której chcesz użyć, nie jest dostępna w Twoim regionie (zobacz [dostępność sprzętu](service-tiers-vcore.md#hardware-availability)), możesz ją zażądać, wykonując poniższe kroki.

1. Wybierz inny typ przydziału **żądania przydziału** .

1. W polu **Opis** należy podać żądanie, włącznie z nazwą generowania sprzętu i nazwą regionu, w którym jest ona potrzebna.

   ![Żądaj sprzętu w nowym regionie](./media/quota-increase-request/hardware-in-new-region.png)

## <a name="submit-your-request"></a>Prześlij żądanie

Ostatnim krokiem jest uzupełnienie pozostałych szczegółów żądania limitu przydziału usługi SQL Database. Następnie wybierz pozycję **Dalej: Przeglądanie i tworzenie >>** i po przejrzeniu szczegółów żądania kliknij pozycję **Utwórz**, aby przesłać żądanie.

## <a name="next-steps"></a>Następne kroki

Po przesłaniu żądania zostanie ono sprawdzone. Otrzymasz odpowiedź na podstawie informacji podanych w formularzu.

Aby uzyskać więcej informacji na temat innych limitów platformy Azure, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
