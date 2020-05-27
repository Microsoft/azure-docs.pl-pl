---
title: Zwiększanie limitu przydziału żądań i uzyskiwanie pomocy technicznej
description: Jak utworzyć żądanie pomocy technicznej w Azure Portal usługi Azure Synapse Analytics. Przydział żądań zwiększa lub pobiera pomoc techniczną dotyczącą rozwiązywania problemów.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0788f978fd25356b230a7923def6cbbea3dc305
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835465"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Zwiększanie limitu przydziału żądań i uzyskiwanie pomocy technicznej dla usługi Azure Synapse Analytics

W tym artykule opisano sposób przesyłania biletu pomocy technicznej w Azure Portal usługi Azure Synapse Analytics. Ten proces umożliwia zażądanie zwiększenia limitu przydziału lub przesłanie żądania pomocy technicznej dla zespołu pomocy technicznej inżynierów.

## <a name="create-a-support-ticket"></a>Tworzenie biletu pomocy technicznej

Wykonaj następujące kroki, aby utworzyć nowe żądanie pomocy technicznej na podstawie Azure Portal usługi Azure Synapse Analytics.

1. W menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Pomoc i obsługa techniczna**.

   ![Łącze Pomoc i obsługa techniczna](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. W oknie **Pomoc i obsługa techniczna**wybierz pozycję **nowe żądanie obsługi**.

    ![Utwórz nowe żądanie obsługi](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Zapoznaj się z [planem pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Pomoc techniczna związana z **rozliczeniami, limitami przydziałów i zarządzaniem subskrypcjami** jest dostępna na wszystkich poziomach pomocy technicznej.
   * Pomoc techniczna dotycząca **usuwania poprawek** jest świadczona za pośrednictwem pomocy technicznej [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)i [Premier](https://azure.microsoft.com/support/plans/premier/) . Naprawa w razie awarii dotyczy problemów napotykanych przez klientów podczas korzystania z platformy Azure, jeśli istnieje uzasadnione podejrzenie, że problem leży po stronie firmy Microsoft.
   * **Wsparcie dla deweloperów** i **usługi doradcze** są dostępne na poziomach pomocy technicznej [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) i [Premium](https://azure.microsoft.com/support/plans/premier/).

   Jeśli masz plan pomocy technicznej Premier, możesz również zgłosić problemy z analizą usługi Azure Synapse w [portalu Microsoft Premier online](https://premier.microsoft.com/). Zobacz [plany pomocy technicznej platformy Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) , aby dowiedzieć się więcej o różnych planach pomocy technicznej, takich jak zakres, czasy odpowiedzi, Cennik itp.  Często zadawane pytania dotyczące pomocy technicznej platformy Azure można znaleźć w artykule [FAQ pomocy technicznej dla platformy Azure](https://azure.microsoft.com/support/faq/).

1. W **polu Typ problemu**wybierz odpowiedni typ problemu. Aby rozwiązać problemy z uszkodzeniem, wybierz pozycję **techniczne**. W przypadku żądań zwiększenia limitu przydziału wybierz pozycję **usługi i limity subskrypcji (przydziały)**.

   ![Wybierz typ problemu](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > W pozostałej części tego artykułu skupia na żądaniach zwiększenia przydziału. Możesz również wybrać tutaj **techniczną** , aby uzyskać pomoc techniczną dotyczącą rozwiązywania problemów. W przypadku wybrania opcji **techniczne**zostanie wyświetlony monit o podanie podsumowania, a następnie zidentyfikowanie typu problemu, wybierając **pozycję Wybierz typ problemu**. Możesz zobaczyć rozwiązania pomagające w rozwiązaniu problemu. Jeśli przedstawione rozwiązania nie rozwiązują problemu, wybierz pozycję **Dalej: szczegóły** i wypełnij formularz, aby przesłać bilet pomocy technicznej.

1. W przypadku żądań zwiększenia limitu przydziału wybierz pozycję **Azure Synapse Analytics** dla **typu limitu przydziału**. Następnie wybierz pozycję **Dalej: rozwiązania >>**.

   ![Wybierz typ limitu przydziału](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. W oknie **szczegóły** wybierz pozycję **Podaj szczegóły** , aby wprowadzić dodatkowe informacje.

   ![Link "Podaj szczegóły"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typy żądań limitu przydziału

Kliknięcie pozycji **Podaj szczegóły** powoduje wyświetlenie okna **Szczegóły przydziału** umożliwiającego dodanie dodatkowych informacji. W poniższych sekcjach opisano różne żądania przydziału dostępne dla usługi Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Jednostki magazynu danych (jednostek dwu) na serwer

Wykonaj następujące kroki, aby zażądać wzrostu jednostek dwu na serwer.

1. Wybierz typ przydziału **jednostki magazynu danych (DTU) na serwer** .

1. Na liście **zasobów** wybierz zasób docelowy.

1. W polu **przydział żądania** wprowadź nowy limit jednostek dwu, którego żądasz.

   ![Szczegóły przydziału jednostek dwu](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Serwery na subskrypcję

Wykonaj następujące kroki, aby zażądać zwiększenia liczby serwerów na subskrypcję.

1. Wybierz typ przydziału **serwery na subskrypcję** .

1. Na liście **Lokalizacja** wybierz region platformy Azure, który ma być używany. Przydział jest przypadany na subskrypcję w każdym regionie.

1. W polu **nowy limit przydziału** wprowadź swój wniosek dotyczący maksymalnej liczby serwerów w tym regionie.

   ![Szczegóły limitu przydziału serwerów](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Włączanie dostępu do subskrypcji do regionu

Niektóre typy ofert nie są dostępne w każdym regionie. Może zostać wyświetlony następujący błąd:

`This location is not available for subscription`

Jeśli Twoja subskrypcja wymaga dostępu w określonym regionie, użyj **innej opcji żądania przydziału** , aby zażądać dostępu. W żądaniu Określ szczegóły oferty i jednostki SKU, które chcesz włączyć dla regionu. Aby poznać opcje oferty i jednostki SKU, zobacz [Cennik usługi Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Inne szczegóły przydziału](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Prześlij żądanie

Ostatnim krokiem jest wypełnienie pozostałych szczegółów żądania obsługi SQL Database. Następnie wybierz pozycję **Dalej: Przeglądanie i tworzenie >>** i po przejrzeniu szczegółów żądania kliknij pozycję **Utwórz**, aby przesłać żądanie.

## <a name="monitor-a-support-ticket"></a>Monitorowanie biletu pomocy technicznej

Po przesłaniu żądania pomocy technicznej zespół pomocy technicznej systemu Azure skontaktuje się z Tobą. Aby sprawdzić stan żądania i szczegółowe informacje, kliknij pozycję **Wszystkie żądania obsługi** na pulpicie nawigacyjnym.

![Sprawdzanie stanu](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Inne zasoby

Możesz również nawiązać połączenie z społecznością usługi Azure Synapse Analytics na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) lub za pomocą [strony pytań&Microsoft pytań i odpowiedzi na Azure SQL Data Warehouse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html).

