---
title: Automatyzowanie odpowiedzi na zagrożenia z elementy PlayBook na platformie Azure — wskaźnik | Microsoft Docs
description: W tym artykule opisano automatyzację na platformie Azure — informacje i przedstawiono sposób korzystania z elementy PlayBook w celu zautomatyzowania zapobiegania zagrożeniom i reagowania na nie.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: yelevin
ms.openlocfilehash: 0158c9f5b9debf0c47978e816951e25634621645
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609807"
---
# <a name="automate-threat-response-with-playbooks-in-azure-sentinel"></a>Automatyzowanie odpowiedzi na zagrożenia za pomocą elementy PlayBook na platformie Azure — wskaźnik

W tym artykule wyjaśniono, co to jest usługa Azure elementy PlayBook, oraz jak korzystać z nich do implementowania operacji aranżacji, automatyzacji i reagowania w zabezpieczeniach (o), osiąganie lepszych wyników podczas oszczędzania czasu i zasobów.

## <a name="what-is-a-playbook"></a>Co to jest element PlayBook?

Zespoły SIEM/SOC są zwykle zasypaniu z alertami i zdarzeniami zabezpieczeń w regularnych odstępach czasu, dzięki czemu w dużej ilości dostępnego personelu są przeważnie. Wynika to również z tego, że wiele alertów jest ignorowanych i wiele incydentów nie jest badanych, a organizacja jest narażona na ataki niezauważalne.

Wiele, jeśli nie większość, tych alertów i zdarzeń jest zgodnych ze wzorcami cyklicznymi, które mogą być rozwiązanymi przez określone i zdefiniowane zestawy akcji korygowania.

Element PlayBook to zbiór akcji korygowania, które można uruchomić z obszaru danych platformy Azure jako procedury. Element PlayBook może pomóc zautomatyzować i zorganizować odpowiedź na zagrożenie; można go uruchomić ręcznie lub skonfigurować do automatycznego uruchamiania w odpowiedzi na określone alerty lub zdarzenia, w przypadku wyzwolenia odpowiednio przez regułę analizy lub regułę automatyzacji.

Elementy PlayBook są tworzone i stosowane na poziomie subskrypcji, ale karta **elementy PlayBook** (w bloku Nowa **Automatyzacja** ) wyświetla wszystkie elementy PlayBook dostępne w ramach wszystkich wybranych subskrypcji.

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Apps podstawowe pojęcia

Elementy PlayBook na platformie Azure — na podstawie przepływów pracy wbudowanych w [Azure Logic Apps](../logic-apps/logic-apps-overview.md), usługi w chmurze, która ułatwia planowanie, Automatyzowanie i organizowanie zadań i przepływów pracy w różnych systemach w całym przedsiębiorstwie. Oznacza to, że elementy PlayBook może korzystać ze wszystkich możliwości i szerszym wbudowanych szablonów Logic Apps ".

> [!NOTE]
> Ponieważ Azure Logic Apps są osobnym zasobem, mogą zostać naliczone dodatkowe opłaty. Aby uzyskać więcej informacji, odwiedź stronę z cennikiem [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) .

Azure Logic Apps komunikują się z innymi systemami i usługami za pomocą łączników. Poniżej przedstawiono krótkie objaśnienie łączników i niektóre z ich ważnych atrybutów:

- **Zarządzany łącznik:** Zestaw akcji i wyzwalaczy otaczających wywołania interfejsów API do określonego produktu lub usługi. Azure Logic Apps oferuje setki łączników do komunikowania się z usługami firmy Microsoft i innych firm.
  - [Lista wszystkich łączników Logic Apps i ich dokumentacji](/connectors/connector-reference/)

- **Łącznik niestandardowy:** Możesz chcieć komunikować się z usługami, które nie są dostępne jako wstępnie utworzone łączniki. Niestandardowe Łączniki wymagają tego, aby można było tworzyć łączniki (i nawet udostępniać je) oraz definiować własne wyzwalacze i akcje.
  - [Tworzenie własnych niestandardowych łączników Logic Apps](/connectors/custom-connectors/create-logic-apps-connector)

- **Łącznik wskaźnikowy platformy Azure:** Aby utworzyć elementy PlayBook, które współpracują z platformą Azure — wskaźnikiem, użyj łącznika Azure wskaźnikowego.
  - [Dokumentacja dotycząca łącznika wskaźnikowego platformy Azure](/connectors/azuresentinel/)

- **Wyzwalacz:** Składnik łącznika, który uruchamia element PlayBook. Definiuje schemat, który element PlayBook oczekuje na odebranie po wyzwoleniu. Łącznik wskaźnikowego platformy Azure ma obecnie dwa wyzwalacze:
  - [Wyzwalacz alertu](/connectors/azuresentinel/#triggers): element PlayBook odbiera alert jako dane wejściowe.
  - [Wyzwalacz zdarzenia](/connectors/azuresentinel/#triggers): element PlayBook odbiera zdarzenie jako dane wejściowe oraz wszystkie zawarte w nim alerty i jednostki.

    > [!IMPORTANT]
    >
    > - Funkcja **wyzwalacza zdarzenia** dla usługi elementy PlayBook jest obecnie w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

- **Akcje:** Akcje to wszystkie kroki wykonywane po wyzwalaczu. Mogą być ułożone sekwencyjnie, równolegle lub w macierzy złożonych warunków.

- **Pola dynamiczne:** Pola tymczasowe, określane przez schemat danych wyjściowych wyzwalaczy i akcji i wypełniane przez ich rzeczywiste dane wyjściowe, które mogą być używane w następujących akcjach.

### <a name="permissions-required"></a>Wymagane uprawnienia

 Aby dać zespołowi SecOP możliwości korzystania z Logic Apps na potrzeby operacji aranżacji zabezpieczeń, automatyzacji i odpowiedzi (o) — to znaczy, aby utworzyć i uruchomić elementy PlayBook na platformie Azure, można przypisać role platformy Azure do określonych członków zespołu operacji zabezpieczeń lub całego zespołu. Poniżej opisano różne dostępne role oraz zadania, do których powinny zostać przypisane:

#### <a name="azure-roles-for-logic-apps"></a>Role platformy Azure dla Logic Apps

- **Współautor aplikacji logiki** umożliwia zarządzanie aplikacjami logiki i uruchamianie elementy PlayBook, ale nie można zmienić dostępu do nich (dla potrzeb roli **właściciela** ).
- **Operator aplikacji logiki**  pozwala odczytywać, włączać i wyłączać aplikacje logiki, ale nie można ich edytować ani aktualizować.

#### <a name="azure-roles-for-sentinel"></a>Role platformy Azure dla kontrolki wskaźnikowej

- Rola **współautora wskaźnikowego platformy Azure** umożliwia dołączenie element PlayBook do reguły analizy.
- Rola **obiektu odpowiadającego wskaźnikowi systemu Azure** — pozwala ręcznie uruchomić element PlayBook.
- **Współautor usługi Azure wskaźnikowej automatyzacji** pozwala regułom automatyzacji uruchamiać elementy PlayBook. Nie jest używany do żadnego innego celu.

#### <a name="learn-more"></a>Więcej tutaj

- [Dowiedz się więcej na temat ról platformy Azure w Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [Dowiedz się więcej na temat ról platformy Azure na platformie Azure — wskaźnik](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Procedura tworzenia element PlayBook

- [Zdefiniuj scenariusz automatyzacji](#use-cases-for-playbooks).

- [Kompiluj aplikację logiki platformy Azure](tutorial-respond-threats-playbook.md).

- [Przetestuj aplikację logiki](#run-a-playbook-manually-on-an-alert).

- Dołącz element PlayBook do [reguły automatyzacji](#incident-creation-automated-response) lub [reguły analizy](#alert-creation-automated-response)lub [Uruchom ręcznie, jeśli](#run-a-playbook-manually-on-an-alert)jest to wymagane.

### <a name="use-cases-for-playbooks"></a>Przypadki użycia dla elementy PlayBook

Platforma Azure Logic Apps oferuje setki akcji i wyzwalaczy, więc można utworzyć niemal każdy scenariusz automatyzacji. Zalecane jest, aby na platformie Azure — od następujących scenariuszy SOC:

#### <a name="enrichment"></a>Wzbogacanie

**Zbieranie danych i dołączanie ich do zdarzenia w celu podejmowania bardziej przemyślanych decyzji.**

Na przykład:

Zdarzenie wskaźnikowe platformy Azure zostało utworzone na podstawie alertu przez regułę analizy, która generuje jednostki adresów IP.

Zdarzenie wyzwala regułę automatyzacji, która uruchamia element PlayBook z następującymi krokami:

- Zacznij od [utworzenia nowego zdarzenia wskaźnikowego platformy Azure](/connectors/azuresentinel/#triggers). Jednostki reprezentowane w zdarzeniu są przechowywane w polach dynamicznych wyzwalacza zdarzenia.

- Dla każdego adresu IP należy wykonać zapytanie do zewnętrznego dostawcy analizy zagrożeń, na przykład [łącznie z wirusami](https://www.virustotal.com/), aby uzyskać więcej danych.

- Dodaj zwrócone dane i szczegółowe informacje jako komentarze zdarzenia.

#### <a name="bi-directional-sync"></a>Synchronizacja dwukierunkowa

**Elementy PlayBook może służyć do synchronizowania wydarzeń wskaźnikowych platformy Azure z innymi systemami tworzenia biletów.**

Na przykład:

Utwórz regułę automatyzacji dla wszystkich procesów tworzenia zdarzeń i Dołącz element PlayBook, który otwiera bilet w usługi ServiceNow:

- Zacznij od [utworzenia nowego zdarzenia wskaźnikowego platformy Azure](/connectors/azuresentinel/#triggers).

- Utwórz nowy bilet w [usługi ServiceNow](/connectors/service-now/).

- Dołącz do biletu nazwę zdarzenia, ważne pola i adres URL zdarzenia wskaźnikowego platformy Azure w celu ułatwienia przestawiania.

#### <a name="orchestration"></a>Aranżacja

**Korzystając z platformy czatu SOC, można lepiej kontrolować kolejkę zdarzeń.**

Na przykład:

Zdarzenie wskaźnikowe platformy Azure zostało utworzone na podstawie alertu przez regułę analizy, która generuje jednostki nazw użytkowników i adresów IP.

Zdarzenie wyzwala regułę automatyzacji, która uruchamia element PlayBook z następującymi krokami:

- Zacznij od [utworzenia nowego zdarzenia wskaźnikowego platformy Azure](/connectors/azuresentinel/#triggers).

- Wyślij wiadomość do kanału operacji zabezpieczeń w usłudze [Microsoft Teams](/connectors/teams/) lub [zapasu czasu](/connectors/slack/) , aby upewnić się, że analitycy zabezpieczeń wiedzą o zdarzeniu.

- Wyślij wszystkie informacje z alertu pocztą e-mail do administratora sieci w starszej firmie i administratora zabezpieczeń. Wiadomość e-mail będzie zawierać przyciski opcji **Blokuj** i **Ignoruj** użytkownika.

- Poczekaj, aż otrzymasz odpowiedź od administratorów, a następnie Kontynuuj, aby uruchomić.

- Jeśli wybrano opcję **Zablokuj** administratorów, Wyślij polecenie do zapory, aby zablokować adres IP w alercie, a drugi w usłudze Azure AD wyłączyć użytkownika.

#### <a name="response"></a>Reakcja

**Natychmiastowe reagowanie na zagrożenia, z minimalnymi zależnościami człowieka.**

Dwa przykłady:

**Przykład 1:** Odpowiedz na regułę analizy, która wskazuje, że naruszony użytkownik został ujawniony przez [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md):

   - Zacznij od [utworzenia nowego zdarzenia wskaźnikowego platformy Azure](/connectors/azuresentinel/#triggers).

   - Dla każdej jednostki użytkownika w zdarzeniu podejrzanym o naruszenie zabezpieczeń:

     - Wyślij komunikat zespołu do użytkownika, żądając potwierdzenia, że użytkownik wykonał podejrzaną akcję.

     - Sprawdź, czy Azure AD Identity Protection, aby [potwierdzić, że stan użytkownika został naruszony](/connectors/azureadip/#confirm-a-risky-user-as-compromised). Azure AD Identity Protection będzie etykietą użytkownika jako **ryzykowną** i Zastosuj wszelkie już skonfigurowane zasady wymuszania — na przykład, aby wymagać od użytkownika korzystania z usługi MFA podczas kolejnego logowania.

        > [!NOTE]
        > Element PlayBook nie inicjuje żadnej akcji wymuszania dla użytkownika ani nie inicjuje żadnej konfiguracji zasad wymuszania. Informuje on tylko Azure AD Identity Protection o konieczności zastosowania już zdefiniowanych zasad. Każde wymuszenie jest zależne wyłącznie od odpowiednich zasad zdefiniowanych w Azure AD Identity Protection.

**Przykład 2:** Odpowiedz na regułę analizy wskazującą, że zagrożony komputer został odnaleziony przez usługę [Microsoft Defender dla punktu końcowego](/windows/security/threat-protection/):

   - Zacznij od [utworzenia nowego zdarzenia wskaźnikowego platformy Azure](/connectors/azuresentinel/#triggers).

   - W celu przeanalizowania podejrzanych maszyn uwzględnionych w jednostkach zdarzeń należy użyć akcji **Pobierz hosty** na platformie Azure.

   - Wydaj polecenie do usługi Microsoft Defender dla punktu końcowego, aby [odizolować maszynę](/connectors/wdatp/#actions---isolate-machine) w alercie.

## <a name="how-to-run-a-playbook"></a>Jak uruchomić element PlayBook

Elementy PlayBook można uruchomić **ręcznie** lub **automatycznie**.

Uruchamianie ich ręcznie oznacza, że po otrzymaniu alertu można uruchomić element PlayBook na żądanie jako odpowiedź na wybrany alert. Obecnie ta funkcja jest obsługiwana tylko dla alertów, a nie dla zdarzeń.

Uruchamianie ich automatycznie oznacza, że są ustawiane jako automatyczne odpowiedzi w regule analizy (dla alertów) lub jako akcja w regule automatyzacji (dla zdarzeń). [Dowiedz się więcej o regułach automatyzacji](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Ustaw automatyczną odpowiedź

Zespoły operacji zabezpieczeń mogą znacząco ograniczyć obciążenie, w pełni automatyzując rutynowe odpowiedzi na powtarzające się typy zdarzeń i alertów, co umożliwia skoncentrowanie się na unikatowych zdarzeniach i alertach, analizowanie wzorców, polowaniu zagrożeń i nie tylko.

Ustawienie automatycznej reakcji oznacza, że za każdym razem, gdy reguła analizy jest wyzwalana, oprócz tworzenia alertu reguła zostanie uruchomiona element PlayBook, która będzie otrzymywać jako dane wejściowe alert utworzony przez regułę.

Jeśli alert tworzy zdarzenie, zdarzenie spowoduje wyzwolenie reguły automatyzacji, która może z kolei uruchamiać element PlayBook, która otrzyma jako dane wejściowe zdarzenia utworzone przez alert.

#### <a name="alert-creation-automated-response"></a>Automatyczna odpowiedź na tworzenie alertów

Dla elementy PlayBook, które są wyzwalane przez tworzenie alertów i otrzymywanie alertów jako ich danych wejściowych (pierwszy krok to "po wyzwoleniu alertu dotyczącego usługi Azure wskaźnikowego"), Dołącz element PlayBook do reguły analizy:

1. Edytuj [regułę analizy](tutorial-detect-threats-custom.md) , która generuje alert, dla którego chcesz zdefiniować automatyczną odpowiedź.

1. W obszarze **Automatyzacja alertów** na karcie **automatyczne odpowiedzi** wybierz pozycję element PlayBook lub elementy PlayBook, która będzie wyzwalana przez tę regułę analizy po utworzeniu alertu.

#### <a name="incident-creation-automated-response"></a>Automatyczna odpowiedź na tworzenie zdarzeń

W przypadku elementy PlayBook, które są wyzwalane przez tworzenie zdarzeń i odbieranie zdarzeń jako ich danych wejściowych (pierwszy krok to "Kiedy wyzwalane jest zdarzenie wskaźnikowe platformy Azure"), Utwórz regułę automatyzacji i Zdefiniuj w niej akcję **Run element PlayBook** . Można to zrobić na dwa sposoby:

- Edytuj regułę analizy, która generuje zdarzenie, dla którego chcesz zdefiniować automatyczną odpowiedź. W obszarze **Automatyzacja zdarzeń** na karcie **automatyczne odpowiedzi** Utwórz regułę automatyzacji. Spowoduje to utworzenie zautomatyzowanej odpowiedzi tylko dla tej reguły analizy.

- Na karcie **reguły automatyzacji** w bloku **Automatyzacja** Utwórz nową regułę automatyzacji i określ odpowiednie warunki i wymagane akcje. Ta reguła automatyzacji zostanie zastosowana do dowolnej reguły analizy, która spełnia określone warunki.

    > [!NOTE]
    > Aby można było uruchomić element PlayBook z reguły automatyzacji, wskaźnik na platformie Azure używa konta usługi przeznaczonego do tego celu. Użycie tego konta (w przeciwieństwie do konta użytkownika) powoduje zwiększenie poziomu zabezpieczeń usługi i włączenie interfejsu API reguł automatyzacji do obsługi przypadków użycia ciągłej integracji/ciągłego wdrażania.
    >
    > To konto musi mieć przyznane jawne uprawnienia do grupy zasobów, w której znajduje się element PlayBook. W tym momencie Każda reguła automatyzacji będzie mogła uruchamiać dowolne element PlayBook w tej grupie zasobów.
    >
    > Po dodaniu akcji **Uruchom element PlayBook** do reguły automatyzacji zostanie wyświetlona lista rozwijana elementy PlayBook. Elementy PlayBook, do którego nie ma uprawnień na platformie Azure, będą wyświetlane jako niedostępne ("wygaszone"). Można udzielić uprawnienia do punktu kontrolnego platformy Azure, wybierając łącze **Zarządzaj uprawnieniami element PlayBook** .
    >
    > W scenariuszu obejmującym wiele dzierżawców ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) należy zdefiniować uprawnienia w dzierżawie, w której przebywa element PlayBook, nawet jeśli zasada automatyzacji wywołująca element PlayBook znajduje się w innej dzierżawie. Aby to zrobić, musisz mieć uprawnienia **właściciela** do grupy zasobów element PlayBook.

Zapoznaj się z [kompletnymi instrukcjami dotyczącymi tworzenia reguł automatyzacji](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Uruchom element PlayBook ręcznie dla alertu

Ręczne wyzwalanie jest dostępne w portalu Azure wskaźnikowym na następujących blokach:

- W widoku **incydentów** wybierz konkretne zdarzenie, otwórz jego kartę **alerty** i wybierz alert.

- W obszarze **Badanie** wybierz konkretny alert.

1. Kliknij pozycję **Wyświetl elementy PlayBook** dla wybranego alertu. Otrzymasz listę wszystkich elementy PlayBook, które zaczynają się od **momentu wyzwolenia alertu dotyczącego usługi Azure wskaźnikowego** i dostępu do programu.

1. Kliknij pozycję **Uruchom** w wierszu określonego element PlayBook, aby go wyzwolić.

1. Wybierz kartę **uruchomienia** , aby wyświetlić listę wszystkich przypadków, w których wszystkie element PlayBook zostały uruchomione w ramach tego alertu. Ukończenie dowolnego uruchomienia na tej liście może potrwać kilka sekund.

1. Kliknięcie określonego uruchomienia spowoduje otwarcie dziennika pełnego uruchomienia w Logic Apps.

### <a name="run-a-playbook-manually-on-an-incident"></a>Ręczne uruchamianie element PlayBook na zdarzeniu

Jeszcze nieobsługiwane. <!--make this a note instead? -->

## <a name="manage-your-playbooks"></a>Zarządzanie elementy PlayBook

Na karcie **elementy PlayBook** zostanie wyświetlona lista wszystkich elementy PlayBook, do których masz dostęp, filtrowanych przez subskrypcje, które są aktualnie wyświetlane na platformie Azure. Filtr subskrypcje jest dostępny w menu **katalog + subskrypcja** w globalnym nagłówku strony.

Kliknięcie nazwy element PlayBook kieruje Cię do strony głównej element PlayBook w Logic Apps. Kolumna **stan** wskazuje, czy jest włączona, czy wyłączona.

**Typ wyzwalacza** reprezentuje wyzwalacz Logic Apps, który uruchamia ten element PlayBook.

| Typ wyzwalacza | Wskazuje typy składników w element PlayBook |
|-|-|
| **Zdarzenie/alert dotyczący platformy Azure** | Element PlayBook jest uruchamiany przy użyciu jednego z wyzwalaczy wskaźnikowych (alertu, zdarzenia) |
| **Korzystanie z akcji wskaźnikowej platformy Azure** | Element PlayBook jest uruchamiany z wyzwalaczem innym niż wskaźnik wskazujący, ale używa akcji wskaźnikowej platformy Azure |
| **Inne** | Element PlayBook nie zawiera żadnych składników wskaźnikowych |
| **Nie zainicjowano** | Element PlayBook został utworzony, ale nie zawiera składników (wyzwalacze lub akcje). |
|

Na stronie aplikacji logiki element PlayBook można zobaczyć więcej informacji na temat element PlayBook, w tym dziennika wszystkich momentów jego uruchomienia, a także wynik (powodzenie lub niepowodzenie i inne szczegóły). Możesz również wprowadzić Logic Apps projektanta i bezpośrednio edytować element PlayBook, jeśli masz odpowiednie uprawnienia.

### <a name="api-connections"></a>Połączenia interfejsu API

Połączenia interfejsu API są używane do łączenia Logic Apps z innymi usługami. Za każdym razem, gdy tworzone jest nowe uwierzytelnianie do Logic Apps łącznika, tworzony jest nowy zasób typu **połączenie interfejsu API** i zawiera informacje zawarte podczas konfigurowania dostępu do usługi.

Aby wyświetlić wszystkie połączenia interfejsu API, wprowadź *połączenia interfejsu API* w polu wyszukiwania nagłówka Azure Portal. Zwróć uwagę na interesujące Cię kolumny:

- Nazwa wyświetlana — "przyjazna" nazwa nadana połączeniu za każdym razem, gdy ją tworzysz.
- Stan — wskazuje stan połączenia: błąd, połączony.
- Grupy zasobów — połączenia interfejsu API są tworzone w grupie zasobów zasobu element PlayBook (Logic Apps).

Innym sposobem wyświetlania połączeń interfejsu API jest przejście do bloku **wszystkie zasoby** i odfiltrowanie go według typu *połączenia interfejsu API*. Dzięki temu można zaznaczać, znakować i usuwać wiele połączeń jednocześnie.

Aby zmienić autoryzację istniejącego połączenia, wprowadź zasób połączenia i wybierz pozycję **Edytuj połączenie z interfejsem API**.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: używanie elementy PlayBook do automatyzowania odpowiedzi na zagrożenia na platformie Azure — wskaźnik](tutorial-respond-threats-playbook.md)
