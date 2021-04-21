---
title: Informacje Azure Key Vault certyfikatów — Azure Key Vault
description: Omówienie interfejsu REST Azure Key Vault certyfikatów.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: fb69068ddac311a8020a76eec9b18fab3256fea6
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752538"
---
# <a name="about-azure-key-vault-certificates"></a>Informacje o certyfikatach usługi Azure Key Vault

Key Vault certyfikatów zapewnia zarządzanie certyfikatami x509 i następujące zachowania:  

-   Umożliwia właścicielowi certyfikatu utworzenie certyfikatu za pośrednictwem Key Vault lub przez zaimportowanie istniejącego certyfikatu. Obejmuje certyfikaty z podpisem własnym i certyfikat wygenerowany przez urząd certyfikacji.
-   Umożliwia właścicielowi Key Vault zaimplementowanie bezpiecznego magazynu certyfikatów X509 i zarządzanie nimi bez interakcji z materiałami klucza prywatnego.  
-   Umożliwia właścicielowi certyfikatu utworzenie zasad, które Key Vault zarządzania cyklem życia certyfikatu.  
-   Umożliwia właścicielom certyfikatów podanie informacji kontaktowych w celu powiadamiania o zdarzeniach cyklu życia wygaśnięcia i odnowienia certyfikatu.  
-   Obsługuje automatyczne odnawianie przy użyciu wybranych wystawców — Key Vault certyfikatów X509 partnera/ urzędów certyfikacji.

>[!Note]
>Dostawcy/urzędy, którzy nie są partnerami, są również dozwolone, ale nie będą obsługiwać funkcji automatycznego odnawiania.

## <a name="composition-of-a-certificate"></a>Kompozycja certyfikatu

Po utworzeniu Key Vault jest tworzony adresowalny klucz i klucz tajny o tej samej nazwie. Klucz Key Vault umożliwia operacje na kluczach, a klucz Key Vault tajny umożliwia pobranie wartości certyfikatu jako klucza tajnego. Certyfikat Key Vault również zawiera metadane publicznego certyfikatu x509.  

Identyfikator i wersja certyfikatów są podobne do kluczy i wpisów tajnych. Konkresowa wersja adresowalnego klucza i klucza tajnego utworzonego za pomocą Key Vault wersji certyfikatu jest dostępna w odpowiedzi Key Vault certyfikatu.
 
![Certyfikaty są obiektami złożonymi](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Klucz eksportowalny lub nieeksportowalny

Po utworzeniu Key Vault można go pobrać z adresowalnego klucza tajnego przy użyciu klucza prywatnego w formacie PFX lub PEM. Zasady używane do tworzenia certyfikatu muszą wskazywać, że klucz można eksportować. Jeśli zasady wskazują, że nie można eksportować, klucz prywatny nie jest częścią wartości po pobraniu jako klucz tajny.  

Adresowalny klucz staje się bardziej istotny w przypadku nieeksportowalnych certyfikatów KV. Operacje adresowalnego klucza KV są mapowane z pola *keyusage* zasad certyfikatów KV używanych do tworzenia certyfikatu KV.  

Typ pary kluczy obsługiwanej dla certyfikatów

 - Obsługiwane typy kluczy: RSA, RSA-HSM, EC, EC-HSM, oct (wymienione tutaj [)](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)Eksportowanie jest dozwolone tylko w przypadku RSA, EC. Kluczy HSM nie można eksportować.

|Typ klucza|Informacje|Zabezpieczenia|
|--|--|--|
|**Rsa**| Klucz RSA "Chroniony przez oprogramowanie"|FIPS 140-2 Poziom 1|
|**RSA-HSM**| Klucz RSA "chroniony przez moduł HSM" (tylko wersja Premium SKU)|MODUŁ HSM fips 140-2 poziom 2|
|**EC**| Klucz krzywej wielokropka "Chronione przez oprogramowanie"|FIPS 140-2 Poziom 1|
|**MODUŁ HSM EC**| Klucz krzywej wielokropka "chronione przez moduł HSM" (tylko wersja Premium SKU)|MODUŁ HSM fips 140-2 poziom 2|
|||

## <a name="certificate-attributes-and-tags"></a>Atrybuty i tagi certyfikatu

Oprócz metadanych certyfikatu, adresowalnego klucza i adresowalnego klucza tajnego, certyfikat Key Vault również atrybuty i tagi.  

### <a name="attributes"></a>Atrybuty

Atrybuty certyfikatu są dublowane do atrybutów adresowalnego klucza i klucza tajnego utworzonych podczas tworzenia certyfikatu KV.  

Certyfikat Key Vault ma następujące atrybuty:  

-   *enabled*: wartość logiczna, opcjonalna, wartość domyślna to **true.** Można określić, aby wskazać, czy dane certyfikatu mogą być pobierane jako klucz tajny lub czy mogą być obsługiwane jako klucz. Używana również w połączeniu z *nbf* i *exp,* gdy operacja występuje między *nbf* i *exp*, i będzie dozwolone tylko wtedy, gdy włączona jest ustawiona na wartość true. Operacje poza *oknem nbf* *i exp* są automatycznie niedozwolone.  

Istnieją dodatkowe atrybuty tylko do odczytu, które są uwzględnione w odpowiedzi:

-   *created*: IntDate: wskazuje, kiedy ta wersja certyfikatu została utworzona.  
-   *updated:* IntDate: wskazuje, kiedy ta wersja certyfikatu została zaktualizowana.  
-   *exp*: IntDate: zawiera wartość daty wygaśnięcia certyfikatu x509.  
-   *nbf:* IntDate: zawiera wartość daty certyfikatu x509.  

> [!Note] 
> Jeśli certyfikat usługi Key Vault wygaśnie, adresowany klucz i wpis tajny przestaną działać.  

### <a name="tags"></a>Tagi

 Określony przez klienta słownik par klucz-wartość, podobny do tagów w kluczach i wpisach tajnych.  

 > [!Note]
> Tagi są czytelne dla obiektu wywołującego, jeśli mają listę lub mają uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).  

## <a name="certificate-policy"></a>Zasady certyfikatów

Zasady certyfikatów zawierają informacje na temat tworzenia cyklu życia certyfikatu Key Vault zarządzania nim. Gdy certyfikat z kluczem prywatnym jest importowany do magazynu kluczy, zasady domyślne są tworzone przez odczytanie certyfikatu x509.  

Gdy certyfikat Key Vault jest tworzony od podstaw, należy dostarczyć zasady. Zasady określają sposób tworzenia tej Key Vault wersji certyfikatu lub następnej Key Vault wersji certyfikatu. Po utworzeniu zasady nie są wymagane w kolejnych operacjach tworzenia dla przyszłych wersji. Istnieje tylko jedno wystąpienie zasad dla wszystkich wersji Key Vault certyfikatu.  

Na wysokim poziomie zasady certyfikatów zawierają następujące informacje (ich definicje można znaleźć [tutaj):](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy)  

-   Właściwości certyfikatu X509: zawiera nazwę podmiotu, alternatywne nazwy podmiotu i inne właściwości używane do tworzenia żądania certyfikatu x509.  
-   Właściwości klucza: zawiera pola typ klucza, długość klucza, pola eksportowalne i ReuseKeyOnRenewal. Te pola instruuje magazyn kluczy, jak wygenerować klucz. 
     - Obsługiwane typy kluczy: RSA, RSA-HSM, EC, EC-HSM, oct (wymienione [tutaj](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)) 
-   Właściwości wpisów tajnych: zawierają właściwości wpisów tajnych, takie jak typ zawartości adresowalnego wpisu tajnego w celu wygenerowania wartości wpisów tajnych do pobierania certyfikatu jako tajnego.  
-   Akcje okresu istnienia: zawierają akcje okresu istnienia dla certyfikatu KV. Każda akcja okresu istnienia zawiera:  

     - Wyzwalacz: określony za pośrednictwem dni przed wygaśnięciem lub procentem zakresu okresu istnienia  

     - Akcja: określanie typu akcji — *emailContacts* lub *autoRenew*  

-   Wystawca: parametry dotyczące wystawcy certyfikatu do użycia w celu wystawienia certyfikatów x509.  
-   Atrybuty zasad: zawiera atrybuty skojarzone z zasadami  

### <a name="x509-to-key-vault-usage-mapping"></a>X509 do Key Vault mapowania użycia

W poniższej tabeli przedstawiono mapowanie zasad użycia klucza x509 na efektywne operacje klucza utworzonego w ramach tworzenia Key Vault certyfikatu.

|**Flagi użycia klucza X509**|**Key Vault kluczowych operacyjno**|**Zachowanie domyślne**|
|----------|--------|--------|
|Szyfrowanie danych|szyfrowanie, odszyfrowywanie| Nie dotyczy |
|DecipherOnly|Odszyfrować| Nie dotyczy  |
|DigitalSignature|podpisywanie, weryfikowanie| Key Vault bez specyfikacji użycia w czasie tworzenia certyfikatu | 
|EncipherOnly|encrypt| Nie dotyczy |
|KeyCertSign|podpisywanie, weryfikowanie|Nie dotyczy|
|Szyfrowanie klucza|wrapKey, unwrapKey| Key Vault bez specyfikacji użycia w czasie tworzenia certyfikatu | 
|NonRepudiation|podpisywanie, weryfikowanie| Nie dotyczy |
|crlsign|podpisywanie, weryfikowanie| Nie dotyczy |

## <a name="certificate-issuer"></a>Wystawca certyfikatu

Obiekt Key Vault zawiera konfigurację używaną do komunikowania się z wybranym dostawcą wystawcy certyfikatów w celu zamówienia certyfikatów x509.  

-   Key Vault z następującymi dostawcami wystawców certyfikatów dla certyfikatów TLS/SSL

|**Nazwa dostawcy**|**Lokalizacje**|
|----------|--------|
|DigiCert|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i Azure Government|
|GlobalSign|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i Azure Government|

Aby można było utworzyć wystawcę certyfikatu w Key Vault, należy pomyślnie wykonać kroki 1 i 2 wymagań wstępnych.  

1. Dołączanie do dostawców urzędu certyfikacji  

    -   Administrator organizacji musi dochować swojej firmy (np. Contoso) z co najmniej jednym dostawcą urzędu certyfikacji.  

2. Administrator tworzy poświadczenia żądają dla Key Vault rejestrowania (i odnawiania) certyfikatów TLS/SSL  

    -   Zawiera konfigurację, która ma być używana do tworzenia obiektu wystawcy dostawcy w magazynie kluczy  

Aby uzyskać więcej informacji na temat tworzenia obiektów wystawcy z portalu certyfikatów, zobacz [blog Key Vault Certyfikatów](/archive/blogs/kv/manage-certificates-via-azure-key-vault)  

Key Vault umożliwia utworzenie wielu obiektów wystawców z różnymi konfiguracjami dostawcy wystawców. Po utworzeniu obiektu wystawcy można odwoływać się do jego nazwy w jednej lub wielu zasadach certyfikatów. Odwołanie do obiektu wystawcy powoduje, że Key Vault konfiguracji określonej w obiekcie wystawcy podczas żądania certyfikatu x509 od dostawcy urzędu certyfikacji podczas tworzenia i odnawiania certyfikatu.  

Obiekty wystawcy są tworzone w magazynie i mogą być używane tylko z certyfikatami KV w tym samym magazynie.  

## <a name="certificate-contacts"></a>Kontakty z certyfikatami

Kontakty certyfikatów zawierają informacje kontaktowe do wysyłania powiadomień wyzwalanych przez zdarzenia okresu istnienia certyfikatu. Informacje o kontaktach są współdzielone przez wszystkie certyfikaty w magazynie kluczy. Powiadomienie o zdarzeniu dla dowolnego certyfikatu w magazynie kluczy jest wysyłane do wszystkich określonych kontaktów. Aby uzyskać informacje na temat sposobu ustawienia kontaktu z certyfikatem, zobacz [tutaj](overview-renew-certificate.md#steps-to-set-certificate-notifications)  

## <a name="certificate-access-control"></a>Certyfikat Access Control

 Kontrola dostępu do certyfikatów jest zarządzana przez usługę Key Vault i jest dostarczana przez magazyn kluczy, który zawiera te certyfikaty. Zasady kontroli dostępu dla certyfikatów różnią się od zasad kontroli dostępu dla kluczy i wpisów tajnych w tym samym Key Vault. Użytkownicy mogą tworzyć co najmniej jeden magazyn do przechowywania certyfikatów w celu obsługi odpowiedniej segmentacji i zarządzania certyfikatami w scenariuszu.  Aby uzyskać więcej informacji na temat kontroli dostępu do certyfikatów, zobacz [tutaj](certificate-access-control.md)

## <a name="next-steps"></a>Następne kroki

- [Informacje o usłudze Key Vault](../general/overview.md)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](../general/about-keys-secrets-certificates.md)
- [Informacje o kluczach](../keys/about-keys.md)
- [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
