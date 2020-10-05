---
title: Tworzenie aplikacji kontenera systemu Linux na Service Fabric na platformie Azure
description: W tym przewodniku Szybki start skompilujesz obraz Docker za pomocą własnej aplikacji, wypchniesz obraz do rejestru kontenerów, a następnie wdrożysz kontener do klastra usługi Service Fabric.
ms.topic: quickstart
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 27a21a685d8dc4aa92585ce49b21d1986a54209f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91541513"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Szybki start: wdrażanie kontenerów systemu Linux w usłudze Service Fabric

Usługa Azure Service Fabric to platforma systemów rozproszonych ułatwiająca pakowanie i wdrażanie skalowalnych oraz niezawodnych mikrousług i kontenerów, a także zarządzanie nimi.

W tym przewodniku Szybki start przedstawiono sposób wdrażania kontenerów systemu Linux w klastrze usługi Service Fabric na platformie Azure. Po wykonaniu tych czynności będziesz mieć gotową aplikację do głosowania składającą się z frontonu internetowego w języku Python oraz zaplecza Redis działającego w klastrze usługi Service Fabric. Dowiesz się też, jak przenieść aplikację do trybu failover oraz jak skalować aplikację w klastrze.

![Strona internetowa aplikacji do głosowania][quickstartpic]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

1. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

2. Instalowanie [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

3. Zainstaluj [interfejs wiersza polecenia i zestaw SDK usługi Service Fabric](service-fabric-get-started-linux.md#installation-methods).

4. Instalowanie usługi [git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Pobieranie pakietu aplikacji

Do wdrożenia kontenerów w usłudze Service Fabric potrzebujesz zestawu plików manifestu (definicja aplikacji), które opisują poszczególne kontenery i aplikację.

W konsoli za pomocą usługi Git sklonuj kopię definicji aplikacji, a następnie przejdź do katalogu `Voting` w klonie.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Tworzenie klastra usługi Service Fabric

Aby wdrożyć aplikację na platformie Azure, potrzebujesz klastra usługi Service Fabric używanego do uruchamiania aplikacji. Następujące polecenia tworzą klaster pięciowęzłowy na platformie Azure.  To polecenie tworzy też certyfikat z podpisem własnym, dodaje go do magazynu kluczy i pobiera certyfikat lokalnie. Nowy certyfikat służy do zabezpieczania klastra podczas wdrażania i jest używany do uwierzytelniania klientów.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName='containertestcluster' 
ClusterName='containertestcluster' 
Location='eastus' 
Password='q6D7nN%6ck@6' 
Subject='containertestcluster.eastus.cloudapp.azure.com' 
VaultName='containertestvault' 
VmPassword='Mypa$$word!321'
VmUserName='sfadminuser'

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> Usługa frontonu internetowego została skonfigurowana do nasłuchiwania ruchu przychodzącego na porcie 80. Domyślnie port 80 jest otwarty w module równoważenia obciążenia platformy Azure i na maszynach wirtualnych klastra.
>

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Usługa Service Fabric udostępnia kilka narzędzi, których możesz używać do zarządzania klastrem i jego aplikacjami:

- Service Fabric Explorer — narzędzie oparte na przeglądarce.
- Interfejs wiersza polecenia usługi Service Fabric, który korzysta z interfejsu wiersza polecenia platformy Azure. 
- Polecenia programu PowerShell.

W tym przewodniku Szybki start jest używany interfejs wiersza polecenia usługi Service Fabric i (internetowe) narzędzie Service Fabric Explorer. Aby użyć narzędzia Service Fabric Explorer, musisz zaimportować do przeglądarki plik PFX certyfikatu. Domyślnie plik PFX nie ma hasła.

domyślną przeglądarką w systemie Ubuntu 16.04 jest Mozilla Firefox. Aby zaimportować certyfikat w przeglądarce Firefox, kliknij przycisk menu w prawym górnym rogu przeglądarki, a następnie kliknij pozycję **Opcje**. Na stronie **Preferencje** użyj pola wyszukiwania, aby wyszukać ciąg „certyfikaty”. Kliknij przycisk **Wyświetl certyfikaty**, wybierz kartę **Twoje certyfikaty**, kliknij pozycję **Importuj** i postępuj zgodnie z monitami, aby zaimportować certyfikat.

   ![Instalowanie certyfikatu w programie Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Wdrażanie aplikacji usługi Service Fabric

1. Połącz się z klastrem usługi Service Fabric na platformie Azure przy użyciu interfejsu wiersza polecenia. Punkt końcowy to punkt końcowy zarządzania klastrem. Plik PEM został utworzony w poprzedniej sekcji. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Użyj skryptu instalacji, aby skopiować definicję aplikacji do głosowania do klastra, zarejestrować typ aplikacji i utworzyć wystąpienie aplikacji.  Plik certyfikatu PEM powinien znajdować się w tym samym katalogu co plik *install.sh*.

    ```bash
    ./install.sh
    ```

3. Otwórz przeglądarkę internetową i przejdź do punktu końcowego narzędzia Service Fabric Explorer dla Twojego klastra. Punkt końcowy ma następujący format:  **https:// \<my-azure-service-fabric-cluster-url> : 19080/Explorer**; na przykład `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer` . </br>

4. Rozwiń węzeł **aplikacje** , aby zobaczyć, że istnieje teraz wpis dla typu aplikacji do głosowania i utworzonego wystąpienia.

    ![Service Fabric Explorer][sfx]

5. Aby nawiązać połączenie z uruchomionym kontenerem, otwórz przeglądarkę internetową i przejdź do adresu URL swojego klastra, na przykład `http://containertestcluster.eastus.cloudapp.azure.com:80`. W przeglądarce powinna zostać wyświetlona aplikacja do głosowania.

    ![Strona internetowa aplikacji do głosowania][quickstartpic]

> [!NOTE]
> Aplikacje usługi Service Fabric możesz także wdrożyć przy użyciu narzędzia Docker Compose. Na przykład następujące polecenie może służyć do wdrażania i instalowania aplikacji w klastrze przy użyciu narzędzia Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Przenoszenie kontenera do trybu failover w klastrze

Usługa Service Fabric zapewnia, że Twoje wystąpienia kontenera zostaną automatycznie przeniesione do innych węzłów w klastrze, jeśli wystąpi awaria. Można również ręcznie opróżnić węzeł na potrzeby kontenerów, a następnie bezproblemowo przejść do innych węzłów w klastrze. Usługa Service Fabric udostępnia kilka metod skalowania usług. W poniższych krokach będziesz używać narzędzia Service Fabric Explorer.

Aby przenieść kontener frontonu do trybu failover, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze, na przykład `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Kliknij węzeł **fabric:/Voting/azurevotefront** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID). W widoku drzewa zwróć uwagę na nazwę węzła przedstawiającą węzły, w których obecnie uruchomiono kontener, na przykład `_nodetype_1`.
3. Rozwiń węzeł **Węzły** w widoku drzewa. Kliknij wielokropek (...) obok węzła działającego w kontenerze.
4. Wybierz pozycję **Uruchom ponownie**, aby ponownie uruchomić ten węzeł, i potwierdź akcję ponownego uruchomienia. Ponowne uruchomienie powoduje przeniesienie kontenera do trybu failover w innym węźle klastra.

    ![Widok węzła w narzędziu Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalowanie aplikacji i usług w klastrze

Usługi Service Fabric można łatwo skalować w klastrze w celu uwzględnienia obciążenia w usługach. Skalowanie usługi odbywa się przez zmienianie liczby wystąpień uruchomionych w klastrze.

Aby skalować usługę internetową frontonu, wykonaj następujące czynności:

1. Otwórz narzędzie Service Fabric Explorer w klastrze, na przykład `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Kliknij wielokropek (trzy kropki) obok węzła **fabric:/Voting/azurevotefront** w widoku drzewa i wybierz pozycję **Skaluj usługę**.

    ![Rozpoczynanie skalowania usługi w narzędziu Service Fabric Explorer][containersquickstartscale]

    Teraz możesz skalować liczbę wystąpień usługi internetowej frontonu.

3. Zmień liczbę na **2** i kliknij pozycję **Skaluj usługę**.
4. Kliknij węzeł **fabric:/Voting/azurevotefront** w widoku drzewa i rozwiń węzeł partycji (reprezentowany przez identyfikator GUID).

    ![Zakończenie skalowania usługi w narzędziu Service Fabric Explorer][containersquickstartscaledone]

    Teraz możesz zobaczyć, że usługa ma dwa wystąpienia. W widoku drzewa możesz zobaczyć, w których węzłach uruchomiono wystąpienia.

Za pomocą tego prostego zadania zarządzania zostały podwojone zasoby dostępne dla usługi frontonu na potrzeby przetwarzania obciążenia użytkownika. Pamiętaj, że nie musisz mieć wielu wystąpień usługi, aby działała ona niezawodnie. W przypadku awarii usługa Service Fabric gwarantuje uruchomienie nowego wystąpienie usługi w klastrze.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Użyj skryptu odinstalowywania (uninstall.sh) udostępnionego w szablonie, aby usunąć wystąpienie aplikacji z klastra i wyrejestrować typ aplikacji. Ten skrypt wymaga pewnego czasu, aby wyczyścić wystąpienie, dlatego nie należy uruchamiać skryptu instalacji natychmiast po tym skrypcie. Narzędzia Service Fabric Explorer możesz użyć do określenia typu wyrejestrowanej aplikacji i tego, kiedy wystąpienie zostało usunięte.

```bash
./uninstall.sh
```

Najprostszym sposobem na usunięcie klastra i wszystkich wykorzystywanych przez niego zasobów jest usunięcie grupy zasobów.

Zaloguj się do platformy Azure i wybierz identyfikator subskrypcji, z którym chcesz usunąć klaster. Identyfikator subskrypcji można znaleźć po zalogowaniu się w witrynie Azure Portal. Usuń grupę zasobów i wszystkie zasoby klastra, korzystając z [polecenia AZ Group Delete](/cli/azure/group?view=azure-cli-latest).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Po zakończeniu pracy z klastrem, możesz usunąć certyfikat ze swojego magazynu certyfikatów. Na przykład:
- W systemie Windows: użyj [przystawki MMC certyfikatów](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Pamiętaj, aby wybrać pozycję **Moje konto użytkownika** podczas dodawania przystawki. Przejdź do pozycji `Certificates - Current User\Personal\Certificates` i usuń certyfikat.
- Na komputerze Mac: użyj aplikacji pęku kluczy.
- W systemie Ubuntu: wykonaj czynności, które zostały użyte do wyświetlenia certyfikatów, i usuń certyfikat.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start aplikacja kontenera systemu Linux została wdrożona w klastrze usługi Service Fabric na platformie Azure i przeniesiona do trybu failover oraz przeskalowano ją w klastrze. Aby dowiedzieć się więcej o pracy z kontenerami systemu Linux w usłudze Service Fabric, przejdź do samouczka dla aplikacji kontenera systemu Linux.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji kontenera systemu Linux](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
