---
title: Rozpoczynanie pracy z certyfikatami usługi Key Vault
description: Poniższe scenariusze przedstawiają kilka podstawowych zastosowań usługi zarządzania certyfikatami Key Vault, w tym dodatkowe kroki wymagane do utworzenia pierwszego certyfikatu w magazynie kluczy.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 06/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 85f17897c0e3089a2d2bc5b172e98fa24e8085ff
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920444"
---
# <a name="get-started-with-key-vault-certificates"></a>Rozpoczynanie pracy z certyfikatami usługi Key Vault
Poniższe scenariusze przedstawiają kilka podstawowych zastosowań usługi zarządzania certyfikatami Key Vault, w tym dodatkowe kroki wymagane do utworzenia pierwszego certyfikatu w magazynie kluczy.

Przedstawiono następujące kwestie:
- Tworzenie pierwszego certyfikatu Key Vault
- Tworzenie certyfikatu z urzędem certyfikacji, który jest partnerem z Key Vault
- Tworzenie certyfikatu z urzędem certyfikacji, który nie jest partnerem z Key Vault
- Importowanie certyfikatu

## <a name="certificates-are-complex-objects"></a>Certyfikaty są obiektami złożonymi
Certyfikaty składają się z trzech powiązanych zasobów połączonych razem jako certyfikat Key Vault. metadane certyfikatu, klucz i wpis tajny.


![Certyfikaty są złożone](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Tworzenie pierwszego certyfikatu Key Vault  
 Aby można było utworzyć certyfikat w Key Vault (KV), należy pomyślnie wykonać kroki wymagane przez 1 i 2, a dla tego użytkownika/organizacji musi istnieć Magazyn kluczy.  

**Krok 1** — dostawcy urzędu certyfikacji  
-   Dołączanie do programu jako administrator IT, administrator infrastruktury PKI lub każda osoba zarządzająca kontami z urzędami certyfikacji dla danej firmy (np. Firma Contoso) jest warunkiem wstępnym korzystania z certyfikatów Key Vault.  
    Następujące urzędy certyfikacji są bieżącymi dostawcami partnerskimi z Key Vault. Więcej informacji można znaleźć [tutaj](./create-certificate.md#partnered-ca-providers)   
    -   DigiCert — Key Vault oferuje OV protokołu TLS/SSL z DigiCert.  
    -   GlobalSign — Key Vault oferuje OV protokołu TLS/SSL z GlobalSign.  

**Krok 2** . Administrator konta dla dostawcy urzędu certyfikacji tworzy poświadczenia, które mają być używane przez Key Vault do rejestrowania, odnawiania i używania certyfikatów TLS/SSL za pośrednictwem Key Vault.

**Krok 3** — administrator firmy Contoso, a także pracownik firmy contoso (Key Vault użytkownika), który jest właścicielem certyfikatów, w zależności od urzędu certyfikacji, może uzyskać certyfikat od administratora lub bezpośrednio z konta z urzędem certyfikacji.  

- Rozpocznij operację dodawania poświadczeń do magazynu kluczy przez ustawienie zasobu [wystawcy certyfikatu](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) . Wystawca certyfikatu jest jednostką reprezentowaną w Azure Key Vault (KV) jako zasób CertificateIssuer. Służy do przekazywania informacji o źródle certyfikatu KV; Nazwa wystawcy, dostawca, poświadczenia i inne szczegóły administracyjne.
  - Np. MyDigiCertIssuer  
    -   Dostawca  
    -   Poświadczenia — poświadczenia konta urzędu certyfikacji. Każdy urząd certyfikacji ma własne określone dane.  

    Aby uzyskać więcej informacji na temat tworzenia kont z dostawcami urzędów certyfikacji, zobacz pokrewny wpis w [blogu Key Vault](/archive/blogs/kv/manage-certificates-via-azure-key-vault).  

**Krok 3,1** — Konfigurowanie [kontaktów certyfikatów](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) na potrzeby powiadomień. Jest to kontakt dla Key Vault użytkownika. Key Vault nie wymusza tego kroku.  

Uwaga — Ten proces, przez krok 3,1, jest operacją jednorazowej.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Tworzenie certyfikatu z urzędem certyfikacji partner z Key Vault

![Tworzenie certyfikatu za pomocą Key Vault partnerskiego urzędu certyfikacji](../media/certificate-authority-2.png)

**Krok 4** . Poniższe opisy odnoszą się do zielonych numerowanych kroków na powyższym diagramie.  
  (1) — na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna od utworzenia klucza w magazynie kluczy.  
  (2) — Key Vault wysyła żądanie certyfikatu TLS/SSL do urzędu certyfikacji.  
  (3) — Twoja aplikacja sonduje, w pętli i w procesie oczekiwania, do Key Vault do ukończenia certyfikatu. Tworzenie certyfikatu kończy się, gdy usługa Key Vault otrzyma odpowiedź od urzędu certyfikacji z certyfikatem X.509.  
  (4) — urząd certyfikacji odpowiada na żądanie certyfikatu TLS/SSL Key Vault przy użyciu certyfikatu x509 TLS/SSL.  
  (5) — nowe utworzenie certyfikatu kończy się na połączeniu z połączeniem certyfikatu x509 urzędu certyfikacji.  

  Key Vault User — tworzy certyfikat przez określenie zasad

  -   Powtórz w razie konieczności  
  -   Ograniczenia zasad  
      -   Właściwości x509  
      -   Kluczowe właściwości  
      -   Odwołanie dostawcy — > np. MyDigiCertIssure  
      -   Informacje o odnowieniu — > np. 90 dni przed wygaśnięciem  

  - Proces tworzenia certyfikatu jest zwykle procesem asynchronicznym i obejmuje sondowanie magazynu kluczy w celu utworzenia operacji tworzenia certyfikatu.  
[Pobierz operację certyfikatu](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Stan: ukończono, Niepowodzenie i informacje o błędzie lub, anulowano  
      -   Ze względu na opóźnienie do utworzenia można zainicjować operację anulowania. Anulowanie może być nieskuteczne.  

### <a name="network-security-and-access-policies-associated-with-integrated-ca"></a>Zasady zabezpieczeń i dostępu sieciowego skojarzone ze zintegrowanym urzędem certyfikacji
Usługa Key Vault wysyła żądania do urzędu certyfikacji (ruch wychodzący). W związku z tym jest w pełni zgodny z magazynami kluczy z włączoną obsługą zapory. Key Vault nie udostępnia zasad dostępu do urzędu certyfikacji. Urząd certyfikacji musi być skonfigurowany tak, aby akceptował żądania podpisywania niezależnie. [Przewodnik integrowania zaufanego urzędu certyfikacji](./how-to-integrate-certificate-authority.md)

## <a name="import-a-certificate"></a>Importowanie certyfikatu  
 Alternatywnie — można zaimportować certyfikat do Key Vault — PFX lub PEM.  

 Importuj certyfikat — wymaga, aby PEM lub PFX znajdować się na dysku i mieć klucz prywatny. 
-   Należy określić: nazwę magazynu i nazwę certyfikatu (zasady są opcjonalne)

-   Pliki PEM/PFX zawierają atrybuty, które mogą być przeanalizowane i używane do wypełniania zasad certyfikatów. Jeśli zasady certyfikatów są już określone, KV spróbuje dopasować dane z pliku PFX/PEM.  

-   Po zakończeniu importowania kolejne operacje będą korzystać z nowych zasad (nowych wersji).  

-   Jeśli nie ma żadnych dalszych operacji, w pierwszej kolejności Key Vault jest wysyłana informacja o wygaśnięciu. 

-   Ponadto użytkownik może edytować zasady, które są funkcjonalne w czasie importowania, ale zawierają wartości domyślne, gdy nie zostały określone żadne informacje podczas importowania. Np. Brak informacji o wystawcy  

### <a name="formats-of-import-we-support"></a>Obsługiwane formaty importowania
Azure Key Vault obsługuje pliki certyfikatu PEM i pfx na potrzeby importowania certyfikatów do magazynu kluczy.
Obsługujemy następujący typ importu dla formatu pliku PEM. Jeden certyfikat szyfrowany przez PEM wraz z zakodowanym niezaszyfrowanym kluczem PKCS # 8, który ma następujące

-----ROZPOCZNIJ CERTYFIKAT----------CERTYFIKAT KOŃCOWY-----

-----ROZPOCZNIJ KLUCZ PRYWATNY----------KOŃCOWYM KLUCZEM PRYWATNYM-----

Podczas importowania certyfikatu należy upewnić się, że klucz jest dołączony do samego pliku. Jeśli klucz prywatny jest osobno w innym formacie, należy połączyć klucz z certyfikatem. Niektóre urzędy certyfikacji dostarczają certyfikaty w różnych formatach, dlatego przed zaimportowaniem certyfikatu upewnij się, że są one w formacie PEM lub PFX. 

### <a name="formats-of-merge-csr-we-support"></a>Formaty w formacie scalania CSR obsługiwane przez nas
AKV obsługuje 2 formaty oparte na PEM. Możliwe jest scalenie pojedynczego certyfikatu zakodowanego PKCS # 8 lub szyfrowania Base64 (łańcuch certyfikatów podpisanych przez urząd certyfikacji). 

-----ROZPOCZNIJ CERTYFIKAT----------CERTYFIKAT KOŃCOWY-----


## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Tworzenie certyfikatu z urzędem certyfikacji niepartnerskim Key Vault  
 Ta metoda umożliwia pracę z innymi urzędami certyfikacji niż dostawcami partnerskimi Key Vault, co oznacza, że organizacja może pracować z wybranym urzędem certyfikacji.  

![Tworzenie certyfikatu przy użyciu własnego urzędu certyfikacji](../media/certificate-authority-1.png)  

 Poniższe opisy kroków odpowiadają zielonym listom kroków z poprzedniego diagramu.  

  (1) — na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna od utworzenia klucza w magazynie kluczy.  

  (2) — Key Vault zwraca do aplikacji żądanie podpisania certyfikatu (CSR).  

  (3) — aplikacja przekazuje CSR do wybranego urzędu certyfikacji.  

  (4) — wybrany urząd certyfikacji odpowiada za pomocą certyfikatu x509.  

  (5) — Twoja aplikacja kończy Tworzenie nowego certyfikatu przy użyciu fuzji certyfikatu x509 z urzędu certyfikacji.
