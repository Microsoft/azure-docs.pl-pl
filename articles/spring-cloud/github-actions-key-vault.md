---
title: Uwierzytelnianie chmury Azure wiosennej za pomocą Key Vault w akcjach GitHub
description: Jak używać magazynu kluczy z przepływem pracy ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0ea0db1faf8c452958b8d95c193d45506057777c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878566"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Uwierzytelnianie chmury Azure wiosennej za pomocą Key Vault w akcjach GitHub

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Magazyn kluczy jest bezpiecznym miejscem do przechowywania kluczy. Użytkownicy przedsiębiorstwa muszą przechowywać poświadczenia dla środowisk CI/CD w zakresie, który kontroluje. Klucz pobierania poświadczeń w magazynie kluczy powinien być ograniczony do zakresu zasobów.  Ma dostęp tylko do zakresu magazynu kluczy, a nie całego zakresu platformy Azure. Jest to klucz, który może jedynie otworzyć silne pole nie jest kluczem głównym, który może otwierać wszystkie drzwi w budynku. Jest to sposób, aby uzyskać klucz z innym kluczem, który jest przydatny w przepływie pracy CICD. 

## <a name="generate-credential"></a>Generuj poświadczenia
Aby wygenerować klucz w celu uzyskania dostępu do magazynu kluczy, wykonaj poniższe polecenie na komputerze lokalnym:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Zakres określony przez `--scopes` parametr ogranicza dostęp klucza do zasobu.  Może uzyskać dostęp tylko do silnego pola.

Z wynikami:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Następnie Zapisz wyniki do wpisów **tajnych** usługi GitHub zgodnie z opisem w temacie [Konfigurowanie repozytorium GitHub i uwierzytelnianie przy użyciu platformy Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Dodawanie zasad dostępu dla poświadczeń
Utworzone powyżej poświadczenia mogą uzyskać tylko ogólne informacje dotyczące Key Vault, a nie zawartości przechowywanej przez nią.  Aby uzyskać wpisy tajne przechowywane w Key Vault, należy ustawić zasady dostępu dla poświadczenia.

Przejdź do pulpitu nawigacyjnego **Key Vault** w Azure Portal, kliknij menu **Kontrola dostępu** , a następnie otwórz kartę **przypisania ról** . Wybierz pozycję **aplikacje** dla **typu** i `This resource` **zakresu**.  Powinno zostać wyświetlone poświadczenie utworzone w poprzednim kroku:

 ![Ustawianie zasad dostępu](./media/github-actions/key-vault1.png)

Skopiuj nazwę poświadczenia, na przykład `azure-cli-2020-01-19-04-39-02` . Otwórz menu **zasady dostępu** , a następnie kliknij pozycję **+ Dodaj zasady dostępu** .  Wybierz pozycję `Secret Management` dla **szablonu**, a następnie wybierz pozycję **podmiot zabezpieczeń**. Wklej nazwę poświadczenia w  / polu wejściowym **SELECT** Principal:

 ![Wybierz pozycję](./media/github-actions/key-vault2.png)

 Kliknij przycisk **Dodaj** w oknie dialogowym **Dodawanie zasad dostępu** , a następnie kliknij przycisk **Zapisz**.

## <a name="generate-full-scope-azure-credential"></a>Generowanie poświadczeń platformy Azure z pełnym zakresem
Jest to klucz główny, aby otworzyć wszystkie drzwi w budynku. Procedura jest podobna do poprzedniego kroku, ale w tym miejscu zmienimy zakres na wygenerowanie klucza głównego:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Wyniki:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Skopiuj cały ciąg JSON.  Wróć do pulpitu nawigacyjnego **Key Vault** . Otwórz menu wpisy **tajne** , a następnie kliknij przycisk **Generuj/Importuj** . Wprowadź nazwę wpisu tajnego, na przykład `AZURE-CREDENTIALS-FOR-SPRING` . Wklej ciąg poświadczeń JSON do pola wprowadzania **wartości** . Możesz zauważyć, że pole wprowadzania wartości jest jednowierszowym polem tekstowym, a nie w wielowierszowym obszarze tekstu.  Możesz wkleić pełny ciąg JSON.

 ![Pełne poświadczenie zakresu](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Łączenie poświadczeń w akcjach usługi GitHub
Ustaw poświadczenia używane podczas wykonywania potoku CICD:

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Następne kroki
* [Działania w witrynie GitHub w chmurze](./spring-cloud-howto-github-actions.md)
