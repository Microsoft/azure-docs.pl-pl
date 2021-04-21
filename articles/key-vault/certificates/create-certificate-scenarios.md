---
title: Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem
description: Scenariusze pokazujące szereg opcji tworzenia, monitorowania i interakcji z procesem tworzenia certyfikatu za pomocą Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8e4acb5195497dd31f466829b1cde301ba9696b3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751098"
---
# <a name="monitor-and-manage-certificate-creation"></a>Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem
Dotyczy: Azure

Scenariusze/operacje opisane w tym artykule są następujące:

- Żądanie certyfikatu KV z obsługiwanym wystawcą
- Pobierz oczekujące żądanie — stan żądania to "inProgress"
- Pobierz oczekujące żądanie — żądanie ma stan "ukończono"
- Pobierz oczekujące żądanie — stan oczekującego żądania to "anulowane" lub "niepowodzenie"
- Pobierz oczekujące żądanie — stan oczekującego żądania to "usunięty" lub "nadpisany"
- Utwórz (lub zaimportuj), gdy istnieje oczekujące żądanie — stan to "inProgress"
- Scalanie w przypadku utworzenia oczekującego żądania z wystawcą (na przykład DigiCert)
- Żądanie anulowania, gdy stan oczekującego żądania to "inProgress"
- Usuwanie obiektu oczekującego żądania
- Ręczne tworzenie certyfikatu KV
- Scalanie po utworzeniu oczekującego żądania — ręczne tworzenie certyfikatu

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Żądanie certyfikatu KV z obsługiwanym wystawcą 

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Poniższe przykłady wymagają, aby obiekt o nazwie "mydigicert" był już dostępny w magazynie kluczy z dostawcą wystawcy digiCert. Wystawca certyfikatu jest jednostką reprezentowaną w Azure Key Vault (KV) jako zasób CertificateIssuer. Służy do podania informacji o źródle certyfikatu KV; nazwa wystawcy, dostawca, poświadczenia i inne szczegóły administracyjne.

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

## <a name="get-pending-request---request-status-is-inprogress"></a>Pobierz oczekujące żądanie — stan żądania to "inProgress"

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Pobierz `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Jeśli *request_id* w zapytaniu, działa jak filtr. Jeśli *request_id* w zapytaniu i obiekcie oczekującym są różne, zwracany jest kod stanu HTTP 404.

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

## <a name="get-pending-request---request-status-is-complete"></a>Pobierz oczekujące żądanie — żądanie ma stan "ukończono"

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

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Pobierz oczekujące żądanie — stan oczekującego żądania to "anulowane" lub "niepowodzenie"

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
> Kod błędu może *mieć* wartość "Błąd wystawcy certyfikatu" lub "Żądanie zostało odrzucone" odpowiednio na podstawie błędu wystawcy lub użytkownika.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Pobierz oczekujące żądanie — stan oczekującego żądania to "usunięte" lub "nadpisane"
Oczekujący obiekt może zostać usunięty lub zastąpiony przez operację tworzenia/importowania, gdy jego stan nie jest "inProgress".

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

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Utwórz (lub zaimportuj), gdy istnieje oczekujące żądanie — stan to "inProgress"
Obiekt oczekujący ma cztery możliwe stany: "inprogress", "canceled", "failed" lub "completed".

Jeśli stan oczekującego żądania to "inprogress", operacje tworzenia (i importowania) nie powiodą się z kodem stanu HTTP 409 (konflikt).

Aby rozwiązać konflikt:

- Jeśli certyfikat jest tworzony ręcznie, możesz ukończyć certyfikat KV, scaliąc lub usunąć oczekujący obiekt.

- Jeśli certyfikat jest tworzony przy użyciu wystawcy, możesz poczekać na jego zakończenie, niepowodzenie lub anulowanie. Alternatywnie możesz usunąć oczekujący obiekt.

> [!NOTE]
> Usunięcie oczekującego obiektu może lub nie może anulować żądania certyfikatu x509 u dostawcy.

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

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Scalanie w przypadku utworzenia oczekującego żądania z wystawcą
Scalanie nie jest dozwolone, gdy oczekujący obiekt został utworzony przy użyciu wystawcy, ale jest dozwolony, gdy jego stan to "inProgress". 

Jeśli żądanie utworzenia certyfikatu x509 nie powiedzie się lub z jakiegoś powodu zostanie anulowane, a certyfikat x509 może zostać pobrany poza pasmem, można wykonać operację scalania w celu ukończenia certyfikatu KV.

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

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Żądanie anulowania, gdy stan oczekującego żądania to "inProgress"
Można zażądać tylko anulowania. Żądanie może zostać anulowane lub nie może zostać anulowane. Jeśli żądanie nie ma stanu "inProgress", zwracany jest stan HTTP 400 (Złe żądanie).

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|Patch|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Patch `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Patch `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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

## <a name="delete-a-pending-request-object"></a>Usuwanie obiektu oczekującego żądania

> [!NOTE]
> Usunięcie oczekującego obiektu może lub nie może anulować żądania certyfikatu x509 u dostawcy.

|Metoda|Identyfikator URI żądania|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Żądanie
Usunąć `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

LUB

Usunąć `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

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
Certyfikat wystawiony przez urząd certyfikacji można utworzyć za pomocą procesu tworzenia ręcznego. Ustaw nazwę wystawcy na "Nieznany" lub nie określaj pola wystawcy.

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

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Scalanie po utworzeniu oczekującego żądania — ręczne tworzenie certyfikatu

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
|x5c|Tak|array|\<introducing version>|Łańcuch certyfikatów X509 jako podstawowa tablica ciągów 64.|

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
