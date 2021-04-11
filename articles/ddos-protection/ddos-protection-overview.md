---
title: Azure DDoS Protection — Omówienie
description: Dowiedz się, jak usługa Azure DDoS Protection w warstwie Standardowa w połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji zapewnia ochronę przed atakami DDoS.
services: virtual-network
documentationcenter: na
author: aletheatoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: c8b3b0227e293c42ac9db8f888bc863e073ff6f5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103338"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection — Omówienie

Ataki typu „rozproszona odmowa usługi” (Distributed Denial of Service, DDoS) należą do największych obaw związanych z dostępnością i zabezpieczeniami wśród klientów, którzy planują przeniesienie swoich aplikacji do chmury. Atak DDoS próbuje wymusić wyczerpanie zasobów aplikacji, co sprawia, że aplikacja jest niedostępna dla uprawnionych użytkowników. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu.

Każda właściwość na platformie Azure jest chroniona przez ochronę infrastruktury DDoS (podstawowa) platformy Azure bez dodatkowych kosztów. Skalowanie i pojemność sieci platformy Azure wdrożonej globalnie zapewnia ochronę przed typowymi atakami warstwy sieci przez zawsze włączone monitorowanie ruchu i środki zaradcze w czasie rzeczywistym. W DDoS Protection Basic nie są wymagane żadne zmiany w konfiguracji użytkownika ani aplikacji. DDoS Protection podstawowa pomaga chronić wszystkie usługi platformy Azure, w tym usługi PaaS, takie jak Azure DNS.

Azure DDoS Protection Standard, w połączeniu z najlepszymi rozwiązaniami dotyczącymi projektowania aplikacji, zapewnia ulepszone funkcje ograniczenia DDoS w celu obrony przed atakami DDoS. Jest on automatycznie dostosowany do ochrony określonych zasobów platformy Azure w sieci wirtualnej. Ochrona jest prosta do włączenia w przypadku każdej nowej lub istniejącej sieci wirtualnej i nie wymaga żadnych zmian aplikacji ani zasobów. Ma kilka korzyści w porównaniu z usługą podstawową, w tym rejestrowanie, alerty i dane telemetryczne. 

![Porównanie usługi Azure DDoS Protection](./media/ddos-protection-overview/ddos-comparison.png)

Usługa Azure DDoS Protection nie przechowuje danych klienta.

## <a name="features"></a>Funkcje

- **Integracja z platformą natywną:** Natywnie zintegrowane z platformą Azure. Obejmuje konfigurację za pomocą Azure Portal. W standardzie DDoS Protection są poznanie zasobów i konfiguracji zasobów.
- **Ochrona gotowe:** Uproszczona konfiguracja natychmiast chroni wszystkie zasoby w sieci wirtualnej zaraz po włączeniu DDoS Protection Standard. Nie jest wymagana żadna interwencja ani definicja użytkownika. 
- **Zawsze włączone monitorowanie ruchu:** Wzorce ruchu aplikacji są monitorowane przez 24 godziny na dobę, 7 dni w tygodniu, szukając wskaźników ataków DDoS. Program DDoS Protection Standard natychmiast i automatycznie ogranicza atak po jego wykryciu.
- **Dostrajanie adaptacyjne:** Profilowanie ruchu inteligentnego uzyskuje informacje o ruchu aplikacji w czasie i wybiera i aktualizuje profil, który jest najbardziej odpowiedni dla Twojej usługi. Profil dostosowuje się w miarę zmiany ruchu w czasie.
- **Ochrona wielowarstwowa:** Po wdrożeniu za pomocą zapory aplikacji sieci Web (WAF DDoS Protection) standard chroni zarówno w warstwie sieciowej (warstwa 3 i 4, oferowany przez Azure DDoS Protection standard), jak i w warstwie aplikacji (warstwa 7, oferowana przez WAF). Oferty WAF obejmują usługi Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oraz oferty zapory aplikacji internetowych innych firm dostępne w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).
- **Rozbudowana Skala łagodzenia:** Ponad 60 różnych typów ataków można ograniczyć, korzystając z globalnej pojemności, aby chronić przed największym znanymi atakami DDoS.
- **Analiza ataków:** Uzyskaj szczegółowe raporty w ciągu pięciu minut podczas ataku i kompletne podsumowanie po zakończeniu ataku. Dzienniki przepływów ograniczenia przesyłania strumieniowego na [platformie Azure](../sentinel/connect-azure-ddos-protection.md) — dane bezpieczeństwa i zabezpieczenia w trybie offline oraz system zarządzania zdarzeniami (Siem) na potrzeby monitorowania niemal w czasie rzeczywistym podczas ataku.
- **Metryki ataków:** Podsumowania metryk z każdego ataku są dostępne za pośrednictwem Azure Monitor.
- **Alerty ataków:** Alerty można skonfigurować przy uruchamianiu i zatrzymywaniu ataku oraz przez czas trwania ataku przy użyciu wbudowanych metryk ataku. Alerty integrują się z oprogramowaniem operacyjnym, takimi jak dzienniki monitora Microsoft Azure, Splunk, Azure Storage, Poczta E-mail i Azure Portal.
- **DDoS szybka odpowiedź**: Zaangażuj zespół szybkiego reagowania (DRR) DDoS Protection, aby uzyskać pomoc przy badaniu i analizie ataku. Aby dowiedzieć się więcej, zobacz [szybkie reagowanie na DDoS](ddos-rapid-response.md).
- **Gwarancja kosztów:** Odbieraj środki na korzystanie z usług transferu danych i skalowania aplikacji w poziomie dla kosztów zasobów wynikających z udokumentowanych ataków DDoS.

## <a name="pricing"></a>Ceny

Plany ochrony DDoS mają stałą miesięczną opłatą równą $2 944 miesięcznie, która obejmuje do 100 publicznych adresów IP. W przypadku ochrony dodatkowych zasobów będzie kosztować dodatkowe $30 za każdy zasób miesięcznie.

W ramach dzierżawy może być używany jeden plan ochrony DDoS w wielu subskrypcjach, więc nie ma potrzeby tworzenia więcej niż jednego planu ochrony DDoS.

Aby dowiedzieć się więcej na temat Azure DDoS Protection standardowych cen, zobacz [Azure DDoS Protection standardowych cen](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="reference-architectures"></a>Architektury referencyjne

Standard DDoS Protection jest przeznaczony dla [usług, które są wdrażane w sieci wirtualnej](../virtual-network/virtual-network-for-azure-services.md). W przypadku innych usług stosowana jest domyślna usługa DDoS Protection podstawowa. Aby dowiedzieć się więcej o obsługiwanych architekturach, zobacz [DDoS Protection architektur referencyjnych](./ddos-protection-reference-architectures.md). 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Utwórz plan DDoS Protection](manage-ddos-protection.md)