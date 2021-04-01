---
title: Azure Active Directory urzędy certyfikacji
description: Lista zaufanych certyfikatów używanych na platformie Azure
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96859135"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Urzędy certyfikacji używane przez Azure Active Directory

> [!IMPORTANT]
> Informacje na tej stronie dotyczą tylko jednostek, które jawnie określają listę akceptowalnych urzędów certyfikacji. Takie rozwiązanie, znane jako Przypinanie certyfikatów, powinno być unikane, chyba że nie ma żadnych innych opcji.

Każda jednostka próbująca uzyskać dostęp do usług tożsamości Azure Active Directory (Azure AD) za pośrednictwem protokołów TLS/SSL zostanie wyświetlona przy użyciu certyfikatów z urzędów certyfikacji wymienionych poniżej. Jeśli jednostka ufa tym urzędom certyfikacji, może użyć certyfikatów do zweryfikowania tożsamości i autentyczności usług tożsamości i ustanowienia bezpiecznych połączeń.

Urzędy certyfikacji mogą być klasyfikowane do głównych urzędów certyfikacji i pośrednich urzędów certyfikacji. Zazwyczaj główne urzędy certyfikacji mają jeden lub więcej skojarzonych pośrednich urzędów certyfikacji. W tym artykule wymieniono główne urzędy certyfikacji używane przez usługi Azure AD Identity Services i pośrednie urzędy certyfikacji skojarzone z każdym z tych katalogów głównych. W przypadku każdego urzędu certyfikacji dodaliśmy Uniform Resource Identifiers (URI), aby pobrać skojarzony dostęp do informacji o urzędach (AIA) i pliki punktu dystrybucji listy odwołania certyfikatów (CDP). W razie potrzeby podajmy również identyfikator URI punktu końcowego protokołu stanu certyfikatów online (OCSP).

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>Urzędy certyfikacji używane w publicznych chmurze platformy Azure i na platformie Azure dla instytucji rządowych

Różne usługi mogą korzystać z różnych głównych lub pośrednich urzędów certyfikacji. W związku z tym mogą być wymagane wszystkie wpisy wymienione poniżej.

### <a name="digicert-global-root-g2"></a>DigiCert Global root — G2


| Główny urząd certyfikacji| Numer seryjny| Data wygaśnięcia daty wydania| Odcisk palca SHA1| Identyfikatory URI |
| - |- |-|-|-|-|
| DigiCert Global root — G2| 033af1e6a711a 9a0bb2864b11d09fae5| 1 sierpnia 2013 <br>15 stycznia 2038| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>Skojarzone pośrednich urzędów certyfikacji

| Wystawianie i pośredni urząd certyfikacji| Numer seryjny| Data wygaśnięcia daty wydania| Odcisk palca SHA1| Identyfikatory URI |
| - | - | - | - | - | 
| Microsoft Azure protokołu TLS wystawiającego certyfikaty 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 29 lipca 2020<br>27 czerwca 2024| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|Microsoft Azure protokołu TLS wystawiającego certyfikaty 02| 0c6ae97cced59983 8690a00a9ea53214| 29 lipca 2020<br>27 czerwca 2024| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure protokołu TLS wystawiającego certyfikaty 05| 0d7bede97d8209967a 52631b8bdd18bd| 29 lipca 2020<br>27 czerwca 2024| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure protokół TLS wystawiającego certyfikaty 06| 02e79171fb8021e93fe 2d983834c50c0| 29 lipca 2020<br>27 czerwca 2024| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust, główny

| Główny urząd certyfikacji| Numer seryjny| Data wygaśnięcia daty wydania| Odcisk palca SHA1| Identyfikatory URI |
| - | - | - | - | - | 
| Baltimore CyberTrust, główny| 020000b9| 12 maja 2000<br>12 maja 2025| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[KOLEJCE](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Skojarzone pośrednich urzędów certyfikacji

| Wystawianie i pośredni urząd certyfikacji| Numer seryjny| Data wygaśnięcia daty wydania| Odcisk palca SHA1| Identyfikatory URI |
| - | - | - | - | - | 
| Urząd certyfikacji RSA TLS 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 21 lipca 2020 r.<br>8 października 2024| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[KOLEJCE](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 21 lipca 2020 r.<br>20 maja 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[KOLEJCE](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>Globalny główny urząd certyfikacji DigiCert

| Główny urząd certyfikacji| Numer seryjny| Data wygaśnięcia daty wydania| Odcisk palca SHA1| Identyfikatory URI |
| - | - | - | - | - | 
| Globalny główny urząd certyfikacji DigiCert| 083be056904246 b1a1756ac95991c74a| 9 listopada, 2006<br>9 listopada, 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[KOLEJCE](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>Skojarzone pośrednich urzędów certyfikacji

| Wystawianie i pośredni urząd certyfikacji| Numer seryjny| Data wygaśnięcia daty wydania| Odcisk palca SHA1| Identyfikatory URI |
| - | - | - | - | - |
| DigiCert algorytmu SHA2 bezpieczny serwer urzędu certyfikacji| 01fda3eb6eca75c 888438b724bcfbc91| 8 marca, 2013 marca, 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[KOLEJCE](http://ocsp.digicert.com/) |
| DigiCert algorytmu SHA2 bezpieczny serwer urzędu certyfikacji |02742eaa17ca8e21 c717bb1ffcfd0ca0 |22 września, 2020<br>22 września, 2030|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[KOLEJCE](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>Urzędy certyfikacji używane na platformie Azure (Chiny) w chmurze

### <a name="digicert-global-root-ca"></a>Globalny główny urząd certyfikacji DigiCert


| Główny urząd certyfikacji| Numer seryjny| Data wygaśnięcia daty wydania| Odcisk palca SHA1| Identyfikatory URI |
| - | - | - | - | - |
| Globalny główny urząd certyfikacji DigiCert| 083be056904246b 1a1756ac95991c74a| Lis. 9, 2006<br>Lis. 9, 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[KOLEJCE](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>Skojarzony pośredni urząd certyfikacji

| Wystawianie i pośredni urząd certyfikacji| Numer seryjny| Data wygaśnięcia daty wydania| Odcisk palca SHA1| Identyfikatory URI |
| - | - | - | - | - | - |
| DigiCert Basic RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 4 marca 2020<br>4 marca 2030| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[KOLEJCE](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Następne kroki
[Informacje o Microsoft 365 łańcuchach szyfrowania](/microsoft-365/compliance/encryption-office-365-certificate-chains)
