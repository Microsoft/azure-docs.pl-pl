---
title: łącznik zarządzania usługami IT w Log Analytics
description: Ten artykuł zawiera omówienie łącznik zarządzania usługami IT (ITSMC) i informacje na temat używania go do monitorowania i zarządzania elementami roboczymi narzędzia ITSM w programie Log Analytics i szybkiego rozwiązywania problemów.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: b4e35296a999070a6f536f4a52cfb7c3a1d4f42a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186494"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Połącz platformę Azure z narzędziami narzędzia ITSM przy użyciu łącznik zarządzania usługami IT

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Łącznik zarządzania usługami IT (ITSMC) umożliwia połączenie platformy Azure z obsługiwanym produktem lub usługą zarządzania usługami IT (narzędzia ITSM).

Usługi platformy Azure, takie jak Azure Log Analytics i Azure Monitor, udostępniają narzędzia umożliwiające wykrywanie, analizowanie i rozwiązywanie problemów z zasobami platformy Azure i spoza niej. Jednak elementy robocze związane z problemem zwykle znajdują się w narzędzia ITSM produktu lub usłudze. ITSMC zapewnia dwukierunkową połączenie między narzędziami platformy Azure i narzędzia ITSM, które ułatwiają szybsze rozwiązywanie problemów.

ITSMC obsługuje połączenia z następującymi narzędziami narzędzia ITSM:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Od 1 do 2020 Cherwell i Provance narzędzia ITSM integracji z usługą Azure alert nie będą już dostępne dla nowych klientów. Nowe połączenia narzędzia ITSM nie będą obsługiwane. Istniejące połączenia narzędzia ITSM będą obsługiwane.

Za pomocą ITSMC można:

-  Utwórz elementy robocze w narzędziu narzędzia ITSM na podstawie alertów platformy Azure (alertów metryk, alertów dziennika aktywności i alertów Log Analytics).
-  Opcjonalnie możesz zsynchronizować zdarzenie i dane żądania zmiany z narzędzia Narzędzia ITSM w obszarze roboczym usługi Azure Log Analytics.

Aby uzyskać informacje na temat warunków prawnych i zasad zachowania poufności informacji, zobacz zasady [zachowania poufności informacji firmy Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Możesz rozpocząć korzystanie z ITSMC, wykonując następujące czynności:

1.  [Dodaj ITSMC.](#add-it-service-management-connector)
2.  [Utwórz połączenie narzędzia ITSM.](#create-an-itsm-connection)
3.  [Użyj połączenia.](#use-itsmc)


##  <a name="add-it-service-management-connector"></a>Dodaj łącznik zarządzania usługami IT

Aby można było utworzyć połączenie, należy dodać ITSMC.

1. W Azure Portal wybierz pozycję **Utwórz zasób**:

   ![Zrzut ekranu pokazujący element menu Utwórz zasób.](media/itsmc-overview/azure-add-new-resource.png)

2. Wyszukaj **Łącznik zarządzania usługami IT** w witrynie Azure Marketplace. Wybierz pozycję **Utwórz**:

   ![Zrzut ekranu przedstawiający przycisk Utwórz w portalu Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. W sekcji **obszar roboczy pakietu OMS** wybierz obszar roboczy usługi Azure log Analytics, w którym chcesz zainstalować ITSMC.
   >[!NOTE]
   > * W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor, obszary robocze OMS są teraz określane jako *log Analytics obszary robocze*.
   > * ITSMC można zainstalować tylko w obszarze roboczym Log Analytics w następujących regionach: Wschodnie stany USA, zachodnie stany USA 2, Południowo-środkowe stany USA, zachodnie stany USA, US Gov Arizona, US Gov Wirginia, Kanada środkowa, Europa Zachodnia, Południowe Zjednoczone Królestwo, Azja Południowo-Wschodnia, Japonia Wschodniej, Indie Środkowe i Australia Południowo-Wschodnia.


4. W sekcji **obszar roboczy log Analytics** wybierz grupę zasobów, w której chcesz utworzyć zasób ITSMC:

   ![Zrzut ekranu przedstawiający sekcję Log Analytics obszaru roboczego.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >W ramach trwającego przejścia z Microsoft Operations Management Suite (OMS) do Azure Monitor, obszary robocze OMS są teraz określane jako *log Analytics obszary robocze*.

5. Wybierz przycisk **OK**.

Po wdrożeniu zasobu ITSMC w prawym górnym rogu okna pojawi się powiadomienie.


## <a name="create-an-itsm-connection"></a>Utwórz połączenie narzędzia ITSM

Po zainstalowaniu programu ITSMC można utworzyć połączenie.

Aby utworzyć połączenie, należy wykonać odpowiednie przygotowanie narzędzia Narzędzia ITSM, aby umożliwić połączenie z ITSMC.  

W oparciu o produkt narzędzia ITSM, z którym nawiązujesz połączenie, wybierz jedną z poniższych linków, aby uzyskać instrukcje:

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Po jego narzędzi Narzędzia ITSM wykonaj następujące kroki, aby utworzyć połączenie:

1. W obszarze **wszystkie zasoby** Znajdź pozycję **Servicedesk (*Nazwa obszaru roboczego*)**:

   ![Zrzut ekranu pokazujący najnowsze zasoby w Azure Portal.](media/itsmc-overview/itsm-connections.png)

1. W obszarze **źródła danych obszaru roboczego** w okienku po lewej stronie wybierz pozycję **połączenia narzędzia ITSM**:

   ![Zrzut ekranu pokazujący element menu Narzędzia ITSM Connections.](media/itsmc-overview/add-new-itsm-connection.png)
   Na tej stronie zostanie wyświetlona lista połączeń.
1. Wybierz pozycję **Dodaj połączenie**.

4. Określ ustawienia połączenia zgodnie z opisem w temacie [Konfigurowanie połączenia usługi ITSMC z produktami/usługami narzędzia ITSM](./itsmc-connections.md).

   > [!NOTE]
   >
   > Domyślnie program ITSMC odświeża dane konfiguracji połączenia co 24 godziny. Aby natychmiast odświeżyć dane połączenia w celu odzwierciedlenia wszelkich edytowanych lub wprowadzonych aktualizacji szablonów, wybierz przycisk **Synchronizuj** w bloku połączenia:
   >
   > ![Zrzut ekranu pokazujący przycisk Synchronizuj w bloku połączenie.](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>Użyj ITSMC
   Za pomocą ITSMC można tworzyć elementy robocze z alertów platformy Azure, alertów Log Analytics i Log Analytics rekordów dzienników.

## <a name="template-definitions"></a>Definicje szablonów
   Istnieją typy elementów roboczych, które mogą używać szablonów, które są zdefiniowane przez narzędzie narzędzia ITSM.
Za pomocą szablonów można zdefiniować pola, które będą automatycznie wypełniane zgodnie z ustalonymi wartościami, które są zdefiniowane jako część grupy akcji. Szablony można definiować w narzędziu narzędzia ITSM. Można zdefiniować szablon, który ma być używany jako część definicji grupy akcji.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Tworzenie narzędzia ITSM elementów roboczych na podstawie alertów platformy Azure

Po utworzeniu połączenia narzędzia ITSM można tworzyć elementy robocze w narzędziu narzędzia ITSM w oparciu o alerty platformy Azure. Aby utworzyć elementy robocze, użyj akcji narzędzia ITSM w grupie akcji.

Grupy akcji umożliwiają modularne i wielokrotne użycie metody wyzwalania akcji dla alertów platformy Azure. Za pomocą grup akcji można korzystać z alertów metryk, alertów dziennika aktywności i alertów usługi Azure Log Analytics w Azure Portal.

> [!NOTE]
> Po utworzeniu połączenia narzędzia ITSM należy poczekać 30 minut na zakończenie procesu synchronizacji.
> 

Użyj następującej procedury, aby utworzyć elementy robocze:

1. W Azure Portal wybierz pozycję  **alerty**.
2. W menu w górnej części ekranu wybierz pozycję **Zarządzaj akcjami**:

    ![Zrzut ekranu pokazujący element menu Zarządzaj akcjami.](media/itsmc-overview/action-groups-selection-big.png)

   Zostanie wyświetlone okno **Tworzenie grupy akcji** .

3. Wybierz **subskrypcję** i **grupę zasobów** , w której chcesz utworzyć grupę akcji. Podaj **nazwę grupy akcji** i **nazwę wyświetlaną** dla grupy akcji. Wybierz pozycję **Dalej: powiadomienia**.

    ![Zrzut ekranu przedstawiający okno Tworzenie grupy akcji.](media/itsmc-overview/action-groups-details.png)

4. Na liście powiadomień wybierz pozycję **Dalej: akcje**.
5. Na liście Akcje wybierz pozycję **Narzędzia ITSM** na liście **Typ akcji** . Podaj **nazwę** akcji. Wybierz przycisk pióra reprezentujący **szczegóły edycji**.
6. Na liście **subskrypcja** wybierz subskrypcję, w której znajduje się obszar roboczy log Analytics. Na liście **połączenie** wybierz swoją nazwę łącznika narzędzia ITSM. Zostanie nadana nazwa obszaru roboczego. Na przykład MyITSMConnector (mój obszar roboczy).

7. Wybierz typ **elementu pracy** .

8. Jeśli chcesz wypełnić pola z ustalonymi wartościami, wybierz opcję **Użyj szablonu niestandardowego**. W przeciwnym razie wybierz istniejący [szablon](#template-definitions) na liście **szablon** i wprowadź wartości ustalone w polach szablon.

9. W przypadku wybrania opcji **Utwórz poszczególne elementy robocze dla każdego elementu konfiguracji** każdy element konfiguracji będzie miał własny element roboczy. Dla każdego elementu konfiguracji będzie istniał jeden element roboczy. Zostanie ona zaktualizowana zgodnie z alertami, które zostaną utworzone.

   * W przypadku wybrania opcji na liście rozwijanej element roboczy "zdarzenie" lub "Alert": w przypadku wyczyszczenia pola wyboru **Utwórz poszczególne elementy robocze dla każdego elementu konfiguracji** każdy alert utworzy nowy element roboczy. Może istnieć więcej niż jeden alert dla każdego elementu konfiguracji.

   ![Zrzut ekranu przedstawiający okno biletu narzędzia ITSM.](media/itsmc-overview/itsm-action-configuration.png)
   
   * W przypadku wybrania opcji "zdarzenie" w menu rozwijanym elementu pracy: w przypadku wybrania opcji **Utwórz poszczególne elementy robocze dla każdego wpisu dziennika** w wyborze przycisków radiowych każdy alert utworzy nowy element roboczy. W przypadku wybrania opcji **Utwórz indywidualne elementy robocze dla każdego elementu konfiguracji** w zaznaczeniu przycisków radiowych każdy element konfiguracji będzie miał własny element roboczy.
   ![Zrzut ekranu przedstawiający okno biletu narzędzia ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Wybierz przycisk **OK**.

Gdy tworzysz lub edytujesz regułę alertu platformy Azure, Użyj grupy akcji, która ma akcję narzędzia ITSM. Po wyzwoleniu alertu element roboczy jest tworzony lub aktualizowany w narzędziu narzędzia ITSM.

> [!NOTE]
>
>- Informacje o cenach akcji narzędzia ITSM można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/monitor/) dla grup akcji.
>
>
>- Pole Krótki opis w definicji reguły alertu jest ograniczone do 40 znaków podczas wysyłania ich przy użyciu akcji narzędzia ITSM.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Wizualizuj i analizuj dane zdarzenia i żądania zmiany

W zależności od konfiguracji podczas konfigurowania połączenia ITSMC może synchronizować do 120 dni zdarzenia i dane żądania zmiany. Schemat rekordów dziennika dla tych danych znajduje się w [następnej sekcji](#additional-information) tego artykułu.

Możesz wizualizować dane zdarzenia i żądania zmiany za pomocą pulpitu nawigacyjnego ITSMC:

![Zrzut ekranu przedstawiający pulpit nawigacyjny ITSMC.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Pulpit nawigacyjny zawiera również informacje o stanie łącznika, którego można użyć jako punktu wyjścia do analizowania problemów z połączeniami.

Możesz również wizualizować zdarzenia zsynchronizowane z komputerami, których dotyczy problem, w Service Map.

Service Map automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Umożliwia wyświetlanie Twoich serwerów w miarę ich działania: jako połączone systemy, które dostarczają usługi o kluczowym znaczeniu. Service Map przedstawia połączenia między serwerami, procesami i portami w ramach dowolnej architektury połączonej z protokołem TCP. Poza instalacją agenta nie jest wymagana żadna konfiguracja. Aby uzyskać więcej informacji, zobacz [używanie Service map](../insights/service-map.md).

Jeśli używasz Service Map, możesz wyświetlić elementy pomocy technicznej utworzone w rozwiązaniach narzędzia ITSM, jak pokazano poniżej:

![Zrzut ekranu przedstawiający ekran Log Analytics.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


## <a name="additional-information"></a>Dodatkowe informacje

### <a name="data-synced-from-your-itsm-product"></a>Dane zsynchronizowane z produktu narzędzia ITSM
Zdarzenia i żądania zmiany są synchronizowane z poziomu produktu narzędzia ITSM do obszaru roboczego Log Analytics w oparciu o konfigurację połączenia.

W tej sekcji przedstawiono kilka przykładów danych zebranych przez ITSMC.

Pola w **ServiceDesk_CL** różnią się w zależności od typu elementu pracy, który można zaimportować do log Analytics. Poniżej znajduje się lista pól dla dwóch typów elementów roboczych:

**Element roboczy:** **zdarzenia**  
ServiceDeskWorkItemType_s = "zdarzenie"

**Pola**

- ServiceDeskConnectionName
- Identyfikator działu obsługi
- Stan
- Pilność
- Wpływ
- Priorytet
- Eskalacja
- Created By
- Rozwiązane przez
- Zamknięte przez
- Element źródłowy
- Przypisano do
- Kategoria
- Tytuł
- Opis
- Data utworzenia
- Data zamknięcia
- Data rozwiązania
- Data ostatniej modyfikacji
- Computer (Komputer)


**Element roboczy:** **żądania zmiany**

ServiceDeskWorkItemType_s = "ChangeRequest"

**Pola**
- ServiceDeskConnectionName
- Identyfikator działu obsługi
- Created By
- Zamknięte przez
- Element źródłowy
- Przypisano do
- Tytuł
- Typ
- Kategoria
- Stan
- Eskalacja
- Stan konfliktu
- Pilność
- Priorytet
- Ryzyko
- Wpływ
- Przypisano do
- Data utworzenia
- Data zamknięcia
- Data ostatniej modyfikacji
- Żądana Data
- Planowana data rozpoczęcia
- Planowana data zakończenia
- Data rozpoczęcia pracy
- Data zakończenia pracy
- Opis
- Computer (Komputer)

## <a name="output-data-for-a-servicenow-incident"></a>Dane wyjściowe dla zdarzenia usługi ServiceNow

| Pole Log Analytics | Pole usługi ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| IncidentState_s | Stan |
| Urgency_s |Pilność |
| Impact_s |Wpływ|
| Priority_s | Priorytet |
| CreatedBy_s | Otwarte przez |
| ResolvedBy_s | Rozwiązane przez|
| ClosedBy_s  | Zamknięte przez |
| Source_s| Typ kontaktu |
| AssignedTo_s | Przypisano do  |
| Category_s | Kategoria |
| Title_s|  Krótki opis |
| Description_s|  Uwagi |
| CreatedDate_t|  Otworzyć |
| ClosedDate_t| zamknięte|
| ResolvedDate_t|Resolved|
| Computer (Komputer)  | Pozycja konfiguracji |

## <a name="output-data-for-a-servicenow-change-request"></a>Dane wyjściowe żądania zmiany usługi ServiceNow

| Log Analytics | Pole usługi ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Liczba |
| CreatedBy_s | Żądane przez |
| ClosedBy_s | Zamknięte przez |
| AssignedTo_s | Przypisano do  |
| Title_s|  Krótki opis |
| Type_s|  Typ |
| Category_s|  Kategoria |
| CRState_s|  Stan|
| Urgency_s|  Pilność |
| Priority_s| Priorytet|
| Risk_s| Ryzyko|
| Impact_s| Wpływ|
| RequestedDate_t  | Żądane w dniu |
| ClosedDate_t | Data zamknięcia |
| PlannedStartDate_t  |     Planowana data rozpoczęcia |
| PlannedEndDate_t  |   Planowana data zakończenia |
| WorkStartDate_t  | Rzeczywista data rozpoczęcia |
| WorkEndDate_t | Rzeczywista data zakończenia|
| Description_s | Opis |
| Computer (Komputer)  | Element konfiguracji |


## <a name="troubleshoot-itsm-connections"></a>Rozwiązywanie problemów z połączeniami narzędzia ITSM
- Jeśli połączenie nie powiedzie się z interfejsu użytkownika połączonego źródła i **wystąpi błąd podczas zapisywania komunikatu połączenia** , wykonaj następujące czynności:
   - Dla połączeń usługi ServiceNow, Cherwell i Provance:  
     - Upewnij się, że poprawnie wprowadzono nazwę użytkownika, hasło, identyfikator klienta i klucz tajny klienta dla każdego połączenia.  
     - Upewnij się, że masz wystarczające uprawnienia do odpowiedniego produktu narzędzia ITSM, aby nawiązać połączenie.  
   - Dla połączeń Service Manager:  
     - Upewnij się, że aplikacja sieci Web została pomyślnie wdrożona, a połączenie hybrydowe zostało utworzone. Aby sprawdzić, czy połączenie zostało pomyślnie nawiązane z lokalnym komputerem Service Manager, przejdź do adresu URL aplikacji sieci Web, zgodnie z opisem w dokumentacji dotyczącej tworzenia [połączenia hybrydowego](./itsmc-connections.md#configure-the-hybrid-connection).  

- Jeśli dane z usługi ServiceNow nie są synchronizowane do Log Analytics, upewnij się, że wystąpienie usługi ServiceNow nie jest w stanie uśpienia. Usługi ServiceNow dev Instances czasami przechodzi do trybu uśpienia, gdy są bezczynne przez długi czas. Jeśli to się nie dzieje, zgłoś problem.
- Jeśli Log Analytics alerty są wyzwalane, ale elementy robocze nie są tworzone w produkcie narzędzia ITSM, jeśli elementy konfiguracji nie są tworzone/połączone z elementami roboczymi lub innych informacji, zobacz następujące zasoby:
   -  ITSMC: rozwiązanie pokazuje podsumowanie połączeń, elementów roboczych, komputerów i nie tylko. Wybierz kafelek z etykietą **stanu łącznika** . Wykonanie tej czynności spowoduje przeprowadzenie **rejestrowania wyszukiwania** przy użyciu odpowiedniego zapytania. `LogType_S`Aby uzyskać więcej informacji, zobacz rekordy dziennika z a `ERROR` .
   - Strona **przeszukiwania dzienników** : Wyświetl błędy i powiązane informacje bezpośrednio przy użyciu zapytania `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Rozwiązywanie problemów z wdrażaniem aplikacji sieci Web Service Manager
-   Jeśli masz problemy z wdrażaniem aplikacji sieci Web, upewnij się, że masz uprawnienia do tworzenia/wdrażania zasobów w ramach subskrypcji.
-   Jeśli podczas uruchamiania [skryptu](itsmc-service-manager-script.md)otrzymasz **odwołanie do obiektu, które nie jest ustawione na wystąpienie błędu obiektu** , upewnij się, że wprowadzono prawidłowe wartości w sekcji **Konfiguracja użytkownika** .
-   Jeśli nie można utworzyć przestrzeni nazw usługi Service Bus Relay, upewnij się, że wymagany dostawca zasobów jest zarejestrowany w subskrypcji. Jeśli nie jest ona zarejestrowana, należy ręcznie utworzyć przestrzeń nazw usługi Service Bus Relay z Azure Portal. Można go również utworzyć podczas [tworzenia połączenia hybrydowego](./itsmc-connections.md#configure-the-hybrid-connection) w Azure Portal.


## <a name="contact-us"></a>Skontaktuj się z nami

Jeśli masz zapytania lub opinie na temat łącznik zarządzania usługami IT, skontaktuj się z nami pod adresem [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Następne kroki
[Dodaj narzędzia ITSM produkty/usługi do łącznik zarządzania usługami IT](./itsmc-connections.md)