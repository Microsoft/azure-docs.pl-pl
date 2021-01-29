---
title: Wprowadzenie do skalowania automatycznego na platformie Azure
description: Dowiedz się, jak skalować aplikację internetową zasobów, usługę w chmurze, maszynę wirtualną lub zestaw skalowania maszyn wirtualnych na platformie Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 9bbd4da77d2892064906dc7ae272bcc770b6bdc4
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055284"
---
# <a name="get-started-with-autoscale-in-azure"></a>Wprowadzenie do autoskalowania na platformie Azure
W tym artykule opisano sposób konfigurowania ustawień automatycznego skalowania dla zasobu w Microsoft Azure Portal.

Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [zestawów skalowania maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)i [usług API Management](../../api-management/api-management-key-concepts.md).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Odkryj ustawienia skalowania automatycznego w Twojej subskrypcji

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Możesz odkryć wszystkie zasoby, dla których Skalowanie automatyczne ma zastosowanie w Azure Monitor. Wykonaj następujące kroki, aby zapoznać się z przewodnikiem krok po kroku:

1. Otwórz [Azure Portal.][1]
1. Kliknij ikonę Azure Monitor w lewym okienku.
  ![Otwórz Azure Monitor][2]
1. Kliknij pozycję **Automatyczne skalowanie** , aby wyświetlić wszystkie zasoby, dla których ma zastosowanie automatyczne skalowanie, wraz z bieżącym stanem automatycznego skalowania.
  ![Odnajdź automatyczne skalowanie w Azure Monitor][3]

Możesz użyć okienka filtr u góry, aby określić zakres w dół listy, aby wybrać zasoby w określonej grupie zasobów, określonych typach zasobów lub konkretnego zasobu.

Dla każdego zasobu znajduje się bieżąca liczba wystąpień i stan skalowania automatycznego. Stan skalowania automatycznego to:

- **Nie skonfigurowano**: nie włączono jeszcze funkcji automatycznego skalowania dla tego zasobu.
- **Włączone**: włączono funkcję automatycznego skalowania dla tego zasobu.
- **Wyłączone**: wyłączono automatyczne skalowanie dla tego zasobu.

## <a name="create-your-first-autoscale-setting"></a>Utwórz pierwsze ustawienie automatycznego skalowania

Teraz przejdźmy do prostego przewodnika krok po kroku, aby utworzyć pierwsze ustawienie automatycznego skalowania.

1. Otwórz blok **Skalowanie automatyczne** w Azure monitor i wybierz zasób, który chcesz skalować. (W poniższych krokach użyto planu App Service skojarzonego z aplikacją sieci Web. Możesz [utworzyć swoją pierwszą aplikację sieci web ASP.NET na platformie Azure w ciągu 5 minut.][4]
1. Należy pamiętać, że bieżąca liczba wystąpień to 1. Kliknij pozycję **Włącz automatyczne skalowanie**.
  ![Ustawienie skalowania dla nowej aplikacji sieci Web][5]
1. Podaj nazwę ustawienia skalowania, a następnie kliknij pozycję **Dodaj regułę**. Zwróć uwagę na Opcje reguły skalowania, które są otwierane jako okienko kontekstowe po prawej stronie. Domyślnie powoduje to ustawienie opcji skalowanie liczby wystąpień o 1, jeśli wartość procentowa procesora CPU zasobu przekracza 70 procent. Pozostaw wartości domyślne, a następnie kliknij przycisk **Dodaj**.
  ![Tworzenie ustawienia skalowania dla aplikacji sieci Web][6]
1. Twoja pierwsza reguła skalowania została teraz utworzona. Należy pamiętać, że środowisko użytkownika zaleca najlepsze rozwiązania i stwierdza, że "zalecane jest posiadanie co najmniej jednej reguły skalowania". W tym celu:

    a. Kliknij pozycję **Dodaj regułę**.

    b. Ustaw **operator** na wartość **mniejszą niż**.

    c. Ustaw **próg** na **20**.

    d. Ustaw **operację** , aby **zmniejszyć liczbę przez**.

   Teraz powinno być dostępne ustawienie skalowania, które umożliwia skalowanie w poziomie i skalowanie w zależności od użycia procesora CPU.
   ![Skalowanie w oparciu o procesor CPU][8]
1. Kliknij pozycję **Zapisz**.

Gratulacje! Pomyślnie utworzono pierwsze ustawienie skalowania w celu automatycznego skalowania aplikacji sieci Web na podstawie użycia procesora CPU.

> [!NOTE]
> Te same kroki mają zastosowanie do rozpoczynania pracy z zestawem skalowania maszyn wirtualnych lub rolą usługi w chmurze.

## <a name="other-considerations"></a>Inne zagadnienia
### <a name="scale-based-on-a-schedule"></a>Skalowanie na podstawie harmonogramu
Oprócz skalowania opartego na procesorze CPU można ustawić skalę w różny sposób dla określonych dni tygodnia.

1. Kliknij pozycję **Dodaj warunek skali**.
1. Ustawianie trybu skalowania i reguł jest taka sama jak w przypadku warunku domyślnego.
1. Wybierz opcję **Powtarzaj określone dni** w harmonogramie.
1. Wybierz dni i godzinę rozpoczęcia/zakończenia, dla których ma zostać zastosowany warunek skali.

![Warunek skalowania na podstawie harmonogramu][9]
### <a name="scale-differently-on-specific-dates"></a>Skalowanie w różny sposób w przypadku określonych dat
Oprócz skalowania opartego na procesorze CPU można ustawić skalę w różny sposób dla konkretnych dat.

1. Kliknij pozycję **Dodaj warunek skali**.
1. Ustawianie trybu skalowania i reguł jest taka sama jak w przypadku warunku domyślnego.
1. Wybierz pozycję **Określ daty rozpoczęcia/zakończenia** harmonogramu.
1. Wybierz datę początkową/końcową oraz godzinę początkową/końcową, dla których ma zostać zastosowany warunek skali.

![Warunek skalowania na podstawie dat][10]

### <a name="view-the-scale-history-of-your-resource"></a>Wyświetlanie historii skali zasobu
Za każdym razem, gdy zasób jest skalowany w górę lub w dół, rejestrowane jest zdarzenie w dzienniku aktywności. Możesz wyświetlić historię skalowania zasobu w ciągu ostatnich 24 godzin, przełączając się na kartę **historia uruchamiania** .

![Historia uruchamiania][11]

Jeśli chcesz wyświetlić pełną historię skalowania (przez maksymalnie 90 dni), wybierz **pozycję kliknij tutaj, aby zobaczyć więcej szczegółów**. Zostanie otwarty dziennik aktywności z opcją automatycznego skalowania wstępnie wybraną dla zasobu i kategorii.

### <a name="view-the-scale-definition-of-your-resource"></a>Wyświetlanie definicji skali zasobu
Automatyczne skalowanie jest zasobem Azure Resource Manager. Definicję skali można wyświetlić w formacie JSON, przełączając się na kartę **JSON** .

![Definicja skali][12]

Zmiany w formacie JSON można wprowadzać bezpośrednio, w razie potrzeby. Te zmiany zostaną odzwierciedlone po ich zapisaniu.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Wyłącz automatyczne skalowanie i ręcznie Skaluj wystąpienia
Mogą wystąpić sytuacje, w których chcesz wyłączyć bieżące ustawienie skalowania i ręcznie skalować zasób.

Kliknij przycisk **Wyłącz automatyczne skalowanie** u góry.
![Wyłącz automatyczne skalowanie][13]

> [!NOTE]
> Ta opcja powoduje wyłączenie konfiguracji. Można jednak wrócić do niego po ponownym włączeniu automatycznego skalowania.

Teraz można ustawić liczbę wystąpień, które mają być skalowane ręcznie.

![Ustaw skalowanie ręczne][14]

Zawsze możesz wrócić do automatycznego skalowania, klikając pozycję **Włącz automatyczne skalowanie** , a następnie przycisk **Zapisz**.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Kierowanie ruchu do wystąpień w dobrej kondycji (App Service)

<a id="health-check-path"></a>

Gdy aplikacja sieci Web platformy Azure jest skalowana w poziomie do wielu wystąpień, App Service może przeprowadzić kontrolę kondycji wystąpień w celu kierowania ruchu do wystąpień w dobrej kondycji. Aby dowiedzieć się więcej, zobacz [ten artykuł na App Service kontroli kondycji](../../app-service/monitor-instances-health-check.md).

## <a name="moving-autoscale-to-a-different-region"></a>Przeniesienie skalowania automatycznego do innego regionu
W tej sekcji opisano, jak przenieść automatyczne skalowanie platformy Azure do innego regionu w ramach tej samej subskrypcji i grupy zasobów. Aby przenieść ustawienia automatycznego skalowania, można użyć interfejsu API REST.
### <a name="prerequisite"></a>Wymaganie wstępne
1. Upewnij się, że subskrypcja i Grupa zasobów są dostępne, a szczegóły w regionach źródłowym i docelowym są identyczne.
1. Upewnij się, że funkcja automatycznego skalowania platformy Azure jest dostępna w [regionie świadczenia usługi Azure, do którego chcesz przejść](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).

### <a name="move"></a>Move
Użyj [interfejsu API REST](/rest/api/monitor/autoscalesettings/createorupdate) , aby utworzyć ustawienie automatycznego skalowania w nowym środowisku. Ustawienie skalowania automatycznego utworzone w regionie docelowym będzie kopią ustawienia skalowania automatycznego w regionie źródłowym.

Nie można przenieść [ustawień diagnostycznych](./diagnostic-settings.md) , które zostały utworzone w skojarzeniu z ustawieniem skalowania automatycznego w regionie źródłowym. Należy ponownie utworzyć ustawienia diagnostyczne w regionie docelowym po zakończeniu tworzenia ustawień autosprzedaży. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Dowiedz się więcej o przenoszeniu zasobów między regionami platformy Azure
Aby dowiedzieć się więcej o przenoszeniu zasobów między regionami i odzyskiwaniem po awarii na platformie Azure, zapoznaj się z tematem [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md) .

## <a name="next-steps"></a>Następne kroki
- [Tworzenie alertu dziennika aktywności w celu monitorowania wszystkich operacji aparatu automatycznego skalowania w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Utwórz alert dziennika aktywności, aby monitorować wszystkie operacje skalowania automatycznego skalowania w poziomie i skalowania w poziomie w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
