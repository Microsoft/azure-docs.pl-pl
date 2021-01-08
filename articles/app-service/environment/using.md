---
title: Używanie środowiska usługi App Service
description: Dowiedz się, jak używać App Service Environment do hostowania izolowanych aplikacji.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c0ceae8727681c045c3bbf3e6626937633b38997
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013536"
---
# <a name="using-an-app-service-environment"></a>Korzystanie z App Service Environment

App Service Environment (ASE) jest wdrożeniem pojedynczej dzierżawy Azure App Service, która wprowadza bezpośrednio do Virtual Network platformy Azure. Jest to system, który jest używany tylko przez jednego klienta. Aplikacje wdrożone w środowisku ASE podlegają funkcjom sieci, które są stosowane do podsieci środowiska ASE. Nie są dostępne żadne dodatkowe funkcje, które muszą być włączone w aplikacjach, aby podlegać tym funkcjom sieci. 

## <a name="create-an-app-in-an-ase"></a>Tworzenie aplikacji w środowisku ASE

Aby utworzyć aplikację w środowisku ASE, należy użyć tego samego procesu jak w przypadku normalnego tworzenia aplikacji, ale z kilkoma niewielkimi różnicami. Podczas tworzenia nowego planu App Service:

- Zamiast wybierać lokalizację geograficzną, w której ma zostać wdrożona aplikacja, należy wybrać środowisko ASE jako lokalizację.
- Wszystkie plany App Service utworzone w środowisku ASE mogą znajdować się tylko w warstwie cenowej wyizolowanej v2.

Jeśli nie masz środowiska ASE, możesz go utworzyć, postępując zgodnie z instrukcjami w temacie [tworzenie App Service Environment][MakeASE].

Aby utworzyć aplikację w środowisku ASE:

1. Wybierz pozycję **Utwórz zasób**  >  **Sieć Web + aplikacje mobilne**  >  **aplikacji sieci Web**.

1. Wybierz subskrypcję.

1. Wprowadź nazwę nowej grupy zasobów lub wybierz pozycję **Użyj istniejącej** , a następnie wybierz jedną z listy rozwijanej.

1. wprowadź nazwę aplikacji. Jeśli w środowisku ASE został już wybrany plan App Service, nazwa domeny aplikacji odzwierciedla nazwę domeny środowiska ASE:

    ![Tworzenie aplikacji w środowisku ASE][1]

1. Wybierz typ publikacji, stos i system operacyjny.

1.  Wybierz region. W tym miejscu musisz wybrać wcześniej istniejące App Service Environment v3.  Nie można utworzyć ASEv3 podczas tworzenia aplikacji 

1. Wybierz istniejący plan App Service w środowisku ASE lub Utwórz nowy. W przypadku tworzenia nowej aplikacji wybierz rozmiar, który ma być używany dla planu App Service. Jedyną jednostką SKU, którą można wybrać dla aplikacji, jest jednostka SKU z cennikiem w wersji 2.

    ![Warstwy cenowe wyizolowane v2][2]

    > [!NOTE]
    > Aplikacje Linux i aplikacje systemu Windows nie mogą znajdować się w tym samym planie App Service, ale mogą być w tej samej App Service Environment.
    >

1. Wybierz pozycję * * Dalej: monitorowanie * * Jeśli chcesz włączyć usługi App Insights w aplikacji, możesz to zrobić w tym miejscu podczas tworzenia przepływu. 

1.  Wybierz **Następny: Tagi** Dodaj Tagi, które chcesz umieścić w aplikacji  

1. Wybierz pozycję **Przegląd + Utwórz**, upewnij się, że informacje są poprawne, a następnie wybierz pozycję **Utwórz**.

## <a name="how-scale-works"></a>Jak działa skalowanie

Każda aplikacja App Service uruchamiana w planie App Service. Środowiska App Service przechowują plany App Service, a App Service plany przechowują aplikacje. Skalowanie aplikacji pozwala skalować plan App Service i wszystkie aplikacje tego samego planu.

W przypadku skalowania planu App Service wymagana jest automatyczna infrastruktura. Istnieje opóźnienie czasu na skalowanie operacji podczas dodawania infrastruktury. Podczas skalowania planu App Service wszystkie inne operacje skalowania wymagane przez ten sam system operacyjny i rozmiar będą oczekiwać do momentu pierwszego zakończenia. Po zakończeniu operacji skalowania blokującego wszystkie żądania w kolejce są przetwarzane w tym samym czasie. Operacja skalowania w jednym rozmiarze i systemie operacyjnym nie będzie blokować skalowania innych kombinacji rozmiaru i systemu operacyjnego. Na przykład w przypadku skalowania planu App Service w systemie Windows I2v2 wszystkie inne żądania skalowania I2v2 systemu Windows w tym środowisku ASE zostaną dodane do kolejki do momentu zakończenia tego procesu.   

W App Service wielodostępnym skalowanie jest natychmiastowe, ponieważ pula zasobów jest łatwo dostępna do obsługi. W środowisku ASE nie ma takiego buforu, a zasoby są przyliczane na podstawie potrzeb.

## <a name="app-access"></a>Dostęp do aplikacji

W środowisku ASE sufiks domeny używany do tworzenia aplikacji to *. &lt; asename &gt; . appserviceenvironment.NET*. Jeśli środowisko ASE ma nazwę _My-ASE_ i hostuje aplikację o nazwie _contoso_ w tym środowisku ASE, dotrzesz do niej przy użyciu następujących adresów URL:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Aby uzyskać informacje o sposobach tworzenia środowiska ASE, zobacz [tworzenie App Service Environment][MakeASE].

Adres URL usługi SCM służy do uzyskiwania dostępu do konsoli usługi kudu lub publikowania aplikacji przy użyciu Web Deploy. Aby uzyskać informacje na temat konsoli kudu, zobacz [kudu Console for Azure App Service][Kudu]. Konsola kudu udostępnia interfejs użytkownika sieci Web do debugowania, przekazywania plików, edytowania plików i wielu innych.

### <a name="dns-configuration"></a>Konfiguracja usługi DNS 

Środowisko ASE używa prywatnych punktów końcowych dla ruchu przychodzącego. Nie jest on automatycznie konfigurowany przy użyciu stref prywatnych Azure DNS. Jeśli chcesz użyć własnego serwera DNS, musisz dodać następujące rekordy:

1. Utwórz strefę dla &lt; nazwy środowiska ASE &gt; . appserviceenvironment.NET
1. Utwórz rekord A w tej strefie, który wskazuje * na adres IP ruchu przychodzącego używany przez prywatny punkt końcowy środowiska ASE
1. Utwórz w tej strefie rekord A wskazujący, że adres IP dla ruchu przychodzącego używany przez prywatny punkt końcowy środowiska ASE
1. Utwórz strefę w programie &lt; ASE Name &gt; . appserviceenvironment.NET o nazwie SCM
1. Utwórz rekord A w strefie SCM, który wskazuje na adres IP używany przez prywatny punkt końcowy środowiska ASE

Aby skonfigurować serwer DNS w Azure DNS strefach prywatnych:

1. Utwórz Azure DNS strefę prywatną o nazwie <ASE name> . appserviceenvironment.NET
1. Utwórz rekord A w tej strefie, który wskazuje * na adres IP ILB
1. Utwórz rekord A w tej strefie, który wskazuje na ILB adres IP
1. Utwórz rekord A w tej strefie, który wskazuje *. SCM na adres IP ILB

Ustawienia DNS dla domyślnego sufiksu domeny środowiska ASE nie ograniczają aplikacji do dostępu do tych nazw. Możesz ustawić niestandardową nazwę domeny bez żadnej weryfikacji aplikacji w środowisku ASE. Jeśli chcesz utworzyć strefę o nazwie *contoso.NET*, możesz to zrobić i wskazać adres IP dla ruchu przychodzącego. Niestandardowa nazwa domeny działa w przypadku żądań aplikacji, ale nie dla witryny SCM. Witryna SCM jest dostępna tylko w witrynie *&lt; nazwa_aplikacji &gt; . SCM. &lt; asename &gt; . appserviceenvironment.NET*. 

## <a name="publishing"></a>Publikowanie

W środowisku ASE, podobnie jak w App Service wielodostępne, można publikować według następujących metod:

- Wdrażanie w sieci Web
- Ciągła integracja (CI)
- Przeciąganie i upuszczanie w konsoli kudu
- Środowisko IDE, takie jak Visual Studio, zaćmienie lub IntelliJ pomysł

W środowisku ASE punkty końcowe publikowania są dostępne tylko za pomocą adresu przychodzącego używanego przez prywatny punkt końcowy. Jeśli nie masz dostępu sieciowego do prywatnego adresu punktu końcowego, nie możesz publikować żadnych aplikacji w tym środowisku ASE.  Środowisk IDE musi również mieć dostęp sieciowy do ILB, aby opublikować go bezpośrednio.

Bez dodatkowych zmian internetowe systemy CI, takie jak GitHub i Azure DevOps, nie współpracują z ILB ASE, ponieważ punkt końcowy publikowania nie jest dostępny dla Internetu. Możesz włączyć publikowanie w środowisku ILB ASE z poziomu usługi Azure DevOps, instalując własny Agent wydania w sieci wirtualnej, która zawiera ILB ASE. 

## <a name="storage"></a>Magazyn

Środowisko ASE ma 1 TB magazynu dla wszystkich aplikacji w środowisku ASE. Plan App Service w odizolowanej cenie SKU ma limit wynoszący 250 GB. W środowisku ASE 250 GB miejsca do magazynowania jest dodawane App Service planowanie do limitu 1 TB. Możesz mieć więcej App Service planów niż tylko cztery, ale nie Dodaliśmy więcej miejsca poza limit 1 TB.

## <a name="logging"></a>Rejestrowanie

Możesz zintegrować środowisko ASE z Azure Monitor, aby wysyłać dzienniki dotyczące środowiska ASE do usługi Azure Storage, Azure Event Hubs lub Log Analytics. Te elementy są rejestrowane Dzisiaj:

| Istniał | Komunikat |
|---------|----------|
| Środowisko ASE jest w złej kondycji | Określony środowisko ASE jest w złej kondycji ze względu na nieprawidłową konfigurację sieci wirtualnej. Środowisko ASE zostanie zawieszone w przypadku kontynuowania stanu złej kondycji. Upewnij się, że zostały wykonane następujące wytyczne: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Za mało miejsca w podsieci ASE | Określone środowisko ASE znajduje się w podsieci, która jest w prawie nieprawidłowym miejscu. Istnieją {0} adresy. Po wyczerpaniu tych adresów środowisko ASE nie będzie w stanie skalować.  |
| Środowisko ASE zbliża się do łącznego limitu wystąpień | Określone środowisko ASE zbliża się do całkowitego limitu wystąpień środowiska ASE. Obecnie zawiera {0} App Service wystąpienia planu o maksymalnej liczbie 201 wystąpień. |
| Środowisko ASE nie może nawiązać połączenia z zależnością | Nie można nawiązać połączenia z określonym środowiskiem ASE {0} .  Upewnij się, że zostały wykonane następujące wytyczne: https://docs.microsoft.com/azure/app-service/environment/network-info . |
| Środowisko ASE jest zawieszone | Określona środowisko ASE jest zawieszone. Zawieszenie środowiska ASE może być spowodowane brakiem konta lub konfiguracją nieprawidłowej sieci wirtualnej. Rozwiąż główną przyczynę i Wznów środowisko ASE, aby kontynuować obsługę ruchu. |
| Rozpoczęto Uaktualnianie środowiska ASE | Rozpoczęto Uaktualnianie platformy do określonego środowiska ASE. Oczekiwane opóźnienia operacji skalowania. |
| Ukończono Uaktualnianie środowiska ASE | Uaktualnianie platformy do określonego środowiska ASE zostało zakończone. |
| Rozpoczęto operacje skalowania | {0}Rozpoczęcie skalowanie planu App Service (). Żądany stan: {1} I {2} pracownicy.
| Ukończono operacje skalowania | Zakończono skalowanie planu App Service ( {0} ). Bieżący stan: {1} I {2} pracownicy. |
| Operacje skalowania zakończyły się niepowodzeniem | Skalowanie planu App Service ( {0} ) nie powiodło się. Bieżący stan: {1} I {2} pracownicy. |

Aby włączyć rejestrowanie w środowisku ASE:

1. W portalu przejdź do pozycji **Ustawienia diagnostyki**.
1. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.
1. Podaj nazwę dla integracji dzienników.
1. Wybierz i skonfiguruj lokalizacje docelowe dzienników.
1. Wybierz pozycję **AppServiceEnvironmentPlatformLogs**.

![Ustawienia dziennika diagnostyki środowiska ASE][4]

W przypadku integracji z usługą Log Analytics można zobaczyć dzienniki, wybierając pozycję **dzienniki** w portalu środowiska ASE i tworząc zapytanie względem **AppServiceEnvironmentPlatformLogs**. Dzienniki są emitowane tylko wtedy, gdy środowisko ASE ma zdarzenie, które zostanie wyzwolone. Jeśli środowisko ASE nie ma takiego zdarzenia, nie będzie żadnych dzienników. Aby szybko zobaczyć przykład dzienników w obszarze roboczym Log Analytics, wykonaj operację skalowania z jednym z planów App Service w środowisku ASE. Następnie można uruchomić zapytanie z **AppServiceEnvironmentPlatformLogs** , aby wyświetlić te dzienniki. 

**Tworzenie alertu**

Aby utworzyć alert dotyczący dzienników, postępuj zgodnie z instrukcjami w temacie [Tworzenie, wyświetlanie i zarządzanie alertami dzienników przy użyciu Azure monitor](../../azure-monitor/platform/alerts-log.md). W skrócie:

* Otwieranie strony alertów w portalu ASE
* Wybierz **nową regułę alertu**
* Wybierz zasób, który ma być obszarem roboczym Log Analytics
* Ustaw warunek z niestandardowym wyszukiwaniem w dzienniku, aby użyć zapytania, takiego jak "AppServiceEnvironmentPlatformLogs | gdzie ResultDescription zawiera "rozpoczęto skalowanie" lub dowolny z nich. Ustaw odpowiednio próg. 
* Dodaj lub Utwórz grupę akcji zgodnie z potrzebami. Grupa akcji służy do definiowania odpowiedzi na alert, takiej jak wysyłanie wiadomości e-mail lub wiadomości SMS
* Nazwij swój alert i Zapisz go.

## <a name="internal-encryption"></a>Szyfrowanie wewnętrzne

App Service Environment działa jako system czarnego pudełka, w którym nie są widoczne wewnętrzne składniki lub komunikacja w systemie. Aby zapewnić większą przepływność, szyfrowanie nie jest domyślnie włączone między składnikami wewnętrznymi. System jest zabezpieczony, ponieważ ruch jest całkowicie niedostępny do monitorowania lub dostępu. Jeśli istnieje wymóg zgodności, który wymaga pełnego szyfrowania ścieżki danych od końca do końca szyfrowania, można włączyć tę funkcję w interfejsie użytkownika **konfiguracji** środowiska ASE.

![Włącz szyfrowanie wewnętrzne][5]

Spowoduje to zaszyfrowanie wewnętrznego ruchu sieciowego w środowisku ASE między frontonami i pracownikami, zaszyfrowanie pliku stronicowania, a także zaszyfrowanie dysków procesów roboczych. Po włączeniu InternalEncryption clusterSetting może mieć wpływ na wydajność systemu. Po wprowadzeniu zmiany w celu włączenia InternalEncryption środowisko ASE będzie w stanie niestabilnym, dopóki zmiana nie zostanie w pełni rozpropagowana. Zakończenie propagacji zmiany może potrwać kilka godzin, w zależności od liczby wystąpień w środowisku ASE. Zdecydowanie zalecamy, aby nie włączać tego na platformie ASE, gdy jest ona używana. Jeśli musisz włączyć tę funkcję w aktywnie używanym środowisku ASE, zdecydowanie zalecamy przekierowanie ruchu do środowiska kopii zapasowej do momentu zakończenia operacji.

## <a name="upgrade-preference"></a>Preferencja uaktualniania

Jeśli masz wiele środowisk ASE, możesz chcieć uaktualnić niektóre środowisk ASE przed innymi. W ramach obiektu **Menedżer zasobów ASE HostingEnvironment** można ustawić wartość dla **upgradePreference**. Ustawienie **upgradePreference** można skonfigurować przy użyciu szablonu, ARMClient lub https://resources.azure.com . Trzy możliwe wartości to:

- **Brak**: platforma Azure UAKTUALNI środowisko ASE bez określonej partii. Ta wartość jest domyślna.
- **Wczesne**: środowisko ASE zostanie uaktualnione w pierwszej połowie App Service uaktualnieniami.
- **Późne**: środowisko ASE zostanie uaktualnione w drugiej połowie App Service uaktualnień.

Aby skonfigurować preferencje uaktualniania, przejdź do interfejsu użytkownika **konfiguracji** środowiska ASE. 

Funkcja **upgradePreferences** jest najbardziej zrozumiała, gdy masz wiele środowisk aseów, ponieważ "wczesne" środowisk ASE zostanie uaktualnione przed "późnym" środowisk ASE. Jeśli masz wiele środowisk aseów, należy ustawić swoje programowanie i test środowisk ASE na "wczesne", a produkcyjny środowisk ASE "późno".

## <a name="delete-an-ase"></a>Usuwanie środowiska ASE

Aby usunąć środowisko ASE:

1. Wybierz pozycję **Usuń** w górnej części okienka **App Service Environment** .

1. Wprowadź nazwę środowiska ASE, aby potwierdzić, że chcesz go usunąć. Usunięcie środowiska ASE spowoduje również usunięcie całej jego zawartości.

    ![Usuwanie środowiska ASE][3]

1. Wybierz przycisk **OK**.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
