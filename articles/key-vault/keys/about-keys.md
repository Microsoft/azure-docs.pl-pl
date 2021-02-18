---
title: Klucze — informacje Azure Key Vault
description: Omówienie Azure Key Vault interfejsu REST i szczegółów dla deweloperów kluczy.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 3c4bb61217c7b972220a55a4837c2b3db980f2ca
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095984"
---
# <a name="about-keys"></a>Informacje o kluczach

Azure Key Vault oferuje dwa typy zasobów do przechowywania kluczy kryptograficznych i zarządzania nimi. Magazyny obsługują klucze chronione przez oprogramowanie oraz chronione przez moduł HSM (sprzętowego modułu zabezpieczeń). Zarządzane sprzętowych modułów zabezpieczeń obsługują tylko klucze chronione przez moduł HSM. 

|Typ zasobu|Metody ochrony klucza|Podstawowy adres URL punktu końcowego płaszczyzny danych|
|--|--|--|
| **Magazyny** | Chronione przez oprogramowanie<br/><br/>oraz<br/><br/>Chronione przez moduł HSM (z jednostką SKU Premium)</li></ul> | https://{nazwa magazynu}. magazyn. Azure. NET |
| * * Zarządzane sprzętowych modułów zabezpieczeń * * | Chronione przez moduł HSM | https://{HSM-Name}. managedhsm. Azure. NET |
||||

- **Magazyny** — magazyny zapewniają niedrogie, łatwe do wdrożenia, wiele dzierżawców, odporne na strefy (tam, gdzie to możliwe) rozwiązanie do zarządzania kluczami o wysokiej dostępności odpowiednie dla najpopularniejszych scenariuszy aplikacji w chmurze.
- **Zarządzany** moduł HSM zarządzany przez sprzętowych modułów zabezpieczeń zapewnia pojedyncze dzierżawy, odporne na strefy (o ile są dostępne), sprzętowych modułów zabezpieczeń o wysokiej dostępności do przechowywania kluczy kryptograficznych i zarządzania nimi. Najbardziej odpowiednie dla aplikacji i scenariuszy użycia, które obsługują klucze o wysokiej wartości. Pomaga również spełnić najbardziej rygorystyczne wymagania dotyczące zabezpieczeń, zgodności i przepisów. 

> [!NOTE]
> Magazyny umożliwiają również przechowywanie kilku typów obiektów, takich jak wpisy tajne, certyfikaty i klucze kont magazynu, oraz zarządzanie nimi, oprócz kluczy kryptograficznych.

Klucze kryptograficzne w Key Vault są reprezentowane jako obiekty klucza internetowego JSON [JWK]. Specyfikacje JavaScript Object Notation (JSON) i podpisywanie i szyfrowanie obiektów JavaScript są następujące:

-   [Klucz internetowy JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Szyfrowanie sieci Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algorytmy sieci Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Podpis internetowy JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Podstawowe specyfikacje JWK/JWA są również rozszerzane w celu włączenia typów kluczy unikatowych dla implementacji Azure Key Vault i zarządzanych modułów HSM. 

Klucze chronione przez moduł HSM (określane również jako klucze HSM) są przetwarzane w module HSM (sprzętowego modułu zabezpieczeń) i zawsze pozostają na granicy ochrony HSM. 

- Magazyny korzystają z zweryfikowanych sprzętowych modułów zabezpieczeń **poziomu 2 FIPS 140-2** do ochrony kluczy HSM w udostępnionej infrastrukturze zaplecza modułu HSM. 
- W zarządzanym module HSM są używane moduły HSM w trybie **FIPS 140-2 Level 3** do ochrony kluczy. Każda pula modułów HSM jest izolowanym wystąpieniem pojedynczej dzierżawy z własną [domeną zabezpieczeń](../managed-hsm/security-domain.md) zapewniającą kompletną izolację kryptograficzną od wszystkich innych sprzętowych modułów zabezpieczeń udostępniania tej samej infrastruktury sprzętowej.

Te klucze są chronione w pulach modułu HSM o pojedynczej dzierżawie. W postaci miękkiej lub wyeksportowanej z obsługiwanego urządzenia HSM można zaimportować klucz RSA, EC i symetryczny. Klucze można również generować w pulach HSM. Podczas importowania kluczy HSM przy użyciu metody opisanej w [BYOK (Dostarcz własny klucz) Specyfikacja](../keys/byok-specification.md)umożliwia bezpieczny materiał klucza transportowego do zarządzanych pul modułu HSM. 

Aby uzyskać więcej informacji na temat granic geograficznych, zobacz [Microsoft Azure Centrum zaufania](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Typy kluczy i metody ochrony

Key Vault obsługuje klucze RSA i CE. Zarządzany moduł HSM obsługuje klucze RSA, EC i symetryczne. 

### <a name="hsm-protected-keys"></a>Klucze chronione przez moduł HSM

|Typ klucza|Magazyny (tylko wersja Premium)|Zarządzane sprzętowych modułów zabezpieczeń|
|--|--|--|
|**EC-HSM**: klucz krzywej eliptycznej | Obsługiwane | Obsługiwane|
|**RSA-HSM**: klucz RSA|Obsługiwane|Obsługiwane|
|**OCT-HSM**: klucz symetryczny|Nieobsługiwane|Obsługiwane|
|||

### <a name="software-protected-keys"></a>Klucze chronione przez oprogramowanie

|Typ klucza|Magazyny|Zarządzane sprzętowych modułów zabezpieczeń|
|--|--|--|
**RSA**: klucz RSA "chronione przez oprogramowanie"|Obsługiwane|Nieobsługiwane
**EC**: klucz krzywej eliptyczna "chronione przez oprogramowanie"|Obsługiwane|Nieobsługiwane
|||

### <a name="compliance"></a>Zgodność

|Typ klucza i miejsce docelowe|Zgodność|
|---|---|
|Klucze chronione przez oprogramowanie w magazynach (jednostki SKU w warstwie Premium & standard) | Poziom FIPS 140-2|
|Klucze chronione przez moduł HSM w magazynach (jednostka SKU Premium)| FIPS 140-2 Level 2|
|Klucze chronione przez moduł HSM w zarządzanym module HSM|Poziom standardu FIPS 140-2|
|||



Zobacz [typy kluczy, algorytmy i operacje,](about-keys-details.md) Aby uzyskać szczegółowe informacje dotyczące każdego typu klucza, algorytmów, operacji, atrybutów i tagów.

## <a name="next-steps"></a>Następne kroki
- [Informacje o usłudze Key Vault](../general/overview.md)
- [Informacje o zarządzanym module HSM](../managed-hsm/overview.md)
- [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- [Informacje o certyfikatach](../certificates/about-certificates.md)
- [Omówienie interfejsu API REST Key Vault](../general/about-keys-secrets-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
