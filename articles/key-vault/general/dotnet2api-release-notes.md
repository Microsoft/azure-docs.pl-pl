---
title: Informacje o wersji interfejsu API platformy .NET 2. x Key Vault | Microsoft Docs
description: Dowiedz się, jak aktualizować aplikacje przeznaczone dla wcześniejszych wersji Azure Key Vault do pracy z wersją 2,0 biblioteki Azure Key Vault dla języków C# i .NET.
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 018570019b306dced76760fefa4441ee7d86ad2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013959"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2,0 — informacje o wersji i przewodnik migracji
Poniższe informacje ułatwiają migrowanie do wersji 2,0 biblioteki Azure Key Vault dla języków C# i .NET.  Aplikacje przeznaczone dla wcześniejszych wersji muszą zostać zaktualizowane w celu obsługi najnowszej wersji.  Te zmiany są konieczne, aby w pełni obsługiwać nowe i udoskonalone funkcje, takie jak **Key Vault Certificates**.

## <a name="key-vault-certificates"></a>Key Vault certyfikaty

Key Vault certyfikaty zarządzają certyfikatami x509 i obsługują następujące zachowania:  

* Utwórz certyfikaty za pomocą procesu tworzenia Key Vault lub zaimportuj istniejący certyfikat. Dotyczy to zarówno certyfikatów z podpisem własnym, jak i urzędu certyfikacji (CA).
* Bezpiecznie przechowuj magazyn certyfikatów x509 i Zarządzaj nim bez interakcji z użyciem materiału klucza prywatnego.  
* Zdefiniuj zasady, które umożliwiają bezpośrednie Key Vault zarządzania cyklem życia certyfikatu.  
* Podaj informacje kontaktowe dla zdarzeń cyklu życia, takie jak ostrzeżenia o wygasaniu i powiadomienia o odnowieniu.  
* Automatycznie Odnów certyfikaty z wybranymi wystawcami (dostawcy certyfikatów x509 partnera Key Vault i urzędów certyfikacji). * certyfikat pomocy technicznej od alternatywnego (niepartnerskiego) zapewnia i urzędy certyfikacji (nie obsługuje automatycznego odnawiania).  

## <a name="net-support"></a>Obsługa platformy .NET

* Program **.net 4,0** nie jest obsługiwany przez 2,0 wersję biblioteki Azure Key Vault .NET
* **.NET Framework 4.5.2** jest obsługiwany przez wersję 2,0 biblioteki Azure Key Vault .NET
* **.NET Standard 1,4** jest obsługiwana w wersji 2,0 biblioteki Azure Key Vault .NET

## <a name="namespaces"></a>Przestrzenie nazw

* Przestrzeń nazw dla **modeli** została zmieniona z **Microsoft. Azure. z magazynu** kluczy na **Microsoft. Azure. kluczy. models**.
* **Magazyn Microsoft. Azure. kluczy. wewnętrzna** przestrzeń nazw została porzucona.
* Następujące przestrzenie nazw zależności zestawu Azure SDK mają 

    - **Hyak. Common** to teraz **Microsoft. REST**.
    - **Hyak. Common. Internals** to teraz **Microsoft. Rest. Serialization**.

## <a name="type-changes"></a>Zmiany typu

* *Wpis tajny* został zmieniony na *SecretBundle*
* *Słownik* został zmieniony na *IDictionary*
* *List \<T> , String []* zmieniono na *IList \<T>*
* *NextList* zmieniony na  *NextPageLink*

## <a name="return-types"></a>Typy zwracane

* **Lista** i **SecretList** teraz zwracają *iPage \<T>* zamiast *ListKeysResponseMessage*
* Wygenerowany **BackupKeyAsync** teraz zwraca *BackupKeyResult*, który zawiera *wartość* (obiekt BLOB tworzenia kopii zapasowej). Wcześniej Metoda została opakowana i zwróciła tylko wartość.

## <a name="exceptions"></a>Wyjątki

* *KeyVaultClientException* został zmieniony na *KeyVaultErrorException*
* Błąd usługi został zmieniony z *wyjątku. Błąd* do *wyjątku. Treść. Error. Message*.
* Usunięto dodatkowe informacje z komunikatu o błędzie dla **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktory

* Zamiast zaakceptowania *HttpClient* jako argumentu konstruktora, Konstruktor akceptuje tylko *HttpClientHandler* lub *DelegatingHandler []*.

## <a name="downloaded-packages"></a>Pobrane pakiety

Gdy Klient przetwarza zależność Key Vault, pobierane są następujące pakiety:

### <a name="previous-package-list"></a>Lista poprzednich pakietów

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Lista bieżących pakietów

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Zmiany klas

* Klasa **UnixEpoch** została usunięta.
* Nazwa klasy **Base64UrlConverter** jest zmieniona na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Inne zmiany

* Do tej wersji interfejsu API dodano obsługę konfiguracji zasad ponawiania operacji w przypadku błędów przejściowych.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Pakiet NuGet Microsoft. Azure. Management.

* Dla operacji, które zwróciły *Magazyn*, zwracany typ to Klasa, która zawiera właściwość **magazynu** . Typem zwracanym jest teraz *Magazyn*.
* *PermissionsToKeys* i *PermissionsToSecrets* są teraz *uprawnieniami. klucze* i *uprawnienia. wpisy tajne*
* Pewne zmiany typów zwracanych dotyczą również płaszczyzny kontrolnej.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft. Azure. z magazynem kluczy. rozszerzenia NuGet

* Pakiet jest podzielony na **platformę Microsoft. Azure. kluczy. Extensions** i **Microsoft. Azure. The. Cryptography** dla operacji kryptograficznych.

