---
title: Rozwiązywanie problemów z logowaniem przy użyciu dostępu warunkowego — Azure Active Directory
description: W tym artykule opisano, co należy zrobić, gdy zasady dostępu warunkowego powodują nieoczekiwane wyniki
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f722977329bd5d79d4d0e410a29c730faf00c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92145087"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Rozwiązywanie problemów z logowaniem przy użyciu dostępu warunkowego

Informacje przedstawione w tym artykule mogą służyć do rozwiązywania nieoczekiwanych wyników logowania związanych z dostępem warunkowym przy użyciu komunikatów o błędach i dziennika logowania usługi Azure AD.

## <a name="select-all-consequences"></a>Wybierz konsekwencje "wszystkie"

Struktura dostępu warunkowego zapewnia dużą elastyczność konfiguracji. Jednak doskonałe elastyczność oznacza, że należy uważnie przejrzeć każdą zasadę konfiguracji przed jej zwolnieniem, aby uniknąć niepożądanych wyników. W tym kontekście należy zwrócić szczególną uwagę na przydziały mające wpływ na kompletne zestawy, takie jak **Wszyscy użytkownicy/grupy/aplikacje w chmurze**.

Organizacje powinny unikać następujących konfiguracji:

**Dla wszystkich użytkowników wszystkie aplikacje w chmurze:**

- **Blokuj dostęp** — ta konfiguracja blokuje całą organizację.
- **Wymagaj, aby urządzenie było oznaczone jako zgodne** — dla użytkowników, którzy nie zarejestrowali jeszcze urządzeń, te zasady blokują dostęp do portalu usługi Intune. Jeśli jesteś administratorem programu bez zarejestrowanego urządzenia, ta zasada uniemożliwia powrót do Azure Portal, aby zmienić zasady.
- **Wymagaj hybrydowego urządzenia dołączonego do domeny usługi Azure AD** — ta zasada blokuje również możliwość blokowania dostępu dla wszystkich użytkowników w organizacji, jeśli nie posiadają hybrydowego urządzenia dołączonego do usługi Azure AD.
- **Wymagaj zasad ochrony aplikacji** — ten blok dostępu blokuje również możliwość blokowania dostępu dla wszystkich użytkowników w organizacji, jeśli nie masz zasad usługi Intune. Jeśli jesteś administratorem bez aplikacji klienckiej z zasadami ochrony aplikacji usługi Intune, te zasady uniemożliwiają powrót do portali, takich jak usługa Intune i platforma Azure.

**Dla wszystkich użytkowników, wszystkie aplikacje w chmurze, wszystkie platformy urządzeń:**

- **Blokuj dostęp** — ta konfiguracja blokuje całą organizację.

## <a name="conditional-access-sign-in-interrupt"></a>Przerwanie logowania dostępu warunkowego

Pierwszy sposób polega na przejrzeniu wyświetlonego komunikatu o błędzie. W przypadku problemów z logowaniem przy użyciu przeglądarki sieci Web sama strona błędu zawiera szczegółowe informacje. Te informacje mogą zawierać opis problemu, który może sugerować rozwiązanie.

![Wymagane jest logowanie urządzenia zgodnego z błędami](./media/troubleshoot-conditional-access/image1.png)

W powyższym błędzie komunikat stwierdza, że dostęp do aplikacji jest możliwy tylko z urządzeń lub aplikacji klienckich, które spełniają zasady zarządzania urządzeniami przenośnymi firmy. W takim przypadku aplikacja i urządzenie nie są zgodne z tymi zasadami.

## <a name="azure-ad-sign-in-events"></a>Zdarzenia logowania do usługi Azure AD

Druga metoda uzyskiwania szczegółowych informacji na temat przerwania logowania polega na przejrzeniu zdarzeń logowania usługi Azure AD w celu sprawdzenia, które zasady dostępu warunkowego lub zasady zostały zastosowane.

Więcej informacji o problemie można znaleźć, klikając **więcej szczegółów** na stronie błędu początkowego. Kliknięcie **większej ilości szczegółów** spowoduje ujawnienie informacji dotyczących rozwiązywania problemów, które są przydatne podczas wyszukiwania zdarzeń logowania usługi Azure AD dla konkretnego zdarzenia awarii, które użytkownik wykryje lub podczas otwierania zdarzenia pomocy technicznej w firmie Microsoft.

![Więcej informacji na temat dostępu warunkowego do logowania w przeglądarce sieci Web.](./media/troubleshoot-conditional-access/image2.png)

Aby dowiedzieć się, które zasady dostępu warunkowego lub zasady są stosowane, i dlaczego należy wykonać następujące czynności.

1. Zaloguj się do **Azure Portal** jako Administrator globalny, administrator zabezpieczeń lub czytnik globalny.
1. Przejdź do **Azure Active Directory**  >  **logowania**.
1. Znajdź zdarzenie logowania do przeglądu. Dodaj lub Usuń filtry i kolumny, aby odfiltrować zbędne informacje.
   1. Dodaj filtry, aby zawęzić zakres:
      1. **Identyfikator korelacji** w przypadku konkretnego zdarzenia do zbadania.
      1. **Dostęp warunkowy** , aby zobaczyć błąd zasad i powodzenie. Ustaw zakres filtru, aby pokazać tylko błędy, aby ograniczyć wyniki.
      1. **Nazwa użytkownika** , aby wyświetlić informacje powiązane z określonymi użytkownikami.
      1. **Data** zakresu do podanego przedziału czasu.

   ![Wybieranie filtru dostępu warunkowego w dzienniku logowania](./media/troubleshoot-conditional-access/image3.png)

1. Po znalezieniu zdarzenia logowania odpowiadającego błędowi logowania użytkownika wybierz kartę **dostęp warunkowy** . Na karcie dostęp warunkowy zostaną wyświetlone określone zasady lub zasady, które spowodowały przerwanie logowania.
   1. Informacje na karcie **Rozwiązywanie problemów i pomoc techniczna** mogą stanowić wyraźny powód, dla którego logowanie nie powiodło się, takie jak urządzenie, które nie spełnia wymagań dotyczących zgodności.
   1. Aby dowiedzieć się więcej, przejdź do szczegółów konfiguracji zasad, klikając **nazwę zasad**. Kliknięcie **nazwy zasad** spowoduje wyświetlenie interfejsu użytkownika konfiguracji zasad dla wybranych zasad na potrzeby przeglądania i edytowania.
   1. **Szczegóły dotyczące** **użytkownika** i urządzenia, które były używane na potrzeby oceny zasad dostępu warunkowego, są również dostępne na kartach **podstawowe informacje**, **Lokalizacja**, **Informacje o urządzeniu**, **szczegóły uwierzytelniania** i **dodatkowe szczegóły** dotyczące zdarzenia logowania.

### <a name="policy-details"></a>Szczegóły zasad

Wybranie wielokropka po prawej stronie zasad w zdarzeniu logowania powoduje wyświetlenie szczegółów zasad. Dzięki temu administratorzy mogą dowiedzieć się więcej o tym, dlaczego zasady zostały pomyślnie zastosowane.

   ![Karta dostęp warunkowy zdarzenia logowania](./media/troubleshoot-conditional-access/image5.png)

   ![Szczegóły zasad (wersja zapoznawcza)](./media/troubleshoot-conditional-access/policy-details.png)

Po lewej stronie znajdują się szczegółowe informacje zebrane podczas logowania, a po prawej stronie znajdują się szczegółowe informacje o tym, czy te szczegóły spełniają wymagania zastosowania zasad dostępu warunkowego. Zasady dostępu warunkowego są stosowane tylko wtedy, gdy wszystkie warunki są spełnione lub nie zostały skonfigurowane.

Jeśli informacje w zdarzeniu nie są wystarczające, aby zrozumieć wyniki logowania lub dostosować zasady w celu uzyskania pożądanych wyników, można otworzyć zdarzenie obsługi. Przejdź do karty **Rozwiązywanie problemów i pomoc techniczna** dla tego zdarzenia logowania, a następnie wybierz pozycję **Utwórz nowe żądanie obsługi**.

![Karta Rozwiązywanie problemów i obsługa zdarzenia logowania](./media/troubleshoot-conditional-access/image6.png)

Podczas przesyłania zdarzenia podaj identyfikator i datę i godzinę żądania logowania w szczegółach przesyłania zdarzenia. Te informacje umożliwią działowi pomocy technicznej firmy Microsoft znalezienie danego zdarzenia.

### <a name="conditional-access-error-codes"></a>Kody błędów dostępu warunkowego

| Kod błędu logowania | Ciąg błędu |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>Co należy zrobić, jeśli masz zablokowany Azure Portal?

Jeśli masz zablokowany dostęp do Azure Portal z powodu niepoprawnego ustawienia zasad dostępu warunkowego:

- Sprawdź, czy w organizacji znajdują się inni administratorzy, którzy nie zostali jeszcze Zablokowani. Administrator z dostępem do Azure Portal może wyłączyć zasady wpływające na logowanie. 
- Jeśli żadna z administratorów w organizacji nie może zaktualizować zasad, Prześlij żądanie pomocy technicznej. Pomoc techniczna firmy Microsoft może przejrzeć i po potwierdzeniu zaktualizować zasady dostępu warunkowego, które uniemożliwiają dostęp.

## <a name="next-steps"></a>Następne kroki

- [Raporty dotyczące logowań w portalu Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Rozwiązywanie problemów z dostępem warunkowym przy użyciu narzędzia What If](troubleshoot-conditional-access-what-if.md)
