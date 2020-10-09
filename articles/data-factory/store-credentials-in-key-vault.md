---
title: Przechowywanie poświadczeń w usłudze Azure Key Vault
description: Dowiedz się, jak przechowywać poświadczenia dla magazynów danych używanych w magazynie kluczy platformy Azure, które Azure Data Factory mogą być automatycznie pobierane w czasie wykonywania.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: 22ab4433d84db926733fd0b18035875e63322dda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81451690"
---
# <a name="store-credential-in-azure-key-vault"></a>Przechowywanie poświadczeń w Azure Key Vault

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Poświadczenia dla magazynów danych i obliczeń można przechowywać w [Azure Key Vault](../key-vault/general/overview.md). Azure Data Factory Pobiera poświadczenia podczas wykonywania działania, które korzysta z magazynu/obliczeń danych.

Obecnie wszystkie typy działań z wyjątkiem działania niestandardowego obsługują tę funkcję. Aby uzyskać szczegółowe informacje na temat konfiguracji łącznika, należy zapoznać się z sekcją "właściwości połączonej usługi" w [poszczególnych tematach łączników](copy-activity-overview.md#supported-data-stores-and-formats) .

## <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja opiera się na tożsamości zarządzanej przez fabrykę danych. Dowiedz się, jak działa z [tożsamości zarządzanej usługi Fabryka danych](data-factory-service-identity.md) i upewnij się, że Fabryka danych ma skojarzoną aplikację.

## <a name="steps"></a>Kroki

Aby odwołać się do poświadczeń przechowywanych w Azure Key Vault, należy:

1. **Pobierz tożsamość zarządzaną przez fabrykę danych** , kopiując wartość "Identyfikator obiektu tożsamości zarządzanej" wygenerowaną wraz z fabryką. Jeśli używasz interfejsu użytkownika tworzenia APD, identyfikator obiektu tożsamości zarządzanej będzie wyświetlany w oknie Azure Key Vault tworzenia połączonej usługi. można go również pobrać z Azure Portal, aby [pobrać tożsamość zarządzaną przez fabrykę danych](data-factory-service-identity.md#retrieve-managed-identity).
2. **Przyznaj zarządzanej tożsamości dostęp do Azure Key Vault.** W magazynie kluczy — > zasad dostępu — > Dodaj zasady dostępu, Wyszukaj tę tożsamość zarządzaną, aby udzielić uprawnienia **Get** na liście rozwijanej uprawnienia do klucza tajnego. Umożliwia to wyznaczeniu fabryki dostęp do wpisów tajnych w magazynie kluczy.
3. **Utwórz połączoną usługę wskazującą Azure Key Vault.** Zapoznaj się z [Azure Key Vault połączoną usługą](#azure-key-vault-linked-service).
4. **Utwórz połączoną usługę magazynu danych, w której znajduje się odwołanie do odpowiedniego klucza tajnego przechowywanego w magazynie kluczy.** Zapoznaj się z [wpisem tajnym referencyjnym przechowywanym w magazynie kluczy](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Połączona usługa Azure Key Vault

Następujące właściwości są obsługiwane dla Azure Key Vault połączonej usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type musi mieć wartość: **AzureKeyVault**. | Tak |
| baseUrl | Określ adres URL Azure Key Vault. | Tak |

**Korzystanie z interfejsu użytkownika tworzenia:**

Wybierz pozycję **połączenia**  ->  **połączone usługi**  ->  **Nowość**. W obszarze Nowa połączona usługa Wyszukaj i wybierz pozycję "Azure Key Vault":

![Azure Key Vault wyszukiwania](media/store-credentials-in-key-vault/search-akv.png)

Wybierz Azure Key Vault, w którym są przechowywane Twoje poświadczenia. Możesz **przetestować połączenie** , aby upewnić się, że połączenie usługi AKV jest prawidłowe. 

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

Następujące właściwości są obsługiwane podczas konfigurowania pola w połączonej usłudze, do którego odwołuje się klucz tajny magazynu kluczy:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| typ | Właściwość Type pola musi być ustawiona na wartość: **AzureKeyVaultSecret**. | Tak |
| secretName | Nazwa wpisu tajnego w Azure Key Vault. | Tak |
| Wersjawpisutajnego | Wersja wpisu tajnego w Azure Key Vault.<br/>Jeśli nie zostanie określony, zawsze używa najnowszej wersji klucza tajnego.<br/>Jeśli ta wartość jest określona, zostanie ona dodana do danej wersji.| Nie |
| store | Odnosi się do Azure Key Vault połączonej usługi, która jest używana do przechowywania poświadczeń. | Tak |

**Korzystanie z interfejsu użytkownika tworzenia:**

Wybierz **Azure Key Vault** dla pól tajnych podczas tworzenia połączenia z magazynem danych/obliczeniem. Wybierz połączoną usługę Azure Key Vault i podaj **nazwę klucza tajnego**. Opcjonalnie możesz również podać wersję tajną. 

>[!TIP]
>W przypadku łączników korzystających z parametrów połączenia w połączonej usłudze, takich jak SQL Server, BLOB Storage itp., można wybrać opcję przechowywania tylko pola tajnego, np. Password in AKV lub przechowywania całych parametrów połączenia w AKV. Obie opcje można znaleźć w interfejsie użytkownika.

![Konfigurowanie Azure Key Vault klucza tajnego](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Przykład JSON: (zobacz sekcję "hasło")**

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
Listę magazynów danych obsługiwanych jako źródła i ujścia przez działanie kopiowania w Azure Data Factory można znaleźć w temacie [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
