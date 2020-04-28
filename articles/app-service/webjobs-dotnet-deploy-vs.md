---
title: Tworzenie i wdrażanie webjobs przy użyciu usługi VS
description: Dowiedz się, jak tworzyć usługi Azure WebJobs w programie Visual Studio i wdrażać je w usłudze Azure App Service, w tym tworzyć zaplanowane zadania.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: d73c858bdbfee7c5b7c7e31b62b1f601b7b6838a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866435"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Develop and deploy WebJobs using Visual Studio - Azure App Service (Tworzenie i wdrażanie zadań WebJob za pomocą programu Visual Studio — usługa Azure App Service)

W tym artykule wyjaśniono, jak za pomocą programu Visual Studio wdrożyć projekt aplikacji konsoli do aplikacji sieci web w [usłudze App Service](overview.md) jako [azure webjob](https://go.microsoft.com/fwlink/?LinkId=390226). Aby uzyskać informacje dotyczące wdrażania funkcji WebJobs przy użyciu [portalu Azure,](https://portal.azure.com)zobacz [Uruchamianie zadań w tle za pomocą funkcji WebJobs](webjobs-create.md).

W jednej aplikacji sieci web można publikować wiele aplikacji WebJobs. Upewnij się, że każdy webjob w aplikacji sieci web ma unikatową nazwę.

Wersja 3.x [zestawu SDK usługi Azure WebJobs](webjobs-sdk-how-to.md) umożliwia tworzenie funkcji WebJobs, które są uruchamiane jako aplikacje .NET Core lub .NET Framework, podczas gdy wersja 2.x obsługuje tylko platformę .NET Framework. Sposób wdrażania projektu WebJobs jest inny dla projektów .NET Core w porównaniu z projektami .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs jako aplikacje konsoli .NET Core

Korzystając z wersji 3.x webjobs, można tworzyć i publikować aplikacje konsoli WebJobs jako aplikacje konsoli .NET Core. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia i publikowania aplikacji konsoli .NET Core na platformie Azure jako robota WebJob, zobacz [Wprowadzenie do sdk Azure WebJobs SDK do przetwarzania w tle opartego na zdarzeniach.](webjobs-sdk-get-started.md)

> [!NOTE]
> Program .NET Core WebJobs nie może być połączony z projektami sieci Web. Jeśli musisz wdrożyć webjob z aplikacją internetową, należy [utworzyć webjob jako aplikację konsoli .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Wdrażanie w usłudze Azure App Service

Publikowanie programu .NET Core WebJob w usłudze App Service w programie Visual Studio korzysta z tych samych narzędzi, co publikowanie aplikacji ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Typy dżlochu sieci Web

Domyślnie robota WebJob opublikowanego z projektu konsoli .NET Core jest uruchamiana tylko wtedy, gdy jest wyzwalana lub na żądanie. Można również zaktualizować projekt, aby [działał zgodnie z harmonogramem](#scheduled-execution) lub działał w sposób ciągły.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Zaplanowane wykonanie

Podczas publikowania aplikacji konsoli .NET Core na platformie Azure do projektu jest dodawany nowy plik *settings.job.* Ten plik służy do ustawiania harmonogramu wykonywania dla usługi WebJob. Aby uzyskać więcej informacji, zobacz [Planowanie wyzwalanego robota WebJob](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Ciągła realizacja

Za pomocą programu Visual Studio można zmienić usługę WebJob do ciągłego uruchamiania, gdy opcja Zawsze włączone jest włączona na platformie Azure.

1. Jeśli jeszcze tego nie zrobiono, [opublikuj projekt na platformie Azure](#deploy-to-azure-app-service).

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. Na karcie **Publikowanie** wybierz pozycję **Ustawienia**. 

1. W oknie dialogowym **Ustawienia profilu** wybierz pozycję **Ciągły** dla typu **programu WebJob**i wybierz pozycję **Zapisz**.

    ![Okno dialogowe Publikowania ustawień usługi WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Wybierz **pozycję Publikuj,** aby ponownie opublikować robotę WebJob ze zaktualizowanymi ustawieniami.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs jako aplikacje konsoli programu .NET Framework  

Gdy program Visual Studio wdraża projekt aplikacji konsoli .NET z obsługą programu WebJobs, kopiuje pliki środowiska uruchomieniowego do odpowiedniego folderu w aplikacji sieci web *(App_Data/jobs/continuous* dla ciągłych funkcji WebJobs i *App_Data/zadań/wyzwalanych* dla zaplanowanych lub na żądanie webjobs).

Projekt z obsługą webjobs ma dodane następujące elementy:

* Pakiet [NuGet firmy Microsoft.web.webJobs.Publish.](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/)
* Plik [webjob-publish-settings.json](#publishsettings) zawierający ustawienia wdrożenia i harmonogramu. 

![Diagram przedstawiający, co jest dodawane do aplikacji konsoli, aby włączyć wdrażanie jako WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Można dodać te elementy do istniejącego projektu aplikacji konsoli lub użyć szablonu, aby utworzyć nowy projekt aplikacji konsoli z obsługą konsoli z obsługą webjobs. 

Projekt można wdrożyć jako robota WebJob samodzielnie lub połączyć go z projektem sieci web, tak aby był on automatycznie wdrażany przy każdym wdrożeniu projektu sieci web. Aby połączyć projekty, program Visual Studio zawiera nazwę projektu obsługującego funkcje WebJobs w pliku [webjobs-list.json](#webjobslist) w projekcie sieci web.

![Diagram przedstawiający tworzenie łączenia projektu WebJob z projektem internetowym](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Wymagania wstępne

Jeśli używasz programu Visual Studio 2015, zainstaluj [zestaw SDK platformy Azure dla platformy .NET (Visual Studio 2015).](https://azure.microsoft.com/downloads/)

Jeśli używasz programu Visual Studio 2017, zainstaluj [obciążenie deweloperskie platformy Azure.](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>Włączanie wdrażania aplikacji WebJobs dla istniejącego projektu aplikacji konsoli

Dostępne są dwie opcje:

* [Włącz automatyczne wdrażanie za pomocą projektu sieci Web](#convertlink).

  Skonfiguruj istniejący projekt aplikacji konsoli, tak aby był automatycznie wdrażany jako usługa WebJob podczas wdrażania projektu sieci web. Użyj tej opcji, jeśli chcesz uruchomić webjob w tej samej aplikacji sieci web, w której uruchomisz powiązaną aplikację sieci web.

* [Włącz wdrażanie bez projektu sieci web](#convertnolink).

  Skonfiguruj istniejący projekt aplikacji konsoli do wdrożenia jako webjob samodzielnie, bez łącza do projektu sieci web. Użyj tej opcji, jeśli chcesz samodzielnie uruchomić robota WebJob w aplikacji sieci web, bez aplikacji sieci web uruchomionej w aplikacji sieci web. Można to zrobić, aby móc skalować zasoby WebJob niezależnie od zasobów aplikacji sieci web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Włączanie automatycznego wdrażania webjobs za pomocą projektu sieci Web

1. Kliknij prawym przyciskiem myszy projekt sieci Web w **Eksploratorze rozwiązań,** a następnie kliknij polecenie **Dodaj** > **istniejący projekt jako usługę Azure WebJob**.
   
    ![Istniejący projekt jako azure webjob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Zostanie wyświetlone okno dialogowe Dodawanie usługi [Azure WebJob.](#configure)
2. Na liście rozwijanej **Nazwa projektu** wybierz projekt aplikacji konsoli do dodania jako robota WebJob.
   
    ![Wybieranie projektu w oknie dialogowym Dodawanie usługi Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Wypełnij okno dialogowe [Dodawanie usługi Azure WebJob,](#configure) a następnie kliknij przycisk **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>Włączanie wdrażania aplikacji WebJobs bez projektu sieci Web
1. Kliknij prawym przyciskiem myszy projekt aplikacji konsoli w **Eksploratorze rozwiązań,** a następnie kliknij polecenie **Publikuj jako usługę Azure WebJob...**. 
   
    ![Publikowanie jako azure webjob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Zostanie wyświetlone okno dialogowe [Dodawanie usługi Azure WebJob](#configure) z zaznaczonym projektem w polu **Nazwa projektu.**
2. Wypełnij okno dialogowe [Dodawanie usługi Azure WebJob,](#configure) a następnie kliknij przycisk **OK**.
   
   Zostanie wyświetlony Kreator **publikowania w sieci Web.**  Jeśli nie chcesz publikować natychmiast, zamknij kreatora. Wprowadzone ustawienia są zapisywane, gdy chcesz [wdrożyć projekt](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Tworzenie nowego projektu z obsługą funkcji WebJobs
Aby utworzyć nowy projekt z obsługą webjobs, można użyć szablonu projektu aplikacji konsoli i włączyć wdrażanie webjobs, jak wyjaśniono w [poprzedniej sekcji](#convert). Alternatywnie można użyć szablonu nowego projektu WebJobs:

* [Użyj szablonu nowego projektu WebJobs dla niezależnego webjob](#createnolink)
  
    Utwórz projekt i skonfiguruj go do wdrożenia samodzielnie jako robota WebJob, bez łącza do projektu sieci web. Użyj tej opcji, jeśli chcesz samodzielnie uruchomić robota WebJob w aplikacji sieci web, bez aplikacji sieci web uruchomionej w aplikacji sieci web. Można to zrobić, aby móc skalować zasoby WebJob niezależnie od zasobów aplikacji sieci web.
* [Użyj szablonu nowego projektu WebJobs dla robota WebJob połączonego z projektem sieci Web](#createlink)
  
    Utwórz projekt, który jest skonfigurowany do automatycznego wdrażania jako webjob po wdrożeniu projektu sieci web w tym samym rozwiązaniu. Użyj tej opcji, jeśli chcesz uruchomić webjob w tej samej aplikacji sieci web, w której uruchomisz powiązaną aplikację sieci web.

> [!NOTE]
> Szablon nowego projektu WebJobs automatycznie instaluje pakiety NuGet i zawiera kod w *Program.cs* dla [pakietu WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Jeśli nie chcesz używać zestawie SDK webjobs, usuń `host.RunAndBlock` lub zmień instrukcję w *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>Użyj szablonu nowego projektu WebJobs dla niezależnego webjob
1. Kliknij **pozycję Plik** > **nowego projektu**, a następnie w oknie dialogowym Nowy **projekt** kliknij pozycję **Cloud** > **Azure WebJob (.NET Framework).**
   
    ![Okno dialogowe Nowy projekt z szablonem WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Postępuj zgodnie ze wskazówkami pokazanymi wcześniej, aby [projekt aplikacji konsoli był niezależnym projektem WebJobs](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>Użyj szablonu nowego projektu WebJobs dla robota WebJob połączonego z projektem sieci Web
1. Kliknij prawym przyciskiem myszy projekt sieci Web w **Eksploratorze rozwiązań,** a następnie kliknij polecenie **Dodaj** > **nowy projekt usługi Azure WebJob .**
   
    ![Nowy wpis menu projektu usługi WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Zostanie wyświetlone okno dialogowe Dodawanie usługi [Azure WebJob.](#configure)
2. Wypełnij okno dialogowe [Dodawanie usługi Azure WebJob,](#configure) a następnie kliknij przycisk **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Okno dialogowe Dodawanie usługi Azure WebJob
Okno dialogowe **Dodaj usługę Azure WebJob** umożliwia wprowadzenie nazwy webjob i ustawienie trybu uruchamiania dla usługi WebJob. 

![Okno dialogowe Dodawanie usługi Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Pola w tym oknie dialogowym odpowiadają polom w oknie dialogowym **Dodaj webjob** w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadań w tle za pomocą aplikacji WebJobs](webjobs-create.md).

> [!NOTE]
> * Aby uzyskać informacje na temat wdrażania wiersza polecenia, zobacz [Włączanie wiersza polecenia lub ciągłe dostarczanie funkcji Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Jeśli wdrożysz robotę WebJob, a następnie zdecydujesz, że chcesz zmienić typ webjob i ponownie wdrożyć, musisz usunąć plik *webjobs-publish-settings.json.* Spowoduje to, że program Visual Studio ponownie wyświetli opcje publikowania, dzięki czemu można zmienić typ programu WebJob.
> * Jeśli wdrożysz robota WebJob, a później zmienisz tryb uruchamiania z ciągłego na nie continuous lub vice versa, program Visual Studio utworzy nową robotę WebJob na platformie Azure po ponownej wdrażaniu. Jeśli zmienisz inne ustawienia planowania, ale pozostawisz tryb uruchamiania taki sam lub przełączysz się między zaplanowane i na żądanie, program Visual Studio aktualizuje istniejące zadanie zamiast tworzyć nowe.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
Podczas konfigurowania aplikacji konsoli dla wdrożenia webjobs, Visual Studio instaluje [pakiet Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet i przechowuje informacje o harmonogramie w pliku *webjob-publish-settings.json* w folderze *Właściwości* projektu projektu WebJobs. Oto przykład tego pliku:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Można edytować ten plik bezpośrednio, a program Visual Studio udostępnia intellisense. Schemat pliku jest przechowywany [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) w i można go tam przeglądać.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
Po połączeniu projektu z włączoną funkcją WebJobs z projektem sieci Web program Visual Studio *przechowuje* nazwę projektu WebJobs w pliku *webjobs-list.json* w folderze Właściwości projektu sieci web. Lista może zawierać wiele projektów WebJobs, jak pokazano w poniższym przykładzie:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Można edytować ten plik bezpośrednio, a program Visual Studio udostępnia intellisense. Schemat pliku jest przechowywany [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) w i można go tam przeglądać.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Wdrażanie projektu WebJobs
Projekt WebJobs, który został połączony z projektem sieci web wdraża się automatycznie z projektem sieci web. Aby uzyskać informacje na temat wdrażania projektu sieci Web, zobacz **instrukcje obsługi wdrażanie** > **aplikacji** w lewej nawigacji.

Aby samodzielnie wdrożyć projekt WebJobs, kliknij go prawym przyciskiem myszy w **Eksploratorze rozwiązań** i kliknij polecenie **Publikuj jako usługę Azure WebJob...**. 

![Publikowanie jako azure webjob](./media/webjobs-dotnet-deploy-vs/paw.png)

W przypadku niezależnego webjob pojawia się ten sam kreator **publikowania sieci Web,** który jest używany do projektów internetowych, ale z mniejszą liczbą ustawień dostępnych do zmiany.

## <a name="scheduling-a-triggered-webjob"></a>Planowanie wyzwalanego zadania WebJob

WebJobs używa pliku *settings.job* do określenia, kiedy jest uruchamiany plik WebJob. Ten plik służy do ustawiania harmonogramu wykonywania dla usługi WebJob. Poniższy przykład działa co godzinę od 9:00 do 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ten plik musi znajdować się w katalogu głównym folderu WebJobs, wraz ze `wwwroot\app_data\jobs\triggered\{job name}` `wwwroot\app_data\jobs\continuous\{job name}`skryptem webjob, takim jak lub . Podczas wdrażania programu WebJob z programu `settings.job` Visual Studio należy oznaczyć właściwości pliku jako **kopiowanie, jeśli jest ono nowsze.** 

Podczas [tworzenia webjob z witryny Azure portal](webjobs-create.md), plik settings.job jest tworzony dla Ciebie.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Wyrażenia CRON

WebJobs używa tych samych wyrażeń CRON do planowania jako wyzwalacza czasomierza w usłudze Azure Functions. Aby dowiedzieć się więcej o obsłudze cron, zobacz [artykuł referencyjny wyzwalacza czasomierza](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>settings.job, odwołanie

Następujące ustawienia są obsługiwane przez WebJobs:

| **Ustawienie** | **Typ**  | **Opis** |
| ----------- | --------- | --------------- |
| `is_in_place` | Wszystkie | Umożliwia uruchamianie zadania w miejscu bez uprzedniego skopiowania do folderu tymczasowego. Aby dowiedzieć się więcej, zobacz [Katalog roboczy WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Ciągłe | WebJobs należy uruchamiać tylko w jednym wystąpieniu, gdy jest skalowany w poziomie. Aby dowiedzieć się więcej, zobacz [Ustawianie zadania ciągłego jako pojedynczego](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Wywołany | Uruchom webjob na harmonogram oparty na cron. Aby dowiedzieć się więcej, zobacz [artykuł referencyjny wyzwalacza czasomierza](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Wszystkie | Umożliwia kontrolę zachowania zamknięcia systemu. Aby dowiedzieć się więcej, zobacz [Wdzięczne zamykanie](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o sdku WebJobs](webjobs-sdk-how-to.md)
