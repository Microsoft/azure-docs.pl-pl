---
title: Co nowego w usłudze Azure cache for Redis
description: Najnowsze aktualizacje usługi Azure cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91492538"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Co nowego w usłudze Azure cache for Redis

## <a name="azure-tls-certificate-change"></a>Zmiana certyfikatu protokołu TLS platformy Azure

Firma Microsoft aktualizuje usługi platformy Azure, aby używać certyfikatów serwera TLS z innego zestawu urzędów certyfikacji. Ta zmiana jest wprowadzana w fazach od 13 sierpnia 2020 do 26 października 2020 (szacowane). Platforma Azure wprowadza tę zmianę, ponieważ [bieżące certyfikaty urzędu certyfikacji nie są zgodne z jednym z wymagań linii bazowej urzędu certyfikacji/przeglądarki](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951). Ten problem został zgłoszony w dniu 1 lipca 2020 i dotyczy wielu popularnych dostawców infrastruktury kluczy publicznych (PKI) na całym świecie. Większość certyfikatów TLS używanych przez usługi platformy Azure jest obecnie dostępna w głównej infrastrukturze PKI *Baltimore CyberTrust* . Usługa Pamięć podręczna systemu Azure dla usługi Redis będzie nadal łańcuchować do głównego katalogu Baltimore CyberTrust. Certyfikaty serwera TLS są jednak wystawiane przez nowe pośrednich urzędów certyfikacji (ICAs), począwszy od 12 października 2020.

> [!NOTE]
> Ta zmiana jest ograniczona do usług w publicznych [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Wyklucza suwerenne (np. Chiny) lub chmury rządowe.
>
>

### <a name="does-this-change-affect-me"></a>Czy ta zmiana wpłynie na mnie?

Oczekujemy, że ta zmiana nie ma wpływu na większość pamięci podręcznej platformy Azure dla klientów Redis. Może to mieć wpływ na aplikację, jeśli jawnie określi listę akceptowalnych certyfikatów, a także rolę "Przypinanie certyfikatów". Jeśli zostanie on przypięty do certyfikatu pośredniego lub liścia zamiast elementu głównego Baltimore CyberTrust, należy **podjąć natychmiastowe akcje** zmiany konfiguracji certyfikatu.

Poniższa tabela zawiera informacje o wycofywanych certyfikatach. W zależności od tego, który certyfikat jest używany przez aplikację, może być konieczne jego zaktualizowanie, aby zapobiec utracie łączności z wystąpieniem usługi Azure cache for Redis.

| Typ urzędu certyfikacji | Current | Post kroczące (12 października 2020) | Akcja |
| ----- | ----- | ----- | ----- |
| Główny | Odcisk palca: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> Wygaśnięcie: poniedziałek, 12 maja, 2025, 4:59:00 PM<br><br> Nazwa podmiotu:<br> CN = Baltimore CyberTrust root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | Nie zmienia | Brak |
| Elementów pośredniczących | Odciski palca<br> CN = Microsoft IT TLS CA 1<br> Odcisk palca: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> Odcisk palca: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> Odcisk palca: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> Odcisk palca: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> Wygaśnięcie: piątek, maj 20, 2024 5:52:38 AM<br><br> Nazwa podmiotu:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Waszyngton<br> C = US<br> | Odciski palca<br> CN = Microsoft RSA TLS CA 01<br> Odcisk palca: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> Odcisk palca: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> Wygaśnięcie: wtorek, 8 października 2024 12:00:00 AM;<br><br> Nazwa podmiotu:<br> O = Microsoft Corporation<br> C = US<br> | Wymagane |

### <a name="what-actions-should-i-take"></a>Jakie akcje należy wykonać?

Jeśli aplikacja używa magazynu certyfikatów systemu operacyjnego lub przypina element Baltimore do innych użytkowników, nie jest wymagana żadna akcja. Z drugiej strony, jeśli aplikacja przypina każdy pośredni lub liść certyfikat TLS, zalecamy przypięcie następujących elementów głównych:

| Certyfikat | Odcisk palca |
| ----- | ----- |
| [Główny urząd certyfikacji Baltimore](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Główny urząd certyfikacji RSA firmy Microsoft 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [DigiCert Global root — G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> Należy często zmieniać certyfikaty pośredniczące i liściowe. Zalecamy, aby nie podejmować zależnych od nich zależności. Zamiast tego przypinaj aplikację do certyfikatu głównego, ponieważ jest ona rzadziej przetwarzana.
>
>

Aby nadal przypiąć certyfikaty pośrednie, Dodaj następujący kod do listy przypiętych certyfikatów pośrednich, która zawiera kilka dodatkowych, aby zminimalizować przyszłe zmiany:

| Nazwa pospolita urzędu certyfikacji | Odcisk palca |
| ----- | ----- |
| [Urząd certyfikacji RSA TLS 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure protokołu TLS wystawiającego certyfikaty 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure protokołu TLS wystawiającego certyfikaty 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure protokołu TLS wystawiającego certyfikaty 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure protokół TLS wystawiającego certyfikaty 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

Jeśli aplikacja sprawdza poprawność certyfikatu w kodzie, należy zmodyfikować go w celu rozpoznania właściwości (np. wystawców, odcisku palca) nowo przypiętych certyfikatów. Ta dodatkowa weryfikacja powinna obejmować wszystkie przypięte certyfikaty, które są bardziej szczegółowe.

## <a name="next-steps"></a>Następne kroki

Jeśli masz dodatkowe pytania, skontaktuj się z nami, korzystając z [pomocy technicznej](https://azure.microsoft.com/support/options/).  
