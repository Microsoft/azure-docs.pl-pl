---
title: Konfigurowanie środowisk przejściowych
description: Dowiedz się, jak wdrażać aplikacje w gnieździe nieprodukcyjnym i przełączać je do środowiska produkcyjnego. Zwiększ niezawodność i Usuń przestoje aplikacji z wdrożeń.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: ef90603e8c8cdd66d43b9f88f6d128d8a472fd8a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150332"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurowanie środowisk przejściowych w usłudze Azure App Service
<a name="Overview"></a>

Podczas wdrażania aplikacji sieci Web, aplikacji sieci Web w systemie Linux, zaplecza mobilnego lub aplikacji interfejsu API do [Azure App Service](./overview.md)można użyć oddzielnego miejsca wdrożenia zamiast domyślnego miejsca produkcyjnego, gdy jest uruchomiony w warstwie planu warstwy **standardowa**, **Premium**lub App Service **izolowana** . Miejsca wdrożenia to aplikacje na żywo z własnymi nazwami hostów. Elementy zawartości i konfiguracji aplikacji można wymieniać między dwoma miejscami wdrożenia, w tym z miejscem produkcyjnym. 

Wdrażanie aplikacji w gnieździe nieprodukcyjnym ma następujące zalety:

* Można sprawdzić poprawność zmian aplikacji w miejscu wdrożenia przejściowego przed jego zainstalowaniem z miejscem produkcyjnym.
* Najpierw Wdrażaj aplikację w gnieździe i wymieniaj ją w środowisku produkcyjnym, aby upewnić się, że wszystkie wystąpienia gniazda są rozgrzane przed zamianą na środowisko produkcyjne. Eliminuje to przestoje podczas wdrażania aplikacji. Przekierowywanie ruchu jest płynne i żadne żądania nie są porzucane z powodu operacji zamiany. Możesz zautomatyzować ten cały przepływ pracy, konfigurując [Automatyczne zamienianie](#Auto-Swap) , gdy nie jest wymagana Walidacja sprzed wymiany.
* Po wymianie miejsce z wcześniej przemieszczoną aplikacją ma teraz poprzednią aplikację produkcyjną. Jeśli zmiany wprowadzone w gnieździe produkcyjnym nie są oczekiwane, można wykonać te same zamiany w celu przywrócenia "ostatniej znanej dobrej lokacji".

Każda App Service warstwy planu obsługuje inną liczbę miejsc wdrożenia. Nie ma dodatkowych opłat za korzystanie z miejsc wdrożenia. Aby sprawdzić liczbę gniazd obsługiwanych przez warstwę aplikacji, zobacz [limity App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Aby skalować aplikację do innej warstwy, upewnij się, że warstwa docelowa obsługuje liczbę gniazd używanych przez aplikację. Na przykład jeśli aplikacja ma więcej niż pięć gniazd, nie można skalować jej w dół do warstwy **standardowa** , ponieważ warstwa **standardowa** obsługuje tylko pięć miejsc wdrożenia. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Dodawanie miejsca
Aplikacja musi być uruchomiona w warstwie **standardowa**, **Premium**lub **izolowanej** , aby można było włączyć wiele miejsc wdrożenia.


1. w [Azure Portal](https://portal.azure.com/)Wyszukaj i wybierz pozycję **App Services** i wybierz aplikację. 
   
    ![Wyszukaj App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. W lewym okienku wybierz pozycję miejsca **wdrożenia**  >  **Dodaj miejsce**.
   
    ![Dodawanie nowego miejsca wdrożenia](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Jeśli aplikacja nie jest jeszcze w warstwie **standardowa**, **Premium**lub **izolowana** , zostanie wyświetlony komunikat z informacją o obsługiwanych warstwach umożliwiających publikowanie etapowe. W tym momencie możesz wybrać opcję **Uaktualnij** i przejść do karty **Skala** aplikacji przed kontynuowaniem.
   > 

3. W oknie dialogowym **Dodaj gniazdo** Nadaj nazwę gnieździe i wybierz, czy chcesz sklonować konfigurację aplikacji z innego miejsca wdrożenia. Wybierz pozycję **Dodaj** , aby kontynuować.
   
    ![Źródło konfiguracji](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Konfigurację można sklonować z dowolnego istniejącego miejsca. Ustawienia, które można klonować, obejmują ustawienia aplikacji, parametry połączeń, wersje języka Framework, gniazda sieci Web, wersje protokołu HTTP i bitową platformę.

4. Po dodaniu miejsca wybierz pozycję **Zamknij** , aby zamknąć okno dialogowe. Nowe miejsce jest teraz wyświetlane na stronie **miejsc wdrożenia** . Domyślnie **ruch%** ma wartość 0 dla nowego gniazda, a cały ruch klienta jest kierowany do miejsca produkcyjnego.

5. Wybierz nowe miejsce wdrożenia, aby otworzyć stronę zasobów tego gniazda.
   
    ![Tytuł miejsca wdrożenia](./media/web-sites-staged-publishing/StagingTitle.png)

    Miejsce przejściowe ma stronę zarządzania, podobnie jak każda inna aplikacja App Service. Można zmienić konfigurację gniazda. Aby przypominać, że oglądasz miejsce wdrożenia, nazwa aplikacji jest wyświetlana jako **\<app-name>/\<slot-name>** , a typ aplikacji to **App Service (gniazdo)**. Możesz również zobaczyć miejsce jako oddzielną aplikację w grupie zasobów, używając tych samych oznaczeń.

6. Wybierz adres URL aplikacji na stronie zasobów gniazda. Miejsce wdrożenia ma własną nazwę hosta i jest również działającą aplikacją. Aby ograniczyć dostęp publiczny do miejsca wdrożenia, zobacz [Azure App Service ograniczenia adresów IP](app-service-ip-restrictions.md).

Nowe miejsce wdrożenia nie ma zawartości, nawet w przypadku klonowania ustawień z innego gniazda. Na przykład możesz [publikować w tym gnieździe przy użyciu narzędzia Git](./deploy-local-git.md). Można wdrożyć w gnieździe z innej gałęzi repozytorium lub innego repozytorium.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Co się stanie w trakcie wymiany

### <a name="swap-operation-steps"></a>Etapy operacji zamiany

W przypadku wymiany dwóch gniazd (zwykle z miejsca przejściowego w gnieździe produkcyjnym) App Service wykonuje następujące czynności, aby upewnić się, że w gnieździe docelowym nie występują przestoje:

1. Zastosuj następujące ustawienia z miejsca docelowego (na przykład gniazdo produkcyjne) do wszystkich wystąpień miejsca źródłowego: 
    - Ustawienia aplikacji [specyficzne dla gniazda](#which-settings-are-swapped) i parametry połączenia, jeśli ma zastosowanie.
    - Ustawienia [ciągłego wdrażania](deploy-continuous-deployment.md) , jeśli są włączone.
    - [App Service ustawienia uwierzytelniania](overview-authentication-authorization.md) , jeśli są włączone.
    
    Każdy z tych przypadków wyzwala ponowne uruchomienie wszystkich wystąpień w gnieździe źródłowym. Podczas [wymiany z podglądem](#Multi-Phase)oznacza koniec pierwszej fazy. Operacja wymiany jest wstrzymana i można sprawdzić, czy gniazdo źródłowe działa prawidłowo z ustawieniami miejsca docelowego.

1. Zaczekaj na każde wystąpienie w gnieździe źródłowym, aby zakończyć jego ponowne uruchomienie. Jeśli jakiekolwiek wystąpienie nie powiedzie się ponownie, operacja wymiany przywraca wszystkie zmiany w gnieździe źródłowym i zatrzymuje operację.

1. Jeśli [lokalna pamięć podręczna](overview-local-cache.md) jest włączona, Wyzwalaj inicjalizację lokalnej pamięci podręcznej, wysyłając żądanie HTTP do głównego katalogu aplikacji ("/") w każdym wystąpieniu miejsca źródłowego. Poczekaj, aż każde wystąpienie zwróci odpowiedź HTTP. Inicjalizacja lokalnej pamięci podręcznej powoduje inne ponowne uruchomienie każdego wystąpienia.

1. Jeśli [Funkcja autoswap](#Auto-Swap) jest włączona z [niestandardowym rozruchem](#Warm-up), wyzwól [INICJOWANIE aplikacji](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) , wysyłając żądanie HTTP do głównego katalogu aplikacji ("/") w każdym wystąpieniu miejsca źródłowego.

    Jeśli `applicationInitialization` nie jest określony, wyzwól żądanie HTTP do katalogu głównego aplikacji w gnieździe źródłowym dla każdego wystąpienia. 
    
    Jeśli wystąpienie zwróci dowolną odpowiedź HTTP, jest uznawana za rozgrzaną.

1. Jeśli wszystkie wystąpienia w gnieździe źródłowym zostaną rozgrzane pomyślnie, Zastąp dwa gniazda, przełączając reguły routingu dla dwóch miejsc. Po wykonaniu tego kroku miejsce docelowe (na przykład miejsce produkcyjne) zawiera aplikację, która została wcześniej poddana w gnieździe źródłowym.

1. Teraz, gdy gniazdo źródłowe zawiera wcześniej aplikację do wstępnego zamiany w gnieździe docelowym, wykonaj tę samą operację, stosując wszystkie ustawienia i ponownie uruchamiając wystąpienia.

W dowolnym momencie operacji wymiany wszystkie prace inicjujące zamienione aplikacje odbywają się w miejscu źródłowym. Gniazdo docelowe pozostaje w trybie online, gdy gniazdo źródłowe jest przygotowywane i przerywane, bez względu na to, gdzie swap zakończy się powodzeniem, czy niepowodzeniem. Aby wymienić miejsce przejściowe z miejscem produkcyjnym, upewnij się, że miejsce produkcyjne jest zawsze miejscem docelowym. W ten sposób operacja zamiany nie ma wpływu na aplikację produkcyjną.

### <a name="which-settings-are-swapped"></a>Które ustawienia są zamieniane?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Aby skonfigurować ustawienie aplikacji lub parametry połączenia w celu przejścia do określonego gniazda (nie zamieniono), przejdź do strony **konfiguracji** dla tego gniazda. Dodaj lub Edytuj ustawienie, a następnie wybierz pozycję **ustawienie miejsca wdrożenia**. Zaznaczenie tego pola wyboru instruuje App Service, że ustawienie nie jest wymieniane. 

![Ustawienie miejsca](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Wymiana dwóch gniazd 
Możesz wymienić miejsca wdrożenia na stronie **miejsc wdrożenia** aplikacji i stronie **Przegląd** . Aby uzyskać szczegółowe informacje techniczne na temat wymiany gniazda, zobacz [co się dzieje podczas wymiany](#AboutConfiguration).

> [!IMPORTANT]
> Przed zamianą aplikacji z miejsca wdrożenia na produkcyjną upewnij się, że produkcja jest miejscem docelowym i że wszystkie ustawienia w gnieździe źródłowym są skonfigurowane dokładnie tak, jak chcesz je w środowisku produkcyjnym.
> 
> 

Aby wymienić miejsca wdrożenia:

1. Przejdź do strony miejsca **wdrożenia** aplikacji i wybierz pozycję **Zamień**.
   
    ![Przycisk zamiany](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Okno dialogowe **Zamiana** zawiera ustawienia w wybranych źródłowej i docelowej gnieździe, które zostaną zmienione.

2. Wybierz żądane miejsce **źródłowe** i **docelowe** . Zwykle miejscem docelowym jest miejsce produkcyjne. Zaznacz również karty **zmiany źródła** i **docelowe zmiany** , a następnie sprawdź, czy zmiany konfiguracji są oczekiwane. Po zakończeniu możesz zamienić miejsca bezpośrednio, wybierając pozycję **Zamień**.

    ![Kończenie zamiany](./media/web-sites-staged-publishing/SwapImmediately.png)

    Aby sprawdzić, jak miejsce docelowe będzie uruchamiane z nowymi ustawieniami przed faktycznym zastąpieniem, nie zaznaczaj opcji **Zamień**, ale postępuj zgodnie z instrukcjami w artykule [wymiana z podglądem](#Multi-Phase).

3. Po zakończeniu zamknij okno dialogowe, wybierając pozycję **Zamknij**.

Jeśli masz jakieś problemy, zobacz [Rozwiązywanie problemów z wymianą](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Zamień na wersję zapoznawczą (wymiana wielofazowy)

Przed przejściem do środowiska produkcyjnego jako miejsca docelowego Sprawdź, czy aplikacja jest uruchamiana z zamienionymi ustawieniami. Gniazdo źródłowe jest również rozgrzane przed ukończeniem wymiany, które jest odpowiednie dla aplikacji o znaczeniu strategicznym.

Podczas przeprowadzania wymiany w wersji zapoznawczej App Service wykonuje tę samą [operację zamiany](#AboutConfiguration) , ale wstrzymuje się po pierwszym kroku. Następnie można sprawdzić wynik w miejscu przejściowym przed ukończeniem zamiany. 

W przypadku anulowania zamiany App Service ponowne stosowanie elementów konfiguracji do miejsca źródłowego.

Aby zamienić z podglądem:

1. Postępuj zgodnie z instrukcjami w [miejsc wdrożenia wymiany](#Swap) , ale wybierz pozycję **Przeprowadź wymianę z podglądem**.

    ![Zamień na wersję zapoznawczą](./media/web-sites-staged-publishing/SwapWithPreview.png)

    W oknie dialogowym pokazano, w jaki sposób konfiguracja w gnieździe źródłowym zmienia się w fazie 1 oraz jak zmienia się miejsce źródłowe i docelowe w fazie 2.

2. Gdy wszystko będzie gotowe do rozpoczęcia wymiany, wybierz pozycję **Rozpocznij zamianę**.

    Po zakończeniu fazy 1 powiadomienie zostanie wyświetlone w oknie dialogowym. Wyświetl podgląd wymiany w gnieździe źródłowym, przechodząc do `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. Gdy wszystko będzie gotowe do ukończenia oczekującej zamiany, wybierz pozycję **Zakończ zamianę** w ramach **akcji zamiany** i wybierz pozycję **Zakończ zamianę**.

    Aby anulować oczekujące zamiany, wybierz pozycję **Anuluj zamianę** .

4. Po zakończeniu zamknij okno dialogowe, wybierając pozycję **Zamknij**.

Jeśli masz jakieś problemy, zobacz [Rozwiązywanie problemów z wymianą](#troubleshoot-swaps).

Aby zautomatyzować wielofazowy etap wymiany, zobacz [Automatyzacja przy użyciu programu PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Wycofywanie wymiany
Jeśli jakiekolwiek błędy wystąpią w gnieździe docelowym (na przykład w gnieździe produkcyjnym) po wymianie gniazd, Przywróć gniazda do swoich stanów sprzed zamiany, zamieniając te same dwa gniazda natychmiast.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurowanie zamiany automatycznej

> [!NOTE]
> Funkcja autoswap nie jest obsługiwana w aplikacjach sieci Web w systemie Linux.

Funkcja autoswap usprawnia scenariusze usługi Azure DevOps, w której aplikacja ma być ciągle wdrażana z zerowym zimnym rozpoczęciem i bez przestojów dla klientów aplikacji. Gdy funkcja automatycznej wymiany jest włączona z gniazda do środowiska produkcyjnego, przy każdym wypchnięciu zmian w kodzie w tym miejscu App Service automatycznie [zamienia aplikację na produkcję](#swap-operation-steps) , gdy zostanie ona rozgrzana w miejscu źródłowym.

   > [!NOTE]
   > Przed skonfigurowaniem autowymiany dla miejsca produkcyjnego Rozważ przetestowanie autowymiany w nieprodukcyjnym gnieździe docelowym.
   > 

Aby skonfigurować funkcję autoswap:

1. Przejdź do strony zasobów aplikacji. Wybierz pozycję Konfiguracja **miejsc wdrożenia**  >  *\<desired source slot>*  >  **Configuration**  >  **Ustawienia ogólne**.
   
2. W przypadku opcji **autoswap włączone**wybierz pozycję **włączone**. Następnie wybierz żądane miejsce docelowe dla **miejsca wdrożenia autoswap**, a następnie wybierz pozycję **Zapisz** na pasku poleceń. 
   
    ![Opcje konfigurowania opcji autoswap](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Wykonaj wypychanie kodu do miejsca źródłowego. Funkcja autoswap następuje po krótkim czasie, a aktualizacja zostanie odzwierciedlona w adresie URL miejsca docelowego.

Jeśli masz jakieś problemy, zobacz [Rozwiązywanie problemów z wymianą](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Określ niestandardową rozgrzewanie

Niektóre aplikacje mogą wymagać niestandardowych akcji rozgrzewania przed wymianą. `applicationInitialization`Element konfiguracji w web.config pozwala określić niestandardowe akcje inicjowania. [Operacja wymiany](#AboutConfiguration) czeka na zakończenie tej niestandardowej rozgrzanej wartości przed zamianą na miejsce docelowe. Oto przykładowy fragment web.config.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Aby uzyskać więcej informacji na temat dostosowywania `applicationInitialization` elementu, zobacz [najczęstsze błędy wymiany miejsca wdrożenia i sposoby ich naprawiania](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Możesz również dostosować zachowanie rozgrzewania, korzystając z jednego lub obu następujących [ustawień aplikacji](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Ścieżka do polecenia ping do rozgrzewania lokacji. Dodaj to ustawienie aplikacji, określając ścieżkę niestandardową rozpoczynającą się od ukośnika jako wartość. Może to być na przykład `/statuscheck`. Wartość domyślna to `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Prawidłowe kody odpowiedzi HTTP dla operacji rozgrzewania. Dodaj to ustawienie aplikacji z rozdzieloną przecinkami listą kodów HTTP. Przykładem jest `200,202` . Jeśli zwrócony kod stanu nie znajduje się na liście, operacje rozgrzewania i swap są zatrzymane. Domyślnie wszystkie kody odpowiedzi są prawidłowe.

> [!NOTE]
> `<applicationInitialization>`Element konfiguracji jest częścią każdego uruchomienia aplikacji, podczas gdy dwa ustawienia aplikacji do rozgrzanych zachowań stosują się tylko do wymiany gniazd.

Jeśli masz jakieś problemy, zobacz [Rozwiązywanie problemów z wymianą](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorowanie wymiany

Jeśli [operacja wymiany](#AboutConfiguration) trwa długo, możesz uzyskać informacje o operacji wymiany w [dzienniku aktywności](../azure-monitor/platform/platform-logs-overview.md).

Na stronie zasobów aplikacji w portalu w lewym okienku wybierz pozycję **Dziennik aktywności**.

Operacja zamiany zostanie wyświetlona w zapytaniu dziennika jako `Swap Web App Slots` . Można ją rozwinąć i wybrać jedną z podoperacji lub błędów, aby wyświetlić szczegóły.

## <a name="route-traffic"></a>Ruch tras

Domyślnie wszystkie żądania klientów do produkcyjnego adresu URL aplikacji ( `http://<app_name>.azurewebsites.net` ) są kierowane do miejsca produkcyjnego. Część ruchu można skierować do innego gniazda. Ta funkcja jest przydatna, jeśli potrzebujesz opinii użytkowników o nowej aktualizacji, ale nie możesz jej zwolnić do środowiska produkcyjnego.

### <a name="route-production-traffic-automatically"></a>Automatyczne kierowanie ruchu produkcyjnego

Aby automatycznie kierować ruch produkcyjny:

1. Przejdź do strony zasobów aplikacji i wybierz pozycję miejsca **wdrożenia**.

2. W kolumnie **ruch%** gniazda, do którego chcesz kierować dane, określ wartość procentową (od 0 do 100) reprezentującą łączny ruch, który ma być kierowany. Wybierz pozycję **Zapisz**.

    ![Ustawianie wartości procentowej ruchu](./media/web-sites-staged-publishing/RouteTraffic.png)

Po zapisaniu tego ustawienia określony procent klientów jest losowo kierowany do gniazda nieprodukcyjnego. 

Po automatycznym przekierowaniu klienta do określonego gniazda zostanie on przypięty do tego gniazda w okresie istnienia sesji klienta. W przeglądarce klienta możesz sprawdzić, do którego gniazda sesja jest przypięta, przeglądając `x-ms-routing-name` plik cookie w nagłówkach HTTP. Żądanie, które jest kierowane do miejsca "przemieszczania", zawiera plik cookie `x-ms-routing-name=staging` . Żądanie, które jest kierowane do miejsca produkcyjnego, zawiera plik cookie `x-ms-routing-name=self` .

   > [!NOTE]
   > Obok Azure Portal można również użyć [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) polecenia w interfejsie użytkownika platformy Azure, aby ustawić wartości procentowe routingu z narzędzi Ci/CD, takich jak potoki DevOps lub inne systemy automatyzacji.
   > 

### <a name="route-production-traffic-manually"></a>Ręczne kierowanie ruchu produkcyjnego

Oprócz automatycznego routingu ruchu App Service mogą kierować żądania do określonego gniazda. Jest to przydatne, jeśli chcesz, aby użytkownicy mogli wyrazić zgodę na lub zrezygnować z aplikacji w wersji beta. Aby ręcznie skierować ruch produkcyjny, należy użyć `x-ms-routing-name` parametru zapytania.

Aby umożliwić użytkownikom zrezygnowanie z aplikacji w wersji beta, można na przykład umieścić ten link na stronie sieci Web:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Ciąg `x-ms-routing-name=self` określa miejsce produkcyjne. Po uzyskaniu dostępu do linku przez przeglądarkę klienta zostanie on przekierowany do miejsca produkcyjnego. Każde kolejne żądanie zawiera `x-ms-routing-name=self` plik cookie, który przypina sesję do miejsca produkcyjnego.

Aby umożliwić użytkownikom dołączanie do aplikacji w wersji beta, należy ustawić ten sam parametr zapytania na nazwę gniazda nieprodukcyjnego. Oto przykład:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Domyślnie nowe gniazda otrzymują regułę routingu `0%` wyświetloną w kolorze szarym. Gdy jawnie ustawisz tę wartość na `0%` (pokazana w kolorze czarnym), użytkownicy mogą uzyskać dostęp do miejsca przejściowego ręcznie przy użyciu `x-ms-routing-name` parametru zapytania. Ale nie są one automatycznie kierowane do gniazda, ponieważ wartość procentowa routingu jest ustawiona na 0. Jest to zaawansowany scenariusz, w którym można "ukryć" miejsce przejściowe z publicznego, jednocześnie umożliwiając zespołom wewnętrznym testowanie zmian w gnieździe.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Usuń gniazdo

Wyszukaj i wybierz aplikację. Wybierz pozycję Omówienie **miejsc wdrożenia**  >  *\<slot to delete>*  >  **Overview**. Typ aplikacji jest wyświetlany jako **App Service (gniazdo)** , aby przypominać, że oglądasz miejsce wdrożenia. Na pasku poleceń wybierz pozycję **Usuń** .  

![Usuń miejsce wdrożenia](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatyzacja przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell to moduł, który udostępnia polecenia cmdlet do zarządzania platformą Azure za pomocą programu Windows PowerShell, w tym obsługę zarządzania miejscami wdrożenia w programie Azure App Service.

Informacje o instalowaniu i konfigurowaniu Azure PowerShell oraz o uwierzytelnianiu Azure PowerShell z subskrypcją platformy Azure znajdują się w temacie [How to Install and configure Microsoft Azure PowerShell](/powershell/azure/).  

---
### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Utwórz miejsce
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Inicjowanie wymiany przy użyciu wersji zapoznawczej (wymiana wielofazowy) i zastosowanie konfiguracji miejsca docelowego w gnieździe źródłowym
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Anuluj oczekującą zamianę (Zamień z przeglądem) i Przywróć konfigurację miejsca źródłowego
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Zamiana miejsc wdrożenia
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorowanie zdarzeń wymiany w dzienniku aktywności
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Usuń gniazdo
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatyzowanie przy użyciu szablonów Menedżer zasobów

[Szablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) to DEKLARACYJNE pliki JSON używane do automatyzowania wdrażania i konfigurowania zasobów platformy Azure. Aby zamienić miejsca przy użyciu szablonów Menedżer zasobów, należy ustawić dwie właściwości dla zasobów *Microsoft. Web/Sites/Slots* i *Microsoft. Web/Sites* :

- `buildVersion`: jest to właściwość ciągu reprezentująca bieżącą wersję aplikacji wdrożonej w gnieździe. Na przykład: "v1", "1.0.0.1" lub "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: jest to właściwość ciągu, która określa `buildVersion` , jakie miejsce powinno mieć gniazdo. Jeśli targetBuildVersion nie jest równa bieżącej `buildVersion` , spowoduje to wyzwolenie operacji zamiany przez znalezienie gniazda, które ma określony `buildVersion` .

### <a name="example-resource-manager-template"></a>Przykładowy szablon Menedżer zasobów

Poniższy szablon Menedżer zasobów zaktualizuje `buildVersion` miejsce przejściowe i ustawi wartość `targetBuildVersion` w miejscu produkcyjnym. Spowoduje to zamianę dwóch miejsc. Szablon zakłada, że masz już webapp utworzony z miejscem o nazwie "przemieszczanie".

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

Ten szablon Menedżer zasobów jest idempotentne, co oznacza, że może być wykonywany wielokrotnie i generować ten sam stan miejsc. Po pierwszym wykonaniu program `targetBuildVersion` będzie pasował do bieżącego `buildVersion` , więc nie zostanie wyzwolone zamiana.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatyzowanie przy użyciu interfejsu wiersza polecenia

Dla poleceń [interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli) dla miejsc wdrożenia zobacz [AZ webapp Deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Rozwiązywanie problemów z wymianami

Jeśli wystąpi błąd podczas [wymiany gniazd](#AboutConfiguration), jest on zalogowany *D:\home\LogFiles\eventlog.xml*. Jest on również rejestrowany w dzienniku błędów specyficznym dla aplikacji.

Poniżej przedstawiono niektóre typowe błędy wymiany:

- Upłynął limit czasu żądania HTTP do katalogu głównego aplikacji. Operacja zamiany czeka przez 90 sekund dla każdego żądania HTTP i ponawia próbę do 5 razy. Jeśli zostanie przekroczony limit czasu dla wszystkich ponownych prób, operacja wymiany zostanie zatrzymana.

- Inicjowanie lokalnej pamięci podręcznej może zakończyć się niepowodzeniem, gdy zawartość aplikacji przekracza limit przydziału dysku lokalnego określonego dla lokalnej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [lokalna pamięć podręczna — Omówienie](overview-local-cache.md).

- Podczas [niestandardowej rozgrzewania](#Warm-up)żądania HTTP są wykonywane wewnętrznie (bez przechodzenia przez zewnętrzny adres URL). Mogą oni zakończyć się niepowodzeniem z pewnymi regułami ponownego zapisywania adresów URL w *Web.config*. Na przykład reguły przekierowania nazw domen lub wymuszania protokołu HTTPS mogą uniemożliwiać rozgrzane żądania docierające do kodu aplikacji. Aby obejść ten problem, zmodyfikuj reguły ponownego zapisu, dodając następujące dwa warunki:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Bez niestandardowej rozgrzewania reguły ponownego zapisywania adresów URL nadal mogą blokować żądania HTTP. Aby obejść ten problem, zmodyfikuj reguły ponownego zapisu, dodając następujący warunek:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- Po wymianie gniazd może wystąpić nieoczekiwane ponowne uruchomienie aplikacji. Jest to spowodowane tym, że po wymianie Konfiguracja powiązania nazwy hosta nie jest zsynchronizowana, co nie powoduje ponownego uruchomienia. Jednak niektóre bazowe zdarzenia magazynu (takie jak przełączanie awaryjne woluminu magazynu) mogą wykryć te rozbieżności i wymusić ponowne uruchomienie wszystkich procesów roboczych. Aby zminimalizować te typy ponownych uruchomień, należy ustawić [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` ustawienie aplikacji](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) na *wszystkie gniazda*. To ustawienie aplikacji *nie działa jednak* z aplikacjami Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Następne kroki
[Blokuj dostęp do gniazd nieprodukcyjnych](app-service-ip-restrictions.md)