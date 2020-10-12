---
title: Metody tworzenia certyfikatów
description: Więcej informacji na temat różnych opcji tworzenia lub importowania certyfikatu Key Vault w programie Azure Key Vault. Istnieje kilka sposobów tworzenia certyfikatu Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 71d8961af372f927fab909073daa715766542f87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606663"
---
# <a name="certificate-creation-methods"></a>Metody tworzenia certyfikatów

 Certyfikat Key Vault (KV) można utworzyć lub zaimportować do magazynu kluczy. Po utworzeniu certyfikatu KV klucz prywatny jest tworzony w magazynie kluczy i nigdy nie jest narażony na właściciela certyfikatu. Poniżej przedstawiono sposoby tworzenia certyfikatu w Key Vault:  

-   **Utwórz certyfikat z podpisem własnym:** Spowoduje to utworzenie pary kluczy publiczny-prywatny i skojarzenie jej z certyfikatem. Certyfikat zostanie podpisany przy użyciu własnego klucza.  

-    **Utwórz ręcznie nowy certyfikat:** Spowoduje to utworzenie pary kluczy publiczny-prywatny i wygenerowanie żądania podpisania certyfikatu X. 509. Żądanie podpisania może być podpisane przez Urząd rejestrowania lub urząd certyfikacji. Podpisany certyfikat x509 można scalić z parą kluczy oczekujących w celu ukończenia certyfikatu KV w Key Vault. Chociaż ta metoda wymaga większej liczby kroków, zapewnia większe bezpieczeństwo, ponieważ klucz prywatny jest tworzony w systemie i jest ograniczony do Key Vault. Wyjaśniono to na poniższym diagramie.  

![Tworzenie certyfikatu przy użyciu własnego urzędu certyfikacji](../media/certificate-authority-1.png)  

Poniższe opisy odnoszą się do zieloną literę kroków z poprzedniego diagramu.

1. Na powyższym diagramie w aplikacji tworzony jest certyfikat, co wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Key Vault zwraca do aplikacji żądanie podpisania certyfikatu (CSR)
3. Twoja aplikacja przekazuje żądanie CSR do wybranego urzędu certyfikacji.
4. Wybrany urząd certyfikacji odpowiada za pomocą certyfikatu x509.
5. Twoja aplikacja kończy Tworzenie nowego certyfikatu z połączeniem certyfikatu x509 z urzędu certyfikacji.

-   **Utwórz certyfikat dla znanego dostawcy wystawcy:** Ta metoda wymaga wykonania jednorazowego zadania tworzenia obiektu wystawcy. Po utworzeniu obiektu wystawcy w magazynie kluczy jego nazwy można odwoływać się w zasadach certyfikatu KV. Żądanie utworzenia takiego certyfikatu z KV spowoduje utworzenie pary kluczy w magazynie i skontaktowanie się z usługą dostawcy wystawcy przy użyciu informacji zawartych w obiekcie wystawcy odwołania w celu uzyskania certyfikatu x509. Certyfikat x509 zostanie pobrany z usługi wystawcy i zostanie scalony z parą kluczy, aby ukończyć tworzenie certyfikatu KV.  

![Tworzenie certyfikatu za pomocą Key Vault partnerskiego urzędu certyfikacji](../media/certificate-authority-2.png)  

Poniższe opisy odnoszą się do zieloną literę kroków z poprzedniego diagramu.

1. Na powyższym diagramie w aplikacji tworzony jest certyfikat, co wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Key Vault wysyła do urzędu certyfikacji żądanie certyfikatu TLS/SSL.
3. Aplikacja przeprowadza sondowanie w procesie pętli i oczekiwania na zakończenie tworzenia certyfikatu w usłudze Key Vault. Tworzenie certyfikatu kończy się, gdy usługa Key Vault otrzyma odpowiedź od urzędu certyfikacji z certyfikatem X.509.
4. Urząd certyfikacji odpowiada na żądanie certyfikatu TLS/SSL Key Vault z certyfikatem protokołu TLS/SSL X. 509.
5. Tworzenie nowego certyfikatu kończy się z połączeniem certyfikatu X. 509 protokołu TLS/SSL dla urzędu certyfikacji.

## <a name="asynchronous-process"></a>Proces asynchroniczny
Tworzenie certyfikatu KV jest procesem asynchronicznym. Ta operacja spowoduje utworzenie żądania certyfikatu KV i zwrócenie kodu stanu HTTP 202 (zaakceptowane). Stan żądania może być śledzony przez sondowanie obiektu oczekującego utworzonego przez tę operację. Pełny identyfikator URI oczekującego obiektu jest zwracany w nagłówku lokalizacji.  

Gdy żądanie utworzenia certyfikatu KV zakończy się, stan oczekującego obiektu zmieni się na "zakończone" z "w toku" i zostanie utworzona nowa wersja certyfikatu KV. Stanie się to bieżąca wersja.  

## <a name="first-creation"></a>Pierwsze tworzenie
 Po utworzeniu certyfikatu KV po raz pierwszy jest tworzony również klucz adresu i wpis tajny o tej samej nazwie co certyfikat. Jeśli nazwa jest już używana, operacja zakończy się niepowodzeniem z kodem stanu HTTP 409 (konflikt).
Klucz z adresami i klucz tajny otrzymują atrybuty z atrybutów certyfikatu KV. Klucz i wpis tajny, które zostały utworzone w ten sposób, są oznaczone jako klucze zarządzane i wpisy tajne, których okres istnienia jest zarządzany przez Key Vault. Klucze zarządzane i wpisy tajne są tylko do odczytu. Uwaga: Jeśli certyfikat KV wygaśnie lub jest wyłączony, odpowiadający mu klucz i wpis tajny staną się nieobsługiwane.  

 Jeśli jest to pierwsza operacja utworzenia certyfikatu KV, wymagane są zasady.  Można również dostarczyć zasady z kolejnymi operacjami tworzenia, aby zastąpić zasób zasad. Jeśli nie podano zasad, zasób zasad usługi jest używany do tworzenia kolejnej wersji certyfikatu KV. Należy pamiętać, że podczas gdy żądanie utworzenia następnej wersji jest w toku, bieżący certyfikat KV i odpowiedni klucz adresu i wpis tajny pozostają bez zmian.  

## <a name="self-issued-certificate"></a>Certyfikat wystawiony automatycznie
 Aby utworzyć certyfikat wystawiony automatycznie, należy ustawić nazwę wystawcy jako "własny" w zasadach certyfikatów, jak pokazano w poniższym fragmencie kodu z zasad certyfikatów.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Jeśli nazwa wystawcy nie zostanie określona, nazwa wystawcy jest ustawiona na wartość "nieznany". Gdy wystawca to "nieznany", właściciel certyfikatu będzie musiał ręcznie uzyskać certyfikat x509 od wystawcy jego wyboru, a następnie scalić publiczny certyfikat x509 z obiektem oczekującym certyfikatu magazynu kluczy w celu ukończenia tworzenia certyfikatu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Dostawcy partnerskich urzędów certyfikacji
Tworzenie certyfikatu można wykonać ręcznie lub przy użyciu wystawcy "samodzielnie". Key Vault również partnerów z niektórymi dostawcami wystawcy, aby uprościć tworzenie certyfikatów. Następujące typy certyfikatów mogą być uporządkowane dla magazynu kluczy z tymi dostawcami wystawcy partnera.  

|Dostawca|Typ certyfikatu|Konfiguracja konfiguracji  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault oferuje certyfikaty SSL OV lub EV z DigiCert| [Przewodnik po integracji](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)
|GlobalSign|Key Vault oferuje certyfikaty SSL OV lub EV z GlobalSign| [Przewodnik po integracji](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Wystawca certyfikatu jest jednostką reprezentowaną w Azure Key Vault (KV) jako zasób CertificateIssuer. Służy do przekazywania informacji o źródle certyfikatu KV; Nazwa wystawcy, dostawca, poświadczenia i inne szczegóły administracyjne.

Należy pamiętać, że gdy zamówienie jest umieszczane u dostawcy wystawcy, może zaistnieć lub zastąpić rozszerzenia certyfikatu x509 oraz okres ważności certyfikatu na podstawie typu certyfikatu.  

 Autoryzacja: wymaga uprawnienia certyfikaty/tworzenie.

## <a name="see-also"></a>Zobacz też

 - [Monitorowanie tworzenia certyfikatów i zarządzanie nim](create-certificate-scenarios.md)
