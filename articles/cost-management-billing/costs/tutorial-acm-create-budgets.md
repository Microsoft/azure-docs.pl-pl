---
title: Samouczek — tworzenie budżetów platformy Azure i zarządzanie nimi
description: Ten samouczek ułatwia planowanie i uwzględnianie kosztów używanych usług platformy Azure.
author: bandersmsft
ms.author: banders
ms.date: 03/09/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 2c0853b68e432fa94e661e68486b9e58b21aa845
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012451"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Samouczek: Tworzenie budżetów platformy Azure i zarządzanie nimi

Budżety w usłudze Cost Management ułatwiają planowanie poprawy odpowiedzialności organizacji. Ułatwiają one informowanie innych osób o ich wydatkach, co umożliwia proaktywne zarządzanie kosztami oraz monitorowanie wydatków w czasie. Alerty można skonfigurować na podstawie rzeczywistego kosztu lub prognozowanego kosztu, aby upewnić się, że wydatki przekraczają limit wydatków w organizacji. Po przekroczeniu utworzonych progów budżetowych wyzwalane są tylko powiadomienia. Nie ma to wpływu na zasoby ani nie następuje zatrzymanie użycia. Budżety umożliwiają porównywanie i śledzenie wydatków, co ułatwia analizę kosztów.

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

Aby utworzyć lub wyświetlić budżet, Otwórz zakres w Azure Portal a następnie wybierz pozycję **budżety** w menu. Na przykład przejdź do obszaru **Subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję **Budżety** w menu. W obszarze budżetów użyj kapsułki **Zakres**, aby przełączyć się na inny zakres, np. grupę zarządzania. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](understand-work-scopes.md).

Utworzone budżety zawierają prosty widok bieżących wydatków.

Wybierz pozycję **Dodaj**.

:::image type="content" source="./media/tutorial-acm-create-budgets/budgets-cost-management.png" alt-text="Screenthost przedstawiający już utworzoną listę budżetów." lightbox="./media/tutorial-acm-create-budgets/budgets-cost-management.png" :::

W oknie **Tworzenie budżetu** upewnij się, że widoczny zakres jest prawidłowy. Wybierz filtry, które chcesz dodać. Filtry umożliwiają tworzenie budżetów dla określonych kosztów, takich jak grupy zasobów w ramach subskrypcji lub usługa, taka jak maszyny wirtualne. Dowolny filtr, którego można użyć w analizie kosztów, można również zastosować do budżetu.

Po określeniu zakresu i filtrów wpisz nazwę budżetu. Następnie wybierz miesięczny, kwartalny lub roczny okres resetowania budżetu. Okres resetowania określa przedział czasu, który jest analizowany przez budżet. Na początku każdego okresu budżetowego oceniany koszt ma wartość zerową. Budżet kwartalny działa tak samo jak budżet miesięczny. Różnica polega na tym, że kwota budżetu kwartalnego jest dzielona równomiernie na trzy miesiące. Kwota budżetu rocznego jest dzielona równomiernie na 12 miesięcy roku kalendarzowego.

Jeśli masz subskrypcję z płatnością zgodnie z rzeczywistym użyciem, MSDN lub programu Visual Studio, Twój okres rozliczeniowy faktury może nie zgadzać się z miesiącem kalendarzowym. W przypadku tych typów subskrypcji i grup zasobów można utworzyć budżet dopasowany do Twojego okresu na fakturze lub miesięcy kalendarzowych. Aby utworzyć budżet dopasowany do okresu na fakturze, wybierz okres resetowania **Miesiąc rozliczeniowy**, **Kwartał rozliczeniowy** lub **Rok rozliczeniowy**. Aby utworzyć budżet dopasowany do miesiąca kalendarzowego, wybierz okres resetowania **Miesięcznie**, **Kwartalnie** lub **Rocznie**.

Następnie wskaż datę wygaśnięcia — budżet utraci wtedy ważność, a szacowanie kosztów zostanie zakończone.

Pojawi się wykres, na którym uwzględniono wybrane pola. Ułatwia on wybranie progu dla budżetu. Sugerowany budżet bazuje na najwyższym prognozowanym koszcie, który może zostać naliczony w przyszłych okresach. Kwotę budżetu można zmienić.

:::image type="content" source="./media/tutorial-acm-create-budgets/create-monthly-budget.png" alt-text="Zrzut ekranu przedstawiający tworzenie budżetu z danymi o kosztach miesięcznych." lightbox="./media/tutorial-acm-create-budgets/create-monthly-budget.png" :::

Po skonfigurowaniu kwoty budżetu wybierz pozycję **dalej** , aby skonfigurować alerty dotyczące budżetu dla rzeczywistych kosztów i prognozowanych alertów budżetu.

## <a name="configure-actual-costs-budget-alerts"></a>Skonfiguruj rzeczywiste alerty budżetu kosztów

Budżety wymagają co najmniej jednego progu kosztów (wartości procentowej budżetu) i odpowiedniego adresu e-mail. Pojedynczy budżet może zawierać maksymalnie pięć progów i pięć adresów e-mail. Po osiągnięciu progu budżetowego są wysyłane wiadomości e-mail z powiadomieniami — zwykle w ciągu godziny od oceny. Rzeczywiste koszty budżetowe alerty są generowane dla kosztu rzeczywistego naliczonego w odniesieniu do skonfigurowanych progów budżetu.

## <a name="configure-forecasted-budget-alerts"></a>Skonfiguruj alerty budżetu prognozowanego

Prognozowane alerty zapewniają zaawansowane powiadomienie, że trendy wydatków mogą przekroczyć budżet. Alerty korzystają z [prognozowanych prognoz kosztów](quick-acm-cost-analysis.md#understand-forecast). Alerty są generowane, gdy projekcja prognozowanego kosztu przekroczy ustawiony próg. Można skonfigurować prognozowany próg (% budżetu). Po spełnieniu progu przewidywanego budżetu powiadomienia są zwykle wysyłane w ciągu godziny od oceny.

Aby przełączać się między konfigurowaniem alertu rzeczywistego a prognozowanego kosztu, użyj `Type` pola podczas konfigurowania alertu, jak pokazano na poniższej ilustracji.

Jeśli chcesz otrzymywać wiadomości e-mail, dodaj adres azure-noreply@microsoft.com do listy zatwierdzonych nadawców, aby wiadomości e-mail nie były umieszczane w folderze wiadomości-śmieci. Aby uzyskać więcej informacji o powiadomieniach, zobacz [Korzystanie z alertów dotyczących kosztów](./cost-mgt-alerts-monitor-usage-spending.md).

W poniższym przykładzie zostanie wygenerowany alert e-mail, gdy zostanie osiągnięty limit 90% budżetu. W przypadku tworzenia budżetu za pomocą interfejsu API budżetów można także przypisać role do osób, które będą otrzymywać alerty. Przypisywanie ról do osób nie jest obsługiwane w witrynie Azure Portal. Aby uzyskać więcej informacji o interfejsie API budżetów platformy Azure, zobacz [Interfejs API budżetów](/rest/api/consumption/budgets). Jeśli chcesz, aby wysyłany alert e-mail był w innym języku, zobacz [Ustawienia regionalne obsługiwane w przypadku wiadomości e-mail z alertami budżetowymi](manage-automation.md#supported-locales-for-budget-alert-emails).

Limity alertów obsługują zakres od 0,01% do 1000% progu budżetu, który podałeś.

:::image type="content" source="./media/tutorial-acm-create-budgets/budget-set-alert.png" alt-text="Zrzut ekranu przedstawiający warunki alertu." lightbox="./media/tutorial-acm-create-budgets/budget-set-alert.png" :::

Po utworzeniu budżetu będzie on widoczny w analizie kosztów. Wyświetlenie budżetu względem trendu wydatków jest jednym z pierwszych kroków wykonywanych w ramach [analizowania kosztów i wydatków](./quick-acm-cost-analysis.md).

:::image type="content" source="./media/tutorial-acm-create-budgets/cost-analysis.png" alt-text="Zrzut ekranu przedstawiający przykładowy budżet z wydatkami podanymi w analizie kosztów." lightbox="./media/tutorial-acm-create-budgets/cost-analysis.png" :::

W poprzednim przykładzie utworzono budżet dla subskrypcji. Budżet można utworzyć również dla grupy zasobów. Jeśli chcesz utworzyć budżet dla grupy zasobów, wybierz kolejno **Zarządzanie kosztami i rozliczenia** &gt; **Subskrypcje** &gt; wybierz subskrypcję > **Grupy zasobów** > wybierz grupę zasobów > **Budżety** >, a następnie wybierz pozycję **Dodaj**, aby dodać budżet.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Tworzenie budżetu dla połączonych kosztów platform Azure i AWS

Możesz grupować koszty platform Azure i AWS razem, przypisując grupę zarządzania do łącznika wraz ze skonsolidowanymi i połączonymi kontami. Przypisz subskrypcje platformy Azure do tej samej grupy zarządzania. Następnie utwórz budżet dla połączonych kosztów.

1. W obszarze Zarządzanie kosztami wybierz pozycję **Budżety**.
1. Wybierz pozycję **Dodaj**.
1. Wybierz pozycję **Zmień zakres**, a następnie wybierz grupę zarządzania.
1. Kontynuuj tworzenie budżetu do momentu ukończenia.

## <a name="costs-in-budget-evaluations"></a>Koszty w ocenach budżetu

Oceny kosztów w budżecie obejmują teraz dane wystąpień zarezerwowanych i zakupów. Jeśli są naliczane opłaty, możesz zacząć otrzymywać alerty, gdy opłaty będą uwzględniane w ocenach. Zaloguj się do [Azure Portal](https://portal.azure.com) , aby sprawdzić, czy progi budżetu zostały prawidłowo skonfigurowane pod kątem ponoszenia nowych kosztów. Wysokość opłat za korzystanie z platformy Azure pozostaje niezmieniona. Od tej pory budżety będą oceniane na podstawie pełniejszego zestawu kosztów. Jeśli opłaty nie mają zastosowania, działanie budżetu pozostaje niezmienione.

Jeśli chcesz odfiltrować nowe koszty, tak aby budżety były oceniane tylko na podstawie opłat za własne użycie platformy Azure, dodaj następujące filtry do budżetu:

- Typ wydawcy: Azure
- Typ opłaty: Sposób użycia

Oceny kosztów budżetowych bazują na rzeczywistych kosztach. Nie obejmują one amortyzacji. Aby uzyskać więcej informacji na temat opcji filtrowania dostępnych w budżetach, zobacz [Omówienie opcji grupowania i filtrowania](group-filter.md).

## <a name="trigger-an-action-group"></a>Wyzwalanie grupy akcji

Tworząc lub edytując budżet dla zakresu subskrypcji lub grupy zasobów, można skonfigurować wywoływanie grupy akcji. W ramach grupy akcji można wykonywać różne akcje po osiągnięciu progu budżetu. 

Grupy akcji są obecnie obsługiwane tylko w przypadku zakresów subskrypcji i grup zasobów. Aby uzyskać więcej informacji na temat tworzenia grup akcji, zobacz [Konfigurowanie podstawowych ustawień grupy akcji](../../azure-monitor/alerts/action-groups.md#configure-basic-action-group-settings). 

Aby uzyskać więcej informacji na temat współdziałania automatyzacji opartej na budżetach z grupami akcji, zobacz [Zarządzanie kosztami przy użyciu budżetów platformy Azure](../manage/cost-management-budget-scenario.md).

Aby utworzyć lub zaktualizować grupy akcji, wybierz pozycję **Zarządzaj grupą akcji** podczas tworzenia lub edytowania budżetu.

:::image type="content" source="./media/tutorial-acm-create-budgets/trigger-action-group.png" alt-text="Zrzut ekranu przedstawiający przykład tworzenia budżetu do wyświetlania grup akcji zarządzania." lightbox="./media/tutorial-acm-create-budgets/trigger-action-group.png" :::

Następnie wybierz pozycję **Dodaj grupę akcji** i utwórz grupę akcji.

Budżet można zintegrować z grupami akcji tylko wtedy, gdy w grupie akcji wyłączono wspólny schemat alertów. Aby uzyskać więcej informacji na temat wyłączania tego schematu, zobacz [Jak włączyć wspólny schemat alertów?](../../azure-monitor/alerts/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>Tworzenie i edytowanie budżetów przy użyciu programu PowerShell

Jeśli jesteś klientem z umową EA, możesz tworzyć i edytować budżety programowo przy użyciu modułu Azure PowerShell. Aby pobrać najnowszą wersję programu Azure PowerShell, uruchom następujące polecenie:

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