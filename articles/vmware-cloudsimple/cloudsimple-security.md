---
title: Rozwiązanie VMware firmy Azure według CloudSimple — zabezpieczenia dla usług CloudSimple Services
description: Opisuje współużytkowane modele odpowiedzialności dla bezpieczeństwa usług CloudSimple Services
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d62eec72a884470ade03897a0533e62c6f1b0f71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898797"
---
# <a name="cloudsimple-security-overview"></a>Przegląd zabezpieczeń CloudSimple

Ten artykuł zawiera omówienie sposobu implementowania zabezpieczeń na platformie Azure VMware przez CloudSimple Service, Infrastructure i centrum danych. Informacje o ochronie danych i zabezpieczeniach, zabezpieczeniach sieci oraz sposobie zarządzania lukami i poprawkami.

## <a name="shared-responsibility"></a>Wspólna odpowiedzialność

Rozwiązanie VMware firmy Azure według CloudSimple używa wspólnego modelu odpowiedzialności do zabezpieczeń. Zaufane zabezpieczenia w chmurze są realizowane za pomocą wspólnych obowiązków klientów i firmy Microsoft jako dostawcy usług. Ta macierz odpowiedzialności zapewnia wyższy poziom bezpieczeństwa i eliminuje pojedyncze punkty awarii.

## <a name="azure-infrastructure"></a>Infrastruktura platformy Azure

Zagadnienia dotyczące zabezpieczeń infrastruktury platformy Azure obejmują centra danych i lokalizację sprzętu.

### <a name="datacenter-security"></a>Zabezpieczenia centrum danych

Firma Microsoft ma cały dział poświęcony na projektowanie, kompilowanie i obsługiwanie fizycznych obiektów, które obsługują platformę Azure. Ten zespół jest inwestowany z zachowaniem bezpieczeństwa fizycznego. Aby uzyskać szczegółowe informacje na temat zabezpieczeń fizycznych, zobacz [usługi platformy Azure, lokalne i zabezpieczenia fizyczne](../security/fundamentals/physical-security.md).

### <a name="equipment-location"></a>Lokalizacja sprzętu

Sprzęt komputerowy bez systemu operacyjnego, który uruchamia chmurę prywatną, jest hostowany w lokalizacji centrum danych platformy Azure.  Klatki, w których znajduje się ten sprzęt, wymagają uwierzytelniania dwuskładnikowego opartego na bioczynnikach biometrycznych w celu uzyskania dostępu.

## <a name="dedicated-hardware"></a>Dedykowany sprzęt

W ramach usługi CloudSimple wszyscy klienci CloudSimple otrzymują dedykowane hosty bez systemu operacyjnego z lokalnymi dołączonymi dyskami, które są fizycznie odizolowane od innego sprzętu dzierżawców. Funkcja hypervisor ESXi z sieci vSAN jest uruchamiana na każdym węźle. Węzły są zarządzane za pomocą dedykowanych przez klienta rozwiązań VMware vCenter i NSX. Udostępnianie sprzętu między dzierżawcami zapewnia dodatkową warstwę izolacji i ochrony zabezpieczeń.

## <a name="data-security"></a>Bezpieczeństwo danych

Klienci zachowują kontrolę i prawo własności do swoich danych. Stewardship danych klienta jest odpowiedzialny za klienta.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrona danych w czasie spoczynku i danych w ruchu w sieciach wewnętrznych

W przypadku danych przechowywanych w środowisku chmury prywatnej można użyć szyfrowania sieci vSAN. szyfrowanie sieci vSAN współpracuje z zewnętrznymi serwerami zarządzania kluczami (KMS) oprogramowania VMware w własnej sieci wirtualnej lub lokalnie.  Klucze szyfrowania danych są kontrolowane samodzielnie. W przypadku danych w ramach chmury prywatnej usługa vSphere obsługuje szyfrowanie danych za pośrednictwem sieci dla całego ruchu VMkernel (łącznie z ruchem vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Ochrona danych danych wymaganych do poruszania się w sieciach publicznych

Aby chronić dane przenoszone za pośrednictwem sieci publicznych, można utworzyć tunele VPN protokołu IPsec i protokołu TLS dla chmur prywatnych. Obsługiwane są typowe metody szyfrowania, w tym 128-bajtowe i 256-Byte AES. Dane przesyłane (łącznie z uwierzytelnianiem, dostępem administracyjnym i danymi klienta) są szyfrowane przy użyciu standardowych mechanizmów szyfrowania (SSH, TLS 1,2 i Secure RDP). Komunikacja, która transportuje poufne informacje korzysta z standardowych mechanizmów szyfrowania.

### <a name="secure-disposal"></a>Bezpieczne usuwanie

Jeśli Twoja usługa CloudSimple wygaśnie lub zostanie zakończona, użytkownik jest odpowiedzialny za usunięcie lub usunięcie danych. CloudSimple będzie współpracować z licencjobiorcą w celu usunięcia lub zwrócenia wszystkich danych klienta zgodnie z umową klienta, z wyjątkiem sytuacji, w której prawo do przechowywania niektórych lub wszystkich danych osobowych w zakresie CloudSimple jest niezbędne. Jeśli to konieczne, aby zachować dane osobowe, CloudSimple będzie archiwizować dane i wdrożyć odpowiednie środki, aby zapobiec dalszemu przetwarzaniu danych klienta.

### <a name="data-location"></a>Lokalizacja danych

Podczas konfigurowania chmur prywatnych należy wybrać region platformy Azure, w którym zostaną wdrożone. Dane maszyny wirtualnej VMware nie są przenoszone z tego fizycznego centrum danych, chyba że wykonywana jest migracja danych ani kopia zapasowa danych poza siedzibą firmy. Możesz również hostować obciążenia i przechowywać dane w wielu regionach świadczenia usługi Azure, jeśli są odpowiednie dla Twoich potrzeb.

Dane klienta, które są rezydentem w chmurze prywatnej — zbieżne węzły nie przechodzą do lokalizacji bez jawnej akcji administratora dzierżawy. Jest on odpowiedzialny za wdrożenie obciążeń w wysokiej dostępności.

### <a name="data-backups"></a>Kopie zapasowe danych

CloudSimple nie tworzy kopii zapasowych ani nie archiwizuje danych klientów. CloudSimple wykonuje okresową kopię zapasową danych vCenter i NSX w celu zapewnienia wysokiej dostępności serwerów zarządzania. Przed utworzeniem kopii zapasowej wszystkie dane są szyfrowane w źródle programu vCenter przy użyciu interfejsów API VMware. Zaszyfrowane dane są transportowane i przechowywane w usłudze Azure Blob. Klucze szyfrowania kopii zapasowych są przechowywane w wysoce zabezpieczonym magazynie zarządzanym CloudSimple uruchomionym w sieci wirtualnej CloudSimple na platformie Azure.

## <a name="network-security"></a>Bezpieczeństwo sieci

Rozwiązanie CloudSimple opiera się na warstwach zabezpieczeń sieci.

### <a name="azure-edge-security"></a>Zabezpieczenia usługi Azure Edge

Usługi CloudSimple Services są oparte na podstawowych zabezpieczeniach sieci zapewnianych przez platformę Azure. Na platformie Azure są stosowane kompleksowe techniki wykrywania i reagowania w odpowiedzi na ataki sieciowe związane z niestandardowymi wzorcami ruchu przychodzącego lub wychodzącego oraz rozproszone ataki typu "odmowa usługi" (DDoS). Ta kontrola zabezpieczeń ma zastosowanie do środowisk chmury prywatnej i oprogramowania płaszczyzny kontroli opracowanego przez CloudSimple.

### <a name="segmentation"></a>Segmentacja

Usługa CloudSimple ma logicznie oddzielić sieci warstwy 2, które ograniczają dostęp do własnych sieci prywatnych w środowisku chmury prywatnej. Możesz zapewnić lepszą ochronę sieci w chmurze prywatnej przy użyciu zapory. Portal CloudSimple umożliwia definiowanie reguł kontroli ruchu sieciowego dla całego ruchu sieciowego, takich jak ruch w chmurze prywatnej, ruch w chmurze prywatnej, ogólny ruch do Internetu oraz ruch sieciowy do sieci lokalnej za pośrednictwem protokołu IPsec lub połączenia ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Luki w zabezpieczeniach i zarządzaniu poprawkami

CloudSimple jest odpowiedzialna za okresowe poprawianie zabezpieczeń zarządzanego oprogramowania VMware (ESXi, vCenter i NSX).

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Klienci mogą uwierzytelniać się na koncie platformy Azure (w usłudze Azure AD) przy użyciu uwierzytelniania wieloskładnikowego lub logowania jednokrotnego jako preferowanego. W Azure Portal można uruchomić Portal CloudSimple bez ponownego wprowadzania poświadczeń.

CloudSimple obsługuje opcjonalną konfigurację źródła tożsamości dla programu vCenter w chmurze prywatnej. Możesz użyć [lokalnego źródła tożsamości](set-vcenter-identity.md), nowego źródła tożsamości dla chmury prywatnej lub [usługi Azure AD](azure-ad.md).

Domyślnie klienci mają uprawnienia, które są niezbędne do codziennej operacji programu vCenter w chmurze prywatnej. Ten poziom uprawnień nie obejmuje dostępu administracyjnego do serwera vCenter. Jeśli dostęp administracyjny jest tymczasowo wymagany, można [eskalować swoje uprawnienia](escalate-private-cloud-privileges.md) przez ograniczony okres czasu podczas wykonywania zadań administracyjnych.
