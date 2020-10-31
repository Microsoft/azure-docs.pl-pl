---
title: Zabezpieczanie centrów wirtualnych platformy Azure przy użyciu usługi Check Point CloudGuard Connect
description: Informacje o programie Check Point CloudGuard Connect to Secure centra wirtualne platformy Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 759b8211aa8790e81c273f3d90d6679e70012ea4
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133660"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Zabezpieczanie koncentratorów wirtualnych przy użyciu usługi Check Point CloudGuard Connect

Check Point CloudGuard Connect to zaufany partner zabezpieczeń w Menedżerze zapory platformy Azure. Chroni globalnie rozproszone rozgałęzienie do Internetu (B2I) lub połączenia sieci wirtualnej z Internetem (V2I) z zaawansowanymi zagrożeniami. 

Przy użyciu prostej konfiguracji w Menedżerze zapory platformy Azure można kierować połączenia z centrum rozgałęzień i sieci wirtualnych do Internetu za pomocą CloudGuard Connect Security as a Service (SECaaS). Ruch jest chroniony podczas przesyłania z centrum do usługi Check Point w chmurze w tunelach IPsec sieci VPN.

Po włączeniu automatycznej synchronizacji w portalu Check Point wszystkie zasoby oznaczone jako *zabezpieczone* w Azure Portal są automatycznie zabezpieczone. Nie musisz dwa razy zarządzać zasobami. Po prostu wybierz opcję bezpiecznego zabezpieczenia w Azure Portal.

Opcja Check Point łączy wiele usług zabezpieczeń w ramach jednego parasola. Zintegrowany ruch zabezpieczeń jest odszyfrowany i sprawdzany w jednym przebiegu. Kontrola aplikacji, filtrowanie adresów URL i rozpoznawanie zawartości (DLP) wymuszają bezpieczne użycie sieci Web i ochronę danych. Adresy IP i oprogramowanie antywirusowe chronią użytkowników przed znanymi programami wykorzystującymi luki w zabezpieczeniach. Bot blokuje połączenia z serwerem poleceń i kontroli i ostrzega użytkownika, Jeśli host jest zainfekowany.

Emulacja zagrożeń (piaskownica) chroni użytkowników przed nieznanymi i zerowymi zagrożeniami. Check Point SandBlast Zero-Day Protection to oparta na chmurze technologia, w której pliki są szybko poddawane kwarantannie i sprawdzane. Działa w wirtualnej piaskownicy, aby poznać złośliwe zachowanie przed wprowadzeniem do sieci. Zapobiega to zagrożeniom przed uszkodzeniem, aby oszczędzić personel cenny czas odpowiedzi na zagrożenia. 

## <a name="deployment-example"></a>Przykład wdrożenia

Obejrzyj poniższy film wideo, aby dowiedzieć się, jak wdrożyć usługę Check Point CloudGuard Connect jako zaufany partner zabezpieczeń Azure.

> [!VIDEO https://youtu.be/C8AuN76DEmU]

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie dostawcy partnera w zakresie zabezpieczeń](deploy-trusted-security-partner.md)