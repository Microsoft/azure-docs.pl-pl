---
title: Metody tworzenia certyfikatów
description: Sposoby tworzenia certyfikatu w magazynie kluczy.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7450dd79247078afe02d1bb63727cfd260d674fc
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866252"
---
# <a name="certificate-creation-methods"></a>Metody tworzenia certyfikatów

 Certyfikat magazynu kluczy (KV) można utworzyć lub zaimportować do magazynu kluczy. Po utworzeniu certyfikatu KV klucz prywatny jest tworzony wewnątrz magazynu kluczy i nigdy nie jest narażony na działanie właściciela certyfikatu. Poniżej przedstawiono sposoby tworzenia certyfikatu w magazynie kluczy:  

-   **Tworzenie certyfikatu z podpisem własnym:** Spowoduje to utworzenie pary kluczy publiczno-prywatnych i skojarzenie jej z certyfikatem. Certyfikat zostanie podpisany własnym kluczem.  

-    **Ręcznie utwórz nowy certyfikat:** Spowoduje to utworzenie pary kluczy publiczno-prywatnych i wygenerowanie żądania podpisywania certyfikatów X.509. Żądanie podpisywania może być podpisane przez urząd rejestracji lub urząd certyfikacji. Podpisany certyfikat x509 można scalić z oczekującą parą kluczy, aby ukończyć certyfikat KV w Magazynie kluczy. Mimo że ta metoda wymaga więcej kroków, zapewnia większe bezpieczeństwo, ponieważ klucz prywatny jest tworzony i ograniczony do usługi Key Vault. Jest to wyjaśnione na poniższym diagramie.  

![Tworzenie certyfikatu z własnym urzędem certyfikacji](../media/certificate-authority-1.png)  

Poniższe opisy odpowiadają zielonym krokom na poprzednim diagramie.

1. Na powyższym diagramie w aplikacji tworzony jest certyfikat, co wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Magazyn kluczy zwraca do aplikacji żądanie podpisywania certyfikatów (CSR)
3. Twoja aplikacja przekazuje żądanie CSR do wybranego urzędu certyfikacji.
4. Wybrany urząd certyfikacji odpowiada certyfikatem X509.
5. Aplikacja kończy tworzenie nowego certyfikatu połączeniem certyfikatu X509 z urzędu certyfikacji.

-   **Utwórz certyfikat u znanego dostawcy wystawcy:** Ta metoda wymaga wykonania jednorazowego zadania tworzenia obiektu wystawcy. Po utworzeniu obiektu wystawcy w magazynie kluczy jego nazwę można odwoływać się do zasad certyfikatu KV. Żądanie utworzenia takiego certyfikatu KV spowoduje utworzenie pary kluczy w magazynie i komunikowanie się z usługą dostawcy wystawcy przy użyciu informacji w obiekcie wystawcy, do którego istnieje odwołanie, w celu uzyskania certyfikatu x509. Certyfikat x509 jest pobierany z usługi wystawcy i jest scalany z parą kluczy w celu ukończenia tworzenia certyfikatu KV.  

![Tworzenie certyfikatu za pomocą urzędu certyfikacji partnerskiego usługi Key Vault](../media/certificate-authority-2.png)  

Poniższe opisy odpowiadają zielonym krokom na poprzednim diagramie.

1. Na powyższym diagramie w aplikacji tworzony jest certyfikat, co wewnętrznie zaczyna się od utworzenia klucza w magazynie kluczy.
2. Magazyn kluczy wysyła żądanie certyfikatu TLS/SSL do urzędu certyfikacji.
3. Aplikacja przeprowadza sondowanie w procesie pętli i oczekiwania na zakończenie tworzenia certyfikatu w usłudze Key Vault. Tworzenie certyfikatu kończy się, gdy usługa Key Vault otrzyma odpowiedź od urzędu certyfikacji z certyfikatem X.509.
4. Urząd certyfikacji odpowiada na żądanie certyfikatu TLS/SSL usługi Key Vault za pomocą certyfikatu TLS/SSL X.509.
5. Tworzenie nowego certyfikatu kończy się połączeniem certyfikatu TLS/SSL X.509 dla urzędu certyfikacji.

## <a name="asynchronous-process"></a>Proces asynchroniczne
Tworzenie certyfikatu KV jest procesem asynchronialnym. Ta operacja utworzy żądanie certyfikatu KV i zwróci kod stanu http 202 (Zaakceptowany). Stan żądania można śledzić przez sondowanie oczekującego obiektu utworzonego przez tę operację. Pełny identyfikator URI oczekującego obiektu jest zwracany w nagłówku LOKALIZACJA.  

Po zakończeniu żądania utworzenia certyfikatu KV stan oczekującego obiektu zmieni się na "ukończony" z "inprogress" i zostanie utworzona nowa wersja certyfikatu KV. Stanie się to bieżącą wersją.  

## <a name="first-creation"></a>Pierwsze stworzenie
 Gdy certyfikat KV jest tworzony po raz pierwszy, klucz adresowa i klucz tajny jest również tworzony o takiej samej nazwie jak certyfikat. Jeśli nazwa jest już w użyciu, operacja zakończy się niepowodzeniem z kodem stanu http 409 (konflikt).
Adresowalny klucz i klucz tajny otrzymują swoje atrybuty z atrybutów certyfikatu KV. Adresowalny klucz i klucz tajny utworzone w ten sposób są oznaczone jako klucze zarządzane i wpisy tajne, których okres istnienia jest zarządzany przez magazyn kluczy. Klucze zarządzane i wpisy tajne są tylko do odczytu. Uwaga: Jeśli certyfikat KV wygaśnie lub zostanie wyłączony, odpowiedni klucz i klucz tajny staną się niesprawne.  

 Jeśli jest to pierwsza operacja utworzenia certyfikatu KV, wymagana jest zasada.  Zasady mogą być również dostarczane z kolejnych operacji tworzenia zastąpić zasób zasad. Jeśli zasady nie są dostarczane, a następnie zasób zasad w usłudze jest używany do tworzenia następnej wersji certyfikatu KV. Należy zauważyć, że gdy żądanie utworzenia następnej wersji jest w toku, bieżący certyfikat KV i odpowiadający mu adresowalny klucz i klucz tajny pozostają niezmienione.  

## <a name="self-issued-certificate"></a>Certyfikat wydany samodzielnie
 Aby utworzyć certyfikat wystawiony samodzielnie, ustaw nazwę wystawcy jako "Self" w zasadach certyfikatów, jak pokazano w poniższym fragmentie kodu z zasad certyfikatów.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Jeśli nazwa wystawcy nie jest określona, nazwa wystawcy jest ustawiona na "Nieznany". Gdy wystawca jest "Nieznany", właściciel certyfikatu będzie musiał ręcznie uzyskać certyfikat x509 od wybranego przez niego wystawcy, a następnie scalić publiczny certyfikat x509 z oczekującym obiektem magazynu kluczy, aby zakończyć tworzenie certyfikatu.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Dostawcy współpracujących urzędów certyfikacji
Tworzenie certyfikatów można wykonać ręcznie lub za pomocą wystawcy "Self". Key Vault współpracuje również z niektórymi dostawcami wystawców, aby uprościć tworzenie certyfikatów. Następujące typy certyfikatów można zamówić dla magazynu kluczy z tymi dostawcami wystawców partnerów.  

|Dostawca|Typ certyfikatu|Konfiguracja konfiguracji  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault oferuje certyfikaty OV lub EV SSL z DigiCert| [Przewodnik integracji](https://docs.digicert.com/certificate-tools/azure-key-vault-integration-guide/)
|GlobalSign|Key Vault oferuje certyfikaty OV lub EV SSL z GlobalSign| [Przewodnik integracji](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Wystawca certyfikatu jest jednostką reprezentowaną w usłudze Azure Key Vault (KV) jako zasób CertificateIssuer. Służy do dostarczania informacji o źródle certyfikatu KV; nazwa wystawcy, dostawca, poświadczenia i inne szczegóły administracyjne.

Należy zauważyć, że gdy zamówienie jest składane u dostawcy wystawcy, może ono honorować lub zastępować rozszerzenia certyfikatów x509 i okres ważności certyfikatu na podstawie typu certyfikatu.  

 Autoryzacja: Wymaga certyfikatów/uprawnień do tworzenia.

## <a name="see-also"></a>Zobacz też

 - [Monitorowanie tworzenia certyfikatów i zarządzanie tym procesem](create-certificate-scenarios.md)
