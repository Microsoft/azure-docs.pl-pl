---
title: Zarządzanie kosztami i własnością w Azure DevTest Labs
description: Ten artykuł zawiera informacje ułatwiające optymalizację kosztu i dostosowanie własności w środowisku.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: dfac69a055c9b0c75032622caf7fb8502fad3406
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328270"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Nadzór nad infrastrukturą Azure DevTest Labs — zarządzanie kosztami i własnością
Koszt i własność to podstawowe zagadnienia, które należy wziąć pod uwagę podczas tworzenia środowisk deweloperskich i testowych. W tej sekcji znajdziesz informacje ułatwiające optymalizację kosztów i dostosowanie własności w środowisku.

## <a name="optimize-for-cost"></a>Optymalizuj pod kątem kosztów

### <a name="question"></a>Pytanie
Jak mogę zoptymalizować się pod kątem kosztów w ramach mojego środowiska DevTest Labs?

### <a name="answer"></a>Odpowiedź
Istnieje wiele wbudowanych funkcji DevTest Labs, które pomagają zoptymalizować się pod kątem kosztów. Aby ograniczyć działania użytkowników, zobacz artykuł [Zarządzanie kosztami, progi](devtest-lab-configure-cost-management.md) [i zasady](devtest-lab-set-lab-policy.md) . 

Gdy korzystasz z usługi DevTest Labs na potrzeby obciążeń deweloperskich i testowych, możesz rozważyć wykorzystanie [korzyści z subskrypcji Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/)w ramach Umowa Enterprise. Alternatywnie, jeśli masz płatność zgodnie z rzeczywistym użyciem, warto wziąć pod uwagę [ofertę DevTest płatność zgodnie z oczekiwaniami](https://azure.microsoft.com/offers/ms-azr-0023p/).

Takie podejście zapewnia wiele korzyści:

- Specjalne niższe stawki za tworzenie i testowanie w przypadku maszyn wirtualnych z systemem Windows, usług Cloud Services, HDInsight, App Service i Logic Apps
- Wspaniałe stawki Umowa Enterprise (EA) na inne usługi platformy Azure
- Dostęp do wyłącznego obrazu deweloperskiego/testowego w galerii, w tym Windows 8.1 i Windows 10
 
Tylko aktywni subskrybenci programu Visual Studio (Subskrypcje standardowe, roczne subskrypcje chmurowe i miesięczne subskrypcje chmury) mogą używać zasobów platformy Azure działających w ramach subskrypcji tworzenia i testowania w przedsiębiorstwie. Użytkownicy końcowi mogą jednak uzyskać dostęp do aplikacji w celu zapewnienia opinii lub przeprowadzenia testów akceptacji. Korzystanie z zasobów w ramach tej subskrypcji jest ograniczone do tworzenia i testowania aplikacji, a nie jest oferowana gwarancja czasu.

W przypadku podjęcia decyzji o skorzystaniu z oferty DevTest należy zauważyć, że ta korzyść dotyczy wyłącznie tworzenia i testowania aplikacji. Użycie w ramach subskrypcji nie jest objęte gwarantowaną finansowo umową SLA, z wyjątkiem używania usług Azure DevOps i HockeyApp.

## <a name="define-role-based-access-across-your-organization"></a>Definiowanie dostępu opartego na rolach w organizacji
### <a name="question"></a>Pytanie
Jak mogę zdefiniować kontrolę dostępu opartą na rolach na platformie Azure dla środowisk DevTest Labs, aby mieć pewność, że może ona być zarządzana, gdy deweloperzy/testy mogą wykonywać swoje prace? 

### <a name="answer"></a>Odpowiedź
Istnieje szeroki wzorzec, jednak szczegóły są zależne od organizacji.

Centralna powinna być tylko to, co jest niezbędne, i umożliwia zespołom projektu i aplikacji posiadanie wymaganego poziomu kontroli. Zazwyczaj oznacza to, że Central IT jest właścicielem subskrypcji i obsługuje podstawowe funkcje IT, takie jak konfiguracje sieci. Zestaw **właścicieli** dla subskrypcji powinien być niewielki. Tacy właściciele mogą mianować dodatkowych właścicieli w razie potrzeby lub zastosować zasady na poziomie subskrypcji, na przykład "Brak publicznego adresu IP".

Może istnieć podzbiór użytkowników, którzy wymagają dostępu między subskrypcjami, takimi jak pomoc lub obsługa warstwy 2. W tym przypadku zalecamy nadanie tym użytkownikom dostępu **współautora** , aby mogli zarządzać zasobami, ale nie zapewniają dostępu użytkowników ani dostosowywać zasad.

Zasób DevTest Labs powinien należeć do właścicieli, którzy znajdują się blisko zespołu projektu/aplikacji. Jest to spowodowane tym, że rozumieją one wymagania w zakresie maszyn i wymaganego oprogramowania. W większości organizacji właściciel tego zasobu DevTest Labs jest często liderem projektu/projektowania. Ten właściciel może zarządzać użytkownikami i zasadami w środowisku laboratoryjnym i może zarządzać wszystkimi maszynami wirtualnymi w środowisku DevTest Labs.

Członków zespołu projektu/aplikacji należy dodać do roli użytkownicy DevTest Labs. Ci użytkownicy mogą tworzyć maszyny wirtualne (w trybie online z zasadami dotyczącymi poziomu laboratorium i subskrypcji). Mogą również zarządzać własnymi maszynami wirtualnymi. Nie mogą oni zarządzać maszynami wirtualnymi należącymi do innych użytkowników.

Aby uzyskać więcej informacji, zobacz artykuł [Azure Enterprise szkielet —](/azure/architecture/cloud-adoption/appendix/azure-scaffold) dokumentacja dotycząca nadzoru subskrypcji.


## <a name="next-steps"></a>Następne kroki
Zobacz [zasady i zgodność firmy](devtest-lab-guidance-governance-policy-compliance.md).
