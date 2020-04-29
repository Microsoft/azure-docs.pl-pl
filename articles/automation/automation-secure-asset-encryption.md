---
title: Szyfrowanie zabezpieczonych zasobów w usłudze Azure Automation
description: Azure Automation chroni Zabezpieczanie zasobów przy użyciu wielu poziomów szyfrowania. Domyślnie Szyfrowanie odbywa się przy użyciu kluczy zarządzanych przez firmę Microsoft. Klienci mogą konfigurować swoje konta usługi Automation, aby używać kluczy zarządzanych przez klienta do szyfrowania. W tym artykule opisano szczegóły obu trybów szyfrowania oraz sposób przełączania między nimi.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 594bac257c2b9739f1ece276c881348b35d2f704
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604813"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Szyfrowanie zabezpieczonych zasobów w usłudze Azure Automation

Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są chronione w Azure Automation przy użyciu wielu poziomów szyfrowania. Na podstawie klucza najwyższego poziomu używanego do szyfrowania istnieją dwa modele do szyfrowania:
-    Korzystanie z kluczy zarządzanych przez firmę Microsoft
-    Korzystanie z kluczy zarządzanych przez klienta

## <a name="microsoft-managed-keys"></a>Klucze zarządzane przez firmę Microsoft

Domyślnie konto Azure Automation używa kluczy zarządzanych przez firmę Microsoft.

Każdy bezpieczny zasób jest szyfrowany i przechowywany w Azure Automation przy użyciu unikatowego klucza (klucza szyfrowania danych), który jest generowany dla każdego konta usługi Automation. Te klucze są szyfrowane i przechowywane w Azure Automation przy użyciu jeszcze innego unikatowego klucza wygenerowanego dla każdego konta o nazwie klucz szyfrowania konta (AEK). Klucze szyfrowania konta zaszyfrowane i przechowywane w Azure Automation przy użyciu kluczy zarządzanych przez firmę Microsoft. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Klucze zarządzane przez klienta z Key Vault (wersja zapoznawcza)

Możesz zarządzać szyfrowaniem bezpiecznych zasobów dla konta usługi Automation własnymi kluczami. W przypadku określenia klucza zarządzanego przez klienta na poziomie konta usługi Automation ten klucz jest używany do ochrony i kontroli dostępu do klucza szyfrowania konta dla konta usługi Automation. To z kolei służy do szyfrowania i odszyfrowywania wszystkich bezpiecznych zasobów. Klucze zarządzane przez klienta zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony zabezpieczonych zasobów.

Użyj Azure Key Vault, aby przechowywać klucze zarządzane przez klienta. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy.  Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Włącz klucze zarządzane przez klienta dla konta usługi Automation

Po włączeniu szyfrowania z kluczami zarządzanymi przez klienta dla konta usługi Automation Azure Automation zawija klucz szyfrowania konta z kluczem zarządzanym przez klienta w skojarzonym magazynie kluczy. Włączenie kluczy zarządzanych przez klienta nie ma wpływu na wydajność, a konto jest szyfrowane z nowym kluczem natychmiast, bez opóźnień.

Nowe konto usługi Automation zawsze jest szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W momencie tworzenia konta nie można włączyć kluczy zarządzanych przez klienta. Klucze zarządzane przez klienta są przechowywane w Azure Key Vault, a Magazyn kluczy musi być zainicjowany przy użyciu zasad dostępu, które przyznają kluczowe uprawnienia do zarządzanej tożsamości skojarzonej z kontem usługi Automation. Tożsamość zarządzana jest dostępna tylko po utworzeniu konta magazynu.

Po zmodyfikowaniu klucza używanego do Azure Automation bezpiecznego szyfrowania zasobów przez włączenie lub wyłączenie kluczy zarządzanych przez klienta, zaktualizowanie wersji klucza lub określenie innego klucza szyfrowanie klucza szyfrowania konta ulegnie zmianie, ale nie trzeba ponownie szyfrować bezpiecznych zasobów na koncie usługi Azure Automation.

W poniższych trzech sekcjach opisano Mechanics włączania kluczy zarządzanych przez klienta dla konta usługi Automation. 

> [!NOTE] 
> Aby włączyć klucze zarządzane przez klienta, należy wprowadzić Azure Automation wywołań interfejsu API REST przy użyciu interfejsu API w wersji 2020-01-13-Preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Wymagania wstępne dotyczące korzystania z kluczy zarządzanych przez klienta w programie Azure Automation

Przed włączeniem kluczy zarządzanych przez klienta dla konta usługi Automation należy upewnić się, że spełnione są następujące wymagania wstępne:

 - Klucz, który ma być obsługiwany przez klienta, jest przechowywany w Azure Key Vault. 
 - Włącz zarówno właściwości **usuwania nietrwałego** , **jak i nie czyść** w magazynie kluczy. Te funkcje są wymagane, aby umożliwić odzyskiwanie kluczy w przypadku przypadkowego usunięcia.
 - Tylko klucze RSA są obsługiwane z szyfrowaniem Azure Automation. Aby uzyskać więcej informacji na temat kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach Azure Key Vault](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- Konto usługi Automation i Magazyn kluczy mogą znajdować się w różnych subskrypcjach, ale muszą znajdować się w tej samej dzierżawie Azure Active Directory.

### <a name="assign-an-identity-to-the-automation-account"></a>Przypisywanie tożsamości do konta usługi Automation

Aby można było używać kluczy zarządzanych przez klienta z kontem usługi Automation, konto usługi Automation musi być uwierzytelniane w magazynie kluczy przechowującym klucze zarządzane przez klienta. Azure Automation używa tożsamości zarządzanych przypisanych przez system do uwierzytelniania konta przy użyciu Azure Key Vault. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Skonfiguruj tożsamość zarządzaną przypisaną przez system do konta usługi Automation przy użyciu następującego wywołania interfejsu API REST:

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

Tożsamość przypisanego systemu dla konta usługi Automation jest zwracana w odpowiedzi podobnej do następującej:

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

### <a name="configure-the-key-vault-access-policy"></a>Konfigurowanie zasad dostępu Key Vault

Gdy zarządzana tożsamość zostanie przypisana do konta usługi Automation, należy skonfigurować dostęp do magazynu kluczy, w którym są przechowywane klucze zarządzane przez klienta. Azure Automation wymaga funkcji **Get**, **Recover**, **wrapKey**, **UnwrapKey** w kluczach zarządzanych przez klienta.

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
> Pola **tenantId** i **objectid** muszą zostać dostarczone z wartościami Identity **. tenantId** i **Identity. principalId** z odpowiedzi na tożsamość zarządzaną dla konta usługi Automation.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Zmień konfigurację konta usługi Automation, aby używała klucza zarządzanego przez klienta

Na koniec możesz przełączyć konto usługi Automation z kluczy zarządzanych przez firmę Microsoft do kluczy zarządzanych przez klienta przy użyciu następującego wywołania interfejsu API REST:

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

### <a name="rotate-customer-managed-keys"></a>Obróć klucze zarządzane przez klienta

Klucz zarządzany przez klienta można obrócić w Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować konto usługi Automation, aby korzystało z nowego identyfikatora URI klucza.

Obracanie klucza nie wyzwala ponownego szyfrowania bezpiecznych zasobów na koncie usługi Automation. Nie są wymagane żadne dalsze działania.

### <a name="revoke-access-to-customer-managed-keys"></a>Odwołaj dostęp do kluczy zarządzanych przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) lub [interfejs wiersza polecenia Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich bezpiecznych zasobów na koncie usługi Automation, ponieważ klucz szyfrowania jest niedostępny przez Azure Automation.

## <a name="next-steps"></a>Następne kroki

- [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md)
- [Zasoby certyfikatów w usłudze Azure Automation](shared-resources/certificates.md)
- [Zasoby poświadczeń w usłudze Azure Automation](shared-resources/credentials.md)
- [Zasoby zmiennych w usłudze Azure Automation](shared-resources/variables.md)
