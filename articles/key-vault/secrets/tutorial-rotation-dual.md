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
ms.openlocfilehash: e7e63ea56edc2b76383ee4c034fd39dd8b8259c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786008"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatyzowanie obrotu wpisu tajnego dla zasobów, które mają dwa zestawy poświadczeń uwierzytelniania

Najlepszym sposobem na uwierzytelnianie w usługach platformy Azure jest użycie [tożsamości zarządzanej](../general/authentication.md), ale istnieje kilka scenariuszy, w których nie jest to opcja. W tych przypadkach są używane klucze dostępu lub hasła. Często należy obrócić klucze dostępu i hasła.

W tym samouczku pokazano, jak zautomatyzować okresowe rotacje wpisów tajnych dla baz danych i usług korzystających z dwóch zestawów poświadczeń uwierzytelniania. W tym samouczku pokazano, jak obrócić klucze konta usługi Azure Storage przechowywane w Azure Key Vault jako wpisy tajne. Zostanie użyta funkcja wyzwalana przez Azure Event Grid powiadomienia. 

> [!NOTE]
> Klucze konta magazynu mogą być automatycznie zarządzane w Key Vault w przypadku dostarczania tokenów sygnatury dostępu współdzielonego dla delegowanego dostępu do konta magazynu. Istnieją usługi, które wymagają parametrów połączenia konta magazynu z kluczami dostępu. W tym scenariuszu zalecamy rozwiązanie tego problemu.

Oto rozwiązanie rotacji opisane w tym samouczku: 

![Diagram przedstawiający rozwiązanie obrotu.](../media/secrets/rotation-dual/rotation-diagram.png)

W tym rozwiązaniu Azure Key Vault magazynuje poszczególne klucze dostępu do konta magazynu jako wersje tego samego klucza tajnego, przemienne między klucz podstawowy i pomocniczy w kolejnych wersjach. Jeśli jeden klucz dostępu jest przechowywany w najnowszej wersji wpisu tajnego, klucz alternatywny zostanie ponownie wygenerowany i dodany do Key Vault jako nowa Najnowsza wersja wpisu tajnego. Rozwiązanie udostępnia cały cykl rotacji aplikacji w celu odświeżenia do najnowszego wygenerowanego klucza. 

1. 30 dni przed datą wygaśnięcia wpisu tajnego, Key Vault publikuje zdarzenie bliskiego wygaśnięcia, aby Event Grid.
1. Event Grid sprawdza subskrypcje zdarzeń i używa POST protokołu HTTP w celu wywołania punktu końcowego aplikacji funkcji subskrybowanego dla zdarzenia.
1. Aplikacja funkcji identyfikuje klucz alternatywny (nie najnowszy) i wywołuje konto magazynu w celu jego ponownego wygenerowania.
1. Aplikacja funkcji dodaje nowy ponownie wygenerowany klucz do Azure Key Vault jako nowa wersja klucza tajnego.

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Cloud Shell](https://shell.azure.com/)platformy Azure. Ten samouczek używa Cloud Shell portalu przy użyciu ENV programu PowerShell
* Azure Key Vault.
* Dwa konta usługi Azure Storage.

Tego linku wdrażania można użyć, jeśli nie masz istniejącego magazynu kluczy i istniejących kont magazynu:

[![Link, który jest wdrażany na platformie Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**. Nazwij grupę **vaultrotation** , a następnie wybierz przycisk **OK**.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

    ![Zrzut ekranu pokazujący sposób tworzenia grupy zasobów.](../media/secrets/rotation-dual/dual-rotation-1.png)

Teraz masz Magazyn kluczy i dwa konta magazynu. Tę konfigurację można sprawdzić w interfejsie wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az resource list -o table -g vaultrotation
```

Wynik będzie wyglądać następująco:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Tworzenie i wdrażanie funkcji rotacji kluczy

Następnie utworzysz aplikację funkcji z tożsamością zarządzaną przez system, a także z innymi wymaganymi składnikami. Wdrożono również funkcję rotacji kluczy konta magazynu.

Funkcja rotacji aplikacji funkcji wymaga następujących składników i konfiguracji:
- Plan Azure App Service
- Konto magazynu do zarządzania wyzwalaczami aplikacji funkcji
- Zasady dostępu do uzyskiwania dostępu do wpisów tajnych w Key Vault
- Rola usługi operatora kluczy konta magazynu przypisana do aplikacji funkcji, aby mogła ona uzyskać dostęp do kluczy dostępu do konta magazynu
- Funkcja rotacji kluczy z wyzwalaczem zdarzenia i wyzwalaczem HTTP (obrót na żądanie)
- Subskrypcja zdarzeń Event Grid dla zdarzenia **SecretNearExpiry**

1. Wybierz link wdrożenie szablonu platformy Azure: 

   [![Link wdrażania szablonu platformy Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Na liście **Grupa zasobów** wybierz pozycję **vaultrotation**.
1. W polu **RG konta magazynu** wprowadź nazwę grupy zasobów, w której znajduje się konto magazynu. Jeśli konto magazynu znajduje się już w tej samej grupie zasobów, w której zostanie wdrożona Funkcja rotacji kluczy, Zachowaj wartość domyślną **[resourceName (). Name]** .
1. W polu **nazwa konta magazynu** wprowadź nazwę konta magazynu, które zawiera klucze dostępu do obrócenia. Jeśli używasz konta magazynu utworzonego w [wymaganiach wstępnych](#prerequisites), Zachowaj wartość domyślną **[concat (Resources (). Name, "Storage")]** .
1. W **Key Vault RG** wprowadź nazwę grupy zasobów, w której znajduje się Twój Magazyn kluczy. Zachowaj wartość domyślną **[resourceName (). Name]** , Jeśli Twój Magazyn kluczy już istnieje w tej samej grupie zasobów, w której zostanie wdrożona Funkcja rotacji kluczy.
1. W polu **nazwa Key Vault** wprowadź nazwę magazynu kluczy. Jeśli używasz magazynu kluczy utworzonego w [wymaganiach wstępnych](#prerequisites), Zachowaj wartość domyślną **[concat (Resources (). Name, "-KV")]** .
1. W polu **Typ planu App Service** wybierz pozycję plan hostingu. **Plan Premium** jest wymagany tylko wtedy, gdy Magazyn kluczy znajduje się za zaporą.
1. W polu **nazwa aplikacja funkcji** wprowadź nazwę aplikacji funkcji.
1. W polu **Nazwa wpisu tajnego** wprowadź nazwę wpisu tajnego, w którym będą przechowywane klucze dostępu.
1. W polu **adres URL repozytorium** wprowadź lokalizację kodu funkcji w witrynie GitHub. W tym samouczku można użyć programu **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

   ![Zrzut ekranu pokazujący sposób tworzenia i wdrażania funkcji.](../media/secrets/rotation-dual/dual-rotation-2.png)

Po wykonaniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów, aplikację funkcji i Application Insights. Po zakończeniu wdrażania zostanie wyświetlona strona:

   ![Zrzut ekranu pokazujący kompletną stronę wdrożenia.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Jeśli wystąpi błąd, możesz wybrać pozycję **Wdróż** ponownie, aby zakończyć wdrażanie składników.


Szablony i kod wdrożenia dla funkcji rotacji można znaleźć w [przykładach platformy Azure](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Dodaj klucze dostępu do konta magazynu do Key Vault

Najpierw ustaw zasady dostępu, aby udzielić uprawnień do zarządzania wpisami **tajnymi** dla podmiotu zabezpieczeń użytkownika:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```

Jako wartość możesz teraz utworzyć nowy klucz tajny z kluczem dostępu konta magazynu. Do wpisów tajnych należy również dodać identyfikator zasobu konta magazynu, tajny okres ważności i identyfikator klucza, aby funkcja rotacji mogła ponownie wygenerować klucz na koncie magazynu.

Określ identyfikator zasobu konta magazynu. Tę wartość można znaleźć we `id` właściwości.

```azurecli
az storage account show -n vaultrotationstorage
```

Wyświetl listę kluczy dostępu do konta magazynu, aby uzyskać wartości klucza:

```azurecli
az storage account keys list -n vaultrotationstorage 
```

Dodaj klucz tajny do magazynu kluczy z datą wygaśnięcia ustawioną na jutro, okres ważności dla 60 dni i identyfikator zasobu konta magazynu. Uruchom to polecenie, używając pobranych wartości dla `key1Value` i `storageAccountResourceId` :

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Powyżej wpisu tajnego zostanie wyzwolone `SecretNearExpiry` zdarzenie w ciągu kilku minut. To zdarzenie spowoduje wyzwolenie funkcji, aby obrócić wpis tajny o ważności ustawiony na 60 dni. W tej konfiguracji zdarzenie "SecretNearExpiry" zostanie wyzwolone co 30 dni (30 dni przed wygaśnięciem), a funkcja rotacji przejdzie alternatywny obrót między Klucz1 i klucz2.

Można sprawdzić, czy klucze dostępu zostały ponownie wygenerowane, pobierając klucz konta magazynu i klucz tajny Key Vault i porównując je.

Użyj tego polecenia, aby uzyskać informacje o kluczu tajnym:
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```

Zwróć uwagę, że `CredentialId` jest ona aktualizowana do alternatywnego `keyName` i `value` wygenerowanego ponownie:

![Zrzut ekranu, który pokazuje dane wyjściowe z wpisu tajnego magazynu kluczy z, Pokaż polecenie dla pierwszego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-4.png)

Pobierz klucze dostępu, aby porównać wartości:
```azurecli
az storage account keys list -n vaultrotationstorage 
```
Należy zauważyć, że `value` klucz jest taki sam jak wpis tajny w magazynie kluczy:

![Zrzut ekranu pokazujący dane wyjściowe polecenia listy kluczy konta magazynu z na pierwszym koncie magazynu.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Dodawanie kont magazynu do rotacji

Możesz użyć tej samej aplikacji funkcji, aby obrócić klucze dla wielu kont magazynu. 

Aby dodać klucze konta magazynu do istniejącej funkcji rotacji, potrzebne są:
- Rola usługi operatora kluczy konta magazynu przypisana do aplikacji funkcji, aby mogła ona uzyskać dostęp do kluczy dostępu do konta magazynu.
- Subskrypcja zdarzeń Event Grid dla zdarzenia **SecretNearExpiry** .

1. Wybierz link wdrożenie szablonu platformy Azure: 

   [![Link wdrażania szablonu platformy Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Na liście **Grupa zasobów** wybierz pozycję **vaultrotation**.
1. W polu **RG konta magazynu** wprowadź nazwę grupy zasobów, w której znajduje się konto magazynu. Jeśli konto magazynu znajduje się już w tej samej grupie zasobów, w której zostanie wdrożona Funkcja rotacji kluczy, Zachowaj wartość domyślną **[resourceName (). Name]** .
1. W polu **nazwa konta magazynu** wprowadź nazwę konta magazynu, które zawiera klucze dostępu do obrócenia.
1. W **Key Vault RG** wprowadź nazwę grupy zasobów, w której znajduje się Twój Magazyn kluczy. Zachowaj wartość domyślną **[resourceName (). Name]** , Jeśli Twój Magazyn kluczy już istnieje w tej samej grupie zasobów, w której zostanie wdrożona Funkcja rotacji kluczy.
1. W polu **nazwa Key Vault** wprowadź nazwę magazynu kluczy.
1. W polu **nazwa aplikacja funkcji** wprowadź nazwę aplikacji funkcji.
1. W polu **Nazwa wpisu tajnego** wprowadź nazwę wpisu tajnego, w którym będą przechowywane klucze dostępu.
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

   ![Zrzut ekranu pokazujący sposób tworzenia dodatkowego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Dodaj inny klucz dostępu do konta magazynu do Key Vault

Określ identyfikator zasobu konta magazynu. Tę wartość można znaleźć we `id` właściwości.
```azurecli
az storage account show -n vaultrotationstorage2
```

Wyświetl listę kluczy dostępu do konta magazynu, aby uzyskać wartość klucz2:

```azurecli
az storage account keys list -n vaultrotationstorage2 
```

Dodaj klucz tajny do magazynu kluczy z datą wygaśnięcia ustawioną na jutro, okres ważności dla 60 dni i identyfikator zasobu konta magazynu. Uruchom to polecenie, używając pobranych wartości dla `key2Value` i `storageAccountResourceId` :

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Użyj tego polecenia, aby uzyskać informacje o kluczu tajnym:
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```

Zwróć uwagę, że `CredentialId` jest ona aktualizowana do alternatywnego `keyName` i `value` wygenerowanego ponownie:

![Zrzut ekranu, który pokazuje dane wyjściowe z wpisu tajnego magazynu kluczy z, Pokaż polecenie dla drugiego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-8.png)

Pobierz klucze dostępu, aby porównać wartości:
```azurecli
az storage account keys list -n vaultrotationstorage 
```

Należy zauważyć, że `value` klucz jest taki sam jak wpis tajny w magazynie kluczy:

![Zrzut ekranu pokazujący dane wyjściowe polecenia listy kluczy konta magazynu z dla drugiego konta magazynu.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Key Vault funkcje obrotu dla dwóch zestawów poświadczeń

Szablon funkcji rotacji dla dwóch zestawów poświadczeń i kilku gotowych do użycia funkcji:

- [Szablon projektu](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Pamięć podręczna Redis](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Konto magazynu](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> Powyższe funkcje rotacji są tworzone przez członka społeczności, a nie przez firmę Microsoft. Azure Functions Wspólnoty nie są obsługiwane w ramach jakichkolwiek programów lub usług pomocy technicznej firmy Microsoft i są udostępniane w postaci, w jakiej są dostępne bez żadnej rękojmi.

## <a name="next-steps"></a>Następne kroki

- Samouczek: [rotacja kluczy tajnych dla jednego zestawu poświadczeń](./tutorial-rotation.md)
- Przegląd: [monitorowanie Key Vault z Azure Event Grid](../general/event-grid-overview.md)
- Instrukcje: [Tworzenie pierwszej funkcji w Azure Portal](../../azure-functions/functions-get-started.md)
- Instrukcje: [otrzymywanie wiadomości e-mail po zmianie Key Vault tajnego](../general/event-grid-logicapps.md)
- Odwołanie: [schemat zdarzeń Azure Event Grid dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)