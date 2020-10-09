---
title: Instalowanie aplikacji w klastrze autonomicznym
description: W tym samouczku dowiesz się, jak zainstalować aplikację w autonomicznym klastrze Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0b35bd02af5e1c90cac2e94d31dfca2344f3cc85
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840629"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Samouczek: wdrażanie aplikacji w autonomicznym klastrze usługi Service Fabric

Klastry autonomiczne usługi Service Fabric umożliwiają wybór własnego środowiska i utworzenie klastra zgodnie z obowiązującą w usłudze Service Fabric zasadą „dowolnego systemu operacyjnego i dowolnej chmury”. W tej serii samouczków utworzysz klaster autonomiczny hostowany w AWS i wdrożono w nim aplikację.

Ten samouczek jest trzecią częścią serii.  Service Fabric klastry autonomiczne oferują możliwość wyboru własnego środowiska i utworzenia klastra w ramach podejścia "dowolnych systemów operacyjnych, wszelkich chmur" z Service Fabric. W tym samouczku pokazano, jak utworzyć infrastrukturę usług AWS niezbędną do obsługi takiego klastra autonomicznego.

W tym artykule dowiesz się, jak:

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

5. Wybierz listę rozwijaną **Punkt końcowy połączenia** i wprowadź publiczną nazwę DNS jednego z węzłów w klastrze.  Na przykład `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Na platformie Azure, w pełni kwalifikowana nazwa domeny (FQDN) nie jest podawana automatycznie, ale można ją łatwo [ustawić na stronie Przegląd maszyny wirtualnej.](../virtual-machines/linux/portal-create-fqdn.md)

6. Otwórz preferowaną przeglądarkę i wpisz adres klastra (punkt końcowy połączenia; ta aplikacja jest wdrożona na porcie 8080 — na przykład ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Odpowiedź interfejsu API z klastra](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób wdrażania aplikacji w klastrze:

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji
> * Wdrażanie w klastrze

Przejdź do części czwartej serii, aby wyczyścić klaster.

> [!div class="nextstepaction"]
> [Czyszczenie zasobów](service-fabric-tutorial-standalone-clean-up.md)
