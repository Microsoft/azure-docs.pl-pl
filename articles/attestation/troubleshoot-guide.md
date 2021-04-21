---
title: Przewodnik rozwiązywania problemów z usługą Azure Attestation
description: Przewodnik po problemach z często obserwowanymi problemami
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d3e34bee3d0f1420b379638389e6fad0a2fed60
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831568"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure rozwiązywania problemów z zaświadczeniami

Obsługa błędów w Azure Attestation jest implementowane zgodnie z [wytycznymi interfejsu API REST firmy Microsoft.](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses) Odpowiedź na błąd zwrócona przez interfejsy API Azure Attestation API zawiera kod stanu HTTP i pary nazwa/wartość z nazwami "code" i "message". Wartość "code" jest czytelna dla człowieka i jest wskaźnikiem typu błędu. Wartość "message" ma pomóc użytkownikowi i zawiera szczegóły błędu.

Jeśli problem nie został rozwiązany w tym artykule, możesz również przesłać żądanie pomocy technicznej platformy Azure na stronie [pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/options/)

Poniżej przedstawiono kilka przykładów błędów zwracanych przez Azure Attestation:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP–401: Brak autoryzacji wyjątku

### <a name="http-status-code"></a>Kod stanu HTTP
401

**Kod błędu** Nieautoryzowanych

**Przykłady scenariuszy**
  - Nie można zarządzać zasadami zaświadczenia, ponieważ użytkownik nie ma przypisanych odpowiednich ról
  - Nie można zarządzać podpisujących zasady za pomocą zaświadczenia, ponieważ użytkownik nie ma przypisanych odpowiednich ról

Użytkownik z rolą Czytelnik próbujący edytować zasady zaświadczenia w programie PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Kroki rozwiązywania problemów**

Aby zarządzać zasadami, użytkownik usługi Azure AD wymaga następujących uprawnień dla akcji:
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Aby wykonać te akcje, użytkownik usługi Azure AD musi mieć rolę "Współautor zaświadczenia" u dostawcy zaświadczenia. Te uprawnienia mogą być również dziedziczone z rolami, takimi jak "Właściciel" (uprawnienia z symbolami wieloznacznymi), "Współautor" (uprawnienia z symbolami wieloznaczny) w subskrypcji/grupie zasobów.  

Aby można było odczytać zasady, użytkownik usługi Azure AD wymaga następującego uprawnienia dla akcji:
- Microsoft.Attestation/attestationProviders/attestation/read

  Aby wykonać tę akcję, użytkownik usługi Azure AD musi mieć rolę "Czytelnik zaświadczenia" u dostawcy zaświadczenia. Uprawnienie do odczytu może być również dziedziczone z rolami, takimi jak "Czytelnik" (uprawnienia z symbolami wieloznacznymi) w subskrypcji/grupie zasobów.  

Aby zweryfikować role w programie PowerShell, uruchom następujące kroki:

a. Uruchom program PowerShell i zaloguj się do platformy Azure za pomocą polecenia cmdlet "Connect-AzAccount"

b. Zapoznaj się ze wskazówkami [w tym miejscu,](../role-based-access-control/role-assignments-list-powershell.md) aby zweryfikować przypisanie roli platformy Azure w dostawcy za pomocą zaświadczenia

c. Jeśli nie znajdziesz odpowiedniego przypisania roli, postępuj zgodnie z instrukcjami podanymi [tutaj](../role-based-access-control/role-assignments-powershell.md)

## <a name="2-http--400-errors"></a>2. Błędy HTTP –400

### <a name="http-status-code"></a>Kod stanu HTTP
400

Istnieją różne przyczyny, dla których żądanie może zwrócić 400. Poniżej przedstawiono kilka przykładów błędów zwracanych przez Azure Attestation API:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Niepowodzenie zaświadczenia z powodu błędów oceny zasad

Zasady zaświadczenia obejmują reguły autoryzacji i reguły wystawiania. Dowód enklawy jest oceniany na podstawie reguł autoryzacji. Reguły wystawiania definiują oświadczenia, które mają być uwzględnione w tokenie zaświadczenia. Jeśli oświadczenia w dowodach enklawy nie są zgodne z regułami autoryzacji, wywołania atestu będą zwracać błąd oceny zasad. 

**Kod błędu** PolicyEvaluationError

**Przykłady scenariuszy** Gdy oświadczenia w cudzysłowie enklawy nie są zgodne z regułami autoryzacji zasad zaświadczenia

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Kroki rozwiązywania problemów** Użytkownicy mogą ocenić dowód enklawy na zasadach zaświadczenia SGX przed skonfigurowaniem tych samych.

Wyślij żądanie do interfejsu API zaświadczenia, podając tekst zasad w parametrze "draftPolicyForAttestation". Interfejs API AttestSgxEnclave będzie używać tego dokumentu zasad podczas wywołania atestu i może służyć do testowania zasad poświadczania przed ich użyciem. Token zaświadczenia wygenerowany, gdy to pole jest obecne, będzie niezabezpieczony.

Zobacz [przykłady zasad zaświadczenia](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Niepowodzenie zaświadczenia z powodu nieprawidłowych danych wejściowych

**Kod błędu** InvalidParameter (Nieprawidłowyparametr)

**Przykłady scenariuszy** Niepowodzenie zaświadczenia SGX z powodu nieprawidłowych danych wejściowych. Poniżej przedstawiono niektóre przykłady komunikatów o błędach:
- Określony cudzysłów był nieprawidłowy z powodu błędu w cudzysłowie 
- Określony cudzysłów był nieprawidłowy, ponieważ urządzenie, na którym został wygenerowany cudzysłów, nie spełnia wymagań planu bazowego platformy Azure
- Określony cudzysłów był nieprawidłowy, ponieważ TCBInfo lub QEID dostarczone przez usługę pamięci podręcznej PCK były nieprawidłowe

**Kroki rozwiązywania problemów**

Microsoft Azure atest obsługuje zaświadczenia ofert SGX wygenerowanych przez zestaw Intel SDK i zestaw OPEN Enclave SDK.

Zapoznaj się z [przykładami kodu](/samples/browse/?expanded=azure&terms=attestation) do przeprowadzania zaświadczenia przy użyciu zestawu OPEN Enclave SDK/Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Błąd nieprawidłowego łańcucha certyfikatów podczas przekazywania zasad/podpisatora zasad

**Kod błędu** InvalidParameter (Nieprawidłowyparametr)

**Przykłady scenariuszy** Skonfiguruj podpisane zasady lub dodaj/usuń podpiszcę zasad, która jest podpisyana przy użyciu nieprawidłowego łańcucha certyfikatów (na przykład gdy rozszerzenie Podstawowe ograniczenia certyfikatu głównego nie jest ustawione na typ podmiotu = urząd certyfikacji)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Kroki rozwiązywania problemów** Certyfikat główny musi być oflagowany jako wystawiony przez urząd certyfikacji (podstawowe ograniczenia X.509), w innym przypadku nie będzie traktowany jako prawidłowy certyfikat. 

Upewnij się, że ustawiono rozszerzenie podstawowych ograniczeń certyfikatu głównego, aby wskazać, że typ podmiotu = urząd certyfikacji

W innym przypadku łańcuch certyfikatów jest uznawany za nieprawidłowy.

Zobacz [przykłady osób podpisanych](./policy-signer-examples.md) [zasad i](./policy-examples.md) zasad 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Niepowodzenie dodawania/usuwania podpisatora zasad

**Kod błędu** InvalidOperation

**Przykłady scenariuszy**

Gdy użytkownik przekaże usługę JWS bez oświadczenia "maa-policyCertificate"

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Gdy użytkownik nie przekaże certyfikatu w formacie JWS

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Kroki rozwiązywania problemów** Aby dodać/usunąć nowy certyfikat podpisywania zasad, użyj certyfikatu RFC7519 JSON Web Token (JWT) z oświadczeniem o nazwie "x-ms-policyCertificate". Wartość oświadczenia to RFC7517 JSON Web Key, która zawiera certyfikat do dodania. JWT musi być podpisany za pomocą klucza prywatnego dowolnego z prawidłowych certyfikatów podpisów zasad skojarzonych z dostawcą. Zobacz [przykłady podpisów zasad.](./policy-signer-examples.md)

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Niepowodzenie konfiguracji zasad zaestation

**Kod błędu** PolicyParsingError

**Przykłady scenariuszy** Zasady z niepoprawną składnią (na przykład brak średnika)/prawidłowe zasady JWT)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Kod błędu** InvalidOperation

**Przykłady scenariuszy** Podano nieprawidłową zawartość (na przykład zasady przekazywania/zasady niepodpisane, gdy jest wymagane podpisywanie zasad)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Kroki rozwiązywania problemów** Upewnij się, że zasady w formacie Tekst są zakodowane w formacie UTF-8.

Jeśli podpisywanie zasad jest wymagane, zasady zaświadczenia muszą być skonfigurowane tylko w formacie RFC7519 JSON Web Token (JWT). Jeśli podpisywanie zasad nie jest wymagane, można skonfigurować zasady w formacie tekstowym lub JWT.

Aby skonfigurować zasady w formacie JWT, należy użyć JWT z oświadczeniem o nazwie "AttestationPolicy". Wartością oświadczenia jest zakodowana w formacie Base64URL wersja tekstu zasad. Jeśli dostawca zaświadczeń jest skonfigurowany przy użyciu certyfikatów osoby podpiszcej zasady, JWT musi być podpisany za pomocą klucza prywatnego dowolnego z prawidłowych certyfikatów podpisów zasad skojarzonych z dostawcą. 

Aby skonfigurować zasady w formacie tekstowym, określ tekst zasad bezpośrednio.

W programie PowerShell określ wartość PolicyFormat jako JWT, aby skonfigurować zasady w formacie JWT. Domyślny format zasad to Tekst.

Zobacz przykłady zasad [zaświadczenia](./policy-examples.md) i [sposób tworzenia zasad zaświadczenia](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Problemy z instalacją az.attestation w programie PowerShell

Nie można zainstalować modułów Az lub Az.Attestation w programie PowerShell

### <a name="error"></a>Błąd

OSTRZEŻENIE: Nie można rozpoznać źródła pakietu " PackageManagement\Install-Package: Nie znaleziono dopasowania dla określonych https://www.powershellgallery.com/api/v2 kryteriów wyszukiwania i nazwy modułu

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Galeria programu PowerShell przestarzałe Transport Layer Security (TLS) w wersjach 1.0 i 1.1. 

Zalecana jest wersja TLS 1.2 lub nowsza. 

Aby kontynuować interakcję z Galeria programu PowerShell, uruchom następujące polecenie przed Install-Module poleceniami

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. Problemy z dostępem/konfiguracją zasad w programie PowerShell

Użytkownik przypisany z odpowiednimi rolami. Jednak w przypadku problemów z autoryzacją podczas zarządzania zasadami zaświadczenia za pomocą programu PowerShell.

### <a name="error"></a>Błąd
Klient z identyfikatorem obiektu o identyfikatorze obiektu nie ma autoryzacji do wykonania akcji &lt; &gt;  Microsoft.Authorization/roleassignments/write w zakresie "subc &lt; subscriptionId &gt; resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/role &lt; assignmentId" lub zakres jest &gt; nieprawidłowy. Jeśli niedawno udzielono dostępu, odśwież swoje poświadczenia

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Poniżej przedstawiono minimalną wersję modułów Az wymaganą do obsługi operacji zaświadczenia: 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Uruchom poniższe polecenie, aby sprawdzić zainstalowaną wersję wszystkich modułów Az 

```powershell
Get-InstalledModule 
```

Jeśli wersje nie są zgodne z minimalnym wymaganiem, uruchom Update-Module polecenia

np. — Update-Module -Name Az.Attestation
