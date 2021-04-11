---
title: Jak utworzyć zasady zaświadczania platformy Azure
description: Wyjaśnienie sposobu tworzenia zasad zaświadczania.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: aaf05b293fa5a13c5fe6e341f7de987c6002a5cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607970"
---
# <a name="how-to-author-an-attestation-policy"></a>Jak utworzyć zasady zaświadczania

Zasady zaświadczania to plik przekazany do Microsoft Azure zaświadczania. Zaświadczanie platformy Azure oferuje elastyczność przekazywania zasad w formacie zasad specyficznych dla zaświadczania. Alternatywnie można również przekazać zakodowaną wersję zasad w podpisie sieci Web JSON. Administrator zasad jest odpowiedzialny za zapisanie zasad zaświadczania. W większości scenariuszy zaświadczania Jednostka uzależniona działa jako administrator zasad. Klient wykonujący wywołanie zaświadczania wysyła dowód zaświadczania, który usługa analizuje i konwertuje na oświadczenia przychodzące (zbiór właściwości, wartość). Następnie usługa przetwarza oświadczenia na podstawie tego, co zostało zdefiniowane w zasadach, i zwraca obliczony wynik.

Zasady zawierają reguły określające kryteria autoryzacji, właściwości i zawartość tokenu zaświadczania. Przykładowy plik zasad wygląda następująco:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnabled", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnabled", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Plik zasad ma trzy segmenty, jak pokazano powyżej:

- **wersja**: wersja jest numerem wersji gramatyki, która jest obserwowana. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    Obecnie jedyną obsługiwaną wersją jest wersja 1,0.

- **reguł autoryzacji**: Kolekcja reguł poświadczeń, które będą sprawdzane jako pierwsze, aby określić, czy zaświadczenie platformy Azure ma przebiegać do **issuancerules**. Reguły dotyczące roszczeń są stosowane w kolejności, w jakiej zostały zdefiniowane.

- **issuancerules**: Kolekcja reguł dotyczących roszczeń, które zostaną ocenione w celu dodania dodatkowych informacji do wyniku zaświadczania zgodnie z definicją w zasadach. Reguły dotyczące roszczeń są stosowane w kolejności, w jakiej są zdefiniowane, i są opcjonalne.

Aby uzyskać więcej informacji [, zobacz zasady dotyczące roszczeń i roszczeń](claim-rule-grammar.md) .
   
## <a name="drafting-the-policy-file"></a>Opracowywanie pliku zasad

1. Utwórz nowy plik.
1. Dodaj wersję do pliku.
1. Dodaj sekcje dla **reguł autoryzacji** i **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Reguły autoryzacji zawierają akcję Odmów () bez żadnego warunku, aby upewnić się, że żadne reguły wystawiania nie są przetwarzane. Alternatywnie reguła autoryzacji może również zawierać akcję Zezwalaj (), aby umożliwić przetwarzanie reguł wystawiania.
  
4. Dodawanie reguł roszczeń do reguł autoryzacji

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Jeśli zestaw zgłaszanych roszczeń zawiera zastrzeżenie pasujące do typu, wartości i wystawcy, Akcja Zezwalaj () wykryje aparat zasad, aby przetworzyć **issuancerules**.
  
5. Dodaj reguły dotyczące roszczeń do **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Zbiór wychodzących roszczeń zawiera element z:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Złożone zasady można przystąpić w podobny sposób. Aby uzyskać więcej informacji, zobacz [przykłady zasad zaświadczania](policy-examples.md).
  
6. Zapisz plik.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Tworzenie pliku zasad w formacie podpisu internetowego JSON

Po utworzeniu pliku zasad, aby przekazać zasady w formacie JWS, wykonaj poniższe czynności.

1. Generuj JWS, RFC 7515 z zasadami (kodowanie UTF-8) jako ładunek
     - Identyfikator ładunku dla zasad zakodowanych w Base64Url powinien mieć wartość "AttestationPolicy".
     
     Przykładowa JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. Obowiązkowe Podpisz zasady. Zaświadczanie platformy Azure obsługuje następujące algorytmy:
     - **Brak**: nie Podpisz ładunku zasad.
     - **RS256**: obsługiwany algorytm podpisywania ładunku zasad

3. Przekaż JWS i sprawdź poprawność zasad.
     - Jeśli plik zasad nie zawiera błędów składni, plik zasad zostanie zaakceptowany przez usługę.
     - Jeśli plik zasad zawiera błędy składniowe, plik zasad zostanie odrzucony przez usługę.

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)
- [Zaświadcz SGX enklawy przy użyciu przykładów kodu](/samples/browse/?expanded=azure&terms=attestation)
