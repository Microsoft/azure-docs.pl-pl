---
title: Informacje o kluczach usługi Azure Key Vault — usługa Azure Key Vault
description: Omówienie interfejsu REST usługi Azure Key Vault i szczegóły dotyczące deweloperów kluczy.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c12135ec6e5a0f4de1fdd46134a056447d3c331
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424237"
---
# <a name="about-azure-key-vault-keys"></a>Informacje o kluczach usługi Azure Key Vault

Usługa Azure Key Vault umożliwia aplikacjom i użytkownikom platformy Microsoft Azure przechowywanie kluczy i używanie ich. Obsługuje wiele typów kluczy i algorytmów i umożliwia korzystanie z sprzętowych modułów zabezpieczeń (HSM) dla kluczy o wysokiej wartości. 

Aby uzyskać bardziej ogólne informacje na temat usługi Key Vault, zobacz [Co to jest usługa Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Poniższe sekcje oferują ogólne informacje mające zastosowanie do implementacji usługi Usługi Usługi Key Vault.

### <a name="supporting-standards"></a>Standardy wspierające

Specyfikacje notacji i szyfrowania obiektów JavaScript (JSON) i JavaScript Object Signing and Encryption (JOSE) są ważnymi informacjami w tle.  

-   [Klucz sieci Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [Szyfrowanie sieci Web JSON (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [Algorytmy JSON Web (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [Podpis JSON Web (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Typy danych

Informacje na temat odpowiednich typów danych dotyczących kluczy, szyfrowania i podpisywania można znaleźć w specyfikacjach jose.  

-   **algorytm** - obsługiwany algorytm dla operacji klucza, na przykład RSA1_5  
-   **wartość tekstu szyfrowania** - oktety tekstu szyfrowanego, zakodowane przy użyciu base64URL  
-   **digest-value** - dane wyjściowe algorytmu mieszania, zakodowane przy użyciu Base64URL  
-   **typ klucza** - jeden z obsługiwanych typów kluczy, na przykład RSA (Rivest-Shamir-Adleman).  
-   **wartość zwykłego tekstu** - oktety w postaci zwykłego tekstu, zakodowane przy użyciu base64URL  
-   **signature-value** - dane wyjściowe algorytmu podpisu zakodowanego przy użyciu base64URL  
-   **base64URL** - wartość binarna zakodowana w base64URL [RFC4648]  
-   **wartość logiczna** — prawda lub fałsz  
-   **Tożsamość** — tożsamość z usługi Azure Active Directory (Azure AD).  
-   **IntDate** - wartość dziesiętna JSON reprezentująca liczbę sekund od 1970-01-01T0:0:0Z UTC do określonej daty/godziny UTC. Szczegółowe informacje na temat daty/godziny można znaleźć w 19339 r., a w szczególności w przypadku czasu utc.  

### <a name="objects-identifiers-and-versioning"></a>Obiekty, identyfikatory i przechowywanie wersji

Obiekty przechowywane w przechowalni kluczy są wersjonatowane przy każdym utworzeniu nowego wystąpienia obiektu. Każdej wersji jest przypisywany unikatowy identyfikator i adres URL. Po utworzeniu obiektu nadana jest unikatowy identyfikator wersji i oznaczona jako bieżąca wersja obiektu. Utworzenie nowego wystąpienia o tej samej nazwie obiektu nadaje nowemu obiektowi unikatowy identyfikator wersji, co powoduje, że staje się on bieżącą wersją.  

Obiekty w magazynie kluczy można rozwiązać przy użyciu bieżącego identyfikatora lub identyfikatora specyficznego dla wersji. Na przykład, biorąc pod `MasterKey`uwagę Klucz o nazwie , wykonywanie operacji z bieżącym identyfikatorem powoduje, że system do korzystania z najnowszej dostępnej wersji. Wykonywanie operacji z identyfikatorem specyficznym dla wersji powoduje, że system używa tej określonej wersji obiektu.  

Obiekty są jednoznacznie identyfikowane w magazynie kluczy przy użyciu adresu URL. Żadne dwa obiekty w systemie nie mają tego samego adresu URL, niezależnie od lokalizacji geograficznej. Pełny adres URL obiektu nosi nazwę identyfikatora obiektu. Adres URL składa się z prefiksu identyfikującego magazyn kluczy, typ obiektu, nazwę obiektu pod warunkiem użytkownika i wersję obiektu. Nazwa obiektu jest niewrażliwa na argumenty i niezmienna. Identyfikatory, które nie zawierają wersji obiektu są określane jako identyfikatory podstawowe.  

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący ogólny format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Gdzie:  

|||  
|-|-|  
|`keyvault-name`|Nazwa magazynu kluczy w usłudze Microsoft Azure Key Vault.<br /><br /> Nazwy magazynu kluczy są wybierane przez użytkownika i są unikatowe globalnie.<br /><br /> Nazwa magazynu kluczy musi być ciągiem znaków 3-24, zawierającym tylko 0-9, a-z, A-Z i -.|  
|`object-type`|Typ obiektu, "klucze" lub "wpisy tajne".|  
|`object-name`|Nazwa `object-name` użytkownika jest nazwą podana przez użytkownika i musi być unikatowa w magazynie kluczy. Nazwa musi być ciągiem znaków 1-127, zawierającym tylko 0-9, a-z, A-Z i -.|  
|`object-version`|An `object-version` jest generowany przez system, 32-znakowy identyfikator ciągu, który jest opcjonalnie używany do adresu unikatową wersję obiektu.|  

## <a name="key-vault-keys"></a>Klucze przechowalni kluczy

### <a name="keys-and-key-types"></a>Klawisze i typy klawiszy

Klucze kryptograficzne w magazynie kluczy są reprezentowane jako obiekty JSON Web Key [JWK]. Podstawowe specyfikacje JWK/JWA są również rozszerzone, aby umożliwić typy kluczy unikatowe dla implementacji usługi Key Vault. Na przykład importowanie kluczy przy użyciu opakowania specyficznego dla dostawcy modułu HSM umożliwia bezpieczny transport kluczy, które mogą być używane tylko w modułach HSM usługi Key Vault.  

- **"Miękkie" klucze:** Klucz przetwarzany w oprogramowaniu przez Key Vault, ale jest szyfrowany w spoczynku przy użyciu klucza systemowego, który znajduje się w modułie HSM. Klienci mogą zaimportować istniejący klucz RSA lub EC (Krzywa eliptyczna) lub zażądać wygenerowania klucza Usługi Key Vault.
- **"Twarde" klucze:** Klucz przetwarzany w module HSM (Hardware Security Module). Te klucze są chronione w jednym z rekordów zabezpieczeń usługi Key Vault HSM Security Worlds (istnieje jeden świat zabezpieczeń na geografię w celu zachowania izolacji). Klienci mogą importować klucz RSA lub EC w formie miękkiej lub eksportując z kompatybilnego urządzenia HSM. Klienci mogą również zażądać magazynu kluczy do wygenerowania klucza. Ten typ klucza dodaje atrybut key_hsm do JWK uzyskać do przenoszenia materiału klucza HSM.

     Aby uzyskać więcej informacji na temat granic geograficznych, zobacz [Centrum zaufania platformy Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/)  

Usługa Key Vault obsługuje tylko klawisze RSA i Elliptic Curve. 

-   **EC**: "Miękki" klawisz krzywej eliptycznej.
-   **EC-HSM**: "Twardy" klawisz krzywej eliptycznej.
-   **RSA**: "Miękki" klawisz RSA.
-   **RSA-HSM**: "Twardy" klucz RSA.

Key Vault obsługuje klucze RSA o rozmiarach 2048, 3072 i 4096. Key Vault obsługuje typy kluczy krzywej eliptycznej P-256, P-384, P-521 i P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Ochrona kryptograficzna

Moduły kryptograficzne używane przez program Key Vault, niezależnie od tego, czy są modułami HSM, czy oprogramowaniem, są sprawdzane fips (Federal Information Processing Standards). Nie musisz robić nic specjalnego, aby uruchomić w trybie FIPS. Klucze **utworzone** lub **zaimportowane** jako chronione przez moduł HSM są przetwarzane wewnątrz modułu HSM, sprawdzane zgodnie z fips 140-2 Poziom 2. Klucze **utworzone** lub **zaimportowane** jako chronione programem są przetwarzane wewnątrz modułów kryptograficznych zweryfikowanych zgodnie z fips 140-2 Poziom 1. Aby uzyskać więcej informacji, zobacz [Klucze i typy kluczy](#keys-and-key-types).

###  <a name="ec-algorithms"></a>Algorytmy WE
 Następujące identyfikatory algorytmów są obsługiwane za pomocą kluczy EC i EC-HSM w magazynie kluczy. 

#### <a name="curve-types"></a>Typy krzywych

-   **P-256** - Krzywa NIST P-256, zdefiniowana w [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - Krzywa SEC SEC SECP256K1, zdefiniowana w [SEC 2: Zalecane parametry domeny krzywej eliptycznej](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - Krzywa NIST P-384, zdefiniowana w [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - Krzywa NIST P-521, zdefiniowana w [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>PODPISZ/SPRAWDŹ

-   **ES256** - ECDSA dla skrótów SHA-256 i kluczy utworzonych za pomocą krzywej P-256. Algorytm ten jest opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA dla skrótów SHA-256 i kluczy utworzonych za pomocą krzywej P-256K. Ten algorytm oczekuje na standaryzację.
-   **ES384** - ECDSA dla skrótów SHA-384 i kluczy utworzonych za pomocą krzywej P-384. Algorytm ten jest opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA dla skrótów SHA-512 i kluczy utworzonych za pomocą krzywej P-521. Algorytm ten jest opisany w [RFC7518](https://tools.ietf.org/html/rfc7518).


###  <a name="rsa-algorithms"></a>Algorytmy RSA  
 Następujące identyfikatory algorytmów są obsługiwane za pomocą kluczy RSA i RSA-HSM w ucho.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, SZYFRUJ/ODSZYFROWYWANIE

-   **RSA1_5** - szyfrowanie kluczy RSAES-PKCS1-V1_5 [RFC3447]  
-   **RSA-OAEP** - RSAES przy użyciu optymalnego szyfrowania asymetrycznego padding (OAEP) [RFC3447], z domyślnymi parametrami określonymi przez RFC 3447 w sekcji A.2.1. Te parametry domyślne są przy użyciu funkcji mieszania SHA-1 i funkcji generowania maski MGF1 z SHA-1.  

#### <a name="signverify"></a>PODPISZ/SPRAWDŹ

-   **PS256** - RSASSA-PSS przy użyciu SHA-256 i MGF1 z SHA-256, jak opisano w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS przy użyciu SHA-384 i MGF1 z SHA-384, jak opisano w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS przy użyciu SHA-512 i MGF1 z SHA-512, jak opisano w [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** - RSASSA-PKCS-v1_5 za pomocą SHA-256. Wartość skrótu dostarczona przez aplikację musi być obliczona przy użyciu sha-256 i musi mieć długość 32 bajtów.  
-   **RS384** - RSASSA-PKCS-v1_5 za pomocą SHA-384. Wartość skrótu dostarczona przez aplikację musi być obliczona przy użyciu sha-384 i musi mieć długość 48 bajtów.  
-   **RS512** - RSASSA-PKCS-v1_5 za pomocą SHA-512. Wartość skrótu dostarczona przez aplikację musi być obliczona przy użyciu sha-512 i musi mieć długość 64 bajtów.  
-   **RSNULL** — zobacz [RFC2437], przypadek użycia specjalistyczne, aby włączyć niektóre scenariusze TLS.  

###  <a name="key-operations"></a>Kluczowe operacje

Usługa Key Vault obsługuje następujące operacje na kluczowych obiektach:  

-   **Utwórz**: Umożliwia klientowi utworzenie klucza w przechowalni kluczy. Wartość klucza jest generowany przez magazyn klucza i przechowywane i nie jest zwalniany do klienta. Klucze asymetryczne mogą być tworzone w przechowalni kluczy.  
-   **Import:** Umożliwia klientowi zaimportować istniejący klucz do usługi Key Vault. Klucze asymetryczne mogą być importowane do usługi Key Vault przy użyciu wielu różnych metod pakowania w konstrukcji JWK. 
-   **Aktualizacja**: Umożliwia klientowi z wystarczającymi uprawnieniami modyfikowanie metadanych (atrybutów klucza) skojarzonych z kluczem wcześniej przechowywanym w magazynie kluczy.  
-   **Usuń**: Umożliwia klientowi z wystarczającymi uprawnieniami usunięcie klucza z usługi Key Vault.  
-   **Lista**: Umożliwia klientowi wyświetlanie listy wszystkich kluczy w danym magazynie kluczy.  
-   **Wersje listy:** Umożliwia klientowi wyświetlanie listy wszystkich wersji danego klucza w danym magazynie kluczy.  
-   **Pobierz:** Umożliwia klientowi pobieranie publicznych części danego klucza w magazynie kluczy.  
-   **Kopia zapasowa**: Eksportuje klucz w postaci chronionej.  
-   **Przywracanie:** Importuje wcześniej utworzony klucz.  

Aby uzyskać więcej informacji, zobacz [Operacje klucza w odwołaniu interfejsu API REST magazynu kluczy](/rest/api/keyvault).  

Po utworzeniu klucza w magazynie kluczy można wykonać następujące operacje kryptograficzne za pomocą klucza:  

-   **Podpisz i sprawdź:** Ściśle, ta operacja jest "sign hash" lub "verify hash", jak Usługa Key Vault nie obsługuje mieszania zawartości w ramach tworzenia podpisu. Aplikacje powinny mieszać dane, które mają być podpisane lokalnie, a następnie zażądać, aby usługa Key Vault podpisała skrót. Weryfikacja podpisanych skrótów jest obsługiwana jako wygodna operacja dla aplikacji, które mogą nie mieć dostępu do [publicznych] materiałów kluczowych. Aby uzyskać najlepszą wydajność aplikacji, sprawdź, czy operacje są wykonywane lokalnie.  
-   **Szyfrowanie kluczy / zawijanie:** Klucz przechowywany w magazynie kluczy może być używany do ochrony innego klucza, zazwyczaj symetrycznego klucza szyfrowania zawartości (CEK). Gdy klucz w przechowalni kluczy jest asymetryczny, używane jest szyfrowanie kluczy. Na przykład RSA-OAEP i WRAPKEY/UNWRAPKEY operacje są równoważne szyfrować/odszyfrować. Gdy klucz w przechowalni kluczy jest symetryczny, używane jest zawijanie kluczy. Na przykład AES-KW. Operacja WRAPKEY jest obsługiwana jako udogodnienie dla aplikacji, które mogą nie mieć dostępu do [public] materiału klucza. Aby uzyskać najlepszą wydajność aplikacji, operacje WRAPKEY powinny być wykonywane lokalnie.  
-   **Szyfruj i odszyfrowywanie:** Klucz przechowywany w magazynie kluczy może być używany do szyfrowania lub odszyfrowywania pojedynczego bloku danych. Rozmiar bloku jest określany przez typ klucza i wybrany algorytm szyfrowania. Operacja Szyfrowanie jest dostępna dla wygody dla aplikacji, które mogą nie mieć dostępu do [publicznych] materiałów kluczowych. Aby uzyskać najlepszą wydajność aplikacji, operacje szyfrowania powinny być wykonywane lokalnie.  

Podczas WRAPKEY/UNWRAPKEY przy użyciu kluczy asymetrycznych może wydawać się zbędne (ponieważ operacja jest odpowiednikiem szyfrowania/odszyfrowywania), użycie różnych operacji jest ważne. Rozróżnienie zapewnia separacji semantyczne i autoryzacji tych operacji i spójności, gdy inne typy kluczy są obsługiwane przez usługę.  

Usługa Key Vault nie obsługuje operacji EXPORT. Po udostępnieniu klucza w systemie nie można go wyodrębnić ani zmodyfikować jego kluczowego materiału. Jednak użytkownicy usługi Key Vault mogą wymagać klucza w innych przypadkach użycia, na przykład po jego usunięciu. W takim przypadku mogą użyć operacji KOPIA ZAPASOWA i PRZYWRACANIE do eksportowania/importowania klucza w postaci chronionej. Klucze utworzone przez operację KOPIA ZAPASOWA nie mogą być użyteczne poza magazynem kluczy. Alternatywnie operacja IMPORT może być używana dla wielu wystąpień usługi Key Vault.  

Użytkownicy mogą ograniczyć dowolną operacje kryptograficzne, które usługa Key Vault obsługuje na podstawie klucza, przy użyciu właściwości key_ops obiektu JWK.  

Aby uzyskać więcej informacji na temat obiektów JWK, zobacz [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).  

###  <a name="key-attributes"></a>Kluczowe atrybuty

Oprócz materiału kluczowego można określić następujące atrybuty. W żądaniu JSON słowo kluczowe atrybuty i nawiasy klamrowe ,{' '}', są wymagane, nawet jeśli nie określono żadnych atrybutów.  

- *włączone*: logiczne, opcjonalne, domyślnie jest **prawdziwe**. Określa, czy klucz jest włączony i użyteczny dla operacji kryptograficznych. Włączony *enabled* atrybut jest używany w połączeniu z *nbf* i *exp*. Gdy operacja występuje między *nbf* i *exp*, będzie dozwolone tylko *wtedy, gdy włączona* jest ustawiona na **true**. Operacje poza oknem *nbf* / *exp* są automatycznie niedozwolone, z wyjątkiem niektórych typów operacji w [określonych warunkach.](#date-time-controlled-operations)
- *nbf*: IntDate, opcjonalnie, domyślnie jest teraz. Atrybut *nbf* (nie wcześniej) określa czas, przed którym klucz NIE MOŻE być używany do operacji kryptograficznych, z wyjątkiem niektórych typów operacji w [określonych warunkach](#date-time-controlled-operations). Przetwarzanie atrybutu *nbf* wymaga, aby bieżąca data/godzina MUSI być po lub równa daty/godzinie nieprzedna wymienionej w atrybucie *nbf.* Key Vault może zapewnić pewną małą swobodę, zwykle nie więcej niż kilka minut, aby uwzględnić pochylenie zegara. Jego wartość MUSI być liczbą zawierającą wartość IntDate.  
- *exp:* IntDate, opcjonalnie, domyślnie jest "na zawsze". Atrybut *exp* (czas wygaśnięcia) określa czas wygaśnięcia, po którym klucz NIE MOŻE być używany do operacji kryptograficznej, z wyjątkiem niektórych typów operacji w [określonych warunkach.](#date-time-controlled-operations) Przetwarzanie atrybutu *exp* wymaga, aby bieżąca data/godzina musi być przed datą/godziną wygaśnięcia wymienioną w atrybucie *exp.* Key Vault może zapewnić pewne małe pole manewru, zazwyczaj nie więcej niż kilka minut, aby uwzględnić pochylenie zegara. Jego wartość MUSI być liczbą zawierającą wartość IntDate.  

Istnieją dodatkowe atrybuty tylko do odczytu, które są zawarte w każdej odpowiedzi, która zawiera kluczowe atrybuty:  

- *utworzony*: IntDate, opcjonalnie. Utworzony *created* atrybut wskazuje, kiedy ta wersja klucza została utworzona. Wartość jest null dla kluczy utworzonych przed dodaniem tego atrybutu. Jego wartość MUSI być liczbą zawierającą wartość IntDate.  
- *aktualizacja*: IntDate, opcjonalnie. *Zaktualizowany* atrybut wskazuje, kiedy ta wersja klucza została zaktualizowana. Wartość jest null dla kluczy, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jego wartość MUSI być liczbą zawierającą wartość IntDate.  

Aby uzyskać więcej informacji na temat IntDate i innych typów danych, zobacz [Typy danych](#data-types)  

#### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Nieuprawnione i wygasłe klucze, poza oknem *nbf* / *exp,* będą działać w celu **odszyfrowania,** **rozpakuj**i **weryfikują** operacje (nie zwróci 403, Zabronione). Uzasadnieniem użycia stanu nieuwzwoleniu jest zezwolenie na testowanie klucza przed użyciem w produkcji. Uzasadnieniem użycia stanu wygasłe jest umożliwienie operacji odzyskiwania na danych, który został utworzony, gdy klucz był prawidłowy. Ponadto można wyłączyć dostęp do klucza przy użyciu zasad usługi Key Vault lub zaktualizować *atrybut klucza włączonego* do **false**.

Aby uzyskać więcej informacji na temat typów danych, zobacz [Typy danych](#data-types).

Aby uzyskać więcej informacji na temat innych możliwych atrybutów, zobacz [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41).

### <a name="key-tags"></a>Znaczniki kluczy

Można określić dodatkowe metadane specyficzne dla aplikacji w postaci tagów. Usługa Key Vault obsługuje maksymalnie 15 znaczników, z których każdy może mieć 256 znaków i wartość 256 znaków.  

>[!Note]
>Znaczniki są czytelne przez obiekt wywołujący, jeśli mają *listę* lub *uzyskać* uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).

###  <a name="key-access-control"></a>Kontrola dostępu do kluczy

Kontrola dostępu dla kluczy zarządzanych przez magazyn kluczy jest dostępna na poziomie magazynu kluczy, który działa jako kontener kluczy. Zasady kontroli dostępu dla kluczy różni się od zasad kontroli dostępu dla wpisów tajnych w tym samym Magazynie kluczy. Użytkownicy mogą utworzyć jeden lub więcej magazynów do przechowywania kluczy i są wymagane do obsługi scenariusza odpowiedniej segmentacji i zarządzania kluczami. Kontrola dostępu do kluczy jest niezależna od kontroli dostępu do wpisów tajnych.  

Następujące uprawnienia mogą być przyznane, na podstawie jednostki użytkownika / usługi, we wpisie kontroli dostępu kluczy w przechowalni. Te uprawnienia ściśle dubluje operacje dozwolone na kluczowym obiekcie.  Udzielanie dostępu do jednostki usługi w magazynie kluczy jest operacją jednorazową i pozostanie taka sama dla wszystkich subskrypcji platformy Azure. Można go użyć do wdrożenia dowolną liczbę certyfikatów. 

- Uprawnienia do operacji zarządzania kluczami
  - *get*: Przeczytaj publiczną część klucza, a także jego atrybuty
  - *lista*: Lista kluczy lub wersji klucza przechowywanego w magazynie kluczy
  - *update*: Aktualizowanie atrybutów klucza
  - *tworzenie*: Tworzenie nowych kluczy
  - *import*: Importowanie klucza do magazynu kluczy
  - *delete*: Usuwanie obiektu klucza
  - *odzyskiwanie*: Odzyskiwanie usuniętego klucza
  - *kopia zapasowa*: Tworzenie kopii zapasowej klucza w magazynie kluczy
  - *przywracanie*: Przywracanie kopii zapasowej klucza do magazynu kluczy

- Uprawnienia do operacji kryptograficznych
  - *odszyfrowywanie*: Użyj klucza, aby odchować sekwencję bajtów
  - *szyfrowanie*: Użyj klucza, aby chronić dowolną sekwencję bajtów
  - *unwrapKey*: Użyj klucza, aby odchronić zawinięte klucze symetryczne
  - *wrapKey*: Użyj klucza, aby chronić klucz symetryczny
  - *verify*: Użyj klucza do weryfikacji skrótów  
  - *znak*: Użyj klawisza, aby podpisać skróty
    
- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszcz (trwale usuń) usunięty klucz

Aby uzyskać więcej informacji na temat pracy z kluczami, zobacz [Operacje klucza w odwołaniu do interfejsu API REST magazynu kluczy](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz [Vaults - Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [przechowalnia — Aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="see-also"></a>Zobacz też

- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
