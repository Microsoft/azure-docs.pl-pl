---
title: Rozpoczynanie pracy ze skalowaniem automatycznym na platformie Azure
description: Dowiedz się, jak skalować aplikację internetową zasobów, usługę w chmurze, maszynę wirtualną lub zestaw skalowania maszyn wirtualnych na platformie Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: b8d16b4e112c9aebe86c60dc01d380d591fc7624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743526"
---
# <a name="get-started-with-autoscale-in-azure"></a>Wprowadzenie do skalowania automatycznego na platformie Azure
W tym artykule opisano sposób konfigurowania ustawień automatycznego skalowania dla zasobu w Microsoft Azure Portal.

Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)i [usług API Management](../../api-management/api-management-key-concepts.md).

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
1. Kliknij przycisk **Zapisz**.

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

Po skalowaniu do wielu wystąpień App Service może przeprowadzić kontrolę kondycji wystąpień w celu kierowania ruchu tylko do wystąpień w dobrej kondycji. Aby to zrobić, Otwórz Portal w App Service, a następnie wybierz pozycję **Sprawdzanie kondycji** w obszarze **monitorowanie**. Wybierz pozycję **Włącz** i podaj prawidłową ścieżkę URL w aplikacji, na przykład `/health` lub `/api/health` . Kliknij przycisk **Zapisz**.

Aby włączyć funkcję z szablonami ARM, należy ustawić `healthcheckpath` Właściwość `Microsoft.Web/sites` zasobu na ścieżkę kontroli kondycji w lokacji, na przykład: `"/api/health/"` . Aby wyłączyć tę funkcję, należy ustawić właściwość z powrotem na pusty ciąg, `""` .

### <a name="health-check-path"></a>Ścieżka sprawdzania kondycji

Ścieżka musi odpowiadać w ciągu dwóch minut od kodu stanu z przedziału od 200 do 299 (włącznie). Jeśli ścieżka nie odpowiada w ciągu dwóch minut lub zwraca kod stanu spoza zakresu, wystąpienie jest uznawane za "w złej kondycji". Kontrola kondycji integruje się z funkcjami uwierzytelniania i autoryzacji App Service, system dociera do punktu końcowego, nawet jeśli te funkcje Secuity są włączone. Jeśli używasz własnego systemu uwierzytelniania, ścieżka sprawdzania kondycji musi zezwalać na dostęp anonimowy. Jeśli lokacja ma tylko HTTP**s**, żądanie Healthcheck zostanie wysłane za pośrednictwem protokołu HTTP**S**.

Ścieżka sprawdzania kondycji powinna sprawdzać krytyczne składniki aplikacji. Na przykład jeśli aplikacja zależy od bazy danych i systemu obsługi komunikatów, punkt końcowy sprawdzania kondycji powinien łączyć się z tymi składnikami. Jeśli aplikacja nie może połączyć się ze składnikiem krytycznym, ścieżka powinna zwrócić kod odpowiedzi 500 na poziomie, aby wskazać, że aplikacja jest w złej kondycji.

#### <a name="security"></a>Zabezpieczenia 

Zespoły programistyczne w dużych przedsiębiorstwach często muszą przestrzegać wymagań w zakresie zabezpieczeń dla dostępnych interfejsów API. Aby zabezpieczyć punkt końcowy Healthcheck, należy najpierw użyć funkcji, takich jak [Ograniczenia adresów IP](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules), [certyfikaty klienta](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)lub Virtual Network, aby ograniczyć dostęp do aplikacji. Sam punkt końcowy Healthcheck można zabezpieczyć przez wymaganie `User-Agent` dopasowania żądania przychodzącego `ReadyForRequest/1.0` . Nie można sfałszować User-Agent, ponieważ żądanie zostało już zabezpieczone przez wcześniejsze funkcje zabezpieczeń.

### <a name="behavior"></a>Zachowanie

Gdy zostanie podana ścieżka sprawdzania kondycji, App Service będzie wysyłać polecenie ping do ścieżki we wszystkich wystąpieniach. Jeśli nie otrzymasz pomyślnego kodu odpowiedzi po 5 poleceniach ping, to wystąpienie jest uznawane za "w złej kondycji". Wystąpienia w złej kondycji zostaną wykluczone z obrotu modułu równoważenia obciążenia. Ponadto podczas skalowania w górę lub w dół App Service wyśle polecenie ping do ścieżki sprawdzania kondycji, aby upewnić się, że nowe wystąpienia są gotowe do żądania.

Pozostałe wystąpienia w dobrej kondycji mogą zwiększyć obciążenie. Aby uniknąć przeciążenia pozostałych wystąpień, nie zostaną wykluczone więcej niż połowę wystąpień. Na przykład jeśli plan App Service zostanie przeskalowany do 4 wystąpień i 3 w złej kondycji, co najwyżej 2 zostanie wykluczone z obrotu modułu równoważenia obciążenia. Pozostałe 2 wystąpienia (1 w złej kondycji i 1 w niezdrowych) będą nadal otrzymywać żądania. W scenariuszu najgorszego przypadku, w którym wszystkie wystąpienia są złej kondycji, żaden z nich nie zostanie wykluczony.

Jeśli wystąpienie pozostaje w złej kondycji w ciągu godziny, zostanie zastąpione nowym wystąpieniem. Co najwyżej jedno wystąpienie zostanie zastąpione na godzinę, co najwyżej trzy wystąpienia dziennie na App Service plan.

### <a name="monitoring"></a>Monitorowanie

Po podaniu ścieżki sprawdzania kondycji aplikacji można monitorować kondycję lokacji przy użyciu Azure Monitor. W bloku **Sprawdzanie kondycji** w portalu kliknij **metryki** na górnym pasku narzędzi. Spowoduje to otwarcie nowego bloku, w którym można zobaczyć historyczny stan kondycji lokacji i utworzyć nową regułę alertu. Więcej informacji o monitorowaniu lokacji [znajduje się w przewodniku na Azure monitor](../../app-service/web-sites-monitor.md).

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
