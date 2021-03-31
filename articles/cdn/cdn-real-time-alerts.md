---
title: Alerty w czasie rzeczywistym Azure CDN | Microsoft Docs
description: Alerty w czasie rzeczywistym w usłudze Microsoft Azure CDN. Alerty w czasie rzeczywistym zapewniają powiadomienia o wydajności punktów końcowych w Twoim profilu CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6811a06eb3483fd53b6e566033935c3b2e00ceca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887243"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alerty w czasie rzeczywistym w usłudze Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Omówienie
W tym dokumencie opisano alerty w czasie rzeczywistym w usłudze Microsoft Azure CDN. Ta funkcja zapewnia powiadomienia w czasie rzeczywistym o wydajności punktów końcowych w Twoim profilu CDN.  Możesz skonfigurować alerty e-mail lub HTTP na podstawie:

* Przepustowość
* Kody stanu
* Stany pamięci podręcznej
* Połączenia

## <a name="creating-a-real-time-alert"></a>Tworzenie alertu w czasie rzeczywistym
1. W [Azure Portal](https://portal.azure.com)przejdź do swojego profilu CDN.
   
    ![Profil CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. W bloku profil usługi CDN kliknij przycisk **Zarządzaj** .
   
    ![Przycisk zarządzania profilem CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.
3. Umieść kursor na karcie **Analiza** , a następnie umieść wskaźnik myszy na wyskakującym menu **statystyk w czasie rzeczywistym** .  Kliknij pozycję **alerty w czasie rzeczywistym**.
   
    ![Portal zarządzania CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Zostanie wyświetlona lista istniejących konfiguracji alertów (jeśli istnieje).
4. Kliknij przycisk **Dodaj alert** .
   
    ![Przycisk dodawania alertu](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Zostanie wyświetlony formularz służący do tworzenia nowego alertu.
   
    ![Nowy formularz alertu](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Jeśli chcesz, aby ten alert był aktywny po kliknięciu przycisku **Zapisz**, zaznacz pole wyboru **alert włączony** .
6. Wprowadź opisową nazwę alertu w polu **Nazwa** .
7. Na liście rozwijanej **Typ nośnika** wybierz pozycję **http Large Object**.
   
    ![Typ nośnika z wybranym dużym obiektem HTTP](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Musisz wybrać **duży obiekt http** jako **Typ nośnika**.  Inne opcje nie są używane przez **Azure CDN z Verizon**.  Niepowodzenie wybrania **dużego obiektu http** powoduje, że alert nigdy nie zostanie wyzwolony.
   > 
   > 
8. Utwórz **wyrażenie** do monitorowania, wybierając **metrykę**, **operator** i **wartość wyzwalającą**.
   
   * W obszarze **Metryka** wybierz typ warunku, który ma być monitorowany.  **Przepustowość MB/s** to wielkość użycia przepustowości w megabitach na sekundę.  **Suma połączeń** to liczba jednoczesnych połączeń HTTP z serwerami brzegowymi.  Aby uzyskać definicje różnych stanów pamięci podręcznej i kodów stanu, zobacz [Azure CDN kodów stanu pamięci podręcznej](/previous-versions/azure/mt759237(v=azure.100)) i [Azure CDN kodów stanu HTTP](/previous-versions/azure/mt759238(v=azure.100))
   * **Operator** jest operatorem matematycznym, który określa relację między metryką a wartością wyzwalacza.
   * **Wartość wyzwalacza** to wartość progowa, która musi zostać spełniona przed wysłaniem powiadomienia.
     
     W poniższym przykładzie utworzone wyrażenie wskazuje, że powiadomienie jest wysyłane, gdy liczba kodów stanu 404 jest większa niż 25.
     
     ![Wyrażenie przykładowe alertu w czasie rzeczywistym](./media/cdn-real-time-alerts/cdn-expression.png)
9. Dla **interwału** Określ, jak często ma być oceniane wyrażenie.
10. Na liście rozwijanej **powiadamianie** wybierz, Kiedy chcesz otrzymywać powiadomienia, gdy wyrażenie ma wartość true.
    
    * **Początkowy warunek** wskazuje, że powiadomienie jest wysyłane po wykryciu określonego warunku.
    * **Końcowy warunek** wskazuje, że powiadomienie jest wysyłane, gdy określony warunek nie zostanie już wykryty. To powiadomienie można wywołać tylko po wykryciu przez nasz system monitorowania sieci określonego warunku.
    * Wartość **ciągła** wskazuje, że powiadomienie jest wysyłane za każdym razem, gdy system monitorowania sieci wykryje określony warunek. Należy pamiętać, że system monitorowania sieci sprawdza tylko raz dla danego warunku.
    * **Warunek początkowy i końcowy** wskazuje, że powiadomienie jest wysyłane po raz pierwszy po wykryciu określonego warunku i ponownie, gdy warunek nie zostanie już wykryty.
1. Jeśli chcesz otrzymywać powiadomienia pocztą e-mail, zaznacz pole wyboru **Powiadamiaj pocztą e-mail** .  
    
    ![Wiadomość E-mail z powiadomieniem](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    W polu **do** wprowadź adres e-mail, na który mają być wysyłane powiadomienia. W przypadku **tematu** i **treści** można pozostawić wartość domyślną lub można dostosować komunikat przy użyciu listy **dostępne słowa kluczowe** , aby dynamicznie wstawiać dane alertu podczas wysyłania wiadomości.
    
    > [!NOTE]
    > Powiadomienie e-mail można przetestować, klikając przycisk **Testuj powiadomienie** , ale dopiero po zapisaniu konfiguracji alertów.
    > 
    > 
12. Jeśli chcesz, aby powiadomienia były ogłaszane na serwerze sieci Web, zaznacz pole wyboru **Powiadamiaj przy użyciu protokołu HTTP Post** .
    
    ![Powiadamiaj za pomocą formularza POST protokołu HTTP](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    W polu **adres URL** wprowadź adres URL, na który ma zostać ogłoszona wiadomość http. W polu tekstowym **nagłówki** wprowadź nagłówki HTTP, które mają być wysyłane w żądaniu.  W przypadku **treści** można dostosować komunikat przy użyciu listy **dostępne słowa kluczowe** , aby dynamicznie wstawiać dane alertu podczas wysyłania komunikatu.  **Nagłówki** i **treść** są domyślne w ładunku XML podobnym do poniższego przykładu:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Powiadomienie HTTP POST można przetestować, klikając przycisk **Testuj powiadomienie** , ale dopiero po zapisaniu konfiguracji alertów.
    > 
    > 
13. Kliknij przycisk **Zapisz** , aby zapisać konfigurację alertu.  Jeśli w kroku 5 został sprawdzony **włączony alert** , Twój alert jest teraz aktywny.

## <a name="next-steps"></a>Następne kroki
* Analizowanie [statystyk w czasie rzeczywistym w Azure CDN](cdn-real-time-stats.md)
* Dig bardziej [Zaawansowane raporty http](cdn-advanced-http-reports.md)
* Analizowanie [wzorców użycia](cdn-analyze-usage-patterns.md)

