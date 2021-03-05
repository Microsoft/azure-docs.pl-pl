---
title: Konfigurowanie zaświadczania platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Jak skonfigurować i skonfigurować dostawcę zaświadczania przy użyciu interfejsu wiersza polecenia platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178715"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Szybki Start: Konfigurowanie zaświadczania platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę z [zaświadczeniem platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/ext/attestation/attestation).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="get-started"></a>Rozpoczęcie pracy

1. Zainstaluj to rozszerzenie przy użyciu poniższego polecenia CLI

   ```azurecli
   az extension add --name attestation
   ```
   
1. Sprawdź wersję

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Aby zalogować się do platformy Azure, użyj następującego polecenia:

   ```azurecli
   az login
   ```

1. W razie potrzeby przejdź do subskrypcji na potrzeby zaświadczania platformy Azure:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Zarejestruj dostawcę zasobów Microsoft. zaświadczania w subskrypcji za pomocą polecenia [AZ Provider Register](/cli/azure/provider#az_provider_register) :

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Aby uzyskać więcej informacji o dostawcach zasobów platformy Azure oraz o sposobie ich konfigurowania i zarządzania nimi, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Należy tylko raz zarejestrować dostawcę zasobów dla subskrypcji.

1. Utwórz grupę zasobów dla dostawcy zaświadczania. Inne zasoby platformy Azure można umieścić w tej samej grupie zasobów, łącznie z maszyną wirtualną z wystąpieniem aplikacji klienta. Uruchom polecenie [AZ Group Create](/cli/azure/group#az_group_create) , aby utworzyć grupę zasobów, lub Użyj istniejącej grupy zasobów:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Tworzenie dostawcy zaświadczania i zarządzanie nim

Poniżej przedstawiono polecenia, których można użyć do utworzenia dostawcy zaświadczania i zarządzania nim:

1. Uruchom polecenie [AZ zaświadcz Create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) , aby utworzyć dostawcę zaświadczania bez wymagania dotyczącego podpisywania zasad:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Uruchom polecenie [AZ zaświadczania show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) , aby pobrać właściwości dostawcy zaświadczania, takie jak status i AttestURI:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   To polecenie wyświetla wartości, takie jak następujące dane wyjściowe:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Dostawcę zaświadczania można usunąć za pomocą polecenia [AZ zaświadczanie Delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) :

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Zarządzanie zasadami

Użyj poleceń opisanych tutaj, aby zapewnić zarządzanie zasadami dla dostawcy zaświadczania, jednego typu zaświadczania w danym momencie.

Polecenie [AZ zaświadczania zasad Pokaż](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) zwraca bieżące zasady dla określonego tee:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> Polecenie wyświetla zasady w formacie tekstowym i tokenu JWT.

Obsługiwane są następujące typy TEE:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Użyj polecenia [AZ zaświadczania zasad Set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) , aby ustawić nowe zasady dla określonego typu zaświadczania.

Aby ustawić zasady w formacie tekstowym dla danego rodzaju zaświadczania przy użyciu ścieżki pliku:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Aby ustawić zasady w formacie JWT dla danego rodzaju typu zaświadczania przy użyciu ścieżki pliku:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Następne kroki

- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Implementowanie zaświadczania za pomocą SGX enklawy przy użyciu przykładów kodu](/samples/browse/?expanded=azure&terms=attestation)
