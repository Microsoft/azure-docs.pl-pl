---
title: Zarządzanie aplikacjami w programie Visual Studio
description: Za pomocą programu Visual Studio Twórz, opracowuj, Pakuj, wdrażaj i Debuguj aplikacje i usługi platformy Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574551"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Korzystanie z programu Visual Studio do uproszczenia zapisywania aplikacji Service Fabric i zarządzania nimi
Za pomocą programu Visual Studio możesz zarządzać aplikacjami i usługami platformy Azure Service Fabric. Po [skonfigurowaniu środowiska programistycznego](service-fabric-get-started.md)można użyć programu Visual Studio do tworzenia aplikacji Service Fabric, dodawania usług lub pakietów, rejestrowania i wdrażania aplikacji w lokalnym klastrze programistycznym.

## <a name="deploy-your-service-fabric-application"></a>Wdrażanie aplikacji Service Fabric
Domyślnie wdrożenie aplikacji łączy następujące kroki w jedną prostą operację:

1. Tworzenie pakietu aplikacji
2. Przekazywanie pakietu aplikacji do magazynu obrazów
3. Rejestrowanie typu aplikacji
4. Usuwanie wszystkich uruchomionych wystąpień aplikacji
5. Tworzenie wystąpienia aplikacji

W programie Visual Studio naciśnięcie klawisza **F5** powoduje wdrożenie aplikacji i dołączenie debugera do wszystkich wystąpień aplikacji. Za pomocą **kombinacji klawiszy CTRL + F5** można wdrożyć aplikację bez debugowania lub można opublikować ją w lokalnym lub zdalnym klastrze przy użyciu profilu publikowania.

### <a name="application-debug-mode"></a>Tryb debugowania aplikacji
Program Visual Studio udostępnia właściwość o nazwie **tryb debugowania aplikacji**, która kontroluje, w jaki sposób program Visual Studios ma obsługiwać wdrażanie aplikacji w ramach debugowania.

#### <a name="to-set-the-application-debug-mode-property"></a>Aby ustawić właściwość tryb debugowania aplikacji
1. W menu skrótów projektu aplikacji Service Fabric (*. sfproj) wybierz **Właściwości** (lub naciśnij klawisz **F4** ).
2. W oknie **Właściwości** ustaw właściwość **tryb debugowania aplikacji** .

![Ustaw właściwość trybu debugowania aplikacji][debugmodeproperty]

#### <a name="application-debug-modes"></a>Tryby debugowania aplikacji

1. **Odśwież aplikację** Ten tryb umożliwia szybkie zmienianie i debugowanie kodu oraz obsługuje edytowanie statycznych plików sieci Web podczas debugowania. Ten tryb działa tylko wtedy, gdy lokalny klaster programistyczny działa w trybie 1-węzłowym. Jest to domyślny tryb debugowania aplikacji.
2. **Usunięcie aplikacji** powoduje, że aplikacja zostanie usunięta po zakończeniu sesji debugowania.
3. **Autouaktualnianie** Aplikacja będzie nadal działać po zakończeniu sesji debugowania. Kolejna sesja debugowania będzie traktować wdrożenie jako uaktualnienie. Proces uaktualniania zachowuje wszystkie dane wprowadzone w poprzedniej sesji debugowania.
4. **Zachowaj aplikację** Aplikacja działa w klastrze po zakończeniu sesji debugowania. Po rozpoczęciu następnej sesji debugowania aplikacja zostanie usunięta.

W przypadku danych z **Autouaktualnianiem** dane są zachowywane przez zastosowanie możliwości uaktualnienia aplikacji Service Fabric. Aby uzyskać więcej informacji na temat uaktualniania aplikacji i sposobu przeprowadzania uaktualnienia w rzeczywistym środowisku, zobacz [Service Fabric upgrade Application](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Dodawanie usługi do aplikacji Service Fabric
Możesz dodać nowe usługi do aplikacji, aby zwiększyć jej funkcjonalność. Aby upewnić się, że usługa jest uwzględniona w pakiecie aplikacji, Dodaj usługę za pomocą **nowej usługi sieci szkieletowej...** .

![Dodawanie nowej usługi Service Fabric][newservice]

Wybierz typ projektu Service Fabric, który ma zostać dodany do aplikacji, a następnie określ nazwę usługi.  Zobacz [Wybieranie platformy dla swojej usługi](service-fabric-choose-framework.md) , aby ułatwić podjęcie decyzji, który typ usługi ma być używany.

![Wybierz typ projektu usługi Service Fabric, który ma zostać dodany do aplikacji][addserviceproject]

Nowa usługa zostanie dodana do rozwiązania i istniejącego pakietu aplikacji. Odwołania do usługi i domyślne wystąpienie usługi zostaną dodane do manifestu aplikacji, co powoduje utworzenie i uruchomienie usługi przy następnym wdrożeniu aplikacji.

![Nowa usługa zostanie dodana do manifestu aplikacji][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Pakowanie aplikacji Service Fabric
Aby wdrożyć aplikację i jej usługi w klastrze, należy utworzyć pakiet aplikacji.  Pakiet organizuje manifest aplikacji, manifesty usług i inne niezbędne pliki w określonym układzie.  Program Visual Studio konfiguruje pakiet w folderze projektu aplikacji i zarządza nim w katalogu "pkg".  Kliknięcie pozycji **pakiet** z menu kontekstowego **aplikacji** powoduje utworzenie lub zaktualizowanie pakietu aplikacji.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Usuwanie aplikacji i typów aplikacji przy użyciu programu Cloud Explorer
Podstawowe operacje zarządzania klastrami można wykonywać z poziomu programu Visual Studio przy użyciu programu Cloud Explorer, który można uruchomić z poziomu menu **Widok** . Na przykład można usunąć aplikacje i anulować obsługę administracyjną typów aplikacji w klastrach lokalnych lub zdalnych.

![Usuwanie aplikacji][removeapplication]

> [!TIP]
> Aby uzyskać bardziej zaawansowaną funkcjonalność zarządzania klastrami, zobacz [wizualizowanie klastra przy użyciu Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Następne kroki
* [Model aplikacji Service Fabric](service-fabric-application-model.md)
* [Wdrażanie aplikacji Service Fabric](service-fabric-deploy-remove-applications.md)
* [Zarządzanie parametrami aplikacji dla wielu środowisk](service-fabric-manage-multiple-environment-app-configuration.md)
* [Debugowanie aplikacji Service Fabric](service-fabric-debugging-your-application.md)
* [Wizualizacja klastra przy użyciu Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
