---
title: Rejestrowanie Azure Key Vault | Microsoft Docs
description: Dowiedz się, jak monitorować dostęp do magazynów kluczy, włączając rejestrowanie dla Azure Key Vault, które zapisuje informacje na koncie usługi Azure Storage, które podano.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 5423fc27ecc58bcd79b36a845e4b7569f342f712
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286707"
---
# <a name="azure-key-vault-logging"></a>Funkcja rejestrowania usługi Azure Key Vault

Po utworzeniu co najmniej jednego magazynu kluczy prawdopodobnie zechcesz monitorować sposób i czas uzyskiwania dostępu do Twoich magazynów kluczy oraz przez kogo. Można to zrobić przez włączenie rejestrowania dla Azure Key Vault, które zapisuje informacje na koncie usługi Azure Storage, które podano. Aby uzyskać wskazówki krok po kroku dotyczące konfigurowania tego ustawienia, zobacz [jak włączyć rejestrowanie Key Vault](howto-logging.md).

Możesz uzyskać dostęp do informacji o rejestrowaniu 10 minut (maksymalnie) po operacji magazynu kluczy. W większości przypadków czas będzie krótszy.  To Ty zarządzasz dziennikami na swoim koncie magazynu:

* Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
* Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.

Aby uzyskać przegląd informacji na temat Key Vault, zobacz [co to jest Azure Key Vault?](overview.md). Informacje o tym, gdzie Key Vault są dostępne, można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/key-vault/). Aby uzyskać informacje na temat korzystania z [Azure Monitor Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="interpret-your-key-vault-logs"></a>Interpretowanie dzienników usługi Key Vault

Po włączeniu rejestrowania nowy kontener o nazwie **Insights-Logs-auditevent** jest tworzony automatycznie dla określonego konta magazynu. Tego samego konta magazynu można używać do zbierania dzienników dla wielu magazynów kluczy.

Poszczególne obiekty blob są przechowywane jako tekst w formacie obiektu blob JSON. Przyjrzyjmy się przykładowym wpisowi dziennika. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

W poniższej tabeli wymieniono nazwy pól i opisy:

| Nazwa pola | Opis |
| --- | --- |
| **pierwszym** |Data i godzina w formacie UTC. |
| **Identyfikator** |Azure Resource Manager identyfikator zasobu. W przypadku dzienników Key Vault jest to zawsze identyfikator zasobu Key Vault. |
| **operationName** |Nazwa operacji zgodnie z opisem w następnej tabeli. |
| **operationVersion** |Wersja interfejsu API REST żądana przez klienta. |
| **kategorii** |Typ wyniku. W przypadku dzienników Key Vault `AuditEvent` jest to jedyna dostępna wartość. |
| **Result** |Wynik żądania interfejsu API REST. |
| **resultSignature** |Stan HTTP. |
| **resultDescription** |Dodatkowy opis wyniku, jeśli jest dostępny. |
| **Milisekundach)** |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Nie obejmuje opóźnienia sieci, więc czas zmierzony po stronie klienta może być niezgodny z tym czasem. |
| **callerIpAddress** |Adres IP klienta, który wykonał żądanie. |
| **korelacj** |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| **Identity** |Tożsamość z tokenu, która została przedstawiona w żądaniu interfejsu API REST. Zwykle jest to "użytkownik", "Nazwa główna usługi" lub kombinacja "użytkownik + appId", jak w przypadku żądania, które wynika z Azure PowerShell polecenia cmdlet. |
| **aœciwoœci** |Informacje, które różnią się w zależności od operacji ( **OperationName** ). W większości przypadków to pole zawiera informacje o kliencie (ciąg agenta użytkownika przekazaną przez klienta), dokładny identyfikator URI żądania interfejsu API REST i kod stanu HTTP. Ponadto, gdy obiekt jest zwracany w wyniku żądania (na przykład **Create** lub **VaultGet** ), zawiera również identyfikator URI klucza (AS `id` ), identyfikator URI magazynu lub identyfikator URI wpisu tajnego. |

Wartości pola **OperationName** są w formacie *ObjectVerb* . Przykład:

* Wszystkie operacje magazynu kluczy mają `Vault<action>` Format, taki jak `VaultGet` i `VaultCreate` .
* Wszystkie operacje na kluczach mają `Key<action>` Format, taki jak `KeySign` i `KeyList` .
* Wszystkie operacje tajne mają `Secret<action>` Format, taki jak `SecretGet` i `SecretListVersions` .

W poniższej tabeli wymieniono wartości **OperationName** i odpowiednie polecenia interfejsu API REST:

### <a name="operation-names-table"></a>Tabela nazw operacji

| operationName | Polecenie interfejsu API REST |
| --- | --- |
| **Authentication** |Uwierzytelnianie za pośrednictwem punktu końcowego Azure Active Directory |
| **VaultGet** |[Pobierz informacje o magazynie kluczy](/rest/api/keyvault/vaults) |
| **VaultPut** |[Utwórz lub zaktualizuj magazyn kluczy](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Usuń magazyn kluczy](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Zaktualizuj magazyn kluczy](/rest/api/keyvault/vaults) |
| **VaultList** |[Utwórz listę wszystkich magazynów kluczy w grupie zasobów](/rest/api/keyvault/vaults) |
| **KeyCreate** |[Utwórz klucz](/rest/api/keyvault/createkey) |
| **KeyGet** |[Pobierz informacje o kluczu](/rest/api/keyvault/getkey) |
| **KeyImport** |[Importuj klucz do magazynu](/rest/api/keyvault/vaults) |
| **KeyBackup** |[Tworzenie kopii zapasowej klucza](/rest/api/keyvault/backupkey) |
| **KeyDelete** |[Usuń klucz](/rest/api/keyvault/deletekey) |
| **KeyRestore** |[Przywróć klucz](/rest/api/keyvault/restorekey) |
| **KeySign** |[Podpisz przy użyciu klucza](/rest/api/keyvault/sign) |
| **KeyVerify** |[Weryfikuj za pomocą klucza](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Opakuj klucz](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Odpakuj klucz](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Szyfruj za pomocą klucza](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Odszyfruj za pomocą klucza](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Zaktualizuj klucz](/rest/api/keyvault/updatekey) |
| **KeyList** |[Utwórz listę kluczy w magazynie](/rest/api/keyvault/vaults) |
| **KeyListVersions** |[Utwórz listę wersji klucza](/rest/api/keyvault/getkeyversions) |
| **SecretSet** |[Utwórz klucz tajny](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Pobierz wpis tajny](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Zaktualizuj klucz tajny](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Usuń klucz tajny](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Utwórz listę kluczy tajnych w magazynie](/rest/api/keyvault/vaults) |
| **SecretListVersions** |[Utwórz listę wersji klucza tajnego](/rest/api/keyvault/getsecretversions) |
| **VaultAccessPolicyChangedEventGridNotification** | Opublikowano zdarzenie zmiany zasad dostępu do magazynu |
| **SecretNearExpiryEventGridNotification** |Opublikowano wpis tajny po bliskim wygaśnięciu zdarzenia |
| **SecretExpiredEventGridNotification** |Opublikowano wydarzenie wygasłe dla wpisu tajnego |
| **KeyNearExpiryEventGridNotification** |Opublikowano wydarzenie bliskie wygaśnięcia |
| **KeyExpiredEventGridNotification** |Opublikowano wygasłe zdarzenie klucza |
| **CertificateNearExpiryEventGridNotification** |Opublikowano wydarzenie bliskie wygaśnięcia certyfikatu |
| **CertificateExpiredEventGridNotification** |Opublikowano zdarzenie wygasłe certyfikatu |

## <a name="use-azure-monitor-logs"></a>Korzystanie z dzienników usługi Azure Monitor

Możesz użyć rozwiązania Key Vault w dziennikach Azure Monitor do przeglądania dzienników Key Vault `AuditEvent` . W dziennikach Azure Monitor są używane zapytania dzienników do analizowania danych i uzyskiwania potrzebnych informacji. 

Aby uzyskać więcej informacji, w tym o sposobie konfigurowania tego elementu, zobacz [Azure Key Vault w Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Następne kroki

- [Jak włączyć rejestrowanie Key Vault](howto-logging.md)
- Aby zapoznać się z samouczkiem korzystającym Azure Key Vault w aplikacji sieci Web platformy .NET, zobacz [Korzystanie z Azure Key Vault z aplikacji sieci Web](tutorial-net-create-vault-azure-web-app.md).
- Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](developers-guide.md).
- Aby uzyskać listę poleceń cmdlet Azure PowerShell 1,0 dla Azure Key Vault, zobacz [polecenia cmdlet Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).