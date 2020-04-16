---
title: Jak generować i przesyłać klucze chronione przez moduł HSM dla usługi Azure Key Vault — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Ten artykuł ułatwia planowanie, generowanie i przenoszenie własnych kluczy chronionych przez moduł HSM do użycia w usłudze Azure Key Vault. Znany również jako przynieść własny klucz (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 77568928e50fb4398aa786dbbd4a44b9277a8d5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429709"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importowanie kluczy chronionych przez moduł HSM do usługi Key Vault (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja jest w wersji zapoznawczej i jest dostępna tylko w regionach platformy Azure *East US 2 EUAP* i *Central US EUAP*. 

Aby uzyskać dodatkową pewność podczas korzystania z usługi Azure Key Vault, można zaimportować lub wygenerować klucz w sprzętowym module zabezpieczeń (HSM); klucz nigdy nie opuści granicy modułu HSM. Ten scenariusz często jest określany jako *bring your own key* (BYOK). Key Vault używa rodziny nCipher nShield modułów HSM (fips 140-2 Poziom 2 zatwierdzone) do ochrony kluczy.

Użyj informacji w tym artykule, aby ułatwić planowanie, generowanie i przenoszenie własnych kluczy chronionych przez moduł HSM do użycia z usługą Azure Key Vault.

> [!NOTE]
> Ta funkcja nie jest dostępna dla platformy Azure China 21Vianet. 
> 
> Ta metoda importu jest dostępna tylko dla [obsługiwanych modułów HSM.](#supported-hsms) 

Aby uzyskać więcej informacji i samouczek, aby rozpocząć korzystanie z usługi Key Vault (w tym jak utworzyć magazyn kluczy dla kluczy chronionych przez moduł HSM), zobacz [Co to jest usługa Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Omówienie

Oto przegląd procesu. W dalszej części artykułu opisano konkretne kroki do wykonania.

* W przechowalni kluczy wygeneruj klucz (określany jako *klucz wymiany klucza* (KEK)). KEK musi być kluczem RSA-HSM, `import` który ma tylko operację klucza. Tylko jednostka SKU usługi Key Vault Premium obsługuje klucze RSA-HSM.
* Pobierz klucz publiczny KEK jako plik pem.
* Przenieś klucz publiczny KEK do komputera w trybie offline, który jest podłączony do lokalnego modułu HSM.
* Na komputerze w trybie offline użyj narzędzia BYOK dostarczonego przez dostawcę modułu HSM, aby utworzyć plik BYOK. 
* Klucz docelowy jest szyfrowany za pomocą KEK, który pozostaje zaszyfrowany, dopóki nie zostanie przeniesiony do modułu HSM usługi Key Vault. Tylko zaszyfrowana wersja klucza opuszcza lokalny moduł HSM.
* KEK, który jest generowany wewnątrz modułu HSM usługi Key Vault nie jest eksportowany. Moduły HSM wymuszają regułę, że nie istnieje żadna przejrzysta wersja KEK poza modułem HSM usługi Key Vault.
* KEK musi znajdować się w tym samym magazynie kluczy, w którym zostanie zaimportowany klucz docelowy.
* Gdy plik BYOK jest przekazywane do usługi Key Vault, moduł HSM usługi Key Vault używa klucza prywatnego KEK do odszyfrowania materiału klucza docelowego i zaimportowania go jako klucza HSM. Ta operacja odbywa się całkowicie wewnątrz modułu HSM magazynu kluczy. Klucz docelowy zawsze pozostaje w granicy ochrony modułu HSM.

## <a name="prerequisites"></a>Wymagania wstępne

W poniższej tabeli wymieniono wymagania wstępne dotyczące używania usługi BYOK w usłudze Azure Key Vault:

| Wymaganie | Więcej informacji |
| --- | --- |
| Subskrypcja platformy Azure |Aby utworzyć magazyn kluczy w usłudze Azure Key Vault, potrzebujesz subskrypcji platformy Azure. [Zarejestruj się, aby uzyskać bezpłatną wersję próbną](https://azure.microsoft.com/pricing/free-trial/). |
| Jednostka SKU premium usługi Key Vault do importowania kluczy chronionych przez moduł HSM |Aby uzyskać więcej informacji na temat warstw i możliwości usług w usłudze Azure Key Vault, zobacz [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Moduł HSM z listy obsługiwanych modułów HSM oraz narzędzie BYOK i instrukcje dostarczone przez dostawcę modułu HSM | Musisz mieć uprawnienia do modułu HSM i podstawową wiedzę na temat sposobu korzystania z modułu HSM. Zobacz [Obsługiwane moduły HSM](#supported-hsms). |
| Interfejsu wiersza polecenia platformy Azure w wersji 2.1.0 lub nowszej | Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>Obsługiwane moduły HSM

|Nazwa dostawcy|Typ dostawcy|Obsługiwane modele HSM|Więcej informacji|
|---|---|---|---|
|Thales|Producent|Rodzina SafeNet Luna HSM 7 z oprogramowaniem układowym w wersji 7.3 lub nowszej| [Narzędzie i dokumentacja SafeNet Luna BYOK](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix ( Fortanix )|HSM jako usługa|Samookadująca się usługa zarządzania kluczami (SDKMS)|[Eksportowanie kluczy SDKMS do dostawców usług w chmurze dla byok — usługa Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Aby zaimportować klucze chronione przez moduł HSM z rodziny modułów HSM nCipher nShield, należy użyć [starszej procedury BYOK](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Obsługiwane typy kluczy

|Nazwa klucza|Typ klucza|Rozmiar klucza|Origin|Opis|
|---|---|---|---|---|
|Klucz wymiany kluczy (KEK)|RSA| 2048 bitów<br />3072-bitowe<br />4096 bitów|Azure Key Vault HSM|Para kluczy RSA z kopii hsm wygenerowana w usłudze Azure Key Vault|
|Klucz docelowy|RSA|2048 bitów<br />3072-bitowe<br />4096 bitów|Moduł HSM dostawcy|Klucz do przeniesienia do modułu HSM usługi Azure Key Vault|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generowanie i przenoszenie klucza do modułu HSM magazynu kluczy

Aby wygenerować i przenieść klucz do modułu HSM usługi Key Vault:

* [Krok 1: Generowanie KEK](#step-1-generate-a-kek)
* [Krok 2: Pobierz klucz publiczny KEK](#step-2-download-the-kek-public-key)
* [Krok 3: Wygeneruj i przygotuj klucz do transferu](#step-3-generate-and-prepare-your-key-for-transfer)
* [Krok 4: Przenieś klucz do usługi Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Krok 1: Generowanie KEK

KEK to klucz RSA, który jest generowany w modułze HSM usługi Key Vault. KEK służy do szyfrowania klucza, który chcesz zaimportować (klucz *docelowy).*

KEK musi być:
- Klucz RSA-HSM (2048-bitowy; 3072-bitowy lub 4096-bitowy)
- Generowane w tym samym magazynie kluczy, w którym zamierzasz zaimportować klucz docelowy
- Utworzono z ustawioną dozwoloną operacją klucza`import`

> [!NOTE]
> KEK musi mieć "import" jako jedyną dozwoloną operację klucza. "import" wzajemnie się wyklucza ze wszystkimi innymi kluczowymi operacjami.

Użyj [polecenia tworzenia klucza az keyvault,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) aby utworzyć `import`kek, który ma ustawioną na . Zapisz identyfikator klucza`kid`( ) który jest zwracany z następującego polecenia. (Użyjesz `kid` wartości w [kroku 3.)](#step-3-generate-and-prepare-your-key-for-transfer)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Krok 2: Pobierz klucz publiczny KEK

Użyj [pobierania klucza az keyvault,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) aby pobrać klucz publiczny KEK do pliku pem. Importowany klucz docelowy jest szyfrowany przy użyciu klucza publicznego KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Przenieś plik KEKforBYOK.publickey.pem na komputer w trybie offline. Ten plik będzie potrzebny w następnym kroku.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Krok 3: Wygeneruj i przygotuj klucz do transferu

Aby pobrać i zainstalować narzędzie BYOK, należy zapoznać się z dokumentacją dostawcy modułu HSM. Postępuj zgodnie z instrukcjami od dostawcy modułu HSM, aby wygenerować klucz docelowy, a następnie utworzyć pakiet transferu kluczy (plik BYOK). Narzędzie BYOK użyje `kid` [z kroku 1](#step-1-generate-a-kek) i pliku KEKforBYOK.publickey.pem pobranego w [kroku 2](#step-2-download-the-kek-public-key) do wygenerowania zaszyfrowanego klucza docelowego w pliku BYOK.

Przenieś plik BYOK na podłączony komputer.

> [!NOTE] 
> Importowanie 1024-bitowych kluczy RSA nie jest obsługiwane. Obecnie importowanie klucza krzywej eliptycznej (EC) nie jest obsługiwane.
> 
> **Znany problem:** Importowanie klucza docelowego RSA 4K z modułów HSM SafeNet Luna jest obsługiwane tylko w oprogramowaniu układowym 7.4.0 lub nowszym.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Krok 4: Przenieś klucz do usługi Azure Key Vault

Aby zakończyć importowanie kluczy, przenieś pakiet transferu kluczy (plik BYOK) z odłączonego komputera do komputera podłączonego do Internetu. Użyj polecenia [importu klucza az keyvault,](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) aby przekazać plik BYOK do modułu HSM usługi Key Vault.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Jeśli przekazywanie zakończy się pomyślnie, narzędzie interfejsu wiersza polecenia platformy Azure wyświetla właściwości importowanego klucza.

## <a name="next-steps"></a>Następne kroki

Teraz można użyć tego klucza chronionego przez moduł HSM w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [porównanie cen i funkcji](https://azure.microsoft.com/pricing/details/key-vault/).



