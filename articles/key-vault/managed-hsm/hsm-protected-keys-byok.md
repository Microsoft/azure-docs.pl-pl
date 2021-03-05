---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla Azure Key Vault zarządzanego modułu HSM — Azure Key Vault | Microsoft Docs
description: Ten artykuł ułatwi zaplanowanie, wygenerowanie i przeniesienie własnych kluczy chronionych przez moduł HSM w celu użycia z zarządzanym modułem HSM. Znana także jako "Przenieś własny klucz" (BYOK).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: dd5b38a858ceba12f5d48f1782da5b85228c4b06
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212114"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Importuj klucze chronione przez moduł HSM do zarządzanego modułu HSM (BYOK)

 Azure Key Vault zarządzanym modułem HSM obsługuje importowanie kluczy generowanych w lokalnym sprzętowym module zabezpieczeń (HSM). klucze nigdy nie pozostawiają granicy ochrony modułu HSM. Ten scenariusz często jest nazywany *własnym kluczem* (BYOK). Zarządzane moduły HSM używają kart HSM LiquidSecurity (poziom 3 trybu FIPS 140-2) do ochrony kluczy.

Informacje przedstawione w tym artykule ułatwiają planowanie, generowanie i przenoszenie własnych kluczy chronionych przez moduł HSM w celu użycia z zarządzanym modułem HSM.

> [!NOTE]
> Ta funkcja jest niedostępna dla platformy Azure w Chinach. Ta metoda importowania jest dostępna tylko dla [obsługiwanych sprzętowych modułów zabezpieczeń](#supported-hsms). 

Aby uzyskać więcej informacji i zapoznać się z samouczkiem, aby rozpocząć korzystanie z zarządzanego modułu HSM, zobacz [co to jest zarządzany moduł HSM?](overview.md).

## <a name="overview"></a>Omówienie

Poniżej przedstawiono omówienie procesu. Konkretne kroki do ukończenia zostały opisane w dalszej części artykułu.

* W zarządzanym module HSM Generuj klucz (nazywany *kluczem wymiany klucza* (KEK)). KEK musi być kluczem HSM RSA, który ma tylko `import` operację klucza. 
* Pobierz klucz publiczny KEK jako plik PEM.
* Prześlij klucz publiczny KEK do komputera w trybie offline, który jest połączony z lokalnym modułem HSM.
* Na komputerze w trybie offline Użyj narzędzia BYOK dostarczonego przez dostawcę modułu HSM, aby utworzyć plik BYOK. 
* Klucz docelowy jest szyfrowany przy użyciu KEK, który pozostaje zaszyfrowany do momentu przetransferowania do zarządzanego modułu HSM. Tylko zaszyfrowana wersja klucza spowoduje pozostawienie lokalnego modułu HSM.
* KEK, który jest generowany w zarządzanym module HSM, nie jest eksportowalny. Sprzętowych modułów zabezpieczeń wymusić regułę, która nie istnieje na zewnątrz zarządzanego modułu HSM.
* KEK musi znajdować się w tym samym zarządzanym module HSM, w którym zostanie zaimportowany klucz docelowy.
* Gdy plik BYOK jest przekazywany do zarządzanego modułu HSM, zarządzany moduł HSM używa klucza prywatnego KEK w celu odszyfrowania materiału klucza docelowego i zaimportowania go jako klucza HSM. Ta operacja występuje całkowicie wewnątrz modułu HSM. Klucz docelowy zawsze pozostaje w granicy ochrony modułu HSM.


## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z poleceń interfejsu wiersza polecenia platformy Azure w tym artykule, należy dysponować następującymi elementami:

* Subskrypcja platformy Microsoft Azure. Jeśli go nie masz, możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial).
* Interfejs wiersza polecenia platformy Azure w wersji 2.12.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* Zarządzanym modułem HSM [sprzętowych modułów zabezpieczeń listę obsługiwaną](#supported-hsms) w Twojej subskrypcji. Zobacz [Szybki Start: udostępnianie i aktywowanie zarządzanego modułu HSM przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md) w celu aprowizacji i aktywowania zarządzanego modułu HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia, możesz wpisać:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat opcji logowania za pośrednictwem interfejsu wiersza polecenia, zobacz [Logowanie za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli)

## <a name="supported-hsms"></a>Obsługiwane sprzętowych modułów zabezpieczeń

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele HSM|Więcej informacji|
|---|---|---|---|
|Oprogramowanie wspomagające nCipher|Instrukcj<br/>Moduł HSM jako usługa|<ul><li>Rodzina sprzętowego nshield sprzętowych modułów zabezpieczeń</li><li>Sprzętowego nshield jako usługa</ul>|[Oprogramowanie wspomagające nCipher nowe narzędzie BYOK i dokumentacja](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Firmy Thales|Producent|<ul><li>Rodzina Luna HSM 7 z oprogramowaniem układowym w wersji 7,3 lub nowszej</li></ul>| [Luna BYOK — narzędzie i dokumentacja](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Instrukcj<br/>Moduł HSM jako usługa|<ul><li>Usługa zarządzania kluczami Self-Defending (SDKMS)</li><li>Equinix SmartKey</li></ul>|[Eksportowanie kluczy SDKMS do dostawców chmury dla BYOK-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Firmy|Producent|Wszystkie LiquidSecurity sprzętowych modułów zabezpieczeń with<ul><li>Oprogramowanie układowe w wersji 2.0.4 lub nowszej</li><li>Oprogramowanie układowe w wersji 3,2 lub nowszej</li></ul>|[Narzędzie i dokumentacja BYOK firmy Marvell](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|Niezależnego dostawcy oprogramowania (system zarządzania kluczami przedsiębiorstwa)|Wiele marek i modeli modułu HSM, w tym<ul><li>Oprogramowanie wspomagające nCipher</li><li>Firmy Thales</li><li>Utimaco</li></ul>[Aby uzyskać szczegółowe informacje, zobacz witrynę Cryptomathic](https://www.cryptomathic.com/azurebyok)|[Cryptomathic BYOK — narzędzie i dokumentacja](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Producent, moduł HSM jako usługa|Rodzina modułów HSM Primus, moduł HSM chmur Securosys|[Primus BYOK — narzędzie i dokumentacja](https://www.securosys.com/primus-azure-byok)|
|StorMagic|Niezależnego dostawcy oprogramowania (system zarządzania kluczami przedsiębiorstwa)|Wiele marek i modeli modułu HSM, w tym<ul><li>Utimaco</li><li>Firmy Thales</li><li>Oprogramowanie wspomagające nCipher</li></ul>[Aby uzyskać szczegółowe informacje, zobacz witrynę StorMagic](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS i Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Producent|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Instrukcj<br/>Moduł HSM jako usługa|zakotwiczenie u. zaufania, CryptoServer|[Przewodnik po narzędziu i integracji narzędzia Utimaco BYOK](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Obsługiwane typy kluczy

|Nazwa klucza|Typ klucza|Rozmiar/krzywa klucza|Origin|Opis|
|---|---|---|---|---|
|Klucz wymiany klucza (KEK)|RSA| 2 048 — bit<br />3 072 — bit<br />4 096 — bit|Zarządzany moduł HSM|Para kluczy RSA z kopią zapasową modułu HSM wygenerowaną w zarządzanym module HSM|
|Klucz docelowy|
||RSA|2 048 — bit<br />3 072 — bit<br />4 096 — bit|Moduł HSM dostawcy|Klucz, który ma zostać przesłany do zarządzanego modułu HSM|
||EC|P-256<br />P-384<br />P-521|Moduł HSM dostawcy|Klucz, który ma zostać przesłany do zarządzanego modułu HSM|
||Klucz symetryczny (OCT-HSM)|128 — bit<br />192 — bit<br />256 — bit|Moduł HSM dostawcy|Klucz, który ma zostać przesłany do zarządzanego modułu HSM|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Generowanie i transferowanie klucza do zarządzanego modułu HSM

Aby wygenerować klucz i przesłać go do zarządzanego modułu HSM:

* [Krok 1. Generowanie elementu KEK](#step-1-generate-a-kek)
* [Krok 2. Pobieranie klucza publicznego KEK](#step-2-download-the-kek-public-key)
* [Krok 3. Generowanie i przygotowywanie klucza do przeniesienia](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4. przeniesienie klucza do zarządzanego modułu HSM](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Krok 1. Generowanie elementu KEK

KEK jest kluczem RSA, który jest generowany w zarządzanym module HSM. KEK jest używany do szyfrowania klucza do zaimportowania (klucz *docelowy* ).

KEK musi być:
- Klucz modułu HSM RSA (2 048-bitowy; 3 072-bitowy; lub 4 096-bitowy)
- Generowane w tym samym zarządzanym module HSM, w którym zamierzasz zaimportować klucz docelowy
- Utworzono z dozwolonymi operacjami Key ustawionymi na `import`

> [!NOTE]
> KEK musi mieć element "Import" jako jedyną dozwoloną operację klucza. "Import" wykluczają się wzajemnie poza wszystkimi innymi kluczowymi operacjami.

Za pomocą polecenia [AZ Key magazynu Utwórz](/cli/azure/keyvault/key#az-keyvault-key-create) polecenie, aby utworzyć element KEK, który ma kluczowe operacje ustawione na `import` . Zapisz identyfikator klucza ( `kid` ), który jest zwracany przez następujące polecenie. (Wartość zostanie użyta `kid` w [kroku 3](#step-3-generate-and-prepare-your-key-for-transfer)).

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Krok 2. Pobieranie klucza publicznego KEK

Użyj [AZ Key magazynu klucza Download](/cli/azure/keyvault/key#az-keyvault-key-download) , aby pobrać klucz publiczny KEK do pliku PEM. Zaimportowany klucz docelowy jest szyfrowany przy użyciu klucza publicznego KEK.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Prześlij plik KEKforBYOK. PublicKey. pem do komputera w trybie offline. Ten plik będzie potrzebny w następnym kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3. Generowanie i przygotowywanie klucza do przeniesienia

Zapoznaj się z dokumentacją dostawcy modułu HSM, aby pobrać i zainstalować narzędzie BYOK. Postępuj zgodnie z instrukcjami dostawcy modułu HSM, aby wygenerować klucz docelowy, a następnie Utwórz pakiet transferu kluczy (plik BYOK). Narzędzie BYOK będzie używać `kid` od [kroku 1](#step-1-generate-a-kek) i pliku KEKforBYOK. PublicKey. pem pobranego w [kroku 2](#step-2-download-the-kek-public-key) , aby wygenerować zaszyfrowany klucz docelowy w pliku BYOK.

Prześlij plik BYOK na podłączonym komputerze.

> [!NOTE] 
> Importowanie kluczy RSA 1 024-bitowe nie jest obsługiwane. Obecnie Importowanie klucza krzywej eliptyczna (EC) nie jest obsługiwane.
>
> **Znany problem**: Importowanie klucza docelowego RSA 4K z Luna sprzętowych modułów zabezpieczeń jest obsługiwane tylko przy użyciu oprogramowania układowego 7.4.0 lub nowszego.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Krok 4. przeniesienie klucza do zarządzanego modułu HSM

Aby ukończyć Importowanie klucza, Przenieś pakiet transferu kluczy (plik BYOK) z odłączonego komputera na komputer połączony z Internetem. Aby przekazać plik BYOK do zarządzanego modułu HSM, użyj polecenia [AZ Key import](/cli/azure/keyvault/key#az-keyvault-key-import) .

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Jeśli przekazywanie zakończy się pomyślnie, interfejs wiersza polecenia platformy Azure wyświetli właściwości zaimportowanego klucza.

## <a name="next-steps"></a>Następne kroki

Teraz można używać tego klucza chronionego przez moduł HSM w zarządzanym module HSM. Aby uzyskać więcej informacji, zobacz [to porównanie cen i funkcji](https://azure.microsoft.com/pricing/details/key-vault/).



