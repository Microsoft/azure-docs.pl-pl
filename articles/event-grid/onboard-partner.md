---
title: Dołączanie jako partner Azure Event Grid przy użyciu Azure Portal
description: Użyj Azure Portal, aby dołączyć partnera Azure Event Grid.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 36fab35923b8a536a9054e5dc4bfa4c5b82172a7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103045"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>Dołączanie jako partner Azure Event Grid przy użyciu Azure Portal
W tym artykule opisano sposób, w jaki dostawcy SaaS innych firm, znani również wydawców lub partnerów zdarzeń, są dołączane do Event Grid, aby można było publikować zdarzenia z ich usług oraz jak te zdarzenia są używane przez użytkowników końcowych.

> [!IMPORTANT]
> Jeśli nie masz doświadczenia ze zdarzeniami partnerskimi, zobacz [Omówienie zdarzeń partnerskich](partner-events-overview.md) , aby zapoznać się ze szczegółowymi informacjami na temat najważniejszych pojęć, które mają na celu zrozumienie, i wykonaj kroki opisane w tym artykule.

## <a name="onboarding-process-for-event-publishers-partners"></a>Proces dołączania dla wydawców zdarzeń (partnerów)
W Nutshell, umożliwienie wykorzystaniu przez użytkowników zdarzeń usługi zwykle obejmuje następujący proces:

1. **Poinformuj Cię** , że jesteś partnerem z zespołem usługi Event Grid, zanim przejdziesz do kolejnego kroku.
1. Utwórz typ tematu partnera, tworząc **rejestrację** . 
1. Utwórz **przestrzeń nazw** .
1. Tworzenie **kanału zdarzeń** i **tematu partnera** (pojedynczy krok).
1. Przetestuj zakończenie funkcji zdarzeń partnerskich.

Aby zapoznać się z krokami #4, należy zdecydować, jakiego rodzaju wrażenia użytkownika należy podać. Możesz wybrać następujące opcje:
- Udostępniaj własne rozwiązanie, zwykle środowisko graficznego interfejsu użytkownika (GUI), hostowane w domenie przy użyciu naszego zestawu SDK i/lub interfejsu API REST do utworzenia kanału zdarzeń i odpowiedniego tematu partnera. Za pomocą tej opcji można poprosił użytkownika o subskrypcję i grupę zasobów, w ramach której utworzysz temat partnera.
- Użyj Azure Portal lub interfejsu wiersza polecenia, aby utworzyć kanał zdarzeń i skojarzony z nim temat partnera. Po wybraniu tej opcji należy uzyskać subskrypcję platformy Azure użytkownika w sposób niezbędny i grupę zasobów, w której zostanie utworzony temat partnera. 

W tym artykule opisano, jak dołączyć Azure Event Grid partnera przy użyciu Azure Portal. 

> [!NOTE]
> Zarejestrowanie typu tematu partnera jest opcjonalnym krokiem. Aby ułatwić podjęcie decyzji, czy należy utworzyć typ tematu partnera, zobacz [zasoby zarządzane przez wydawcę zdarzeń](partner-events-overview.md#resources-managed-by-event-publishers).

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Przekaż zainteresowanie partnerem
Wypełnij [ten formularz](https://aka.ms/gridpartnerform) i skontaktuj się z zespołem Event Grid pod adresem [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . Będziemy mieć konwersacje ze szczegółowymi informacjami na temat przypadków użycia, osób, procesu dołączania, funkcji, cen i innych.

## <a name="prerequisites"></a>Wymagania wstępne
Aby wykonać pozostałe kroki, upewnij się, że masz:

- Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .
- [Dzierżawa](../active-directory/develop/quickstart-create-new-tenant.md)platformy Azure.

## <a name="register-a-partner-topic-type-optional"></a>Zarejestruj typ tematu partnera (opcjonalnie)
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. W okienku nawigacji po lewej stronie wybierz pozycję **wszystkie usługi** , a następnie wpisz w **Event Grid rejestracje partnerów** na pasku wyszukiwania, a następnie wybierz go. 
1. Na stronie **rejestracje partnera Event Grid** wybierz pozycję **+ Dodaj** na pasku narzędzi. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Dodaj link do rejestracji partnera":::
1. Na stronie **Tworzenie typów rejestracji tematu partnera — podstawowe** wprowadź następujące informacje: 
    1. W sekcji **szczegóły projektu** wykonaj następujące kroki:
        1. Wybierz swoją **subskrypcję** platformy Azure. 
        1. Wybierz istniejącą **grupę zasobów** platformy Azure lub Utwórz nową grupę zasobów. 
    1. W sekcji **szczegóły rejestracji** wykonaj następujące kroki:
        1. W polu **Nazwa rejestracji** wprowadź nazwę rejestracji. 
        1. W polu **Nazwa organizacji** wprowadź nazwę organizacji. 
    1. W sekcji **Typ zasobu partnera** wprowadź szczegóły dotyczące typu zasobu, który będzie wyświetlany na stronie **Tworzenie tematu partnera** : 
        1. W **polu Nazwa typu zasobu partnera** wprowadź nazwę dla typu zasobu. Będzie to typ tematu partnera, który zostanie utworzony w ramach subskrypcji platformy Azure. 
        2. W polu **Nazwa wyświetlana** wprowadź przyjazną dla użytkownika nazwę wyświetlaną dla typu tematu partnera (zasobu). 
        3. Wprowadź **Opis typu zasobu** . 
        4. Wprowadź **opis scenariusza** . Powinien on wyjaśniać sposoby użycia lub scenariusze, w których można używać tematów partnerskich dotyczących zasobów.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Dodaj link do rejestracji partnera":::            
1. Wybierz pozycję **Dalej: Usługa niestandardowa** w dolnej części strony. Na karcie **Usługa klienta** na stronie **Tworzenie rejestracji partnera** wprowadź informacje, których użytkownicy będą używać w celu skontaktowania się z Tobą w przypadku problemu ze źródłem zdarzenia:
    1. Wprowadź **numer telefonu** .
    1. Wprowadź numer **wewnętrzny** numeru telefonu.
    1. Wprowadź **adres URL** witryny sieci Web pomocy technicznej. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Dodaj link do rejestracji partnera":::        
1. Wybierz pozycję **Dalej: Tagi** w dolnej części strony. 
1. Na stronie **Tagi** skonfiguruj następujące wartości. 
    1. Wprowadź **nazwę** i **wartość** tagu, który chcesz dodać. Ten krok jest **opcjonalny** . 
    1. Wybierz pozycję **Przegląd + Utwórz** u dołu strony, aby utworzyć rejestrację (typ tematu partnera).

## <a name="create-a-partner-namespace"></a>Tworzenie przestrzeni nazw partnerskiej

1. W Azure Portal wybierz pozycję **wszystkie usługi** w menu nawigacji po lewej stronie, a następnie wpisz **Event Grid przestrzenie nazw partnerów** na pasku wyszukiwania, a następnie wybierz je z listy. 
1. Na stronie **przestrzenie nazw partnerów Event Grid** wybierz pozycję **+ Dodaj** na pasku narzędzi. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Dodaj link do rejestracji partnera":::
1. Na stronie **Tworzenie partnera przestrzeni nazw — podstawowe** podaj następujące informacje.
    1. W sekcji **szczegóły projektu** wykonaj następujące czynności: 
        1. Wybierz **subskrypcję** platformy Azure.
        1. Wybierz istniejącą **grupę zasobów** lub Utwórz grupę zasobów. 
    1. W sekcji **szczegóły przestrzeni nazw** wykonaj następujące czynności:
        1. Wprowadź **nazwę** dla przestrzeni nazw. 
        1. Wybierz **lokalizację** dla przestrzeni nazw. 
    1. W sekcji **szczegóły rejestracji** wykonaj następujące kroki, aby skojarzyć przestrzeń nazw z rejestracją partnera. 
        1. Wybierz **subskrypcję** , w ramach której istnieje Rejestracja partnera. 
        1. Wybierz **grupę zasobów** zawierającą rejestrację partnera. 
        1. Z listy rozwijanej wybierz pozycję **rejestracja partnera** .
    1. Wybierz pozycję **Dalej: Tagi** w dolnej części strony.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Dodaj link do rejestracji partnera":::
1. Na stronie **Tagi** Dodaj Tagi (opcjonalne).
    1. Wprowadź **nazwę** i **wartość** tagu, który chcesz dodać. Ten krok jest **opcjonalny** .
    1. Wybierz pozycję **Recenzja + Utwórz** w dolnej części strony.         
1. Na stronie **Recenzja i tworzenie** Przejrzyj szczegóły, a następnie wybierz pozycję **Utwórz** . 

## <a name="create-an-event-channel"></a>Tworzenie kanału zdarzeń
> [!IMPORTANT]
> Musisz poprosić użytkownika o subskrypcję platformy Azure, grupę zasobów, lokalizację i nazwę tematu partnera, aby utworzyć temat partnera, który będzie używany przez użytkownika i zarządzanie nim.

1. Przejdź do strony **Przegląd** utworzonej przestrzeni nazw. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Dodaj link do rejestracji partnera":::
    partner-namespace-overview.png
1. Wybierz pozycję **+ kanał zdarzenia** na pasku narzędzi. 
1. Na stronie **Tworzenie kanału zdarzeń — podstawowe** określ poniższe informacje. 
    1. W sekcji **szczegóły kanału** wykonaj następujące czynności:
        1. W polu **Nazwa kanału zdarzenia** wprowadź nazwę kanału zdarzenia. 
        1. Wprowadź **Źródło** . Zobacz [specyfikacje zdarzeń 1,0 w chmurze](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) , aby uzyskać pomysł na odpowiednią wartość dla źródła. Zobacz również [Ten przykładowy schemat zdarzeń w chmurze](cloud-event-schema.md#sample-event-using-cloudevents-schema).
        1. Wprowadź źródło (co to jest?).
    1. W sekcji **szczegóły miejsca docelowego** wprowadź szczegóły dotyczące tematu partnera docelowego, który zostanie utworzony dla tego kanału zdarzeń. 
        1. Wprowadź **Identyfikator subskrypcji** , w której zostanie utworzony temat partnera. 
        1. Wprowadź **nazwę grupy zasobów** , w której zostanie utworzony zasób tematu partnera. 
        1. Wprowadź **nazwę tematu partnera** . 
    1. Wybierz pozycję **Dalej: filtry** w dolnej części strony. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Dodaj link do rejestracji partnera":::
1. Na stronie **filtry** Dodaj filtry. wykonaj następujące kroki:
    1. Filtrowanie według atrybutów każdego zdarzenia. Tylko zdarzenia, które pasują do wszystkich filtrów. Można określić maksymalnie 25 filtrów. Porównania uwzględniają wielkość liter. Prawidłowe klucze używane dla filtrów różnią się w zależności od schematu zdarzenia. W poniższym przykładzie,, `eventid` , `source` `eventtype` i `eventtypeversioin` może być używany dla kluczy. Możesz również użyć właściwości niestandardowych wewnątrz ładunku danych, używając `.` jako operatora zagnieżdżania. Na przykład: `data` , `data.key` , `data.key1.key2` .
    1. Wybierz pozycję **Dalej: dodatkowe funkcje** w dolnej części strony. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Dodaj link do rejestracji partnera":::
        create-event-channel-filters-page.png
1. Na stronie **dodatkowe funkcje** można ustawić **czas wygaśnięcia** i **opis tematu partnera** . 
    1. **Czas wygaśnięcia** to godzina, o której temat i skojarzony z nim kanał zdarzenia zostaną automatycznie usunięte, jeśli klient nie zostanie aktywowany. W przypadku, gdy nie podano czasu, zostanie użyta wartość domyślna wynosząca siedem dni. Zaznacz pole wyboru, aby określić własny czas wygaśnięcia. 
    1. Ponieważ ten temat jest zasobem, który nie został utworzony przez użytkownika, **Opis** może pomóc użytkownikowi zrozumieć charakter tego tematu. Ogólny opis zostanie podany, jeśli żaden nie jest ustawiony. Zaznacz pole wyboru, aby ustawić własny opis tematu partnera. 
    1. Wybierz pozycję **Dalej: Przeglądanie i tworzenie** . 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Dodaj link do rejestracji partnera":::
1. Na stronie **Przegląd + tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz** , aby utworzyć kanał zdarzenia. 

## <a name="next-steps"></a>Następne kroki
- [Przegląd tematów dotyczących partnerów](partner-topics-overview.md)
- [Formularz dołączania tematów partnerskich](https://aka.ms/gridpartnerform)
- [Rozwiązanie Auth0 partnera](auth0-overview.md)
- [Jak korzystać z tematu partnera rozwiązanie Auth0](auth0-how-to.md)
