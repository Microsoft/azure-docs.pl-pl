---
title: Zarządzanie usługą Azure Blockchain Service przy użyciu interfejsu wiersza polecenia platformy Azure
description: Jak zarządzać zasobami Azure Blockchain Service interfejsem wiersza polecenia platformy Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 63401f5ce5cd35f63915e03b7f0362811d2660ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768057"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Zarządzanie usługą Azure Blockchain Service przy użyciu interfejsu wiersza polecenia platformy Azure

Oprócz interfejsu Azure Portal wiersza polecenia platformy Azure można zarządzać członkami łańcucha bloków i węzłami transakcji dla Azure Blockchain Service.

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić Cloud Shell na osobnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i używać go lokalnie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Zaloguj się.

    Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się przy użyciu polecenia [az login](/cli/azure/reference-index#az_login).

    ```azurecli
    az login
    ```

    Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    Aby pracować z odwołaniami do rozszerzenia dla interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować rozszerzenie.  Rozszerzenia interfejsu wiersza polecenia platformy Azure dają dostęp do poleceń eksperymentalnych i w wersji wstępnej, które nie zostały jeszcze dostarczone jako część podstawowego interfejsu wiersza polecenia.  Aby dowiedzieć się więcej na temat rozszerzeń, w tym aktualizacji i odinstalowywania, zobacz [Używanie rozszerzeń z interfejsem wiersza polecenia platformy Azure](/cli/azure/azure-cli-extensions-overview).

    Zainstaluj [rozszerzenie dla Azure Blockchain Service,](/cli/azure/ext/blockchain/blockchain) uruchamiając następujące polecenie:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Tworzenie członka łańcucha bloków

Przykład [tworzy w organizacji składowej](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) łańcucha bloków Azure Blockchain Service który uruchamia protokół rejestru kworum w nowym konsorcjum.

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
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service zasoby są tworzone. |
| **Nazwa** | Unikatowa nazwa identyfikująca twój Azure Blockchain Service łańcucha bloków. Nazwa jest używana dla publicznego adresu punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`. |
| **Lokalizacji** | Region świadczenia usługi Azure, w którym jest tworzony członek łańcucha bloków. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. Funkcje mogą nie być dostępne w niektórych regionach. |
| **hasło** | Hasło domyślnego węzła transakcji członka. Użyj hasła do uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym publicznym punktem końcowym węzła transakcji węzła łańcucha bloków. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi mieć długość od 12 & 72 znaków, 1 znak małe litery, 1 znak wielkich liter, 1 liczba i 1 znak specjalny, który nie jest znakiem liczbowym(#), percent(%), przecinek(,), gwiazdka(*), cudzysłów(), podwójny cudzysłów("), pojedynczy \` cudzysłów('), kreska(-) i średnik(;)|
| **Protokół** | Protokół łańcucha bloków. Obecnie obsługiwany *jest* protokół kworum. |
| **Konsorcjum** | Nazwa konsorcjum, które ma dołączyć lub utworzyć. Aby uzyskać więcej informacji na temat konsorcjum, zobacz [Azure Blockchain Service consortium](consortium.md). |
| **consortium-management-account-password** | Hasło do konta konsorcjum jest również nazywane hasłem konta członkowskiego. Hasło konta członkowskiego jest używane do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla członka. Do zarządzania konsorcjum należy użyć konta członkowskiego i hasła do konta członka. |
| **Numer jednostki magazynowej** | Typ warstwy. *Standardowa* lub *Podstawowa.* Użyj warstwy *Podstawowa* do tworzenia, testowania i weryfikacji koncepcji. Użyj warstwy *Standardowa* dla wdrożeń klasy produkcyjnej. Należy również użyć warstwy *Standardowa,* jeśli używasz Blockchain Data Manager lub wysyłasz dużą ilość transakcji prywatnych. Zmiana warstwy cenowej między podstawową i standardową po utworzeniu członków nie jest obsługiwana. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Zmienianie haseł członków łańcucha bloków lub reguł zapory

Przykład [aktualizuje hasło członka łańcucha bloków,](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)hasło do zarządzania konsorcjum i regułę zapory.

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
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service zasoby są tworzone. |
| **Nazwa** | Nazwa identyfikująca twój Azure Blockchain Service członkowski. |
| **hasło** | Hasło domyślnego węzła transakcji członka. Użyj hasła do uwierzytelniania podstawowego podczas nawiązywania połączenia z domyślnym publicznym punktem końcowym węzła transakcji członka łańcucha bloków. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi mieć długość od 12 & 72 znaków, 1 małe litery, 1 znak wielkich liter, 1 liczba i 1 znak specjalny, który nie jest znakiem liczbowym(#), percent(%), przecinek(,), star(*), back quote( ), podwójny cudzysłów("), pojedynczy \` cudzysłów('), kreska(-) i średnik(;)|
| **consortium-management-account-password** | Hasło do konta konsorcjum jest również nazywane hasłem konta członkowskiego. Hasło konta członkowskiego jest używane do szyfrowania klucza prywatnego dla konta Ethereum utworzonego dla członka. Do zarządzania konsorcjum używa się konta członkowskiego i hasła konta członkowskiego. |
| **reguły zapory** | Start i end IP address for IP allow list. |

## <a name="create-transaction-node"></a>Tworzenie węzła transakcji

[Utwórz węzeł transakcji wewnątrz](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) istniejącego członka łańcucha bloków. Dodając węzły transakcji, można zwiększyć izolację zabezpieczeń i rozłożyć obciążenie. Na przykład można mieć punkt końcowy węzła transakcji dla różnych aplikacji klienckich.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service zasoby są tworzone. |
| **Lokalizacji** | Region świadczenia usługi Azure dla członka łańcucha bloków. |
| **nazwa-członka** | Nazwa identyfikująca twój Azure Blockchain Service członkowski. |
| **hasło** | Hasło węzła transakcji. Użyj hasła do uwierzytelniania podstawowego podczas nawiązywania połączenia z publicznym punktem końcowym węzła transakcji. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi mieć długość od 12 & 72 znaków, 1 małe litery, 1 znak wielkich liter, 1 liczba i 1 znak specjalny, który nie jest znakiem liczbowym(#), percent(%), przecinek(,), gwiazdka(*), cudzysłów(), podwójny cudzysłów("), pojedynczy \` cudzysłów('), kreska(-) i średnik(;)|
| **Nazwa** | Nazwa węzła transakcji. |

## <a name="change-transaction-node-password"></a>Zmienianie hasła węzła transakcji

Przykład [aktualizuje hasło węzła](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) transakcji.

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service istnieją zasoby. |
| **nazwa-członka** | Nazwa identyfikująca Azure Blockchain Service członkowski. |
| **hasło** | Hasło węzła transakcji. Użyj hasła do uwierzytelniania podstawowego podczas nawiązywania połączenia z publicznym punktem końcowym węzła transakcji. Hasło musi spełniać trzy z następujących czterech wymagań: długość musi mieć długość od 12 & 72 znaków, 1 małe litery, 1 znak wielkich liter, 1 liczba i 1 znak specjalny, który nie jest znakiem liczbowym(#), percent(%), przecinek(,), gwiazdka(*), cudzysłów(), podwójny cudzysłów("), pojedynczy \` cudzysłów('), kreska(-) i średnik(;)|
| **Nazwa** | Nazwa węzła transakcji. |

## <a name="list-api-keys"></a>Lista kluczy interfejsu API

Klucze interfejsu API mogą służyć do uzyskiwania dostępu do węzła podobnie jak nazwa użytkownika i hasło. Istnieją dwa klucze interfejsu API do obsługi rotacji kluczy. Użyj następującego polecenia, aby [wyświetlić listę kluczy interfejsu API.](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key)

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service zasobów. |
| **Nazwa** | Nazwa członka łańcucha Azure Blockchain Service łańcucha bloków |

## <a name="regenerate-api-keys"></a>Ponowne generowanie kluczy interfejsu API

Użyj następującego polecenia, aby [ponownie wygenerować klucze interfejsu API.](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key)

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service zasobów. |
| **Nazwa** | Nazwa Azure Blockchain Service łańcucha bloków. |
| **keyName** | Zastąp \<keyValue\> element kluczem key1, key2 lub both. |

## <a name="delete-a-transaction-node"></a>Usuwanie węzła transakcji

Przykład [usuwa węzeł transakcji członka łańcucha bloków](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service zasobów. |
| **nazwa-członka** | Nazwa węzła Azure Blockchain Service łańcucha bloków, który zawiera również nazwę węzła transakcji do usunięcia. |
| **Nazwa** | Nazwa węzła transakcji do usunięcia. |

## <a name="delete-a-blockchain-member"></a>Usuwanie członka łańcucha bloków

Przykład [powoduje usunięcie członka łańcucha bloków](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parametr | Opis |
|---------|-------------|
| **grupa zasobów** | Nazwa grupy zasobów, w Azure Blockchain Service istnieją zasoby. |
| **Nazwa** | Nazwa Azure Blockchain Service łańcucha bloków do usunięcia. |

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
| **Roli** | Nazwa roli usługi Azure AD. |
| **Cesjonariusza** | Identyfikator użytkownika usługi Azure AD. Na przykład `user@contoso.com` |
| **Zakres** | Zakres przypisania roli. Może być członkiem łańcucha bloków lub węzłem transakcji. |

**Przykład:**

Udzielanie użytkownikowi usługi Azure AD dostępu do węzła dla członka łańcucha **bloków:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Przykład:**

Udzielanie użytkownikowi usługi Azure AD dostępu do węzła transakcji łańcucha **bloków:**

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
| **Roli** | Nazwa roli usługi Azure AD. |
| **assignee-object-id** | Identyfikator grupy lub aplikacji usługi Azure AD. |
| **Zakres** | Zakres przypisania roli. Może być członkiem łańcucha bloków lub węzłem transakcji. |

**Przykład:**

Udzielanie dostępu do węzła dla **roli aplikacji**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Usuwanie dostępu do węzła usługi Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametr | Opis |
|---------|-------------|
| **Roli** | Nazwa roli usługi Azure AD. |
| **Cesjonariusza** | Identyfikator użytkownika usługi Azure AD. Na przykład `user@contoso.com` |
| **Zakres** | Zakres przypisania roli. Może być członkiem łańcucha bloków lub węzłem transakcji. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, [jak skonfigurować Azure Blockchain Service transakcji za pomocą Azure Portal](configure-transaction-nodes.md).
