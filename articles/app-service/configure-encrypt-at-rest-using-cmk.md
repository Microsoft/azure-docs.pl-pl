---
title: Szyfruj Źródło aplikacji w stanie spoczynku
description: Dowiedz się, jak szyfrować dane aplikacji w usłudze Azure Storage i wdrażać je jako plik pakietu.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 62bb1e51bada155b98fe46740662b86b753d44fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90978483"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Szyfrowanie w spoczynku przy użyciu kluczy zarządzanych przez klienta

Szyfrowanie danych aplikacji sieci Web w spoczynku wymaga konta usługi Azure Storage i Azure Key Vault. Te usługi są używane podczas uruchamiania aplikacji z pakietu wdrożeniowego.

  - [Usługa Azure Storage udostępnia szyfrowanie w stanie spoczynku](../storage/common/storage-service-encryption.md). Możesz użyć kluczy dostarczonych przez system lub własnych, zarządzanych przez klienta kluczy. Jest to miejsce, w którym dane aplikacji są przechowywane, gdy nie są uruchomione w aplikacji sieci Web na platformie Azure.
  - [Uruchamianie z pakietu wdrożeniowego](deploy-run-package.md) jest funkcją wdrażania App Service. Dzięki temu można wdrożyć zawartość witryny na koncie usługi Azure Storage przy użyciu adresu URL sygnatury dostępu współdzielonego (SAS).
  - [Odwołania Key Vault](app-service-key-vault-references.md) są funkcją zabezpieczeń App Service. Umożliwia importowanie wpisów tajnych w środowisku uruchomieniowym jako ustawienia aplikacji. Służy do szyfrowania adresu URL sygnatury dostępu współdzielonego konta usługi Azure Storage.

## <a name="set-up-encryption-at-rest"></a>Konfigurowanie szyfrowania w stanie spoczynku

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najpierw [Utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md) i [Zaszyfruj je za pomocą kluczy zarządzanych przez klienta](../storage/common/encryption-customer-managed-keys.md). Po utworzeniu konta magazynu Użyj [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) do przekazania plików pakietu.

Następnie użyj Eksplorator usługi Storage do [wygenerowania sygnatury dostępu WSPÓŁdzielonego](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Zapisz ten adres URL sygnatury dostępu współdzielonego, który będzie używany później do włączania bezpiecznego dostępu do pakietu wdrożeniowego w czasie wykonywania.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurowanie uruchamiania z pakietu na koncie magazynu
  
Po przekazaniu pliku do usługi BLOB Storage i uzyskaniu adresu URL sygnatury dostępu współdzielonego dla pliku, należy ustawić `WEBSITE_RUN_FROM_PACKAGE` ustawienie aplikacji na adres URL sygnatury dostępu współdzielonego. W poniższym przykładzie jest to możliwe za pomocą interfejsu wiersza polecenia platformy Azure:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Dodanie tego ustawienia aplikacji powoduje ponowne uruchomienie aplikacji sieci Web. Po ponownym uruchomieniu aplikacji przejdź do niej i upewnij się, że aplikacja została uruchomiona prawidłowo przy użyciu pakietu wdrożeniowego. Jeśli aplikacja nie została uruchomiona poprawnie, zapoznaj się [z przewodnikiem rozwiązywania problemów z pakietem](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Szyfrowanie ustawienia aplikacji przy użyciu Key Vault odwołań

Teraz można zastąpić wartość `WEBSITE_RUN_FROM_PACKAGE` Ustawienia aplikacji Key Vault odwołaniem do adresu URL zakodowanego za pomocą sygnatury dostępu współdzielonego. Dzięki temu adres URL sygnatury dostępu współdzielonego jest szyfrowany w Key Vault, co zapewnia dodatkową warstwę zabezpieczeń.

1. Użyj następującego [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) polecenia, aby utworzyć wystąpienie Key Vault.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Postępuj zgodnie [z tymi instrukcjami, aby udzielić aplikacji dostępu](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) do magazynu kluczy:

1. Użyj poniższego [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) polecenia, aby dodać zewnętrzny adres URL jako wpis tajny w magazynie kluczy:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Użyj następującego [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) polecenia, aby utworzyć `WEBSITE_RUN_FROM_PACKAGE` ustawienie aplikacji z wartością jako odwołanie Key Vault do zewnętrznego adresu URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Wartość `<secret-version>` będzie znajdować się w danych wyjściowych poprzedniego `az keyvault secret set` polecenia.

Aktualizacja tego ustawienia aplikacji powoduje ponowne uruchomienie aplikacji sieci Web. Po ponownym uruchomieniu aplikacji przejdź do niej, aby upewnić się, że została uruchomiona poprawnie przy użyciu odwołania Key Vault.

## <a name="how-to-rotate-the-access-token"></a>Jak obrócić token dostępu

Najlepszym rozwiązaniem jest okresowe obracanie klucza sygnatury dostępu współdzielonego konta magazynu. Aby upewnić się, że aplikacja sieci Web nie ma przypadkowo swobodnego dostępu, musisz również zaktualizować adres URL sygnatury SAS w Key Vault.

1. Obróć klucz SAS, przechodząc do konta magazynu w Azure Portal. W obszarze **Ustawienia**  >  **klucze dostępu**kliknij ikonę, aby obrócić klucz SAS.

1. Skopiuj nowy adres URL sygnatury dostępu współdzielonego i użyj następującego polecenia, aby ustawić zaktualizowany adres URL sygnatury dostępu współdzielonego w magazynie kluczy:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Zaktualizuj odwołanie do magazynu kluczy w ustawieniach aplikacji do nowej wersji klucza tajnego:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Wartość `<secret-version>` będzie znajdować się w danych wyjściowych poprzedniego `az keyvault secret set` polecenia.

## <a name="how-to-revoke-the-web-apps-data-access"></a>Jak odwołać dostęp do danych aplikacji sieci Web

Istnieją dwie metody odwołania dostępu aplikacji sieci Web do konta magazynu. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Obróć klucz sygnatury dostępu współdzielonego dla konta usługi Azure Storage

Jeśli klucz sygnatury dostępu współdzielonego dla konta magazynu zostanie obrócony, aplikacja internetowa nie będzie już miała dostępu do konta magazynu, ale będzie nadal działać z ostatnią pobieraną wersją pliku pakietu. Uruchom ponownie aplikację internetową, aby wyczyścić ostatnią pobraną wersję.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Usuń dostęp aplikacji sieci Web do Key Vault

Możesz odwołać dostęp aplikacji sieci Web do danych lokacji, wyłączając dostęp aplikacji sieci Web do Key Vault. Aby to zrobić, Usuń zasady dostępu dla tożsamości aplikacji sieci Web. Jest to taka sama tożsamość, która została utworzona wcześniej podczas konfigurowania odwołań do magazynu kluczy.

## <a name="summary"></a>Podsumowanie

Pliki aplikacji są teraz szyfrowane na koncie magazynu. Po uruchomieniu aplikacja internetowa Pobiera adres URL sygnatury dostępu współdzielonego z magazynu kluczy. Na koniec aplikacja sieci Web ładuje pliki aplikacji z konta magazynu. 

Jeśli musisz odwołać dostęp aplikacji sieci Web do konta magazynu, możesz odwołać dostęp do magazynu kluczy lub obrócić klucze konta magazynu, które unieważnią adres URL sygnatury dostępu współdzielonego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Czy jest dostępna dodatkowa opłata za uruchamianie aplikacji sieci Web z pakietu wdrożeniowego?

Tylko koszt skojarzony z kontem usługi Azure Storage i wszelkimi obowiązującymi opłatami za ruch wychodzący.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Jak działa z pakietu wdrożeniowego, ma wpływ na moją aplikację sieci Web?

- Uruchamianie aplikacji z pakietu wdrożeniowego powoduje, że `wwwroot/` tylko do odczytu. Aplikacja otrzymuje błąd podczas próby zapisu w tym katalogu.
- Formaty TAR i GZIP nie są obsługiwane.
- Ta funkcja jest niezgodna z lokalną pamięcią podręczną.

## <a name="next-steps"></a>Następne kroki

- [Key Vault odwołań dla App Service](app-service-key-vault-references.md)
- [Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md)
