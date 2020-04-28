---
title: Wyświetl Azure Advisor zaleceń dotyczących Ciebie
description: Wyświetl i Filtruj zalecenia dotyczące Azure Advisor, aby zmniejszyć liczbę szumów.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422376"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Wyświetl Azure Advisor zaleceń dotyczących Ciebie

Azure Advisor zawiera zalecenia ułatwiające optymalizację wdrożeń platformy Azure. W ramach usługi Advisor masz dostęp do kilku funkcji, które ułatwiają zawężenie zaleceń do użytkownika.

## <a name="configure-subscriptions-and-resource-groups"></a>Konfigurowanie subskrypcji i grup zasobów

Doradca umożliwia wybranie subskrypcji i grup zasobów, które mają znaczenie dla Ciebie i Twojej organizacji. Zobaczysz tylko zalecenia dotyczące wybranych subskrypcji i grup zasobów. Domyślnie wszystkie są zaznaczone. Ustawienia konfiguracji dotyczą subskrypcji lub grupy zasobów, dlatego te same ustawienia mają zastosowanie do wszystkich użytkowników, którzy mają dostęp do tej subskrypcji lub grupy zasobów. Ustawienia konfiguracji można zmienić w Azure Portal lub programowo.

Aby wprowadzić zmiany w Azure Portal:

1. Otwórz [Azure Advisor](https://aka.ms/azureadvisordashboard) w Azure Portal.

1. Z menu wybierz pozycję **Konfiguracja** .

   ![Menu konfiguracji usługi Advisor](./media/view-recommendations/configuration.png)

1. Zaznacz pole wyboru w kolumnie **Dołącz** dla wszystkich subskrypcji lub grup zasobów, aby otrzymywać zalecenia usługi Advisor. Jeśli to pole jest wyłączone, może nie mieć uprawnień do wprowadzania zmian w konfiguracji tej subskrypcji lub grupy zasobów. Dowiedz się więcej o [uprawnieniach w Azure Advisor](permissions.md).

1. Po wprowadzeniu zmian kliknij przycisk **Zastosuj** u dołu.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrowanie widoku w Azure Portal

Ustawienia konfiguracji pozostają aktywne, dopóki nie zostaną zmienione. Jeśli chcesz ograniczyć widok zaleceń dla pojedynczego wyświetlania, możesz użyć listy rozwijanej znajdującej się w górnej części panelu Advisor. Z paneli przegląd, wysoka dostępność, zabezpieczenia, wydajność, koszt i wszystkie rekomendacje możesz wybrać subskrypcje, typy zasobów i stan rekomendacji, które mają być wyświetlane.

   ![Menu filtrowania klasyfikatora](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Odrzucanie i odkładanie zaleceń

Azure Advisor pozwala odrzucić lub odłożyć zalecenia dotyczące pojedynczego zasobu. Jeśli odjdziesz do rekomendacji, nie widzisz jej ponownie, chyba że ręcznie ją aktywujesz. Jednak odroczenie rekomendacji pozwala określić czas trwania, po którym zalecenie zostanie ponownie aktywowane automatycznie. Odroczenie można wykonać w Azure Portal lub programowo.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Odłożenie jednego zalecenia w Azure Portal 

1. Otwórz [Azure Advisor](https://aka.ms/azureadvisordashboard) w Azure Portal.
1. Wybierz kategorię rekomendacji, aby wyświetlić rekomendacje
1. Wybierz zalecenie z listy zaleceń
1. Wybierz opcję Odłóż lub Odrzuć dla zalecenia, które chcesz odłożyć lub odrzucić

     ![Menu filtrowania klasyfikatora](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Odłóż lub Odrzuć wiele zaleceń w Azure Portal

1. Otwórz [Azure Advisor](https://aka.ms/azureadvisordashboard) w Azure Portal.
1. Wybierz kategorię rekomendacji, aby wyświetlić swoje zalecenia.
1. Wybierz zalecenie z listy zaleceń.
1. Zaznacz pole wyboru po lewej stronie wiersza dla wszystkich zasobów, które chcesz odłożyć lub odrzucić zalecenia.
1. W lewym górnym rogu tabeli wybierz pozycję **Odłóż** lub **Odrzuć** .

     ![Menu filtrowania klasyfikatora](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Musisz mieć uprawnienia współautora lub właściciela, aby odrzucić lub odłożyć zalecenie. Dowiedz się więcej o uprawnieniach w Azure Advisor.

> [!NOTE]
> Jeśli pola wyboru są wyłączone, nadal mogą być ładowane zalecenia. Przed podjęciem próby odłożenia lub odrzucenia Zaczekaj na załadowanie wszystkich zaleceń.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Ponowne uaktywnianie odroczonego lub odrzuconego zalecenia

Można aktywować zalecenie, które zostało odroczone lub odrzucone. Tę akcję można wykonać w Azure Portal lub programowo. W witrynie Azure Portal:

1. Otwórz [Azure Advisor](https://aka.ms/azureadvisordashboard) w Azure Portal.

1. Zmień filtr w panelu przegląd, aby **przesunięty**. Następnie usługa Advisor wyświetla odroczone lub odrzucone zalecenia.

    ![Menu filtrowania klasyfikatora](./media/view-recommendations/activate-postponed.png)

1. Wybierz kategorię, aby zobaczyć **odroczone** i **odrzucone** zalecenia.

1. Wybierz zalecenie z listy zaleceń. Spowoduje to otwarcie zaleceń z **założoną kartą odroczone & odrzucone** , aby wyświetlić zasoby, dla których to zalecenie zostało odroczone lub odrzucone.

1. Kliknij pozycję **Aktywuj** na końcu wiersza. Po kliknięciu Rekomendacja jest aktywna dla tego zasobu i dlatego została usunięta z tej tabeli. Zalecenie jest teraz widoczne na **aktywnej** karcie.
 
     ![Menu filtrowania klasyfikatora](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule wyjaśniono, jak można wyświetlić zalecenia dotyczące Ciebie w Azure Advisor. Aby dowiedzieć się więcej na temat usługi Advisor, zobacz: 

- [Co to jest usługa Azure Advisor?](advisor-overview.md)
- [Wprowadzenie z usługą Advisor](advisor-get-started.md)
- [Uprawnienia w Azure Advisor](permissions.md)



