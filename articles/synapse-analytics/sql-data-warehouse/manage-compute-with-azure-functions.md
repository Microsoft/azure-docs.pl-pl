---
title: 'Samouczek: Zarządzanie obliczeniami przy użyciu Azure Functions'
description: Jak używać Azure Functions do zarządzania obliczeniami dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f0731f0deaf46ec419cfe43037804e10f2b73fd4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448381"
---
# <a name="use-azure-functions-to-manage-compute-resources-for-your-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Użyj Azure Functions do zarządzania zasobami obliczeniowymi dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics

Ten samouczek używa Azure Functions do zarządzania zasobami obliczeniowymi dla dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics.

Aby użyć aplikacja funkcji platformy Azure z dedykowaną pulą SQL (dawniej SQL DW), należy utworzyć [konto nazwy głównej usługi](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Konto nazwy głównej usługi wymaga dostępu współautora w ramach tej samej subskrypcji, co dedykowane wystąpienie puli SQL (dawniej SQL DW).

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Wdrażanie skalowania opartego na czasomierzu przy użyciu szablonu Azure Resource Manager

Aby wdrożyć szablon, potrzebne są następujące informacje:

- Nazwa grupy zasobów, w której znajduje się dedykowane wystąpienie puli SQL (dawniej SQL DW)
- Nazwa serwera, w którym jest używane dedykowane wystąpienie puli SQL (dawniej SQL DW)
- Nazwa dedykowanego wystąpienia puli SQL (dawniej SQL DW)
- Identyfikator dzierżawy (identyfikator katalogu ) usługi Azure Active Directory
- Identyfikator subskrypcji
- Identyfikator aplikacji nazwy głównej usługi
- Klucz tajny usługi nazwy głównej usługi

Po uzyskaniu powyższych informacji Wdróż ten szablon:

[![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

Po wdrożeniu szablonu należy znaleźć trzy nowe zasoby: bezpłatny plan Azure App Service, plan aplikacja funkcji na podstawie zużycia oraz konto magazynu obsługujące rejestrowanie i kolejkę operacji. Kontynuuj czytanie pozostałych sekcji, aby dowiedzieć się, jak zmodyfikować wdrożone funkcje i dostosować je do swoich potrzeb.

## <a name="change-the-compute-level"></a>Zmień poziom obliczeń

1. Przejdź do usługi aplikacji funkcji. Jeśli szablon został wdrożony przy użyciu wartości domyślnych, usługa ta powinna mieć nazwę *DWOperations*. Po otwarciu aplikacji funkcji powinno być widocznych pięć funkcji wdrożonych w usłudze aplikacji funkcji.

   ![Funkcje wdrażane przy użyciu szablonu](./media/manage-compute-with-azure-functions/five-functions.png)

2. Wybierz opcję *DWScaleDownTrigger* lub *DWScaleUpTrigger* , aby skalować w górę lub w dół. Z menu rozwijanego wybierz pozycję Integruj.

   ![Wybieranie pozycji Integruj dla funkcji](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Obecnie powinna być wyświetlana wartość *%ScaleDownTime%* lub *%ScaleUpTime%*. Te wartości wskazują, że harmonogram jest oparty na wartościach określonych w [ustawieniach aplikacji](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Na razie można zignorować tę wartość i zmienić harmonogram na preferowany czas na podstawie następnych kroków.

4. W obszarze harmonogram Dodaj wyrażenie CRONUS, aby odzwierciedlić, jak często chcesz skalować usługę Azure Synapse Analytics.

   ![Zmienianie harmonogramu funkcji](./media/manage-compute-with-azure-functions/change-schedule.png)

   Wartość `schedule` jest [wyrażeniem CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) obejmującym sześć pól:

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Na przykład *"0 30 9 * * 1-5"* będzie odzwierciedlał wyzwalacz każdego dnia tygodnia o godzinie 9:10:30. Aby uzyskać więcej informacji, zapoznaj się z [przykładami harmonogramów](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example) usługi Azure Functions.

## <a name="change-the-time-of-the-scale-operation"></a>Zmień godzinę operacji skalowania

1. Przejdź do usługi aplikacji funkcji. Jeśli szablon został wdrożony przy użyciu wartości domyślnych, usługa ta powinna mieć nazwę *DWOperations*. Po otwarciu aplikacji funkcji powinno być widocznych pięć funkcji wdrożonych w usłudze aplikacji funkcji.

2. Wybierz opcję *DWScaleDownTrigger* lub *DWScaleUpTrigger* , aby skalować wartość obliczeniową w górę lub w dół. Po wybraniu funkcji w okienku powinien zostać wyświetlony plik *index.js*.

   ![Zmienianie poziomu obliczeniowego wyzwalacza funkcji](././media/manage-compute-with-azure-functions/index-js.png)

3. Zmień wartość *ServiceLevelObjective* na żądany poziom i wybierz pozycję Zapisz. *ServiceLevelObjective* to poziom obliczeń, na który będzie skalowany wystąpienie magazynu danych na podstawie harmonogramu zdefiniowanego w sekcji Integruj.

## <a name="use-pause-or-resume-instead-of-scale"></a>Używanie wstrzymywania lub wznawiania zamiast skalowania

Obecnie funkcje włączone domyślnie to *DWScaleDownTrigger* i *DWScaleUpTrigger*. Jeśli zamiast tego chcesz korzystać z funkcji wstrzymywania i wznawiania, możesz włączyć funkcje *DWPauseTrigger* lub *DWResumeTrigger*.

1. Przejdź do okienka Funkcje.

   ![Okienko Funkcje](./media/manage-compute-with-azure-functions/functions-pane.png)

2. Wybierz odpowiedni przełącznik dla odpowiednich wyzwalaczy, które chcesz włączyć.

3. Przejdź do kart *Integracja* odpowiednich wyzwalaczy, aby zmienić ich harmonogram.

   > [!NOTE]
   > Różnica funkcjonalna między wyzwalaczami skalowania i wyzwalaczami wstrzymywania/wznawiania jest komunikatem wysyłanym do kolejki. Aby uzyskać więcej informacji, zobacz [Dodawanie nowej funkcji wyzwalacza](manage-compute-with-azure-functions.md#add-a-new-trigger-function).

## <a name="add-a-new-trigger-function"></a>Dodawanie nowej funkcji wyzwalacza

Obecnie szablon zawiera tylko dwie funkcje skalowania. Korzystając z tych funkcji, w ciągu dnia można skalować w dół tylko raz i jeden raz. Aby uzyskać bardziej szczegółową kontrolę, na przykład skalowanie w dół wiele razy dziennie lub mieć inne zachowanie skalowania w weekendy, należy dodać kolejny wyzwalacz.

1. Utwórz nową pustą funkcję. Wybierz *+* przycisk obok pozycji funkcje, aby wyświetlić okienko szablonu funkcji.

   ![Zrzut ekranu pokazujący menu "aplikacje funkcji" z ikoną "plus" obok wybranej pozycji "Functions" (funkcje).](./media/manage-compute-with-azure-functions/create-new-function.png)

2. W obszarze Język wybierz pozycję *JavaScript*, a następnie wybierz pozycję *TimerTrigger*.

   ![Tworzenie nowej funkcji](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nazwij funkcję i ustaw harmonogram. Ilustracja przedstawia, jak można ustawić wyzwalanie funkcji w każdą sobotę o północy (późny wieczór w piątek).

   ![Skalowanie w dół w sobotę](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Skopiuj zawartość pliku *index.js* z jednej z funkcji wyzwalacza.

   ![Kopiowanie pliku index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Ustaw dla zmiennej operacji odpowiednie zachowanie w następujący sposób:

   ```JavaScript
   // Resume the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the dedicated SQL pool (formerly SQL DW)l instance to DW600c
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600c"
   }
   ```

## <a name="complex-scheduling"></a>Sporządzanie złożonego harmonogramu

W tej sekcji przedstawiono krótkie informacje o tym, co jest niezbędne do uzyskania bardziej złożonego harmonogramu funkcji wstrzymywania, wznawiania i skalowania.

### <a name="example-1"></a>Przykład 1

Codzienne skalowanie w górę o 8:00 do DW600c i skalowanie w dół w 8pm do DW200c.

| Funkcja  | Zaplanuj     | Operacja                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600c"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-2"></a>Przykład 2

Codzienne skalowanie w górę o 8:00 do DW1000c, skalowanie w dół do wartości DW600 w 16:00 i skalowanie w dół o 10pm do DW200c.

| Funkcja  | Zaplanuj     | Operacja                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-3"></a>Przykład 3

Skaluj w górę o 8:00 do DW1000c, Skaluj w dół do DW600c na 16:00 w dniach roboczych. Wstrzymanie w piątek o godz. 23:00, wznowienie w poniedziałek rano o godz. 7:00.

| Funkcja  | Zaplanuj       | Operacja                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 1    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [wyzwalacza czasomierza](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure Functions.

Zobacz [repozytorium przykładów](https://github.com/Microsoft/sql-data-warehouse-samples)dedykowanej puli SQL (dawniej SQL DW).
