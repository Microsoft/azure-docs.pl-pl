---
title: Przechowywanie poświadczeń w usłudze Azure Key Vault
description: Dowiedz się, jak przechowywać poświadczenia dla magazynów danych używanych w magazynie kluczy platformy Azure, które usługa Azure Data Factory może automatycznie pobierać w czasie wykonywania.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: b5a181625488a57de4b878d13c01a8c90bf8785a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414660"
---
# <a name="store-credential-in-azure-key-vault"></a>Poświadczenia magazynu w usłudze Azure Key Vault

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Poświadczenia można przechowywać dla magazynów danych i obliczeń w [usłudze Azure Key Vault](../key-vault/key-vault-overview.md). Usługa Azure Data Factory pobiera poświadczenia podczas wykonywania działania, które używa magazynu danych/obliczeń.

Obecnie wszystkie typy działań z wyjątkiem działania niestandardowego obsługują tę funkcję. W szczególności w przypadku konfiguracji łącznika sprawdź sekcję "połączone właściwości usługi" w [każdym temacie łącznika,](copy-activity-overview.md#supported-data-stores-and-formats) aby uzyskać szczegółowe informacje.

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja opiera się na tożsamości zarządzanej fabrycznie danych. Dowiedz się, jak to działa z [zarządzanej tożsamości dla fabryki danych](data-factory-service-identity.md) i upewnij się, że fabryka danych ma skojarzony.

## <a name="steps"></a>Kroki

Aby odwołać się do poświadczeń przechowywanych w usłudze Azure Key Vault, należy:

1. **Pobierz dane fabrycznie zarządzane tożsamości,** kopiując wartość "Identyfikator obiektu tożsamości zarządzanej" generowane wraz z fabryki. If you use ADF authoring UI, the managed identity object ID will be shown on the Azure Key Vault linked service creation window; można również pobrać go z witryny Azure Portal, odnoszą się do [pobierania danych fabrycznie tożsamości zarządzanej](data-factory-service-identity.md#retrieve-managed-identity).
2. **Udziel dostępu do tożsamości zarządzanej do usługi Azure Key Vault.** W magazynie kluczy -> Zasady dostępu -> Dodaj zasady dostępu, przeszukaj tę tożsamość zarządzaną, aby udzielić uprawnień **Pobierz** w obszarze listy rozwijanej Uprawnienia tajne. Umożliwia to wyznaczone fabryki dostęp do klucza tajnego w magazynie kluczy.
3. **Utwórz połączony serwis wskazujący usługę Azure Key Vault.** Zapoznaj się z [usługą połączonyą usługi Azure Key Vault](#azure-key-vault-linked-service).
4. **Utwórz usługę połączony magazyn danych, wewnątrz którego odwołuje się do odpowiedniego klucza tajnego przechowywanego w magazynie kluczy.** Zapoznaj się [z kluczem tajnym zapisanym w magazynie kluczy](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Połączona usługa Azure Key Vault

Następujące właściwości są obsługiwane dla usługi połączonej usługi Azure Key Vault:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu musi być ustawiona na: **AzureKeyVault**. | Tak |
| baseUrl (podstawa) | Określ adres URL usługi Azure Key Vault. | Tak |

**Korzystanie z interfejsu użytkownika tworzenia:**

Wybierz **pozycję Połączenia** -> **połączone usługi** -> **Nowe**. W obszarze Nowa usługa połączona wyszukaj i wybierz "Azure Key Vault":

![Przeszukanie magazynu kluczy platformy Azure](media/store-credentials-in-key-vault/search-akv.png)

Wybierz aprowizawowaną usługę Azure Key Vault, w której są przechowywane poświadczenia. Można wykonać **test połączenia,** aby upewnić się, że połączenie AKV jest prawidłowe. 

![Konfigurowanie usługi Azure Key Vault](media/store-credentials-in-key-vault/configure-akv.png)

**Przykład JSON:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Wpis tajny odwołania przechowywany w magazynie kluczy

Następujące właściwości są obsługiwane podczas konfigurowania pola w połączonej usłudze odwołującej się do klucza tajnego magazynu kluczy:

| Właściwość | Opis | Wymagany |
|:--- |:--- |:--- |
| type | Właściwość typu pola musi być ustawiona na: **AzureKeyVaultSecret**. | Tak |
| nazwa secretName | Nazwa klucza tajnego w usłudze Azure Key Vault. | Tak |
| secretVersion (wersja secretVersion) | Wersja klucza tajnego w usłudze Azure Key Vault.<br/>Jeśli nie zostanie określony, zawsze używa najnowszej wersji klucza tajnego.<br/>Jeśli jest określony, to przykleja się do danej wersji.| Nie |
| store | Odnosi się do usługi połączonej usługi Azure Key Vault używanej do przechowywania poświadczeń. | Tak |

**Korzystanie z interfejsu użytkownika tworzenia:**

Wybierz **usługę Azure Key Vault** dla pól tajnych podczas tworzenia połączenia z magazynem/obliczaniem danych. Wybierz aprowizowana usługa linked Azure Key Vault i podaj **nazwę tajnego**. Opcjonalnie można również podać tajną wersję. 

>[!TIP]
>W przypadku łączników używających ciągu połączenia w usłudze połączonej, takich jak SQL Server, Magazyn obiektów Blob itp., można wybrać opcję przechowywania tylko pola tajnego, na przykład hasła w programie AKV, lub przechowywania całego ciągu połączenia w programie AKV. Obie opcje można znaleźć w interfejsie użytkownika.

![Konfigurowanie klucza tajnego usługi Azure Key Vault](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Przykład JSON: (patrz sekcja "hasło")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i pochłaniacze przez działanie kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
