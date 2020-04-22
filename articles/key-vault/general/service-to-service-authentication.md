---
title: Uwierzytelnianie usługi do usługi w usłudze Azure Key Vault przy użyciu platformy .NET
description: Użyj biblioteki Microsoft.Azure.Services.AppAuthentication do uwierzytelniania w usłudze Azure Key Vault przy użyciu platformy .NET.
keywords: lokalne poświadczenia uwierzytelniania magazynu kluczy platformy Azure
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: cd630acfd65f0a79c186ba35bc15627bf7ccfdbe
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686189"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Uwierzytelnianie usługi do usługi w usłudze Azure Key Vault przy użyciu platformy .NET

Aby uwierzytelnić się w usłudze Azure Key Vault, potrzebujesz poświadczenia usługi Azure Active Directory (Azure AD), udostępnionego klucza tajnego lub certyfikatu.

Zarządzanie takimi poświadczeniami może być trudne. Kuszące jest wiązanie poświadczeń do aplikacji przez włączenie ich do plików źródłowych lub konfiguracyjnych. Biblioteka `Microsoft.Azure.Services.AppAuthentication` for .NET upraszcza ten problem. Używa poświadczeń dewelopera do uwierzytelniania podczas rozwoju lokalnego. Gdy rozwiązanie zostanie później wdrożone na platformie Azure, biblioteka automatycznie przełącza się na poświadczenia aplikacji. Korzystanie z poświadczeń dewelopera podczas tworzenia lokalnego jest bezpieczniejsze, ponieważ nie trzeba tworzyć poświadczeń usługi Azure AD ani udostępniać poświadczeń między deweloperami.

Biblioteka `Microsoft.Azure.Services.AppAuthentication` zarządza uwierzytelnianie automatycznie, co z kolei pozwala skupić się na rozwiązaniu, a nie poświadczenia. Obsługuje lokalne tworzenie za pomocą programu Microsoft Visual Studio, interfejsu wiersza polecenia platformy Azure lub zintegrowanego uwierzytelniania usługi Azure AD. Po wdrożeniu w zasobie platformy Azure, który obsługuje tożsamość zarządzaną, biblioteka automatycznie używa [zarządzanych tożsamości dla zasobów platformy Azure.](../../active-directory/msi-overview.md) Nie są wymagane żadne zmiany kodu ani konfiguracji. Biblioteka obsługuje również bezpośrednie użycie [poświadczeń klienta](../../azure-resource-manager/resource-group-authenticate-service-principal.md) usługi Azure AD, gdy tożsamość zarządzana nie jest dostępna lub gdy nie można określić kontekstu zabezpieczeń dewelopera podczas tworzenia lokalnego.

## <a name="prerequisites"></a>Wymagania wstępne

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) lub [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Rozszerzenie uwierzytelniania aplikacji dla programu Visual Studio, dostępne jako oddzielne rozszerzenie dla programu Visual Studio 2017 Update 5 i dołączone do produktu w aktualizacji 6 i nowszych. Za pomocą aktualizacji 6 lub nowszej można zweryfikować instalację rozszerzenia uwierzytelniania aplikacji, wybierając narzędzia programu Azure Development z poziomu instalatora programu Visual Studio.

## <a name="using-the-library"></a>Korzystanie z biblioteki

W przypadku aplikacji platformy .NET najprostszym sposobem pracy `Microsoft.Azure.Services.AppAuthentication` z tożsamością zarządzaną jest pakiet. Jak to zrobić krok po kroku:

1. Wybierz **narzędzia** > **NuGet Package Manager** > Manage**Pakiety NuGet dla rozwiązania,** aby dodać odwołania do [microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet pakietów do projektu.

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

Klasa `AzureServiceTokenProvider` buforuje token w pamięci i pobiera go z usługi Azure AD tuż przed wygaśnięciem. Tak, nie trzeba już sprawdzić wygaśnięcia `GetAccessTokenAsync` przed wywołaniem metody. Wystarczy wywołać metodę, gdy chcesz użyć tokenu.

Metoda `GetAccessTokenAsync` wymaga identyfikatora zasobu. Aby dowiedzieć się więcej o usługach platformy Microsoft Azure, zobacz [Jakie są tożsamości zarządzane dla zasobów platformy Azure.](../../active-directory/msi-overview.md)

## <a name="local-development-authentication"></a>Uwierzytelnianie rozwoju lokalnego

W przypadku rozwoju lokalnego istnieją dwa scenariusze uwierzytelniania podstawowego: [uwierzytelnianie w usługach platformy Azure](#authenticating-to-azure-services)i [uwierzytelnianie w usługach niestandardowych.](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Uwierzytelnianie w usługach platformy Azure

Maszyny lokalne nie obsługują zarządzanych tożsamości zasobów platformy Azure. W rezultacie biblioteka `Microsoft.Azure.Services.AppAuthentication` używa poświadczeń dewelopera do uruchomienia w lokalnym środowisku programistycznym. Po wdrożeniu rozwiązania na platformie Azure biblioteka używa tożsamości zarządzanej, aby przełączyć się do przepływu dotacji poświadczeń klienta OAuth 2.0. Takie podejście oznacza, że można przetestować ten sam kod lokalnie i zdalnie bez obaw.

W przypadku `AzureServiceTokenProvider` lokalnego programowania pobiera tokeny przy użyciu **programu Visual Studio**, interfejsu **wiersza polecenia platformy Azure** (CLI) lub **zintegrowanego uwierzytelniania usługi Azure AD.** Każda opcja jest wypróbowywczona sekwencyjnie, a biblioteka używa pierwszej opcji, która powiedzie się. Jeśli żadna `AzureServiceTokenProviderException` opcja nie działa, wyjątek jest zgłaszany ze szczegółowymi informacjami.

#### <a name="authenticating-with-visual-studio"></a>Uwierzytelnianie za pomocą programu Visual Studio

Aby uwierzytelnić się przy użyciu programu Visual Studio:

1. Zaloguj się do programu Visual Studio i użyj**opcji** **narzędzi,**&nbsp;>&nbsp;aby otworzyć **opcje**.

1. Wybierz **pozycję Uwierzytelnianie usługi platformy Azure**, wybierz konto dla rozwoju lokalnego i wybierz przycisk **OK**.

Jeśli napotkasz problemy przy użyciu programu Visual Studio, takie jak błędy, które obejmują plik dostawcy tokenu, należy dokładnie przejrzeć poprzednie kroki.

Może być konieczne ponowne uwierzytelnienie tokenu dewelopera. Aby to zrobić, wybierz pozycję**Opcje** **narzędzi**&nbsp;>&nbsp;, a następnie wybierz pozycję **Uwierzytelnianie usługi&nbsp;&nbsp;platformy Azure**. Poszukaj linku **Ponownie uwierzytelnij** pod wybranym kontem. Wybierz go, aby uwierzytelnić.

#### <a name="authenticating-with-azure-cli"></a>Uwierzytelnianie za pomocą interfejsu wiersza polecenia platformy Azure

Aby użyć interfejsu wiersza polecenia platformy Azure do lokalnego rozwoju, upewnij się, że masz wersję [interfejsu wiersza polecenia platformy Azure w wersji 2.0.12](/cli/azure/install-azure-cli) lub nowszej.

Aby użyć interfejsu wiersza polecenia platformy Azure:

1. Wyszukaj narzędzie platformy Azure CLI na pasku zadań systemu Windows, aby otworzyć **wiersz polecenia platformy Microsoft Azure**.

1. Zaloguj się do witryny Azure portal: *az logowania,* aby zalogować się na platformie Azure.

1. Zweryfikuj dostęp, wprowadzając konto *az get-access-token --resource https:\//vault.azure.net*. Jeśli zostanie wyświetlony błąd, sprawdź, czy prawidłowa wersja interfejsu wiersza polecenia platformy Azure jest poprawnie zainstalowana.

   Jeśli narzędzie cli platformy Azure nie jest zainstalowany w katalogu `AzureServiceTokenProvider` domyślnym, może pojawić się raport o błędzie, który nie można znaleźć ścieżkę dla interfejsu wiersza polecenia platformy Azure. Użyj zmiennej środowiskowej **AzureCLIPath,** aby zdefiniować folder instalacji interfejsu wiersza polecenia platformy Azure. `AzureServiceTokenProvider`dodaje katalog określony w zmiennej środowiskowej **AzureCLIPath** do zmiennej środowiskowej **Path,** gdy jest to konieczne.

1. Jeśli zalogujesz się do interfejsu wiersza polecenia platformy Azure przy użyciu wielu kont lub twoje konto ma dostęp do wielu subskrypcji, musisz określić subskrypcję do użycia. Wprowadź polecenie *az account set --subscription <subscription-id>*.

To polecenie generuje dane wyjściowe tylko w przypadku awarii. Aby zweryfikować bieżące ustawienia `az account list`konta, wprowadź polecenie .

#### <a name="authenticating-with-azure-ad-authentication"></a>Uwierzytelnianie przy użyciu uwierzytelniania usługi Azure AD

Aby użyć uwierzytelniania usługi Azure AD, sprawdź, czy:

- Lokalna usługa Active Directory jest synchronizowana z usługą Azure AD. Aby uzyskać więcej informacji, zobacz [Co to jest tożsamość hybrydowa w usłudze Azure Active Directory?](../../active-directory/connect/active-directory-aadconnect.md).

- Kod jest uruchomiony na komputerze przyłączanym do domeny.

### <a name="authenticating-to-custom-services"></a>Uwierzytelnianie w usługach niestandardowych

Gdy usługa wywołuje usługi platformy Azure, poprzednie kroki działają, ponieważ usługi platformy Azure umożliwiają dostęp zarówno użytkownikom, jak i aplikacjom.

Podczas tworzenia usługi, która wywołuje usługę niestandardową, użyj poświadczeń klienta usługi Azure AD do uwierzytelniania lokalnego rozwoju. Dostępne są dwie opcje:

- Użyj jednostki usługi, aby zalogować się na platformie Azure:

    1. Tworzenie jednostki usługi. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.](/cli/azure/create-an-azure-service-principal-azure-cli)

    1. Użyj interfejsu wiersza polecenia platformy Azure, aby zalogować się za pomocą następującego polecenia:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Ponieważ podmiot usługi może nie mieć dostępu `--allow-no-subscriptions` do subskrypcji, należy użyć argumentu.

- Użyj zmiennych środowiskowych, aby określić szczegóły jednostki usługi. Aby uzyskać więcej informacji, zobacz [Uruchamianie aplikacji przy użyciu jednostki usługi](#running-the-application-using-a-service-principal).

Po zalogowaniu się na `AzureServiceTokenProvider` platformie Azure używa jednostki usługi do pobrania tokenu dla lokalnego rozwoju.

Takie podejście ma zastosowanie tylko do rozwoju lokalnego. Gdy rozwiązanie zostanie wdrożone na platformie Azure, biblioteka przełącza się do tożsamości zarządzanej do uwierzytelniania.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Uruchamianie aplikacji przy użyciu tożsamości zarządzanej lub tożsamości przypisanej przez użytkownika

Po uruchomieniu kodu w usłudze Azure App Service lub maszynie wirtualnej platformy Azure z włączoną tożsamością zarządzaną biblioteka automatycznie używa tożsamości zarządzanej. Żadne zmiany kodu nie są wymagane, ale tożsamość zarządzana musi *mieć* uprawnienia do magazynu kluczy. Możesz nadać tożsamości *zarządzanej* uprawnienia dostępu za pośrednictwem *zasad dostępu*magazynu kluczy .

Alternatywnie można uwierzytelnić przy użyciu tożsamości przypisanej przez użytkownika. Aby uzyskać więcej informacji na temat tożsamości przypisanych przez użytkownika, zobacz [Informacje o tożsamościach zarządzanych dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Aby uwierzytelnić się przy użyciu tożsamości przypisanej przez użytkownika, należy określić identyfikator klienta tożsamości przypisanej przez użytkownika w ciągu połączenia. Parametry połączenia są określone w [polu Obsługa ciągu połączenia](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Uruchamianie aplikacji przy użyciu jednostki usługi

Może być konieczne utworzenie poświadczenia klienta usługi Azure AD do uwierzytelnienia. Taka sytuacja może się zdarzyć w następujących przykładach:

- Kod jest uruchamiany w lokalnym środowisku programistycznym, ale nie w tożsamości dewelopera. Sieć szkieletowa usług, na przykład używa [konta NetworkService](../../service-fabric/service-fabric-application-secret-management.md) dla rozwoju lokalnego.

- Kod jest uruchamiany w lokalnym środowisku programistycznym i uwierzytelniasz się w usłudze niestandardowej, więc nie można użyć tożsamości dewelopera.

- Kod jest uruchomiony na zasobie obliczeniowym platformy Azure, który nie obsługuje jeszcze zarządzanych tożsamości dla zasobów platformy Azure, takich jak usługa Azure Batch.

Istnieją trzy podstawowe metody przy użyciu jednostki usługi do uruchomienia aplikacji. Aby użyć dowolnego z nich, należy najpierw utworzyć jednostkę usługi. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.](/cli/azure/create-an-azure-service-principal-azure-cli)

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Logowanie się do usługi Azure AD za pomocą certyfikatu w lokalnym magazynie kluczy

1. Utwórz certyfikat jednostki usługi przy użyciu polecenia Azure CLI [az ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    To polecenie tworzy plik pem (klucz prywatny), który jest przechowywany w katalogu domowym. Wdrożyć ten certyfikat do magazynu *LocalMachine* lub *CurrentUser.*

    > [!Important]
    > Polecenie CLI generuje plik pem, ale system Windows zapewnia tylko natywną obsługę certyfikatów PFX. Zamiast tego należy wygenerować certyfikat PFX, należy użyć pokazanych tutaj poleceń programu PowerShell: [Utwórz jednostkę usługi z certyfikatem z podpisem własnym.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) Te polecenia automatycznie wdrażają również certyfikat.

1. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** na następującą wartość:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Zastąp *{AppId}*, *{TenantId}* i *{Odcisk palca}* wartościami wygenerowanymi w kroku 1. Zamień *{CertificateStore}* na *LocalMachine*' lub *CurrentUser*, na podstawie planu wdrożenia.

1. Uruchom aplikację.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Logowanie się do usługi Azure AD za pomocą udostępnionego poświadczenia tajnego

1. Utwórz certyfikat jednostki usługi z hasłem przy użyciu polecenia Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) z parametrem --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Ustaw zmienną środowiskową o nazwie **AzureServicesAuthConnectionString** na następującą wartość:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Zastąp _{AppId}_, _{TenantId}_ i _{ClientSecret}_ wartościami wygenerowanymi w kroku 1.

1. Uruchom aplikację.

Gdy wszystko jest poprawnie skonfigurowane, nie są konieczne dalsze zmiany kodu. `AzureServiceTokenProvider`używa zmiennej środowiskowej i certyfikatu do uwierzytelniania w usłudze Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Logowanie się do usługi Azure AD za pomocą certyfikatu w magazynie kluczy

Ta opcja umożliwia przechowywanie certyfikatu klienta jednostki usługi w magazynie kluczy i używanie go do uwierzytelniania jednostkowego usługi. Tej opcji można użyć w następujących scenariuszach:

- Uwierzytelnianie lokalne, gdzie chcesz uwierzytelnić przy użyciu jednostki usługi jawne i chcesz zachować poświadczenie jednostki usługi bezpiecznie w magazynie kluczy. Konto dewelopera musi mieć dostęp do magazynu kluczy.

- Uwierzytelnianie z platformy Azure, gdzie chcesz użyć jawnych poświadczeń i chcesz bezpiecznie zachować poświadczenia jednostki usługi w magazynie kluczy. Tej opcji można użyć w scenariuszu między dzierżawą. Tożsamość zarządzana musi mieć dostęp do magazynu kluczy.

Tożsamość zarządzana lub tożsamość dewelopera muszą mieć uprawnienia do pobierania certyfikatu klienta z usługi Key Vault. Biblioteka AppAuthentication używa pobranego certyfikatu jako poświadczenia klienta jednostki usługi.

Aby użyć certyfikatu klienta do uwierzytelniania jednostkowego usługi:

1. Utwórz certyfikat jednostki usługi i automatycznie przechowuj go w magazynie kluczy. Użyj polecenia Azure CLI [az ad sp create-for-rbac --keyvault \<keyvaultname> --cert \<certificatename> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) command:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Identyfikator certyfikatu będzie adresem URL w formacie`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Zastąp `{KeyVaultCertificateSecretIdentifier}` w tym ciągu połączenia identyfikatorem certyfikatu:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Na przykład, jeśli magazyn kluczy został wywołany *myKeyVault* i utworzono certyfikat o nazwie *myCert,* identyfikator certyfikatu będzie:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Obsługa ciągu połączenia

Domyślnie `AzureServiceTokenProvider` używa wielu metod, aby pobrać token.

Aby kontrolować proces, należy użyć ciągu `AzureServiceTokenProvider` połączenia przekazane do konstruktora lub określone w *AzureServicesAuthConnectionString* zmiennej środowiskowej.

Obsługiwane są następujące opcje:

| Opcja ciągu połączenia | Scenariusz | Komentarze|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Programowanie lokalne | `AzureServiceTokenProvider`Używa AzureCli, aby uzyskać token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Programowanie lokalne | `AzureServiceTokenProvider`używa programu Visual Studio, aby uzyskać token. |
| `RunAs=CurrentUser` | Programowanie lokalne | `AzureServiceTokenProvider`Używa zintegrowanego uwierzytelniania usługi Azure AD, aby uzyskać token. |
| `RunAs=App` | [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`używa tożsamości zarządzanej, aby uzyskać token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Tożsamość przypisana przez użytkownika dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`używa tożsamości przypisanej przez użytkownika, aby uzyskać token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Uwierzytelnianie usług niestandardowych | `KeyVaultCertificateSecretIdentifier`jest tajnym identyfikatorem certyfikatu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Jednostka usługi | `AzureServiceTokenProvider`używa certyfikatu, aby uzyskać token z usługi Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Jednostka usługi | `AzureServiceTokenProvider`używa certyfikatu, aby uzyskać token z usługi Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Jednostka usługi |`AzureServiceTokenProvider`używa klucza tajnego, aby uzyskać token z usługi Azure AD. |

## <a name="samples"></a>Samples

Aby wyświetlić bibliotekę `Microsoft.Azure.Services.AppAuthentication` w akcji, zapoznaj się z poniższymi przykładami kodu.

- [Użyj tożsamości zarządzanej, aby pobrać klucz tajny z usługi Azure Key Vault w czasie wykonywania](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Programowo wdrażaj szablon usługi Azure Resource Manager z maszyny wirtualnej platformy Azure z tożsamością zarządzaną.](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

- [Użyj przykładu .NET Core i tożsamości zarządzanej, aby wywołać usługi platformy Azure z maszyny wirtualnej systemu Azure Linux.](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="appauthentication-troubleshooting"></a>Rozwiązywanie problemów z uwierzytelnianiem aplikacji

### <a name="common-issues-during-local-development"></a>Typowe problemy podczas rozwoju lokalnego

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Narzędzie CLI platformy Azure nie jest zainstalowane, nie jesteś zalogowany lub nie masz najnowszej wersji

Uruchom *token get-access-token konta az,* aby sprawdzić, czy narzędzie interfejsu wiersza polecenia platformy Azure pokazuje token dla Ciebie. Jeśli mówi, że **nie znaleziono takiego programu,** zainstaluj [najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Może zostać wyświetlony monit o zalogowanie się.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>Usługa AzureServiceTokenProvider nie może znaleźć ścieżki dla interfejsu wiersza polecenia platformy Azure

AzureServiceTokenProvider wyszukuje platformę Azure CLI w jego domyślnych lokalizacjach instalacji. Jeśli nie można znaleźć interfejsu wiersza polecenia platformy Azure, ustaw zmienną środowiskową **AzureCLIPath** na folderinstalacyjny interfejsu wiersza polecenia platformy Azure. AzureServiceTokenProvider doda zmienną środowiskową do zmiennej środowiskowej Path.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Jesteś zalogowany do interfejsu wiersza polecenia platformy Azure przy użyciu wielu kont, to samo konto ma dostęp do subskrypcji w wielu dzierżawach lub pojawia się błąd odmowy dostępu podczas próby nawiązywać połączenia podczas tworzenia lokalnego rozwoju

Korzystając z interfejsu wiersza polecenia platformy Azure, ustaw domyślną subskrypcję na taki, który ma konto, którego chcesz użyć. Subskrypcja musi znajdować się w tej samej dzierżawie co zasób, do którego chcesz uzyskać dostęp: **az account set --subscription [subscription-id]**. Jeśli nie dane wyjściowe jest widoczny, to się udało. Sprawdź, czy odpowiednie konto jest teraz domyślne przy użyciu **listy kont az**.

### <a name="common-issues-across-environments"></a>Typowe problemy w różnych środowiskach

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Nieautoryzowany dostęp, odmowa dostępu, zabroniona lub podobny błąd

Używany podmiot zabezpieczeń nie ma dostępu do zasobu, do którego próbuje uzyskać dostęp. Udziel dostępu do zasobu kontu użytkownika lub usługi MSI w usłudze aplikacji "Współautor". Który z nich zależy od tego, czy jest uruchomiony przykład na komputerze lokalnym lub wdrożone na platformie Azure do usługi App Service. Niektóre zasoby, takie jak magazyny kluczy, mają również własne [zasady dostępu,](https://docs.microsoft.com/azure/key-vault/secure-your-key-vault#data-plane-and-access-policies) których używasz, aby udzielić dostępu podmiotom, takim jak użytkownicy, aplikacje i grupy.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Typowe problemy po wdrożeniu w usłudze Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Tożsamość zarządzana nie jest skonfigurowana w usłudze app service

Sprawdź zmienne środowiskowe MSI_ENDPOINT i MSI_SECRET istnieją za pomocą [konsoli debugowania Kudu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Jeśli te zmienne środowiskowe nie istnieją, tożsamość zarządzana nie jest włączona w usłudze App Service.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Typowe problemy podczas wdrażania lokalnie przy łączeniu z usługiami IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Nie można pobrać tokenów podczas debugowania aplikacji w użyczanych programach IIS

Domyślnie AppAuth działa w innym kontekście użytkownika w usługach IIS. Dlatego nie ma dostępu do używania tożsamości dewelopera do pobierania tokenów dostępu. Można skonfigurować programy IIS do uruchamiania w kontekście użytkownika, wykonując następujące dwa kroki:
- Skonfiguruj pulę aplikacji dla aplikacji sieci web, aby działała jako bieżące konto użytkownika. Zobacz więcej informacji [tutaj](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Skonfiguruj "setProfileEnvironment" na "True". Zobacz więcej informacji [tutaj](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Przejdź do %windir%\System32\inetsrv\config\applicationHost.config
    - Wyszukaj hasło "setProfileEnvironment". Jeśli jest ustawiona na "False", zmień ją na "True". Jeśli nie jest obecny, dodaj go jako atrybut do processModel element (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment), i ustawić go na "True".

- Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/index.yml).
- Dowiedz się więcej o [scenariuszach uwierzytelniania usługi Azure AD](../../active-directory/develop/active-directory-authentication-scenarios.md).
