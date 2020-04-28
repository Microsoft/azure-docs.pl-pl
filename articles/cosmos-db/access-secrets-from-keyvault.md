---
title: Używanie Key Vault do przechowywania kluczy Azure Cosmos DB i uzyskiwania do nich dostępu
description: Użyj Azure Key Vault do przechowywania i uzyskiwania dostępu do Azure Cosmos DB parametrów połączenia, kluczy i punktów końcowych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 225221635f978e3d70cec4ce7e9d78d6b100b4fd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618770"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Zabezpieczanie kluczy usługi Azure Cosmos przy użyciu usługi Azure Key Vault 

>[!IMPORTANT]
> Zalecane rozwiązanie do uzyskiwania dostępu do kluczy Azure Cosmos DB polega na użyciu [tożsamości zarządzanej przypisanej do systemu](managed-identity-based-authentication.md). Jeśli usługa nie może korzystać z tożsamości zarządzanych, użyj [rozwiązania opartego na certyfikatach](certificate-based-authentication.md). Jeśli zarówno rozwiązanie tożsamości zarządzanej, jak i rozwiązanie oparte na certyfikatach nie spełniają Twoich potrzeb, użyj poniższego rozwiązania magazynu kluczy.

W przypadku korzystania z Azure Cosmos DB aplikacji można uzyskać dostęp do bazy danych, kolekcji i dokumentów przy użyciu punktu końcowego i klucza w pliku konfiguracji aplikacji.  Nie jest to jednak bezpieczne umieszczanie kluczy i adresów URL bezpośrednio w kodzie aplikacji, ponieważ są one dostępne w formacie zwykłego tekstu dla wszystkich użytkowników. Należy zapewnić dostępność punktu końcowego i kluczy, ale za pomocą bezpiecznego mechanizmu. Usługa Azure Key Vault pomaga w bezpiecznym przechowywaniu wpisów tajnych aplikacji i zarządzaniu nimi.

Następujące kroki są wymagane do przechowywania i odczytywania Azure Cosmos DB kluczy dostępu z Key Vault:

* Tworzenie magazynu kluczy  
* Dodaj klucze dostępu Azure Cosmos DB do Key Vault  
* Tworzenie aplikacji sieci Web platformy Azure  
* Zarejestruj aplikację & Udziel uprawnień do odczytu Key Vault  


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).  
2. Wybierz pozycję **Utwórz zasób > zabezpieczenia > Key Vault**.  
3. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:  
   * **Nazwa:** Podaj unikatową nazwę Key Vault.  
   * **Subskrypcja:** Wybierz subskrypcję, która będzie używana.  
   * W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.  
   * W menu rozwijanym Lokalizacja wybierz lokalizację.  
   * Pozostaw wartości domyślne innych opcji.  
4. Po podaniu powyższych informacje wybierz przycisk **Utwórz**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Dodaj do Key Vault klucze dostępu Azure Cosmos DB.
1. Przejdź do Key Vault utworzonego w poprzednim kroku, Otwórz kartę wpisy **tajne** .  
2. Wybierz pozycję **+ Generuj/Importuj**, 

   * Wybierz opcję **ręczny** dla **opcji przekazywania**.
   * Podaj **nazwę** wpisu tajnego
   * Podaj parametry połączenia konta Cosmos DB w polu **wartość** . Następnie wybierz pozycję **Utwórz**.

   ![Utwórz klucz tajny](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Po utworzeniu wpisu tajnego, otwórz go i skopiuj identyfikator tajny * *, który jest w następującym formacie. Ten identyfikator zostanie użyty w następnej sekcji. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Tworzenie aplikacji sieci Web platformy Azure

1. Utwórz aplikację sieci Web platformy Azure lub aplikację można pobrać z [repozytorium GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Jest to prosta aplikacja MVC.  

2. Rozpakuj pobrane aplikacje i Otwórz plik **HomeController.cs** . Zaktualizuj identyfikator wpisu tajnego w następującym wierszu:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Zapisz** plik, **Skompiluj** rozwiązanie.  
4. Następnie wdróż aplikację na platformie Azure. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Publikuj**. Utwórz nowy profil usługi App Service (możesz nazwać aplikację WebAppKeyVault1) i wybierz pozycję **Publikuj**.   

5. Po wdrożeniu aplikacji. W Azure Portal przejdź do wdrożonej aplikacji sieci Web i Włącz **tożsamość usługi zarządzanej** dla tej aplikacji.  

   ![Tożsamość usługi zarządzanej](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Jeśli aplikacja zostanie uruchomiona teraz, zostanie wyświetlony następujący błąd, ponieważ nie otrzymasz żadnych uprawnień do tej aplikacji w Key Vault.

![Aplikacja wdrożona bez dostępu](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Zarejestruj aplikację & Udziel uprawnień do odczytu Key Vault

W tej sekcji rejestrujesz aplikację przy użyciu Azure Active Directory i nadajesz uprawnienia aplikacji do odczytu Key Vault. 

1. Przejdź do Azure Portal, Otwórz **Key Vault** utworzony w poprzedniej sekcji.  

2. Otwórz przystawkę **zasady dostępu**, wybierz pozycję **+ Dodaj nowy** Znajdź wdrożoną aplikację sieci Web, wybierz pozycję uprawnienia i wybierz pozycję **OK**.  

   ![Dodawanie zasad dostępu](./media/access-secrets-from-keyvault/add-access-policy.png)

Teraz, po uruchomieniu aplikacji, można odczytać klucz tajny z Key Vault.

![Aplikacja wdrożona przy użyciu klucza tajnego](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Analogicznie, możesz dodać użytkownika, aby uzyskać dostęp do magazynu kluczy. Musisz dodać siebie do Key Vault, wybierając pozycję **zasady dostępu** , a następnie przyznaj wszystkim uprawnienia wymagane do uruchomienia aplikacji w programie Visual Studio. Gdy ta aplikacja jest uruchomiona na komputerze stacjonarnym, zajmie Twoją tożsamość.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zaporę dla Azure Cosmos DB Zobacz artykuł [pomocy technicznej zapory](firewall-support.md) .
* Aby skonfigurować punkt końcowy usługi sieci wirtualnej, zobacz [bezpieczny dostęp przy użyciu punktu końcowego usługi wirtualnej](vnet-service-endpoint.md) .
