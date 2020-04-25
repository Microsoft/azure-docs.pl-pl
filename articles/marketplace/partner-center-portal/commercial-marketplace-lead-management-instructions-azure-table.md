---
title: Konfigurowanie komercyjnego zarządzania liderem portalu Marketplace firmy Microsoft przy użyciu tabeli platformy Azure
description: Dowiedz się, jak używać tabeli platformy Azure do zarządzania potencjalni klienci z Microsoft AppSource i witryny Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 9814b03e348fc807c04364afbf027369f917670a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131141"
---
# <a name="configure-lead-management-by-using-an-azure-table"></a>Konfigurowanie zarządzania liderem przy użyciu tabeli platformy Azure

Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w centrum partnerskim, aby otrzymywać Microsoft AppSource i potencjalni klienci portalu Azure Marketplace, możesz użyć tabeli platformy Azure, aby obsłużyć te potencjalni klienci. Następnie można wyeksportować dane i zaimportować je do systemu CRM. Instrukcje zawarte w tym artykule przeprowadzą Cię przez proces tworzenia konta usługi Azure Storage i tabeli platformy Azure w ramach tego konta. Ponadto możesz utworzyć nowy przepływ przy użyciu narzędzia do automatyzacji, aby wysłać powiadomienie e-mail, gdy oferta odbierze potencjalny klient.

## <a name="configure-an-azure-table"></a>Konfigurowanie tabeli platformy Azure

1. Jeśli nie masz konta platformy Azure, możesz [utworzyć bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
1. Po uaktywnieniu konta platformy Azure Zaloguj się do [Azure Portal](https://portal.azure.com).
1. W Azure Portal utworzyć konto magazynu przy użyciu następującej procedury:

    1. Wybierz pozycję **+ Utwórz zasób** na pasku menu po lewej stronie. **Nowe** okienko pojawia się po prawej stronie.
    1. Wybierz pozycję **Magazyn** w **nowym** okienku. Lista **Proponowana** pojawia się po prawej stronie.
    1. Wybierz pozycję **konto magazynu** , aby rozpocząć tworzenie konta. Postępuj zgodnie z instrukcjami w temacie [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Procedura tworzenia konta usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Aby uzyskać więcej informacji na temat kont magazynu, zobacz [samouczek szybki start](https://docs.microsoft.com/azure/storage/). Aby uzyskać więcej informacji na temat cennika usługi Storage, zobacz [Cennik usługi Storage](https://azure.microsoft.com/pricing/details/storage/).

1. Zaczekaj na zainicjowanie obsługi konta magazynu. Ten proces zazwyczaj trwa kilka minut. Następnie uzyskaj dostęp do konta magazynu ze strony **głównej** Azure Portal, wybierając pozycję **Zobacz wszystkie zasoby**. Możesz również wybrać pozycję **wszystkie zasoby** na pasku menu po lewej stronie Azure Portal.

    ![Dostęp do konta usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

1. W okienku konto magazynu wybierz pozycję **klucze dostępu** i skopiuj wartość **Parametry połączenia** dla klucza. Zapisz tę wartość, ponieważ jest to wartość **parametrów połączenia konta magazynu** , która będzie potrzebna w portalu wydawców w celu otrzymywania potencjalnych klientów na potrzeby oferty platformy Azure Marketplace. 

    Oto przykład parametrów połączenia.

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    ![Klucz usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

1. W okienku konto magazynu wybierz pozycję **tabele**, a następnie wybierz pozycję **+ tabela** , aby utworzyć tabelę. Wprowadź nazwę tabeli i wybierz **przycisk OK**. Zapisz tę wartość, ponieważ będzie ona potrzebna, jeśli chcesz skonfigurować przepływ do odbierania powiadomień e-mail po odebraniu potencjalnych klientów.

    ![Tabele platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Możesz użyć [Eksplorator usługi Azure Storage](https://archive.codeplex.com/?p=azurestorageexplorer) lub dowolnego innego narzędzia, aby wyświetlić dane w tabeli magazynu. Możesz również eksportować dane w tabeli platformy Azure. 

## <a name="optional-use-power-automate-with-an-azure-table"></a>Obowiązkowe Używanie automatyzacji zaawansowanej z tabelą platformy Azure 

Możesz użyć [automatyzacji](https://docs.microsoft.com/flow/) , aby zautomatyzować powiadomienia za każdym razem, gdy potencjalny klient zostanie dodany do tabeli platformy Azure. Jeśli nie masz konta, możesz [zarejestrować się w celu uzyskania bezpłatnego konta](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Przykład powiadomienia lidera

Ten przykład służy jako przewodnik tworzenia prostego przepływu, który automatycznie wysyła powiadomienie e-mail po dodaniu nowego potencjalnego klienta do tabeli platformy Azure. W tym przykładzie skonfigurowano cykl do wysyłania informacji o potencjalnym kliencie co godzinę w przypadku zaktualizowania magazynu tabel.

1. Zaloguj się do swojego konta automatyzacji.
1. Na pasku po lewej stronie wybierz pozycję **Moje przepływy**.
1. Na górnym pasku wybierz pozycję **+ Nowy**. 
1. Z listy rozwijanej wybierz pozycję **+ zaplanowane — od pustej**.

   ![Moje przepływy + zaplanowano — z pustej](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. W oknie **kompilacja zaplanowanego przepływu** dla opcji **Powtarzaj w każdym**przypadku wybierz wartość **1** dla interwału i **godziny** dla częstotliwości. Ponadto Nadaj nazwę przepływowi, jeśli chcesz. Wybierz pozycję **Utwórz**.

   >[!NOTE]
   >Mimo że w tym przykładzie używany jest interwał jednogodzinny, można wybrać interwał i częstotliwość, które najlepiej odpowiadają potrzebom biznesowym.

   ![Kompilowanie zaplanowanego przepływu](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. Wybierz pozycję **+ Nowy krok**.
1. W oknie **Wybierz akcję** Wyszukaj ciąg **uzyskaj czas**ostatniej operacji. Następnie w obszarze **Akcje**wybierz pozycję **Pobierz czas ostatniej operacji**.

   ![Wybierz akcję](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. W oknie **Pobierz czas** ostatniej Ustaw **Interwał** na **1**. Z listy rozwijanej **jednostka czasu** wybierz pozycję Hour ( **godzina**).

    >[!IMPORTANT]
    >Upewnij się, że interwał i jednostka czasu wysłana w kroku 8 są zgodne z interwałem i częstotliwością skonfigurowaną na potrzeby cyklu w kroku 5.

    ![Ustaw przedział czasu pobierania](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP] 
   >W każdej chwili można sprawdzić, czy każdy krok jest prawidłowo skonfigurowany. Aby sprawdzić przepływ, wybierz pozycję **moduł sprawdzania przepływu** na pasku menu **przepływ** .

   W następnym zestawie kroków zostanie nawiązane połączenie z tabelą platformy Azure i skonfigurowanie logiki przetwarzania w celu obsługi nowych potencjalnych klientów.

1. Po kroku 8 wybierz pozycję **+ nowy krok**. Następnie wyszukaj pozycję **Pobierz jednostki** w oknie **Wybieranie akcji** .
1. W obszarze **Akcje**wybierz pozycję **Pobierz jednostki (Azure Table Storage)**.
1. W oknie **Table Storage platformy Azure** podaj informacje dla następujących pól i wybierz pozycję **Utwórz**:

    * **Nazwa połączenia**: Podaj zrozumiałą nazwę połączenia, które tworzysz między tym przepływem a tabelą platformy Azure.
    * **Nazwa konta magazynu**: Podaj nazwę konta magazynu dla swojej tabeli platformy Azure. Tę nazwę można znaleźć na stronie **klucze dostępu** konta magazynu.
    * **Współużytkowany klucz magazynu**: Podaj wartość klucza dla konta magazynu w tabeli platformy Azure. Tę wartość można znaleźć na stronie **klucze dostępu** konta magazynu.

      ![Okno Table Storage platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   Po wybraniu opcji **Utwórz**zostanie wyświetlone okno **pobieranie jednostek** . W tym miejscu wybierz pozycję **Pokaż opcje zaawansowane**i podaj informacje dla następujących pól:

   * **Tabela**: wybierz nazwę magazynu tabel platformy Azure (z kroku 6 instrukcji w sekcji "Konfigurowanie tabeli platformy Azure"). Na poniższej ilustracji przedstawiono monit, gdy w tym przykładzie wybrano tabelę "marketplaceleads".

     ![Pobierz okno jednostek](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **Zapytanie filtru**: zaznacz to pole i wklej tę funkcję do pola:`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![Pobierz jednostki, Filtrowanie pól zapytania](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. Po zakończeniu konfigurowania połączenia z tabelą platformy Azure wybierz pozycję **nowy krok** , aby dodać warunek skanowania tabeli platformy Azure pod kątem nowych potencjalnych klientów.

1. W oknie **Wybierz akcję** wybierz pozycję **Akcje**. Następnie wybierz pozycję **Kontrola warunku**.

    ![Wybierz okno akcji](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. W oknie **warunek** wybierz pozycję **Wybierz wartość**. Następnie wybierz pozycję **wyrażenie** w oknie podręcznym.

1. Wklej `length(body('Get_entities')?['value'])` do pola **FX** . Wybierz **przycisk OK** , aby dodać tę funkcję. 

1. Aby zakończyć konfigurowanie warunku:
    1. Opcja Wybierz **jest większa niż** wartość z listy rozwijanej.
    2. Wprowadź **0** jako wartość.

        ![Okno warunku](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   W następnych kilku krokach skonfigurujesz akcję do wykonania w oparciu o wynik warunku:

   * Jeśli warunek jest rozpoznawany jako **nie**, nic nie rób.
   * Jeśli warunek ma **wartość tak**, wyzwól akcję, która łączy konto Office 365 w celu wysłania wiadomości e-mail. 

1. Wybierz pozycję **Dodaj akcję** w obszarze **Jeśli tak**.

    ![Okno warunku, jeśli tak, Dodaj akcję](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. Wybierz pozycję **Wyślij wiadomość e-mail (Office 365 Outlook)**.

    ![Okno warunku, jeśli tak, Wyślij wiadomość e-mail](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >Aby użyć innego dostawcy poczty e-mail, Wyszukaj i wybierz pozycję **Wyślij powiadomienie e-mail (poczta)** jako akcję. Instrukcje pokazują, jak skonfigurować program przy użyciu pakietu Office 365 Outlook, ale instrukcje są podobne dla innego dostawcy poczty e-mail.

1. W oknie Office 365 Outlook podaj informacje dla następujących pól:

    1. **Do**: wprowadź adres e-mail dla wszystkich użytkowników, którzy otrzymają to powiadomienie.
    1. **Temat**: Podaj temat wiadomości e-mail. Przykładem są **nowi potencjalni klienci!**
    1. **Treść**: Dodaj tekst, który ma zostać uwzględniony w każdej wiadomości e-mail (opcjonalnie), a następnie wklej `body('Get_entities')?['value']`w.

    >[!NOTE]
    >Do treści tej wiadomości e-mail można wstawić dodatkowe statyczne lub dynamiczne punkty danych.

    ![Okno warunku, jeśli tak, okno Office 365 Outlook](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. Aby zapisać zmiany, wybierz opcję **Zapisz**. Automatyzuje automatyczne testowanie przepływu pod kątem błędów. Jeśli nie ma żadnych błędów, przepływ zostanie uruchomiony po jego zapisaniu.

Na poniższej ilustracji przedstawiono przykład sposobu, w jaki powinien wyglądać końcowy przepływ.

![Przykład przepływu końcowego](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Zarządzanie przepływem

Zarządzanie przepływem jest proste. Masz pełną kontrolę nad przepływem. Można na przykład ją zatrzymać, edytować, wyświetlić historię uruchamiania i uzyskać analizę. Na poniższej ilustracji przedstawiono dostępne opcje zarządzania przepływem.

 ![Zarządzanie przepływem](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Przepływ pozostaje uruchomiony do momentu zatrzymania go przy użyciu opcji **Wyłącz przepływ** .

Jeśli nie otrzymasz żadnych powiadomień e-mail dotyczących potencjalnych klientów, oznacza to, że nowi potencjalni klienci nie zostali dodani do tabeli platformy Azure. Jeśli wystąpią jakieś błędy przepływu, otrzymasz wiadomość e-mail podobną do tego przykładu.

 ![Powiadomienie e-mail o niepowodzeniu przepływu](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące kroki.

1. Przejdź do strony **konfiguracji oferty** oferty.
1. Wybierz pozycję **Połącz** w sekcji **Zarządzanie potencjalnymi klientami** .
1. W oknie podręcznym **szczegóły połączenia** wybierz pozycję **tabela Azure** dla **miejsca docelowego potencjalnego klienta**. Wklej parametry połączenia z konta usługi Azure Storage utworzonego w ramach wcześniejszych kroków w polu **Parametry połączenia konta magazynu** .
1. **Kontaktowy adres e-mail**: Podaj wiadomości e-mail dla osób w firmie, które powinny otrzymywać powiadomienia pocztą email po odebraniu nowego potencjalnego klienta. Można podać wiele wiadomości e-mail, rozdzielając je średnikami.
1. Kliknij przycisk **OK**.

Aby upewnić się, że pomyślnie nawiązano połączenie z miejscem docelowym potencjalnego klienta, wybierz przycisk **Weryfikuj** . Jeśli się powiedzie, będziesz mieć test w miejscu docelowym potencjalnego klienta.

>[!NOTE]
>Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.

Gdy są generowane potencjalni klienci, firma Microsoft wysyła potencjalni klienci do tabeli platformy Azure. W przypadku skonfigurowania przepływu poczta e-mail również zostanie wysłana na skonfigurowany adres e-mail.

![Zarządzanie potencjalnymi klientami](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Zarządzanie potencjalnymi klientami, szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Zarządzanie potencjalnymi klientami, konto magazynu szczegółów połączenia](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

