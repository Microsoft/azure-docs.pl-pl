---
title: Opracowywanie i wdrażanie zadań WebJob za pomocą programu Visual Studio
description: Dowiedz się, jak opracowywać Azure WebJobs w programie Visual Studio i wdrażać je w Azure App Service, w tym o tworzeniu zaplanowanego zadania.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89226033"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Opracowywanie i wdrażanie zadań WebJob za pomocą programu Visual Studio

W tym artykule wyjaśniono, jak używać programu Visual Studio do wdrażania projektu aplikacji konsolowej w aplikacji sieci Web w [Azure App Service](overview.md) jako [Zadania WebJob platformy Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Aby uzyskać informacje na temat sposobu wdrażania zadań WebJob przy użyciu [Azure Portal](https://portal.azure.com), zobacz [Uruchamianie zadań w tle za pomocą zadań webjob w programie Azure App Service](webjobs-create.md).

Możesz wybrać opcję tworzenia zadania WebJob działającego jako [aplikacja .NET Core](#webjobs-as-net-core-console-apps) lub [aplikacja .NET Framework](#webjobs-as-net-framework-console-apps). Wersja 3. x [zestawu SDK Azure WebJobs](webjobs-sdk-how-to.md) umożliwia tworzenie zadań WebJob, które są uruchamiane jako aplikacje .NET Core lub aplikacje .NET Framework, natomiast wersja 2. x obsługuje tylko .NET Framework. Sposób wdrażania projektu zadań WebJob jest różny dla projektów .NET Core niż w przypadku projektów .NET Framework.

Można opublikować wiele zadań WebJob w pojedynczej aplikacji sieci Web, pod warunkiem, że każde zadanie w aplikacji sieci Web ma unikatową nazwę.

## <a name="webjobs-as-net-core-console-apps"></a>Zadania WebJob jako aplikacje konsolowe platformy .NET Core

W wersji 3. x zestawu SDK Azure WebJobs można tworzyć i publikować Zadania WebJob jako aplikacje konsolowe platformy .NET Core. Aby uzyskać instrukcje krok po kroku dotyczące tworzenia i publikowania aplikacji konsolowej .NET Core na platformie Azure jako zadania WebJob, zobacz [wprowadzenie do zestawu SDK Azure WebJobs na potrzeby przetwarzania w tle opartego na zdarzeniach](webjobs-sdk-get-started.md).

> [!NOTE]
> Zadań WebJob programu .NET Core nie można łączyć z projektami sieci Web. Jeśli musisz wdrożyć zadanie WebJob za pomocą aplikacji sieci Web, [Utwórz zadania WebJobs jako aplikację konsolową .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Wdrażanie w usłudze Azure App Service

Publikowanie Zadania WebJob programu .NET Core w celu Azure App Service z programu Visual Studio używa tych samych narzędzi, co publikowanie aplikacji ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Zadania WebJob jako .NET Framework aplikacje konsolowe  

Jeśli używasz programu Visual Studio do wdrażania projektu aplikacji konsolowej z obsługą zadań WebJob .NET Framework, pliki środowiska uruchomieniowego są kopiowane do odpowiedniego folderu w aplikacji sieci Web (*App_Data/Jobs/Continuous* dla ciągłych zadań webjob i *App_Data/Jobs/Triggered* dla zaplanowanych lub na żądanie zadań WebJob).

Program Visual Studio dodaje następujące elementy do projektu z obsługą zadań WebJob:

* Pakiet NuGet [Microsoft. Web. WebJobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* [webjob-publish-settings.js](#publishsettings) pliku, który zawiera ustawienia wdrożenia i harmonogramu. 

![Diagram przedstawiający elementy dodawane do aplikacji konsolowej w celu włączenia wdrożenia jako zadania WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Można dodać te elementy do istniejącego projektu aplikacji konsolowej lub użyć szablonu, aby utworzyć nowy projekt aplikacji konsolowej z włączoną obsługą zadań WebJob. 

Wdróż projekt jako zadanie WebJob lub połącz go z projektem sieci Web, tak aby automatycznie wdrażał się przy każdym wdrożeniu projektu sieci Web. Aby połączyć projekty, program Visual Studio zawiera nazwę projektu z obsługą zadań WebJob w [webjobs-list.js](#webjobslist) w pliku projektu sieci Web.

![Diagram przedstawiający łączenie projektu WebJob z projektem sieci Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj program Visual Studio 2017 lub Visual Studio 2019 przy użyciu [obciążeń programistycznych platformy Azure](/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> Włącz wdrażanie zadań WebJob dla istniejącego projektu aplikacji konsolowej

Dostępne są dwie opcje:

* [Włącz automatyczne wdrażanie przy użyciu projektu sieci Web](#convertlink).

  Skonfiguruj istniejący projekt aplikacji konsolowej, tak aby był automatycznie wdrażany jako zadanie WebJob podczas wdrażania projektu sieci Web. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w tej samej aplikacji sieci Web, w której uruchomiono powiązaną aplikację sieci Web.

* [Włącz wdrożenie bez projektu sieci Web](#convertnolink).

  Skonfiguruj istniejący projekt aplikacji konsolowej do wdrożenia jako zadanie WebJob, bez linku do projektu sieci Web. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w aplikacji sieci Web, korzystając z aplikacji sieci Web działającej w aplikacji sieci Web. Można to zrobić, aby skalować zasoby WebJob niezależnie od zasobów aplikacji sieci Web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Włącz automatyczne wdrażanie zadań WebJob przy użyciu projektu sieci Web

1. Kliknij prawym przyciskiem myszy projekt sieci Web w **Eksplorator rozwiązań**, a następnie wybierz pozycję **Dodaj**  >  **istniejący projekt jako zadanie WebJob platformy Azure**.
   
    ![Istniejący projekt jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Zostanie wyświetlone okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) .
2. Z listy rozwijanej **Nazwa projektu** wybierz projekt aplikacji konsoli, który ma zostać dodany jako zadanie WebJob.
   
    ![Wybieranie projektu w oknie dialogowym Dodawanie zadania WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Wypełnij okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) , a następnie wybierz przycisk **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Włącz wdrażanie zadań WebJob bez projektu sieci Web
1. Kliknij prawym przyciskiem myszy projekt Aplikacja konsolowa w **Eksplorator rozwiązań**, a następnie wybierz pozycję **Publikuj jako zadanie WebJob platformy Azure**. 
   
    ![Publikuj jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Zostanie wyświetlone okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) z projektem wybranym w polu **Nazwa projektu** .
2. Wypełnij okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) , a następnie wybierz przycisk **OK**.
   
   Zostanie wyświetlony Kreator **publikowania w sieci Web** . Jeśli nie chcesz publikować natychmiast, zamknij kreatora. Wprowadzone ustawienia są zapisywane w przypadku, gdy chcesz [wdrożyć projekt](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Utwórz nowy projekt z obsługą zadań WebJob
Aby utworzyć nowy projekt z obsługą zadań WebJob, użyj szablonu projektu aplikacji konsoli i Włącz wdrożenie zadań WebJob zgodnie z opisem w [poprzedniej sekcji](#convert). Alternatywnie można użyć szablonu Nowy projekt Zadania WebJob:

* [Użycie szablonu New-Project nowego projektu dla niezależnego Zadania WebJob](#createnolink)
  
    Utwórz projekt i skonfiguruj go do wdrożenia jako zadanie WebJob, bez linku do projektu sieci Web. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w aplikacji sieci Web, korzystając z aplikacji sieci Web działającej w aplikacji sieci Web. Można to zrobić, aby skalować zasoby WebJob niezależnie od zasobów aplikacji sieci Web.
* [Użyj szablonu New-Project nowego projektu dla zadania WebJob połączonego z projektem sieci Web](#createlink)
  
    Utwórz projekt, który jest skonfigurowany do automatycznego wdrażania jako zadanie WebJob podczas wdrażania projektu sieci Web w tym samym rozwiązaniu. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w tej samej aplikacji sieci Web, w której uruchomiono powiązaną aplikację sieci Web.

> [!NOTE]
> Szablon nowy projekt Zadania WebJob automatycznie instaluje pakiety NuGet i zawiera kod w *programie program. cs* dla [zestawu SDK usługi WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Jeśli nie chcesz używać zestawu SDK zadań WebJob, Usuń lub Zmień `host.RunAndBlock` instrukcję w *programie program. cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Użycie szablonu New-Project nowego projektu dla niezależnego Zadania WebJob
1. Wybierz pozycję **plik**  >  **Nowy**  >  **projekt**. W oknie dialogowym **Crete nowego projektu** Wyszukaj i wybierz pozycję **zadanie WebJob platformy Azure (.NET Framework)** dla języka C#.
   
2. Postępuj zgodnie z poprzednimi wskazówkami, aby [utworzyć projekt aplikacji konsoli jako niezależny projekt zadań WebJob](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Użyj szablonu New-Project nowego projektu dla zadania WebJob połączonego z projektem sieci Web
1. Kliknij prawym przyciskiem myszy projekt sieci Web w **Eksplorator rozwiązań**, a następnie wybierz pozycję **Dodaj**  >  **Nowy projekt Zadania WebJob platformy Azure**.
   
    ![Nowy wpis menu projektu zadań WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Zostanie wyświetlone okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) .
2. Wypełnij okno dialogowe [Dodawanie zadania WebJob platformy Azure](#configure) , a następnie wybierz przycisk **OK**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>webjob-publish-settings.jspliku
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

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>webjobs-list.jspliku
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

Ten plik można edytować bezpośrednio w programie Visual Studio przy użyciu funkcji IntelliSense. Schemat pliku jest przechowywany w lokalizacji [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Wdróż projekt WebJob
Projekt WebJobs, który został połączony z projektem sieci Web, jest wdrażany automatycznie w projekcie sieci Web. Aby uzyskać informacje na temat wdrażania projektu sieci Web, zobacz **przewodniki dotyczące** wdrażania  >  **aplikacji** w lewym okienku nawigacji.

Aby wdrożyć projekt zadań WebJob, kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Publikuj jako zadanie WebJob platformy Azure**. 

![Publikuj jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

W przypadku niezależnego Zadania WebJob pojawia się ten sam Kreator **publikowania w sieci** Web, który jest używany na potrzeby projektów sieci Web, ale z mniejszą liczbą dostępnych ustawień do zmiany.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Okno dialogowe Dodawanie zadania WebJob platformy Azure
Okno dialogowe **Dodawanie zadania Azure WebJob** umożliwia wprowadzenie nazwy zadania WebJob i ustawienia trybu uruchamiania zadania WebJob. 

![Okno dialogowe Dodawanie zadania WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Niektóre pola w tym oknie dialogowym odpowiadają polom w oknie dialogowym **Dodawanie zadania WebJob** Azure Portal. Aby uzyskać więcej informacji, zobacz [Uruchamianie zadań w tle za pomocą zadań WebJob w Azure App Service](webjobs-create.md).

Informacje o wdrożeniu Zadania WebJob:

* Aby uzyskać informacje o wdrażaniu w wierszu polecenia, zobacz [Włączanie wiersza polecenia lub ciągłe dostarczanie Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).

* W przypadku wdrażania Zadania WebJob, a następnie podjęcia decyzji o konieczności zmiany typu zadania WebJob i ponownego wdrożenia, Usuń *webjobs-publish-settings.js* pliku. Spowoduje to, że program Visual Studio będzie ponownie wyświetlał opcje publikowania, aby można było zmienić typ zadania WebJob.

* W przypadku wdrożenia Zadania WebJob i późniejszego zmiany trybu uruchamiania z ciągłego na non-Continuous lub odwrotnie program Visual Studio utworzy nowy element WebJob na platformie Azure podczas ponownego wdrażania. Jeśli zmienisz inne ustawienia planowania, ale pozostaw tryb uruchamiania tego samego lub przełączasz między zaplanowanym i na żądanie, program Visual Studio zaktualizuje istniejące zadanie zamiast tworzyć nowe.

## <a name="webjob-types"></a>Typy zadań WebJob

Typ zadania WebJob może być *wyzwalany* lub *ciągły*:

- Wyzwolone (domyślnie): wyzwolone zadanie WebJob zaczyna się na podstawie zdarzenia powiązania, zgodnie z [harmonogramem](#scheduling-a-triggered-webjob)lub po jego wyzwoleniu ręcznie (na żądanie). Jest on uruchamiany na jednym wystąpieniu, na którym działa aplikacja sieci Web.

- Ciągłe: [ciągłe](#continuous-execution) zadanie WebJob jest uruchamiane natychmiast po utworzeniu zadania WebJob. Jest ono wykonywane domyślnie dla wszystkich wystąpień skalowania aplikacji sieci Web, ale można je skonfigurować do uruchamiania jako pojedyncze wystąpienie za pośrednictwem *ustawień. job*.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Planowanie wyzwalanego zadania WebJob

Po opublikowaniu aplikacji konsolowej na platformie Azure program Visual Studio ustawia typ zadania WebJob **wyzwalane** domyślnie i dodaje nowy plik *Settings. job* do projektu. W przypadku typów wyzwalanych zadań WebJob można użyć tego pliku, aby ustawić harmonogram wykonywania zadania WebJob.

Użyj pliku *Settings. job* , aby ustawić harmonogram wykonywania zadania WebJob. Poniższy przykład jest uruchamiany co godzinę z 9 AM do 5 PM:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ten plik znajduje się w folderze głównym folderu zadań WebJob z skryptem zadania drukowania, takim jak `wwwroot\app_data\jobs\triggered\{job name}` lub `wwwroot\app_data\jobs\continuous\{job name}` . Podczas wdrażania Zadania WebJob z poziomu programu Visual Studio należy oznaczyć *Ustawienia.* właściwości pliku zadania w programie Visual Studio jako **Kopiuj, jeśli nowszy**.

Jeśli [utworzysz zadanie WebJob na podstawie Azure Portal](webjobs-create.md), zostanie utworzony plik *Settings. job* .

#### <a name="cron-expressions"></a>Wyrażenia firmy CRONUS

Zadania WebJob używają tych samych wyrażeń firmy CRONUS do planowania jako wyzwalacz czasomierza w Azure Functions. Aby dowiedzieć się więcej o pomocy technicznej firmy CRONUS, zobacz [wyzwalacz czasomierza dla Azure Functions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>Dokumentacja ustawień. job

Następujące ustawienia są obsługiwane przez Zadania WebJob:

| **Ustawienie** | **Typ**  | **Opis** |
| ----------- | --------- | --------------- |
| `is_in_place` | Wszystko | Umożliwia uruchomienie zadania WebJob bez uprzedniego kopiowania do folderu tymczasowego. Aby uzyskać więcej informacji, zobacz [katalog roboczy zadania WebJob](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Ciągłe | W przypadku skalowania w poziomie nie uruchamiaj Zadania WebJob tylko dla jednego wystąpienia. Aby uzyskać więcej informacji, zobacz [Ustawianie ciągłego zadania jako pojedyncze](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Wyzwalane | Uruchom zadanie WebJob na podstawie harmonogramu opartego na języku cronus. Aby uzyskać więcej informacji, zobacz [wyrażenia NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Wszystko | Umożliwia sterowanie zachowaniem zamknięcia. Aby uzyskać więcej informacji, zobacz [bezpieczne zamykanie](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Ciągłe wykonywanie

Jeśli włączysz opcję **zawsze włączone** na platformie Azure, możesz użyć programu Visual Studio, aby zmienić zadanie WebJob w sposób ciągły:

1. Jeśli jeszcze tego nie zrobiono, [Opublikuj projekt na platformie Azure](#deploy-to-azure-app-service).

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. Na karcie **Publikowanie** wybierz pozycję **Edytuj**. 

1. W oknie dialogowym **Ustawienia profilu** wybierz pozycję **ciągłe** dla **typu zadania WebJob**, a następnie wybierz pozycję **Zapisz**.

    ![Okno dialogowe Ustawienia publikowania dla zadania WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Wybierz pozycję **Publikuj** na karcie **Publikowanie** , aby ponownie opublikować zadanie WebJob ze zaktualizowanymi ustawieniami.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o zestawie SDK zadań WebJob](webjobs-sdk-how-to.md)