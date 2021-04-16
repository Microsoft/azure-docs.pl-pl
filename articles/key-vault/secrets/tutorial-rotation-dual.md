---
title: Samouczek rotacji zasobów z dwoma zestawami poświadczeń
description: Z tego samouczka dowiesz się, jak zautomatyzować rotację tajnego hasła dla zasobów, które używają dwóch zestawów poświadczeń uwierzytelniania.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 1f656a41b0f447b90f58ec14173e418a2defb72e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484833"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatyzowanie rotacji tajnego dla zasobów, które mają dwa zestawy poświadczeń uwierzytelniania

Najlepszym sposobem uwierzytelniania w usługach [](../general/authentication.md)platformy Azure jest użycie tożsamości zarządzanej, ale w niektórych scenariuszach nie jest to możliwe. W takich przypadkach są używane klucze dostępu lub hasła. Należy często obracać klucze dostępu i hasła.

W tym samouczku pokazano, jak zautomatyzować okresową rotację wpisów tajnych dla baz danych i usług, które używają dwóch zestawów poświadczeń uwierzytelniania. W szczególności w tym samouczku przedstawiono sposób rotacji kluczy kont usługi Azure Storage przechowywanych w usłudze Azure Key Vault jako wpisy tajne. Użyjesz funkcji wyzwolone przez Azure Event Grid powiadomienia. 

> [!NOTE]
> Klucze konta magazynu mogą być automatycznie zarządzane Key Vault użytą w przypadku zapewnienia tokenów sygnatury dostępu współdzielonych w celu delegowanego dostępu do konta magazynu. Istnieją usługi, które wymagają ciągów połączenia konta magazynu z kluczami dostępu. W tym scenariuszu zalecamy to rozwiązanie.

Oto rozwiązanie rotacji opisane w tym samouczku: 

![Diagram przedstawiający rozwiązanie rotacji.](../media/secrets/rotation-dual/rotation-diagram.png)

W tym rozwiązaniu Azure Key Vault indywidualne klucze dostępu konta magazynu jako wersje tego samego klucza tajnego, naprzemiennie między kluczem podstawowym i pomocniczym w kolejnych wersjach. Gdy jeden klucz dostępu jest przechowywany w najnowszej wersji klucza tajnego, klucz alternatywny jest ponownie generowany i dodawany do Key Vault jako nowa najnowsza wersja klucza tajnego. Rozwiązanie zapewnia cały cykl rotacji aplikacji w celu odświeżenia najnowszego ponownie wygenerowanego klucza. 

1. Po upływie 30 dni od daty wygaśnięcia tajnego Key Vault publikuje zdarzenie bliskie wygaśnięcia do Event Grid.
1. Event Grid sprawdza subskrypcje zdarzeń i używa żądania HTTP POST do wywołania punktu końcowego aplikacji funkcji subskrybowanego do zdarzenia.
1. Aplikacja funkcji identyfikuje klucz alternatywny (nie najnowszy) i wywołuje konto magazynu w celu jego ponownego wygenerowania.
1. Aplikacja funkcji dodaje nowy ponownie wygenerowany klucz do Azure Key Vault jako nową wersję klucza tajnego.

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/). W tym samouczku jest Cloud Shell portal z programem PowerShell env
* Azure Key Vault.
* Dwa konta usługi Azure Storage.

Tego linku wdrażania możesz użyć, jeśli nie masz istniejącego magazynu kluczy i istniejących kont magazynu:

[![Link oznaczony etykietą Deploy to Azure (Wd wdrażaj na platformie Azure).](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. W **obszarze Grupa zasobów** wybierz pozycję Utwórz **nową.** Nadaj grupie **nazwę vaultrotation,** a następnie wybierz przycisk **OK.**
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

    ![Zrzut ekranu przedstawiający sposób tworzenia grupy zasobów.](../media/secrets/rotation-dual/dual-rotation-1.png)

Będziesz teraz mieć magazyn kluczy i dwa konta magazynu. Możesz zweryfikować tę konfigurację w interfejsie wiersza polecenia platformy Azure Azure PowerShell uruchamiając to polecenie:
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Wynik będzie wyglądać podobnie do tych danych wyjściowych:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Tworzenie i wdrażanie funkcji rotacji kluczy

Następnie utworzysz aplikację funkcji z tożsamością zarządzaną przez system, oprócz innych wymaganych składników. Wdrożysz również funkcję rotacji dla kluczy konta magazynu.

Funkcja rotacji aplikacji funkcji wymaga następujących składników i konfiguracji:
- Plan Azure App Service aplikacji
- Konto magazynu do zarządzania wyzwalaczami aplikacji funkcji
- Zasady dostępu do uzyskiwania dostępu do wpisów tajnych w Key Vault
- Rola operatora klucza konta magazynu przypisana do aplikacji funkcji w celu uzyskania dostępu do kluczy dostępu do konta magazynu
- Funkcja rotacji kluczy z wyzwalaczem zdarzenia i wyzwalaczem HTTP (rotacja na żądanie)
- Subskrypcja Event Grid dla zdarzenia **SecretNearExpiry**

1. Wybierz link wdrażania szablonu platformy Azure: 

   [![Link wdrażania szablonu platformy Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Na liście **Grupa zasobów** wybierz pozycję **vaultrotation**.
1. W polu **Grupa zasobów konta magazynu** wprowadź nazwę grupy zasobów, w której znajduje się konto magazynu. Zachowaj wartość domyślną **[resourceGroup().name],** jeśli konto magazynu znajduje się już w tej samej grupie zasobów, w której zostanie wdrożona funkcja rotacji kluczy.
1. W **polu Nazwa konta** magazynu wprowadź nazwę konta magazynu zawierającego klucze dostępu do rotacji. Zachowaj wartość domyślną **[concat(resourceGroup().name, 'storage')],** jeśli używasz konta magazynu utworzonego w [wymaganiach wstępnych](#prerequisites).
1. W **polu Key Vault RG** wprowadź nazwę grupy zasobów, w której znajduje się magazyn kluczy. Zachowaj wartość domyślną **[resourceGroup().name],** jeśli magazyn kluczy już istnieje w tej samej grupie zasobów, w której zostanie wdrożona funkcja rotacji kluczy.
1. W **Key Vault Nazwa** klucza wprowadź nazwę magazynu kluczy. Zachowaj wartość domyślną **[concat(resourceGroup().name, '-kv')],** jeśli używasz magazynu kluczy utworzonego w [teksie Prerequisites](#prerequisites).
1. W polu **App Service Plan hostingu** wybierz pozycję Plan hostingu. **Plan Premium** jest wymagany tylko wtedy, gdy magazyn kluczy znajduje się za zaporą.
1. W **polu Nazwa aplikacji** funkcji wprowadź nazwę aplikacji funkcji.
1. W polu **Nazwa klucza** tajnego wprowadź nazwę klucza tajnego, w którym będą przechowywane klucze dostępu.
1. W polu **Adres URL** repozytorium wprowadź lokalizację kodu funkcji w usłudze GitHub. W tym samouczku możesz użyć funkcji **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający sposób tworzenia i wdrażania funkcji.](../media/secrets/rotation-dual/dual-rotation-2.png)

Po ukończeniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów, aplikację funkcji i Application Insights. Po zakończeniu wdrażania zobaczysz tę stronę:

   ![Zrzut ekranu przedstawiający stronę Wdrożenie jest ukończone.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Jeśli wystąpi awaria, możesz wybrać pozycję **Wdeń** ponownie, aby zakończyć wdrażanie składników.


Szablony wdrażania i kod funkcji rotacji można znaleźć w [przykładach platformy Azure.](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell)

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Dodaj klucze dostępu konta magazynu do Key Vault

Najpierw ustaw zasady dostępu, aby udzielić uprawnień **do zarządzania wpisami tajnymi** dla podmiotu zabezpieczeń użytkownika:
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

Teraz możesz utworzyć nowy klucz tajny z kluczem dostępu konta magazynu jako jego wartością. Będziesz również potrzebować identyfikatora zasobu konta magazynu, okresu ważności klucza tajnego i identyfikatora klucza do dodania do klucza tajnego, aby funkcja rotacji może ponownie wygenerować klucz na koncie magazynu.

Określ identyfikator zasobu konta magazynu. Tę wartość można znaleźć we właściwości `id` .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Wymień listę kluczy dostępu do konta magazynu, aby uzyskać wartości kluczy:
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Dodaj klucz tajny do magazynu kluczy z datą wygaśnięcia ustawioną na jutrzejszy okres ważności przez 60 dni i identyfikatorem zasobu konta magazynu. Uruchom to polecenie, używając pobranych wartości dla `key1Value` i `storageAccountResourceId` :

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Powyższy klucz tajny wyzwoli `SecretNearExpiry` zdarzenie w ciągu kilku minut. To zdarzenie z kolei wyzwoli funkcję, aby obrócić klucz tajny z datą wygaśnięcia ustawioną na 60 dni. W tej konfiguracji zdarzenie "SecretNearExpiry" będzie wyzwalane co 30 dni (30 dni przed wygaśnięciem), a funkcja rotacji będzie zmieniać rotację między kluczem key1 i key2.

Możesz sprawdzić, czy klucze dostępu zostały ponownie wygenerowane, pobierania klucza konta magazynu i klucza Key Vault i porównać je.

Użyj tego polecenia, aby uzyskać informacje o kluczu tajnym:
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

Zwróć `CredentialId` uwagę, że zmienna jest aktualizowana do alternatywnej i `keyName` `value` jest ponownie generowana:

![Zrzut ekranu przedstawiający dane wyjściowe polecenia z keyvault secret show dla pierwszego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-4.png)

Pobierz klucze dostępu, aby porównać wartości:
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Zwróć `value` uwagę, że klucz jest taki sam jak klucz tajny w magazynie kluczy:

![Zrzut ekranu przedstawiający dane wyjściowe polecenia z listy kluczy konta magazynu dla pierwszego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Dodawanie kont magazynu do rotacji

Możesz ponownie użyć tej samej aplikacji funkcji, aby obrócić klucze dla wielu kont magazynu. 

Aby dodać klucze konta magazynu do istniejącej funkcji w celu rotacji, potrzebne są:
- Rola operatora klucza konta magazynu przypisana do aplikacji funkcji w celu uzyskania dostępu do kluczy dostępu do konta magazynu.
- Subskrypcja Event Grid dla zdarzenia **SecretNearExpiry.**

1. Wybierz link wdrażania szablonu platformy Azure: 

   [![Link wdrażania szablonu platformy Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Na liście **Grupa zasobów** wybierz pozycję **vaultrotation**.
1. W polu **Grupa zasobów konta magazynu** wprowadź nazwę grupy zasobów, w której znajduje się konto magazynu. Zachowaj wartość domyślną **[resourceGroup().name],** jeśli konto magazynu znajduje się już w tej samej grupie zasobów, w której zostanie wdrożona funkcja rotacji kluczy.
1. W **polu Nazwa konta** magazynu wprowadź nazwę konta magazynu zawierającego klucze dostępu do rotacji.
1. W **polu Key Vault RG** wprowadź nazwę grupy zasobów, w której znajduje się magazyn kluczy. Zachowaj wartość domyślną **[resourceGroup().name],** jeśli magazyn kluczy już istnieje w tej samej grupie zasobów, w której zostanie wdrożona funkcja rotacji kluczy.
1. W **Key Vault Nazwa** klucza wprowadź nazwę magazynu kluczy.
1. W **polu Nazwa aplikacji** funkcji wprowadź nazwę aplikacji funkcji.
1. W polu **Nazwa klucza** tajnego wprowadź nazwę klucza tajnego, w którym będą przechowywane klucze dostępu.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający sposób tworzenia dodatkowego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Dodaj kolejny klucz dostępu konta magazynu do Key Vault

Określ identyfikator zasobu konta magazynu. Tę wartość można znaleźć we właściwości `id` .
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Aby uzyskać wartość key2, należy wyświetlić listę kluczy dostępu konta magazynu:
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Dodaj klucz tajny do magazynu kluczy z datą wygaśnięcia ustawioną na jutrzejszy okres ważności przez 60 dni i identyfikatorem zasobu konta magazynu. Uruchom to polecenie, używając pobranych wartości dla `key2Value` i `storageAccountResourceId` :

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Użyj tego polecenia, aby uzyskać informacje o kluczu tajnym:
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

Zwróć `CredentialId` uwagę, że zmienna jest aktualizowana do alternatywnej `keyName` i `value` jest ponownie generowana:

![Zrzut ekranu przedstawiający dane wyjściowe polecenia z keyvault secret show dla drugiego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-8.png)

Pobierz klucze dostępu, aby porównać wartości:
# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Zwróć `value` uwagę, że klucz jest taki sam jak klucz tajny w magazynie kluczy:

![Zrzut ekranu przedstawiający dane wyjściowe polecenia z storage account keys list dla drugiego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Key Vault funkcje rotacji dla dwóch zestawów poświadczeń

Szablon funkcji rotacji dla dwóch zestawów poświadczeń i kilku gotowych do użycia funkcji:

- [Szablon projektu](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Pamięć podręczna Redis](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Konto magazynu](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> Powyższe funkcje rotacji są tworzone przez członka społeczności, a nie przez firmę Microsoft. Program Community Azure Functions nie są obsługiwane w ramach żadnego programu ani usługi pomocy technicznej firmy Microsoft i są udostępniane w wersji AS IS bez jakiejkolwiek gwarancji.

## <a name="next-steps"></a>Następne kroki

- Samouczek: [rotacja wpisów tajnych dla jednego zestawu poświadczeń](./tutorial-rotation.md)
- Omówienie: [Monitorowanie Key Vault za pomocą Azure Event Grid](../general/event-grid-overview.md)
- Jak utworzyć [pierwszą funkcję w Azure Portal](../../azure-functions/functions-get-started.md)
- Dzieje się tak: [otrzymywanie wiadomości e-mail po Key Vault tajnych](../general/event-grid-logicapps.md)
- Odwołanie: [Azure Event Grid schematu zdarzeń dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
