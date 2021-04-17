---
title: Warunki użytkowania — Azure Active Directory | Microsoft Docs
description: Przed uzyskaniem dostępu Azure Active Directory zacząć korzystać z warunków użytkowania, aby przedstawić informacje pracownikom lub gościom.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 01/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c8e18979ff1575e1a050244a96e7858cdce46b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530242"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory warunki użytkowania

Zasady użytkowania usługi Azure AD zapewniają prostą metodę, za pomocą których organizacje mogą prezentować informacje użytkownikom. Dzięki tej prezentacji użytkownicy mogą zapoznać się z istotnymi zastrzeżeniami do wymagań prawnych lub wymagań dotyczących zgodności. W tym artykule opisano, jak rozpocząć pracę z zasadami warunków użytkowania.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Omówienie filmów wideo

Poniższy klip wideo zawiera krótkie omówienie zasad tou.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Aby uzyskać więcej filmów wideo, zobacz:
- [Jak wdrożyć zasady użytkowania w programie Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Jak stosować zasady użytkowania w programie Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Co mogę zrobić z warunkami użytkowania?

Zasady użytkowania usługi Azure AD mają następujące możliwości:

- Wymagaj od pracowników lub gości zaakceptowania zasad użytkowania przed uzyskaniem dostępu.
- Wymagaj od pracowników lub gości zaakceptowania zasad użytkowania na każdym urządzeniu przed uzyskaniem dostępu.
- Wymagaj, aby pracownicy lub goście akceptowali warunki użytkowania zgodnie z harmonogramem cyklicznym.
- Wymagaj od pracowników lub gości zaakceptowania zasad użytkowania przed zarejestrowaniem informacji zabezpieczających w usłudze Azure AD Multi-Factor Authentication (MFA).
- Wymagaj, aby pracownicy zaakceptowali zasady użytkowania przed zarejestrowaniem informacji zabezpieczających w funkcji samoobsługowego resetowania hasła (SSPR) usługi Azure AD.
- Przedstawianie ogólnych zasad użytkowania dla wszystkich użytkowników w organizacji.
- Prezentuj określone zasady użytkowania na podstawie atrybutów użytkownika (np. warunki dla lekarzy różnią się od warunków dla pielęgniarek, a pracownicy krajowi mają inne warunki niż pracownicy międzynarodowi) za pomocą [grup dynamicznych](../enterprise-users/groups-dynamic-membership.md).
- Prezentowanie określonych zasad użytkowania podczas uzyskiwania dostępu do aplikacji o dużym wpływie na działalność biznesową, takich jak Salesforce.
- Prezentuj zasady użytkowania w różnych językach.
- Lista osób, które zaakceptowały lub nie zaakceptowały Twoich zasad użytkowania.
- Pomoc w spełnianiu przepisów dotyczących prywatności.
- Wyświetlanie dziennika aktywności zasad dotyczących warunków użytkowania dotyczących zgodności i inspekcji.
- Tworzenie zasad warunków użytkowania i zarządzanie nimi przy użyciu [Microsoft Graph API](/graph/api/resources/agreement) (obecnie w wersji zapoznawczej).

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać i konfigurować zasady użytkowania usługi Azure AD, musisz mieć:

- Subskrypcja usługi Azure AD w wersji Premium P1, Premium P2, EMS E3 lub EMS E5.
   - Jeśli nie masz żadnej z tych subskrypcji, możesz [uzyskać dostęp do usługi Azure AD w wersji Premium](../fundamentals/active-directory-get-started-premium.md) lub [włączyć wersję próbnej usługi Azure AD w wersji Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jedno z następujących kont administratora katalogu, który chcesz skonfigurować:
   - Administrator globalny
   - Administrator zabezpieczeń
   - Administrator dostępu warunkowego

## <a name="terms-of-use-document"></a>Dokument z warunkami użytkowania

Zasady użytkowania usługi Azure AD prezentują zawartość w formacie PDF. Zawartość ta może być dowolna i obejmować na przykład istniejące dokumenty kontraktowe, umożliwiając gromadzenie umów użytkowników końcowych podczas logowania. Aby obsługiwać użytkowników na urządzeniach przenośnych, zalecany rozmiar czcionki w pliku PDF wynosi 24 punkty.

## <a name="add-terms-of-use"></a>Dodawanie warunków użytkowania

Po sfinalizowaniu dokumentu zasad użytkowania użyj poniższej procedury, aby go dodać.

1. Zaloguj się do platformy Azure jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

    ![Dostęp warunkowy — Warunki użytkowania blok](./media/terms-of-use/tou-blade.png)

1. Kliknij pozycję **Nowe warunki**.

    ![Okienko Nowy okres użytkowania w celu określenia ustawień warunków użytkowania](./media/terms-of-use/new-tou.png)

1. W polu **Nazwa** wprowadź nazwę zasad użytkowania, które będą używane w Azure Portal.
1. W polu **Nazwa wyświetlana** wprowadź tytuł, który użytkownicy zobaczą po zalogowaniu.
1. Aby **Warunki użytkowania dokument**, przejdź do pliku PDF z warunkami użytkowania i wybierz go.
1. Wybierz język dla dokumentu zasad użytkowania. Opcja języka umożliwia przekazanie wielu zasad użytkowania, z których każda ma inny język. Wersja zasad użytkowania, które użytkownik końcowy zobaczy, będzie oparta na preferencjach przeglądarki.
1. Aby wymagać od użytkowników końcowych wyświetlania zasad użytkowania przed ich zaakceptowaniem, ustaw ustawienie Wymagaj od użytkowników rozwinięcia warunków użytkowania **na** **wartość Wł.**
1. Aby wymagać od użytkowników końcowych zaakceptowania zasad użytkowania na każdym  urządzeniu, z których korzystają, ustaw ustawienie Wymagaj od użytkowników wyrażania zgody na każdym urządzeniu **na wartość Wł.** Użytkownicy mogą wymagać zainstalowania dodatkowych aplikacji, jeśli ta opcja jest włączona. Aby uzyskać więcej informacji, zobacz [Warunki użytkowania 1 urządzenia.](#per-device-terms-of-use)
1. Jeśli chcesz wygasać zgody zasad użytkowania zgodnie z harmonogramem, ustaw ustawienie Wygasanie **zgody** na **wartość Wł.**. W przypadku ustawienia na wartość Wł. zostaną wyświetlone dwa dodatkowe ustawienia harmonogramu.

    ![Ustawienia wygasania zgody na ustawianie daty rozpoczęcia, częstotliwości i czasu trwania](./media/terms-of-use/expire-consents.png)

1. Użyj ustawień **Wygasanie od początku** **i Częstotliwość,** aby określić harmonogram wygasania zasad dotyczących warunków użytkowania. W poniższej tabeli przedstawiono wynik dla kilku przykładowych ustawień:

   | Wygasają, począwszy od | Częstotliwość | Wynik |
   | --- | --- | --- |
   | Today's date (Dzisiejsza data)  | Co miesiąc | Od dzisiaj użytkownicy muszą zaakceptować zasady użytkowania, a następnie ponownie zaakceptować je co miesiąc. |
   | Data w przyszłości  | Co miesiąc | Od dzisiaj użytkownicy muszą zaakceptować zasady użytkowania. Gdy przyszła data wygaśnie, zgody wygasają, a następnie użytkownicy muszą ponownie wyrazić zgodę co miesiąc.  |

   Jeśli na przykład ustawisz wygasanie począwszy od daty na **1** stycznia i częstotliwość na Wartość miesięczna **,** oto jak mogą wystąpić wygaśnięcia dla dwóch użytkowników:

   | Użytkownik | Data pierwszego zaakceptowania | Data pierwszego wygaśnięcia | Druga data wygaśnięcia | Trzecia data wygaśnięcia |
   | --- | --- | --- | --- | --- |
   | Alice | 1 sty | 1 lutego | 1 marca | 1 kw. |
   | Bob | 15 sty | 1 lutego | 1 marca | 1 kw. |

1. Użyj ustawienia Czas trwania przed ponowną opinią wymaga **(dni),** aby określić liczbę dni, po których użytkownik musi ponownie określić warunki użytkowania. Dzięki temu użytkownicy mogą postępować zgodnie z własnym harmonogramem. Jeśli na przykład ustawisz czas trwania **na 30** dni, oto jak mogą wystąpić wygaśnięcia dla dwóch użytkowników:

   | Użytkownik | Data pierwszego zaakceptowania | Data pierwszego wygaśnięcia | Druga data wygaśnięcia | Trzecia data wygaśnięcia |
   | --- | --- | --- | --- | --- |
   | Alice | 1 sty | 31 stycznia | 2 marca | 1 kw. |
   | Bob | 15 sty | 14 lutego | 16 marca | 15 kw. |

   Istnieje możliwość użycia  ustawień Wygasanie zgody i Czas trwania przed ponowną zgodą **wymaga (dni),** ale zazwyczaj używa się jednego lub drugiego.

1. W **obszarze Dostęp warunkowy** użyj listy **szablonów Wymuszaj** przy użyciu zasad dostępu warunkowego, aby wybrać szablon w celu wymuszenia zasad użytkowania.

    ![Lista rozwijana Dostęp warunkowy do wybierania szablonu zasad](./media/terms-of-use/conditional-access-templates.png)

   | Template | Opis |
   | --- | --- |
   | **Dostęp do aplikacji w chmurze dla wszystkich gości** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich gości i wszystkich aplikacji w chmurze. Te zasady mają wpływ na Azure Portal. Po utworzeniu może być konieczne wylogowanie się i zalogowanie. |
   | **Dostęp do aplikacji w chmurze dla wszystkich użytkowników** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich użytkowników i wszystkich aplikacji w chmurze. Te zasady mają wpływ na Azure Portal. Po utworzeniu tego konta konieczne będzie wylogowanie się i zalogowanie. |
   | **Zasady niestandardowe** | Wybierz użytkowników, grupy i aplikacje, do których będą stosowane te zasady użytkowania. |
   | **Tworzenie zasad dostępu warunkowego później** | Te zasady użytkowania będą wyświetlane na liście kontroli udzielania podczas tworzenia zasad dostępu warunkowego. |

   >[!IMPORTANT]
   >Mechanizmy kontroli zasad dostępu warunkowego (w tym zasady użytkowania) nie obsługują wymuszania na kontach usług. Zalecamy wykluczenie wszystkich kont usług z zasad dostępu warunkowego.

    Niestandardowe zasady dostępu warunkowego umożliwiają stosowanie szczegółowych zasad użytkowania, aż do określonej aplikacji w chmurze lub grupy użytkowników. Aby uzyskać więcej informacji, zobacz Szybki start: wymaganie zaakceptowania warunków użytkowania przed [uzyskaniem dostępu do aplikacji w chmurze.](require-tou.md)

1. Kliknij pozycję **Utwórz**.

    Jeśli wybrano niestandardowy szablon dostępu warunkowego, zostanie wyświetlony nowy ekran, który umożliwia utworzenie niestandardowych zasad dostępu warunkowego.

   ![Nowe okienko Dostęp warunkowy w przypadku wybrania niestandardowego szablonu zasad dostępu warunkowego](./media/terms-of-use/custom-policy.png)

   Powinny zostać teraz wyświetlony nowe zasady użytkowania.

   ![Nowe warunki użytkowania wymienione w bloku warunków użytkowania](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Wyświetlanie raportu o tym, kto zaakceptował i odrzucił

W bloku Warunki użytkowania znajduje się liczba użytkowników, którzy je zaakceptowali i odrzucili. Te liczby i osoby, które zaakceptowały/odrzucone, są przechowywane przez okres użytkowania zasad użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

    ![Warunki użytkowania bloku z listą liczby użytkowników, którzy są akceptowani i odrzucani](./media/terms-of-use/view-tou.png)

1. W przypadku zasad warunków użytkowania kliknij liczby w obszarze **Zaakceptowane** lub **Odrzucone,** aby wyświetlić bieżący stan dla użytkowników.

    ![Warunki użytkowania wyrażania zgody z listą użytkowników, którzy zaakceptowali](./media/terms-of-use/accepted-tou.png)

1. Aby wyświetlić historię poszczególnych użytkowników, kliknij wielokropek (**...**), a następnie **pozycję Wyświetl historię.**

    ![Menu kontekstowe Wyświetl historię dla użytkownika](./media/terms-of-use/view-history-menu.png)

   W okienku historii wyświetlania zostanie wyświetlony historię wszystkich akceptowanych, odmówień i wygaśnięcia.

   ![Okienko Wyświetl historię zawiera listę historii akceptowanych, odmówień i wygaśnięcia dla użytkownika](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Wyświetlanie dzienników inspekcji usługi Azure AD

Jeśli chcesz wyświetlić dodatkowe działania, zasady użytkowania usługi Azure AD zawierają dzienniki inspekcji. Każda zgoda użytkownika wyzwala zdarzenie w dziennikach inspekcji przechowywanych przez **30 dni.** Te dzienniki możesz wyświetlić w portalu lub pobrać jako plik CSV.

Aby rozpocząć pracę z dziennikami inspekcji usługi Azure AD, użyj następującej procedury:

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz zasady użytkowania.
1. Kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Warunki użytkowania z wyróżniona opcją Wyświetl dzienniki inspekcji](./media/terms-of-use/audit-tou.png)

1. Na ekranie dzienników inspekcji usługi Azure AD można filtrować informacje przy użyciu podanych list, aby określić konkretne informacje dziennika inspekcji.

    Można również kliknąć pozycję **Pobierz**, aby pobrać informacje w pliku CSV do użytku lokalnego.

   ![Wyświetlanie listy dat, zasad docelowych, zainicjowanych przez i działań na ekranie dzienników inspekcji usługi Azure AD](./media/terms-of-use/audit-logs-tou.png)

   Po kliknięciu dziennika zostanie wyświetlone okienko z dodatkowymi szczegółami aktywności.

   ![Szczegóły aktywności dziennika przedstawiające działanie, stan działania, zainicjowane przez, zasady docelowe](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Jak wyglądają warunki użytkowania dla użytkowników

Po utworzeniu i wymuszenia zasad dotyczącej treści podczas logowania użytkownicy, którzy znajdują się w zakresie, zobaczą następujący ekran.

![Przykładowe warunki użytkowania, które są wyświetlane, gdy użytkownik się](./media/terms-of-use/user-tou.png)

Użytkownicy mogą wyświetlać warunki użytkowania i w razie potrzeby powiększać i pomniejszać za pomocą przycisków.

![Widok warunków użytkowania z przyciskami powiększenia](./media/terms-of-use/zoom-buttons.png)

Na poniższym ekranie pokazano, jak wyglądają zasady dotyczące treści na urządzeniach przenośnych.

![Przykładowe warunki użytkowania, które są wyświetlane, gdy użytkownik korzysta z urządzenia przenośnego](./media/terms-of-use/mobile-tou.png)

Użytkownicy muszą zaakceptować zasady użytkowania tylko raz i nie zobaczą ponownie warunków użytkowania przy kolejnych logowaniach.

### <a name="how-users-can-review-their-terms-of-use"></a>Jak użytkownicy mogą przeglądać warunki użytkowania

Użytkownicy mogą przeglądać i przeglądać zaakceptowane przez nich warunki użytkowania, korzystając z poniższej procedury.

1. Zaloguj się do witryny [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. W prawym górnym rogu kliknij swoją nazwę i wybierz pozycję **Profil.**

    ![Witryna MyApps z otwartym okienkiem użytkownika](./media/terms-of-use/tou14.png)

1. Na stronie Profil kliknij pozycję **Przejrzyj warunki użytkowania**.

    ![Strona profilu użytkownika z linkiem Przejrzyj warunki użytkowania](./media/terms-of-use/tou13a.png)

1. W tym miejscu możesz przejrzeć zaakceptowane zasady użytkowania.

## <a name="edit-terms-of-use-details"></a>Edytowanie szczegółów warunków użytkowania

Można edytować niektóre szczegóły zasad użytkowania, ale nie można modyfikować istniejącego dokumentu. W poniższej procedurze opisano sposób edytowania szczegółów.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz zasady użytkowania, które chcesz edytować.
1. Kliknij **pozycję Edytuj terminy.**
1. W okienku Edytowanie warunków użytkowania można zmienić następujące ustawienia:
    - **Nazwa** — jest to wewnętrzna nazwa tej, która nie jest udostępniana użytkownikom
    - **Nazwa wyświetlana** — jest to nazwa, która jest wyświetlana użytkownikom podczas wyświetlania treści
    - **Wymagaj od użytkowników** rozwinięcia warunków  użytkowania — ustawienie tej opcji na Wł. wymusi rozszerzenie dokumentu zasad użytkowania przed jego zaakceptowaniem.
    - (Wersja zapoznawcza) Możesz **zaktualizować istniejący dokument warunków** użytkowania
    - Język można dodać do istniejącej wersji

   Jeśli istnieją inne ustawienia, które chcesz zmienić, takie jak dokument PDF, wymagają od użytkowników wyrażania zgody na każdym urządzeniu, wygasania zgody, czasu trwania przed ponowną zgodą lub zasad dostępu warunkowego, należy utworzyć nowe zasady warunków świadczenia usług.

    ![Edycja przedstawiająca różne opcje języka ](./media/terms-of-use/edit-terms-use.png)

1. Gdy wszystko będzie gotowe, kliknij przycisk **Zapisz,** aby zapisać zmiany.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Aktualizowanie wersji lub pliku PDF istniejących warunków użytkowania

1.  Zaloguj się do platformy Azure i przejdź do [Warunki użytkowania](https://aka.ms/catou)
2.  Wybierz zasady użytkowania, które chcesz edytować.
3.  Kliknij **pozycję Edytuj terminy.**
4.  W przypadku języka, w jakim chcesz zaktualizować nową wersję, kliknij pozycję **Aktualizuj** w kolumnie akcji

    ![Okienko Edytowania warunków użytkowania z wyświetloną nazwą i opcjami rozwijania](./media/terms-of-use/edit-terms-use.png)

5.  W okienku po prawej stronie przekaż plik PDF dla nowej wersji
6.  W tym miejscu dostępna  jest również opcja przełączania Wymagaj ponownego zaakceptowania, jeśli chcesz, aby użytkownicy akceptowali tę nową wersję przy następnym logować się. Jeśli wymagasz ponownej akceptacji przez użytkowników, przy następnej próbie uzyskania dostępu do zasobu zdefiniowanego w zasadach dostępu warunkowego zostanie wyświetlony monit o zaakceptowanie tej nowej wersji. Jeśli nie wymagasz ponownej zgody użytkowników, ich poprzednia zgoda pozostanie aktualna i tylko nowi użytkownicy, którzy nie wyraziły zgody przed lub których zgoda wygaśnie, zobaczą nową wersję.

    ![Wyróżniona opcja Edytuj ponownie warunki użytkowania](./media/terms-of-use/re-accept.png)

7.  Po przesłaniu nowego pliku PDF i zdecydowaniu o jego ponownejceptu kliknij pozycję Dodaj w dolnej części okienka.
8.  Najnowsza wersja będzie teraz dostępna w kolumnie Dokument.

## <a name="view-previous-versions-of-a-tou"></a>Wyświetlanie poprzednich wersji tou

1.  Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie https://aka.ms/catou.
2.  Wybierz zasady użytkowania, dla których chcesz wyświetlić historię wersji.
3.  Kliknij pozycję **Języki i historia wersji**
4.  Kliknij pozycję **Zobacz poprzednie wersje.**

    ![szczegóły dokumentu, w tym wersje językowe](./media/terms-of-use/document-details.png)

5.  Możesz kliknąć nazwę dokumentu, aby pobrać wersję

## <a name="see-who-has-accepted-each-version"></a>Zobacz, kto zaakceptował każdą wersję

1.  Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie https://aka.ms/catou.
2.  Aby sprawdzić, kto aktualnie zaakceptował tę liczbę, kliknij liczbę w kolumnie **Zaakceptowane** dla chcieć.
3.  Domyślnie na następnej stronie będzie pokazywany bieżący stan akceptacji warunków dla poszczególnych użytkowników
4.  Jeśli chcesz zobaczyć poprzednie zdarzenia wyrażania zgody, możesz wybrać pozycję **Wszystkie** z **listy** rozwijanej Bieżący stan. Teraz możesz zobaczyć zdarzenia poszczególnych użytkowników ze szczegółami każdej wersji i tego, co się stało.
5.  Alternatywnie możesz wybrać określoną wersję  z listy rozwijanej Wersja, aby zobaczyć, kto zaakceptował tę konkretną wersję.


## <a name="add-a-tou-language"></a>Dodawanie języka ToU

W poniższej procedurze opisano sposób dodawania języka tou.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz zasady użytkowania, które chcesz edytować.
1. Kliknij **pozycję Edytuj terminy**
1. Kliknij **pozycję Dodaj** język w dolnej części strony.
1. W okienku Dodawanie warunków użytkowania języka przekaż zlokalizowany plik PDF i wybierz język.

    ![Warunki użytkowania i wyświetlanie karty Języki w okienku szczegółów](./media/terms-of-use/select-language.png)

1. Kliknij **pozycję Dodaj język.**
1. Kliknij pozycję **Zapisz**

1. Kliknij **przycisk Dodaj,** aby dodać język.

## <a name="per-device-terms-of-use"></a>Warunki użytkowania dla 1 urządzenia

Ustawienie **Wymagaj zgody użytkowników** na każde urządzenie umożliwia wymaganie od użytkowników końcowych zaakceptowania warunków użytkowania na każdym urządzeniu, z których korzystają. Użytkownik końcowy będzie musi zarejestrować swoje urządzenie w usłudze Azure AD. Po zarejestrowaniu urządzenia identyfikator urządzenia jest używany do wymuszania zasad użytkowania na każdym urządzeniu.

Oto lista obsługiwanych platform i oprogramowania.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Inne |
> | --- | --- | --- | --- | --- |
> | **Aplikacja natywna** | Yes | Yes | Yes |  |
> | **Microsoft Edge** | Yes | Yes | Yes |  |
> | **Internet Explorer** | Yes | Yes | Yes |  |
> | **Chrome (z rozszerzeniem)** | Yes | Yes | Yes |  |

Warunki użytkowania 1 urządzenia mają następujące ograniczenia:

- Urządzenie może zostać przyłączone tylko do jednej dzierżawy.
- Użytkownik musi mieć uprawnienia do dołączania urządzenia.
- Aplikacja rejestracji w usłudze Intune nie jest obsługiwana. Upewnij się, że jest ona wykluczona z wszelkich zasad dostępu warunkowego wymagających zasad warunków użytkowania.
- Użytkownicy usługi Azure AD B2B nie są obsługiwani.

Jeśli urządzenie użytkownika nie jest przyłączone, otrzyma komunikat o konieczności dołączenia urządzenia. Ich środowisko będzie zależne od platformy i oprogramowania.

### <a name="join-a-windows-10-device"></a>Dołączanie urządzenia z systemem Windows 10

Jeśli użytkownik korzysta z Windows 10 i Microsoft Edge, otrzyma komunikat podobny do poniższego, aby [dołączyć urządzenie.](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)

![Windows 10 i Microsoft Edge — komunikat informujący o tym, że urządzenie musi zostać zarejestrowane](./media/terms-of-use/per-device-win10-edge.png)

Jeśli użytkownik korzysta z przeglądarki Chrome, zostanie wyświetlony monit o zainstalowanie [rozszerzenia Windows 10 Accounts.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)

### <a name="register-an-ios-device"></a>Rejestrowanie urządzenia z systemem iOS

Jeśli użytkownik korzysta z urządzenia z systemem iOS, zostanie wyświetlony monit o zainstalowanie [Microsoft Authenticator aplikacji](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Rejestrowanie urządzenia z systemem Android

Jeśli użytkownik korzysta z urządzenia z systemem Android, zostanie wyświetlony monit o zainstalowanie [Microsoft Authenticator aplikacji](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Przeglądarki

Jeśli użytkownik korzysta z przeglądarki, która nie jest obsługiwana, zostanie poproszony o użycie innej przeglądarki.

![Komunikat informujący o tym, że urządzenie musi być zarejestrowane, ale przeglądarka nie jest obsługiwana](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Usuwanie warunków użytkowania

Stare zasady użytkowania można usunąć, korzystając z poniższej procedury.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz zasady użytkowania, które chcesz usunąć.
1. Kliknij pozycję **Usuń**.
1. W wyświetlonym komunikacie z pytaniem o kontynuowanie kliknij pozycję **Tak**.

    ![Komunikat z prośbą o potwierdzenie usunięcia warunków użytkowania](./media/terms-of-use/delete-tou.png)

   Zasady użytkowania nie powinny już być dla Ciebie widzieć.

## <a name="user-acceptance-record-deletion"></a>Usuwanie rekordów akceptacji użytkowników

Rekordy akceptacji użytkowników są usuwane:

- Gdy administrator jawnie usuwa tou. W takim przypadku usuwane są również wszystkie rekordy akceptacji skojarzone z tą konkretną treścią.
- Gdy dzierżawa utraci licencję Azure Active Directory — wersja Premium dzierżawy.
- Po usunięciu dzierżawy.

## <a name="policy-changes"></a>Zmiany zasad

Zasady dostępu warunkowego są natychmiast obowiązywać. W takim przypadku administrator zacznie widzieć "smutek chmury" lub "problemy z tokenami usługi Azure AD". Administrator musi się wylogować i zalogować ponownie, aby spełnić wymagania nowych zasad.

> [!IMPORTANT]
> W następujących przypadkach uprawnieni użytkownicy muszą się wylogować i zalogować ponownie, aby spełnić wymagania nowych zasad:
>
> - Zasady dostępu warunkowego są włączone dla zasad warunków użytkowania
> - lub utworzono drugie zasady użytkowania

## <a name="b2b-guests"></a>Goście B2B

Większość organizacji ma proces, w którym pracownicy muszą wyrazić zgodę na warunki użytkowania i zasady zachowania poufności informacji w organizacji. Ale jak można wymusić te same zgody dla gości B2B (business-to-business) usługi Azure AD, gdy są oni dodawana za pośrednictwem programu SharePoint lub aplikacji Teams? Za pomocą zasad dostępu warunkowego i warunków użytkowania można wymusić zasady bezpośrednio wobec użytkowników-gości B2B. Podczas przepływu realizacji zaproszenia użytkownikom są prezentowane warunki użytkowania. Ta obsługa jest obecnie dostępna w wersji zapoznawczej.

Warunki użytkowania będą wyświetlane tylko wtedy, gdy użytkownik ma konto gościa w usłudze Azure AD. W usłudze SharePoint Online jest obecnie dostępne środowisko adresata udostępniania zewnętrznego ad [hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) do udostępniania dokumentu lub folderu, które nie wymaga od użytkownika konta gościa. W takim przypadku zasady użytkowania nie są wyświetlane.

![Okienko Użytkownicy i grupy — karta Dołączanie z zaznaczoną opcją Wszyscy użytkownicy-goście](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Obsługa aplikacji w chmurze

Warunki użytkowania mogą być używane dla różnych aplikacji w chmurze, takich jak Azure Information Protection i Microsoft Intune. Ta obsługa jest obecnie dostępna w wersji zapoznawczej.

### <a name="azure-information-protection"></a>Azure Information Protection

Możesz skonfigurować zasady dostępu warunkowego dla aplikacji Azure Information Protection i wymagać zasad użytkowania, gdy użytkownik uzyskuje dostęp do chronionego dokumentu. Spowoduje to wyzwolenie zasad użytkowania przed pierwszym uzyskaniem przez użytkownika dostępu do chronionego dokumentu.

![Okienko Aplikacje w chmurze z Microsoft Azure Information Protection wybraną aplikacją](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune rejestracji

Możesz skonfigurować zasady dostępu warunkowego dla aplikacji rejestracji Microsoft Intune i wymagać zasad użytkowania przed zarejestrowaniem urządzenia w usłudze Intune. Aby uzyskać więcej informacji, zobacz wpis w blogu Read Choosing the right Terms solution for your organization (Przeczytaj wybieranie odpowiedniego rozwiązania [do warunków dla organizacji).](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

![Okienko Aplikacje w chmurze z Microsoft Intune wybraną aplikacją](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Aplikacja rejestracji w usłudze Intune nie jest obsługiwana [w przypadku warunków użytkowania 1 urządzenia.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie: Nie mogę zalogować się przy użyciu programu PowerShell, gdy warunki użytkowania są włączone.**<br />
A: Warunki użytkowania można zaakceptować tylko podczas uwierzytelniania interakcyjnego.

**Pyt. Jak sprawdzić, czy i kiedy użytkownik zaakceptował warunki użytkowania?**<br />
A: W bloku Warunki użytkowania kliknij liczbę w obszarze **Zaakceptowane .** Możesz również wyświetlić lub wyszukać działanie accept w dziennikach inspekcji usługi Azure AD. Aby uzyskać więcej informacji, zobacz View report of who has accepted and declined (Wyświetlanie raportu o tym, kto zaakceptował i odrzucił) oraz View Azure AD audit logs (Wyświetlanie [dzienników inspekcji usługi Azure AD).](#view-azure-ad-audit-logs)

**Pyt. Jak długo są przechowywane informacje?**<br />
O: Liczba użytkowników w raporcie o warunkach użytkowania oraz liczba zaakceptowanych/odrzuconych użytkowników jest przechowywana przez okres użytkowania. Dzienniki inspekcji usługi Azure AD są przechowywane przez 30 dni.

**Pytanie: dlaczego w raporcie o warunkach użytkowania widzę inną liczbę zgody niż w dziennikach inspekcji usługi Azure AD?**<br />
O: Raport warunków użytkowania jest przechowywany przez okres istnienia tych zasad użytkowania, a dzienniki inspekcji usługi Azure AD są przechowywane przez 30 dni. Ponadto raport warunków użytkowania zawiera tylko bieżący stan zgody użytkownika. Jeśli na przykład użytkownik odrzuci, a następnie zaakceptuje warunki użytkowania, raport będzie zawierał tylko zgodę tego użytkownika. Jeśli chcesz wyświetlić historię, możesz użyć dzienników inspekcji usługi Azure AD.

**Pytanie: jeśli edytuję szczegóły zasad dotyczących warunków użytkowania, czy wymaga to ponownej akceptacji przez użytkowników?**<br />
O: Nie. Jeśli administrator edytuje szczegóły zasad użytkowania (nazwa, nazwa wyświetlana, wymaganie rozszerzenia lub dodanie języka przez użytkowników), nie wymaga to ponownego ceptu nowych warunków przez użytkowników.

**Pytanie: Czy mogę zaktualizować istniejący dokument zasad użytkowania?**<br />
O: Obecnie nie można zaktualizować istniejącego dokumentu zasad użytkowania. Aby zmienić dokument zasad użytkowania, należy utworzyć nowe wystąpienie zasad warunków użytkowania.

**Pytanie: jeśli hiperlinki znajdują się w dokumencie PDF zasad użytkowania, czy użytkownicy końcowi będą mogli je kliknąć?**<br />
Odpowiedź: Tak, użytkownicy końcowi mogą wybierać hiperlinki do dodatkowych stron, ale linki do sekcji w dokumencie nie są obsługiwane. Ponadto hiperlinki dotyczące zasad użytkowania nie działają, gdy dostęp do nich jest uzyskiwany z portalu MyApps/MyAccount usługi Azure AD.

**Pytanie: Czy zasady użytkowania mogą obsługiwać wiele języków?**<br />
Odp. Tak. Obecnie istnieje 108 różnych języków, które administrator może skonfigurować dla pojedynczych warunków użytkowania. Administrator może przekazać wiele dokumentów PDF i oznaczyć te dokumenty za pomocą odpowiedniego języka (maksymalnie 108). Gdy użytkownicy końcowi się logują, przyjrzymy się preferencjom języka przeglądarki i wyświetlimy pasujący dokument. Jeśli nie ma dopasowania, zostanie wyświetlany dokument domyślny, który jest pierwszym przekazanym dokumentem.

**Pytanie: kiedy są wyzwalane zasady dotyczące warunków użytkowania?**<br />
A: Zasady dotyczące warunków użytkowania są wyzwalane podczas logowania.

**Pytanie: do jakich aplikacji można kierować zasady dotyczące warunków użytkowania?**<br />
O: Zasady dostępu warunkowego można utworzyć dla aplikacji przedsiębiorstwa przy użyciu nowoczesnego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [aplikacje przedsiębiorstwa](./../manage-apps/view-applications-portal.md).

**Pytanie: Czy mogę dodać wiele zasad użytkowania do danego użytkownika lub aplikacji?**<br />
Odpowiedź: Tak, tworząc wiele zasad dostępu warunkowego przeznaczonych dla tych grup lub aplikacji. Jeśli użytkownik jest w zakresie wielu zasad użytkowania, akceptuje jednocześnie jedno warunki użytkowania.

**Pytanie: Co się stanie, jeśli użytkownik odrzuci zasady użytkowania?**<br />
Odp. Dostęp do aplikacji zostanie zablokowany dla tego użytkownika. Użytkownik musiałby zalogować się ponownie i zaakceptować warunki, aby uzyskać dostęp.

**Pytanie: Czy można wyakceptować zasady użytkowania, które zostały wcześniej zaakceptowane?**<br />
O: Możesz [przejrzeć wcześniej zaakceptowane warunki użytkowania,](#how-users-can-review-their-terms-of-use)ale obecnie nie ma sposobu na cokceptuj.

**Pytanie: Co się stanie, jeśli korzystam również z warunków i postanowień usługi Intune?**<br />
O: Jeśli skonfigurowano zarówno warunki użytkowania usługi Azure AD, jak i warunki i postanowienia usługi [Intune,](/intune/terms-and-conditions-create)użytkownik będzie musiał zaakceptować oba te warunki. Aby uzyskać więcej informacji, zobacz wpis w blogu [Choosing the right Terms solution for your organization (Wybieranie](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)odpowiedniego rozwiązania do warunków dla organizacji).

**Pytanie: Jakie punkty końcowe są przez warunki użytkowania usługi na użytek uwierzytelniania?**<br />
A: Warunki użytkowania do uwierzytelniania są używane następujące punkty końcowe: https://tokenprovider.termsofuse.identitygovernance.azure.com i https://account.activedirectory.windowsazure.com . Jeśli Twoja organizacja ma listę adresów URL zezwalania na rejestrację, musisz dodać te punkty końcowe do listy zezwalania wraz z punktami końcowymi usługi Azure AD na potrzeby logowania.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: wymaganie zaakceptowania warunków użytkowania przed uzyskaniem dostępu do aplikacji w chmurze](require-tou.md)
