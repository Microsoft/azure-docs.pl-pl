---
title: Przypisywanie zasad Azure Key Vault dostępu do sieci (interfejs wiersza polecenia)
description: Jak przy użyciu interfejsu wiersza polecenia platformy Azure przypisać Key Vault dostępu do podmiotu zabezpieczeń lub tożsamości aplikacji.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 96b4daa027871201a201b253721114372e58f377
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751440"
---
# <a name="assign-a-key-vault-access-policy"></a>Przypisywanie zasad Key Vault dostępu

Zasady Key Vault [określają,](../secrets/index.yml)czy dany podmiot zabezpieczeń, czyli użytkownik, aplikacja lub grupa użytkowników, może wykonywać różne operacje na wpisach tajnych Key Vault [,](../keys/index.yml)kluczach i [certyfikatach.](../certificates/index.yml) Zasady dostępu można przypisywać przy użyciu [interfejsu Azure Portal](assign-access-policy-portal.md), interfejsu wiersza polecenia platformy Azure (w tym artykule) [lub Azure PowerShell.](assign-access-policy-powershell.md)

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Aby uzyskać więcej informacji na temat tworzenia grup w Azure Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [az ad group create](/cli/azure/ad/group#az-ad-group-create) i az ad group member [add](/cli/azure/ad/group/member#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure i logowanie

1. Aby lokalnie uruchamiać polecenia interfejsu wiersza polecenia platformy Azure, zainstaluj interfejs [wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
 
    Aby uruchamiać polecenia bezpośrednio w chmurze, użyj [polecenia Azure Cloud Shell](../../cloud-shell/overview.md).

1. Tylko lokalny interfejs wiersza polecenia: zaloguj się do platformy Azure przy użyciu polecenia `az login` :

    ```bash
    az login
    ```

    Polecenie `az login` spowoduje otwarcie okna przeglądarki w celu zebrania poświadczeń w razie potrzeby.

## <a name="acquire-the-object-id"></a>Uzyskanie identyfikatora obiektu

Określ identyfikator obiektu aplikacji, grupy lub użytkownika, do którego chcesz przypisać zasady dostępu:

- Aplikacje i inne jednostki usługi: użyj [polecenia az ad sp list,](/cli/azure/ad/sp#az-ad-sp-list) aby pobrać jednostki usługi. Sprawdź dane wyjściowe polecenia, aby określić identyfikator obiektu podmiotu zabezpieczeń, do którego chcesz przypisać zasady dostępu.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Grupy: użyj [polecenia az ad group list,](/cli/azure/ad/group#az-ad-group-list) filtrując wyniki za pomocą `--display-name` parametru :

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Użytkownicy: użyj [polecenia az ad user show,](/cli/azure/ad/user#az-ad-user-show) przekazując adres e-mail użytkownika w `--id` parametrze :

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Przypisywanie zasad dostępu
    
Użyj polecenia [az keyvault set-policy,](/cli/azure/keyvault#az-keyvault-set-policy) aby przypisać żądane uprawnienia:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Zastąp `<object-id>` identyfikatorem obiektu podmiotu zabezpieczeń.

Podczas przypisywania uprawnień do tych konkretnych typów należy uwzględnić tylko `--secret-permissions` `--key-permissions` wartości , i `--certificate-permissions` . Dopuszczalne wartości dla `<secret-permissions>` , i są podane w `<key-permissions>` `<certificate-permissions>` [dokumentacji az keyvault set-policy.](/cli/azure/keyvault#az-keyvault-set-policy)

## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault zabezpieczeń: Zarządzanie tożsamościami i dostępem](security-overview.md#identity-management)
- [Zabezpiecz magazyn kluczy.](security-overview.md)
- [Azure Key Vault dewelopera](developers-guide.md)