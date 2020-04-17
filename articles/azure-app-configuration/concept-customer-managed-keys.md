---
title: Szyfrowanie danych konfiguracyjnych za pomocą kluczy zarządzanych przez klienta
description: Szyfrowanie danych konfiguracyjnych przy użyciu kluczy zarządzanych przez klienta
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: ace34cf4a72b871ba6646b279007b8ce21c03e9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457437"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Szyfrowanie danych konfiguracji aplikacji za pomocą kluczy zarządzanych przez klienta
Usługa Azure App Configuration [szyfruje poufne informacje w spoczynku.](../security/fundamentals/encryption-atrest.md) Korzystanie z kluczy zarządzanych przez klienta zapewnia lepszą ochronę danych, umożliwiając zarządzanie kluczami szyfrowania.  Gdy używane jest szyfrowanie klucza zarządzanego, wszystkie poufne informacje w konfiguracji aplikacji są szyfrowane za pomocą klucza usługi Azure Key Vault dostarczonego przez użytkownika.  Zapewnia to możliwość obracania klucza szyfrowania na żądanie.  Zapewnia również możliwość odwołania dostępu usługi Azure App Configuration do poufnych informacji, odwołując dostęp do klucza wystąpienia konfiguracji aplikacji.

## <a name="overview"></a>Omówienie 
Usługa Azure App Configuration szyfruje poufne informacje w spoczynku przy użyciu 256-bitowego klucza szyfrowania AES dostarczonego przez firmę Microsoft. Każde wystąpienie konfiguracji aplikacji ma własny klucz szyfrowania zarządzany przez usługę i używany do szyfrowania poufnych informacji. Poufne informacje obejmują wartości znalezione w parach klucz-wartość.  Gdy funkcja klucza zarządzanego przez klienta jest włączona, konfiguracja aplikacji używa tożsamości zarządzanej przypisanej do wystąpienia konfiguracji aplikacji do uwierzytelniania za pomocą usługi Azure Active Directory. Tożsamość zarządzana następnie wywołuje usługę Azure Key Vault i zawija klucz szyfrowania wystąpienia konfiguracji aplikacji. Zawinięty klucz szyfrowania jest następnie przechowywany, a nieopakowany klucz szyfrowania jest buforowany w konfiguracji aplikacji przez jedną godzinę. Konfiguracja aplikacji odświeża nieopakowane wersji klucza szyfrowania wystąpienia konfiguracji aplikacji co godzinę. Zapewnia to dostępność w normalnych warunkach pracy. 

>[!IMPORTANT]
> Jeśli tożsamość przypisana do wystąpienia konfiguracji aplikacji nie jest już autoryzowana do odkręcenia klucza szyfrowania wystąpienia lub jeśli klucz zarządzany zostanie trwale usunięty, nie będzie już można odszyfrować poufnych informacji przechowywanych w wystąpieniu konfiguracji aplikacji. Korzystanie z funkcji [usuwania nietrwałego](../key-vault/general/overview-soft-delete.md) usługi Azure Key Vault zmniejsza ryzyko przypadkowego usunięcia klucza szyfrowania.

Gdy użytkownicy włączą funkcję klucza zarządzanego przez klienta w wystąpieniu konfiguracji aplikacji platformy Azure, kontrolują możliwość dostępu do poufnych informacji w usłudze. Klucz zarządzany służy jako główny klucz szyfrowania. Użytkownik może odwołać dostęp swojego wystąpienia konfiguracji aplikacji do klucza zarządzanego, zmieniając zasady dostępu do magazynu kluczy. Gdy ten dostęp zostanie odwołany, konfiguracja aplikacji utraci możliwość odszyfrowania danych użytkownika w ciągu jednej godziny. W tym momencie wystąpienie konfiguracji aplikacji zabroni wszelkich prób dostępu. Ta sytuacja jest możliwe do odzyskania, przyznając usługę dostęp do klucza zarządzanego po raz kolejny.  W ciągu godziny konfiguracja aplikacji będzie mogła odszyfrować dane użytkownika i działać w normalnych warunkach.

>[!NOTE]
>Wszystkie dane konfiguracji aplikacji platformy Azure są przechowywane przez maksymalnie 24 godziny w izolowanej kopii zapasowej. Obejmuje to nieopakowany klucz szyfrowania. Te dane nie są natychmiast dostępne dla zespołu usługi lub usługi. W przypadku awaryjnego przywracania konfiguracja aplikacji platformy Azure ponownie odwoła się z danych klucza zarządzanego.

## <a name="requirements"></a>Wymagania
Następujące składniki są wymagane do pomyślnego włączenia funkcji klucza zarządzanego przez klienta dla konfiguracji aplikacji platformy Azure:
- Wystąpienie konfiguracji aplikacji platformy Azure w warstwie standardowej
- Usługa Azure Key Vault z włączonymi funkcjami usuwania nietrwałego i ochrony przed przeczyszczeniem
- Klucz RSA lub RSA-HSM w magazynie kluczy
    - Klucz nie może wygasnąć, musi być włączony i musi mieć włączone zarówno zawijanie, jak i rozpakowanie

Po skonfigurowaniu tych zasobów pozostają dwa kroki, aby umożliwić konfiguracji aplikacji platformy Azure używanie klucza Usługi Key Vault:
1. Przypisywanie tożsamości zarządzanej do wystąpienia konfiguracji aplikacji platformy Azure
2. Udziel `GET`tożsamości `WRAP`i `UNWRAP` uprawnień w docelowej polityce dostępu usługi Key Vault.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Włączanie szyfrowania kluczy zarządzanych przez klienta dla wystąpienia konfiguracji aplikacji azure
Aby rozpocząć, musisz poprawnie skonfigurowane wystąpienie konfiguracji aplikacji platformy Azure. Jeśli nie masz jeszcze dostępnego wystąpienia konfiguracji aplikacji, wykonaj jeden z następujących przewodników Szybki start, aby je skonfigurować:
- [Tworzenie aplikacji platformy ASP.NET Core używającej usługi Azure App Configuration](quickstart-aspnet-core-app.md)
- [Tworzenie aplikacji .NET Core za pomocą konfiguracji aplikacji platformy Azure](quickstart-dotnet-core-app.md)
- [tworzenie aplikacji .NET Framework przy użyciu usługi Azure App Configuration](quickstart-dotnet-app.md)
- [Tworzenie aplikacji Java Spring za pomocą konfiguracji aplikacji azure](quickstart-java-spring-app.md)

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, której można użyć do uruchomienia instrukcji wiersza polecenia w tym artykule.  Ma wstępnie zainstalowane typowe narzędzia platformy Azure, w tym zestaw SDK .NET Core. Jeśli jesteś zalogowany do subskrypcji platformy Azure, uruchom usługę [Azure Cloud Shell](https://shell.azure.com) z shell.azure.com.  Więcej informacji o usłudze Azure Cloud Shell można [znaleźć, czytając naszą dokumentację](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Tworzenie i konfigurowanie usługi Azure Key Vault
1. Utwórz usługę Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure.  Należy zauważyć, że oba `vault-name` i `resource-group-name` są dostarczane przez użytkownika i muszą być unikatowe.  Używamy `contoso-vault` i `contoso-resource-group` w tych przykładach.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Włącz ochronę przed usuwaniem i czyszczeniem przeczyszczacza dla magazynu kluczy. Zastąp nazwy magazynu`contoso-vault`kluczy (`contoso-resource-group`) i grupy zasobów ( ) utworzone w kroku 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Utwórz klucz przechowalni kluczy. Podaj `key-name` unikatowy dla tego klucza i`contoso-vault`zastąp nazwy magazynu kluczy ( ) utworzone w kroku 1. Określ, `RSA` czy `RSA-HSM` wolisz, czy szyfrujesz.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    Dane wyjściowe z tego polecenia pokazuje identyfikator klucza ("kid") dla wygenerowanego klucza.  Zanotuj identyfikator klucza, którego chcesz użyć w dalszej części tego ćwiczenia.  Identyfikator klucza ma formularz: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`.  Identyfikator klucza ma trzy ważne składniki:
    1. Identyfikator URI magazynu kluczy: 'https://{mój magazyn kluczy}.vault.azure.net
    1. Nazwa klucza przechowalni kluczy: {Nazwa klucza}
    1. Wersja klucza magazynu kluczy: {Wersja klucza}

1. Utwórz system przypisany tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure, zastępując nazwę wystąpienia konfiguracji aplikacji i grupy zasobów używane w poprzednich krokach. Tożsamość zarządzana będzie używana do uzyskiwania dostępu do klucza zarządzanego. Używamy `contoso-app-config` do zilustrowania nazwy wystąpienia konfiguracji aplikacji:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    Dane wyjściowe tego polecenia obejmują identyfikator główny ("principalId") i identyfikator dzierżawy ("tenandId") tożsamości przypisanej systemowi.  Będzie to używane do udzielania dostępu tożsamości do klucza zarządzanego.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Tożsamość zarządzana wystąpienia konfiguracji aplikacji platformy Azure wymaga dostępu do klucza do sprawdzania poprawności klucza, szyfrowania i odszyfrowywania. Konkretny zestaw akcji, do których potrzebuje `GET` `WRAP`dostępu, `UNWRAP` obejmuje: , i dla kluczy.  Udzielenie dostępu wymaga dublowania tożsamości zarządzanej wystąpienia konfiguracji aplikacji. Ta wartość została uzyskana w poprzednim kroku. Jest to pokazane `contoso-principalId`poniżej jako . Udziel uprawnień do klucza zarządzanego za pomocą wiersza polecenia:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Gdy wystąpienie konfiguracji aplikacji platformy Azure może uzyskać dostęp do klucza zarządzanego, możemy włączyć funkcję klucza zarządzanego przez klienta w usłudze przy użyciu interfejsu wiersza polecenia platformy Azure. Przypomnij następujące właściwości zarejestrowane podczas kroków tworzenia klucza: `key name` `key vault URI`.

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Wystąpienie konfiguracji aplikacji platformy Azure jest teraz skonfigurowane do używania klucza zarządzanego przez klienta przechowywanego w usłudze Azure Key Vault.

## <a name="next-steps"></a>Następne kroki
W tym artykule skonfigurowano wystąpienie konfiguracji aplikacji platformy Azure do używania klucza zarządzanego przez klienta do szyfrowania.  Dowiedz się, jak [zintegrować usługę z tożsamościami zarządzanymi platformy Azure.](howto-integrate-azure-managed-service-identity.md)