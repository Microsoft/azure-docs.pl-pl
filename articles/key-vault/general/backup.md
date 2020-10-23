---
title: Wykonaj kopię zapasową wpisu tajnego, klucza lub certyfikatu przechowywanego w Azure Key Vault | Microsoft Docs
description: Ten dokument pomaga utworzyć kopię zapasową wpisu tajnego, klucza lub certyfikatu przechowywanego w Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/22/2020
ms.author: sudbalas
ms.openlocfilehash: 931aea02d0a3c26bb5c2e7158f9c4360976d3af5
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440522"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault kopia zapasowa

W tym dokumencie przedstawiono sposób tworzenia kopii zapasowych wpisów tajnych, kluczy i certyfikatów przechowywanych w magazynie kluczy. Kopia zapasowa jest przeznaczona do udostępnienia kopii w trybie offline wszystkich wpisów tajnych w przypadku, gdy najprawdopodobniej utracisz dostęp do magazynu kluczy.

## <a name="overview"></a>Omówienie

Azure Key Vault automatycznie udostępnia funkcje ułatwiające zachowanie dostępności i Zapobieganie utracie danych. Utwórz kopię zapasową wpisów tajnych tylko wtedy, gdy masz krytyczne uzasadnienie biznesowe. Tworzenie kopii zapasowych wpisów tajnych w magazynie kluczy może wprowadzać wyzwania operacyjne, takie jak utrzymywanie wielu zestawów dzienników, uprawnień i kopii zapasowych po wygaśnięciu lub przeniesieniu wpisów tajnych.

Key Vault utrzymuje dostępność w scenariuszach awarii i automatycznie przejdzie do trybu failover żądań do sparowanego regionu bez żadnej interwencji użytkownika. Aby uzyskać więcej informacji, zobacz [Azure Key Vault dostępności i nadmiarowości](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).

Jeśli chcesz chronić przed przypadkowym lub złośliwym usunięciem kluczy tajnych, skonfiguruj funkcje ochrony przed usuwaniem i przeczyszczaniem w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [Azure Key Vault Omówienie usuwania nietrwałego](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview).

## <a name="limitations"></a>Ograniczenia

> [!IMPORTANT]
> Key Vault nie obsługuje wykonywania kopii zapasowych więcej niż 500 poprzednich wersji obiektu Key, Secret lub Certificate. Próba utworzenia kopii zapasowej obiektu Key, Secret lub Certificate może spowodować wystąpienie błędu. Nie można usunąć poprzednich wersji klucza, wpisu tajnego ani certyfikatu.

Key Vault nie udostępnia obecnie metody tworzenia kopii zapasowej całego magazynu kluczy w ramach jednej operacji. Każda próba użycia poleceń wymienionych w tym dokumencie do wykonania zautomatyzowanej kopii zapasowej magazynu kluczy może spowodować błędy i nie będzie obsługiwana przez firmę Microsoft lub Azure Key Vault zespół. 

Należy również wziąć pod uwagę następujące konsekwencje:

* Tworzenie kopii zapasowych wpisów tajnych, które mają wiele wersji może spowodować błędy limitu czasu.
* Kopia zapasowa tworzy migawkę do punktu w czasie. Wpisy tajne mogą zostać odnowione podczas tworzenia kopii zapasowej, co powoduje niezgodność kluczy szyfrowania.
* W przypadku przekroczenia limitów usługi magazynu kluczy dla żądań na sekundę Magazyn kluczy zostanie ograniczony i wykonywanie kopii zapasowej zakończy się niepowodzeniem.

## <a name="design-considerations"></a>Zagadnienia dotyczące projektowania

Podczas tworzenia kopii zapasowej obiektu magazynu kluczy, takiego jak klucz tajny, klucz lub certyfikat, operacja tworzenia kopii zapasowej pobiera obiekt jako zaszyfrowany obiekt BLOB. Nie można odszyfrować tego obiektu blob poza platformą Azure. Aby uzyskać użyteczne dane z tego obiektu BLOB, należy przywrócić obiekt BLOB do magazynu kluczy w ramach tej samej subskrypcji platformy Azure i lokalizacji [geograficznej platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć kopię zapasową obiektu magazynu kluczy, musisz dysponować: 

* Uprawnienia na poziomie współautora lub wyższym w ramach subskrypcji platformy Azure.
* Magazyn kluczy głównych zawierający klucze tajne, dla których chcesz utworzyć kopię zapasową.
* Pomocniczy Magazyn kluczy, w którym zostaną przywrócone wpisy tajne.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Tworzenie kopii zapasowej i przywracanie danych z Azure Portal

Wykonaj kroki opisane w tej sekcji, aby utworzyć kopię zapasową i przywrócić obiekty przy użyciu Azure Portal.

### <a name="back-up"></a>Wykonywanie kopii zapasowej

1. Przejdź do witryny Azure Portal.
2. Wybierz magazyn kluczy.
3. Przejdź do obiektu (tajny, klucz lub certyfikat), dla którego chcesz utworzyć kopię zapasową.

    ![Zrzut ekranu przedstawiający miejsce wybrania ustawienia klucze i obiektu w magazynie kluczy.](../media/backup-1.png)

4. Wybierz obiekt.
5. Wybierz pozycję **Pobierz kopię zapasową**.

    ![Zrzut ekranu pokazujący, gdzie wybrać przycisk Pobierz kopię zapasową w magazynie kluczy.](../media/backup-2.png)
    
6. Kliknij pozycję **Pobierz**.

    ![Zrzut ekranu przedstawiający miejsce wybrania przycisku pobierania w magazynie kluczy.](../media/backup-3.png)
    
7. Przechowuj zaszyfrowany obiekt BLOB w bezpiecznej lokalizacji.

### <a name="restore"></a>Przywracanie

1. Przejdź do witryny Azure Portal.
2. Wybierz magazyn kluczy.
3. Przejdź do typu obiektu (klucz tajny, klucz lub certyfikat), który chcesz przywrócić.
4. Wybierz pozycję **Przywróć kopię zapasową**.

    ![Zrzut ekranu przedstawiający miejsce wybrania opcji Przywróć kopię zapasową w magazynie kluczy.](../media/backup-4.png)
    
5. Przejdź do lokalizacji, w której zapisano zaszyfrowany obiekt BLOB.
6. Wybierz przycisk **OK**.

## <a name="back-up-and-restore-from-the-azure-cli"></a>Tworzenie kopii zapasowej i przywracanie danych przy użyciu interfejsu wiersza polecenia platformy Azure

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

## <a name="next-steps"></a>Następne kroki

Włącz [Rejestrowanie i monitorowanie](https://docs.microsoft.com/azure/key-vault/general/logging) dla Key Vault.
