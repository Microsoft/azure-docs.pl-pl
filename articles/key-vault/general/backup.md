---
title: Kopię zapasową klucza tajnego, klucza lub certyfikatu przechowywanego w Azure Key Vault | Microsoft Docs
description: Ten dokument pomaga w kopii zapasowej klucza tajnego, klucza lub certyfikatu przechowywanego w Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/18/2021
ms.author: sudbalas
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 399f55d379f99a784fed97d7e9f72c456eb1f914
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484816"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault kopii zapasowej

W tym dokumencie przedstawiono sposób kopii zapasowych wpisów tajnych, kluczy i certyfikatów przechowywanych w magazynie kluczy. Kopia zapasowa ma na celu zapewnienie kopii wszystkich wpisów tajnych w trybie offline w mało prawdopodobnym przypadku utraty dostępu do magazynu kluczy.

## <a name="overview"></a>Omówienie

Azure Key Vault automatycznie udostępnia funkcje, które pomagają zachować dostępność i zapobiec utracie danych. Kopię zapasową wpisów tajnych należy wrócić tylko wtedy, gdy użytkownik ma krytyczne uzasadnienie biznesowe. Tworzenie kopii zapasowych wpisów tajnych w magazynie kluczy może powodować wyzwania operacyjne, takie jak obsługa wielu zestawów dzienników, uprawnień i kopii zapasowych w przypadku wygaśnięcia lub rotacji wpisów tajnych.

Key Vault dostępność w scenariuszach awarii i będzie automatycznie włączać żądania trybu fail over do sparowanego regionu bez żadnej interwencji użytkownika. Aby uzyskać więcej informacji, [zobacz Azure Key Vault dostępności i nadmiarowości.](./disaster-recovery-guidance.md)

Jeśli chcesz chronić przed przypadkowym lub złośliwym usunięciem wpisów tajnych, skonfiguruj w magazynie kluczy funkcje usuwania nie soft-delete i przeczyszczania. Aby uzyskać więcej informacji, [zobacz Azure Key Vault omówienie usuwania nie programowego.](./soft-delete-overview.md)

## <a name="limitations"></a>Ograniczenia

> [!IMPORTANT]
> Key Vault nie obsługuje możliwości tworzenia kopii zapasowej ponad 500 wcześniejszych wersji klucza, klucza tajnego lub obiektu certyfikatu. Próba kopii zapasowej klucza, klucza tajnego lub obiektu certyfikatu może spowodować błąd. Nie można usunąć poprzednich wersji klucza, klucza tajnego ani certyfikatu.

Key Vault obecnie nie zapewnia możliwości kopii zapasowej całego magazynu kluczy w ramach jednej operacji. Każda próba użycia poleceń wymienionych w tym dokumencie do automatycznego tworzenia kopii zapasowej magazynu kluczy może spowodować błędy i nie będzie obsługiwana przez firmę Microsoft ani zespół Azure Key Vault zabezpieczeń. 

Należy również wziąć pod uwagę następujące konsekwencje:

* W przypadku kopii zapasowej wpisów tajnych, które mają wiele wersji, mogą wystąpić błędy przechowania.
* Kopia zapasowa tworzy migawkę punktu w czasie. Wpisy tajne mogą być odnawiane podczas tworzenia kopii zapasowej, co powoduje niezgodność kluczy szyfrowania.
* Przekroczenie limitów usługi magazynu kluczy dla żądań na sekundę spowoduje ograniczenie magazynu kluczy, a tworzenie kopii zapasowej nie powiedzie się.

## <a name="design-considerations"></a>Zagadnienia dotyczące projektowania

Podczas tworzenia kopii zapasowej obiektu magazynu kluczy, takiego jak klucz tajny, klucz lub certyfikat, operacja tworzenia kopii zapasowej pobierze obiekt jako zaszyfrowany obiekt blob. Tego obiektu blob nie można odszyfrować poza platformą Azure. Aby uzyskać użyteczne dane z tego obiektu blob, musisz przywrócić obiekt blob do magazynu kluczy w ramach tej samej subskrypcji platformy Azure i lokalizacji [geograficznej platformy Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kopię zapasową obiektu magazynu kluczy, musisz mieć: 

* Uprawnienia na poziomie współautora lub wyższym w subskrypcji platformy Azure.
* Podstawowy magazyn kluczy zawierający wpisy tajne, których kopię zapasową chcesz wrócić.
* Pomocniczy magazyn kluczy, w którym zostaną przywrócone wpisy tajne.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Back up and restore from the Azure Portal (Tworzyć i przywracać kopię zapasową z Azure Portal

Wykonaj kroki opisane w tej sekcji, aby wykonać kopię zapasową i przywrócić obiekty przy użyciu Azure Portal.

### <a name="back-up"></a>Wykonywanie kopii zapasowej

1. Przejdź do witryny Azure Portal.
2. Wybierz magazyn kluczy.
3. Przejdź do obiektu (klucza tajnego, klucza lub certyfikatu), którego kopię zapasową chcesz wrócić.

    ![Zrzut ekranu przedstawiający miejsce wybrania ustawienia Klucze i obiektu w magazynie kluczy.](../media/backup-1.png)

4. Wybierz obiekt .
5. Wybierz **pozycję Pobierz kopię zapasową.**

    ![Zrzut ekranu przedstawiający miejsce wybrania przycisku Pobierz kopię zapasową w magazynie kluczy.](../media/backup-2.png)
    
6. Kliknij pozycję **Pobierz**.

    ![Zrzut ekranu przedstawiający miejsce wybrania przycisku Pobierz w magazynie kluczy.](../media/backup-3.png)
    
7. Przechowuj zaszyfrowany obiekt blob w bezpiecznej lokalizacji.

### <a name="restore"></a>Przywracanie

1. Przejdź do witryny Azure Portal.
2. Wybierz magazyn kluczy.
3. Przejdź do typu obiektu (wpis tajny, klucz lub certyfikat), który chcesz przywrócić.
4. Wybierz pozycję **Przywróć kopię zapasową.**

    ![Zrzut ekranu przedstawiający miejsce wybrania opcji Przywróć kopię zapasową w magazynie kluczy.](../media/backup-4.png)
    
5. Przejdź do lokalizacji, w której został zapisany zaszyfrowany obiekt blob.
6. Wybierz przycisk **OK**.

## <a name="back-up-and-restore-from-the-azure-cli-or-azure-powershell"></a>Back up and restore from the Azure CLI or Azure PowerShell

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```azurepowershell
## Log in to Azure
Connect-AzAccount

## Set your subscription
Set-AzContext -Subscription '{AZURE SUBSCRIPTION ID}'

## Back up a certificate in Key Vault
Backup-AzKeyVaultCertificate -VaultName '{Certificate Name}' -Name '{Key Vault Name}'

## Back up a key in Key Vault
Backup-AzKeyVaultKey -VaultName '{Key Name}' -Name '{Key Vault Name}'

## Back up a secret in Key Vault
Backup-AzKeyVaultSecret -VaultName '{Key Vault Name}' -Name '{Secret Name}'

## Restore a certificate in Key Vault
Restore-AzKeyVaultCertificate -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a key in Key Vault
Restore-AzKeyVaultKey -VaultName '{Key Vault Name}' -InputFile '{File Path}'

## Restore a secret in Key Vault
Restore-AzKeyVaultSecret -VaultName '{Key Vault Name}' -InputFile '{File Path}'
```
---

## <a name="next-steps"></a>Następne kroki

Włącz rejestrowanie [i monitorowanie dla](./logging.md) Key Vault.
