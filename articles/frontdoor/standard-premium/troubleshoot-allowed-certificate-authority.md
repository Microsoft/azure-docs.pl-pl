---
title: Dozwolone urzędy certyfikacji dla usług Azure Front-Standard/Premium (wersja zapoznawcza)
description: Ten artykuł zawiera listę wszystkich urzędów certyfikacji dozwolonych podczas tworzenia własnego certyfikatu.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100237"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Dozwolone urzędy certyfikacji dla usług Azure Front-Standard/Premium (wersja zapoznawcza)

Po włączeniu funkcji HTTPS przy użyciu własnego certyfikatu dla niestandardowej domeny Standard/Premium platformy Azure. Do utworzenia certyfikatu TLS/SSL wymagany jest dozwolony urząd certyfikacji (CA). W przeciwnym razie, jeśli używasz niedozwolonego urzędu certyfikacji lub certyfikatu z podpisem własnym, żądanie zostanie odrzucone.

Podczas tworzenia własnego certyfikatu można używać następujących urzędów certyfikacji:

- $ Ufaj zewnętrznym certyfikatem głównym urzędu certyfikacji
- Główny urząd certyfikacji AlphaSSL
- AZWA infrastruktura CA 01
- AZWA infrastruktura urzędu certyfikacji 02
- Ameroot
- APCA-DM3P
- ATOS TrustedRoot 2011
- Główny urząd certyfikacji autopilotażu
- Baltimore CyberTrust, główny
- Publiczny podstawowy urząd certyfikacji klasy 3
- Urząd certyfikacji RSA COMODO
- COMODO RSA — weryfikacja domeny bezpieczny serwer urzędu certyfikacji
- D — zaufanie klasy głównej 3 urzędu certyfikacji 2 2009
- DigiCert Cloud Services CA-1
- Globalny główny urząd certyfikacji DigiCert
- DigiCert Global root — G2
- Urząd certyfikacji DigiCert (High Assurance) — 3
- Główny urząd certyfikacji EV DigiCert o wysokim poziomie zabezpieczeń
- Urząd certyfikacji serwera rozszerzonej weryfikacji DigiCert algorytmu SHA2
- Urząd certyfikacji serwera DigiCert algorytmu SHA2 High Assurance
- DigiCert algorytmu SHA2 bezpieczny serwer urzędu certyfikacji
- Główny urząd certyfikacji DST
- D — zaufanie klasy głównej 3 urzędu certyfikacji 2 2009
- Szyfrowanie wszędzie w przypadku cyfrowych urzędów certyfikacji TLS
- Powierz główny urząd certyfikacji
- Powierz główny urząd certyfikacji — G2
- Urząd certyfikacji Entrust.net (2048)
- Globalny urząd certyfikacji geozaufanej
- Podstawowy urząd certyfikacji geozaufanej
- Podstawowy urząd certyfikacji geozaufanej — G2
- Certyfikat GeoTrust RSA CA 2018
- GlobalSign
- GlobalSign rozszerzona Walidacja CA-SHA256-G2
- GlobalSign weryfikacji organizacji CA — G2
- Główny urząd certyfikacji GlobalSign
- Przejdź do głównego urzędu certyfikacji Daddy — G2
- Przejdź do Daddy bezpiecznego urzędu certyfikacji — G2
- Szyfrujmy Źródło x3
- Mikrosek e-Szigno główny urząd certyfikacji 2009
- QuoVadis główny CA2 G3
- RapidSSL RSA CA 2018
- RootCA1 komunikacji zabezpieczeń
- RootCA2 komunikacji zabezpieczeń
- RootCA3 komunikacji zabezpieczeń
- Symantec Class 3 EV — urząd certyfikacji SSL-G3
- Symantec Class 3 Secure Server CA-G4
- Główny certyfikat firmy Symantec Enterprise Mobile dla firmy Microsoft
- Podstawowy główny urząd certyfikacji Thawte
- Thawte podstawowy główny urząd certyfikacji — G2
- Thawte podstawowy główny urząd certyfikacji — G3
- Thawte RSA CA 2018
- Thawte sygnatura czasowa urzędu certyfikacji
- TrustAsia TLS — urząd certyfikacji RSA
- Urząd certyfikacji rozszerzonej weryfikacji certyfikatu VeriSign Class 3
- VeriSign Class 3 — rozszerzona Walidacja — urząd certyfikacji SGC
- VeriSign Class 3 Public głównego urzędu certyfikacji — G5
- Międzynarodowy serwer urzędu certyfikacji VeriSign — Klasa 3
- Katalog główny usługi sygnatur czasowych VeriSign
- Uniwersalny główny urząd certyfikacji VeriSign
