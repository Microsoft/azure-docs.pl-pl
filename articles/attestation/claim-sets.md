---
title: Azure Attestation zestawów oświadczenia
description: Zestawy oświadczenia Azure Attestation.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517559"
---
# <a name="claim-sets"></a>Zestawy oświadczeń

Oświadczenia generowane w procesie poświadczania enklaw przy użyciu Microsoft Azure zaświadczenia można podzielić na następujące kategorie:

- **Oświadczenia przychodzące:** oświadczenia generowane przez Microsoft Azure zaświadczenia po analizie dowodu zaświadczenia i mogą być używane przez autorów zasad do definiowania reguł autoryzacji w zasadach niestandardowych

- **Oświadczenia wychodzące:** oświadczenia wygenerowane przez Azure Attestation uwzględnione w tokenie zaświadczenia

- **Oświadczenia właściwości:** oświadczenia utworzone jako dane wyjściowe przez Azure Attestation. Zawiera wszystkie oświadczenia reprezentujące właściwości tokenu zaświadczenia, takie jak kodowanie raportu, czas trwania ważności raportu i tak dalej.

## <a name="incoming-claims"></a>Oświadczenia przychodzące 

### <a name="sgx-attestation"></a>SGX atestacja

Oświadczenia, które mają być używane przez autorów zasad do definiowania reguł autoryzacji w zasadach zaświadczenia SGX:

- **x-ms-sgx-is-debuggable:** wartość logiczna wskazująca, czy debugowanie enklawy jest włączone.
  
  Enklawy SGX można ładować z wyłączonym debugowaniem lub włączonym. Gdy flaga jest ustawiona na wartość true w enklawie, włącza funkcje debugowania dla kodu enklawy. Obejmuje to możliwość dostępu do pamięci enklawy. W związku z tym zaleca się ustawienie flagi na wartość true tylko na potrzeby developmentu. Jeśli ta opcja jest włączona w środowisku produkcyjnym, gwarancje zabezpieczeń SGX nie zostaną zachowane.
  
  Azure Attestation użytkownicy mogą użyć zasad zaświadczenia, aby sprawdzić, czy debugowanie jest wyłączone dla enklawy SGX. Po dodaniu reguły zasad zaświadczenia nie powiedzie się, gdy złośliwy użytkownik włączy obsługę debugowania, aby uzyskać dostęp do zawartości enklawy.

- **x-ms-sgx-product-id:** wartość całkowita, która wskazuje identyfikator produktu enklawy SGX.

  Autor enklawy przypisuje identyfikator produktu do każdej enklawy. Identyfikator produktu umożliwia autorowi enklawy segmentację enklaw podpisanych przy użyciu tego samego narzędzia MRSIGNER. Dodając regułę weryfikacji w zasadach zaświadczenia, klienci mogą sprawdzić, czy są one w zamierzonych enklawach. Zaświadczenia nie powiedzie się, jeśli identyfikator produktu enklawy nie pasuje do wartości opublikowanej przez autora enklawy.

- **x-ms-sgx-mrsigner:** wartość ciągu, która identyfikuje autora enklawy SGX.

  MRSIGNER to skrót klucza publicznego autora enklawy, który jest używany do podpisywania pliku binarnego enklawy. Dzięki weryfikacji programu MRSIGNER za pośrednictwem zasad zaświadczenia klienci mogą sprawdzić, czy zaufane pliki binarne działają w enklawie. Jeśli oświadczenie zasad nie jest zgodne z dokumentem MRSIGNER autora enklawy, oznacza to, że plik binarny enklawy nie jest podpisany przez zaufane źródło i zaświadczenia kończy się niepowodzeniem.
  
  Jeśli autor enklawy preferuje rotację funkcji MRSIGNER ze względów bezpieczeństwa, zasady Azure Attestation muszą zostać zaktualizowane w celu obsługi nowych i starych wartości MRSIGNER przed zaktualizowaniem plików binarnych. W przeciwnym razie testy autoryzacji nie powiodą się, co spowoduje błędy zaświadczenia.
  
  Zasady zaświadczenia muszą zostać zaktualizowane przy użyciu poniższego formatu. 
 
  #### <a name="before-key-rotation"></a>Przed rotacją kluczy
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Podczas rotacji kluczy

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Po rotacji kluczy

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave:** wartość ciągu, która identyfikuje kod i dane załadowane w pamięci enklawy. 

  MRENCLAVE jest jednym z pomiarów enklawy, których można użyć do zweryfikowania plików binarnych enklawy. Jest to skrót kodu działającego wewnątrz enklawy. Miara zmienia się wraz z każdą zmianą w kodzie binarnym enklawy. Dzięki weryfikacji MRENCLAVE za pośrednictwem zasad zaświadczenia klienci mogą sprawdzić, czy zamierzone pliki binarne są uruchomione wewnątrz enklawy. Jednak ze względu na to, że aplikacja MRENCLAVE będzie często zmieniana przy użyciu wszelkich trywialnych modyfikacji istniejącego kodu, zaleca się zweryfikowanie plików binarnych enklawy przy użyciu weryfikacji MRSIGNER w zasadach zaświadczenia.

- **x-ms-sgx-svn:** wartość całkowita, która wskazuje numer wersji zabezpieczeń enklawy SGX

  Autor enklawy przypisuje numer wersji zabezpieczeń (SVN) do każdej wersji enklawy SGX. Gdy w kodzie enklawy zostanie wykryty problem z zabezpieczeniami, autor enklawy zwiększa wartość SVN po poprawce luki w zabezpieczeniach. Aby zapobiec interakcji z niezabezpieczoną enklawą, klienci mogą dodać regułę weryfikacji w zasadach zaświadczenia. Jeśli SVN kodu enklawy nie jest zgodne z wersją zalecaną przez autora enklawy, zaświadczenia nie powiedzie się.

Poniższe oświadczenia są uznawane za przestarzałe, ale są w pełni obsługiwane i będą nadal uwzględniane w przyszłości. Zaleca się używanie nieo nadanych nazw oświadczenia.

Przestarzałe oświadczenie | Zalecane oświadczenie
--- | --- 
$is debugowania | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>Poświadczanie za pomocą modułu TPM

Oświadczenia, które mają być używane przez autorów zasad do definiowania reguł autoryzacji w zasadach zaświadczenia modułu TPM:

- **aikValidated:** wartość logiczna zawierająca informacje, jeśli certyfikat AIK (Attestation Identity Key) został zweryfikowany lub nie
- **aikPubHash:** ciąg zawierający base64(SHA256(Klucz publiczny AIK w formacie DER))
- **tpmVersion:** wartość całkowita zawierająca wersję główną Trusted Platform Module (TPM)
- **secureBootEnabled:** wartość logiczna wskazująca, czy bezpieczny rozruch jest włączony
- **iommuEnabled:** wartość logiczna wskazująca, czy włączono jednostkę zarządzania pamięcią we/wy (Iommu)
- **bootDebuggingDisabled:** wartość logiczna wskazująca, czy debugowanie rozruchu jest wyłączone
- **notSafeMode:** wartość logiczna wskazująca, czy system Windows nie jest uruchomiony w trybie awaryjnym
- **notWinPE:** wartość logiczna wskazująca, czy system Windows nie jest uruchomiony w trybie winPE
- **vbsEnabled:** wartość logiczna wskazująca, czy włączono usługę VBS
- **vbsReportPresent:** Wartość logiczna wskazująca, czy raport enklawy VBS jest dostępny

### <a name="vbs-attestation"></a>Zaświadczenia VBS

Oprócz oświadczeń zasad zaświadczenia modułu TPM autorzy zasad mogą używać poniższych oświadczeń do definiowania reguł autoryzacji w zasadach zaświadczenia VBS.

- **enclaveAuthorId:** wartość ciągu zawierająca zakodowaną wartość Base64Url enklawy author id-Identyfikator autora modułu podstawowego dla enklawy
- **enclaveImageId:** wartość ciągu zawierająca zakodowaną wartość Base64Url enklawy Image id-Identyfikator obrazu modułu podstawowego dla enklawy
- **enclaveOwnerId:** wartość ciągu zawierająca zakodowaną wartość Base64Url identyfikatora właściciela enklawy — identyfikator właściciela enklawy
- **enclaveFamilyId:** wartość ciągu zawierająca zakodowaną wartość Base64Url identyfikatora rodziny enklawy. Identyfikator rodziny podstawowego modułu enklawy
- **enclaveSvn:** wartość całkowita zawierająca numer wersji zabezpieczeń podstawowego modułu enklawy
- **enclavePlatformSvn:** wartość całkowita zawierająca numer wersji zabezpieczeń platformy, która hostuje enklawę
- **enclaveFlags:** oświadczenie enclaveFlags jest wartością całkowitą zawierającą flagi opisujące zasady środowiska uruchomieniowego dla enklawy

## <a name="outgoing-claims"></a>Oświadczenia wychodzące 

### <a name="common-for-all-attestation-types"></a>Wspólne dla wszystkich typów zaeświadczenia

Azure Attestation zawiera poniższe oświadczenia w tokenie zaświadczenia dla wszystkich typów zaświadczenia. 

- **x-ms-ver:** wersja schematu JWT (oczekiwana wersja to "1.0")
- **x-ms-attestation-type:** wartość ciągu reprezentująca typ zaświadczenia 
- **x-ms-policy-hash:** skrót zasad oceny Azure Attestation obliczony jako BASE64URL(SHA256(UTF8(BASE64URL(UTF8(tekst zasad)))))
- **x-ms-policy-signer:** obiekt JSON z elementem członkowskim "jwk" reprezentującym klucz używany przez klienta do podpisania zasad. Ma to zastosowanie, gdy klient przekaże podpisane zasady

Poniższe nazwy oświadczenia są używane ze specyfikacji [IETF JWT](https://tools.ietf.org/html/rfc7519)

- **Oświadczenie "jti" (identyfikator JWT)** — unikatowy identyfikator dla JWT
- **"iss" (wystawca) —** podmiot zabezpieczeń, który wystawił JWT 
- **"iat" (wystawiony o) oświadczenia -** czas, o którym JWT został wystawiony o 
- **Oświadczenie "exp" (czas wygaśnięcia) —** czas wygaśnięcia, po którym nie można zaakceptować JWT do przetwarzania
- **Oświadczenie "nbf" (nie przed) —** nie przed czasem, przed którym nie można zaakceptować JWT do przetwarzania 

Poniższe nazwy roszczeń są używane ze specyfikacji [roboczej IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Nonce claim" (nonce)** — nieprzekształcana bezpośrednia kopia opcjonalnej wartości nonce dostarczonej przez klienta 

Poniższe oświadczenia są uznawane za przestarzałe, ale są w pełni obsługiwane i będą nadal uwzględniane w przyszłości. Zaleca się używanie nieo wycofanych nazw oświadczenia.

Przestarzałe oświadczenie | Zalecane oświadczenie
--- | --- 
Ver | x-ms-ver
Tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>SGX atestacja 

Poniższe oświadczenia są generowane i uwzględniane w tokenie zaświadczenia przez usługę do zaświadczenia SGX.

- **x-ms-sgx-is-debuggable:** wartość logiczna wskazująca, czy enklawa ma włączone debugowanie, czy nie
- **x-ms-sgx-product-id:** wartość identyfikatora produktu enklawy SGX 
- **x-ms-sgx-mrsigner:** wartość zakodowana w formacie hex pola "mrsigner" oferty
- **x-ms-sgx-mrenclave:** wartość zakodowana w formacie hex pola "mrenclave" oferty
- **x-ms-sgx-svn:** numer wersji zabezpieczeń zakodowany w cudzysłowie 
- **x-ms-sgx-ehd**: enklawa, w których przechowywane są dane sformatowane jako BASE64URL(dane przechowywane w enklawie)
- **x-ms-sgx-wymuś:** obiekt JSON opisujący suport używany do wykonywania zaświadczenia. Wartość oświadczenia x-ms-sgx-przejmuje zagnieżdżony obiekt JSON z następującymi parami klucz/wartość:
    - **qeidcertshash:** wartość SHA256 dla certyfikatów wystawiających tożsamość w enklawie (QE)
    - **qeidcrlhash:** wartość SHA256 listy CRL wystawiania certyfikatów tożsamości QE
    - **qeidhash:** wartość SHA256 tożsamości QE
    - **quotehash:** wartość SHA256 ocenianego cudzysłowu
    - **tcbinfocertshash:** wartość SHA256 certyfikatów wystawiających informacje TCB
    - **tcbinfocrlhash:** wartość SHA256 listy CRL wystawiania informacji TCB
    - **tcbinfohash:** wartość SHA256 obiektu informacyjnego TCB

Poniższe oświadczenia są uznawane za przestarzałe, ale są w pełni obsługiwane i będą nadal uwzględniane w przyszłości. Zaleca się używanie nieocenionych nazw oświadczenia.

Przestarzałe oświadczenie | Zalecane oświadczenie
--- | --- 
$is debugowania | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenklawa | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-wymusz

### <a name="tpm-and-vbs-attestation"></a>Zaświadczenia modułu TPM i VBS

- **cnf (potwierdzenie):** oświadczenie "cnf" służy do identyfikowania klucza dowodu posiadania. Oświadczenie potwierdzenia zdefiniowane w dokumencie RFC 7800 zawiera publiczną część attestowanych kluczy enklawy reprezentowanych jako obiekt JSON Web Key (JWK) (RFC 7517)
- **rp_data (dane** jednostki zależnej): dane jednostki zależnej, jeśli są określone w żądaniu, używane przez jednostkę jednostki zależnej jako nonce do zagwarantowania świeżości raportu. rp_data jest dodawana tylko wtedy, gdy istnieje rp_data

## <a name="property-claims"></a>Oświadczenia właściwości

### <a name="tpm-and-vbs-attestation"></a>Zaświadczenia modułu TPM i VBS

- **report_validity_in_minutes:** oświadczenie liczby całkowitej oznaczace, jak długo token jest ważny.
  - **Wartość domyślna (czas):** jeden dzień w minutach.
  - **Wartość maksymalna (czas):** jeden rok w minutach.
- **omit_x5c:** oświadczenie logiczne wskazujące, Azure Attestation należy pominąć certyfikat używany do potwierdzenia autentyczności usługi. W przypadku wartości true wartość x5t zostanie dodana do tokenu zaświadczenia. Jeśli wartość false (domyślna), x5c zostanie dodany do tokenu zaświadczenia.

## <a name="next-steps"></a>Następne kroki
- [Tworzenie i podpisywanie zasad zaświadczenia](author-sign-policy.md)
- [Konfigurowanie aplikacji Azure Attestation użyciu programu PowerShell](quickstart-powershell.md)
