---
title: Zarządzanie aktualizacjami dla wielu maszyn wirtualnych platformy Azure
description: W tym artykule opisano sposób zarządzania aktualizacjami dla maszyn wirtualnych platformy Azure i maszyn wirtualnych innych niż Azure.
services: automation
ms.subservice: update-management
ms.date: 03/26/2020
ms.topic: conceptual
ms.openlocfilehash: 5376562d9df35539a33f6746b387a1ff7083b8f1
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676441"
---
# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Zarządzanie aktualizacjami dla wielu maszyn wirtualnych platformy Azure

Usługi Azure Automation Update Management umożliwiają zarządzanie aktualizacjami i poprawkami maszyn wirtualnych systemu Windows i Linux. Korzystając z konta usługi [Azure Automation](automation-offering-get-started.md), można wykonywać następujące czynności:

- Dołączanie maszyn wirtualnych.
- Ocenianie stanu dostępnych aktualizacji.
- Planowanie instalacji wymaganych aktualizacji.
- Przejrzyj wyniki wdrożenia, aby sprawdzić, czy aktualizacje zostały pomyślnie zastosowane do wszystkich maszyn wirtualnych, dla których jest włączone zarządzanie aktualizacjami.

Aby dowiedzieć się więcej o wymaganiach systemowych dotyczących zarządzania aktualizacjami, zobacz [Wymagania klienta usługi Zarządzania aktualizacjami](automation-update-management.md#clients).

## <a name="prerequisites"></a>Wymagania wstępne

* Maszyna wirtualna lub komputer z zainstalowanym obsługiwanym systemem operacyjnym.
* Dostęp do repozytorium aktualizacji dla maszyn wirtualnych z systemem Linux dołączanych do zarządzania aktualizacjami.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Włącz zarządzanie aktualizacjami dla maszyn wirtualnych platformy Azure

W witrynie Azure portal otwórz konto automatyzacji, a następnie wybierz pozycję **Aktualizuj zarządzanie**.

Wybierz **pozycję Dodaj maszyny wirtualne platformy Azure**.

![Karta Dodaj maszynę wirtualną Azure](./media/manage-update-multi/update-onboard-vm.png)

Wybierz maszynę wirtualną, którą chcesz dołączyć.

W obszarze **Włącz zarządzanie aktualizacjami**wybierz pozycję **Włącz,** aby przywkłędować maszynę wirtualną.

![Okno dialogowe Włączanie rozwiązania Update Management](./media/manage-update-multi/update-enable.png)

Po zakończeniu dołączania zarządzanie aktualizacjami jest włączone dla maszyny wirtualnej.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Włącz zarządzanie aktualizacjami dla maszyn wirtualnych i komputerów innych niż Azure

Agent analizy dzienników dla systemów Windows i Linux musi być zainstalowany na maszynach wirtualnych, które są uruchomione w sieci firmowej lub innym środowisku chmury, aby umożliwić im zarządzanie aktualizacjami. Aby poznać wymagania systemowe i obsługiwane metody wdrażania agenta na komputerach hostowanych poza platformą Azure, zobacz [Omówienie agenta usługi Log Analytics.](../azure-monitor/platform/log-analytics-agent.md)

## <a name="view-computers-attached-to-your-automation-account"></a>Wyświetlanie komputerów dołączonych do konta automatyzacji

Po włączeniu zarządzania aktualizacjami dla komputerów można wyświetlić informacje o komputerze, wybierając pozycję **Komputery**. Można wyświetlić informacje o nazwie komputera, stanie zgodności, środowisku, typie systemu operacyjnego, zainstalowanych aktualizacjach krytycznych i zabezpieczeń, innych zainstalowanych aktualizacjach i gotowości agenta aktualizacji dla komputerów.

  ![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-computers-tab.png)

Komputery, które zostały ostatnio włączone dla zarządzania aktualizacjami, mogły nie zostać jeszcze ocenione. Stan zgodności dla tych `Not assessed`komputerów jest . Oto lista możliwych wartości stanu zgodności:

- `Compliant`: Komputery, na których nie brakuje aktualizacji krytycznych lub zabezpieczeń.
- `Non-compliant`: Na komputerach brakuje co najmniej jednej aktualizacji krytycznej lub zabezpieczeń.
- `Not assessed`: Dane oceny aktualizacji nie zostały odebrane z komputera w oczekiwanych ramach czasowych. W przypadku komputerów z systemem Linux oczekiwany przedział czasu to ostatnia godzina. W przypadku komputerów z systemem Windows oczekiwany przedział czasu to ostatnie 12 godzin.

Aby wyświetlić stan agenta, wybierz łącze w kolumnie **Aktualizuj gotowość agenta.** Wybranie tej opcji powoduje otwarcie okienka Proces hybrydowy i wyświetlenie stanu procesu roboczego hybrydowego. Na poniższej ilustracji przedstawiono przykład agenta, który nie był połączony z zarządzaniem aktualizacjami przez dłuższy czas:

![Karta z wyświetlonymi komputerami](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>Wyświetlanie oceny aktualizacji

Po włączeniu zarządzania aktualizacjami zostanie otwarte okienko Zarządzanie aktualizacjami. Możesz wyświetlić listę brakujących aktualizacji na karcie **Brakujące aktualizacje**.

## <a name="collect-data"></a>Zbieranie danych

Agenci instalowane na maszynach wirtualnych i komputerach zbierają dane dotyczące aktualizacji. Agenci wysyłają dane do usługi Azure Update Management.

### <a name="supported-agents"></a>Obsługiwani agenci

W poniższej tabeli opisano połączone źródła obsługiwane przez to rozwiązanie:

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows |Tak |Usługa Zarządzanie aktualizacjami zbiera informacje o aktualizacjach systemu od agentów systemu Windows, a następnie inicjuje instalację wymaganych aktualizacji. |
| Agenci dla systemu Linux |Tak |Usługa Update Management zbiera informacje o aktualizacjach systemu od agentów systemu Linux, a następnie inicjuje instalację wymaganych aktualizacji w obsługiwanych dystrybucjach. |
| Grupa zarządzania programu Operations Manager |Tak |Usługa Zarządzanie aktualizacjami zbiera informacje o aktualizacjach systemu od agentów z połączonej grupy zarządzania. |
| Konto usługi Azure Storage |Nie |Usługa Azure Storage nie zawiera informacji o aktualizacjach systemu. |

### <a name="collection-frequency"></a>Częstotliwość zbierania

Po zakończeniu skanowania komputera w celu zapewnienia zgodności z aktualizacjami agent przesyła zbiorczo informacje do dzienników usługi Azure Monitor. Na komputerze z systemem Windows skanowanie zgodności jest domyślnie uruchamiane co 12 godzin.

Oprócz harmonogramu skanowania skanowanie w poszukiwaniu zgodności aktualizacji jest inicjowane w ciągu 15 minut od ponownego uruchomienia programu MMA, przed instalacją aktualizacji i po instalacji aktualizacji.

W przypadku komputera z systemem Linux skanowanie zgodności jest domyślnie wykonywane co godzinę. Jeśli agent MMA zostanie ponownie uruchomiony, skanowanie zgodności jest inicjowane w ciągu 15 minut.

Wyświetlenie zaktualizowanych danych z zarządzanych komputerów na pulpicie nawigacyjnym może potrwać od 30 minut do 6 godzin.

## <a name="schedule-an-update-deployment"></a>Planowanie wdrożenia aktualizacji

Aby zainstalować aktualizacje, zaplanuj wdrożenie zgodne z harmonogramem wydania i oknem usługi. Możesz wybrać typy aktualizacji, które mają zostać uwzględnione we wdrożeniu. Możesz na przykład uwzględnić aktualizacje krytyczne lub aktualizacje zabezpieczeń i wykluczyć pakiety zbiorcze aktualizacji.

>[!NOTE]
>Podczas planowania wdrożenia aktualizacji tworzy zasób [harmonogramu](shared-resources/schedules.md) połączony z **patch-MicrosoftOMSComputers** runbook, który obsługuje wdrożenia aktualizacji na komputerach docelowych. Jeśli usuniesz zasób harmonogramu z witryny Azure portal lub przy użyciu programu PowerShell po utworzeniu wdrożenia, przerywa zaplanowane wdrożenie aktualizacji i przedstawia błąd podczas próby ponownego skonfigurowania go z portalu. Zasób harmonogramu można usunąć tylko przez usunięcie odpowiedniego harmonogramu wdrażania.
>

Aby zaplanować nowe wdrożenie aktualizacji dla co najmniej jednej maszyny wirtualnej, w obszarze **Zarządzanie aktualizacjami**wybierz pozycję **Zaplanuj wdrożenie aktualizacji**.

W okienku **wdrażania Nowa aktualizacja** określ następujące informacje:

- **Nazwa**: Wprowadź unikatową nazwę, aby zidentyfikować wdrożenie aktualizacji.
- **System operacyjny**: Wybierz **Windows** lub **Linux**.
- **Grupy do aktualizacji:** Zdefiniuj kwerendę na podstawie kombinacji subskrypcji, grup zasobów, lokalizacji i tagów, aby utworzyć dynamiczną grupę maszyn wirtualnych platformy Azure do uwzględnienia we wdrożeniu. W przypadku maszyn wirtualnych innych niż Azure zapisane wyszukiwania są używane do tworzenia grupy dynamicznej do uwzględnienia we wdrożeniu. Aby dowiedzieć się więcej, zobacz [Grupy dynamiczne](automation-update-management-groups.md).
- **Maszyny do aktualizacji:** Wybierz zapisane wyszukiwanie, zaimportowana grupa lub wybierz pozycję Maszyny, aby wybrać maszyny, które chcesz zaktualizować.

   >[!NOTE]
   >Wybranie opcji Zapisane wyszukiwanie nie powoduje zwrócenia tożsamości komputera, a jedynie ich nazw. Jeśli masz kilka maszyn wirtualnych o tej samej nazwie w wielu grupach zasobów, są one zwracane w wynikach. Za pomocą **grup do aktualizacji** opcja jest zalecane, aby upewnić się, że zawiera unikatowe maszyny wirtualne spełniające kryteria.

   Jeśli wybierzesz **Machine**, gotowość komputera jest wyświetlana w **kolumnie Gotowość agenta aktualizacji.** Stan kondycji komputera można zobaczyć przed zaplanowaniem wdrożenia aktualizacji. Aby dowiedzieć się więcej na temat różnych metod tworzenia grup komputerów w dziennikach usługi Azure Monitor, zobacz [Computer groups in Azure Monitor logs (Grupy komputerów w dziennikach usługi Azure Monitor)](../azure-monitor/platform/computer-groups.md)

  ![Nowe okienko wdrażania aktualizacji](./media/manage-update-multi/update-select-computers.png)

- **Klasyfikacja aktualizacji**: Wybierz typy oprogramowania, które mają być uwzględnione we wdrożeniu aktualizacji. Aby uzyskać opis typów klasyfikacji, zobacz [Aktualizowanie klasyfikacji](automation-view-update-assessments.md#update-classifications). Dostępne są następujące typy klasyfikacji:
  - Aktualizacje krytyczne
  - Aktualizacje zabezpieczeń
  - Pakiety zbiorcze aktualizacji
  - Pakiety funkcji
  - Dodatki Service Pack
  - Aktualizacje definicji
  - narzędzia
  - Aktualizacje

- **Aktualizacje do uwzględnienia/wykluczenia** — spowoduje to otwarcie strony Uwzględnij/Wyklucz. Aktualizacje, które mają zostać uwzględnione lub wykluczone, znajdują się na osobnych kartach. Aby uzyskać dodatkowe informacje dotyczące sposobu obsługi [dołączania,](automation-tutorial-update-management.md#schedule-an-update-deployment)zobacz Planowanie wdrożenia aktualizacji .

> [!NOTE]
> Ważne jest, aby wiedzieć, że wykluczenia zastępują inkluzje. Na przykład, jeśli zdefiniujesz regułę wykluczania `*`, to żadne poprawki lub pakiety nie są zainstalowane, ponieważ wszystkie są wykluczone. Wykluczone poprawki nadal są wyświetlane jako brakujące w maszynie. Dla komputerów z systemem Linux, jeśli pakiet jest dołączony, ale ma pakiet zależny, który został wykluczony, pakiet nie jest zainstalowany.

> [!NOTE]
> Nie można określić aktualizacji, które zostały zastąpione do włączenia z wdrożeniem aktualizacji.
>

- **Ustawienia harmonogramu**: możesz zaakceptować domyślną datę i godzinę, czyli 30 minut po bieżącej godzinie. Można również określić inny czas.

   Możesz też określić, czy wdrożenie ma występować raz, czy zgodnie z harmonogramem cyklicznym. Aby skonfigurować harmonogram cykliczny, w obszarze **Cykle**wybierz pozycję **Cykliczne**.

   ![Okno dialogowe Ustawienia harmonogramu](./media/manage-update-multi/update-set-schedule.png)

- **Skrypty wstępne i końcowe**: wybierz skrypty do uruchomienia przed i po wdrożeniu. Aby dowiedzieć się więcej, zobacz [Zarządzanie skryptami wstępnymi i końcowymi](pre-post-scripts.md).
- **Okno konserwacji (w minutach)**: Określ okres, przez który ma nastąpić wdrożenie aktualizacji. To ustawienie pozwala zagwarantować, że zmiany będą wprowadzane w ramach zdefiniowanych okien obsługi.

- **Kontrola ponownego uruchamiania** — to ustawienie określa sposób obsługi ponownych rozruchów dla wdrożenia aktualizacji.

   |Opcja|Opis|
   |---|---|
   |Uruchom ponownie, jeśli jest to wymagane| **(Domyślnie)** W razie potrzeby zostanie zainicjowany ponowny rozruch, jeśli na to zezwala okno konserwacji.|
   |Zawsze uruchamiaj ponownie|Ponowne uruchomienie jest inicjowane niezależnie od tego, czy jest wymagane. |
   |Nigdy nie uruchamiaj ponownie|Niezależnie od tego, czy wymagany jest ponowny rozruch, ponowne uruchomienie jest pomijane.|
   |Tylko ponowne uruchomienie — aktualizacje nie zostaną zainstalowane|Ta opcja ignoruje instalowanie aktualizacji i inicjuje tylko ponowne uruchomienie.|

Po zakończeniu konfigurowania harmonogramu wybierz przycisk **Utwórz,** aby powrócić do pulpitu nawigacyjnego stanu. W tabeli **Zaplanowane** jest wyświetlany utworzony harmonogram wdrażania.

> [!NOTE]
> Rozwiązanie Update Management obsługuje wdrażanie aktualizacji tej samej firmy i wstępne pobieranie poprawek. Wymaga to zmian w systemach, które są łatane, zobacz [pomoc techniczną pierwszej firmy i przed pobraniem,](automation-configure-windows-update.md#pre-download-updates) aby dowiedzieć się, jak skonfigurować te ustawienia w systemach.

## <a name="view-results-of-an-update-deployment"></a>Wyświetlanie wyników wdrażania aktualizacji

Po rozpoczęciu zaplanowanego wdrażania stan tego wdrożenia można sprawdzić na karcie **Wdrożenia aktualizacji** w obszarze rozwiązania **Update Management**.

Jeśli wdrożenie jest aktualnie uruchomione, wyświetlany jest stan **W toku**. Po pomyślnym zakończeniu wdrażania stan zmienia się na **Pomyślnie**.

W przypadku błędu co najmniej jednej aktualizacji w ramach wdrożenia jest wyświetlany stan **Częściowe niepowodzenie**.

![Stan wdrożenia aktualizacji](./media/manage-update-multi/update-view-results.png)

Aby wyświetlić pulpit nawigacyjny wdrożenia aktualizacji, wybierz ukończone wdrożenie.

Okienko Wyniki aktualizacji zawiera całkowitą liczbę aktualizacji i wyniki wdrożenia maszyny wirtualnej. Tabela po prawej stronie zawiera szczegółowy podział każdej aktualizacji i wyników instalacji. Wyniki instalacji mogą mieć jedną z następujących wartości:

- `Not attempted`: Aktualizacja nie została zainstalowana, ponieważ na podstawie zdefiniowanego okna konserwacji nie było wystarczającego czasu.
- `Succeeded`: Aktualizacja powiodła się.
- `Failed`: Aktualizacja nie powiodła się.

Aby wyświetlić wszystkie wpisy dziennika utworzone przez wdrożenie, wybierz pozycję **Wszystkie dzienniki**.

Aby wyświetlić strumień zadań w yjsce runbook, który zarządza wdrożeniem aktualizacji na docelowej maszynie wirtualnej, wybierz kafelek wyjściowy.

Aby wyświetlić szczegółowe informacje o błędach związanych z wdrożeniem, wybierz pozycję **Błędy**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o dziennikach, danych wyjściowych i błędach zarządzania aktualizacjami, zobacz [Rekordy aktualizacji kwerend dla zarządzania aktualizacjami](automation-update-management-query-logs.md).
