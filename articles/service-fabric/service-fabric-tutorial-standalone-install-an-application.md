---
title: Instalowanie aplikacji w klastrze autonomicznym
description: W tym samouczku dowiesz się, jak zainstalować aplikację w autonomicznym klastrze Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 8dd47d27f928cb33e5a2bbc3ba9a6ab2bc044852
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245385"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Samouczek: wdrażanie aplikacji w autonomicznym klastrze usługi Service Fabric

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków utworzysz klaster autonomiczny hostowany w AWS i wdrożono w nim aplikację.

Ten samouczek jest trzecią częścią serii.  Service Fabric klastry autonomiczne oferują możliwość wyboru własnego środowiska i utworzenia klastra w ramach podejścia "dowolnych systemów operacyjnych, wszelkich chmur" z Service Fabric. W tym samouczku pokazano, jak utworzyć infrastrukturę usług AWS niezbędną do obsługi takiego klastra autonomicznego.

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji
> * Wdrażanie w klastrze

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* [Zainstaluj program Visual Studio 2019](https://www.visualstudio.com/) i zainstaluj obciążenia związane z programowaniem i **programowaniem** na **platformie Azure** .
* [Instalowanie zestawu SDK usługi Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Wdrażanie aplikacji w klastrze usługi Service Fabric

Po pobraniu aplikacji można ją wdrożyć w klastrze bezpośrednio z programu Visual Studio.

1. Otwórz program Visual Studio.

2. Wybierz pozycję **plik**  >  **Otwórz**

3. Przejdź do folderu, do którego sklonowano repozytorium git, i wybierz plik Voting.sln.

4. Kliknij prawym przyciskiem myszy projekt aplikacji `Voting` w Eksploratorze rozwiązań i wybierz polecenie **Publikuj**.

5. Wybierz listę rozwijaną **Punkt końcowy połączenia** i wprowadź publiczną nazwę DNS jednego z węzłów w klastrze.  Na przykład `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Należy pamiętać, że na platformie Azure nie jest podawana w pełni kwalifikowana nazwa domeny (FQDN), ale można ją łatwo [ustawić na stronie Przegląd maszyny wirtualnej.](../virtual-machines/linux/portal-create-fqdn.md)

6. Otwórz preferowaną przeglądarkę i wpisz adres klastra (punkt końcowy połączenia; ta aplikacja jest wdrożona na porcie 8080 — na przykład ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Odpowiedź interfejsu API z klastra](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Następne kroki

W części trzeciej tej serii przedstawiono sposób wdrażania aplikacji w klastrze:

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji
> * Wdrażanie w klastrze

Przejdź do części czwartej serii, aby wyczyścić klaster.

> [!div class="nextstepaction"]
> [Czyszczenie zasobów](service-fabric-tutorial-standalone-clean-up.md)
