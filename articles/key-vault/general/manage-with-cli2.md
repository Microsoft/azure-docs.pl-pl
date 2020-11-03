---
title: Zarządzanie Azure Key Vault przy użyciu interfejsu wiersza polecenia Azure Key Vault | Microsoft Docs
description: Ten artykuł służy do automatyzowania typowych zadań w Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: cb7e047d998342125a52af5ea3ae1e88fe88d313
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289893"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure 

W tym artykule opisano, jak rozpocząć pracę z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure.  Możesz wyświetlić informacje o:

- Jak utworzyć kontener z ograniczeniami (magazyn) na platformie Azure
- Dodawanie klucza, wpisu tajnego lub certyfikatu do magazynu kluczy
- Rejestrowanie aplikacji przy użyciu Azure Active Directory
- Autoryzowanie aplikacji do korzystania z klucza lub wpisu tajnego
- Ustawianie zaawansowanych zasad dostępu magazynu kluczy
- Praca z sprzętowymi modułami zabezpieczeń (sprzętowych modułów zabezpieczeń)
- Usuwanie magazynu kluczy oraz skojarzonych kluczy i wpisów tajnych
- Różne polecenia interfejsu wiersza polecenia platformy Azure dla wielu platform


Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Ten artykuł nie zawiera instrukcji dotyczących sposobu pisania aplikacji platformy Azure, która zawiera jeden z kroków, który pokazuje, jak autoryzować aplikację do korzystania z klucza lub wpisu tajnego w magazynie kluczy.
>

Aby zapoznać się z omówieniem Azure Key Vault, zobacz [co to jest Azure Key Vault?](overview.md)). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z poleceń interfejsu wiersza polecenia platformy Azure w tym artykule, należy dysponować następującymi elementami:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs Command-Line platformy Azure w wersji 2,0 lub nowszej. Aby zainstalować najnowszą wersję, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Aplikacja, która zostanie skonfigurowana do korzystania z klucza lub hasła utworzonego w tym artykule. Przykładowa aplikacja jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Aby uzyskać instrukcje, zobacz zawarty plik Readme.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Uzyskiwanie pomocy dotyczącej międzyplatformowego interfejsu Command-Line platformy Azure

W tym artykule założono, że znasz interfejs wiersza polecenia (bash, Terminal, wiersz polecenia).

Aby wyświetlić pomoc dla konkretnych poleceń, można użyć parametru--help lub-h. Alternatywnie można również użyć formatu pomocy systemu Azure [polecenie] [opcje]. W razie wątpliwości dotyczących parametrów wymaganych przez polecenie zapoznaj się z pomocą. Na przykład następujące polecenia zwracają te same informacje:

```azurecli
az account set --help
az account set -h
```

Możesz również zapoznać się z następującymi artykułami, aby zapoznać się z Azure Resource Manager w interfejsie Command-Line na wielu platformach platformy Azure:

* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Jak utworzyć kontener z ograniczeniami (magazyn) na platformie Azure

Magazyny są zabezpieczonymi kontenerami objętymi sprzętowymi modułami zabezpieczeń. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny usługi Key Vault umożliwiają także kontrolowanie i rejestrowanie dostępu do wszelkich elementów, które są w nich przechowywane. Usługa Azure Key Vault obsługuje żądania i odnawianie certyfikatów protokołu TLS (Transport Layer Security), udostępniając funkcje wymagane przez niezawodne rozwiązania do zarządzania cyklem życia certyfikatu. W następnych krokach utworzysz magazyn.

### <a name="connect-to-your-subscriptions"></a>Nawiązywanie połączenia z subskrypcjami

Aby zalogować się interaktywnie, użyj następującego polecenia:

```azurecli
az login
```
Aby zalogować się przy użyciu konta organizacyjnego, możesz przekazać nazwę użytkownika i hasło.

```azurecli
az login -u username@domain.com -p password
```

Jeśli masz więcej niż jedną subskrypcję i chcesz określić, która ma być używana, wpisz następujące polecenie, aby wyświetlić subskrypcje dla konta:

```azurecli
az account list
```

Określ subskrypcję z parametrem subskrypcji.

```azurecli
az account set --subscription <subscription name or ID>
```

Aby uzyskać więcej informacji na temat konfigurowania interfejsu Command-Line międzyplatformowego platformy Azure, zobacz [Install the Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Tworzenie nowej grupy zasobów

W przypadku korzystania z Azure Resource Manager wszystkie powiązane zasoby są tworzone wewnątrz grupy zasobów. Magazyn kluczy można utworzyć w istniejącej grupie zasobów. Jeśli chcesz użyć nowej grupy zasobów, możesz utworzyć nową.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

Pierwszy parametr jest nazwą grupy zasobów, a drugi parametr jest lokalizacją. Aby uzyskać listę wszystkich możliwych typów lokalizacji:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Rejestrowanie dostawcy zasobów Key Vault

 Podczas próby utworzenia nowego magazynu kluczy może zostać wyświetlony komunikat o błędzie "subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw" Microsoft. Key magazynu "". Jeśli zostanie wyświetlony komunikat, upewnij się, że dostawca zasobów Key Vault jest zarejestrowany w ramach subskrypcji. Jest to jednorazowa operacja dla każdej subskrypcji.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Użyj `az keyvault create` polecenia, aby utworzyć magazyn kluczy. Ten skrypt ma trzy obowiązkowe parametry: nazwę grupy zasobów, nazwę magazynu kluczy i lokalizację geograficzną.

Aby utworzyć nowy magazyn o nazwie **ContosoKeyVault** , w grupie zasobów  **ContosoResourceGroup** , znajdującej się w lokalizacji **Azja Wschodnia** , wpisz: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

Dane wyjściowe tego polecenia przedstawiają właściwości utworzonego magazynu kluczy. Dwie najważniejsze właściwości to:

* **Nazwa** : w tym przykładzie nazwa to ContosoKeyVault. Ta nazwa będzie używana do innych poleceń Key Vault.
* **vaultUri** : w tym przykładzie identyfikator URI to https://contosokeyvault.vault.azure.net . Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure ma teraz uprawnienia do wykonywania dowolnych operacji na tym magazynie kluczy. Od tej pory nikt nie jest autoryzowany.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Dodawanie klucza, wpisu tajnego lub certyfikatu do magazynu kluczy

Jeśli chcesz, aby Azure Key Vault utworzyć dla Ciebie klucz chroniony oprogramowaniem, użyj `az key create` polecenia.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Jeśli masz istniejący klucz w pliku PEM, możesz go przekazać do Azure Key Vault. Klucz można chronić za pomocą oprogramowania lub modułu HSM. Ten przykład służy do importowania klucza z pliku PEM i ochrony go za pomocą oprogramowania przy użyciu hasła "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Teraz można odwołać się do klucza utworzonego lub przekazanego do Azure Key Vault przy użyciu identyfikatora URI. Użyj, **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** Aby zawsze pobrać bieżącą wersję. Użyj protokołu https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Key/[NazwaKlucza]/[klucz-Unique-ID], aby uzyskać tę określoną wersję. Przykładowy adres URL to **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Dodaj wpis tajny do magazynu, który jest hasłem o nazwie SQLPassword i ma wartość "hVFkk965BuUv" do magazynów kluczy platformy Azure. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Odwołuje się do tego hasła przy użyciu identyfikatora URI. Użyj **https://ContosoVault.vault.azure.net/secrets/SQLPassword** , aby zawsze pobrać bieżącą wersję, i https://[nazwa usługi magazynu]. magazyn. Azure. NET/Secret/[Secret-Name]/[Secret-Unique-ID], aby uzyskać tę określoną wersję. Przykładowy adres URL to **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Zaimportuj certyfikat do magazynu przy użyciu pliku PEM lub PFX.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Wyświetlmy teraz utworzony klucz, klucz tajny lub certyfikat:

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

## <a name="registering-an-application-with-azure-active-directory"></a>Rejestrowanie aplikacji przy użyciu Azure Active Directory

Ten krok będzie zazwyczaj wykonywany przez programistę na innym komputerze. Nie jest to specyficzne dla Azure Key Vault, ale jest tutaj zawarte w celu zapewnienia świadomości. Aby ukończyć rejestrację aplikacji, Twoje konto, magazyn i aplikacja muszą znajdować się w tym samym katalogu platformy Azure.

Aplikacje używające magazynu kluczy muszą zostać uwierzytelnione przy użyciu tokenu z usługi Azure Active Directory.  Właściciel aplikacji musi najpierw zarejestrować ją w Azure Active Directory. Na koniec rejestracji właściciel aplikacji otrzymuje następujące wartości:

- **Identyfikator aplikacji** (znany również jako identyfikator klienta usługi AAD lub AppID)
- **Klucz uwierzytelniania** (nazywany też wspólnym wpisem tajnym). 

Aby uzyskać token, aplikacja musi przedstawić obie wartości usłudze Azure Active Directory. Sposób, w jaki aplikacja jest skonfigurowana w celu uzyskania tokenu, będzie zależeć od aplikacji. W przypadku [przykładowej aplikacji usługi Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) właściciel aplikacji ustawia te wartości w pliku app.config.

Aby uzyskać szczegółowe instrukcje dotyczące rejestrowania aplikacji z Azure Active Directory należy zapoznać się z artykułami dotyczącymi [integrowania aplikacji z Azure Active Directory](../../active-directory/develop/quickstart-register-app.md), [używania portalu do tworzenia aplikacji Azure Active Directory i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md), oraz [utworzyć jednostkę usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

Aby zarejestrować aplikację w Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autoryzowanie aplikacji do korzystania z klucza lub wpisu tajnego

Aby autoryzować aplikację do uzyskiwania dostępu do klucza lub wpisu tajnego w magazynie, użyj `az keyvault set-policy` polecenia.

Na przykład jeśli nazwa magazynu to ContosoKeyVault, aplikacja ma identyfikator aplikacji 8f8c4bbd-485b-45FD-98f7-ec6300b7b4ed i chcesz autoryzować aplikację do odszyfrowywania i podpisywania kluczy w magazynie, użyj następującego polecenia:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Aby autoryzować tę samą aplikację do odczytu wpisów tajnych w magazynie, wpisz następujące polecenie:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Ustawianie zaawansowanych zasad dostępu magazynu kluczy

Użyj [AZ Key Update](/cli/azure/keyvault#az-keyvault-update) , aby włączyć zaawansowane zasady dla magazynu kluczy.

 Włącz Key Vault wdrożenia: umożliwia maszynom wirtualnym pobieranie certyfikatów przechowywanych jako wpisy tajne z magazynu.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Włącz Key Vault szyfrowania dysku: wymagane w przypadku korzystania z magazynu dla usługi Azure Disk Encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Włącz Key Vault wdrożenia szablonu: umożliwia Menedżer zasobów pobierania wpisów tajnych z magazynu.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Praca z sprzętowymi modułami zabezpieczeń (sprzętowych modułów zabezpieczeń)

Aby uzyskać gwarancję, można importować lub generować klucze z sprzętowych modułów zabezpieczeń (sprzętowych modułów zabezpieczeń), które nigdy nie opuszczają granicy modułu HSM. Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2. Jeżeli te wymagania nie odnoszą się do Ciebie, pomiń tę sekcję i przejdź do sekcji Usuwanie magazynu kluczy oraz skojarzonych kluczy i kluczy tajnych.

Aby utworzyć klucze chronione przez moduł HSM, musisz mieć subskrypcję magazynu obsługującą klucze chronione przez moduł HSM.

Podczas tworzenia magazynu kluczy należy dodać parametr "SKU":

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Do tego magazynu możesz dodać klucze chronione oprogramowaniem (jak pokazano wcześniej) oraz klucze chronione modułem HSM. Aby utworzyć klucz chroniony przez moduł HSM, ustaw parametr docelowy na "HSM":

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Za pomocą następującego polecenia można zaimportować klucz z pliku PEM na komputerze. To polecenie importuje klucz do modułu HSM w usłudze Key Vault:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Następne polecenie importuje pakiet "Przenieś własny klucz" (BYOK). Umożliwia to wygenerowanie własnego klucza w lokalnym module HSM i przeniesienie go do modułów HSM w usłudze Key Vault bez opuszczania przez klucz granic modułu HSM:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Aby uzyskać szczegółowe instrukcje dotyczące sposobu generowania tego pakietu BYOK, zobacz [jak używać kluczy HSM-Protected z Azure Key Vault](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Usuwanie magazynu kluczy oraz skojarzonych kluczy i wpisów tajnych

Jeśli magazyn kluczy i jego klucze lub wpisy tajne nie są już potrzebne, można usunąć magazyn kluczy za pomocą `az keyvault delete` polecenia:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Możesz także usunąć całą grupę zasobów platformy Azure zawierającą magazyn kluczy oraz inne zasoby, które zostały dodane do tej grupy:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Różne polecenia interfejsu wiersza polecenia platformy Azure dla wielu platform

Inne polecenia, które mogą być przydatne do zarządzania Azure Key Vault.

To polecenie wyświetla tabelaryczny widok wszystkich kluczy i wybranych właściwości:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

To polecenie wyświetla pełną listę właściwości dla określonego klucza:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

To polecenie wyświetla tabelaryczny widok wszystkich nazw kluczy tajnych i wybranych właściwości:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Oto przykład sposobu usuwania określonego klucza:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Oto przykład sposobu usuwania określonego klucza tajnego:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać pełne informacje na temat interfejsu wiersza polecenia platformy Azure dla poleceń magazynu kluczy, zobacz temat [Informacje o Key Vault CLI](/cli/azure/keyvault).

- Aby uzyskać informacje dotyczące programowania, zobacz [przewodnik dewelopera Azure Key Vault](developers-guide.md)

- Aby uzyskać informacje na temat Azure Key Vault i sprzętowych modułów zabezpieczeń, zobacz [jak używać kluczy HSM-Protected z Azure Key Vault](../keys/hsm-protected-keys.md).