---
title: Korzystanie z przepływów pracy do integrowania aplikacji IoT Central platformy Azure z innymi usługami w chmurze | Microsoft Docs
description: Ten artykuł zawiera opis sposobu konfigurowania reguł i akcji, które integrują aplikację IoT Central z innymi usługami w chmurze. Aby utworzyć regułę zaawansowaną, należy użyć łącznika IoT Central w programie do automatyzacji lub Azure Logic Apps.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 257855b4f7b1fae56ed8d6a063acfb0588da9b6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92123324"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Korzystanie z przepływów pracy do integrowania aplikacji IoT Central platformy Azure z innymi usługami w chmurze

*Ten artykuł ma zastosowanie do konstruktorów rozwiązań.*

Można tworzyć reguły w IoT Central, które wyzwalają akcje, takie jak wysyłanie wiadomości e-mail w odpowiedzi na warunki związane z telemetrią, takie jak temperatura urządzenia przekroczenia progu.

Łącznik Azure IoT Central v3 na potrzeby automatyzacji i Azure Logic Apps pozwala tworzyć bardziej zaawansowane reguły automatyzacji operacji w IoT Central:

- Gdy reguła jest uruchamiana w aplikacji IoT Central platformy Azure, może ona wyzwolić przepływ pracy w programie do automatyzacji lub Azure Logic Apps. Te przepływy pracy mogą uruchamiać akcje w innych usługach w chmurze, takich jak Microsoft 365 lub usługa innej firmy.
- Zdarzenie w innej usłudze w chmurze, takie jak Microsoft 365, może wyzwolić przepływ pracy w programie do automatyzacji lub Azure Logic Apps. Te przepływy pracy mogą uruchamiać akcje lub pobierać dane z aplikacji IoT Central.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz mieć aktywną subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Skonfigurowanie rozwiązania wymaga aplikacji IoT Central w wersji 3. Aby dowiedzieć się, jak sprawdzić wersję aplikacji, zobacz [Informacje o aplikacji](./howto-get-app-info.md). Aby dowiedzieć się, jak utworzyć aplikację IoT Central, zobacz [Tworzenie aplikacji IoT Central platformy Azure](./quick-deploy-iot-central.md).

> [!NOTE]
> Jeśli używasz aplikacji IoT Central w wersji 2, zobacz [Tworzenie przepływów pracy za pomocą łącznika IoT Central w Azure Logic Apps](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) w witrynie w poprzedniej wersji, a następnie użyj łącznika Azure IoT Central v2

## <a name="trigger-a-workflow-from-a-rule"></a>Wyzwalanie przepływu pracy z poziomu reguły

Aby można było wyzwolić przepływ pracy w programie do automatyzacji lub Azure Logic Apps, musisz mieć regułę w aplikacji IoT Central. Aby dowiedzieć się więcej, zobacz [Konfigurowanie reguł i akcji na platformie Azure IoT Central](./howto-configure-rules.md).

Aby dodać łącznik **usługi Azure IoT Central v3 (wersja zapoznawcza** ) jako wyzwalacz w programie do automatyzowania:

1. W obszarze Automatyzacja wybierz pozycję **+ Utwórz**, a następnie wybierz kartę **niestandardową** .
1. Wyszukaj *IoT Central* i wybierz łącznik **Azure IoT Central v3 — wersja zapoznawcza** .
1. Na liście wyzwalaczy wybierz opcję **gdy reguła jest wyzwalana (wersja zapoznawcza)**.
1. W kroku **gdy reguła jest wyzwalana** wybierz aplikację IoT Central i regułę, z której korzystasz.

Aby dodać łącznik **usługi Azure IoT Central v3 (wersja zapoznawcza** ) jako wyzwalacz w Azure Logic Apps:

1. W **projektancie Logic Apps** wybierz szablon **pustej aplikacji logiki** .
1. W Projektancie wybierz kartę **niestandardowe** .
1. Wyszukaj *IoT Central* i wybierz łącznik **Azure IoT Central v3 — wersja zapoznawcza** .
1. Na liście wyzwalaczy wybierz opcję **gdy reguła jest wyzwalana (wersja zapoznawcza)**.
1. W kroku **gdy reguła jest wyzwalana** wybierz aplikację IoT Central i regułę, z której korzystasz.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Znajdź łącznik usługi Azure IoT Central — wersja zapoznawcza i wybierz wyzwalacz":::

Teraz możesz dodać więcej kroków do przepływu pracy, aby skompilować scenariusz integracji.

## <a name="run-an-action"></a>Uruchamianie akcji

Akcje można uruchamiać w aplikacji IoT Central z poziomu przepływów pracy automatyzacji i Azure Logic Apps. Najpierw należy utworzyć przepływ pracy i zdefiniować wyzwalacz, aby uruchomić przepływ pracy. Następnie użyj łącznika **Azure IoT Central v3 — wersja zapoznawcza** jako akcji.

Aby dodać łącznik **Azure IoT Central v3 (wersja zapoznawcza** ) jako akcję w programie:

1. W obszarze Automatyzacja w programie w obszarze **Wybierz akcję** wybierz kartę **niestandardową** .
1. Wyszukaj *IoT Central* i wybierz łącznik **usługi Azure IoT Central v3 — wersja zapoznawcza** .
1. Na liście akcji wybierz akcję IoT Central, której chcesz użyć.
1. W kroku akcja wykonaj konfigurację wybranej akcji. Następnie wybierz pozycję **Zapisz**.

Aby dodać łącznik **usługi Azure IoT Central v3 (wersja zapoznawcza** ) jako akcję w Azure Logic Apps:

1. W **projektancie Logic Apps**, w panelu **Wybierz akcję** wybierz kartę **niestandardowa** .
1. Wyszukaj *IoT Central* i wybierz łącznik **Azure IoT Central v3 — wersja zapoznawcza** .
1. Na liście akcji wybierz akcję IoT Central, której chcesz użyć.
1. W kroku akcja wykonaj konfigurację wybranej akcji. Następnie wybierz pozycję **Zapisz**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Znajdź łącznik Azure IoT Central v3 i wybierz akcję":::

## <a name="list-of-actions"></a>Lista akcji

Na poniższej liście przedstawiono wszystkie dostępne akcje IoT Central w łączniku **platformy Azure IoT Central v3 (wersja zapoznawcza** ) oraz ich opcje konfiguracji. Wiele pól może mieć dynamicznie wygenerowaną zawartość. Na przykład poprzedni krok może ustalić identyfikator urządzenia, na którym działa bieżący krok.

### <a name="create-or-update-a-device"></a>Utwórz lub zaktualizuj urządzenie

Użyj tej akcji, aby utworzyć lub zaktualizować urządzenie w aplikacji IoT Central.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do utworzenia lub zaktualizowania. |
| Approved (Zatwierdzono) | Zdecyduj, czy urządzenie zostało zatwierdzone do łączenia się z IoT Central. |
| Opis urządzenia | Szczegółowy opis urządzenia. |
| Nazwa urządzenia | Nazwa wyświetlana urządzenia. |
| Szablon urządzenia | Wybierz z listy szablonów urządzeń w aplikacji IoT Central. |
| Symulowane | Wybierz, czy urządzenie jest symulowane. |

### <a name="delete-a-device"></a>Usuwanie urządzenia

Użyj tej akcji, aby usunąć urządzenie z aplikacji IoT Central.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do usunięcia. |

### <a name="execute-a-device-command"></a>Wykonaj polecenie urządzenia

Użyj tej akcji, aby wykonać polecenie zdefiniowane w jednym z interfejsów urządzenia.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do usunięcia. |
| Składnik urządzenia | Interfejs w szablonie urządzenia, który zawiera polecenie. |
| Polecenie urządzenia | Wybierz jedno z poleceń w wybranym interfejsie. |
| Szablon urządzenia | Wybierz z listy szablonów urządzeń w aplikacji IoT Central. |
| Ładunek żądania polecenia urządzenia | Jeśli polecenie wymaga ładunku żądania, Dodaj je tutaj.  |

> [!NOTE]
> Nie można wybrać składnika urządzenia do momentu wybrania szablonu urządzenia.

### <a name="get-a-device-by-id"></a>Pobieranie urządzenia według identyfikatora

Użyj tej akcji, aby pobrać szczegóły urządzenia.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do usunięcia. |

Możesz użyć zwróconych szczegółów w wyrażeniach dynamicznych w innych akcjach. Zwrócone szczegóły urządzenia obejmują: **zatwierdzone**, **treść**, **Opis urządzenia**, **Nazwa urządzenia**, **szablon urządzenia**, **aprowizacji** i **symulowane**.

### <a name="get-device-cloud-properties"></a>Pobierz właściwości chmury urządzenia

Ta akcja umożliwia pobranie wartości właściwości chmury dla określonego urządzenia.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do usunięcia. |
| Szablon urządzenia | Wybierz z listy szablonów urządzeń w aplikacji IoT Central. |

Możesz użyć zwracanych wartości właściwości chmury w wyrażeniach dynamicznych w innych akcjach.

### <a name="get-device-properties"></a>Pobierz właściwości urządzenia

Ta akcja umożliwia pobranie wartości właściwości dla określonego urządzenia.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do usunięcia. |
| Szablon urządzenia | Wybierz z listy szablonów urządzeń w aplikacji IoT Central. |

Wartości zwracanych właściwości można użyć w wyrażeniach dynamicznych w innych akcjach.

### <a name="get-device-telemetry-value"></a>Pobierz wartość telemetrii urządzenia

Użyj tej akcji, aby pobrać wartości telemetryczne dla określonego urządzenia.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do usunięcia. |
| Szablon urządzenia | Wybierz z listy szablonów urządzeń w aplikacji IoT Central. |

Możesz użyć zwracanych wartości telemetrii w wyrażeniach dynamicznych w innych akcjach.

### <a name="update-device-cloud-properties"></a>Aktualizowanie właściwości chmury urządzenia

Ta akcja służy do aktualizowania wartości właściwości chmury dla określonego urządzenia.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do usunięcia. |
| Szablon urządzenia | Wybierz z listy szablonów urządzeń w aplikacji IoT Central. |
| Właściwości chmury | Po wybraniu szablonu urządzenia zostanie dodane pole dla każdej właściwości chmury zdefiniowanej w szablonie. |

### <a name="update-device-properties"></a>Aktualizowanie właściwości urządzenia

Ta akcja służy do aktualizowania wartości właściwości zapisywalnych dla określonego urządzenia.

| Pole | Opis |
| ----- | ----------- |
| Aplikacja | Wybierz z listy aplikacji IoT Central. |
| Urządzenie | Unikatowy identyfikator urządzenia do usunięcia. |
| Szablon urządzenia | Wybierz z listy szablonów urządzeń w aplikacji IoT Central. |
| Właściwości do zapisu | Po wybraniu szablonu urządzenia jest dodawane pole dla każdej właściwości możliwej do zapisu zdefiniowanej w szablonie. |

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak utworzyć zaawansowaną regułę w aplikacji IoT Central platformy Azure, możesz dowiedzieć się, jak [analizować dane urządzeń w aplikacji IoT Central platformy Azure](howto-create-analytics.md) .