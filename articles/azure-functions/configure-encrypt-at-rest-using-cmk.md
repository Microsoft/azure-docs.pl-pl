---
title: Szyfrowanie źródła aplikacji w spoczynku
description: Zaszyfruj dane aplikacji w usłudze Azure Storage i wdaj je jako plik pakietu.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 71668bf27628bb2af2dfc7112d28ba10ecfdf9f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768817"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>Szyfrowanie danych aplikacji w spoczynku przy użyciu kluczy zarządzanych przez klienta

Szyfrowanie danych aplikacji funkcji w spoczynku wymaga konta usługi Azure Storage i Azure Key Vault. Te usługi są używane podczas uruchamiania aplikacji z pakietu wdrożeniowego.

  - [Usługa Azure Storage zapewnia szyfrowanie danych magazynowych.](../storage/common/storage-service-encryption.md) Możesz użyć kluczy dostarczanych przez system lub własnych kluczy zarządzanych przez klienta. Jest to miejsce, w którym dane aplikacji są przechowywane, gdy nie są uruchomione w aplikacji funkcji na platformie Azure.
  - [Uruchamianie z pakietu wdrożeniowego](run-functions-from-deployment-package.md) jest funkcją wdrażania App Service. Umożliwia wdrażanie zawartości witryny z konta usługi Azure Storage przy użyciu adresu URL sygnatury dostępu współdzielonego (SAS).
  - [Key Vault odwołania](../app-service/app-service-key-vault-references.md) są funkcją zabezpieczeń App Service. Umożliwia importowanie wpisów tajnych w czasie wykonywania jako ustawień aplikacji. Użyj tej funkcji, aby zaszyfrować adres URL sygnatury dostępu współdzielonego konta usługi Azure Storage.

## <a name="set-up-encryption-at-rest"></a>Konfigurowanie szyfrowania danych spoczynku

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najpierw utwórz [konto usługi Azure Storage i](../storage/common/storage-account-create.md) [zaszyfruj je przy użyciu kluczy zarządzanych przez klienta.](../storage/common/customer-managed-keys-overview.md) Po utworzeniu konta magazynu użyj Eksplorator usługi Azure Storage [do](../vs-azure-tools-storage-manage-with-storage-explorer.md) przekazywania plików pakietu.

Następnie użyj interfejsu Eksplorator usługi Storage, aby [wygenerować sygnaturę dostępu współdzielonego](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Zapisz ten adres URL sygnatury dostępu współdzielonego, który będzie używany później w celu zapewnienia bezpiecznego dostępu do pakietu wdrożeniowego w czasie wykonywania.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurowanie uruchamiania z pakietu z konta magazynu
  
Po przesłaniu pliku do usługi Blob Storage i użyciu adresu URL sygnatury dostępu współdzielonego dla pliku ustaw ustawienie aplikacji `WEBSITE_RUN_FROM_PACKAGE` na adres URL sygnatury dostępu współdzielonego. Poniższy przykład robi to za pomocą interfejsu wiersza polecenia platformy Azure:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Dodanie tego ustawienia aplikacji powoduje ponowne uruchomienie aplikacji funkcji. Po ponownym uruchomieniu aplikacji przejdź do jej strony i upewnij się, że aplikacja została prawidłowo uruchomiona przy użyciu pakietu wdrożeniowego. Jeśli aplikacja nie została prawidłowo uruchamiana, zobacz [przewodnik rozwiązywania](run-functions-from-deployment-package.md#troubleshooting)problemów z uruchamianiem z pakietu .

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Szyfrowanie ustawienia aplikacji przy użyciu Key Vault odwołań

Teraz możesz zastąpić wartość ustawienia aplikacji odwołaniem do Key Vault `WEBSITE_RUN_FROM_PACKAGE` adresu URL zakodowanym w sygnaturze dostępu współdzielonego. Dzięki temu adres URL sygnatury dostępu współdzielonego jest Key Vault, co zapewnia dodatkową warstwę zabezpieczeń.

1. Użyj następującego [`az keyvault create`](/cli/azure/keyvault#az_keyvault_create) polecenia, aby utworzyć Key Vault wystąpienie.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Postępuj [zgodnie z tymi instrukcjami, aby udzielić aplikacji dostępu](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) do magazynu kluczy:

1. Użyj następującego [`az keyvault secret set`](/cli/azure/keyvault/secret#az_keyvault_secret_set) polecenia, aby dodać zewnętrzny adres URL jako klucz tajny w magazynie kluczy:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Użyj następującego polecenia, aby utworzyć ustawienie aplikacji z wartością [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) `WEBSITE_RUN_FROM_PACKAGE` jako Key Vault odwołaniem do zewnętrznego adresu URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Będzie `<secret-version>` w danych wyjściowych poprzedniego `az keyvault secret set` polecenia.

Zaktualizowanie tego ustawienia aplikacji powoduje ponowne uruchomienie aplikacji funkcji. Po ponownym uruchomieniu aplikacji przejdź do jej strony, aby upewnić się, że została ona uruchomiona prawidłowo, korzystając z Key Vault referencyjnej.

## <a name="how-to-rotate-the-access-token"></a>Jak obrócić token dostępu

Najlepszym rozwiązaniem jest okresowe obracanie klucza sygnatury dostępu współdzielonego konta magazynu. Aby upewnić się, że aplikacja funkcji nie uzyskuje przypadkowo luźnego dostępu, należy również zaktualizować adres URL sygnatury dostępu współdzielonego w Key Vault.

1. Aby obrócić klucz sygnatury dostępu współdzielonego, przejdź do konta magazynu w Azure Portal. W **obszarze Ustawienia** Klucze  >  **dostępu** kliknij ikonę, aby obrócić klucz sygnatury dostępu współdzielonego.

1. Skopiuj nowy adres URL sygnatury dostępu współdzielonego i użyj następującego polecenia, aby ustawić zaktualizowany adres URL sygnatury dostępu współdzielonego w magazynie kluczy:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Zaktualizuj odwołanie do magazynu kluczy w ustawieniu aplikacji na nową wersję klucza tajnego:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Będzie `<secret-version>` w danych wyjściowych poprzedniego `az keyvault secret set` polecenia.

## <a name="how-to-revoke-the-function-apps-data-access"></a>Jak odwołać dostęp do danych aplikacji funkcji

Istnieją dwie metody odwoływania dostępu aplikacji funkcji do konta magazynu. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Rotacja klucza sygnatury dostępu współdzielonego dla konta usługi Azure Storage

Jeśli klucz sygnatury dostępu współdzielonego dla konta magazynu zostanie obrócony, aplikacja funkcji nie będzie już mieć dostępu do konta magazynu, ale będzie nadal działać z ostatnią pobraną wersją pliku pakietu. Uruchom ponownie aplikację funkcji, aby wyczyścić ostatnią pobraną wersję.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Usuwanie dostępu aplikacji funkcji do aplikacji Key Vault

Możesz odwołać dostęp aplikacji funkcji do danych witryny, wyłączając dostęp aplikacji funkcji do Key Vault. W tym celu usuń zasady dostępu dla tożsamości aplikacji funkcji. Jest to ta sama tożsamość, która została utworzona wcześniej podczas konfigurowania odwołań do magazynu kluczy.

## <a name="summary"></a>Podsumowanie

Pliki aplikacji są teraz szyfrowane w spoczynku na koncie magazynu. Po uruchamianiu aplikacji funkcji pobiera ona adres URL sygnatury dostępu współdzielonego z magazynu kluczy. Na koniec aplikacja funkcji ładuje pliki aplikacji z konta magazynu. 

Jeśli musisz odwołać dostęp aplikacji funkcji do konta magazynu, możesz odwołać dostęp do magazynu kluczy lub obrócić klucze konta magazynu, co unieważnia adres URL sygnatury dostępu współdzielonego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Czy jest naliczana dodatkowa opłata za uruchomienie aplikacji funkcji z pakietu wdrożeniowego?

Tylko koszt skojarzony z kontem usługi Azure Storage i wszelkie stosowne opłaty za ruch wychodzący.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Jak uruchomienie z pakietu wdrożeniowego wpływa na moją aplikację funkcji?

- Uruchomienie aplikacji z pakietu wdrożeniowego powoduje, `wwwroot/` że aplikacja jest tylko do odczytu. Aplikacja otrzymuje błąd podczas próby zapisu w tym katalogu.
- Formaty TAR i GZIP nie są obsługiwane.
- Ta funkcja nie jest zgodna z lokalną pamięcią podręczną.

## <a name="next-steps"></a>Następne kroki

- [Key Vault odwołań do App Service](../app-service/app-service-key-vault-references.md)
- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)