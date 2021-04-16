---
title: Skonfiguruj prywatne punkty końcowe dla Azure Cosmos DB magazynu analitycznego.
description: Dowiedz się, jak skonfigurować zarządzane prywatne punkty końcowe dla Azure Cosmos DB analitycznych w celu ograniczenia dostępu do sieci.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: fd0b3ada5fec283562cee9727e3f805a7d34c532
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479053"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Konfigurowanie Azure Private Link dla Azure Cosmos DB magazynu analitycznego
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

W tym artykule dowiesz się, jak skonfigurować zarządzane prywatne punkty końcowe dla magazynu Azure Cosmos DB analitycznego. Jeśli używasz magazynu transakcyjnego, zobacz [artykuł Prywatne punkty](how-to-configure-private-endpoints.md) końcowe magazynu transakcyjnego. Za [pomocą zarządzanych](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)prywatnych punktów końcowych można ograniczyć dostęp sieciowy do magazynu analitycznego Azure Cosmos DB do zarządzanego Virtual Network skojarzonego z Azure Synapse roboczym. Zarządzane prywatne punkty końcowe ustanawiają prywatne łącze do magazynu analitycznego.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>Włączanie prywatnego punktu końcowego dla magazynu analitycznego

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Konfigurowanie Azure Synapse Analytics z zarządzaną siecią wirtualną

[Utwórz obszar roboczy w Azure Synapse Analytics z włączoną eksfiltracją danych.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Dzięki [ochronie przed eksfiltracją](../synapse-analytics/security/workspace-data-exfiltration-protection.md)danych można zapewnić, że złośliwi użytkownicy nie będą w stanie kopiować ani przesyłać danych z zasobów platformy Azure do lokalizacji spoza zakresu organizacji.

Następujące ograniczenia dostępu mają zastosowanie, gdy ochrona przed eksfiltracją danych jest włączona dla Azure Synapse Analytics roboczego:

* Jeśli używasz platformy Azure Spark na Azure Synapse Analytics, dostęp jest dozwolony tylko do zatwierdzonych zarządzanych prywatnych punktów końcowych Azure Cosmos DB magazynu analitycznego.

* Jeśli używasz bez serwera pul SQL usługi Synapse, możesz odpytnąć dowolne konto Azure Cosmos DB za pomocą Azure Synapse Link. Jednak żądania zapisu tworzące tabele zewnętrzne zgodnie z wybieraniem [(CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) są dozwolone tylko do zatwierdzonych prywatnych punktów końcowych zarządzania w sieci wirtualnej obszaru roboczego.

> [!NOTE]
> Po utworzeniu obszaru roboczego nie można zmienić zarządzanej sieci wirtualnej ani konfiguracji eksfiltracji danych.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Dodawanie zarządzanego prywatnego punktu końcowego Azure Cosmos DB magazynu analitycznego

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. W Azure Portal przejdź do swojego Synapse Analytics roboczego i otwórz **okienko** Przegląd.

1. Uruchom Synapse Studio, przechodząc do **Wprowadzenie** i wybierz pozycję **Otwórz w** obszarze Otwórz **Synapse Studio**.

1. W Synapse Studio otwórz **kartę** Zarządzanie.

1. Przejdź do **zarządzanych prywatnych punktów końcowych** i wybierz pozycję **Nowy**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Utwórz nowy prywatny punkt końcowy dla magazynu analitycznego." border="true":::

1. Wybierz **Azure Cosmos DB (interfejs API SQL),** aby > **Kontynuuj.**

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Wybierz Azure Cosmos DB API SQL, aby utworzyć prywatny punkt końcowy." border="true":::

1. Wypełnij formularz Nowy **zarządzany prywatny punkt końcowy,** aby uzyskać następujące informacje:

   * **Nazwa** — nazwa zarządzanego prywatnego punktu końcowego. Nie można zaktualizować tej nazwy po jej utworzeniu.
   * **Opis** — podaj przyjazny opis identyfikujący prywatny punkt końcowy.
   * **Subskrypcja platformy Azure** — Azure Cosmos DB konto z listy dostępnych kont w twoich subskrypcjach platformy Azure.
   * **Azure Cosmos DB konta —** wybierz istniejące konto Azure Cosmos DB typu SQL lub MongoDB.
   * **Docelowy podzesieć** — wybierz jedną z następujących **opcji:** Analiza: Jeśli chcesz dodać prywatny punkt końcowy Azure Cosmos DB magazynu analitycznego.
     **Sql** (lub **MongoDB):** jeśli chcesz dodać oltp lub transakcyjny punkt końcowy konta.

   > [!NOTE]
   > Możesz dodać prywatne punkty końcowe magazynu transakcyjnego i analitycznego do tego samego Azure Cosmos DB w Azure Synapse Analytics roboczym. Jeśli chcesz uruchamiać tylko zapytania analityczne, możesz chcieć zamapować tylko analityczny prywatny punkt końcowy.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Wybierz analizę dla docelowego podzródła." border="true":::

1. Po utworzeniu przejdź do nazwy prywatnego punktu końcowego i wybierz pozycję **Zarządzaj zatwierdzeniami w Azure Portal**.

1. Przejdź do swojego Azure Cosmos DB, wybierz prywatny punkt końcowy, a następnie wybierz pozycję **Zatwierdź.**

1. Wróć do obszaru Synapse Analytics i kliknij przycisk **Odśwież** w **okienku Zarządzane prywatne punkty** końcowe. Sprawdź, czy prywatny punkt końcowy ma **stan Zatwierdzone.**

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Sprawdź, czy prywatny punkt końcowy został zatwierdzony." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Użyj Apache Spark dla Azure Synapse Analytics

Jeśli utworzono obszar roboczy Azure Synapse z włączona ochroną przed eksfiltracją danych, dostęp wychodzący z platformy Synapse Spark do kont Azure Cosmos DB domyślnie będzie blokowany. Ponadto, jeśli Azure Cosmos DB ma już istniejący prywatny punkt końcowy, dostęp do niego zostanie zablokowany przez usługę Synapse Spark.

Aby zezwolić na dostęp do Azure Cosmos DB danych:

* Jeśli używasz usługi Azure Synapse Link do wykonywania zapytań Azure Cosmos DB danych, dodaj zarządzany analityczny prywatny punkt końcowy dla Azure Cosmos DB danych. 

* Jeśli używasz wsadowych operacji zapisu/odczytu i/lub strumieniowego zapisu/odczytu do magazynu transakcyjnego, dodaj zarządzany prywatny punkt końcowy *SQL* lub *MongoDB* dla Azure Cosmos DB danych. Ponadto należy również ustawić tryb *connectionMode* na *gateway,* jak pokazano w poniższym fragmencie kodu:

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

## <a name="using-synapse-serverless-sql-pools"></a>Korzystanie z bez serwera pul SQL usługi Synapse

Bezserowe pule SQL usługi Synapse korzystają z funkcji wielodostępnych, które nie są wdrażane w zarządzanej sieci wirtualnej. Jeśli konto Azure Cosmos DB ma istniejący prywatny punkt końcowy, bez serwera pula SQL usługi Synapse nie będzie mieć dostępu do konta z powodu kontroli izolacji sieci na Azure Cosmos DB danych.

Aby skonfigurować izolację sieci dla tego konta z obszaru roboczego usługi Synapse:

1. Zezwalaj obszarowi roboczego synapse na dostęp do Azure Cosmos DB, `NetworkAclBypassResourceId` określając ustawienie na koncie.

   **Korzystanie z programu PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Korzystanie z interfejsu wiersza polecenia platformy Azure**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB i Azure Synapse Analytics powinny być w tej samej dzierżawie Azure Active Directory (AD).

2. Teraz możesz uzyskać dostęp do konta z bez serwera pul SQL przy użyciu zapytań T-SQL za pośrednictwem Azure Synapse Link. Jednak aby zapewnić izolację sieci dla danych w  magazynie analitycznym, należy dodać zarządzany przez analizę prywatny punkt końcowy dla tego konta. W przeciwnym razie dostęp publiczny do danych w magazynie analitycznym nie zostanie zablokowany.

> [!IMPORTANT]
> Jeśli używasz usługi Azure Synapse Link i potrzebujesz izolacji sieci dla danych w magazynie analitycznym, musisz zamapować konto usługi Azure Cosmos DB na obszar roboczy usługi Synapse przy użyciu zarządzanego przez analizę prywatnego punktu końcowego. 

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do wykonywania zapytań [w magazynie analitycznym za pomocą Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Wprowadzenie do wykonywania zapytań [w magazynie analitycznym przy użyciu Azure Synapse bez serwera SQL](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
