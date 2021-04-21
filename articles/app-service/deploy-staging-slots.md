---
title: Konfigurowanie środowisk przejściowych
description: Dowiedz się, jak wdrażać aplikacje w miejscu nieprodukcyjną i automatycznie wychwytować je do produkcji. Zwiększ niezawodność i wyeliminuj przestoje aplikacji po wdrożeniach.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 8a26332250f5c53116d940a2b625eb8d7991c187
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833116"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurowanie środowisk przejściowych w usłudze Azure App Service
<a name="Overview"></a>

Podczas wdrażania aplikacji internetowej, aplikacji internetowej w systemie Linux, aplikacji mobilnej lub aplikacji interfejsu API do usługi [Azure App Service](./overview.md)można użyć oddzielnego miejsca wdrożenia zamiast domyślnego miejsca produkcyjnego w przypadku korzystania z warstwy **standardowego,** **Premium** lub izolowanego **App Service** produkcyjnego. Miejsca wdrożenia to aplikacje na żywo z własnymi nazwami hostów. Zawartość aplikacji i elementy konfiguracji można zamienić między dwoma miejscami wdrożenia, w tym z miejsca produkcyjnego. 

Wdrożenie aplikacji w miejscu nieprodukcyjną ma następujące korzyści:

* Możesz zweryfikować zmiany aplikacji w przejściowym miejscu wdrożenia przed zamianą jej na miejsce produkcyjne.
* Najpierw wdrożenie aplikacji w miejscu i zamiana jej na produkcyjne zapewnia rozgrywanie wszystkich wystąpień miejsca przed zamianą na produkcyjne. Eliminuje to przestoje podczas wdrażania aplikacji. Przekierowywanie ruchu jest bezproblemowe i żadne żądania nie są porzucane z powodu operacji wymiany. Ten cały przepływ pracy można zautomatyzować, konfigurując zamianę [automatyczną,](#Auto-Swap) gdy weryfikacja przed zamianą nie jest potrzebna.
* Po zamianie miejsce z wcześniej przejechaną aplikacją ma teraz poprzednią aplikację produkcyjną. Jeśli zmiany zamienione w miejscu produkcyjnym nie są zgodnie z oczekiwaniami, możesz wykonać tę samą zamianę natychmiast, aby uzyskać z powrotem "ostatnią znaną dobrą witrynę".

Każda App Service warstwy planu wdrożenia obsługuje inną liczbę miejsc wdrożenia. Korzystanie z miejsc wdrożenia nie jest naliczane za dodatkową opłatą. Aby dowiedzieć się, jaka jest liczba miejsc, które obsługuje warstwa aplikacji, [zobacz limity App Service aplikacji.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) 

Aby skalować aplikację do innej warstwy, upewnij się, że warstwa docelowa obsługuje liczbę miejsc, z których aplikacja już korzysta. Jeśli na przykład aplikacja ma więcej niż pięć miejsc, nie można jej skalować w dół do warstwy **Standardowa,** ponieważ warstwa **Standardowa** obsługuje tylko pięć miejsc wdrożenia. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Dodawanie miejsca
Aby można było włączyć wiele miejsc wdrożenia, aplikacja musi działać w warstwie **Standardowa,** **Premium** lub **Izolowana.**


1. Na stronie [Azure Portal](https://portal.azure.com/)wyszukaj i wybierz pozycję **App Services** wybierz aplikację. 
   
    ![Wyszukaj App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. W okienku po lewej stronie wybierz pozycję **Miejsca wdrożenia Dodaj**  >  **miejsce**.
   
    ![Dodawanie nowego miejsca wdrożenia](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Jeśli aplikacja nie znajduje się jeszcze w warstwie **Standardowa,** **Premium** lub **Izolowana,** zostanie wyświetlony komunikat informujący o obsługiwanych warstwach umożliwiających publikowanie etapowe. W tym momencie możesz wybrać  pozycję Uaktualnij i przejść do karty **Skala** aplikacji przed kontynuowaniem.
   > 

3. W **oknie dialogowym Dodawanie** miejsca podaj nazwę miejsca i wybierz, czy sklonować konfigurację aplikacji z innego miejsca wdrożenia. Wybierz **pozycję Dodaj,** aby kontynuować.
   
    ![Źródło konfiguracji](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Konfigurację można sklonować z dowolnego istniejącego miejsca. Ustawienia, które można sklonować, obejmują ustawienia aplikacji, parametry połączenia, wersje platformy językowej, gniazda internetowe, wersję protokołu HTTP i bitowość platformy.

4. Po dodaniu miejsca wybierz pozycję **Zamknij,** aby zamknąć okno dialogowe. Nowe miejsce jest teraz wyświetlane na **stronie Miejsca wdrożenia.** Domyślnie wartość **% ruchu** jest ustawiona na 0 dla nowego miejsca, a cały ruch klientów jest przekierowyny do miejsca produkcyjnego.

5. Wybierz nowe miejsce wdrożenia, aby otworzyć stronę zasobów tego miejsca.
   
    ![Tytuł miejsca wdrożenia](./media/web-sites-staged-publishing/StagingTitle.png)

    Miejsce przejściowe zawiera stronę zarządzania, podobnie jak każda inna App Service aplikacji. Możesz zmienić konfigurację miejsca. Aby przypominać, że przeglądasz miejsce wdrożenia, nazwa aplikacji jest wyświetlana jako , a typ aplikacji **\<app-name>/\<slot-name>** **jest App Service (miejsce).** Możesz również zobaczyć miejsce jako oddzielną aplikację w grupie zasobów z tym samym oznaczeniem.

6. Wybierz adres URL aplikacji na stronie zasobu miejsca. Miejsce wdrożenia ma własną nazwę hosta i jest również aplikacją żywą. Aby ograniczyć dostęp publiczny do miejsca wdrożenia, zobacz [Azure App Service ograniczenia adresów IP.](app-service-ip-restrictions.md)

Nowe miejsce wdrożenia nie ma zawartości, nawet jeśli sklonuje się ustawienia z innego miejsca. Na przykład możesz opublikować [w tym miejscu za pomocą narzędzia Git](./deploy-local-git.md). Możesz wdrożyć w miejscu z innej gałęzi repozytorium lub innego repozytorium.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Co się dzieje podczas zamiany

### <a name="swap-operation-steps"></a>Kroki operacji zamiany

W przypadku zamiany dwóch miejsc (zazwyczaj z miejsca przejściowego do miejsca produkcyjnego) App Service, aby upewnić się, że w miejscu docelowym nie wystąpi przestój:

1. Zastosuj następujące ustawienia z miejsca docelowego (na przykład miejsca produkcyjnego) do wszystkich wystąpień miejsca źródłowego: 
    - [Ustawienia aplikacji specyficzne](#which-settings-are-swapped) dla miejsca i parametry połączenia, jeśli ma to zastosowanie.
    - [Ustawienia ciągłego](deploy-continuous-deployment.md) wdrażania, jeśli są włączone.
    - [App Service ustawienia uwierzytelniania,](overview-authentication-authorization.md) jeśli są włączone.
    
    Każdy z tych przypadków wyzwala ponowne uruchomienie wszystkich wystąpień w miejscu źródłowym. Podczas [zamiany z podglądem](#Multi-Phase)oznacza to koniec pierwszej fazy. Operacja zamiany jest wstrzymana i można sprawdzić, czy miejsce źródłowe działa prawidłowo z ustawieniami miejsca docelowego.

1. Poczekaj na zakończenie ponownego uruchamiania każdego wystąpienia w miejscu źródłowym. Jeśli ponowne uruchomienie jakiegokolwiek wystąpienia nie powiedzie się, operacja zamiany przywraca wszystkie zmiany do miejsca źródłowego i zatrzymuje operację.

1. Jeśli [włączono lokalną](overview-local-cache.md) pamięć podręczną, wyzwolij inicjowanie lokalnej pamięci podręcznej, wywołując żądanie HTTP do katalogu głównego aplikacji ("/") w każdym wystąpieniu miejsca źródłowego. Poczekaj, aż każde wystąpienie zwróci dowolną odpowiedź HTTP. Inicjalizacja lokalnej pamięci podręcznej powoduje kolejne ponowne uruchomienie każdego wystąpienia.

1. Jeśli [zamiana automatyczna](#Auto-Swap) jest włączona [](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) przy użyciu niestandardowej rozgrzewki, [](#Warm-up)wyzwolij inicjację aplikacji, wywołując żądanie HTTP do katalogu głównego aplikacji ("/") w każdym wystąpieniu miejsca źródłowego.

    Jeśli `applicationInitialization` nie zostanie określony, wyzwolij żądanie HTTP do katalogu głównego aplikacji w miejscu źródłowym w każdym wystąpieniu. 
    
    Jeśli wystąpienie zwraca dowolną odpowiedź HTTP, uważa się, że jest rozgrzewce.

1. Jeśli wszystkie wystąpienia w miejscu źródłowym zostaną rozgrzewce pomyślnie, zamień dwa miejsca, przełączając reguły rozsyłania dla dwóch miejsc. Po tym kroku miejsce docelowe (na przykład miejsce produkcyjne) zawiera aplikację, która została wcześniej rozgrzana w miejscu źródłowym.

1. Teraz, gdy miejsce źródłowe zawiera aplikację przed zamianą wcześniej w miejscu docelowym, wykonaj tę samą operację, stosując wszystkie ustawienia i ponownie uruchamiając wystąpienia.

W dowolnym momencie operacji zamiany cała praca nad zainicjowania zamienione aplikacje odbywa się w miejscu źródłowym. Miejsce docelowe pozostaje w trybie online podczas przygotowania i rozgrzewki miejsca źródłowego, niezależnie od tego, gdzie zamiana zakończy się powodzeniem lub niepowodzeniem. Aby zamienić miejsce przejściowe na miejsce produkcyjne, upewnij się, że miejsce produkcyjne jest zawsze miejscem docelowym. Dzięki temu operacja zamiany nie ma wpływu na aplikację produkcyjną.

### <a name="which-settings-are-swapped"></a>Które ustawienia są zamieniane?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Aby skonfigurować ustawienie aplikacji lub ciąg połączenia tak, aby nie były zamieniane w określonym miejscu, przejdź do strony **Konfiguracja** dla tego miejsca. Dodaj lub edytuj ustawienie, a następnie wybierz **ustawienie miejsca wdrożenia.** Zaznaczenie tego pola wyboru App Service, że ustawienie nie jest zamienialne. 

![Ustawienie miejsca](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Zamiana dwóch miejsc 
Miejsca wdrożenia można zamienić na stronie Miejsca **wdrożenia** aplikacji i **na stronie** Przegląd. Aby uzyskać szczegółowe informacje techniczne dotyczące zamiany miejsca, zobacz [Co się dzieje podczas zamiany](#AboutConfiguration).

> [!IMPORTANT]
> Przed zamianą aplikacji z miejsca wdrożenia na produkcyjne upewnij się, że miejsce docelowe to produkcja i że wszystkie ustawienia w miejscu źródłowym są skonfigurowane dokładnie tak, jak chcesz, aby były w środowisku produkcyjnym.
> 
> 

Aby zamienić miejsca wdrożenia:

1. Przejdź do strony Miejsca wdrożenia aplikacji **i** wybierz pozycję **Zamień**.
   
    ![Przycisk Zamień](./media/web-sites-staged-publishing/SwapButtonBar.png)

    W **oknie** dialogowym Zamiana są wyświetlane ustawienia w wybranych gniazdach źródłowych i docelowych, które zostaną zmienione.

2. Wybierz żądane **miejsca źródłowe** **i docelowe.** Zazwyczaj obiektem docelowym jest miejsce produkcyjne. Ponadto wybierz karty **Zmiany źródła** i **Zmiany docelowe** i sprawdź, czy zmiany konfiguracji są oczekiwane. Po zakończeniu możesz natychmiast zamienić miejsca, wybierając pozycję **Zamień**.

    ![Kończenie zamiany](./media/web-sites-staged-publishing/SwapImmediately.png)

    Aby zobaczyć, jak miejsce docelowe zostanie uruchomione z nowymi ustawieniami przed rozpoczęciem zamiany, nie wybieraj opcji Zamień **,** ale postępuj zgodnie z instrukcjami w temacie [Zamiana z podglądem](#Multi-Phase).

3. Po zakończeniu zamknij okno dialogowe, wybierając pozycję **Zamknij**.

Jeśli masz jakiekolwiek problemy, zobacz Rozwiązywanie [problemów z zamianami](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Zamiana z podglądem (zamiana wielofazowa)

Przed zamianą do produkcji jako miejsce docelowe sprawdź, czy aplikacja działa z zamienione ustawienia. Miejsce źródłowe jest również rozgrzewowane przed zakończeniem zamiany, co jest pożądane w przypadku aplikacji o znaczeniu krytycznym.

Podczas zamiany z podglądem program App Service tę [samą](#AboutConfiguration) operację zamiany, ale jest wstrzymywany po pierwszym kroku. Następnie możesz sprawdzić wynik w miejscu przejściowym przed zakończeniem zamiany. 

Jeśli anulujesz zamianę, App Service elementy konfiguracji do miejsca źródłowego.

Aby zamienić się z podglądem:

1. Postępuj zgodnie z instrukcjami z [tematu Zamiana miejsc wdrożenia,](#Swap) ale wybierz pozycję **Wykonaj zamianę z podglądem**.

    ![Zamiana z podglądem](./media/web-sites-staged-publishing/SwapWithPreview.png)

    W oknie dialogowym pokazano, jak zmienia się konfiguracja w miejscu źródłowym w fazie 1 oraz jak zmienia się miejsce źródłowe i docelowe w fazie 2.

2. Gdy wszystko będzie gotowe do rozpoczęcia zamiany, wybierz pozycję **Rozpocznij zamianę.**

    Po zakończeniu fazy 1 w oknie dialogowym zostanie wyświetlone powiadomienie. Wyświetl podgląd zamiany w miejscu źródłowym, przechodząc `https://<app_name>-<source-slot-name>.azurewebsites.net` do . 

3. Gdy wszystko będzie gotowe do ukończenia zamiany oczekującej, wybierz **akcję** Ukończ zamianę w **zamian** i wybierz pozycję **Ukończ zamianę.**

    Aby anulować oczekującą zamianę, wybierz **pozycję Anuluj zamianę.**

4. Po zakończeniu zamknij okno dialogowe, wybierając pozycję **Zamknij**.

Jeśli masz jakiekolwiek problemy, zobacz Rozwiązywanie [problemów z zamianami](#troubleshoot-swaps).

Aby zautomatyzować zamianę wielofazową, zobacz [Automatyzowanie przy użyciu programu PowerShell.](#automate-with-powershell)

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Wycofywanie zamiany
Jeśli w miejscu docelowym (na przykład w miejscu produkcyjnym) wystąpią błędy po zamianie miejsca, przywróć miejsca do ich stanów przed zamianą, natychmiast zamieniając te same dwa miejsca.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurowanie zamiany automatycznej

> [!NOTE]
> Zamiana automatyczna nie jest obsługiwana w aplikacjach internetowych w systemie Linux.

Zamiana automatyczna Azure DevOps scenariuszach, w których chcesz stale wdrażać aplikację bez zimnych startów i zerowych przestojów dla klientów aplikacji. Gdy zamiana automatyczna jest włączona z miejsca do produkcji, za każdym razem, gdy wypychasz zmiany kodu do tego miejsca, program App Service automatycznie zamienia aplikację w środowisku produkcyjnym po rozgrzewce w miejscu źródłowym. [](#swap-operation-steps)

   > [!NOTE]
   > Przed skonfigurowaniem zamiany automatycznej dla miejsca produkcyjnego rozważ przetestowanie zamiany automatycznej w miejscu docelowym nieprodukcyjną.
   > 

Aby skonfigurować zamianę automatyczną:

1. Przejdź do strony zasobów aplikacji. Wybierz **pozycję Miejsca wdrożenia**  >  *\<desired source slot>*  >  **Ustawienia** ogólne  >  **konfiguracji.**
   
2. W **przypadku opcji Zamiana automatyczna** włączona wybierz **pozycję Wł.**. Następnie wybierz żądane miejsce docelowe dla miejsca wdrożenia zamiany **automatycznej** i wybierz **pozycję Zapisz** na pasku poleceń. 
   
    ![Opcje konfigurowania zamiany automatycznej](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Wykonaj wypychanie kodu do miejsca źródłowego. Zamiana automatyczna następuje po krótkim czasie, a aktualizacja jest odzwierciedlana pod adresem URL miejsca docelowego.

Jeśli masz jakiekolwiek problemy, zobacz Rozwiązywanie [problemów z zamianami](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Określanie niestandardowej rozgrzewki

Niektóre aplikacje mogą wymagać niestandardowych akcji rozgrzewki przed zamianą. Element `applicationInitialization` konfiguracji w web.config umożliwia określenie niestandardowych akcji inicjowania. Operacja [zamiany](#AboutConfiguration) czeka na zakończenie tej niestandardowej rozgrzewki przed zamianą z miejsce docelowe. Oto przykładowy web.config fragment.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Aby uzyskać więcej informacji na temat dostosowywania elementu, zobacz Najczęściej spotykane błędy zamiany miejsca wdrożenia `applicationInitialization` [i jak je naprawić.](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)

Zachowanie rozgrzewki można również dostosować przy użyciu jednego lub obu następujących [ustawień aplikacji:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: ścieżka do polecenia ping w celu rozgrzewki witryny. Dodaj to ustawienie aplikacji, określając ścieżkę niestandardową, która rozpoczyna się ukośnikiem jako wartością. Może to być na przykład `/statuscheck`. Wartość domyślna to `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: prawidłowe kody odpowiedzi HTTP dla operacji rozgrzewki. Dodaj to ustawienie aplikacji z rozdzielaną przecinkami listą kodów HTTP. Przykładem jest `200,202` . Jeśli zwróconego kodu stanu nie ma na liście, operacje rozgrzewki i wymiany zostaną zatrzymane. Domyślnie wszystkie kody odpowiedzi są prawidłowe.
- `WEBSITE_WARMUP_PATH`: ścieżka względna w lokacji, która powinna być wysyłana za pomocą polecenia ping przy każdym ponownym uruchomieniu lokacji (nie tylko podczas zamiany miejsca). Przykładowe wartości `/statuscheck` to lub ścieżka główna, `/` .

> [!NOTE]
> Element konfiguracji jest częścią każdego uruchamiania aplikacji, natomiast dwa ustawienia aplikacji dotyczące zachowania rozgrzewki dotyczą `<applicationInitialization>` tylko zamiany miejsca.

Jeśli masz jakiekolwiek problemy, zobacz Rozwiązywanie [problemów z zamianami](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorowanie zamiany

Jeśli operacja [zamiany](#AboutConfiguration) trwa długo, możesz uzyskać informacje o operacji zamiany w [dzienniku aktywności](../azure-monitor/essentials/platform-logs-overview.md).

Na stronie zasobów aplikacji w portalu w okienku po lewej stronie wybierz pozycję **Dziennik aktywności.**

Operacja zamiany jest wyświetlana w zapytaniu dziennika jako `Swap Web App Slots` . Możesz go rozwinąć i wybrać jedną z podoperacji lub błędów, aby wyświetlić szczegóły.

## <a name="route-traffic"></a>Przekieruj ruch

Domyślnie wszystkie żądania klientów kierowane do produkcyjnego adresu URL aplikacji () są `http://<app_name>.azurewebsites.net` kierowane do miejsca produkcyjnego. Część ruchu można przekierowyć do innego miejsca. Ta funkcja jest przydatna, jeśli potrzebujesz opinii użytkowników na temat nowej aktualizacji, ale nie możesz jeszcze jej wydać w środowisku produkcyjnym.

### <a name="route-production-traffic-automatically"></a>Automatyczne przekieruj ruch produkcyjny

Aby automatycznie rozsyłać ruch produkcyjny:

1. Przejdź do strony zasobów aplikacji i wybierz pozycję **Miejsca wdrożenia.**

2. W **kolumnie Procent** ruchu w miejscu, do którego chcesz przekierowyć trasę, określ wartość procentową (od 0 do 100) reprezentującą łączną ilość ruchu, który chcesz przekierowyć. Wybierz pozycję **Zapisz**.

    ![Ustawianie procentu ruchu](./media/web-sites-staged-publishing/RouteTraffic.png)

Po zapisaniu ustawienia określona wartość procentowa klientów jest losowo przekierowyowana do miejsca nieprodukcji. 

Gdy klient jest automatycznie przekierowyowany do określonego miejsca, jest on "przypięty" do tego miejsca na czas życia tej sesji klienta. W przeglądarce klienta możesz zobaczyć, do którego miejsca jest przypięta sesja, patrząc na `x-ms-routing-name` plik cookie w nagłówkach HTTP. Żądanie, które jest kierowane do miejsca "przejściowego", ma plik cookie `x-ms-routing-name=staging` . Żądanie, które jest kierowane do miejsca produkcyjnego, ma plik cookie `x-ms-routing-name=self` .

   > [!NOTE]
   > Obok polecenia Azure Portal można również użyć polecenia w interfejsie wiersza polecenia platformy Azure, aby ustawić wartości procentowe routingu z narzędzi ci/CD, takich jak potoki DevOps lub inne systemy [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az_webapp_traffic_routing_set) automatyzacji.
   > 

### <a name="route-production-traffic-manually"></a>Ręczne przekieruj ruch produkcyjny

Oprócz automatycznego routingu ruchu usługa App Service kierowanie żądań do określonego miejsca. Jest to przydatne, gdy chcesz, aby użytkownicy mogli zrezygnować z aplikacji w wersji beta lub z nich zrezygnować. Aby ręcznie przekierowyć ruch produkcyjny, należy użyć `x-ms-routing-name` parametru zapytania.

Aby na przykład pozwolić użytkownikom zrezygnować z aplikacji w wersji beta, możesz umieścić ten link na stronie internetowej:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Ciąg określa `x-ms-routing-name=self` miejsce produkcyjne. Gdy przeglądarka klienta uzyskuje dostęp do linku, jest on przekierowywany do miejsca produkcyjnego. Każde kolejne żądanie ma plik cookie, który `x-ms-routing-name=self` przypina sesję do miejsca produkcyjnego.

Aby pozwolić użytkownikom wybrać aplikację w wersji beta, ustaw ten sam parametr zapytania na nazwę miejsca nieprodukcyjną. Oto przykład:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Domyślnie nowe gniazda mają regułę rozsyłania `0%` , wyświetlaną na szaro. Jeśli jawnie ustawisz tę wartość na (wyświetlaną jako czarny tekst), użytkownicy mogą ręcznie uzyskać dostęp do miejsca przejściowego przy `0%` użyciu `x-ms-routing-name` parametru zapytania. Nie będą one jednak automatycznie kierowane do miejsca, ponieważ wartość procentowa routingu jest ustawiona na 0. Jest to zaawansowany scenariusz, w którym można "ukryć" miejsce przejściowe przed publicznym, zezwalając zespołom wewnętrznym na testowanie zmian w miejscu.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Usuwanie miejsca

Wyszukaj i wybierz aplikację. Wybierz **pozycję Miejsca wdrożenia —**  >  *\<slot to delete>*  >  **omówienie.** Typ aplikacji jest wyświetlany jako **App Service (miejsce),** aby przypominać o wyświetlaniu miejsca wdrożenia. Wybierz **pozycję Usuń** na pasku poleceń.  

![Usuwanie miejsca wdrożenia](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatyzacja przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell to moduł, który udostępnia polecenia cmdlet do zarządzania platformą Azure za pośrednictwem usługi Windows PowerShell, w tym obsługę zarządzania miejscami wdrożenia w Azure App Service.

Aby uzyskać informacje na temat instalowania i konfigurowania Azure PowerShell oraz uwierzytelniania Azure PowerShell subskrypcji platformy Azure, zobacz Jak zainstalować i [skonfigurować](/powershell/azure/)Microsoft Azure PowerShell .  

---
### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Tworzenie miejsca
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Inicjowanie zamiany za pomocą wersji zapoznawczej (zamiana wielofazowa) i stosowanie konfiguracji miejsca docelowego do miejsca źródłowego
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Anulowanie oczekującej zamiany (zamiana z przeglądem) i przywracanie konfiguracji miejsca źródłowego
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Zamiana miejsc wdrożenia
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorowanie zdarzeń zamiany w dzienniku aktywności
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Usuwanie miejsca
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatyzowanie przy użyciu Resource Manager szablonów

[Azure Resource Manager to](../azure-resource-manager/templates/overview.md) deklaratywne pliki JSON służące do automatyzowania wdrażania i konfigurowania zasobów platformy Azure. Aby zamienić miejsca przy użyciu Resource Manager szablonów, ustawisz dwie właściwości w zasobach *Microsoft.Web/sites/slots* i *Microsoft.Web/sites:*

- `buildVersion`: jest to właściwość ciągu reprezentująca bieżącą wersję aplikacji wdrożonej w miejscu. Na przykład: "v1", "1.0.0.1" lub "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: jest to właściwość ciągu określająca, `buildVersion` co powinno mieć miejsce. Jeśli targetBuildVersion nie jest równa bieżącej wartości , spowoduje to wyzwolenie operacji zamiany przez znalezienie `buildVersion` miejsca, które ma określony element `buildVersion` .

### <a name="example-resource-manager-template"></a>Przykładowy Resource Manager szablonu

Poniższy Resource Manager zaktualizuje obiekt miejsca przejściowego i `buildVersion` ustawi wartość w `targetBuildVersion` miejscu produkcyjnym. Spowoduje to zamianę dwóch miejsc. W szablonie przyjęto założenie, że masz już utworzoną aplikacji internetowej z miejscem o nazwie "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Ten Resource Manager jest idempotentny, co oznacza, że może być wykonywany wielokrotnie i tworzyć ten sam stan miejsc. Po pierwszym wykonaniu wartość `targetBuildVersion` będzie odpowiadać bieżącej , `buildVersion` więc zamiana nie zostanie wyzwolona.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatyzacja przy użyciu interfejsu wiersza polecenia

Aby uzyskać [polecenia interfejsu](https://github.com/Azure/azure-cli) wiersza polecenia platformy Azure dla miejsc wdrożenia, zobacz [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Rozwiązywanie problemów z zamianami

Jeśli podczas zamiany miejsca wystąpi jakikolwiek [błąd,](#AboutConfiguration)zostanie onD:\home\LogFiles\eventlog.xml *.* Jest on również rejestrowany w dzienniku błędów specyficznym dla aplikacji.

Poniżej podano niektóre typowe błędy wymiany:

- Uchyliliśmy czas żądania HTTP do katalogu głównego aplikacji. Operacja zamiany czeka 90 sekund dla każdego żądania HTTP, a ponowne próby są 5 razy. Jeśli zostanie użytą wartość dla wszystkich ponownych prób, operacja zamiany zostanie zatrzymana.

- Inicjowanie lokalnej pamięci podręcznej może się nie powieść, gdy zawartość aplikacji przekroczy limit przydziału dysku lokalnego określony dla lokalnej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie lokalnej pamięci podręcznej](overview-local-cache.md).

- Podczas [niestandardowej rozgrzewki](#Warm-up)żądania HTTP są dokonywane wewnętrznie (bez użycia zewnętrznego adresu URL). Mogą one nie powieść się z pewnymi regułami ponownego pisali adresy URL *wWeb.config*. Na przykład reguły przekierowywania nazw domen lub wymuszania protokołu HTTPS mogą uniemożliwiać dotarcie żądań rozgrzewki do kodu aplikacji. Aby ominąc ten problem, zmodyfikuj reguły ponownego pisu, dodając następujące dwa warunki:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Bez niestandardowej rozgrzewki reguły ponownego pisali adresy URL nadal mogą blokować żądania HTTP. Aby ominąc ten problem, zmodyfikuj reguły ponownego pisu, dodając następujący warunek:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Po zamianie miejsca aplikacja może doświadczyć nieoczekiwanych ponownego uruchomienia. Dzieje się tak, ponieważ po zamianie konfiguracja powiązania nazwy hosta nie jest zsynchronizowana, co samo w sobie nie powoduje ponownego uruchomienia. Jednak niektóre podstawowe zdarzenia magazynu (takie jak trybu failover woluminu magazynu) mogą wykryć te rozbieżności i wymusić ponowne uruchomienie wszystkich procesów roboczych. Aby zminimalizować tego typu ponowne uruchomienia, ustaw [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` ustawienie aplikacji we](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) wszystkich *gniazdach*. To ustawienie aplikacji nie działa *jednak* z Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Następne kroki
[Blokowanie dostępu do miejsc nieprodukcyjnych](app-service-ip-restrictions.md)
