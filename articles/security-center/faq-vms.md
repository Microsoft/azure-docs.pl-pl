---
title: Azure Security Center często zadawane pytania — pytania dotyczące maszyn wirtualnych
description: Często zadawane pytania dotyczące maszyn wirtualnych w Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0b7817539a26b7ebfa24efd086edf8b0ef82eae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80436008"
---
# <a name="faq---questions-about-virtual-machines"></a>Często zadawane pytania — pytania dotyczące maszyn wirtualnych


## <a name="what-types-of-virtual-machines-are-supported"></a>Jakie typy maszyn wirtualnych są obsługiwane?

Monitorowanie i zalecenia są dostępne dla maszyn wirtualnych utworzonych przy użyciu zarówno [klasycznych, jak i Menedżer zasobów modeli wdrażania](../azure-classic-rm.md).

Zobacz [obsługiwane platformy w Azure Security Center](security-center-os-coverage.md) , aby uzyskać listę obsługiwanych platform.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Dlaczego nie Azure Security Center rozpoznać rozwiązania chroniącego przed złośliwym kodem uruchomionego na maszynie wirtualnej platformy Azure?

Azure Security Center ma wgląd w oprogramowanie chroniące przed złośliwym kodem instalowany za pomocą rozszerzeń platformy Azure. Na przykład program Security Center nie może wykryć ochrony przed złośliwym kodem, która została wstępnie zainstalowana na dostarczonym obrazie, lub jeśli zainstalowano oprogramowanie chroniące przed złośliwym oprogramowaniem na maszynach wirtualnych przy użyciu własnych procesów (takich jak systemy zarządzania konfiguracją).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Dlaczego otrzymuję komunikat "brakujące dane skanowania" dla mojej maszyny wirtualnej?

Ten komunikat pojawia się, gdy dla maszyny wirtualnej nie ma żadnych danych skanowania. Po włączeniu funkcji zbierania danych w usłudze Azure Security Center wypełnienie danych skanowania może zająć trochę czasu (mniej niż godzinę). Po początkowym wypełnieniu danych skanowania możesz otrzymać taki komunikat, ponieważ w ogóle nie ma danych skanowania lub nie ma ostatnich danych skanowania. Skanowanie nie wypełnia się dla zatrzymanej maszyny wirtualnej. Ten komunikat może również zostać wyświetlony, jeśli dane skanowania nie zostały ostatnio zapełnione (zgodnie z zasadami przechowywania dla agenta systemu Windows, którego wartość domyślna to 30 dni).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Jak często program Security Center skanuje w poszukiwaniu luk w zabezpieczeniach systemu operacyjnego, aktualizacji systemu oraz problemów z programem Endpoint Protection?

Poniżej przedstawiono czasy opóźnienia Security Center skanowania luk w zabezpieczeniach, aktualizacji i problemów:

- Konfiguracje zabezpieczeń systemu operacyjnego — dane są aktualizowane w ciągu 48 godzin
- Aktualizacje systemu — dane są aktualizowane w ciągu 24 godzin
- Problemy Endpoint Protection — dane są aktualizowane w ciągu 8 godzin

Program Security Center zwykle skanuje nowe dane co godzinę i odświeża odpowiednio zalecenia. 

> [!NOTE]
> Security Center używa agenta Log Analytics do zbierania i przechowywania danych. Aby dowiedzieć się więcej, zobacz [Azure Security Center migracji platformy](security-center-platform-migration.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Dlaczego otrzymuję komunikat "Brak agenta maszyny wirtualnej"?

Aby można było włączyć zbieranie danych, Agent maszyny wirtualnej musi być zainstalowany na maszynach wirtualnych. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu witryny Azure Marketplace. Aby uzyskać informacje na temat sposobu instalowania agenta maszyny wirtualnej na innych maszynach wirtualnych, zobacz wpis w blogu [Agent i rozszerzenia](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).