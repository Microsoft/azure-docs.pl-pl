---
title: Wdrażanie kodu przy użyciu pliku ZIP lub WAR
description: Dowiedz się, jak wdrożyć aplikację w Azure App Service pomocą pliku ZIP (lub pliku WAR dla deweloperów języka Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9f59576ea66b72a492e1e6c665a51258861842dd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833023"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Wdrażanie aplikacji w Azure App Service za pomocą pliku ZIP lub WAR

W tym artykule pokazano, jak za pomocą pliku ZIP lub WAR wdrożyć aplikację internetową w [Azure App Service](overview.md). 

To wdrożenie pliku ZIP używa tej samej usługi Kudu, która pozwala na ciągłe wdrażanie oparte na integracji. Kudu obsługuje następujące funkcje wdrażania plików ZIP: 

- Usunięcie plików pozostawione z poprzedniego wdrożenia.
- Opcja włączenie domyślnego procesu kompilacji, który obejmuje przywracanie pakietów.
- Dostosowywanie wdrożenia, w tym uruchamianie skryptów wdrażania.  
- Dzienniki wdrażania. 
- Limit rozmiaru pliku to 2048 MB.

Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Wdrożenie pliku WAR wdraża plik [WAR](https://wikipedia.org/wiki/WAR_(file_format)) na App Service do uruchomienia aplikacji internetowej Java. Zobacz [Deploy WAR file (Wdrażanie pliku WAR).](#deploy-war-file)

> [!NOTE]
> W przypadku korzystania z funkcji pliki będą kopiowane tylko wtedy, gdy ich znaczniki czasu nie są zgodne z tym, `ZipDeploy` co zostało już wdrożone. Wygenerowanie pliku zip przy użyciu procesu kompilacji, który buforuje dane wyjściowe, może przyspieszyć wdrażanie. Aby [uzyskać więcej informacji,](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)zobacz Wdrażanie z pliku zip lub adresu URL.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, utwórz aplikację [App Service](./index.yml)lub użyj aplikacji utworzonej na podstawie innego samouczka.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Powyższy punkt końcowy nie działa w tym momencie App Services Linux. Zamiast tego rozważ użycie protokołu FTP lub [interfejsu API wdrażania pliku ZIP.](faq-app-service-linux.md#continuous-integration-and-deployment)

## <a name="deploy-zip-file-with-azure-cli"></a>Wdrażanie pliku ZIP przy użyciu interfejsu wiersza polecenia platformy Azure

Wdeń przekazany plik ZIP do aplikacji internetowej za pomocą polecenia [az webapp deployment source config-zip.](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip)  

Poniższy przykład umożliwia wdrożenie przekazanego pliku ZIP. W przypadku korzystania z lokalnej instalacji interfejsu wiersza polecenia platformy Azure określ ścieżkę do lokalnego pliku ZIP dla programu `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

To polecenie wdraża pliki i katalogi z pliku ZIP do domyślnego folderu aplikacji App Service (`\home\site\wwwroot`) i ponownie uruchamia aplikację.

Domyślnie aparat wdrażania zakłada, że plik ZIP jest gotowy do uruchomienia w stanie gotowości i nie uruchamia automatyzacji kompilacji. Aby włączyć tę samą automatyzację kompilacji co we wdrożeniu [usługi Git,](deploy-local-git.md)ustaw ustawienie aplikacji, uruchamiając następujące polecenie w `SCM_DO_BUILD_DURING_DEPLOYMENT` [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Więcej informacji zawiera [dokumentacja aparatu Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Wdrażanie pliku WAR

Aby wdrożyć plik WAR w App Service, wyślij żądanie POST na adres `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . Żądanie POST musi zawierać plik WAR w treści wiadomości. Poświadczenia wdrażania dla aplikacji są podawane w żądaniu za pomocą podstawowego uwierzytelniania HTTP.

Zawsze używaj `/api/wardeploy` podczas wdrażania plików WAR. Ten interfejs API rozwinie plik WAR i umieść go na udostępnionym dysku plików. korzystanie z innych interfejsów API wdrażania może spowodować niespójne zachowanie. 

Do uwierzytelniania HTTP BASIC potrzebne są poświadczenia App Service wdrożenia. Aby dowiedzieć się, jak ustawić poświadczenia wdrożenia, zobacz [Set and reset user-level credentials](deploy-configure-credentials.md#userscope)(Ustawianie i resetowanie poświadczeń na poziomie użytkownika).

### <a name="with-curl"></a>Z listą cURL

W poniższym przykładzie użyto narzędzia cURL do wdrożenia pliku war. Zastąp symbole zastępcze `<username>` , `<war-file-path>` i `<app-name>` . Po wyświetleniu monitu przez program cURL wpisz hasło.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Z programem PowerShell

W poniższym przykładzie [użyto nazwy Publish-AzWebapp,](/powershell/module/az.websites/publish-azwebapp) aby przekazać plik war. Zastąp symbole zastępcze `<group-name>` , `<app-name>` i `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Następne kroki

W przypadku bardziej zaawansowanych scenariuszy wdrażania spróbuj [wdrożyć na platformie Azure za pomocą narzędzia Git.](deploy-local-git.md) Wdrożenie oparte na usłudze Git na platformie Azure umożliwia kontrolę wersji, przywracanie pakietów, program MSBuild i nie tylko.

## <a name="more-resources"></a>Więcej zasobów

* [Kudu: wdrażanie z pliku zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service wdrażania](deploy-ftp.md)
