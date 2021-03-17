---
title: łącznik zarządzania usługami IT w Log Analytics
description: Ten artykuł zawiera omówienie łącznik zarządzania usługami IT (ITSMC) i informacje na temat używania go do monitorowania i zarządzania elementami roboczymi narzędzia ITSM w programie Log Analytics i szybkiego rozwiązywania problemów.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 98f53ec1b6506a6d47146377e837576254f445e2
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601070"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>Łączenie platformy Azure z narzędziami narzędzia ITSM przy użyciu rozwiązania do zarządzania usługami IT

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Ten artykuł zawiera informacje dotyczące sposobu konfigurowania łącznik zarządzania usługami IT (ITSMC) w Log Analytics do centralnego zarządzania elementami roboczymi usługi IT Service Management (narzędzia ITSM).

## <a name="add-it-service-management-connector"></a>Dodaj łącznik zarządzania usługami IT

Aby można było utworzyć połączenie, należy zainstalować ITSMC.

1. W Azure Portal wybierz pozycję **Utwórz zasób**:

   ![Zrzut ekranu pokazujący element menu służący do tworzenia zasobu.](media/itsmc-overview/azure-add-new-resource.png)

2. Wyszukaj **Łącznik zarządzania usługami IT** w witrynie Azure Marketplace. Następnie wybierz pozycję **Utwórz**:

   ![Zrzut ekranu przedstawiający przycisk Utwórz w portalu Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. W sekcji **La Workspace** wybierz obszar roboczy log Analytics, w którym chcesz zainstalować ITSMC.
   > [!NOTE]
   > Możesz zainstalować ITSMC w obszarach roboczych Log Analytics tylko w następujących regionach: Wschodnie stany USA, zachodnie stany USA 2, Południowo-środkowe stany USA, zachodnio-środkowe stany USA, US Gov Arizona, US Gov Wirginia, Kanada środkowa, Europa Zachodnia, Południowe Zjednoczone Królestwo, Azja Południowo-Wschodnia, Japonia Wschodniej, Indie Środkowe i Australia Południowo-Wschodnia.

4. W sekcji **obszar roboczy log Analytics** wybierz grupę zasobów, w której chcesz utworzyć zasób ITSMC:

   ![Zrzut ekranu przedstawiający sekcję Log Analytics obszaru roboczego.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor, obszary robocze OMS są teraz nazywane *obszarami roboczymi log Analytics*.

5. Wybierz przycisk **OK**.

Po wdrożeniu zasobu ITSMC w prawym górnym rogu okna pojawi się powiadomienie.

## <a name="create-an-itsm-connection"></a>Utwórz połączenie narzędzia ITSM

Po zainstalowaniu programu ITSMC należy wykonać przygotowanie narzędzia Narzędzia ITSM, aby zezwolić na połączenie z usługi ITSMC. W oparciu o produkt narzędzia ITSM, z którym nawiązujesz połączenie, wybierz jedną z poniższych linków, aby uzyskać instrukcje:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Po jego narzędzia Narzędzia ITSM wykonaj następujące kroki, aby utworzyć połączenie:

1. W obszarze **wszystkie zasoby** Znajdź pozycję **Servicedesk (*Nazwa obszaru roboczego*)**:

   ![Zrzut ekranu pokazujący najnowsze zasoby w Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

1. W obszarze **źródła danych obszaru roboczego** w okienku po lewej stronie wybierz pozycję **połączenia narzędzia ITSM**:

   ![Zrzut ekranu pokazujący element menu Narzędzia ITSM Connections.](media/itsmc-overview/add-new-itsm-connection.png)

1. Wybierz pozycję **Dodaj połączenie**.

1. Określ ustawienia połączenia zgodnie z używanym produktem narzędzia ITSM:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > Domyślnie program ITSMC odświeża dane konfiguracji połączenia co 24 godziny. Aby natychmiast odświeżyć dane połączenia w celu odzwierciedlenia zmian wprowadzonych przez użytkownika, wybierz przycisk **Synchronizuj** w okienku połączenie:
   >
   > ![Zrzut ekranu pokazujący przycisk Synchronizuj w okienku połączenie.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure

Po utworzeniu połączenia narzędzia ITSM można użyć ITMC do tworzenia elementów roboczych w narzędziu narzędzia ITSM na podstawie alertów platformy Azure. Aby utworzyć elementy robocze, użyj akcji narzędzia ITSM w grupie akcji.

Grupy akcji umożliwiają modularne i wielokrotne użycie metody wyzwalania akcji dla alertów platformy Azure. Za pomocą grup akcji można korzystać z alertów metryk, alertów dziennika aktywności i alertów Log Analytics w Azure Portal.

> [!NOTE]
> Po utworzeniu połączenia narzędzia ITSM należy poczekać 30 minut na zakończenie procesu synchronizacji.

## <a name="define-a-template"></a>Definiowanie szablonu

Niektóre typy elementów roboczych mogą korzystać z szablonów zdefiniowanych w narzędziu narzędzia ITSM. Za pomocą szablonów można zdefiniować pola, które będą automatycznie wypełniane zgodnie z ustalonymi wartościami dla grupy akcji. Można zdefiniować szablon, który ma być używany jako część definicji grupy akcji. Informacje o sposobach tworzenia szablonów — można znaleźć w dokumentacji usługi ServiceNow https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html .

Aby utworzyć grupę akcji:

1. W Azure Portal wybierz pozycję  **alerty**.
2. W menu w górnej części ekranu wybierz pozycję **Zarządzaj akcjami**:

    ![Zrzut ekranu pokazujący element menu Zarządzaj akcjami.](media/itsmc-overview/action-groups-selection-big.png)

   Zostanie wyświetlone okno **Tworzenie grupy akcji** .

3. Wybierz **subskrypcję** i **grupę zasobów** , w której chcesz utworzyć grupę akcji. Podaj wartości w polu **Nazwa grupy akcji** i **Nazwa wyświetlana** dla grupy akcji. Następnie wybierz pozycję **Dalej: powiadomienia**.

    ![Zrzut ekranu przedstawiający okno Tworzenie grupy akcji.](media/itsmc-overview/action-groups-details.png)

4. Na karcie **powiadomienia** wybierz pozycję **Dalej: akcje**.
5. Na karcie **Akcje** wybierz pozycję **Narzędzia ITSM** na liście **Typ akcji** . W polu **Nazwa** Podaj nazwę akcji. Następnie wybierz przycisk pióra reprezentujący **szczegóły edycji**.

    ![Zrzut ekranu pokazujący wybory dla tworzenia grupy akcji.](media/itsmc-definition/action-group-pen.png)

6. Na liście **subskrypcja** wybierz subskrypcję zawierającą obszar roboczy log Analytics. Na liście **połączenie** wybierz swoją nazwę łącznika narzędzia ITSM. Zostanie nadana nazwa obszaru roboczego. Przykładem jest *MyITSMConnector (mój obszar roboczy)*.

7. Wybierz typ **elementu pracy** .

8. Jeśli chcesz wypełnić pola z ustalonymi wartościami, wybierz opcję **Użyj szablonu niestandardowego**. W przeciwnym razie wybierz istniejący [szablon](#define-a-template) na liście **szablon** i wprowadź wartości ustalone w polach szablon.

9. W ostatniej sekcji interfejsu w celu utworzenia grupy akcji narzędzia ITSM można zdefiniować liczbę elementów roboczych, które będą tworzone dla każdego alertu.

   > [!NOTE]
   > Ta sekcja ma zastosowanie tylko w przypadku alertów dotyczących przeszukiwania dzienników. Dla wszystkich innych typów alertów utworzysz jeden element roboczy dla każdego alertu.

   * W przypadku wybrania **incydentu** lub **alertu** na liście rozwijanej **element roboczy** można utworzyć poszczególne elementy robocze dla każdego elementu konfiguracji.
    
     ![Zrzut ekranu pokazujący obszar biletów I T S z incydentem wybranym jako element roboczy.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Jeśli zaznaczysz pole wyboru **Utwórz indywidualne elementy robocze dla każdego elementu konfiguracji** , każdy element konfiguracji w każdym alercie utworzy nowy element roboczy. Ponieważ istnieją różne alerty dotyczące tych samych elementów konfiguracji, których dotyczy ten problem, dla każdego elementu konfiguracji będzie więcej niż jeden element roboczy.

       Na przykład alert zawierający trzy elementy konfiguracji spowoduje utworzenie trzech elementów roboczych. Alert, który ma jeden element konfiguracji, utworzy jeden element roboczy.
        
     * Jeśli wyczyścisz pole wyboru **Utwórz pojedyncze elementy robocze dla każdego elementu konfiguracji** , ITSMC utworzy jeden element roboczy dla każdej reguły alertu i doda do niego wszystkie elementy konfiguracji, których to dotyczy. Zostanie utworzony nowy element roboczy, jeśli poprzedni zostanie zamknięty.

       >[!NOTE]
       > W takim przypadku niektóre wyzwolone alerty nie generują nowych elementów roboczych w narzędziu narzędzia ITSM.

       Na przykład alert zawierający trzy elementy konfiguracji spowoduje utworzenie jednego elementu pracy. Jeśli alert dla tej samej reguły alertu, co w poprzednim przykładzie ma jeden element konfiguracji, ten element konfiguracji zostanie dołączony do listy elementów konfiguracji, których to dotyczy, w utworzonym elemencie roboczym. Alert dotyczący innej reguły alertu, która ma jeden element konfiguracji, utworzy jeden element roboczy.

   * Jeśli wybrano opcję **zdarzenie** na liście rozwijanej **element roboczy** , można utworzyć poszczególne elementy robocze dla każdego wpisu dziennika lub dla każdego elementu konfiguracji.
    
     ![Zrzut ekranu pokazujący obszar biletów I T S ze zdarzeniem wybranym jako element roboczy.](media/itsmc-overview/itsm-action-configuration-event.png)

     * W przypadku wybrania opcji **Utwórz poszczególne elementy robocze dla każdego wpisu dziennika (pole elementu konfiguracji nie jest wypełnione. Może spowodować powstanie dużej liczby elementów roboczych.)**, dla każdego wiersza zostanie utworzony element roboczy w wynikach wyszukiwania zapytania dotyczącego alertu wyszukiwania w dzienniku. Właściwość Description w ładunku elementu pracy będzie zawierać wiersz z wyników wyszukiwania.
      
     * W przypadku wybrania opcji **Utwórz poszczególne elementy robocze dla każdego elementu konfiguracji** każdy element konfiguracji w każdym alercie zostanie utworzony nowy element roboczy. Każdy element konfiguracji może mieć więcej niż jeden element roboczy w systemie narzędzia ITSM. Ta opcja jest taka sama jak zaznaczenie pola wyboru, które jest wyświetlane po wybraniu **zdarzenia** jako typ elementu pracy.

10. Wybierz przycisk **OK**.

Gdy tworzysz lub edytujesz regułę alertu platformy Azure, Użyj grupy akcji, która ma akcję narzędzia ITSM. Po wyzwoleniu alertu element roboczy jest tworzony lub aktualizowany w narzędziu narzędzia ITSM.

> [!NOTE]
> Informacje o cenach akcji narzędzia ITSM można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/monitor/) dla grup akcji.
>
> Pole Krótki opis w definicji reguły alertu jest ograniczone do 40 znaków podczas wysyłania ich przy użyciu akcji narzędzia ITSM.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z ITSMC](./itsmc-resync-servicenow.md)
