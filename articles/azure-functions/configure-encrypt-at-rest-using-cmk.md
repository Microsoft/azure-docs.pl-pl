---
title: Szyfruj Źródło aplikacji w stanie spoczynku
description: Szyfruj dane aplikacji w usłudze Azure Storage i wdróż je jako plik pakietu.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 6e4cf16118e748e3ee1d1ff4899730463565ec70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108035"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>Szyfruj dane aplikacji w stanie spoczynku przy użyciu kluczy zarządzanych przez klienta

Szyfrowanie danych aplikacji funkcji w spoczynku wymaga konta usługi Azure Storage i Azure Key Vault. Te usługi są używane podczas uruchamiania aplikacji z pakietu wdrożeniowego.

  - [Usługa Azure Storage udostępnia szyfrowanie w stanie spoczynku](../storage/common/storage-service-encryption.md). Możesz użyć kluczy dostarczonych przez system lub własnych, zarządzanych przez klienta kluczy. Jest to miejsce, w którym dane aplikacji są przechowywane, gdy nie są uruchomione w aplikacji funkcji na platformie Azure.
  - [Uruchamianie z pakietu wdrożeniowego](run-functions-from-deployment-package.md) jest funkcją wdrażania App Service. Dzięki temu można wdrożyć zawartość witryny na koncie usługi Azure Storage przy użyciu adresu URL sygnatury dostępu współdzielonego (SAS).
  - [Odwołania Key Vault](../app-service/app-service-key-vault-references.md) są funkcją zabezpieczeń App Service. Umożliwia importowanie wpisów tajnych w środowisku uruchomieniowym jako ustawienia aplikacji. Służy do szyfrowania adresu URL sygnatury dostępu współdzielonego konta usługi Azure Storage.

## <a name="set-up-encryption-at-rest"></a>Konfigurowanie szyfrowania w stanie spoczynku

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najpierw [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md) i [Zaszyfruj je za pomocą kluczy zarządzanych przez klienta](../storage/common/customer-managed-keys-overview.md). Po utworzeniu konta magazynu Użyj [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) do przekazania plików pakietu.

Następnie użyj Eksplorator usługi Storage do [wygenerowania sygnatury dostępu WSPÓŁdzielonego](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Zapisz ten adres URL sygnatury dostępu współdzielonego, który będzie używany później do włączania bezpiecznego dostępu do pakietu wdrożeniowego w czasie wykonywania.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurowanie uruchamiania z pakietu na koncie magazynu
  
Po przekazaniu pliku do usługi BLOB Storage i uzyskaniu adresu URL sygnatury dostępu współdzielonego dla pliku, należy ustawić `WEBSITE_RUN_FROM_PACKAGE` ustawienie aplikacji na adres URL sygnatury dostępu współdzielonego. W poniższym przykładzie jest to możliwe za pomocą interfejsu wiersza polecenia platformy Azure:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Dodanie tego ustawienia aplikacji spowoduje ponowne uruchomienie aplikacji funkcji. Po ponownym uruchomieniu aplikacji przejdź do niej i upewnij się, że aplikacja została uruchomiona prawidłowo przy użyciu pakietu wdrożeniowego. Jeśli aplikacja nie została uruchomiona poprawnie, zapoznaj się [z przewodnikiem rozwiązywania problemów z pakietem](run-functions-from-deployment-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Szyfrowanie ustawienia aplikacji przy użyciu Key Vault odwołań

Teraz można zastąpić wartość `WEBSITE_RUN_FROM_PACKAGE` Ustawienia aplikacji Key Vault odwołaniem do adresu URL zakodowanego za pomocą sygnatury dostępu współdzielonego. Dzięki temu adres URL sygnatury dostępu współdzielonego jest szyfrowany w Key Vault, co zapewnia dodatkową warstwę zabezpieczeń.

1. Użyj następującego [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) polecenia, aby utworzyć wystąpienie Key Vault.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Postępuj zgodnie [z tymi instrukcjami, aby udzielić aplikacji dostępu](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) do magazynu kluczy:

1. Użyj poniższego [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) polecenia, aby dodać zewnętrzny adres URL jako wpis tajny w magazynie kluczy:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Użyj następującego [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) polecenia, aby utworzyć `WEBSITE_RUN_FROM_PACKAGE` ustawienie aplikacji z wartością jako odwołanie Key Vault do zewnętrznego adresu URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Wartość `<secret-version>` będzie znajdować się w danych wyjściowych poprzedniego `az keyvault secret set` polecenia.

Aktualizacja tego ustawienia aplikacji powoduje ponowne uruchomienie aplikacji funkcji. Po ponownym uruchomieniu aplikacji przejdź do niej, aby upewnić się, że została uruchomiona poprawnie przy użyciu odwołania Key Vault.

## <a name="how-to-rotate-the-access-token"></a>Jak obrócić token dostępu

Najlepszym rozwiązaniem jest okresowe obracanie klucza sygnatury dostępu współdzielonego konta magazynu. Aby upewnić się, że aplikacja funkcji nie ma niezamierzonego dostępu, należy również zaktualizować adres URL SAS w Key Vault.

1. Obróć klucz SAS, przechodząc do konta magazynu w Azure Portal. W obszarze **Ustawienia**  >  **klucze dostępu** kliknij ikonę, aby obrócić klucz SAS.

1. Skopiuj nowy adres URL sygnatury dostępu współdzielonego i użyj następującego polecenia, aby ustawić zaktualizowany adres URL sygnatury dostępu współdzielonego w magazynie kluczy:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Zaktualizuj odwołanie do magazynu kluczy w ustawieniach aplikacji do nowej wersji klucza tajnego:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Wartość `<secret-version>` będzie znajdować się w danych wyjściowych poprzedniego `az keyvault secret set` polecenia.

## <a name="how-to-revoke-the-function-apps-data-access"></a>Jak odwołać dostęp do danych aplikacji funkcji

Istnieją dwie metody odwołania dostępu aplikacji funkcji do konta magazynu. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Obróć klucz sygnatury dostępu współdzielonego dla konta usługi Azure Storage

Jeśli klucz sygnatury dostępu współdzielonego dla konta magazynu zostanie obrócony, aplikacja funkcji nie będzie już miała dostępu do konta magazynu, ale będzie nadal działać z ostatnią pobieraną wersją pliku pakietu. Aby wyczyścić ostatnią pobraną wersję, uruchom ponownie aplikację funkcji.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Usuń dostęp aplikacji funkcji do Key Vault

Możesz odwołać dostęp aplikacji funkcji do danych lokacji, wyłączając dostęp aplikacji funkcji do Key Vault. W tym celu Usuń zasady dostępu dla tożsamości aplikacji funkcji. Jest to taka sama tożsamość, która została utworzona wcześniej podczas konfigurowania odwołań do magazynu kluczy.

## <a name="summary"></a>Podsumowanie

Pliki aplikacji są teraz szyfrowane na koncie magazynu. Po uruchomieniu aplikacja funkcji Pobiera adres URL sygnatury dostępu współdzielonego z magazynu kluczy. Na koniec aplikacja funkcji ładuje pliki aplikacji z konta magazynu. 

Jeśli musisz odwołać dostęp aplikacji funkcji do konta magazynu, możesz odwołać dostęp do magazynu kluczy lub obrócić klucze konta magazynu, które unieważnią adres URL sygnatury dostępu współdzielonego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Czy jest dostępna dodatkowa opłata za uruchamianie mojej aplikacji funkcji z pakietu wdrożeniowego?

Tylko koszt skojarzony z kontem usługi Azure Storage i wszelkimi obowiązującymi opłatami za ruch wychodzący.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Jak działa z pakietu wdrożeniowego, ma wpływ na moją aplikację funkcji?

- Uruchamianie aplikacji z pakietu wdrożeniowego powoduje, że `wwwroot/` tylko do odczytu. Aplikacja otrzymuje błąd podczas próby zapisu w tym katalogu.
- Formaty TAR i GZIP nie są obsługiwane.
- Ta funkcja jest niezgodna z lokalną pamięcią podręczną.

## <a name="next-steps"></a>Następne kroki

- [Key Vault odwołań dla App Service](../app-service/app-service-key-vault-references.md)
- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)