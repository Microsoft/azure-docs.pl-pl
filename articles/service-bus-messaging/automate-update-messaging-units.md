---
title: Azure Service Bus — automatycznie Aktualizuj jednostki obsługi komunikatów
description: W tym artykule pokazano, jak za pomocą automatycznie aktualizować jednostki obsługi komunikatów Service Bus przestrzeni nazw.
ms.topic: how-to
ms.date: 09/15/2020
ms.openlocfilehash: 932c7bb1235cb54aefe67253e38e1683187f4d2c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581640"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Automatycznie Aktualizuj jednostki obsługi komunikatów Azure Service Bus przestrzeni nazw 
Funkcja automatycznego skalowania umożliwia korzystanie z odpowiedniej ilości zasobów, aby obsłużyć obciążenie aplikacji. Pozwala to na dodawanie zasobów w celu zwiększenia obciążenia, a także oszczędność pieniędzy dzięki usunięciu zasobów znajdujących się w stanie bezczynności. Zobacz [Omówienie automatycznego skalowania w Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md) , aby dowiedzieć się więcej na temat funkcji automatycznego skalowania Azure monitor. 

Warstwa Premium komunikatów usługi Service Bus zapewnia izolację zasobów na poziomie procesora CPU i pamięci, dlatego obciążenia poszczególnych klientów są od siebie odizolowane. Ten kontener zasobów jest nazywany **jednostką obsługi komunikatów**. Aby dowiedzieć się więcej o jednostkach obsługi komunikatów, zobacz [Service Bus Premium Messaging](service-bus-premium-messaging.md). 

Korzystając z funkcji automatycznego skalowania dla przestrzeni nazw w warstwie Premium Service Bus, można określić minimalną i maksymalną liczbę [jednostek obsługi komunikatów](service-bus-premium-messaging.md) oraz automatycznie dodawać lub usuwać jednostki obsługi komunikatów na podstawie zestawu reguł. 

Można na przykład zaimplementować następujące scenariusze skalowania dla przestrzeni nazw Service Bus przy użyciu funkcji skalowania automatycznego. 

- Zwiększaj liczbę jednostek obsługi komunikatów dla Service Bus przestrzeni nazw, gdy użycie procesora CPU w przestrzeni nazw przekracza 75%. 
- Zmniejsz jednostki obsługi komunikatów dla przestrzeni nazw Service Bus, gdy użycie procesora CPU w przestrzeni nazw spadnie poniżej 25%. 
- Używaj większej liczby jednostek obsługi komunikatów w godzinach pracy i mniej niż w godzinach. 

W tym artykule pokazano, jak można automatycznie skalować przestrzeń nazw Service Bus (Aktualizuj [jednostki obsługi komunikatów](service-bus-premium-messaging.md)) w Azure Portal. 

> [!IMPORTANT]
> Ten artykuł ma zastosowanie tylko do warstwy **premium** Azure Service Bus. 

## <a name="autoscale-setting-page"></a>Strona ustawienia skalowania automatycznego
Najpierw wykonaj następujące kroki, aby przejść do strony **ustawień skalowania automatycznego** dla obszaru nazw Service Bus.

1. Zaloguj się do [Azure Portal](https://portal.azure.com). 
2. Na pasku wyszukiwania wpisz **Service Bus**, z listy rozwijanej wybierz pozycję **Service Bus** , a następnie naciśnij klawisz **Enter**. 
1. Wybierz **przestrzeń nazw Premium** z listy przestrzeni nazw. 
1. Przejdź do strony **skalowanie** . 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Service Bus obszar nazw — Strona skalowania":::

## <a name="manual-scale"></a>Skalowanie ręczne 
To ustawienie umożliwia ustawienie stałej liczby jednostek obsługi komunikatów dla przestrzeni nazw. 

1. Na stronie **ustawienia skalowania automatycznego** wybierz pozycję **skalowanie ręczne** , jeśli nie została jeszcze wybrana. 
1. Dla ustawienia **jednostki obsługi komunikatów** wybierz liczbę jednostek obsługi komunikatów z listy rozwijanej.
1. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienie. 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="Ręczne skalowanie jednostek obsługi komunikatów":::       


## <a name="custom-autoscale---default-condition"></a>Niestandardowe automatyczne skalowanie — warunek domyślny
Można skonfigurować automatyczne skalowanie jednostek obsługi komunikatów przy użyciu warunków. Ten warunek skalowania jest wykonywany, gdy żaden z pozostałych warunków skalowania nie jest zgodny. Warunek domyślny można ustawić w jeden z następujących sposobów:

- Skalowanie na podstawie metryki (na przykład użycia procesora CPU lub pamięci)
- Skalowanie do określonej liczby jednostek obsługi komunikatów

Nie można ustawić harmonogramu automatycznego skalowania w określonych dniach lub zakresach dat dla warunku domyślnego. Ten warunek skalowania jest wykonywany, gdy żaden z pozostałych warunków skalowania nie jest zgodny z harmonogramem. 

### <a name="scale-based-on-a-metric"></a>Skalowanie na podstawie metryki
Poniższa procedura pokazuje, jak dodać warunek, aby automatycznie zwiększyć liczbę jednostek obsługi komunikatów (skalować w poziomie), gdy użycie procesora CPU jest większe niż 75% i zmniejsz liczbę jednostek obsługi komunikatów (skalowanie w górę), gdy użycie procesora CPU jest mniejsze niż 25%. Przyrosty są wykonywane z przedziału od 1 do 2, od 2 do 4 i od 4 do 8. Podobnie, zmniejszenia są wykonywane z 8 do 4, 4 do 2 i od 2 do 1. 

1. Na stronie **Ustawienia automatycznego skalowania** wybierz pozycję **niestandardowe Skalowanie automatyczne** dla opcji **Wybierz sposób skalowania zasobu** . 
1. W sekcji **domyślnej** strony określ **nazwę** warunku domyślnego. Wybierz ikonę **ołówka** , aby edytować tekst. 
1. Wybierz pozycję **skalowanie na podstawie metryki** dla **trybu skalowania**. 
1. Wybierz pozycję **+ Dodaj regułę**. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="Skalowanie domyślne na podstawie metryki":::    
1. Na stronie **reguła skalowania** wykonaj następujące kroki:
    1. Wybierz metrykę z listy rozwijanej **Nazwa metryki** . W tym przykładzie jest to **procesor CPU**. 
    1. Wybierz operatory i wartości progowe. W tym przykładzie są one **większe niż** i **75** dla **progu metryki do wyzwalania akcji skalowania**. 
    1. W sekcji **Akcja** wybierz **operację** . W tym przykładzie jest ustawiony na wartość **Zwiększ**. 
    1. Następnie wybierz pozycję **Dodaj**
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="Domyślne — skalowanie w poziomie, jeśli użycie procesora CPU jest większe niż 75%":::       

        > [!NOTE]
        > Funkcja automatycznego skalowania zwiększa jednostki obsługi komunikatów dla przestrzeni nazw, jeśli całkowite użycie procesora CPU przekracza 75% w tym przykładzie. Przyrosty są wykonywane z przedziału od 1 do 2, od 2 do 4 i od 4 do 8. 
1. Wybierz pozycję **+ Dodaj regułę** ponownie i wykonaj następujące kroki na stronie **reguły skalowania** :
    1. Wybierz metrykę z listy rozwijanej **Nazwa metryki** . W tym przykładzie jest to **procesor CPU**. 
    1. Wybierz operatory i wartości progowe. W tym przykładzie są one **mniejsze niż** i **25** dla **progu metryki do wyzwalania akcji skalowania**. 
    1. W sekcji **Akcja** wybierz **operację** . W tym przykładzie ustawiono opcję **Zmniejsz**. 
    1. Następnie wybierz pozycję **Dodaj** 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="Domyślne skalowanie w przypadku, gdy użycie procesora CPU jest mniejsze niż 25%":::       

        > [!NOTE]
        > Funkcja automatycznego skalowania zmniejsza jednostki obsługi komunikatów dla przestrzeni nazw, jeśli całkowite użycie procesora CPU spadnie poniżej 25% w tym przykładzie. Zmniejszenia są wykonywane z 8 do 4, 4 do 2 i od 2 do 1. 
1. Ustaw **minimalną** i **maksymalną** i **domyślną** liczbę jednostek obsługi komunikatów.

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="Domyślna reguła oparta na metryce":::
1. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienie skalowania automatycznego. 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>Skalowanie do określonej liczby jednostek obsługi komunikatów
Wykonaj następujące kroki, aby skonfigurować regułę do skalowania przestrzeni nazw do korzystania z określonej liczby jednostek obsługi komunikatów. Ponownie warunek domyślny jest stosowany, gdy żaden z pozostałych warunków skalowania nie jest zgodny. 

1. Na stronie **Ustawienia automatycznego skalowania** wybierz pozycję **niestandardowe Skalowanie automatyczne** dla opcji **Wybierz sposób skalowania zasobu** . 
1. W sekcji **domyślnej** strony określ **nazwę** warunku domyślnego. 
1. Wybierz pozycję **Skaluj do określonych jednostek obsługi komunikatów** dla **trybu skalowania**. 
1. W obszarze **jednostki obsługi komunikatów** wybierz liczbę domyślnych jednostek obsługi komunikatów. 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="Domyślne skalowanie do określonych jednostek obsługi komunikatów":::       

## <a name="custom-autoscale---additional-conditions"></a>Niestandardowe Skalowanie automatyczne — dodatkowe warunki
W poprzedniej sekcji pokazano, jak dodać warunek domyślny dla ustawienia skalowania automatycznego. W tej sekcji pokazano, jak dodać więcej warunków do ustawienia skalowania automatycznego. Aby uzyskać dodatkowe warunki inne niż domyślne, można ustawić harmonogram na podstawie określonych dni tygodnia lub zakresu dat. 

### <a name="scale-based-on-a-metric"></a>Skalowanie na podstawie metryki
1. Na stronie **Ustawienia automatycznego skalowania** wybierz pozycję **niestandardowe Skalowanie automatyczne** dla opcji **Wybierz sposób skalowania zasobu** . 
1. Wybierz pozycję **Dodaj warunek skali** w bloku **domyślnym** . 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Niestandardowy — Dodawanie łącza warunku skalowania":::    
1. Określ **nazwę** warunku. 
1. Upewnij się, że wybrano opcję **skalowanie na podstawie metryki** . 
1. Wybierz pozycję **+ Dodaj regułę** , aby dodać regułę, aby zwiększyć liczbę jednostek obsługi komunikatów, gdy całkowite użycie procesora CPU przekracza 75%. Wykonaj kroki z sekcji [warunku domyślnego](#custom-autoscale---default-condition) . 
5. Ustaw **minimalną** i **maksymalną** i **domyślną** liczbę jednostek obsługi komunikatów.
6. Możesz również ustawić **harmonogram** dla warunku niestandardowego (ale nie dla warunku domyślnego). Możesz określić datę początkową i końcową dla warunku (lub) wybrać określone dni (poniedziałek, wtorek itd.) w tygodniu. 
    1. W przypadku wybrania opcji **Określ datę początkową/końcową** wybierz pozycję **strefa czasowa**, **datę i godzinę rozpoczęcia** oraz **datę i godzinę zakończenia** (jak pokazano na poniższej ilustracji), aby obowiązywać warunek. 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="Minimalna, maksymalna i domyślna wartość dla liczby jednostek obsługi komunikatów":::
    1. W przypadku wybrania opcji **Powtarzaj określone dni** wybierz pozycję dni tygodnia, strefa czasowa, czas rozpoczęcia i czas zakończenia, kiedy warunek ma być stosowany. 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="Powtarzaj określone dni":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>Skalowanie do określonej liczby jednostek obsługi komunikatów
1. Na stronie **Ustawienia automatycznego skalowania** wybierz pozycję **niestandardowe Skalowanie automatyczne** dla opcji **Wybierz sposób skalowania zasobu** . 
1. Wybierz pozycję **Dodaj warunek skali** w bloku **domyślnym** . 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="Niestandardowy — Dodawanie łącza warunku skalowania":::    
1. Określ **nazwę** warunku. 
2. Wybierz opcję **Skaluj do określonych jednostek obsługi komunikatów** dla **trybu skalowania**. 
1. Wybierz liczbę **jednostek obsługi komunikatów** z listy rozwijanej. 
6. Dla **harmonogramu** Określ datę początkową i końcową dla warunku (lub) wybierz określone dni (poniedziałek, wtorek itd.) przez tydzień i godzinę. 
    1. W przypadku wybrania opcji **Określ daty rozpoczęcia/zakończenia** wybierz **strefę czasową**, **datę i godzinę rozpoczęcia** oraz **datę i godzinę zakończenia** obowiązywania warunku. 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="skalowanie do określonych jednostek obsługi komunikatów — daty rozpoczęcia i zakończenia":::        
    1. W przypadku wybrania opcji **Powtarzaj określone dni** wybierz pozycję dni tygodnia, strefa czasowa, czas rozpoczęcia i czas zakończenia, kiedy warunek ma być stosowany.
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="skalowanie do określonych jednostek obsługi komunikatów — powtarzanie określonych dni":::

> [!IMPORTANT]
> Aby dowiedzieć się więcej o tym, jak działają ustawienia skalowania automatycznego, szczególnie w jaki sposób wybiera profil lub warunek i szacuje wiele reguł, zobacz [Omówienie ustawień skalowania automatycznego](../azure-monitor/autoscale/autoscale-understanding-settings.md).          

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o jednostkach obsługi komunikatów, zobacz [Obsługa komunikatów w warstwie Premium](service-bus-premium-messaging.md)

