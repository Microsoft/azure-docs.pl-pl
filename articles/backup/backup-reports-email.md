---
title: Raporty Azure Backup e-mail
description: Twórz zautomatyzowane zadania, aby otrzymywać raporty okresowe pocztą e-mail
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 8c18d4c7a3c7a9ba343296961fa9a44614366405
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510509"
---
# <a name="email-azure-backup-reports"></a>Raporty Azure Backup e-mail

Za pomocą funkcji **raportów e-mail** dostępnej w raportach usługi Backup można utworzyć automatyczne zadania do otrzymywania raportów okresowych za pośrednictwem poczty e-mail. Ta funkcja działa przez wdrożenie aplikacji logiki w środowisku platformy Azure, która wysyła zapytania do danych z wybranych obszarów roboczych Log Analytics (LA) na podstawie wprowadzonych danych wejściowych. [Dowiedz się więcej o usłudze Logic Apps i ich cenach](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Wprowadzenie

Aby skonfigurować zadania poczty e-mail za pomocą raportów kopii zapasowych, wykonaj następujące czynności:

1.  Przejdź do   >  **raportów kopii zapasowych** centrum kopii zapasowych i kliknij kartę **raport e-mail** .
2.  Utwórz zadanie, określając następujące informacje:
    * **Szczegóły zadania** — nazwa aplikacji logiki, która ma zostać utworzona, oraz subskrypcja, Grupa zasobów i lokalizacja, w której ma zostać utworzona. Należy pamiętać, że aplikacja logiki może wykonywać zapytania dotyczące danych w wielu subskrypcjach, grupach zasobów i lokalizacjach (zgodnie z definicją w sekcji filtry raportu), ale jest tworzona w kontekście pojedynczej subskrypcji, grupy zasobów i lokalizacji.
    * **Dane do eksportu** — karta, która ma zostać wyeksportowana. Można utworzyć pojedynczą aplikację zadania na kartę lub wysłać pocztą e-mail wszystkie karty za pomocą jednego zadania, wybierając opcję **wszystkie karty** .
    * **Opcje poczty e-mail**: częstotliwość wiadomości E-mail, identyfikatory e-mail adresatów i temat wiadomości e-mail.

    ![Karta e-mail](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Po kliknięciu przycisku **Prześlij** i **Potwierdź**, aplikacja logiki zostanie utworzona. Aplikacja logiki i powiązane z nim połączenia interfejsu API są tworzone za pomocą tagu **UsedByBackupReports: true** , aby można było łatwo odnaleźć. Aby pomyślnie uruchomić aplikację logiki, należy wykonać jednorazowy krok autoryzacji, zgodnie z opisem w sekcji poniżej.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Autoryzuj połączenia z dziennikami Azure Monitor i pakietem Office 365

Aplikacja logiki używa łącznika [azuremonitorlogs](https://docs.microsoft.com/connectors/azuremonitorlogs/) do wykonywania zapytań dotyczących obszarów roboczych La i używa łącznika [programu Outlook dla usługi Office 365](https://docs.microsoft.com/connectors/office365connector/) do wysyłania wiadomości e-mail. Należy wykonać jednorazową autoryzację dla tych dwóch łączników. 
 
Aby przeprowadzić autoryzację, wykonaj poniższe kroki:

1.  Przejdź do **Logic Apps** w Azure Portal.
2.  Wyszukaj nazwę utworzonej aplikacji logiki i przejdź do zasobu.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Kliknij element menu **połączenia interfejsu API** .

    ![Połączenia interfejsu API](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Zobaczysz dwa połączenia z formatem `<location>-azuremonitorlogs` i `<location>-office365` -czyli _East-azuremonitorlogs_ i wschodnie do usługi _Office 365_.
5.  Przejdź do każdego z tych połączeń i wybierz element menu **Edytuj połączenie interfejsu API** . Na wyświetlonym ekranie wybierz opcję **Autoryzuj** i Zapisz połączenie po zakończeniu autoryzacji.

    ![Autoryzowanie połączenia](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Aby sprawdzić, czy aplikacja logiki działa po autoryzacji, możesz przejść z powrotem do aplikacji logiki, otworzyć **Przegląd** i wybrać polecenie **Uruchom wyzwalacz** w górnym okienku, aby sprawdzić, czy wiadomość e-mail jest generowana pomyślnie.

## <a name="contents-of-the-email"></a>Zawartość wiadomości e-mail

* Wszystkie wykresy i wykresy wyświetlane w portalu są dostępne jako zawartość wbudowana w wiadomości e-mail.
* Siatki wyświetlane w portalu są dostępne jako załączniki *. csv w wiadomości e-mail.
* Dane wyświetlane w wiadomości e-mail korzystają ze wszystkich filtrów na poziomie raportu wybranych przez użytkownika w raporcie w momencie tworzenia zadania poczty e-mail.
* Filtry na poziomie karty, takie jak **Nazwa wystąpienia kopii zapasowej**, **Nazwa zasad** i tak dalej, nie są stosowane. Jedynym wyjątkiem jest siatka **optymalizacji przechowywania** na karcie **Optymalizacja** , w której stosowane są filtry **dzienne**, **tygodniowe**, **miesięczne** i **roczne** .
* Zakres czasu i typ agregacji (dla wykresów) są oparte na wyborze zakresu czasu użytkownika w raportach. Na przykład jeśli wybór zakresu czasu jest ostatni 60 dni (tłumaczenie na tygodniowy typ agregacji), a częstotliwość poczty e-mail jest codziennie, odbiorca otrzyma wiadomość e-mail codziennie z wykresami obejmującymi dane, które są wykonywane w ciągu ostatniego 60-dniowego okresu, z danymi zagregowanymi na poziomie co tydzień.

## <a name="troubleshooting-issues"></a>Rozwiązywanie problemów

Jeśli nie otrzymujesz wiadomości e-mail zgodnie z oczekiwaniami nawet po pomyślnym wdrożeniu aplikacji logiki, możesz wykonać poniższe kroki, aby rozwiązać problem z konfiguracją:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Scenariusz 1: otrzymywanie wiadomości e-mail ani wiadomości e-mail o błędzie nie powiodło się

* Ten problem może wystąpić, ponieważ łącznik interfejsu API programu Outlook nie jest autoryzowany. Aby autoryzować połączenie, wykonaj kroki autoryzacji podane powyżej.

* Ten problem może również wystąpić, jeśli podczas tworzenia aplikacji logiki został określony nieprawidłowy odbiorca wiadomości e-mail. Aby sprawdzić, czy Adresat poczty e-mail został określony prawidłowo, możesz przejść do aplikacji logiki w Azure Portal, otworzyć projektanta aplikacji logiki i wybrać pozycję e-mail krok, aby sprawdzić, czy są używane poprawne identyfikatory poczty e-mail.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Scenariusz 2: otrzymywanie wiadomości e-mail z informacją o błędzie informującym, że nie można wykonać operacji na aplikacji logiki

Aby rozwiązać ten problem:
1.  Przejdź do aplikacji logiki w Azure Portal.
2.  W dolnej części ekranu **przeglądu** zostanie wyświetlona sekcja **historia uruchomień** . Możesz otworzyć w najnowszym przebiegu i zobaczyć, które kroki w przepływie pracy nie powiodły się. Możliwe przyczyny:
    * **Łącznik dzienników Azure monitor nie został autoryzowany**: Aby rozwiązać ten problem, wykonaj kroki autoryzacji zgodnie z powyższymi informacjami.
    * **Błąd w La Query**: w przypadku dostosowania aplikacji logiki przy użyciu własnych zapytań błąd w którejkolwiek z tych zapytań może spowodować niepowodzenie działania aplikacji logiki. Możesz wybrać odpowiedni krok i wyświetlić błąd, który powoduje nieprawidłowe działanie zapytania.

Jeśli problemy będą się powtarzać, skontaktuj się z pomocą techniczną firmy Microsoft.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o raportach kopii zapasowych](https://docs.microsoft.com/azure/backup/configure-reports)
