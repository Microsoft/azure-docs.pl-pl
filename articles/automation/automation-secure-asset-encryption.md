---
title: Szyfrowanie zabezpieczonych zasobów w usłudze Azure Automation
description: Ten artykuł zawiera pojęcia dotyczące konfigurowania konta usługi Automation do korzystania z szyfrowania.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 7d59ca60c7f90c227885927086511bd1f8ac7ca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185844"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Szyfrowanie zabezpieczonych zasobów w usłudze Azure Automation

Zabezpieczanie zasobów w Azure Automation obejmuje poświadczenia, certyfikaty, połączenia i zmienne zaszyfrowane. Te zasoby są chronione w Azure Automation przy użyciu wielu poziomów szyfrowania. Na podstawie klucza najwyższego poziomu używanego do szyfrowania istnieją dwa modele do szyfrowania:

- Korzystanie z kluczy zarządzanych przez firmę Microsoft
- Używanie kluczy, którymi zarządzasz

## <a name="microsoft-managed-keys"></a>Klucze zarządzane przez firmę Microsoft

Domyślnie konto Azure Automation używa kluczy zarządzanych przez firmę Microsoft.

Każdy bezpieczny zasób jest szyfrowany i przechowywany w Azure Automation przy użyciu unikatowego klucza (klucza szyfrowania danych), który jest generowany dla każdego konta usługi Automation. Te klucze są szyfrowane i przechowywane w Azure Automation przy użyciu jeszcze innego unikatowego klucza wygenerowanego dla każdego konta o nazwie klucz szyfrowania konta (AEK). Klucze szyfrowania konta zaszyfrowane i przechowywane w Azure Automation przy użyciu kluczy zarządzanych przez firmę Microsoft. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Klucze zarządzane przy użyciu Key Vault (wersja zapoznawcza)

Możesz zarządzać szyfrowaniem bezpiecznych zasobów dla konta usługi Automation własnymi kluczami. W przypadku określenia klucza zarządzanego przez klienta na poziomie konta usługi Automation ten klucz jest używany do ochrony i kontroli dostępu do klucza szyfrowania konta dla konta usługi Automation. To z kolei służy do szyfrowania i odszyfrowywania wszystkich bezpiecznych zasobów. Klucze zarządzane przez klienta zapewniają większą elastyczność tworzenia, obracania, wyłączania i odwoływania kontroli dostępu. Możesz również przeprowadzać inspekcję kluczy szyfrowania używanych do ochrony zabezpieczonych zasobów.

Użyj Azure Key Vault, aby przechowywać klucze zarządzane przez klienta. Możesz utworzyć własne klucze i zapisać je w magazynie kluczy lub użyć Azure Key Vault interfejsów API do wygenerowania kluczy.  Aby uzyskać więcej informacji na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Korzystanie z kluczy zarządzanych przez klienta dla konta usługi Automation

W przypadku korzystania z szyfrowania z kluczami zarządzanymi przez klienta dla konta usługi Automation Azure Automation zawija klucz szyfrowania konta z kluczem zarządzanym przez klienta w skojarzonym magazynie kluczy. Włączenie kluczy zarządzanych przez klienta nie ma wpływu na wydajność, a konto jest szyfrowane z nowym kluczem natychmiast, bez opóźnień.

Nowe konto usługi Automation zawsze jest szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. W momencie tworzenia konta nie można włączyć kluczy zarządzanych przez klienta. Klucze zarządzane przez klienta są przechowywane w Azure Key Vault, a Magazyn kluczy musi być zainicjowany przy użyciu zasad dostępu, które przyznają kluczowe uprawnienia do zarządzanej tożsamości skojarzonej z kontem usługi Automation. Tożsamość zarządzana jest dostępna tylko po utworzeniu konta magazynu.

Po zmodyfikowaniu klucza używanego do Azure Automation bezpiecznego szyfrowania zasobów przez włączenie lub wyłączenie kluczy zarządzanych przez klienta, zaktualizowanie wersji klucza lub określenie innego klucza szyfrowanie klucza szyfrowania konta ulegnie zmianie, ale nie trzeba ponownie szyfrować bezpiecznych zasobów na koncie usługi Azure Automation.

> [!NOTE] 
> Aby włączyć klucze zarządzane przez klienta, należy wprowadzić Azure Automation wywołań interfejsu API REST przy użyciu interfejsu API w wersji 2020-01-13-Preview

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Wymagania wstępne dotyczące korzystania z kluczy zarządzanych przez klienta w programie Azure Automation

Przed włączeniem kluczy zarządzanych przez klienta dla konta usługi Automation należy upewnić się, że zostały spełnione następujące wymagania wstępne:

 - Klucz, który ma być obsługiwany przez klienta, jest przechowywany w Azure Key Vault. 
 - Włącz zarówno właściwości **usuwania nietrwałego** , **jak i nie czyść** w magazynie kluczy. Te funkcje są wymagane, aby umożliwić odzyskiwanie kluczy w przypadku przypadkowego usunięcia.
 - Tylko klucze RSA są obsługiwane z szyfrowaniem Azure Automation. Aby uzyskać więcej informacji na temat kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md).
- Konto usługi Automation i Magazyn kluczy mogą znajdować się w różnych subskrypcjach, ale muszą znajdować się w tej samej dzierżawie Azure Active Directory.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Przypisanie tożsamości do konta usługi Automation

Aby można było używać kluczy zarządzanych przez klienta z kontem usługi Automation, konto usługi Automation musi być uwierzytelniane w magazynie kluczy przechowującym klucze zarządzane przez klienta. Azure Automation używa tożsamości zarządzanych przypisanych przez system do uwierzytelniania konta przy użyciu Azure Key Vault. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

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

Tożsamość przypisana przez system dla konta usługi Automation jest zwracana w odpowiedzi podobnej do następującej:

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

### <a name="configuration-of-the-key-vault-access-policy"></a>Konfiguracja zasad dostępu Key Vault

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

## <a name="rotation-of-a-customer-managed-key"></a>Rotacja klucza zarządzanego przez klienta

Klucz zarządzany przez klienta można obrócić w Azure Key Vault zgodnie z zasadami zgodności. Gdy klucz jest obrócony, należy zaktualizować konto usługi Automation, aby korzystało z nowego identyfikatora URI klucza.

Obracanie klucza nie wyzwala ponownego szyfrowania bezpiecznych zasobów na koncie usługi Automation. Nie są wymagane żadne dalsze działania.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Odwoływanie dostępu do klucza zarządzanego przez klienta

Aby odwołać dostęp do kluczy zarządzanych przez klienta, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) lub [interfejs wiersza polecenia Azure Key Vault](/cli/azure/keyvault). Odwoływanie dostępu skutecznie blokuje dostęp do wszystkich bezpiecznych zasobów na koncie usługi Automation, ponieważ klucz szyfrowania jest niedostępny przez Azure Automation.

## <a name="next-steps"></a>Następne kroki

- Aby zrozumieć Azure Key Vault, zobacz [co to jest Azure Key Vault?](../key-vault/general/overview.md).
- Aby korzystać z certyfikatów, zobacz [Zarządzanie certyfikatami w Azure Automation](shared-resources/certificates.md).
- Aby obsłużyć poświadczenia, zobacz [Zarządzanie poświadczeniami w Azure Automation](shared-resources/credentials.md).
- Aby używać zmiennych automatyzacji, [Zarządzaj zmiennymi w Azure Automation](shared-resources/variables.md).
- Aby uzyskać pomoc podczas pracy z połączeniami, zobacz [Zarządzanie połączeniami w Azure Automation](automation-connections.md).
