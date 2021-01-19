---
title: Podstawowe pojęcia dotyczące zaświadczania platformy Azure
description: Podstawowe pojęcia dotyczące zaświadczania platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: c6c09dc771692cb2fc2f36840e729874cfaf2d09
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572820"
---
# <a name="basic-concepts"></a>Koncepcje podstawowe

Poniżej przedstawiono podstawowe pojęcia związane z zaświadczeniem Microsoft Azure.

## <a name="json-web-token-jwt"></a>Token sieci Web JSON (JWT)

[Token sieci Web JSON](https://jwt.io/) (JWT) to otwarta standardowa metoda [RFC7519](https://tools.ietf.org/html/rfc7519) w celu bezpiecznego przekazywania informacji między stronami jako obiektem JavaScript Object Notation (JSON). Te informacje mogą być zweryfikowane i zaufane, ponieważ są podpisane cyfrowo. JWTs można podpisać przy użyciu klucza tajnego lub pary kluczy publicznych/prywatnych.

## <a name="json-web-key-jwk"></a>Klucz internetowy JSON (JWK)

[Klucz internetowy JSON](https://tools.ietf.org/html/rfc7517) (JWK) to struktura danych JSON, która reprezentuje klucz kryptograficzny. Ta specyfikacja definiuje również strukturę danych JSON zestawu JWK, która reprezentuje zestaw JWKs.

## <a name="attestation-provider"></a>Dostawca zaświadczania

Dostawca zaświadczania należy do dostawcy zasobów platformy Azure o nazwie Microsoft. zaświadczanie. Dostawca zasobów jest punktem końcowym usługi, który zapewnia umowę REST zaświadczania platformy Azure i jest wdrażany przy użyciu [Azure Resource Manager](../azure-resource-manager/management/overview.md). Każdy dostawca zaświadczania jest uznawany za określone, wykrywalne zasady. Dostawcy zaświadczania zostaną utworzeni przy użyciu domyślnych zasad dla każdego typu zaświadczania (należy zauważyć, że VBS enklawy nie ma zasad domyślnych). Zobacz [przykłady zasad zaświadczania](policy-examples.md) , aby uzyskać więcej szczegółowych informacji na temat zasad domyślnych dla SGX.

### <a name="regional-default-provider"></a>Domyślny dostawca regionalny

Zaświadczanie platformy Azure zapewnia domyślnego dostawcę w każdym regionie. Klienci mogą wybrać użycie domyślnego dostawcy zaświadczania lub utworzyć własnych dostawców przy użyciu zasad niestandardowych. Dostawcy domyślnie są dostępni przez dowolnego użytkownika usługi Azure AD, a zasady skojarzone z domyślnym dostawcą nie mogą być modyfikowane.

| Region (Region) | Zaświadczanie identyfikatora URI | 
|--|--|
| East US | `https://sharedeus.eus.attest.azure.net` | 
| Zachodnie stany USA | `https://sharedwus.wus.attest.azure.net` | 
| Południowe Zjednoczone Królestwo | `https://shareduks.uks.attest.azure.net` | 
| Zachodnie Zjednoczone Królestwo| `https://sharedukw.ukw.attest.azure.net  ` | 
| Kanada Wschodnia | `https://sharedcae.cae.attest.azure.net` | 
| Kanada Środkowa | `https://sharedcac.cac.attest.azure.net` | 
| Europa Północna | `https://sharedneu.neu.attest.azure.net` | 
| West Europe| `https://sharedweu.weu.attest.azure.net` | 
| Wschodnie stany USA 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| Central US | `https://sharedcus.cus.attest.azure.net` | 

## <a name="attestation-request"></a>Żądanie zaświadczania

Żądanie zaświadczania jest serializowanym obiektem JSON wysłanym przez aplikację kliencką do dostawcy zaświadczania. Obiekt request dla SGX enklawy ma dwie właściwości: 
- "Quote" — wartość właściwości "Quote" to ciąg zawierający zaBase64URLą reprezentację cytatu zaświadczania
- "EnclaveHeldData" — wartość właściwości "EnclaveHeldData" jest ciągiem zawierającym reprezentację Base64URL zaszyfrowanej danych enklawy.

Zaświadczanie platformy Azure będzie sprawdzać poprawność podanego elementu "quota", a następnie upewnić się, że Skrót SHA256 podanej enklawy jest przechowywany w pierwszych 32 bajtach pola reportData w ofercie. 

## <a name="attestation-policy"></a>Zasady zaświadczania

Zasady zaświadczania są używane do przetwarzania dowodów zaświadczania i są konfigurowane przez klientów. Na początku zaświadczania platformy Azure jest aparatem zasad, który przetwarza oświadczenia tworzące dowody. Zasady służą do ustalania, czy zaświadczanie platformy Azure wystawia token zaświadczania na podstawie dowodu (lub nie), a tym samym poświadcza zaświadczanie (lub nie). W związku z tym niepowodzenie przekazania wszystkich zasad spowoduje, że nie zostanie wystawiony token JWT.

Jeśli zasady domyślne w ramach dostawcy zaświadczania nie spełnią potrzeb, klienci będą mogli tworzyć niestandardowe zasady w dowolnym regionie obsługiwanym przez zaświadczanie platformy Azure. Zarządzanie zasadami to kluczowa funkcja udostępniona klientom przez zaświadczanie platformy Azure. Zasady będą specyficzne dla typu zaświadczania i mogą służyć do identyfikowania enclaves lub dodawania oświadczeń do tokenu wyjściowego lub modyfikowania oświadczeń w tokenie wyjściowym. 

Zobacz [przykłady zasad zaświadczania](policy-examples.md) dla przykładów zasad.

## <a name="benefits-of-policy-signing"></a>Zalety podpisywania zasad

Zasady zaświadczania to ostatecznie ustalenie, czy token zaświadczania zostanie wystawiony przez zaświadczenie platformy Azure. Zasady określają również oświadczenia, które mają być generowane w token zaświadczania. Jest to szczególnie ważne, że zasady oceniane przez usługę są w rzeczywistości zasadami zapisanymi przez administratora i nie zostały naruszone ani zmodyfikowane przez podmioty zewnętrzne. 

Model zaufania definiuje model autoryzacji dostawcy zaświadczania do definiowania i aktualizacji zasad.  Obsługiwane są dwa modele — jeden oparty na autoryzacji usługi Azure AD i jeden w oparciu o posiadanie kluczy kryptograficznych zarządzanych przez klienta (nazywanych modelem izolowanym).  Model izolowany umożliwi zaświadczenie platformy Azure, aby upewnić się, że zasady przesłane przez klienta nie zostały naruszone.

W modelu izolowanym administrator tworzy dostawcę zaświadczania, określając zestaw zaufanych certyfikatów X. 509 podpisywania w pliku. Administrator może dodać podpisane zasady do dostawcy zaświadczania. Podczas przetwarzania żądania zaświadczania zaświadczenie platformy Azure sprawdzi podpisy zasad przy użyciu klucza publicznego reprezentowanego przez parametr "JWK" lub "x5c" w nagłówku.  Zaświadczanie platformy Azure sprawdzi również, czy klucz publiczny w nagłówku żądania znajduje się na liście zaufanych certyfikatów podpisywania skojarzonych z dostawcą zaświadczania. W ten sposób Jednostka uzależniona (zaświadczanie platformy Azure) może ufać zasadom podpisanym za pomocą certyfikatów X. 509, o których wie. 

Zobacz [przykłady certyfikatu osoby podpisującej zasady](policy-signer-examples.md) dla przykładów.

## <a name="attestation-token"></a>Token zaświadczania

Odpowiedź zaświadczania platformy Azure będzie ciągiem JSON, którego wartość zawiera token JWT. Zaświadczanie platformy Azure spowoduje spakowanie oświadczeń i wygenerowanie podpisanego tokenu JWT. Operacja podpisywania jest przeprowadzana przy użyciu certyfikatu z podpisem własnym z nazwą podmiotu zgodną z elementem AttestUri dostawcy zaświadczania.

Interfejs API pobierania metadanych OpenID Connect zwraca odpowiedź konfiguracyjną OpenID Connect, zgodnie z definicją podaną przez [Protokół OpenID Connect Connection Protocol](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). Interfejs API pobiera metadane dotyczące certyfikatów podpisywania używanych przez zaświadczanie platformy Azure.

Przykład generowania tokenu JWT dla SGX enklawy:

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0"
}.[Signature]
```
Niektóre z użytych powyżej oświadczeń są uważane za przestarzałe, ale są w pełni obsługiwane.  Zaleca się, aby wszystkie przyszłe kod i narzędzia używały nieprzestarzałych nazw zgłoszeń. Aby uzyskać więcej informacji [, zobacz oświadczenia wystawione przez zaświadczenie platformy Azure](claim-sets.md) .

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie danych magazynowanych

W celu zabezpieczenia danych klienta zaświadczanie platformy Azure zachowuje swoje dane w usłudze Azure Storage. Usługa Azure Storage udostępnia szyfrowanie danych przechowywanych w centrach danych i odszyfrowuje je w celu uzyskania dostępu do nich. To szyfrowanie odbywa się przy użyciu zarządzanego klucza szyfrowania firmy Microsoft. 

Oprócz ochrony danych w usłudze Azure Storage zaświadczenie platformy Azure wykorzystuje również Azure Disk Encryption (ADE) do szyfrowania maszyn wirtualnych usługi. W przypadku zaświadczania platformy Azure działającego w enklawy w środowiskach przetwarzania poufnego platformy Azure rozszerzenie ADE nie jest obecnie obsługiwane. W takich scenariuszach, aby zapobiec przechowywaniu danych w pamięci, plik stronicowania jest wyłączony. 

Na lokalnych dyskach twardych wystąpienia zaświadczania platformy Azure nie są utrwalane żadne dane klienta.


## <a name="next-steps"></a>Następne kroki

- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)
