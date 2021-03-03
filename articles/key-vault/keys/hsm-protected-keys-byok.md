---
title: Generowanie & transferu kluczy chronionych przez moduł HSM — BYOK — Azure Key Vault
description: Ten artykuł ułatwia planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault. Znana także jako "Przenieś własny klucz" (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 1e7ea0dc929fdbb4ca306405e6ed8993ed2e4afe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "100386105"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Importuj klucze chronione przez moduł HSM do Key Vault (BYOK)

Aby uzyskać gwarancję w przypadku korzystania z Azure Key Vault, można zaimportować lub wygenerować klucz w sprzętowym module zabezpieczeń (HSM). klucz nigdy nie pozostawia granicy modułu HSM. Ten scenariusz często jest nazywany *własnym kluczem* (BYOK). W celu ochrony kluczy Key Vault używa rodziny oprogramowanie wspomagające nCipher sprzętowego nshield sprzętowych modułów zabezpieczeń (zweryfikowany poziom 2 trybu FIPS 140-2).

Informacje przedstawione w tym artykule ułatwiają planowanie, generowanie i transferowanie własnych kluczy chronionych przez moduł HSM w celu korzystania z Azure Key Vault.

> [!NOTE]
> Ta funkcja jest niedostępna dla platformy Azure w Chinach. 
> 
> Ta metoda importowania jest dostępna tylko dla [obsługiwanych sprzętowych modułów zabezpieczeń](#supported-hsms). 

Aby uzyskać więcej informacji, a także zapoznać się z samouczkiem, aby rozpocząć korzystanie z Key Vault (w tym o sposobie tworzenia magazynu kluczy dla kluczy chronionych przez moduł HSM), zobacz [co to jest Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Omówienie

Poniżej przedstawiono omówienie procesu. Konkretne kroki do ukończenia zostały opisane w dalszej części artykułu.

* W Key Vault Wygeneruj klucz (nazywany *kluczem wymiany klucza* (KEK)). KEK musi być kluczem HSM RSA, który ma tylko `import` operację klucza. Tylko Key Vault SKU Premium obsługuje klucze modułu HSM RSA.
* Pobierz klucz publiczny KEK jako plik PEM.
* Prześlij klucz publiczny KEK do komputera w trybie offline, który jest połączony z lokalnym modułem HSM.
* Na komputerze w trybie offline Użyj narzędzia BYOK dostarczonego przez dostawcę modułu HSM, aby utworzyć plik BYOK. 
* Klucz docelowy jest szyfrowany przy użyciu KEK, który pozostaje zaszyfrowany do momentu przeniesienia do modułu HSM Key Vault. Tylko zaszyfrowana wersja klucza spowoduje pozostawienie lokalnego modułu HSM.
* KEK, który jest generowany wewnątrz modułu HSM Key Vault, nie jest eksportowalny. Sprzętowych modułów zabezpieczeń wymusić regułę, która nie istnieje na zewnątrz modułu HSM Key Vault.
* KEK musi znajdować się w tym samym magazynie kluczy, w którym zostanie zaimportowany klucz docelowy.
* Gdy plik BYOK jest przekazywany do Key Vault, moduł HSM Key Vault używa klucza prywatnego KEK w celu odszyfrowania materiału klucza docelowego i zaimportowania go jako klucza HSM. Ta operacja występuje całkowicie wewnątrz modułu HSM Key Vault. Klucz docelowy zawsze pozostaje w granicy ochrony modułu HSM.

## <a name="prerequisites"></a>Wymagania wstępne

Poniższa tabela zawiera listę wymagań wstępnych dotyczących używania programu BYOK w Azure Key Vault:

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Do utworzenia magazynu kluczy w Azure Key Vault potrzebna jest subskrypcja platformy Azure. [Zarejestruj się, aby skorzystać z bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/). |
| Jednostka SKU Key Vault Premium do importowania kluczy chronionych przez moduł HSM |Aby uzyskać więcej informacji na temat warstw i możliwości usług w Azure Key Vault, zobacz [cennik Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Moduł HSM z listy obsługiwanych sprzętowych modułów zabezpieczeń oraz narzędzie BYOK i instrukcje dostarczone przez dostawcę modułu HSM | Musisz mieć uprawnienia do modułu HSM oraz podstawową wiedzę na temat sposobu korzystania z modułu HSM. Zobacz [obsługiwane sprzętowych modułów zabezpieczeń](#supported-hsms). |
| Interfejs wiersza polecenia platformy Azure w wersji 2.1.0 lub nowszej | Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).|

## <a name="supported-hsms"></a>Obsługiwane sprzętowych modułów zabezpieczeń

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele HSM|Więcej informacji|
|---|---|---|---|
|Oprogramowanie wspomagające nCipher|Instrukcj<br/>Moduł HSM jako usługa|<ul><li>Rodzina sprzętowego nshield sprzętowych modułów zabezpieczeń</li><li>Sprzętowego nshield jako usługa</ul>|[Oprogramowanie wspomagające nCipher nowe narzędzie BYOK i dokumentacja](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Firmy Thales|Producent|<ul><li>Rodzina Luna HSM 7 z oprogramowaniem układowym w wersji 7,3 lub nowszej</li></ul>| [Luna BYOK — narzędzie i dokumentacja](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Instrukcj<br/>Moduł HSM jako usługa|<ul><li>Usługa zarządzania kluczami Self-Defending (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Eksportowanie kluczy SDKMS do dostawców chmury dla BYOK-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Firmy|Producent|Wszystkie LiquidSecurity sprzętowych modułów zabezpieczeń with<ul><li>Oprogramowanie układowe w wersji 2.0.4 lub nowszej</li><li>Oprogramowanie układowe w wersji 3,2 lub nowszej</li></ul>|[Narzędzie i dokumentacja BYOK firmy Marvell](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|Niezależnego dostawcy oprogramowania (system zarządzania kluczami przedsiębiorstwa)|Wiele marek i modeli modułu HSM, w tym<ul><li>Oprogramowanie wspomagające nCipher</li><li>Firmy Thales</li><li>Utimaco</li></ul>[Aby uzyskać szczegółowe informacje, zobacz witrynę Cryptomathic](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK — narzędzie i dokumentacja](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Instrukcj<br/>Moduł HSM jako usługa|Rodzina modułów HSM Primus, moduł HSM chmur Securosys|[Primus BYOK — narzędzie i dokumentacja](https://www.securosys.com/primus-azure-byok)|
|StorMagic|Niezależnego dostawcy oprogramowania (system zarządzania kluczami przedsiębiorstwa)|Wiele marek i modeli modułu HSM, w tym<ul><li>Utimaco</li><li>Firmy Thales</li><li>Oprogramowanie wspomagające nCipher</li></ul>[Aby uzyskać szczegółowe informacje, zobacz witrynę StorMagic](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS i Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Producent|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Instrukcj<br/>Moduł HSM jako usługa|zakotwiczenie u. zaufania, CryptoServer|[Przewodnik po narzędziu i integracji narzędzia Utimaco BYOK](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Obsługiwane typy kluczy

|Nazwa klucza|Typ klucza|Rozmiar/krzywa klucza|Origin|Opis|
|---|---|---|---|---|
|Klucz wymiany klucza (KEK)|RSA| 2 048 — bit<br />3 072 — bit<br />4 096 — bit|Moduł HSM Azure Key Vault|Para kluczy RSA z kopią zapasową modułu HSM wygenerowaną w Azure Key Vault|
|Klucz docelowy|
||RSA|2 048 — bit<br />3 072 — bit<br />4 096 — bit|Moduł HSM dostawcy|Klucz, który ma zostać przesłany do modułu HSM Azure Key Vault|
||EC|P-256<br />P-384<br />P-521|Moduł HSM dostawcy|Klucz, który ma zostać przesłany do modułu HSM Azure Key Vault|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generowanie i przenoszenie klucza do modułu HSM Key Vault

Aby wygenerować i przesłać klucz do Key Vault modułu HSM:

* [Krok 1. Generowanie elementu KEK](#step-1-generate-a-kek)
* [Krok 2. Pobieranie klucza publicznego KEK](#step-2-download-the-kek-public-key)
* [Krok 3. Generowanie i przygotowywanie klucza do przeniesienia](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4. przeniesienie klucza do Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Krok 1. Generowanie elementu KEK

KEK jest kluczem RSA, który jest generowany w Key Vault module HSM. KEK jest używany do szyfrowania klucza do zaimportowania (klucz *docelowy* ).

KEK musi być:
- Klucz modułu HSM RSA (2 048-bitowy; 3 072-bitowy; lub 4 096-bitowy)
- Generowane w tym samym magazynie kluczy, w którym zamierzasz zaimportować klucz docelowy
- Utworzono z dozwolonymi operacjami Key ustawionymi na `import`

> [!NOTE]
> KEK musi mieć element "Import" jako jedyną dozwoloną operację klucza. "Import" wykluczają się wzajemnie poza wszystkimi innymi kluczowymi operacjami.

Za pomocą polecenia [AZ Key magazynu Utwórz](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-create) polecenie, aby utworzyć element KEK, który ma kluczowe operacje ustawione na `import` . Zapisz identyfikator klucza ( `kid` ), który jest zwracany przez następujące polecenie. (Wartość zostanie użyta `kid` w [kroku 3](#step-3-generate-and-prepare-your-key-for-transfer)).

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Krok 2. Pobieranie klucza publicznego KEK

Użyj [AZ Key magazynu klucza Download](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-download) , aby pobrać klucz publiczny KEK do pliku PEM. Zaimportowany klucz docelowy jest szyfrowany przy użyciu klucza publicznego KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Prześlij plik KEKforBYOK. PublicKey. pem do komputera w trybie offline. Ten plik będzie potrzebny w następnym kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3. Generowanie i przygotowywanie klucza do przeniesienia

Zapoznaj się z dokumentacją dostawcy modułu HSM, aby pobrać i zainstalować narzędzie BYOK. Postępuj zgodnie z instrukcjami dostawcy modułu HSM, aby wygenerować klucz docelowy, a następnie Utwórz pakiet transferu kluczy (plik BYOK). Narzędzie BYOK będzie używać `kid` od [kroku 1](#step-1-generate-a-kek) i pliku KEKforBYOK. PublicKey. pem pobranego w [kroku 2](#step-2-download-the-kek-public-key) , aby wygenerować zaszyfrowany klucz docelowy w pliku BYOK.

Prześlij plik BYOK na podłączonym komputerze.

> [!NOTE] 
> Importowanie kluczy RSA 1 024-bitowe nie jest obsługiwane. Importowanie klucza krzywej eliptyczna z krzywą P-256 K nie jest obsługiwane.
> 
> **Znany problem**: Importowanie klucza docelowego RSA 4K z Luna sprzętowych modułów zabezpieczeń jest obsługiwane tylko przy użyciu oprogramowania układowego 7.4.0 lub nowszego.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Krok 4. przeniesienie klucza do Azure Key Vault

Aby ukończyć Importowanie klucza, Przenieś pakiet transferu kluczy (plik BYOK) z odłączonego komputera na komputer połączony z Internetem. Aby przekazać plik BYOK do modułu HSM Key Vault, użyj polecenia [AZ Key import](/cli/azure/keyvault/key?view=azure-cli-latest&preserve-view=true#az-keyvault-key-import) .

Aby zaimportować klucz RSA, użyj następującego polecenia. Parametr--kty jest opcjonalny i ma wartość domyślną "RSA-HSM".
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Aby zaimportować klucz we, należy określić typ klucza i nazwę krzywej.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Jeśli przekazywanie zakończy się pomyślnie, interfejs wiersza polecenia platformy Azure wyświetli właściwości zaimportowanego klucza.

## <a name="next-steps"></a>Następne kroki

Teraz można używać tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [to porównanie cen i funkcji](https://azure.microsoft.com/pricing/details/key-vault/).



