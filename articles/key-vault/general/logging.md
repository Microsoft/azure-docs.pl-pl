---
title: Azure Key Vault rejestrowania | Microsoft Docs
description: Dowiedz się, jak monitorować dostęp do magazynów kluczy, włączając rejestrowanie dla magazynu Azure Key Vault, co pozwala zapisywać informacje na podaną przez Ciebie koncie usługi Azure Storage.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 5847fcb2cf553e1fcc744877e52dbbdf1f24d992
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751836"
---
# <a name="azure-key-vault-logging"></a>Funkcja rejestrowania usługi Azure Key Vault

Po utworzeniu co najmniej jednego magazynu kluczy prawdopodobnie będziesz chcieć monitorować sposób i czas dostępu do magazynów kluczy oraz ich użytkowników. Możesz to zrobić, włączając rejestrowanie dla Azure Key Vault, co powoduje zapisanie informacji na podaną przez Ciebie koncie usługi Azure Storage. Aby uzyskać szczegółowe wskazówki dotyczące konfigurowania tej funkcji, zobacz [How to enable Key Vault logging](howto-logging.md)(Jak włączyć rejestrowanie danych).

Dostęp do informacji rejestrowania można uzyskać co najwyżej 10 minut po operacji magazynu kluczy. W większości przypadków czas będzie krótszy.  Zarządzanie dziennikami na Twoim koncie magazynu zależy od Ciebie:

* Użyj standardowych metod kontroli dostępu platformy Azure na koncie magazynu, aby zabezpieczyć dzienniki przez ograniczenie dostępu do nich.
* Usuń dzienniki, których już nie chcesz przechowywać na koncie magazynu.

Aby uzyskać omówienie Key Vault, zobacz [Co to jest Azure Key Vault?](overview.md). Aby uzyskać informacje o tym, Key Vault jest dostępna, zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/key-vault/). Aby uzyskać informacje na [temat używania Azure Monitor dla Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="interpret-your-key-vault-logs"></a>Interpretowanie dzienników usługi Key Vault

Po włączeniu rejestrowania dla określonego konta magazynu zostanie automatycznie utworzony nowy kontener o nazwie **insights-logs-auditevent.** Tego samego konta magazynu można użyć do zbierania dzienników dla wielu magazynów kluczy.

Poszczególne obiekty blob są przechowywane jako tekst w formacie obiektu blob JSON. Przyjrzyjmy się przykładowej pozycji dziennika. 

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

W poniższej tabeli wymieniono nazwy i opisy pól:

| Nazwa pola | Opis |
| --- | --- |
| **Czas** |Data i godzina w czasie UTC. |
| **resourceId** |Azure Resource Manager identyfikatora zasobu. W Key Vault dziennikach jest to zawsze identyfikator Key Vault zasobu. |
| **operationName** |Nazwa operacji zgodnie z opisem w następnej tabeli. |
| **operationVersion** |Wersja interfejsu API REST żądana przez klienta. |
| **Kategorii** |Typ wyniku. W Key Vault jest `AuditEvent` pojedynczą dostępną wartością. |
| **Resulttype** |Wynik żądania interfejsu API REST. |
| **resultSignature** |Stan HTTP. |
| **resultDescription** |Dodatkowy opis wyniku, jeśli jest dostępny. |
| **durationMs** |Czas potrzebny do obsłużenia żądania interfejsu API REST podany w milisekundach. Nie obejmuje opóźnienia sieci, więc czas zmierzony po stronie klienta może być niezgodny z tym czasem. |
| **callerIpAddress** |Adres IP klienta, który złożył żądanie. |
| **Correlationid** |Opcjonalny identyfikator GUID, który klient może przekazać w celu skorelowania dzienników po stronie klienta z dziennikami po stronie usługi (Key Vault). |
| **Tożsamości** |Tożsamość z tokenu, który został przedstawiony w żądaniu interfejsu API REST. Zazwyczaj jest to "użytkownik", "nazwa główna usługi" lub kombinacja "user+appId", jak w przypadku żądania, które wynika z Azure PowerShell cmdlet. |
| **Właściwości** |Informacje, które różnią się w zależności od operacji **(operationName).** W większości przypadków to pole zawiera informacje o kliencie (ciąg agenta użytkownika przekazany przez klienta), dokładny identyfikator URI żądania interfejsu API REST i kod stanu HTTP. Ponadto, gdy obiekt jest zwracany w wyniku żądania (na przykład **KeyCreate** lub **VaultGet),** zawiera również identyfikator URI klucza (jako ), identyfikator URI magazynu lub identyfikator URI klucza `id` tajnego. |

Wartości **pól operationName** są w *formacie ObjectVerb.* Na przykład:

* Wszystkie operacje magazynu kluczy mają `Vault<action>` format, taki jak `VaultGet` i `VaultCreate` .
* Wszystkie operacje klucza mają `Key<action>` format, taki jak `KeySign` i `KeyList` .
* Wszystkie operacje na kluczu tajnym `Secret<action>` mają format, taki `SecretGet` jak i `SecretListVersions` .

W poniższej tabeli wymieniono **wartości operationName** i odpowiednie polecenia interfejsu API REST:

### <a name="operation-names-table"></a>Tabela nazw operacji

# <a name="vault"></a>[Magazyn](#tab/Vault)

| operationName | Polecenie interfejsu API REST |
| --- | --- |
| **Authentication** |Uwierzytelnianie za pośrednictwem Azure Active Directory końcowego |
| **VaultGet** |[Pobierz informacje o magazynie kluczy](/rest/api/keyvault/vaults) |
| **VaultPut** |[Utwórz lub zaktualizuj magazyn kluczy](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Usuń magazyn kluczy](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Zaktualizuj magazyn kluczy](/rest/api/keyvault/vaults) |
| **VaultList** |[Utwórz listę wszystkich magazynów kluczy w grupie zasobów](/rest/api/keyvault/vaults) |
| **VaultPurge** |[Przeczyszczanie usuniętego magazynu](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Odzyskiwanie usuniętego magazynu|
| **VaultGetDeleted** |[Uzyskiwanie usuniętego magazynu](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Lista usuniętych magazynów](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | Opublikowano zdarzenie zmiany zasad dostępu do magazynu |

# <a name="keys"></a>[Klucze](#tab/Keys)

| operationName | Polecenie interfejsu API REST |
| --- | --- |
| **KeyCreate** |[Utwórz klucz](/rest/api/keyvault/createkey) |
| **KeyGet** |[Pobierz informacje o kluczu](/rest/api/keyvault/getkey) |
| **KeyImport** |[Importuj klucz do magazynu](/rest/api/keyvault/vaults) |
| **KeyDelete** |[Usuń klucz](/rest/api/keyvault/deletekey) |
| **KeySign** |[Podpisz przy użyciu klucza](/rest/api/keyvault/sign) |
| **KeyVerify** |[Weryfikuj za pomocą klucza](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Opakuj klucz](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Odpakuj klucz](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Szyfruj za pomocą klucza](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Odszyfruj za pomocą klucza](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Zaktualizuj klucz](/rest/api/keyvault/updatekey) |
| **KeyList** |[Utwórz listę kluczy w magazynie](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[Utwórz listę wersji klucza](/rest/api/keyvault/getkeyversions) |
| **Kluczpurge** |[Przeczyszczanie klucza](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Tworzenie kopii zapasowej klucza](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Przywróć klucz](/rest/api/keyvault/restorekey) |
| **KeyRecover** |[Odzyskiwanie klucza](/rest/api/keyvault/recoverdeletedkey) |
| **Element KeyGetDeleted** |[Uzyskiwanie usuniętego klucza](/rest/api/keyvault/getdeletedkey) |
| **Element KeyListDeleted** |[Lista usuniętych kluczy w magazynie](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |Opublikowano zdarzenie bliskie wygaśnięcia klucza |
| **KeyExpiredEventGridNotification** |Opublikowano zdarzenie wygasłe klucza |

# <a name="secrets"></a>[Wpisy tajne](#tab/Secrets)

| operationName | Polecenie interfejsu API REST |
| --- | --- |
| **SecretSet** |[Utwórz klucz tajny](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Uzyskiwanie tajnego](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Zaktualizuj klucz tajny](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Usuń klucz tajny](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Utwórz listę kluczy tajnych w magazynie](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Utwórz listę wersji klucza tajnego](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[Przeczyszczanie tajnego](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Tworzenie kopii zapasowej tajnego](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Przywracanie tajnego](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Odzyskiwanie tajnego](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Uzyskiwanie usuniętego tajnego](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[Lista usuniętych wpisów tajnych w magazynie](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |Opublikowano zdarzenie tajne bliskie wygaśnięcia |
| **SecretExpiredEventGridNotification** |Opublikowano zdarzenie wygasłe w kluczu tajnym |

# <a name="certificates"></a>[Certyfikaty](#tab/Cerificates)

| operationName | Polecenie interfejsu API REST |
| --- | --- |
| **CertificateGet** |[Uzyskiwanie informacji o certyfikacie](/rest/api/keyvault/getcertificate) |
| **CertificateCreate** |[Tworzenie certyfikatu](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[Importowanie certyfikatu do magazynu](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[Aktualizowanie certyfikatu](/rest/api/keyvault/updatecertificate) |
| **Lista certyfikatów** |[Lista certyfikatów w magazynie](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[Lista wersji certyfikatu](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[Usuwanie certyfikatu](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[Przeczyszczanie certyfikatu](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Tworzenie kopii zapasowej certyfikatu](/rest/api/keyvault/backupcertificate) |
| **Magazyn certyfikatów** |[Przywracanie certyfikatu](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[Odzyskiwanie certyfikatu](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[Uzyskiwanie usuniętego certyfikatu](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[Lista usuniętych certyfikatów w magazynie](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Uzyskiwanie zasad certyfikatów](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[Aktualizowanie zasad certyfikatów](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[Tworzenie zasad certyfikatów](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Uzyskiwanie kontaktów z certyfikatem](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[Ustawianie kontaktów certyfikatów](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Usuwanie kontaktów z certyfikatami](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[Uzyskiwanie wystawcy certyfikatu](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[Ustawianie wystawcy certyfikatu](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[Aktualizowanie wystawcy certyfikatu](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[Usuwanie wystawcy certyfikatu](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[Lista wystawców certyfikatów](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |Rejestrowanie certyfikatu |
| **CertificateRenew** |Odnawianie certyfikatu |
| **CertificatePendingGet** |Pobieranie oczekującego certyfikatu |
| **CertificatePendingMerge** |Oczekiwanie na scalenie certyfikatu |
| **CertificatePendingUpdate** |Oczekiwanie na aktualizację certyfikatu |
| **CertificatePendingDelete** |Usuwanie oczekującego certyfikatu |
| **CertificateNearExpiryEventGridNotification** |Opublikowano zdarzenie bliskie wygaśnięcia certyfikatu |
| **CertificateExpiredEventGridNotification** |Opublikowano zdarzenie wygasłego certyfikatu |

---

## <a name="use-azure-monitor-logs"></a>Korzystanie z dzienników usługi Azure Monitor

Możesz użyć rozwiązania Key Vault w dziennikach Azure Monitor, aby przejrzeć Key Vault `AuditEvent` dzienników. W Azure Monitor dziennikach zapytania dzienników są wykorzystywane do analizowania danych i uzyskania potrzebnych informacji. 

Aby uzyskać więcej informacji, w tym na temat sposobu jej skonfigurowania, [zobacz Azure Key Vault w Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Następne kroki

- [Jak włączyć Key Vault rejestrowania](howto-logging.md)
- [Azure Monitor](../../azure-monitor/index.yml)
- Aby uzyskać samouczek, który używa Azure Key Vault w aplikacji internetowej .NET, zobacz [Azure Key Vault z aplikacji internetowej](tutorial-net-create-vault-azure-web-app.md).
- Odwołania dotyczące programowania znajdują się w [przewodniku dewelopera usługi Azure Key Vault](developers-guide.md).
- Aby uzyskać listę Azure PowerShell cmdlet w Azure Key Vault 1.0, zobacz [Azure Key Vault cmdlet](/powershell/module/az.keyvault/#key_vault).