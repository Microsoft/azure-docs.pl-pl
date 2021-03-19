---
title: Omówienie funkcji Microsoft Threat Modeling Tool — Azure
description: Dowiedz się więcej o wszystkich funkcjach dostępnych w Threat Modeling Tool, takich jak widok analizy i raporty.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 65a88f309602462e77336181316c0d5bf19a8a1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90980675"
---
# <a name="threat-modeling-tool-feature-overview"></a>Omówienie funkcji Threat Modeling Tool

Threat Modeling Tool może pomóc w zaspokojeniu potrzeb modelowania zagrożeń. Aby uzyskać podstawowe wprowadzenie do narzędzia, zobacz Wprowadzenie do [Threat Modeling Tool](threat-modeling-tool-getting-started.md).

> [!NOTE]
>Threat Modeling Tool jest często aktualizowana, więc sprawdź, czy ten przewodnik często zawiera najnowsze funkcje i ulepszenia.

Aby otworzyć pustą stronę, wybierz pozycję **Utwórz model**.

![Pusta strona](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Aby wyświetlić funkcje aktualnie dostępne w narzędziu, należy użyć modelu zagrożeń utworzonego przez nasz zespół w przykładowym [wprowadzeniem](threat-modeling-tool-getting-started.md) .

![Podstawowy model zagrożeń](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Nawigacja

Przed przedyskutowaniem wbudowanych funkcji przejdźmy do najważniejszych składników znalezionych w narzędziu.

### <a name="menu-items"></a>Elementy menu

Środowisko jest podobne do innych produktów firmy Microsoft. Przejrzyjmy elementy menu najwyższego poziomu.

![Elementy menu](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Etykieta                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Plik** | <ul><li>Otwieranie, zapisywanie i zamykanie plików</li><li>Zaloguj się i wyloguj się z kont usługi OneDrive.</li><li>Udostępnij linki (Wyświetl i edytuj).</li><li>Wyświetl informacje o pliku.</li><li>Zastosuj nowy szablon do istniejących modeli.</li></ul> |
| **Edytuj** | Akcje cofania i ponawiania, a także kopiowanie, wklejanie i usuwanie. |
| **Wyświetlanie** | <ul><li>Przełączaj się między widokiem **Analizowanie** i **projektowanie** .</li><li>Otwórz zamknięte okna (na przykład wzorników, właściwości elementów i komunikatów).</li><li>Zresetuj układ do ustawień domyślnych.</li></ul> |
| **4b** | Dodawanie i usuwanie diagramów oraz poruszanie się po kartach diagramów. |
| **Raporty** | Twórz raporty HTML, aby udostępniać je innym osobom. |
| **Pomoc** | Znajdź przewodniki ułatwiające korzystanie z tego narzędzia. |

Symbole są skrótami dla menu najwyższego poziomu:

| Symbol                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Otwórz** | Otwiera nowy plik. |
| **Zapisz** | Zapisuje bieżący plik. |
| **Projekt** | Otwiera widok **projekt** , w którym można tworzyć modele. |
| **Analiza** | Pokazuje wygenerowane zagrożenia i ich właściwości. |
| **Dodaj diagram** | Dodaje nowy diagram (podobny do nowych kart w programie Excel). |
| **Usuń diagram** | Usuwa bieżący diagram. |
| **Kopiuj/Wytnij/Wklej** | Kopiuje, wycina i wkleja elementy. |
| **Cofnij/ponów** | Cofa i wykonuje akcje. |
| **Powiększ/Pomniejsz** | Powiększa i zmniejsza diagram, aby uzyskać lepszy widok. |
| **Opinia** | Otwiera forum MSDN. |

### <a name="canvas"></a>Kanwa

Kanwa to miejsce, w którym można przeciągać i upuszczać elementy. Przeciąganie i upuszczanie to najszybszy i najbardziej wydajny sposób tworzenia modeli. Możesz również kliknąć prawym przyciskiem myszy i wybrać pozycję elementy z menu, aby dodać ogólne wersje elementów, jak pokazano poniżej:

#### <a name="drop-the-stencil-on-the-canvas"></a>Upuść wzornik na kanwie

![Porzucenie kanwy](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Wybierz wzornik

![Właściwości elementu](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Wzorniki

W oparciu o wybrany szablon można znaleźć wszystkie wzorniki dostępne do użycia. Jeśli nie możesz znaleźć odpowiednich elementów, użyj innego szablonu. Można też zmodyfikować szablon, aby odpowiadał Twoim potrzebom. Ogólnie rzecz biorąc, można znaleźć kombinację kategorii takich jak:

| Nazwa wzornika                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Proces** | Aplikacje, wtyczki przeglądarki, wątki, maszyny wirtualne |
| **Zewnętrzna aktor** | Dostawcy uwierzytelniania, przeglądarki, użytkownicy, aplikacje sieci Web |
| **Magazyn danych** | Pamięć podręczna, magazyn, pliki konfiguracji, bazy danych, rejestr |
| **Przepływ danych** | Binary, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, nazwany potok, RPC/DCOM, SMB, UDP |
| **Granica linii/obramowania zaufania** | Sieci firmowe, Internet, maszyna, Piaskownica, tryb użytkownika/jądra |

### <a name="notesmessages"></a>Uwagi/wiadomości

| Składnik                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Komunikaty** | Wewnętrzna logika narzędzi, która ostrzega użytkowników za każdym razem, gdy wystąpi błąd, taki jak brak przepływów danych między elementami. |
| **Uwagi** | Ręczne notatki są dodawane do pliku przez zespoły inżynieryjne w całym procesie projektowania i recenzowania. |

### <a name="element-properties"></a>Właściwości elementu

Właściwości elementu różnią się w zależności od wybranych elementów. Poza granicami zaufania wszystkie inne elementy zawierają trzy opcje ogólne:

| Właściwość elementu                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Nazwa** | Jest to przydatne w przypadku nazywania procesów, sklepów, aktorów i przepływów, dzięki czemu można je łatwo rozpoznać. |
| **Poza zakresem** | W przypadku wybrania element jest pobierany z matrycy generacji zagrożeń (niezalecane). |
| **Przyczyna braku zakresu** | Pole uzasadnienia, aby umożliwić użytkownikom dowiedzieć się, dlaczego została wybrana poza zakresem. |

Właściwości są zmieniane w poszczególnych kategoriach elementów. Zaznacz każdy element, aby sprawdzić dostępne opcje. Możesz też otworzyć szablon, aby dowiedzieć się więcej. Zapoznaj się z funkcjami.

## <a name="welcome-screen"></a>Ekran powitalny

Po otwarciu aplikacji zobaczysz ekran **powitalny** .

### <a name="open-a-model"></a>Otwórz model

Zatrzymaj wskaźnik myszy nad **otwartym modelem** , aby odsłonić dwie opcje: **Otwórz z tego komputera** i **Otwórz z usługi OneDrive**. Pierwsza opcja powoduje otwarcie ekranu **otwieranie pliku** . Druga opcja przeprowadzi Cię przez proces logowania do usługi OneDrive. Po pomyślnym uwierzytelnieniu można wybrać foldery i pliki.

![Otwórz model](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Otwórz z komputera lub OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Opinie, sugestie i problemy

Po wybraniu **opinii, sugestii i problemów** przejdź do forum MSDN dla narzędzi SDL. Możesz odczytywać inne osoby, które mówią o tym narzędziu, w tym o obejściach i nowych pomysłach.

![Zrzut ekranu przedstawia przycisk z odpowiedzią na tekst, sugestiami i problemami.](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>widok Projekt

Po otwarciu lub utworzeniu nowego modelu zostanie otwarty widok **projekt** .

### <a name="add-elements"></a>Dodawanie elementów

Elementy w siatce można dodać na dwa sposoby:

- **Przeciągnij i upuść**: Przeciągnij żądany element do siatki. Następnie użyj właściwości elementu, aby podać dodatkowe informacje.
- **Kliknij prawym** przyciskiem myszy: kliknij prawym przyciskiem myszy w dowolnym miejscu siatki, a następnie wybierz pozycję elementy z menu rozwijanego. Na ekranie zostanie wyświetlona ogólna reprezentacja elementu, który został wybrany.

### <a name="connect-elements"></a>Połącz elementy

Elementy można łączyć na dwa sposoby:

- **Przeciągnij i upuść**: Przeciągnij żądany przepływu danych do siatki, a następnie połącz oba punkty końcowe z odpowiednimi elementami.
- **Kliknij przycisk + SHIFT**: kliknij pierwszy element (wysyłając dane), naciśnij i przytrzymaj klawisz Shift, a następnie wybierz drugi element (dane odebrane). Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Połącz**. Jeśli używasz przepływu danych dwukierunkowych, kolejność nie jest tak ważna.

### <a name="properties"></a>Właściwości

 Aby wyświetlić właściwości, które można zmodyfikować w wzornikach, wybierz wzornik, a informacje są wypełniane odpowiednio. Poniższy przykład ukazuje przed i po przeciągnięciu wzornika **bazy danych** na diagram:

#### <a name="before"></a>Stary adres

![Stary adres](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Po

![Po](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Komunikaty

Jeśli utworzysz model zagrożeń i zapomnisz połączyć przepływy danych z elementami, otrzymasz powiadomienie. Komunikat można zignorować lub można wykonać instrukcje, aby rozwiązać problem. 

![Zrzut ekranu przedstawia łącznik modelu zagrożeń, który nie jest połączony z elementami, z komunikatem, że przyczyną jest ten problem.](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Uwagi

Aby dodać uwagi do diagramu, przełącz się z karty **komunikaty** na kartę **notatki** .

## <a name="analysis-view"></a>Widok analizy

Po skompilowaniu diagramu wybierz symbol **analizy** (Lupa) na pasku narzędzi skróty, aby przełączyć się do widoku **analizy** .

![Widok analizy](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Wybór wygenerowanego zagrożenia

Po wybraniu zagrożenia można użyć trzech odrębnych funkcji:

| Cecha                               | Informacje      |
| --------------------------------------- | ------------ |
| **Wskaźnik odczytu** | <p>Zagrożenie jest oznaczone jako przeczytane, co pomaga śledzić przeglądane elementy.</p><p>![Wskaźnik odczytu/odczytania](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Fokus interakcji** | <p>Wyróżniono interakcje na diagramie należącym do zagrożenia.</p><p>![Fokus interakcji](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Właściwości zagrożeń** | <p>Dodatkowe informacje o zagrożeniu pojawiają się w oknie **Właściwości zagrożeń** .</p><p>![Właściwości zagrożeń](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Zmiana priorytetu

Można zmienić poziom priorytetu każdego z wygenerowanych zagrożeń. Różne kolory ułatwiają identyfikowanie zagrożeń o wysokim poziomie, średnim i niskim priorytecie.

![Zmiana priorytetu](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Pola edytowalne właściwości zagrożeń

Jak widać na poprzedniej ilustracji, można zmienić informacje wygenerowane przez narzędzie. Możesz również dodać informacje do określonych pól, takich jak uzasadnienie. Te pola są generowane przez szablon. Jeśli potrzebujesz więcej informacji dotyczących poszczególnych zagrożeń, możesz wprowadzić modyfikacje.

![Właściwości zagrożeń](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Raporty

Po zakończeniu zmieniania priorytetów i aktualizacji stanu każdego z wygenerowanych zagrożeń można zapisać plik i/lub wydrukować raport. Przejdź do pozycji **raport**  >  **Utwórz pełny raport**. Nadaj raportowi nazwę i zobacz coś podobnego do poniższej ilustracji:

![Zrzut ekranu przedstawia przykładowy raport modelowania zagrożeń, w tym podsumowanie, diagramy i inne informacje.](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Następne kroki

- Wyślij swoje pytania, komentarze i wątpliwości do programu tmtextsupport@microsoft.com . **[Pobierz](https://aka.ms/threatmodelingtool)** Threat Modeling Tool, aby rozpocząć pracę.
- Aby współtworzyć szablon dla społeczności, przejdź do naszej strony serwisu [GitHub](https://github.com/Microsoft/threat-modeling-templates) .