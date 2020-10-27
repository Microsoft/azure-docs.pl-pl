---
title: Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń przy użyciu platformy Azure Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak tworzyć niestandardowe reguły analizy w celu wykrywania zagrożeń bezpieczeństwa przy użyciu platformy Azure. Skorzystaj z grupowania zdarzeń i grupowania alertów, a następnie zapoznaj się z wyłączoną autowyłączenie.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: b5cf2b473b6b08dcd77f1a8612d19cea26fc16b9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546759"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Samouczek: Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń

Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md) na platformie Azure, można utworzyć reguły niestandardowe, które mogą wyszukiwać określone kryteria w środowisku i generować zdarzenia, gdy kryteria są dopasowane, aby można było je zbadać. Ten samouczek ułatwia tworzenie reguł niestandardowych w celu wykrywania zagrożeń przy użyciu platformy Azure.

Ten samouczek pomaga wykrywać zagrożenia przy użyciu platformy Azure.
> [!div class="checklist"]
> * Tworzenie reguł analizy
> * Automatyzowanie odpowiedzi na zagrożenia

## <a name="create-custom-analytics-rules"></a>Tworzenie niestandardowych reguł analizy

Można utworzyć niestandardowe reguły analizy, które ułatwią wyszukiwanie typów zagrożeń i anomalii podejrzanych w danym środowisku. Reguła gwarantuje, że od razu otrzymasz powiadomienie, dzięki czemu można klasyfikacja, zbadać i skorygować zagrożenia.

1. W Azure Portal w obszarze wskaźnik platformy Azure wybierz pozycję **Analiza** .

1. Na górnym pasku menu wybierz pozycję **+ Utwórz** i wybierz pozycję **zaplanowana reguła zapytania** . Spowoduje to otwarcie **kreatora reguły analizy** .

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query.png" alt-text="Utwórz zaplanowane zapytanie":::

1. Na karcie **Ogólne** Podaj unikatową **nazwę** i **Opis** . W polu **taktykę** można wybierać spośród kategorii ataków, według których ma zostać sklasyfikowana reguła. W razie potrzeby ustaw **ważność** alertu. Po utworzeniu reguły jego **stan** jest domyślnie **włączony** , co oznacza, że zostanie uruchomiony natychmiast po zakończeniu tworzenia. Jeśli nie chcesz, aby był uruchamiany natychmiast, wybierz pozycję **wyłączone** , a reguła zostanie dodana do karty **aktywne reguły** i możesz ją włączyć z niej, gdy będzie potrzebna.

    ![Rozpocznij tworzenie niestandardowej reguły analizy](media/tutorial-detect-threats-custom/general-tab.png)

1. Na karcie **Ustawianie logiki reguły** można napisać zapytanie bezpośrednio w polu **zapytania reguły** lub utworzyć zapytanie w log Analytics, a następnie skopiować i wkleić je w tym miejscu.
 
   ![Utwórz zapytanie na platformie Azure — wskaźnik](media/tutorial-detect-threats-custom/settings-tab.png)

   - Zobacz **Podgląd wyników** w prawo, gdzie wskaźnik na platformie Azure pokazuje liczbę wyników (zdarzenia dziennika), a zapytanie zostanie wygenerowane i zmienione na bieżąco podczas pisania i konfigurowania zapytania. Wykres pokazuje liczbę wyników w określonym przedziale czasu, która jest określana przez ustawienia w sekcji **planowanie zapytań** .
    - Jeśli zobaczysz, że zapytanie wyzwoli zbyt wiele lub zbyt częste alerty, możesz ustawić linię bazową w sekcji **próg alertu** .

      Oto przykładowe zapytanie, które może wysyłać alerty po utworzeniu nietypowej liczby zasobów w działaniu platformy Azure.

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > - Długość zapytania powinna wynosić od 1 do 10 000 znaków i nie może zawierać znaku "Search \* " ani "Union \* ".
        >
        > - Używanie funkcji ADX do tworzenia zapytań usługi Azure Eksplorator danych w ramach okna zapytania Log Analytics **nie jest obsługiwane** .

    1. Sekcja **Mapuj jednostki** służy do łączenia parametrów z wyników zapytania do jednostek rozpoznanych przez wskaźnik na platformie Azure. Te jednostki stanowią podstawę do dalszej analizy, w tym grupowanie alertów na zdarzenia na karcie **Ustawienia zdarzenia** .
  
    1. W sekcji **planowanie zapytań** ustaw następujące parametry:

       1. Ustaw **Uruchom zapytanie co** , aby kontrolować częstotliwość uruchamiania zapytania — tak często, jak co 5 minut, lub tak rzadko, jak raz dziennie.

       1. Ustaw **dane wyszukiwania od czasu ostatniego** , aby określić okres danych uwzględnionych w zapytaniu — na przykład może wysyłać zapytania do ostatnich 10 minut danych lub ostatnich 6 godzin.

          > [!NOTE]
          > **Interwały zapytań i okres lookback**
          > - Te dwa ustawienia są niezależne od siebie, aż do momentu. Zapytanie można uruchomić w krótkim odstępie czasu, przez okres dłuższy niż interwał (w efekcie mającym nakładające się zapytania), ale nie można uruchomić zapytania w przedziale czasowym, który przekracza okres pokrycia, w przeciwnym razie w ogólnym zapotrzebowaniu na zapytania będą występować przerwy.
          >
          > **Opóźnienie pozyskiwania**
          > - W celu uwzględnienia **opóźnień** , które mogą wystąpić między generowaniem zdarzenia w źródle i jego pozyskaniu na platformie Azure, a w celu zapewnienia pełnego pokrycia bez duplikowania danych, wskaźnik platformy Azure będzie uruchamiać zaplanowane reguły analizy w przypadku **opóźnienia** z zaplanowanym czasem.

    1. Użyj sekcji **próg alertu** , aby zdefiniować linię bazową. Na przykład ustaw **Wygeneruj alert, gdy liczba wyników zapytania** **jest większa niż** i wprowadź liczbę 1000, jeśli chcesz, aby reguła wygenerowała alert tylko wtedy, gdy kwerenda zwróci więcej niż 1000 wyników przy każdym uruchomieniu. To pole jest wymagane, dlatego jeśli nie chcesz ustawiać planu bazowego — czyli jeśli chcesz, aby alert rejestrował każde zdarzenie — wprowadź wartość 0 w polu Liczba.
    
    1. W obszarze **grupowanie zdarzeń** wybierz jeden z dwóch sposobów obsługi grupowania **zdarzeń** w **alertach** : 

       - **Grupuj wszystkie zdarzenia w pojedynczym alercie** (ustawienie domyślne). Reguła generuje pojedynczy alert przy każdym uruchomieniu, pod warunkiem, że zapytanie zwróci więcej wyników niż określony **próg alertu** powyżej. Alert zawiera podsumowanie wszystkich zdarzeń zwróconych w wyniku. 

       - **Wyzwalaj alert dla każdego zdarzenia** . Reguła generuje unikatowy alert dla każdego zdarzenia zwróconego przez zapytanie. Jest to przydatne, jeśli chcesz, aby zdarzenia były wyświetlane indywidualnie, lub jeśli chcesz pogrupować je według określonych parametrów — według użytkownika, nazwy hosta lub innego elementu. Można zdefiniować te parametry w zapytaniu.
    
       Obecnie liczba alertów, które może wygenerować reguła, jest ograniczona do 20. Jeśli w określonej zasadzie **grupowanie zdarzeń** jest ustawione na **wyzwolenie alertu dla każdego zdarzenia** , a zapytanie reguły zwróci więcej niż 20 zdarzeń, każde pierwsze 19 zdarzeń wygeneruje unikatowy alert, a dwudziesty alert podsumowuje cały zestaw zwracanych zdarzeń. Innymi słowy, dwudziesty alert to co zostało wygenerowane w ramach opcji **Grupuj wszystkie zdarzenia do pojedynczego alertu** .

       > [!NOTE]
       > Czym różnią się **zdarzenia** i **alerty** ?
       >
       > - **Zdarzenie** to opis pojedynczego wystąpienia. Na przykład pojedynczy wpis w pliku dziennika może być traktowany jako zdarzenie. W tym kontekście zdarzenie odnosi się do pojedynczego wyniku zwróconego przez zapytanie w regule analizy.
       >
       > - **Alert** to zbiór zdarzeń, które razem są istotne w przypadku bezpieczeństwa punktu widzenia zabezpieczeń. Alert może zawierać pojedyncze zdarzenie, jeśli zdarzenie miało znaczące konsekwencje dla bezpieczeństwa — logowanie administracyjne z obcego kraju poza godzinami pracy, na przykład.
       >
       > - Co to jest **incydenty** ? Wewnętrzna logika wskaźnikowa platformy Azure tworzy **zdarzenia** z **alertów** lub grup alertów. Kolejka incydentów to główne punkty analityków, badanie i korygowanie Klasyfikacja.
       > 
       > Na platformie Azure Wskaźnikowanie pozyskuje zdarzenia pierwotne z niektórych źródeł danych i już przetwarza alerty od innych użytkowników. Ważne jest, aby zwrócić uwagę na to, do której kwestii w dowolnym momencie.

       > [!IMPORTANT]
       > Grupowanie zdarzeń jest obecnie w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. W sekcji **pomijania** można włączyć opcję **Zatrzymaj uruchomienie zapytania po wygenerowaniu alertu** **w** przypadku, gdy otrzymasz alert, chcesz wstrzymać operację tej reguły przez okres czasu przekraczający Interwał zapytania. W przypadku włączenia tej opcji należy ustawić polecenie **Zatrzymaj wykonywanie zapytania przez** czas, w którym zapytanie powinno zostać zatrzymane, do 24 godzin.

1. Na karcie **Ustawienia zdarzenia** możesz określić, czy i w jaki sposób wskaźnik platformy Azure ma przełączać alerty do zdarzeń z możliwością działania. Jeśli ta karta jest poprzednia, wskaźnik myszy platformy Azure utworzy pojedyncze, oddzielne zdarzenie z każdego i każdego alertu. Można zrezygnować z tworzenia zdarzeń lub zgrupować kilka alertów w jednym zdarzeniu, zmieniając ustawienia na tej karcie.

   > [!IMPORTANT]
   > Karta Ustawienia zdarzenia jest obecnie dostępna w publicznej wersji zapoznawczej. Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. W sekcji **Ustawienia zdarzenia** **Tworzenie zdarzeń z alertów wyzwalanych przez tę regułę analizy** jest domyślnie ustawione na wartość **włączone** , co oznacza, że punkt kontrolny platformy Azure utworzy pojedyncze, oddzielne zdarzenie z każdego i każdego alertu wyzwalanego przez regułę.
       - Jeśli nie chcesz, aby ta reguła nie powodowała tworzenia żadnych zdarzeń (na przykład jeśli ta reguła jest tylko do zbierania informacji do dalszej analizy), ustaw dla tej opcji wartość **wyłączone** .

    1. Jeśli w sekcji **grupowanie alertów** ma zostać wygenerowane pojedyncze zdarzenie z grupy do 150 podobnych lub cyklicznych alertów (patrz Uwaga), ustaw **alerty powiązane z grupą, wyzwalane przez tę regułę analizy, w zdarzeniach** do **włączenia** i ustaw następujące parametry.

    - **Ogranicz grupę do alertów utworzonych w ramach wybranego przedziału czasu** : Określ przedział czasu, w którym będą grupowane podobne lub cykliczne alerty. Wszystkie odpowiednie alerty w tym przedziale czasu spowodują zbiorcze wygenerowanie incydentu lub zestawu incydentów (w zależności od ustawień grupowania poniżej). Alerty poza tym przedziałem czasu generują oddzielne zdarzenie lub zestaw zdarzeń.

    - **Grupuj alerty wyzwalane przez tę regułę analizy w ramach pojedynczego zdarzenia przez** : Wybierz podstawę, z której będą grupowane alerty:

        - **Grupuj alerty w pojedynczym zdarzeniu, jeśli wszystkie jednostki pasują** do siebie: alerty są grupowane razem, jeśli współużytkują te same wartości dla każdej mapowanej jednostki (zdefiniowanej na karcie Reguły logiki zasad powyżej). Jest to zalecane ustawienie.

        - **Grupuj wszystkie alerty wyzwalane przez tę regułę w pojedynczym zdarzeniu** : wszystkie alerty wygenerowane przez tę regułę są grupowane razem, nawet jeśli nie mają identycznych wartości.

        - **Grupuj alerty w pojedynczym zdarzeniu, jeśli wybrane jednostki są zgodne** : alerty są grupowane razem, jeśli współużytkują te same wartości dla niektórych mapowanych jednostek (które można wybrać z listy rozwijanej). Możesz chcieć użyć tego ustawienia, jeśli na przykład chcesz utworzyć oddzielne zdarzenia na podstawie źródłowego lub docelowego adresu IP.

    - **Ponownie otwórz zamknięte zdarzenia dopasowane** : Jeśli zdarzenie zostało rozwiązane i zamknięte, a później zostanie wygenerowany inny alert, który powinien należeć do tego zdarzenia, ustaw to ustawienie na **włączone** , jeśli chcesz ponownie otworzyć zamknięte zdarzenie i pozostaw jako **wyłączone** , jeśli chcesz, aby alert utworzył nowe zdarzenie.
    
        > [!NOTE]
        > Do 150 alertów można grupować w jedno zdarzenie. W przypadku wygenerowania więcej niż 150 alertów przez regułę grupującą je w pojedyncze zdarzenie zostanie wygenerowane nowe zdarzenie zawierające te same szczegóły zdarzenia co oryginał, a nadmiarowe alerty zostaną zgrupowane w nowym zdarzeniu.

1. Na karcie **odpowiedzi automatyczne** zaznacz wszystkie elementy PlayBook, które mają być uruchamiane automatycznie po wygenerowaniu alertu przez regułę niestandardową. Aby uzyskać więcej informacji na temat tworzenia i automatyzowania elementy PlayBook, zobacz [reagowanie na zagrożenia](tutorial-respond-threats-playbook.md).

1. Wybierz pozycję **Przejrzyj i Utwórz,** aby przejrzeć wszystkie ustawienia nowej reguły alertu, a następnie wybierz pozycję **Utwórz, aby zainicjować regułę alertu** .
  
1. Po utworzeniu alertu reguła niestandardowa zostanie dodana do tabeli w obszarze **aktywne reguły** . Z tej listy można włączać, wyłączać lub usuwać każdą regułę.

1. Aby wyświetlić wyniki utworzonych reguł alertów, przejdź do strony **incydenty** , na której można klasyfikacja, [zbadać zdarzenia](tutorial-investigate-cases.md)i skorygować zagrożenia.


> [!NOTE]
> Alerty generowane na platformie Azure — wskaźnikiem dostępności są dostępne za pomocą [Microsoft Graph zabezpieczenia](https://aka.ms/securitygraphdocs). Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą alertów zabezpieczeń Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Zaplanowana reguła nie została wykonana lub jest wyświetlana z opcją autowyłączenia dodaną do nazwy

Jest to rzadki przypadek, w którym nie można uruchomić zaplanowanej reguły zapytania, ale może się to zdarzyć. Platforma Azure wskaźnikowego klasyfikuje błędy jako przejściowe lub stałe, na podstawie określonego typu awarii i okoliczności, które doprowadziły do niego.

#### <a name="transient-failure"></a>Błąd przejściowy

Przejściowy błąd występuje z powodu sytuacji, która jest tymczasowa i wkrótce wróci do normalnego, w którym momencie wykonanie reguły powiedzie się. Niektóre przykłady błędów, które klasyfikują wskaźnik platformy Azure jako przejściowe:

- Uruchamianie zapytania reguły trwa zbyt długo i przekracza limit czasu.
- Problemy z łącznością między źródłami danych a Log Analytics lub między Log Analytics i wskaźnikiem kontrolnym platformy Azure.
- Wszelkie inne nowe i nieznane awarie są uznawane za przejściowe.

W przypadku błędu przejściowego wskaźnik platformy Azure próbuje wykonać regułę ponownie po wstępnie określonym i ciągle rosnących odstępach czasu, aż do momentu. Następnie reguła zostanie uruchomiona ponownie tylko w następnym zaplanowanym czasie. Reguła nigdy nie zostanie wyłączona z powodu przejściowego błędu.

#### <a name="permanent-failure---rule-auto-disabled"></a>Trwały błąd — reguła została wyłączona samoobsługowo

Trwały błąd występuje ze względu na zmianę warunków zezwalających na uruchomienie reguły, która bez interwencji człowieka nie powróci do poprzedniego stanu. Poniżej przedstawiono kilka przykładów niepowodzeń, które są klasyfikowane jako trwałe:

- Docelowy obszar roboczy (na którym jest obsługiwana kwerenda reguły) został usunięty.
- Tabela docelowa (na której jest obsługiwana kwerenda reguły) została usunięta.
- Wskaźnik "Azure" został usunięty z docelowego obszaru roboczego.
- Funkcja używana przez zapytanie reguły nie jest już prawidłowa; został on zmodyfikowany lub usunięty.
- Zmieniono uprawnienia do jednego ze źródeł danych zapytania reguły.
- Jedno z źródeł danych zapytania reguły zostało usunięte lub rozłączone.

**W przypadku wstępnie określoną liczbę kolejnych stałych niepowodzeń tego samego typu i tej samej reguły,** Punkt kontrolny platformy Azure nie próbuje wykonać reguły, a także wykonuje następujące czynności:

- Wyłącza regułę.
- Dodaje wyrazy **"automatycznie wyłączone"** na początku nazwy reguły.
- Dodaje przyczynę niepowodzenia (i wyłączenie) do opisu reguły.

Możesz łatwo określić obecność wszystkich reguł, które są wyłączone, sortując listę reguł według nazwy. Reguły autowyłączone będą znajdować się na początku lub w górnej części listy.

Menedżerów SOC powinni mieć pewność, że lista reguł ma być regularnie sprawdzana pod kątem obecności reguł, które są wyłączone.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób rozpoczynania wykrywania zagrożeń przy użyciu platformy Azure.

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, [Skonfiguruj automatyczne reagowanie na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).

