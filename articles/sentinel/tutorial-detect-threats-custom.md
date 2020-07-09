---
title: Tworzenie niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń za pomocą platformy Azure. Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak tworzyć niestandardowe reguły analityczne w celu wykrywania podejrzanych zagrożeń przy użyciu platformy Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77585111"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Samouczek: Tworzenie niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń

Po [podłączeniu źródeł danych](quickstart-onboard.md)   do usługi Azure wskaźnikowej można utworzyć niestandardowe reguły, które mogą wyszukiwać określone kryteria w środowisku i generować zdarzenia, gdy kryteria są dopasowane, aby można było je zbadać. Ten samouczek ułatwia tworzenie reguł niestandardowych w celu wykrywania zagrożeń przy użyciu platformy Azure.

Ten samouczek pomaga wykrywać zagrożenia przy użyciu platformy Azure.
> [!div class="checklist"]
> * Tworzenie reguł analitycznych
> * Automatyzowanie odpowiedzi na zagrożenia

## <a name="create-custom-analytic-rules"></a>Tworzenie niestandardowych reguł analitycznych

Można utworzyć niestandardowe reguły analityczne ułatwiające wyszukiwanie typów zagrożeń i anomalii podejrzanych w danym środowisku. Reguła gwarantuje, że od razu otrzymasz powiadomienie, dzięki czemu można klasyfikacja, zbadać i skorygować zagrożenia.

1. W Azure Portal w obszarze wskaźnik platformy Azure wybierz pozycję **Analiza**.

1. Na górnym pasku menu wybierz pozycję **+ Utwórz** i wybierz pozycję **zaplanowana reguła zapytania**. Spowoduje to otwarcie **kreatora reguły analizy**.

    ![Utwórz zaplanowane zapytanie](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na karcie **Ogólne** Podaj unikatową **nazwę**i **Opis**. W polu **taktykę** można wybierać spośród kategorii ataków, według których ma zostać sklasyfikowana reguła. W razie potrzeby ustaw **ważność** alertu. Po utworzeniu reguły jego **stan** jest domyślnie **włączony** , co oznacza, że zostanie uruchomiony natychmiast po zakończeniu tworzenia. Jeśli nie chcesz, aby był uruchamiany natychmiast, wybierz pozycję **wyłączone**, a reguła zostanie dodana do karty **aktywne reguły** i możesz ją włączyć z niej, gdy będzie potrzebna.

    ![Rozpocznij tworzenie niestandardowej reguły analitycznej](media/tutorial-detect-threats-custom/general-tab.png)

1. Na karcie **Ustawianie logiki reguły** można napisać zapytanie bezpośrednio w polu **zapytania reguły** lub utworzyć zapytanie w log Analytics, a następnie skopiować i wkleić je w tym miejscu.
 
   ![Utwórz zapytanie na platformie Azure — wskaźnik](media/tutorial-detect-threats-custom/settings-tab.png)

   - Zobacz **Podgląd wyników** w prawo, gdzie wskaźnik na platformie Azure pokazuje liczbę wyników (zdarzenia dziennika), a zapytanie zostanie wygenerowane i zmienione na bieżąco podczas pisania i konfigurowania zapytania. Wykres pokazuje liczbę wyników w określonym przedziale czasu, która jest określana przez ustawienia w sekcji **planowanie zapytań** .
    - Jeśli zobaczysz, że zapytanie wyzwoli zbyt wiele lub zbyt częste alerty, możesz ustawić linię bazową w sekcji **próg alertu** .

      Oto przykładowe zapytanie, które może wysyłać alerty po utworzeniu nietypowej liczby zasobów w działaniu platformy Azure.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > Długość zapytania powinna wynosić od 1 do 10 000 znaków i nie może zawierać znaku "Search \* " ani "Union \* ".

    1. Sekcja **Mapuj jednostki** służy do łączenia parametrów z wyników zapytania do jednostek rozpoznanych przez wskaźnik na platformie Azure. Te jednostki stanowią podstawę do dalszej analizy, w tym grupowanie alertów na zdarzenia na karcie **Ustawienia zdarzenia** .
    1. W sekcji **planowanie zapytań** ustaw następujące parametry:

       1. Ustaw **Uruchom zapytanie co** , aby kontrolować częstotliwość uruchamiania zapytania — tak często, jak co 5 minut, lub tak rzadko, jak raz dziennie.

       1. Ustaw **dane wyszukiwania od czasu ostatniego** , aby określić okres danych uwzględnionych w zapytaniu — na przykład może wysyłać zapytania do ostatnich 10 minut danych lub ostatnich 6 godzin.

       > [!NOTE]
       > Te dwa ustawienia są niezależne od siebie, aż do momentu. Zapytanie można uruchomić w krótkim odstępie czasu, przez okres dłuższy niż interwał (w efekcie mającym nakładające się zapytania), ale nie można uruchomić zapytania w przedziale czasowym, który przekracza okres pokrycia, w przeciwnym razie w ogólnym zapotrzebowaniu na zapytania będą występować przerwy.

    1. Użyj sekcji **próg alertu** , aby zdefiniować linię bazową. Na przykład ustaw **Wygeneruj alert, gdy liczba wyników zapytania** **jest większa niż** i wprowadź liczbę 1000, jeśli chcesz, aby reguła wygenerowała alert tylko wtedy, gdy kwerenda generuje więcej niż 1000 wyników przy każdym uruchomieniu. Ponieważ to pole jest wymagane, jeśli nie chcesz ustawiać planu bazowego — czyli jeśli chcesz, aby alert rejestrował każde zdarzenie — wprowadź wartość 0 w polu Liczba.

    1. W sekcji **pomijania** można włączyć opcję **Zatrzymaj uruchomienie zapytania po wygenerowaniu alertu** **w** przypadku, gdy otrzymasz alert, chcesz wstrzymać operację tej reguły przez okres czasu przekraczający Interwał zapytania. W przypadku włączenia tej opcji należy ustawić polecenie **Zatrzymaj wykonywanie zapytania przez** czas, w którym zapytanie powinno zostać zatrzymane, do 24 godzin.

1. Na karcie **Ustawienia zdarzenia** możesz określić, czy i w jaki sposób wskaźnik platformy Azure ma przełączać alerty do zdarzeń z możliwością działania. Jeśli ta karta jest poprzednia, wskaźnik myszy platformy Azure utworzy pojedyncze, oddzielne zdarzenie z każdego i każdego alertu. Można zrezygnować z tworzenia zdarzeń lub zgrupować kilka alertów w jednym zdarzeniu, zmieniając ustawienia na tej karcie.

    1. W sekcji **Ustawienia zdarzenia** **Tworzenie zdarzeń z alertów wyzwalanych przez tę regułę analizy** jest domyślnie ustawione na wartość **włączone**, co oznacza, że punkt kontrolny platformy Azure utworzy pojedyncze, oddzielne zdarzenie z każdego i każdego alertu wyzwalanego przez regułę.<br></br>Jeśli nie chcesz, aby ta reguła nie powodowała tworzenia żadnych zdarzeń (na przykład jeśli ta reguła jest tylko do zbierania informacji do dalszej analizy), ustaw dla tej opcji wartość **wyłączone**.

    1. Jeśli w sekcji **grupowanie alertów** ma być generowane pojedyncze zdarzenie z grupy podobnych lub cyklicznych alertów, należy ustawić **alerty powiązane z grupą, wyzwalane przez tę regułę analizy, w zdarzeniach** do **włączenia**i ustawić następujące parametry.

    1. **Ogranicz grupę do alertów utworzonych w ramach wybranego przedziału czasu**:<br></br> Określ przedział czasu, w którym będą grupowane podobne lub cykliczne alerty. Wszystkie odpowiednie alerty w tym przedziale czasu spowodują zbiorcze wygenerowanie incydentu lub zestawu incydentów (w zależności od ustawień grupowania poniżej). Alerty poza tym przedziałem czasu generują oddzielne zdarzenie lub zestaw zdarzeń.

    2. **Grupuj alerty wyzwalane przez tę regułę analizy w ramach pojedynczego zdarzenia przez**: Wybierz podstawę, z której będą grupowane alerty:

        - **Grupuj alerty w pojedynczym zdarzeniu, jeśli wszystkie jednostki pasują do siebie**: <br></br>Alerty są pogrupowane w przypadku, gdy współużytkują identyczne wartości dla każdej z mapowanych jednostek (zdefiniowane na karcie zasady definiowania logiki powyżej). Jest to zalecane ustawienie.

        - **Grupuj wszystkie alerty wyzwalane przez tę regułę w pojedynczym zdarzeniu**: <br></br>Wszystkie alerty wygenerowane przez tę regułę są grupowane razem, nawet jeśli nie mają identycznych wartości.

        - **Grupuj alerty w pojedynczym zdarzeniu, jeśli wybrane jednostki są zgodne**: <br></br>Alerty są pogrupowane w przypadku udostępniania identycznych wartości dla niektórych mapowanych jednostek (które można wybrać z listy rozwijanej). Możesz chcieć użyć tego ustawienia, jeśli na przykład chcesz utworzyć oddzielne zdarzenia na podstawie źródłowego lub docelowego adresu IP.

    3. **Ponownie otwórz zamknięte zdarzenia dopasowane**: Jeśli zdarzenie zostało zamknięte (oznacza to, że problem został rozwiązany), a następnie zostanie wygenerowany inny alert, który został pogrupowany w tym zdarzeniu, ustaw to ustawienie na **włączone** , jeśli chcesz ponownie otworzyć zamknięte zdarzenie i pozostaw je **wyłączone** , jeśli chcesz, aby alert utworzył nowe zdarzenie.

1. Na karcie **odpowiedzi automatyczne** zaznacz wszystkie elementy PlayBook, które mają być uruchamiane automatycznie po wygenerowaniu alertu przez regułę niestandardową. Aby uzyskać więcej informacji na temat tworzenia i automatyzowania elementy PlayBook, zobacz [reagowanie na zagrożenia](tutorial-respond-threats-playbook.md).

1. Wybierz pozycję **Przejrzyj i Utwórz,** aby przejrzeć wszystkie ustawienia nowej reguły alertu, a następnie wybierz pozycję **Utwórz, aby zainicjować regułę alertu**.
  
1. Po utworzeniu alertu reguła niestandardowa zostanie dodana do tabeli w obszarze **aktywne reguły**. Z tej listy można włączać, wyłączać lub usuwać każdą regułę.

1. Aby wyświetlić wyniki utworzonych reguł alertów, przejdź do strony **incydenty** , na której można klasyfikacja, [zbadać zdarzenia](tutorial-investigate-cases.md)i skorygować zagrożenia.


> [!NOTE]
> Alerty generowane na platformie Azure — wskaźnikiem dostępności są dostępne za pomocą [Microsoft Graph zabezpieczenia](https://aka.ms/securitygraphdocs). Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą alertów zabezpieczeń Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób rozpoczynania wykrywania zagrożeń przy użyciu platformy Azure.

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, [Skonfiguruj automatyczne reagowanie na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).

