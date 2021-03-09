---
title: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać zasobami Azure Cosmos DB przy użyciu szablonów Azure Portal, PowerShell, interfejsu wiersza polecenia i Azure Resource Manager
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.openlocfilehash: d542e2b4e5db86fd3354514790e718f0694a09a5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489756"
---
# <a name="manage-an-azure-cosmos-account"></a>Zarządzanie kontem usługi Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W poniższym artykule opisano sposób zarządzania różnymi zadaniami w ramach konta usługi Azure Cosmos przy użyciu witryny Azure Portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i szablonów usługi Azure Resource Manager.

## <a name="create-an-account"></a>Tworzenie konta

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Tworzenie konta Azure Cosmos dB przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

Zobacz [Tworzenie konta Azure Cosmos dB przy użyciu programu PowerShell](manage-with-powershell.md#create-account)

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Szablon usługi Azure Resource Manager

Zobacz [Tworzenie konta Azure Cosmos DB z szablonami Azure Resource Manager](./manage-with-templates.md)

## <a name="addremove-regions-from-your-database-account"></a>Dodawanie/usuwanie regionów z poziomu konta bazy danych

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure Portal

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Przejdź do konta usługi Azure Cosmos i otwórz menu **Replikacja danych globalnie** .

1. Aby dodać regiony, zaznacz sześciokąty na mapie z **+** etykietą odpowiadającą żądanym regionom. Alternatywnie, aby dodać region, wybierz opcję **+ Dodaj region** i wybierz region z menu rozwijanego.

1. Aby usunąć regiony, usuń co najmniej jeden region z mapy, wybierając niebieskie sześciokąty ze znacznikami wyboru. Możesz też wybrać ikonę „kosza na śmieci” (🗑) z prawej strony regionu.

1. Aby zapisać zmiany, wybierz pozycję **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="Menu dodawania lub usuwania regionów":::

W trybie zapisu w jednym regionie nie można usunąć regionu zapisu. Aby można było usunąć bieżący region zapisu, należy przejść do trybu failover w innym regionie.

W trybie zapisu w wielu regionach można dodawać lub usuwać dowolne regiony, o ile pozostaje co najmniej jeden region.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Dodawanie lub usuwanie regionów przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#add-or-remove-regions)

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

Zobacz [Dodawanie lub usuwanie regionów przy użyciu programu PowerShell](manage-with-powershell.md#update-account)

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>Konfigurowanie wielu regionów zapisu

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure Portal

Otwórz kartę **Replikuj dane globalnie** i wybierz pozycję **Włącz** , aby włączyć zapisywanie w ramach wieloregionu. Po włączeniu zapisów obejmujących wiele regionów wszystkie regiony odczytu znajdujące się obecnie na koncie staną się regionami odczytu i zapisu.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Konto usługi Azure Cosmos służy do konfigurowania wieloregionowego zrzutu ekranu":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Włączanie regionów wielu zapisów za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#enable-multiple-write-regions)

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Zobacz [Włączanie regionów wielu zapisów przy użyciu programu PowerShell](manage-with-powershell.md#multi-region-writes)

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Szablon usługi Resource Manager

Konto można migrować z jednego regionu zapisu do wielu regionów zapisu, wdrażając szablon Menedżer zasobów używany do tworzenia konta i ustawienia `enableMultipleWriteLocations: true` . Następujący szablon Azure Resource Manager jest nieminimalnym szablonem, który spowoduje wdrożenie konta Azure Cosmos dla interfejsu API SQL z dwoma regionami i włączenie wielu lokalizacji zapisu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Włączanie automatycznego przejścia do trybu failover dla konta usługi Azure Cosmos

Opcja automatycznej pracy awaryjnej umożliwia Azure Cosmos DB przełączenia w tryb failover do regionu z najwyższym priorytetem trybu failover bez akcji użytkownika, jeśli region staje się niedostępny. Gdy włączona jest automatyczna tryb failover, priorytet regionu można modyfikować. Aby można było włączyć automatyczne przełączanie do trybu failover, konto musi mieć co najmniej dwa regiony.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Na koncie usługi Azure Cosmos Otwórz okienko **Replikuj dane globalnie** .

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu globalnego replikowania danych":::

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**. 

4. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menu Automatyczne przełączanie w tryb failover w portalu":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Włączanie automatycznego przełączania do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#enable-automatic-failover)

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Zobacz [Włączanie automatycznego przełączania do trybu failover przy użyciu programu PowerShell](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ustawianie priorytetów trybu failover dla konta usługi Azure Cosmos

Po skonfigurowaniu konta usługi Cosmos do automatycznego przełączania w tryb failover priorytet trybu failover dla regionów można zmienić.

> [!IMPORTANT]
> Nie można zmodyfikować regionu zapisu (priorytet trybu failover równy zero), jeśli konto jest skonfigurowane do automatycznego przełączania do trybu failover. Aby zmienić region zapisu, należy wyłączyć automatyczne przełączanie do trybu failover i przeprowadzić ręczną pracę awaryjną.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Na koncie usługi Azure Cosmos Otwórz okienko **Replikuj dane globalnie** .

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu globalnego replikowania danych":::

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**.

4. Aby zmodyfikować priorytety trybu failover, przeciągnij regiony odczytu za pośrednictwem trzech kropek znajdujących się po lewej stronie wiersza, który jest wyświetlany po umieszczeniu na nich wskaźnika myszy.

5. Wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="Menu Automatyczne przełączanie w tryb failover w portalu":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [Ustawianie priorytetu trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#set-failover-priority)

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Zobacz [Ustawianie priorytetu trybu failover przy użyciu programu PowerShell](manage-with-powershell.md#modify-failover-priority)

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Ręczne przechodzenie do trybu failover na koncie usługi Azure Cosmos

> [!IMPORTANT]
> Aby ta operacja zakończyła się pomyślnie, należy skonfigurować konto usługi Azure Cosmos do ręcznego przełączania do trybu failover.

Proces przeprowadzania ręcznej pracy awaryjnej polega na zmianie regionu zapisu konta (priorytetu trybu failover = 0) w innym regionie skonfigurowanym dla tego konta.

> [!NOTE]
> Kont z wieloma regionami zapisu nie można przełączyć ręcznie do trybu failover. W przypadku aplikacji korzystających z zestawu SDK usługi Azure Cosmos zestaw SDK wykrywa, kiedy region stanie się niedostępny, a następnie przekierować automatycznie do następnego najbliższego regionu.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos i otwórz menu **Replikacja danych globalnie** .

2. W górnej części menu wybierz pozycję **Ręczne przejście do trybu failover**.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="Menu globalnego replikowania danych":::

3. W menu **Ręczne przejście do trybu failover** wybierz nowy region zapisu. Zaznacz pole wyboru, aby wskazać, że rozumiesz, iż ta opcja zmienia region zapisu.

4. Aby wyzwolić tryb failover, wybierz pozycję **OK**.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="Menu Ręczne przejście do trybu failover w portalu":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

Zobacz [wyzwalanie ręcznego przełączania do trybu failover przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#trigger-manual-failover)

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Zobacz [wyzwalanie ręcznego przełączania w tryb failover przy użyciu programu PowerShell](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i zapoznać się z przykładami dotyczącymi zarządzania kontem usługi Azure Cosmos oraz bazą danych i kontenerami, przeczytaj następujące artykuły:

* [Zarządzanie Azure Cosmos DB przy użyciu Azure PowerShell](manage-with-powershell.md)
* [Zarządzanie usługą Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md)