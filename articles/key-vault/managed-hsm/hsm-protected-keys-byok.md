---
title: Jak generować i transferować klucze chronione przez moduł HSM dla zarządzanego Azure Key Vault HSM — Azure Key Vault | Microsoft Docs
description: Ten artykuł pomoże Ci zaplanować, wygenerować i przenieść własne klucze chronione przez moduł HSM do użycia z zarządzanym modułem HSM. Znana również jako bring your own key (BYOK).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: cc9037db3289d7fb3287a8994a8ff6a68fc0583a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790585"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Importowanie kluczy chronionych przez moduł HSM do zarządzanego modułu HSM (BYOK)

 Azure Key Vault HSM obsługuje importowanie kluczy wygenerowanych w lokalnym sprzętowym module zabezpieczeń (HSM); Klucze nigdy nie opuszczą granicy ochrony modułu HSM. Ten scenariusz jest często określany jako *bring your own key* (BYOK). Zarządzany moduł HSM używa adapterów HSM LiquidSecurity HSM (zweryfikowanych w modelu FIPS 140-2 poziom 3) do ochrony kluczy.

Informacje zawarte w tym artykule ułatwiają planowanie, generowanie i przenoszenie własnych kluczy chronionych przez moduł HSM do użycia z zarządzanym modułem HSM.

> [!NOTE]
> Ta funkcja nie jest dostępna dla Azure (Chiny) — 21Vianet. Ta metoda importowania jest dostępna tylko dla [obsługiwanych modułów HSM.](#supported-hsms) 

Aby uzyskać więcej informacji i samouczek dotyczący rozpoczynania pracy z zarządzanym modułem HSM, zobacz [Co to jest zarządzany moduł HSM?](overview.md).

## <a name="overview"></a>Omówienie

Oto omówienie tego procesu. Konkretne kroki do wykonania zostały opisane w dalszej części artykułu.

* W zarządzanym modułem HSM wygeneruj klucz (nazywany kluczem wymiany *kluczy* ( KEK) . Klucz KEK musi być kluczem RSA-HSM, który ma tylko `import` operację klucza. 
* Pobierz klucz publiczny KEK jako plik PEM.
* Przenieś klucz publiczny KEK na komputer w trybie offline połączony z lokalnym modułem HSM.
* Na komputerze w trybie offline użyj narzędzia BYOK dostarczonego przez dostawcę modułu HSM, aby utworzyć plik BYOK. 
* Klucz docelowy jest szyfrowany kluczem KEK, który pozostaje zaszyfrowany do momentu jego przeniesienia do zarządzanego modułu HSM. Tylko zaszyfrowana wersja klucza opuszcza lokalną wersję modułu HSM.
* Nie można eksportować KEK, który jest generowany wewnątrz zarządzanego modułu HSM. Moduły HSM wymuszają regułę, że poza zarządzanym modułem HSM nie istnieje żadna jasna wersja KEK.
* Klucz szyfrowania kluczy musi znajdować się w tym samym zarządzanym hsm, w którym zostanie zaimportowany klucz docelowy.
* Po przesłaniu pliku BYOK do zarządzanego modułu HSM zarządzany moduł HSM używa klucza prywatnego KEK do odszyfrowania materiału klucza docelowego i zaimportowania go jako klucza HSM. Ta operacja odbywa się całkowicie wewnątrz modułu HSM. Klucz docelowy zawsze pozostaje w granicach ochrony modułu HSM.


## <a name="prerequisites"></a>Wymagania wstępne

Aby używać poleceń interfejsu wiersza polecenia platformy Azure w tym artykule, musisz mieć następujące elementy:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Zarządzany moduł HSM [jest listą obsługiwanych modułów HSM](#supported-hsms) w ramach subskrypcji. Zobacz [Szybki start: aprowizuj i aktywuj zarządzany moduł HSM](quick-create-cli.md) przy użyciu interfejsu wiersza polecenia platformy Azure, aby aprowizować i aktywować zarządzany moduł HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli)

## <a name="supported-hsms"></a>Obsługiwane moduły HSM

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele HSM|Więcej informacji|
|---|---|---|---|
|nCipher|Producent<br/>Moduł HSM jako usługa|<ul><li>Rodzina modułów HSM nShield</li><li>nShield jako usługa</ul>|[nCipher new BYOK tool and documentation (Nowe narzędzie BYOK i dokumentacja nCipher)](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Producent|<ul><li>Rodzina Modułów HSM 7 z oprogramowaniem układowym w wersji 7.3 lub nowszej</li></ul>| [Narzędzie BYOK i dokumentacja firmy ByOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Producent<br/>Moduł HSM jako usługa|<ul><li>Self-Defending zarządzania kluczami (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Eksportowanie kluczy ZESTAWU SDKMS do dostawców usług w chmurze w celu rozwiązania BYOK — Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Producent|Wszystkie moduły HSM LiquidSecurity z<ul><li>Oprogramowanie układowe w wersji 2.0.4 lub nowszej</li><li>Oprogramowanie układowe w wersji 3.2 lub nowszej</li></ul>|[Dokumentacja i narzędzie ByOK w łasce dla użytkowników](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Kryptograficzne|ISV (Enterprise Key Management System)|Wiele marek i modeli modułów HSM, w tym<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Aby uzyskać szczegółowe informacje, zobacz [witrynę kryptograficzną](https://www.cryptomathic.com/azurebyok)|[Kryptograficzne narzędzie BYOK i dokumentacja](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Producent, moduł HSM jako usługa|Rodzina modułów HSM Primus, securosys clouds HSM|[Narzędzie BYOK Primus i dokumentacja](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Wiele marek i modeli modułów HSM, w tym<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Aby uzyskać [szczegółowe informacje, zobacz witrynę StorMagic](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS i Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Producent|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Producent<br/>Moduł HSM jako usługa|u.trust Anchor, CryptoServer|[Narzędzie BYOK Utimaco i przewodnik integracji](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Obsługiwane typy kluczy

|Nazwa klucza|Typ klucza|Rozmiar klucza/krzywa|Origin|Opis|
|---|---|---|---|---|
|Klucz wymiany klucza (KEK)|RSA| 2048-bitowy<br />3072-bitowy<br />4096-bitowy|Zarządzany moduł HSM|Para kluczy RSA z modułem HSM wygenerowana w zarządzanym hsm|
|Klucz docelowy|
||RSA|2048-bitowy<br />3072-bitowy<br />4096-bitowy|Moduł HSM dostawcy|Klucz do przeniesienia do zarządzanego modułu HSM|
||EC|P-256<br />P-384<br />P-521|Moduł HSM dostawcy|Klucz do przeniesienia do zarządzanego modułu HSM|
||Klucz symetryczny (oct-HSM)|128-bitowy<br />192-bitowy<br />256-bitowy|Moduł HSM dostawcy|Klucz do przeniesienia do zarządzanego modułu HSM|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Generowanie klucza i przenoszenie go do zarządzanego modułu HSM

Aby wygenerować i przenieść klucz do zarządzanego modułu HSM:

* [Krok 1. Generowanie KEK](#step-1-generate-a-kek)
* [Krok 2. Pobieranie klucza publicznego klucza KEK](#step-2-download-the-kek-public-key)
* [Krok 3. Generowanie i przygotowywanie klucza do transferu](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4. Przenoszenie klucza do zarządzanego modułu HSM](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Krok 1. Generowanie KEK

Klucz KEK to klucz RSA generowany w zarządzanym hsm. Klucz KEK jest używany do szyfrowania klucza, który chcesz zaimportować *(klucz* docelowy).

KEK musi być:
- Klucz RSA-HSM (2048-bitowy, 3072-bitowy lub 4096-bitowy)
- Wygenerowane w tym samym zarządzanym hsm, w którym zamierzasz zaimportować klucz docelowy
- Utworzono z dozwolonymi operacjami kluczy ustawionymi na wartość `import`

> [!NOTE]
> Klucz KEK musi mieć "import" jako jedyną dozwoloną operację klucza. Operacja "import" wzajemnie się wyklucza ze wszystkimi innymi operacjami klucza.

Użyj polecenia [az keyvault key create,](/cli/azure/keyvault/key#az_keyvault_key_create) aby utworzyć klucz szyfrowania kluczy, który ma operacje klucza ustawione na `import` wartość . Zanotuj identyfikator klucza ( `kid` ), który jest zwracany z następującego polecenia. (Użyjemy `kid` wartości w [kroku 3).](#step-3-generate-and-prepare-your-key-for-transfer)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Krok 2. Pobieranie klucza publicznego klucza KEK

Użyj [funkcji az keyvault key download,](/cli/azure/keyvault/key#az_keyvault_key_download) aby pobrać klucz publiczny KEK do pliku pem. Zaimportowany klucz docelowy jest szyfrowany przy użyciu klucza publicznego KEK.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Przenieś plik KEKforBYOK.publickey.pem na komputer w trybie offline. Ten plik będzie potrzebny w następnym kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3. Generowanie i przygotowywanie klucza do transferu

Zapoznaj się z dokumentacją dostawcy modułu HSM, aby pobrać i zainstalować narzędzie BYOK. Postępuj zgodnie z instrukcjami od dostawcy modułu HSM, aby wygenerować klucz docelowy, a następnie utwórz pakiet transferu kluczy (plik BYOK). Narzędzie BYOK użyje plików z kroku `kid` [1](#step-1-generate-a-kek) i KEKforBYOK.publickey.pem pobranych w kroku [2](#step-2-download-the-kek-public-key) w celu wygenerowania zaszyfrowanego klucza docelowego w pliku BYOK.

Przenieś plik BYOK na połączony komputer.

> [!NOTE] 
> Importowanie kluczy 1024-bitowych RSA nie jest obsługiwane. Obecnie importowanie klucza krzywej eliptycznej (EC) nie jest obsługiwane.
>
> **Znany problem:** Importowanie klucza docelowego RSA 4K z modułów HSM Firmy Hsm jest obsługiwane tylko w przypadku oprogramowania układowego w wersji 7.4.0 lub nowszej.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Krok 4. Przenoszenie klucza do zarządzanego modułu HSM

Aby ukończyć importowanie klucza, przenieś pakiet transferu kluczy (plik BYOK) z odłączonego komputera na komputer połączony z Internetem. Użyj polecenia [az keyvault key import,](/cli/azure/keyvault/key#az_keyvault_key_import) aby przekazać plik BYOK do zarządzanego modułu HSM.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Jeśli przekazywanie powiedzie się, interfejs wiersza polecenia platformy Azure wyświetli właściwości zaimportowanych kluczy.

## <a name="next-steps"></a>Następne kroki

Teraz możesz używać tego klucza chronionego przez moduł HSM w zarządzanym hsm. Aby uzyskać więcej informacji, zobacz [to porównanie cen i funkcji.](https://azure.microsoft.com/pricing/details/key-vault/)
