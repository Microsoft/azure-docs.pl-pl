---
title: Uruchamianie aplikacji z pakietu ZIP
description: Wdróż pakiet ZIP aplikacji z niepodzielną. Popraw przewidywalność i niezawodność zachowania aplikacji podczas procesu wdrażania ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 036dd8c86251af2ed5553939e74a0d85fc303ecb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577550"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uruchom aplikację w Azure App Service bezpośrednio z pakietu ZIP

W [Azure App Service](overview.md)można uruchamiać aplikacje bezpośrednio z pliku pakietu zip wdrożenia. W tym artykule pokazano, jak włączyć tę funkcję w aplikacji.

Wszystkie inne metody wdrażania w App Service mają coś wspólnego: pliki są wdrażane do *D:\home\site\wwwroot* w aplikacji (lub */Home/site/wwwroot* dla aplikacji systemu Linux). Ponieważ ten sam katalog jest używany przez aplikację w czasie wykonywania, istnieje możliwość, że wdrożenie nie powiedzie się z powodu konfliktów blokady plików, a w przypadku aplikacji jest nieprzewidywalne, ponieważ niektóre pliki nie zostały jeszcze zaktualizowane.

Natomiast w przypadku uruchamiania bezpośrednio z pakietu pliki w pakiecie nie są kopiowane do katalogu *wwwroot* . Zamiast tego pakiet ZIP jest instalowany bezpośrednio jako katalog katalogu *wwwroot* tylko do odczytu. Istnieje kilka korzyści do uruchomienia bezpośrednio z pakietu:

- Eliminuje konflikty blokady plików między wdrożeniem i środowiskiem uruchomieniowym.
- Zapewnia, że w dowolnym momencie są uruchomione tylko aplikacje w pełni wdrożone.
- Program można wdrożyć w aplikacji produkcyjnej (z ponownym uruchomieniem).
- Zwiększa wydajność wdrożeń Azure Resource Manager.
- Może skrócić czas zimnego startu, szczególnie w przypadku funkcji JavaScript z dużymi drzewami pakietów npm.

> [!NOTE]
> Obecnie obsługiwane są tylko pliki pakietu ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Włącz uruchamianie z pakietu

`WEBSITE_RUN_FROM_PACKAGE`Ustawienie aplikacji umożliwia uruchamianie z pakietu. Aby je ustawić, uruchom następujące polecenie w interfejsie wiersza polecenia platformy Azure.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` umożliwia uruchamianie aplikacji z pakietu lokalnego dla aplikacji. Można również [uruchomić z pakietu zdalnego](#run-from-external-url-instead).

## <a name="run-the-package"></a>Uruchom pakiet

Najprostszym sposobem uruchomienia pakietu w App Service jest interfejs wiersza polecenia platformy Azure [AZ webapp Deployment Source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) . Na przykład:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Ponieważ `WEBSITE_RUN_FROM_PACKAGE` ustawienie aplikacji jest ustawione, to polecenie nie wyodrębnia zawartości pakietu do katalogu *D:\home\site\wwwroot* aplikacji. Zamiast tego przekazuje plik ZIP jako-is do *D:\home\data\SitePackages* i tworzy *packagename.txt* w tym samym katalogu, który zawiera nazwę pakietu zip do załadowania w czasie wykonywania. Jeśli pakiet ZIP zostanie przekazany w inny sposób (na przykład [FTP](deploy-ftp.md)), należy ręcznie utworzyć katalog *D:\home\data\SitePackages* i plik *packagename.txt* .

Polecenie spowoduje również ponowne uruchomienie aplikacji. Ponieważ `WEBSITE_RUN_FROM_PACKAGE` jest ustawiona, App Service instaluje przekazany pakiet jako katalog katalogu *wwwroot* tylko do odczytu i uruchamia aplikację bezpośrednio z tego zainstalowanego katalogu.

## <a name="run-from-external-url-instead"></a>Zamiast tego Uruchom z zewnętrznego adresu URL

Możesz również uruchomić pakiet z zewnętrznego adresu URL, takiego jak Azure Blob Storage. Aby przekazać pliki pakietu do konta magazynu obiektów blob, można użyć [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) . Należy użyć prywatnego kontenera magazynu z [sygnaturą dostępu współdzielonego (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) , aby umożliwić środowisko uruchomieniowe App Service, aby bezpiecznie uzyskać dostęp do pakietu. 

Po przekazaniu pliku do usługi BLOB Storage i uzyskaniu adresu URL sygnatury dostępu współdzielonego dla pliku ustaw wartość `WEBSITE_RUN_FROM_PACKAGE` Ustawienia aplikacji na adres URL. W poniższym przykładzie jest to możliwe za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Jeśli publikujesz zaktualizowany pakiet o tej samej nazwie w usłudze BLOB Storage, musisz ponownie uruchomić aplikację, aby zaktualizowany pakiet został załadowany do App Service.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Uruchamianie bezpośrednio z pakietu sprawia, że `wwwroot` tylko do odczytu. Aplikacja otrzyma błąd, jeśli spróbuje zapisać pliki w tym katalogu.
- Formaty TAR i GZIP nie są obsługiwane.
- Plik ZIP może składać się z maksymalnie 1 GB
- Ta funkcja jest niezgodna z [lokalną pamięcią podręczną](overview-local-cache.md).
- W celu zwiększenia wydajności zimnej lokalizacji Użyj opcji zip ( `WEBSITE_RUN_FROM_PACKAGE` = 1).

## <a name="more-resources"></a>Więcej zasobów

- [Ciągłe wdrażanie dla Azure App Service](deploy-continuous-deployment.md)
- [Wdróż kod z plikiem ZIP lub WAR](deploy-zip.md)
