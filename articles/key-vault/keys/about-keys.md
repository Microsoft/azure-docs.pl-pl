---
title: Informacje o kluczach — Azure Key Vault
description: Omówienie interfejsu REST Azure Key Vault szczegóły dla deweloperów kluczy.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: b9565095a40052a940d7a7b31f0fd3a27e0e75c2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815004"
---
# <a name="about-keys"></a>Informacje o kluczach

Azure Key Vault udostępnia dwa typy zasobów do przechowywania kluczy kryptograficznych i zarządzania nimi. Magazyny obsługują klucze chronione przez oprogramowanie i klucze chronione przez moduł HSM (Sprzętowy moduł zabezpieczeń). Zarządzane moduły HSM obsługują tylko klucze chronione przez moduł HSM. 

|Typ zasobu|Metody ochrony kluczy|Podstawowy adres URL punktu końcowego płaszczyzny danych|
|--|--|--|
| **Magazyny** | Chronione przez oprogramowanie<br/><br/>oraz<br/><br/>Chronione przez moduł HSM (z sku Premium)</li></ul> | https://{nazwa magazynu}.vault.azure.net |
| **Zarządzane moduły HSM** | Chronione przez moduł HSM | https://{nazwa-hsm}.managedhsm.azure.net |
||||

- **Magazyny** — magazyny zapewniają ekonomiczne, łatwe do wdrożenia, wielodostępne, odporne na strefy (jeśli są dostępne) rozwiązanie do zarządzania kluczami o wysokiej dostępie, odpowiednie dla większości typowych scenariuszy aplikacji w chmurze.
- **Zarządzane moduły HSM** — zarządzany moduł HSM udostępnia jednodostępne, odporne na strefy (jeśli są dostępne) moduły HSM o wysokiej dostępie do przechowywania kluczy kryptograficznych i zarządzania nimi. Najbardziej odpowiedni dla aplikacji i scenariuszy użycia, które obsługują klucze o wysokiej wartości. Pomaga również spełnić najbardziej rygorystyczne wymagania dotyczące zabezpieczeń, zgodności i przepisów prawnych. 

> [!NOTE]
> Magazyny umożliwiają również przechowywanie kilku typów obiektów, takich jak wpisy tajne, certyfikaty i klucze kont magazynu, oraz zarządzanie nimi, oprócz kluczy kryptograficznych.

Klucze kryptograficzne w Key Vault są reprezentowane jako JSON Web Key [JWK]. Specyfikacje JavaScript Object Notation (JSON) i JavaScript Object Signing and Encryption (JOSE) to:

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algorytmy internetowe JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Podstawowe specyfikacje JWK/JWA są również rozszerzane w celu włączenia typów kluczy unikatowych dla implementacji Azure Key Vault zarządzanych modułów HSM. 

Klucze chronione przez moduł HSM (nazywane również kluczami HSM) są przetwarzane w module HSM (Sprzętowy moduł zabezpieczeń) i zawsze pozostają granicą ochrony modułu HSM. 

- Magazyny używają modułów HSM zweryfikowanych w programie **FIPS 140-2** poziom 2 do ochrony kluczy HSM w udostępnionej infrastrukturze zaplecza modułu HSM. 
- Zarządzany moduł HSM używa modułów HSM zweryfikowanych w programie **FIPS 140-2 poziom 3** do ochrony kluczy. Każda pula modułów HSM jest izolowanym [](../managed-hsm/security-domain.md) wystąpieniem pojedynczej dzierżawy z własną domeną zabezpieczeń zapewniającą pełną izolację kryptograficzna od wszystkich innych modułów HSM współużytkowanych przez tę samą infrastrukturę sprzętową.

Klucze te są chronione w pulach modułów HSM z jedną dzierżawą. Można zaimportować klucz RSA, EC i klucz symetryczny w postaci nie programowej lub eksportując z obsługiwanego urządzenia HSM. Klucze można również generować w pulach modułów HSM. Zaimportowanie kluczy HSM przy użyciu metody opisanej w specyfikacji [BYOK (bring your own key)](../keys/byok-specification.md)umożliwia bezpieczny materiał klucza transportu do zarządzanych pul modułów HSM. 

Aby uzyskać więcej informacji na temat granic geograficznych, [zobacz Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Typy kluczy i metody ochrony

Key Vault obsługuje klucze RSA i EC. Zarządzany moduł HSM obsługuje klucze RSA, EC i symetryczne. 

### <a name="hsm-protected-keys"></a>Klucze chronione przez moduł HSM

|Typ klucza|Magazyny (tylko wersja Premium SKU)|Zarządzane moduły HSM|
|--|--|--|
|**EC-HSM:** klucz krzywej wielokropka | Obsługiwane | Obsługiwane|
|**RSA-HSM:** klucz RSA|Obsługiwane|Obsługiwane|
|**oct-HSM:** klucz symetryczny|Nieobsługiwane|Obsługiwane|
|||

### <a name="software-protected-keys"></a>Klucze chronione przez oprogramowanie

|Typ klucza|Magazyny|Zarządzane moduły HSM|
|--|--|--|
**RSA:** klucz RSA "chroniony przez oprogramowanie"|Obsługiwane|Nieobsługiwane
**EC:** klucz krzywej wielokropka "chronionej przez oprogramowanie"|Obsługiwane|Nieobsługiwane
|||

### <a name="compliance"></a>Zgodność

|Typ klucza i miejsce docelowe|Zgodność|
|---|---|
|Klucze chronione przez oprogramowanie w magazynach (jednostki SKU w & Premium i Standardowa) | FIPS 140-2 Poziom 1|
|Klucze chronione przez moduł HSM w magazynach (SKU Premium)| FIPS 140-2 Level 2|
|Klucze chronione przez moduł HSM w zarządzanym hsm|FIPS 140-2 Poziom 3|
|||



Zobacz [Typy kluczy, algorytmy](about-keys-details.md) i operacje, aby uzyskać szczegółowe informacje o poszczególnych typach kluczy, algorytmach, operacjach, atrybutach i tagach.

## <a name="next-steps"></a>Następne kroki
- [Informacje o usłudze Key Vault](../general/overview.md)
- [Informacje o zarządzanym module HSM](../managed-hsm/overview.md)
- [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- [Informacje o certyfikatach](../certificates/about-certificates.md)
- [Key Vault interfejsu API REST](../general/about-keys-secrets-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
