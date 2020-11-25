---
title: Przewodnik rozwiązywania problemów z usługą Azure Attestation
description: Rozwiązywanie problemów z często obserwowanymi problemami
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: fe347983e2143c7a4bdf00ca0207356c881c66ac
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95891286"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Przewodnik rozwiązywania problemów z zaświadczeniem Microsoft Azure

Obsługa błędów w zaświadczeniu platformy Azure została zaimplementowana zgodnie z [zaleceniami interfejsu API REST firmy Microsoft](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses). Odpowiedź na błąd zwrócona przez interfejsy API zaświadczania platformy Azure zawiera kod stanu HTTP i pary nazwa/wartość z nazwami "Code" i "Message". Wartość "Code" jest czytelna dla człowieka i jest wskaźnikiem typu błędu. Wartość "Message" ma pomóc użytkownikowi i zawiera szczegóły błędu.

Jeśli problem nie został rozwiązany w tym artykule, możesz również przesłać żądanie pomocy technicznej platformy Azure na [stronie pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/).

Poniżej przedstawiono przykłady błędów zwracanych przez zaświadczanie platformy Azure:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: nieautoryzowany wyjątek

### <a name="http-status-code"></a>Kod stanu HTTP
401

**Kod błędu** Próby

**Przykłady scenariuszy**
  - Niepowodzenie zaświadczania, jeśli użytkownik nie jest przypisany do roli czytelnik zaświadczania
  - Nie można zarządzać zasadami zaświadczania, ponieważ użytkownik nie jest przypisany przy użyciu odpowiednich ról
  - Nie można zarządzać osobami podpisującymi zasady zaświadczania, ponieważ użytkownik nie jest przypisany przy użyciu odpowiednich ról

Użytkownik z rolą czytnika próbujących edytować zasady zaświadczania w programie PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Kroki rozwiązywania problemów**

Aby można było wyświetlić zasady zaświadczania/podpisywanie zasad, użytkownik usługi Azure AD wymaga uprawnień do działania:
- Microsoft. zaświadczanie/attestationProviders/zaświadczanie/odczytanie

  To uprawnienie można przypisać do użytkownika usługi AD za pomocą roli, takiej jak "właściciel" (uprawnienia symboli wieloznacznych) lub "czytelnik" (uprawnienia symboli wieloznacznych) lub "czytelnik zaświadczania" (określone uprawnienia tylko do zaświadczania platformy Azure).

Aby można było dodawać/usuwać osoby podpisujące zasady lub aby skonfigurować zasady, użytkownik usługi Azure AD musi mieć następujące uprawnienia dla "akcji":
- Microsoft. zaświadczanie/attestationProviders/zaświadczanie/zapis
- Microsoft. zaświadczanie/attestationProviders/zaświadczanie/usunięcie

  Te uprawnienia można przypisać do użytkownika usługi AD za pomocą roli, takiej jak "właściciel" (uprawnienia symboli wieloznacznych), "Współautor" (uprawnienia symboli wieloznacznych) lub "Współautor zaświadczania" (określone uprawnienia tylko do zaświadczania platformy Azure).

Klienci mogą wybrać użycie domyślnego dostawcy zaświadczania lub utworzyć własnych dostawców przy użyciu zasad niestandardowych. Aby wysyłać żądania zaświadczania do niestandardowych dostawców zaświadczania, dla użytkownika wymagana jest rola "właściciel" (uprawnienia symboli wieloznacznych) lub "czytelnik" (uprawnienia symboli wieloznacznych) lub "czytelnik zaświadczania". Dostawcy domyślnie są dostępni przez dowolnego użytkownika usługi Azure AD.

Aby sprawdzić role w programie PowerShell, uruchom następujące polecenie:

a. Uruchom program PowerShell i zaloguj się do platformy Azure za pomocą polecenia cmdlet "Connect-AzAccount"

b. Weryfikowanie ustawień przypisywania ról platformy Azure


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  Powinny zostać wyświetlone informacje podobne do następujących:

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

c. Jeśli na liście nie znajduje się odpowiednie przypisanie roli, postępuj zgodnie z instrukcjami w [tym miejscu](/azure/role-based-access-control/role-assignments-powershell)

## <a name="2-http--400-errors"></a>2. HTTP – błędy 400

### <a name="http-status-code"></a>Kod stanu HTTP
400

Istnieją różne przyczyny, dla których żądanie może zwrócić 400. Poniżej przedstawiono kilka przykładów błędów zwracanych przez interfejsy API zaświadczania platformy Azure:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Niepowodzenie zaświadczania z powodu błędów oceny zasad

Zasady zaświadczania obejmują reguły autoryzacji i reguły wystawiania. Enklawy dowody są oceniane na podstawie reguł autoryzacji. Reguły wystawiania definiują oświadczenia, które mają być zawarte w tokenie zaświadczania. Jeśli oświadczenia w dowodu enklawy są niezgodne z regułami autoryzacji, zaświadczanie wywołań zwróci błąd oceny zasad. 

**Kod błędu** PolicyEvaluationError

**Przykłady scenariuszy** Gdy oświadczenia w ofercie enklawy są niezgodne z regułami autoryzacji zasad zaświadczania

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Kroki rozwiązywania problemów** Użytkownicy mogą oszacować enklawy przed zasadami zaświadczania o SGX przed rozpoczęciem konfigurowania tego samego.

Wyślij żądanie zaświadczania interfejsu API, dostarczając tekst zasad w parametrze "draftPolicyForAttestation". Interfejs API AttestSgxEnclave użyje tego dokumentu zasad podczas wywołania zaświadczania i może służyć do testowania zasad zaświadczania przed ich użyciem. Token zaświadczania wygenerowany, gdy to pole jest obecne, będzie niezabezpieczony.

Zobacz [przykłady zasad zaświadczania](/azure/attestation/policy-examples)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Niepowodzenie zaświadczania z powodu nieprawidłowych danych wejściowych

**Kod błędu** Nieprawidłowy parametr

**Przykłady scenariuszy** Niepowodzenie zaświadczania SGX z powodu nieprawidłowych danych wejściowych. Poniżej przedstawiono przykłady dla komunikatów o błędach:
- Określony cytat jest nieprawidłowy z powodu błędu w zabezpieczeniach oferty 
- Określony cytat jest nieprawidłowy, ponieważ urządzenie, na którym wygenerowało ofertę, nie spełnia wymagań dotyczących planu bazowego platformy Azure
- Określony cytat jest nieprawidłowy, ponieważ TCBInfo lub QEID dostarczony przez Cache Service PCK jest nieprawidłowy

**Kroki rozwiązywania problemów**

Zaświadczanie Microsoft Azure obsługuje zaświadczanie ofert SGX generowanych przez zestaw SDK firmy Intel i Open enklawy SDK.

Zapoznaj się z [przykładami kodu](/samples/browse/?expanded=azure&terms=attestation) dotyczącymi wykonywania zaświadczania przy użyciu zestawu SDK open enklawy/zestawu Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Nieprawidłowy błąd łańcucha certyfikatów podczas przekazywania osoby podpisującej zasady/zasady

**Kod błędu** Nieprawidłowy parametr

**Przykłady scenariuszy** Skonfiguruj podpisane zasady lub Dodaj/Usuń podpisywanie zasad, które są podpisane przy użyciu nieprawidłowego łańcucha certyfikatów (na przykład gdy podstawowe warunki ograniczające certyfikatu głównego nie są ustawione na typ podmiotu = CA)

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Kroki rozwiązywania problemów** Certyfikat główny musi być oflagowany jako wystawiony przez urząd certyfikacji (ograniczenia podstawowe X. 509), w przeciwnym razie nie będzie traktowany jako prawidłowy certyfikat. 

Upewnij się, że rozszerzenie podstawowych warunków ograniczających certyfikatu głównego jest ustawione na wskazuje, że typ podmiotu = CA

W przeciwnym razie łańcuch certyfikatów jest uznawany za nieprawidłowy.

Zobacz przykłady dotyczące [podpisywania zasad](/azure/attestation/policy-signer-examples) i [zasad](/azure/attestation/policy-examples) 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Błąd dodawania/usuwania osoby podpisującej zasady

**Kod błędu** InvalidOperation

**Przykłady scenariuszy**

Gdy użytkownik przekazuje JWS bez żądania "Maa-policyCertificate"

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

Jeśli użytkownik nie przekaże certyfikatu w formacie JWS

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

**Kroki rozwiązywania problemów** Aby dodać/usunąć nowy certyfikat osoby podpisującej zasady, użyj tokenu sieci Web JSON RFC7519 (JWT) z zastrzeżeniem o nazwie "x-MS-policyCertificate". Wartość tego żądania jest kluczem sieci Web JSON RFC7517, który zawiera certyfikat do dodania. Token JWT musi być podpisany za pomocą klucza prywatnego dowolnego z prawidłowych certyfikatów podpisywania zasad skojarzonych z dostawcą. Zobacz [przykłady dotyczące podpisywania zasad](/azure/attestation/policy-signer-examples).

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Niepowodzenie konfiguracji zasad zaświadczania

**Kod błędu** PolicyParsingError

**Przykłady scenariuszy** Podano zasady z nieprawidłową składnią (na przykład brak średnika)/Valid zasady JWT)

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

**Przykłady scenariuszy** Podano nieprawidłową zawartość (na przykład przekazywanie zasad/niepodpisanych zasad, gdy jest wymagane podpisywanie zasad)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Kroki rozwiązywania problemów** Upewnij się, że zasady w formacie tekstowym są zakodowane przy użyciu kodowania UTF-8.

Jeśli podpisywanie zasad jest wymagane, zasady zaświadczania muszą być skonfigurowane tylko w formacie tokenu RFC7519 JSON (JWT). Jeśli podpisywanie zasad nie jest wymagane, zasady można skonfigurować w formacie tekstu lub tokenu JWT.

Aby skonfigurować zasady w formacie JWT, użyj tokenu JWT z zastrzeżeniem o nazwie "AttestationPolicy". Wartość tego żądania jest Base64URL zaszyfrowanej wersji tekstu zasad. Jeśli dostawca zaświadczania jest skonfigurowany przy użyciu certyfikatów podpisywania zasad, token JWT musi być podpisany przy użyciu klucza prywatnego dowolnego z prawidłowych certyfikatów podpisywania zasad skojarzonych z dostawcą. 

Aby skonfigurować zasady w formacie tekstowym, należy bezpośrednio określić tekst zasad.

W programie PowerShell Określ PolicyFormat jako JWT, aby skonfigurować zasady w formacie JWT. Domyślny format zasad to Text.

Zobacz [przykłady zasad](/azure/attestation/policy-examples) zaświadczania i [sposób tworzenia zasad zaświadczania](/azure/attestation/author-sign-policy) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. AZ. zaświadczanie problemów z instalacją w programie PowerShell

Nie można zainstalować poleceń AZ lub AZ. zaświadczania w programie PowerShell

### <a name="error"></a>Błąd

Ostrzeżenie: nie można rozpoznać źródła pakietu " https://www.powershellgallery.com/api/v2 " PackageManagement\Install-Package: nie znaleziono dopasowania dla określonych kryteriów wyszukiwania i nazwy modułu

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Galeria programu PowerShell ma przestarzałe wersje Transport Layer Security (TLS) 1,0 i 1,1. 

Zalecane jest szyfrowanie TLS 1,2 lub nowsza. 

Aby nadal korzystać z Galeria programu PowerShell, uruchom następujące polecenie przed poleceniami Install-Module

**[Net. ServicePointManager]:: to elementu SecurityProtocol = [net. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. problemy z dostępem/konfiguracją zasad w programie PowerShell

Użytkownik przypisany do odpowiednich ról. Jednak podczas zarządzania zasadami zaświadczania przy użyciu programu PowerShell występują problemy z autoryzacją.

### <a name="error"></a>Błąd
Klient z identyfikatorem obiektu nie <object Id>  ma autoryzacji do wykonania akcji Microsoft. Authorization/RoleAssignments/Write w zakresie "kupione/ <subscriptionId> resourcegroups/secure_enclave_poc/Providers/Microsoft.Authorization/RoleAssignments/ <role assignmentId> " lub zakres jest nieprawidłowy. Jeśli dostęp został niedawno udzielony, Odśwież swoje poświadczenia

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Poniżej wymieniono minimalną wersję AZ modułów wymaganą do obsługi operacji zaświadczania: 

 **AZ 4.5.0** 
 
 **AZ. Accounts 1.9.2**
 
 **AZ. zaświadczanie 0.1.8** 

Uruchom poniższe polecenie, aby sprawdzić zainstalowaną wersję wszystkich poleceń AZ module 

```powershell
Get-InstalledModule 
```

Jeśli wersje nie są zgodne z wymaganiem minimalnym, uruchom polecenia Update-Module

np.-Update-Module-Name AZ. zaświadczanie

