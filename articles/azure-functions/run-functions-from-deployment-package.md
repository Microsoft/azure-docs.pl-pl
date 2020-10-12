---
title: Uruchamianie Azure Functions z pakietu
description: Aby środowisko uruchomieniowe Azure Functions uruchomiło funkcje, instalując plik pakietu wdrożeniowego zawierający pliki projektu aplikacji funkcji.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: b2d90cf78263b30b4315199cf1c543186a435f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639889"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Uruchamianie Azure Functions z pliku pakietu

Na platformie Azure możesz uruchamiać funkcje bezpośrednio z pliku pakietu wdrożeniowego w aplikacji funkcji. Druga opcja polega na wdrożeniu plików w `d:\home\site\wwwroot` katalogu aplikacji funkcji.

W tym artykule opisano zalety uruchamiania funkcji z pakietu. Pokazano również, jak włączyć tę funkcję w aplikacji funkcji.

## <a name="benefits-of-running-from-a-package-file"></a>Zalety uruchamiania z pliku pakietu
  
Istnieje kilka korzyści, które należy wykonać, korzystając z pliku pakietu:

+ Zmniejsza ryzyko problemów z blokowaniem kopiowania plików.
+ Program można wdrożyć w aplikacji produkcyjnej (z ponownym uruchomieniem).
+ Możesz mieć pewne pliki, które są uruchomione w aplikacji.
+ Zwiększa wydajność [wdrożeń Azure Resource Manager](functions-infrastructure-as-code.md).
+ Może skrócić czas zimnego startu, szczególnie w przypadku funkcji JavaScript z dużymi drzewami pakietów npm.

Aby uzyskać więcej informacji, zobacz [ten anons](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Włączanie funkcji do uruchamiania z pakietu

Aby umożliwić uruchamianie aplikacji funkcji z pakietu, wystarczy dodać `WEBSITE_RUN_FROM_PACKAGE` ustawienie do ustawień aplikacji funkcji. `WEBSITE_RUN_FROM_PACKAGE`Ustawienie może mieć jedną z następujących wartości:

| Wartość  | Opis  |
|---------|---------|
| **`1`**  | Zalecane w przypadku aplikacji funkcji działających w systemie Windows. Uruchom plik pakietu w `d:\home\data\SitePackages` folderze aplikacji funkcji. Jeśli nie jest [wdrażany przy użyciu narzędzia zip Deploy](#integration-with-zip-deployment), ta opcja wymaga, aby folder miał również plik o nazwie `packagename.txt` . Ten plik zawiera tylko nazwę pliku pakietu w folderze, bez odstępów. |
|**`<URL>`**  | Lokalizacja określonego pliku pakietu, który ma zostać uruchomiony. Korzystając z usługi BLOB Storage, należy użyć prywatnego kontenera z [sygnaturą dostępu współdzielonego (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) , aby umożliwić środowisko uruchomieniowe funkcji dostęp do pakietu. Aby przekazać pliki pakietu do konta magazynu obiektów blob, można użyć [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) . Po określeniu adresu URL należy również [zsynchronizować wyzwalacze](functions-deployment-technologies.md#trigger-syncing) po opublikowaniu zaktualizowanego pakietu. |

> [!CAUTION]
> W przypadku uruchamiania aplikacji funkcji w systemie Windows opcja zewnętrzny adres URL daje w wyniku gorszą wydajność zimnego startu. Podczas wdrażania aplikacji funkcji w systemie Windows należy ustawić wartość `WEBSITE_RUN_FROM_PACKAGE` `1` i opublikować ją z wdrożeniem zip.

Poniżej przedstawiono aplikację funkcji skonfigurowaną do uruchamiania z pliku zip hostowanego w usłudze Azure Blob Storage:

![WEBSITE_RUN_FROM_ZIP ustawienie aplikacji](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Obecnie obsługiwane są tylko pliki pakietu zip.

## <a name="integration-with-zip-deployment"></a>Integracja z wdrożeniem zip

[Wdrożenie zip][Zip deployment for Azure Functions] to funkcja Azure App Service, która umożliwia wdrożenie projektu aplikacji funkcji w `wwwroot` katalogu. Projekt jest spakowany jako plik wdrożenia. zip. Te same interfejsy API mogą służyć do wdrażania pakietu w `d:\home\data\SitePackages` folderze. W przypadku `WEBSITE_RUN_FROM_PACKAGE` Ustawienia aplikacji w `1` interfejsie API wdrożenia zip Skopiuj pakiet do `d:\home\data\SitePackages` folderu zamiast wyodrębniania plików do programu `d:\home\site\wwwroot` . Tworzy również `packagename.txt` plik. Po ponownym uruchomieniu pakiet jest instalowany `wwwroot` jako system plików tylko do odczytu. Aby uzyskać więcej informacji o wdrażaniu zip, zobacz [zip Deployment for Azure Functions](deployment-zip-push.md).

> [!NOTE]
> Po wystąpieniu wdrożenia zostanie wyzwolone ponowne uruchomienie aplikacji funkcji. Przed ponownym uruchomieniem programu można wykonać wszystkie istniejące wykonania funkcji lub przekroczyć limit czasu. Aby dowiedzieć się więcej, zobacz [zachowania wdrażania](functions-deployment-technologies.md#deployment-behaviors).

## <a name="adding-the-website_run_from_package-setting"></a>Dodawanie ustawienia WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Polecenie Uruchom z pakietu sprawia, że jest `wwwroot` tylko do odczytu, więc podczas zapisywania plików w tym katalogu zostanie wyświetlony komunikat o błędzie.
- Formaty tar i gzip nie są obsługiwane.
- Ta funkcja nie składa się z lokalnej pamięci podręcznej.
- W celu zwiększenia wydajności zimnej lokalizacji Użyj opcji zip ( `WEBSITE_RUN_FROM_PACKAGE` = 1).
- Uruchamianie z pakietu jest niezgodne z opcją dostosowania wdrożenia ( `SCM_DO_BUILD_DURING_DEPLOYMENT=true` ), krok kompilacji zostanie zignorowany podczas wdrażania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągłe wdrażanie dla Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
