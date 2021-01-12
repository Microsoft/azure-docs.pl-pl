---
title: łącznik zarządzania usługami IT w Log Analytics
description: Ten artykuł zawiera omówienie łącznik zarządzania usługami IT (ITSMC) i informacje na temat używania go do monitorowania i zarządzania elementami roboczymi narzędzia ITSM w programie Log Analytics i szybkiego rozwiązywania problemów.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: a7e39bb8ed742007a13a222771b430372d50e889
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071701"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Połącz platformę Azure z narzędziami narzędzia ITSM przy użyciu łącznik zarządzania usługami IT

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania łącznik zarządzania usługami IT (ITSMC) w Log Analytics, aby centralnie zarządzać elementami roboczymi.

## <a name="add-it-service-management-connector"></a>Dodaj łącznik zarządzania usługami IT

Aby można było utworzyć połączenie, należy dodać ITSMC.

1. W Azure Portal wybierz pozycję **Utwórz zasób**:

   ![Zrzut ekranu pokazujący element menu Utwórz zasób.](media/itsmc-overview/azure-add-new-resource.png)

2. Wyszukaj **Łącznik zarządzania usługami IT** w witrynie Azure Marketplace. Wybierz pozycję **Utwórz**:

   ![Zrzut ekranu przedstawiający przycisk Utwórz w portalu Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. W sekcji **La Workspace** wybierz obszar roboczy usługi Azure log Analytics, w którym chcesz zainstalować ITSMC.
   >[!NOTE]
   >
   > * ITSMC można zainstalować tylko w obszarze roboczym Log Analytics w następujących regionach: Wschodnie stany USA, zachodnie stany USA 2, Południowo-środkowe stany USA, zachodnie stany USA, US Gov Arizona, US Gov Wirginia, Kanada środkowa, Europa Zachodnia, Południowe Zjednoczone Królestwo, Azja Południowo-Wschodnia, Japonia Wschodniej, Indie Środkowe i Australia Południowo-Wschodnia.

4. W sekcji **obszar roboczy log Analytics** wybierz grupę zasobów, w której chcesz utworzyć zasób ITSMC:

   ![Zrzut ekranu przedstawiający sekcję Log Analytics obszaru roboczego.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor, obszary robocze OMS są teraz określane jako *log Analytics obszary robocze*.

5. Wybierz pozycję **OK**.

Po wdrożeniu zasobu ITSMC w prawym górnym rogu okna pojawi się powiadomienie.

## <a name="create-an-itsm-connection"></a>Utwórz połączenie narzędzia ITSM

Po zainstalowaniu programu ITSMC można utworzyć połączenie.

Aby utworzyć połączenie, należy wykonać odpowiednie przygotowanie narzędzia Narzędzia ITSM, aby umożliwić połączenie z ITSMC.  

W oparciu o produkt narzędzia ITSM, z którym nawiązujesz połączenie, wybierz jedną z poniższych linków, aby uzyskać instrukcje:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Po jego narzędzi Narzędzia ITSM wykonaj następujące kroki, aby utworzyć połączenie:

1. W obszarze **wszystkie zasoby** Znajdź pozycję **Servicedesk (*Nazwa obszaru roboczego*)**:

   ![Zrzut ekranu pokazujący najnowsze zasoby w Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

1. W obszarze **źródła danych obszaru roboczego** w okienku po lewej stronie wybierz pozycję **połączenia narzędzia ITSM**:

   ![Zrzut ekranu pokazujący element menu Narzędzia ITSM Connections.](media/itsmc-overview/add-new-itsm-connection.png)
1. Wybierz pozycję **Dodaj połączenie**.

1. Określ ustawienia połączenia zgodnie z opisem w temacie narzędzia ITSM Products/Services:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > Domyślnie program ITSMC odświeża dane konfiguracji połączenia co 24 godziny. Aby natychmiast odświeżyć dane połączenia w celu odzwierciedlenia wszelkich edytowanych lub wprowadzonych aktualizacji szablonów, wybierz przycisk **Synchronizuj** w bloku połączenia:
   >
   > ![Zrzut ekranu pokazujący przycisk Synchronizuj w bloku połączenie.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>Użyj ITSMC

   Można użyć ITSMC do tworzenia alertów z alertów Azure Monitor w narzędziu narzędzia ITSM.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure

Po utworzeniu połączenia narzędzia ITSM można tworzyć elementy robocze w narzędziu narzędzia ITSM w oparciu o alerty platformy Azure. Aby utworzyć elementy robocze, użyj akcji narzędzia ITSM w grupie akcji.

Grupy akcji umożliwiają modularne i wielokrotne użycie metody wyzwalania akcji dla alertów platformy Azure. Za pomocą grup akcji można korzystać z alertów metryk, alertów dziennika aktywności i alertów usługi Azure Log Analytics w Azure Portal.

> [!NOTE]
> Po utworzeniu połączenia narzędzia ITSM należy poczekać 30 minut na zakończenie procesu synchronizacji.

### <a name="template-definitions"></a>Definicje szablonów

   Istnieją typy elementów roboczych, które mogą używać szablonów, które są zdefiniowane przez narzędzie narzędzia ITSM.
Za pomocą szablonów można zdefiniować pola, które będą automatycznie wypełniane zgodnie z ustalonymi wartościami, które są zdefiniowane jako część grupy akcji. Szablony można definiować w narzędziu narzędzia ITSM.
Można zdefiniować szablon, który ma być używany jako część definicji grupy akcji.

Aby utworzyć grupy akcji, należy wykonać czynności opisane w poniższej procedurze:

1. W Azure Portal wybierz pozycję  **alerty**.
2. W menu w górnej części ekranu wybierz pozycję **Zarządzaj akcjami**:

    ![Zrzut ekranu pokazujący element menu Zarządzaj akcjami.](media/itsmc-overview/action-groups-selection-big.png)

   Zostanie wyświetlone okno **Tworzenie grupy akcji** .

3. Wybierz **subskrypcję** i **grupę zasobów** , w której chcesz utworzyć grupę akcji. Podaj **nazwę grupy akcji** i **nazwę wyświetlaną** dla grupy akcji. Wybierz pozycję **Dalej: powiadomienia**.

    ![Zrzut ekranu przedstawiający okno Tworzenie grupy akcji.](media/itsmc-overview/action-groups-details.png)

4. Na liście powiadomień wybierz pozycję **Dalej: akcje**.
5. Na liście Akcje wybierz pozycję **Narzędzia ITSM** na liście **Typ akcji** . Podaj **nazwę** akcji. Wybierz przycisk pióra reprezentujący **szczegóły edycji**.

    ![Zrzut ekranu pokazujący definicję grupy akcji.](media/itsmc-definition/action-group-pen.png)

6. Na liście **subskrypcja** wybierz subskrypcję, w której znajduje się obszar roboczy log Analytics. Na liście **połączenie** wybierz swoją nazwę łącznika narzędzia ITSM. Zostanie nadana nazwa obszaru roboczego. Na przykład MyITSMConnector (mój obszar roboczy).

7. Wybierz typ **elementu pracy** .

8. Jeśli chcesz wypełnić pola z ustalonymi wartościami, wybierz opcję **Użyj szablonu niestandardowego**. W przeciwnym razie wybierz istniejący [szablon](#template-definitions) na liście **szablon** i wprowadź wartości ustalone w polach szablon.

9. W ostatniej sekcji definicji grupy akcji narzędzia ITSM można zdefiniować liczbę elementów roboczych, które zostaną utworzone dla każdego alertu.

    >[!NOTE]
    >
    > * Ta sekcja ma zastosowanie tylko w przypadku alertów dotyczących przeszukiwania dzienników.
    > * Dla wszystkich innych typów alertów zostanie utworzony jeden element roboczy dla każdego alertu.

    * W przypadku wybrania opcji "element roboczy" "zdarzenie" lub "Alert": ![ zrzut ekranu przedstawiający okno zdarzenia narzędzia ITSM.](media/itsmc-overview/itsm-action-configuration.png)
        * W przypadku zaznaczenia pola wyboru **"Utwórz poszczególne elementy robocze dla każdego elementu konfiguracji"** każdy element konfiguracji w każdym alercie utworzy nowy element roboczy. W wyniku kilku alertów dla tych samych elementów konfiguracji, których dotyczy problem, istnieje więcej niż jeden element roboczy dla każdego elementu konfiguracji.

             Na przykład:
             1) Alert 1 z 3 elementami konfiguracji: A, B, C-utworzy 3 elementy robocze.
             2) Alert 2 z 1 elementem konfiguracji: A — utworzy 1 element roboczy.

        * W przypadku wyczyszczenia pola wyboru **"Utwórz poszczególne elementy robocze dla każdego elementu konfiguracji"** Łącznik narzędzia ITSM utworzy pojedynczy element roboczy dla każdej reguły alertu i doda do niego wszystkie elementy konfiguracji, których to dotyczy. Zostanie utworzony nowy element roboczy, jeśli poprzedni zostanie zamknięty.

        >[!NOTE]
        > W takim przypadku część wygenerowanego alertu nie spowoduje wygenerowania nowych elementów roboczych w narzędziu narzędzia ITSM.

        Na przykład:
         1) Alert 1 z 3 elementami konfiguracji: A, B, C-utworzy 1 element roboczy.
         2) Alert 2 dla tej samej reguły alertu jak w kroku a z 1 element konfiguracji: D-D zostanie dołączony do listy elementów konfiguracji, których dotyczy problem, w elemencie roboczym utworzonym w kroku a.
         3) Alert 3 dla innej reguły alertu z 1 elementem konfiguracji: E-utworzy 1 element roboczy.

    * W przypadku wybrania opcji "element roboczy" "zdarzenie": ![ zrzut ekranu przedstawiający okno zdarzenia narzędzia ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

        * W przypadku wybrania opcji **"Utwórz pojedyncze elementy robocze dla każdego wpisu dziennika (pole elementu konfiguracji nie jest wypełnione. Może spowodować powstanie dużej liczby elementów roboczych.) "** w obszarze przyciski radiowe zostanie utworzony element roboczy dla każdego wiersza w wynikach wyszukiwania zapytania o alert przeszukiwania dzienników. W ładunku elementu pracy Właściwość Description będzie zawierać wiersz z wyników wyszukiwania.
        * W przypadku wybrania opcji **"Utwórz indywidualne elementy robocze dla każdego elementu konfiguracji"** w wyborze przycisków radiowych każdy element konfiguracji w każdym alercie zostanie utworzony nowy element roboczy. W systemie narzędzia ITSM może istnieć więcej niż jeden element roboczy na element konfiguracji. Ta wartość będzie taka sama jak zaznaczenie pola wyboru w sekcji incydentu/alertu.

10. Wybierz pozycję **OK**.

Gdy tworzysz lub edytujesz regułę alertu platformy Azure, Użyj grupy akcji, która ma akcję narzędzia ITSM. Po wyzwoleniu alertu element roboczy jest tworzony lub aktualizowany w narzędziu narzędzia ITSM.

> [!NOTE]
>
>- Informacje o cenach akcji narzędzia ITSM można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/monitor/) dla grup akcji.
>
>
>- Pole Krótki opis w definicji reguły alertu jest ograniczone do 40 znaków podczas wysyłania ich przy użyciu akcji narzędzia ITSM.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów w łączniku ITSM](./itsmc-resync-servicenow.md)
