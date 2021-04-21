---
title: Azure Key Vault kluczy, wpisów tajnych i certyfikatów — omówienie
description: Omówienie interfejsu AZURE KEY VAULT i szczegółów dewelopera dotyczących kluczy, wpisów tajnych i certyfikatów.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: e3eb77d15c288c93298da6dd79a76565e5d67f96
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749946"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Azure Key Vault kluczy, wpisów tajnych i certyfikatów — omówienie

Azure Key Vault umożliwia aplikacjom Microsoft Azure i użytkownikom przechowywanie i używanie kilku typów danych tajnych/kluczowych. Key Vault zasobów obsługuje dwa typy zasobów: magazyny i zarządzane moduły HSM.

## <a name="dns-suffixes-for-base-url"></a>Sufiksy DNS dla podstawowego adresu URL
 W poniższej tabeli przedstawiono podstawowy sufiks DNS adresu URL używany przez punkt końcowy płaszczyzny danych dla magazynów i zarządzanych pul modułów HSM w różnych środowiskach chmury.

Środowisko chmury | Sufiks DNS dla magazynów | Sufiks DNS dla zarządzanych modułów HSM
---|---|---
Azure Cloud | .vault.azure.net | .managedhsm.azure.net
Azure China Cloud | .vault.azure.cn | Nieobsługiwane
Wersja platformy Azure dla administracji USA | .vault.usgovcloudapi.net | Nieobsługiwane
Niemiecki chmura platformy Azure | .vault.microsoftazure.de | Nieobsługiwane
|||


## <a name="object-types"></a>Typy obiektów
 W poniższej tabeli przedstawiono typy obiektów i ich sufiksy w bazowym adresie URL.

Typ obiektu|Sufiks adresu URL|Magazyny|Zarządzane pule modułów HSM
--|--|--|--
**Klucze kryptograficzne**||
Klucze chronione przez moduł HSM|/keys|Obsługiwane|Obsługiwane
Klucze chronione przez oprogramowanie|/keys|Obsługiwane|Nieobsługiwane
**Inne typy obiektów**||
Wpisy tajne|/secrets|Obsługiwane|Nieobsługiwane
Certyfikaty|/certificates|Obsługiwane|Nieobsługiwane
Klucze kont magazynu|/storage|Obsługiwane|Nieobsługiwane
|||
- **Klucze kryptograficzne:** obsługują wiele typów kluczy i algorytmów oraz umożliwiają korzystanie z kluczy chronionych przez oprogramowanie i hsm. Aby uzyskać więcej informacji, zobacz [About keys (Informacje o kluczach).](../keys/about-keys.md)
- **Wpisy** tajne: zapewnia bezpieczny magazyn wpisów tajnych, takich jak hasła i parametry połączenia bazy danych. Aby uzyskać więcej informacji, zobacz [About secrets (Informacje o wpisach tajnych).](../secrets/about-secrets.md)
- **Certyfikaty:** obsługuje certyfikaty, które są zbudowane na podstawie kluczy i wpisów tajnych, i dodają funkcję automatycznego odnawiania. Aby uzyskać więcej informacji, zobacz [About certificates (Informacje o certyfikatach).](../certificates/about-certificates.md)
- **Klucze konta usługi Azure Storage:** może zarządzać kluczami konta usługi Azure Storage. Wewnętrznie Key Vault listę (synchronizację) kluczy przy użyciu konta usługi Azure Storage i okresowe ponowne generowanie (obracanie) kluczy. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami konta magazynu za pomocą Key Vault](../secrets/overview-storage-keys.md).

Aby uzyskać więcej ogólnych informacji o Key Vault, zobacz [Informacje o Azure Key Vault](overview.md). Aby uzyskać więcej informacji na temat zarządzanych pul modułów HSM, zobacz Co to jest [Azure Key Vault HSM?](../managed-hsm/overview.md)


## <a name="data-types"></a>Typy danych

Zapoznaj się ze specyfikacjami JOSE, aby zapoznać się z odpowiednimi typami danych dla kluczy, szyfrowania i podpisywania.  

-   **algorytm** — obsługiwany algorytm dla operacji klucza, na przykład RSA1_5  
-   **ciphertext-value —** oktety tekstu szyfrowania zakodowane przy użyciu base64URL  
-   **digest-value** — dane wyjściowe algorytmu wyznaczania wartości skrótu zakodowane przy użyciu wartości Base64URL  
-   **typ-klucza** — jeden z obsługiwanych typów kluczy, na przykład RSA (Rivest-Shamir-Adleman).  
-   **plaintext-value —** oktety w postaci zwykłego tekstu zakodowane przy użyciu base64URL  
-   **signature-value —** dane wyjściowe algorytmu podpisu zakodowane przy użyciu wartości Base64URL  
-   **base64URL —** wartość binarna zakodowana w formacie Base64URL [RFC4648]  
-   **wartość logiczna —** prawda lub fałsz  
-   **Tożsamość** — tożsamość z Azure Active Directory (AAD).  
-   **IntDate** — wartość dziesiętna JSON reprezentująca liczbę sekund od 1970-01-01T0:0:0Z UTC do określonej daty/godziny UTC. Zobacz RFC3339, aby uzyskać szczegółowe informacje dotyczące daty/godziny, w szczególności czasu UTC.  

## <a name="objects-identifiers-and-versioning"></a>Obiekty, identyfikatory i wersje

Obiekty przechowywane w Key Vault są wersjonarowane przy każdym utworzeniu nowego wystąpienia obiektu. Każda wersja ma przypisany unikatowy identyfikator i adres URL. Po pierwszym utworzeniu obiektu jest mu nadany unikatowy identyfikator wersji i oznaczany jako bieżąca wersja obiektu. Utworzenie nowego wystąpienia o tej samej nazwie obiektu nadaje nowemu obiektowi unikatowy identyfikator wersji, co powoduje, że staje się on bieżącą wersją.  

Obiekty w Key Vault można rozwiązać, określając wersję lub pomijając wersję operacji w bieżącej wersji obiektu. Na przykład w przypadku klucza o nazwie wykonywanie operacji bez określenia wersji powoduje, że `MasterKey` system używa najnowszej dostępnej wersji. Wykonywanie operacji przy użyciu identyfikatora specyficznego dla wersji powoduje, że system używa tej określonej wersji obiektu.  

### <a name="vault-name-and-object-name"></a>Nazwa magazynu i nazwa obiektu
Obiekty są jednoznacznie identyfikowane w Key Vault przy użyciu adresu URL. Żadne dwa obiekty w systemie nie mają tego samego adresu URL, niezależnie od lokalizacji geograficznej. Pełny adres URL obiektu jest nazywany identyfikatorem obiektu. Adres URL składa się z prefiksu, który identyfikuje Key Vault, typ obiektu, nazwę obiektu podaną przez użytkownika i wersję obiektu. W nazwie obiektu nie jest roz rozwrażliwa i niezmienna. Identyfikatory, które nie zawierają wersji obiektu, są określane jako identyfikatory podstawowe.  

Aby uzyskać więcej informacji, zobacz [Authentication, requests, and responses (Uwierzytelnianie, żądania i odpowiedzi)](authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący ogólny format (w zależności od typu kontenera):  

- **W przypadku magazynów:**`https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **W przypadku zarządzanych pul modułów HSM:**`https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Zobacz [Obsługa typów obiektów](#object-types) dla typów obiektów obsługiwanych przez każdy typ kontenera.

Gdzie:  

| Element | Opis |  
|-|-|  
|`vault-name` lub `hsm-name`|Nazwa magazynu lub zarządzanej puli modułów HSM w Microsoft Azure Key Vault usługi.<br /><br />Nazwy magazynów i nazwy zarządzanych pul modułów HSM są wybierane przez użytkownika i są globalnie unikatowe.<br /><br />Nazwa magazynu i nazwa puli zarządzanego modułu HSM muszą być ciągami znaków od 3 do 24, zawierającymi tylko wartości od 0 do 9, a–z, A–Z i -.|  
|`object-type`|Typ obiektu , "klucze", "wpisy tajne" lub "certyfikaty".|  
|`object-name`|Jest `object-name` nazwą podaną przez użytkownika i musi być unikatowa w obrębie Key Vault. Nazwa musi być ciągiem znaków od 1 do 127, rozpoczynającym się od litery i zawierającym tylko wartości od 0 do 9, a–z, A–Z i -.|  
|`object-version`|To `object-version` generowany przez system, 32-znakowy identyfikator ciągu, który jest opcjonalnie używany do adresowania unikatowej wersji obiektu.|  

## <a name="next-steps"></a>Następne kroki

- [Informacje o kluczach](../keys/about-keys.md)
- [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- [Informacje o certyfikatach](../certificates/about-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
