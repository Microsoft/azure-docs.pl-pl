---
title: Informacje o wersji programu Key Vault .NET 2.x API| Dokumenty firmy Microsoft
description: Deweloperzy platformy .NET będą używać tego interfejsu API do kodowaniu usługi Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
editor: bryanla
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: mbaldwin
ms.openlocfilehash: 436b9c1569d7c33f79a126cd4d0513bac9385d8a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431919"
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Usługa Azure Key Vault .NET 2.0 — przewodnik po wersjach i przewodnikach po migracji
Poniższe informacje pomagają w migracji do wersji 2.0 biblioteki usługi Azure Key Vault dla języka C# i .NET.  Aplikacje napisane dla wcześniejszych wersji muszą być aktualizowane w celu obsługi najnowszej wersji.  Zmiany te są potrzebne do pełnej obsługi nowych i ulepszonych funkcji, takich jak **certyfikaty Usługi Key Vault.**

## <a name="key-vault-certificates"></a>Certyfikaty magazynu kluczy

Certyfikaty magazynu kluczy zarządzają certyfikatami x509 i obsługują następujące zachowania:  

* Tworzenie certyfikatów za pomocą procesu tworzenia magazynu kluczy lub importowanie istniejącego certyfikatu. Obejmuje to certyfikaty wygenerowane zarówno samodzielnie, jak i urzędu certyfikacji (CA).
* Bezpiecznie przechowuj i zarządzaj magazynem certyfikatów x509 bez interakcji przy użyciu materiałów klucza prywatnego.  
* Zdefiniuj zasady, które kierują magazyn kluczy do zarządzania cyklem życia certyfikatu.  
* Podaj informacje kontaktowe dotyczące zdarzeń cyklu życia, takich jak ostrzeżenia o wygaśnięciu i powiadomienia o odnowieniu.  
* Automatyczne odnawianie certyfikatów z wybranymi wystawcami (dostawcami certyfikatów X509 partnera usługi Key Vault i urzędami certyfikacji).* Certyfikat pomocy technicznej od alternatywnych (niebędących partnerami) zapewnia i urzędów certyfikacji (nie obsługuje automatycznego odnawiania).  

## <a name="net-support"></a>Obsługa platformy .NET

* **Program .NET 4.0** nie jest obsługiwany przez wersję 2.0 biblioteki .NET usługi Azure Key Vault
* **Program .NET Framework 4.5.2** jest obsługiwany przez wersję 2.0 biblioteki .NET usługi Azure Key Vault
* **Program .NET Standard 1.4** jest obsługiwany przez wersję 2.0 biblioteki .NET usługi Azure Key Vault

## <a name="namespaces"></a>Namespaces

* Obszar nazw **dla modeli** zostanie zmieniony z **Microsoft.Azure.KeyVault** na **Microsoft.Azure.KeyVault.Models**.
* Obszar nazw **Microsoft.Azure.KeyVault.Internal** zostanie usunięty.
* Następujące obszary nazw zależności sdku platformy Azure mają 

    - **Hyak.Common** jest teraz **Microsoft.Rest**.
    - **Hyak.Common.Internals** jest teraz **Microsoft.Rest.Serialization**.

## <a name="type-changes"></a>Zmiany typu

* *Klucz tajny* zmieniony na *SecretBundle*
* *Słownik* zmieniony na *IDictionary*
* *Lista\<T>, ciąg []* zmieniony na *\<IList T>*
* *NextList* zmieniono na *NextPageLink*

## <a name="return-types"></a>Typy zwrotów

* **KeyList** i **SecretList** teraz zwraca *IPage\<T>* zamiast *ListKeysResponseMessage*
* Wygenerowany **BackupKeyAsync** zwraca teraz *BackupKeyResult*, który zawiera *wartość* (kopia zapasowa obiektu blob). Wcześniej metoda została opakowana i zwróciła tylko wartość.

## <a name="exceptions"></a>Wyjątki

* *KeyVaultClientException* został zmieniony na *KeyVaultErrorException*
* Błąd usługi zmienił się z *wyjątku. Błąd* *wyjątku. Body.Error.Message*.
* Usunięto dodatkowe informacje z komunikatu o błędzie **[JsonExtensionData]**.

## <a name="constructors"></a>Konstruktorów

* Zamiast akceptować *Argument HttpClient* jako konstruktora, konstruktor akceptuje tylko *HttpClientHandler* lub *DelegatingHandler[]*.

## <a name="downloaded-packages"></a>Pobrane pakiety

Gdy klient przetwarza zależność usługi Key Vault, pobierane są następujące pakiety:

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

* **Usunięto klasę UnixEpoch.**
* **Nazwa klasy Base64UrlConverter** została zmieniona na **Base64UrlJsonConverter**.

## <a name="other-changes"></a>Inne zmiany

* Obsługa konfiguracji zasady ponawiania operacji KV w przypadku błędów przejściowych została dodana do tej wersji interfejsu API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Dla operacji, które *zwróciły przechowalnię,* typem zwracanym była klasa zawierająca właściwość **Vault.** Typ zwracany to teraz *Vault*.
* *PermissionsToKeys* i *PermissionsToSecrets* są teraz *Permissions.Keys* i *Permissions.Secrets*
* Niektóre zmiany typów zwracanych dotyczą również płaszczyzny kontrolnej.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* Pakiet jest podzielony na **microsoft.Azure.KeyVault.Extensions** i **Microsoft.Azure.KeyVault.Cryptography** dla operacji kryptograficznych.

