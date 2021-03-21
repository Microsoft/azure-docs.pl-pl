---
title: Skonfiguruj prywatne punkty końcowe dla Azure Cosmos DB magazynu analitycznego.
description: Dowiedz się, jak skonfigurować zarządzane prywatne punkty końcowe dla Azure Cosmos DB magazynu analitycznego w celu ograniczenia dostępu do sieci.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 2f15b397fbceb9e097d94080ba03fba50a96ed06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048509"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Skonfiguruj prywatne punkty końcowe dla Azure Cosmos DB magazynu analitycznego
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

W tym artykule dowiesz się, jak skonfigurować zarządzane prywatne punkty końcowe dla Azure Cosmos DB magazynu analitycznego. Jeśli używasz magazynu transakcyjnego, zobacz [prywatne punkty końcowe dla artykułu magazynów transakcyjnych](how-to-configure-private-endpoints.md) . Korzystając z zarządzanych prywatnych punktów końcowych, możesz ograniczyć dostęp sieciowy do Azure Cosmos DB magazynu analitycznego do sieci wirtualnej zarządzanej przez usługę Azure Synapse. Zarządzane prywatne punkty końcowe nawiązują prywatny link do magazynu analitycznego.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>Włącz prywatny punkt końcowy dla magazynu analitycznego

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Konfigurowanie obszaru roboczego usługi Azure Synapse Analytics przy użyciu zarządzanej sieci wirtualnej

[Utwórz obszar roboczy w usłudze Azure Synapse Analytics z włączonymi danymi eksfiltracji.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Dzięki [ochronie danych eksfiltracji](../synapse-analytics/security/workspace-data-exfiltration-protection.md)można zagwarantować, że Złośliwi użytkownicy nie będą mogli kopiować ani przesyłać danych z zasobów platformy Azure do lokalizacji poza zasięgiem organizacji.

Następujące ograniczenia dostępu są stosowane, gdy ochrona danych eksfiltracji jest włączona dla obszaru roboczego analizy Synapse Azure:

* Jeśli używasz platformy Azure Spark dla usługi Azure Synapse Analytics, dostęp jest dozwolony tylko do zatwierdzonych zarządzanych prywatnych punktów końcowych dla Azure Cosmos DB magazynu analitycznego.

* Jeśli używasz Synapse puli SQL bez serwera, możesz wysyłać zapytania do dowolnego konta Azure Cosmos DB za pomocą usługi Azure Synapse link. Jednak żądania zapisu, które [tworzą tabele zewnętrzne jako Select (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) , są dozwolone tylko dla zatwierdzonych prywatnych punktów końcowych w sieci wirtualnej obszaru roboczego.

> [!NOTE]
> Po utworzeniu obszaru roboczego nie można zmienić konfiguracji zarządzanej sieci wirtualnej i danych eksfiltracji.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Dodaj zarządzany prywatny punkt końcowy dla Azure Cosmos DB magazynu analitycznego

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. W Azure Portal przejdź do obszaru roboczego analizy Synapse i Otwórz okienko **Przegląd** .

1. Uruchom program Synapse Studio, przechodząc do okienka **wprowadzenie** i wybierając pozycję **Otwórz** w obszarze **Otwórz Synapse Studio**.

1. W programie Synapse Studio Otwórz kartę **Zarządzanie** .

1. Przejdź do **zarządzanych prywatnych punktów końcowych** i wybierz pozycję **Nowy**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Utwórz nowy prywatny punkt końcowy dla magazynu analitycznego." border="true":::

1. Wybierz typ konta **Azure Cosmos dB (interfejs SQL API)** > **Kontynuuj**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Wybierz pozycję Azure Cosmos DB SQL API, aby utworzyć prywatny punkt końcowy." border="true":::

1. Wypełnij formularz **nowego zarządzanego prywatnego punktu końcowego** z następującymi szczegółami:

   * **Nazwa — nazwa** zarządzanego prywatnego punktu końcowego. Tej nazwy nie można zaktualizować po jej utworzeniu.
   * **Opis** — Podaj przyjazny opis identyfikujący prywatny punkt końcowy.
   * **Subskrypcja platformy Azure** — wybierz konto Azure Cosmos DB z listy dostępnych kont w ramach subskrypcji platformy Azure.
   * **Nazwa konta Azure Cosmos DB** — Wybierz istniejące konto Azure Cosmos DB typu SQL lub MongoDB.
   * **Target sub-zasobów** — wybierz jedną z następujących opcji: **analityczne**: Jeśli chcesz dodać prywatny punkt końcowy dla Azure Cosmos DB magazynu analitycznego.
     **SQL** (lub **MongoDB**): Jeśli chcesz dodać punkt końcowy OLTP lub konto transakcyjne.

   > [!NOTE]
   > Do tego samego konta Azure Cosmos DB w obszarze roboczym usługi Azure Synapse Analytics można dodać zarówno magazyn transakcyjny, jak i prywatne punkty końcowe magazynu analitycznego. Jeśli chcesz tylko uruchamiać zapytania analityczne, możesz zmapować tylko analityczny punkt końcowy.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Wybierz wartość analityczną dla podzasobu docelowego." border="true":::

1. Po utworzeniu przejdź do nazwy prywatnego punktu końcowego i wybierz pozycję **Zarządzaj zatwierdzeniami w Azure Portal**.

1. Przejdź do konta Azure Cosmos DB, wybierz prywatny punkt końcowy i wybierz pozycję **Zatwierdź**.

1. Wróć do obszaru roboczego Synapse Analytics, a następnie kliknij przycisk **Odśwież** w okienku **zarządzane prywatne punkty końcowe** . Sprawdź, czy prywatny punkt końcowy jest w stanie **zatwierdzone** .

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Sprawdź, czy prywatny punkt końcowy został zatwierdzony." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Korzystanie z Apache Spark usługi Azure Synapse Analytics

Jeśli utworzono obszar roboczy Azure Synapse z włączoną ochroną eksfiltracji, dostęp wychodzący z Synapse Spark do Azure Cosmos DB kont zostanie domyślnie zablokowany. Ponadto jeśli Azure Cosmos DB ma już istniejący prywatny punkt końcowy, dostęp do usługi Synapse Spark zostanie zablokowany.

Aby zezwolić na dostęp do Azure Cosmos DB danych:

* Jeśli używasz linku Azure Synapse do wykonywania zapytań dotyczących danych Azure Cosmos DB, Dodaj zarządzany prywatny punkt końcowy **analityczny** dla konta Azure Cosmos DB.

* Jeśli używasz zapisu wsadowego/odczytów i/lub przesyłania strumieniowego zapisu/odczytów w magazynie transakcyjnym, Dodaj zarządzany punkt końcowy *SQL* lub *MongoDB* dla konta Azure Cosmos DB. Ponadto należy ustawić wartość *connectionmode* na *Gateway* , jak pokazano w poniższym fragmencie kodu:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Korzystanie z Synapse puli SQL bezserwerowych

Synapse bezserwerowe pule SQL używają funkcji wielodostępnych, które nie są wdrażane w zarządzanej sieci wirtualnej. Jeśli konto Azure Cosmos DB ma istniejący prywatny punkt końcowy, Synapse dostęp do konta w puli SQL bezserwerowej nie zostanie zablokowany z powodu kontroli izolacji sieci na koncie Azure Cosmos DB.

Aby skonfigurować izolację sieci dla tego konta z poziomu obszaru roboczego Synapse:

1. Zezwól obszarowi roboczemu Synapse na dostęp do konta Azure Cosmos DB przez określenie `NetworkAclBypassResourceId` Ustawienia dla konta.

   **Korzystanie z programu PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Korzystanie z interfejsu wiersza polecenia platformy Azure**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Konto Azure Cosmos DB i obszar roboczy usługi Azure Synapse Analytics powinny znajdować się w tej samej dzierżawie Azure Active Directory (AD).

2. Teraz możesz uzyskiwać dostęp do konta z pul SQL bezserwerowych przy użyciu zapytań T-SQL za pośrednictwem usługi Azure Synapse link. Aby jednak zapewnić izolację sieci dla danych w magazynie analitycznym, należy **dodać do tego konta zarządzany prywatny** punkt końcowy. W przeciwnym razie dane w magazynie analitycznym nie będą blokowane przed dostępem publicznym.

> [!IMPORTANT]
> Jeśli korzystasz z usługi Azure Synapse link i potrzebujesz izolacji sieci dla danych w magazynie analitycznym, musisz zmapować konto Azure Cosmos DB do obszaru roboczego Synapse przy **użyciu zarządzanego** przez niego prywatnego punktu końcowego.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do [wysyłania zapytań do magazynu analitycznego za pomocą usługi Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Wprowadzenie do [wysyłania zapytań do magazynu analitycznego za pomocą pul SQL Synapse bez serwera Azure](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
