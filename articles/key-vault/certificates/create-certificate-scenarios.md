---
title: Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem
description: Scenariusze ukazujące szereg opcji tworzenia, monitorowania i współpracy z procesem tworzenia certyfikatów przy użyciu Key Vault.
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81430970"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem
Dotyczy: Azure

Scenariusze/operacje opisane w tym artykule są następujące:

- Zażądaj certyfikatu KV z obsługiwanego wystawcy
- Pobieranie oczekujących żądań — stan żądania to "w toku"
- Pobieranie oczekujących żądań — stan żądania to "ukończone"
- Pobieranie oczekujących żądań oczekujących na żądanie ma stan "anulowane" lub "Niepowodzenie"
- Pobieranie oczekujących żądań oczekujących na żądanie jest "usunięte" lub "nadpisane"
- Utwórz (lub zaimportuj), gdy istnieje oczekujące żądanie — stan to "w toku"
- Scalanie po utworzeniu żądania oczekującego za pomocą wystawcy (na przykład DigiCert)
- Żądaj anulowania, gdy oczekujące żądanie ma stan "w toku"
- Usuwanie oczekującego obiektu żądania
- Ręczne tworzenie certyfikatu KV
- Scalanie po utworzeniu oczekującego żądania — ręczne tworzenie certyfikatów

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Zażądaj certyfikatu KV z obsługiwanego wystawcy 

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Poniższe przykłady wymagają, aby obiekt o nazwie "mydigicert" był już dostępny w magazynie kluczy z dostawcą wystawcy jako DigiCert. Wystawca certyfikatu jest jednostką reprezentowaną w Azure Key Vault (KV) jako zasób CertificateIssuer. Służy do przekazywania informacji o źródle certyfikatu KV; Nazwa wystawcy, dostawca, poświadczenia i inne szczegóły administracyjne.

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

### <a name="response"></a>Reakcja

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Pobieranie oczekujących żądań — stan żądania to "w toku"

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Jeśli *request_id* jest określona w zapytaniu, działa jak filtr. Jeśli *request_id* w zapytaniu i w obiekcie oczekiwania są inne, zwracany jest kod stanu HTTP 404.

### <a name="response"></a>Reakcja

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

## <a name="get-pending-request---request-status-is-complete"></a>Pobieranie oczekujących żądań — stan żądania to "ukończone"

### <a name="request"></a>Żądanie

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Reakcja

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Pobieranie oczekujących żądań oczekujących na żądanie ma stan "anulowane" lub "Niepowodzenie"

### <a name="request"></a>Żądanie

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Reakcja

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
> Wartość *ErrorCode* może być "błędem wystawcy certyfikatu" lub "żądanie odrzucone" na podstawie odpowiednio wystawcy lub błędu użytkownika.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Pobieranie oczekujących żądań oczekujących na żądanie jest "usunięte" lub "nadpisane"
Obiekt oczekujący może zostać usunięty lub zastąpiony przez operację tworzenia/importowania, gdy jego stanem nie jest "w toku".

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Reakcja

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Utwórz (lub zaimportuj), gdy istnieje oczekujące żądanie — stan to "w toku"
Obiekt oczekujący ma cztery możliwe Stany; "InProgress", "anulowane", "zakończone niepowodzeniem" lub "ukończone".

Gdy stan oczekującego żądania to "w trakcie", operacje tworzenia (i importowania) zakończą się niepowodzeniem z kodem stanu HTTP 409 (konflikt).

Aby rozwiązać konflikt:

- Jeśli certyfikat jest tworzony ręcznie, można wykonać operację scalania lub usuwania w obiekcie oczekującym.

- Jeśli certyfikat jest tworzony za pomocą wystawcy, możesz poczekać, aż certyfikat zakończy się niepowodzeniem lub zostanie anulowany. Alternatywnie możesz usunąć obiekt oczekujący.

> [!NOTE]
> Usunięcie obiektu oczekującego może lub nie anuluje żądania certyfikatu x509 z dostawcą.

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

### <a name="response"></a>Reakcja

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Scal po utworzeniu żądania oczekującego za pomocą wystawcy
Scalanie jest niedozwolone, gdy obiekt oczekujący jest tworzony za pomocą wystawcy, ale jest dozwolony, gdy jego stan to "w toku". 

Jeśli żądanie utworzenia certyfikatu x509 zakończy się niepowodzeniem lub zostanie anulowane z jakiegoś powodu, a certyfikat x509 można pobrać za pośrednictwem środków poza pasmem, można wykonać operację scalania w celu ukończenia certyfikatu KV.

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Żądanie

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>Reakcja

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Żądaj anulowania, gdy oczekujące żądanie ma stan "w toku"
Można żądać anulowania tylko. Żądanie może lub nie może być anulowane. Jeśli żądanie nie jest "w toku", zwracany jest stan HTTP 400 (Nieprawidłowe żądanie).

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|WYSŁANA|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
WYSŁANA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

WYSŁANA `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>Reakcja

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
> Usunięcie oczekującego obiektu może być niemożliwe lub nie anuluje żądania certyfikatu x509 z dostawcą.

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
USUNIĘTY `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

USUNIĘTY `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>Reakcja

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
W procesie tworzenia ręcznego można utworzyć certyfikat wystawiony z wybranym przez urząd certyfikacji. Ustaw nazwę wystawcy na "nieznany" lub nie określaj pola wystawcy.

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

### <a name="response"></a>Reakcja

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Scalanie po utworzeniu oczekującego żądania — ręczne tworzenie certyfikatów

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
|x5c|Tak|array|\<introducing version>|Łańcuch certyfikatu x509 jako podstawową tablicę ciągów 64.|

### <a name="response"></a>Reakcja

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
