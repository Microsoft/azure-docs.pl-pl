---
title: Typy kluczy, algorytmy i operacje — Azure Key Vault
description: Obsługiwane typy kluczy, algorytmy i operacje (szczegóły).
services: key-vault
author: amitbapat
manager: msmbaldwin
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: ambapat
ms.openlocfilehash: 675c4f04ece322000ae0ebb44d6291c455db9397
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98133280"
---
# <a name="key-types-algorithms-and-operations"></a>Typy kluczy, algorytmy i operacje

Key Vault obsługuje dwa typy zasobów: magazyny i zarządzane sprzętowych modułów zabezpieczeń. Oba typy zasobów obsługują różne klucze szyfrowania. Aby wyświetlić podsumowanie obsługiwanych typów kluczy, typy ochrony według poszczególnych typów zasobów, zobacz [Informacje o kluczach](about-keys.md).

W poniższej tabeli przedstawiono podsumowanie typów kluczy i obsługiwane algorytmy.

|Typy/rozmiary/krzywe kluczy| Szyfruj/Odszyfruj<br>(Zawijanie/odpakowywanie) | Podpisz/Weryfikuj | 
| --- | --- | --- |
|WE-P256, P256K-P384, EC-521|NA|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K, 3K, 4K| RSA1_5<br>RSA — OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES 128-bit, 256-bitowy <br/>(Tylko zarządzany moduł HSM)| AES — KW<br>AES — GCM<br>AES — CBC| NA| 
|||

##  <a name="ec-algorithms"></a>Algorytmy we
 Następujące identyfikatory algorytmów są obsługiwane przez klucze HSM

### <a name="curve-types"></a>Typy krzywych

-   **P-256** -krzywa NIST p-256, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256 k** — krzywa s SECP256K1, zdefiniowana w [s 2: zalecane parametry domeny krzywej eliptycznej](https://www.secg.org/sec2-v2.pdf).
-   **P-384** -krzywa NIST p-384, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** -krzywa NIST p-521, zdefiniowana w trybie [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

### <a name="signverify"></a>PODPISZ/WERYFIKUJ

-   **ES256** -ECDSA dla skrótów SHA-256 i kluczy utworzonych przy użyciu krzywej P-256. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA dla skrótów SHA-256 i kluczy utworzonych przy użyciu krzywej P-256 k. Ten algorytm oczekuje na standaryzację.
-   **ES384** -ECDSA dla skrótów SHA-384 i kluczy utworzonych przy użyciu krzywej P-384. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA dla skrótów SHA-512 i kluczy utworzonych przy użyciu krzywej P-521. Ten algorytm został opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).

##  <a name="rsa-algorithms"></a>Algorytmy RSA  
 Następujące identyfikatory algorytmów są obsługiwane przez klucze HSM i RSA  

### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, SZYFROWANIE/ODSZYFROWYWANIE

-   Szyfrowanie klucza **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** -RSAES przy użyciu optymalnego wypełnienia asymetrycznego szyfrowania (OAEP) [RFC3447] z domyślnymi parametrami określonymi w dokumencie RFC 3447 w sekcji A. 2.1. Te parametry domyślne używają funkcji hash algorytmu SHA-1 i funkcji generowania maski MGF1 z algorytmem SHA-1.  
-  **RSA-OAEP-256** — RSAES przy użyciu optymalnego uzupełniania asymetrycznego szyfrowania z funkcją mieszania sha-256 i funkcją generowania maski MGF1 z algorytmem sha-256

### <a name="signverify"></a>PODPISZ/WERYFIKUJ

-   **PS256** -RSASSA-PSS przy użyciu algorytmu sha-256 i MGF1 z algorytmem sha-256, zgodnie z opisem w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** -RSASSA-PSS przy użyciu algorytmu sha-384 i MGF1 z algorytmem sha-384, zgodnie z opisem w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** -RSASSA-PSS przy użyciu algorytmu sha-512 i MGF1 z algorytmem sha-512, zgodnie z opisem w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-256. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-256 i musi mieć długość 32 bajtów.  
-   **RS384** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-384. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-384 i musi mieć długość 48 bajtów.  
-   **RS512** -RSASSA-PKCS-v1_5 przy użyciu algorytmu SHA-512. Wartość skrótu podana w aplikacji musi być obliczona przy użyciu algorytmu SHA-512 i musi mieć długość 64 bajtów.  
-   **RSNULL** — zobacz [RFC2437](https://tools.ietf.org/html/rfc2437), wyspecjalizowany przypadek użycia, aby włączyć określone scenariusze protokołu TLS.  

##  <a name="symmetric-key-algorithms-managed-hsm-only"></a>Algorytmy klucza symetrycznego (tylko zarządzany moduł HSM)
- Szyfrowanie kluczy AES **-kW** -AES ([RFC3394](https://tools.ietf.org/html/rfc3394)).
- Szyfrowanie **AES-GCM** -AES w trybie licznika Galois ([NIST SP PS 800-38D](https://csrc.nist.gov/publications/sp800))
- Szyfrowanie **AES-CBC** -AES w trybie łańcucha szyfrowego ([NIST SP 800-38a](https://csrc.nist.gov/publications/sp800))

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

- *włączone*: wartość logiczna, opcjonalna, **wartość** domyślna to true. Określa, czy klucz jest włączony i użyteczny dla operacji kryptograficznych. *Włączony* atrybut jest używany w połączeniu z *NBF* i *EXP*. Gdy operacja przejdzie między *NBF* i *EXP*, będzie dozwolona tylko wtedy, gdy wartość *Enabled* jest ustawiona na **true**. Operacje poza oknem   /  *EXP* protokołu NBF są automatycznie niedozwolone, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations).
- *NBF*: IntDate, opcjonalnie, domyślnie jest teraz. Atrybut *NBF* (nie wcześniej) określa czas, po którym klucz nie może być używany do operacji kryptograficznych, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations). Przetwarzanie atrybutu *NBF* wymaga, aby bieżąca data/godzina musiała być późniejsza lub równa wartości nie wcześniejsza niż data/godzina określona w atrybucie *NBF* . Key Vault może przewidywać nieco małych Leeway, zwykle nie więcej niż kilka minut, aby uwzględnić pochylenie zegara. Wartość musi być liczbą zawierającą wartość IntDate.  
- *EXP*: IntDate, opcjonalne, wartość domyślna to "nieskończona". Atrybut *EXP* (czas wygaśnięcia) określa czas wygaśnięcia dla lub po upływie którego klucz nie może być używany do operacji kryptograficznej, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations). Przetwarzanie atrybutu *EXP* wymaga, aby bieżąca data/godzina musiała być wcześniejsza niż data/godzina wygaśnięcia określona w atrybucie *EXP* . Key Vault może przewidywać nieco małych Leeway, zwykle nie więcej niż kilka minut, do konta w celu przesunięcia zegara. Wartość musi być liczbą zawierającą wartość IntDate.  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w dowolnej odpowiedzi zawierającej atrybuty klucza:  

- *utworzono*: IntDate, opcjonalnie. *Utworzony* atrybut wskazuje, kiedy ta wersja klucza została utworzona. Wartość jest zerowa dla kluczy utworzonych przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate, opcjonalnie. *Zaktualizowany* atrybut wskazuje, kiedy ta wersja klucza została zaktualizowana. Wartość jest równa null dla kluczy, które były ostatnio aktualizowane przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  

Aby uzyskać więcej informacji na temat IntDate i innych typów danych, zobacz [informacje o kluczach, wpisach tajnych i certyfikatach: [typy danych](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Klucze, które nie są jeszcze prawidłowe i wygasły, poza oknem EXP usługi *NBF*  /   , będą działały w przypadku operacji **odszyfrowania**, **odpakowania** i **weryfikacji** (nie zwróci 403, zabronione). Uzasadnienie użycia nieprawidłowego stanu ma umożliwić przetestowanie klucza przed użyciem produkcji. Uzasadnienie dotyczące korzystania z stanu wygasłego polega na umożliwieniu operacji odzyskiwania danych, które zostały utworzone, gdy klucz był prawidłowy. Ponadto można wyłączyć dostęp do klucza przy użyciu zasad Key Vault lub przez zaktualizowanie atrybutu *Enabled* klucza na **wartość false**.

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
