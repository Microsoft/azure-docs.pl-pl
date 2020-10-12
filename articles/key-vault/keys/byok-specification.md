---
title: Wprowadź własną specyfikację klucza — Azure Key Vault | Microsoft Docs
description: W tym dokumencie opisano sposób wprowadzenia własnej specyfikacji klucza.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: feef35ef86a933f32949468366fea85eb87d4866
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315783"
---
# <a name="bring-your-own-key-specification"></a>Specyfikacja używania własnego klucza

W tym dokumencie opisano specyfikacje dotyczące importowania kluczy chronionych przez moduł HSM z sprzętowych modułów zabezpieczeń lokalnego klientów do Key Vault.

## <a name="scenario"></a>Scenariusz

Klient Key Vault chce bezpiecznie przetransferować klucz z lokalnego modułu HSM poza platformę Azure do Azure Key Vault kopii zapasowych modułu HSM. Proces importowania klucza wygenerowanego poza Key Vault jest zwykle określany jako Bring Your Own Key (BYOK).

Wymagania są następujące:
* Klucz, który ma zostać przesłany nigdy nie istnieje poza modułem HSM w postaci zwykłego tekstu.
* Klucz, który ma być transferowany poza modułem HSM, jest zawsze chroniony przez klucz przechowywany w module HSM Azure Key Vault

## <a name="terminology"></a>Terminologia

|Nazwa klucza|Typ klucza|Origin|Opis|
|---|---|---|---|
|Klucz wymiany klucza (KEK)|RSA|Moduł HSM Azure Key Vault|Para kluczy RSA z kopią zapasową modułu HSM wygenerowaną w Azure Key Vault
Zawijanie klucza|AES|Moduł HSM dostawcy|Klucz [tymczasowych] AES generowany przez moduł HSM Premium
Klucz docelowy|RSA, WE, AES|Moduł HSM dostawcy|Klucz, który ma zostać przesłany do modułu HSM Azure Key Vault

**Klucz wymiany klucza**: klucz do wykonania kopii zapasowej generowany przez klienta w magazynie kluczy, w którym zostanie zaimportowany klucz BYOK. Ta KEK musi mieć następujące właściwości:

* Jest kluczem modułu HSM RSA (4096-bitowym lub 3072-bitowym lub 2048-bitowym)
* Nastąpi stałe key_ops (import), które umożliwią korzystanie z niego tylko podczas BYOK
* Musi znajdować się w tym samym magazynie, w którym zostanie zaimportowany klucz docelowy

## <a name="user-steps"></a>Kroki użytkownika

W celu przeprowadzenia transferu klucza użytkownik wykonuje następujące czynności:

1. Generuj KEK.
2. Pobierz klucz publiczny KEK.
3. Za pomocą narzędzia BYOK dostarczonego przez dostawcę modułu HSM zaimportuj KEK do docelowego modułu HSM i wyeksportuj klucz docelowy chroniony przez KEK.
4. Zaimportuj chroniony klucz docelowy do Azure Key Vault.

Klienci używają narzędzia BYOK i dokumentacji dostarczonej przez dostawcę modułu HSM, aby wykonać kroki 3. Generuje kluczowy obiekt BLOB transferu (plik ". BYOK").


## <a name="hsm-constraints"></a>Ograniczenia modułu HSM

Istniejący moduł HSM może stosować ograniczenia związane z zarządzanym kluczem, w tym:
* Może być konieczne skonfigurowanie modułu HSM w taki sposób, aby zezwalał na eksport oparty na zawijaniu kluczy
* Aby można było zezwolić na użycie kontrolowanego eksportu, może być konieczne oznaczenie klucza docelowego CKA_EXTRACTABLE dla modułu HSM
* W niektórych przypadkach może być konieczne oznaczenie klucza KEK i otoki jako CKA_TRUSTED. Dzięki temu można używać go do zawijania kluczy w module HSM.

Konfiguracja źródłowego modułu HSM jest zwykle poza zakresem tej specyfikacji. Firma Microsoft oczekuje, że dostawca modułu HSM wygenerował dokumentację dołączoną do narzędzia BYOK w celu uwzględnienia takich kroków konfiguracyjnych.

> [!NOTE]
> Kroki 1, 2 i 4 opisane poniżej można wykonać przy użyciu innych interfejsów, takich jak Azure PowerShell i witryna Azure Portal. Mogą być również wykonywane programowo przy użyciu równoważnych funkcji w Key Vault SDK.

### <a name="step-1-generate-kek"></a>Krok 1. Generowanie KEK

Za pomocą polecenia **AZ Key magazynu klucz Create Utwórz** KEK z zestawem operacji Key, które mają zostać zaimportowane. Zanotuj identyfikator klucza "dziecko" zwrócony przez poniższe polecenie.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>Krok 2. Pobieranie klucza publicznego KEK

Pobierz część klucza publicznego KEK i Zapisz ją w pliku PEM.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>Kroki 3. Generowanie obiektu BLOB transferu kluczy przy użyciu dostarczonego przez dostawcę modułu HSM narzędzia BYOK

Klient będzie używać narzędzia BYOK dostarczonego przez dostawcę modułu HSM do tworzenia obiektu BLOB transferu klucza (przechowywanego jako plik ". BYOK"). Klucz publiczny KEK (plik PEM) będzie jednym z danych wejściowych tego narzędzia.

#### <a name="key-transfer-blob"></a>Obiekt BLOB transferu kluczy
Długoterminowe firma Microsoft chce użyć mechanizmu CKM_RSA_AES_KEY_WRAP PKCS # 11 do transferu klucza docelowego do Azure Key Vault, ponieważ ten mechanizm tworzy pojedynczy obiekt BLOB i co ważniejsze, pośredni klucz AES jest obsługiwany przez dwa sprzętowych modułów zabezpieczeń i ma gwarancję, że jest to wartość tymczasowych. Ten mechanizm nie jest obecnie dostępny w niektórych sprzętowych modułów zabezpieczeń, ale połączenie klucza docelowego z CKM_AES_KEY_WRAP_PAD przy użyciu klucza AES i ochrona klucza AES przy użyciu CKM_RSA_PKCS_OAEP powoduje utworzenie równoważnego obiektu BLOB.

Tekst klucza docelowego zależy od typu klucza: 
* W przypadku klucza RSA kodowanie klucza prywatnego ASN. 1 DER [RFC3447] opakowane w PKCS # 8 [RFC5208] 
* W przypadku klucza w postaci klucza prywatnego numer ASN. 1 DER Encoding [RFC5915] opakowany w formacie PKCS # 8 [RFC5208]
* W przypadku klucza oktetowego nieprzetworzone bajty klucza

Bajty dla klucza w postaci zwykłego tekstu są następnie przekształcane przy użyciu mechanizmu CKM_RSA_AES_KEY_WRAP:
* Klucz tymczasowych AES jest generowany i szyfrowany za pomocą klucza otoki RSA przy użyciu RSA-OAEP z algorytmem SHA1.
* Zakodowany klucz w postaci zwykłego tekstu jest szyfrowany przy użyciu klucza AES przy użyciu zawijania kluczy AES z uzupełnieniem.
* Zaszyfrowany klucz AES i zaszyfrowany klucz w postaci zwykłego tekstu są łączone w celu utworzenia końcowego obiektu BLOB szyfrowanego.

Format obiektu BLOB transferu używa serializacji kompaktowej szyfrowania sieci Web (RFC7516) JSON jako pojazdu do dostarczania wymaganych metadanych do usługi w celu poprawnego odszyfrowywania.

Jeśli CKM_RSA_AES_KEY_WRAP_PAD jest używany, serializacji JSON obiektu BLOB transferu będzie:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* dziecko = identyfikator klucza KEK. W przypadku Key Vault kluczy wygląda to następująco: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = algorytm. 
* dir = tryb bezpośredni, tj. dziecko, do którego się odwołuje, jest używane do bezpośredniej ochrony tekstu szyfrowanego, który jest dokładną reprezentacją CKM_RSA_AES_KEY_WRAP
* Generator = pole informacyjne, które oznacza nazwę i wersję narzędzia BYOK oraz źródłowego producenta i modelu modułu HSM. Te informacje są przeznaczone do użycia w rozwiązywaniu problemów i obsłudze.

Obiekt BLOB JSON jest przechowywany w pliku z rozszerzeniem ". BYOK", dzięki czemu klienci Azure PowerShell/CLI traktują go prawidłowo, gdy używane są polecenia "Add-AzKeyVaultKey" (PSH —) lub "AZ Storage Key Imports" (CLI).

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>Krok 4. przekazywanie obiektu BLOB transferu klucza w celu zaimportowania klucza HSM

Klient przetransferuje plik BLOB transferu kluczy (". BYOK") na stację roboczą online, a następnie uruchamia polecenie **AZ Storage Key import** w celu zaimportowania tego obiektu BLOB jako nowego klucza z kopią zapasową modułu HSM do Key Vault. 

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```

Wykonanie powyższego polecenia powoduje wysłanie żądania interfejsu API REST w następujący sposób:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Treść żądania:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```
wartość "key_hsm" to cała zawartość KeyTransferPackage-ContosoFirstHSMkey. BYOK zakodowana w formacie base64.

## <a name="references"></a>Odwołania
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)

## <a name="next-steps"></a>Następne kroki
* Instrukcje krok po kroku: [Importowanie kluczy chronionych przez moduł HSM do Key Vault (BYOK)](hsm-protected-keys-byok.md)

