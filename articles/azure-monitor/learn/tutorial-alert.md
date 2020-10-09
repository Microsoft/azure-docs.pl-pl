---
title: Wysyłanie alertów z usługi Azure Application Insights | Microsoft Docs
description: Samouczek dotyczący wysyłania alertów w odpowiedzi na błędy w aplikacji za pomocą usługi Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 706f3913e25eca6240c186e45709faf6c77620bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324577"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Monitorowanie kondycji aplikacji i wysyłanie powiązanych alertów za pomocą usługi Azure Application Insights

Usługa Azure Application Insights umożliwia monitorowanie aplikacji i wysyłania alertów, gdy aplikacja jest niedostępna lub występują w niej błędy bądź problemy z wydajnością.  Ten samouczek przeprowadzi Cię przez proces tworzenia testów w celu ciągłego sprawdzenia dostępności aplikacji.

Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie testu dostępności stale sprawdzającego odpowiedź aplikacji
> * Wysyłanie wiadomości e-mail do administratorów, gdy występuje problem

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

Utwórz [zasób Application Insights](./dotnetcore-quick-start.md#enable-application-insights).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Tworzenie testu dostępności

Testy dostępności w usłudze Application Insights umożliwiają automatyczne testowanie aplikacji z różnych lokalizacji na całym świecie.   W tym samouczku wykonasz test adresu URL, aby upewnić się, że aplikacja sieci Web jest dostępna.  Możesz również utworzyć szczegółowy przewodnik, aby dokładnie przetestować działanie aplikacji. 

1. Wybierz pozycję **Application Insights**, a następnie wybierz swoją subskrypcję.  

2. Wybierz pozycję **dostępność** w menu **Zbadaj** , a następnie kliknij pozycję **Utwórz test**.

    ![Dodawanie testu dostępności](media/tutorial-alert/add-test-001.png)

3. Wpisz nazwę testu i pozostaw inne wartości domyślne.  Wybranie tej opcji spowoduje wyzwolenie żądania dla adresu URL aplikacji co 5 minut od pięciu różnych lokalizacji geograficznych.

4. Wybierz pozycję **alerty** , aby otworzyć listę rozwijaną **alerty** , w której można zdefiniować szczegóły dotyczące sposobu reagowania w przypadku niepowodzenia testu. Wybierz pozycję **niemal w czasie rzeczywistym** i Ustaw stan na **włączone.**

    Wpisz adres e-mail, na który zostanie wysłana wiadomość, gdy zostaną spełnione kryteria alertu.  Opcjonalnie można wpisać adres elementu webhook, który ma zostać wywołany po spełnieniu kryteriów alertu.

    ![Tworzenie testu](media/tutorial-alert/create-test-001.png)

5. Wróć do panelu test, wybierz wielokropek i edytuj alert, aby wprowadzić konfigurację dla alertu niemal w czasie rzeczywistym.

    ![Edytuj alert](media/tutorial-alert/edit-alert-001.png)

6. Ustawianie lokalizacji zakończonych niepowodzeniem na wartość większą lub równą 3. Utwórz [grupę akcji](../platform/action-groups.md) , aby skonfigurować, kto ma otrzymywać powiadomienia o naruszeniu progu alertu.

    ![Zapisz interfejs użytkownika alertu](media/tutorial-alert/save-alert-001.png)

7. Po skonfigurowaniu alertu kliknij nazwę testu, aby wyświetlić szczegóły z poszczególnych lokalizacji. Testy można wyświetlać zarówno w postaci wykresu liniowego, jak i wykresu punktowego, aby wizualizować sukcesy i niepowodzenia w danym przedziale czasu.

    ![Szczegóły testu](media/tutorial-alert/test-details-001.png)

8. Aby przejść do szczegółów dowolnego testu, kliknij jego kropkę na wykresie punktowym. Spowoduje to uruchomienie widoku szczegółów transakcji end-to-end. W poniższym przykładzie przedstawiono szczegółowe informacje dotyczące żądania zakończonego niepowodzeniem.

    ![Wynik testu](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Następne kroki

Po poznaniu sposobów tworzenia alertów w odpowiedzi na problemy przejdź do następnego samouczka, aby się dowiedzieć, jak analizować sposób, w jaki użytkownicy wchodzą w interakcje z Twoją aplikacją.

> [!div class="nextstepaction"]
> [Zrozumienie zachowań użytkowników](./tutorial-users.md)

