---
title: Azure Table Storage | Portal Azure Marketplace
description: Konfigurowanie zarządzania liderem w usłudze Azure Table Storage.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280358"
---
# <a name="lead-management-instructions-for-table-storage"></a>Instrukcje dotyczące zarządzania potencjalnymi klientami dla usługi Table Storage

W tym artykule opisano sposób konfigurowania usługi Azure Table Storage w celu zarządzania potencjalnymi klientami sprzedaży. Magazyn tabel umożliwia przechowywanie i modyfikowanie informacji o klientach.

## <a name="configure-table-storage"></a>Konfigurowanie magazynu tabel

1. Jeśli nie masz konta platformy Azure, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
1. Po uaktywnieniu konta zaloguj się do [Azure Portal](https://portal.azure.com).
1. W Azure Portal wykonaj następujące kroki:  
    1. Wybierz pozycję **+ Utwórz zasób** w okienku po lewej stronie. Zostanie otwarte **nowe** okienko.
    1. W okienku **Nowy** wybierz pozycję **Magazyn**. Lista **Proponowana** zostanie otwarta po prawej stronie.
    1. Wybierz pozycję **konto magazynu**. Następnie postępuj zgodnie z instrukcjami w obszarze [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Tworzenie konta usługi Azure Storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Aby uzyskać więcej informacji na temat kont magazynu, zobacz [samouczki szybkiego startu](https://docs.microsoft.com/azure/storage/). Aby uzyskać informacje o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

1. Zaczekaj na zainicjowanie obsługi konta magazynu, co zazwyczaj trwa kilka minut. Następnie uzyskaj dostęp do konta ze strony głównej Azure Portal: wybierz pozycję **Zobacz wszystkie zasoby** lub **wszystkie zasoby** w okienku nawigacji.

    ![Dostęp do konta usługi Azure Storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. W okienku konto magazynu Skopiuj parametry połączenia konta magazynu dla klucza. Wklej ją w polu **Parametry połączenia** dla konta magazynu w Portal Cloud partner.

    Przykładowe parametry połączenia:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Klucz usługi Azure Storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Aby wyświetlić dane w magazynie tabel, można użyć [Eksplorator usługi Azure Storage](https://azurestorageexplorer.codeplex.com/) lub podobnego narzędzia. Można również eksportować dane z tego programu.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Używanie Microsoft Flow z usługą Table Storage (*Opcjonalnie*)

Za pomocą [Microsoft Flow](https://docs.microsoft.com/flow/) można automatycznie wysyłać powiadomienia, gdy potencjalny klient zostanie dodany do magazynu tabel. Jeśli nie masz konta Microsoft Flow, [zarejestruj się, aby skorzystać z bezpłatnego konta](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Przykład powiadomienia lidera

Ten przykład pokazuje, jak utworzyć przepływ podstawowy. Przepływ automatycznie wysyła powiadomienie e-mail o godzinie, gdy nowi potencjalni klienci zostaną dodani do magazynu tabel.

1. Zaloguj się do konta Microsoft Flow.
1. W okienku nawigacji po lewej stronie wybierz pozycję **Moje przepływy**.
1. Na górnym pasku nawigacyjnym wybierz pozycję **+ Nowy**.  
1. Z listy rozwijanej wybierz pozycję **+ Utwórz z pustego**.
1. W obszarze **Utwórz przepływ od pustego**wybierz pozycję **Utwórz z pustego**.

   ![Utwórz nowy przepływ z pustego](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Na stronie wyszukiwanie łączników i wyzwalaczy wybierz pozycję **wyzwalacze**.
1. W obszarze **wyzwalacze**wybierz pozycję **cykl**.
1. W oknie **cykl** należy zachować domyślne ustawienie wartości **1** dla **interwału**. Z listy rozwijanej **częstotliwość** wybierz pozycję **godzina**.

   >[!NOTE] 
   >Ten przykład używa interwału jednogodzinnego. Można jednak wybrać interwał i częstotliwość, które najlepiej odpowiadają potrzebom biznesowym.

   ![Ustawianie częstotliwości dla cyklu 1 godziny](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Wybierz pozycję **+ nowy krok**.
1. Wyszukaj ciąg **uzyskaj czas**ostatniej reakcji, a następnie wybierz pozycję **Pobierz termin** w obszarze **Wybierz akcję**.

    ![Znajdź i wybierz akcję "Pobierz przeszły czas"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. W oknie **Pobierz czas** ostatniej Ustaw **Interwał** na **1**.  Z listy rozwijanej **jednostka czasu** wybierz pozycję Hour ( **godzina**).
    >[!IMPORTANT] 
    >Upewnij się, że **Interwał** i **jednostka czasu** są zgodne z interwałem i częstotliwością skonfigurowaną na potrzeby cyklu (krok 8).

    ![Ustawianie interwału czasu pobierania](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Możesz sprawdzić swój przepływ w dowolnym momencie, aby sprawdzić, czy każdy krok jest prawidłowo skonfigurowany: wybierz pozycję **moduł sprawdzania przepływu** z paska menu przepływ.

W następnym zestawie kroków zostanie nawiązane połączenie z tabelą magazynową i skonfigurowanie logiki przetwarzania w celu obsługi nowych potencjalnych klientów.

1. Po kroku **Pobierz poprzedni czas** wybierz pozycję **+ nowy krok**, a następnie wyszukaj pozycję **Pobierz jednostki**.
1. W obszarze **Akcje**wybierz pozycję **Pobierz jednostki**, a następnie wybierz pozycję **Pokaż opcje zaawansowane**.
1. W oknie **Pobierz jednostki** wypełnij następujące pola:

   - **Tabela**: nazwa magazynu tabel. Na poniższej ilustracji przedstawiono wprowadzenie "MarketPlaceLeads":

     ![Wybierz niestandardową wartość dla nazwy tabeli platformy Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Zapytanie filtru**: po zaznaczeniu tego pola w oknie podręcznym zostanie wyświetlona ikona **uzyskaj czas** ostatniej. Wybierz **poprzedni czas** , aby użyć tej wartości jako sygnatury czasowej do filtrowania zapytania. Lub można wkleić następującą funkcję do pola:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Konfigurowanie funkcji zapytania filtru](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Wybierz pozycję **nowy krok** , aby dodać warunek do skanowania magazynu tabel dla nowych potencjalnych klientów.

   ![Użyj "nowego kroku", aby dodać warunek do skanowania magazynu tabel](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. W oknie **Wybierz akcję** wybierz pozycję **Akcje**, a następnie wybierz pozycję **Kontrola warunku**.

     ![Dodawanie kontrolki warunku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. W oknie **warunek** wybierz pozycję **Wybierz wartość**, a następnie w oknie podręcznym wybierz pozycję **wyrażenie** .
1. Wklej `length(body('Get_entities')?['value'])` w polu ***FX*** . Wybierz **przycisk OK** , aby dodać tę funkcję. 



     ![Dodaj funkcję do warunku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Skonfiguruj akcję do wykonania w oparciu o wynik warunku.

    1. Opcja Wybierz **jest większa niż** wartość z listy rozwijanej.
   1. Wprowadź **0** jako wartość.

     ![Skonfiguruj akcję na podstawie wyników warunku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Jeśli warunek jest rozpoznawany jako "Jeśli nie," nie rób niczego.

    Jeśli warunek jest rozpoznawany jako "Jeśli tak", wyzwala akcję, która łączy konto Office 365 w celu wysłania wiadomości e-mail:
   1. Wybierz pozycję **Dodaj akcję**.
   1. Wybierz akcję **Wyślij wiadomość e-mail**.
   1. W oknie **wysyłanie wiadomości e-mail** wprowadź informacje w następujących polach:

      - **Do**: adres e-mail dla wszystkich użytkowników, którzy otrzymają powiadomienie.
      - **Temat**: temat wiadomości e-mail. Na przykład: *nowi potencjalni klienci!*
      - **Treść**: tekst, który ma zostać uwzględniony w każdej wiadomości e-mail (opcjonalnie). Również wklej `body('Get_entities')?['value']` jako funkcję, aby wstawić informacje o potencjalnym kliencie.

        >[!NOTE] 
        >W treści wiadomości e-mail można wstawić dodatkowe statyczne lub dynamiczne punkty danych.

      ![Skonfiguruj adres e-mail powiadomienia o potencjalnym kliencie](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Aby zapisać zmiany, wybierz opcję **Zapisz**. Microsoft Flow automatycznie przetestuje go pod kątem błędów. Jeśli nie ma żadnych błędów, przepływ zostanie uruchomiony po jego zapisaniu.

    Na poniższej ilustracji przedstawiono przykład sposobu, w jaki powinien wyglądać końcowy przepływ.

    [![Sekwencja przepływu końcowego](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (*Wybierz obraz, aby go powiększyć).*

### <a name="manage-your-flow"></a>Zarządzanie przepływem

Zarządzanie przepływem jest łatwe w obsłudze. Masz pełną kontrolę nad przepływem. Można na przykład ją zatrzymać, edytować, wyświetlić historię uruchamiania i uzyskać analizę. Na poniższej ilustracji przedstawiono opcje zarządzania przepływem.

 ![Opcje zarządzania przepływem](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Przepływ pozostaje uruchomiony do momentu wybrania opcji **Wyłącz przepływ**.

Jeśli nie otrzymasz żadnych powiadomień o potencjalnych klientach, do magazynu tabel nie dodano żadnych nowych potencjalnych klientów.
Jeśli wystąpi błąd przepływu, otrzymasz wiadomość e-mail podobną do następującego przykładu:

 ![Powiadomienie e-mail o niepowodzeniu przepływu](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
