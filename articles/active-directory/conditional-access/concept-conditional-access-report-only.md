---
title: Co to jest tryb tylko do raportowania dostępu warunkowego? -Azure Active Directory
description: Jak korzystać z pomocy w trybie tylko do raportowania z wdrożeniem zasad dostępu warunkowego
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f4e4851d406b14ed38665274401b96446ac6a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579080"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Co to jest tryb tylko do raportowania dostępu warunkowego?

Dostęp warunkowy jest szeroko używany przez naszych klientów w celu zapewnienia bezpieczeństwa przez zastosowanie odpowiednich kontroli dostępu w odpowiednich przypadkach. Jednak jednym z wyzwań związanych z wdrażaniem zasad dostępu warunkowego w organizacji jest określenie wpływu na użytkowników końcowych. Może być trudne, aby przewidzieć liczbę i nazwy użytkowników, na które mają wpływ typowe inicjatywy wdrażania, takie jak blokowanie starszego uwierzytelniania, wymaganie uwierzytelniania wieloskładnikowego w przypadku populacji użytkowników lub wdrażanie zasad dotyczących ryzyka związanego z logowaniem. 

Tryb tylko do raportowania jest nowym stanem zasad dostępu warunkowego, który umożliwia administratorom ocenę wpływu zasad dostępu warunkowego przed włączeniem ich w środowisku.  Z wersją trybu tylko do raportowania:

- Zasady dostępu warunkowego można włączyć w trybie tylko raportowanie.
- Zasady w trybie tylko do raportowania są oceniane, ale nie są wymuszane.
- Wyniki są rejestrowane na kartach **dostęp warunkowy** i **tylko raporty** w obszarze Szczegóły dziennika logowania.
- Klienci z subskrypcją Azure Monitor mogą monitorować wpływ ich zasad dostępu warunkowego za pomocą skoroszytu dostępu warunkowego usługi Insights.

> [!VIDEO https://www.youtube.com/embed/NZbPYfhb5Kc]

> [!WARNING]
> Zasady w trybie tylko do raportowania, które wymagają zgodnych urządzeń, mogą monitować użytkowników o komputery Mac, iOS i Android w celu wybrania certyfikatu urządzenia podczas obliczania zasad, nawet jeśli zgodność urządzenia nie jest wymuszana. Te polecenia mogą powtarzać się, dopóki urządzenie nie zostanie zgodne. Aby uniemożliwić użytkownikom końcowym otrzymywanie pojawiających się w trakcie logowania, należy wykluczyć platformy urządzeń Mac, iOS i Android z zasad tylko do raportowania, które wykonują testy zgodności urządzeń. Należy pamiętać, że tryb tylko do raportowania nie ma zastosowania do zasad dostępu warunkowego z zakresem "akcje użytkownika".

![Karta tylko raport w dzienniku logowania usługi Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Wyniki zasad

Gdy zasady w trybie tylko do raportowania są oceniane dla danego logowania, istnieją cztery nowe możliwe wartości wyniku:

| Wynik | Opis |
| --- | --- |
| Tylko raport: sukces | Wszystkie skonfigurowane warunki zasad, wymagane kontrolki grantu nieinteraktywnego i kontrolki sesji zostały spełnione. Na przykład wymaganie uwierzytelniania wieloskładnikowego jest spełnione przez wystąpienie usługi MFA już obecne w tokenie lub zgodne zasady dotyczące urządzeń są spełnione przez przeprowadzenie kontroli urządzenia na zgodnym urządzeniu. |
| Tylko raport: niepowodzenie | Wszystkie skonfigurowane warunki zasad zostały spełnione, ale nie wszystkie wymagane kontrolki dotacji nieinterakcyjnych lub kontrolki sesji zostały spełnione. Na przykład zasady mają zastosowanie do użytkownika, w którym skonfigurowano kontrolkę bloku lub jeśli urządzenie nie spełnia zgodnych zasad dotyczących urządzeń. |
| Tylko raport: wymagana akcja użytkownika | Wszystkie skonfigurowane warunki zasad zostały spełnione, ale akcja użytkownika będzie wymagana do spełnienia wymaganych kontrolek uprawnień lub kontrolek sesji. W trybie tylko do raportowania użytkownik nie jest monitowany o spełnienie wymaganych kontrolek. Na przykład użytkownicy nie otrzymują monitu o wyzwania związane z uwierzytelnianiem wieloskładnikowym ani warunki użytkowania.   |
| Tylko raport: nie zastosowano | Nie wszystkie skonfigurowane warunki zasad zostały spełnione. Na przykład użytkownik jest wykluczony z zasad lub zasady dotyczą tylko niektórych zaufanych nazw lokalizacji. |

## <a name="conditional-access-insights-workbook"></a>Skoroszyt usługi Dostęp warunkowy Insights

Administratorzy mają możliwość tworzenia wielu zasad w trybie tylko do raportowania, dlatego konieczne jest zrozumienie poszczególnych wpływów poszczególnych zasad i łączny wpływ wielu zasad jednocześnie. Nowy skoroszyt usługi Dostęp warunkowy pozwala administratorom na wizualizację zapytań dostępu warunkowego i monitorowanie wpływu zasad dla danego zakresu czasu, zestawu aplikacji i użytkowników. 
 
## <a name="next-steps"></a>Następne kroki

[Konfigurowanie trybu tylko do raportowania w zasadach dostępu warunkowego](howto-conditional-access-insights-reporting.md)
