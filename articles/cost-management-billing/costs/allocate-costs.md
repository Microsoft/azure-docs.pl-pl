---
title: Alokowanie kosztów platformy Azure
description: W tym artykule wyjaśniono, jak utworzyć reguły alokacji kosztów w celu dystrybuowania kosztów subskrypcji, grup zasobów lub tagów do innych użytkowników.
author: bandersmsft
ms.author: banders
ms.date: 03/23/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: e7afef7e0a10bb4be3c30112fc207467167e4a17
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726524"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Tworzenie reguł alokacji kosztów platformy Azure i zarządzanie nimi (wersja zapoznawcza)

Duże przedsiębiorstwa często korzystają z usług lub zasobów platformy Azure zarządzanych centralnie, ale używanych przez różne wewnętrzne działy lub jednostki biznesowe. Zazwyczaj zespół ds. zarządzania centralnego ponownie przydziela koszty usług udostępnionych do wewnętrznych działów lub jednostek organizacyjnych, które aktywnie korzystają z tych usług. Ten artykuł pomaga zrozumieć koncepcję alokacji kosztów w usłudze Azure Cost Management i wyjaśnia sposób jej użycia.

Alokacja kosztów umożliwia ponowne przydzielenie lub rozłożenie kosztów usług udostępnionych w ramach subskrypcji, grup zasobów lub tagów do innych subskrypcji, grup zasobów lub tagów w organizacji. Alokacja kosztów powoduje przeniesienie kosztów usług udostępnionych do innej subskrypcji lub innych grup zasobów czy tagów należących do zużywających je wewnętrznych działów lub jednostek biznesowych. Innymi słowy, alokacja kosztów ułatwia zarządzanie _koszami_ i wyświetlanie ich z uwzględnieniem różnych lokalizacji.

Alokacja kosztów nie ma wpływu na fakturę. Obowiązki dotyczące rozliczeń pozostają niezmienione. Głównym celem alokacji kosztów jest pomoc w przeniesieniu kosztów do innych jednostek. Wszystkie procesy związane z obciążeniem zwrotnym są wykonywane w organizacji poza platformą Azure. Alokacja kosztów ułatwia dokonywanie obciążenia zwrotnego kosztów poprzez wyświetlanie ich podczas ponownego przydzielania lub dystrybuowania.

Wgląd w przydzielone koszty można uzyskać w obszarze analizy kosztów. Są one wyświetlane jako dodatkowe elementy skojarzone z docelowymi subskrypcjami, grupami zasobów lub tagami określonymi podczas tworzenia reguły alokacji kosztów.

> [!NOTE]
> Funkcja alokacji kosztów w usłudze Azure Cost Management jest obecnie dostępna w publicznej wersji zapoznawczej. Niektóre funkcje usługi Cost Management mogą nie być obsługiwane lub mogą mieć ograniczone możliwości.

## <a name="prerequisites"></a>Wymagania wstępne

- Alokacja kosztów jest aktualnie dostępna tylko dla klientów, którzy mają [umowę z Klientem Microsoft](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) lub [umowę Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/).
- Aby utworzyć regułę alokacji kosztów lub zarządzać nią, należy użyć konta administratora przedsiębiorstwa stosowanego w ramach [umów Enterprise](../manage/understand-ea-roles.md). Można też użyć [konta rozliczeniowego](../manage/understand-mca-roles.md) stosowanego w ramach umów z Klientem Microsoft, o ile jest się jego właścicielem.

## <a name="create-a-cost-allocation-rule"></a>Tworzenie reguły alokacji kosztów

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com/).
2. Przejdź do obszaru **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami**.
3. W obszarze **Ustawienia** > **Konfiguracja** wybierz pozycję **Alokacja kosztów (wersja zapoznawcza)** .
4. Upewnij się, że wybrano właściwe konto rejestracji lub konto rozliczeniowe umowy EA.
5. Wybierz pozycję **+Dodaj**.
6. Wprowadź tekst opisu dla nazwy reguły alokacji kosztów.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Przykład przedstawiający tworzenie nazwy reguły" lightbox="./media/allocate-costs/rule-name.png" :::

Data rozpoczęcia oceny reguły umożliwia generowanie wstępnie wypełnionych wartości procentowych alokacji kosztów.

1. Wybierz pozycję **Dodaj źródła**, a następnie wybierz subskrypcje, grupy zasobów lub tagi, aby wskazać koszty poddawane dystrybucji.
2. Wybierz pozycję **Dodaj elementy docelowe**, a następnie wybierz subskrypcje, grupy zasobów lub tagi, które otrzymają przydzielone koszty.
3. Jeśli musisz utworzyć dodatkowe reguły alokacji kosztów, powtórz ten proces.

## <a name="configure-the-allocation-percentage"></a>Konfigurowanie procentu alokacji

Skonfigurowanie procentu alokacji pozwala określić sposób proporcjonalnego rozdzielania kosztów pomiędzy określone elementy docelowe. Utworzenie reguły alokacji jest również możliwe poprzez ręczne zdefiniowanie wartości procentowych. Koszty można też rozdzielić proporcjonalnie w oparciu o bieżące wykorzystanie mocy obliczeniowej, magazynu lub sieci przez poszczególne elementy docelowe.

W przypadku dystrybucji kosztów według kosztów zasobów obliczeniowych, kosztów magazynu lub kosztów sieci proporcjonalną wartość procentową uzyskuje się poprzez ocenę wybranych kosztów elementów docelowych. Koszty są skojarzone z typem zasobu dla bieżącego miesiąca rozliczeniowego.

Podczas dystrybucji kosztów w stosunku do łącznych kosztów wartość procentowa jest przydzielana proporcjonalnie na podstawie sumy lub łącznego kosztu wybranych elementów docelowych w bieżącym miesiącu rozliczeniowym.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Przykład przedstawiający procent alokacji" lightbox="./media/allocate-costs/cost-distribution.png" :::

Następnie określa się wstępnie wypełnione stałe wartości procentowe. Mają one zastosowanie do wszystkich bieżących alokacji. Wartości procentowe ulegają zmianie tylko wtedy, gdy reguła jest aktualizowana ręcznie.

1. Na liście **Wypełnij wstępnie procent do** wybierz jedną z następujących opcji.
    - **Rozłóż równomiernie** — każdy element docelowy otrzymuje równy procent całkowitego kosztu.
    - **Całkowity koszt** – tworzy współczynnik proporcjonalny względem elementów docelowych w oparciu o ich całkowity koszt. Współczynnik ten służy do podziału kosztów pochodzących z określonych źródeł.
    - **Koszt zasobów obliczeniowych** — tworzy współczynnik proporcjonalny względem elementów docelowych w oparciu o ich koszt zasobów obliczeniowych na platformie Azure (typy zasobów w przestrzeni nazw [Microsoft. Compute](/azure/templates/microsoft.compute/allversions)). Współczynnik ten służy do podziału kosztów pochodzących z określonych źródeł.
    - **Koszt magazynu** — tworzy współczynnik proporcjonalny względem elementów docelowych w oparciu o ich koszty magazynu na platformie Azure (typy zasobów w przestrzeni nazw [Microsoft.Storage](/azure/templates/microsoft.storage/allversions)). Współczynnik ten służy do podziału kosztów pochodzących z określonych źródeł.
    - **Koszt sieci** — tworzy współczynnik proporcjonalny względem elementów docelowych w oparciu o ich koszty sieci na platformie Azure (typy zasobów w przestrzeni nazw [Microsoft. Network](/azure/templates/microsoft.network/allversions)). Współczynnik ten służy do podziału kosztów pochodzących z określonych źródeł.
    - **Niestandardowe** – umożliwia ręczne określenie wartości procentowej. Podane wartości muszą w sumie wynosić równo 100%.
1. Po skonfigurowaniu reguły wybierz pozycję **Utwórz**.

Reguła alokacji rozpocznie przetwarzanie. Gdy reguła jest aktywna, wszystkie koszty wybranego źródła są przydzielane do określonych elementów docelowych.

> [!NOTE] 
> Przetwarzanie nowej reguły w celu jej aktywacji może potrwać do dwóch godzin.

## <a name="verify-the-cost-allocation-rule"></a>Weryfikowanie reguły alokacji kosztów

Gdy reguła alokacji kosztów jest aktywna, koszty z wybranych źródeł są dystrybuowane do określonych elementów docelowych alokacji. Korzystając z poniższych informacji, zweryfikuj, czy koszt został prawidłowo przydzielony do elementów docelowych.

### <a name="view-cost-allocation-for-a-subscription"></a>Wyświetlanie alokacji kosztów dla subskrypcji

Wpływ reguły alokacji można sprawdzić w obszarze analizy kosztów. W witrynie Azure Portal przejdź do sekcji [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Wybierz z listy subskrypcję objętą aktywną regułą alokacji kosztów. Następnie z menu wybierz pozycję **Analiza kosztów**. W obszarze Analiza kosztów wybierz pozycję **Grupuj według**, a następnie wybierz pozycję **Alokacja kosztów**. W wyświetlonym widoku pojawi się skrócony podział kosztów wygenerowany przez subskrypcję, a także koszty przydzielone do danej subskrypcji (co widać na poniższej ilustracji).

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Przykład przedstawiający podział kosztów" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Wyświetlanie alokacji kosztów dla grupy zasobów

Podobny proces należy zastosować w przypadku reguły alokacji kosztów dotyczącej grupy zasobów. W witrynie Azure Portal przejdź do strony [Grupy zasobów](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). Wybierz z listy grupę zasobów objętą aktywną regułą alokacji kosztów. Następnie z menu wybierz pozycję **Analiza kosztów**. W obszarze Analiza kosztów wybierz pozycję **Grupuj według**, a następnie wybierz pozycję **Alokacja kosztów**. W wyświetlonym widoku pojawi się skrócony podział kosztów wygenerowany przez grupę zasobów, a także koszt przydzielony do danej grupy zasobów.

### <a name="view-cost-allocation-for-tags"></a>Wyświetlanie alokacji kosztów dla tagów

W witrynie Azure Portal przejdź do obszaru **Zarządzanie kosztami i rozliczenia** > **Zarządzanie kosztami** > **Analiza kosztów**. Na stronie Analiza kosztów wybierz pozycję **Dodaj filtr**. Wybierz pozycję **Tag**, a następnie wybierz klucz tagu i wartości tagów, do których przydzielono koszty.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Przykład przedstawiający koszty dla elementów otagowanych" lightbox="./media/allocate-costs/tagged-costs.png" :::

Oto wideo, w których pokazano, jak utworzyć regułę alokacji kosztów.

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]


## <a name="edit-an-existing-cost-allocation-rule"></a>Edytowanie istniejącej reguły alokacji kosztów

Regułę alokacji kosztów można edytować w celu zmiany źródła lub elementu docelowego, a także zaktualizowania wstępnie wypełnionej wartości procentowej dotyczącej zasobów obliczeniowych, magazynu lub sieci. Edycja reguł przebiega w podobny sposób, co ich tworzenie. Ponowna modyfikacja istniejących reguł może potrwać do dwóch godzin.

## <a name="current-limitations"></a>Bieżące ograniczenia

Alokacja kosztów w usłudze Cost Management jest obecnie obsługiwana w widokach analizy kosztów, budżetów i prognoz. Przydzielone koszty są również wyświetlane na liście subskrypcji i na stronie Omówienie subskrypcji.

Publiczna wersja zapoznawcza alokacji kosztów nie obsługuje obecnie następujących elementów:

- Zaplanowane [eksporty](tutorial-export-acm-data.md)
- Dane udostępniane przez interfejs API [szczegółów użycia](/rest/api/consumption/usagedetails/list)
- Obszar subskrypcji rozliczeniowych
- [Aplikacja Cost Management w usłudze Power BI](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Łącznik w programie Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management)


## <a name="next-steps"></a>Następne kroki

- Przeczytaj często [zadawane Cost Management + Billing odpowiedzi](../cost-management-billing-faq.yml) na pytania i odpowiedzi dotyczące alokacji kosztów.
- Utwórz lub zaktualizuj reguły alokacji za pomocą [Interfejsu API Rest alokacji kosztów](/rest/api/cost-management/costallocationrules)
- Dowiedz się więcej na temat [sposobu optymalizowania inwestycji w chmurę za pomocą usługi Azure Cost Management](cost-mgt-best-practices.md)