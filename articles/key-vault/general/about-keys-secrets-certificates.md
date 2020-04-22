---
title: Informacje o kluczach, wpisach tajnych i certyfikatach usługi Azure Key Vault — usługa Azure Key Vault
description: Omówienie interfejsu REST usługi Azure Key Vault i szczegółów dewelopera dla kluczy, wpisów tajnych i certyfikatów.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 241efab246dc903981da570a4191f93cc744bca7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726445"
---
# <a name="about-keys-secrets-and-certificates"></a>Klucze, wpisy tajne i certyfikaty — informacje

Usługa Azure Key Vault umożliwia aplikacjom i użytkownikom platformy Microsoft Azure przechowywanie i używanie kilku typów danych tajnych/kluczowych:

- Klucze kryptograficzne: Obsługuje wiele typów kluczy i algorytmów i umożliwia korzystanie z sprzętowych modułów zabezpieczeń (HSM) dla kluczy o wysokiej wartości. Aby uzyskać więcej informacji, zobacz [Informacje o klawiszach](../keys/about-keys.md).
- Wpisy tajne: zapewnia bezpieczne przechowywanie wpisów tajnych, takich jak hasła i parametry połączenia bazy danych. Aby uzyskać więcej informacji, zobacz [Informacje o tajemnicach](../secrets/about-secrets.md).
- Certyfikaty: Obsługuje certyfikaty, które są zbudowane na kluczach i wpisach tajnych i dodają funkcję automatycznego odnawiania. Aby uzyskać więcej informacji, zobacz [Informacje o certyfikatach](../certificates/about-certificates.md).
- Usługa Azure Storage: może zarządzać kluczami konta usługi Azure Storage. Wewnętrznie Usługa Key Vault może wymieniać (synchronizować) klucze za pomocą konta usługi Azure Storage i okresowo ponownie wygenerować (obrócić) klucze. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami kont magazynu za pomocą usługi Key Vault](../secrets/overview-storage-keys.md).

Aby uzyskać bardziej ogólne informacje na temat usługi Key Vault, zobacz [Informacje o usłudze Azure Key Vault](overview.md).

## <a name="data-types"></a>Typy danych

Informacje na temat odpowiednich typów danych dotyczących kluczy, szyfrowania i podpisywania można znaleźć w specyfikacjach jose.  

-   **algorytm** - obsługiwany algorytm dla operacji klucza, na przykład RSA1_5  
-   **wartość tekstu szyfrowania** - oktety tekstu szyfrowanego, zakodowane przy użyciu base64URL  
-   **digest-value** - dane wyjściowe algorytmu mieszania, zakodowane przy użyciu Base64URL  
-   **typ klucza** - jeden z obsługiwanych typów kluczy, na przykład RSA (Rivest-Shamir-Adleman).  
-   **wartość zwykłego tekstu** - oktety w postaci zwykłego tekstu, zakodowane przy użyciu base64URL  
-   **signature-value** - dane wyjściowe algorytmu podpisu zakodowanego przy użyciu base64URL  
-   **base64URL** - wartość binarna zakodowana w base64URL [RFC4648]  
-   **wartość logiczna** — prawda lub fałsz  
-   **Tożsamość** — tożsamość z usługi Azure Active Directory (AAD).  
-   **IntDate** - wartość dziesiętna JSON reprezentująca liczbę sekund od 1970-01-01T0:0:0Z UTC do określonej daty/godziny UTC. Szczegółowe informacje na temat daty/godziny można znaleźć w 19339 r., a w szczególności w przypadku czasu utc.  

## <a name="objects-identifiers-and-versioning"></a>Obiekty, identyfikatory i przechowywanie wersji

Obiekty przechowywane w przechowalni kluczy są wersjonatowane przy każdym utworzeniu nowego wystąpienia obiektu. Każdej wersji jest przypisywany unikatowy identyfikator i adres URL. Po utworzeniu obiektu nadana jest unikatowy identyfikator wersji i oznaczona jako bieżąca wersja obiektu. Utworzenie nowego wystąpienia o tej samej nazwie obiektu nadaje nowemu obiektowi unikatowy identyfikator wersji, co powoduje, że staje się on bieżącą wersją.  

Obiekty w magazynie kluczy można rozwiązać przy użyciu bieżącego identyfikatora lub identyfikatora specyficznego dla wersji. Na przykład, biorąc pod `MasterKey`uwagę Klucz o nazwie , wykonywanie operacji z bieżącym identyfikatorem powoduje, że system do korzystania z najnowszej dostępnej wersji. Wykonywanie operacji z identyfikatorem specyficznym dla wersji powoduje, że system używa tej określonej wersji obiektu.  

Obiekty są jednoznacznie identyfikowane w magazynie kluczy przy użyciu adresu URL. Żadne dwa obiekty w systemie nie mają tego samego adresu URL, niezależnie od lokalizacji geograficznej. Pełny adres URL obiektu nosi nazwę identyfikatora obiektu. Adres URL składa się z prefiksu identyfikującego magazyn kluczy, typ obiektu, nazwę obiektu pod warunkiem użytkownika i wersję obiektu. Nazwa obiektu jest niewrażliwa na argumenty i niezmienna. Identyfikatory, które nie zawierają wersji obiektu są określane jako identyfikatory podstawowe.  

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie, żądania i odpowiedzi](authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący ogólny format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Gdzie:  

|||  
|-|-|  
|`keyvault-name`|Nazwa magazynu kluczy w usłudze Microsoft Azure Key Vault.<br /><br /> Nazwy magazynu kluczy są wybierane przez użytkownika i są unikatowe globalnie.<br /><br /> Nazwa magazynu kluczy musi być ciągiem znaków 3-24, zawierającym tylko 0-9, a-z, A-Z i -.|  
|`object-type`|Typ obiektu, "keys", "secrets" lub 'certificates'.|  
|`object-name`|Nazwa `object-name` użytkownika jest nazwą podana przez użytkownika i musi być unikatowa w magazynie kluczy. Nazwa musi być ciągiem znaków 1-127, zawierającym tylko 0-9, a-z, A-Z i -.|  
|`object-version`|An `object-version` jest generowany przez system, 32-znakowy identyfikator ciągu, który jest opcjonalnie używany do adresu unikatową wersję obiektu.|  

## <a name="next-steps"></a>Następne kroki

- [Informacje o kluczach](../keys/about-keys.md)
- [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- [Informacje o certyfikatach](../certificates/about-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)