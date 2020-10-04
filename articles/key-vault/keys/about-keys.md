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
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8ac5b0976e677cc48fb3fe47c50797b07acdbc44
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708870"
---
# <a name="about-keys"></a>Informacje o kluczach

Azure Key Vault oferuje dwa typy zasobów do przechowywania kluczy kryptograficznych i zarządzania nimi:

|Typ zasobu|Metody ochrony klucza|Podstawowy adres URL punktu końcowego płaszczyzny danych|
|--|--|--|
| **Magazyny** | Chronione przez oprogramowanie<br/><br/>oraz<br/><br/>Chronione przez moduł HSM (z jednostką SKU Premium)</li></ul> | https://{nazwa magazynu}. magazyn. Azure. NET |
| **Zarządzane pule modułu HSM** | Chronione przez moduł HSM | https://{HSM-Name}. managedhsm. Azure. NET |
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
- Zarządzane pule modułu HSM używają sprawdzonych modułów HSM **140-2 Level 3** w celu ochrony kluczy. Każda pula modułów HSM jest izolowanym wystąpieniem pojedynczej dzierżawy z własną [domeną zabezpieczeń](../managed-hsm/security-domain.md) zapewniającą pełną izolację kryptograficzną od wszystkich innych pul modułu HSM, które współużytkują tę samą infrastrukturę sprzętową.

Te klucze są chronione w pulach modułu HSM o pojedynczej dzierżawie. W postaci miękkiej lub wyeksportowanej z obsługiwanego urządzenia HSM można zaimportować klucz RSA, EC i symetryczny. Klucze można również generować w pulach HSM. Podczas importowania kluczy HSM przy użyciu metody opisanej w [BYOK (Dostarcz własny klucz) Specyfikacja](../keys/byok-specification.md)umożliwia bezpieczny materiał klucza transportowego do zarządzanych pul modułu HSM. 

Aby uzyskać więcej informacji na temat granic geograficznych, zobacz [Microsoft Azure Centrum zaufania](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-protection-methods-and-algorithms"></a>Typy kluczy, metody ochrony i algorytmy

Key Vault obsługuje klucze RSA, EC i symetryczne. 

### <a name="hsm-protected-keys"></a>Klucze chronione przez moduł HSM

|Typ klucza|Magazyny (tylko wersja Premium)|Zarządzane pule modułu HSM|
|--|--|--|--|
**EC-HSM**: klucz krzywej eliptycznej|Moduł HSM FIPS 140-2 Level 2|Moduł HSM FIPS 140-2 Level 3
**RSA-HSM**: klucz RSA|Moduł HSM FIPS 140-2 Level 2|Moduł HSM FIPS 140-2 Level 3
**OCT-HSM**: symetryczny|Nieobsługiwane|Moduł HSM FIPS 140-2 Level 3
||||

### <a name="software-protected-keys"></a>Klucze chronione przez oprogramowanie

|Typ klucza|Magazyny|Zarządzane pule modułu HSM|
|--|--|--|--|
**RSA**: klucz RSA "chronione przez oprogramowanie"|Poziom FIPS 140-2|Nieobsługiwane
**EC**: klucz krzywej eliptyczna "chronione przez oprogramowanie"|Poziom FIPS 140-2|Nieobsługiwane
||||

### <a name="supported-algorithms"></a>Obsługiwane algorytmy

|Typy/rozmiary/krzywe kluczy| Szyfruj/Odszyfruj<br>(Zawijanie/odpakowywanie) | Podpisz/Weryfikuj | 
| --- | --- | --- |
|WE-P256, P256K-P384, EC-521|NA|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA — OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES 128-bit, 256-bitowy| AES — KW<br>AES — GCM<br>AES — CBC| NA| 
|||

###  <a name="ec-algorithms"></a>Algorytmy we
 Następujące identyfikatory algorytmów są obsługiwane przez klucze HSM

#### <a name="curve-types"></a>Typy krzywych

-   **P-256** -krzywa NIST p-256, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256 k** — krzywa s SECP256K1, zdefiniowana w [s 2: zalecane parametry domeny krzywej eliptycznej](https://www.secg.org/sec2-v2.pdf).
-   **P-384** -krzywa NIST p-384, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** -krzywa NIST p-521, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>PODPISZ/WERYFIKUJ

-   **ES256** -ECDSA dla skrótów SHA-256 i kluczy utworzonych przy użyciu krzywej P-256. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA dla skrótów SHA-256 i kluczy utworzonych przy użyciu krzywej P-256 k. Ten algorytm oczekuje na standaryzację.
-   **ES384** -ECDSA dla skrótów SHA-384 i kluczy utworzonych przy użyciu krzywej P-384. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA dla skrótów SHA-512 i kluczy utworzonych przy użyciu krzywej P-521. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>Algorytmy RSA  
 Następujące identyfikatory algorytmów są obsługiwane przez klucze HSM i RSA  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, SZYFROWANIE/ODSZYFROWYWANIE

-   Szyfrowanie klucza **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** -RSAES przy użyciu optymalnego wypełnienia asymetrycznego szyfrowania (OAEP) [RFC3447] z domyślnymi parametrami określonymi w dokumencie RFC 3447 w sekcji A. 2.1. Te parametry domyślne używają funkcji hash algorytmu SHA-1 i funkcji generowania maski MGF1 z algorytmem SHA-1.  
-  **RSA-OAEP-256** — RSAES przy użyciu optymalnego uzupełniania asymetrycznego szyfrowania z funkcją mieszania sha-256 i funkcją generowania maski MGF1 z algorytmem sha-256

#### <a name="signverify"></a>PODPISZ/WERYFIKUJ

-   **PS256** -RSASSA-PSS przy użyciu algorytmu sha-256 i MGF1 z algorytmem sha-256, zgodnie z opisem w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** -RSASSA-PSS przy użyciu algorytmu sha-384 i MGF1 z algorytmem sha-384, zgodnie z opisem w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** -RSASSA-PSS przy użyciu algorytmu sha-512 i MGF1 z algorytmem sha-512, zgodnie z opisem w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-256. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-256 i musi mieć długość 32 bajtów.  
-   **RS384** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-384. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-384 i musi mieć długość 48 bajtów.  
-   **RS512** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-512. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-512 i musi mieć długość 64 bajtów.  
-   **RSNULL** — zobacz [RFC2437](https://tools.ietf.org/html/rfc2437), wyspecjalizowany przypadek użycia, aby włączyć określone scenariusze protokołu TLS.  

###  <a name="symmetric-key-algorithms"></a>Algorytmy klucza symetrycznego
- Szyfrowanie kluczy AES **-kW** -AES ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- Szyfrowanie **AES-GCM** -AES w trybie licznika Galois ([NIST SP800-38D](https://csrc.nist.gov/publications/sp800))
- Szyfrowanie **AES-CBC** -AES w trybie łańcuchowym bloku szyfrowania ([NIST SP800-38a](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> Bieżąca implementacja algorytmu AES-GCM i odpowiednie interfejsy API są eksperymentalne. Implementacja i interfejsy API mogą znacząco ulec zmianie w przyszłych iteracjach. 

##  <a name="key-operations"></a>Najważniejsze operacje

Zarządzany moduł HSM obsługuje następujące operacje na obiektach kluczowych:  

-   **Utwórz**: umożliwia klientowi utworzenie klucza w Key Vault. Wartość klucza jest generowana przez Key Vault i jest przechowywana i nie jest wydawana dla klienta. Klucze asymetryczne mogą być tworzone w Key Vault.  
-   **Import**: umożliwia klientowi Importowanie istniejącego klucza do Key Vault. Klucze asymetryczne mogą być importowane do Key Vault przy użyciu wielu różnych metod pakowania w ramach konstrukcji JWK. 
-   **Aktualizacja**: umożliwia klientowi z wystarczającymi uprawnieniami do modyfikowania metadanych (atrybutów kluczowych) skojarzonych z kluczem, który został wcześniej zapisany w Key Vault.  
-   **Usuń**: umożliwia klientowi z odpowiednimi uprawnieniami do usuwania klucza z Key Vault.  
-   **Lista**: umożliwia klientowi Wyświetlanie listy wszystkich kluczy w danym Key Vault.  
-   **Wersje list**: umożliwia klientowi Wyświetlanie listy wszystkich wersji danego klucza w danym Key Vault.  
-   **Get**: umożliwia klientowi pobranie publicznych części danego klucza w Key Vault.  
-   **Kopia zapasowa**: Eksportuje klucz w formularzu chronionym.  
-   **Przywracanie**: importuje poprzednio kopię zapasową klucza.  

Aby uzyskać więcej informacji, zobacz [kluczowe operacje w dokumentacji interfejsu API REST Key Vault](/rest/api/keyvault).  

Po utworzeniu klucza w Key Vault można wykonać następujące operacje kryptograficzne przy użyciu klucza:  

-   **Podpisz i Weryfikuj**: absolutnie ta operacja jest "Podpisz skrótem" lub "Weryfikuj skrót", ponieważ Key Vault nie obsługuje mieszania zawartości w ramach tworzenia podpisu. Aplikacje powinny skrótować dane, które mają być podpisane lokalnie, a następnie żądać Key Vault podpisywania skrótu. Weryfikacja podpisywanych skrótów jest obsługiwana jako wygodna operacja dla aplikacji, które mogą nie mieć dostępu do materiału klucza [Public]. Aby zapewnić najlepszą wydajność aplikacji, należy przeprowadzić operacje weryfikacji lokalnie.  
-   **Szyfrowanie/Zawijanie kluczy**: Klucz przechowywany w Key Vault może służyć do ochrony innego klucza, zazwyczaj klucza szyfrowania zawartości symetrycznej (CEK). Gdy klucz w Key Vault jest asymetryczny, używane jest szyfrowanie klucza. Na przykład RSA-OAEP i operacje WRAPKEY/UNWRAPKEY są równoważne z SZYFROWANIEm/ODSZYFROWYWAniem. Gdy klucz w Key Vault jest symetryczny, używane jest Zawijanie kluczy. Na przykład AES-KW. Operacja WRAPKEY jest obsługiwana jako wygoda dla aplikacji, które mogą nie mieć dostępu do materiału klucza [Public]. W celu uzyskania najlepszej wydajności aplikacji WRAPKEY operacje powinny być wykonywane lokalnie.  
-   **Szyfrowanie i odszyfrowywanie**: klucz zapisany w Key Vault może służyć do szyfrowania lub odszyfrowywania pojedynczego bloku danych. Rozmiar bloku jest określany na podstawie typu klucza i wybranego algorytmu szyfrowania. Operacja szyfrowania została udostępniona dla wygody dla aplikacji, które mogą nie mieć dostępu do materiału klucza [Public]. W celu uzyskania najlepszej wydajności aplikacji należy wykonać operacje szyfrowania lokalnie.  

Podczas gdy WRAPKEY/UNWRAPKEY użycie kluczy asymetrycznych może wydawać się zbędne (ponieważ operacja jest równoważna z SZYFROWANIEm/ODSZYFROWYWAniem), użycie odrębnych operacji jest ważne. Rozróżnienie zapewnia semantykę i separację autoryzacji tych operacji oraz spójność, gdy inne typy kluczy są obsługiwane przez usługę.  

Key Vault nie obsługuje operacji EKSPORTOWANIA. Po zainicjowaniu klucza w systemie nie można go wyodrębnić ani zmodyfikować materiału klucza. Jednak użytkownicy Key Vault mogą wymagać ich klucza dla innych przypadków użycia, na przykład po usunięciu. W takim przypadku mogą oni użyć operacji tworzenia kopii zapasowej i przywracania, aby wyeksportować/zaimportować klucz w formularzu chronionym. Klucze utworzone przez operację tworzenia kopii zapasowej nie mogą być używane poza Key Vault. Alternatywnie operacja importowania może być używana z wieloma wystąpieniami Key Vault.  

Użytkownicy mogą ograniczyć każdą z operacji kryptograficznych, które Key Vault obsługiwane przez poszczególne klucze przy użyciu właściwości key_ops obiektu JWK.  

Aby uzyskać więcej informacji na temat obiektów JWK, zobacz [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

## <a name="key-attributes"></a>Atrybuty klucza

Oprócz materiału kluczowego można określić następujące atrybuty. W żądaniu JSON atrybuty słowo kluczowe i nawiasy klamrowe "{" "}" są wymagane nawet wtedy, gdy nie określono żadnych atrybutów.  

- *włączone*: wartość logiczna, opcjonalna, **wartość**domyślna to true. Określa, czy klucz jest włączony i użyteczny dla operacji kryptograficznych. *Włączony* atrybut jest używany w połączeniu z *NBF* i *EXP*. Gdy operacja przejdzie między *NBF* i *EXP*, będzie dozwolona tylko wtedy, gdy wartość *Enabled* jest ustawiona na **true**. Operacje poza oknem *nbf*  /  *EXP* protokołu NBF są automatycznie niedozwolone, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations).
- *NBF*: IntDate, opcjonalnie, domyślnie jest teraz. Atrybut *NBF* (nie wcześniej) określa czas, po którym klucz nie może być używany do operacji kryptograficznych, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations). Przetwarzanie atrybutu *NBF* wymaga, aby bieżąca data/godzina musiała być późniejsza lub równa wartości nie wcześniejsza niż data/godzina określona w atrybucie *NBF* . Key Vault może przewidywać nieco małych Leeway, zwykle nie więcej niż kilka minut, aby uwzględnić pochylenie zegara. Wartość musi być liczbą zawierającą wartość IntDate.  
- *EXP*: IntDate, opcjonalne, wartość domyślna to "nieskończona". Atrybut *EXP* (czas wygaśnięcia) określa czas wygaśnięcia dla lub po upływie którego klucz nie może być używany do operacji kryptograficznej, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations). Przetwarzanie atrybutu *EXP* wymaga, aby bieżąca data/godzina musiała być wcześniejsza niż data/godzina wygaśnięcia określona w atrybucie *EXP* . Key Vault może przewidywać nieco małych Leeway, zwykle nie więcej niż kilka minut, do konta w celu przesunięcia zegara. Wartość musi być liczbą zawierającą wartość IntDate.  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w dowolnej odpowiedzi zawierającej atrybuty klucza:  

- *utworzono*: IntDate, opcjonalnie. *Utworzony* atrybut wskazuje, kiedy ta wersja klucza została utworzona. Wartość jest zerowa dla kluczy utworzonych przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate, opcjonalnie. *Zaktualizowany* atrybut wskazuje, kiedy ta wersja klucza została zaktualizowana. Wartość jest równa null dla kluczy, które były ostatnio aktualizowane przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  

Aby uzyskać więcej informacji na temat IntDate i innych typów danych, zobacz [informacje o kluczach, wpisach tajnych i certyfikatach: [typy danych](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Klucze, które nie są jeszcze prawidłowe i wygasły, poza oknem EXP usługi *NBF*  /  *exp* , będą działały w przypadku operacji **odszyfrowania**, **odpakowania**i **weryfikacji** (nie zwróci 403, zabronione). Uzasadnienie użycia nieprawidłowego stanu ma umożliwić przetestowanie klucza przed użyciem produkcji. Uzasadnienie dotyczące korzystania z stanu wygasłego polega na umożliwieniu operacji odzyskiwania danych, które zostały utworzone, gdy klucz był prawidłowy. Ponadto można wyłączyć dostęp do klucza przy użyciu zasad Key Vault lub przez zaktualizowanie atrybutu *Enabled* klucza na **wartość false**.

Aby uzyskać więcej informacji na temat typów danych, zobacz [typy danych](../general/about-keys-secrets-certificates.md#data-types).

Aby uzyskać więcej informacji na temat innych możliwych atrybutów, zobacz [klucz internetowy JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

## <a name="key-tags"></a>Najważniejsze Tagi

W postaci tagów można określić dodatkowe metadane specyficzne dla aplikacji. Key Vault obsługuje do 15 tagów, z których każdy może mieć nazwę znaku 256 i wartość znaku 256.  

> [!NOTE] 
> Tagi są odczytywane przez obiekt wywołujący, jeśli mają *listę* lub *uzyskują* uprawnienia do tego klucza.

##  <a name="key-access-control"></a>Kontrola dostępu do kluczy

Kontrola dostępu do kluczy zarządzanych przez Key Vault jest udostępniana na poziomie Key Vault, który działa jako kontener kluczy. Zasady kontroli dostępu dla kluczy różnią się od zasad kontroli dostępu dla wpisów tajnych w tym samym Key Vault. Użytkownicy mogą utworzyć jeden lub więcej magazynów w celu przechowywania kluczy i muszą utrzymywać odpowiednie segmentacje i zarządzanie kluczami. Kontrola dostępu do kluczy jest niezależna od kontroli dostępu do wpisów tajnych.  

Następujące uprawnienia można udzielić dla poszczególnych użytkowników/głównych usług, w wpis kontroli dostępu do kluczy w magazynie. Te uprawnienia ściśle duplikują operacje dozwolone dla obiektu klucza.  Udzielanie dostępu do jednostki usługi w magazynie kluczy jest operacją jednorazowej, która pozostanie taka sama dla wszystkich subskrypcji platformy Azure. Można go użyć do wdrożenia dowolnej liczby certyfikatów. 

- Uprawnienia do operacji zarządzania kluczami
  - *pobieranie*: odczytywanie publicznej części klucza oraz jego atrybutów
  - *Lista*: Lista kluczy lub wersji klucza przechowywanego w magazynie kluczy
  - *Aktualizacja*: aktualizowanie atrybutów klucza
  - *Tworzenie*: tworzenie nowych kluczy
  - *Import*: Importowanie klucza do magazynu kluczy
  - *Usuń*: usuwanie obiektu klucza
  - *Odzyskaj*: Odzyskaj usunięty klucz
  - *kopia zapasowa*: Tworzenie kopii zapasowej klucza w magazynie kluczy
  - *przywracanie*: Przywracanie klucza kopii zapasowej do magazynu kluczy

- Uprawnienia do operacji kryptograficznych
  - *Odszyfruj*: Użyj klucza, aby wyłączyć ochronę sekwencji bajtów
  - *Szyfruj*: Użyj klucza do ochrony dowolnej sekwencji bajtów
  - *unwrapKey*: Użyj klucza, aby wyłączyć ochronę opakowanych kluczy symetrycznych
  - *wrapKey*: Użyj klucza do ochrony klucza symetrycznego
  - *Weryfikuj*: Użyj klucza, aby zweryfikować skróty  
  - *Sign*: Użyj klucza do podpisywania skróconych
    
- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: przeczyszczanie (trwałe usuwanie) usuniętego klucza

Aby uzyskać więcej informacji na temat pracy z kluczami, zobacz [najważniejsze operacje w temacie Informacje o interfejsie API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Następne kroki
- [Informacje o usłudze Key Vault](../general/overview.md)
- [Informacje o zarządzanym module HSM](../managed-hsm/overview.md)
- [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- [Informacje o certyfikatach](../certificates/about-certificates.md)
- [Omówienie interfejsu API REST Key Vault](../general/about-keys-secrets-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
