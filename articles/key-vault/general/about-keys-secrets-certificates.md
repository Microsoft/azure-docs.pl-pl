---
title: Azure Key Vault klucze, wpisy tajne i certyfikaty — Omówienie
description: Omówienie Azure Key Vault interfejsu REST i szczegółów dla deweloperów kluczy, wpisów tajnych i certyfikatów.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 78f228a5e188bc930a9e7484f4c982ba746331dd
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357780"
---
# <a name="azure-key-vault-keys-secrets-and-certificates-overview"></a>Azure Key Vault klucze, wpisy tajne i certyfikaty — Omówienie

Azure Key Vault umożliwia aplikacjom Microsoft Azure i użytkownikom przechowywanie i używanie kilku typów danych tajnych/kluczy. Dostawca zasobów Key Vault obsługuje dwa typy zasobów: magazyny i zarządzane sprzętowych modułów zabezpieczeń.

## <a name="dns-suffixes-for-base-url"></a>Sufiksy DNS dla podstawowego adresu URL
 W poniższej tabeli przedstawiono sufiks DNS podstawowego adresu URL używany przez punkt końcowy płaszczyzny danych dla magazynów i zarządzane pule modułów HSM w różnych środowiskach chmurowych.

Środowisko chmury | Sufiks DNS dla magazynów | Sufiks DNS dla zarządzanego sprzętowych modułów zabezpieczeń
---|---|---
Chmura platformy Azure | . vault.azure.net | . managedhsm.azure.net
Chmura Chińska platformy Azure | . vault.azure.cn | Nieobsługiwane
Wersja platformy Azure dla administracji USA | . vault.usgovcloudapi.net | Nieobsługiwane
Chmura w języku niemieckim platformy Azure | . vault.microsoftazure.de | Nieobsługiwane
|||


## <a name="object-types"></a>Typy obiektów
 W poniższej tabeli przedstawiono typy obiektów i ich sufiksy w podstawowym adresie URL.

Typ obiektu|Sufiks adresu URL|Magazyny|Zarządzane pule modułu HSM
--|--|--|--
**Klucze kryptograficzne**||
Klucze chronione przez moduł HSM|/keys|Obsługiwane|Obsługiwane
Klucze chronione przez oprogramowanie|/keys|Obsługiwane|Nieobsługiwane
**Inne typy obiektów**||
Wpisy tajne|/secrets|Obsługiwane|Nieobsługiwane
Certyfikaty|/certificates|Obsługiwane|Nieobsługiwane
Klucze kont magazynu|/storageaccount|Obsługiwane|Nieobsługiwane
|||
- **Klucze kryptograficzne** : obsługuje wiele typów kluczy i algorytmów oraz umożliwia korzystanie z kluczy chronionych przez moduł HSM i chronionych przez moduł zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Informacje o kluczach](../keys/about-keys.md).
- Wpisy **tajne** : zapewnia bezpieczny magazyn wpisów tajnych, takich jak hasła i parametry połączenia bazy danych. Aby uzyskać więcej informacji, zobacz [Informacje o wpisach tajnych](../secrets/about-secrets.md).
- **Certyfikaty** : obsługuje certyfikaty, które są oparte na kluczach i wpisach tajnych i dodają funkcję automatycznego odnawiania. Aby uzyskać więcej informacji, zobacz [Informacje o certyfikatach](../certificates/about-certificates.md).
- **Klucze konta usługi Azure Storage** : mogą zarządzać kluczami konta usługi Azure Storage. Wewnętrznie Key Vault mogą wyświetlać (synchronizować) klucze przy użyciu konta usługi Azure Storage i ponownie generować (obrócić) klucze okresowo. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami konta magazynu przy użyciu Key Vault](../secrets/overview-storage-keys.md).

Aby uzyskać więcej ogólnych informacji na temat Key Vault, zobacz [Informacje o Azure Key Vault](overview.md). Aby uzyskać więcej informacji na temat zarządzanych pul modułów HSM, zobacz co to jest [Azure Key Vault zarządzany moduł HSM?](../managed-hsm/overview.md)


## <a name="data-types"></a>Typy danych

Zapoznaj się ze specyfikacją JOSE dla odpowiednich typów danych dla kluczy, szyfrowania i podpisywania.  

-   **algorytm** — obsługiwany algorytm dla operacji Key, na przykład RSA1_5  
-   tekst **szyfrowany-wartość** — umożliwia szyfrowanie oktetów tekstu zakodowanych przy użyciu Base64URL  
-   **Digest-Value** — dane wyjściowe algorytmu wyznaczania wartości skrótu zakodowane przy użyciu Base64URL  
-   **Typ klucza** — jeden z obsługiwanych typów kluczy, na przykład RSA (Rivest-Shamir-Adleman).  
-   **zwykły tekst —** oktety w postaci zwykłego tekstu, kodowane przy użyciu Base64URL  
-   **sygnatura-wartość** — dane wyjściowe algorytmu podpisu zakodowane przy użyciu Base64URL  
-   **base64URL** -a BASE64URL [RFC4648] zakodowana wartość binarną  
-   wartość **logiczna** — true lub false  
-   **Tożsamość** — tożsamość z Azure Active Directory (AAD).  
-   **IntDate** — wartość dziesiętna JSON reprezentująca liczbę sekund od 1970-01-01T0:0: 0Z UTC do określonej daty/godziny UTC. Zobacz RFC3339, aby uzyskać szczegółowe informacje dotyczące daty/godziny, ogólnie i czasu UTC.  

## <a name="objects-identifiers-and-versioning"></a>Obiekty, identyfikatory i przechowywanie wersji

Obiekty przechowywane w Key Vault są używane w wersji za każdym razem, gdy zostanie utworzone nowe wystąpienie obiektu. Każda wersja ma przypisany unikatowy identyfikator i adres URL. Gdy obiekt jest tworzony, otrzymuje unikatowy identyfikator wersji i jest oznaczony jako bieżąca wersja obiektu. Utworzenie nowego wystąpienia o tej samej nazwie obiektu daje nowemu obiektowi unikatowy identyfikator wersji, powodując, że staje się on bieżącą wersją.  

Obiekty w Key Vault mogą być rozkierowane przez określenie wersji lub pominięcie wersji dla operacji w bieżącej wersji obiektu. Na przykład, jeśli klucz o nazwie `MasterKey` , wykonywanie operacji bez określania wersji powoduje, że system używa najnowszej dostępnej wersji. Wykonywanie operacji z identyfikatorem specyficznym dla wersji powoduje, że system używa tej konkretnej wersji obiektu.  

Obiekty są jednoznacznie identyfikowane w Key Vault przy użyciu adresu URL. Żadne dwa obiekty w systemie nie mają tego samego adresu URL, niezależnie od lokalizacji geograficznej. Pełny adres URL do obiektu jest nazywany identyfikatorem obiektu. Adres URL składa się z prefiksu, który identyfikuje Key Vault, typ obiektu, nazwę obiektu dostarczoną przez użytkownika i wersję obiektu. Nazwa obiektu nie uwzględnia wielkości liter i jest niezmienna. Identyfikatory, które nie zawierają wersji obiektu, są określane jako identyfikatory podstawowe.  

Aby uzyskać więcej informacji, zobacz [uwierzytelnianie, żądania i odpowiedzi](authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący format ogólny (w zależności od typu kontenera):  

- **W przypadku magazynów** : `https://{vault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

- **W przypadku zarządzanych pul modułu HSM** : `https://{hsm-name}.managedhsm.azure.net/{object-type}/{object-name}/{object-version}`  

> [!NOTE]
> Zobacz [Obsługa typu obiektu](#object-types) dla typów obiektów obsługiwanych przez każdy typ kontenera.

Gdzie:  

| Element | Opis |  
|-|-|  
|`vault-name` lub `hsm-name`|Nazwa magazynu lub Zarządzana pula modułów HSM w usłudze Microsoft Azure Key Vault.<br /><br />Nazwy magazynów i zarządzane nazwy puli modułów HSM są wybierane przez użytkownika i globalnie unikatowe.<br /><br />Nazwa magazynu i Nazwa puli zarządzanych modułów HSM muszą być ciągiem znaków 3-24, zawierającym tylko 0-9, a-z, A-Z i-.|  
|`object-type`|Typ obiektu, "Keys", "Secret" lub "Certificates".|  
|`object-name`|`object-name`A to nazwa podana przez użytkownika dla i musi być unikatowa w obrębie Key Vault. Nazwa musi być ciągiem znaków 1-127, rozpoczynając od litery i zawierającą tylko 0-9, a-z, A-Z, i-.|  
|`object-version`|`object-version`Jest wygenerowanym przez system 32 identyfikatorem ciągu znaków, który jest opcjonalnie używany do adresowania unikatowej wersji obiektu.|  

## <a name="next-steps"></a>Następne kroki

- [Informacje o kluczach](../keys/about-keys.md)
- [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- [Informacje o certyfikatach](../certificates/about-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
