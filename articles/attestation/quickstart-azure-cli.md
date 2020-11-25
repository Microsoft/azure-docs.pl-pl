---
title: Konfigurowanie zaświadczania platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Jak skonfigurować i skonfigurować dostawcę zaświadczania przy użyciu interfejsu wiersza polecenia platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: dee9e7596c0a30301d9e0453ef22a6dfe9541522
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "96020946"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Szybki Start: Konfigurowanie zaświadczania platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Rozpocznij pracę z zaświadczeniem platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure w celu skonfigurowania zaświadczania.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Wprowadzenie

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

1. Uruchom polecenie [AZ zaświadcz Create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) , aby utworzyć dostawcę zaświadczania:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   Parametr **--certs-Input-Path** określa zestaw zaufanych kluczy podpisywania. Jeśli określisz nazwę pliku dla tego parametru, dostawca zaświadczania musi być skonfigurowany tylko przy użyciu zasad w formacie ze znakiem JWT. W przeciwnym razie zasady można skonfigurować w formacie tekstu lub niepodpisanego tokenu JWT. Aby uzyskać informacje na temat tokenu JWT, zobacz [podstawowe pojęcia](basic-concepts.md). Aby uzyskać przykłady certyfikatów, zobacz [przykłady certyfikatu osoby podpisującej zasady zaświadczania](policy-signer-examples.md).

1. Uruchom polecenie [AZ zaświadczania show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) , aby pobrać właściwości dostawcy zaświadczania, takie jak status i AttestURI:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
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
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Zarządzanie zasadami

Aby zarządzać zasadami, użytkownik usługi Azure AD wymaga następujących uprawnień `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Te uprawnienia można przypisać do użytkownika usługi Azure AD za pomocą roli, takiej jak `Owner` (uprawnienia symboli wieloznacznych), `Contributor` (uprawnienia symboli wieloznacznych) lub `Attestation Contributor` (określonych uprawnień tylko do zaświadczania platformy Azure).  

Do odczytania zasad użytkownik usługi Azure AD wymaga następujących uprawnień `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`

To uprawnienie można przypisać do użytkownika usługi Azure AD za pomocą roli, takiej jak `Reader` (uprawnienia symboli wieloznacznych) lub `Attestation Reader` (określonych uprawnień tylko do zaświadczania platformy Azure).

Użyj poleceń opisanych tutaj, aby zapewnić zarządzanie zasadami dla dostawcy zaświadczania, po jednym TEE w danym momencie.

Polecenie [AZ zaświadczania zasad Pokaż](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) zwraca bieżące zasady dla określonego tee:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> Polecenie wyświetla zasady w formacie tekstowym i tokenu JWT.

Obsługiwane są następujące typy TEE:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Użyj polecenia [AZ zaświadczania zasad Set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) , aby ustawić nowe zasady dla określonego tee.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

Zasady zaświadczania w formacie JWT muszą zawierać element Claim o nazwie `AttestationPolicy` . Podpisane zasady muszą być podpisane przy użyciu klucza odpowiadającego dowolnemu z istniejących certyfikatów podpisywania zasad.

Aby uzyskać przykłady zasad, zobacz [przykłady zasad zaświadczania](policy-examples.md).

Polecenie [AZ zaświadczanie zasad resetuje](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) nowe zasady dla określonego tee.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Zarządzanie certyfikatami podpisywania zasad

Użyj następujących poleceń, aby zarządzać certyfikatami podpisywania zasad dla dostawcy zaświadczania:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Certyfikat podpisywania zasad jest podpisanym tokenem JWT o nazwie `maa-policyCertificate` . Wartość tego żądania to JWK, który zawiera zaufany klucz podpisywania do dodania. Token JWT musi być podpisany przy użyciu klucza prywatnego, który odnosi się do któregokolwiek z istniejących certyfikatów podpisywania zasad. Aby uzyskać informacje o tokenach JWT i JWK, zobacz [podstawowe pojęcia](basic-concepts.md).

Wszystkie operacje manipulowania semantyką certyfikatu podpisującego zasady muszą być wykonywane poza interfejsem wiersza polecenia platformy Azure. Tak długo, jak w przypadku interfejsu wiersza polecenia platformy Azure, jest to prosty ciąg.

Aby uzyskać przykłady certyfikatów, zobacz [przykłady certyfikatu osoby podpisującej zasady zaświadczania](policy-signer-examples.md).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Implementowanie zaświadczania za pomocą SGX enklawy przy użyciu przykładów kodu](/samples/browse/?expanded=azure&terms=attestation)
