---
title: Protokół zabezpieczeń oparty na wirtualizacji (VBS) dla zaświadczania platformy Azure
description: Protokół zaświadczania VBS
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507917"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>Trusted Platform Module (TPM) i zabezpieczenia oparte na wirtualizacji (VBS) enklawy — protokół zaświadczania 

Microsoft Azure zaświadczania, aby zapewnić silną gwarancję bezpieczeństwa, polega na sprawdzeniu, czy łańcuch zaufania jest utrzymywany z poziomu głównego zaufania (TPM) do uruchamiania funkcji hypervisor i bezpiecznego jądra. Aby osiągnąć to zaświadczenie platformy Azure, należy zaświadczać o stanie rozruchu maszyny, zanim będziemy mogli ustanowić zaufanie w bezpiecznej enklawy. System operacyjny, funkcja hypervisor i bezpieczne pliki binarne jądra muszą być podpisane przez odpowiednie oficjalne urzędy firmy Microsoft i skonfigurowane w bezpieczny sposób. Po powiązaniu zaufania między Trusted Platform Module (TPM) i kondycją funkcji hypervisor firma Microsoft może ufać zabezpieczeniom opartym na wirtualizacji (VBS) enklawy IDKs dostarczonym w mierzonym dzienniku rozruchu, dzięki czemu możemy sprawdzić, czy para kluczy została wygenerowana przez enklawy i mennic raport zaświadczania, który wiąże zaufanie w tym kluczu i zawiera inne oświadczenia, takie jak właściwości poziomu zabezpieczeń i zaświadczania rozruchu. 

VBS enclaves wymaga, aby moduł TPM zapewniał pomiar do sprawdzania poprawności podstawy zabezpieczeń. VBS enclaves są zaświadczone przez punkt końcowy modułu TPM z dodaniem do obiektu żądania w protokole. 

## <a name="protocol-messages"></a>Komunikaty protokołu

### <a name="init-message"></a>Komunikat init

#### <a name="direction"></a>Kierunek

Zaświadczanie klienta > platformy Azure

#### <a name="payload"></a>Ładunku

```
{ 
  "type": "aikcert" 
} 
```

"Type" (ciąg ASCII): reprezentuje typ żądanego zaświadczania. Obecnie obsługiwana jest tylko wartość "aikcert".

### <a name="challenge-message"></a>Komunikat wyzwania

#### <a name="direction"></a>Kierunek

Zaświadczanie platformy Azure — klient >

#### <a name="payload"></a>Ładunku

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**Challenge** (BASE64URL (octets)): wartość losowa wystawiona przez usługę.

**service_context** (BASE64URL (octets)): kontekst nieprzezroczysty utworzony przez usługę.


### <a name="request-message"></a>Komunikat żądania

#### <a name="direction"></a>Kierunek

Zaświadczanie klienta > platformy Azure 

#### <a name="payload"></a>Ładunku

```
{
  "request": "<JWS>"
}
```

**żądanie** (JWS): żądanie składa się ze struktury podpisu internetowego JSON (JWS). Poniżej przedstawiono JWS chroniony nagłówek i ładunek JWS. Podobnie jak w przypadku dowolnej struktury JWS, końcowa wartość składa się z:

BASE64URL (UTF8 (JWS Protected header) | | '.' ||

BASE64URL (ładunek JWS) | | '.' ||

BASE64URL (JWS Signature)

##### <a name="jws-protected-header"></a>JWS chroniony nagłówek

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>Ładunek JWS

Ładunek JWS może być typu Basic lub VBS. Podstawowa jest używana, gdy dowód zaświadczania nie obejmuje danych VBS. 

Tylko przykład TPM: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

Przykład enklawy TPM + VBS: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (StringOrURI): identyfikator jednostki uzależnionej. Używane przez usługę w obliczaniu żądania identyfikatora maszyny

**rp_data** (BASE64URL (octets)): dane nieprzezroczyste przesłane przez jednostkę uzależnioną. Jest to zwykle używane przez jednostkę uzależnioną jako identyfikator jednorazowy w celu zagwarantowania Aktualności raportu

**Challenge** (BASE64URL (octets)): wartość losowa wystawiona przez usługę

**tpm_att_data**: dane zaświadczania dotyczące modułu TPM

- **srtm_boot_log (BASE64URL (octets))**: SRTM dzienników rozruchowych pobieranych przez funkcję Tbsi_Get_TCG_Log_Ex z typem dziennika = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (octets))**: SRTM wznawia dziennik jako pobrany przez funkcję Tbsi_Get_TCG_Log_Ex z typem dziennika = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (octets))**: drtm dzienników rozruchowych pobieranych przez funkcję Tbsi_Get_TCG_Log_Ex z typem dziennika = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (octets))**: drtm wznawia dziennik jako pobrany przez funkcję Tbsi_Get_TCG_Log_Ex z typem dziennika = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (octets))**: certyfikat X. 509 dla zestawu AIK, zwracany przez funkcję NCryptGetProperty z właściwością = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: publiczna część zestawu AIK reprezentowana jako obiekt klucza internetowego JSON (JWK) (RFC 7517)

- **current_claim (BASE64URL (octets))**: w ramach żądania zaświadczania dla bieżącego stanu PCR, zwrócone przez funkcję NCryptCreateClaim z dwClaimType = NCRYPT_CLAIM_PLATFORM i NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK ustawionym na uwzględnienie wszystkich PCR. Wezwanie wysyłane przez usługę należy również użyć w obliczeniach tego żądania

- **boot_claim (BASE64URL (octets))**: poświadczenie zaświadczania dla stanu PCR na rozruchu, jak zostało zwrócone przez funkcję NCryptCreateClaim z dwClaimType = NCRYPT_CLAIM_PLATFORM i NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK ustawionym do uwzględnienia wszystkich PCR

**vsm_report**   (BASE64URL (OCTETs)): Raport zaświadczania VBS enklawy zwracany przez funkcję EnclaveGetAttestationReport. Parametr EnclaveData musi być skrótem SHA-512 wartości report_signed (włącznie z otwierającym i zamykającym nawiasem klamrowym). Dane wejściowe funkcji mieszania są UTF8 (report_signed)

**attest_key**: publiczna część klucza enklawy reprezentowana jako obiekt klucza internetowego JSON (JWK) (RFC 7517)

**custom_claims**: tablica niestandardowych oświadczeń enklawy wysłanych do usługi, które mogą być oceniane przez zasady. Zgłoszenie

- **Nazwa (ciąg)**: Nazwa żądania. Ta nazwa zostanie dołączona do adresu URL określonego przez usługę zaświadczania (aby uniknąć konfliktów), a ciąg połączony jest typem żądania, które może być używane w zasadach

- **wartość (String)**: wartość żądania

- **value_type (String)**: typ danych wartości odszkodowania

**service_context** (BASE64URL (octets)): kontekst nieprzezroczysty utworzony przez usługę.

### <a name="report-message"></a>Komunikat raportu

#### <a name="direction"></a>Kierunek

Zaświadczanie platformy Azure — klient >

#### <a name="payload"></a>Ładunku

```
{
  "report": "<JWT>"
}
```

**raport** (JWT): Raport zaświadczania w formacie tokenu sieci Web JSON (JWT) (RFC 7519).

## <a name="next-steps"></a>Następne kroki

- [Przepływ pracy zaświadczania platformy Azure](workflow.md)
