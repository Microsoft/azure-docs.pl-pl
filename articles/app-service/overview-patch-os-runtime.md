---
title: Erze poprawek systemu operacyjnego i środowiska uruchomieniowego
description: Dowiedz się, jak Azure App Service aktualizować system operacyjny i środowiska uruchomieniowe, jakie środowiska uruchomieniowe i poprawki mają być używane przez aplikacje oraz jak uzyskać powiadomienia o aktualizacjach.
ms.topic: article
ms.date: 02/02/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8b52223aea0f0bdfecf58906ac192e893da3b47d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96558491"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Stosowanie poprawek systemu operacyjnego i środowiska uruchomieniowego w Azure App Service

W tym artykule pokazano, jak uzyskać pewne informacje o wersji dotyczące systemu operacyjnego lub oprogramowania w [App Service](overview.md). 

App Service to platforma jako usługa, która oznacza, że system operacyjny i stos aplikacji są zarządzane przez platformę Azure; zarządzasz tylko aplikacją i jej danymi. [Usługa Azure Virtual Machines](../virtual-machines/index.yml)udostępnia większą kontrolę nad systemem operacyjnym i stosem aplikacji. Z tego względu przydatne może być App Service użytkownikowi, aby poznać więcej informacji, takich jak:

-   Jak i kiedy są stosowane aktualizacje systemu operacyjnego?
-   W jaki sposób App Service poprawek w przypadku poważnych luk w zabezpieczeniach (na przykład zero dni)?
-   Które wersje systemu operacyjnego i środowiska uruchomieniowego są uruchomione w aplikacjach?

Ze względów bezpieczeństwa niektóre informacje o zabezpieczeniach nie są publikowane. Artykuł ma jednak na celu wyeliminowanie problemów przez zmaksymalizowanie przejrzystości procesu i sposób, w jaki można zachować aktualność w odniesieniu do powiadomień związanych z zabezpieczeniami lub aktualizacji środowiska uruchomieniowego.

## <a name="how-and-when-are-os-updates-applied"></a>Jak i kiedy są stosowane aktualizacje systemu operacyjnego?

Platforma Azure zarządza poprawkami systemu operacyjnego na dwóch poziomach, serwerach fizycznych i maszynach wirtualnych gościa, na których działają App Service zasoby. Oba są aktualizowane co miesiąc, co jest zgodne z harmonogramem comiesięcznych [poprawek](/security-updates/) miesięcznych. Te aktualizacje są stosowane automatycznie w sposób zapewniający gwarancję umowy SLA wysokiej dostępności usług platformy Azure. 

Aby uzyskać szczegółowe informacje na temat sposobu stosowania aktualizacji, zobacz [sztucznae Magic za App Service aktualizacje systemu operacyjnego](https://azure.github.io/AppService/2018/01/18/Demystifying-the-magic-behind-App-Service-OS-updates.html).

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Jak platforma Azure zajmuje się znaczącymi lukami w zabezpieczeniach?

W przypadku poważnych luk w zabezpieczeniach, takich jak [luki w zabezpieczeniach z zerami](https://wikipedia.org/wiki/Zero-day_(computing)), aktualizacje o wysokim priorytecie są obsługiwane na zasadzie wielkości liter.

Bądź na bieżąco z krytycznymi powiadomieniami o zabezpieczeniach na platformie Azure, odwiedzając Blog dotyczący [zabezpieczeń platformy Azure](https://azure.microsoft.com/blog/topics/security/). 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>Kiedy są obsługiwane, dodawane lub przestarzałe środowiska uruchomieniowe języka?

Nowe stabilne wersje obsługiwanego środowiska uruchomieniowego języka (główna, pomocnicza lub poprawka) są okresowo dodawane do wystąpień App Service. Niektóre aktualizacje zastępują istniejącą instalację, podczas gdy inne są instalowane równolegle z istniejącymi wersjami. Instalacja zastąpień oznacza, że aplikacja jest automatycznie uruchamiana w zaktualizowanym środowisku uruchomieniowym. Instalacja równoczesna oznacza, że musisz ręcznie przeprowadzić migrację aplikacji, aby skorzystać z nowej wersji środowiska uruchomieniowego. Aby uzyskać więcej informacji, zobacz jedną z podsekcji.

Aktualizacje i przestarzałe środowiska uruchomieniowego są ogłoszone w tym miejscu:

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> Informacje w tym artykule dotyczą środowiska uruchomieniowego języka, które są wbudowane w aplikację App Service. Niestandardowe środowisko uruchomieniowe przekazywane do App Service, na przykład pozostaje niezmienione, chyba że zostanie ręcznie uaktualnione.
>
>

### <a name="new-patch-updates"></a>Nowe aktualizacje poprawek

Aktualizacje poprawek do wersji .NET, PHP, Java SDK lub Tomcat są automatycznie stosowane przez zastąpienie istniejącej instalacji najnowszą wersją. Aktualizacje poprawek Node.js są instalowane równolegle z istniejącymi wersjami (podobnie jak wersje główne i pomocnicze w następnej sekcji). Nowe wersje poprawek w języku Python można instalować ręcznie za pomocą [rozszerzeń lokacji](https://azure.microsoft.com/blog/azure-web-sites-extensions/)obok wbudowanych instalacji języka Python.

### <a name="new-major-and-minor-versions"></a>Nowe wersje główne i pomocnicze

Po dodaniu nowej wersji głównej lub pomocniczej jest ona instalowana równolegle z istniejącymi wersjami. Możesz ręcznie uaktualnić aplikację do nowej wersji. W przypadku skonfigurowania wersji środowiska uruchomieniowego w pliku konfiguracji (na przykład `web.config` i `package.json` ) należy przeprowadzić uaktualnienie przy użyciu tej samej metody. Jeśli użyto ustawienia App Service w celu skonfigurowania wersji środowiska uruchomieniowego, można ją zmienić w [Azure Portal](https://portal.azure.com) lub przez uruchomienie polecenia [interfejsu CLI platformy Azure](/cli/azure/get-started-with-azure-cli) w [Cloud Shell](../cloud-shell/overview.md), jak pokazano w następujących przykładach:

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>Przestarzałe wersje  

Gdy Starsza wersja jest przestarzała, Data usunięcia zostaje ogłoszona, aby można było odpowiednio zaplanować uaktualnienie wersji środowiska uruchomieniowego. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>Jak można wykonać zapytanie o stan aktualizacji systemu operacyjnego i środowiska uruchomieniowego w moich wystąpieniach?  

Podczas gdy krytyczne informacje o systemie operacyjnym są blokowane przed dostępem (zobacz [funkcje systemu operacyjnego na Azure App Service](operating-system-functionality.md)), [konsola kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) umożliwia wykonywanie zapytań dotyczących wystąpienia App Service w odniesieniu do wersji systemu operacyjnego i wersji środowiska uruchomieniowego. 

W poniższej tabeli przedstawiono, jak wersje systemu Windows i środowisko uruchomieniowe języka, w którym są uruchomione aplikacje:

| Informacje | Gdzie można go znaleźć | 
|-|-|
| Wersja systemu Windows | Zobacz `https://<appname>.scm.azurewebsites.net/Env.cshtml` (w obszarze informacje o systemie) |
| Wersja platformy .NET | W `https://<appname>.scm.azurewebsites.net/DebugConsole` wierszu polecenia Uruchom następujące polecenie: <br>`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full"` |
| Wersja platformy .NET Core | W `https://<appname>.scm.azurewebsites.net/DebugConsole` wierszu polecenia Uruchom następujące polecenie: <br> `dotnet --version` |
| Wersja języka PHP | W `https://<appname>.scm.azurewebsites.net/DebugConsole` wierszu polecenia Uruchom następujące polecenie: <br> `php --version` |
| Domyślna wersja Node.js | W [Cloud Shell](../cloud-shell/overview.md)Uruchom następujące polecenie: <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Wersja języka Python | W `https://<appname>.scm.azurewebsites.net/DebugConsole` wierszu polecenia Uruchom następujące polecenie: <br> `python --version` |  
| Wersja środowiska Java | W `https://<appname>.scm.azurewebsites.net/DebugConsole` wierszu polecenia Uruchom następujące polecenie: <br> `java -version` |  

> [!NOTE]  
> Dostęp do lokalizacji w rejestrze `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages` , gdzie są przechowywane informacje o [poprawkach "KB"](/security-updates/SecurityBulletins/securitybulletins) , jest zablokowany.
>
>

## <a name="more-resources"></a>Więcej zasobów

[Centrum zaufania: zabezpieczenia](https://www.microsoft.com/en-us/trustcenter/security)  
[64 ASP.NET Core bit na Azure App Service](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
