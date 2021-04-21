---
title: Metody tworzenia certyfikatów
description: Dowiedz się więcej o różnych opcjach tworzenia lub importowania certyfikatu Key Vault w Azure Key Vault. Istnieje kilka sposobów tworzenia Key Vault certyfikatu.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 72ff2a1a7b8bcff768248833183ce03a169f9a4d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752124"
---
# <a name="certificate-creation-methods"></a>Metody tworzenia certyfikatów

 Certyfikat Key Vault (KV) można utworzyć lub zaimportować do magazynu kluczy. Po utworzeniu certyfikatu KV klucz prywatny jest tworzony wewnątrz magazynu kluczy i nigdy nie jest narażony właścicielowi certyfikatu. Poniżej przedstawiono sposoby tworzenia certyfikatu w programie Key Vault:  

-   **Utwórz certyfikat z podpisem własnym:** Spowoduje to utworzenie pary kluczy publiczny-prywatny i skojarzenie jej z certyfikatem. Certyfikat zostanie podpisany za pomocą własnego klucza.  

-    **Utwórz nowy certyfikat ręcznie:** Spowoduje to utworzenie pary kluczy publiczny-prywatny i wygenerowanie żądania podpisania certyfikatu X.509. Żądanie podpisania może zostać podpisane przez twój urząd rejestrowania lub urząd certyfikacji. Podpisany certyfikat x509 można scalić z parą kluczy oczekujących, aby ukończyć certyfikat KV w Key Vault. Mimo że ta metoda wymaga większej liczby kroków, zapewnia większe bezpieczeństwo, ponieważ klucz prywatny jest tworzony w programie i jest ograniczony Key Vault. Wyjaśniono to na poniższym diagramie.  

![Tworzenie certyfikatu przy użyciu własnego urzędu certyfikacji](../media/certificate-authority-1.png)  

Poniższe opisy odpowiadają krokom z zielonymi literami na poprzednim diagramie.

1. Na powyższym diagramie w aplikacji tworzony jest certyfikat, co wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Key Vault aplikacji zwraca żądanie podpisania certyfikatu (CSR)
3. Twoja aplikacja przekazuje żądanie CSR do wybranego urzędu certyfikacji.
4. Wybrany urząd certyfikacji odpowiada certyfikatem X509.
5. Aplikacja kończy tworzenie nowego certyfikatu przy użyciu połączenia certyfikatu X509 z urzędu certyfikacji.

-   **Utwórz certyfikat przy użyciu znanego dostawcy wystawcy:** Ta metoda wymaga wykonania jednego zadania w celu utworzenia obiektu wystawcy. Po utworzeniu obiektu wystawcy w magazynie kluczy można odwoływać się do jego nazwy w zasadach certyfikatu KV. Żądanie utworzenia takiego certyfikatu KV spowoduje utworzenie pary kluczy w magazynie i komunikację z usługą dostawcy wystawców przy użyciu informacji w obiekcie wystawcy, do których się odwołujesz, aby uzyskać certyfikat x509. Certyfikat x509 jest pobierany z usługi wystawcy i scalony z parą kluczy w celu ukończenia tworzenia certyfikatu KV.  

![Tworzenie certyfikatu za pomocą Key Vault partnerskiego urzędu certyfikacji](../media/certificate-authority-2.png)  

Poniższe opisy odpowiadają krokom z zielonymi literami na poprzednim diagramie.

1. Na powyższym diagramie w aplikacji tworzony jest certyfikat, co wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Key Vault wysyła żądanie certyfikatu TLS/SSL do urzędu certyfikacji.
3. Aplikacja przeprowadza sondowanie w procesie pętli i oczekiwania na zakończenie tworzenia certyfikatu w usłudze Key Vault. Tworzenie certyfikatu kończy się, gdy usługa Key Vault otrzyma odpowiedź od urzędu certyfikacji z certyfikatem X.509.
4. Urząd certyfikacji odpowiada na Key Vault certyfikatu TLS/SSL przy użyciu certyfikatu TLS/SSL X.509.
5. Tworzenie nowego certyfikatu kończy się połączeniem certyfikatu TLS/SSL X.509 dla urzędu certyfikacji.

## <a name="asynchronous-process"></a>Proces asynchroniczny
Tworzenie certyfikatu KV jest procesem asynchronicznym. Ta operacja spowoduje utworzenie żądania certyfikatu KV i zwrócenie kodu stanu HTTP 202 (Zaakceptowane). Stan żądania można śledzić, sondując oczekujący obiekt utworzony przez tę operację. Pełny identyfikator URI oczekującego obiektu jest zwracany w nagłówku LOCATION.  

Po zakończeniu żądania utworzenia certyfikatu KV stan oczekującego obiektu zmieni się na "ukończony" z "w toku" i zostanie utworzona nowa wersja certyfikatu KV. Stanie się to bieżącą wersją.  

## <a name="first-creation"></a>Pierwsze utworzenie
 Podczas tworzenia certyfikatu KV po raz pierwszy adresowalny klucz i klucz tajny są również tworzone z taką samą nazwą jak certyfikat. Jeśli nazwa jest już w użyciu, operacja nie powiedzie się z kodem stanu HTTP 409 (konflikt).
Adresowalny klucz i klucz tajny uzyskają atrybuty z atrybutów certyfikatu KV. Adresowalny klucz i wpis tajny utworzone w ten sposób są oznaczone jako zarządzane klucze i wpisy tajne, których okres istnienia jest zarządzany przez Key Vault. Zarządzane klucze i wpisy tajne są tylko do odczytu. Uwaga: Jeśli certyfikat KV wygaśnie lub zostanie wyłączony, odpowiedni klucz i klucz tajny staną się niedziałalne.  

 Jeśli jest to pierwsza operacja tworzenia certyfikatu KV, wymagane są zasady.  Zasady można również dostarczać z kolejnymi operacjami tworzenia, aby zastąpić zasób zasad. Jeśli zasady nie są podane, zasób zasad w usłudze jest używany do tworzenia następnej wersji certyfikatu KV. Należy pamiętać, że gdy żądanie utworzenia następnej wersji jest w toku, bieżący certyfikat KV oraz odpowiedni adresowalny klucz i klucz tajny pozostaną niezmienione.  

## <a name="self-issued-certificate"></a>Certyfikat wystawiony samodzielnie
 Aby utworzyć certyfikat wystawiony samodzielnie, ustaw nazwę wystawcy na "Self" w zasadach certyfikatów, jak pokazano w poniższym fragmencie kodu z zasad certyfikatów.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Jeśli nazwa wystawcy nie zostanie określona, nazwa wystawcy zostanie ustawiona na "Nieznany". Gdy wystawca ma "Nieznany", właściciel certyfikatu będzie musiał ręcznie uzyskać certyfikat x509 od wybranego wystawcy, a następnie scalić publiczny certyfikat x509 z obiektem oczekującym na certyfikat magazynu kluczy, aby ukończyć tworzenie certyfikatu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Dostawcy partnerskiego urzędu certyfikacji
Tworzenie certyfikatu można wykonać ręcznie lub przy użyciu wystawcy "Self". Key Vault również współpracuje z niektórymi dostawcami wystawców, aby uprościć tworzenie certyfikatów. Następujące typy certyfikatów można zamówić dla magazynu kluczy u tych dostawców wystawców partnerów.  

|Dostawca|Typ certyfikatu|Konfiguracja  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault oferuje certyfikaty SSL OV lub EV za pomocą firmy DigiCert| [Przewodnik integracji](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault oferuje certyfikaty SSL OV lub EV z globalSign| [Przewodnik integracji](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Wystawca certyfikatu to jednostka reprezentowana w Azure Key Vault (KV) jako zasób CertificateIssuer. Służy do podania informacji o źródle certyfikatu KV; nazwa wystawcy, dostawca, poświadczenia i inne szczegóły administracyjne.

Należy pamiętać, że gdy zamówienie zostanie złożone u dostawcy wystawcy, może ono honorować lub zastępować rozszerzenia certyfikatów x509 i okres ważności certyfikatu na podstawie typu certyfikatu.  

 Autoryzacja: wymaga certyfikatów/uprawnień do tworzenia.

## <a name="see-also"></a>Zobacz też

 - Przewodnik dotyczący tworzenia certyfikatów w usłudze Key Vault [portal,](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal)interfejs wiersza polecenia platformy [Azure,](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-cli) [Azure PowerShell](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-powershell)
 - [Monitorowanie tworzenia certyfikatów i zarządzanie nim](create-certificate-scenarios.md)
