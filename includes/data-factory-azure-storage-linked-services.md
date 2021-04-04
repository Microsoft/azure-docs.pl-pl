---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001748"
---
### <a name="azure-storage-linked-service"></a>Połączona usługa Azure Storage
**Połączona usługa Azure Storage** umożliwia łączenie konta usługi Azure Storage z usługą Azure Data Factory przy użyciu **klucza konta**, który zapewnia fabrykę danych z dostępem globalnym do usługi Azure Storage. Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi Azure Storage.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi mieć wartość: **AzureStorage** |Tak |
| Parametry połączenia |Określ informacje, które są konieczne do nawiązania połączenia z usługą Azure Storage dla właściwości connectionString. |Tak |

Aby uzyskać informacje na temat pobierania kluczy dostępu do konta magazynu, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../articles/storage/common/storage-account-keys-manage.md).

**Przykład:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Połączona usługa Azure Storage SAS
Sygnatura dostępu współdzielonego (SAS, shared access signature) zapewnia delegowany dostęp do zasobów w ramach konta magazynu. Umożliwia przyznanie klientowi ograniczonych uprawnień do obiektów na koncie magazynu przez określony czas i z określonym zestawem uprawnień bez konieczności udostępniania kluczy dostępu do konta. Sygnatura dostępu współdzielonego to identyfikator URI, który obejmuje parametry zapytania, wszystkie informacje niezbędne do uzyskania uwierzytelniony dostęp do zasobu magazynu. Aby uzyskać dostęp do zasobów magazynu z sygnaturą dostępu współdzielonego, klient musi przekazać sygnaturę dostępu współdzielonego tylko do odpowiedniego konstruktora lub metody. Aby uzyskać więcej informacji na temat SAS, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> Azure Data Factory teraz obsługuje tylko **sygnatury dostępu współdzielonego usługi** , ale nie konta SAS. Zwróć uwagę na adres URL sygnatury dostępu współdzielonego generable z Azure Portal lub Eksplorator usługi Storage jest to konto SAS, które nie jest obsługiwane.

> [!TIP]
> Możesz wykonać poniższe polecenia programu PowerShell, aby wygenerować sygnaturę dostępu współdzielonego usługi dla konta magazynu (Zastąp symbol zastępczy i Udziel wymaganych uprawnień): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Połączona usługa Azure Storage SAS umożliwia łączenie konta usługi Azure Storage z fabryką danych platformy Azure przy użyciu sygnatury dostępu współdzielonego (SAS). Zapewnia ona fabrykę danych z ograniczonym dostępnym przez czas dostępem do wszystkich/konkretnych zasobów (BLOB/Container) w magazynie. Poniższa tabela zawiera opis elementów JSON specyficznych dla połączonej usługi Azure Storage SAS. 

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ |Właściwość Type musi mieć wartość: **AzureStorageSas** |Tak |
| sasUri |Określ identyfikator URI sygnatury dostępu współdzielonego do zasobów usługi Azure Storage, takich jak obiekt BLOB, kontener lub tabela.  |Tak |

**Przykład:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

Podczas tworzenia **identyfikatora URI sygnatury dostępu współdzielonego** Rozważ następujące kwestie:  

* Ustaw odpowiednie **uprawnienia** do odczytu/zapisu dla obiektów na podstawie sposobu używania połączonej usługi (odczyt, zapis, odczyt/zapis) w fabryce danych.
* Ustaw odpowiednio **czas wygaśnięcia** . Upewnij się, że dostęp do obiektów usługi Azure Storage nie wygasa w aktywnym okresie potoku.
* Identyfikator URI należy utworzyć na odpowiednim poziomie kontenera/obiektu BLOB lub tabeli na podstawie potrzeb. Identyfikator URI sygnatury dostępu współdzielonego do obiektu blob platformy Azure umożliwia usłudze Data Factory dostęp do tego określonego obiektu BLOB. Identyfikator URI sygnatury dostępu współdzielonego do kontenera obiektów blob platformy Azure umożliwia usłudze Data Factory Iterowanie za pomocą obiektów BLOB w tym kontenerze. Jeśli chcesz zapewnić dostęp do więcej/mniej obiektów później, lub zaktualizuj identyfikator URI sygnatury dostępu współdzielonego, pamiętaj, aby zaktualizować połączoną usługę nowym identyfikatorem URI.   

