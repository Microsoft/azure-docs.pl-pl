---
title: Tworzenie i publikowanie aplikacji a.Net Core w zdalnym klastrze z systemem Linux
description: Tworzenie i publikowanie .Net Core aplikacji przeznaczonych dla zdalnego klastra systemu Linux z programu Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614353"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Tworzenie i publikowanie .Net Core aplikacji przeznaczonych dla zdalnego klastra Service Fabric systemu Linux przy użyciu programu Visual Studio
Za pomocą narzędzi Visual Studio można tworzyć i publikować Service Fabric aplikacje .Net Core ukierunkowane na klaster Service Fabric systemu Linux. Wersja zestawu SDK musi mieć wartość 3,4 lub nowszą, aby wdrożyć aplikację .Net Core ukierunkowaną na klastry Service Fabric systemu Linux z programu Visual Studio.

> [!Note]
> Program Visual Studio nie obsługuje debugowania aplikacji Service Fabric przeznaczonych dla systemu Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Tworzenie Service Fabric aplikacji docelowej .Net Core
1. Uruchom program Visual Studio jako **administrator**.
2. Utwórz projekt o **>nowym >projekcie**.
3. W oknie dialogowym **Nowy projekt** wybierz pozycję **Cloud-> Service Fabric aplikacji**.
![Tworzenie aplikacji]
4. Nadaj nazwę aplikacji, a następnie kliknij przycisk **OK**.
5. Na stronie **Nowa usługa Service Fabric** wybierz typ usługi, którą chcesz utworzyć, w **sekcji .NET Core**.
![Utwórz usługę]

## <a name="deploy-to-a-remote-linux-cluster"></a>Wdrażanie do zdalnego klastra systemu Linux
1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Kompiluj**.
![Kompilacja — aplikacja]
2. Po zakończeniu procesu kompilacji aplikacji kliknij prawym przyciskiem myszy usługę i wybierz polecenie Edytuj **plik CSPROJ**.
![Edit-csproj]
3. Edytuj Właściwość UpdateServiceFabricManifestEnabled z true na **false** , jeśli usługa jest **typem projektu aktora**. Jeśli aplikacja nie ma usługi aktora, przejdź do kroku 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Ustawienie UpdateServiceFabricManifestEnabled na false powoduje wyłączenie aktualizacji ServiceManifest.xml podczas kompilacji. Wszelkie zmiany, takie jak dodawanie, usuwanie lub zmiana nazwy usługi, nie zostaną odzwierciedlone w ServiceManifest.xml. W przypadku wprowadzenia jakichkolwiek zmian należy ręcznie zaktualizować element servicemanifest lub tymczasowo ustawić UpdateServiceFabricManifestEnabled na wartość true i skompilować usługę, która zaktualizuje ServiceManifest.xml, a następnie przywraca ją z powrotem do wartości false.
>

4. Zaktualizuj RuntimeIndetifier z platformy Win7 – x64 na platformę docelową w projekcie usługi.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. W elemencie servicemanifest zaktualizuj program EntryPoint do pliku. exe. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Publikuj**. Zostanie wyświetlone okno dialogowe **Publikowanie**.
7. W obszarze **punkt końcowy połączenia**wybierz punkt końcowy klastra zdalnego Service Fabric Linux, który ma być celem.
![publikowanie aplikacji]

<!--Image references-->
[Tworzenie aplikacji]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[Utwórz usługę]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[Kompilacja — aplikacja]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[Edit-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publikowanie aplikacji]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o rozpoczynaniu [pracy z usługą Service Fabric z .NET Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
