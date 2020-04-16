---
title: Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem
description: Scenariusze przedstawiające szereg opcji tworzenia, monitorowania i interakcji z procesem tworzenia certyfikatów za pomocą usługi Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 02e13ce81ed2f11c0bb69015a4864c4a1ad55593
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430970"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem
Dotyczy: Azure

Scenariusze / operacje opisane w tym artykule są następujące:

- Poproś o certyfikat KV z obsługiwanym wystawcą
- Pobierz oczekujące żądanie - stan żądania to "inProgress"
- Pobierz oczekujące żądanie - stan żądania jest "kompletny"
- Pobierz oczekujące żądanie - stan oczekującego żądania jest "anulowany" lub "nie powiodło się"
- Pobierz oczekujące żądanie - stan oczekującego żądania jest "usunięty" lub "zastąpiony"
- Utwórz (lub Importuj), gdy istnieje oczekujące żądanie - stan to "inProgress"
- Scalanie, gdy oczekujące żądanie jest tworzone z wystawcą (digicert, na przykład)
- Żądanie anulowania, gdy stan oczekującego żądania to "inProgress"
- Usuwanie oczekującego obiektu żądania
- Ręczne tworzenie certyfikatu KV
- Scalanie podczas tworzenia oczekującego żądania — ręczne tworzenie certyfikatów

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Poproś o certyfikat KV z obsługiwanym wystawcą 

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Poniższe przykłady wymagają, aby obiekt o nazwie "mydigicert" był już dostępny w magazynie kluczy u dostawcy wystawcy jako DigiCert. Wystawca certyfikatu jest jednostką reprezentowaną w usłudze Azure Key Vault (KV) jako zasób CertificateIssuer. Służy do dostarczania informacji o źródle certyfikatu KV; nazwa wystawcy, dostawca, poświadczenia i inne szczegóły administracyjne.

### <a name="request"></a>Żądanie

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>Odpowiedź

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Pobierz oczekujące żądanie - stan żądania to "inProgress"

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Pobierz`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Jeśli *request_id* jest określony w kwerendzie, działa jak filtr. Jeśli *request_id* w kwerendzie i w oczekującym obiekcie są różne, zwracany jest kod stanu http 404.

### <a name="response"></a>Odpowiedź

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>Pobierz oczekujące żądanie - stan żądania jest "kompletny"

### <a name="request"></a>Żądanie

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Pobierz`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Odpowiedź

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Pobierz oczekujące żądanie - stan oczekującego żądania jest "anulowany" lub "nie powiodło się"

### <a name="request"></a>Żądanie

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Pobierz`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Odpowiedź

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> Wartość kodu *błędu* może być "Błąd wystawcy certyfikatu" lub "Żądanie odrzucone" na podstawie odpowiednio przez wystawcę lub błąd użytkownika.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Pobierz oczekujące żądanie - stan oczekującego żądania jest "usunięty" lub "zastąpiony"
Oczekujący obiekt można usunąć lub nadpisać operacją tworzenia/importowania, gdy jego stan nie jest "inProgress".

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Pobierz`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Odpowiedź

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Utwórz (lub Importuj), gdy istnieje oczekujące żądanie - stan to "inProgress"
Obiekt oczekującowy ma cztery możliwe stany; "inprogress", "canceled", "failed" lub "completed".

Gdy stan oczekującego żądania jest "inprogress", tworzenie (i import) operacje zakończy się niepowodzeniem z kodem stanu http 409 (konflikt).

Aby rozwiązać konflikt:

- Jeśli certyfikat jest tworzony ręcznie, można ukończyć certyfikat KV, wykonując scalanie lub usuwanie na oczekującym obiekcie.

- Jeśli certyfikat jest tworzony z wystawcą, można poczekać, aż certyfikat zostanie ukończony, zakończy się niepowodzeniem lub zostanie anulowany. Alternatywnie można usunąć obiekt oczekujący.

> [!NOTE]
> Usunięcie oczekującego obiektu może, ale nie może anulować żądania certyfikatu x509 u dostawcy.

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Żądanie

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>Odpowiedź

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Scalanie podczas tworzenia oczekującego żądania z wystawcą
Scalanie nie jest dozwolone, gdy oczekujący obiekt jest tworzony z wystawcą, ale jest dozwolone, gdy jego stan jest "inProgress". 

Jeśli żądanie utworzenia certyfikatu x509 nie powiedzie się lub zostanie anulowane z jakiegoś powodu, a certyfikat x509 może zostać pobrany za pomocą środków pozapasmowych, można wykonać operację scalania w celu ukończenia certyfikatu KV.

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Żądanie

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Odpowiedź

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Żądanie anulowania, gdy stan oczekującego żądania to "inProgress"
Anulowanie rezerwacji można tylko złożyć. Żądanie może, ale nie musi zostać anulowane. Jeśli żądanie nie jest "inProgress", zwracany jest stan http 400 (Złe żądanie).

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|Patch|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Patch`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Patch`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Odpowiedź

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Usuwanie oczekującego obiektu żądania

> [!NOTE]
> Usunięcie oczekującego obiektu może, ale nie może anulować żądania certyfikatu x509 u dostawcy.

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Usunąć`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Usunąć`“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Odpowiedź

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>Ręczne tworzenie certyfikatu KV
Certyfikat wystawiony na wybrany urząd certyfikacji można utworzyć za pomocą procesu ręcznego tworzenia. Ustaw nazwę wystawcy na "Nieznany" lub nie precyzuj pola wystawcy.

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Żądanie

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>Odpowiedź

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Scalanie podczas tworzenia oczekującego żądania — ręczne tworzenie certyfikatów

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Żądanie

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Nazwa elementu|Wymagany|Typ|Wersja|Opis|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Tak|tablica|\<wprowadzenie> wersji|Łańcuch certyfikatów X509 jako podstawowa tablica 64-strunowa.|

### <a name="response"></a>Odpowiedź

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```
