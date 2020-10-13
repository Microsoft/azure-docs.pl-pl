---
title: Jak automatycznie diagnozować i rozwiązywać problemy w chmurze Azure wiosennej
description: Dowiedz się, jak automatycznie diagnozować i rozwiązywać problemy w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9d572545f589475dc2b460db90329fe8b0a838c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904293"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Jak automatycznie diagnozować i rozwiązywać problemy w chmurze Azure wiosennej

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Diagnostyka chmurowa platformy Azure to interaktywny interfejs ułatwiający Rozwiązywanie problemów z aplikacją. Nie jest wymagana żadna konfiguracja. Po znalezieniu problemów Diagnostyka chmurowa platformy Azure wskazuje, co jest nieprawidłowe, i prowadzi użytkownika do informacji ułatwiających rozwiązywanie problemów i rozwiązywanie problemu.

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Wdrożone wystąpienie usługi w chmurze Azure wiosną. Skorzystaj z naszego [przewodnika Szybki Start dotyczącego wdrażania aplikacji za pomocą interfejsu wiersza polecenia platformy Azure,](spring-cloud-quickstart.md) aby rozpocząć pracę.
* Co najmniej jedna aplikacja została już utworzona w wystąpieniu usługi.

## <a name="navigate-to-the-diagnostics-page"></a>Przejdź do strony Diagnostyka
1. Zaloguj się do Portalu Azure.
2. Przejdź do strony **omówienia** chmury Azure wiosennej.
3. Otwórz okno **diagnozowanie i rozwiązywanie problemów** z menu po lewej stronie strony.

 ![Diagnozuj, okno dialogowe rozwiązywania](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Wyszukaj zarejestrowane problemy
Aby znaleźć problem, można wyszukać, wpisując słowo kluczowe lub klikając pozycję Grupa rozwiązań w celu zbadania wszystkich w tej kategorii.

 ![Problemy z wyszukiwaniem](media/spring-cloud-diagnose/search-detectors.png)

W przypadku wybrania opcji **Konfiguracja kondycji serwera**konfiguracji, **stanu kondycji serwera konfiguracji**lub **historii aktualizacji serwera konfiguracji** zostaną wyświetlone różne wyniki.

![Opcje problemów](media/spring-cloud-diagnose/detectors-options.png)

Znajdź detektor docelowy i kliknij go, aby wykonać operację. Po wykonaniu detektora zostaną wyświetlone podsumowanie danych diagnostycznych. Możesz wybrać opcję **Wyświetl pełny raport** , aby sprawdzić szczegóły diagnostyczne lub kliknąć przycisk **Pokaż menu kafelka** , aby wrócić do listy detektorów.

 ![Diagnostyka podsumowania](media/spring-cloud-diagnose/summary-diagnostics.png)

Na stronie szczegółów diagnostycznych można zmienić zakres czasu diagnostyki na kontroler w prawym górnym rogu. Aby wyświetlić więcej metryk lub dzienników, Przełącz każdą diagnostykę. Metryki i dzienniki mogą wynosić 15 minut.

 ![Szczegóły diagnostyki](media/spring-cloud-diagnose/diagnostics-details.png)

Niektóre wyniki zawierają powiązaną dokumentację.

 ![Powiązane szczegóły](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie zasobów Spring Cloud przy użyciu alertów i grup akcji](spring-cloud-tutorial-alerts-action-groups.md)
* [Mechanizmy kontroli zabezpieczeń dla usługi Azure Spring Cloud](spring-cloud-concept-security-controls.md)