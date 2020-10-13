---
title: Uwierzytelnianie między usługami Azure Key Vault przy użyciu platformy .NET
description: Użyj biblioteki Microsoft. Azure. Services. AppAuthentication do uwierzytelniania w celu Azure Key Vault przy użyciu platformy .NET.
keywords: poświadczenia lokalne uwierzytelniania magazynu kluczy Azure
author: msmbaldwin
services: key-vault
ms.author: mbaldwin
ms.date: 09/04/2020
ms.topic: how-to
ms.service: key-vault
ms.openlocfilehash: 1a6ec20d860a409bbe7d3114c54e1e46a75968a0
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970116"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Uwierzytelnianie między usługami Azure Key Vault przy użyciu platformy .NET

> [!NOTE]
> **Microsoft. Azure. Services. AppAuthentication** nie jest już zalecane do użycia z nowym zestawem SDK Key Vault. Jest on zastępowany nową **biblioteką klienta usługi Azure Identity** , dostępną dla platform .NET, Java, TypeScript i Python i należy jej używać dla wszystkich nowych rozwiązań programistycznych. Więcej informacji można znaleźć tutaj: [uwierzytelnianie do Key Vault w kodzie](https://docs.microsoft.com/azure/key-vault/general/developers-guide#azure-identity-client-libraries).

Aby uwierzytelnić się w Azure Key Vault, musisz mieć poświadczenia Azure Active Directory (Azure AD), wspólny klucz tajny lub certyfikat.

Zarządzanie takimi poświadczeniami może być trudne. Zachęcamy do podzielenia poświadczeń do aplikacji przez uwzględnienie ich w plikach źródłowych lub konfiguracji. `Microsoft.Azure.Services.AppAuthentication`Biblioteka for .NET upraszcza ten problem. Używa poświadczeń dewelopera do uwierzytelniania podczas lokalnego tworzenia oprogramowania. Gdy rozwiązanie zostanie wdrożone później na platformie Azure, biblioteka automatycznie przełączy się na poświadczenia aplikacji. Korzystanie z poświadczeń dewelopera podczas lokalnego tworzenia jest bezpieczniejsze, ponieważ nie ma potrzeby tworzenia poświadczeń usługi Azure AD ani udostępniania poświadczeń między deweloperami.

`Microsoft.Azure.Services.AppAuthentication`Biblioteka zarządza uwierzytelnianiem automatycznie, co z kolei umożliwia skoncentrowanie się na rozwiązaniu, a nie na Twoich poświadczeniach. Obsługuje ona lokalne Programowanie przy użyciu Microsoft Visual Studio, interfejsu wiersza polecenia platformy Azure lub zintegrowanego uwierzytelniania usługi Azure AD. W przypadku wdrożenia do zasobu platformy Azure, który obsługuje tożsamość zarządzaną, biblioteka automatycznie używa [zarządzanych tożsamości dla zasobów platformy Azure](../../active-directory/msi-overview.md). Nie są wymagane żadne zmiany kodu ani konfiguracji. Biblioteka obsługuje również bezpośrednie użycie [poświadczeń klienta](../../azure-resource-manager/resource-group-authenticate-service-principal.md) usługi Azure AD, gdy zarządzana tożsamość nie jest dostępna lub gdy nie można określić kontekstu zabezpieczeń dewelopera podczas lokalnego tworzenia.

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual studio 2019](https://www.visualstudio.com/downloads/) lub [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Rozszerzenie uwierzytelniania aplikacji dla programu Visual Studio, dostępne jako osobne rozszerzenie programu Visual Studio 2017 Update 5 i powiązane z produktem w Update 6 lub nowszym. W przypadku aktualizacji Update 6 lub nowszej można zweryfikować instalację rozszerzenia uwierzytelnianie aplikacji, wybierając pozycję Narzędzia deweloperskie platformy Azure z poziomu Instalatora programu Visual Studio.

## <a name="using-the-library"></a>Korzystanie z biblioteki

W przypadku aplikacji .NET Najprostszym sposobem pracy z zarządzaną tożsamością jest `Microsoft.Azure.Services.AppAuthentication` pakiet. Jak to zrobić krok po kroku:

1. Wybierz kolejno pozycje **Narzędzia**  >  **Menedżer pakietów NuGet**  >  **Zarządzanie pakietami NuGet dla rozwiązania** , aby dodać odwołania do pakietów NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Dodaj następujący kod:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

Klasa bezpieczna wątkowo `AzureServiceTokenProvider` buforuje token w pamięci i pobiera go z usługi Azure AD tuż przed wygaśnięciem. Oznacza to, że nigdy nie trzeba sprawdzać wygaśnięcia tokenu przed wywołaniem `GetAccessTokenAsync` metody. 

`GetAccessTokenAsync`Metoda wymaga identyfikatora zasobu. Aby dowiedzieć się więcej na temat usług Microsoft Azure Services, zobacz [co to jest tożsamość zarządzana dla zasobów platformy Azure](../../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Lokalne uwierzytelnianie programistyczne

W przypadku lokalnego projektowania istnieją dwa podstawowe scenariusze uwierzytelniania: [uwierzytelnianie w usługach platformy Azure](#authenticating-to-azure-services)i [uwierzytelnianie w usługach niestandardowych](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Uwierzytelnianie w usługach platformy Azure

Maszyny lokalne nie obsługują tożsamości zarządzanych dla zasobów platformy Azure. W związku z tym `Microsoft.Azure.Services.AppAuthentication` Biblioteka używa poświadczeń dewelopera do uruchamiania w lokalnym środowisku programistycznym. Gdy rozwiązanie zostanie wdrożone na platformie Azure, biblioteka używa zarządzanej tożsamości w celu przełączenia się do przepływu przydzielenia poświadczeń klienta OAuth 2,0. Takie podejście oznacza, że można testować ten sam kod lokalnie i zdalnie bez obaw.

W przypadku lokalnego programowania program `AzureServiceTokenProvider` Pobiera tokeny przy użyciu **programu Visual Studio**, **interfejsu wiersza polecenia platformy Azure** lub **zintegrowanego uwierzytelniania usługi Azure AD**. Każda opcja jest podejmowana sekwencyjnie, a Biblioteka używa pierwszej opcji, która się powiedzie. Jeśli żadna opcja nie działa, `AzureServiceTokenProviderException` zostanie zgłoszony wyjątek ze szczegółowymi informacjami.

#### <a name="authenticating-with-visual-studio"></a>Uwierzytelnianie za pomocą programu Visual Studio

Aby uwierzytelnić się za pomocą programu Visual Studio:

1. Zaloguj się do programu Visual Studio i użyj opcji **narzędzi**, &nbsp; > &nbsp; **Options** aby otworzyć **Opcje**.

1. Wybierz pozycję **uwierzytelnianie usługi platformy Azure**, wybierz konto do lokalnego tworzenia i wybierz pozycję **OK**.

W przypadku problemów z używaniem programu Visual Studio, takich jak błędy, które obejmują plik dostawcy tokenu, należy uważnie przejrzeć powyższe kroki.

Może być konieczne ponowne uwierzytelnienie tokenu dewelopera. W tym celu wybierz pozycję **Narzędzia** &nbsp; > &nbsp; **Opcje**, a następnie wybierz **pozycję &nbsp; &nbsp; uwierzytelnianie usługi platformy Azure**. Poszukaj linku **ponowne uwierzytelnianie** w ramach wybranego konta. Wybierz go do uwierzytelnienia.

#### <a name="authenticating-with-azure-cli"></a>Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure

Aby użyć interfejsu wiersza polecenia platformy Azure do lokalnego tworzenia, upewnij się, że korzystasz z [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.12 lub nowszej.

Aby użyć interfejsu wiersza polecenia platformy Azure:

1. Poszukaj interfejsu wiersza polecenia platformy Azure na pasku zadań systemu Windows, aby otworzyć **wiersz poleceń Microsoft Azure**.

1. Zaloguj się do Azure Portal: *AZ login* , aby zalogować się do platformy Azure.

1. Sprawdź dostęp, wprowadzając *AZ Account Get-Access-token--Resource https: \/ /Vault.Azure.NET*. Jeśli wystąpi błąd, sprawdź, czy odpowiednia wersja interfejsu wiersza polecenia platformy Azure jest prawidłowo zainstalowana.

   Jeśli interfejs wiersza polecenia platformy Azure nie jest zainstalowany w katalogu domyślnym, może zostać wyświetlony komunikat o błędzie, który `AzureServiceTokenProvider` nie może znaleźć ścieżki do interfejsu wiersza polecenia platformy Azure. Użyj zmiennej środowiskowej **AzureCLIPath** , aby zdefiniować folder instalacyjny interfejsu wiersza polecenia platformy Azure. `AzureServiceTokenProvider` w razie potrzeby dodaje katalog określony w zmiennej środowiskowej **AzureCLIPath** do zmiennej środowiskowej **Path** .

1. Jeśli logujesz się do interfejsu wiersza polecenia platformy Azure przy użyciu wielu kont lub Twoje konto ma dostęp do wielu subskrypcji, musisz określić subskrypcję, która ma zostać użyta. Wprowadź polecenie *AZ Account Set--subscription <Subscription-id>*.

To polecenie generuje dane wyjściowe tylko w przypadku niepowodzenia. Aby sprawdzić bieżące ustawienia konta, wprowadź polecenie `az account list` .

#### <a name="authenticating-with-azure-ad-authentication"></a>Uwierzytelnianie przy użyciu uwierzytelniania usługi Azure AD

Aby użyć uwierzytelniania usługi Azure AD, sprawdź, czy:

- Twoje lokalne Active Directory synchronizowane z usługą Azure AD. Aby uzyskać więcej informacji, zobacz [co to jest tożsamość hybrydowa z Azure Active Directory?](../../active-directory/connect/active-directory-aadconnect.md).

- Kod jest uruchomiony na komputerze przyłączonym do domeny.

### <a name="authenticating-to-custom-services"></a>Uwierzytelnianie w usługach niestandardowych

Gdy usługa wywołuje usługi platformy Azure, poprzednie kroki działają, ponieważ usługi platformy Azure umożliwiają dostęp zarówno do użytkowników, jak i aplikacji.

Podczas tworzenia usługi, która wywołuje usługę niestandardową, Użyj poświadczeń klienta usługi Azure AD na potrzeby lokalnego uwierzytelniania na potrzeby tworzenia oprogramowania. Dostępne są dwie opcje:

- Zaloguj się do platformy Azure przy użyciu nazwy głównej usługi:

    1. Tworzenie jednostki usługi. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Użyj interfejsu wiersza polecenia platformy Azure, aby zalogować się przy użyciu następującego polecenia:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Ponieważ jednostka usługi może nie mieć dostępu do subskrypcji, użyj `--allow-no-subscriptions` argumentu.

- Użyj zmiennych środowiskowych, aby określić szczegóły jednostki usługi. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji przy użyciu nazwy głównej usługi](#running-the-application-using-a-service-principal).

Po zalogowaniu się do platformy Azure program `AzureServiceTokenProvider` korzysta z jednostki usługi w celu pobrania tokenu na potrzeby lokalnego tworzenia oprogramowania.

Takie podejście ma zastosowanie tylko do lokalnego projektowania. Gdy Twoje rozwiązanie zostanie wdrożone na platformie Azure, biblioteka przełączy się do zarządzanej tożsamości w celu uwierzytelnienia.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Uruchamianie aplikacji przy użyciu tożsamości zarządzanej lub przypisanej przez użytkownika

Gdy uruchamiasz swój kod na Azure App Service lub na maszynie wirtualnej platformy Azure z włączoną tożsamością zarządzaną, biblioteka automatycznie używa zarządzanej tożsamości. Nie są wymagane żadne zmiany w kodzie, ale zarządzana tożsamość musi mieć uprawnienia do *pobierania* dla magazynu kluczy. Tożsamość zarządzaną można udzielić za *GET* pomocą *zasad dostępu*magazynu kluczy.

Alternatywnie można uwierzytelniać się przy użyciu tożsamości przypisanej do użytkownika. Aby uzyskać więcej informacji na temat tożsamości przypisanych przez użytkownika, zobacz [Informacje o zarządzanych tożsamościach dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types). Aby uwierzytelnić się za pomocą tożsamości przypisanej do użytkownika, należy określić identyfikator klienta tożsamości przypisanej do użytkownika w parametrach połączenia. Parametry połączenia są określone w [obsłudze parametrów połączenia](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Uruchamianie aplikacji przy użyciu nazwy głównej usługi

Może być konieczne utworzenie poświadczeń klienta usługi Azure AD w celu uwierzytelnienia. Ta sytuacja może wystąpić w następujących przykładach:

- Kod jest uruchamiany w lokalnym środowisku programistycznym, ale nie w ramach tożsamości dewelopera. Service Fabric, na przykład używa [konta NetworkService](../../service-fabric/service-fabric-application-secret-management.md) do lokalnego tworzenia.

- Kod jest uruchamiany w lokalnym środowisku programistycznym i jest uwierzytelniany w usłudze niestandardowej, dlatego nie można używać tożsamości dewelopera.

- Kod jest uruchamiany w zasobie obliczeniowym platformy Azure, który nie obsługuje jeszcze zarządzanych tożsamości dla zasobów platformy Azure, takich jak Azure Batch.

Istnieją trzy podstawowe metody używania nazwy głównej usługi do uruchamiania aplikacji. Aby użyć dowolnego z nich, należy najpierw utworzyć nazwę główną usługi. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Logowanie do usługi Azure AD przy użyciu certyfikatu w lokalnym magazynie kluczy

1. Utwórz certyfikat główny usługi przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) .

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    To polecenie tworzy plik PEM (klucz prywatny), który jest przechowywany w katalogu macierzystym. Wdróż ten certyfikat w magazynie *LocalMachine* lub *CurrentUser* .

    > [!Important]
    > Polecenie interfejsu wiersza polecenia generuje plik PEM, ale system Windows zapewnia natywną obsługę certyfikatów PFX. Aby zamiast tego wygenerować certyfikat PFX, Użyj poleceń programu PowerShell przedstawionych tutaj: [Utwórz nazwę główną usługi z certyfikatem z](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)podpisem własnym. Te polecenia powodują również automatyczne wdrożenie certyfikatu.

1. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** na następującą wartość:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Zastąp *{AppID}*, *{TenantId}* i *{odcisk palca}* wartościami wygenerowanymi w kroku 1. Zastąp element *{CertificateStore}* atrybutem *LocalMachine*' lub *CurrentUser*na podstawie planu wdrożenia.

1. Uruchom aplikację.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Zaloguj się do usługi Azure AD przy użyciu poświadczeń wspólnego klucza tajnego

1. Utwórz certyfikat jednostki usługi za pomocą hasła przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) z parametrem--SDK-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** na następującą wartość:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Zastąp zmienne _{AppID}_, _{TenantId}_ i _{ClientSecret}_ wartościami wygenerowanymi w kroku 1.

1. Uruchom aplikację.

Po poprawnym skonfigurowaniu wszystkiego nie są wymagane żadne dalsze zmiany w kodzie. `AzureServiceTokenProvider` używa zmiennej środowiskowej i certyfikatu do uwierzytelniania w usłudze Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Logowanie do usługi Azure AD za pomocą certyfikatu w Key Vault

Ta opcja umożliwia przechowywanie certyfikatu klienta jednostki usługi w Key Vault i używanie go do uwierzytelniania jednostki usługi. Tej opcji można użyć w następujących scenariuszach:

- Uwierzytelnianie lokalne, w którym chcesz przeprowadzić uwierzytelnianie przy użyciu jawnej jednostki usługi, i chcę bezpiecznie zachować poświadczenia jednostki usługi w magazynie kluczy. Konto dewelopera musi mieć dostęp do magazynu kluczy.

- Uwierzytelnianie z platformy Azure, w którym chcesz użyć jawnego poświadczenia i chcesz bezpiecznie zachować poświadczenia jednostki usługi w magazynie kluczy. Tej opcji można użyć w przypadku scenariusza obejmującego wiele dzierżawców. Tożsamość zarządzana musi mieć dostęp do magazynu kluczy.

Zarządzana tożsamość lub tożsamość dewelopera musi mieć uprawnienia do pobierania certyfikatu klienta z Key Vault. Biblioteka AppAuthentication używa pobranego certyfikatu jako poświadczenia klienta jednostki usługi.

Aby użyć certyfikatu klienta do uwierzytelniania jednostki usługi:

1. Utwórz certyfikat główny usługi i automatycznie Zapisz go w Key Vault. Użyj interfejsu wiersza polecenia platformy Azure [AZ AD Sp Create-for-RBAC--kluczy \<keyvaultname> --CERT \<certificatename> --Create-CERT--Skip-przypisania](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Identyfikator certyfikatu będzie adresem URL w formacie `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Zastąp `{KeyVaultCertificateSecretIdentifier}` wartość w tym parametrach połączenia identyfikatorem certyfikatu:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Na przykład, Jeśli Twój Magazyn kluczy został wywołany *myKeyVault* i został utworzony certyfikat o nazwie Moje *Certyfikaty*, identyfikator certyfikatu:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Obsługa parametrów połączenia

Domyślnie program `AzureServiceTokenProvider` próbuje użyć następujących metod uwierzytelniania w celu pobrania tokenu:

- [Zarządzana tożsamość zasobów platformy Azure](../..//active-directory/managed-identities-azure-resources/overview.md)
- Uwierzytelnianie programu Visual Studio
- [Uwierzytelnianie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)
- [Zintegrowane uwierzytelnianie systemu Windows](/aspnet/web-api/overview/security/integrated-windows-authentication)

Aby kontrolować proces, należy użyć parametrów połączenia przekazaną do `AzureServiceTokenProvider` konstruktora lub określonych w zmiennej środowiskowej *AzureServicesAuthConnectionString* .  Obsługiwane są następujące opcje:

| Opcja parametrów połączenia | Scenariusz | Komentarze|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Programowanie lokalne | `AzureServiceTokenProvider` używa AzureCli, aby uzyskać token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Programowanie lokalne | `AzureServiceTokenProvider` uzyskuje token przy użyciu programu Visual Studio. |
| `RunAs=CurrentUser` | Programowanie lokalne | Nieobsługiwane w programie .NET Core. `AzureServiceTokenProvider` uzyskuje token przy użyciu zintegrowanego uwierzytelniania usługi Azure AD. |
| `RunAs=App` | [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` uzyskuje token przy użyciu tożsamości zarządzanej. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Tożsamość przypisana przez użytkownika dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) | `AzureServiceTokenProvider` używa tożsamości przypisanej do użytkownika w celu uzyskania tokenu. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Niestandardowe uwierzytelnianie usług | `KeyVaultCertificateSecretIdentifier` jest identyfikatorem tajnym certyfikatu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Jednostka usługi | `AzureServiceTokenProvider` używa certyfikatu w celu pobrania tokenu z usługi Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Jednostka usługi | `AzureServiceTokenProvider` Pobiera token z usługi Azure AD przy użyciu certyfikatu|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Jednostka usługi |`AzureServiceTokenProvider` Pobiera token z usługi Azure AD przy użyciu klucza tajnego. |

## <a name="samples"></a>Samples

Aby wyświetlić `Microsoft.Azure.Services.AppAuthentication` bibliotekę w działaniu, zapoznaj się z poniższymi przykładami kodu.

- [Używanie tożsamości zarządzanej do pobierania klucza tajnego z Azure Key Vault w czasie wykonywania](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Programistycznie Wdróż szablon Azure Resource Manager z maszyny wirtualnej platformy Azure z zarządzaną tożsamością](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Użyj przykładu .NET Core i zarządzanej tożsamości, aby wywoływać usługi platformy Azure z maszyny wirtualnej z systemem Linux na platformie Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Rozwiązywanie problemów z AppAuthentication

### <a name="common-issues-during-local-development"></a>Typowe problemy występujące podczas lokalnego tworzenia oprogramowania

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Interfejs wiersza polecenia platformy Azure nie jest zainstalowany, nie jest zalogowany lub nie masz najnowszej wersji

Uruchom *AZ Account Get-Access-token* , aby sprawdzić, czy w interfejsie wiersza polecenia platformy Azure jest wyświetlany token. Jeśli **nie ma takiego programu**, zainstaluj [najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Może zostać wyświetlony monit o zalogowanie się.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider nie może znaleźć ścieżki dla interfejsu wiersza polecenia platformy Azure

AzureServiceTokenProvider szuka interfejsu wiersza polecenia platformy Azure w domyślnej lokalizacji instalacji. Jeśli nie można znaleźć interfejsu wiersza polecenia platformy Azure, Ustaw zmienną środowiskową **AzureCLIPath** na folder instalacji interfejsu wiersza polecenia platformy Azure. AzureServiceTokenProvider doda zmienną środowiskową do zmiennej środowiskowej PATH.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Logujesz się do interfejsu wiersza polecenia platformy Azure przy użyciu wielu kont, to samo konto ma dostęp do subskrypcji w wielu dzierżawach lub otrzymujesz błąd odmowy dostępu podczas próby wywołania podczas lokalnego tworzenia

Korzystając z interfejsu wiersza polecenia platformy Azure, Ustaw domyślną subskrypcję na taką, która ma konto, którego chcesz użyć. Subskrypcja musi znajdować się w tej samej dzierżawie co zasób, do którego chcesz uzyskać dostęp: **AZ Account Set--Subscription [Identyfikator subskrypcji]**. Jeśli dane wyjściowe nie są widoczne, zakończyły się pomyślnie. Sprawdź, czy odpowiednie konto jest teraz domyślnie przy użyciu polecenia **AZ Account List**.

### <a name="common-issues-across-environments"></a>Typowe problemy w różnych środowiskach

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Nieautoryzowany dostęp, odmowa dostępu, dostęp zabroniony lub podobny błąd

Używany podmiot zabezpieczeń nie ma dostępu do zasobu, do którego próbuje uzyskać dostęp. Przyznaj kontu użytkownika lub użytkownikowi "Współautor" App Service, aby uzyskać dostęp do zasobu. Który z nich zależy od tego, czy używasz przykładu na komputerze lokalnym, czy też wdrożonego na platformie Azure do App Service. Niektóre zasoby, takie jak magazyny kluczy, również mają własne [zasady dostępu](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) , które umożliwiają udzielanie dostępu do podmiotów zabezpieczeń, takich jak użytkownicy, aplikacje i grupy.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Typowe problemy występujące podczas wdrażania programu w celu Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Tożsamość zarządzana nie jest skonfigurowana na App Service

Sprawdź zmienne środowiskowe MSI_ENDPOINT i MSI_SECRET istnieją przy użyciu [konsoli debugowania kudu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Jeśli te zmienne środowiskowe nie istnieją, tożsamość zarządzana nie jest włączona na App Service.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Typowe problemy występujące podczas wdrażania lokalnego przy użyciu usług IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Nie można pobrać tokenów podczas debugowania aplikacji w usługach IIS

Domyślnie AppAuth jest uruchamiany w innym kontekście użytkownika w usługach IIS. Dlatego nie ma dostępu do pobierania tokenów dostępu przy użyciu tożsamości dewelopera. Można skonfigurować usługi IIS do uruchamiania z kontekstem użytkownika, wykonując następujące dwa kroki:
- Skonfiguruj pulę aplikacji dla aplikacji sieci Web do uruchamiania jako bieżące konto użytkownika. Zobacz więcej informacji [tutaj](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Skonfiguruj wartość "setProfileEnvironment" jako "true". Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Przejdź do% windir% \System32\inetsrv\config\applicationHost.config
    - Wyszukaj ciąg "setProfileEnvironment". Jeśli jest ustawiona na wartość "false", Zmień ją na "true". Jeśli nie jest obecny, Dodaj go jako atrybut do elementu processModel ( /configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment ) i ustaw go na wartość "true".

- Dowiedz się więcej o [zarządzanych tożsamościach dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/index.yml).
- Dowiedz się więcej o [scenariuszach uwierzytelniania usługi Azure AD](../../active-directory/develop/active-directory-authentication-scenarios.md).
