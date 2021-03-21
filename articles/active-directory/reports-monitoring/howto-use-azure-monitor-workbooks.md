---
title: Azure Monitor skoroszyty dla raportów | Microsoft Docs
description: Dowiedz się, jak używać Azure Monitor skoroszytów dla raportów Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b84c38b28b51f867160272883465242fc81ff2bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588050"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Jak używać Azure Monitor skoroszytów dla raportów Azure Active Directory

> [!IMPORTANT]
> Aby zoptymalizować bazowe zapytania w tym skoroszycie, kliknij pozycję "Edytuj", kliknij ikonę ustawienia i wybierz obszar roboczy, w którym chcesz uruchomić te zapytania. Skoroszyty domyślnie będą wybierać wszystkie obszary robocze, w których są rozsyłane dzienniki usługi Azure AD. 

Czy chcesz:

- Rozumiesz wpływ [zasad dostępu warunkowego](../conditional-access/overview.md) na logowanie użytkowników?

- Rozwiązywanie problemów z błędami logowania w celu uzyskania lepszych wglądu w kondycję logowania w organizacji i szybkie rozwiązywanie problemów?

- Wiedzieć, kto używa starszych uwierzytelnień do logowania się do środowiska? (Przez [blokowanie starszego uwierzytelniania](../conditional-access/block-legacy-authentication.md)można poprawić ochronę dzierżawy).

- Czy musisz zrozumieć wpływ zasad dostępu warunkowego w dzierżawie?

- Czy chcesz, aby przeglądanie: zarejestrowano logowania, skoroszyt raportuje liczbę użytkowników, którym udzielono lub odmówiono dostępu, a także liczbę użytkowników, których zasady dostępu warunkowego zostały pominięte podczas uzyskiwania dostępu do zasobów?

- Interesuje się opracowywanie dokładniejszych informacji o: szczegóły skoroszytu na warunek, tak aby wpływ zasad można było zasubskrybować według warunku, w tym platformy urządzeń, stanu urządzenia, aplikacji klienckiej, ryzyka związanego z logowaniem, lokalizacji i aplikacji?

- Uzyskaj dokładniejsze informacje o zapytaniach dziennika logowania, w tym skoroszycie przedstawiono liczbę użytkowników, którym udzielono lub odmówiono dostępu, a także liczbę użytkowników, których zasady dostępu warunkowego zostały pominięte podczas uzyskiwania dostępu do zasobów.

- Aby ułatwić rozwiązanie tych pytań, Azure Active Directory udostępnia skoroszyty do monitorowania. [Azure monitor skoroszyty](../../azure-monitor/visualize/workbooks-overview.md) łączą teksty, zapytania analityczne, metryki i parametry w rozbudowanych raportach interaktywnych.



W tym artykule:

- Przyjęto założenie, że wiesz już, jak [tworzyć interaktywne raporty przy użyciu skoroszytów monitorowania](../../azure-monitor/visualize/workbooks-overview.md).

- Wyjaśnia, jak używać skoroszytów do monitorowania, aby zrozumieć wpływ zasad dostępu warunkowego, rozwiązywać problemy z błędami logowania i identyfikować starsze uwierzytelnienia.
 


## <a name="prerequisites"></a>Wymagania wstępne

Aby używać skoroszytów monitorowania, potrzebne są:

- Dzierżawa Azure Active Directory z licencją premium (P1 lub P2). Dowiedz się [, jak uzyskać licencję Premium](../fundamentals/active-directory-get-started-premium.md).

- [Obszar roboczy usługi Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

- [Dostęp](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) do obszaru roboczego usługi log Analytics
- Poniższe role w Azure Active Directory (Jeśli uzyskujesz dostęp do Log Analytics za poorednictwem portalu Azure Active Directory)
    - Administrator zabezpieczeń
    - Czytelnik zabezpieczeń
    - Czytelnik raportu
    - Administrator globalny

## <a name="roles"></a>Role
Aby zarządzać skoroszytami, musisz mieć jedną z następujących ról oraz mieć [dostęp do podstawowego](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) obszaru roboczego log Analytics:
-   Administrator globalny
-   Administrator zabezpieczeń
-   Czytelnik zabezpieczeń
-   Czytelnik raportu
-   Administrator aplikacji

## <a name="workbook-access"></a>Dostęp do skoroszytu 

Aby uzyskać dostęp do skoroszytów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do **Azure Active Directory**  >  **monitorowanie**  >  **skoroszytów**. 

1. Wybierz raport lub szablon lub na pasku narzędzi wybierz pozycję **Otwórz**. 

![Znajdowanie skoroszytów Azure Monitor w usłudze Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Analiza logowania

Aby uzyskać dostęp do skoroszytu analizy logowania, w sekcji **użycie** wybierz pozycję **logowania**. 

Ten skoroszyt zawiera następujące trendy dotyczące logowania:

- Wszystkie logowania

- Powodzenie

- Akcja oczekującego użytkownika

- Niepowodzenie

Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Apps

- Użytkownicy

![Analiza logowania](./media/howto-use-azure-monitor-workbooks/43.png)


Dla każdego trendu otrzymujesz podział według następujących kategorii:

- Lokalizacja

    ![Logowania według lokalizacji](./media/howto-use-azure-monitor-workbooks/45.png)

- Urządzenie

    ![Logowania według urządzenia](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Logowania przy użyciu starszego uwierzytelniania 


Aby uzyskać dostęp do skoroszytu w celu logowania przy użyciu [starszego uwierzytelniania](../conditional-access/block-legacy-authentication.md), w sekcji **użycie** wybierz pozycję **logowania przy użyciu starszego uwierzytelniania**. 

Ten skoroszyt zawiera następujące trendy dotyczące logowania:

- Wszystkie logowania

- Powodzenie


Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Apps

- Użytkownicy

- Protokoły

![Logowania przy użyciu starszego uwierzytelniania](./media/howto-use-azure-monitor-workbooks/47.png)


Dla każdego trendu można uzyskać Podział według aplikacji i protokołu.

![Starsze-uwierzytelnianie logowania według aplikacji i protokołu](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Logowania przez dostęp warunkowy 


Aby uzyskać dostęp do skoroszytu w przypadku logowania za pomocą [zasad dostępu warunkowego](../conditional-access/overview.md), w sekcji **dostęp warunkowy** wybierz pozycję **logowania przez dostęp warunkowy**. 

Ten skoroszyt zawiera trendy dotyczące wyłączonych logowań. Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Apps

- Użytkownicy

![Logowania przy użyciu dostępu warunkowego](./media/howto-use-azure-monitor-workbooks/49.png)


W przypadku wyłączonych logowań otrzymujesz podział według stanu dostępu warunkowego.

![Zrzut ekranu przedstawia stan dostępu warunkowego i ostatnie logowania.](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Szczegółowe informacje na temat dostępu warunkowego

### <a name="overview"></a>Omówienie

Skoroszyty zawierają zapytania dzienników logowania, które mogą pomóc administratorom IT monitorować wpływ zasad dostępu warunkowego w ich dzierżawie. Istnieje możliwość raportowania liczby użytkowników, którym udzielono lub odmówiono dostępu. Skoroszyt zawiera szczegółowe informacje na temat liczby użytkowników, którzy mają pominięte zasady dostępu warunkowego na podstawie atrybutów tych użytkowników w momencie logowania. Zawiera szczegółowe informacje na temat stanu, dzięki czemu wpływ zasad może być zapisywany na warunek, w tym na platformie urządzenia, stanie urządzenia, aplikacji klienckiej, zagrożeniu logowania, lokalizacji i aplikacji.

### <a name="instructions"></a>Instrukcje 
Aby uzyskać dostęp do skoroszytu na potrzeby wglądu w szczegółowe dane, wybierz skoroszyt **dostęp warunkowy** w sekcji dostęp warunkowy. Ten skoroszyt pokazuje oczekiwany wpływ poszczególnych zasad dostępu warunkowego w dzierżawie. Wybierz co najmniej jedną zasadę dostępu warunkowego z listy rozwijanej i Zawęź zakres skoroszytu, stosując następujące filtry: 

- **Zakres czasu**

- **Użytkownik**

- **Aplikacje**

- **Widok danych**

![Zrzut ekranu przedstawia okienko dostęp warunkowy, w którym można wybrać zasady dostępu warunkowego.](./media/howto-use-azure-monitor-workbooks/access-insights.png)


Podsumowanie wpływu pokazuje liczbę użytkowników lub logowań, dla których wybrane zasady miały określony wynik. Suma to liczba użytkowników lub logowania, dla których wybrane zasady zostały ocenione w wybranym zakresie czasu. Kliknij kafelek, aby odfiltrować dane w skoroszycie według tego typu wyniku. 

![Zrzut ekranu przedstawia kafelki, które mają być używane do filtrowania wyników, takich jak całkowita, sukces i niepowodzenie.](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Ten skoroszyt pokazuje także wpływ wybranych zasad z podziałem na sześć warunków: 
- **Stan urządzenia**
- **Platforma urządzeń**
- **Aplikacje klienckie**
- **Ryzyko związane z logowaniem**
- **Lokalizacja**
- **Aplikacje**

![Zrzut ekranu przedstawia szczegóły z całkowitego filtru logowania.](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Możesz również zbadać poszczególne logowania, przefiltrowane według parametrów wybranych w skoroszycie. Wyszukaj poszczególnych użytkowników, posortowane według częstotliwości logowania i Wyświetl ich odpowiednie zdarzenia logowania. 

![Zrzut ekranu przedstawia poszczególne logowania, które można przejrzeć.](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Operacje logowania przez przyznanie kontroli

Aby uzyskać dostęp do skoroszytu w celu logowania przez [przyznanie kontroli](../conditional-access/controls.md), w sekcji **dostęp warunkowy** wybierz pozycję **logowania przez przyznanie kontrolek**. 

Ten skoroszyt zawiera następujące wyłączane trendy dotyczące logowania:

- Wymaganie uwierzytelniania wieloskładnikowego
 
- Wymaganie warunków użytkowania

- Wymagaj zasad zachowania poufności informacji

- Inne


Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Apps

- Użytkownicy

![Operacje logowania przez przyznanie kontroli](./media/howto-use-azure-monitor-workbooks/50.png)


Dla każdego trendu można uzyskać Podział według aplikacji i protokołu.

![Podział najnowszych logowań](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analiza niepowodzeń logowania

Użyj skoroszytu **analizy błędów logowania** , aby rozwiązać problemy z:

- Logowania
- Zasady dostępu warunkowego
- Starsza wersja uwierzytelniania 


Aby uzyskać dostęp do logowania za pomocą danych dostępu warunkowego, w sekcji **Rozwiązywanie problemów** wybierz pozycję **logowania przy użyciu starszego uwierzytelniania**. 

Ten skoroszyt zawiera następujące trendy dotyczące logowania:

- Wszystkie logowania

- Powodzenie

- Oczekująca akcja

- Niepowodzenie


Każdy trend można filtrować według następujących kategorii:

- Przedział czasu

- Apps

- Użytkownicy

![Rozwiązywanie problemów z logowaniem](./media/howto-use-azure-monitor-workbooks/52.png)


Aby pomóc w rozwiązywaniu problemów z logowaniem, Azure Monitor zapewnia podział według następujących kategorii:

- Najważniejsze błędy

    ![Podsumowanie najważniejszych błędów](./media/howto-use-azure-monitor-workbooks/53.png)

- Logowania czekające na akcję użytkownika

    ![Podsumowanie logowań oczekujących na akcję użytkownika](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Następne kroki

[Twórz interaktywne raporty przy użyciu skoroszytów monitorowania](../../azure-monitor/visualize/workbooks-overview.md).