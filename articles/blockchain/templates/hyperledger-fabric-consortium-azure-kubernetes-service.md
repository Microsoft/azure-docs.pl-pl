---
title: Wdróż konsorcjum sieci szkieletowej w usłudze Azure Kubernetes Service
description: Jak wdrożyć i skonfigurować sieć konsorcjum sieci szkieletowej w usłudze Azure Kubernetes Service
ms.date: 01/08/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: c0e7f3e7ab83f64cebd990de57d48c97891edb7f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897262"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Wdróż konsorcjum sieci szkieletowej w usłudze Azure Kubernetes Service

W celu wdrożenia i skonfigurowania sieci szkieletowej dla konsorcjum Fabric na platformie Azure można użyć szablonu Sieć szkieletowa w ramach usługi Azure Kubernetes Service (AKS).

Zapoznanie się z tym artykułem umożliwi:

- Posiadanie działającej wiedzy o sieci szkieletowej i składników, które tworzą bloki konstrukcyjne sieci szkieletowej łańcucha bloków.
- Dowiedz się, jak wdrażać i konfigurować sieć konsorcjum sieci szkieletowej w usłudze Azure Kubernetes na potrzeby scenariuszy produkcyjnych.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Wybierz rozwiązanie Azure łańcucha bloków

Przed rozpoczęciem korzystania z szablonu rozwiązania Porównaj swój scenariusz ze wspólnym przypadkiem użycia dostępnych opcji usługi Azure łańcucha bloków:

Opcja | Model usług | Typowy przypadek użycia
-------|---------------|-----------------
Szablony rozwiązań | IaaS | Szablony rozwiązań są Azure Resource Manager szablonów, których można użyć do aprowizacji w pełni skonfigurowanej topologii sieci łańcucha bloków. Szablony wdrażają i konfigurują Microsoft Azure usług obliczeniowych, sieciowych i magazynowych dla typu sieci łańcucha bloków. Szablony rozwiązań są udostępniane bez umowy dotyczącej poziomu usług. Aby uzyskać pomoc techniczną, użyj [strony Microsoft Q&](/answers/topics/azure-blockchain-workbench.html) .
[Usługa Azure Blockchain](../service/overview.md) | PaaS | Usługa Azure łańcucha bloków w wersji zapoznawczej upraszcza tworzenie, zarządzanie i nadzór nad sieciami łańcucha bloków konsorcjum. Usługa Azure łańcucha bloków Service umożliwia korzystanie z rozwiązań, które wymagają PaaS, zarządzania konsorcjum oraz prywatności umów i transakcji.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS i PaaS | Usługa Azure łańcucha bloków Workbench w wersji zapoznawczej to zbiór usług i funkcji platformy Azure, które ułatwiają tworzenie i wdrażanie aplikacji łańcucha bloków w celu udostępniania procesów i danych firmowych innym organizacjom. Użyj usługi Azure łańcucha bloków Workbench do prototypowania rozwiązania łańcucha bloków lub weryfikacji koncepcji dla aplikacji łańcucha bloków. Usługa Azure łańcucha bloków Workbench jest świadczona bez umowy dotyczącej poziomu usług. Aby uzyskać pomoc techniczną, użyj [strony Microsoft Q&](/answers/topics/azure-blockchain-workbench.html) .

## <a name="hyperledger-fabric-consortium-architecture"></a>Architektura konsorcjum sieci szkieletowej

Aby utworzyć sieć szkieletową z księgą w systemie Azure, należy wdrożyć usługę zamawiania i organizację z węzłami równorzędnymi. Za pomocą szablonu rozwiązania do obsługi sieci szkieletowej w usłudze Azure Kubernetes Service można tworzyć węzły kolejności lub węzły równorzędne. Należy wdrożyć szablon dla każdego węzła, który ma zostać utworzony.

Podstawowe składniki, które są tworzone w ramach wdrożenia szablonu są następujące:

- **Węzły programu orderer**: węzeł, który jest odpowiedzialny za porządkowanie transakcji w księdze. Wraz z innymi węzłami uporządkowane węzły tworzą usługę porządkowania sieci szkieletowej.

- **Węzły równorzędne**: węzeł, który głównie obsługuje księgi i inteligentne kontrakty, które są podstawowymi elementami sieci.

- **Urząd certyfikacji sieci szkieletowej**: Urząd certyfikacji (CA) dla sieci szkieletowej z księgą. Urząd certyfikacji sieci szkieletowej umożliwia zainicjowanie i uruchomienie procesu serwera, który hostuje urząd certyfikacji. Umożliwia zarządzanie tożsamościami i certyfikatami. Każdy klaster AKS wdrożony jako część szablonu będzie miał domyślnie ten urząd certyfikacji sieci szkieletowej.

- **CouchDB lub LevelDB**: światowe bazy danych stanu dla węzłów równorzędnych. LevelDB jest domyślną bazą danych stanu osadzoną w węźle równorzędnym. Przechowuje dane chaincode jako proste pary klucz/wartość i obsługuje tylko klucz, zakres kluczy i kwerendy klucza złożonego. CouchDB to opcjonalna alternatywna baza danych stanu, która obsługuje zaawansowane zapytania, gdy wartości danych chaincode są modelowane jako kod JSON.

Szablon w ramach wdrożenia umożliwia rozmieszczenie różnych zasobów platformy Azure w ramach subskrypcji. Wdrożone zasoby platformy Azure to:

- **Klaster AKS**: klaster usługi Azure Kubernetes, który jest skonfigurowany zgodnie z parametrami wejściowymi dostarczonymi przez klienta. Klaster AKS ma różne zasobniki skonfigurowane do uruchamiania składników sieci szkieletowej. Utworzone zasobniki to:

  - **Narzędzia sieci szkieletowej**: narzędzia odpowiedzialne za Konfigurowanie składników sieci szkieletowej.
  - **Zamówienie/elementy równorzędne**: węzły sieci szkieletowej.
  - **Serwer proxy**: serwer proxy NGNIX pod za pomocą którego aplikacje klienckie mogą komunikować się z klastrem AKS.
  - **Urząd certyfikacji sieci szkieletowej**: na tym komputerze, na którym działa urząd certyfikacji sieci szkieletowej.
- **PostgreSQL**: wystąpienie bazy danych, która przechowuje tożsamości urzędu certyfikacji sieci szkieletowej.

- **Magazyn kluczy**: wystąpienie usługi Azure Key Vault, która została wdrożona w celu zapisania poświadczeń urzędu certyfikacji sieci szkieletowej oraz certyfikatów głównych dostarczonych przez klienta. Magazyn jest używany w przypadku ponawiania próby wdrożenia szablonu, aby obsłużyć Mechanics szablonu.
- **Dysk zarządzany**: wystąpienie usługi Managed disks platformy Azure, która zapewnia trwały magazyn dla księgi i dla bazy danych stanu świata węzła równorzędnego.
- **Publiczny adres IP**: punkt końcowy klastra AKS wdrożony na potrzeby komunikacji z klastrem.

## <a name="deploy-the-orderer-and-peer-organization"></a>Wdróż organizację orderer i webpeer

Aby rozpocząć, musisz mieć subskrypcję platformy Azure, która może obsługiwać wdrażanie kilku maszyn wirtualnych i kont magazynu w warstwie Standardowa. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

Aby rozpocząć wdrażanie składników sieciowych w sieci szkieletowej, przejdź do [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Utwórz zasób**  >  **łańcucha bloków**, a następnie wyszukaj w **usłudze Azure Kubernetes Service (wersja zapoznawcza)**.

2. Wprowadź szczegóły projektu na karcie **podstawy** .

    ![Zrzut ekranu, na którym jest wyświetlana karta podstawy.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Wprowadź następujące informacje:
    - **Subskrypcja**: wybierz nazwę subskrypcji, w której chcesz wdrożyć składniki sieci szkieletowej.
    - **Grupa zasobów**: Utwórz nową grupę zasobów lub wybierz istniejącą pustą grupę zasobów. Grupa zasobów będzie przechowywać wszystkie zasoby wdrożone w ramach szablonu.
    - **Region**: Wybierz region platformy Azure, w którym chcesz wdrożyć klaster usługi Azure Kubernetes dla składników sieci szkieletowej. Szablon jest dostępny we wszystkich regionach, w których AKS jest dostępny. Wybierz region, w którym subskrypcja nie ma limitu przydziału maszyny wirtualnej (VM).
    - **Prefiks zasobu**: wprowadź prefiks do nazewnictwa wdrożonych zasobów. Musi zawierać mniej niż sześć znaków, a kombinacja znaków musi zawierać zarówno cyfry, jak i litery.
4. Wybierz kartę **Ustawienia sieci szkieletowej** , aby zdefiniować składniki sieci szkieletowej, które zostaną wdrożone.

    ![Zrzut ekranu przedstawiający kartę Ustawienia sieci szkieletowej.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Wprowadź następujące informacje:
    - **Nazwa organizacji**: Wprowadź nazwę organizacji sieci szkieletowej transakcji, która jest wymagana dla różnych operacji na płaszczyźnie danych. Nazwa organizacji musi być unikatowa dla każdego wdrożenia.
    - **Składnik sieci szkieletowej**: wybierz pozycję **porządkowanie usług** lub **węzłów równorzędnych** w oparciu o składnik sieci łańcucha bloków, który chcesz skonfigurować.
    - **Liczba węzłów**: poniższe dwa typy węzłów:
        - **Porządkowanie usługi**: Wybierz liczbę węzłów, aby zapewnić odporność na uszkodzenia sieci. Obsługiwana liczba węzłów zamówienia to 3, 5 i 7.
        - **Węzły równorzędne**: można wybrać od 1 do 10 węzłów na podstawie wymagań.
    - **Baza danych stanu węzła równorzędnego**: Wybierz między LevelDB i CouchDB. To pole jest wyświetlane po wybraniu **węzła równorzędnego** na liście rozwijanej **składnik sieci szkieletowej** .
    - **Nazwa użytkownika urzędu sieci szkieletowej**: Wprowadź nazwę użytkownika używaną do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Hasło urzędu sieci szkieletowej**: wprowadź hasło do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Potwierdź hasło**: Potwierdź hasło urzędu sieci szkieletowej.
    - **Certyfikaty**: Jeśli chcesz użyć własnych certyfikatów głównych, aby zainicjować urząd certyfikacji sieci szkieletowej, a następnie wybierz opcję **Przekaż certyfikat główny dla urzędu certyfikacji sieci szkieletowej** . W przeciwnym razie urząd certyfikacji sieci szkieletowej domyślnie tworzy certyfikaty z podpisem własnym.
    - **Certyfikat główny**: Przekaż certyfikat główny (klucz publiczny), z którym musi zostać zainicjowany urząd certyfikacji sieci szkieletowej. Obsługiwane są certyfikaty formatu PEM. Certyfikaty powinny być prawidłowe i znajdować się w strefie czasowej UTC.
    - **Klucz prywatny certyfikatu głównego**: Przekaż klucz prywatny certyfikatu głównego. Jeśli masz certyfikat. pem, który ma połączony klucz publiczny i prywatny, przekaż go również w tym miejscu.


6. Wybierz kartę **Ustawienia klastra AKS** , aby zdefiniować konfigurację klastra usługi Azure Kubernetes, która jest podstawową infrastrukturą, w której zostaną skonfigurowane składniki sieci szkieletowej.

    ![Zrzut ekranu przedstawiający kartę Ustawienia klastra K S.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

7. Wprowadź następujące informacje:
    - **Nazwa klastra Kubernetes**: Zmień nazwę klastra AKS, jeśli to konieczne. To pole jest wstępnie wypełniane na podstawie podanego prefiksu zasobu.
    - **Wersja Kubernetes**: Wybierz wersję Kubernetes, która zostanie wdrożona w klastrze. W zależności od regionu wybranego na karcie **podstawowe** dostępne wersje mogą ulec zmianie.
    - **Prefiks DNS**: wprowadź prefiks nazwy systemu nazw domen (DNS) dla klastra AKS. Użyjesz usługi DNS do nawiązywania połączenia z interfejsem API Kubernetes podczas zarządzania kontenerami po utworzeniu klastra.
    - **Rozmiar węzła**: rozmiar węzła Kubernetes można wybrać z listy jednostek składowania maszyn wirtualnych (SKU) dostępnych na platformie Azure. W celu uzyskania optymalnej wydajności zalecamy użycie standardowego DS3 v2.
    - **Liczba węzłów**: wprowadź liczbę węzłów Kubernetes, które mają zostać wdrożone w klastrze. Zaleca się pozostawienie tej liczby węzłów równej lub większej niż liczba węzłów sieci szkieletowej w ramach księgi głównej określonych na karcie **Ustawienia sieci szkieletowej** .
    - **Identyfikator klienta nazwy głównej usługi**: Wprowadź identyfikator klienta istniejącej jednostki usługi lub Utwórz nową. Nazwa główna usługi jest wymagana do uwierzytelniania AKS. Zapoznaj się z [instrukcjami tworzenia nazwy głównej usługi](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal).
    - **Klucz tajny klienta jednostki usługi**: Wprowadź klucz tajny klienta w jednostce usługi podanej w identyfikatorze klienta dla jednostki usługi.
    - **Potwierdź klucz tajny klienta**: Potwierdź klucz tajny klienta dla jednostki usługi.
    - **Włącz monitorowanie kontenera**: Wybierz, aby włączyć monitorowanie AKS, które umożliwia wypychanie dzienników AKS do określonego obszaru roboczego log Analytics.
    - **Obszar roboczy log Analytics**: obszar roboczy log Analytics zostanie wypełniony domyślnym obszarem roboczym, który został utworzony, jeśli monitorowanie jest włączone.

8. Wybierz kartę **Przegląd i tworzenie** . Ten krok wyzwala sprawdzanie poprawności dla podanych wartości.
9. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

    Wdrożenie zazwyczaj trwa od 10 do 12 minut. Czas może się różnić w zależności od rozmiaru i liczby określonych węzłów AKS.
10. Po pomyślnym wdrożeniu otrzymasz powiadomienie za pomocą powiadomień platformy Azure w prawym górnym rogu.
11. Wybierz pozycję **Przejdź do grupy zasobów** , aby sprawdzić wszystkie zasoby utworzone w ramach wdrożenia szablonu. Wszystkie nazwy zasobów rozpoczną się od prefiksu podanego na karcie **podstawy** .

## <a name="build-the-consortium"></a>Kompiluj konsorcjum

Aby skompilować konsorcjum łańcucha bloków po wdrożeniu usługi porządkowania i węzłów równorzędnych, wykonaj następujące kroki w kolejności. Skrypt sieci szkieletowej Azure (azhlf) pomaga w konfigurowaniu konsorcjum, tworzeniu kanału i wykonywaniu operacji chaincode.

> [!NOTE]
> Skrypt sieci szkieletowej Azure (azhlf) został zaktualizowany w celu zapewnienia większej funkcjonalności. Jeśli chcesz odwołać się do starego skryptu, zobacz [plik Readme w witrynie GitHub](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md). Ten skrypt jest zgodny z siecią szkieletową Kubernetes w wersji szablonu usługi Azure 2.0.0 i nowszych. Aby sprawdzić wersję wdrożenia, wykonaj kroki opisane w sekcji [Rozwiązywanie problemów](#troubleshoot).

> [!NOTE]
> Skrypt jest dostarczany, aby pomóc w tworzeniu tylko scenariuszy demonstracyjnych, deweloperskich i testowych. Kanał i konsorcjum tworzone przez ten skrypt mają podstawowe zasady dotyczące sieci szkieletowej w celu uproszczenia, programowania i testowania scenariuszy. W przypadku konfiguracji produkcyjnej Zalecamy zaktualizowanie zasad sieci szkieletowej kanału/konsorcjum zgodnie z wymaganiami dotyczącymi zgodności w organizacji przy użyciu natywnych interfejsów API sieci szkieletowej.


Wszystkie polecenia do uruchamiania skryptu sieci szkieletowej platformy Azure można wykonać za pomocą interfejsu wiersza polecenia (CLI) platformy Azure bash. Możesz zalogować się do Azure Cloud Shell za pomocą ![ opcji szablonu usługi Sieć szkieletowa w usłudze Azure Kubernetes w ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) prawym górnym rogu Azure Portal. W wierszu polecenia wpisz `bash` i wybierz klawisz ENTER, aby przełączyć się do interfejsu CLI bash, lub wybierz pozycję **bash** na pasku narzędzi Cloud Shell.

Aby uzyskać więcej informacji, zobacz [Azure Cloud Shell](../../cloud-shell/overview.md) .

![Zrzut ekranu przedstawiający polecenia w Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Na poniższej ilustracji przedstawiono krok po kroku proces tworzenia konsorcjum między organizacją programu orderer a organizacją równorzędną. Poniższe sekcje zawierają szczegółowe polecenia, aby wykonać te kroki.

![Diagram procesu, w którym ma zostać skompilowany konsorcjum.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Po zakończeniu początkowej konfiguracji Użyj aplikacji klienckiej, aby wykonać następujące operacje:  

- Zarządzanie kanałami
- Zarządzanie konsorcjum
- Chaincode Management

### <a name="download-client-application-files"></a>Pobierz pliki aplikacji klienckiej

Pierwszą konfiguracją jest pobranie plików aplikacji klienta. Uruchom następujące polecenia, aby pobrać wszystkie wymagane pliki i pakiety:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Te polecenia spowodują klonowanie kodu aplikacji klienta sieci szkieletowej platformy Azure z publicznej repozytorium GitHub, a następnie załadowanie wszystkich zależnych pakietów npm. Po pomyślnym wykonaniu polecenia zobaczysz folder node_modules w bieżącym katalogu. Wszystkie wymagane pakiety są ładowane w folderze node_modules.

### <a name="set-up-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Wszystkie zmienne środowiskowe są zgodne z konwencją nazewnictwa zasobów platformy Azure.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Ustawianie zmiennych środowiskowych dla klienta organizacji programu orderer

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Ustawianie zmiennych środowiskowych dla klienta w organizacji równorzędnej

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Na podstawie liczby organizacji równorzędnych w danym konsorcjum może być konieczne powtórzenie poleceń elementów równorzędnych i odpowiednie ustawienie zmiennej środowiskowej.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Ustawianie zmiennych środowiskowych dla konta usługi Azure Storage

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Użyj następujących poleceń, aby utworzyć konto usługi Azure Storage. Jeśli masz już konto usługi Azure Storage, Pomiń ten krok.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Użyj następujących poleceń, aby utworzyć udział plików na koncie usługi Azure Storage. Jeśli masz już udział plików, Pomiń ten krok.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Użyj następujących poleceń, aby wygenerować parametry połączenia dla udziału plików platformy Azure.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importowanie profilu połączenia z organizacją, tożsamości administratora i MSP

Użyj następujących poleceń, aby pobrać profil połączenia organizacji, tożsamość użytkownika administratora i dostawcę usług zarządzanych (MSP) z klastra usługi Azure Kubernetes i zapisać te tożsamości w lokalnym magazynie aplikacji klienta. Przykładem lokalnego magazynu jest katalog *azhlfTool/* Stores.

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

### <a name="create-a-channel"></a>Tworzenie kanału

Z poziomu klienta organizacji programu orderer Użyj następującego polecenia, aby utworzyć kanał, który zawiera tylko organizację programu orderer.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Dodawanie organizacji równorzędnej do zarządzania konsorcjum

>[!NOTE]
> Przed rozpoczęciem pracy z każdą operacją konsorcjum upewnij się, że ukończono wstępne konfigurowanie aplikacji klienckiej.  

Uruchom następujące polecenia w danej kolejności, aby dodać organizację równorzędną w kanale i konsorcjum: 

1.  Z poziomu klienta organizacji równorzędnej Przekaż MSP organizacji równorzędnej w usłudze Azure Storage.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Z poziomu klienta organizacji programu orderer Pobierz MSP organizacji równorzędnej z usługi Azure Storage. Następnie wydaj polecenie, aby dodać organizację równorzędną w kanale i konsorcjum.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Z poziomu klienta organizacji programu orderer Przekaż profil połączenia programu orderer do usługi Azure Storage, aby organizacja równorzędna mogła łączyć się z węzłami programu orderer przy użyciu tego profilu połączenia.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Z poziomu klienta organizacji równorzędnej Pobierz profil połączenia programu orderer z usługi Azure Storage. Następnie uruchom polecenie, aby dodać węzły równorzędne w kanale.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Podobnie aby dodać więcej organizacji równorzędnych w kanale, zaktualizuj zmienne środowiskowe elementów równorzędnych zgodnie z wymaganą organizacją równorzędną i wykonaj kroki od 1 do 4.

### <a name="set-anchor-peers"></a>Ustawianie zakotwiczenia elementów równorzędnych

Z poziomu klienta organizacji równorzędnej Uruchom polecenie, aby ustawić kotwicę elementów równorzędnych dla organizacji równorzędnej w określonym kanale.

>[!NOTE]
> Przed uruchomieniem tego polecenia upewnij się, że w kanale jest dodana organizacja równorzędna przy użyciu poleceń zarządzania konsorcjum.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` jest rozdzielaną spacją listą węzłów równorzędnych, które mają być ustawiane jako zakotwiczenie elementu równorzędnego. Na przykład:

  - Ustaw `<anchorPeersList>` jako, `"peer1"` Jeśli chcesz ustawić tylko węzeł Peer1 jako zakotwiczenie elementu równorzędnego.
  - Ustaw `<anchorPeersList>` jako, `"peer1" "peer3"` Jeśli chcesz ustawić zarówno węzły Peer1, jak i peer3 jako elementy równorzędne zakotwiczenia.

## <a name="chaincode-management-commands"></a>Polecenia zarządzania Chaincode

>[!NOTE]
> Przed rozpoczęciem pracy z dowolną operacją chaincode upewnij się, że wykonano początkową konfigurację aplikacji klienckiej.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Ustawianie zmiennych środowiskowych specyficznych dla chaincode

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Zainstaluj chaincode  

Uruchom następujące polecenie, aby zainstalować chaincode w organizacji równorzędnej.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Polecenie zainstaluje chaincode na wszystkich węzłach równorzędnych zestawu organizacji równorzędnych w `ORGNAME` zmiennej środowiskowej. Jeśli co najmniej dwie organizacje równorzędne znajdują się w kanale i chcesz zainstalować chaincode na wszystkich z nich, Uruchom to polecenie oddzielnie dla każdej organizacji równorzędnej.  

Wykonaj następujące kroki:  

1.  Ustaw `ORGNAME` i zgodnie z parametrem `USER_IDENTITY` `peerOrg1` i uruchom `./azhlf chaincode install` polecenie.  
2.  Ustaw `ORGNAME` i zgodnie z parametrem `USER_IDENTITY` `peerOrg2` i uruchom `./azhlf chaincode install` polecenie.  

### <a name="instantiate-chaincode"></a>Tworzenie wystąpienia chaincode  

W aplikacji klienckiej równorzędnej Uruchom następujące polecenie, aby utworzyć wystąpienie chaincode w kanale.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Przekaż nazwę funkcji tworzenia wystąpień i listę oddzielonych spacjami argumentów w `<instantiateFunc>` i `<instantiateFuncArgs>` odpowiednio. Na przykład, aby utworzyć wystąpienie chaincode_example02. go chaincode, `<instantiateFunc>` Ustaw `init` na `<instantiateFuncArgs>` i `"a" "2000" "b" "1000"` .

Możesz również przekazać plik JSON konfiguracji kolekcji przy użyciu `--collections-config` flagi. Lub ustaw argumenty przejściowe przy użyciu `-t` flagi podczas tworzenia wystąpienia chaincode używanych dla transakcji prywatnych.

Na przykład:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>`Część jest ścieżką do pliku JSON, który zawiera kolekcje zdefiniowane dla tworzenia wystąpienia chaincode danych prywatnych. Plik JSON konfiguracji kolekcji przykładowej można znaleźć względem katalogu *azhlfTool* w następującej ścieżce: `./samples/chaincode/src/private_marbles/collections_config.json` .
Przekaż `<transientArgs>` jako prawidłowy kod JSON w formacie ciągu. Wszystkie znaki specjalne są wyprowadzane. Na przykład: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Uruchom polecenie jeden raz z dowolnej organizacji równorzędnej w kanale. Po pomyślnym przesłaniu transakcji do programu orderer program zamawiający dystrybuuje tę transakcję do wszystkich organizacji równorzędnych w kanale. Następnie Chaincode jest tworzone na wszystkich węzłach równorzędnych na wszystkich organizacjach równorzędnych w kanale.  

### <a name="invoke-chaincode"></a>Wywołaj chaincode  

Z poziomu klienta organizacji równorzędnej Uruchom następujące polecenie, aby wywołać funkcję chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Przekaż nazwę funkcji Invoke i rozdzieloną spacjami listę argumentów w  `<invokeFunction>`   i  `<invokeFuncArgs>`   odpowiednio. Kontynuując korzystanie z chaincode_example02. chaincode. Przejdź do przykładu, aby wykonać operację Invoke, ustaw  `<invokeFunction>`   na wartość  `invoke`   i  `<invokeFuncArgs>`   `"a" "b" "10"` .  

>[!NOTE]
> Uruchom polecenie jeden raz z dowolnej organizacji równorzędnej w kanale. Po pomyślnym przesłaniu transakcji do programu orderer program zamawiający dystrybuuje tę transakcję do wszystkich organizacji równorzędnych w kanale. Stan Światowy jest następnie aktualizowany na wszystkich węzłach równorzędnych wszystkich organizacji równorzędnych w kanale.  


### <a name="query-chaincode"></a>Chaincode zapytania  

Uruchom następujące polecenie, aby wysłać zapytanie do chaincode:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Zatwierdzanie elementów równorzędnych jest elementami równorzędnymi, gdzie chaincode jest zainstalowana i jest wywoływana do wykonania transakcji. Należy ustawić `<endorsingPeers>` , aby zawierała nazwy węzłów równorzędnych z bieżącej organizacji równorzędnej. Wyświetl listę elementów równorzędnych poświadczających dla danej kombinacji chaincode i kanału rozdzielonych spacjami. Na przykład: `-p "peer1" "peer3"`.

Jeśli używasz *azhlfTool* do instalacji chaincode, Przekaż wszystkie nazwy węzłów równorzędnych jako wartość do argumentu zatwierdzania elementu równorzędnego. Chaincode jest zainstalowany w każdym węźle równorzędnym dla tej organizacji. 

Przekaż nazwę funkcji zapytania i listę argumentów rozdzielonych spacjami w  `<queryFunction>`   i  `<queryFuncArgs>`   odpowiednio. Ponownie pobierając chaincode_example02. Przejdź chaincode jako odwołanie, aby wysłać zapytanie o wartość "a" w stanie światowym, ustaw  `<queryFunction>`   na  `query` i  `<queryArgs>` `"a"` .  

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="find-deployed-version"></a>Znajdź wdrożoną wersję

Uruchom następujące polecenia, aby znaleźć wersję wdrożenia szablonu. Ustaw zmienne środowiskowe zgodnie z grupą zasobów, w której szablon został wdrożony.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Poprawka poprzedniej wersji

Jeśli masz problemy z uruchamianiem programu chaincode na wszystkich wdrożeniach wersji szablonu poniżej v 3.0.0, wykonaj poniższe kroki, aby zastosować poprawkę do węzłów równorzędnych.

Pobierz skrypt wdrażania elementu równorzędnego.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Uruchom skrypt przy użyciu następującego polecenia zastępującego parametry dla elementu równorzędnego.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Zaczekaj, aż wszystkie węzły równorzędne zostaną pobrane. Można zawsze sprawdzić stan węzłów równorzędnych w innym wystąpieniu powłoki przy użyciu poniższego polecenia.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Aby zachować aktualność w przypadku ofert usług łańcucha bloków i informacji z zespołu inżynierów łańcucha bloków platformy Azure, odwiedź [Blog Azure łańcucha bloków](https://azure.microsoft.com/blog/topics/blockchain/).

Aby przekazać opinie o produkcie lub zażądać nowych funkcji, opublikuj lub zagłosuj na pomysł za pośrednictwem [forum opinii platformy Azure dla usługi łańcucha bloków](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Pomoc techniczna w społeczności

Zaangażuj się z specjalistami ds. inżynierów firmy Microsoft i usługi Azure łańcucha bloków Community:

- [Microsoft Q&stronie](/answers/topics/azure-blockchain-workbench.html) 
   
  Wsparcie inżynieryjne dla szablonów łańcucha bloków jest ograniczone do problemów z wdrażaniem.
- [Społeczność techniczna firmy Microsoft](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
