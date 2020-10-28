---
title: Omówienie odzyskiwania Azure Key Vault | Microsoft Docs
description: Funkcja Key Vault Recovery została zaprojektowana tak, aby zapobiegać przypadkowemu lub złośliwemu usuwaniu magazynu kluczy i kluczy tajnych oraz certyfikatów przechowywanych w magazynie kluczy.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: ShaneBala-keyvault
ms.author: sudbalas
manager: ravijan
ms.date: 09/30/2020
ms.openlocfilehash: fbeb6f5f223642c09183c149188c6717c1f33a8e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92748505"
---
# <a name="how-to-enable-soft-delete-and-purge-protection"></a>Włączanie trwałego usuwania i przeczyszczania ochrony

W tym artykule opisano dwie funkcje odzyskiwania Azure Key Vault, nietrwałego usuwania i przeczyszczania. Ten dokument zawiera omówienie tych funkcji oraz sposób zarządzania nimi za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure i Azure PowerShell.

## <a name="what-are-soft-delete-and-purge-protection"></a>Co to są operacje usuwania nietrwałego i przeczyszczania

Nietrwałe usuwanie i ochrona przed czyszczeniem to dwie różne funkcje odzyskiwania magazynu kluczy.
> [!IMPORTANT]
> Ochrona usuwania nietrwałego musi być włączona we wszystkich magazynach kluczy. Możliwość wyłączenia ochrony przed usunięciem nietrwałego zostanie wycofana z grudnia 2020. Więcej informacji można znaleźć [ **tutaj** .](soft-delete-change.md)

**Usuwanie nietrwałe** zostało zaprojektowane, aby zapobiec przypadkowemu usunięciu magazynu kluczy oraz kluczy, wpisów tajnych i certyfikatów przechowywanych w magazynie kluczy. Pomyśl o usuwaniu nietrwałego, podobnie jak kosz. Po usunięciu magazynu kluczy lub obiektu magazynu kluczy pozostanie on odwracalny dla konfigurowanego okresu przechowywania użytkownika lub domyślnego 90 dni. Magazyny kluczy w stanie usuniętej nietrwałe mogą być również **przeczyszczane** , co oznacza, że są trwale usuwane. Pozwala to na ponowne utworzenie magazynów kluczy i obiektów magazynu kluczy o tej samej nazwie. Zarówno odzyskiwanie, jak i usunięcie magazynów kluczy i obiektów wymagają podniesionych uprawnień zasad dostępu. **Po włączeniu usuwania nietrwałego nie można go wyłączyć.**

Należy pamiętać, że **nazwy magazynów kluczy są globalnie unikatowe** , dlatego nie będzie można utworzyć magazynu kluczy o takiej samej nazwie jak Magazyn kluczy w stanie usunięte nietrwałe. Podobnie nazwy kluczy, wpisów tajnych i certyfikatów są unikatowe w ramach magazynu kluczy. Nie będzie można utworzyć wpisu tajnego, klucza lub certyfikatu o takiej samej nazwie jak w stanie usunięte nietrwałe.

**Ochrona przed przeczyszczeniem** została zaprojektowana, aby zapobiec usunięciu magazynu kluczy, kluczy, wpisów tajnych i certyfikatów przez złośliwego użytkownika. Należy zastanowić się, że jest to kosz z blokadą opartą na czasie. W dowolnym momencie można odzyskać elementy w ramach konfigurowalnego okresu przechowywania. **Nie będzie możliwe trwałe usunięcie lub przeczyszczenie magazynu kluczy do momentu, gdy upłynie okres przechowywania.** Gdy okres przechowywania upływający z magazynu kluczy lub obiekt magazynu kluczy zostanie automatycznie przeczyszczony.

> [!NOTE]
> Ochrona przed przeczyszczeniem została zaprojektowana tak, aby żadna rola administratora nie mogła przesłonić, wyłączyć ani obejść ochrony przed usunięciem. **Po włączeniu ochrony nie można jej wyłączyć ani przesłonić przez żadną osobę, która obejmuje firmę Microsoft.** Oznacza to, że należy odzyskać usunięty Magazyn kluczy lub poczekać na upłynąć okresu przechowywania przed ponownym użyciem nazwy magazynu kluczy.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>Sprawdź, czy w magazynie kluczy jest włączona funkcja usuwania nietrwałego i Włącz opcję usuwania nietrwałego

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz magazyn kluczy.
1. Kliknij blok "właściwości".
1. Sprawdź, czy przycisk radiowy obok pozycji usuwanie nietrwałe jest ustawiony na wartość "Włącz odzyskiwanie".
1. Jeśli nie włączono usuwania nietrwałego w magazynie kluczy, kliknij przycisk radiowy, aby włączyć usuwanie nietrwałe, i kliknij przycisk "Zapisz".

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="<zrzut ekranu Azure Portal>":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>Udzielanie dostępu do jednostki usługi w celu przeczyszczenia i odzyskania usuniętych wpisów tajnych

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz magazyn kluczy.
1. Kliknij blok "zasady dostępu".
1. W tabeli Znajdź wiersz podmiotu zabezpieczeń, do którego chcesz udzielić dostępu (lub Dodaj nowy podmiot zabezpieczeń).
1. Kliknij listę rozwijaną dla kluczy, certyfikatów i wpisów tajnych.
1. Przewiń w dół listy rozwijanej, a następnie kliknij pozycję "Odzyskaj" i "Wyczyść"
1. Podmioty zabezpieczeń będą potrzebować również funkcji pobierania i wyświetlania listy w celu wykonania większości operacji.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="<zrzut ekranu Azure Portal>":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>Wyświetlanie, odzyskiwanie lub przeczyszczanie nietrwałego magazynu kluczy

1. Zaloguj się w witrynie Azure Portal.
1. Kliknij pasek wyszukiwania w górnej części strony.
1. W obszarze "ostatnie usługi" kliknij pozycję "Key Vault". Nie klikaj pojedynczego magazynu kluczy.
1. W górnej części ekranu kliknij opcję "Zarządzaj usuniętymi magazynami".
1. Po prawej stronie ekranu zostanie otwarte okienko kontekstowe.
1. Wybierz subskrypcję.
1. Jeśli Twój Magazyn kluczy został usunięty z nietrwałego, zostanie wyświetlony w okienku kontekstowym po prawej stronie.
1. Jeśli istnieje zbyt wiele magazynów, możesz kliknąć przycisk "Załaduj więcej" u dołu okienka kontekstowego lub użyć interfejsu wiersza polecenia lub programu PowerShell, aby uzyskać wyniki.
1. Po znalezieniu magazynu, który chcesz odzyskać lub wyczyścić, zaznacz pole wyboru obok niego.
1. Zaznacz opcję Odzyskaj u dołu okienka kontekstowego, jeśli chcesz odzyskać Magazyn kluczy.
1. Wybierz opcję przeczyszczania, jeśli chcesz trwale usunąć magazyn kluczy.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="<zrzut ekranu Azure Portal>":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="<zrzut ekranu Azure Portal>":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>Wyświetlanie, odzyskiwanie lub przeczyszczanie usuniętych, nietrwałych wpisów tajnych, kluczy i certyfikatów

1. Zaloguj się w witrynie Azure Portal.
1. Wybierz magazyn kluczy.
1. Wybierz blok odpowiadający typowi wpisu tajnego, który ma być zarządzany (klucze, wpisy tajne lub certyfikaty).
1. W górnej części ekranu kliknij pozycję "Zarządzaj usuniętymi (klucze, wpisy tajne lub certyfikaty)".
1. Okienko kontekstowe zostanie wyświetlone po prawej stronie ekranu.
1. Jeśli wpis tajny, klucz lub certyfikat nie znajduje się na liście, nie jest w stanie nieusuniętym.
1. Wybierz wpis tajny, klucz lub certyfikat, którym chcesz zarządzać.
1. Wybierz opcję odzyskiwania lub przeczyszczania w dolnej części okienka kontekstowego.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="<zrzut ekranu Azure Portal>":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (interfejs wiersza polecenia)

* Sprawdź, czy w magazynie kluczy jest włączona funkcja usuwania nietrwałego

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Włączanie usuwania nietrwałego w magazynie kluczy

    Wszystkie nowe magazyny kluczy mają domyślnie włączone usuwanie nietrwałe. Jeśli masz już Magazyn kluczy, w którym nie włączono usuwania nietrwałego, użyj następującego polecenia, aby włączyć usuwanie nietrwałe.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* Usuń Magazyn kluczy (odwracalne w przypadku włączenia usuwania nietrwałego)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* Wyświetl wszystkie nietrwałe magazyny kluczy

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* Odzyskaj usunięty nietrwale Magazyn kluczy

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Przeczyść Magazyn kluczy usunięty z nietrwałego **(ostrzeżenie! Ta operacja spowoduje trwałe usunięcie Twojego magazynu kluczy)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* Włącz ochronę przed czyszczeniem w magazynie kluczy

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>Certyfikaty (CLI)

* Udzielanie dostępu do przeczyszczania i odzyskiwania certyfikatów

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* Usuń certyfikat

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Wyświetl listę usuniętych certyfikatów

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Odzyskaj usunięty certyfikat

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* Przeczyszczanie usuniętego certyfikatu nietrwałego **(ostrzeżenie! Ta operacja spowoduje trwałe usunięcie certyfikatu.**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>Klucze (CLI)

* Udzielanie dostępu w celu przeczyszczenia i odzyskania kluczy

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Klawisz Delete

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Wyświetl usunięte klucze

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Odzyskaj usunięty klucz

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* Wyczyść klucz nietrwały **(ostrzeżenie! Ta operacja spowoduje trwałe usunięcie klucza.**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>Wpisy tajne (CLI)

* Udzielanie dostępu do przeczyszczania i odzyskiwania wpisów tajnych

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* Usuń klucz tajny

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Utwórz listę usuniętych wpisów tajnych

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* Odzyskaj usunięte hasło

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* Wyczyść klucz tajny nietrwałe **(ostrzeżenie! Ta operacja spowoduje trwałe usunięcie klucza TAJNego.**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (program PowerShell)

* Sprawdź, czy w magazynie kluczy jest włączona funkcja usuwania nietrwałego

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* Usuń Magazyn kluczy

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* Wyświetl wszystkie nietrwałe magazyny kluczy

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* Odzyskaj usunięty nietrwale Magazyn kluczy

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* Wyczyść Magazyn kluczy usuniętych nietrwałego **(ostrzeżenie! Ta operacja spowoduje trwałe usunięcie Twojego magazynu kluczy)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* Włącz ochronę przed czyszczeniem w magazynie kluczy

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>Certyfikaty (program PowerShell)

* Przyznawanie uprawnień do odzyskiwania i przeczyszczania certyfikatów

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* Usuwanie certyfikatu

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* Wyświetlanie listy wszystkich usuniętych certyfikatów w magazynie kluczy

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* Odzyskaj certyfikat w stanie usunięty

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* Przeczyszczanie nieusuniętego certyfikatu **(ostrzeżenie! Ta operacja spowoduje trwałe usunięcie certyfikatu.**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>Klucze (program PowerShell)

* Przyznawanie uprawnień do odzyskiwania i przeczyszczania kluczy

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* Usuń klucz

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* Wyświetlanie listy wszystkich usuniętych certyfikatów w magazynie kluczy

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* Aby odzyskać klucz nietrwały

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* Przeczyszczanie klucza nietrwałego **(ostrzeżenie! Ta operacja spowoduje trwałe usunięcie klucza.**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>Wpisy tajne (program PowerShell)

* Przyznawanie uprawnień do odzyskiwania i przeczyszczania wpisów tajnych

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* Usuń wpis tajny o nazwie SQLPassword

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* Wyświetlanie listy wszystkich usuniętych wpisów tajnych w magazynie kluczy

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* Odzyskaj wpis tajny w stanie usunięty

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* Przeczyszczanie wpisu tajnego w stanie usunięte **(ostrzeżenie! Ta operacja spowoduje trwałe usunięcie klucza.**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
