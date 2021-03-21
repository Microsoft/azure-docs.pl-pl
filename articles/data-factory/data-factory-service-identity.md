---
title: Tożsamość zarządzana dla usługi Data Factory
description: Informacje o tożsamości zarządzanej Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: jingwang
ms.openlocfilehash: e0d3b551265a480a700f374ddfcf89dd4d93333f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389165"
---
# <a name="managed-identity-for-data-factory"></a>Tożsamość zarządzana dla usługi Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten artykuł pomaga zrozumieć, co to jest tożsamość zarządzana Data Factory (znana wcześniej jako tożsamość usługi zarządzanej/MSI) i jak działa.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Podczas tworzenia fabryki danych można utworzyć zarządzaną tożsamość wraz z tworzeniem fabryczną. Zarządzana tożsamość to zarządzana aplikacja zarejestrowana do Azure Active Directory i reprezentuje tę konkretną fabrykę danych.

Tożsamość zarządzana dla Data Factory korzysta z następujących funkcji:

- [Poświadczenie magazynu w Azure Key Vault](store-credentials-in-key-vault.md), w którym ma być używana tożsamość zarządzana fabryki danych do Azure Key Vault uwierzytelniania.
- Łączniki, w tym [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md)i [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md).
- [Działanie sieci Web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generuj tożsamość zarządzaną

Tożsamość zarządzana dla Data Factory jest generowana w następujący sposób:

- Podczas tworzenia fabryki danych za poorednictwem **Azure Portal lub programu PowerShell**, tożsamość zarządzana zawsze zostanie utworzona automatycznie.
- Podczas tworzenia fabryki danych za pomocą **zestawu SDK** tożsamość zarządzana zostanie utworzona tylko wtedy, gdy w obiekcie fabryki zostanie określona wartość "Identity = New FactoryIdentity ()". Zobacz przykład w programie [.NET — szybki start — tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Podczas tworzenia fabryki danych przy użyciu **interfejsu API REST** tożsamość zarządzana zostanie utworzona tylko wtedy, gdy w treści żądania określono sekcję "Identity". Zobacz przykład w temacie [Szybki Start — tworzenie fabryki danych](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Jeśli okaże się, że Fabryka danych nie jest skojarzona z tożsamością [zarządzaną](#retrieve-managed-identity) , można ją jawnie wygenerować, aktualizując jednocześnie fabrykę danych przy użyciu inicjatora tożsamości:

- [Generowanie tożsamości zarządzanej przy użyciu programu PowerShell](#generate-managed-identity-using-powershell)
- [Generowanie tożsamości zarządzanej przy użyciu interfejsu API REST](#generate-managed-identity-using-rest-api)
- [Generuj zarządzaną tożsamość przy użyciu szablonu Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generuj tożsamość zarządzaną za pomocą zestawu SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Nie można zmodyfikować tożsamości zarządzanej. Aktualizacja fabryki danych, która ma już tożsamość zarządzana nie ma żadnego wpływu, zarządzana tożsamość jest zachowana bez zmian.
>- W przypadku zaktualizowania fabryki danych, która ma już tożsamość zarządzaną bez określenia parametru "Identity" w obiekcie fabryki lub bez określenia "Identity" w treści żądania REST, zostanie wyświetlony komunikat o błędzie.
>- Po usunięciu fabryki danych skojarzona tożsamość zarządzana zostanie usunięta.

### <a name="generate-managed-identity-using-powershell"></a>Generowanie tożsamości zarządzanej przy użyciu programu PowerShell

Call **Set-AzDataFactoryV2** polecenie, a następnie zobaczysz nowo generowane pola "Identity":

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

Wywołaj poniżej interfejsu API z sekcją "Identity" w treści żądania:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Treść żądania**: Dodaj "tożsamość": {"Type": "SystemAssigned"}.

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

**Odpowiedź**: tożsamość zarządzana jest tworzona automatycznie, a sekcja "Identity" jest odpowiednio wypełniana.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generuj zarządzaną tożsamość przy użyciu szablonu Azure Resource Manager

**Szablon**: Dodaj "Identity": {"Type": "SystemAssigned"}.

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

### <a name="generate-managed-identity-using-sdk"></a>Generuj tożsamość zarządzaną za pomocą zestawu SDK

Wywołaj funkcję create_or_update fabryki danych z tożsamością = New FactoryIdentity (). Przykładowy kod korzystający z platformy .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Pobierz tożsamość zarządzaną

Tożsamość zarządzaną można pobrać z Azure Portal lub programowo. W poniższych sekcjach przedstawiono kilka przykładów.

>[!TIP]
> Jeśli nie widzisz tożsamości zarządzanej, [Wygeneruj tożsamość zarządzaną](#generate-managed-identity) , aktualizując fabrykę.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Pobierz tożsamość zarządzaną za pomocą Azure Portal

Informacje o tożsamości zarządzanej można znaleźć na stronie Azure Portal — > właściwości > fabryki danych.

- Identyfikator obiektu tożsamości zarządzanej
- Zarządzana dzierżawa tożsamości
- Identyfikator zarządzanej aplikacji tożsamości

Informacje o tożsamości zarządzanej również zostaną wyświetlone podczas tworzenia połączonej usługi, która obsługuje uwierzytelnianie tożsamości zarządzanej, takie jak obiekt blob platformy Azure, Azure Data Lake Storage, Azure Key Vault itd.

Podczas udzielania uprawnień Użyj identyfikatora obiektu lub nazwy fabryki danych (jako nazwy tożsamości zarządzanej), aby znaleźć tę tożsamość.

### <a name="retrieve-managed-identity-using-powershell"></a>Pobieranie tożsamości zarządzanej przy użyciu programu PowerShell

Identyfikator podmiotu zabezpieczeń tożsamości zarządzanej i identyfikator dzierżawy zostaną zwrócone, gdy uzyskasz określoną fabrykę danych w następujący sposób. Użyj **PrincipalId** , aby udzielić dostępu:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Identyfikator aplikacji można uzyskać, kopiując powyżej identyfikatora podmiotu zabezpieczeń, a następnie uruchamiając polecenie Azure Active Directory z IDENTYFIKATORem podmiotu zabezpieczeń jako parametr.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>Pobieranie tożsamości zarządzanej przy użyciu interfejsu API REST

Identyfikator podmiotu zabezpieczeń tożsamości zarządzanej i identyfikator dzierżawy zostaną zwrócone, gdy uzyskasz określoną fabrykę danych w następujący sposób.

Wywołaj poniżej interfejsu API w żądaniu:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Odpowiedź**: otrzymasz odpowiedź, jak pokazano w poniższym przykładzie. Sekcja "tożsamość" jest odpowiednio wypełniana.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Aby pobrać zarządzaną tożsamość z szablonu ARM, **Dodaj sekcję Outputs w pliku JSON** usługi ARM:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi tematami, które wprowadzają, kiedy i jak używać tożsamości zarządzanej przez fabrykę danych:

- [Przechowywanie poświadczeń w Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopiowanie danych z/do Azure Data Lake Store przy użyciu tożsamości zarządzanych na potrzeby uwierzytelniania zasobów platformy Azure](connector-azure-data-lake-store.md)

Zobacz [zarządzane tożsamości dla zasobów platformy Azure — omówienie](../active-directory/managed-identities-azure-resources/overview.md) , aby uzyskać więcej informacji na temat tożsamości zarządzanych dla zasobów platformy Azure, na podstawie których jest oparta tożsamość zarządzana w usłudze Data Factory.