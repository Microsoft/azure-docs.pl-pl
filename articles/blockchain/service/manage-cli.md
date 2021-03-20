---
title: Zarządzanie usługą Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure
description: Jak zarządzać usługą Azure łańcucha bloków za pomocą interfejsu wiersza polecenia platformy Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87170857"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Zarządzanie usługą Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure

Oprócz Azure Portal można użyć interfejsu wiersza polecenia platformy Azure, aby zarządzać członkami łańcucha bloków i węzłami transakcji dla usługi Azure łańcucha bloków.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Zaloguj się.

    Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się przy użyciu polecenia [az login](/cli/azure/reference-index#az-login).

    ```azurecli
    az login
    ```

    Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    Aby pracować z odwołaniami do rozszerzenia dla interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować rozszerzenie.  Rozszerzenia interfejsu wiersza polecenia platformy Azure dają dostęp do poleceń eksperymentalnych i w wersji wstępnej, które nie zostały jeszcze dostarczone jako część podstawowego interfejsu wiersza polecenia.  Aby dowiedzieć się więcej na temat rozszerzeń, w tym aktualizacji i odinstalowywania, zobacz [Używanie rozszerzeń z interfejsem wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

    Zainstaluj [rozszerzenie usługi Azure łańcucha bloków](/cli/azure/ext/blockchain/blockchain) , uruchamiając następujące polecenie:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Utwórz składową łańcucha bloków

Przykład [tworzy element członkowski łańcucha bloków](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) w usłudze Azure łańcucha bloków Service, która uruchamia protokół finansów kworum w nowym konsorcjum.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Unikatowa nazwa identyfikująca członka usługi Azure łańcucha bloków Service łańcucha bloków. Nazwa jest używana dla publicznego adresu punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`. |
| **przeniesienie** | Region świadczenia usługi Azure, w którym jest tworzony element członkowski łańcucha bloków. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. Funkcje mogą być niedostępne w niektórych regionach. |
| **hasło** | Hasło dla domyślnego węzła transakcji elementu członkowskiego. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym punktem końcowym węzła transakcji elementu członkowskiego łańcucha bloków. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów ( \` ), podwójny cudzysłów ("), apostrof (), kreska () i semicolumn (;)|
| **protokol** | Protokół łańcucha bloków. Obecnie jest obsługiwany protokół *kworum* . |
| **ustanawiające** | Nazwa konsorcjum do przyłączenia lub utworzenia. Aby uzyskać więcej informacji na temat konsorcjów, zobacz [Azure łańcucha bloków Service Consortium](consortium.md). |
| **konsorcjum — zarządzanie — hasło** | Hasło konta konsorcjum jest również znane jako hasło do konta elementu członkowskiego. Hasło konta elementu członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla elementu członkowskiego. Do zarządzania konsorcjum używasz konta elementu członkowskiego i hasła konta elementu członkowskiego. |
| **Magazyn** | Typ warstwy. *Standardowa* lub *podstawowa*. Skorzystaj z warstwy *podstawowa* na potrzeby tworzenia, testowania i sprawdzania poprawności koncepcji. Użyj warstwy *standardowa* dla wdrożeń klasy produkcyjnej. W przypadku korzystania z łańcucha bloków Data Manager lub wysyłania dużej liczby transakcji prywatnych należy również użyć warstwy *standardowa* . Zmiana warstwy cenowej między podstawowa i Standardowa po utworzeniu elementu członkowskiego nie jest obsługiwana. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Zmień łańcucha bloków lub reguły zapory

Przykład [aktualizuje hasło elementu członkowskiego łańcucha bloków](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update), hasło zarządzania konsorcjum i regułę zapory.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa identyfikująca członka usługi Azure łańcucha bloków. |
| **hasło** | Hasło dla domyślnego węzła transakcji elementu członkowskiego. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym punktem końcowym węzła transakcji elementu członkowskiego łańcucha bloków. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów ( \` ), podwójny cudzysłów ("), apostrof (), kreska () i semicolumn (;)|
| **konsorcjum — zarządzanie — hasło** | Hasło konta konsorcjum jest również znane jako hasło do konta elementu członkowskiego. Hasło konta elementu członkowskiego służy do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla elementu członkowskiego. Do zarządzania konsorcjum używasz konta elementu członkowskiego i hasła konta elementu członkowskiego. |
| **Zapora — reguły** | Początkowy i końcowy adres IP listy dozwolonych adresów IP. |

## <a name="create-transaction-node"></a>Utwórz węzeł transakcji

[Utwórz węzeł transakcji](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) wewnątrz istniejącego elementu członkowskiego łańcucha bloków. Dodając węzły transakcji, można zwiększyć izolację zabezpieczeń i rozłożyć obciążenie. Można na przykład mieć punkt końcowy węzła transakcji dla różnych aplikacji klienckich.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. |
| **przeniesienie** | Region platformy Azure elementu członkowskiego łańcucha bloków. |
| **Nazwa elementu członkowskiego** | Nazwa identyfikująca członka usługi Azure łańcucha bloków. |
| **hasło** | Hasło dla węzła transakcji. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z publicznym punktem końcowym węzła transakcji. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów ( \` ), podwójny cudzysłów ("), apostrof (), kreska () i semicolumn (;)|
| **Nazwij** | Nazwa węzła transakcji. |

## <a name="change-transaction-node-password"></a>Zmień hasło węzła transakcji

Przykład [aktualizuje hasło węzła transakcji](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) .

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwa elementu członkowskiego** | Nazwa identyfikująca członka usługi Azure łańcucha bloków. |
| **hasło** | Hasło dla węzła transakcji. Użyj hasła uwierzytelniania podstawowego podczas nawiązywania połączenia z publicznym punktem końcowym węzła transakcji. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów ( \` ), podwójny cudzysłów ("), apostrof (), kreska () i semicolumn (;)|
| **Nazwij** | Nazwa węzła transakcji. |

## <a name="list-api-keys"></a>Wyświetl listę kluczy interfejsu API

Klucze interfejsu API mogą służyć do dostępu do węzła, podobnie jak nazwa użytkownika i hasło. Istnieją dwa klucze interfejsu API do obsługi rotacji kluczy. Użyj poniższego polecenia, aby [wyświetlić listę kluczy interfejsu API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key).

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa elementu członkowskiego usługi Azure łańcucha bloków Service łańcucha bloków |

## <a name="regenerate-api-keys"></a>Wygeneruj ponownie klucze interfejsu API

Użyj poniższego polecenia, aby [ponownie wygenerować klucze interfejsu API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key).

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa członka usługi Azure łańcucha bloków Service łańcucha bloków. |
| **keyName** | Zamień \<keyValue\> na wartość Klucz1, klucz2 lub Both. |

## <a name="delete-a-transaction-node"></a>Usuń węzeł transakcji

Przykład [usuwa węzeł transakcji elementu członkowskiego łańcucha bloków](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwa elementu członkowskiego** | Nazwa elementu członkowskiego usługi Azure łańcucha bloków Service łańcucha bloków, który zawiera również nazwę węzła transakcji do usunięcia. |
| **Nazwij** | Nazwa węzła transakcji do usunięcia. |

## <a name="delete-a-blockchain-member"></a>Usuwanie elementu członkowskiego łańcucha bloków

Przykład [usuwa element członkowski łańcucha bloków](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa członka usługi Azure łańcucha bloków Service łańcucha bloków do usunięcia. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Udzielanie dostępu użytkownikowi usługi Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametr | Opis |
|---------|-------------|
| **roli** | Nazwa roli usługi Azure AD. |
| **osoby przydzielonej** | Identyfikator użytkownika usługi Azure AD. Na przykład `user@contoso.com` |
| **Scope** | Zakres przypisania roli. Może być elementem członkowskim łańcucha bloków lub węzłem transakcji. |

**Przykład:**

Udziel dostępu do węzła dla użytkownika usługi Azure AD **do łańcucha bloków:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Przykład:**

Przyznaj dostęp do węzła dla użytkownika usługi Azure AD do **węzła transakcji** łańcucha bloków:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Udzielanie dostępu do węzła dla roli aplikacji lub grupy usługi Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parametr | Opis |
|---------|-------------|
| **roli** | Nazwa roli usługi Azure AD. |
| **przydzielone-ID obiektu** | Identyfikator grupy usługi Azure AD lub identyfikator aplikacji. |
| **Scope** | Zakres przypisania roli. Może być elementem członkowskim łańcucha bloków lub węzłem transakcji. |

**Przykład:**

Przyznaj dostęp do węzła dla **roli aplikacji**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Usuń dostęp do węzła usługi Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametr | Opis |
|---------|-------------|
| **roli** | Nazwa roli usługi Azure AD. |
| **osoby przydzielonej** | Identyfikator użytkownika usługi Azure AD. Na przykład `user@contoso.com` |
| **Scope** | Zakres przypisania roli. Może być elementem członkowskim łańcucha bloków lub węzłem transakcji. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak skonfigurować węzły transakcji usługi Azure łańcucha bloków za pomocą Azure Portal](configure-transaction-nodes.md).
