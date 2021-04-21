---
title: Key Vault informacji o wersji interfejsu API platformy .NET 2.x| Microsoft Docs
description: Dowiedz się, jak aktualizować aplikacje napisane dla starszych wersji Azure Key Vault do pracy z wersją 2.0 biblioteki Azure Key Vault dla języka C# i .NET.
services: key-vault
author: msmbaldwin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: b923cc289dc0229f18eba144b09ceb34e0edea4e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751890"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure Key Vault .NET 2.0 — informacje o wersji i przewodnik migracji
Poniższe informacje pomagają w migracji do wersji 2.0 biblioteki Azure Key Vault dla języka C# i programu .NET.  Aplikacje napisane dla wcześniejszych wersji muszą być aktualizowane w celu obsługi najnowszej wersji.  Te zmiany są potrzebne do pełnej obsługi nowych i ulepszonych funkcji, takich **jak Key Vault certyfikatów.**

## <a name="key-vault-certificates"></a>Key Vault certyfikatów

Key Vault certyfikatów zarządzają certyfikatami x509 i obsługują następujące zachowania:  

* Tworzenie certyfikatów za pośrednictwem Key Vault lub importowanie istniejącego certyfikatu. Dotyczy to zarówno certyfikatów z podpisem własnym, jak i certyfikatów wygenerowanych przez urząd certyfikacji.
* Bezpieczne przechowywanie magazynu certyfikatów x509 i zarządzanie nimi bez interakcji przy użyciu materiału klucza prywatnego.  
* Zdefiniuj zasady, Key Vault zarządzanie cyklem życia certyfikatu.  
* Podaj informacje kontaktowe dotyczące zdarzeń cyklu życia, takie jak ostrzeżenia o wygaśnięciu i powiadomienia o odnowieniu.  
* Automatycznie odnawiaj certyfikaty przy użyciu wybranych wystawców (dostawców certyfikatów X509 i urzędów certyfikacji partnera Key Vault).* Certyfikat pomocy technicznej od innych dostawców (innych niż partner) zapewnia i urzędy certyfikacji (nie obsługuje automatycznego odnawiania).  

## <a name="net-support"></a>Obsługa .NET

* **Program .NET 4.0** nie jest obsługiwany przez wersję 2.0 biblioteki Azure Key Vault .NET
* **.NET Framework 4.5.2** jest obsługiwana przez wersję 2.0 biblioteki Azure Key Vault .NET
* **.NET Standard 1.4** jest obsługiwana przez wersję 2.0 biblioteki Azure Key Vault .NET

## <a name="namespaces"></a>Przestrzenie nazw

* Przestrzeń nazw dla **modeli zostanie** zmieniona z **Microsoft.Azure.KeyVault** na **Microsoft.Azure.KeyVault.Models.**
* Przestrzeń **nazw Microsoft.Azure.KeyVault.Internal** jest porzucana.
* Następujące przestrzenie nazw zależności zestawu Azure SDK mają następujące 

    - **Hyak.Common** to teraz **Microsoft.Rest.**
    - **Hyak.Common.Internals** to teraz **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Zmiany typu

* *Klucz tajny* został zmieniony na *SecretBundle*
* *Słownik został* zmieniony na *IDictionary*
* *Lista \<T> , ciąg []* został zmieniony na *IList \<T>*
* *NextList* zmieniono na  *NextPageLink*

## <a name="return-types"></a>Zwracane typy

* **Funkcje KeyList** **i SecretList** teraz zwraca *wartość \<T> IPage* zamiast *ListKeysResponseMessage*
* Wygenerowana wartość **BackupKeyAsync** zwraca teraz *wartość BackupKeyResult,* która zawiera *wartość* (obiekt blob kopii zapasowej). Wcześniej metoda była opakowana i zwracała tylko wartość .

## <a name="exceptions"></a>Wyjątki

* *Element KeyVaultClientException* został zmieniony na *KeyVaultErrorException*
* Błąd usługi zmienił się z *wyjątku. Błąd* *wyjątku. Body.Error.Message*.
* Usunięto dodatkowe informacje z komunikatu o błędzie **dla [JsonExtensionData]**.

## <a name="constructors"></a>Konstruktory

* Zamiast akceptować *HttpClient* jako argument konstruktora, konstruktor akceptuje tylko *HttpClientHandler* lub *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Pobrane pakiety

Gdy klient przetwarza Key Vault zależności, pobierane są następujące pakiety:

### <a name="previous-package-list"></a>Poprzednia lista pakietów

* `package id="Hyak.Common" version="1.0.2" targetFramework="net45"`
* `package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"`
* `package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"`
* `package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"`
* `package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"`
* `package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"`
* `package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"`

### <a name="current-package-list"></a>Bieżąca lista pakietów

* `package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"`
* `package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"`

## <a name="class-changes"></a>Zmiany klas

* **Klasa UnixEpoch** została usunięta.
* **Nazwa klasy Base64UrlConverter** została zmieniona na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Inne zmiany

* Do tej wersji interfejsu API dodano obsługę konfiguracji zasad ponawiania operacji KV w przypadku błędów przejściowych.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* W przypadku operacji, które zwróciły *magazyn*, zwracany typ to klasa, która zawiera właściwość **Vault.** Zwracany typ to teraz *Vault*.
* *PermissionsToKeys* i *PermissionsToSecrets* to teraz *Permissions.Keys* i *Permissions.Secrets*
* Niektóre zmiany zwracanych typów dotyczą również płaszczyzny sterowania.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Pakiet został rozbity na elementy **Microsoft.Azure.KeyVault.Extensions** i **Microsoft.Azure.KeyVault.Cryptography** na operacje kryptografii.

