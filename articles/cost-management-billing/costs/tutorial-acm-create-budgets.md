---
title: Samouczek — tworzenie budżetów platformy Azure i zarządzanie nimi
description: Ten samouczek ułatwia planowanie i uwzględnianie kosztów używanych usług platformy Azure.
author: bandersmsft
ms.author: banders
ms.date: 01/27/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 5659f3b2d020a97ed2460c55283bb41f2f7606cc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943736"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Samouczek: Tworzenie budżetów platformy Azure i zarządzanie nimi

Budżety w usłudze Cost Management ułatwiają planowanie poprawy odpowiedzialności organizacji. Za pomocą budżetów możesz obserwować koszt wykorzystywanych lub subskrybowanych usług platformy Azure w określonym czasie. Ułatwiają one informowanie innych osób o ich wydatkach, co umożliwia proaktywne zarządzanie kosztami oraz monitorowanie wydatków w czasie. Po przekroczeniu utworzonych progów budżetowych wyzwalane są tylko powiadomienia. Nie ma to wpływu na zasoby ani nie następuje zatrzymanie użycia. Budżety umożliwiają porównywanie i śledzenie wydatków, co ułatwia analizę kosztów.

Dane dotyczące kosztów i użycia są zwykle dostępne w ciągu 8–24 godzin, a ocena budżetów na podstawie tych kosztów odbywa się co 24 godziny. Nie zapomnij zapoznać się ze specyfiką [aktualizacji danych dotyczących kosztów i użycia](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Po osiągnięciu progu budżetowego są wysyłane wiadomości e-mail z powiadomieniami — zwykle w ciągu godziny od oceny.

Na koniec okresu (co miesiąc, co kwartał lub co rok), gdy użytkownik wybierze datę wygaśnięcia w przyszłości, budżety są automatycznie resetowane do takiej samej kwoty. Ponieważ budżety są resetowane do takiej samej kwoty, w przypadku konieczności jej zmiany w przyszłym okresie należy utworzyć kolejny budżet. Po wygaśnięciu budżetu zostanie on automatycznie usunięty.

Przykłady zawarte w tym samouczku przedstawiają procesy tworzenia i edytowania budżetu dla subskrypcji Azure Enterprise Agreement (EA).

Obejrzyj film [Stosowanie budżetów do subskrypcji w witrynie Azure Portal](https://www.youtube.com/watch?v=UrkHiUx19Po), aby dowiedzieć się, jak tworzyć budżety na platformie Azure w celu monitorowania wydatków. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure Portal
> * Tworzenie i edytowanie budżetów przy użyciu programu PowerShell
> * Tworzenie budżetu za pomocą szablonu usługi Azure Resource Manager

## <a name="prerequisites"></a>Wymagania wstępne

Budżety są obsługiwane dla następujących typów kont i zakresów platformy Azure:

- Zakresy kontroli dostępu opartej na rolach na platformie Azure (Azure RBAC)
    - Grupy zarządzania
    - Subskrypcja
- Zakresy umowy Enterprise Agreement
    - Konto billingowe
    - Dział
    - Konto rejestracji
- Umowy indywidualne
    - Konto billingowe
- Zakresy umowy klienta firmy Microsoft
    - Konto billingowe
    - Profil rozliczeniowy
    - Sekcja faktury
    - Klient
- Zakresy platformy AWS
    - Konto zewnętrzne
    - Subskrypcja zewnętrzna


Aby wyświetlić budżety, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure.

Jeśli masz nową subskrypcję, nie możesz od razu tworzyć budżetu ani korzystać z innych funkcji usługi Cost Management. Aby można było korzystać ze wszystkich funkcji usługi Cost Management, może upłynąć do 48 godzin.

W przypadku subskrypcji Azure EA wyświetlanie budżetów wymaga dostępu do odczytu. Aby tworzyć budżety i zarządzać nimi, musisz mieć uprawnienie współautora.

Na potrzeby tworzenia budżetów przez użytkownika i grupę w ramach subskrypcji są obsługiwane następujące uprawnienia, czyli zakresy, platformy Azure. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

- Właściciel — może tworzyć, modyfikować i usuwać budżety dla subskrypcji.
- Współautor i współautor usługi Cost Management — może tworzyć, modyfikować i usuwać swoje budżety. Może też modyfikować kwoty budżetów utworzonych przez innych użytkowników.
- Czytelnik i czytelnik usługi Cost Management — może wyświetlać budżety, do których ma uprawnienia.

Aby uzyskać więcej informacji na temat przypisywania uprawnień do danych usługi Cost Management, zobacz [Przypisywanie dostępu do danych usługi Cost Management](./assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Tworzenie budżetu w witrynie Azure Portal

Możesz utworzyć budżet subskrypcji platformy Azure dla okresu miesięcznego, kwartalnego lub rocznego.

Aby utworzyć lub wyświetlić budżet, otwórz żądany zakres w witrynie Azure Portal i wybierz pozycję **Budżety** w menu. Na przykład przejdź do obszaru **Subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję **Budżety** w menu. W obszarze budżetów użyj kapsułki **Zakres**, aby przełączyć się na inny zakres, np. grupę zarządzania. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

Utworzone budżety zawierają prosty widok bieżących wydatków.

Wybierz pozycję **Dodaj**.

![Przykładowa lista utworzonych budżetów](./media/tutorial-acm-create-budgets/budgets01.png)

W oknie **Tworzenie budżetu** upewnij się, że widoczny zakres jest prawidłowy. Wybierz filtry, które chcesz dodać. Filtry umożliwiają tworzenie budżetów dla określonych kosztów, takich jak grupy zasobów w ramach subskrypcji lub usługa, taka jak maszyny wirtualne. Dowolny filtr, którego można użyć w analizie kosztów, można również zastosować do budżetu.

Po określeniu zakresu i filtrów wpisz nazwę budżetu. Następnie wybierz miesięczny, kwartalny lub roczny okres resetowania budżetu. Okres resetowania określa przedział czasu, który jest analizowany w ramach budżetu. Na początku każdego okresu budżetowego oceniany koszt ma wartość zerową. Budżet kwartalny działa tak samo jak budżet miesięczny. Różnica polega na tym, że kwota budżetu kwartalnego jest dzielona równomiernie na trzy miesiące. Kwota budżetu rocznego jest dzielona równomiernie na 12 miesięcy roku kalendarzowego.

Jeśli masz subskrypcję z płatnością zgodnie z rzeczywistym użyciem, MSDN lub programu Visual Studio, Twój okres rozliczeniowy faktury może nie zgadzać się z miesiącem kalendarzowym. W przypadku tych typów subskrypcji i grup zasobów można utworzyć budżet dopasowany do Twojego okresu na fakturze lub miesięcy kalendarzowych. Aby utworzyć budżet dopasowany do okresu na fakturze, wybierz okres resetowania **Miesiąc rozliczeniowy**, **Kwartał rozliczeniowy** lub **Rok rozliczeniowy**. Aby utworzyć budżet dopasowany do miesiąca kalendarzowego, wybierz okres resetowania **Miesięcznie**, **Kwartalnie** lub **Rocznie**.

Następnie wskaż datę wygaśnięcia — budżet utraci wtedy ważność, a szacowanie kosztów zostanie zakończone.

Pojawi się wykres, na którym uwzględniono wybrane pola. Ułatwia on wybranie progu dla budżetu. Sugerowany budżet bazuje na najwyższym prognozowanym koszcie, który może zostać naliczony w przyszłych okresach. Kwotę budżetu można zmienić.

![Przykład przedstawiający tworzenie budżetu z miesięcznymi kosztami ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Po skonfigurowaniu kwoty budżetu wybierz przycisk **Dalej**, aby skonfigurować alerty dotyczące budżetu. Budżety wymagają co najmniej jednego progu kosztów (wartości procentowej budżetu) i odpowiedniego adresu e-mail. Pojedynczy budżet może zawierać maksymalnie pięć progów i pięć adresów e-mail. Po osiągnięciu progu budżetowego są wysyłane wiadomości e-mail z powiadomieniami — zwykle w ciągu godziny od oceny.

Jeśli chcesz otrzymywać wiadomości e-mail, dodaj adres azure-noreply@microsoft.com do listy zatwierdzonych nadawców, aby wiadomości e-mail nie były umieszczane w folderze wiadomości-śmieci. Aby uzyskać więcej informacji o powiadomieniach, zobacz [Korzystanie z alertów dotyczących kosztów](./cost-mgt-alerts-monitor-usage-spending.md).

W poniższym przykładzie zostanie wygenerowany alert e-mail, gdy koszty osiągną 90% wartości budżetu. W przypadku tworzenia budżetu za pomocą interfejsu API budżetów można także przypisać role do osób, które będą otrzymywać alerty. Przypisywanie ról do osób nie jest obsługiwane w witrynie Azure Portal. Aby uzyskać więcej informacji o interfejsie API budżetów platformy Azure, zobacz [Interfejs API budżetów](/rest/api/consumption/budgets). Jeśli chcesz, aby wysyłany alert e-mail był w innym języku, zobacz [Ustawienia regionalne obsługiwane w przypadku wiadomości e-mail z alertami budżetowymi](manage-automation.md#supported-locales-for-budget-alert-emails).

Limity alertów obsługują zakres od 0,01 do 1000% podanego progu budżetu.

![Przykładowe warunki alertu](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Utworzony budżet jest widoczny w obszarze analizy kosztów. Wyświetlenie budżetu względem trendu wydatków jest jednym z pierwszych kroków wykonywanych w ramach [analizowania kosztów i wydatków](./quick-acm-cost-analysis.md).

![Przykładowy budżet z wydatkami w analizie kosztów](./media/tutorial-acm-create-budgets/cost-analysis.png)

W poprzednim przykładzie utworzono budżet dla subskrypcji. Budżet można utworzyć również dla grupy zasobów. Jeśli chcesz utworzyć budżet dla grupy zasobów, wybierz kolejno **Zarządzanie kosztami i rozliczenia** &gt; **Subskrypcje** &gt; wybierz subskrypcję > **Grupy zasobów** > wybierz grupę zasobów > **Budżety** >, a następnie wybierz pozycję **Dodaj**, aby dodać budżet.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Tworzenie budżetu dla połączonych kosztów platform Azure i AWS

Możesz grupować koszty platform Azure i AWS razem, przypisując grupę zarządzania do łącznika wraz ze skonsolidowanymi i połączonymi kontami. Przypisz subskrypcje platformy Azure do tej samej grupy zarządzania. Następnie utwórz budżet dla połączonych kosztów.

1. W obszarze Zarządzanie kosztami wybierz pozycję **Budżety**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Zmień zakres**, a następnie wybierz grupę zarządzania.
1. Kontynuuj tworzenie budżetu do momentu ukończenia.

## <a name="costs-in-budget-evaluations"></a>Koszty w ocenach budżetu

Oceny kosztów w budżecie obejmują teraz dane wystąpień zarezerwowanych i zakupów. Jeśli są naliczane opłaty, możesz zacząć otrzymywać alerty, gdy opłaty będą uwzględniane w ocenach. Zalecamy zalogowanie się do witryny [Azure Portal](https://portal.azure.com), aby sprawdzić, czy progi budżetowe zostały prawidłowo skonfigurowane pod kątem uwzględniania nowych kosztów. Wysokość opłat za korzystanie z platformy Azure pozostaje niezmieniona. Od tej pory budżety będą oceniane na podstawie pełniejszego zestawu kosztów. Jeśli opłaty nie mają zastosowania, działanie budżetu pozostaje niezmienione.

Jeśli chcesz odfiltrować nowe koszty, tak aby budżety były oceniane tylko na podstawie opłat za własne użycie platformy Azure, dodaj następujące filtry do budżetu:

- Typ wydawcy: Azure
- Typ opłaty: Sposób użycia

Oceny kosztów budżetowych bazują na rzeczywistych kosztach. Nie obejmują one amortyzacji. Aby uzyskać więcej informacji na temat opcji filtrowania dostępnych w budżetach, zobacz [Omówienie opcji grupowania i filtrowania](group-filter.md).

## <a name="trigger-an-action-group"></a>Wyzwalanie grupy akcji

Tworząc lub edytując budżet dla zakresu subskrypcji lub grupy zasobów, można skonfigurować wywoływanie grupy akcji. W ramach grupy akcji można wykonywać różne akcje po osiągnięciu progu budżetu. Grupy akcji są obecnie obsługiwane tylko w przypadku zakresów subskrypcji i grup zasobów. Aby uzyskać więcej informacji na temat grup akcji, zobacz [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal](../../azure-monitor/platform/action-groups.md). Aby uzyskać więcej informacji na temat współdziałania automatyzacji opartej na budżetach z grupami akcji, zobacz [Zarządzanie kosztami przy użyciu budżetów platformy Azure](../manage/cost-management-budget-scenario.md).

Aby utworzyć lub zaktualizować grupy akcji, wybierz pozycję **Zarządzaj grupami akcji** podczas tworzenia lub edytowania budżetu.

![Przykład przedstawiający utworzony budżet z wyświetloną opcją Zarządzaj grupami akcji](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Następnie wybierz pozycję **Dodaj grupę akcji** i utwórz grupę akcji.

![Obraz przedstawiający okno Dodawanie grupy akcji](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Po utworzeniu grupy akcji zamknij okno, aby wrócić do budżetu.

Skonfiguruj budżet tak, aby po osiągnięciu indywidualnego progu została użyta grupa akcji. Obsługiwanych jest maksymalnie pięć progów.

![Przykład przedstawiający wybór grupy akcji dla warunku alertu](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

W poniższym przykładzie przedstawiono progi budżetu ustawione na 50%, 75% i 100%. Konfiguracja każdego progu obejmuje wyzwalanie określonych akcji w ramach wyznaczonej grupy akcji.

![Przykład przedstawiający warunki alertu, których konfiguracja obejmuje różne grupy akcji i typy akcji](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Budżet można zintegrować z grupami akcji tylko wtedy, gdy w grupie akcji wyłączono wspólny schemat alertów. Aby uzyskać więcej informacji na temat wyłączania tego schematu, zobacz [Jak włączyć wspólny schemat alertów?](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>Tworzenie i edytowanie budżetów przy użyciu programu PowerShell

Klienci z umowami EA mogą programowo tworzyć i edytować budżety przy użyciu modułu programu Azure PowerShell.  Aby pobrać najnowszą wersję programu Azure PowerShell, uruchom następujące polecenie:

```azurepowershell-interactive
install-module -name Az
```

Poniższe przykładowe polecenia tworzą budżet.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Tworzenie budżetu za pomocą szablonu usługi Azure Resource Manager

Budżet można utworzyć za pomocą szablonu usługi Azure Resource Manager. Sposób korzystania z tego szablonu opisano w artykule [Tworzenie budżetu za pomocą szablonu usługi Azure Resource Manager](quick-create-budget-template.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli masz utworzony budżet, który nie jest już potrzebny, wyświetl jego szczegóły i usuń go.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie budżetu w witrynie Azure Portal
> * Tworzenie i edytowanie budżetów przy użyciu programu PowerShell
> * Tworzenie budżetu za pomocą szablonu usługi Azure Resource Manager

Przejdź do następnego samouczka, aby skonfigurować cykliczne eksportowanie danych związanych z zarządzaniem kosztami.

> [!div class="nextstepaction"]
> [Eksportowanie danych i zarządzanie nimi](tutorial-export-acm-data.md)