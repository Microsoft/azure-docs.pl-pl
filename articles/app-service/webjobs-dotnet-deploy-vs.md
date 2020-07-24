---
title: Opracowywanie i wdrażanie zadań WebJob przy użyciu programu VS
description: Dowiedz się, jak opracowywać Azure WebJobs w programie Visual Studio i wdrażać je w Azure App Service, w tym o tworzeniu zaplanowanego zadania.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: cf2437443350cf9b21b6abc0d0dd1dbd5d22e41f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083195"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Develop and deploy WebJobs using Visual Studio - Azure App Service (Tworzenie i wdrażanie zadań WebJob za pomocą programu Visual Studio — usługa Azure App Service)

W tym artykule wyjaśniono, jak używać programu Visual Studio do wdrażania projektu aplikacji konsolowej w aplikacji sieci Web w [App Service](overview.md) jako [Zadania WebJob platformy Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Aby uzyskać informacje na temat sposobu wdrażania zadań WebJob przy użyciu [Azure Portal](https://portal.azure.com), zobacz [Uruchamianie zadań w tle za pomocą zadań WebJob](webjobs-create.md).

Można opublikować wiele zadań WebJob w pojedynczej aplikacji sieci Web. Upewnij się, że każde zadanie WebJob w aplikacji sieci Web ma unikatową nazwę.

Wersja 3. x [zestawu SDK Azure WebJobs](webjobs-sdk-how-to.md) umożliwia tworzenie zadań WebJob, które są uruchamiane jako aplikacje .NET Core lub aplikacje .NET Framework, natomiast wersja 2. x obsługuje tylko .NET Framework. Sposób wdrażania projektu zadań WebJob jest różny dla projektów .NET Core, a .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>Zadania WebJob jako aplikacje konsolowe platformy .NET Core

W przypadku korzystania z wersji 3. x zadań WebJob można tworzyć i publikować Zadania WebJob jako aplikacje konsolowe platformy .NET Core. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia i publikowania aplikacji konsolowej .NET Core na platformie Azure jako zadania WebJob, zobacz [wprowadzenie do zestawu SDK Azure WebJobs na potrzeby przetwarzania w tle opartego na zdarzeniach](webjobs-sdk-get-started.md).

> [!NOTE]
> Zadań WebJob programu .NET Core nie można łączyć z projektami sieci Web. Jeśli musisz wdrożyć zadanie WebJob za pomocą aplikacji sieci Web, [Utwórz zadanie WebJob jako aplikację konsolową .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Wdrażanie w usłudze Azure App Service

Publikowanie Zadania WebJob programu .NET Core w celu App Service z programu Visual Studio używa tych samych narzędzi, co publikowanie aplikacji ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Typy zadań WebJob

Domyślnie zadanie WebJob publikowane z poziomu projektu konsoli .NET Core jest uruchamiane tylko w przypadku wyzwolenia lub na żądanie. Możesz również zaktualizować projekt do [uruchamiania zgodnie z harmonogramem](#scheduled-execution) lub działać w sposób ciągły.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Zaplanowane wykonanie

Po opublikowaniu aplikacji konsolowej .NET Core na platformie Azure nowe *Ustawienia* są dodawane do projektu. Ten plik służy do ustawiania harmonogramu wykonywania zadania WebJob. Aby uzyskać więcej informacji, zobacz [Planowanie wyzwalanego Zadania WebJob](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Ciągłe wykonywanie

Możesz użyć programu Visual Studio, aby zmienić zadanie WebJob tak, aby działało stale, gdy jest włączone na platformie Azure.

1. Jeśli jeszcze tego nie zrobiono, [Opublikuj projekt na platformie Azure](#deploy-to-azure-app-service).

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. Na karcie **Publikowanie** wybierz pozycję **Ustawienia**. 

1. W oknie dialogowym **Ustawienia profilu** wybierz pozycję **ciągłe** dla **typu zadania WebJob**, a następnie wybierz pozycję **Zapisz**.

    ![Okno dialogowe ustawień publikowania dla zadania WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Wybierz pozycję **Publikuj** , aby ponownie opublikować zadanie WebJob ze zaktualizowanymi ustawieniami.

## <a name="webjobs-as-net-framework-console-apps"></a>Zadania WebJob jako .NET Framework aplikacje konsolowe  

Gdy program Visual Studio wdroży projekt aplikacji konsolowej z włączoną obsługą zadań WebJob .NET Framework, kopiuje pliki środowiska uruchomieniowego do odpowiedniego folderu w aplikacji sieci Web (*App_Data/Jobs/Continuous* dla ciągłych zadań webjob i *App_Data/Jobs/Triggered* na potrzeby zadań WebJob zaplanowanych lub na żądanie).

Projekt z włączoną obsługą zadań WebJob ma dodane następujące elementy:

* Pakiet NuGet [Microsoft. Web. WebJobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* [webjob-publish-settings.js](#publishsettings) pliku, który zawiera ustawienia wdrożenia i harmonogramu. 

![Diagram przedstawiający elementy dodawane do aplikacji konsolowej w celu włączenia wdrożenia jako zadania WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Można dodać te elementy do istniejącego projektu aplikacji konsolowej lub użyć szablonu, aby utworzyć nowy projekt aplikacji konsolowej z włączoną obsługą zadań WebJob. 

Projekt można wdrożyć jako zadanie WebJob lub połączyć go z projektem sieci Web, dzięki czemu zostanie on automatycznie wdrożony po wdrożeniu projektu sieci Web. Aby połączyć projekty, program Visual Studio zawiera nazwę projektu z obsługą zadań WebJob w [webjobs-list.js](#webjobslist) w pliku projektu sieci Web.

![Diagram przedstawiający łączenie projektu WebJob z projektem sieci Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Wymagania wstępne

Jeśli używasz programu Visual Studio 2015, zainstaluj [zestaw Azure SDK dla programu .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Jeśli używasz programu Visual Studio 2017, zainstaluj [obciążenie Programowanie na platformie Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>Włącz wdrażanie zadań WebJob dla istniejącego projektu aplikacji konsolowej

Dostępne są dwie opcje:

* [Włącz automatyczne wdrażanie przy użyciu projektu sieci Web](#convertlink).

  Skonfiguruj istniejący projekt aplikacji konsolowej, tak aby był automatycznie wdrażany jako zadanie WebJob podczas wdrażania projektu sieci Web. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w tej samej aplikacji sieci Web, w której uruchomiono powiązaną aplikację sieci Web.

* [Włącz wdrożenie bez projektu sieci Web](#convertnolink).

  Skonfiguruj istniejący projekt aplikacji konsolowej do wdrożenia jako zadanie WebJob, bez linku do projektu sieci Web. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w aplikacji sieci Web, korzystając z aplikacji sieci Web działającej w aplikacji sieci Web. Można to zrobić, aby móc skalować zasoby WebJob niezależnie od zasobów aplikacji sieci Web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Włącz automatyczne wdrażanie zadań WebJob przy użyciu projektu sieci Web

1. Kliknij prawym przyciskiem myszy projekt sieci Web w **Eksplorator rozwiązań**, a następnie kliknij pozycję **Dodaj**  >  **istniejący projekt jako zadanie WebJob platformy Azure**.
   
    ![Istniejący projekt jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Zostanie wyświetlone okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) .
2. Z listy rozwijanej **Nazwa projektu** wybierz projekt aplikacji konsolowej, który ma zostać dodany jako zadanie WebJob.
   
    ![Wybieranie projektu w oknie dialogowym Dodawanie zadania WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Wypełnij okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) , a następnie kliknij przycisk **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>Włącz wdrażanie zadań WebJob bez projektu sieci Web
1. Kliknij prawym przyciskiem myszy projekt Aplikacja konsolowa w **Eksplorator rozwiązań**, a następnie kliknij pozycję **Opublikuj jako zadanie WebJob platformy Azure.** 
   
    ![Publikuj jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Zostanie wyświetlone okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) z projektem wybranym w polu **Nazwa projektu** .
2. Wypełnij okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) , a następnie kliknij przycisk **OK**.
   
   Zostanie wyświetlony Kreator **publikowania w sieci Web** .  Jeśli nie chcesz publikować natychmiast, zamknij kreatora. Wprowadzone ustawienia są zapisywane w przypadku, gdy chcesz [wdrożyć projekt](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Utwórz nowy projekt z obsługą zadań WebJob
Aby utworzyć nowy projekt z obsługą zadań WebJob, można użyć szablonu projektu aplikacji konsolowej i włączyć wdrożenie zadań WebJob zgodnie z opisem w [poprzedniej sekcji](#convert). Alternatywnie można użyć szablonu Nowy projekt Zadania WebJob:

* [Użycie szablonu New-Project nowego projektu dla niezależnego Zadania WebJob](#createnolink)
  
    Utwórz projekt i skonfiguruj go do wdrożenia jako zadanie WebJob, bez linku do projektu sieci Web. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w aplikacji sieci Web, korzystając z aplikacji sieci Web działającej w aplikacji sieci Web. Można to zrobić, aby móc skalować zasoby WebJob niezależnie od zasobów aplikacji sieci Web.
* [Użyj szablonu New-Project nowego projektu dla zadania WebJob połączonego z projektem sieci Web](#createlink)
  
    Utwórz projekt, który jest skonfigurowany do automatycznego wdrażania jako zadanie WebJob, gdy zostanie wdrożony projekt sieci Web w tym samym rozwiązaniu. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w tej samej aplikacji sieci Web, w której uruchomiono powiązaną aplikację sieci Web.

> [!NOTE]
> Szablon nowy projekt Zadania WebJob automatycznie instaluje pakiety NuGet i zawiera kod w *program.cs* dla [zestawu SDK usługi WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Jeśli nie chcesz używać zestawu SDK zadań WebJob, Usuń lub Zmień `host.RunAndBlock` instrukcję w *program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>Użycie szablonu New-Project nowego projektu dla niezależnego Zadania WebJob
1. Kliknij pozycję **plik**  >  **Nowy projekt**, a następnie w oknie dialogowym **Nowy projekt** kliknij pozycję **Cloud**  >  **zadanie WebJob platformy Azure w chmurze (.NET Framework)**.
   
    ![Okno dialogowe Nowy projekt przedstawiające Szablon Zadania WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Postępuj zgodnie ze wskazówkami podanymi wcześniej, aby [uczynić aplikację konsolową projektu niezależnego Zadania WebJob](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>Użyj szablonu New-Project nowego projektu dla zadania WebJob połączonego z projektem sieci Web
1. Kliknij prawym przyciskiem myszy projekt sieci Web w **Eksplorator rozwiązań**, a następnie kliknij pozycję **Dodaj**  >  **Nowy projekt Zadania WebJob platformy Azure**.
   
    ![Nowy wpis menu projektu zadań WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Zostanie wyświetlone okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) .
2. Wypełnij okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) , a następnie kliknij przycisk **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Okno dialogowe Dodawanie zadania WebJob platformy Azure
W oknie dialogowym **Dodawanie zadania WebJob platformy Azure** można wprowadzić nazwę i tryb uruchamiania zadania WebJob dla zadania WebJob. 

![Okno dialogowe Dodawanie zadania WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Pola w tym oknie dialogowym odpowiadają polom w oknie dialogowym **Dodawanie zadania WebJob** Azure Portal. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadań w tle za pomocą zadań WebJob](webjobs-create.md).

> [!NOTE]
> * Aby uzyskać informacje o wdrażaniu w wierszu polecenia, zobacz [Włączanie wiersza polecenia lub ciągłe dostarczanie Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * W przypadku wdrażania Zadania WebJob i decydowania o konieczności zmiany typu zadania WebJob i ponownego wdrożenia należy usunąć *webjobs-publish-settings.js* pliku. Spowoduje to, że program Visual Studio będzie ponownie wyświetlał opcje publikowania, aby można było zmienić typ zadania WebJob.
> * W przypadku wdrożenia Zadania WebJob i późniejszego zmiany trybu uruchamiania z ciągłego na non-Continuous lub odwrotnie program Visual Studio utworzy nowy element WebJob na platformie Azure podczas ponownego wdrażania. Jeśli zmienisz inne ustawienia planowania, ale pozostaw tryb uruchamiania tego samego lub przełączasz między harmonogramem i na żądanie, program Visual Studio zaktualizuje istniejące zadanie zamiast tworzyć nowe.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.jsna
Po skonfigurowaniu aplikacji konsolowej do wdrożenia usługi WebJobs program Visual Studio instaluje pakiet NuGet [Microsoft. Web. WebJobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) i zapisuje informacje o planowaniu w *webjob-publish-settings.js* w pliku w folderze *Właściwości* projektu projektu WebJobs. Oto przykład tego pliku:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

Można edytować ten plik bezpośrednio, a program Visual Studio udostępnia funkcję IntelliSense. Schemat pliku jest przechowywany w lokalizacji [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) i można go tam przeglądać.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.jsna
W przypadku łączenia projektu z obsługą zadań WebJob z projektem sieci Web program Visual Studio przechowuje nazwę projektu zadań WebJob w *webjobs-list.js* w pliku w folderze *Właściwości* projektu sieci Web. Lista może zawierać wiele projektów zadań WebJob, jak pokazano w następującym przykładzie:

```json
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
```

Można edytować ten plik bezpośrednio, a program Visual Studio udostępnia funkcję IntelliSense. Schemat pliku jest przechowywany w lokalizacji [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) i można go tam przeglądać.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Wdróż projekt WebJob
Projekt zadań WebJob, który został połączony z projektem sieci Web, jest wdrażany automatycznie w projekcie sieci Web. Informacje o wdrażaniu projektu sieci Web znajdują się w temacie **przewodniki dotyczące**  >  **wdrażania aplikacji** na lewym pasku nawigacyjnym.

Aby wdrożyć projekt zadań WebJob, kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** a następnie kliknij pozycję **Opublikuj jako zadanie WebJob platformy Azure.** 

![Publikuj jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

W przypadku niezależnego Zadania WebJob pojawia się ten sam Kreator **publikowania w sieci** Web, który jest używany na potrzeby projektów sieci Web, ale z mniejszą liczbą dostępnych ustawień do zmiany.

## <a name="scheduling-a-triggered-webjob"></a>Planowanie wyzwalanego zadania WebJob

Zadania WebJob wykorzystują plik *Settings. job* , aby określić, kiedy ma zostać uruchomione zadanie drukowania. Ten plik służy do ustawiania harmonogramu wykonywania zadania WebJob. Poniższy przykład jest uruchamiany co godzinę z 9 AM do 5 PM:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ten plik musi znajdować się w folderze głównym folderu zadań WebJob, po stronie skryptu w tym pliku, na przykład `wwwroot\app_data\jobs\triggered\{job name}` lub `wwwroot\app_data\jobs\continuous\{job name}` . Podczas wdrażania Zadania WebJob z poziomu programu Visual Studio Oznacz `settings.job` właściwości pliku jako **Kopiuj, jeśli nowszy**. 

Po [utworzeniu zadania WebJob na podstawie Azure Portal](webjobs-create.md)zostanie utworzony plik Settings. job.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Wyrażenia firmy CRONUS

Zadania WebJob używają tych samych wyrażeń firmy CRONUS do planowania jako wyzwalacz czasomierza w Azure Functions. Aby dowiedzieć się więcej o pomocy technicznej firmy CRONUS, zobacz [wyzwalacz czasomierza dla Azure Functions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>Dokumentacja ustawień. job

Następujące ustawienia są obsługiwane przez Zadania WebJob:

| **Ustawienie** | **Typ**  | **Opis** |
| ----------- | --------- | --------------- |
| `is_in_place` | Wszystko | Zezwala na uruchamianie zadania bez uprzedniego kopiowania do folderu tymczasowego. Aby dowiedzieć się więcej, zobacz [katalog roboczy usługi WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Ciągłe | W przypadku skalowania w poziomie należy uruchamiać tylko zadania WebJob w jednym wystąpieniu. Aby dowiedzieć się więcej, zobacz [Ustawianie ciągłego zadania jako pojedyncze](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Wyzwalane | Uruchom zadanie WebJob na podstawie harmonogramu opartego na języku cronus. Aby dowiedzieć się więcej, zobacz artykuł dotyczący [wyzwalacza czasomierza](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Wszystko | Umożliwia sterowanie zachowaniem zamknięcia. Aby dowiedzieć się więcej, zobacz temat [bezpieczne zamykanie](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zestawie SDK zadań WebJob](webjobs-sdk-how-to.md)
