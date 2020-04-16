---
title: Mój pierwszy graficzny element runbook w usłudze Azure Automation
description: Samouczek, który przeprowadzi Cię przez procesy tworzenia, testowania i publikowania prostego graficznego elementu Runbook.
keywords: runbook, szablon runbook, automatyzacja runbook, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: bcef0574e16e0b4d28755716c32670b00c65af14
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406103"
---
# <a name="my-first-graphical-runbook"></a>Mój pierwszy graficzny element Runbook

> [!div class="op_single_selector"]
> * [Element graficzny](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Przepływ pracy programu PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Ten samouczek przeprowadzi Cię przez proces tworzenia [graficznego elementu Runbook](automation-runbook-types.md#graphical-runbooks) w usłudze Azure Automation. Zacznij od prostego śmiętu, który można przetestować i opublikować, podczas nauki śledzenia stanu zadania likścień. Następnie zmodyfikuj element runbook, aby faktycznie zarządzać zasobami platformy Azure, w tym przypadku uruchamianie maszyny wirtualnej platformy Azure. Ukończ samouczek, aby uczynić system runbook bardziej niezawodnym, dodając parametry zestawu runbook i łącza warunkowe.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Konto usługi Automation](automation-offering-get-started.md) do przechowywania elementu Runbook i uwierzytelniania w zasobach platformy Azure. To konto musi mieć uprawnienia do uruchamiania i zatrzymywania maszyny wirtualnej.
* Maszyna wirtualna platformy Azure. Ponieważ zatrzymujesz i uruchamiasz tę maszynę, nie powinna to być maszyna wirtualna produkcyjna.

## <a name="step-1---create-runbook"></a>Krok 1. Tworzenie elementu runbook

Zacznij od utworzenia prostego podstawowego `Hello World`podstawowego podstawowego tekstu .

1. W witrynie Azure Portal otwórz konto usługi Automation. 

    Strona konta usługi Automation umożliwia szybki przegląd zasobów na tym koncie. Konto powinno mieć już pewne elementy zawartości. Większość z tych zasobów to moduły automatycznie uwzględnione w nowym koncie automatyzacji. Powinieneś również mieć zasób poświadczeń skojarzony z subskrypcją.
2. Wybierz **pozycję Elementy runbook w** obszarze **Automatyzacja procesów,** aby otworzyć listę śmięty.
3. Utwórz nowy projekt runbook, wybierając **pozycję Utwórz projekt runbook**.
4. Nadaj elementowi Runbook nazwę **MyFirstRunbook-Graphical**.
5. W takim przypadku masz zamiar utworzyć [graficzny element runbook](automation-graphical-authoring-intro.md). Wybierz **opcję Graficzny** dla typu **eksmisja**.<br> ![Nowy element Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Kliknij pozycję **Utwórz**, aby utworzyć element Runbook i otworzyć edytor graficzny.

## <a name="step-2---add-activities"></a>Krok 2 - Dodawanie działań

Kontrolka Biblioteka po lewej stronie edytora umożliwia wybranie działań do dodania do elementu Runbook. Zamierzasz dodać polecenie `Write-Output` cmdlet do pliku wyjściowego tekstu z ego księgi runbook.

1. W formancie Biblioteka kliknij pole `write-output`wyszukiwania i wpisz . Wyniki wyszukiwania są wyświetlane na poniższej ilustracji. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Przewiń listę w dół. Kliknij prawym przyciskiem myszy **pozycję Zapis-dane wyjściowe** i wybierz polecenie **Dodaj do kanwy**. Alternatywnie można kliknąć wielokropek (...) obok nazwy polecenia cmdlet, a następnie wybrać **Dodaj do obszaru roboczego**.
1. Kliknij pozycję **Write-Output** na kanwie. Ta akcja otwiera stronę kontroli konfiguracji, która umożliwia skonfigurowanie działania.
1. Pole **Etykieta** domyślnie ma nazwę polecenia cmdlet, ale można je zmienić na bardziej przyjazne. Zmień go `Write Hello World to output`na .
1. Kliknij pozycję **Parametry**, aby podać wartości parametrów polecenia cmdlet.

   Niektóre polecenia cmdlet mają wiele zestawów parametrów i należy wybrać, który z nich ma być używany. W takim `Write-Output` przypadku ma tylko jeden zestaw parametrów.

1. Wybierz `InputObject` parametr. Jest to parametr, który służy do określenia tekstu do wysłania do strumienia wyjściowego.
1. Menu rozwijane **Źródło danych** zawiera źródła, których można użyć do wypełniania wartości parametru. W tym menu wybierz polecenie **Wyrażenie programu PowerShell**. 

   Można użyć danych wyjściowych z takich źródeł, jak inne działanie, zasób automatyzacji lub wyrażenie programu PowerShell. W tym przypadku wyjście `Hello World`jest po prostu . Możesz użyć wyrażenia programu PowerShell i wprowadzić ciąg.<br>

1. W polu **Wyrażenie** `Hello World` wpisz, a następnie kliknij dwukrotnie przycisk **OK,** aby powrócić do kanwy.
1. Zapisz element Runbook, klikając przycisk **Zapisz**.

## <a name="step-3---test-the-runbook"></a>Krok 3. Testowanie elementu Runbook

Przed opublikowaniem likemi, aby udostępnić go w produkcji, należy przetestować go, aby upewnić się, że działa poprawnie. Testowanie uruchomieniu uruchamia jego wersji roboczej i umożliwia interaktywne wyświetlanie jego danych wyjściowych.

1. Wybierz **okienko testu,** aby otworzyć okienko testowe.
1. Kliknij opcję **Uruchom**, aby rozpocząć test. Powinna to być jedyna włączona opcja.
1. Należy zauważyć, że [zadanie runbook](automation-runbook-execution.md) jest tworzony i jego stan jest wyświetlany w okienku.

   Stan zadania rozpoczyna `Queued`się jako , wskazując, że zadanie oczekuje na pracownika przewodniczącego w chmurze, aby stać się dostępne. Stan zmienia `Starting` się, gdy pracownik żąda zadania. Na koniec stan `Running` staje się, gdy runbook faktycznie zaczyna działać.

1. Po zakończeniu zadania runbook okienko Test wyświetla jego dane wyjściowe. W takim przypadku `Hello World`widzisz .

    ![Witaj, świecie](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Zamknij okienko testowania, aby wrócić do kanwy.

## <a name="step-4---publish-and-start-the-runbook"></a>Krok 4. Publikowanie i uruchamianie elementu Runbook

Utworzony projekt runbook jest nadal w trybie roboczym. Aby można było go uruchomić w środowisku produkcyjnym, należy go opublikować. Podczas publikowania elementu Runbook można zastąpić istniejącą wersję opublikowaną wersją roboczą. W tym przypadku nie ma jeszcze wersji opublikowanej, ponieważ element runbook został dopiero utworzony.

1. Wybierz **pozycję Publikuj,** aby opublikować projekt runbook, a następnie **przycisk Tak** po wyświetleniu monitu.
1. Przewiń w lewo, aby wyświetlić runbook na stronie Runbooks, i zwróć uwagę, że wartość **Stan autora** jest ustawiona na **Opublikowano**.
1. Przewiń z powrotem w prawo, aby wyświetlić stronę **MyFirstRunbook-Graphical**.

   Opcje u góry umożliwiają teraz uruchomienie elementu runbook, zaplanowanie przyszłej godziny rozpoczęcia lub utworzenie [elementu webhook,](automation-webhooks.md) aby można było uruchomić element runbook za pośrednictwem wywołania HTTP.

1. Wybierz **przycisk Start,** a następnie **przycisk Tak** po wyświetleniu monitu o uruchomienie śmięty.
1. Okienko Zadania jest otwierane dla zadania uruchomieniu, które zostało utworzone. Sprawdź, czy w polu **Stan zadania** jest wyświetlany **plik Zakończone**.
1. Kliknij **przycisk Wyjście,** aby otworzyć stronę `Hello World` Wyjście, na której można wyświetlić wyświetlane.
1. Zamknij stronę Dane wyjściowe.
1. Kliknij pozycję **Wszystkie dzienniki**, aby otworzyć okienko strumieni dla zadania elementu Runbook. Powinien być `Hello World` widoczny tylko w strumieniu wyjściowym. 

    Należy zauważyć, że okienko Strumienie można wyświetlić inne strumienie dla zadania przewodniczącego, takich jak pełne i strumienie błędów, jeśli runbook zapisuje do nich.
1. Zamknij okienko Strumienie i okienko zadania, aby powrócić do strony MyFirstRunbook-Graphical.
1. Aby wyświetlić wszystkie zadania dla wiązki uruchomieniu, wybierz pozycję **Zadania** w obszarze **Zasoby**. Strona Zadania zawiera listę wszystkich zadań utworzonych przez projekt runbook. Na liście powinna być wyświetlana tylko jedno zadanie, ponieważ zadanie zostało uruchomione tylko raz.
1. Kliknij nazwę zadania, aby otworzyć to samo okienko zadania, które zostało wyświetlone podczas uruchamiania likwidu. To okienko służy do wyświetlania szczegółów każdego zadania utworzonego dla egonatu.

## <a name="step-5---create-variable-assets"></a>Krok 5. Tworzenie zmiennych elementów zawartości

Testowano i opublikowano swój program runbook, ale do tej pory nie robi nic przydatnego do zarządzania zasobami platformy Azure. Przed skonfigurowaniem elementa runbook do uwierzytelniania należy utworzyć zmienną do przechowywania identyfikatora subskrypcji, skonfigurować działanie do uwierzytelniania, a następnie odwołać się do zmiennej. W tym odwołanie do kontekstu subskrypcji pozwala łatwo pracować z wieloma subskrypcjami.

1. Skopiuj swój identyfikator subskrypcji z opcji **Subskrypcje** w okienku nawigacji.
1. Na stronie Konta automatyzacji wybierz pozycję **Zmienne** w obszarze **Zasoby udostępnione**.
1. Wybierz **pozycję Dodaj zmienną**.
1. Na stronie Nowa zmienna w podanych polach wykonuj następujące ustawienia.

    * **Nazwa** -- `AzureSubscriptionId`wpisz .
    * **Wartość** — wprowadź identyfikator subskrypcji. 
    * **Wpisz** -- zachowaj zaznaczony ciąg.
    * **Szyfrowanie** — użyj wartości domyślnej.
1. Kliknij pozycję **Utwórz**, aby utworzyć zmienną.

## <a name="step-6---add-authentication"></a>Krok 6 - Dodawanie uwierzytelniania

Teraz, gdy masz zmienną do przechowywania identyfikatora subskrypcji, można skonfigurować runbook do uwierzytelniania przy użyciu poświadczeń Uruchom jako dla subskrypcji. W tym celu dodano połączenie Azure Run As jako zasób. Należy również dodać polecenie cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) i polecenie cmdlet [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) do obszaru roboczego.

>[!NOTE]
>Dla uruchomieniu programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są `Connect-AzAccount`aliasy dla . Należy pamiętać, że te aliasy nie są dostępne dla cząstek graficznych. Graficzny element runbook może `Connect-AzAccount`używać tylko siebie.

1. Przejdź do egobratu i wybierz **pozycję Edytuj** na stronie MyFirstRunbook-Graphical.
1. Nie potrzebujesz już `Write Hello World to output` wpisu. Wystarczy kliknąć wielokropek i wybrać **Usuń**.
1. W formancie Biblioteka rozwiń węzeł **ZASOBY**, a następnie **pozycję Połączenia**. Dodaj `AzureRunAsConnection` do kanwy, wybierając pozycję **Dodaj do kanwy**.
1. Zmień `AzureRunAsConnection` nazwę `Get Run As Connection`na .
1. W formancie `Connect-AzAccount` Biblioteka wpisz pole wyszukiwania.
1. Dodaj `Connect-AzAccount` do obszaru roboczego.
1. Umieść `Get Run As Connection` wskaźnik myszy na wysokości dołu kształtu. Kliknij okrąg i `Connect-AzAccount` przeciągnij strzałkę, aby utworzyć łącze. Projekt uruchamia się `Get Run As Connection` od, `Connect-AzAccount`a następnie uruchamia program .<br> ![Tworzenie połączenia między działaniami](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na kanwie `Connect-AzAccount`wybierz opcję . W okienku formantu Konfiguracja wpisz **zaloguj się na platformę Azure** w polu **Etykieta.**
1. Kliknij pozycję **Parametry**i zostanie wyświetlona strona Konfiguracja parametrów działania.
1. Polecenie `Connect-AzAccount` cmdlet ma wiele zestawów parametrów i należy wybrać jeden przed podaniem wartości parametrów. Kliknij **pozycję Zestaw parametrów,** a następnie wybierz pozycję **ServicePrincipalCertificateWithSubscriptionId**.
1. Parametry dla tego zestawu parametrów są wyświetlane na stronie Konfiguracja parametrów działania. Kliknij pozycję **APPLICATIONID**.<br> ![Dodawanie parametrów konta platformy Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na stronie Wartość parametru wykonuj następujące ustawienia, a następnie kliknij przycisk **OK**.

   * **Źródło danych** - wybierz **wyjście aktywności**.
   * Lista źródeł danych — wybierz **pozycję Pobierz połączenie automatyzacji**.
   * **Ścieżka pola** -- typ `ApplicationId`. Określasz nazwę właściwości ścieżki pola, ponieważ działanie wyprowadza obiekt z wieloma właściwościami.

1. Kliknij pozycję **CERTIFICATETHUMBPRINT**, a następnie na stronie Wartość parametru wytypuj następujące ustawienia, a następnie kliknij przycisk **OK**.

    * **Źródło danych** - wybierz **wyjście aktywności**.
    * Lista źródeł danych — wybierz **pozycję Pobierz połączenie automatyzacji**.
    * **Ścieżka pola** -- typ `CertificateThumbprint`.
1. Kliknij pozycję **SERVICEPRINCIPAL**, a następnie na stronie Wartość parametru wybierz pozycję **ConstantValue** for the **Data source** field; kliknij opcję **Prawda**; a następnie kliknij przycisk **OK**.
1. Kliknij **pozycję TENANTID**i wykonuj następujące ustawienia na stronie Wartość parametru. Po zakończeniu kliknij przycisk **OK** dwa razy.

    * **Źródło danych** - wybierz **wyjście aktywności**. 
    * Lista źródeł danych — wybierz **pozycję Pobierz połączenie automatyzacji**.
    * **Ścieżka pola** -- typ `TenantId`. 
1. W formancie `Set-AzContext` Biblioteka wpisz pole wyszukiwania.
1. Dodaj `Set-AzContext` do obszaru roboczego.
1. Wybierz `Set-AzContext` na kanwie. W okienku formantu `Specify Subscription Id` Konfiguracja wprowadź pole **Etykieta.**
1. Kliknij **pozycję Parametry** i zostanie wyświetlona strona Konfiguracja parametrów działania.
1. Polecenie `Set-AzContext` cmdlet ma wiele zestawów parametrów i należy wybrać jeden przed podaniem wartości parametrów. Kliknij **pozycję Zestaw parametrów,** a następnie wybierz pozycję **SubscriptionId**.
1. Parametry dla tego zestawu parametrów są wyświetlane na stronie Konfiguracja parametrów działania. Kliknij pozycję **Identyfikator subskrypcji**.
1. Na stronie Wartość parametru wybierz pozycję **Zmienny zasób** dla pola **Źródło danych** i wybierz **pozycję AzureSubscriptionId** z listy źródłowej. Po zakończeniu kliknij przycisk **OK** dwa razy.
1. Umieść `Login to Azure` wskaźnik myszy na wysokości dołu kształtu. Kliknij okrąg i `Specify Subscription Id`przeciągnij strzałkę do . W tym momencie twój system runbook powinien wyglądać następująco.

    ![Konfiguracja uwierzytelniania elementu Runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Krok 7. Dodawanie działania w celu uruchomienia maszyny wirtualnej

Teraz należy dodać `Start-AzVM` działanie, aby uruchomić maszynę wirtualną. Możesz wybrać dowolną maszynę wirtualną w ramach subskrypcji platformy Azure i na razie dysk koduj jej nazwę w pochwale [start-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0)

1. W formancie `Start-Az` Biblioteka wpisz pole wyszukiwania.
2. Dodaj `Start-AzVM` do obszaru roboczego, a `Specify Subscription Id`następnie kliknij i przeciągnij go pod spodem .
1. Umieść `Specify Subscription Id` wskaźnik myszy na wysokości dołu kształtu. Kliknij okrąg i `Start-AzVM`przeciągnij strzałkę do .
1. Wybierz pozycję `Start-AzVM`. Kliknij **pozycję Parametry,** a następnie **pozycję Zestaw parametrów,** aby wyświetlić zestawy dla działania.
1. Wybierz **opcję ResourceGroupNameParameterSetName** dla zestawu parametrów. Pola **ResourceGroupName** i **Name** mają obok nich wykrzykniki wskazujące, że są to wymagane parametry. Należy zauważyć, że oba pola oczekują wartości ciągu.
1. Wybierz pozycję **Nazwa**. Wybierz **wyrażenie programu PowerShell** dla pola **Źródło danych.** Dla maszyny Wirtualnej, która służy do uruchamiania tego systemu runbook, wpisz nazwę komputera otoczone cudzysłowami. Kliknij przycisk **OK**.
1. Wybierz pozycję **ResourceGroupName**. Użyj wyrażenia value **PowerShell** dla pola **Źródło danych** i wpisz nazwę grupy zasobów otoczonej podwójnymi cudzysłowami. Kliknij przycisk **OK**.
1. Kliknij **okienko testu,** aby można było przetestować projekt runbook.
1. Kliknij **przycisk Start,** aby rozpocząć test. Po jej zakończeniu upewnij się, że maszyna wirtualna została uruchomiona. W tym momencie twój system runbook powinien wyglądać następująco.

    ![Konfiguracja uwierzytelniania elementu Runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Krok 8 - Dodawanie dodatkowych parametrów wejściowych

Twój rekord runbook aktualnie uruchamia maszynę wirtualną w `Start-AzVM` grupie zasobów określonej dla polecenia cmdlet. System runbook będzie bardziej przydatny, jeśli określisz zarówno nazwę, jak i grupę zasobów podczas uruchamiania egobratu. Dodajmy parametry wejściowe do zestawu runbook, aby zapewnić tę funkcjonalność.

1. Otwórz edytor graficzny, klikając przycisk **Edytuj** na stronie MyFirstRunbook-Graphical.
1. Wybierz **pozycję Dane wejściowe i wyjściowe,** a następnie **dodaj dane wejściowe,** aby otworzyć okienko Parametr wejściowy księgi chyłka.
1. W podanych polach należy wprowadzić następujące ustawienia, a następnie kliknąć przycisk **OK**.
   * **Nazwa** -- `VMName`określ .
   * **Wpisz** - zachowaj ustawienie ciągu.
   * **Obowiązkowe** - zmień wartość na **Tak**.
1. Utwórz drugi parametr wejściowy o nazwie, `ResourceGroupName` a następnie kliknij przycisk **OK,** aby zamknąć okienko Wejście i Dane wyjściowe.<br> ![Parametry wejściowe elementu Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Wybierz `Start-AzVM` działanie, a następnie kliknij pozycję **Parametry**.
1. Zmień pole **Źródło danych** dla danych na **dane wejściowe** **Runbook** . Następnie wybierz **VMName**.
1. Zmień pole **Źródło danych** dla **resourcegroupname** na **dane runbook,** a następnie wybierz pozycję **ResourceGroupName**.<br> ![Parametry start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Zapisz element Runbook, a następnie otwórz okienko testowania. Teraz możesz podać wartości dwóch zmiennych wejściowych, które zostaną użyte w teście.
1. Zamknij okienko testowania.
1. Kliknij pozycję **Opublikuj**, aby opublikować nową wersję elementu Runbook.
1. Zatrzymaj maszynę wirtualną, która została uruchomiona wcześniej.
1. Kliknij pozycję **Uruchom**, aby uruchomić element Runbook. Wpisz wartości dla `VMName` `ResourceGroupName` i dla maszyny Wirtualnej, które mają się rozpocząć.
1. Po zakończeniu uruchomieniu upewnij się, że maszyna wirtualna została uruchomiona.

## <a name="step-9---create-a-conditional-link"></a>Krok 9. Tworzenie połączenia warunkowego

Teraz można zmodyfikować runbook tak, aby tylko próby uruchomienia maszyny Wirtualnej, jeśli nie jest jeszcze uruchomiony. W tym celu dodano polecenie cmdlet [Get-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) które pobiera stan na poziomie wystąpienia maszyny Wirtualnej. Następnie można dodać moduł kodu przepływu pracy `Get Status` programu PowerShell wywołany fragmentem kodu programu PowerShell, aby ustalić, czy stan maszyny Wirtualnej jest uruchomiony lub zatrzymany. Łącze warunkowe `Get Status` z `Start-AzVM` modułu jest uruchamiane tylko wtedy, gdy bieżący stan uruchomiony zostanie zatrzymany. Na końcu tej procedury system runbook `Write-Output` używa polecenia cmdlet do wysuwu komunikatu informującego, czy maszyna wirtualna została pomyślnie uruchomiona.

1. Otwórz **MyFirstRunbook-Graphical** w edytorze graficznym.
1. Usuń łącze między `Specify Subscription Id` i `Start-AzVM` klikając na niego, a następnie naciskając przycisk **Usuń**.
1. W formancie `Get-Az` Biblioteka wpisz pole wyszukiwania.
1. Dodaj `Get-AzVM` do obszaru roboczego.
1. Zaznacz, `Get-AzVM` a następnie kliknij pozycję **Zestaw parametrów,** aby wyświetlić zestawy poleceń cmdlet. 
1. Wybierz zestaw parametrów **GetVirtualMachineInResourceGroupNameParamSet**. Pola **ResourceGroupName** i **Name** mają obok nich wykrzykniki wskazujące, że określają wymagane parametry. Należy zauważyć, że oba pola oczekują wartości ciągu.
1. W obszarze **Źródło danych** dla **Name**wybierz pozycję **Runbook input**, a następnie **VMName**. Kliknij przycisk **OK**.
1. W obszarze **Źródło danych** dla **ResourceGroupName**wybierz pozycję **Dane eksletuj**, a następnie **Nazwa grupy zasobów**. Kliknij przycisk **OK**.
1. W obszarze **Źródło danych** dla **stanu**wybierz pozycję **Stała wartość**, a następnie Wartość **Prawda**. Kliknij przycisk **OK**.
1. Utwórz łącze z `Specify Subscription Id` do `Get-AzVM`.
1. W kontrolce Biblioteka rozwiń pozycję **Formant ywy** i dodaj **kod** do kanwy.  
1. Utwórz łącze z `Get-AzVM` do `Code`.  
1. Kliknij, `Code` a w okienku Konfiguracja zmień etykietę na **Uzyskaj stan**.
1. Wybierz `Code` i zostanie wyświetlona strona Edytor kodu.  
1. Wklej następujący fragment kodu do strony edytora.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Utwórz łącze z `Get Status` do `Start-AzVM`.

    ![Element Runbook z modułem kodu](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Wybierz łącze i w okienku Konfiguracja zmień **warunek Zastosuj** na **Tak**. Należy zauważyć, że łącze staje się linią przerywaną, co wskazuje, że działanie docelowe jest uruchamiane tylko wtedy, gdy warunek jest rozpoznawany jako true.  
1. W przypadku wyrażenia `$ActivityOutput['Get Status'] -eq "Stopped"` **Warunek**wpisz . `Start-AzVM`teraz działa tylko wtedy, gdy maszyna wirtualna jest zatrzymana.
1. W kontrolce Biblioteka rozwiń pozycję **Polecenia cmdlet**, a następnie pozycję **Microsoft.PowerShell.Utility**.
1. Dodaj `Write-Output` do kanwy dwa razy.
1. W przypadku `Write-Output` pierwszego formantu kliknij pozycję **Parametry** i zmień wartość **Etykieta** na **Powiadom maszynę wirtualną uruchomiona**.
1. W przypadku **inputobject**zmień **źródło danych** na wyrażenie programu `$VMName successfully started.` **PowerShell**i wpisz wyrażenie .
1. W drugim `Write-Output` formancie kliknij pozycję **Parametry** i zmień wartość **Etykieta** na **Powiadomienie o uruchomieniu maszyny Wirtualnej nie powiodło się**.
1. W przypadku **inputobject**zmień **źródło danych** na wyrażenie programu `$VMName could not start` **PowerShell**i wpisz wyrażenie .
1. Tworzenie łączy `Start-AzVM` `Notify VM Started` z `Notify VM Start Failed`do i .
1. Zaznacz łącze do `Notify VM Started` i zmień Zastosuj **warunek** do true.
1. W przypadku **wyrażenia** `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`Warunek wpisz . Ten `Write-Output` formant jest teraz uruchamiany tylko wtedy, gdy maszyna wirtualna zostanie pomyślnie uruchomiony.
1. Zaznacz łącze do `Notify VM Start Failed` i zmień Zastosuj **warunek** do true.
1. W polu **Wyrażenie** Warunek `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`wpisz polecenie . Ten `Write-Output` formant jest teraz uruchamiany tylko wtedy, gdy maszyna wirtualna nie została pomyślnie uruchomiona. Twój projekt runbook powinien wyglądać jak na poniższej ilustracji.

    ![Element Runbook z pozycją Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Zapisz element Runbook, a następnie otwórz okienko testowania.
1. Uruchom program runbook z zatrzymaną maszyną wirtualną i urządzenie powinno zostać uruchomione.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tworzenie grafiki, zobacz [Tworzenie graficzne w usłudze Azure Automation](automation-graphical-authoring-intro.md).
* Aby rozpocząć pracę z elementami Runbook programu PowerShell, zobacz artykuł [My first PowerShell runbook](automation-first-runbook-textual-powershell.md) (Mój pierwszy element Runbook programu PowerShell).
* Aby rozpocząć pracę z uruchomieniu przepływem pracy programu PowerShell, zobacz [Mój pierwszy program PowerShell .](automation-first-runbook-textual.md)
* Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).