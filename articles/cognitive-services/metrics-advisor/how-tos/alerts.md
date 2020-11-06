---
title: Konfigurowanie alertów klasyfikatora metryk
titleSuffix: Azure Cognitive Services
description: Jak skonfigurować alerty klasyfikatora metryk przy użyciu punktów zaczepienia dla poczty e-mail, sieci Web i usługi Azure DevOps.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420924"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Instrukcje: Konfigurowanie alertów i otrzymywanie powiadomień przy użyciu punktu zaczepienia

Po wykryciu anomalii przez klasyfikatora metryk zostanie wyzwolone powiadomienie o alercie na podstawie ustawień alertów przy użyciu punktu zaczepienia. Ustawienia alertu można użyć w połączeniu z wieloma konfiguracjami wykrywania, ale różne parametry są dostępne do dostosowania reguły alertu.

## <a name="create-a-hook"></a>Tworzenie punktu zaczepienia

Klasyfikator metryk obsługuje trzy różne typy punktów zaczepienia: hak poczty e-mail, element webhook i Azure DevOps. Możesz wybrać ten, który działa w konkretnym scenariuszu.       

### <a name="email-hook"></a>Hak poczty e-mail

> [!Note]
> Administratorzy zasobów klasyfikatora metryk muszą skonfigurować ustawienia poczty E-mail oraz dane wejściowe protokołu SMTP do usługi Metric Advisor przed wysłaniem alertów anomalii. Administrator grupy zasobów lub administrator subskrypcji musi przypisać co najmniej jedną Cognitive Services rolę *administratora klasyfikatora metryk* na karcie kontroli dostępu zasobu klasyfikatora metryk. [Dowiedz się więcej na temat konfiguracji ustawień poczty e-mail](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

Aby utworzyć punkt zaczepienia wiadomości e-mail, dostępne są następujące parametry: 

Punkt zaczepienia wiadomości e-mail to kanał służący do wysyłania alertów anomalii do adresów e-mail określonych w sekcji **wiadomości e-mail** . Wysyłane są dwa typy wiadomości e-mail z alertami: *strumieniowe źródło danych nie jest dostępne* i *raporty zdarzeń* zawierające jedną lub wiele anomalii. 

|Parametr |Opis  |
|---------|---------|
| Nazwa | Nazwa punktu zaczepienia wiadomości e-mail |
| Wyślij wiadomość e-mail do| Adresy e-mail, na które będą wysyłane alerty|
| Link zewnętrzny | Opcjonalne pole, które umożliwia niestandardowe przekierowanie, takie jak uwagi dotyczące rozwiązywania problemów. |
| Dostosowany Tytuł alertu anomalii | Szablon tytułu obsługuje `${severity}` , `${alertSettingName}` , `${datafeedName}` , `${metricName}` , `${detectConfigName}` , `${timestamp}` , `${topDimension}` , `${incidentCount}` , `${anomalyCount}`

Po kliknięciu przycisku **OK** zostanie utworzony punkt zaczepienia wiadomości e-mail. Można go użyć w dowolnym ustawieniach alertu w celu otrzymywania alertów anomalii. 

### <a name="web-hook"></a>Element webhook

> [!Note]
> * Użyj metody **post** żądania.
> * Treść żądania będzie być podobna do:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Gdy element webhook jest tworzony lub modyfikowany, interfejs API zostanie wywołany jako test z pustą treścią żądania. Interfejs API musi zwrócić kod HTTP 200.

Element webhook jest punktem wejścia dla wszystkich informacji dostępnych w usłudze Advisor metryk i wywołuje interfejs API udostępniony przez użytkownika po wyzwoleniu alertu. Wszystkie alerty można wysyłać za pomocą elementu webhook.

Aby utworzyć element webhook, należy dodać następujące informacje:

|Parametr |Opis  |
|---------|---------|
|Punkt końcowy     | Adres interfejsu API, który ma zostać wywołany w przypadku wyzwolenia alertu.        |
|Nazwa użytkownika/hasło | Do uwierzytelniania na adres API. Pozostaw ten kolor czarny, jeśli uwierzytelnianie nie jest wymagane.         |
|Header     | Niestandardowe nagłówki w wywołaniu interfejsu API.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="okno tworzenia elementu webhook.":::

Gdy powiadomienie jest wypychane za pomocą elementu webhook, można użyć następujących interfejsów API, aby uzyskać szczegółowe informacje o alercie. Ustaw *sygnaturę czasową* i *alertSettingGuid* w usłudze interfejsu API, która jest używana do wypychania do, a następnie użyj następujących zapytań: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

Klasyfikator metryk obsługuje również automatyczne tworzenie elementu pracy w usłudze Azure DevOps w celu śledzenia problemów/błędów w przypadku wykrycia nieprawidłowości. Wszystkie alerty można wysyłać za pomocą punktów zaczepienia usługi Azure DevOps.

Aby utworzyć punkt zaczepienia usługi Azure DevOps, należy dodać następujące informacje

|Parametr |Opis  |
|---------|---------|
| Nazwa | Nazwa punktu zaczepienia |
| Organizacja | Organizacja, do której należy DevOps |
| Project | Konkretny projekt w DevOps. |
| Token dostępu |  Token do uwierzytelniania w usłudze DevOps. | 

> [!Note]
> Musisz przyznać uprawnienia do zapisu, jeśli chcesz, aby klasyfikator metryk utworzył elementy robocze na podstawie alertów anomalii. Po utworzeniu punktów zaczepienia można korzystać z nich w dowolnym z ustawień alertów. Zarządzanie punktami zaczepienia na stronie **Ustawienia punktu zaczepienia** .

## <a name="add-or-edit-alert-settings"></a>Dodawanie lub edytowanie ustawień alertów

Przejdź do strony szczegółów metryk, aby znaleźć sekcję **Ustawienia alertu** w lewym dolnym rogu strony szczegółów metryk. Zawiera listę wszystkich ustawień alertów, które mają zastosowanie do wybranej konfiguracji wykrywania. Po utworzeniu nowej konfiguracji wykrywania nie ma żadnego ustawienia alertu i alerty nie będą wysyłane.  
Aby zmodyfikować ustawienia alertów, można użyć ikon **Dodaj** , **Edytuj** i **Usuń** .

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Element menu Ustawienia alertu.":::

Kliknij przycisk **Dodaj** lub **Edytuj** , aby wyświetlić okno umożliwiające dodanie lub edycję ustawień alertów.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Dodawanie lub edytowanie ustawień alertów":::

**Nazwa ustawienia alertu** : Nazwa tego ustawienia alertu. Zostanie ona wyświetlona w tytule wiadomości e-mail z alertami.

**Punkty zaczepienia** : lista punktów zaczepienia, do których mają być wysyłane alerty.

Sekcja zaznaczona na poniższym zrzucie ekranu to ustawienia dla jednej konfiguracji wykrywania. Można ustawić różne ustawienia alertów dla różnych konfiguracji wykrywania. Wybierz konfigurację docelową przy użyciu trzeciej listy rozwijanej w tym oknie. 

### <a name="filter-settings"></a>Ustawienia filtru 

Poniżej przedstawiono ustawienia filtru dla jednej konfiguracji wykrywania.

**Alert dla programu** ma 4 opcje filtrowania anomalii:

* **Anomalie we wszystkich seriach** : wszystkie anomalie zostaną uwzględnione w alercie.         
* **Anomalie w grupie serii** : Filtruj Serie według wartości wymiarów. Ustaw określone wartości dla niektórych wymiarów. Anomalie zostaną uwzględnione w alercie tylko wtedy, gdy seria pasuje do określonej wartości.       
* **Anomalie w ulubionych seriach** : tylko seria oznaczona jako ulubiona zostanie uwzględniona w alercie.        |
* **Anomalie w pierwszych N wszystkich seriach** : filtr jest przeznaczony dla przypadku, w którym należy zwrócić uwagę na serię, której wartość znajduje się w pierwszych N. Będziemy szukać niektórych sygnatur czasowych i sprawdzić, czy wartość serii w tych sygnaturach czasowych jest w pierwszych N. Jeśli liczba "w pierwszych n" jest większa niż określona liczba, anomalia zostanie uwzględniona w alercie.        |

**Filtrowanie opcji anomalii** jest dodatkowym filtrem z następującymi opcjami:

- **ważność** : anomalia zostanie uwzględniona tylko wtedy, gdy ważność anomalii znajduje się w określonym zakresie.
- **Odłóż** : tymczasowo Zatrzymaj alerty dla anomalii w następnych N punktach (okresie), gdy zostanie wyzwolone w alercie.
    - odłożenie **typu** : po ustawieniu na **serie** wyzwolone anomalie przejdzie tylko na jego serię. W przypadku **metryki** jedna wyzwolona anomalia przeniesie wszystkie serie w tej metryki.
    - odłożenie **liczby** : liczba punktów (okres) do odłożenia.
    - **Resetuj dla nieprawidłowego** : w przypadku wybrania wyzwalanej anomalii spowoduje odłożenie tylko następnych niepowodzeń anomalii. Jeśli jeden z następujących punktów danych nie jest anomalią, odłożenie zostanie zresetowane z tego punktu; Po zaznaczeniu tej opcji, jedna wyzwolona anomalia odbędzie odłożyć następne n punktów (okres), nawet jeśli kolejne punkty danych nie będą anomaliami.
- **wartość** (opcjonalnie): Filtruj według wartości. Tylko wartości punktów, które spełniają warunek, będą uwzględniane anomalie. Jeśli używasz odpowiedniej wartości innej metryki, nazwy wymiarów dwóch metryk powinny być spójne.

Anomalie, które nie zostały odfiltrowane, zostaną wysłane w alercie.

### <a name="add-cross-metric-settings"></a>Dodawanie ustawień międzymetrycznych

Kliknij pozycję **+ Dodaj ustawienia krzyżowe** na stronie Ustawienia alertu, aby dodać kolejną sekcję.

Selektor **operatora** jest relacją logiczną każdej sekcji, aby określić, czy wyśle alert.


|Operator  |Opis  |
|---------|---------|
|AND     | Wyślij alert tylko wtedy, gdy seria pasuje do każdej sekcji alertu, a wszystkie punkty danych są anomaliami. Jeśli metryki mają różne nazwy wymiarów, alert nigdy nie zostanie wyzwolony.         |
|LUB     | Wyślij alert, jeśli co najmniej jedna sekcja zawiera anomalie.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Operator dla wielu sekcji ustawień alertów":::

## <a name="next-steps"></a>Następne kroki

- [Dostosowywanie wykrywania anomalii przy użyciu opinii](anomaly-feedback.md)
- [Diagnozuj zdarzenie](diagnose-incident.md).
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](configure-metrics.md)