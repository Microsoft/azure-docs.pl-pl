---
title: Wdrażanie aplikacji w sposób predykcyjny przy użyciu platformy ARM
description: Dowiedz się, jak wdrożyć wiele aplikacji Azure App Service jako pojedynczą jednostkę i w sposób przewidywalny przy użyciu szablonów zarządzania zasobami platformy Azure i skryptów programu PowerShell.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 6c45d2da8658740b5e5e7e3dceb7478ea28d712c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962030"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Udostępnianie i wdrażanie mikrousług na platformie Azure
Ten samouczek pokazuje, jak zainicjować i wdrożyć aplikację składającą się z [mikrousług](https://en.wikipedia.org/wiki/Microservices) w [Azure App Service](https://azure.microsoft.com/services/app-service/) jako pojedynczą jednostkę i w sposób przewidywalny przy użyciu szablonów grup zasobów JSON i skryptów programu PowerShell. 

Podczas aprowizacji i wdrażania aplikacji o dużej skali, które składają się z wysoce rozłączonych mikrousług, powtarzalność i przewidywalność są decydujące dla sukcesu. [Azure App Service](https://azure.microsoft.com/services/app-service/) umożliwia tworzenie mikrousług obejmujących aplikacje sieci Web, zaplecza mobilne i aplikacje interfejsu API. [Azure Resource Manager](../azure-resource-manager/management/overview.md) umożliwia zarządzanie wszystkimi mikrousługami jako jednostką wraz z zależnościami zasobów, takimi jak baza danych i ustawienia kontroli źródła. Teraz możesz również wdrożyć taką aplikację przy użyciu szablonów JSON i prostego skryptu programu PowerShell. 

## <a name="what-you-will-do"></a>Co należy zrobić
W samouczku zostanie wdrożona aplikacja obejmująca następujące możliwości:

* Dwie App Service aplikacje (tj. dwie mikrousługi)
* SQL Database zaplecza
* Ustawienia aplikacji, parametry połączenia i kontrola źródła
* Application Insights, alerty, ustawienia skalowania automatycznego

## <a name="tools-you-will-use"></a>Narzędzia, które będą używane
W tym samouczku zostaną użyte następujące narzędzia. Ponieważ nie jest to obszerna dyskusja nad narzędziami, chcę przejść do kompleksowego scenariusza i po prostu przedstawić krótkie wprowadzenie do każdego z nich i gdzie można znaleźć więcej informacji na jego temat. 

### <a name="azure-resource-manager-templates-json"></a>Szablony Azure Resource Manager (JSON)
Za każdym razem, gdy tworzysz aplikację w Azure App Service, na przykład Azure Resource Manager używa szablonu JSON do tworzenia całej grupy zasobów z zasobami składników. Złożony szablon z [portalu Azure Marketplace](../marketplace/index.yml) może obejmować bazę danych, konta magazynu, plan App Service, samą aplikację, reguły alertów, ustawienia aplikacji, ustawienia automatycznego skalowania i wiele innych. wszystkie te szablony są dostępne dla użytkownika za pośrednictwem programu PowerShell. Aby uzyskać więcej informacji na temat szablonów Azure Resource Manager, zobacz [tworzenie Azure Resource Manager szablonów](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Zestaw Azure SDK 2,6 dla programu Visual Studio
Najnowszy zestaw SDK zawiera ulepszenia obsługi szablonów Menedżer zasobów w edytorze JSON. Za pomocą tej funkcji można szybko utworzyć szablon grupy zasobów od podstaw lub otworzyć istniejący szablon JSON (taki jak pobrany szablon galerii) do modyfikacji, wypełnić plik parametrów, a nawet wdrożyć grupę zasobów bezpośrednio z rozwiązania grupy zasobów platformy Azure.

Aby uzyskać więcej informacji, zobacz [zestaw Azure SDK 2,6 dla programu Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 lub nowszy
Począwszy od wersji 0.8.0, instalacja Azure PowerShell obejmuje moduł Azure Resource Manager oprócz modułu platformy Azure. Ten nowy moduł umożliwia tworzenie skryptów wdrożenia grup zasobów.

Aby uzyskać więcej informacji, zobacz [używanie Azure PowerShell z Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

### <a name="azure-resource-explorer"></a>Eksplorator zasobów Azure
To [Narzędzie do podglądu](https://resources.azure.com) umożliwia zapoznanie się z definicjami JSON wszystkich grup zasobów w ramach subskrypcji i poszczególnych zasobów. W narzędziu można edytować definicje JSON zasobu, usuwać całą hierarchię zasobów i tworzyć nowe zasoby.  Informacje, które są łatwo dostępne w tym narzędziu, są bardzo przydatne w przypadku tworzenia szablonów, ponieważ pokazują, jakie właściwości należy ustawić dla określonego typu zasobu, prawidłowych wartości itd. Możesz nawet utworzyć grupę zasobów w [witrynie Azure Portal](https://portal.azure.com/), a następnie sprawdzić jej definicje JSON w narzędziu Eksplorator, aby ułatwić templatize grupy zasobów.

### <a name="deploy-to-azure-button"></a>Przycisk Wdróż na platformie Azure
Jeśli używasz usługi GitHub do kontroli źródła, możesz umieścić [przycisk Wdróż na platformie Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) w pliku Readme. MD, który umożliwia interfejs użytkownika wdrożenia z kluczem na platformie Azure. Można to zrobić dla dowolnej prostej aplikacji, która pozwala na wdrożenie całej grupy zasobów przez umieszczenie azuredeploy.jsw pliku w katalogu głównym repozytorium. Ten plik JSON, który zawiera szablon grupy zasobów, będzie używany przez przycisk Wdróż na platformie Azure w celu utworzenia grupy zasobów. Aby zapoznać się z przykładem, zobacz [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) przykład, który będzie używany w tym samouczku.

## <a name="get-the-sample-resource-group-template"></a>Pobierz szablon przykładowej grupy zasobów
Teraz przyjrzyjmy się do niego.

1. Przejdź do przykładu App Service [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) .
2. W readme.md kliknij pozycję **Wdróż na platformie Azure**.
3. Nastąpi przekierowanie do witryny [wdrożenia na platformę Azure](https://deploy.azure.com) i poproszenie o wprowadzenie parametrów wdrożenia. Należy zauważyć, że większość pól jest wypełniana nazwą repozytorium i kilka losowych ciągów. Możesz zmienić wszystkie pola, jeśli chcesz, ale jedyną czynnością, którą trzeba wprowadzić, są SQL Server logowania administracyjnego i hasło, a następnie kliknij przycisk **dalej**.
   
   ![Przedstawia parametry wdrożenia wejściowego w lokacji wdrożenia na platformie Azure.](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Następnie kliknij przycisk **Wdróż** , aby rozpocząć proces wdrażania. Po zakończeniu działania procesu, kliknij http://todoapp link *XXXX*. azurewebsites.NET, aby przejrzeć wdrożoną aplikację. 
   
   ![Pokazuje proces wdrażania aplikacji.](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Interfejs użytkownika może działać nieco wolniej po pierwszym przejściu do niego, ponieważ aplikacje są właśnie uruchamiane, ale przekonasz się, że jest to w pełni funkcjonalna aplikacja.
5. Na stronie Wdrażanie kliknij link **Zarządzaj** , aby wyświetlić nową aplikację w witrynie Azure Portal.
6. Na liście rozwijanej **podstawowe** kliknij link grupy zasobów. Należy również pamiętać, że aplikacja jest już połączona z repozytorium GitHub w ramach **projektu zewnętrznego**. 
   
   ![Pokazuje link grupy zasobów w sekcji listy rozwijanej podstawowe informacje.](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. W bloku Grupa zasobów Zwróć uwagę, że w grupie zasobów znajdują się już dwie aplikacje i jeden SQL Database.
   
   ![Pokazuje zasoby dostępne w grupie zasobów.](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Wszystko, co zostało już przeprowadzone w ciągu kilku krótkich minut, to w pełni wdrożona aplikacja dwuletnia ze wszystkimi składnikami, zależnościami, ustawieniami, bazą danych i publikowaniem ciągłym, skonfigurowanym przez zautomatyzowaną organizację w Azure Resource Manager. Wszystkie te czynności zostały wykonane przez dwie rzeczy:

* Przycisk Wdróż na platformie Azure
* azuredeploy.jsw katalogu głównym repozytorium

Te same dziesiątki, setki lub tysiące czasu można wdrożyć za każdym razem. Powtarzalność i przewidywalność tego podejścia umożliwiają łatwe i bezpieczne wdrażanie aplikacji o dużej skali.

## <a name="examine-or-edit-azuredeployjson"></a>Badaj (lub Edytuj) AZUREDEPLOY.JSna
Teraz przyjrzyjmy się konfigurowaniu repozytorium GitHub. Będziesz używać edytora JSON w zestawie SDK platformy Azure, więc jeśli jeszcze nie zainstalowano [zestawu Azure .NET sdk 2,6](https://azure.microsoft.com/downloads/), zrób to teraz.

1. Sklonuj repozytorium [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) przy użyciu ulubionego narzędzia Git. Na poniższym zrzucie ekranu robię to w Team Explorer w Visual Studio 2013.
   
   ![Pokazuje, jak używać narzędzia Git do klonowania repozytorium ToDoApp.](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. W katalogu głównym repozytorium Otwórz azuredeploy.jsw programie Visual Studio. Jeśli nie widzisz okienka konspektu JSON, musisz zainstalować zestaw Azure .NET SDK.
   
   ![Pokazuje okienko konspektu JSON w programie Visual Studio.](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nie mogę opisać wszystkich szczegółów w formacie JSON, ale sekcja [więcej zasobów](#resources) zawiera linki do uczenia się w języku szablonów grupy zasobów. W tym miejscu chcę pokazać interesujące funkcje, które mogą pomóc Ci rozpocząć tworzenie własnego szablonu niestandardowego dla wdrożenia aplikacji.

### <a name="parameters"></a>Parametry
Zapoznaj się z sekcją parametrów, aby zobaczyć, że w przypadku większości tych parametrów przycisk **Wdróż na platformie Azure** monituje o wprowadzenie. Witryna za przyciskiem **Wdróż na platformie Azure** wypełnia wejściowy interfejs użytkownika przy użyciu parametrów zdefiniowanych w azuredeploy.json. Te parametry są używane w ramach definicji zasobów, takich jak nazwy zasobów, wartości właściwości itp.

### <a name="resources"></a>Zasoby
W węźle zasoby można zobaczyć, że są zdefiniowane 4 zasoby najwyższego poziomu, w tym wystąpienie SQL Server, plan App Service i dwie aplikacje. 

#### <a name="app-service-plan"></a>Plan usługi App Service
Zacznijmy od prostego zasobu poziomu głównego w formacie JSON. W konspekcie JSON kliknij plan App Service o nazwie **[hostingPlanName]** , aby wyróżnić odpowiedni kod JSON. 

![Pokazuje sekcję [hostingPlanName] kodu JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Należy zauważyć, że `type` element określa ciąg dla planu App Service (jest on nazywany farmą serwerów o długim, długim czasie temu), a inne elementy i właściwości są wypełniane przy użyciu parametrów zdefiniowanych w pliku JSON, a ten zasób nie zawiera żadnych zasobów zagnieżdżonych.

> [!NOTE]
> Należy zauważyć, że wartość `apiVersion` instruuje platformę Azure, która wersja interfejsu API REST ma używać definicji zasobu JSON z i może mieć wpływ na sposób formatowania zasobu wewnątrz `{}` . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Następnie kliknij zasób SQL Server o nazwie **SqlServer** w konspekcie JSON.

![Przedstawia zasób SQL Server o nazwie SQLServer w konspekcie JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Zwróć uwagę na następujące informacje o wyróżnionym kodzie JSON:

* Użycie parametrów zapewnia, że utworzone zasoby mają nazwy i są skonfigurowane w taki sposób, aby były spójne ze sobą.
* Zasób programu SQLServer ma dwa zagnieżdżone zasoby, z których każdy ma inną wartość `type` .
* Zagnieżdżone zasoby wewnątrz `“resources”: […]` , w których zdefiniowano bazę danych i reguły zapory, mają `dependsOn` element określający identyfikator zasobu na poziomie głównym zasobu SqlServer. Oznacza to, że Azure Resource Manager, "przed utworzeniem tego zasobu, ten zasób musi już istnieć. a jeśli inny zasób jest zdefiniowany w szablonie, utwórz go najpierw.
  
  > [!NOTE]
  > Aby uzyskać szczegółowe informacje na temat korzystania z `resourceId()` funkcji, zobacz [Azure Resource Manager Template Functions](../azure-resource-manager/templates/template-functions-resource.md#resourceid).
  > 
  > 
* Efektem `dependsOn` elementu jest to, że Azure Resource Manager mogą wiedzieć, które zasoby mogą być tworzone równolegle i jakie zasoby muszą być tworzone sekwencyjnie. 

#### <a name="app-service-app"></a>Aplikacja usługi App Service
Teraz przechodźmy na same rzeczywiste aplikacje, które są bardziej skomplikowane. Kliknij aplikację [zmienne (apiSiteName ')] w konspekcie JSON, aby zaznaczyć swój kod JSON. Zauważysz, że rzeczy są znacznie bardziej interesujące. W tym celu porozmawiamy o tych funkcjach po jednym:

##### <a name="root-resource"></a>Zasób główny
Aplikacja zależy od dwóch różnych zasobów. Oznacza to, że Azure Resource Manager utworzy aplikację dopiero po utworzeniu zarówno planu App Service, jak i wystąpienia SQL Server.

![Pokazuje zależności aplikacji w planie App Service i wystąpieniu SQL Server.](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Ustawienia aplikacji
Ustawienia aplikacji są również zdefiniowane jako zasób zagnieżdżony.

![Pokazuje ustawienia aplikacji zdefiniowane jako zasób zagnieżdżony w kodzie JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

W `properties` elemencie dla programu `config/appsettings` masz dwa ustawienia aplikacji w formacie `"<name>" : "<value>"` .

* `PROJECT` to [ustawienie KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , które mówi wdrożenia platformy Azure, który projekt ma być używany w rozwiązaniu z programem Visual Studio. Pokażę później sposób skonfigurowania kontroli źródła, ale ponieważ kod ToDoApp znajduje się w wieloprojektowym rozwiązaniu Visual Studio, to ustawienie jest wymagane.
* `clientUrl` jest po prostu ustawieniem aplikacji, którego używa kod aplikacji.

##### <a name="connection-strings"></a>Parametry połączeń
Parametry połączenia są również zdefiniowane jako zasób zagnieżdżony.

![Pokazuje, w jaki sposób parametry połączenia są definiowane jako zasób zagnieżdżony w kodzie JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

W `properties` elemencie dla `config/connectionstrings` każdego ciągu połączenia jest również zdefiniowana jako para Name: Value z określonym formatem `"<name>" : {"value": "…", "type": "…"}` . Dla `type` elementu, możliwe wartości to `MySql` ,, `SQLServer` `SQLAzure` , i `Custom` .

> [!TIP]
> Aby uzyskać ostateczną listę typów parametrów połączenia, uruchom następujące polecenie w Azure PowerShell: \[ enum]:: GetNames ("Microsoft. windowsazure. Commands. Utilities. Web. Services. webentities. DatabaseType")
> 
> 

##### <a name="source-control"></a>Kontrola źródła
Ustawienia kontroli źródła są również zdefiniowane jako zasób zagnieżdżony. Azure Resource Manager używa tego zasobu do konfigurowania ciągłego publikowania (zobacz zastrzeżenie na `IsManualIntegration` później), a także do uruchamiania automatycznego wdrażania kodu aplikacji podczas przetwarzania pliku JSON.

![Pokazuje, w jaki sposób ustawienia kontroli źródła są definiowane jako zasób zagnieżdżony w kodzie JSON.](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl``branch`powinien być całkiem intuicyjny i powinien wskazywać repozytorium git i nazwę gałęzi, z której ma zostać opublikowany. Są one definiowane przez parametry wejściowe. 

Zwróć uwagę na `dependsOn` element, który oprócz samego zasobu aplikacji `sourcecontrols/web` zależy również od `config/appsettings` i `config/connectionstrings` . Jest to spowodowane tym `sourcecontrols/web` , że po skonfigurowaniu tego procesu proces wdrażania platformy Azure automatycznie podejmie próbę wdrożenia, skompilowania i uruchomienia kodu aplikacji. W związku z tym Wstawianie tej zależności pomaga upewnić się, że aplikacja ma dostęp do wymaganych ustawień aplikacji i parametrów połączenia przed uruchomieniem kodu aplikacji. 

> [!NOTE]
> Należy również pamiętać, że `IsManualIntegration` jest ustawiony na `true` . Ta właściwość jest niezbędna w tym samouczku, ponieważ nie jest ona własnością repozytorium GitHub i w związku z tym nie może udzielić uprawnienia do platformy Azure w celu skonfigurowania ciągłego publikowania z [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) (np. wypychania automatycznych aktualizacji repozytorium na platformę Azure). Wartości domyślnej `false` dla określonego repozytorium można użyć tylko w przypadku skonfigurowania poświadczeń usługi GitHub właściciela w [Azure Portal](https://portal.azure.com/) przed. Innymi słowy, jeśli wcześniej skonfigurowano kontrolę źródła w serwisie GitHub lub BitBucket dla dowolnej aplikacji w [witrynie Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń użytkownika, platforma Azure zapamiętaje poświadczenia i użyje ich za każdym razem, gdy wdrożono dowolną aplikację z serwisu GitHub lub BitBucket w przyszłości. Jeśli jednak jeszcze tego nie zrobiono, wdrożenie szablonu JSON zakończy się niepowodzeniem, gdy Azure Resource Manager próbuje skonfigurować ustawienia kontroli źródła aplikacji, ponieważ nie może zalogować się do usługi GitHub lub BitBucket przy użyciu poświadczeń właściciela repozytorium.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Porównanie szablonu JSON ze wdrożoną grupą zasobów
W tym miejscu możesz przejść przez wszystkie bloki aplikacji w [witrynie Azure Portal](https://portal.azure.com/), ale istnieje inne narzędzie, które jest równie użyteczne. Przejdź do narzędzia [Azure Resource Explorer](https://resources.azure.com) Preview, które zapewnia reprezentację w formacie JSON wszystkich grup zasobów w Twoich subskrypcjach, ponieważ rzeczywiście istnieją w zapleczu platformy Azure. Możesz również zobaczyć, jak hierarchia JSON grupy zasobów na platformie Azure odpowiada hierarchii w pliku szablonu, który jest używany do jego tworzenia.

Na przykład po przejściu do narzędzia [Azure Resource Explorer](https://resources.azure.com) i rozszerzeniu węzłów w Eksploratorze można zobaczyć grupę zasobów i zasoby poziomu głównego, które są zbierane w ramach odpowiednich typów zasobów.

![Wyświetl grupę zasobów i zasoby na poziomie głównym w rozwiniętym narzędziu Eksplorator zasobów platformy Azure.](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Jeśli przejdziesz do szczegółów aplikacji, powinno być możliwe wyświetlenie szczegółów konfiguracji aplikacji podobnej do poniższego zrzutu ekranu:

![Przejdź do szczegółów, aby wyświetlić szczegóły konfiguracji w aplikacji.](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Ponownie zagnieżdżone zasoby powinny mieć hierarchię podobną do tych w pliku szablonu JSON i powinny być widoczne ustawienia aplikacji, parametry połączenia itp., odpowiednio odzwierciedlone w okienku JSON. Brak ustawień w tym miejscu może wskazywać na problem z plikiem JSON i może pomóc w rozwiązywaniu problemów z plikiem szablonu JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Wdróż samodzielnie szablon grupy zasobów
Przycisk **Wdróż na platformie Azure** jest świetny, ale umożliwia wdrożenie szablonu grupy zasobów w azuredeploy.jstylko wtedy, gdy została już wypchnięci azuredeploy.jsw usłudze GitHub. Zestaw Azure .NET SDK udostępnia także narzędzia do wdrażania dowolnego pliku szablonu JSON bezpośrednio z komputera lokalnego. W tym celu wykonaj poniższe kroki:

1. W programie Visual Studio kliknij pozycję **plik**  >  **Nowy**  >  **projekt**.
2. Kliknij **Visual C#** pozycję  >  **Cloud**  >  **Grupa zasobów platformy Azure**w chmurze Visual C#, a następnie kliknij przycisk **OK**.
   
   ![Utwórz nowy projekt jako grupę zasobów platformy Azure w zestawie SDK platformy Azure.](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. W obszarze **Wybierz szablon platformy Azure**wybierz pozycję **pusty szablon** i kliknij przycisk **OK**.
4. Przeciągnij azuredeploy.jsdo folderu **Template** w nowym projekcie.
   
   ![Pokazuje wynik przeciągnięcia azuredeploy.jsw pliku do folderu szablonów projektu.](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. W obszarze Eksplorator rozwiązań otwórz skopiowane azuredeploy.jsna stronie.
6. Na przykład w celu dodania do pliku JSON standardowych zasobów usługi Application Insights, klikając pozycję **Dodaj zasób**. Jeśli interesuje Cię wdrażanie pliku JSON, przejdź do kroku Wdróż.
   
   ![Pokazuje przycisk Dodaj zasób, który służy do dodawania standardowych zasobów usługi Application Insight do pliku JSON.](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Wybierz pozycję **Application Insights dla Web Apps**, upewnij się, że zaznaczono istniejący plan App Service i aplikację, a następnie kliknij przycisk **Dodaj**.
   
   ![Pokazuje wybór Application Insights dla Web Apps, nazwy, planu App Service i aplikacji sieci Web.](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Teraz będzie można zobaczyć kilka nowych zasobów, w zależności od zasobów i ich działania, w zależności od planu App Service lub aplikacji. Te zasoby nie są włączane w istniejącej definicji i można je zmienić.
   
   ![Wyświetl nowe zasoby, które mają zależności od planu App Service lub aplikacji.](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. W konspekcie JSON kliknij pozycję **AppInsights automatyczne skalowanie** , aby zaznaczyć swój kod JSON. Jest to ustawienie skalowania dla planu App Service.
9. W wyróżnionym kodzie JSON Znajdź `location` `enabled` właściwości i ustaw je, jak pokazano poniżej.
   
   ![Pokazuje lokalizację i włączone właściwości w kodzie JSON skalowania automatycznego appInsights oraz wartości, które należy ustawić.](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. W konspekcie JSON kliknij pozycję **CPUHigh appInsights** , aby zaznaczyć swój kod JSON. Jest to alert.
11. Znajdź `location` właściwości i `isEnabled` i ustaw je, jak pokazano poniżej. Wykonaj te same inne trzy alerty (purpurowe cebule).
    
    ![Pokazuje lokalizację i właściwości isEnabled w kodzie JSON CPUHigh appInsights i wartości, które powinny być ustawiane na.](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Teraz można przystąpić do wdrożenia. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Wdróż**  >  **nowe wdrożenie**.
    
    ![Pokazuje, jak wdrożyć nowy projekt.](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Zaloguj się do konta platformy Azure, jeśli jeszcze tego nie zrobiono.
14. Wybierz istniejącą grupę zasobów w ramach subskrypcji lub Utwórz nową, wybierz pozycję **azuredeploy.jsna**, a następnie kliknij przycisk **Edytuj parametry**.
    
    ![Pokazuje, w jaki sposób edytować parametry w azuredeploy.jspliku.](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Teraz będzie można edytować wszystkie parametry zdefiniowane w pliku szablonu w tabeli całkiem. Parametry, które definiują ustawienia domyślne, mają już wartości domyślne, a parametry, które definiują listę dozwolonych wartości, będą wyświetlane jako listy rozwijane.
    
    ![Przedstawia parametry, które definiują listę dozwolonych wartości jako listy rozwijane.](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Wypełnij wszystkie puste parametry i Użyj [adresu repozytorium GitHub do ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) **w ramach ponownego wypełniania**. Następnie kliknij przycisk **Zapisz**.
    
    ![Pokazuje nowo wypełnione parametry azuredeploy.jspliku.](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Skalowanie automatyczne to funkcja oferowana w warstwie **standardowa** lub wyższa, a alerty na poziomie planu są oferowane w warstwie **podstawowa** lub wyższej, należy ustawić parametr **SKU** na **Standard** lub **Premium** , aby wyświetlić wszystkie nowe zasoby usługi App Insights.
    > 
    > 
16. Kliknij pozycję **Wdróż**. Jeśli zaznaczono opcję **Zapisz hasła**, hasło zostanie zapisane w pliku parametrów **w postaci zwykłego tekstu**. W przeciwnym razie podczas procesu wdrażania zostanie wyświetlony monit o podanie hasła do bazy danych.

Gotowe. Teraz wystarczy przejść do witryny [Azure Portal](https://portal.azure.com/) i narzędzia [Azure Resource Explorer](https://resources.azure.com) , aby wyświetlić nowe alerty i ustawienia skalowania automatycznego dodane do aplikacji wdrożonej w formacie JSON.

Kroki opisane w tej sekcji zostały wykonane głównie w następujący sposób:

1. Przygotowany plik szablonu
2. Utworzono plik parametrów, aby przejść do pliku szablonu
3. Wdrożono plik szablonu z plikiem parametrów

Ostatni krok jest łatwo wykonywany przez polecenie cmdlet programu PowerShell. Aby zobaczyć, co program Visual Studio miał podczas wdrażania aplikacji, Otwórz Scripts\Deploy-AzureResourceGroup.ps1. W tym miejscu istnieje dużo kodu, ale po prostu wyróżnienie całego kodu potrzebnego do wdrożenia pliku szablonu za pomocą pliku parametrów.

![Pokazuje odpowiedni kod w skrypcie, którego potrzebujesz użyć do wdrożenia pliku szablonu z plikiem parametrów.](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Ostatnie polecenie cmdlet, `New-AzureResourceGroup` , jest tym, które faktycznie wykonuje akcję. Wszystko to powinno zademonstrować, że za pomocą narzędzi można w sposób nieprzewidywalny wdrożyć aplikację w chmurze. Za każdym razem, gdy uruchamiasz polecenie cmdlet w tym samym szablonie z tym samym plikiem parametrów, uzyskasz ten sam wynik.

## <a name="summary"></a>Podsumowanie
W DevOps, powtarzalność i przewidywalność to klucze do każdego pomyślnego wdrożenia aplikacji na dużą skalę składającej się z mikrousług. W tym samouczku wdrożono aplikację dwuczęściową na platformie Azure jako pojedynczą grupę zasobów przy użyciu szablonu Azure Resource Manager. Miejmy nadzieję, że posiadasz wiedzę, której potrzebujesz, aby rozpocząć konwersję aplikacji na platformę Azure do szablonu i można ją przewidzieć i wdrożyć. 

<a name="resources"></a>

## <a name="more-resources"></a>Dodatkowe zasoby
* [Język szablonu Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Funkcje szablonu Azure Resource Manager](../azure-resource-manager/templates/template-functions.md)
* [Wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Używanie programu Azure PowerShell z usługą Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)
* [Rozwiązywanie problemów z wdrożeniami grup zasobów na platformie Azure](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o składni i właściwościach JSON dla typów zasobów wdrożonych w tym artykule, zobacz:

* [Microsoft. SQL/serwery](/azure/templates/microsoft.sql/servers)
* [Microsoft. SQL/serwery/bazy danych](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft. SQL/serwery/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft. Web/dopuszczalna](/azure/templates/microsoft.web/serverfarms)
* [Microsoft. Web/witryny](/azure/templates/microsoft.web/sites)
* [Microsoft. Web/Sites/miejsca](/azure/templates/microsoft.web/sites/slots)
* [Microsoft. Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)