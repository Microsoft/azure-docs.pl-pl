---
title: Zmiany certyfikatu protokołu TLS platformy Azure
description: Zmiany certyfikatu protokołu TLS platformy Azure
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 11/10/2020
ms.author: mbaldwin
ms.openlocfilehash: 955990ed9209ea1e12eed824241e8a5a456ed73b
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444881"
---
# <a name="azure-tls-certificate-changes"></a>Zmiany certyfikatu protokołu TLS platformy Azure  

Firma Microsoft aktualizuje usługi platformy Azure, aby używać certyfikatów TLS z innego zestawu głównych urzędów certyfikacji (CA). Ta zmiana jest wykonywana, ponieważ bieżące certyfikaty urzędu certyfikacji nie są zgodne z jednym z wymagań linii bazowej urzędu certyfikacji/przeglądarki.

## <a name="when-will-this-change-happen"></a>Kiedy to nastąpi zmiana?

Istniejące punkty końcowe platformy Azure przeszły w sposób fazowy od 13 sierpnia 2020. Wszystkie nowo utworzone punkty końcowe protokołu TLS/SSL usługi Azure zawierają zaktualizowane certyfikaty łańcucha do nowych głównych urzędów certyfikacji.

Szczegóły dotyczące usługi:

- Usługi [Azure Active Directory](../../active-directory/index.yml) (Azure AD) zaczęły to przechodzenie od 7 lipca 2020.
- [Usługi Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) i [DPS](../../iot-dps/index.yml) POzostaną w głównym urzędzie certyfikacji Baltimore CyberTrust, ale ich pośrednie urzędy certyfikacji zmienią się. [Kliknij tutaj, aby uzyskać szczegółowe informacje](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- [Usługa Azure Storage](../../storage/index.yml) pozostanie w głównym urzędzie certyfikacji Baltimore CyberTrust, ale ich pośrednie urzędy certyfikacji zmienią się. [Kliknij tutaj, aby uzyskać szczegółowe informacje](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518).
- [Usługa Azure cache for Redis](../../azure-cache-for-redis/index.yml) pozostanie w głównym urzędzie certyfikacji Baltimore CyberTrust, ale ich pośrednie urzędy certyfikacji zmienią się. [Kliknij tutaj, aby uzyskać szczegółowe informacje](../../azure-cache-for-redis/cache-whats-new.md).

> [!IMPORTANT]
> Aby uniknąć błędów łączności podczas próby nawiązania połączenia z usługami platformy Azure, klienci mogą wymagać aktualizacji aplikacji po tej zmianie.

## <a name="what-is-changing"></a>Co się zmieni?

Dzisiaj większość certyfikatów protokołu TLS używanych przez łańcuch usług platformy Azure ma następujący główny urząd certyfikacji:

| Nazwa pospolita urzędu certyfikacji | Odcisk palca (SHA1) |
|--|--|
| [Baltimore CyberTrust, główny](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Certyfikaty TLS używane przez usługi platformy Azure będą łańcucha do jednego z następujących głównych urzędów certyfikacji:

| Nazwa pospolita urzędu certyfikacji | Odcisk palca (SHA1) |
|--|--|
| [DigiCert Global root — G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [Globalny główny urząd certyfikacji DigiCert](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust, główny](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D — zaufanie klasy głównej 3 urzędu certyfikacji 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Główny urząd certyfikacji RSA firmy Microsoft 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Główny urząd certyfikacji ECC firmy Microsoft 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Kiedy można zrezygnować z starego pośredniego odcisku palca?

Bieżące certyfikaty urzędu certyfikacji *nie* zostaną odwołane do 15 lutego 2021. Po tej dacie można usunąć stare odciski palców z kodu.

W przypadku zmiany daty otrzymasz powiadomienie o nowej dacie odwołania.

## <a name="will-this-change-affect-me"></a>Czy ta zmiana wpłynie na mnie? 

Oczekujemy, że nie wpłynie to na **większość klientów platformy Azure** .  Jednak może to mieć wpływ na aplikację, jeśli jawnie określi listę akceptowalnych urzędów certyfikacji. To rozwiązanie jest znane jako Przypinanie certyfikatów.

Oto kilka sposobów na wykrycie, czy ma to wpływ na aplikację:

- Przeszukaj kod źródłowy dla odcisku palca, nazwy pospolitej i innych właściwości certyfikatu dowolnego urzędu certyfikacji TLS firmy Microsoft znajdującego się w [tym miejscu](https://www.microsoft.com/pki/mscorp/cps/default.htm). Jeśli istnieje dopasowanie, będzie to miało wpływ na aplikację. Aby rozwiązać ten problem, należy zaktualizować kod źródłowy obejmujący nowe urzędy certyfikacji. Najlepszym rozwiązaniem jest upewnienie się, że urzędy certyfikacji mogą być dodawane lub edytowane przy użyciu krótkiej uwagi. Uregulowania branżowe wymagają, aby certyfikaty urzędu certyfikacji były zastępowane w ciągu siedmiu dni, a tym samym klienci korzystają z przypinania, aby szybko reagować.

- Jeśli masz aplikację, która integruje się z interfejsami API platformy Azure lub innymi usługami platformy Azure i nie masz pewności, czy używa ona przypinania certyfikatów, skontaktuj się z dostawcą aplikacji.

- Różne systemy operacyjne i środowiska uruchomieniowe języka, które komunikują się z usługami platformy Azure, mogą wymagać dodatkowych kroków w celu poprawnego skompilowania łańcucha certyfikatów przy użyciu tych nowych katalogów głównych:
    - **Linux** : wiele dystrybucji wymaga dodania urzędów certyfikacji do/etc/SSL/certs. Aby uzyskać szczegółowe instrukcje, zapoznaj się z dokumentacją dystrybucji.
    - **Java** : Upewnij się, że magazyn kluczy Java zawiera wymienione powyżej urzędy certyfikacji.
    - **System Windows działający w odłączonych środowiskach** : systemy działające w środowiskach odłączonych muszą mieć dodane nowe katalogi główne do magazynu zaufanych głównych urzędów certyfikacji, a następnie pośrednicy dodani do magazynu pośrednich urzędów certyfikacji.
    - **Android** : Zapoznaj się z dokumentacją urządzenia i wersji systemu Android.
    - **Inne urządzenia sprzętowe, zwłaszcza IoT** : skontaktuj się z producentem urządzenia.

- Jeśli masz środowisko, w którym reguły zapory są ustawione tak, aby zezwalały na połączenia wychodzące tylko na pobieranie określonych list odwołania certyfikatów (CRL) i/lub lokalizacje weryfikacyjne protokołu stanu certyfikatów (OCSP). Musisz zezwolić na następującą listę CRL i adresy URL protokołu OCSP:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46;d zaufania&#46;sieci
    - http://root-c3-ca2-2009&#46; Protokół OCSP&#46;d — zaufanie&#46;sieci
    - http://crl&#46; Microsoft&#46;com
    - http://oneocsp&#46; Microsoft&#46;com
    - http://ocsp&#46; msocsp&#46;com
    - http://www&#46; Microsoft&#46;com/pkiops

## <a name="next-steps"></a>Następne kroki

Jeśli masz dodatkowe pytania, skontaktuj się z nami, korzystając z [pomocy technicznej](https://azure.microsoft.com/support/options/).
