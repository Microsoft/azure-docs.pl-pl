---
title: Szyfrowanie danych konfiguracji przy użyciu kluczy zarządzanych przez klienta
description: Szyfrowanie danych konfiguracji przy użyciu kluczy zarządzanych przez klienta
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 8942c93b7346613b8cfdc97d9afe09f1c473fb10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384875"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Szyfrowanie danych konfiguracji aplikacji przy użyciu kluczy zarządzanych przez klienta
Usługa Azure App Configuration [szyfruje poufne informacje w stanie spoczynku](../security/fundamentals/encryption-atrest.md). Użycie kluczy zarządzanych przez klienta zapewnia ulepszoną ochronę danych przez umożliwienie zarządzania kluczami szyfrowania.  Gdy używane jest szyfrowanie klucza zarządzanego, wszystkie informacje poufne w konfiguracji aplikacji są szyfrowane za pomocą klucza Azure Key Vault dostarczonego przez użytkownika.  Dzięki temu można obrócić klucz szyfrowania na żądanie.  Umożliwia także odwoływanie dostępu do informacji poufnych z usługi Azure App Configuration poprzez odwoływanie dostępu do klucza wystąpienia konfiguracji aplikacji.

## <a name="overview"></a>Omówienie 
Usługa Azure App Configuration szyfruje poufne informacje przechowywane przy użyciu 256-bitowego klucza szyfrowania AES dostarczonego przez firmę Microsoft. Każde wystąpienie konfiguracji aplikacji ma własny klucz szyfrowania, który jest zarządzany przez usługę i służy do szyfrowania poufnych informacji. Informacje poufne zawierają wartości znajdujące się w parach klucz-wartość.  W przypadku włączenia funkcji klucza zarządzanego przez klienta konfiguracja aplikacji używa zarządzanej tożsamości przypisanej do wystąpienia konfiguracji aplikacji w celu uwierzytelniania za pomocą Azure Active Directory. Tożsamość zarządzana następnie wywołuje Azure Key Vault i zawija klucz szyfrowania wystąpienia konfiguracji aplikacji. Opakowany klucz szyfrowania jest przechowywany, a nieopakowany klucz szyfrowania jest buforowany w ramach konfiguracji aplikacji przez jedną godzinę. Konfiguracja aplikacji odświeża nieopakowaną wersję klucza szyfrowania wystąpienia konfiguracji aplikacji co godzinę. Zapewnia to dostępność w normalnych warunkach operacyjnych. 

>[!IMPORTANT]
> Jeśli tożsamość przypisana do wystąpienia konfiguracji aplikacji nie jest już autoryzowana do odpakowania klucza szyfrowania wystąpienia lub jeśli klucz zarządzany zostanie trwale usunięty, nie będzie można odszyfrować poufnych informacji przechowywanych w wystąpieniu konfiguracji aplikacji. Użycie funkcji [usuwania nietrwałego](../key-vault/general/soft-delete-overview.md) Azure Key Vault ogranicza szansę przypadkowego usunięcia klucza szyfrowania.

Gdy użytkownicy włączają funkcję klucz zarządzany przez klienta w wystąpieniu usługi Azure App Configuration, kontrolują możliwość uzyskiwania dostępu do poufnych informacji. Klucz zarządzany służy jako główny klucz szyfrowania. Użytkownik może odwołać dostęp swojego wystąpienia konfiguracji aplikacji do swojego klucza zarządzanego przez zmianę ich zasad dostępu magazynu kluczy. Po odwołaniu tego dostępu Konfiguracja aplikacji utraci możliwość odszyfrowania danych użytkownika w ciągu jednej godziny. W tym momencie wystąpienie konfiguracji aplikacji zabroni wszystkich prób dostępu. Tę sytuację można przywrócić, dając ponownie dostęp do klucza zarządzanego.  W ciągu jednej godziny konfiguracja aplikacji będzie mogła odszyfrowywać dane użytkowników i działać w normalnych warunkach.

>[!NOTE]
>Wszystkie dane konfiguracji aplikacji platformy Azure są przechowywane przez maksymalnie 24 godziny w odizolowanej kopii zapasowej. Obejmuje to nieopakowany klucz szyfrowania. Te dane nie są natychmiast dostępne dla zespołu usługi lub usługi. W przypadku przywracania awaryjnego usługa Azure App Configuration spowoduje ponowne odwołanie się do danych klucza zarządzanego.

## <a name="requirements"></a>Wymagania
Następujące składniki są wymagane, aby pomyślnie włączyć funkcję klucz zarządzany przez klienta dla konfiguracji aplikacji platformy Azure:
- Wystąpienie konfiguracji aplikacji platformy Azure w warstwie Standardowa
- Azure Key Vault z włączonymi funkcjami usuwania nietrwałego i przeczyszczania ochrony
- Klucz RSA lub RSA-HSM w Key Vault
    - Klucz nie może być wygasły, musi być włączony i musi mieć włączone jednocześnie funkcje zawijania i rozwinięcia.

Po skonfigurowaniu tych zasobów należy wykonać dwa kroki, aby zezwolić usłudze Azure App Configuration na używanie klucza Key Vault:
1. Przypisywanie tożsamości zarządzanej do wystąpienia konfiguracji aplikacji platformy Azure
2. Przyznaj tożsamość `GET` , `WRAP` i `UNWRAP` uprawnienia w zasadach dostępu Key Vault docelowej.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Włącz szyfrowanie klucza zarządzanego przez klienta dla wystąpienia konfiguracji aplikacji platformy Azure
Aby rozpocząć, musisz prawidłowo skonfigurować wystąpienie konfiguracji aplikacji platformy Azure. Jeśli nie masz jeszcze dostępnego wystąpienia konfiguracji aplikacji, wykonaj jedną z następujących przewodników Szybki Start, aby je skonfigurować:
- [Tworzenie aplikacji platformy ASP.NET Core używającej usługi Azure App Configuration](quickstart-aspnet-core-app.md)
- [Tworzenie aplikacji .NET Core przy użyciu konfiguracji aplikacji platformy Azure](quickstart-dotnet-core-app.md)
- [tworzenie aplikacji .NET Framework przy użyciu usługi Azure App Configuration](quickstart-dotnet-app.md)
- [Tworzenie aplikacji ze sprężyną Java przy użyciu konfiguracji aplikacji platformy Azure](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell to bezpłatna interaktywna powłoka, której można użyć do uruchomienia instrukcji wiersza polecenia w tym artykule.  Ma ona popularne narzędzia platformy Azure preinstalowane, w tym zestaw .NET Core SDK. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

### <a name="create-and-configure-an-azure-key-vault"></a>Tworzenie i Konfigurowanie Azure Key Vault
1. Utwórz Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure.  Należy zauważyć, że oba `vault-name` i `resource-group-name` są podane przez użytkownika i muszą być unikatowe.  Używamy `contoso-vault` i `contoso-resource-group` w tych przykładach.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Włącz nietrwałe usuwanie i przeczyszczanie ochrony dla Key Vault. Zastąp nazwy Key Vault ( `contoso-vault` ) i grupy zasobów ( `contoso-resource-group` ) utworzonych w kroku 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Utwórz klucz Key Vault. Podaj unikatową wartość `key-name` dla tego klucza i Zastąp nazwy Key Vault ( `contoso-vault` ) utworzonych w kroku 1. Określ, czy wolisz, czy `RSA` `RSA-HSM` szyfrowanie.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    Dane wyjściowe tego polecenia przedstawiają identyfikator klucza ("dziecko") dla wygenerowanego klucza.  Zanotuj identyfikator klucza do użycia w dalszej części tego ćwiczenia.  Identyfikator klucza ma postać: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  Identyfikator klucza ma trzy ważne składniki:
    1. Identyfikator URI Key Vault: "https://{mój magazyn kluczy}. magazyn. Azure. NET
    1. Nazwa klucza Key Vault: {Nazwa klucza}
    1. Wersja klucza Key Vault: {wersja klucza}

1. Utwórz tożsamość zarządzaną przypisaną przez system za pomocą interfejsu wiersza polecenia platformy Azure, zastępując nazwę wystąpienia konfiguracji aplikacji i grupę zasobów używaną w poprzednich krokach. Zarządzana tożsamość zostanie użyta w celu uzyskania dostępu do klucza zarządzanego. Używamy `contoso-app-config` do zilustrowania nazwy wystąpienia konfiguracji aplikacji:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    Dane wyjściowe tego polecenia obejmują Identyfikator podmiotu zabezpieczeń ("principalId") i identyfikator dzierżawy ("tenandId") tożsamości przypisanej do systemu.  Identyfikatory te będą używane w celu udzielenia tożsamości dostępu do klucza zarządzanego.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Zarządzana tożsamość wystąpienia konfiguracji aplikacji platformy Azure wymaga dostępu do klucza w celu weryfikacji klucza, szyfrowania i odszyfrowywania. Określony zestaw akcji, do których wymaga dostępu, obejmuje: `GET` , `WRAP` i `UNWRAP` dla kluczy.  Przyznanie dostępu wymaga identyfikatora podmiotu zabezpieczeń zarządzanej tożsamości wystąpienia konfiguracji aplikacji. Ta wartość została uzyskana w poprzednim kroku. Jest on widoczny poniżej `contoso-principalId` . Udziel uprawnienia do klucza zarządzanego przy użyciu wiersza polecenia:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Gdy wystąpienie usługi Azure App Configuration ma dostęp do klucza zarządzanego, można włączyć funkcję klucz zarządzany przez klienta w usłudze przy użyciu interfejsu wiersza polecenia platformy Azure. Odwołaj następujące właściwości zarejestrowane podczas kroków tworzenia klucza: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Wystąpienie konfiguracji aplikacji platformy Azure jest teraz skonfigurowane do używania klucza zarządzanego przez klienta przechowywanego w Azure Key Vault.

## <a name="next-steps"></a>Następne kroki
W tym artykule opisano konfigurowanie wystąpienia konfiguracji aplikacji platformy Azure do używania klucza zarządzanego przez klienta do szyfrowania.  Dowiedz się, jak [zintegrować usługę z tożsamościami zarządzanymi przez platformę Azure](howto-integrate-azure-managed-service-identity.md).
