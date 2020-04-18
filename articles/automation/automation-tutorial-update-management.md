---
title: Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure
description: Ten artykuł zawiera omówienie sposobu używania usługi Azure Automation Update Management do zarządzania aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure i maszyn wirtualnych innych niż Azure.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 62c661f75aef77117a61be7e802562e6dde17ba5
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604671"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Zarządzanie aktualizacjami i poprawkami dla maszyn wirtualnych platformy Azure

Przy użyciu rozwiązania Update Management można zarządzać aktualizacjami i poprawkami dla maszyn wirtualnych. Z tym samouczku dowiesz się, jak szybko ocenić stan dostępnych aktualizacji, zaplanować instalację wymaganych aktualizacji, przejrzeć wyniki wdrożenia oraz utworzyć alert sprawdzający, czy aktualizacje zostały pomyślnie zastosowane.

Aby uzyskać informacje o cenach, zobacz [cennik usługi Automation dla rozwiązania Update Management](https://azure.microsoft.com/pricing/details/automation/).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie oceny aktualizacji
> * Konfigurowanie alertów
> * Planowanie wdrożenia aktualizacji
> * Wyświetlanie wyników wdrożenia

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Rozwiązanie [do zarządzania aktualizacjami](automation-update-management.md) włączone dla co najmniej jednej maszyny wirtualnej.
* [Maszyna wirtualna](../virtual-machines/windows/quick-create-portal.md) do dołączenia.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="view-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu zarządzania aktualizacjami zostanie otwarta strona zarządzania aktualizacjami. Jeśli aktualizacje są identyfikowane jako brakujące, lista brakujących aktualizacji jest wyświetlana na karcie **Brakujące aktualizacje.**

W obszarze **Łącze Informacje**wybierz łącze aktualizacji, aby otworzyć artykuł pomocy technicznej dla aktualizacji. Możesz dowiedzieć się ważnych informacji o aktualizacji.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kliknij w dowolnym miejscu aktualizacji, aby otworzyć okienko wyszukiwania dziennika dla wybranej aktualizacji. Zapytanie dotyczące przeszukiwania dzienników jest wstępnie zdefiniowane dla tej określonej aktualizacji. Można zmodyfikować tę kwerendę lub utworzyć własną kwerendę, aby wyświetlić szczegółowe informacje o aktualizacjach wdrożonych lub brakujących w środowisku.

![Wyświetlanie stanu aktualizacji](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Konfigurowanie alertów

W tym kroku dowiesz się, jak skonfigurować alert powiadamiający o stanie wdrożenia aktualizacji.

### <a name="alert-conditions"></a>Warunki alertu

Na koncie automatyzacji przejdź do **witryny Alerty** w obszarze **Monitorowanie**, a następnie kliknij pozycję **Nowa reguła alertu**.

Twoje konto automatyzacji jest już wybrane jako zasób. Jeśli chcesz go zmienić, kliknij przycisk **Wybierz**. Na stronie Wybieranie zasobu wybierz polecenie **Konta automatyzacji** z menu rozwijanego **Filtruj według typu zasobu.** Wybierz konto automatyzacji, a następnie kliknij przycisk **Gotowe**.

Kliknij pozycję **Dodaj warunek**, aby wybrać odpowiedni sygnał dla wdrożenia aktualizacji. W poniższej tabeli przedstawiono szczegóły dwóch dostępnych sygnałów.

|Nazwa sygnału|Wymiary|Opis|
|---|---|---|
|`Total Update Deployment Runs`|— Nazwa wdrażania aktualizacji<br>— Stan|Ten sygnał alerty o ogólnym stanie wdrożenia aktualizacji.|
|`Total Update Deployment Machine Runs`|— Nazwa wdrażania aktualizacji</br>— Stan</br>— Komputer docelowy</br>- Aktualizacja identyfikatora przebiegu wdrożenia|Ten sygnał alarmuje o stanie wdrożenia aktualizacji przeznaczonych dla określonych komputerów.|

W przypadku wymiaru wybierz prawidłową wartość z listy. Jeśli żądanej wartości nie ma na liście, kliknij **\+** znak obok wymiaru i wpisz nazwę niestandardową. Następnie wybierz wartość, która ma być wyszukyna. Jeśli chcesz wybrać wszystkie wartości dla wymiaru, kliknij przycisk **Wybierz. \* ** Jeśli nie wybierzesz wartości dla wymiaru, zarządzanie aktualizacjami ignoruje ten wymiar.

![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/signal-logic.png)

W obszarze **Logika alertu** w polu **Próg** wprowadź **1**. Po zakończeniu wybierz pozycję **Gotowe**.

### <a name="alert-details"></a>Szczegóły alertu

Poniżej **2. Zdefiniuj szczegóły alertu**, wprowadź nazwę i opis alertu. Ustaw opcję **Ważność** na **Informacyjny (ważność 2)** w przypadku pomyślnego przebiegu lub **Informacyjny (ważność 1)** w przypadku nieudanego przebiegu.

![Konfigurowanie logiki sygnału](./media/automation-tutorial-update-management/define-alert-details.png)

W obszarze **Grupy akcji**wybierz pozycję **Utwórz nowy**. Grupa akcji to grupa składająca się z akcji, których można używać w wielu alertach. Akcje mogą obejmować powiadomienia e-mail, programy runbook, elementów webhook i wiele innych. Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi](../azure-monitor/platform/action-groups.md).

W polu **Nazwa grupy Akcja** wprowadź nazwę alertu i krótką nazwę. Usługa Update Management używa krótkiej nazwy zamiast pełnej nazwy grupy akcji podczas wysyłania powiadomień przy użyciu określonej grupy.

W obszarze **Akcje**wprowadź nazwę akcji, na przykład **Powiadomienie e-mail**. W przypadku **typu akcji**wybierz **pozycję E-mail/SMS/Push/Voice**. Aby uzyskać **szczegółowe informacje,** wybierz pozycję **Edytuj szczegóły**.

W okienku E-mail/SMS/Push/Głos wprowadź nazwę. Zaznacz pole wyboru **Poczta e-mail,** a następnie wprowadź prawidłowy adres e-mail.

![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/configure-email-action-group.png)

W okienku Poczta e-mail/SMS/Wypychanie/Głos kliknij przycisk **OK**. W okienku Dodawanie grupy akcji kliknij przycisk **OK**.

Aby dostosować temat wiadomości e-mail z alertem, w obszarze **Utwórz regułę**, w obszarze **Dostosowywanie akcji**wybierz pozycję **Temat wiadomości e-mail**. Po zakończeniu wybierz pozycję **Utwórz regułę alertu**. Alert informuje, kiedy wdrożenie aktualizacji zakończy się pomyślnie i które maszyny były częścią uruchomienia wdrożenia aktualizacji.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Następnie zaplanuj wdrożenie zgodnie z harmonogramem wydawania i oknem obsługi, aby zainstalować aktualizacje. Można wybrać typy aktualizacji, które mają być uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

>[!NOTE]
>Planowanie wdrożenia aktualizacji tworzy zasób [harmonogramu](shared-resources/schedules.md) połączony z elementem runbook **Patch-MicrosoftOMSComputers,** który obsługuje wdrożenie aktualizacji na komputerach docelowych. Jeśli usuniesz zasób harmonogramu z witryny Azure portal lub przy użyciu programu PowerShell po utworzeniu wdrożenia, usunięcie przerywa zaplanowane wdrożenie aktualizacji i przedstawia błąd podczas próby ponownego skonfigurowania zasobu harmonogramu z portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.  

Aby zaplanować nowe wdrożenie aktualizacji dla maszyny wirtualnej, przejdź do rozwiązania **Update Management**, a następnie wybierz pozycję **Zaplanuj wdrażanie aktualizacji**.

W obszarze **Nowe wdrożenie aktualizacji** podaj następujące informacje:

* **Nazwa**: wprowadź unikatową nazwę wdrożenia aktualizacji.

* **System operacyjny**: wybierz docelowy system operacyjny do wdrażania aktualizacji.

* **Grupy do aktualizacji (wersja zapoznawcza)**: Zdefiniuj kwerendę, która łączy subskrypcję, grupy zasobów, lokalizacje i tagi, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure do uwzględnienia we wdrożeniu. Aby dowiedzieć się więcej, zobacz [Grupy dynamiczne](automation-update-management-groups.md).

* **Maszyny do aktualizacji:** Wybierz zapisane wyszukiwanie, Zaimportowana grupa lub wybierz **opcję Maszyny** z menu rozwijanego i wybierz poszczególne maszyny. Jeśli wybierzesz **Machine**, gotowość każdego komputera jest wyświetlana w **aktualizacji agenta gotowości** kolumny. Aby dowiedzieć się więcej o różnych metodach tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Grupy komputerów w dziennikach usługi Azure Monitor](../azure-monitor/platform/computer-groups.md).

* **Klasyfikacja aktualizacji:** Dla każdego produktu usuń zaznaczenie wszystkich obsługiwanych klasyfikacji aktualizacji, ale tych, które mają być uwzględnione we wdrożeniu aktualizacji. W tym samouczku pozostaw wszystkie typy wybrane dla wszystkich produktów.

  Dostępne są następujące typy klasyfikacji:

   |System operacyjny  |Typ  |
   |---------|---------|
   |Windows     | Aktualizacje krytyczne</br>Aktualizacje zabezpieczeń</br>Pakiety zbiorcze aktualizacji</br>Pakiety funkcji</br>Dodatki Service Pack</br>Aktualizacje definicji</br>narzędzia</br>Aktualizacje<br>Sterownik        |
   |Linux     | Aktualizacje krytyczne i zabezpieczeń</br>Inne aktualizacje       |

   Aby uzyskać opisy typów klasyfikacji, zobacz [Aktualizowanie klasyfikacji](automation-view-update-assessments.md#update-classifications).

* **Aktualizacje do uwzględnienia/wykluczenia** — otwiera stronę Dołącz/Wyklucz. Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na oddzielnych kartach, określając numery identyfikatorów artykułu KB. Określając jeden lub więcej numerów identyfikatorów, należy usunąć lub odznaczyć wszystkie klasyfikacje z wdrożeniem aktualizacji. Gwarantuje to, że żadne inne aktualizacje nie są zawarte w pakiecie aktualizacji podczas określania identyfikatorów aktualizacji.

> [!NOTE]
> Ważne jest, aby wiedzieć, że wykluczenia zastępują inkluzje. Na przykład, jeśli zdefiniujesz regułę wykluczenia `*`, Zarządzanie aktualizacjami nie instaluje żadnych poprawek ani pakietów, ponieważ wszystkie są wykluczone. Wykluczone poprawki nadal są wyświetlane jako brakujące w maszynie. W przypadku komputerów z systemem Linux jeśli zostanie dołączyć pakiet, który ma pakiet zależny, który został wykluczony, zarządzanie aktualizacjami nie instaluje pakietu głównego.

> [!NOTE]
> Nie można określić aktualizacje, które zostały zastąpione do włączenia z wdrożenia aktualizacji.
>

* **Ustawienia harmonogramu**: spowoduje otwarcie okienka Ustawienia harmonogramu. Domyślny czas rozpoczęcia to 30 minut po bieżącej godzinie. Czas rozpoczęcia można ustawić na dowolny czas od 10 minut w przyszłości.

   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z ustawionym harmonogramem cyklicznym. W obszarze **Cykl** wybierz pozycję **Raz**. Pozostaw wartość domyślną jako 1 dzień i kliknij przycisk **OK**. Te wpisy konfigurują harmonogram cykliczny.

* **Skrypty wstępne i końcowe**: wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz [Zarządzanie skryptami wstępnymi i końcowymi](pre-post-scripts.md).

* **Okno konserwacji (w minutach)**: pozostaw wartość domyślną. Systemy konserwacji kontrolują czas instalacji aktualizacji. Podczas określania okna konserwacji należy wziąć pod uwagę następujące szczegóły:

  * Obsługa systemu Windows określa liczbę zainstalowanych aktualizacji.
  * Zarządzanie aktualizacjami nie zatrzymuje instalowania nowych aktualizacji, jeśli zbliża się koniec okna konserwacji.
  * Zarządzanie aktualizacjami nie kończy aktualizacji w toku, jeśli okno konserwacji zostanie przekroczone.
  * Jeśli okno konserwacji zostanie przekroczone w systemie Windows, często jest to spowodowane tym, że zainstalowanie aktualizacji dodatku Service Pack zajmuje dużo czasu.

  > [!NOTE]
  > Aby uniknąć stosowania aktualizacji poza oknem konserwacji na Ubuntu, ponownie skonfiguruj pakiet unattended-upgrade, aby wyłączyć aktualizacje automatyczne. Aby uzyskać informacje dotyczące konfigurowania pakietu, zobacz [temat Aktualizacje automatyczne w Przewodniku po serwerze Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Opcje ponownego uruchamiania:** Służy do określania opcji obsługi ponownych rozruchów. Dostępne są następujące opcje:
  * Uruchom ponownie, jeśli to konieczne (domyślnie)
  * Zawsze uruchamiaj ponownie
  * Nigdy nie uruchamiaj ponownie
  * Tylko ponowne uruchomienie - nie instaluje aktualizacji

> [!NOTE]
> Klucze rejestru wymienione w obszarze [Klucze rejestru używane do zarządzania ponownym uruchomieniem](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) mogą spowodować zdarzenie ponownego **uruchomienia,** jeśli opcje ponownego uruchomienia są ustawione na **Nigdy nie uruchamiaj ponownie.**

Po zakończeniu konfigurowania harmonogramu kliknij przycisk **Utwórz**.

![Okienko ustawień harmonogramu aktualizacji](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Nastąpi powrót do pulpitu nawigacyjnego stanu. Wybierz **zaplanowane wdrożenia aktualizacji,** aby wyświetlić harmonogram wdrażania właśnie utworzony.

> [!NOTE]
> Usługa Zarządzania aktualizacjami obsługuje wdrażanie aktualizacji innych firm i wstępne pobieranie poprawek. Ta obsługa wymaga zmian w systemach, które są załatane. Zobacz [Pomoc techniczna w pierwszej partii i przed pobraniem,](automation-configure-windows-update.md) aby dowiedzieć się, jak skonfigurować te ustawienia w systemach.

Można również tworzyć wdrożenia aktualizacji programowo. Aby dowiedzieć się, jak utworzyć wdrożenie aktualizacji za pomocą interfejsu API REST, zobacz [Konfiguracje aktualizacji oprogramowania — Tworzenie](/rest/api/automation/softwareupdateconfigurations/create). Istnieje również przykładowy system runbook, którego można użyć do utworzenia wdrożenia aktualizacji tygodniowej. Aby dowiedzieć się więcej o tym zestawieniu, zobacz [Tworzenie cotygodniowego wdrożenia aktualizacji dla co najmniej jednej maszyny wirtualnej w grupie zasobów](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** w obszarze rozwiązania **Update Management**. Wyświetlany stan **W toku** oznacza, że wdrożenie jest aktualnie uruchomione. Po pomyślnym zakończeniu wdrażania stan zmieni się na **Pomyślnie**. Jeśli występują błędy z jedną lub kilkoma aktualizacjami we wdrożeniu, stan jest **częściowo nie powiódł się**.

Wybierz ukończone wdrożenie aktualizacji, aby wyświetlić jego pulpit nawigacyjny.

![Pulpit nawigacyjny stanu wdrożenia aktualizacji dla określonego wdrożenia](./media/automation-tutorial-update-management/manageupdates-view-results.png)

Obszar **Wyniki aktualizacji** zawiera podsumowanie z łączną liczbą aktualizacji i wynikami wdrożenia na maszynie wirtualnej. Tabela po prawej stronie pokazuje szczegółowy podział każdej aktualizacji i wyniki instalacji.

Dostępne wartości to:

* **Nie podjęto próby:** aktualizacja nie została zainstalowana, ponieważ nie było wystarczającego czasu, na podstawie zdefiniowanego czasu trwania okna konserwacji.
* **Powodzenie**: aktualizacja powiodła się.
* **Niepowodzenie**: aktualizacja nie powiodła się.

Wybierz **wszystkie dzienniki,** aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie.

Wybierz kafelek **Dane wyjściowe**, aby wyświetlić strumień zadań elementu runbook odpowiedzialnego za zarządzanie wdrożeniem aktualizacji na docelowej maszynie wirtualnej.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

Po pomyślnym zakończeniu wdrażania aktualizacji otrzymasz potwierdzoną wiadomość e-mail podobną do następującej:

![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dołączanie maszyny wirtualnej dla rozwiązania Update Management
> * Wyświetlanie oceny aktualizacji
> * Konfigurowanie alertów
> * Planowanie wdrożenia aktualizacji
> * Wyświetlanie wyników wdrożenia

Kontynuowanie omawiania rozwiązania Update Management.

> [!div class="nextstepaction"]
> [Update Management solution](automation-update-management.md) (Rozwiązanie Update Management)