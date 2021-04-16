---
title: Żądanie zwiększenia limitu przydziału i uzyskiwanie pomocy technicznej
description: Jak utworzyć wniosek o pomoc techniczną w Azure Portal dla Azure Synapse Analytics. Zażądaj zwiększenia limitu przydziału lub uzyskaj obsługę rozwiązywania problemów.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: julieMSFT
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 5256c6d25a9c7acfc45cdffee05c95fb3407c24a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568322"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Żądanie zwiększenia limitu przydziału i uzyskiwanie pomocy Azure Synapse Analytics

W tym artykule opisano sposób przesyłania biletu pomocy technicznej w Azure Portal dla Azure Synapse Analytics. Ten proces umożliwia zażądanie zwiększenia limitu przydziału lub przesłanie wniosku o pomoc techniczną do zespołu inżynieryjnego.

## <a name="create-a-support-ticket"></a>Tworzenie biletu pomocy technicznej

Aby utworzyć nowy wniosek o pomoc techniczną z aplikacji Azure Portal for Azure Synapse Analytics, należy wykonać poniższe Azure Synapse Analytics.

1. W menu [Azure Portal](https://portal.azure.com) pozycję **Pomoc i obsługa techniczna.**

   ![Link Pomoc i obsługa techniczna](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. W **pomocniku Pomoc i obsługa techniczna** wybierz pozycję Nowy **wniosek o pomoc techniczną.**

    ![Tworzenie nowego wniosku o pomoc techniczną](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Przejrzyj plan [pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)

   * Pomoc techniczna związana z **rozliczeniami, limitami przydziałów i zarządzaniem subskrypcjami** jest dostępna na wszystkich poziomach pomocy technicznej.
   * **Naprawa w przypadku usterek** jest [zapewniana](https://azure.microsoft.com/support/plans/developer/)za pośrednictwem pomocy [technicznej developer, standard,](https://azure.microsoft.com/support/plans/standard/) [pomoc techniczna Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)lub [Premier.](https://azure.microsoft.com/support/plans/premier/) Naprawa w razie awarii dotyczy problemów napotykanych przez klientów podczas korzystania z platformy Azure, jeśli istnieje uzasadnione podejrzenie, że problem leży po stronie firmy Microsoft.
   * **Wsparcie dla deweloperów** i **usługi doradcze** są dostępne na poziomach pomocy technicznej [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) i [Premium](https://azure.microsoft.com/support/plans/premier/).

   Jeśli masz plan pomocy technicznej Premier, możesz również zgłosić problemy Azure Synapse Analytics w portalu [Microsoft Premier Online.](https://premier.microsoft.com/) Zobacz [Plany pomocy technicznej platformy Azure,](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) aby dowiedzieć się więcej o różnych planach pomocy technicznej, w tym o zakresie, czasach odpowiedzi, cenach itp.  Aby uzyskać często zadawane pytania dotyczące pomocy technicznej platformy Azure, zobacz Często zadawane pytania dotyczące pomocy [technicznej platformy Azure.](https://azure.microsoft.com/support/faq/)

1. W **przypadku typu** problemu wybierz odpowiedni typ problemu. W przypadku problemów z naprawianiem włamań wybierz **pozycję Techniczny.** W przypadku żądań zwiększenia limitu przydziału wybierz **pozycję Limity usługi i subskrypcji (limity przydziału).**

   ![Wybierz typ problemu](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > W pozostałej części tego artykułu skoncentrowano się na żądaniach zwiększenia limitu przydziału. W tym miejscu możesz również wybrać **pozycję Techniczny,** aby uzyskać pomoc techniczną w zakresie rozwiązywania problemów. W przypadku wybrania **opcji Techniczny** zostanie poproszony o podanie podsumowania, a następnie zidentyfikowanie typu problemu przez wybranie opcji Wybierz **typ problemu.** Możesz zobaczyć rozwiązania, które pomogą rozwiązać problem. Jeśli przedstawione rozwiązania nie rozwiążą problemu, wybierz pozycję **Dalej:Szczegóły** i wypełnij formularz, aby przesłać bilet pomocy technicznej.

1. W przypadku żądań zwiększenia limitu przydziału **wybierz Azure Synapse Analytics** jako typ **limitu przydziału**. Następnie wybierz **pozycję Dalej: >>.**

   ![Wybieranie typu limitu przydziału](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. W **oknie Szczegóły** wybierz pozycję **Wprowadź szczegóły,** aby wprowadzić dodatkowe informacje.

   ![Link "Podaj szczegóły"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typy żądań limitu przydziału

Wybranie **opcji Wprowadź szczegóły** powoduje wyświetlenie okna Szczegóły **limitu** przydziału, w którym można dodać dodatkowe informacje. W poniższych sekcjach opisano różne żądania limitu przydziału dostępne dla Azure Synapse Analytics.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>Synapse SQL jednostek Data Warehouse (jednostek DWU) na serwer

Aby zażądać zwiększenia liczby jednostki DWU na serwer, należy wykonać poniższe kroki.

1. Wybierz typ **limitu przydziału Synapse SQL puli** zasobów na serwer.

1. Wybierz **zasób,** do którego chcesz zastosować zwiększenie limitu przydziału, korzystając z listy rozwijanej.

1. Wprowadź nowy limit przydziału w **sekcji Żądanie limitu przydziału.**

1. Wybierz przycisk **Zapisz i kontynuuj**.

   ![Szczegóły limitu przydziału jednostek DWU](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>Serwery na subskrypcję

Aby zażądać zwiększenia liczby serwerów na subskrypcję, należy wykonać następujące czynności:

1. Wybierz typ **limitu przydziału SQL Server** na subskrypcję.

1. Na liście **Lokalizacja** wybierz region świadczenia usługi Azure do użycia. Limit przydziału jest na subskrypcję w każdym regionie.

1. W **polu Request quota** (Żądanie limitu przydziału) wprowadź żądanie dotyczące maksymalnej liczby serwerów w tym regionie.

   ![Szczegóły limitu przydziału serwerów](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. Wybierz przycisk **Zapisz i kontynuuj**.

Niektóre typy ofert nie są dostępne w każdym regionie. Może zostać wyświetlony następujący błąd:

![Błąd dostępu do regionu](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>Włączanie dostępu subskrypcji do regionu

Aby włączyć dostęp do regionu dla subskrypcji, należy wykonać następujące czynności:  

1. Wybierz typ **limitu przydziału Synapse SQL puli danych (magazyn** danych).

1. Wybierz region, wybierając pozycję **Lokalizacja** z listy rozwijanej.

1. Wskaż wymagania dotyczące wydajności jednostek DWU w sekcji **Wymagane jednostek DWU.**

1. Wprowadź opis **wymagań biznesowych.** 

1. Wybierz przycisk **Zapisz i kontynuuj**.

![Dostęp regionu](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>W przypadku innych żądań limitu przydziału

Wybierz **pozycję Inne żądanie limitu przydziału** z menu rozwijanego typ limitu przydziału dla innych typów żądań limitu przydziału:

![Inne szczegóły limitu przydziału](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details.png)

## <a name="submit-your-request"></a>Prześlij żądanie

Ostatnim krokiem jest wypełnienie pozostałych szczegółów SQL Database pomocy technicznej. Następnie wybierz **pozycję Dalej: Przeglądanie i tworzenie>>**.

![Przeglądanie szczegółów tworzenia](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

Po przejrzeniu szczegółów żądania wybierz pozycję **Utwórz,** aby przesłać żądanie.

![Tworzenie biletu](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>Monitorowanie biletu pomocy technicznej

Po przesłaniu wniosku o pomoc techniczną zespół pomocy technicznej platformy Azure skontaktuje się z Tobą. Aby sprawdzić stan żądania i szczegóły, wybierz pozycję **Wszystkie żądania pomocy technicznej** na pulpicie nawigacyjnym.

![Sprawdzanie stanu](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Inne zasoby

Możesz również nawiązać połączenie ze społecznością Azure Synapse Analytics w witrynie [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) lub za pośrednictwem strony [pytania microsoft Q&A dla Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html).