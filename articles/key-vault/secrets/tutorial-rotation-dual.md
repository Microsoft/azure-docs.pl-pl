---
title: Samouczek dotyczący rotacji zasobów z dwoma zestawami poświadczeń
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zautomatyzować rotację klucza tajnego dla zasobów, które używają dwóch zestawów poświadczeń uwierzytelniania.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 5da31d45e068f414c8afa38bcb46cdf1f790a9e5
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843281"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Automatyzowanie obrotu wpisu tajnego dla zasobów przy użyciu dwóch zestawów poświadczeń uwierzytelniania

Najlepszym sposobem na uwierzytelnianie w usługach platformy Azure jest użycie [tożsamości zarządzanej](../general/authentication.md), ale istnieje kilka scenariuszy, w których nie jest to opcja. W tych przypadkach są używane klucze dostępu lub hasła. Klucze dostępu i hasła powinny być często obracane.

W tym samouczku pokazano, jak zautomatyzować okresowe rotacje wpisów tajnych dla baz danych i usług korzystających z dwóch zestawów poświadczeń uwierzytelniania. W tym samouczku przeniesiesz klucze konta usługi Azure Storage przechowywane w Azure Key Vault jako wpisy tajne przy użyciu funkcji wyzwalanej przez Azure Event Grid powiadomienie. :

> [!NOTE]
> Klucze konta magazynu mogą być automatycznie zarządzane w Key Vault przez udostępnienie tokenów sygnatury dostępu współdzielonego dla delegowanego dostępu do konta magazynu. Istnieją usługi, które wymagają parametrów połączenia konta magazynu z kluczem dostępu i w tym scenariuszu jest to rozwiązanie zalecane.

![Diagram rozwiązania rotacji](../media/secrets/rotation-dual/rotation-diagram.png)

W powyższym rozwiązaniu Azure Key Vault przechowuje pojedyncze klucze dostępu do konta magazynu jako wersje tego samego klucza tajnego między kluczem podstawowym i pomocniczym w kolejnych wersjach. Ponieważ jeden klucz dostępu jest przechowywany w najnowszej wersji wpisu tajnego, klucz alternatywny zostaje ponownie wygenerowany i dodany do Key Vault jako nowa i Najnowsza wersja klucza tajnego. To rozwiązanie udostępnia cały cykl rotacji aplikacji w celu odświeżenia do najnowszego wygenerowanego klucza. 

1. 30 dni przed datą wygaśnięcia wpisu tajnego, Key Vault publikuje zdarzenie "bliskie wygaśnięcia", aby Event Grid.
1. Event Grid sprawdza subskrypcje zdarzeń i używa POST protokołu HTTP w celu wywołania punktu końcowego aplikacji funkcji subskrybowanego dla zdarzenia.
1. Aplikacja funkcji identyfikuje klucz alternatywny (inny niż najnowszy) i wywołuje konto magazynu w celu jego ponownego wygenerowania
1. Aplikacja funkcji dodaje nowy ponownie wygenerowany klucz do Azure Key Vault jako nowa wersja klucza tajnego.

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* W usłudze Azure Key Vault
* Dwa konta usługi Azure Storage

Jeśli nie masz istniejącego magazynu kluczy i kont magazynu, możesz użyć poniższego linku wdrażania:

[![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**. Nadaj grupie nazwę **akvrotation** i kliknij przycisk **OK**.
1. Wybierz pozycję **Przeglądanie+tworzenie**.
1. Wybierz pozycję **Utwórz**

    ![Tworzenie grupy zasobów](../media/secrets/rotation-dual/dual-rotation-1.png)

Teraz masz Magazyn kluczy i dwa konta magazynu. Tę konfigurację można sprawdzić w interfejsie wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az resource list -o table -g akvrotation
```

Wynik będzie wyglądać następująco:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Utwórz i Wdróż funkcję rotacji klucza konta magazynu
> [!IMPORTANT]
> Poniższy szablon wymaga Key Vault, konto usługi Azure Storage i funkcja platformy Azure, aby znajdować się w tej samej grupie zasobów

Następnie Utwórz aplikację funkcji z tożsamością zarządzaną przez system, a także z innymi wymaganymi składnikami i Wdróż funkcje rotacji kluczy konta magazynu

Funkcje rotacji aplikacji funkcji wymagają następujących składników i konfiguracji:
- Plan Azure App Service
- Konto magazynu wymagane do zarządzania wyzwalaczem aplikacji funkcji
- Zasady dostępu do uzyskiwania dostępu do wpisów tajnych w Key Vault
- Przypisywanie roli usługi operatora kluczy konta magazynu do funkcji aplikacji w celu uzyskania dostępu do kluczy dostępu do konta magazynu
- Funkcje rotacji kluczy konta magazynu z wyzwalaczem zdarzeń i wyzwalaczem http (obrót na żądanie)
- Subskrypcja zdarzeń EventGrid dla zdarzenia **SecretNearExpiry**

1. Wybierz link wdrożenie szablonu platformy Azure: 

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Na liście **Grupa zasobów** wybierz pozycję **akvrotation**.
1. W polu **nazwa konta magazynu**wpisz nazwę konta magazynu z kluczami dostępu, aby obrócić
1. W polu **nazwa Key Vault**wpisz nazwę magazynu kluczy
1. W polu **nazwa aplikacja funkcji**wpisz nazwę aplikacji funkcji
1. W polu **Nazwa wpisu tajnego**wpisz nazwę wpisu tajnego, w którym będą przechowywane klucze dostępu
1. W **adresie URL repozytorium**wpisz kod funkcji Lokalizacja usługi GitHub ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** )
1. Wybierz pozycję **Przeglądanie+tworzenie**.
1. Wybierz pozycję **Utwórz**

   ![Przejrzyj i Utwórz pierwsze konto magazynu](../media/secrets/rotation-dual/dual-rotation-2.png)

Po wykonaniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów, aplikację funkcji, Application Insights. Po ukończeniu wdrożenia powinien zostać wyświetlony poniższy ekran: ![ ukończono wdrażanie](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> W przypadku jakichkolwiek błędów można kliknąć pozycję **Wdróż** ponownie, aby zakończyć wdrażanie pozostałych składników.


Szablony wdrażania i kod funkcji rotacji można znaleźć w witrynie [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Dodaj klucz dostępu do konta magazynu do Key Vault

Najpierw ustaw zasady dostępu, aby przyznać użytkownikom uprawnienia do zarządzania wpisami *tajnymi* :

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Teraz możesz utworzyć nowy wpis tajny przy użyciu klucza dostępu konta magazynu jako wartość. Wymagany jest również identyfikator zasobu konta magazynu, tajny okres ważności i identyfikator klucza do dodania do wpisu tajnego, więc Funkcja rotacji może ponownie wygenerować klucz na koncie magazynu.

Pobierz identyfikator zasobu konta magazynu. Wartość można znaleźć w obszarze `id` Właściwość
```azurecli
az storage account show -n akvrotationstorage
```

Wyświetl listę kluczy dostępu do konta magazynu w celu pobrania wartości klucza

```azurecli
az storage account keys list -n akvrotationstorage 
```

Wypełnij pobrane wartości dla **key1Value** i **storageAccountResourceId**

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Tworzenie wpisu tajnego z krótką datą wygaśnięcia spowoduje opublikowanie `SecretNearExpiry` zdarzenia w ciągu kilku minut, co spowoduje wyzwolenie funkcji na rotację klucza tajnego.

Można sprawdzić, czy klucze dostępu są generowane ponownie, pobierając i porównując klucze konta magazynu oraz Key Vault klucz tajny.

Można wyświetlić informacje o kluczu tajnym przy użyciu poniższego polecenia:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Powiadomienie, które `CredentialId` zostało zaktualizowane do alternatywnej `keyName` i `value` jest ponownie generowane w wyniku polecenia ![ AZ Storage Storage Secret show dla pierwszego konta magazynu](../media/secrets/rotation-dual/dual-rotation-4.png)

Pobierz klucze dostępu, aby sprawdzić poprawność wartości
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Dane wyjściowe polecenia AZ Storage account Keys list dla pierwszego konta magazynu](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Dodawanie dodatkowych kont magazynu do rotacji

Ta sama aplikacja funkcji może zostać użyta ponownie w celu przerotacjnia wielu kont magazynu. 

Dodawanie dodatkowych kluczy konta magazynu do rotacji do istniejącej funkcji wymaga:
- Przypisywanie roli usługi operatora kluczy konta magazynu do funkcji aplikacji w celu uzyskania dostępu do kluczy dostępu do konta magazynu
- Subskrypcja zdarzeń EventGrid dla zdarzenia **SecretNearExpiry**

1. Wybierz link wdrożenie szablonu platformy Azure: 

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Na liście **Grupa zasobów** wybierz pozycję **akvrotation**.
1. W polu **nazwa konta magazynu**wpisz nazwę konta magazynu z kluczami dostępu, aby obrócić
1. W polu **nazwa Key Vault**wpisz nazwę magazynu kluczy
1. W polu **nazwa aplikacja funkcji**wpisz nazwę aplikacji funkcji
1. W polu **Nazwa wpisu tajnego**wpisz nazwę wpisu tajnego, w którym będą przechowywane klucze dostępu
1. Wybierz pozycję **Przeglądanie+tworzenie**.
1. Wybierz pozycję **Utwórz**

   ![Przejrzyj i Utwórz drugie konto magazynu](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Dodaj inny klucz dostępu do konta magazynu do Key Vault

Pobierz identyfikator zasobu konta magazynu. Wartość można znaleźć w obszarze `id` Właściwość
```azurecli
az storage account show -n akvrotationstorage2
```

Wyświetl listę kluczy dostępu do konta magazynu w celu pobrania wartości klucz2

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Wypełnij pobrane wartości dla **key2Value** i **storageAccountResourceId**

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Pokaż informacje o kluczu tajnym przy użyciu poniższego polecenia:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Powiadomienie, które `CredentialId` zostało zaktualizowane do alternatywnej `keyName` i `value` jest ponownie generowane w wyniku polecenia ![ AZ Storage Storage Secret show dla drugiego konta magazynu](../media/secrets/rotation-dual/dual-rotation-8.png)

Pobierz klucze dostępu, aby sprawdzić poprawność wartości
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Dane wyjściowe polecenia AZ Storage account Keys list dla drugiego konta magazynu](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Dostępne Key Vault podwójne funkcje rotacji poświadczeń

- [Konto magazynu](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Pamięć podręczna Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Dowiedz się więcej
- Przegląd: [monitorowanie Key Vault z Azure Event Grid](../general/event-grid-overview.md)
- Instrukcje: [Tworzenie pierwszej funkcji w Azure Portal](../../azure-functions/functions-create-first-azure-function.md)
- Instrukcje: [otrzymywanie wiadomości e-mail po zmianie wpisu tajnego magazynu kluczy](../general/event-grid-logicapps.md)
- [Schemat zdarzeń Azure Event Grid dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
