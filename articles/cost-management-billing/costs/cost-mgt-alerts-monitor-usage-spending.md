---
title: Monitorowanie użycia i wydatków przy użyciu alertów dotyczących kosztów
description: W tym artykule opisano, jak alerty dotyczące kosztów ułatwiają monitorowanie użycia i wydatków w usłudze Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: d1546ef92d923b9d72c4aca0ddf61d2b93646656
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602347"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Monitorowanie użycia i wydatków za pomocą alertów o kosztach

Ten artykuł ułatwia zapoznanie się z alertami usługi Cost Management oraz używanie ich do monitorowania użycia i wydatków na platformie Azure. Alerty dotyczące kosztów są generowane automatycznie w oparciu o zużycie zasobów platformy Azure. Alerty pokazują wszystkie aktywne alerty dotyczące zarządzanie kosztami i rozliczeń w jednym miejscu. Gdy zużycie osiągnie określoną wartość progową, alerty są generowane przez usługę Cost Management. Istnieją trzy typy alertów dotyczących kosztów: alerty budżetowe, alerty kredytowe i alerty dotyczące limitu przydziału wydatków dla działu.

## <a name="budget-alerts"></a>Alerty budżetowe

Alerty budżetowe informują, gdy wydatki, w oparciu o użycie lub koszt, osiągną lub przekroczą kwotę zdefiniowaną w [warunku alertu budżetu](tutorial-acm-create-budgets.md). Budżety usługi Cost Management są tworzone za pomocą witryny Azure Portal lub interfejsu API [użycia platformy Azure](/rest/api/consumption).

W witrynie Azure Portal budżety są definiowane według kosztu. W przypadku korzystania z interfejsu API użycia platformy Azure budżety są definiowane w oparciu o koszty lub w oparciu o wykorzystanie użycia. Alerty budżetowe obsługują zarówno budżety oparte na kosztach, jak i oparte na użyciu. Alerty budżetowe są generowane automatycznie za każdym razem, gdy spełnione są warunki alertu budżetowego. Wszystkie alerty dotyczące kosztów można wyświetlić w witrynie Azure Portal. Po każdym wygenerowaniu alertu jest on wyświetlany w obszarze alertów dotyczących kosztów. Wiadomość e-mail z alertem jest również wysyłana do osób z listy adresatów alertu budżetu.

Za pomocą interfejsu API budżetu możesz wysyłać alerty e-mail w innym języku. Aby uzyskać więcej informacji, zobacz [Ustawienia regionalne obsługiwane w przypadku wiadomości e-mail z alertami budżetowymi](manage-automation.md#supported-locales-for-budget-alert-emails).

## <a name="credit-alerts"></a>Alerty dotyczące środków

Alerty dotyczące środków powiadamiają o wykorzystaniu przedpłaty platformy Azure (wcześniej nazywanej zobowiązaniem pieniężnym). Opłata z góry za platformę Azure jest przeznaczona dla organizacji z umowami Enterprise Agreement. Alerty dotyczące środków są generowane automatycznie przy przekroczeniu 90% i 100% salda środków przedpłaty na platformę Azure. Po wygenerowaniu alertu jest on uwzględniany w alertach dotyczących kosztów i w wiadomościach e-mail wysyłanych do właścicieli konta.

## <a name="department-spending-quota-alerts"></a>Alerty dotyczące limitów przydziału wydatków dla działu

Alerty dotyczące limitów przydziału wydatków dla działu powiadamiają użytkownika, gdy wydatki działu osiągną ustalony próg limitu przydziału. Limity przydziału wydatków są konfigurowane w witrynie EA Portal. Za każdym razem, gdy zostanie osiągnięty próg, generowana jest wiadomość e-mail do właścicieli działów i ten fakt jest wyświetlany w alertach dotyczących kosztów. Próg może na przykład wynosić 50% lub 75% limitu przydziału.

## <a name="supported-alert-features-by-offer-categories"></a>Obsługiwane funkcje alertów według kategorii ofert

Obsługa typów alertów zależy od typu posiadanego konta platformy Azure (oferty firmy Microsoft). W poniższej tabeli przedstawiono funkcje alertów, które są obsługiwane przez różne oferty firmy Microsoft. Pełną listę ofert firmy Microsoft można wyświetlić w sekcji [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md).

| Typ alertu | Enterprise Agreement | Umowa klienta firmy Microsoft | Web direct/Płatność zgodnie z rzeczywistym użyciem |
|---|---|---|---|
| Budżet | ✔ | ✔ | ✔ |
| Środki | ✔ |✘ | ✘ |
| Limity przydziału wydatków dla działu | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Wyświetlanie alertów dotyczących kosztów

Aby wyświetlić alerty dotyczące kosztów, otwórz żądany zakres w witrynie Azure Portal i w menu wybierz pozycję **Budżety**. Użyj kapsułki **Zakres**, aby przełączyć się na inny zakres. W menu wybierz pozycję **Alerty dotyczące kosztów**. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

![Przykładowy obraz alertów pokazanych w usłudze Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Całkowita liczba aktywnych i odrzuconych alertów jest wyświetlana na stronie alertów dotyczących kosztów.

Dla wszystkich alertów jest wyświetlany typ alertu. W przypadku alertu dotyczącego budżetu jest wyświetlana przyczyna jego wygenerowania i nazwa budżetu, do którego ma on zastosowanie. Każdy alert zawiera datę wygenerowania, stan oraz zakres (subskrypcję lub grupę zarządzania), do którego odnosi się ten alert.

Alert może mieć stan **aktywny** i **odrzucony**. Stan aktywny oznacza, że alert jest nadal istotny. Stan odrzucony oznacza, że ktoś oznaczył alert, aby ustawić go jako nieistotny.

Wybierz alert z listy, aby wyświetlić jego szczegóły. Szczegóły alertu zawierają więcej informacji na temat alertu. Alerty dotyczące budżetu obejmują link do odpowiedniego budżetu. Jeśli dla alertu dotyczącego budżetu jest dostępna rekomendacja, jest również wyświetlany link do tej rekomendacji. Alerty dotyczące budżetu i środków oraz alerty dotyczące limitów przydziału wydatków dla działu mają link do analizy kosztów, gdzie można zbadać koszty w zakresie alertu. W poniższym przykładzie przedstawiono wydatki dla działu wraz ze szczegółami alertów.

![Przykładowy obraz przedstawiający wydatki dla działu wraz ze szczegółami alertów](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Podczas przeglądania szczegółów odrzuconego alertu można go ponownie uaktywnić, jeśli jest potrzebne ręczne działanie. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykładowy obraz przedstawiający opcje odrzucania i uaktywniania](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Zobacz też

- Jeśli tworzysz budżet lub ustawiasz warunki alertów dla budżetu po raz pierwszy, ukończ samouczek [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md).