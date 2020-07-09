---
title: Azure Key Vault kopia zapasowa | Microsoft Docs
description: Ten dokument pomaga utworzyć kopię zapasową wpisu tajnego, klucza lub certyfikatu przechowywanego w Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147833"
---
# <a name="azure-key-vault-backup"></a>Azure Key Vault kopia zapasowa

W tym dokumencie pokazano, jak wykonać kopię zapasową poszczególnych wpisów tajnych, kluczy i certyfikatów przechowywanych w magazynie kluczy. Ta kopia zapasowa jest przeznaczona do udostępnienia kopii w trybie offline wszystkich wpisów tajnych w przypadku, gdy najprawdopodobniej utracisz dostęp do magazynu kluczy.

## <a name="overview"></a>Omówienie

Key Vault automatycznie udostępnia kilka funkcji w celu zapewnienia dostępności i zapobiegania utracie danych. Ta kopia zapasowa powinna zostać podjęta tylko wtedy, gdy istnieje krytyczne uzasadnienie biznesowe do utrzymania kopii zapasowych kluczy tajnych. Tworzenie kopii zapasowych wpisów tajnych w magazynie kluczy może prowadzić do dodatkowych wyzwań operacyjnych, takich jak obsługa wielu zestawów dzienników, uprawnień i kopii zapasowych, gdy klucze tajne wygasną lub obracają się.

Key Vault utrzymuje dostępność w scenariuszach awarii i automatycznie przejdzie do trybu failover żądań do sparowanego regionu bez interwencji użytkownika. Aby uzyskać więcej informacji, zobacz następujący link. [Azure Key Vault odzyskiwania po awarii](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

Key Vault chroni przed przypadkowym i złośliwym usuwaniem kluczy tajnych za poorednictwem ochrony trwałego usuwania i przeczyszczania. Jeśli chcesz chronić przed przypadkowym lub złośliwym usunięciem wpisów tajnych, skonfiguruj funkcje ochrony przed usuwaniem i przeczyszczaniem w magazynie kluczy. Aby uzyskać więcej informacji, zapoznaj się z następującym dokumentem. [Azure Key Vault odzyskiwanie](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Ograniczenia

Azure Key Vault obecnie nie obsługuje tworzenia kopii zapasowej całego magazynu kluczy w ramach jednej operacji. Wszystkie próby użycia poleceń wymienionych w tym dokumencie do wykonania zautomatyzowanej kopii zapasowej magazynu kluczy nie będą obsługiwane przez firmę Microsoft ani zespół Azure Key Vault.

Próba użycia poleceń przedstawionych w dokumencie poniżej w celu utworzenia automatyzacji niestandardowej może spowodować błędy.

* Tworzenie kopii zapasowych wpisów tajnych z wieloma wersjami może spowodować błędy limitu czasu.
* Kopia zapasowa tworzy migawkę do punktu w czasie. Wpisy tajne mogą zostać odnowione podczas wykonywania kopii zapasowej, co powoduje niezgodność kluczy szyfrowania.
* Przekroczenie limitów usługi magazynu kluczy dla żądań na sekundę spowoduje ograniczenie magazynu kluczy i spowoduje niepowodzenie tworzenia kopii zapasowej.

## <a name="design-considerations"></a>Zagadnienia dotyczące projektowania

Podczas wykonywania kopii zapasowej obiektu przechowywanego w magazynie kluczy (tajny, klucz lub certyfikat) operacja tworzenia kopii zapasowej pobiera obiekt jako zaszyfrowany obiekt BLOB. Nie można odszyfrować tego obiektu blob poza platformą Azure. Aby uzyskać użyteczne dane z tego obiektu BLOB, należy przywrócić obiekt BLOB do magazynu kluczy w ramach tej samej subskrypcji platformy Azure i lokalizacji geograficznej platformy Azure.
[Azure lokalizacje geograficzne](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Wymagania wstępne

* Poziom współautora lub wyższe uprawnienia do subskrypcji platformy Azure
* Magazyn kluczy głównych zawierający klucze tajne, dla których chcesz utworzyć kopię zapasową
* Pomocniczy Magazyn kluczy, w którym zostaną przywrócone wpisy tajne.

## <a name="back-up-and-restore-with-azure-portal"></a>Tworzenie kopii zapasowej i przywracanie za pomocą Azure Portal

### <a name="back-up"></a>Wykonywanie kopii zapasowej

1. Przejdź do witryny Azure Portal.
2. Wybierz magazyn kluczy.
3. Przejdź do obiektu (wpisu tajnego, klucza lub certyfikatu), dla którego chcesz utworzyć kopię zapasową.

    ![Obraz](../media/backup-1.png)

4. Wybierz obiekt.
5. Wybierz pozycję "Pobierz kopię zapasową"

    ![Obraz](../media/backup-2.png)
    
6. Kliknij przycisk Pobierz.

    ![Obraz](../media/backup-3.png)
    
7. Przechowuj zaszyfrowany obiekt BLOB w bezpiecznej lokalizacji.

### <a name="restore"></a>Przywracanie

1. Przejdź do witryny Azure Portal.
2. Wybierz magazyn kluczy.
3. Przejdź do typu obiektu (klucz tajny, klucz lub certyfikat), który chcesz przywrócić.
4. Wybierz pozycję "Przywróć kopię zapasową"

    ![Obraz](../media/backup-4.png)
    
5. Przejdź do lokalizacji, w której zapisano zaszyfrowany obiekt BLOB.
6. Wybierz pozycję "OK".

## <a name="back-up-and-restore-with-the-azure-cli"></a>Tworzenie kopii zapasowej i przywracanie za pomocą interfejsu wiersza polecenia platformy Azure

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Następne kroki

Włącz rejestrowanie i monitorowanie dla Azure Key Vault. [Rejestrowanie Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/logging)
