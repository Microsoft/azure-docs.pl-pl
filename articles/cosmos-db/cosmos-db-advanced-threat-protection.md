---
title: Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB
description: Dowiedz się, jak Azure Cosmos DB zapewnia szyfrowanie danych w stanie spoczynku i sposób ich implementacji.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: 57d319d54d15b72747da029d365137f5b5bb384c
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489255"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)

Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do kont Azure Cosmos DB lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń, nawet bez biegłych zabezpieczeń, i integrowanie ich z centralnymi systemami monitorowania zabezpieczeń.

Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. Te alerty zabezpieczeń są zintegrowane z usługą  [Azure Security Center](https://azure.microsoft.com/services/security-center/)i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zagrożeń.

> [!NOTE]
>
> * Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB jest obecnie dostępna tylko dla interfejsu API SQL.
> * Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB nie jest obecnie dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

W przypadku pełnego badania alertów zabezpieczeń zaleca się włączenie [rejestrowania diagnostycznego w Azure Cosmos DB](./monitor-cosmos-db.md), które rejestruje operacje na samej bazie danych, w tym CRUD operacje na wszystkich dokumentach, kontenerach i bazach danych.

## <a name="threat-types"></a>Typy zagrożeń

Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Obecnie można wyzwolić następujące alerty:

- **Dostęp z nietypowych lokalizacji**: ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do konta usługi Azure Cosmos, w którym ktoś nawiązał połączenie z punktem końcowym Azure Cosmos DB z nietypowej lokalizacji geograficznej. W niektórych przypadkach alert wykrywa legalną akcję, co oznacza, że jest to nowa aplikacja lub operacja konserwacji dewelopera. W innych przypadkach alert wykrywa złośliwe działanie od byłego pracownika, ataku zewnętrznego itp.

- **Nietypowa wyodrębnianie danych**: ten alert jest wyzwalany, gdy klient wyodrębnia nietypową ilość danych z konta Azure Cosmos DB. Może to być objaw niektórych eksfiltracji danych, które są wykonywane w celu przetransferowania wszystkich danych przechowywanych na koncie do zewnętrznego magazynu danych.



## <a name="configure-advanced-threat-protection"></a>Konfigurowanie zaawansowanej ochrony przed zagrożeniami

Można skonfigurować zaawansowaną ochronę przed zagrożeniami na kilka sposobów, które opisano w poniższych sekcjach.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Uruchom Azure Portal pod adresem  [https://portal.azure.com](https://portal.azure.com/) .

2. Z poziomu konta Azure Cosmos DB z menu **Ustawienia** wybierz pozycję **Zabezpieczenia zaawansowane**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="Konfigurowanie ATP":::

3. W bloku **zaawansowanej konfiguracji zabezpieczeń** :

    * Kliknij opcję **zaawansowanej ochrony przed zagrożeniami** , aby ustawić ją **na wartość włączone**.
    * Kliknij przycisk **Zapisz**, aby zapisać nowe lub zaktualizowane zasady usługi Advanced Threat Protection.   

### <a name="rest-api"></a>[Interfejs API REST](#tab/rest-api)

Użyj poleceń interfejsu API REST, aby utworzyć, zaktualizować lub pobrać ustawienie zaawansowanej ochrony przed zagrożeniami dla określonego konta Azure Cosmos DB.

* [Zaawansowana ochrona przed zagrożeniami — tworzenie](/rest/api/securitycenter/advancedthreatprotection/create)
* [Zaawansowana ochrona przed zagrożeniami — Pobierz](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Użyj następujących poleceń cmdlet programu PowerShell:

* [Włączanie zaawansowanej ochrony przed zagrożeniami](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)
* [Uzyskaj zaawansowaną ochronę przed zagrożeniami](/powershell/module/az.security/get-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)
* [Wyłącz zaawansowaną ochronę przed zagrożeniami](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection?viewFallbackFrom=azps-2.4.0)

### <a name="arm-template"></a>[Szablon usługi ARM](#tab/arm-template)

Użyj szablonu Azure Resource Manager (ARM), aby skonfigurować Cosmos DB z włączoną funkcją zaawansowanej ochrony przed zagrożeniami.
Aby uzyskać więcej informacji, zobacz [Tworzenie konta CosmosDB z zaawansowaną ochroną przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Użyj Azure Policy, aby włączyć zaawansowaną ochronę przed zagrożeniami dla Cosmos DB.

1. Uruchom stronę **definicje zasad** platformy Azure i Wyszukaj zasady **Wdróż zaawansowaną ochronę przed zagrożeniami dla Cosmos DB** .

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Konfigurowanie ATP"::: 

1. Kliknij pozycję **Wdróż zaawansowaną ochronę przed zagrożeniami dla programu CosmosDB** , a następnie kliknij pozycję **Przypisz**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Konfigurowanie ATP":::


1. W polu **zakres** kliknij trzy kropki, wybierz subskrypcję platformy Azure lub grupę zasobów, a następnie kliknij pozycję **Wybierz**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="Konfigurowanie ATP":::


1. Wprowadź inne parametry, a następnie kliknij przycisk **Przypisz**.




## <a name="manage-atp-security-alerts"></a>Zarządzanie alertami zabezpieczeń ATP

W przypadku wystąpienia anomalii działania Azure Cosmos DB alert zabezpieczeń jest wyzwalany z informacjami o podejrzanym zdarzeniu zabezpieczeń. 

 Z poziomu Azure Security Center można przeglądać bieżące [alerty zabezpieczeń](../security-center/security-center-alerts-overview.md)i zarządzać nimi.  Kliknij określony alert w [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) , aby wyświetlić możliwe przyczyny i zalecane działania w celu zbadania i ograniczenia potencjalnego zagrożenia. Na poniższej ilustracji przedstawiono przykład szczegółów alertów, które znajdują się w Security Center.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Konfigurowanie ATP":::

Powiadomienie e-mail jest również wysyłane z danymi alertów i zalecanymi akcjami. Na poniższej ilustracji przedstawiono przykład wiadomości e-mail dotyczącej alertu.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Konfigurowanie ATP":::

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB alerty ATP

 Aby wyświetlić listę alertów wygenerowanych podczas monitorowania kont Azure Cosmos DB, zapoznaj się z sekcją [Cosmos DB alertów](../security-center/alerts-reference.md#alerts-azurecosmos) w dokumentacji dotyczącej Azure Security Center.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [rejestrowaniu diagnostycznym w Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Dowiedz się więcej o [Azure Security Center](../security-center/security-center-introduction.md)