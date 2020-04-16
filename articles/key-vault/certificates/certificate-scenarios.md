---
title: Wprowadzenie do certyfikatów usługi Key Vault
description: W poniższych scenariuszach opisano kilka podstawowych zastosowań usługi zarządzania certyfikatami usługi Key Vault, w tym dodatkowe kroki wymagane do utworzenia pierwszego certyfikatu w magazynie kluczy.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 5881314f0d3c62e7d6181ebd7bb27a5e0e87729a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431945"
---
# <a name="get-started-with-key-vault-certificates"></a>Wprowadzenie do certyfikatów usługi Key Vault
W poniższych scenariuszach opisano kilka podstawowych zastosowań usługi zarządzania certyfikatami usługi Key Vault, w tym dodatkowe kroki wymagane do utworzenia pierwszego certyfikatu w magazynie kluczy.

Przedstawiono następujące elementy:
- Tworzenie pierwszego certyfikatu usługi Key Vault
- Tworzenie certyfikatu z urzędem certyfikacji, który współpracuje z magazynem kluczy
- Tworzenie certyfikatu z urzędem certyfikacji, który nie jest partnerem usługi Key Vault
- Importowanie certyfikatu

## <a name="certificates-are-complex-objects"></a>Certyfikaty są obiektami złożonymi
Certyfikaty składają się z trzech powiązanych ze sobą zasobów połączonych ze sobą jako certyfikat usługi Key Vault; metadanych certyfikatu, klucza i klucza tajnego.


![Certyfikaty są złożone](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Tworzenie pierwszego certyfikatu usługi Key Vault  
 Aby można było utworzyć certyfikat w magazynie kluczy (KV), kroki 1 i 2 muszą zostać pomyślnie wykonane, a magazyn kluczy musi istnieć dla tego użytkownika/ organizacji.  

**Krok 1** - Dostawcy urzędu certyfikacji (CA)  
-   Na pokładzie jako administrator IT, administrator infrastruktury kluczy publicznych lub osoba zarządzająca kontami w kasach, dla danej firmy (np. Contoso) jest warunkiem wstępnym używania certyfikatów usługi Key Vault.  
    Następujące kluczowe dostawcy usług kryjucego kluczy są następującymi dostawcami współpracującymi z usługą Key Vault:  
    -   DigiCert - Key Vault oferuje certyfikaty OV TLS/SSL z DigiCert.  
    -   GlobalSign - Key Vault oferuje certyfikaty OV TLS/SSL z GlobalSign.  

**Krok 2** — administrator konta dla dostawcy urzędu certyfikacji tworzy poświadczenia, które mają być używane przez magazyn klucza do rejestrowania, odnawiania i używania certyfikatów TLS/SSL za pośrednictwem usługi Key Vault.

**Krok 3** — administrator contoso wraz z pracownikiem firmy Contoso (użytkownikm usługi Key Vault), który jest właścicielem certyfikatów, w zależności od urzędu certyfikacji, mogą uzyskać certyfikat od administratora lub bezpośrednio z konta w ucho.  

- Rozpocznij operację dodawania poświadczeń do magazynu kluczy, ustawiając zasób [wystawcy certyfikatu.](/rest/api/keyvault/setcertificateissuer/setcertificateissuer) Wystawca certyfikatu jest jednostką reprezentowaną w usłudze Azure Key Vault (KV) jako zasób CertificateIssuer. Służy do dostarczania informacji o źródle certyfikatu KV; nazwa wystawcy, dostawca, poświadczenia i inne szczegóły administracyjne.
  - Np. MyDigiCertIssuer  
    -   Dostawca  
    -   Poświadczenia — poświadczenia konta urzędu certyfikacji. Każdy urząd certyfikacji ma swoje własne dane.  

    Aby uzyskać więcej informacji na temat tworzenia kont u dostawców urzędu certyfikacji, zobacz powiązany wpis w [blogu Usługi Key Vault](https://aka.ms/kvcertsblog).  

**Krok 3.1** - [Konfigurowanie kontaktów certyfikatów](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) dla powiadomień. Jest to kontakt dla użytkownika usługi Key Vault. Usługa Key Vault nie wymusza tego kroku.  

Uwaga — ten proces, za pomocą kroku 3.1, jest operacją jednorazową.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Tworzenie certyfikatu z urzędem certyfikacji współpracującym z magazynem kluczy

![Tworzenie certyfikatu za pomocą urzędu certyfikacji partnerskiego usługi Key Vault](../media/certificate-authority-2.png)

**Krok 4** - Poniższe opisy odpowiadają zielonym ponumerowanym stopniom na poprzednim diagramie.  
  (1) - Na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.  
  (2) — magazyn kluczy wysyła żądanie certyfikatu TLS/SSL do urzędu certyfikacji.  
  (3) — twoje sondy aplikacji, w pętli i procesu oczekiwania, dla magazynu kluczy do ukończenia certyfikatu. Tworzenie certyfikatu kończy się, gdy usługa Key Vault otrzyma odpowiedź od urzędu certyfikacji z certyfikatem X.509.  
  (4) — urząd certyfikacji odpowiada na żądanie certyfikatu TLS/SSL usługi Key Vault certyfikatem X509 TLS/SSL.  
  (5) — Tworzenie nowego certyfikatu kończy się połączeniem certyfikatu X509 dla urzędu certyfikacji.  

  Użytkownik usługi Key Vault — tworzy certyfikat, określając zasadę

  -   Powtórz w razie potrzeby  
  -   Ograniczenia zasad  
      -   Właściwości X509  
      -   Kluczowe właściwości  
      -   Odwołanie do dostawcy - > ex. MyDigiCertIssure  
      -   Informacje o odnowieniu - > ex. 90 dni przed wygaśnięciem  

  - Proces tworzenia certyfikatu jest zwykle procesem asynchronizacyjnym i obejmuje sondowanie magazynu kluczy dla stanu operacji tworzenia certyfikatu.  
[Uzyskaj operację certyfikatu](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: ukończony, nie powiódł się z informacją o błędzie lub anulowany  
      -   Ze względu na opóźnienie do utworzenia można zainicjować operację anulowania. Anulowanie może, ale nie musi być skuteczne.  

## <a name="import-a-certificate"></a>Importowanie certyfikatu  
 Alternatywnie – certyfikat można zaimportować do usługi Key Vault — PFX lub PEM.  

 Certyfikat importu — wymaga, aby PEM lub PFX były na dysku i miały klucz prywatny. 
-   Należy określić: nazwę przechowalni i nazwę certyfikatu (zasada jest opcjonalna)

-   Pliki PEM / PFX zawierają atrybuty, które KV może przeanalizować i użyć do wypełniania zasad certyfikatów. Jeśli zasady certyfikatów są już określone, KV spróbuje dopasować dane z pliku PFX / PEM.  

-   Po zakończeniu importu kolejne operacje będą używać nowych zasad (nowych wersji).  

-   Jeśli nie ma żadnych dalszych operacji, pierwszą rzeczą, jaką robi Usługa Key Vault, jest wysłanie powiadomienia o wygaśnięciu. 

-   Ponadto użytkownik może edytować zasady, która działa w momencie importowania, ale zawiera wartości domyślne, w których nie określono żadnych informacji podczas importowania. Np. brak informacji o emitentze  

### <a name="formats-of-import-we-support"></a>Obsługiwane przez nas formaty importu
Obsługujemy następujący typ formatu pliku Importuj dla PEM. Pojedynczy certyfikat zakodowany pem wraz z zakodowanym, niezaszyfrowanym kluczem PKCS#8, który ma

-----BEGIN CERTIFICATE----- -----END CERTIFICATE-----

-----POCZYNAJ KLUCZ PRYWATNY----- -----NAKAZ KLUCZ PRYWATNY-----

Przy scalaniu certyfikatów obsługujemy 2 formaty oparte na PEM. Można scalić pojedynczy certyfikat zakodowany pkcs#8 lub zakodowany plik P7B zakodowany base64. -----BEGIN CERTIFICATE----- -----END CERTIFICATE-----

Obecnie nie obsługujemy kluczy EC w formacie PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Tworzenie certyfikatu z urzędem certyfikacji, który nie jest partnerem usługi Key Vault  
 Ta metoda umożliwia pracę z innymi urzędami certyfikacji niż dostawcy współpracujący z usługą Key Vault, co oznacza, że organizacja może pracować z wybranym urzędem certyfikacji.  

![Tworzenie certyfikatu z własnym urzędem certyfikacji](../media/certificate-authority-1.png)  

 Poniższe opisy kroków odpowiadają zielonymi literami kroków na poprzednim diagramie.  

  (1) - Na powyższym diagramie aplikacja tworzy certyfikat, który wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.  

  (2) — Usługa Key Vault zwraca do aplikacji żądanie podpisywania certyfikatów (CSR).  

  (3) - Aplikacja przekazuje CSR do wybranego urzędu certyfikacji.  

  (4) — wybrany urząd certyfikacji odpowiada certyfikatem X509.  

  (5) — aplikacja kończy tworzenie nowego certyfikatu połączeniem certyfikatu X509 z urzędu certyfikacji.

