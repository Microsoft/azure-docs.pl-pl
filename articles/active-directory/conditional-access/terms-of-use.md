---
title: Warunki użytkowania-Azure Active Directory | Microsoft Docs
description: Zacznij korzystać z Azure Active Directory warunki użytkowania, aby przedstawić informacje pracownikom lub Gościom przed uzyskaniem dostępu.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 12/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3e64b0af455ab1f84653093b26654530ee3dfab
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232781"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory warunki użytkowania

Zasady użytkowania usługi Azure AD zapewniają prostą metodę, która może być używana przez organizacje do prezentowania informacji użytkownikom końcowym. Dzięki tej prezentacji użytkownicy mogą zapoznać się z istotnymi zastrzeżeniami do wymagań prawnych lub wymagań dotyczących zgodności. W tym artykule opisano sposób rozpoczynania pracy z zasadami użytkowania (warunków użytkowania).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Wideo z omówieniem

Poniższy klip wideo zawiera krótkie omówienie warunków użytkowania.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Aby uzyskać dodatkowe filmy wideo, zobacz:
- [Jak wdrożyć zasady warunków użytkowania w programie Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Jak wdrożyć zasady użytkowania w Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Co mogę zrobić z warunkami użytkowania?

Zasady użytkowania usługi Azure AD mają następujące możliwości:

- Przed uzyskaniem dostępu należy wymagać od pracowników lub Gości akceptacji zasad użytkowania.
- Przed uzyskaniem dostępu należy wymagać od pracowników lub Gości zaakceptowania zasad użytkowania na każdym urządzeniu.
- Wymaganie, aby pracownicy lub Goście zaakceptowali warunki użytkowania zasad zgodnie z cyklicznym harmonogramem.
- Przed zarejestrowaniem informacji o zabezpieczeniach w usłudze Azure AD Multi-Factor Authentication (MFA) wymagane jest, aby pracownicy lub Goście zaakceptowali warunki użytkowania.
- Przed zarejestrowaniem informacji o zabezpieczeniach w usłudze Azure AD Resetowanie hasła (SSPR) należy wymagać od pracowników akceptacji warunków użytkowania.
- Zaprezentowanie ogólnych zasad użytkowania dla wszystkich użytkowników w organizacji.
- Istnieją określone warunki użytkowania zasad na podstawie atrybutów użytkownika (np. warunki dla lekarzy różnią się od warunków dla pielęgniarek, a pracownicy krajowi mają inne warunki niż pracownicy międzynarodowi) za pomocą [grup dynamicznych](../enterprise-users/groups-dynamic-membership.md).
- Podczas uzyskiwania dostępu do aplikacji o wysokiej wpływ na działalność biznesową, takich jak Salesforce, należy przedstawić określone warunki użytkowania.
- Obecne warunki użytkowania zasad w różnych językach.
- Lista użytkowników, którzy mają lub nie zaakceptowali zasad użytkowania.
- Pomoc w zasadach zachowania poufności informacji.
- Wyświetla dziennik warunków użytkowania zasad dotyczących zgodności i inspekcji.
- Utwórz zasady użytkowania i zarządzaj nimi za pomocą [Microsoft Graph interfejsów API](/graph/api/resources/agreement?view=graph-rest-beta) (obecnie w wersji zapoznawczej).

## <a name="prerequisites"></a>Wymagania wstępne

Aby móc korzystać z zasad dotyczących korzystania z usługi Azure AD i konfigurować je, musisz dysponować:

- Subskrypcja usługi Azure AD w wersji Premium P1, Premium P2, EMS E3 lub EMS E5.
   - Jeśli nie masz żadnej z tych subskrypcji, możesz [uzyskać dostęp do usługi Azure AD w wersji Premium](../fundamentals/active-directory-get-started-premium.md) lub [włączyć wersję próbnej usługi Azure AD w wersji Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jedno z następujących kont administratora katalogu, który chcesz skonfigurować:
   - Administrator globalny
   - Administrator zabezpieczeń
   - Administrator dostępu warunkowego

## <a name="terms-of-use-document"></a>Dokument z warunkami użytkowania

Zasady używania warunków użytkowania usługi Azure AD używają formatu PDF do prezentowania zawartości. Zawartość ta może być dowolna i obejmować na przykład istniejące dokumenty kontraktowe, umożliwiając gromadzenie umów użytkowników końcowych podczas logowania. W celu obsługi użytkowników na urządzeniach przenośnych zalecanym rozmiarem czcionki w pliku PDF jest 24 punkty.

## <a name="add-terms-of-use"></a>Dodaj warunki użytkowania

Po sfinalizowaniu dokumentu zasad użytkowania Użyj następującej procedury, aby ją dodać.

1. Zaloguj się do platformy Azure jako Administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

    ![Dostęp warunkowy — blok Warunki użytkowania](./media/terms-of-use/tou-blade.png)

1. Kliknij pozycję **Nowe warunki**.

    ![Okienko nowe warunki użytkowania umożliwiające określenie warunków użytkowania](./media/terms-of-use/new-tou.png)

1. W polu **Nazwa** wprowadź nazwę zasad dotyczących warunków użytkowania, które będą używane w Azure Portal.
1. W polu **Nazwa wyświetlana** wprowadź tytuł wyświetlany użytkownikom podczas logowania.
1. W przypadku **warunki użytkowania dokumentu** przejdź do pliku PDF z ustalonymi zasadami użytkowania i wybierz go.
1. Wybierz język dokumentu zasad użytkowania. Opcja język umożliwia przekazanie wielu zasad użytkowania, z których każdy ma inny język. Wersja zasad warunków użytkowania, które zobaczy użytkownik końcowy, będzie oparta na preferencjach przeglądarki.
1. Aby wymagać od użytkowników końcowych wyświetlania zasad użytkowania przed ich zaakceptowaniem, ustaw opcję **Wymagaj, aby użytkownicy mogli rozwijać warunki użytkowania** . 
1. Aby wymagać od użytkowników końcowych akceptacji zasad użytkowania na wszystkich urządzeniach, z których uzyskują dostęp, ustaw opcję **Wymagaj od użytkowników zgody na każde urządzenie** **na.** Jeśli ta opcja jest włączona, użytkownicy mogą być zobowiązani do instalowania dodatkowych aplikacji. Aby uzyskać więcej informacji, zobacz [warunki użytkowania poszczególnych urządzeń](#per-device-terms-of-use).
1. Jeśli chcesz, aby zasady użytkowania były wygaśnie zgodnie z harmonogramem, ustaw **wygasanie** z **dniem**. W przypadku wybrania opcji włączone są wyświetlane dwa dodatkowe ustawienia harmonogramu.

    ![Ustawienia wygasania, aby ustawić datę początkową, częstotliwość i czas trwania](./media/terms-of-use/expire-consents.png)

1. Użyj ustawień **Data wygaśnięcia** i **częstotliwość** , aby określić harmonogram wygasania zasad użytkowania. W poniższej tabeli przedstawiono wyniki dla kilku przykładowych ustawień:

   | Wygasanie od | Częstotliwość | Wynik |
   | --- | --- | --- |
   | Dzisiejsza data  | Miesięcznie | Rozpoczynając od dzisiaj, użytkownicy muszą zaakceptować zasady użytkowania i ponownie akceptować każdy miesiąc. |
   | Data w przyszłości  | Miesięcznie | Rozpoczynając od dzisiaj, użytkownicy muszą zaakceptować zasady użytkowania. Po upływie tego czasu termin wysłane zostanie wygaśnie, a następnie użytkownicy muszą ponownie zaakceptować każdy miesiąc.  |

   Na przykład jeśli ustawisz wygaśnięcie, rozpoczynając od **1 stycznia** , a częstotliwość na **co miesiąc**, poniżej przedstawiono sposób wygaśnięcia może wystąpić w przypadku dwóch użytkowników:

   | Użytkownik | Data pierwszej akceptacji | Data pierwszego wygaśnięcia | Druga data wygaśnięcia | Data wygaśnięcia trzeciej |
   | --- | --- | --- | --- | --- |
   | Robert | 1 sty | 1 lutego | Mar 1 | 1 kwietnia |
   | Bob | 15 sty | 1 lutego | Mar 1 | 1 kwietnia |

1. Użyj ustawienia **czas trwania przed ponowną akceptacją (w dniach)** , aby określić liczbę dni, po której użytkownik musi ponownie zaakceptować warunki użytkowania. Dzięki temu użytkownicy mogą postępować zgodnie z własnymi harmonogramami. Na przykład jeśli ustawisz czas trwania na **30** dni, poniżej przedstawiono sposób wygaśnięcia może wystąpić w przypadku dwóch użytkowników:

   | Użytkownik | Data pierwszej akceptacji | Data pierwszego wygaśnięcia | Druga data wygaśnięcia | Data wygaśnięcia trzeciej |
   | --- | --- | --- | --- | --- |
   | Robert | 1 sty | 31 stycznia | Mar 2 | 1 kwietnia |
   | Bob | 15 sty | 14 lutego | Mar 16 | Kwi 15 |

   Możliwe jest użycie limitów czasu **wygaśnięcia** i **czas trwania przed ponowną akceptacją (w dniach)** , ale zazwyczaj używasz jednej lub drugiej.

1. W obszarze **dostęp warunkowy** Użyj listy **szablon zasady wymuszania dostępu warunkowego** , aby wybrać szablon do wymuszania zasad użytkowania.

    ![Lista rozwijana dostęp warunkowy do wybierania szablonu zasad](./media/terms-of-use/conditional-access-templates.png)

   | Template | Opis |
   | --- | --- |
   | **Dostęp do aplikacji w chmurze dla wszystkich Gości** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich Gości i wszystkich aplikacji w chmurze. Te zasady mają wpływ na Azure Portal. Po utworzeniu tej czynności może być konieczne wylogowanie się i zalogowanie się. |
   | **Dostęp do aplikacji w chmurze dla wszystkich użytkowników** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich użytkowników i aplikacji w chmurze. Te zasady mają wpływ na Azure Portal. Po utworzeniu tego konta będzie wymagane wylogowanie się i zalogowanie się. |
   | **Zasady niestandardowe** | Wybierz użytkowników, grupy i aplikacje, do których zostaną zastosowane zasady dotyczące warunków użytkowania. |
   | **Utwórz zasady dostępu warunkowego później** | Te zasady użytkowania zostaną wyświetlone na liście Udziel kontroli podczas tworzenia zasad dostępu warunkowego. |

   >[!IMPORTANT]
   >Kontrolki zasad dostępu warunkowego (w tym zasady użytkowania) nie obsługują wymuszania na kontach usług. Zalecamy wykluczenie wszystkich kont usług z zasad dostępu warunkowego.

    Niestandardowe zasady dostępu warunkowego umożliwiają szczegółowe warunki użytkowania zasad, w dół do określonej aplikacji w chmurze lub grupy użytkowników. Aby uzyskać więcej informacji, zobacz [Szybki Start: Wymagaj akceptacji warunków użytkowania przed uzyskaniem dostępu do aplikacji w chmurze](require-tou.md).

1. Kliknij pozycję **Utwórz**.

    W przypadku wybrania niestandardowego szablonu dostępu warunkowego zostanie wyświetlony nowy ekran, który umożliwia utworzenie niestandardowych zasad dostępu warunkowego.

   ![Nowe okienko dostępu warunkowego w przypadku wybrania szablonu zasad dostępu warunkowego](./media/terms-of-use/custom-policy.png)

   Teraz powinny zostać wyświetlone nowe zasady użytkowania.

   ![Nowe warunki użytkowania wymienione w bloku warunki użytkowania](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Wyświetl raport dotyczący użytkowników, którzy zaakceptowali i odrzucili

W bloku Warunki użytkowania znajduje się liczba użytkowników, którzy je zaakceptowali i odrzucili. Te liczniki i osoby, które zaakceptowały/odrzucają, są przechowywane w okresie istnienia zasad użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

    ![W bloku Warunki użytkowania wyświetlana jest liczba zaakceptowanych i odrzuconych elementów przez użytkownika](./media/terms-of-use/view-tou.png)

1. W przypadku zasad dotyczących warunków użytkowania kliknij numery w obszarze **zaakceptowane** lub **odrzucone** , aby wyświetlić bieżący stan dla użytkowników.

    ![Okienko zaakceptowanych Warunki użytkowania zawiera listę użytkowników, którzy zaakceptowali](./media/terms-of-use/accepted-tou.png)

1. Aby wyświetlić historię poszczególnych użytkowników, kliknij przycisk wielokropka (**...**), a następnie **Wyświetl historię**.

    ![Wyświetl menu kontekstowe dla użytkownika](./media/terms-of-use/view-history-menu.png)

   W okienku Wyświetl historię zostanie wyświetlona historia wszystkich zatwierdzeń, odrzuconych i wygaśnięć.

   ![Okienko historia wyświetlania wyświetla listę akceptowanych, odrzuconych i wygasających użytkowników](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Wyświetlanie dzienników inspekcji usługi Azure AD

Jeśli chcesz wyświetlić dodatkowe działanie, zasady dotyczące warunków użytkowania usługi Azure AD obejmują dzienniki inspekcji. Każda zgoda użytkownika wyzwala zdarzenie w dziennikach inspekcji przechowywanych przez **30 dni**. Te dzienniki możesz wyświetlić w portalu lub pobrać jako plik CSV.

Aby rozpocząć pracę z dziennikami inspekcji usługi Azure AD, wykonaj czynności opisane w poniższej procedurze:

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz zasady użytkowania.
1. Kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Warunki użytkowania blok z wyróżnioną opcją Wyświetl dzienniki inspekcji](./media/terms-of-use/audit-tou.png)

1. Na ekranie dzienników inspekcji usługi Azure AD można filtrować informacje przy użyciu podanych list, aby określić docelowe informacje dziennika inspekcji.

    Można również kliknąć pozycję **Pobierz**, aby pobrać informacje w pliku CSV do użytku lokalnego.

   ![Ekran dzienników inspekcji usługi Azure AD — Data, zasady docelowe, zainicjowane przez i działanie](./media/terms-of-use/audit-logs-tou.png)

   Po kliknięciu dziennika zostanie wyświetlone okienko z dodatkowymi szczegółami działania.

   ![Szczegóły działania dziennika przedstawiającego działanie, stan działania, zainicjowane przez, zasady docelowe](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Jakie warunki użytkowania wyglądają dla użytkowników

Gdy zasady użytkowania zostaną utworzone i wymuszone, użytkownicy, którzy znajdują się w zakresie, zobaczą następujący ekran podczas logowania.

![Przykładowe warunki użytkowania, które pojawiają się po zalogowaniu się użytkownika](./media/terms-of-use/user-tou.png)

Użytkownicy mogą wyświetlać warunki użytkowania zasad i, w razie potrzeby, powiększać i pomniejszać.

![Widok warunków użytkowania z przyciskami powiększenia](./media/terms-of-use/zoom-buttons.png)

Na poniższym ekranie przedstawiono sposób, w jaki zasady użytkowania są używane na urządzeniach przenośnych.

![Przykładowe warunki użytkowania, które pojawiają się, gdy użytkownik loguje się na urządzeniu przenośnym](./media/terms-of-use/mobile-tou.png)

Użytkownicy muszą zaakceptować warunki użytkowania tylko raz, a zasady użytkowania nie będą ponownie widoczne przy kolejnych logowaniach.

### <a name="how-users-can-review-their-terms-of-use"></a>Jak użytkownicy mogą przeglądać swoje warunki użytkowania

Użytkownicy mogą przeglądać i przeglądać Warunki użytkowania, które zaakceptowali, wykonując poniższą procedurę.

1. Zaloguj się do witryny [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. W prawym górnym rogu kliknij swoją nazwę i wybierz pozycję **profil**.

    ![Witryna moje aplikacje z otwartym okienkiem użytkownika](./media/terms-of-use/tou14.png)

1. Na stronie Profil kliknij pozycję **Przejrzyj warunki użytkowania**.

    ![Strona profilu dla użytkownika pokazująca link warunki użytkowania](./media/terms-of-use/tou13a.png)

1. Z tego miejsca możesz zapoznać się z zaakceptowanymi zasadami użytkowania.

## <a name="edit-terms-of-use-details"></a>Edytuj szczegóły warunków użytkowania

Można edytować niektóre szczegóły zasad użytkowania, ale nie można modyfikować istniejącego dokumentu. Poniższa procedura opisuje sposób edycji szczegółów.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz zasady dotyczące warunków użytkowania, które chcesz edytować.
1. Kliknij pozycję **Edytuj warunki**.
1. W okienku edytowanie warunków użytkowania można zmienić następujące elementy:
    - **Nazwa** — jest to wewnętrzna nazwa warunków użytkowania, która nie jest udostępniana użytkownikom końcowym
    - **Nazwa wyświetlana** — jest to nazwa, którą użytkownicy końcowi mogą zobaczyć podczas wyświetlania warunków użytkowania
    - **Wymaganie, aby użytkownicy mogli rozwijać warunki użytkowania** — ustawienie tej opcji **na wartość włączone** spowoduje wymuszenie zakończenia używania dokumentu zasad użytkowania przez użytkownika przed jego zaakceptowaniem.
    - Przeglądania Możesz **zaktualizować istniejące warunki użytkowania** dokumentu
    - Możesz dodać język do istniejącej warunków użytkowania

   Jeśli istnieją inne ustawienia, które chcesz zmienić, takie jak dokument PDF, użytkownicy muszą wyrazić zgodę na każde urządzenie, wygasnąć, czas trwania przed ponowną akceptacją lub zasady dostępu warunkowego, należy utworzyć nowe zasady warunków użytkowania.

    ![Edycja przedstawiająca różne opcje języka ](./media/terms-of-use/edit-terms-use.png)

1. Gdy skończysz, kliknij przycisk **Zapisz** , aby zapisać zmiany.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Aktualizowanie wersji lub pliku PDF istniejących warunków użytkowania

1.  Zaloguj się do platformy Azure i przejdź do [warunki użytkowania](https://aka.ms/catou)
2.  Wybierz zasady dotyczące warunków użytkowania, które chcesz edytować.
3.  Kliknij pozycję **Edytuj warunki**.
4.  W przypadku języka, w którym chcesz zaktualizować nową wersję, kliknij przycisk **Aktualizuj** w kolumnie Akcja.

    ![Okienko Edycja warunków użytkowania z pokazywaniem opcji Nazwa i rozwiń](./media/terms-of-use/edit-terms-use.png)

5.  W okienku po prawej stronie Przekaż plik PDF dla nowej wersji.
6.  Dostępna jest również opcja przełączania **w tym miejscu, aby wymagać** od użytkowników zaakceptowania nowej wersji przy następnym logowaniu. Jeśli potrzebujesz, aby użytkownicy mogli je ponownie zaakceptować, następnym razem próbującym uzyskać dostęp do zasobu zdefiniowanego w zasadach dostępu warunkowego zostanie wyświetlony monit o zaakceptowanie tej nowej wersji. Jeśli nie chcesz, aby użytkownicy mogli się ponownie akceptować, ich Poprzednia zgoda będzie aktualna, a tylko nowi użytkownicy, którzy nie wyraziły zgody przed lub których ważność wygaśnie, będą widzieć nową wersję.

    ![Opcja Edytuj warunki użytkowania ponownie Zaakceptuj zaznaczone](./media/terms-of-use/re-accept.png)

7.  Po przekazaniu nowego pliku PDF i podjęciu decyzji o ponownym zaakceptowaniu kliknij pozycję Dodaj w dolnej części okienka.
8.  Zobaczysz teraz najnowszą wersję w kolumnie dokument.

## <a name="view-previous-versions-of-a-terms-of-use"></a>Wyświetlanie poprzednich wersji warunków użytkowania

1.  Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie https://aka.ms/catou.
2.  Wybierz zasady użytkowania, dla których chcesz wyświetlić historię wersji.
3.  Kliknij **Języki i historię wersji**
4.  Kliknij pozycję **Zobacz poprzednie wersje.**

    ![szczegóły dokumentu, w tym wersje językowe](./media/terms-of-use/document-details.png)

5.  Możesz kliknąć nazwę dokumentu, aby pobrać tę wersję

## <a name="see-who-has-accepted-each-version"></a>Zobacz, kto zaakceptował każdą wersję

1.  Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie https://aka.ms/catou.
2.  Aby zobaczyć, kto aktualnie zaakceptował warunków użytkowania, kliknij liczbę w kolumnie **zaakceptowane** dla warunków użytkowania.
3.  Domyślnie na następnej stronie zostanie wyświetlony bieżący stan akceptowania przez każdego użytkownika do warunków użytkowania
4.  Jeśli chcesz zobaczyć poprzednie zdarzenia dotyczące zgody, możesz wybrać opcję **wszystkie** z listy rozwijanej **bieżący stan** . Teraz można zobaczyć zdarzenia poszczególnych użytkowników w szczegółowych informacjach o każdej wersji i o tym, co się stało.
5.  Alternatywnie możesz wybrać określoną wersję z listy rozwijanej **wersja**  , aby zobaczyć, kto zaakceptował tę określoną wersję.


## <a name="add-a-terms-of-use-language"></a>Dodawanie języka warunków użytkowania

Poniższa procedura opisuje sposób dodawania języka warunków użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz zasady dotyczące warunków użytkowania, które chcesz edytować.
1. Kliknij pozycję **Edytuj warunki**
1. Kliknij pozycję **Dodaj język** w dolnej części strony.
1. W okienku Dodaj język warunków użytkowania Przekaż zlokalizowany plik PDF i wybierz język.

    ![Warunki użytkowania wybrane i pokazywanie karty Języki w okienku szczegółów](./media/terms-of-use/select-language.png)

1. Kliknij pozycję **Dodaj język**.
1. Kliknij pozycję **Zapisz**.

1. Kliknij przycisk **Dodaj** , aby dodać język.

## <a name="per-device-terms-of-use"></a>Warunki użytkowania poszczególnych urządzeń

Ustawienie **Wymagaj od użytkowników zgody na każde urządzenie** umożliwia użytkownikom końcowym zaakceptowanie zasad użytkowania na wszystkich urządzeniach, z których uzyskują dostęp. Użytkownik końcowy będzie musiał zarejestrować swoje urządzenie w usłudze Azure AD. Gdy urządzenie jest zarejestrowane, identyfikator urządzenia służy do wymuszania zasad użytkowania na poszczególnych urządzeniach.

Poniżej znajduje się lista obsługiwanych platform i oprogramowania.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Inne |
> | --- | --- | --- | --- | --- |
> | **Aplikacja natywna** | Tak | Tak | Tak |  |
> | **Microsoft Edge** | Tak | Tak | Tak |  |
> | **Internet Explorer** | Tak | Tak | Tak |  |
> | **Chrome (z rozszerzeniem)** | Tak | Tak | Tak |  |

Warunki użytkowania poszczególnych urządzeń mają następujące ograniczenia:

- Urządzenie może być przyłączone tylko do jednej dzierżawy.
- Użytkownik musi mieć uprawnienia do przyłączania urządzenia.
- Aplikacja do rejestracji w usłudze Intune nie jest obsługiwana. Upewnij się, że jest ona wykluczona z zasad dostępu warunkowego wymagających warunków użytkowania.
- Użytkownicy B2B usługi Azure AD nie są obsługiwani.

Jeśli urządzenie użytkownika nie jest przyłączone, otrzyma komunikat informujący o konieczności przyłączenia urządzenia. Ich środowisko jest zależne od platformy i oprogramowania.

### <a name="join-a-windows-10-device"></a>Dołączanie urządzenia z systemem Windows 10

Jeśli użytkownik korzysta z systemu Windows 10 i Microsoft Edge, otrzyma komunikat podobny do poniższego, aby [dołączyć urządzenie](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![System Windows 10 i Microsoft Edge — komunikat wskazujący, że urządzenie musi być zarejestrowane](./media/terms-of-use/per-device-win10-edge.png)

Jeśli używają przeglądarki Chrome, zostanie wyświetlony monit o zainstalowanie [rozszerzenia konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Rejestrowanie urządzenia z systemem iOS

Jeśli użytkownik korzysta z urządzenia z systemem iOS, zostanie wyświetlony monit o zainstalowanie [aplikacji Microsoft Authenticator](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Rejestrowanie urządzenia z systemem Android

Jeśli użytkownik korzysta z urządzenia z systemem Android, zostanie wyświetlony monit o zainstalowanie [aplikacji Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Przeglądarki

Jeśli użytkownik korzysta z nieobsługiwanej przeglądarki, zostanie poproszony o użycie innej przeglądarki.

![Komunikat wskazujący, że urządzenie musi być zarejestrowane, ale przeglądarka nie jest obsługiwana](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Usuń warunki użytkowania

Stare warunki użytkowania zasad można usunąć, korzystając z następującej procedury.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz zasady dotyczące warunków użytkowania, które chcesz usunąć.
1. Kliknij pozycję **Usuń**.
1. W wyświetlonym komunikacie z pytaniem o kontynuowanie kliknij pozycję **Tak**.

    ![Komunikat z prośbą o potwierdzenie usunięcia warunków użytkowania](./media/terms-of-use/delete-tou.png)

   Zasady użytkowania nie powinny już być widoczne.

## <a name="deleted-users-and-active-terms-of-use"></a>Usunięci Użytkownicy i aktywne warunki użytkowania

Domyślnie usunięty użytkownik jest w stanie usunięcia w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby. Po 30 dniach użytkownik jest trwale usuwany. Ponadto przy użyciu portalu Azure Active Directory Administrator globalny może jawnie [usunąć niedawno usuniętego użytkownika](../fundamentals/active-directory-users-restore.md) przed osiągnięciem tego okresu. Jeden użytkownik został trwale usunięty. kolejne dane dotyczące tego użytkownika zostaną usunięte z zasad aktywnych warunków użytkowania. Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.

## <a name="policy-changes"></a>Zmiany zasad

Zasady dostępu warunkowego zaczynają obowiązywać natychmiast. W takim przypadku administrator rozpocznie wyświetlanie "chmur sad" lub "problemy z tokenem usługi Azure AD". Administrator musi się wylogować i zalogować się ponownie w celu spełnienia nowych zasad.

> [!IMPORTANT]
> W następujących przypadkach uprawnieni użytkownicy muszą się wylogować i zalogować ponownie, aby spełnić wymagania nowych zasad:
>
> - zasady dostępu warunkowego są włączone w zasadach użytkowania
> - lub jest tworzona druga zasada warunków użytkowania

## <a name="b2b-guests"></a>Goście B2B

W większości organizacji istnieje proces, w którym pracownicy mogą wyrazić zgodę na warunki użytkowania zasad i zasady zachowania poufności informacji w organizacji. Ale jak można wymusić te same działania dotyczące Gości usługi Azure AD Business-to-Business (B2B), gdy są dodawane za pośrednictwem programu SharePoint lub zespołów? Korzystając z zasad dostępu warunkowego i warunków użytkowania, można wymusić zasady bezpośrednio do użytkowników Gości B2B. W trakcie przepływu wykupu zaproszenia użytkownik jest prezentowany zgodnie z zasadami użytkowania. Ta pomoc techniczna jest obecnie dostępna w wersji zapoznawczej.

Zasady Warunki użytkowania będą wyświetlane tylko wtedy, gdy użytkownik ma konto gościa w usłudze Azure AD. Usługa SharePoint Online aktualnie ma [środowisko odbiorcy udostępniania zewnętrznego ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) umożliwiające udostępnianie dokumentu lub folderu, który nie wymaga, aby użytkownik miał konto gościa. W takim przypadku zasady użytkowania nie są wyświetlane.

![Okienko Użytkownicy i grupy — karta Dołącz z opcją wszyscy użytkownicy-Goście zaznaczona](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Obsługa aplikacji w chmurze

Zasady Warunki użytkowania mogą być używane dla różnych aplikacji w chmurze, takich jak Azure Information Protection i Microsoft Intune. Ta pomoc techniczna jest obecnie dostępna w wersji zapoznawczej.

### <a name="azure-information-protection"></a>Azure Information Protection

Zasady dostępu warunkowego można skonfigurować dla aplikacji Azure Information Protection i wymagać zasady użytkowania, gdy użytkownik uzyskuje dostęp do chronionego dokumentu. Spowoduje to wyzwolenie zasad użytkowania przed uzyskaniem dostępu do chronionego dokumentu po raz pierwszy.

![Okienko aplikacje w chmurze z wybraną aplikacją Microsoft Azure Information Protection](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Rejestracja Microsoft Intune

Zasady dostępu warunkowego można skonfigurować dla aplikacji do rejestracji Microsoft Intune i wymagać zasad użytkowania przed rejestracją urządzenia w usłudze Intune. Aby uzyskać więcej informacji, zapoznaj się z tematem Przeczytaj, jak [rozwiązać odpowiednie warunki w Twoim wpisie w blogu organizacji](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Okienko aplikacje w chmurze z wybraną aplikacją Microsoft Intune](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Aplikacja do rejestracji w usłudze Intune nie jest obsługiwana w przypadku [warunków użytkowania poszczególnych urządzeń](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: nie mogę zalogować się przy użyciu programu PowerShell, gdy warunki użytkowania są włączone.**<br />
Odp.: Warunki użytkowania może zostać zaakceptowany tylko przy uwierzytelnianiu interaktywnie.

**Pyt. Jak sprawdzić, czy i kiedy użytkownik zaakceptował warunki użytkowania?**<br />
Odp.: w bloku Warunki użytkowania kliknij liczbę w obszarze **zaakceptowane**. Możesz również wyświetlić lub przeszukać działanie Zaakceptuj w dziennikach inspekcji usługi Azure AD. Aby uzyskać więcej informacji, zobacz Wyświetlanie raportu dla użytkowników, którzy zaakceptowali i odrzucili i [wyświetlania dzienników inspekcji usługi Azure AD](#view-azure-ad-audit-logs).

**Pyt. Jak długo są przechowywane informacje?**<br />
Odp.: użytkownik liczy w raporcie warunki użytkowania i kto zaakceptował/odrzucony przez okres użytkowania warunków użytkowania. Dzienniki inspekcji usługi Azure AD są przechowywane przez 30 dni.

**P: Dlaczego widzę inną liczbę przesłanych elementów w raporcie warunki użytkowania w porównaniu z dziennikami inspekcji usługi Azure AD?**<br />
Odp.: Raport warunki użytkowania jest przechowywany przez okres istnienia zasad użytkowania, podczas gdy dzienniki inspekcji usługi Azure AD są przechowywane przez 30 dni. Ponadto w raporcie warunki użytkowania są wyświetlane tylko bieżące Stany zgody użytkownika. Na przykład jeśli użytkownik odrzuci, a następnie zaakceptuje, raport warunki użytkowania będzie zawierać tylko ten użytkownik. Jeśli musisz wyświetlić historię, możesz użyć dzienników inspekcji usługi Azure AD.

**P: Jeśli edytuję Szczegóły zasad dotyczących warunków użytkowania, czy użytkownicy wymagają ponownego zaakceptowania?**<br />
Odp.: nie, jeśli administrator edytuje szczegóły dotyczące warunków użytkowania (Name, Display Name, wymagaj od użytkowników powiększania lub dodania języka), nie wymaga od użytkowników ponownej akceptacji nowych warunków.

**P: Czy mogę zaktualizować istniejące warunki użytkowania dokumentu zasad?**<br />
Odp.: obecnie nie można zaktualizować istniejących warunków użytkowania dokumentu zasad. Aby zmienić dokument zasad warunków użytkowania, należy utworzyć nowe warunki użytkowania wystąpienia zasad.

**P: Jeśli hiperłącza znajdują się w dokumencie warunki użytkowania dokumentu PDF, użytkownicy końcowi będą mogli je klikać?**<br />
Odp.: tak, użytkownicy końcowi mogą wybrać hiperłącza do dodatkowych stron, ale linki do sekcji w dokumencie nie są obsługiwane. Ponadto hiperłącza w warunkach użytkowania pliki PDF zasad nie działają w przypadku uzyskiwania dostępu do usługi Azure AD webapps/webaccount.

**P: czy zasady dotyczące warunków użytkowania obsługują wiele języków?**<br />
Odp. Tak. Obecnie istnieją 108 różne języki, które administrator może skonfigurować dla jednej zasady użytkowania. Administrator może przekazać wiele dokumentów PDF i oznaczyć je za pomocą odpowiedniego języka (do 108). Gdy użytkownicy końcowi zalogują się, zobaczą preferencje językowe przeglądarki i wyświetlają pasujący dokument. Jeśli nie ma dopasowania, zostanie wyświetlony dokument domyślny, który jest pierwszym przekazaniem dokumentu.

**P: Kiedy zasady użytkowania są wyzwalane?**<br />
Odp.: zasady użytkowania są wyzwalane podczas logowania.

**P: Jakie aplikacje mogą być ukierunkowane na warunki użytkowania?**<br />
Odp.: można utworzyć zasady dostępu warunkowego w aplikacjach dla przedsiębiorstw korzystających z nowoczesnego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [aplikacje przedsiębiorstwa](./../manage-apps/view-applications-portal.md).

**P: Czy można dodać wiele zasad użytkowania do danego użytkownika lub aplikacji?**<br />
Odp.: tak, przez utworzenie wielu zasad dostępu warunkowego przeznaczonych dla tych grup lub aplikacji. Jeśli użytkownik znajduje się w zakresie wielu zasad użytkowania, zaakceptuje jedną warunki użytkowania w danym momencie.

**P: co się stanie, jeśli użytkownik odrzuci zasady użytkowania?**<br />
Odp. Dostęp do aplikacji zostanie zablokowany dla tego użytkownika. Użytkownik będzie musiał ponownie zalogować się i zaakceptować warunki w celu uzyskania dostępu.

**P: Czy możliwe jest nieakceptowanie zasad użytkowania, które zostały wcześniej zaakceptowane?**<br />
Odp.: można [przejrzeć wcześniej zaakceptowane zasady użytkowania](#how-users-can-review-their-terms-of-use), ale obecnie nie można jej zaakceptować.

**P: co się stanie, jeśli będę również korzystać z warunków i postanowień usługi Intune?**<br />
Odp.: w przypadku skonfigurowania warunków użytkowania usługi Azure AD i warunków [i postanowień usługi Intune](/intune/terms-and-conditions-create)użytkownik będzie musiał zaakceptować oba te czynności. Aby uzyskać więcej informacji, zapoznaj się z tematem [Wybieranie odpowiedniego rozwiązania dla wpisu w blogu organizacji](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**P: jakie punkty końcowe są używane przez usługę do uwierzytelniania?**<br />
Odp.: Warunki użytkowania wykorzystuje następujące punkty końcowe do uwierzytelniania: https://tokenprovider.termsofuse.identitygovernance.azure.com i https://account.activedirectory.windowsazure.com . Jeśli Twoja organizacja ma listę adresów URL do rejestracji, konieczne będzie dodanie tych punktów końcowych do listy dozwolonych oraz punktów końcowych usługi Azure AD w celu zalogowania.

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Wymagaj akceptacji warunków użytkowania przed uzyskaniem dostępu do aplikacji w chmurze](require-tou.md)
