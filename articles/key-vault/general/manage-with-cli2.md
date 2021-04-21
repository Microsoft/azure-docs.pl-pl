---
title: Zarządzanie Azure Key Vault przy użyciu interfejsu wiersza polecenia — Azure Key Vault | Microsoft Docs
description: Skorzystaj z tego artykułu, aby zautomatyzować typowe zadania w Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0b29b668b21c375dd1202652b5093526f648c300
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749604"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure 

W tym artykule opisano, jak rozpocząć pracę z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure.  Możesz zobaczyć informacje na temat:

- Jak utworzyć kontener ze wzmacnianiem zabezpieczeń (magazyn) na platformie Azure
- Dodawanie klucza, klucza tajnego lub certyfikatu do magazynu kluczy
- Rejestrowanie aplikacji w Azure Active Directory
- Autoryzowanie aplikacji do używania klucza lub klucza tajnego
- Ustawianie zaawansowanych zasad dostępu magazynu kluczy
- Praca ze sprzętowym modułem zabezpieczeń (HSM)
- Usuwanie magazynu kluczy oraz skojarzonych kluczy i wpisów tajnych
- Różne międzyplatformowe polecenia interfejsu wiersza polecenia platformy Azure


Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Ten artykuł nie zawiera instrukcji dotyczących pisania aplikacji platformy Azure, którą zawiera jeden z kroków, który pokazuje, jak autoryzować aplikację do używania klucza lub klucza tajnego w magazynie kluczy.
>

Aby uzyskać omówienie Azure Key Vault, zobacz [Co to jest Azure Key Vault?](overview.md)) Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem utwórz bezpłatne konto.

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać poleceń interfejsu wiersza polecenia platformy Azure w tym artykule, musisz mieć następujące elementy:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Azure Command-Line Interface w wersji 2.0 lub nowszej. Aby zainstalować najnowszą wersję, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)
* Aplikacja, która zostanie skonfigurowana do używania klucza lub hasła, które zostały określone w tym artykule. Przykładowa aplikacja jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Aby uzyskać instrukcje, zobacz dołączony plik Readme.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Uzyskiwanie pomocy z interfejsem azure-cross-platform Command-Line Interface

W tym artykule założono, że znasz interfejs wiersza polecenia (bash, terminal, wiersz polecenia).

Parametr --help lub -h może służyć do wyświetlania pomocy dotyczącej określonych poleceń. Alternatywnie można również użyć formatu Pomoc platformy Azure [polecenie] [opcje]. Jeśli masz wątpliwości dotyczące parametrów wymaganych przez polecenie, zapoznaj się z pomocą. Na przykład następujące polecenia zwracają te same informacje:

```azurecli
az account set --help
az account set -h
```

Możesz również przeczytać następujące artykuły, aby zapoznać się z Azure Resource Manager w interfejsie azure cross-platform Command-Line Interface:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Jak utworzyć kontener ze wzmacnianiem zabezpieczeń (magazyn) na platformie Azure

Magazyny to zabezpieczone kontenery chronione przez sprzętowe moduły zabezpieczeń. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny usługi Key Vault umożliwiają także kontrolowanie i rejestrowanie dostępu do wszelkich elementów, które są w nich przechowywane. Usługa Azure Key Vault obsługuje żądania i odnawianie certyfikatów protokołu TLS (Transport Layer Security), udostępniając funkcje wymagane przez niezawodne rozwiązania do zarządzania cyklem życia certyfikatu. W następnych krokach utworzysz magazyn.

### <a name="connect-to-your-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami

Aby zalogować się interaktywnie, użyj następującego polecenia:

```azurecli
az login
```
Aby zalogować się przy użyciu konta organizacyjnego, możesz przekazać nazwę użytkownika i hasło.

```azurecli
az login -u username@domain.com -p password
```

Jeśli masz więcej niż jedną subskrypcję i musisz określić, której użyć, wpisz następujące informacje, aby wyświetlić subskrypcje dla konta:

```azurecli
az account list
```

Określ subskrypcję za pomocą parametru subskrypcji.

```azurecli
az account set --subscription <subscription name or ID>
```

Aby uzyskać więcej informacji na temat konfigurowania międzyplatformowego interfejsu Command-Line Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)

### <a name="create-a-new-resource-group"></a>Tworzenie nowej grupy zasobów

W przypadku Azure Resource Manager wszystkie powiązane zasoby są tworzone wewnątrz grupy zasobów. Magazyn kluczy można utworzyć w istniejącej grupie zasobów. Jeśli chcesz użyć nowej grupy zasobów, możesz utworzyć nową.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Pierwszy parametr to nazwa grupy zasobów, a drugi parametr to lokalizacja. Aby uzyskać listę wszystkich możliwych lokalizacji, wpisz:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Rejestrowanie Key Vault zasobów sieciowych

 Podczas próby utworzenia nowego magazynu kluczy może zostać wyświetlony komunikat o błędzie "Subskrypcja nie jest zarejestrowana do używania przestrzeni nazw "Microsoft.KeyVault". Jeśli zostanie wyświetlony ten komunikat, upewnij się, Key Vault dostawca zasobów jest zarejestrowany w subskrypcji. Jest to jednorazowa operacja dla każdej subskrypcji.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Użyj polecenia `az keyvault create` , aby utworzyć magazyn kluczy. Ten skrypt ma trzy obowiązkowe parametry: nazwę grupy zasobów, nazwę magazynu kluczy i lokalizację geograficzną.

Aby utworzyć nowy magazyn o nazwie **ContosoKeyVault** w grupie zasobów  **ContosoResourceGroup,** która znajduje się w **Azja Wschodnia** lokalizacji, wpisz: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Dane wyjściowe tego polecenia pokazują właściwości utworzonego magazynu kluczy. Dwie najważniejsze właściwości to:

* **name**: w tym przykładzie nazwa to ContosoKeyVault. Użyjesz tej nazwy dla innych poleceń Key Vault polecenia.
* **vaultUri:** w tym przykładzie jest to https://contosokeyvault.vault.azure.net . Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym magazynie kluczy. W tej pory nikt inny nie ma autoryzacji.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Dodawanie klucza, klucza tajnego lub certyfikatu do magazynu kluczy

Jeśli chcesz Azure Key Vault klucz chroniony przez oprogramowanie, użyj `az key create` polecenia .

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Jeśli masz istniejący klucz w pliku PEM, możesz przekazać go do Azure Key Vault. Klucz można chronić za pomocą oprogramowania lub modułu HSM. Ten przykład importuje klucz z pliku pem i chroni go oprogramowaniem przy użyciu hasła "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Teraz możesz odwoływać się do klucza, który został utworzony lub przekazany do Azure Key Vault, przy użyciu jego URI. Użyj `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey` , aby zawsze uzyskać bieżącą wersję. Użyj https://[keyvault-name].vault.azure.net/keys/[nazwa_klucza]/[key-unique-id], aby uzyskać tę konkretną wersję. Na przykład `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87`. 

Dodaj do magazynu klucz tajny o nazwie SQLPassword o wartości "hVFkk965BuUv" do usługi Azure Key Vault. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Odwołaj się do tego hasła przy użyciu jego wartości URI. Użyj funkcji , aby zawsze pobrać bieżącą **https://ContosoVault.vault.azure.net/secrets/SQLPassword** wersję, https://[nazwa magazynu kluczy].vault.azure.net/secret/[nazwa-klucza-tajnego]/[secret-unique-id], aby uzyskać tę konkretną wersję. Przykładowy adres URL to **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Zaimportuj certyfikat do magazynu przy użyciu pliku pem lub pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Wyświetlmy utworzony klucz, klucz tajny lub certyfikat:

* Aby wyświetlić klucze, wpisz: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Aby wyświetlić wpisy tajne, wpisz: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Aby wyświetlić certyfikaty, wpisz: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Rejestrowanie aplikacji za pomocą Azure Active Directory

Ten krok będzie zazwyczaj wykonywany przez programistę na innym komputerze. Nie jest ona specyficzna dla Azure Key Vault, ale jest tu uwzględniona w celu świadomości. Aby ukończyć rejestrację aplikacji, Twoje konto, magazyn i aplikacja muszą być w tym samym katalogu platformy Azure.

Aplikacje używające magazynu kluczy muszą zostać uwierzytelnione przy użyciu tokenu z usługi Azure Active Directory.  Właściciel aplikacji musi najpierw zarejestrować ją w Azure Active Directory aplikacji. Na koniec rejestracji właściciel aplikacji otrzymuje następujące wartości:

- Identyfikator **aplikacji** (znany również jako identyfikator klienta usługi AAD lub identyfikator aplikacji)
- **Klucz uwierzytelniania** (nazywany też wspólnym wpisem tajnym). 

Aby uzyskać token, aplikacja musi przedstawić obie wartości usłudze Azure Active Directory. Sposób skonfigurowania aplikacji w celu uzyskania tokenu będzie zależeć od aplikacji. W przypadku [przykładowej aplikacji usługi Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) właściciel aplikacji ustawia te wartości w pliku app.config.

Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania aplikacji w usłudze Azure Active Directory, zapoznaj się z artykułami [Integrating applications with Azure Active Directory](../../active-directory/develop/quickstart-register-app.md)(Integrowanie aplikacji z usługą Azure Active Directory), Use portal to create an Azure Active Directory application and service principal that can access [resources](../../active-directory/develop/howto-create-service-principal-portal.md)(Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy [Azure)](/cli/azure/create-an-azure-service-principal-azure-cli)za pomocą portalu .

Aby zarejestrować aplikację w Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autoryzowanie aplikacji do używania klucza lub klucza tajnego

Aby autoryzować aplikację do uzyskiwania dostępu do klucza lub klucza tajnego w magazynie, użyj `az keyvault set-policy` polecenia .

Jeśli na przykład nazwa magazynu to ContosoKeyVault, aplikacja ma appID 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed i chcesz autoryzować aplikację do odszyfrowywania i podpisywania przy użyciu kluczy w magazynie, użyj następującego polecenia:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Aby autoryzować tę samą aplikację do odczytu wpisów tajnych w magazynie, wpisz następujące polecenie:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy

Użyj [narzędzia az keyvault update,](/cli/azure/keyvault#az-keyvault-update) aby włączyć zaawansowane zasady dla magazynu kluczy.

 Włącz Key Vault wdrożenia: umożliwia maszynom wirtualnym pobieranie certyfikatów przechowywanych jako wpisy tajne z magazynu.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Włącz Key Vault szyfrowania dysków: wymagane w przypadku korzystania z magazynu na potrzeby usługi Azure Disk Encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Włącz Key Vault wdrażania szablonu: umożliwia Resource Manager wpisów tajnych z magazynu.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Praca ze sprzętowym modułem zabezpieczeń (HSM)

Aby uzyskać dodatkową gwarancję, można zaimportować lub wygenerować klucze ze sprzętowych modułów zabezpieczeń (HSM), które nigdy nie opuszczają granicy modułu HSM. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Jeżeli te wymagania nie odnoszą się do Ciebie, pomiń tę sekcję i przejdź do sekcji Usuwanie magazynu kluczy oraz skojarzonych kluczy i kluczy tajnych.

Aby utworzyć klucze chronione przez moduł HSM, musisz mieć subskrypcję magazynu obsługującą klucze chronione przez moduł HSM.

Podczas tworzenia funkcji keyvault dodaj parametr "sku":

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Do tego magazynu możesz dodać klucze chronione oprogramowaniem (jak pokazano wcześniej) oraz klucze chronione modułem HSM. Aby utworzyć klucz chroniony przez moduł HSM, ustaw parametr Destination na wartość "HSM":

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

Aby uzyskać bardziej szczegółowe instrukcje dotyczące sposobu generowania tego pakietu BYOK, zobacz Jak używać [kluczy HSM-Protected z Azure Key Vault](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Usuwanie magazynu kluczy oraz skojarzonych kluczy i wpisów tajnych

Jeśli magazyn kluczy i jego klucze lub wpisy tajne nie są już potrzebne, możesz go usunąć za pomocą `az keyvault delete` polecenia :

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Możesz także usunąć całą grupę zasobów platformy Azure zawierającą magazyn kluczy oraz inne zasoby, które zostały dodane do tej grupy:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Różne międzyplatformowe polecenia interfejsu wiersza polecenia platformy Azure

Inne polecenia, które mogą okazać się przydatne do zarządzania Azure Key Vault.

To polecenie wyświetla tabelaryczne wyświetlanie wszystkich kluczy i wybranych właściwości:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

To polecenie wyświetla pełną listę właściwości dla określonego klucza:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

To polecenie wyświetla tabelaryczne wyświetlanie wszystkich nazw tajnych i wybranych właściwości:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Oto przykład sposobu usuwania określonego klucza:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Oto przykład usuwania określonego tajnego:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać pełną listę poleceń usługi Key Vault dla interfejsu wiersza polecenia platformy Azure, zobacz [Key Vault interfejsu wiersza polecenia](/cli/azure/keyvault).

- Aby uzyskać informacje dotyczące programowania, [Azure Key Vault przewodnik dewelopera](developers-guide.md)

- Aby uzyskać informacje dotyczące Azure Key Vault i modułów HSM, zobacz Jak używać [kluczy HSM-Protected z](../keys/hsm-protected-keys.md)Azure Key Vault .
