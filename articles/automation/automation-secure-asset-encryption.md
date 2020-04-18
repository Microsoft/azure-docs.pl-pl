---
title: Szyfrowanie zabezpieczonych zasobów w usłudze Azure Automation
description: Usługa Azure Automation chroni bezpieczne zasoby przy użyciu wielu poziomów szyfrowania. Domyślnie szyfrowanie odbywa się przy użyciu kluczy zarządzanych przez firmę Microsoft. Klienci mogą skonfigurować swoje konta automatyzacji do używania kluczy zarządzanych przez klienta do szyfrowania. W tym artykule opisano szczegóły obu trybów szyfrowania i jak można przełączać się między nimi.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 594bac257c2b9739f1ece276c881348b35d2f704
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604813"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Szyfrowanie zabezpieczonych zasobów w usłudze Azure Automation

Bezpieczne zasoby w usłudze Azure Automation obejmują poświadczenia, certyfikaty, połączenia i zaszyfrowane zmienne. Te zasoby są chronione w usłudze Azure Automation przy użyciu wielu poziomów szyfrowania. Na podstawie klucza najwyższego poziomu używanego do szyfrowania istnieją dwa modele szyfrowania:
-    Korzystanie z kluczy zarządzanych przez firmę Microsoft
-    Korzystanie z kluczy zarządzanych przez klienta

## <a name="microsoft-managed-keys"></a>Klucze zarządzane przez firmę Microsoft

Domyślnie konto usługi Azure Automation używa kluczy zarządzanych przez firmę Microsoft.

Każdy bezpieczny zasób jest szyfrowany i przechowywany w usłudze Azure Automation przy użyciu unikatowego klucza (klucz szyfrowania danych), który jest generowany dla każdego konta automatyzacji. Te klucze same są szyfrowane i przechowywane w usłudze Azure Automation przy użyciu innego unikatowego klucza, który jest generowany dla każdego konta o nazwie klucz szyfrowania konta (AEK). Te klucze szyfrowania konta zaszyfrowane i przechowywane w usłudze Azure Automation przy użyciu kluczy zarządzanych przez firmę Microsoft. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Klucze zarządzane przez klienta z magazynem kluczy (wersja zapoznawcza)

Szyfrowanie bezpiecznych zasobów dla konta automatyzacji można zarządzać za pomocą własnych kluczy. Po określeniu klucza zarządzanego przez klienta na poziomie konta automatyzacji, ten klucz jest używany do ochrony i kontrolowania dostępu do klucza szyfrowania konta dla konta automatyzacji. To z kolei służy do szyfrowania i odszyfrowywania wszystkich bezpiecznych zasobów. Klucze zarządzane przez klienta oferują większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Można również przeprowadzić inspekcję kluczy szyfrowania używanych do ochrony bezpiecznych zasobów.

Użyj usługi Azure Key Vault do przechowywania kluczy zarządzanych przez klienta. Można utworzyć własne klucze i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy.  Aby uzyskać więcej informacji na temat usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Włączanie kluczy zarządzanych przez klienta dla konta automatyzacji

Po włączeniu szyfrowania za pomocą kluczy zarządzanych przez klienta dla konta automatyzacji usługa Azure Automation zawija klucz szyfrowania konta kluczem zarządzanym przez klienta w skojarzonym magazynie kluczy. Włączenie kluczy zarządzanych przez klienta nie wpływa na wydajność, a konto jest szyfrowane za pomocą nowego klucza natychmiast, bez żadnych opóźnień.

Nowe konto automatyzacji jest zawsze szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Nie jest możliwe włączenie kluczy zarządzanych przez klienta w momencie utworzenia konta. Klucze zarządzane przez klienta są przechowywane w usłudze Azure Key Vault, a magazyn kluczy musi być aprowizowany za pomocą zasad dostępu, które przyznają uprawnienia klucza do tożsamości zarządzanej skojarzonej z kontem automatyzacji. Tożsamość zarządzana jest dostępna tylko po utworzeniu konta magazynu.

Podczas modyfikowania klucza używanego do szyfrowania zasobów bezpiecznych usługi Azure Automation, włączając lub wyłączając klucze zarządzane przez klienta, aktualizując wersję klucza lub określając inny klucz, zmienia się szyfrowanie klucza szyfrowania konta, ale bezpieczne zasoby na koncie usługi Azure Automation nie muszą być ponownie szyfrowane.

W poniższych trzech sekcjach opisano mechanikę włączania kluczy zarządzanych przez klienta dla konta automatyzacji. 

> [!NOTE] 
> Aby włączyć klucze zarządzane przez klienta, należy wykonać wywołania interfejsu API REST usługi Azure Automation przy użyciu interfejsu API w wersji 2020-01-13-preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Wymagania wstępne dotyczące korzystania z kluczy zarządzanych przez klienta w usłudze Azure Automation

Przed włączeniem kluczy zarządzanych przez klienta dla konta automatyzacji należy upewnić się, że spełnione są następujące wymagania wstępne:

 - Klucz obsługi klienta jest przechowywany w magazynie kluczy azure. 
 - Włącz właściwości **Usuwanie nietrwałe** i **Nie czyścić** w przechowalni kluczy. Funkcje te są wymagane, aby umożliwić odzyskanie kluczy w przypadku przypadkowego usunięcia.
 - Tylko klucze RSA są obsługiwane za pomocą szyfrowania usługi Azure Automation. Aby uzyskać więcej informacji o kluczach, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach usługi Azure Key Vault](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- Konto automatyzacji i magazyn kluczy mogą znajdować się w różnych subskrypcjach, ale muszą znajdować się w tej samej dzierżawie usługi Azure Active Directory.

### <a name="assign-an-identity-to-the-automation-account"></a>Przypisywanie tożsamości do konta automatyzacji

Aby używać kluczy zarządzanych przez klienta z kontem automatyzacji, konto automatyzacji musi uwierzytelnić się w magazynie kluczy przechowując klucze zarządzane przez klienta. Usługa Azure Automation używa tożsamości zarządzanych przypisanych do systemu w celu uwierzytelnienia konta w usłudze Azure Key Vault. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Skonfiguruj system przypisany tożsamości zarządzanej do konta Automatyzacja przy użyciu następującego wywołania interfejsu API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Treść żądania:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Tożsamości przypisanej do systemu dla konta automatyzacji jest zwracany w odpowiedzi podobnej do następujących:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu usługi Key Vault

Po przypisaniu tożsamości zarządzanej do konta automatyzacji można skonfigurować dostęp do magazynu kluczy przechowując klucze zarządzane przez klienta. Usługa Azure Automation wymaga **get**, **recover**, **wrapKey**, **UnwrapKey** na klucze zarządzane przez klienta.

Takie zasady dostępu można ustawić przy użyciu następującego wywołania interfejsu API REST:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Treść żądania:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Pola **identyfikatora i** **identyfikatora obiektu** muszą być dostarczane z wartościami **identity.tenantId** i **identity.principalId** odpowiednio z odpowiedzi na tożsamość zarządzaną dla konta automatyzacji.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Zmienianie konfiguracji konta automatyzacji w celu używania klucza zarządzanego przez klienta

Na koniec można przełączyć konto automatyzacji z kluczy zarządzanych przez firmę Microsoft do kluczy zarządzanych przez klienta, przy użyciu następującego wywołania interfejsu API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Treść żądania:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Przykładowa odpowiedź

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Zarządzanie cyklem życia kluczy zarządzanych przez klienta

### <a name="rotate-customer-managed-keys"></a>Obracanie kluczy zarządzanych przez klienta

Klucz zarządzany przez klienta można obrócić w usłudze Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować konto automatyzacji, aby użyć nowego klucza URI.

Obracanie klucza nie powoduje ponownego szyfrowania bezpiecznych zasobów na koncie automatyzacji. Nie są wymagane żadne dalsze działania.

### <a name="revoke-access-to-customer-managed-keys"></a>Cofanie dostępu do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) lub [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich bezpiecznych zasobów na koncie automatyzacji, ponieważ klucz szyfrowania jest niedostępny dla usługi Azure Automation.

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)
- [Zasoby certyfikatów w usłudze Azure Automation](shared-resources/certificates.md)
- [Zasoby poświadczeń w usłudze Azure Automation](shared-resources/credentials.md)
- [Zasoby zmiennych w usłudze Azure Automation](shared-resources/variables.md)
