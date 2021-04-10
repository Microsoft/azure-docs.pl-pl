---
title: Tworzenie dedykowanego klastra Event Hubs przy użyciu Azure Portal
description: W tym przewodniku szybki start dowiesz się, jak utworzyć klaster usługi Azure Event Hubs przy użyciu Azure Portal.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 596b07210d35f648af78e45b7472c620a4361e8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601027"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Szybki Start: tworzenie dedykowanego klastra Event Hubs przy użyciu Azure Portal 
Klastry Event Hubs oferują wdrożenia z jedną dzierżawą dla klientów z najbardziej wymaganymi potrzebami przesyłania strumieniowego. Ta oferta ma gwarantowaną umowę SLA na 99,99% i jest dostępna tylko w naszej dedykowanej warstwie cenowej. [Klaster Event Hubs](event-hubs-dedicated-overview.md) może odbierać miliony zdarzeń na sekundę z gwarantowaną pojemnością i drugim opóźnieniem. Przestrzenie nazw i centra zdarzeń utworzone w ramach klastra obejmują wszystkie funkcje standardowej oferty i wiele innych, ale bez ograniczeń związanych z transferem danych przychodzących. Oferta dedykowana obejmuje również popularne funkcje [przechwytywania Event Hubs](event-hubs-capture-overview.md) bez dodatkowych kosztów, co pozwala na automatyczne tworzenie partii i rejestrowanie strumieni danych na [platformie Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) lub [Azure Data Lake Storage generacji 1](../data-lake-store/data-lake-store-overview.md).

Dedykowane klastry są udostępniane i są rozliczane według **jednostek pojemności**, wstępnie przydzielonej ilości zasobów procesora i pamięci. W każdym klastrze można zakupić 1, 2, 4, 8, 12, 16 lub 20 jednostek. W tym przewodniku szybki start przeprowadzimy Cię przez proces tworzenia 1% CU Event Hubs klastra za pomocą Azure Portal.

> [!NOTE]
> To środowisko samoobsługowe jest obecnie dostępne w wersji zapoznawczej w witrynie [Azure Portal](https://aka.ms/eventhubsclusterquickstart). Jeśli masz jakieś pytania dotyczące dedykowanej oferty, skontaktuj się z [zespołem Event Hubs](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Konto platformy Azure. Jeśli nie masz takiego konta, [Kup](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) je przed rozpoczęciem. Ta funkcja nie jest obsługiwana przez bezpłatne konto platformy Azure. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (wersja 15,3, 26730,01) lub nowszy.
- [Zestaw .NET Standard SDK](https://dotnet.microsoft.com/download) w wersji 2.0 lub nowszej.
- [Utworzono grupę zasobów](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Tworzenie klastra Event Hubs — warstwa Dedykowana
Klaster Event Hubs udostępnia unikatowy kontener zakresu, w którym można utworzyć co najmniej jeden obszar nazw. W tej fazie zapoznawczej środowiska samoobsługowego w portalu można utworzyć 1 CU klastrów w wybranych regionach. Jeśli potrzebujesz klastra większego niż 1 CU, możesz przesłać żądanie pomocy technicznej platformy Azure w celu skalowania klastra po jego utworzeniu.

> [!IMPORTANT]
> Nie będzie można usunąć klastra przez co najmniej 4 godziny od momentu jego utworzenia. W związku z tym opłata zostanie naliczona za co najmniej 4 godziny korzystania z klastra. Aby uzyskać więcej informacji na temat cen, zobacz [Event Hubs-Cennik](https://azure.microsoft.com/pricing/details/event-hubs/). 

Aby utworzyć klaster w grupie zasobów przy użyciu Azure Portal, wykonaj następujące czynności:

1. Użyj [tego linku](https://aka.ms/eventhubsclusterquickstart) , aby utworzyć klaster na Azure Portal. W lewym okienku nawigacji wybierz pozycję **wszystkie usługi** , a następnie wpisz ciąg "Event Hubs klastrów" na pasku wyszukiwania i wybierz pozycję "klastry Event Hubs" z listy wyników.
2. Na stronie **Tworzenie klastra** skonfiguruj następujące ustawienia:
    1. Wprowadź **nazwę klastra**. System od razu sprawdza, czy nazwa jest dostępna.
    2. Wybierz **subskrypcję** , w której chcesz utworzyć klaster.
    3. Wybierz **grupę zasobów** , w której chcesz utworzyć klaster.
    4. Wybierz **lokalizację** klastra. Jeśli preferowany region jest wyszarzony, jest tymczasowo poza pojemnością i można przesłać [żądanie pomocy technicznej](#submit-a-support-request) do zespołu Event Hubs.
    5. Wybierz przycisk **Następny: Tagi** w dolnej części strony. Może być konieczne odczekanie kilku minut, aby system przeprowadził pełną aprowizację zasobów.

        ![Tworzenie Event Hubs klastra — Strona podstawy](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Na stronie **Tagi** skonfiguruj następujące elementy:
    1. Wprowadź **nazwę** i **wartość** tagu, który chcesz dodać. Ten krok jest **opcjonalny**.  
    2. Wybierz przycisk **Recenzja + Utwórz** .

        ![Utwórz stronę Event Hubs klastra — Strona tagów](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. Na stronie **Recenzja i tworzenie** Przejrzyj szczegóły, a następnie wybierz pozycję **Utwórz**. 

    ![Tworzenie strony klastra Event Hubs — Recenzja i Tworzenie strony](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Tworzenie przestrzeni nazw i centrum zdarzeń w klastrze

1. Aby utworzyć przestrzeń nazw w klastrze, na stronie **Event Hubs klastra** dla klastra wybierz pozycję **+ przestrzeń nazw** z górnego menu.

    ![Strona Zarządzanie klastrem — przycisk Dodaj przestrzeń nazw](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Na stronie **Tworzenie przestrzeni nazw** wykonaj następujące czynności:
    1. Wprowadź **nazwę dla przestrzeni nazw**.  System sprawdza, czy nazwa jest dostępna.
    2. Przestrzeń nazw dziedziczy następujące właściwości:
        1. Identyfikator subskrypcji
        2. Grupa zasobów
        3. Lokalizacja
        4. Nazwa klastra
    3. Wybierz pozycję **Utwórz** , aby utworzyć przestrzeń nazw. Teraz możesz zarządzać klastrem.  

        ![Tworzenie przestrzeni nazw na stronie klastra](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. Po utworzeniu przestrzeni nazw można [utworzyć centrum zdarzeń](event-hubs-create.md#create-an-event-hub) , tak jak zwykle utworzysz je w przestrzeni nazw. 


## <a name="submit-a-support-request"></a>Prześlij żądanie pomocy technicznej

Jeśli chcesz zmienić rozmiar klastra po utworzeniu lub jeśli preferowany region nie jest dostępny, Prześlij żądanie pomocy technicznej, wykonując następujące czynności:

1. W obszarze [Azure Portal](https://portal.azure.com)wybierz pozycję **Pomoc i obsługa techniczna** w menu po lewej stronie.
2. Wybierz pozycję **+ nowe żądanie obsługi** w menu Pomoc techniczna.
3. Na stronie Pomoc techniczna wykonaj następujące kroki:
    1. W polu **typ problemu** wybierz pozycję **techniczne** z listy rozwijanej.
    2. W polu **Subskrypcja** wybierz subskrypcję.
    3. W obszarze **Usługa** wybierz pozycję **Moje usługi**, a następnie wybierz pozycję **Event Hubs**.
    4. W obszarze **zasób** wybierz swój klaster, jeśli istnieje już, w przeciwnym razie wybierz pozycję **Ogólne pytanie/zasób** niedostępne.
    5. W obszarze **typ problemu** wybierz pozycję **przydział**.
    6. Aby uzyskać **podtyp problemu**, wybierz jedną z następujących wartości z listy rozwijanej:
        1. Wybierz pozycję **żądanie dla dedykowanej jednostki SKU** , aby zażądać, aby funkcja była obsługiwana w Twoim regionie.
        2. Wybierz pozycję **Żądaj, aby skalować dedykowany klaster w górę lub w dół** , jeśli chcesz skalować w górę lub w dół dedykowany klaster. 
    7. W **temacie** opisz problem.

        ![Strona biletu pomocy technicznej](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Usuwanie dedykowanego klastra
 
1. Aby usunąć klaster, wybierz pozycję **Usuń** z górnego menu. 

    > [!IMPORTANT]
    > Nie będzie można usunąć klastra przez co najmniej 4 godziny od momentu jego utworzenia. W związku z tym opłata zostanie naliczona za co najmniej 4 godziny korzystania z klastra. Aby uzyskać więcej informacji na temat cen, zobacz [Event Hubs-Cennik](https://azure.microsoft.com/pricing/details/event-hubs/).     
1. Zostanie wyświetlony komunikat z potwierdzeniem, że chcesz usunąć klaster.
1. Wpisz **nazwę klastra** , a następnie wybierz pozycję **Usuń** , aby usunąć klaster.

    ![Usuń stronę klastra](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Następne kroki
W tym artykule opisano tworzenie klastra Event Hubs. Aby uzyskać instrukcje krok po kroku dotyczące wysyłania i odbierania zdarzeń z centrum zdarzeń oraz przechwytywania zdarzeń do usługi Azure Storage lub Azure Data Lake Store, zobacz następujące samouczki:

- Wysyłanie i odbieranie zdarzeń 
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
- [Włączanie przechwytywania Event Hubs przy użyciu Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Użyj usługi Azure Event Hubs dla Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
