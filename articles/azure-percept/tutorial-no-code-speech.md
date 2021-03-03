---
title: Tworzenie asystenta głosowego za pomocą usługi Azure Percept DK i usługi Azure Percept audio
description: Dowiedz się, jak utworzyć i wdrożyć rozwiązanie do obsługi mowy bez kodu na platformie Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: de85c4f8cdcd9781345ee1488549aab23e38ec5c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665218"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Tworzenie asystenta głosowego za pomocą usługi Azure Percept DK i usługi Azure Percept audio

W tym samouczku utworzysz asystenta głosowego na podstawie szablonu, który ma być używany z funkcjami Azure Percept DK i Azure Percept. Pokaz asystenta głosowego jest uruchamiany w ramach [usługi Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) i zawiera wybrane obiekty wirtualne sterowane głosami. Aby kontrolować obiekt, należy powiedzieć słowo kluczowe, czyli słowo lub krótka fraza, która wznawia urządzenie, a następnie polecenie. Każdy szablon odpowiada zestawowi konkretnych poleceń.

Ten przewodnik przeprowadzi Cię przez proces konfigurowania urządzeń, tworzenia asystenta głosowego i niezbędnych zasobów [usług mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview) , testowania asystenta głosowego, konfigurowania słowa kluczowego i tworzenia niestandardowych słów kluczowych.

## <a name="prerequisites"></a>Wymagania wstępne

- Azure Percept DK (Devkit)
- Azure Percept — dźwięk
- Głośnik lub słuchawki (opcjonalnie)
- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/)
- [Środowisko instalacyjne platformy Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): nawiązano połączenie Devkit z siecią Wi-Fi, utworzono IoT Hub i połączono devkit z IoT Hub

## <a name="device-setup"></a>Konfiguracja urządzenia

1. (Opcjonalnie) Połącz prelegenta lub słuchawkę z modelem audio SoM za pośrednictwem gniazda słuchawek, które jest oznaczone jako "line out". Pozwoli to na słuchanie odpowiedzi dźwiękowych asystenta głosowego. Jeśli nie połączysz prelegenta lub słuchawki, nadal będzie można zobaczyć odpowiedzi jako tekst w oknie demonstracji.

1. Podłącz model SoM audio do tablicy nośnej Devkit za pomocą kabla USB-A do mikrob.

1. Włącz Devkit.

    - Dioda LED L01 w modelu SoM audio zmieni się na Solid zielony, aby wskazać, że urządzenie zostało włączone.
    - L02 diody LED zmieni się na miganie, aby wskazać, że model SoM audio jest uwierzytelniany.

1. Poczekaj na zakończenie procesu uwierzytelniania — może to potrwać do 3 minut.

1. Przejdź do następnej sekcji, gdy zobaczysz jedną z następujących czynności:

    - Dioda LED L01 wyłącza się, a L02 wyłącza kolor biały. Oznacza to, że uwierzytelnianie jest ukończone i Devkit nie został jeszcze skonfigurowany za pomocą słowa kluczowego.
    - Wszystkie trzy diody LED zmienią kolor na niebieski. Oznacza to, że uwierzytelnianie jest ukończone i Devkit jest skonfigurowany za pomocą słowa kluczowego.

    > [!NOTE]
    > Skontaktuj się z pomocą techniczną, aby dowiedzieć się, czy Devkit nie uwierzytelnia się.

## <a name="create-a-voice-assistant-using-an-available-template"></a>Tworzenie asystenta głosowego przy użyciu dostępnego szablonu

1. Przejdź do [usługi Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Otwórz kartę **samouczki & pokazów** .

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Zrzut ekranu przedstawiający stronę główną Azure Portal.":::

1. Kliknij pozycję **Wypróbuj szablony asystenta głosowego** w obszarze **samouczki i pokazy mowy**. Spowoduje to otwarcie okna po prawej stronie ekranu.

1. Wykonaj następujące czynności w oknie:

    1. W menu rozwijanym **IoT Hub** wybierz Centrum IoT, z którym nawiązano połączenie z usługą Devkit.

    1. W menu rozwijanym **urządzenie** wybierz swój Devkit.

    1. Wybierz jeden z dostępnych szablonów asystenta głosowego.

    1. Kliknij pole wyboru **Akceptuję warunki & dla tego projektu** .

    1. Kliknij pozycję **Utwórz**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Zrzut ekranu przedstawiający tworzenie szablonu asystenta głosowego.":::

1. Po kliknięciu przycisku **Utwórz** w portalu zostanie otwarte inne okno umożliwiające utworzenie zasobu motywu mowy. Wykonaj następujące czynności w oknie:

    1. W polu **subskrypcja** wybierz subskrypcję platformy Azure.

    1. Wybierz preferowaną grupę zasobów z menu rozwijanego **Grupa zasobów** . Jeśli chcesz utworzyć nową grupę zasobów, która ma być używana z asystentem głosu, kliknij przycisk **Utwórz** w menu rozwijanym i postępuj zgodnie z instrukcjami.

    1. W obszarze **prefiks aplikacji** wprowadź nazwę. Będzie to prefiks projektu i nazwy polecenia niestandardowego.

    1. W obszarze **region** wybierz region, w którym mają zostać wdrożone zasoby.

    1. W obszarze **warstwa cenowa przewidywania Luis** wybierz pozycję **standardowa** (warstwa Bezpłatna nie obsługuje żądań mowy).

    1. Kliknij przycisk **Utwórz**. Zasoby dla aplikacji asystenta głosowego zostaną wdrożone w Twojej subskrypcji.

        > [!WARNING]
        > **Nie** zamykaj okna do momentu zakończenia wdrażania zasobu przez portal. Zamknięcie okna przedwcześnie może skutkować nieoczekiwanym zachowaniem asystenta głosowego. Po wdrożeniu zasobu zostanie wyświetlony pokaz.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Zrzut ekranu okna wyboru subskrypcji i grupy zasobów.":::

## <a name="test-out-your-voice-assistant"></a>Testowanie asystenta głosowego

Aby korzystać z asystenta głosowego, należy powiedzieć słowa kluczowego, po którym następuje polecenie. Gdy obiekt SoM jest rozpoznawany jako słowo kluczowe, urządzenie emituje CHIME (który można usłyszeć, jeśli nastąpi połączenie z prelegentem lub słuchawą), a diody LED migają niebiesko. Diody LED przełączają się na niebieską, podczas gdy polecenie jest przetwarzane. Odpowiedź asystenta głosowego na polecenie zostanie wydrukowany tekst w oknie demonstracji i wyemitowana audibly przez prelegenta lub słuchawkę. Domyślne słowo kluczowe (wymienione obok **słowa kluczowego Custom**) ma ustawioną wartość "Computer", a każdy szablon zawiera zestaw zgodnych poleceń, które umożliwiają współdziałanie z obiektami wirtualnymi w oknie demonstracyjnym. Na przykład jeśli korzystasz z demonstracji Hotelarstwo lub opieki zdrowotnej, powiedz "Computer, Włącz TV", aby włączyć wirtualną TV.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Zrzut ekranu przedstawiający okno demonstracyjne Hotelarstwo.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Polecenia demonstracji Hotelarstwo i opieki zdrowotnej

Zarówno pokazy opieki zdrowotnej, jak i Hotelarstwo mają wirtualne telewizory, lampki, żaluzje i termostaty, z którymi można korzystać. Obsługiwane są następujące polecenia (i dodatkowe zmiany):

* "Włącz/Wyłącz świateł".
* "Włącz/Wyłącz telewizor".
* "Włącz/Wyłącz AC".
* "Otwórz/Zamknij żaluzje".
* "Ustaw temperaturę na X stopni". (X jest pożądaną temperaturą, np. 75).

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Zrzut ekranu przedstawiający okno demonstracji dotyczącej opieki zdrowotnej.":::

### <a name="automotive-demo-commands"></a>Polecenia demonstracyjne motoryzacyjne

Demonstracja motoryzacyjna zawiera wirtualne rozgrzane, programowe i termostaty, z których można korzystać. Obsługiwane są następujące polecenia (i dodatkowe zmiany):

* "Włącz/Wyłącz demrozer".
* "Włącz/Wyłącz ciepły siedzisko".
* "Ustaw temperaturę na X stopni". (X jest pożądaną temperaturą, np. 75).
* "Zwiększ/Zmniejsz temperaturę o stopniu Y".

:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Zrzut ekranu przedstawiający okno pokazowe platformy motoryzacyjnej.":::

### <a name="inventory-demo-commands"></a>Polecenia demonstracyjne spisu

Demonstracja spisu zawiera wybrane niebieskie, żółte i zielone pola, które umożliwiają współdziałanie z aplikacją wirtualnego spisu. Obsługiwane są następujące polecenia (i dodatkowe zmiany):

* "Dodawanie/usuwanie pól X". (X jest liczbą pól, np. 4).
* "Kolejność/dostarczanie pól X".
* "Ile pól znajduje się w magazynie"?
* "Liczba pól Y". (T to kolor pól, na przykład żółty).
* "Wyślij wszystko w magazynie".

:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Zrzut ekranu przedstawiający okno pokazowe spisu.":::

## <a name="configure-your-keyword"></a>Skonfiguruj słowo kluczowe

Aby zmienić słowo kluczowe, kliknij przycisk **Zmień** obok **słowa kluczowego Custom** w oknie demonstracyjnym. Wybierz jedno z dostępnych słów kluczowych i kliknij przycisk **Zapisz**. Będziesz mieć możliwość wyboru spośród wstępnie skompilowanych słów kluczowych i utworzonych niestandardowych słów kluczowych.

:::image type="content" source="./media/tutorial-no-code-speech/change-keyword.png" alt-text="Zrzut ekranu przedstawiający wybór dostępnych słów kluczowych.":::

### <a name="create-a-custom-keyword"></a>Tworzenie niestandardowego słowa kluczowego

Aby utworzyć niestandardowe słowo kluczowe, kliknij pozycję **+ Utwórz niestandardowe słowo kluczowe** w górnej części okna demonstracyjnego. Wprowadź odpowiednie słowo kluczowe, które może być pojedynczym słowem lub krótką frazą, wybierz **zasób mowy** (znajduje się obok **polecenia Custom** w oknie demonstracyjnym i zawiera prefiks aplikacji), a następnie kliknij przycisk **Zapisz**. Szkolenie dotyczące niestandardowego słowa kluczowego może zakończyć się w ciągu zaledwie kilku sekund.

:::image type="content" source="./media/tutorial-no-code-speech/custom-keyword.png" alt-text="Zrzut ekranu przedstawiający okno tworzenia niestandardowego słowa kluczowego.":::

## <a name="create-a-custom-command"></a>Utwórz polecenie niestandardowe

Portal udostępnia również funkcje tworzenia niestandardowych poleceń z istniejącymi zasobami mowy. "Polecenie niestandardowe" odwołuje się do samej aplikacji asystenta głosowego, a nie określonego polecenia w ramach istniejącej aplikacji. Utworzenie polecenia niestandardowego powoduje utworzenie nowego projektu mowy, który należy dodatkowo opracować w programie [Speech Studio](https://speech.microsoft.com/).

Aby utworzyć nowe polecenie niestandardowe z poziomu okna demonstracyjnego, kliknij pozycję **+ Utwórz niestandardowe polecenie** u góry strony i wykonaj następujące czynności:

1. Wprowadź nazwę dla polecenia niestandardowego.

1. Wprowadź opis projektu (opcjonalnie).

1. Wybierz preferowany język.

1. Wybierz zasób mowy.

1. Wybierz zasób LUIS.

1. Wybierz zasób LUIS Authoring lub Utwórz nowy.

1. Kliknij pozycję **Utwórz**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Zrzut ekranu przedstawiający okno tworzenia poleceń niestandardowych.":::

Po utworzeniu polecenia niestandardowego musisz przejść do programu [Speech Studio](https://speech.microsoft.com/) , aby uzyskać dalsze prace programistyczne. Jeśli otworzysz program Speech Studio i nie widzisz na liście polecenia niestandardowego, wykonaj następujące kroki:

1. W panelu menu po lewej stronie w usłudze Azure Percept Studio kliknij pozycję **Speech** w obszarze **projekty AI**.

1. Wybierz kartę **polecenia** .

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Zrzut ekranu przedstawiający listę poleceń niestandardowych dostępnych do edycji.":::

1. Wybierz polecenie niestandardowe, które chcesz utworzyć. Spowoduje to otwarcie projektu w programie Speech Studio.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Zrzut ekranu przedstawiający ekran główny programu Speech Studio.":::

Więcej informacji na temat opracowywania poleceń niestandardowych można znaleźć w [dokumentacji usługi mowy](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-commands).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>Asystent głosowy został utworzony, ale nie odpowiada na polecenia

Sprawdź sygnalizatory LED w modelu SoM audio:

* Trzy pełne niebieskie sygnalizatory wskazują, że asystent głosowy jest gotowy i oczekuje na słowo kluczowe.
* Jeśli centralny dioda LED (L02) jest biała, Devkit została zainicjowana i musi być skonfigurowana za pomocą słowa kluczowego.
* Dowolna kombinacja zielonych sygnalizatorów wskazuje, że nie ukończono jeszcze inicjalizacji modelu audio. Inicjowanie może potrwać kilka minut.

Aby uzyskać więcej informacji na temat wskaźników LED modelu SoM (audio), zobacz artykuł dotyczący diody LED.

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>Asystent głosowy nie odpowiada na niestandardowe słowo kluczowe utworzone w programie Speech Studio

Taka sytuacja może wystąpić, jeśli moduł mowy jest nieaktualny. Wykonaj następujące kroki, aby zaktualizować moduł mowy do najnowszej wersji:

1. Kliknij pozycję **urządzenia** w panelu menu po lewej stronie strony głównej usługi Azure Percept Studio.

1. Znajdź i wybierz urządzenie.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Zrzut ekranu listy urządzeń w usłudze Azure Percept Studio.":::

1. W oknie urządzenie wybierz kartę **mowa** .

1. Sprawdź wersję modułu Speech. Jeśli dostępna jest aktualizacja, zobaczysz przycisk **aktualizacji** obok numeru wersji.

    :::image type="content" source="./media/tutorial-no-code-speech/devkit.png" alt-text="Zrzut ekranu przedstawiający okno ustawień mowy Devkit.":::

1. Kliknij przycisk **Aktualizuj** , aby wdrożyć aktualizację modułu mowy. Proces aktualizacji trwa zwykle 2-3 minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z aplikacją asystenta głosowego wykonaj następujące kroki, aby wyczyścić zasoby mowy wdrożone w ramach tego samouczka:

1. W [Azure Portal](https://ms.portal.azure.com/#home)wybierz pozycję **grupy zasobów** w panelu menu po lewej stronie lub wpisz ją na pasku wyszukiwania.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Zrzut ekranu przedstawiający Azure Portal Strona główna pokazująca panel menu po lewej stronie i grupy zasobów.":::

1. Wybierz swoją grupę zasobów.

1. Zaznacz wszystkie sześć zasobów zawierających prefiks aplikacji i kliknij ikonę **Usuń** w górnym panelu menu.

    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Zrzut ekranu zasobów mowy wybranych do usunięcia.":::

1. Aby potwierdzić usunięcie, wpisz **tak** w polu potwierdzenia, sprawdź, czy wybrano poprawne zasoby, a następnie kliknij przycisk **Usuń**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Zrzut ekranu przedstawiający okno potwierdzenia usunięcia.":::

> [!WARNING]
> Spowoduje to usunięcie wszelkich niestandardowych słów kluczowych utworzonych za pomocą usuwanych zasobów mowy, a pokaz asystenta głosowego przestanie działać.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy utworzono rozwiązanie do obsługi mowy bez kodu, spróbuj utworzyć [rozwiązanie "bez kodu"](./tutorial-nocode-vision.md) dla platformy Azure Percept.
