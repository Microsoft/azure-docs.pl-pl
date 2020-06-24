---
title: Zarządzanie usługą Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure
description: Jak zarządzać usługą Azure łańcucha bloków za pomocą interfejsu wiersza polecenia platformy Azure
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: fc00bedee5ff55033a1d65c6d5d6bfa766f0f01e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208165"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Zarządzanie usługą Azure łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure

Oprócz Azure Portal można użyć interfejsu wiersza polecenia platformy Azure, aby zarządzać członkami łańcucha bloków i węzłami transakcji dla usługi Azure łańcucha bloków.

Upewnij się, że zainstalowano najnowszy [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i zalogowano się na koncie platformy Azure w programie `az login` .

W poniższych przykładach Zastąp przykład `<parameter names>` własnymi wartościami.

## <a name="create-blockchain-member"></a>Utwórz składową łańcucha bloków

Przykład tworzy element członkowski łańcucha bloków w usłudze Azure łańcucha bloków Service, która uruchamia protokół finansów kworum w nowym konsorcjum.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Unikatowa nazwa identyfikująca członka usługi Azure łańcucha bloków Service łańcucha bloków. Nazwa jest używana dla publicznego adresu punktu końcowego. Na przykład `myblockchainmember.blockchain.azure.com`. |
| **przeniesienie** | Region świadczenia usługi Azure, w którym jest tworzony element członkowski łańcucha bloków. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. |
| **hasło** | Hasło konta elementu członkowskiego. Hasło konta elementu członkowskiego służy do uwierzytelniania w publicznym punkcie końcowym elementu członkowskiego łańcucha bloków przy użyciu uwierzytelniania podstawowego. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi należeć do zakresu od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów ( \` ), podwójny cudzysłów ("), apostrof (), kreska () i semicolumn (;)|
| **protokol** | Publiczna wersja zapoznawcza obsługuje kworum. |
| **ustanawiające** | Nazwa konsorcjum do przyłączenia lub utworzenia. |
| **consortiumManagementAccountPassword** | Hasło zarządzania konsorcjum. Hasło służy do przyłączania konsorcjum. |
| **ruleName** | Nazwa reguły listy dozwolonych zakres adresów IP. Opcjonalny parametr reguł zapory.|
| **Wartość** | Początek zakresu adresów IP dla listy dozwolonych. Opcjonalny parametr reguł zapory. |
| **endIpAddress** | Koniec zakresu adresów IP dla listy dozwolonych. Opcjonalny parametr reguł zapory. |
| **skuName** | Typ warstwy. Użyj S0 dla Standard i B0 dla warstwy Podstawowa. |

## <a name="change-blockchain-member-password"></a>Zmień hasło elementu członkowskiego łańcucha bloków

Przykład zmienia hasło elementu członkowskiego łańcucha bloków.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa identyfikująca członka usługi Azure łańcucha bloków. |
| **hasło** | Hasło konta elementu członkowskiego. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów (), \` podwójny cudzysłów ("), apostrof (), kreska () i średnika (;). |

## <a name="create-transaction-node"></a>Utwórz węzeł transakcji

Utwórz węzeł transakcji wewnątrz istniejącego elementu członkowskiego łańcucha bloków. Dodając węzły transakcji, można zwiększyć izolację zabezpieczeń i rozłożyć obciążenie. Można na przykład mieć punkt końcowy węzła transakcji dla różnych aplikacji klienckich.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa elementu członkowskiego usługi Azure łańcucha bloków Service łańcucha bloków, który zawiera również nową nazwę węzła transakcji. |
| **przeniesienie** | Region świadczenia usługi Azure, w którym jest tworzony element członkowski łańcucha bloków. Na przykład `eastus`. Wybierz lokalizację najbliżej użytkowników lub innych aplikacji Azure. |
| **hasło** | Hasło węzła transakcji. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów (), \` podwójny cudzysłów ("), apostrof (), kreska () i średnika (;). |
| **ruleName** | Nazwa reguły listy dozwolonych zakres adresów IP. Opcjonalny parametr reguł zapory. |
| **Wartość** | Początek zakresu adresów IP dla listy dozwolonych. Opcjonalny parametr reguł zapory. |
| **endIpAddress** | Koniec zakresu adresów IP dla listy dozwolonych. Opcjonalny parametr reguł zapory.|

## <a name="change-transaction-node-password"></a>Zmień hasło węzła transakcji

Przykład zmienia hasło węzła transakcji.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa elementu członkowskiego usługi Azure łańcucha bloków Service łańcucha bloków, który zawiera również nową nazwę węzła transakcji. |
| **hasło** | Hasło węzła transakcji. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów (), \` podwójny cudzysłów ("), apostrof (), kreska () i średnika (;). |

## <a name="change-consortium-management-account-password"></a>Zmień hasło konta zarządzania konsorcjum

Konto zarządzania konsorcjum służy do zarządzania członkostwem konsorcjum. Każdy element członkowski jest jednoznacznie identyfikowany przez konto zarządzania konsorcjum i można zmienić hasło tego konta za pomocą poniższego polecenia.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której są tworzone zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa identyfikująca członka usługi Azure łańcucha bloków. |
| **consortiumManagementAccountPassword** | Hasło konta zarządzania konsorcjum. Hasło musi spełniać trzy z czterech następujących wymagań: długość musi wynosić od 12 & 72 znaków, 1 małe litery, 1 wielkie litery, 1 cyfra i 1 znak specjalny, który nie jest znakiem numeru (#), procent (%), przecinek (,), gwiazdka (), cudzysłów (), \` podwójny cudzysłów ("), apostrof (), kreska () i średnika (;). |
  
## <a name="update-firewall-rules"></a>Aktualizowanie reguł zapory

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa członka usługi Azure łańcucha bloków Service łańcucha bloków. |
| **ruleName** | Nazwa reguły listy dozwolonych zakres adresów IP. Opcjonalny parametr reguł zapory.|
| **Wartość** | Początek zakresu adresów IP dla listy dozwolonych. Opcjonalny parametr reguł zapory.|
| **endIpAddress** | Koniec zakresu adresów IP dla listy dozwolonych. Opcjonalny parametr reguł zapory.|

## <a name="list-api-keys"></a>Wyświetl listę kluczy interfejsu API

Klucze interfejsu API mogą służyć do dostępu do węzła, podobnie jak nazwa użytkownika i hasło. Istnieją dwa klucze interfejsu API do obsługi rotacji kluczy. Użyj poniższego polecenia, aby wyświetlić listę kluczy interfejsu API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa elementu członkowskiego usługi Azure łańcucha bloków Service łańcucha bloków, który zawiera również nową nazwę węzła transakcji. |

## <a name="regenerate-api-keys"></a>Wygeneruj ponownie klucze interfejsu API

Użyj poniższego polecenia, aby ponownie wygenerować klucze interfejsu API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa elementu członkowskiego usługi Azure łańcucha bloków Service łańcucha bloków, który zawiera również nową nazwę węzła transakcji. |
| **keyName** | Zamień \<keyValue\> na wartość Klucz1 lub klucz2. |

## <a name="delete-a-transaction-node"></a>Usuń węzeł transakcji

Przykład usuwa węzeł transakcji elementu członkowskiego łańcucha bloków.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa elementu członkowskiego usługi Azure łańcucha bloków Service łańcucha bloków, który zawiera również nazwę węzła transakcji do usunięcia. |

## <a name="delete-a-blockchain-member"></a>Usuwanie elementu członkowskiego łańcucha bloków

Przykład usuwa element członkowski łańcucha bloków.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Opis |
|---------|-------------|
| **Grupa zasobów** | Nazwa grupy zasobów, w której znajdują się zasoby usługi Azure łańcucha bloków. |
| **Nazwij** | Nazwa członka usługi Azure łańcucha bloków Service łańcucha bloków do usunięcia. |

## <a name="azure-active-directory"></a>Usługa Azure Active Directory

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
| **osoby przydzielonej** | Identyfikator użytkownika usługi Azure AD. Na przykład: `user@contoso.com` |
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

Przyznaj dostęp do węzła dla użytkownika usługi Azure AD do **węzła transakcji**łańcucha bloków:

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
| **osoby przydzielonej** | Identyfikator użytkownika usługi Azure AD. Na przykład: `user@contoso.com` |
| **Scope** | Zakres przypisania roli. Może być elementem członkowskim łańcucha bloków lub węzłem transakcji. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak skonfigurować węzły transakcji usługi Azure łańcucha bloków za pomocą Azure Portal](configure-transaction-nodes.md).
