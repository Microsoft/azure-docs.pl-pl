---
title: Azure Key Vault omówienie odzyskiwania | Microsoft Docs
description: Key Vault odzyskiwania zostały zaprojektowane tak, aby zapobiec przypadkowemu lub złośliwemu usunięciu magazynu kluczy oraz wpisów tajnych, kluczy i certyfikatów przechowywanych wewnątrz magazynu kluczy.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: c3ffbba9546ada54a42c3f2c2aa5d98da599b353
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749739"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>Azure Key Vault odzyskiwania za pomocą usuwania nie softowego i ochrony przed przeczyszczaniem

W tym artykule omyliśmy dwie funkcje odzyskiwania Azure Key Vault, usuwanie nie softowe i ochronę przed przeczyszczaniem. Ten dokument zawiera omówienie tych funkcji i pokazuje, jak zarządzać nimi za pośrednictwem Azure Portal, interfejsu wiersza polecenia platformy Azure i Azure PowerShell.

Aby uzyskać więcej informacji o Key Vault, zobacz
- [Omówienie usługi Key Vault](overview.md)
- [Azure Key Vault kluczy, wpisów tajnych i certyfikatów — omówienie](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/dotnet)
* [Moduł programu PowerShell](/powershell/azure/install-az-ps).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* Interfejs Key Vault — można go utworzyć przy użyciu interfejsu [wiersza Azure Portal](../general/quick-create-portal.md) [platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md)
* Użytkownik będzie potrzebować następujących uprawnień (na poziomie subskrypcji), aby wykonywać operacje na nieukreślonym magazynie:

  | Uprawnienie | Opis |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|Wyświetlanie właściwości nieukońcowego magazynu kluczy|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|Przeczyszczanie nieukońcowego magazynu kluczy|


## <a name="what-are-soft-delete-and-purge-protection"></a>Co to jest usuwanie nie soft-delete i ochrona przed przeczyszczaniem

[Usuwanie nie softne](soft-delete-overview.md) i ochrona przed przeczyszczaniem to dwie różne funkcje odzyskiwania magazynu kluczy.

> [!IMPORTANT]
> Włączenie usuwania nie softowego ma kluczowe znaczenie dla zapewnienia, że magazyny kluczy i poświadczenia są chronione przed przypadkowym usunięciem. Jednak włączenie usuwania nie softowego jest uznawane za zmianę przerywaną, ponieważ może wymagać zmiany logiki aplikacji lub zapewnienia dodatkowych uprawnień do jednostki usługi. Przed włączeniem usuwania nie softowego, korzystając z poniższych instrukcji, upewnij się, że aplikacja jest zgodna ze zmianą, korzystając z tego [ **dokumentu tutaj.**](soft-delete-change.md)

**Usuwanie nie softowe** zostało zaprojektowane tak, aby zapobiec przypadkowemu usunięciu magazynu kluczy oraz kluczy, wpisów tajnych i certyfikatów przechowywanych wewnątrz magazynu kluczy. Usuwanie nieumyślne przypomina kosz. Usunięcie magazynu kluczy lub obiektu magazynu kluczy pozostanie możliwe do odzyskania przez użytkownika konfigurowalny okres przechowywania lub domyślnie 90 dni. Magazyny kluczy w stanie usunięcia  nietrwałych można również przeczyścić, co oznacza, że zostaną trwale usunięte. Umożliwia to ponowne utworzenie magazynów kluczy i obiektów magazynu kluczy o tej samej nazwie. Zarówno odzyskiwanie, jak i usuwanie magazynów kluczy i obiektów wymaga uprawnień zasad dostępu z podwyższonym poziomem uprawnień. **Po włączeniu usuwania nie soft nie można go wyłączyć.**

Należy pamiętać, że nazwy magazynów kluczy są globalnie **unikatowe,** dlatego nie będzie można utworzyć magazynu kluczy o takiej samej nazwie jak magazyn kluczy w stanie usunięcia nie soft. Podobnie nazwy kluczy, wpisów tajnych i certyfikatów są unikatowe w magazynie kluczy. Nie będzie można utworzyć klucza tajnego, klucza ani certyfikatu o takiej samej nazwie jak inna w stanie usunięcia nieukreślone.

**Ochrona przed przeczyszczaniem** została zaprojektowana tak, aby zapobiec usunięciu magazynu kluczy, kluczy, wpisów tajnych i certyfikatów przez złośliwego testera. Pomyśl o tym jak o koszu z blokadą opartą na czasie. Elementy można odzyskać w dowolnym momencie w konfigurowalnym okresie przechowywania. **Nie będzie można trwale usunąć ani przeczyścić magazynu kluczy, dopóki nie upłynie okres przechowywania.** Po upływie okresu przechowywania magazyn kluczy lub obiekt magazynu kluczy zostaną automatycznie przeczyszone.

> [!NOTE]
> Ochrona przed przeczyszczaniem jest zaprojektowana tak, aby żadna rola administratora ani uprawnienia nie mogą przesłaniać, wyłączać ani pomijać ochrony przed przeczyszczaniem. **Po włączeniu ochrony przed przeczyszczaniem nie można jej wyłączyć ani zastąpić dla wszystkich użytkowników, w tym firmy Microsoft.** Oznacza to, że należy odzyskać usunięty magazyn kluczy lub poczekać na upłynięcie okresu przechowywania przed ponownego nadaniem nazwy magazynu kluczy.

Aby uzyskać więcej informacji na temat usuwania nie programowego, [zobacz Azure Key Vault omówienie usuwania nie soft-delete](soft-delete-overview.md)

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Sprawdź, czy w magazynie kluczy włączono usuwanie nie softowe, i włącz usuwanie nie soft

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz magazyn kluczy.
1. Kliknij blok "Właściwości".
1. Sprawdź, czy przycisk radiowy obok opcji usuwania nie soft-delete ma ustawioną wartość "Włącz odzyskiwanie".
1. Jeśli usuwanie nie soft-delete nie jest włączone w magazynie kluczy, kliknij przycisk radiowy, aby włączyć usuwanie nie softowe, a następnie kliknij przycisk "Zapisz".

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="W oknie Właściwości wyróżniona jest wartość Usuwanie nieukreślone, tak jak wartość , aby ją włączyć.":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Udzielanie dostępu do jednostki usługi w celu przeczyszczania i odzyskiwania usuniętych wpisów tajnych

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz magazyn kluczy.
1. Kliknij blok "Zasady dostępu".
1. W tabeli znajdź wiersz podmiotu zabezpieczeń, do których chcesz udzielić dostępu (lub dodaj nowy podmiot zabezpieczeń).
1. Kliknij pozycję rozwijaną dla kluczy, certyfikatów i wpisów tajnych.
1. Przewiń do dołu listy rozwijanej i kliknij pozycję "Odzyskaj" i "Przeczyść"
1. Podmioty zabezpieczeń będą również potrzebować funkcji get i list, aby wykonywać większość operacji.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="W okienku nawigacji po lewej stronie wyróżnione są zasady dostępu. W zasadach dostępu jest wyświetlana lista rozwijana Pozycje tajne i wybrane są cztery elementy: Pobierz, Lista, Odzyskaj i Przeczyść.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Lista, odzyskiwanie lub przeczyszczanie nieu programowo usuniętego magazynu kluczy

1. Zaloguj się w witrynie Azure Portal.
1. Kliknij pasek wyszukiwania w górnej części strony.
1. W obszarze "Ostatnie usługi" kliknij pozycję "Key Vault". Nie klikaj pojedynczego magazynu kluczy.
1. W górnej części ekranu kliknij opcję "Zarządzaj usuniętymi magazynami"
1. Po prawej stronie ekranu zostanie otwarte okienko kontekstowe.
1. Wybierz subskrypcję.
1. Jeśli magazyn kluczy został usunięty nie softnie, pojawi się on w okienku kontekstu po prawej stronie.
1. Jeśli istnieje zbyt wiele magazynów, możesz kliknąć pozycję "Załaduj więcej" w dolnej części okienka kontekstu lub użyć interfejsu wiersza polecenia lub programu PowerShell, aby uzyskać wyniki.
1. Po odnalezieniu magazynu, który chcesz odzyskać lub przeczyścić, zaznacz pole wyboru obok niego.
1. Wybierz opcję odzyskiwania w dolnej części okienka kontekstu, jeśli chcesz odzyskać magazyn kluczy.
1. Wybierz opcję przeczyszczania, jeśli chcesz trwale usunąć magazyn kluczy.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="W przypadku magazynów kluczy opcja Zarządzaj usuniętymi magazynami jest wyróżniona.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="Na stronie Zarządzanie usuniętymi magazynami kluczy wyróżniony i wybrany jest tylko wymieniony magazyn kluczy, a przycisk Odzyskaj jest wyróżniony.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Lista, odzyskiwanie lub przeczyszczanie niealgotowych usuniętych wpisów tajnych, kluczy i certyfikatów

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz magazyn kluczy.
1. Wybierz blok odpowiadający typowi klucza tajnego, którym chcesz zarządzać (klucze, wpisy tajne lub certyfikaty).
1. W górnej części ekranu kliknij pozycję "Zarządzanie usuniętymi (kluczami, wpisami tajnymi lub certyfikatami)
1. Po prawej stronie ekranu zostanie wyświetlone okienko kontekstowe.
1. Jeśli twój klucz tajny, klucz lub certyfikat nie znajduje się na liście, nie jest w stanie usunięcia nieukreślone.
1. Wybierz klucz tajny, klucz lub certyfikat, który chcesz zarządzać.
1. Wybierz opcję odzyskiwania lub przeczyszczania w dolnej części okienka kontekstu.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="W przypadku kluczy opcja Zarządzaj usuniętymi kluczami jest wyróżniona.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (interfejs wiersza polecenia)

* Sprawdź, czy magazyn kluczy ma włączone usuwanie nie soft-delete

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Włączanie usuwania nie programowego w magazynie kluczy

    Wszystkie nowe magazyny kluczy mają domyślnie włączoną funkcję usuwania nie soft. Jeśli obecnie masz magazyn kluczy, który nie ma włączonego usuwania nie soft delete, użyj następującego polecenia, aby włączyć usuwanie nie soft.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Usuwanie magazynu kluczy (możliwe do odzyskania, jeśli włączono usuwanie nie softne)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Lista wszystkich magazynów kluczy usuniętych nieu programowo

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Odzyskiwanie nie soft-deleted magazynu kluczy

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Przeczyszczanie nieukońcowego magazynu kluczy **(OSTRZEŻENIE! TA OPERACJA SPOWODUJE TRWAŁE USUNIĘCIE MAGAZYNU KLUCZY)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Włączanie ochrony przed przeczyszczaniem w magazynie kluczy

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certyfikaty (interfejs wiersza polecenia)

* Udzielanie dostępu do przeczyszczania i odzyskiwania certyfikatów

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Usuwanie certyfikatu

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Lista usuniętych certyfikatów

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Odzyskiwanie usuniętego certyfikatu

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Przeczyszczanie nieukońcowego usuniętego certyfikatu **(OSTRZEŻENIE! TA OPERACJA SPOWODUJE TRWAŁE USUNIĘCIE CERTYFIKATU)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Klucze (interfejs wiersza polecenia)

* Udzielanie dostępu do przeczyszczania i odzyskiwania kluczy

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Klawisz Delete

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Lista usuniętych kluczy

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Odzyskiwanie usuniętego klucza

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Przeczyszczanie klucza usuniętego nie soft -deleted **(OSTRZEŻENIE! TA OPERACJA SPOWODUJE TRWAŁE USUNIĘCIE KLUCZA)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Wpisy tajne (interfejs wiersza polecenia)

* Udzielanie dostępu do przeczyszczania i odzyskiwania wpisów tajnych

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Usuwanie tajnego

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Lista usuniętych wpisów tajnych

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Odzyskiwanie usuniętego tajnego

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Przeczyść nieukreślony klucz tajny **(OSTRZEŻENIE! TA OPERACJA SPOWODUJE TRWAŁE USUNIĘCIE TWOJEGO TAJNEGO.**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* Sprawdź, czy magazyn kluczy ma włączone usuwanie nie soft-delete

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Usuwanie magazynu kluczy

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Lista wszystkich nie soft-deleted magazynów kluczy

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Odzyskiwanie nie soft-deleted magazynu kluczy

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Przeczyść nieukreślony magazyn kluczy **(OSTRZEŻENIE! TA OPERACJA SPOWODUJE TRWAŁE USUNIĘCIE MAGAZYNU KLUCZY)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Włączanie ochrony przed przeczyszczaniem w magazynie kluczy

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certyfikaty (PowerShell)

* Udzielanie uprawnień do odzyskiwania i przeczyszczania certyfikatów

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Usuwanie certyfikatu

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Lista wszystkich usuniętych certyfikatów w magazynie kluczy

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Odzyskiwanie certyfikatu w stanie usunięcia

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Przeczyszczanie nie soft-deleted certyfikatu **(OSTRZEŻENIE! TA OPERACJA SPOWODUJE TRWAŁE USUNIĘCIE CERTYFIKATU)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Klucze (PowerShell)

* Udzielanie uprawnień do odzyskiwania i przeczyszczania kluczy

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Usuwanie klucza

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Lista wszystkich usuniętych certyfikatów w magazynie kluczy

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Aby odzyskać klucz usunięty nieu programowo

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Przeczyszczanie klucza nie soft-deleted **(OSTRZEŻENIE! TA OPERACJA SPOWODUJE TRWAŁE USUNIĘCIE KLUCZA)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Wpisy tajne (PowerShell)

* Udzielanie uprawnień do odzyskiwania i przeczyszczania wpisów tajnych

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Usuwanie tajnego hasła o nazwie SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Lista wszystkich usuniętych wpisów tajnych w magazynie kluczy

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Odzyskiwanie tajnego stanu usunięcia

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Przeczyszczanie tajnego w stanie usunięcia **(OSTRZEŻENIE! TA OPERACJA SPOWODUJE TRWAŁE USUNIĘCIE KLUCZA)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault cmdlet programu PowerShell](/powershell/module/az.keyvault)
- [Key Vault poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure/keyvault)
- [Azure Key Vault kopii zapasowej](backup.md)
- [Jak włączyć Key Vault rejestrowania](howto-logging.md)
- [Bezpieczny dostęp do magazynu kluczy](security-overview.md)
- [Azure Key Vault dewelopera](developers-guide.md)
- [Najlepsze rozwiązania dotyczące korzystania z magazynu kluczy](security-overview.md)