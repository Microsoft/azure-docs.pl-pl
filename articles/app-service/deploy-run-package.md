---
title: Uruchamianie aplikacji z pakietu ZIP
description: Wdrażanie pakietu ZIP aplikacji z niepodzielnością. Zwiększ przewidywalność i niezawodność zachowania aplikacji podczas procesu wdrażania pliku ZIP.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: d3315370342f54091598aa3f77f70f03bda4ad33
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772743"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uruchamianie aplikacji w programie Azure App Service bezpośrednio z pakietu ZIP

W [Azure App Service](overview.md)można uruchamiać aplikacje bezpośrednio z pliku pakietu ZIP wdrożenia. W tym artykule pokazano, jak włączyć tę funkcję w aplikacji.

Wszystkie inne metody wdrażania w programie App Service mają coś wspólnego: pliki są wdrażane w folderze *D:\home\site\wwwroot* w aplikacji (lub */home/site/wwwroot* dla aplikacji systemu Linux). Ponieważ aplikacja używa tego samego katalogu w czasie wykonywania, wdrożenie może się nie powieść z powodu konfliktów blokady plików i nieprzewidywalne zachowanie aplikacji, ponieważ niektóre pliki nie zostały jeszcze zaktualizowane.

Natomiast w przypadku uruchamiania bezpośrednio z pakietu pliki w pakiecie nie są kopiowane do *katalogu wwwroot.* Zamiast tego sam pakiet ZIP jest instalowane bezpośrednio jako katalog *wwwroot* tylko do odczytu. Uruchamianie bezpośrednio z pakietu ma kilka zalet:

- Eliminuje konflikty blokady plików między wdrożeniem a środowiskiem uruchomieniowym.
- Zapewnia, że w dowolnym momencie będą uruchomione tylko w pełni wdrożone aplikacje.
- Można wdrożyć w aplikacji produkcyjnej (z ponownym uruchomieniem).
- Zwiększa wydajność Azure Resource Manager wdrożeniach.
- Może skrócić czas zimnego startu, szczególnie w przypadku funkcji języka JavaScript z dużymi drzewami pakietów npm.

> [!NOTE]
> Obecnie obsługiwane są tylko pliki pakietu ZIP.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Włączanie uruchamiania z pakietu

Ustawienie `WEBSITE_RUN_FROM_PACKAGE` aplikacji umożliwia uruchamianie z pakietu. Aby go ustawić, uruchom następujące polecenie za pomocą interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` Umożliwia uruchamianie aplikacji z pakietu lokalnego do aplikacji. Można również uruchomić [z pakietu zdalnego](#run-from-external-url-instead).

## <a name="run-the-package"></a>Uruchamianie pakietu

Najprostszym sposobem uruchomienia pakietu w pliku App Service jest polecenie [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) interfejsu wiersza polecenia platformy Azure. Na przykład:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Ponieważ ustawienie aplikacji jest ustawione, to polecenie nie wyodrębnia zawartości pakietu do `WEBSITE_RUN_FROM_PACKAGE` *katalogu D:\home\site\wwwroot* aplikacji. Zamiast tego plik ZIP jest przesyłany bez zmian do folderu *D:\home\data\SitePackages* i tworzy plik *packagename.txt* w tym samym katalogu, który zawiera nazwę pakietu ZIP do załadowania w czasie wykonywania. W przypadku przekazywania pakietu ZIP w inny sposób (na przykład [ftp)](deploy-ftp.md)należy ręcznie utworzyć katalog *D:\home\data\SitePackages* ipackagename.txt *pliku.*

Polecenie powoduje również ponowne uruchomienie aplikacji. Ponieważ plik jest ustawiony, App Service zainstalowany przekazany pakiet jako katalog wwwroot tylko do odczytu i uruchamia aplikację `WEBSITE_RUN_FROM_PACKAGE` bezpośrednio z tego zainstalowanego katalogu. 

## <a name="run-from-external-url-instead"></a>Zamiast tego uruchom z zewnętrznego adresu URL

Możesz również uruchomić pakiet z zewnętrznego adresu URL, takiego jak Azure Blob Storage. Możesz użyć tej [Eksplorator usługi Azure Storage,](../vs-azure-tools-storage-manage-with-storage-explorer.md) aby przekazać pliki pakietu na konto usługi Blob Storage. Należy użyć prywatnego kontenera magazynu z sygnaturą dostępu współdzielonego [(SAS),](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) aby umożliwić środowisku uruchomienioweowi App Service bezpieczne uzyskiwanie dostępu do pakietu. 

Po przesłaniu pliku do usługi Blob Storage i użyciu adresu URL sygnatury dostępu współdzielonego dla pliku ustaw ustawienie aplikacji `WEBSITE_RUN_FROM_PACKAGE` na adres URL. Poniższy przykład robi to za pomocą interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

W przypadku publikowania zaktualizowanego pakietu o tej samej nazwie w magazynie obiektów blob należy ponownie uruchomić aplikację, aby zaktualizowany pakiet został załadowany do App Service.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Uruchamianie bezpośrednio z pakietu sprawia, `wwwroot` że jest tylko do odczytu. Jeśli aplikacja spróbuje zapisać pliki w tym katalogu, zostanie wyświetlony błąd.
- Formaty TAR i GZIP nie są obsługiwane.
- Plik ZIP może mieć co najwyżej 1 GB
- Ta funkcja nie jest zgodna z [lokalną pamięcią podręczną.](overview-local-cache.md)
- Aby poprawić wydajność zimnego startu, użyj lokalnej opcji Zip ( `WEBSITE_RUN_FROM_PACKAGE` =1).

## <a name="more-resources"></a>Więcej zasobów

- [Ciągłe wdrażanie dla Azure App Service](deploy-continuous-deployment.md)
- [Wdrażanie kodu przy użyciu pliku ZIP lub WAR](deploy-zip.md)
