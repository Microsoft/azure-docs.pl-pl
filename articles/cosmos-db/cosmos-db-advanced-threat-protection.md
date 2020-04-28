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
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77614831"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB (wersja zapoznawcza)

Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB stanowi dodatkową warstwę analizy zabezpieczeń, która wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do kont Azure Cosmos DB lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń, nawet bez biegłych zabezpieczeń, i integrowanie ich z centralnymi systemami monitorowania zabezpieczeń.

Alerty zabezpieczeń są wyzwalane, gdy wystąpią anomalie działania. Te alerty zabezpieczeń są zintegrowane z usługą [Azure Security Center](https://azure.microsoft.com/services/security-center/)i są również wysyłane pocztą e-mail do administratorów subskrypcji, ze szczegółowymi informacjami o podejrzanych działaniach i zaleceniach dotyczących sposobu badania i korygowania zagrożeń.

> [!NOTE]
>
> * Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB jest obecnie dostępna tylko dla interfejsu API SQL.
> * Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB nie jest obecnie dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

W przypadku pełnego badania alertów zabezpieczeń zaleca się włączenie [rejestrowania diagnostycznego w Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), które rejestruje operacje na samej bazie danych, w tym CRUD operacje na wszystkich dokumentach, kontenerach i bazach danych.

## <a name="threat-types"></a>Typy zagrożeń

Zaawansowana ochrona przed zagrożeniami dla Azure Cosmos DB wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania. Obecnie można wyzwolić następujące alerty:

- **Dostęp z nietypowych lokalizacji**: ten alert jest wyzwalany w przypadku zmiany wzorca dostępu do konta usługi Azure Cosmos, w którym ktoś nawiązał połączenie z punktem końcowym Azure Cosmos DB z nietypowej lokalizacji geograficznej. W niektórych przypadkach alert wykrywa legalną akcję, co oznacza, że jest to nowa aplikacja lub operacja konserwacji dewelopera. W innych przypadkach alert wykrywa złośliwe działanie od byłego pracownika, ataku zewnętrznego itp.

- **Nietypowa wyodrębnianie danych**: ten alert jest wyzwalany, gdy klient wyodrębnia nietypową ilość danych z konta Azure Cosmos DB. Może to być objaw niektórych eksfiltracji danych, które są wykonywane w celu przetransferowania wszystkich danych przechowywanych na koncie do zewnętrznego magazynu danych.

## <a name="set-up-advanced-threat-protection"></a>Skonfiguruj zaawansowaną ochronę przed zagrożeniami

### <a name="set-up-atp-using-the-portal"></a>Konfigurowanie usługi ATP przy użyciu portalu

1. Uruchom Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).

2. Z poziomu konta Azure Cosmos DB z menu **Ustawienia** wybierz pozycję **Zabezpieczenia zaawansowane**.

    ![Konfigurowanie ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. W bloku **zaawansowanej konfiguracji zabezpieczeń** :

    * Kliknij opcję **zaawansowanej ochrony przed zagrożeniami** , aby ustawić ją **na wartość włączone**.
    * Kliknij przycisk **Zapisz**, aby zapisać nowe lub zaktualizowane zasady usługi Advanced Threat Protection.   

### <a name="set-up-atp-using-rest-api"></a>Konfigurowanie usługi ATP przy użyciu interfejsu API REST

Użyj poleceń interfejsu API REST, aby utworzyć, zaktualizować lub pobrać ustawienie zaawansowanej ochrony przed zagrożeniami dla określonego konta Azure Cosmos DB.

* [Zaawansowana ochrona przed zagrożeniami — tworzenie](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Zaawansowana ochrona przed zagrożeniami — Pobierz](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Konfigurowanie usługi ATP przy użyciu Azure PowerShell

Użyj następujących poleceń cmdlet programu PowerShell:

* [Włączanie zaawansowanej ochrony przed zagrożeniami](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Uzyskaj zaawansowaną ochronę przed zagrożeniami](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Wyłącz zaawansowaną ochronę przed zagrożeniami](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Używanie szablonów usługi Azure Resource Manager

Użyj szablonu Azure Resource Manager, aby skonfigurować Cosmos DB z włączoną funkcją zaawansowanej ochrony przed zagrożeniami.
Aby uzyskać więcej informacji, zobacz [Tworzenie konta CosmosDB z zaawansowaną ochroną przed zagrożeniami](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Używanie Azure Policy

Użyj Azure Policy, aby włączyć zaawansowaną ochronę przed zagrożeniami dla Cosmos DB.

1. Uruchom stronę **definicje zasad** platformy Azure i Wyszukaj zasady **Wdróż zaawansowaną ochronę przed zagrożeniami dla Cosmos DB** .

    ![Zasady wyszukiwania](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Kliknij pozycję **Wdróż zaawansowaną ochronę przed zagrożeniami dla programu CosmosDB** , a następnie kliknij pozycję **Przypisz**.

    ![Wybierz subskrypcję lub grupę](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. W polu **zakres** kliknij trzy kropki, wybierz subskrypcję platformy Azure lub grupę zasobów, a następnie kliknij pozycję **Wybierz**.

    ![Strona definicji zasad](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Wprowadź inne parametry, a następnie kliknij przycisk **Przypisz**.

## <a name="manage-atp-security-alerts"></a>Zarządzanie alertami zabezpieczeń ATP

W przypadku wystąpienia anomalii działania Azure Cosmos DB alert zabezpieczeń jest wyzwalany z informacjami o podejrzanym zdarzeniu zabezpieczeń. 

 Z poziomu Azure Security Center można przeglądać bieżące [alerty zabezpieczeń](../security-center/security-center-alerts-overview.md)i zarządzać nimi.  Kliknij określony alert w [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) , aby wyświetlić możliwe przyczyny i zalecane działania w celu zbadania i ograniczenia potencjalnego zagrożenia. Na poniższej ilustracji przedstawiono przykład szczegółów alertów, które znajdują się w Security Center.

 ![Szczegóły zagrożenia](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Powiadomienie e-mail jest również wysyłane z danymi alertów i zalecanymi akcjami. Na poniższej ilustracji przedstawiono przykład wiadomości e-mail dotyczącej alertu.

 ![Szczegóły alertu](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB alerty ATP

 Aby wyświetlić listę alertów wygenerowanych podczas monitorowania kont Azure Cosmos DB, zapoznaj się z sekcją [Cosmos DB alertów](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) w dokumentacji dotyczącej Azure Security Center.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [rejestrowaniu diagnostycznym w Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Dowiedz się więcej o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
