---
title: Konsorcjum sieci szkieletowej w ramach usługi Azure Kubernetes Service (AKS)
description: Jak wdrożyć i skonfigurować sieć szkieletową z systemem webledger w usłudze Azure Kubernetes Service
ms.date: 07/07/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 1e90eeccb015b4d5ef78b79297565ddde9cfa305
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081285"
---
# <a name="hyperledger-fabric-consortium-on-azure-kubernetes-service-aks"></a>Konsorcjum sieci szkieletowej w ramach usługi Azure Kubernetes Service (AKS)

Można użyć szablonu sieci szkieletowej (HLF) w szablonie usługi Azure Kubernetes Service (AKS) w celu wdrożenia i skonfigurowania sieci szkieletowej dla konsorcjum Fabric na platformie Azure.

Zapoznanie się z tym artykułem umożliwi:

- Uzyskaj praktyczną wiedzę na temat sieci szkieletowej i różne składniki tworzące bloki konstrukcyjne sieci szkieletowej łańcucha bloków.
- Dowiedz się, jak wdrożyć i skonfigurować konsorcjum sieci szkieletowej w usłudze Azure Kubernetes na potrzeby scenariuszy produkcyjnych.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Wybierz rozwiązanie Azure łańcucha bloków

Przed wybraniem użycia szablonu rozwiązania Porównaj swój scenariusz z typowymi przypadkami użycia dostępnych opcji usługi Azure łańcucha bloków.

Opcja | Model usług | Typowy przypadek użycia
-------|---------------|-----------------
Szablony rozwiązań | IaaS | Szablony rozwiązań są Azure Resource Manager szablonów, których można użyć do aprowizacji w pełni skonfigurowanej topologii sieci łańcucha bloków. Szablony wdrażają i konfigurują Microsoft Azure usług obliczeniowych, sieci i magazynu dla danego typu sieci łańcucha bloków. Szablony rozwiązań są udostępniane bez umowy dotyczącej poziomu usług. Aby uzyskać pomoc techniczną, Skorzystaj z [&stronie pytań](/answers/topics/azure-blockchain-workbench.html) i odpowiedzi.
[Azure Blockchain Service](../service/overview.md) | PaaS | Usługa Azure łańcucha bloków w wersji zapoznawczej upraszcza tworzenie, zarządzanie i nadzór nad sieciami łańcucha bloków konsorcjum. Korzystaj z usługi Azure łańcucha bloków Service, aby uzyskać rozwiązania wymagające PaaS, zarządzania konsorcjum oraz prywatności umów i transakcji.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS i PaaS | Usługa Azure łańcucha bloków Workbench w wersji zapoznawczej to zbiór usług i funkcji platformy Azure, które ułatwiają tworzenie i wdrażanie aplikacji łańcucha bloków w celu udostępniania procesów i danych firmowych innym organizacjom. Użyj usługi Azure łańcucha bloków Workbench do tworzenia prototypów rozwiązania łańcucha bloków lub weryfikacji koncepcji łańcucha blokówowej aplikacji. Usługa Azure Blockchain Workbench jest świadczona bez umowy dotyczącej poziomu usług. Aby uzyskać pomoc techniczną, Skorzystaj z [&stronie pytań](/answers/topics/azure-blockchain-workbench.html) i odpowiedzi.

## <a name="hyperledger-fabric-consortium-architecture"></a>Architektura konsorcjum sieci szkieletowej

Aby utworzyć sieć szkieletową z systemem Azure, należy wdrożyć usługę porządkowania i organizację z węzłami równorzędnymi. Różne podstawowe składniki, które są tworzone w ramach wdrożenia szablonu są następujące:

- **Węzły programu orderer**: węzeł, który jest odpowiedzialny za porządkowanie transakcji w księdze. Wraz z innymi węzłami uporządkowane węzły tworzą usługę porządkowania sieci szkieletowej.

- **Węzły równorzędne**: węzeł, który głównie obsługuje księgi i inteligentne kontrakty, te podstawowe elementy sieci.

- **Urząd certyfikacji sieci szkieletowej**: Urząd certyfikacji sieci szkieletowej jest urzędem certyfikacji dla sieci szkieletowej. Urząd certyfikacji sieci szkieletowej umożliwia zainicjowanie i uruchomienie procesu serwera, który jest hostem urzędu certyfikacji. Umożliwia zarządzanie tożsamościami i certyfikatami. Każdy klaster AKS wdrożony jako część szablonu będzie miał domyślnie ten urząd certyfikacji sieci szkieletowej.

- **CouchDB lub LevelDB**: ogólnoświatowa baza danych stanu dla węzłów równorzędnych może być przechowywana w LevelDB lub CouchDB. LevelDB to domyślna baza danych stanu osadzona w węźle równorzędnym, która przechowuje dane chaincode jako proste pary klucz-wartość i obsługuje tylko klucze, zakres kluczy i kwerendy klucza złożonego. CouchDB to opcjonalna alternatywna baza danych stanu, która obsługuje zaawansowane zapytania, gdy wartości danych chaincode są modelowane jako kod JSON.

Szablon w ramach wdrożenia umożliwia rozmieszczenie różnych zasobów platformy Azure w ramach subskrypcji. Do różnych wdrożonych zasobów platformy Azure należą:

- **Klaster AKS**: klaster usługi Azure Kubernetes skonfigurowany zgodnie z parametrami wejściowymi dostarczonymi przez klienta. Klaster AKS ma różne zasobniki skonfigurowane do uruchamiania składników sieci szkieletowej. Tworzone są różne zasobniki:

  - **Narzędzia sieci szkieletowej**: Narzędzie sieci szkieletowej jest odpowiedzialne za Konfigurowanie składników sieci szkieletowej.
  - **Zamówienie/elementy równorzędne**: węzły sieci HLF.
  - **Serwer proxy**: serwer proxy NGNIX pod za pomocą którego aplikacje klienckie mogą być interfejsem z klastrem AKS.
  - **Urząd certyfikacji sieci szkieletowej**: na tym komputerze, na którym działa urząd certyfikacji sieci szkieletowej.
- **PostgreSQL**: wystąpienie PostgreSQL zostało wdrożone w celu obsługi tożsamości urzędu certyfikacji sieci szkieletowej.

- **Magazyn kluczy Azure**: wystąpienie magazynu kluczy zostało wdrożone w celu zapisania poświadczeń urzędu certyfikacji sieci szkieletowej oraz certyfikatów głównych dostarczonych przez klienta, które są używane w przypadku ponawiania próby wdrożenia szablonu, aby obsłużyć Mechanics szablonu.
- **Dysk zarządzany przez platformę Azure**: dysk zarządzany na platformie Azure jest przeznaczony dla magazynu trwałego dla bazy danych stanu w świecie i węzła równorzędnego.
- **Publiczny adres IP**: publiczny punkt końcowy IP klastra AKS wdrożony do współdzielenia z klastrem.

## <a name="hyperledger-fabric-blockchain-network-setup"></a>Konfiguracja sieci szkieletowej webłańcucha bloków Network

Aby rozpocząć, musisz mieć subskrypcję platformy Azure, która może obsługiwać wdrażanie kilku maszyn wirtualnych i kont magazynu w warstwie Standardowa. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

Skonfiguruj sieć szkieletową webłańcucha bloków Network, wykonując następujące czynności:

- [Wdrażanie programu orderer/organizacja równorzędna](#deploy-the-ordererpeer-organization)
- [Kompiluj konsorcjum](#build-the-consortium)

## <a name="deploy-the-ordererpeer-organization"></a>Wdrażanie programu orderer/organizacja równorzędna

Aby rozpocząć wdrażanie składników sieciowych HLF, przejdź do [Azure Portal](https://portal.azure.com). Wybierz pozycję **Utwórz zasób > łańcucha bloków** > wyszukiwanie **w sieci szkieletowej w usłudze Azure Kubernetes**.

1. Wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie szablonu. Zostanie wyświetlona **usługa tworzenia sieci szkieletowej w usłudze Azure Kubernetes** .

    ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-fabric-aks.png)

2. Wprowadź szczegóły projektu na stronie **podstawy** .

    ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Wprowadź następujące wartości:
    - **Subskrypcja**: wybierz nazwę subskrypcji, w której chcesz wdrożyć składniki sieci HLF.
    - **Grupa zasobów**: Utwórz nową grupę zasobów lub wybierz istniejącą pustą grupę zasobów, a grupa zasobów będzie przechowywać wszystkie zasoby wdrożone w ramach szablonu.
    - **Region**: Wybierz region platformy Azure, w którym chcesz wdrożyć klaster usługi Azure Kubernetes dla składników HLF. Szablon jest dostępny we wszystkich regionach, w których dostępny jest AKS, upewnij się, że wybrano region, w którym subskrypcja nie powoduje limitu przydziału maszyny wirtualnej.
    - **Prefiks zasobu**: prefiks do nazewnictwa wdrożonych zasobów. Długość prefiksu zasobu musi być krótsza niż sześć znaków, a kombinacja znaków musi zawierać cyfry i litery.
4. Wybierz kartę **Ustawienia sieci szkieletowej** , aby zdefiniować składniki sieci HLF, które zostaną wdrożone.

    ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Wprowadź następujące wartości:
    - **Nazwa organizacji**: Nazwa organizacji sieci szkieletowej, która jest wymagana dla różnych operacji na płaszczyźnie danych. Nazwa organizacji musi być unikatowa dla każdego wdrożenia.
    - **Składnik sieci szkieletowej**: wybierz pozycję porządkowanie usługi lub węzłów równorzędnych na podstawie składnika sieci łańcucha bloków, który chcesz skonfigurować.
    - **Liczba węzłów** — następujące dwa typy węzłów:
        - Porządkowanie usługi — wybierz liczbę węzłów, w przypadku których podano odporność na uszkodzenia sieci. Tylko 3, 5 i 7 są obsługiwaną liczbą węzłów programu orderer.
        - Węzły równorzędne — można wybrać 1-10 węzłów na podstawie wymagań.
    - **Baza danych stanu węzła równorzędnego**: Wybierz między LevelDB i CoucbDB. To pole jest wyświetlane, gdy użytkownik wybierze węzeł równorzędny w liście rozwijanej składnik sieci szkieletowej.
    - **Nazwa użytkownika sieci szkieletowej**: Wprowadź nazwę użytkownika używaną do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Hasło urzędu sieci szkieletowej**: wprowadź hasło do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Potwierdź hasło**: Potwierdź hasło urzędu sieci szkieletowej.
    - **Certyfikaty**: Jeśli chcesz użyć własnych certyfikatów głównych w celu zainicjowania urzędu certyfikacji sieci szkieletowej, wybierz opcję Przekaż certyfikat główny dla sieci szkieletowej, a w przeciwnym razie domyślnie urząd certyfikacji sieci szkieletowej tworzy certyfikaty z podpisem własnym.
    - **Certyfikat główny**: Przekaż certyfikat główny (klucz publiczny), z którym musi zostać zainicjowany urząd certyfikacji sieci szkieletowej. Certyfikaty formatu PEM są obsługiwane, certyfikaty powinny być prawidłowe w strefie czasowej UTC.
    - **Klucz prywatny certyfikatu głównego**: Przekaż klucz prywatny certyfikatu głównego. Jeśli masz certyfikat. pem, który ma połączony klucz publiczny i prywatny, przekaż go również w tym miejscu.


6. Wybierz kartę **Ustawienia klastra AKS** , aby zdefiniować konfigurację klastra usługi Azure Kubernetes, która jest podstawową infrastrukturą, w której zostaną skonfigurowane składniki sieci szkieletowej.

    ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Wprowadź następujące wartości:
    - **Nazwa klastra Kubernetes**: Nazwa utworzonego klastra AKS. To pole jest wstępnie wypełniane na podstawie podanego prefiksu zasobu, w razie potrzeby można je zmienić.
    - **Wersja Kubernetes**: wersja Kubernetes, która zostanie wdrożona w klastrze. W oparciu o region wybrany na karcie **podstawowe** dostępne wersje mogą ulec zmianie.
    - **Prefiks DNS**: prefiks nazwy systemu nazw domen (DNS) dla klastra AKS. Usługa DNS umożliwia łączenie się z interfejsem API Kubernetes podczas zarządzania kontenerami po utworzeniu klastra.
    - **Rozmiar węzła**: rozmiar węzła Kubernetes można wybrać z listy jednostek składowania maszyn wirtualnych dostępnych na platformie Azure. W celu uzyskania optymalnej wydajności zalecamy użycie standardowego DS3 v2.
    - **Liczba węzłów**: liczba węzłów Kubernetes, które mają zostać wdrożone w klastrze. Zaleca się pozostawienie tej liczby węzłów co najmniej równej liczbie węzłów HLF określonych w ustawieniach sieci szkieletowej.
    - **Identyfikator klienta nazwy głównej usługi**: Wprowadź identyfikator klienta istniejącej jednostki usługi lub Utwórz nową, która jest wymagana do uwierzytelniania AKS. Zobacz, kroki [tworzenia nazwy głównej usługi](/powershell/azure/create-azure-service-principal-azureps?view=azps-3.2.0#create-a-service-principal).
    - **Klucz tajny klienta jednostki usługi**: Wprowadź klucz tajny klienta w jednostce usługi podanej w identyfikatorze klienta nazwy głównej usługi.
    - **Potwierdź klucz tajny klienta**: Potwierdź klucz tajny klienta podany w kluczu tajnym klienta jednostki usługi.
    - **Włącz monitorowanie kontenera**: Wybierz, aby włączyć monitorowanie AKS, które umożliwia wypychanie dzienników AKS do określonego obszaru roboczego log Analytics.
    - **Obszar roboczy log Analytics**: obszar roboczy usługi log Analytics zostanie wypełniony domyślnym obszarem roboczym, który jest tworzony w przypadku włączenia monitorowania.

8. Po wprowadzeniu wszystkich powyższych szczegółów wybierz pozycję **Przegląd i Utwórz** kartę. Przegląd i tworzenie wyzwalają weryfikację podanych wartości.
9. Po zakończeniu walidacji można wybrać pozycję **Utwórz**.
Wdrożenie zazwyczaj trwa 10-12 minut, może się różnić w zależności od rozmiaru i liczby określonych węzłów AKS.
10. Po pomyślnym wdrożeniu otrzymujesz powiadomienie za pomocą powiadomień platformy Azure w prawym górnym rogu.
11. Wybierz pozycję **Przejdź do grupy zasobów** , aby sprawdzić wszystkie zasoby utworzone w ramach wdrożenia szablonu. Wszystkie nazwy zasobów rozpoczną się od prefiksu podanego w ustawieniach **podstawy** .

## <a name="build-the-consortium"></a>Kompiluj konsorcjum

Aby skompilować łańcucha bloków konsorcjum do wdrożenia usługi porządkowania i węzłów równorzędnych, należy wykonać poniższe kroki w kolejności. Usługa Azure HLF Script (azhlf), która pomaga w konfigurowaniu konsorcjum, tworzeniu kanału i chaincode operacji.

> [!NOTE]
> W skrypcie istnieje aktualizacja, która ma na celu zapewnienie większej funkcjonalności za pomocą skryptu HLF platformy Azure. Jeśli chcesz odwołać się do starego skryptu, [Zobacz tutaj](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Ten skrypt jest zgodny z siecią szkieletową w witrynie Azure Kubernetes Service w wersji 2.0.0 lub nowszej. Aby sprawdzić wersję wdrożenia, wykonaj kroki opisane w sekcji [Rozwiązywanie problemów](#troubleshoot).

> [!NOTE]
> Udostępniony skrypt usługi Azure HLF (azhlf) ma pomóc tylko w scenariuszach demonstracyjnych/DevTest. W przypadku kanału i konsorcjum utworzonego przez ten skrypt istnieją podstawowe zasady HLF w celu uproszczenia scenariusza pokaz/DevTest. W przypadku konfiguracji produkcyjnej zalecamy aktualizowanie zasad HLFi kanału/konsorcjum zgodnie z wymaganiami zgodności organizacji przy użyciu natywnych interfejsów API HLF.


Wszystkie polecenia służące do uruchamiania skryptu Azure HLF można wykonać za pomocą wiersza polecenia usługi Azure bash. Interfejs (CLI). Możesz zalogować się do wersji sieci Web usługi Azure Shell za pomocą  ![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) Opcja w prawym górnym rogu Azure Portal. W wierszu polecenia wpisz bash i ENTER, aby przełączyć się do interfejsu CLI bash.

Aby uzyskać więcej informacji, zobacz temat [Azure Shell](../../cloud-shell/overview.md) .

![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Na poniższej ilustracji przedstawiono proces krok po kroku dotyczący tworzenia konsorcjum między organizacją programu orderer a organizacją równorzędną. Szczegółowe polecenia służące do wykonywania tych kroków są przechwytywane w poniższych sekcjach.

![Szablon sieci szkieletowej w usłudze Azure Kubernetes](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Wykonaj poniższe polecenia, aby skonfigurować początkową aplikację kliencką: 

1.  [Pobierz pliki aplikacji klienckiej](#download-client-application-files)
2.  [Ustawianie zmiennych środowiskowych](#setup-environment-variables)
3.  [Importuj profil połączenia organizacji, użytkownika administracyjnego i MSP](#import-organization-connection-profile-admin-user-identity-and-msp)

Po zakończeniu konfiguracji początkowej możesz użyć aplikacji klienckiej, aby wykonać poniższe operacje:  

- [Polecenia zarządzania kanałem](#channel-management-commands)
- [Polecenia zarządzania konsorcjum](#consortium-management-commands)
- [Polecenia zarządzania Chaincode](#chaincode-management-commands)

### <a name="download-client-application-files"></a>Pobierz pliki aplikacji klienckiej

Pierwszą konfiguracją jest pobranie plików aplikacji klienta. Wykonaj poniższe polecenie, aby pobrać wszystkie wymagane pliki i pakiety:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup

```
Te polecenia spowodują klonowanie kodu aplikacji klienckiej platformy Azure HLF z publicznego repozytorium GitHub, a następnie załadowanie wszystkich zależnych pakietów npm. Po pomyślnym wykonaniu polecenia zobaczysz folder node_modules w bieżącym katalogu. Wszystkie wymagane pakiety są ładowane w folderze node_modules.


### <a name="setup-environment-variables"></a>Ustawianie zmiennych środowiskowych

> [!NOTE]
> Wszystkie zmienne środowiskowe są zgodne z konwencją nazewnictwa zasobów platformy Azure.


**Ustaw poniższe zmienne środowiskowe dla klienta organizacji programu orderer**


```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```
**Ustaw poniższe zmienne środowiskowe dla klienta organizacji równorzędnej**

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

> [!NOTE]
> W oparciu o liczbę organizacje równorzędnych w konsorcjum, może być konieczne powtórzenie poleceń elementów równorzędnych i odpowiednie ustawienie zmiennej środowiskowej.

**Ustaw następujące zmienne środowiskowe w celu skonfigurowania konta usługi Azure Storage**

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Postępuj zgodnie z poniższymi krokami, aby utworzyć konto usługi Azure Storage. Jeśli masz już utworzone konto usługi Azure Storage, Pomiń następujące kroki

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Postępuj zgodnie z poniższymi krokami, aby utworzyć udział plików na koncie usługi Azure Storage. Jeśli masz już utworzony udział plików, Pomiń te kroki

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Wykonaj poniższe kroki, aby wygenerować parametry połączenia udziału plików platformy Azure

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-organization-connection-profile-admin-user-identity-and-msp"></a>Importuj profil połączenia organizacji, tożsamość użytkownika administracyjnego i MSP

Wystaw poniższe polecenia, aby pobrać profil połączenia organizacji, tożsamość użytkownika administracyjnego i MSP z klastra usługi Azure Kubernetes i zapisać te tożsamości w lokalnym magazynie aplikacji klienta, np. w katalogu "azhlfTool/Stores".

W przypadku organizacji programu orderer:

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

W przypadku organizacji równorzędnej:

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="channel-management-commands"></a>Polecenia zarządzania kanałem

> [!NOTE]
> Przed rozpoczęciem pracy z dowolną operacją kanału upewnij się, że jest wykonywana początkowa konfiguracja aplikacji klienckiej.  

Poniżej przedstawiono dwa polecenia zarządzania kanałem:

1. [Utwórz kanał — polecenie](#create-channel-command)
2. [Ustawienie polecenia zakotwiczenia elementów równorzędnych](#setting-anchor-peers-command)


#### <a name="create-channel-command"></a>Utwórz kanał — polecenie

Z klienta organizacji programu orderer, wydaj polecenie, aby utworzyć nowy kanał. To polecenie spowoduje utworzenie kanału z tylko organizacją programu orderer.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

#### <a name="setting-anchor-peers-command"></a>Ustawienie polecenia zakotwiczenia elementów równorzędnych
Z poziomu klienta organizacji równorzędnej, wystaw poniżej polecenie, aby ustawić elementy równorzędne zakotwiczone dla organizacji równorzędnej w określonym kanale.

>[!NOTE]
> Przed wykonaniem tego polecenia upewnij się, że w kanale zostanie dodana organizacja równorzędna korzystająca z poleceń zarządzania konsorcjum.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY
```

`<anchorPeersList>`jest rozdzielaną spacją listą węzłów równorzędnych, które mają być ustawiane jako zakotwiczenie elementu równorzędnego. Przykład:

  - Ustaw `<anchorPeersList>` jako "Peer1", jeśli chcesz ustawić tylko węzeł Peer1 jako zakotwiczenie elementu równorzędnego.
  - Ustaw `<anchorPeersList>` jako "Peer1" "peer3", jeśli chcesz ustawić zarówno węzeł Peer1, jak i peer3 jako zakotwiczenie elementu równorzędnego.

### <a name="consortium-management-commands"></a>Polecenia zarządzania konsorcjum

>[!NOTE]
> Przed rozpoczęciem pracy z każdą operacją konsorcjum upewnij się, że jest wykonywana początkowa konfiguracja aplikacji klienckiej.  

Wykonaj poniższe polecenia w danej kolejności, aby dodać organizację równorzędną w kanale i konsorcjum
1.  W obszarze klient organizacji równorzędnej Przekaż MSP organizacja równorzędna do usługi Azure Storage

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Z poziomu klienta organizacji programu orderer Pobierz organizację równorzędną MSP z usługi Azure Storage, a następnie wydaj polecenie, aby dodać organizację równorzędną w kanale/konsorcjum.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Z klienta organizacji programu orderer, Przekaż profil połączenia programu orderer do usługi Azure Storage, aby organizacja równorzędna mogła łączyć się z węzłami programu orderer przy użyciu tego profilu połączenia

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Z poziomu klienta organizacji równorzędnej Pobierz profil połączenia programu orderer z usługi Azure Storage, a następnie wydaj polecenie, aby dodać węzły równorzędne w kanale

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Podobnie aby dodać więcej organizacji równorzędnych w kanale, zaktualizuj zmienne środowiskowe elementów równorzędnych zgodnie z wymaganą organizacją równorzędną i wykonaj kroki od 1 do 4.


### <a name="chaincode-management-commands"></a>Polecenia zarządzania Chaincode

>[!NOTE]
> Przed rozpoczęciem pracy z dowolną operacją chaincode upewnij się, że jest wykonywana początkowa konfiguracja aplikacji klienckiej.  

**Ustaw następujące zmienne środowiskowe chaincode**

```bash
# peer organization name where chaincode operation is to be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang' and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is place.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode is to be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

Następujące operacje chaincode można wykonać:  

- [Zainstaluj chaincode](#install-chaincode)  
- [Tworzenie wystąpienia chaincode](#instantiate-chaincode)  
- [Wywołaj chaincode](#invoke-chaincode)
- [Chaincode zapytania](#query-chaincode)


### <a name="install-chaincode"></a>Zainstaluj chaincode  

Wykonaj poniższe polecenie, aby zainstalować chaincode w organizacji równorzędnej.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Zainstaluje chaincode na wszystkich węzłach równorzędnych zestawu organizacji równorzędnych w zmiennej środowiskowej ORGNAME. Jeśli w kanale znajdują się co najmniej dwie organizacje równorzędne i chcesz zainstalować chaincode na wszystkich z nich, wykonaj to polecenie oddzielnie dla każdej organizacji równorzędnej.  

Wykonaj następujące czynności:  

1.  Ustaw `ORGNAME` i `USER_IDENTITY` zgodnie z peerOrg1 i wydaj `./azhlf chaincode install` polecenie.  
2.  Ustaw `ORGNAME` i `USER_IDENTITY` zgodnie z peerOrg2 i wydaj `./azhlf chaincode install` polecenie.  

### <a name="instantiate-chaincode"></a>Tworzenie wystąpienia chaincode  

Z aplikacji klienckiej równorzędnej wykonaj poniższe polecenie, aby utworzyć wystąpienie chaincode w kanale.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -v $CC_VERSION -l $CC_LANG -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>  
```
Przekaż nazwę funkcji tworzenia wystąpień i listę oddzielonych spacjami argumentów w `<instantiateFunc>` i `<instantiateFuncArgs>` odpowiednio. Na przykład w chaincode_example02. Przejdź do chaincode, aby utworzyć wystąpienie chaincode ustawione `<instantiateFunc>` na `init` i `<instantiateFuncArgs>` na wartość "a" "2000" "b" "1000".

> [!NOTE]
> Wykonaj polecenie dla raz z dowolnej organizacji równorzędnej w kanale. Po pomyślnym przesłaniu transakcji do programu orderer program zamawiający dystrybuuje tę transakcję do wszystkich organizacji równorzędnych w kanale. W związku z tym chaincode jest tworzona na wszystkich węzłach równorzędnych na wszystkich organizacjach równorzędnych w kanale.  


### <a name="invoke-chaincode"></a>Wywołaj chaincode  

Z poziomu klienta organizacji równorzędnej wykonaj poniższe polecenie, aby wywołać funkcję chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Przekaż nazwę funkcji wywołania i listę oddzielonych spacjami argumentów w  `<invokeFunction>`   i  `<invokeFuncArgs>`   odpowiednio. Kontynuując korzystanie z chaincode_example02. chaincode. Przejdź do przykładu, aby wykonać operację Invoke ustawioną  `<invokeFunction>`   na  `invoke`   i  `<invokeFuncArgs>`   "a" "b" "10".  

>[!NOTE]
> Wykonaj polecenie dla raz z dowolnej organizacji równorzędnej w kanale. Po pomyślnym przesłaniu transakcji do programu orderer program zamawiający dystrybuuje tę transakcję do wszystkich organizacji równorzędnych w kanale. W związku z tym stan świata zostanie zaktualizowany we wszystkich węzłach równorzędnych w kanale.  


### <a name="query-chaincode"></a>Chaincode zapytania  

Wykonaj poniższe polecenie, aby wysłać zapytanie do chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs>  
```
Przekaż listę argumentów Nazwa funkcji zapytania i rozdzieloną spacjami w  `<queryFunction>`   i  `<queryFuncArgs>`   odpowiednio. Ponownie, pobierając chaincode_example02. chaincode jako odwołanie, aby zbadać wartość "a" w stanie świecie ustawionym  `<queryFunction>`   na  `query` i  `<queryArgs>` na "a".  

## <a name="troubleshoot"></a>Rozwiąż problemy

**Aby sprawdzić wersję uruchomionego szablonu**

Uruchom poniższe polecenia, aby znaleźć wersję wdrożenia szablonu.

Ustaw poniższe zmienne środowiskowe jako dla grupy zasobów, w której szablon został wdrożony.

```bash

SWITCH_TO_AKS_CLUSTER() { az aks get-credentials --resource-group $1 --name $2 --subscription $3; }
AKS_CLUSTER_SUBSCRIPTION=<AKSClusterSubscriptionID>
AKS_CLUSTER_RESOURCE_GROUP=<AKSClusterResourceGroup>
AKS_CLUSTER_NAME=<AKSClusterName>
```
Uruchom następujące polecenie, aby wydrukować wersję szablonu
```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3

```

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

W przypadku usługi Azure łańcucha bloków News odwiedź [blog usługi Azure łańcucha bloków](https://azure.microsoft.com/blog/topics/blockchain/) , aby zachować aktualność w zakresie usług łańcucha bloków i uzyskać informacje od zespołu inżynierów ds. platformy Azure łańcucha bloków.

Aby przekazać opinie o produkcie lub zażądać nowych funkcji, opublikuj lub zagłosuj na pomysł za pośrednictwem [forum opinii platformy Azure dla usługi łańcucha bloków](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Pomoc techniczna w społeczności

Współpracuj z inżynierami firmy Microsoft i ekspertów społeczności Azure łańcucha bloków.

- [Microsoft Q&strony pytania](/answers/topics/azure-blockchain-workbench.html). Wsparcie inżynieryjne dla szablonów łańcucha bloków jest ograniczone do problemów z wdrażaniem.
- [Witryna Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
