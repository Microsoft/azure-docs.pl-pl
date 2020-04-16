---
title: Tożsamość zarządzana dla usługi Data Factory
description: Dowiedz się więcej o tożsamości zarządzanej dla usługi Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: d47450f3252074d3bae8df97766bf8858fca5972
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416595"
---
# <a name="managed-identity-for-data-factory"></a>Tożsamość zarządzana dla usługi Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten artykuł pomaga zrozumieć, co jest zarządzana tożsamości dla fabryki danych (dawniej znany jako tożsamość usługi zarządzanej/MSI) i jak to działa.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Podczas tworzenia fabryki danych można utworzyć tożsamość zarządzaną wraz z tworzeniem fabryki. Tożsamość zarządzana jest aplikacją zarządzaną zarejestrowaną w usłudze Azure Active Directory i reprezentuje tę fabrykę określonych danych.

Tożsamość zarządzana dla usługi Data Factory korzysta z następujących funkcji:

- [Poświadczenia magazynu w usłudze Azure Key Vault](store-credentials-in-key-vault.md), w którym to przypadku tożsamość zarządzana fabrycznie danych jest używana do uwierzytelniania usługi Azure Key Vault.
- Łączniki, w tym [magazyn obiektów blob platformy Azure,](connector-azure-blob-storage.md) [usługa Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [usługa Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [usługa Azure SQL Database](connector-azure-sql-database.md)i usługa Azure SQL Data [Warehouse](connector-azure-sql-data-warehouse.md).
- [Aktywność w sieci Web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generowanie tożsamości zarządzanej

Tożsamość zarządzana dla usługi Data Factory jest generowana w następujący sposób:

- Podczas tworzenia fabryki danych za pośrednictwem **witryny Azure portal lub programu PowerShell**tożsamość zarządzana będzie zawsze tworzona automatycznie.
- Podczas tworzenia fabryki danych za pomocą **SDK,** tożsamość zarządzana zostanie utworzona tylko wtedy, gdy określisz "Tożsamość = nowa FactoryIdentity()" w obiekcie fabrycznym do utworzenia. Zobacz przykład w [.NET Szybki start - tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Podczas tworzenia fabryki danych za pośrednictwem **interfejsu REST API**tożsamość zarządzana zostanie utworzona tylko wtedy, gdy określisz sekcję "tożsamość" w treści żądania. Zobacz przykład w [rest szybki start - tworzenie fabryki danych](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Jeśli okaże się, że fabryka danych nie ma skojarzonej tożsamości zarządzanej po [pobraniu instrukcji tożsamości zarządzanej,](#retrieve-managed-identity) możesz ją wygenerować, aktualizując fabrykę danych za pomocą inicjatora tożsamości programowo:

- [Generowanie tożsamości zarządzanej przy użyciu programu PowerShell](#generate-managed-identity-using-powershell)
- [Generowanie tożsamości zarządzanej przy użyciu interfejsu API REST](#generate-managed-identity-using-rest-api)
- [Generowanie tożsamości zarządzanej przy użyciu szablonu usługi Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generowanie tożsamości zarządzanej przy użyciu sdk](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Nie można zmodyfikować tożsamości zarządzanej. Aktualizowanie fabryki danych, które już mają tożsamość zarządzaną nie będzie miało żadnego wpływu, tożsamość zarządzana jest utrzymywana bez zmian.
>- Jeśli zaktualizujesz fabrykę danych, która ma już tożsamość zarządzaną bez określania parametru "identity" w obiekcie fabrycznym lub bez określania sekcji "tożsamość" w treści żądania REST, zostanie wyświetlony błąd.
>- Po usunięciu fabryki danych skojarzona tożsamość zarządzana zostanie usunięta.

### <a name="generate-managed-identity-using-powershell"></a>Generowanie tożsamości zarządzanej przy użyciu programu PowerShell

Wywołanie **Set-AzDataFactoryV2** polecenia ponownie, a następnie widzisz "Tożsamość" pola są nowo generowane:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Generowanie tożsamości zarządzanej przy użyciu interfejsu API REST

Zadzwoń poniżej interfejsu API z sekcji "tożsamość" w treści żądania:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Treść żądania:** dodaj "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Odpowiedź:** tożsamość zarządzana jest tworzona automatycznie, a sekcja "tożsamość" jest odpowiednio wypełniana.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generowanie tożsamości zarządzanej przy użyciu szablonu usługi Azure Resource Manager

**Szablon**: dodaj "tożsamość": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Generowanie tożsamości zarządzanej przy użyciu sdk

Wywołanie funkcji create_or_update fabryki danych za pomocą funkcji Identity=new FactoryIdentity(). Przykładowy kod przy użyciu platformy .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Pobieranie tożsamości zarządzanej

Tożsamość zarządzana można pobrać z witryny Azure portal lub programowo. W poniższych sekcjach przedstawiono niektóre przykłady.

>[!TIP]
> Jeśli nie widzisz tożsamości zarządzanej, [wygeneruj zarządzana tożsamość,](#generate-managed-identity) aktualizując fabrykę.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Pobieranie tożsamości zarządzanej przy użyciu portalu Azure

Informacje o tożsamości zarządzanej można znaleźć w witrynie Azure portal -> fabrycznie danych właściwości ->.

- Identyfikator obiektu tożsamości zarządzanej
- Dzierżawa tożsamości zarządzanej
- Identyfikator aplikacji tożsamości zarządzanej

Informacje o tożsamości zarządzanej pojawią się również podczas tworzenia połączonej usługi obsługującej uwierzytelnianie tożsamości zarządzanej, takie jak azure blob, usługa Azure Data Lake Storage, usługa Azure Key Vault itp.

Podczas udzielania uprawnień użyj identyfikatora obiektu lub nazwy fabryki danych (jako nazwy tożsamości zarządzanej), aby znaleźć tę tożsamość.

### <a name="retrieve-managed-identity-using-powershell"></a>Pobieranie tożsamości zarządzanej przy użyciu programu PowerShell

Identyfikator jednostki zarządzanej i identyfikator dzierżawy zostaną zwrócone po otrzymaniu określonej fabryki danych w następujący sposób. Użyj **obiektu PrincipalId,** aby udzielić dostępu:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Identyfikator aplikacji można uzyskać, kopiując powyższy identyfikator jednostki, a następnie uruchamiając polecenie poniżej usługi Azure Active Directory z identyfikatorem głównym jako parametrem.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Następne kroki
Zobacz następujące tematy, które wprowadzają, kiedy i jak używać tożsamości zarządzanej w fabryce danych:

- [Poświadczenia magazynu w usłudze Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopiowanie danych z/do usługi Azure Data Lake Store przy użyciu tożsamości zarządzanych do uwierzytelniania zasobów platformy Azure](connector-azure-data-lake-store.md)

Zobacz [tożsamości zarządzane dla zasobów platformy Azure Omówienie](/azure/active-directory/managed-identities-azure-resources/overview) więcej tła na temat tożsamości zarządzanych dla zasobów platformy Azure, na których opiera się tożsamość zarządzana fabrycznie danych. 
