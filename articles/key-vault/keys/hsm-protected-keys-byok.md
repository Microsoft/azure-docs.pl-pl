---
title: Jak wygenerować klucze chronione & HSM — BYOK — Azure Key Vault
description: Ten artykuł pomoże Ci zaplanować, wygenerować i przenieść własne klucze chronione przez moduł HSM do użycia z Azure Key Vault. Znana również jako bring your own key (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: f1b5d6499594e9026e1615be5361c52c9ce2f4ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774813"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Importowanie kluczy chronionych przez moduł HSM do Key Vault (BYOK)

Aby uzyskać dodatkową pewność podczas korzystania Azure Key Vault, można zaimportować lub wygenerować klucz w sprzętowym module zabezpieczeń (HSM). Klucz nigdy nie opuści granicy modułu HSM. Ten scenariusz jest często określany jako *bring your own key* (BYOK). Key Vault ochrony kluczy używa rodziny modułów HSM nCipher nShield (zweryfikowanych w programie FIPS 140-2 poziom 2).

Informacje zawarte w tym artykule ułatwiają planowanie, generowanie i przenoszenie własnych kluczy chronionych przez moduł HSM do użycia z Azure Key Vault.

> [!NOTE]
> Ta funkcja nie jest dostępna dla Azure (Chiny) — 21Vianet. 
> 
> Ta metoda importowania jest dostępna tylko dla [obsługiwanych modułów HSM.](#supported-hsms) 

Aby uzyskać więcej informacji oraz samouczek dotyczący rozpoczynania pracy z usługą Key Vault (w tym sposób tworzenia magazynu kluczy dla kluczy chronionych przez moduł HSM), zobacz Co to jest [Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Omówienie

Oto omówienie tego procesu. Konkretne kroki do wykonania zostały opisane w dalszej części artykułu.

* W Key Vault wygeneruj klucz (określany jako klucz wymiany *klucza* (KEK) . Klucz KEK musi być kluczem RSA-HSM, który ma tylko `import` operację klucza. Tylko Key Vault SKU Premium obsługuje klucze RSA-HSM.
* Pobierz klucz publiczny KEK jako plik PEM.
* Przenieś klucz publiczny KEK na komputer w trybie offline połączony z lokalnym modułem HSM.
* Na komputerze w trybie offline użyj narzędzia BYOK dostarczonego przez dostawcę modułu HSM, aby utworzyć plik BYOK. 
* Klucz docelowy jest szyfrowany kluczem KEK, który pozostaje zaszyfrowany, dopóki nie zostanie przeniesiony do Key Vault HSM. Tylko zaszyfrowana wersja klucza opuszcza lokalną wersję modułu HSM.
* Nie można eksportować modułu KEK wygenerowanego w Key Vault HSM. Moduły HSM wymuszają regułę, że poza modułem HSM nie istnieje Key Vault KEK.
* Klucz szyfrowania kluczy musi znajdować się w tym samym magazynie kluczy, w którym zostanie zaimportowany klucz docelowy.
* Po przesłaniu pliku BYOK do usługi Key Vault moduł HSM usługi Key Vault używa klucza prywatnego KEK do odszyfrowania materiału klucza docelowego i zaimportowania go jako klucza HSM. Ta operacja odbywa się całkowicie wewnątrz Key Vault HSM. Klucz docelowy zawsze pozostaje w granicach ochrony modułu HSM.

## <a name="prerequisites"></a>Wymagania wstępne

W poniższej tabeli wymieniono wymagania wstępne dotyczące korzystania z usługi BYOK w programie Azure Key Vault:

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Do utworzenia magazynu kluczy w Azure Key Vault potrzebna jest subskrypcja platformy Azure. [Zarejestruj się, aby uzyskać bezpłatną wersję próbną.](https://azure.microsoft.com/pricing/free-trial/) |
| A Key Vault SKU Premium do importowania kluczy chronionych przez moduł HSM |Aby uzyskać więcej informacji o warstwach usług i możliwościach usługi Azure Key Vault, [zobacz cennik Key Vault usługi](https://azure.microsoft.com/pricing/details/key-vault/). |
| Moduł HSM z listy obsługiwanych modułów HSM oraz narzędzie BYOK i instrukcje dostarczone przez dostawcę modułu HSM | Musisz mieć uprawnienia do modułu HSM i podstawową wiedzę na temat sposobu korzystania z modułu HSM. Zobacz [Obsługiwane moduły HSM.](#supported-hsms) |
| Interfejs wiersza polecenia platformy Azure w wersji 2.1.0 lub nowszej | Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli)|

## <a name="supported-hsms"></a>Obsługiwane moduły HSM

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele modułów HSM|Więcej informacji|
|---|---|---|---|
|nCipher|Producent<br/>Moduł HSM jako usługa|<ul><li>Rodzina modułów HSM nShield</li><li>nShield jako usługa</ul>|[nCipher new BYOK tool and documentation (Nowe narzędzie BYOK i dokumentacja nCipher)](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Producent|<ul><li>Rodzina Modułów HSM 7 z oprogramowaniem układowym w wersji 7.3 lub nowszej</li></ul>| [Narzędzie BYOK i dokumentacja firmy ByOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Producent<br/>Moduł HSM jako usługa|<ul><li>Self-Defending zarządzania kluczami (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Eksportowanie kluczy ZESTAWU SDKMS do dostawców usług w chmurze w celu rozwiązania BYOK — Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Producent|Wszystkie moduły HSM LiquidSecurity z<ul><li>Oprogramowanie układowe w wersji 2.0.4 lub nowszej</li><li>Oprogramowanie układowe w wersji 3.2 lub nowszej</li></ul>|[Dokumentacja i narzędzie BYOK](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Kryptograficzne|ISV (Enterprise Key Management System)|Wiele marek i modeli modułów HSM, w tym<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Aby uzyskać szczegółowe informacje, zobacz [witrynę kryptograficzną](https://www.cryptomathic.com/azurebyok)|[Kryptograficzne narzędzie BYOK i dokumentacja](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Producent<br/>Moduł HSM jako usługa|Rodzina modułów HSM Primus, securosys clouds HSM|[Narzędzie BYOK Primus i dokumentacja](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Enterprise Key Management System)|Wiele marek i modeli modułów HSM, w tym<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Aby uzyskać [szczegółowe informacje, zobacz witrynę StorMagic](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS i Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Producent|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Producent<br/>Moduł HSM jako usługa|u.trust Anchor, CryptoServer|[Narzędzie BYOK Utimaco i przewodnik integracji](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Obsługiwane typy kluczy

|Nazwa klucza|Typ klucza|Rozmiar klucza/krzywa|Origin|Opis|
|---|---|---|---|---|
|Klucz wymiany klucza (KEK)|RSA| 2048-bitowy<br />3072-bitowy<br />4096-bitowy|Azure Key Vault HSM|Para kluczy RSA z modułem HSM wygenerowana w Azure Key Vault|
|Klucz docelowy|
||RSA|2048-bitowy<br />3072-bitowy<br />4096-bitowy|Moduł HSM dostawcy|Klucz, który ma zostać przeniesiony do Azure Key Vault HSM|
||EC|P-256<br />P-384<br />P-521|Moduł HSM dostawcy|Klucz, który ma zostać przeniesiony do Azure Key Vault HSM|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generowanie klucza i przenoszenie go do Key Vault HSM

Aby wygenerować klucz i przenieść go do Key Vault HSM:

* [Krok 1. Generowanie KEK](#step-1-generate-a-kek)
* [Krok 2. Pobieranie klucza publicznego KEK](#step-2-download-the-kek-public-key)
* [Krok 3. Generowanie i przygotowywanie klucza do transferu](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4. Przenoszenie klucza do Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Krok 1. Generowanie KEK

Klucz KEK to klucz RSA generowany w Key Vault HSM. Klucz KEK jest używany do szyfrowania klucza, który chcesz zaimportować *(klucz* docelowy).

KEK musi być:
- Klucz RSA-HSM (2048-bitowy, 3072-bitowy lub 4096-bitowy)
- Wygenerowane w tym samym magazynie kluczy, w którym zamierzasz zaimportować klucz docelowy
- Utworzono z dozwolonymi operacjami kluczy ustawionymi na wartość `import`

> [!NOTE]
> Klucz KEK musi mieć "import" jako jedyną dozwoloną operację klucza. Operacja "import" wzajemnie się wyklucza ze wszystkimi innymi operacjami klucza.

Użyj polecenia [az keyvault key create,](/cli/azure/keyvault/key#az_keyvault_key_create) aby utworzyć klucz szyfrowania kluczy, który ma operacje klucza ustawione na `import` wartość . Zanotuj identyfikator klucza ( `kid` ), który jest zwracany z następującego polecenia. (Użyjemy `kid` wartości w [kroku 3).](#step-3-generate-and-prepare-your-key-for-transfer)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Krok 2. Pobieranie klucza publicznego klucza KEK

Użyj [funkcji az keyvault key download,](/cli/azure/keyvault/key#az_keyvault_key_download) aby pobrać klucz publiczny KEK do pliku pem. Zaimportowany klucz docelowy jest szyfrowany przy użyciu klucza publicznego KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Przenieś plik KEKforBYOK.publickey.pem na komputer w trybie offline. Ten plik będzie potrzebny w następnym kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3. Generowanie i przygotowywanie klucza do transferu

Zapoznaj się z dokumentacją dostawcy modułu HSM, aby pobrać i zainstalować narzędzie BYOK. Postępuj zgodnie z instrukcjami od dostawcy modułu HSM, aby wygenerować klucz docelowy, a następnie utwórz pakiet transferu kluczy (plik BYOK). Narzędzie BYOK użyje plików z kroku `kid` [1](#step-1-generate-a-kek) i KEKforBYOK.publickey.pem pobranych w kroku [2](#step-2-download-the-kek-public-key) w celu wygenerowania zaszyfrowanego klucza docelowego w pliku BYOK.

Przenieś plik BYOK na połączony komputer.

> [!NOTE] 
> Importowanie kluczy 1024-bitowych RSA nie jest obsługiwane. Importowanie klucza krzywej wielokropka z krzywą P-256K nie jest obsługiwane.
> 
> **Znany problem:** Importowanie klucza docelowego RSA 4K z modułów HSM Firmy Hsm jest obsługiwane tylko w przypadku oprogramowania układowego w wersji 7.4.0 lub nowszej.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Krok 4. Przenoszenie klucza do Azure Key Vault

Aby ukończyć importowanie klucza, przenieś pakiet transferu kluczy (plik BYOK) z odłączonego komputera na komputer połączony z Internetem. Użyj polecenia [az keyvault key import,](/cli/azure/keyvault/key#az_keyvault_key_import) aby przekazać plik BYOK do Key Vault HSM.

Aby zaimportować klucz RSA, użyj następującego polecenia. Parametr --kty jest opcjonalny, a wartość domyślna to "RSA-HSM".
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Aby zaimportować klucz EC, należy określić typ klucza i nazwę krzywej.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Jeśli przekazywanie powiedzie się, interfejs wiersza polecenia platformy Azure wyświetli właściwości zaimportowanych kluczy.

## <a name="next-steps"></a>Następne kroki

Teraz możesz używać tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [to porównanie cen i funkcji.](https://azure.microsoft.com/pricing/details/key-vault/)
