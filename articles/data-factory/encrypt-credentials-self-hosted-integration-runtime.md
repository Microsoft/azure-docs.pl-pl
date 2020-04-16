---
title: Szyfrowanie poświadczeń w fabryce danych platformy Azure
description: Dowiedz się, jak szyfrować i przechowywać poświadczenia dla lokalnych magazynów danych na komputerze z własnym hostowanym środowiskiem uruchomieniowym integracji.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8ce26360aca8d8408135cbe89aabff4f923013b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416368"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Szyfrowanie poświadczeń dla lokalnych magazynów danych w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Poświadczenia lokalne (połączone z poufnymi informacjami) można szyfrować i przechowywać na komputerze z własnym środowiskiem działania integracji. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Plik definicji JSON z poświadczeniami należy przekazać do <br/>Polecenie cmdlet [**New-AzDataFactoryV2LinkedServiceEncryptedCredCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) do produkcji wyjściowego pliku definicji JSON z zaszyfrowanymi poświadczeniami. Następnie użyj zaktualizowanej definicji JSON, aby utworzyć połączone usługi.

## <a name="author-sql-server-linked-service"></a>Usługa połączona author SQL Server
Utwórz plik JSON o nazwie **SqlServerLinkedService.json** w dowolnym folderze z następującą zawartością:  

Przed zapisaniem `<servername>`pliku zamień `<username>`, i `<password>` wartości dla programu SQL Server. `<databasename>` I zastąpić `<integration runtime name>` nazwą środowiska wykonawczego integracji. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Szyfrowanie poświadczeń
Aby zaszyfrować poufne dane z ładunku JSON w lokalnym środowisku wykonywania integracji hostowanej samodzielnie, uruchom **program New-AzDataFactoryV2LinkedServiceEncryptedCredcredential**i przekaż ładunek JSON. To polecenie cmdlet zapewnia, że poświadczenia są szyfrowane przy użyciu DPAPI i przechowywane w węźle środowiska wykonawczego integracji hostowanej samodzielnie lokalnie. Ładunek wyjściowy zawierający zaszyfrowane odwołanie do poświadczeń może zostać przekierowany do innego pliku JSON (w tym przypadku 'encryptedLinkedService.json').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Używanie JSON z zaszyfrowanymi poświadczeniami
Teraz użyj wyjściowego pliku JSON z poprzedniego polecenia zawierającego zaszyfrowane poświadczenia, aby skonfigurować **program SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat zagadnień dotyczących zabezpieczeń dotyczących przenoszenia danych, zobacz [Zagadnienia dotyczące zabezpieczeń przenoszenia danych](data-movement-security-considerations.md).

