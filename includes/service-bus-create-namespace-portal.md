---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d2c309340155bc626d4da94d74aee9be51bde510
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90606646"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Tworzenie przestrzeni nazw w witrynie Azure Portal
Aby rozpocząć korzystanie z obsługi wiadomości usługi Service Bus na platformie Azure, musisz najpierw utworzyć przestrzeń nazw o nazwie, która jest unikatowa w obrębie platformy Azure. Przestrzeń nazw zapewnia kontener zakresu na potrzeby adresowania zasobów usługi Service Bus w aplikacji.

Aby utworzyć przestrzeń nazw:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
2. W lewym okienku nawigacyjnym portalu wybierz pozycję **+ Utwórz zasób**, a następnie wybierz pozycje **Integracja** i **Service Bus**.

    ![Utwórz zasób -> Integracja -> Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. W oknie dialogowym **Tworzenie przestrzeni nazw** wykonaj następujące czynności: 
    1. Wprowadź **nazwę dla przestrzeni nazw**. System od razu sprawdza, czy nazwa jest dostępna. Aby uzyskać listę reguł nazewnictwa przestrzeni nazw, zobacz [Tworzenie interfejsu API REST przestrzeni nazw](/rest/api/servicebus/create-namespace).
    2. Wybierz warstwę cenową (Podstawowa, Standardowa lub Premium) dla przestrzeni nazw. Jeśli chcesz korzystać z [tematów i subskrypcji](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), wybierz warstwę Standardowa lub Premium. Tematy/subskrypcje nie są obsługiwane w warstwie cenowej Podstawowa.
    3. W przypadku wybrania warstwy cenowej **Premium** wykonaj następujące czynności: 
        1. Określ liczbę **jednostek obsługi komunikatów**. Warstwa Premium zapewnia izolację zasobów na poziomie procesora i pamięci, dlatego poszczególne obciążenia są od siebie odizolowane. Ten kontener zasobów jest nazywany jednostką obsługi komunikatów. Przestrzeń nazw Premium ma co najmniej jedną jednostkę obsługi komunikatów. Możesz wybrać 1, 2 lub 4 jednostki obsługi komunikatów dla każdej przestrzeni nazw usługi Service Bus w warstwie Premium. Aby uzyskać więcej informacji, zobacz [Obsługa komunikatów w usłudze Service Bus w warstwie Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Określ, czy chcesz utworzyć **strefowo nadmiarową** przestrzeń nazw. Nadmiarowość strefowa zapewnia rozszerzoną dostępność przez rozproszenie replik w różnych strefach dostępności w jednym regionie bez dodatkowych kosztów. Aby uzyskać więcej informacji, zobacz [Availability zones in Azure (Strefy dostępności na platformie Azure)](../articles/availability-zones/az-overview.md).
    4. W polu **Subskrypcja** wybierz subskrypcję platformy Azure, w której ma zostać utworzona przestrzeń nazw.
    5. W polu **Grupa zasobów** wybierz istniejącą grupę zasobów, w której znajdzie się przestrzeń nazw, lub utwórz nową.      
    6. W polu **Lokalizacja** wybierz region, w którym powinna być hostowana przestrzeń nazw.
    7. Wybierz przycisk **Utwórz**. W systemie zostanie utworzona i włączona przestrzeń nazw. Proces inicjowania obsługi administracyjnej zasobów dla konta w systemie może potrwać kilka minut.
   
        ![Create namespace](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Upewnij się, że przestrzeń nazw usługi Service Bus została pomyślnie wdrożona. Aby wyświetlić powiadomienia, wybierz **ikonę dzwonka (Alerty)** na pasku narzędzi. Wybierz **nazwę grupy zasobów** w powiadomieniu, jak pokazano na ilustracji. Zostanie wyświetlona grupa zasobów, która zawiera przestrzeń nazw usługi Service Bus.

    ![Alert dotyczący wdrożenia](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Na stronie **Grupa zasobów** dla grupy zasobów wybierz swoją **przestrzeń nazw usługi Service Bus**. 

    ![Strona Grupa zasobów — wybieranie przestrzeni nazw usługi Service Bus](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Zostanie wyświetlona strona główna dla przestrzeni nazw usługi Service Bus. 

    ![Strona główna przestrzeni nazw usługi Service Bus](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia 
Utworzenie nowej przestrzeni nazw powoduje automatyczne wygenerowanie początkowej reguły sygnatury dostępu współdzielonego ze skojarzoną parą kluczy podstawowego i pomocniczego, która przyznaje pełną kontrolę nad wszystkimi aspektami przestrzeni nazw. Zapoznaj się z artykułem [Uwierzytelnianie i autoryzacja w usłudze Service Bus](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md), aby dowiedzieć się, jak utworzyć reguły z bardziej ograniczonymi prawami dla zwykłych nadawców i odbiorców. Aby skopiować klucze podstawowe i pomocnicze dla przestrzeni nazw, wykonaj następujące czynności: 

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij nowo utworzoną nazwę przestrzeni nazw.
2. W oknie przestrzeni nazw kliknij pozycję **Zasady dostępu współdzielonego**.
3. Na ekranie **Zasady dostępu współdzielonego** kliknij pozycję **RootManageSharedAccessKey**.
   
    ![Zrzut ekranu przedstawia okno zasady dostępu współdzielonego z wyróżnionymi zasadami.](./media/service-bus-create-namespace-portal/connection-info.png)
4. W oknie **Zasady: RootManageSharedAccessKey** kliknij przycisk kopiowania obok pozycji **Podstawowe parametry połączenia**, aby skopiować parametry połączenia do schowka w celu późniejszego użycia. Wklej tę wartość do Notatnika lub innej tymczasowej lokalizacji.
   
    ![Zrzut ekranu przedstawia zasadę S A S o nazwie RootManageSharedAccessKey, która obejmuje klucze i parametry połączenia.](./media/service-bus-create-namespace-portal/connection-string.png)
5. Powtórz poprzedni krok, kopiując i wklejając wartość pozycji **Klucz podstawowy** w lokalizacji tymczasowej do późniejszego użycia.

<!--Image references-->

