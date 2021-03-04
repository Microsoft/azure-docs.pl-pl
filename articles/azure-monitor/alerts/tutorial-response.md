---
title: Odpowiadanie na zdarzenia przy użyciu alertów usługi Azure Log Analytics | Microsoft Docs
description: Ten samouczek pomaga w zrozumieniu alertów w usłudze Log Analytics, które pomagają identyfikować ważne informacje w obszarze roboczym oraz aktywnie powiadamiają użytkownika o problemach lub wywołują akcje w celu ich skorygowania.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/05/2018
ms.custom: mvc
ms.openlocfilehash: 2564711fce6e8578829fb15fdbc04ae2b9d64959
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039431"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Odpowiadanie na zdarzenia przy użyciu alertów platformy Azure Monitor
Alerty platformy Azure Monitor mogą identyfikować ważne informacje w repozytorium usługi Log Analytics. Są one tworzone przy użyciu reguł alertów, które automatycznie uruchamiają przeszukiwanie dzienników w regularnych odstępach czasu. Jeśli wyniki przeszukiwania dzienników pasują do danych kryteriów, zostaje utworzony rekord alertu. Ponadto można go skonfigurować tak, aby generował automatyczną odpowiedź.  Ten samouczek jest kontynuacją samouczka [Tworzenie i udostępnianie pulpitów nawigacyjnych z danymi usługi Log Analytics](../visualize/tutorial-logs-dashboards.md).   

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie reguły alertu
> * Konfigurowanie grupy akcji w celu wysyłania powiadomień e-mail

Do utworzenia przykładu przedstawionego w tym samouczku potrzebna jest istniejąca maszyna wirtualna [połączona z obszarem roboczym usługi Log Analytics](../vm/quick-collect-azurevm.md).  

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-alerts"></a>Tworzenie alertów
Alerty są tworzone na podstawie reguł alertów na platformie Azure Monitor i mogą automatycznie uruchamiać zapisane zapytania lub niestandardowe wyszukiwania dziennika w regularnych odstępach czasu.  Możesz utworzyć alerty w oparciu o konkretne metryki wydajności lub w momencie, w którym zostają utworzone określone zdarzenia, w przypadku braku zdarzenia, a także w sytuacji, w której wiele zdarzeń zostaje utworzonych w danym przedziale czasu.  Na przykład alertów można używać do powiadamiania użytkownika, gdy średnie użycie procesora CPU przekroczy określony próg, gdy zostanie wykryty brak aktualizacji lub gdy zostanie wygenerowane zdarzenie po wykryciu, że określona usługa systemu Windows lub demon systemu Linux nie są uruchomione.  Jeśli wyniki przeszukiwania dzienników pasują do określonych kryteriów, zostaje utworzony alert. Następnie reguła może automatycznie uruchomić jedną lub kilka akcji, np. powiadomić użytkownika o alercie lub wywołać inny proces. 

W poniższym przykładzie tworzona jest reguła alertu pomiaru metryki na podstawie zapytania *Maszyny wirtualne platformy Azure — użycie procesora* znajdującego się w [samouczku Wizualizacja danych](../visualize/tutorial-logs-dashboards.md).  Alert jest tworzony dla każdej maszyny wirtualnej, która przekracza próg 90%.  

1. W Azure Portal kliknij pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz pozycję **Log Analytics**.
2. W okienku po lewej stronie wybierz pozycję **Alerty**, a następnie kliknij przycisk **Nowa reguła alertu** w górnej części strony, aby utworzyć nowy alert.<br><br> ![Tworzenie nowej reguły alertu](./media/tutorial-response/alert-rule-02.png)<br>
3. W pierwszym kroku w sekcji **Utwórz alert** wybierzesz jako zasób obszar roboczy usługi Log Analytics, ponieważ jest to sygnał alertu oparty na dzienniku.  Filtruj wyniki: jeśli masz więcej subskrypcji, wybierz z listy rozwijanej **Subskrypcję** zawierającą maszynę wirtualną oraz obszar roboczy usługi Log Analytics, które zostały utworzone wcześniej.  Filtruj **Typ zasobu**, wybierając z listy rozwijanej pozycję **Log Analytics**.  Na końcu wybierz kolejno opcje **Zasób** **DefaultLAWorkspace**, a następnie kliknij przycisk **Gotowe**.<br><br> ![Zadanie tworzenia alertu — krok 1](./media/tutorial-response/alert-rule-03.png)<br>
4. W sekcji **Kryteria alertu** kliknij przycisk **Dodaj kryteria**, aby wybrać zapisane zapytanie, a następnie podaj logikę, zgodnie z którą działa reguła alertu.  W okienku **Konfigurowanie logiki sygnału** wybierz z listy pozycję *Maszyny wirtualne platformy Azure — użycie procesora*.  Okienko jest aktualizowane i zawiera ustawienia konfiguracji dla alertu.  W górnej części znajdują się wyniki z ostatnich 30 minut dla wybranego sygnału i samo zapytanie wyszukiwania.  
5. Skonfiguruj alert przy użyciu następujących informacji:  
   a. Z listy rozwijanej **na podstawie** wybierz pozycję **pomiar metryki**.  Pomiar metryki utworzy alert dla każdego obiektu w zapytaniu z wartością, która przekracza określony próg.  
   b. W polu **Warunek** wybierz wartość **Większe niż**, a następnie wprowadź **90** w polu **Próg**.  
   c. W sekcji Wyzwalaj alert w oparciu o wybierz opcję **Kolejne naruszenia**, z listy rozwijanej wybierz opcję **Większe niż** i wprowadź wartość 3.  
   d. W sekcji Ocena w oparciu o zmień wartość pozycji **Okres** na **30** minut. Reguła będzie uruchamiana co pięć minut i będzie zwracać rekordy utworzone w ciągu ostatnich 30 minut od aktualnej godziny.  Ustawienie okresu na szerszy zakres może spowodować potencjalne opóźnienie danych i gwarantuje, że zapytanie zwróci dane w celu uniknięcia fałszywie negatywnego wyniku, jeśli alert nigdy nie został uruchomiony.  
6. Kliknij przycisk **Gotowe**, aby zakończyć tworzenie reguły alertu.<br><br> ![Konfigurowanie sygnału alertu](./media/tutorial-response/alert-signal-logic-02.png)<br> 
7. Teraz przejdź do drugiego kroku: podaj nazwę alertu w polu **Nazwa reguły alertu**, np. **Procent procesora przekracza 90 procent**.  Wpisz **Opis**, podając szczegóły alertu, a następnie wybierz z dostępnych opcji pozycję **Critical(Sev 0)** (Krytyczny (ważność 0)) jako wartość pola **Ważność**.<br><br> ![Konfigurowanie szczegółów alertu](./media/tutorial-response/alert-signal-logic-04.png)<br>
8. Aby aktywować regułę alertu od razu po utworzeniu, zaakceptuj wartość domyślną w polu **Włącz regułę po utworzeniu**.
9. W trzecim i ostatnim kroku określ **Grupę akcji**, która gwarantuje, że za każdym razem, gdy zostaje wywołany alert, wykonywane są te same akcje, i która może być używana dla każdej zdefiniowanej reguły.  Skonfiguruj nową grupę akcji, wprowadzając następujące informacje:  
   a. Wybierz pozycję **Nowa grupa akcji**, zostanie wyświetlone okienko **Dodaj grupę akcji**.  
   b. W polu **Nazwa grupy akcji** wpisz nazwę, np. **Dział operacji IT — powiadom**, a w polu **Krótka nazwa** wpisz skrót, np. **itops-n**.  
   c. Sprawdź, czy wartości domyślne w polach **Subskrypcja** i **Grupa zasobów** są poprawne. Jeśli nie, wybierz je z listy rozwijanej.   
   d. W sekcji Akcje wpisz nazwę akcji, np. **Wyślij wiadomość e-mail**, a w obszarze **Typ akcji** wybierz z listy rozwijanej pozycję **Wiadomość e-mail/SMS/wypychana/głosowa**. Po prawej stronie zostanie otwarte okienko właściwości **Wiadomość e-mail/SMS/wypychana/głosowa**, w którym podać dodatkowe informacje.  
   e. W okienku **Wiadomość e-mail/SMS/wypychana/głosowa** włącz opcję **E-mail** i podaj prawidłowy adres e-mail SMTP, na który mają być wysyłane wiadomości.  
   f. Kliknij przycisk **OK**, aby zapisać zmiany.<br><br> 

    ![Tworzenie nowej grupy akcji](./media/tutorial-response/action-group-properties-01.png)

10. Kliknij pozycję **OK**, aby ukończyć konfigurowanie grupy akcji. 
11. Kliknij przycisk **Utwórz regułę alertu**, aby zdefiniować regułę alertu. Rozpoczyna ona działanie od razu.<br><br> ![Zakończenie tworzenia nowej reguły alertu](./media/tutorial-response/alert-rule-01.png)<br> 

## <a name="view-your-alerts-in-azure-portal"></a>Wyświetlanie alertów w witrynie Azure Portal
Po utworzeniu alertu możesz wyświetlać alerty Azure w jednym okienku i zarządzać wszystkimi regułami alertów w subskrypcjach Azure. Jest w nim wyświetlana lista wszystkich reguł alertów (włączonych lub wyłączonych), którą można porządkować na podstawie zasobów docelowych, grup zasobów, nazwy reguły lub stanu. Znajduje się tu również podsumowanie wszystkich wyzwolonych alertów oraz wszystkich skonfigurowanych/włączonych reguł alertów.<br><br> ![Strona stanu alertów platformy Azure](./media/tutorial-response/azure-alerts-02.png)  

Po wyzwoleniu alertu tabela zawiera warunek alertu oraz liczbę jego wystąpień w wybranym okresie (domyślnie w ciągu ostatnich sześciu godzin).  W skrzynce odbiorczej powinna się znajdować odpowiednia wiadomość e-mail zawierająca informacje podobne do tych w poniższym przykładzie: maszynę wirtualną, której alert dotyczy, oraz najważniejsze wyniki pasujące do zapytania wyszukiwania w tym przypadku.<br><br> ![Przykład akcji alertu przy użyciu wiadomości e-mail](./media/tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób aktywnego identyfikowania i reagowania reguł alertów na problemy podczas uruchamiania przeszukiwania dzienników w zaplanowanych odstępach czasu i w przypadku dopasowania do określonych kryteriów.

Użyj tego linku, aby wyświetlić przykłady wstępnie utworzonych skryptów usługi Log Analytics.  

> [!div class="nextstepaction"]
> [Przykłady skryptów usługi Log Analytics](../powershell-samples.md)