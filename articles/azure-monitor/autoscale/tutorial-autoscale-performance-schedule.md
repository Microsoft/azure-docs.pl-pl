---
title: Automatyczne skalowanie zasobów platformy Azure na podstawie danych lub harmonogramu
description: Tworzenie ustawienia automatycznego skalowania dla planu usługi App Service przy użyciu danych metryk i harmonogramu
author: anirudhcavale
services: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.subservice: autoscale
ms.openlocfilehash: b5bcc68ddf26dd1fb8421224307c5aa20179afab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100627783"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Tworzenie ustawienia automatycznego skalowania dla zasobów platformy Azure na podstawie danych wydajności lub harmonogramu

Ustawienia automatycznego skalowania umożliwiają dodawanie/usuwanie wystąpień usługi na podstawie wstępnie ustawionych warunków. Te ustawienia można tworzyć za pośrednictwem portalu. Ta metoda zapewnia interfejs użytkownika oparty na przeglądarce przeznaczony do tworzenia i konfigurowania ustawienia automatycznego skalowania. 

W tym samouczku zostaną wykonane następujące czynności: 
> [!div class="checklist"]
> * Tworzenie aplikacji internetowej i planu usługi App Service
> * Konfigurowanie reguł automatycznego skalowania w celu skalowania na zewnątrz i do wewnątrz na podstawie liczby żądań odbieranych przez aplikację internetową
> * Wyzwalanie akcji skalowania do wewnątrz i obserwowanie wzrostu liczby wystąpień
> * Wyzwalanie akcji skalowania na zewnątrz i obserwowanie spadku liczby wystąpień
> * Czyszczenie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do [Azure Portal](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>Tworzenie aplikacji internetowej i planu usługi App Service
1. Kliknij opcję **Utwórz zasób** z okienka nawigacji po lewej stronie.
2. Wyszukaj i wybierz element *Aplikacja internetowa*, a następnie kliknij pozycję **Utwórz**.
3. Wybierz nazwę aplikacji, np. *TestowaAplikacjaInternetowaSkalowania*. Utwórz nową grupę zasobów *myResourceGroup' lub umieść ją w wybranej grupie zasobów.

Twoje zasoby powinny zostać aprowizowane w ciągu kilku minut. W pozostałej części tego samouczka korzystaj z tej aplikacji internetowej i odpowiadającego jej planu usługi App Service.

   ![Tworzenie nowej usługi App Service w portalu](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Przechodzenie do ustawień automatycznego skalowania
1. W okienku nawigacji po lewej stronie wybierz opcję **Monitor**. Po załadowaniu strony wybierz kartę **Automatyczne skalowanie**.
2. Tutaj znajduje się lista zasobów w ramach Twojej subskrypcji, które obsługują automatyczne skalowanie. Zidentyfikuj plan usługi App Service, który został utworzony wcześniej w samouczku, i kliknij go.

    ![Zrzut ekranu przedstawia Azure Portal z monitorem, a następnie automatycznie Skaluj zaznaczone.](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. W ustawieniu automatycznego skalowania kliknij przycisk **Włącz automatyczne skalowanie**.

Kilka następnych kroków pomoże Ci w takim wypełnieniu ekranu automatycznego skalowania, aby wyglądał jak na poniższym obrazie:

   ![Zapisywanie ustawienia automatycznego skalowania](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="configure-default-profile"></a>Konfigurowanie profilu domyślnego
1. W polu **Nazwa** podaj nazwę ustawienia automatycznego skalowania.
2. Upewnij się, że w profilu domyślnym opcja **Tryb skalowania** jest ustawiona na wartość „Skaluj do określonej liczby wystąpień”.
3. Ustaw liczbę wystąpień na **1**. To ustawienie powoduje, że kiedy żaden inny profil nie jest aktywny (nie obowiązuje), profil domyślny przywraca wartość 1 dla liczby wystąpień.

   ![Zrzut ekranu przedstawia stronę ustawienia skalowania automatycznego o nazwie wprowadzonej dla ustawienia.](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Tworzenie profilu cyklu

1. Kliknij link **Dodaj warunek skalowania** w obszarze profilu domyślnego.

2. Zmień wartość w polu **Nazwa** profilu na „Profil od poniedziałku do piątku”.

3. Upewnij się, że opcja **Tryb skalowania** jest ustawiona na wartość „Skalowanie na podstawie metryki”.

4. W sekcji **Limity wystąpień** ustaw pozycję **Minimum** na wartość „1”, pozycję **Maksimum** na wartość „2”, a pozycję **Domyślnie** na wartość „1”. To ustawienie zapewni, że profil nie przeprowadzi automatycznego skalowania planu usługi w taki sposób, aby miał mniej niż 1 wystąpienie lub więcej niż 2 wystąpienia. Jeśli profil nie ma wystarczających danych do podjęcia decyzji, zastosuje domyślną liczbę wystąpień (w tym przypadku 1).

5. Dla pozycji **Harmonogram** wybierz wartość „Powtarzaj w określone dni”.

6. Ustaw profil tak, aby powtarzał się od poniedziałku do piątku, od 09:00 do 18:00 czasu pacyficznego. To ustawienie powoduje, że profil jest aktywny i obowiązuje tylko od 9:00 do 18:00, od poniedziałku do piątku. W pozostałych godzinach ustawienie automatycznego skalowania korzysta z profilu „Domyślny”.

## <a name="create-a-scale-out-rule"></a>Tworzenie reguły zwiększania skali w poziomie

1. W profilu „Profil od poniedziałku do piątku”.

2. Kliknij link **Dodaj regułę**.

3. Ustaw pozycję **Źródło metryki** na wartość „Inny zasób”. Ustaw pozycję **Typ zasobu** na wartość „App Services” i pozycję **Zasób** na aplikację internetową utworzoną wcześniej w tym samouczku.

4. Ustaw pozycję **Agregacja czasu** na wartość „Łącznie”, pozycję **Nazwa metryki** na wartość „Żądania” i pozycję **Statystyka ziarna czasu** na wartość „Suma”.

5. Ustaw pozycję **Operator** na wartość „Większe niż”, pozycję **Próg** na wartość „10” i pozycję **Czas trwania** na „5” minut.

6. Ustaw pozycję **Operacja** na wartość „Zwiększ liczbę o”, pozycję **Liczba wystąpień** na wartość „1” i pozycję **Czas ochładzania** na „5” minut.

7. Kliknij przycisk **Dodaj** .

Ta reguła zapewnia, że jeśli aplikacja internetowa odbierze więcej niż 10 żądań w czasie nie dłuższym niż 5 minut, do Twojego planu usługi App Service zostanie dodane jedno wystąpienie na potrzeby zarządzania obciążeniem.

   ![Tworzenie reguły zwiększania skali w poziomie](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Tworzenie reguły zmniejszania skali w poziomie
Zaleca się, aby wraz z regułą skalowania do wewnątrz zawsze istniała reguła skalowania na zewnątrz. Jeśli istnieją obie reguły, zasoby nie będą nadmiernie aprowizowane. Nadmierna aprowizacja oznacza, że działa więcej wystąpień niż jest to konieczne do obsługi bieżącego obciążenia. 

1. W profilu „Profil od poniedziałku do piątku”.

2. Kliknij link **Dodaj regułę**.

3. Ustaw pozycję **Źródło metryki** na wartość „Inny zasób”. Ustaw pozycję **Typ zasobu** na wartość „App Services” i pozycję **Zasób** na aplikację internetową utworzoną wcześniej w tym samouczku.

4. Ustaw pozycję **Agregacja czasu** na wartość „Łącznie”, pozycję **Nazwa metryki** na wartość „Żądania” i pozycję **Statystyka ziarna czasu** na wartość „Średnia”.

5. Ustaw pozycję **Operator** na wartość „Mniejsze niż”, pozycję **Próg** na wartość „5” i pozycję **Czas trwania** na „5” minut.

6. Ustaw pozycję **Operacja** na wartość „Zmniejsz liczbę o”, pozycję **Liczba wystąpień** na wartość „1” i pozycję **Czas ochładzania** na „5” minut.

7. Kliknij przycisk **Dodaj** .

    ![Tworzenie reguły zmniejszania skali w poziomie](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Zapisz** ustawienie automatycznego skalowania.

    ![Zapisywanie ustawienia automatycznego skalowania](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Wyzwalanie akcji skalowania do wewnątrz
Aby wyzwolić warunek skalowania do wewnątrz we właśnie utworzonym ustawieniu automatycznego skalowania, aplikacja internetowa musi odebrać ponad 10 żądań w mniej niż 5 minut.

1. Otwórz okno przeglądarki i przejdź do aplikacji internetowej utworzonej wcześniej w tym samouczku. Adres URL aplikacji internetowej można znaleźć w witrynie Azure Portal, przechodząc do zasobu aplikacji internetowej i klikając przycisk **Przeglądaj** na karcie „Przegląd”.

2. Szybko ponownie załaduj stronę ponad 10 razy.

3. W okienku nawigacji po lewej stronie wybierz opcję **Monitor**. Po załadowaniu strony wybierz kartę **Skalowanie automatyczne** .

4. Z listy wybierz plan usługi App Service używany w tym samouczku.

5. W ustawieniu automatycznego skalowania kliknij kartę **Historia uruchamiania**.

6. Zostanie wyświetlony wykres odzwierciedlający liczbę wystąpień planu usługi App Service w czasie.

7. W ciągu kilku minut liczba wystąpień powinna wzrosnąć z 1 na 2.

8. Pod wykresem znajdują się wpisy dziennika aktywności dla każdej akcji skalowania podjętej przez to ustawienie automatycznego skalowania.

## <a name="trigger-scale-in-action"></a>Wyzwalanie akcji skalowania na zewnątrz
Warunek skalowania na zewnątrz w ustawieniu automatycznego skalowania jest wyzwalany, jeśli w ciągu 10 minut aplikacja internetowa odbierze mniej niż 5 żądań. 

1. Upewnij się, że żadne żądania nie są wysyłane do aplikacji internetowej.

2. Załaduj witrynę Azure Portal.

3. W okienku nawigacji po lewej stronie wybierz opcję **Monitor**. Po załadowaniu strony wybierz kartę **Skalowanie automatyczne** .

4. Z listy wybierz plan usługi App Service używany w tym samouczku.

5. W ustawieniu automatycznego skalowania kliknij kartę **Historia uruchamiania**.

6. Zostanie wyświetlony wykres odzwierciedlający liczbę wystąpień planu usługi App Service w czasie.

7. W ciągu kilku minut liczba wystąpień powinna zmniejszyć się z 2 na 1. Zajmie to co najmniej 100 minut.  

8. Pod wykresem znajdują się odpowiednie wpisy dziennika aktywności dla każdej akcji skalowania podjętej przez to ustawienie automatycznego skalowania.

    ![Widok akcji skalowania na zewnątrz](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz aplikację internetową utworzoną w tym samouczku.

2. Na stronie zasobów kliknij pozycję **Usuń**, potwierdź usunięcie, wpisując w polu tekstowym wartość **tak**, i kliknij pozycję **Usuń**.

3. Następnie wybierz zasób planu usługi App Service i kliknij pozycję **Usuń**.

4. Potwierdź usunięcie, wpisując w polu tekstowym wartość **tak**, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:  
> [!div class="checklist"]
> * Utworzenie aplikacji internetowej i planu usługi App Service
> * Skonfigurowanie reguł automatycznego skalowania w celu skalowania na zewnątrz i do wewnątrz na podstawie liczby żądań odbieranych przez aplikację internetową
> * Wyzwolenie akcji skalowania do wewnątrz i zaobserwowanie wzrostu liczby wystąpień
> * Wyzwolenie akcji skalowania na zewnątrz i zaobserwowanie spadku liczby wystąpień
> * Wyczyszczenie zasobów


Aby dowiedzieć się więcej na temat ustawień automatycznego skalowania, przejdź do [omówienia automatycznego skalowania](../autoscale/autoscale-overview.md).

> [!div class="nextstepaction"]
> [Archiwizuj dane monitorowania](../essentials/platform-logs-overview.md)

