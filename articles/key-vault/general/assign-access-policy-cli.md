---
title: Przypisywanie zasad dostępu Azure Key Vault (interfejs wiersza polecenia)
description: Jak używać interfejsu wiersza polecenia platformy Azure do przypisywania zasad dostępu Key Vault do nazwy głównej usługi lub aplikacji.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 0c7910ac149c8de43eeac92913a0d314fcc1854e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934581"
---
# <a name="assign-a-key-vault-access-policy"></a>Przypisywanie zasad dostępu Key Vault

Zasady dostępu Key Vault określają, czy dana jednostka usługi, mianowicie aplikacja lub Grupa użytkowników, może wykonywać różne operacje na Key Vault [kluczach tajnych](../secrets/index.yml), [kluczach](../keys/index.yml)i [certyfikatach](../certificates/index.yml). Zasady dostępu można przypisywać przy użyciu [Azure Portal](assign-access-policy-portal.md), interfejsu wiersza polecenia platformy Azure (tego artykułu) lub [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Aby uzyskać więcej informacji na temat tworzenia grup w Azure Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ AD Group Create](/cli/azure/ad/group#az-ad-group-create) i [AZ AD Group member Add](/cli/azure/ad/group/member#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure i logowanie

1. Aby uruchomić polecenie interfejsu wiersza polecenia platformy Azure lokalnie, zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
 
    Aby uruchomić polecenia bezpośrednio w chmurze, użyj [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Tylko lokalny interfejs wiersza polecenia: Zaloguj się do platformy Azure przy użyciu `az login` :

    ```bash
    az login
    ```

    W `az login` razie potrzeby polecenie otwiera okno przeglądarki w celu zebrania poświadczeń.

## <a name="acquire-the-object-id"></a>Uzyskiwanie identyfikatora obiektu

Określ identyfikator obiektu aplikacji, grupy lub użytkownika, do którego chcesz przypisać zasady dostępu:

- Aplikacje i inne jednostki usługi: Użyj polecenia [AZ AD Sp list](/cli/azure/ad/sp#az-ad-sp-list) , aby pobrać jednostki usługi. Sprawdź dane wyjściowe polecenia, aby określić identyfikator obiektu podmiotu zabezpieczeń, do którego chcesz przypisać zasady dostępu.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Grupy: Użyj polecenia [AZ AD Group list](/cli/azure/ad/group#az-ad-group-list) , filtrując wyniki przy użyciu `--display-name` parametru:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Użytkownicy: Użyj polecenia [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) , przekazując adres e-mail użytkownika w `--id` parametrze:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Przypisywanie zasad dostępu
    
Za pomocą polecenia [AZ demagazyn Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) Przypisz odpowiednie uprawnienia:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Zamień `<object-id>` na identyfikator obiektu nazwy głównej usługi.

Wystarczy tylko `--secret-permissions` , `--key-permissions` i `--certificate-permissions` podczas przypisywania uprawnień do tych konkretnych typów. Dozwolone wartości dla `<secret-permissions>` , `<key-permissions>` i `<certificate-permissions>` są określone w dokumentacji [AZ-magazyn Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) .

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczenia Azure Key Vault: Zarządzanie tożsamościami i dostępem](security-overview.md#identity-management)
- [Zabezpiecz swój magazyn kluczy](secure-your-key-vault.md).
- [Przewodnik dewelopera Azure Key Vault](developers-guide.md)