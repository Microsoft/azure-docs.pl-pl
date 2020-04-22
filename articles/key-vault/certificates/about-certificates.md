---
title: Certyfikaty usługi Azure Key Vault — usługa Azure Key Vault
description: Omówienie interfejsu i certyfikatów USŁUGI Azure Key Vault REST.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 5e014634ecb251f05710de16daee30d72dae619e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685899"
---
# <a name="about-azure-key-vault-certificates"></a>Certyfikaty usługi Azure Key Vault — informacje

Obsługa certyfikatów usługi Key Vault zapewnia zarządzanie certyfikatami x509 i następującymi zachowaniami:  

-   Umożliwia właścicielowi certyfikatu utworzenie certyfikatu za pośrednictwem procesu tworzenia magazynu kluczy lub za pomocą importu istniejącego certyfikatu. Obejmuje certyfikaty z podpisem własnym i certyfikatami wygenerowanymi przez urząd certyfikacji.
-   Umożliwia właścicielowi certyfikatu usługi Key Vault implementowanie bezpiecznego przechowywania i zarządzania certyfikatami X509 bez interakcji z materiałem klucza prywatnego.  
-   Umożliwia właścicielowi certyfikatu utworzenie zasad, które nakazuje magazynowi kluczy zarządzanie cyklem życia certyfikatu.  
-   Umożliwia właścicielom certyfikatów dostarczanie informacji kontaktowych w celu powiadamiania o zdarzeniach cyklu życia wygaśnięcia i odnawiania certyfikatu.  
-   Obsługuje automatyczne odnawianie z wybranymi wystawcami — dostawcami certyfikatów /urzędami certyfikacji programu Key Vault partner X509.

>[!Note]
>Dostawcy/władze niebędące partnerami również są dozwolone, ale nie obsługują funkcji automatycznego odnawiania.

## <a name="composition-of-a-certificate"></a>Skład certyfikatu

Podczas tworzenia certyfikatu usługi Key Vault tworzony jest również klucz adresowa i klucz tajny o tej samej nazwie. Klucz przechowalni kluczy umożliwia operacje klucza, a klucz tajny magazynu kluczy umożliwia pobieranie wartości certyfikatu jako klucza tajnego. Certyfikat Usługi Key Vault zawiera również publiczne metadane certyfikatu x509.  

Identyfikator i wersja certyfikatów jest podobny do kluczy i wpisów tajnych. W odpowiedzi na certyfikat usługi Key Vault dostępna jest określona wersja klucza adresowalnej i klucza tajnego utworzonego za pomocą wersji certyfikatu usługi Key Vault.
 
![Certyfikaty są obiektami złożonymi](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Klucz do eksportu lub nieeksportowania

Po utworzeniu certyfikatu Usługi Key Vault można go pobrać z adresowalnej tajemnicy tajnej za pomocą klucza prywatnego w formacie PFX lub PEM. Zasady używane do tworzenia certyfikatu muszą wskazywać, że klucz można wyeksportować. Jeśli zasada wskazuje, że nie można eksportować, klucz prywatny nie jest częścią wartości po pobraniu jako klucz tajny.  

Klucz adresowalny staje się bardziej odpowiedni w przypadku certyfikatów KV nieeksportowalnych. Operacje adresowalne klucza KV są mapowane z pola *keyusage* zasad certyfikatu KV używanych do tworzenia certyfikatu KV.  

Obsługiwane są dwa typy kluczy — *RSA* lub *RSA HSM* z certyfikatami. Eksportowanie jest dozwolone tylko przy pomocy rsa, nie jest obsługiwane przez moduł HSM RSA.  

## <a name="certificate-attributes-and-tags"></a>Atrybuty certyfikatów i znaczniki

Oprócz metadanych certyfikatów, adresowalny klucz i adresowalny klucz tajny, certyfikat Usługi Key Vault zawiera również atrybuty i tagi.  

### <a name="attributes"></a>Atrybuty

Atrybuty certyfikatu są dublowane do atrybutów klucza adresowalnej i klucza tajnego utworzonego podczas tworzenia certyfikatu KV.  

Certyfikat Usługi Key Vault ma następujące atrybuty:  

-   *włączone*: logiczne, opcjonalne, domyślnie jest **prawdziwe**. Można określić, aby wskazać, czy dane certyfikatu mogą być pobierane jako tajne lub obsługiwane jako klucz. Używany również w połączeniu z *nbf* i *exp,* gdy operacja występuje między *nbf* i *exp*, i będzie dozwolone tylko wtedy, gdy włączona jest ustawiona na true. Operacje poza oknem *nbf* i *exp* są automatycznie niedozwolone.  

Istnieją dodatkowe atrybuty tylko do odczytu, które są zawarte w odpowiedzi:

-   *utworzony*: IntDate: wskazuje, kiedy ta wersja certyfikatu została utworzona.  
-   *aktualizacja*: IntDate: wskazuje, kiedy ta wersja certyfikatu została zaktualizowana.  
-   *exp*: IntDate: zawiera wartość daty wygaśnięcia certyfikatu x509.  
-   *nbf*: IntDate: zawiera wartość daty certyfikatu x509.  

> [!Note] 
> Jeśli certyfikat Usługi Key Vault wygaśnie, adresowalny klucz i klucz tajny stają się niesprawne.  

### <a name="tags"></a>Tagi

 Klient określony słownik par wartości klucza, podobne do tagów w klucze i wpisy tajne.  

 > [!Note]
> Znaczniki są czytelne przez obiekt wywołujący, jeśli mają *listę* lub *uzyskać* uprawnienia do tego typu obiektu (klucze, wpisy tajne lub certyfikaty).

## <a name="certificate-policy"></a>Zasady dotyczące certyfikatów

Zasady certyfikatów zawierają informacje dotyczące tworzenia i zarządzania cyklem życia certyfikatu usługi Key Vault. Gdy certyfikat z kluczem prywatnym jest importowany do magazynu kluczy, domyślna zasada jest tworzona przez odczyt certyfikatu x509.  

Gdy certyfikat usługi Key Vault jest tworzony od podstaw, należy podać zasadę. Zasady określają sposób tworzenia tej wersji certyfikatu usługi Key Vault lub następnej wersji certyfikatu Usługi Key Vault. Po ustanowieniu zasad nie jest wymagane z kolejnych operacji tworzenia dla przyszłych wersji. Istnieje tylko jedno wystąpienie zasad dla wszystkich wersji certyfikatu usługi Key Vault.  

Na wysokim poziomie zasady certyfikatów zawierają następujące informacje:  

-   Właściwości certyfikatu X509: zawiera nazwę podmiotu, alternatywne nazwy podmiotu i inne właściwości używane do tworzenia żądania certyfikatu x509.  
-   Właściwości klucza: zawiera pola klucza typu, długości klucza, eksportowania i ponownego użycia. Te pola instruują przechowalnię kluczy dotyczące sposobu generowania klucza.  
-   Właściwości klucza tajnego: zawiera tajne właściwości, takie jak typ zawartości adresowalny klucz tajny do generowania wartości tajnej, do pobierania certyfikatu jako klucz tajny.  
-   Akcje okresu istnienia: zawiera akcje okresu istnienia certyfikatu KV. Każda czynność dożywotnia zawiera:  

     - Wyzwalacz: określony przez dni przed wygaśnięciem lub procent zakresu okresu istnienia  

     - Akcja: określanie typu akcji — *emailContacts* lub *autoRenew*  

-   Wystawca: Parametry dotyczące wystawcy certyfikatu używanego do wystawiania certyfikatów x509.  
-   Atrybuty zasad: zawiera atrybuty skojarzone z zasadami  

### <a name="x509-to-key-vault-usage-mapping"></a>Mapowanie użycia od X509 do usługi Key Vault

Poniższa tabela przedstawia mapowanie zasad użycia klucza x509 do skutecznych operacji klucza klucza utworzonego w ramach tworzenia certyfikatu usługi Key Vault.

|**Flagi użycia kluczy X509**|**Operacje kluczy przechowalni kluczy**|**Zachowanie domyślne**|
|----------|--------|--------|
|DataSeksfera|szyfruj, odszyfrowywanie| Nie dotyczy |
|DecipherOnly|Odszyfrować| Nie dotyczy  |
|Podpis cyfrowy|podpisz, sprawdź| Domyślna usługa Key Vault bez specyfikacji użycia w czasie tworzenia certyfikatu | 
|EncipherOnly|encrypt| Nie dotyczy |
|Znak KeyCertSign|podpisz, sprawdź|Nie dotyczy|
|KeyEncipherment (Szyfrowanie klawiszowe)|wrapKey, odrapać Klucz| Domyślna usługa Key Vault bez specyfikacji użycia w czasie tworzenia certyfikatu | 
|Brak zmian|podpisz, sprawdź| Nie dotyczy |
|znak crlsign|podpisz, sprawdź| Nie dotyczy |

## <a name="certificate-issuer"></a>Wystawca certyfikatu

Obiekt certyfikatu Usługi Key Vault zawiera konfigurację używaną do komunikowania się z wybranym dostawcą wystawcy certyfikatów w celu zamówienia certyfikatów x509.  

-   Key Vault współpracuje z następującymi dostawcami wystawców certyfikatów dla certyfikatów TLS/SSL

|**Nazwa dostawcy**|**Lokalizacje**|
|----------|--------|
|DigiCert|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i usłudze Azure Dla Instytucji Rządowych|
|GlobalSign|Obsługiwane we wszystkich lokalizacjach usługi magazynu kluczy w chmurze publicznej i usłudze Azure Dla Instytucji Rządowych|

Aby wystawca certyfikatu mógł zostać utworzony w magazynie kluczy, należy pomyślnie wykonać kroki 1 i 2 wymagań wstępnych.  

1. Dołączanie do dostawców urzędu certyfikacji  

    -   Administrator organizacji musi w swojej firmie (np. Contoso) z co najmniej jednym dostawcą urzędu certyfikacji.  

2. Administrator tworzy poświadczenia żądania dla usługi Key Vault w celu rejestrowania (i odnawiania) certyfikatów TLS/SSL  

    -   Udostępnia konfigurację, która ma być używana do utworzenia obiektu wystawcy dostawcy w magazynie kluczy  

Aby uzyskać więcej informacji na temat tworzenia obiektów wystawców z portalu Certyfikaty, zobacz [blog Certyfikaty magazynu kluczy](https://aka.ms/kvcertsblog)  

Usługa Key Vault umożliwia tworzenie wielu obiektów wystawców z inną konfiguracją dostawcy wystawcy. Po utworzeniu obiektu wystawcy można odwoływać się do jego nazwy w jednej lub wielu zasadach certyfikatów. Odwoływanie się do obiektu wystawcy nakazuje magazynowi kluczy funkcji używanie konfiguracji określonej w obiekcie wystawcy podczas żądania certyfikatu x509 od dostawcy urzędu certyfikacji podczas tworzenia i odnawiania certyfikatu.  

Obiekty wystawcy są tworzone w przechowalni i mogą być używane tylko z certyfikatami KV w tym samym magazynie.  

## <a name="certificate-contacts"></a>Kontakty certyfikatów

Kontakty certyfikatów zawierają informacje kontaktowe do wysyłania powiadomień wyzwalanych przez zdarzenia okresu istnienia certyfikatu. Informacje o kontaktach są udostępniane przez wszystkie certyfikaty w magazynie kluczy. Powiadomienie jest wysyłane do wszystkich określonych kontaktów dla zdarzenia dla dowolnego certyfikatu w magazynie kluczy.  

Jeśli zasady certyfikatu są ustawione na automatyczne odnawianie, powiadomienie jest wysyłane w następujących zdarzeniach.  

- Przed odnowieniem certyfikatu
- Po odnowieniu certyfikatu, podając, czy certyfikat został pomyślnie odnowiony lub jeśli wystąpił błąd, wymagających ręcznego odnowienia certyfikatu.  

  Gdy zasady certyfikatów, które mają być odnawiane ręcznie (tylko wiadomości e-mail), powiadomienie jest wysyłane, gdy nadszedł czas, aby odnowić certyfikat.  

## <a name="certificate-access-control"></a>Kontrola dostępu do certyfikatów

 Kontrola dostępu dla certyfikatów jest zarządzana przez magazyn kluczy i jest dostarczana przez Magazyn kluczy, który zawiera te certyfikaty. Zasady kontroli dostępu dla certyfikatów różni się od zasad kontroli dostępu dla kluczy i wpisów tajnych w tym samym Magazynie kluczy. Użytkownicy mogą utworzyć jeden lub więcej magazynów do przechowywania certyfikatów, aby zachować scenariusz odpowiedniej segmentacji i zarządzania certyfikatami.  

 Następujące uprawnienia mogą być używane, na zasadzie jednostkowej, we wpisie kontroli dostępu do wpisu nieudzielonych wpisów w magazynie kluczy i ściśle odzwierciedla operacje dozwolone w obiekcie tajnym:  

- Uprawnienia do operacji zarządzania certyfikatami
  - *pobierz*: Pobierz bieżącą wersję certyfikatu lub dowolną wersję certyfikatu 
  - *lista*: Lista bieżących certyfikatów lub wersji certyfikatu  
  - *update*: Aktualizowanie certyfikatu
  - *tworzenie*: Tworzenie certyfikatu usługi Key Vault
  - *import*: Importowanie materiału certyfikatu do certyfikatu magazynu kluczy
  - *delete*: Usuwanie certyfikatu, jego zasad i wszystkich jego wersji  
  - *odzyskiwanie*: Odzyskiwanie usuniętego certyfikatu
  - *kopia zapasowa*: Tworzenie kopii zapasowej certyfikatu w magazynie kluczy
  - *przywracanie*: Przywracanie kopii zapasowej certyfikatu do magazynu kluczy
  - *zarządzanie kontaktami*: Zarządzanie kontaktami certyfikatów usługi Key Vault  
  - *zarządzanieissuers*: Zarządzanie urzędami certyfikatów usługi Key Vault /wystawców
  - *getissuers*: Pobierz certyfikat organów / emitentów
  - *listissuers*: Lista organów/wystawców certyfikatu  
  - *setissuers*: Tworzenie lub aktualizowanie urzędów/wystawców certyfikatu usługi Key Vault  
  - *deleteissuers*: Usuwanie organów/wystawców certyfikatu magazynu kluczy  
 
- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszcz (trwale usuń) usunięty certyfikat

Aby uzyskać więcej informacji, zobacz [operacje certyfikatów w interfejsie API REST magazynu kluczy](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz [Vaults - Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [przechowalnia — Aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Następne kroki

- [Informacje o usłudze Key Vault](../general/overview.md)
- [Klucze, wpisy tajne i certyfikaty — informacje](../general/about-keys-secrets-certificates.md)
- [Informacje o kluczach](../keys/about-keys.md)
- [Informacje o wpisach tajnych](../secrets/about-secrets.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)