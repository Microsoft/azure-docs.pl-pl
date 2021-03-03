---
title: Przewodnik Szybki Start platformy Azure — tworzenie centrum zdarzeń przy użyciu Azure Portal
description: W tym przewodniku szybki start dowiesz się, jak utworzyć centrum zdarzeń platformy Azure przy użyciu Azure Portal.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 6bd9d247db6cd5a956ff4bce9b70f1f8a5a7499a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742045"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Szybki start: tworzenie centrum zdarzeń przy użyciu witryny Azure Portal
Azure Event Hubs to usługa do przesyłania strumieniowego danych Big Data i usługi pozyskiwania zdarzeń, które mogą odbierać i przetwarzać miliony zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym przewodniku Szybki start utworzysz centrum zdarzeń za pomocą witryny [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start, upewnij się, że dysponujesz następującymi elementami:

- Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczna kolekcja zasobów platformy Azure. Wszystkie zasoby są wdrażane i zarządzane w ramach grupy zasobów. Aby utworzyć grupę zasobów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W okienku nawigacji po lewej stronie wybierz pozycję **grupy zasobów**. Następnie wybierz pozycję **Dodaj**.

   ![Grupy zasobów — przycisk Dodaj](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. W polu **Subskrypcja** wybierz nazwę subskrypcji platformy Azure, w której chcesz utworzyć grupę zasobów.
1. Wpisz unikatową **nazwę grupy zasobów**. System natychmiast sprawdzi, czy nazwa jest dostępna w aktualnie wybranej subskrypcji platformy Azure.
1. Wybierz **region** dla grupy zasobów.
1. Wybierz pozycję **Przejrzyj i utwórz**.

   ![Grupa zasobów — tworzenie](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. Na stronie **Przeglądanie + tworzenie** wybierz pozycję **Utwórz**. 

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs

Przestrzeń nazw Event Hubs zapewnia unikatowy kontener zakresu, w którym można utworzyć co najmniej jedno centrum zdarzeń. Aby utworzyć przestrzeń nazw w grupie zasobów przy użyciu portalu, wykonaj następujące akcje:

1. W witrynie Azure Portal wybierz pozycję **Utwórz zasób** w lewym górnym rogu ekranu.
1. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie, a następnie wybierz **gwiazdkę (`*`)** obok pozycji **Event Hubs** w kategorii **Analiza**. Upewnij się, że usługa **Event Hubs** została dodana do kategorii **ULUBIONE** w menu nawigacji po lewej stronie. 
    
   ![Wyszukiwanie usługi Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. Wybierz pozycję **Event Hubs** w obszarze **ULUBIONE** w menu nawigacji po lewej stronie, a następnie wybierz pozycję **Dodaj** na pasku narzędzi.

   ![Przycisk dodawania](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. Na stronie **Tworzenie przestrzeni nazw** wykonaj następujące czynności:  
   1. Wybierz **subskrypcję**, w ramach której chcesz utworzyć przestrzeń nazw.  
   1. Wybierz **grupę zasobów** utworzoną w poprzednim kroku.   
   1. Wprowadź **nazwę** dla przestrzeni nazw. System od razu sprawdza, czy nazwa jest dostępna.  
   1. Wybierz **lokalizację** dla przestrzeni nazw.
   1. Wybierz **warstwę cenową** (podstawowa lub standardowa). Aby dowiedzieć się więcej o różnicach między warstwami podstawowa i standardowa, zobacz [Event Hubs Cennik](https://azure.microsoft.com/pricing/details/event-hubs/), [różnice między warstwami](event-hubs-faq.md#what-is-the-difference-between-event-hubs-basic-and-standard-tiers)oraz [limity przydziału i limitów](event-hubs-quotas.md). 
   1. Pozostaw ustawienia **jednostek przepływności** . Jednostki przepływności to zakupione wcześniej jednostki pojemności. Aby dowiedzieć się więcej o jednostkach przepływności, zobacz [Event Hubs skalowalność](event-hubs-scalability.md#throughput-units).  
   1. Wybierz pozycję **Recenzja + Utwórz** w dolnej części strony.
      
      ![Tworzenie przestrzeni nazw centrum zdarzeń](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. Na stronie **Recenzja i tworzenie** Sprawdź ustawienia, a następnie wybierz pozycję **Utwórz**. Zaczekaj na zakończenie wdrażania. 
      
      ![Przejrzyj i Utwórz stronę](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. Na stronie **wdrażanie** wybierz pozycję **Przejdź do zasobu** , aby przejść do strony dla obszaru nazw. 
      
      ![Wdrożenie zakończone — przejdź do zasobu](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Upewnij się, że na stronie **Event Hubs przestrzeni nazw** została wyświetlona podobna do poniższego przykładu:   
      
      ![Strona główna przestrzeń nazw](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

      > [!NOTE]
      > Usługa Azure Event Hubs udostępnia punkt końcowy Kafka. Ten punkt końcowy umożliwia Event Hubsom przestrzeni nazw natywne zrozumienie protokołu komunikatów [Apache Kafka](https://kafka.apache.org/intro) i interfejsów API. Korzystając z tej możliwości, można komunikować się z centrami zdarzeń w taki sam sposób, jak w przypadku Kafka tematów bez zmiany klientów protokołu lub uruchamiania własnych klastrów. Event Hubs obsługuje [Apache Kafka wersje 1,0](https://kafka.apache.org/10/documentation.html) i nowsze. Aby uzyskać więcej informacji, zobacz [Korzystanie z Event Hubs z aplikacji Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Aby utworzyć centrum zdarzeń w przestrzeni nazw, wykonaj następujące akcje:

1. Na stronie przestrzeni nazw usługi Event Hubs wybierz pozycję **Event Hubs** w menu po lewej stronie.
1. W górnej części okna wybierz pozycję **+ centrum zdarzeń**.
   
    ![Dodaj centrum zdarzeń — przycisk](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Wpisz nazwę centrum zdarzeń, a następnie wybierz pozycję **Utwórz**.
   
    ![Tworzenie centrum zdarzeń](./media/event-hubs-quickstart-portal/create-event-hub5.png)

    Ustawienie **liczba partycji** pozwala zrównoleglanie użycie wielu odbiorców. Aby uzyskać więcej informacji, zobacz [partycje](event-hubs-scalability.md#partitions).

    Ustawienie **przechowywania wiadomości** określa, jak długo usługa Event Hubs przechowuje dane. Aby uzyskać więcej informacji, zobacz [przechowywanie zdarzeń](event-hubs-features.md#event-retention).
1. Możesz sprawdzić stan tworzenia centrum zdarzeń w alertach. Po utworzeniu centrum zdarzeń zostanie ono wyświetlone na liście centrów zdarzeń.

    ![Centrum zdarzeń zostało utworzone](./media/event-hubs-quickstart-portal/event-hub-created.png)
    
## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono grupę zasobów, przestrzeń nazw usługi Event Hubs i centrum zdarzeń. Aby uzyskać instrukcje krok po kroku dotyczące wysyłania zdarzeń do (lub) odbierania zdarzeń z centrum zdarzeń, zobacz następujące samouczki: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Przejdź](event-hubs-go-get-started-send.md)
- [C (tylko wysyłanie)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tylko odbieranie)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
