---
title: Zarządzanie usługą Azure Key Vault przy użyciu interfejsu wiersza polecenia — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Ten artykuł służy do automatyzacji typowych zadań w aplikacji Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 127f0cdfc8cecf9789a68210f4b7ce1927333cc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422585"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Zarządzanie magazynem kluczy przy użyciu interfejsu wiersza polecenia platformy Azure 

W tym artykule opisano, jak rozpocząć pracę z usługą Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure.  Możesz zobaczyć informacje na temat:

- Jak utworzyć utwardzony kontener (magazyn) na platformie Azure
- Dodawanie klucza, klucza tajnego lub certyfikatu do magazynu kluczy
- Rejestrowanie aplikacji w usłudze Azure Active Directory
- Autoryzowanie aplikacji do używania klucza lub klucza tajnego
- Ustawianie zaawansowanych zasad dostępu magazynu kluczy
- Praca ze sprzętowymi modułami zabezpieczeń (HSM)
- Usuwanie magazynu kluczy i skojarzonych kluczy i wpisów tajnych
- Różne polecenia interfejsu wiersza polecenia platformy Azure dla różnych platform


Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Ten artykuł nie zawiera instrukcji dotyczących sposobu pisania aplikacji platformy Azure, który zawiera jeden z kroków, który pokazuje, jak autoryzować aplikację do używania klucza lub klucza tajnego w magazynie kluczy.
>

Aby zapoznać się z omówieniem usługi Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](overview.md) Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć poleceń interfejsu wiersza polecenia platformy Azure w tym artykule, musisz mieć następujące elementy:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby zainstalować najnowszą wersję, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Aplikacja, która zostanie skonfigurowana do używania klucza lub hasła, które można utworzyć w tym artykule. Przykładowa aplikacja jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Instrukcje można znaleźć w dołączonym pliku Readme.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Uzyskiwanie pomocy w zakresie interfejsu wiersza polecenia platformy Azure dla różnych platform

W tym artykule założono, że znasz interfejs wiersza polecenia (Bash, Terminal, Wiersz polecenia).

Parametr --help lub -h może służyć do wyświetlania pomocy dla określonych poleceń. Alternatywnie można również użyć formatu pomocy platformy Azure [command] [options]. W razie wątpliwości co do parametrów wymaganych przez polecenie, należy zwrócić się o pomoc. Na przykład następujące polecenia zwracają te same informacje:

```azurecli
az account set --help
az account set -h
```

Możesz również zapoznać się z następującymi artykułami, aby zapoznać się z usługą Azure Resource Manager w interfejsie wiersza polecenia platformy Azure między platformami:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Jak utworzyć utwardzony kontener (magazyn) na platformie Azure

Magazyny są zabezpieczone kontenery wspierane przez sprzętowe moduły zabezpieczeń. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny usługi Key Vault umożliwiają także kontrolowanie i rejestrowanie dostępu do wszelkich elementów, które są w nich przechowywane. Usługa Azure Key Vault obsługuje żądania i odnawianie certyfikatów protokołu TLS (Transport Layer Security), udostępniając funkcje wymagane przez niezawodne rozwiązania do zarządzania cyklem życia certyfikatu. W następnych krokach utworzysz przechowalnię.

### <a name="connect-to-your-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami

Aby zalogować się interaktywnie, użyj następującego polecenia:

```azurecli
az login
```
Aby zalogować się przy użyciu konta organizacji, możesz przekazać nazwę użytkownika i hasło.

```azurecli
az login -u username@domain.com -p password
```

Jeśli masz więcej niż jedną subskrypcję i musisz określić, którego chcesz użyć, wpisz następujące elementy, aby wyświetlić subskrypcje dla swojego konta:

```azurecli
az account list
```

Określ subskrypcję z parametrem subskrypcji.

```azurecli
az account set --subscription <subscription name or ID>
```

Aby uzyskać więcej informacji na temat konfigurowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Tworzenie nowej grupy zasobów

Podczas korzystania z usługi Azure Resource Manager wszystkie powiązane zasoby są tworzone wewnątrz grupy zasobów. Magazyn kluczy można utworzyć w istniejącej grupie zasobów. Jeśli chcesz użyć nowej grupy zasobów, możesz utworzyć nową.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Pierwszym parametrem jest nazwa grupy zasobów, a drugim parametrem jest lokalizacja. Aby uzyskać listę wszystkich możliwych lokalizacji typu:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Rejestrowanie dostawcy zasobów usługi Key Vault

 Podczas próby utworzenia nowego magazynu kluczy może zostać wyświetlony błąd "Subskrypcja nie jest zarejestrowana w celu użycia obszaru nazw 'Microsoft.KeyVault'". Jeśli pojawi się ten komunikat, upewnij się, że dostawca zasobów usługi Key Vault jest zarejestrowany w subskrypcji. Jest to jednorazowa operacja dla każdej subskrypcji.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Użyj `az keyvault create` polecenia, aby utworzyć magazyn kluczy. Ten skrypt ma trzy obowiązkowe parametry: nazwę grupy zasobów, nazwę magazynu kluczy i lokalizację geograficzną.

Aby utworzyć nowy magazyn o nazwie **ContosoKeyVault**w grupie zasobów **ContosoResourceGroup**, zamieszkałej w lokalizacji **Azji Wschodniej,** należy wpisać: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Dane wyjściowe tego polecenia pokazuje właściwości magazynu kluczy, który został utworzony. Dwie najważniejsze właściwości to:

* **nazwa**: W przykładzie nazwa to ContosoKeyVault. Użyjesz tej nazwy dla innych poleceń Magazynu kluczy.
* **vaultUri**: W przykładzie identyfikator https://contosokeyvault.vault.azure.netURI jest . Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym magazynie kluczy. Jak dotąd nikt inny nie jest upoważniony.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Dodawanie klucza, klucza tajnego lub certyfikatu do magazynu kluczy

Jeśli chcesz, aby usługa Azure Key Vault utworzyła dla `az key create` Ciebie klucz chroniony programowo, użyj tego polecenia.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Jeśli masz istniejący klucz w pliku pem, możesz przekazać go do usługi Azure Key Vault. Klucz można chronić za pomocą oprogramowania lub modułu HSM. W tym przykładzie importuje klucz z pliku pem i chroni go za pomocą oprogramowania, używając hasła "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Teraz można odwoływać się do klucza, który został utworzony lub przekazany do usługi Azure Key Vault, przy użyciu jego identyfikatora URI. Użyj, **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** aby zawsze uzyskać bieżącą wersję. Aby uzyskać tę konkretną wersję, należy użyć pliku https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id]. Na przykład **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Dodaj klucz tajny do magazynu, który jest hasłem o nazwie SQLPassword i który ma wartość "hVFkk965BuUv" do usługi Azure Key Vaults. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Odwołaj się do tego hasła przy użyciu jego identyfikatora URI. Użyj, **https://ContosoVault.vault.azure.net/secrets/SQLPassword** aby zawsze uzyskać bieżącą wersję i https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id], aby uzyskać tę konkretną wersję. Na przykład **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Zaimportuj certyfikat do magazynu przy użyciu pliku pem lub .pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Wyświetlmy klucz, klucz tajny lub utworzony certyfikat:

* Aby wyświetlić klucze, wpisz: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Aby wyświetlić swoje wpisy tajne, wpisz: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Aby wyświetlić certyfikaty, wpisz: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory

Ten krok będzie zazwyczaj wykonywany przez programistę na innym komputerze. Nie jest specyficzne dla usługi Azure Key Vault, ale jest uwzględniony w tym miejscu, dla świadomości. Aby ukończyć rejestrację aplikacji, twoje konto, magazyn i aplikacja muszą znajdować się w tym samym katalogu platformy Azure.

Aplikacje używające magazynu kluczy muszą zostać uwierzytelnione przy użyciu tokenu z usługi Azure Active Directory.  Właściciel aplikacji musi najpierw zarejestrować ją w usłudze Azure Active Directory. Na koniec rejestracji właściciel aplikacji otrzymuje następujące wartości:

- Identyfikator **aplikacji** (znany również jako identyfikator klienta usługi AAD lub identyfikator aplikacji)
- **Klucz uwierzytelniania** (nazywany też wspólnym wpisem tajnym). 

Aby uzyskać token, aplikacja musi przedstawić obie wartości usłudze Azure Active Directory. Jak aplikacja jest skonfigurowana do uzyskania tokenu będzie zależeć od aplikacji. W przypadku [przykładowej aplikacji usługi Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) właściciel aplikacji ustawia te wartości w pliku app.config.

Aby uzyskać szczegółowe kroki dotyczące rejestrowania aplikacji za pomocą usługi Azure Active Directory, należy przejrzeć artykuły [zatytułowane Integrowanie aplikacji z usługą Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md), [Użyj portalu do utworzenia aplikacji i jednostki usługi Azure Active Directory, która może uzyskiwać dostęp do zasobów,](../../active-directory/develop/howto-create-service-principal-portal.md)i [utworzyć jednostkę usługi Azure za pomocą interfejsu wiersza polecenia platformy Azure.](/cli/azure/create-an-azure-service-principal-azure-cli)

Aby zarejestrować aplikację w usłudze Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autoryzowanie aplikacji do używania klucza lub klucza tajnego

Aby autoryzować aplikację, aby uzyskać dostęp do klucza lub klucza tajnego w przechowalni, użyj `az keyvault set-policy` polecenia.

Na przykład jeśli nazwa repozytorium to ContosoKeyVault, aplikacja ma identyfikator appID 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed i chcesz autoryzować aplikację do odszyfrowania i podpisania kluczami w magazynie, użyj następującego polecenia:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Aby autoryzować tę samą aplikację do odczytu wpisów tajnych w magazynie, wpisz następujące polecenie:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy

Użyj [aktualizacji az keyvault,](/cli/azure/keyvault#az-keyvault-update) aby włączyć zaawansowane zasady dla magazynu kluczy.

 Włącz magazyn kluczy do wdrożenia: umożliwia maszynom wirtualnym pobieranie certyfikatów przechowywanych jako wpisy tajne z magazynu.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Włącz magazyn kluczy do szyfrowania dysku: wymagane podczas korzystania z magazynu dla szyfrowania dysku Azure.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Włącz magazyn kluczy do wdrożenia szablonu: umożliwia Menedżerowi zasobów pobieranie wpisów tajnych z magazynu.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Praca ze sprzętowymi modułami zabezpieczeń (HSM)

Aby uzyskać dodatkową pewność, można importować lub generować klucze ze sprzętowych modułów zabezpieczeń (HSM), które nigdy nie opuszczają granicy modułu HSM. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Jeżeli te wymagania nie odnoszą się do Ciebie, pomiń tę sekcję i przejdź do sekcji Usuwanie magazynu kluczy oraz skojarzonych kluczy i kluczy tajnych.

Aby utworzyć klucze chronione przez moduł HSM, musisz mieć subskrypcję magazynu obsługującą klucze chronione przez moduł HSM.

Podczas tworzenia keyvault, dodać parametr 'sku':

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Do tego magazynu możesz dodać klucze chronione oprogramowaniem (jak pokazano wcześniej) oraz klucze chronione modułem HSM. Aby utworzyć klucz chroniony przez moduł HSM, ustaw parametr Destination na "HSM":

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Za pomocą następującego polecenia można zaimportować klucz z pliku pem na komputerze. To polecenie importuje klucz do modułu HSM w usłudze Key Vault:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Następne polecenie importuje pakiet "bring your own key" (BYOK). Umożliwia to wygenerowanie własnego klucza w lokalnym module HSM i przeniesienie go do modułów HSM w usłudze Key Vault bez opuszczania przez klucz granic modułu HSM:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Aby uzyskać bardziej szczegółowe instrukcje dotyczące generowania tego pakietu BYOK, zobacz [Jak używać kluczy chronionych przez moduł HSM z usługą Azure Key Vault](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Usuwanie magazynu kluczy i skojarzonych kluczy i wpisów tajnych

Jeśli magazyn kluczy i jego klucze lub klucze nie są `az keyvault delete` już potrzebne, można usunąć magazyn kluczy za pomocą polecenia:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Możesz także usunąć całą grupę zasobów platformy Azure zawierającą magazyn kluczy oraz inne zasoby, które zostały dodane do tej grupy:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Różne polecenia interfejsu wiersza polecenia platformy Azure dla różnych platform

Inne polecenia, które mogą okazać się przydatne do zarządzania usługą Azure Key Vault.

To polecenie wyświetla tabelaryczne wyświetlanie wszystkich klawiszy i wybranych właściwości:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

To polecenie wyświetla pełną listę właściwości dla określonego klucza:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

To polecenie wyświetla tabelaryczne wyświetlanie wszystkich tajnych nazw i wybranych właściwości:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Oto przykład usuwania określonego klucza:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Oto przykład usuwania określonego klucza tajnego:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać pełne odwołanie do interfejsu wiersza polecenia usługi Azure CLI dla poleceń magazynu kluczy, zobacz [odwołanie do interfejsu wiersza polecenia magazynu kluczy](/cli/azure/keyvault).

- Aby uzyskać informacje o programowaniu, zobacz [przewodnik dla deweloperów usługi Azure Key Vault](developers-guide.md)

- Aby uzyskać informacje na temat usługi Azure Key Vault i hsms, zobacz [Jak używać kluczy chronionych przez moduł HSM z usługą Azure Key Vault](../keys/hsm-protected-keys.md).
