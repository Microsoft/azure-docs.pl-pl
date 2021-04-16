---
title: Wdrażanie Hyperledger Fabric konsorcjum na Azure Kubernetes Service
description: Jak wdrożyć i skonfigurować sieć Hyperledger Fabric konsorcjum na Azure Kubernetes Service
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: 03f19d1922c011c1b5304b66488e9fa8de703bf9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478321"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>Wdrażanie Hyperledger Fabric konsorcjum na Azure Kubernetes Service

Szablon usługi Hyperledger Fabric on Azure Kubernetes Service (AKS) umożliwia wdrożenie i skonfigurowanie sieci Hyperledger Fabric konsorcjum na platformie Azure.

Zapoznanie się z tym artykułem umożliwi:

- Posiadaj wiedzę na temat Hyperledger Fabric i składników, które tworzą bloki konstrukcyjne Hyperledger Fabric łańcucha bloków.
- Wiesz, jak wdrożyć i skonfigurować sieć Hyperledger Fabric konsorcjum na Azure Kubernetes Service dla scenariuszy produkcyjnych.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Wybieranie Azure Blockchain rozwiązania

Zanim zdecydujesz się użyć szablonu rozwiązania, porównaj swój scenariusz z najczęściej występującymi przypadkami użycia dostępnych Azure Blockchain opcji:

Opcja | Model usług | Wspólny przypadek użycia
-------|---------------|-----------------
Szablony rozwiązań | IaaS | Szablony rozwiązań to Azure Resource Manager, których można użyć do aprowizować w pełni skonfigurowaną topologię sieci łańcucha bloków. Szablony wdrażają i konfigurują Microsoft Azure obliczeniowe, sieciowe i magazynowe dla typu sieci łańcucha bloków. Szablony rozwiązań są udostępniane bez umowy poziomu usług. Użyj strony [Microsoft Q&A, aby](/answers/topics/azure-blockchain-workbench.html) uzyskać pomoc techniczną.
[Usługa Azure Blockchain](../service/overview.md) | PaaS | Azure Blockchain Service zapoznawcza upraszcza tworzenie i zarządzanie sieciami łańcucha bloków konsorcjum oraz zarządzanie nimi. Użyj Azure Blockchain Service rozwiązań, które wymagają rozwiązania PaaS, zarządzania konsorcjum lub prywatności kontraktów i transakcji.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS i PaaS | Azure Blockchain Workbench zapoznawcza to zbiór usług i możliwości platformy Azure, które ułatwiają tworzenie i wdrażanie aplikacji łańcucha bloków w celu udostępniania procesów biznesowych i danych innym organizacjom. Użyj Azure Blockchain Workbench do tworzenia prototypów rozwiązania łańcucha bloków lub weryfikacji koncepcji dla aplikacji łańcucha bloków. Azure Blockchain Workbench jest dostarczany bez umowy poziomu usług. Aby uzyskać [pomoc techniczną, skorzystaj ze](/answers/topics/azure-blockchain-workbench.html) strony&A usługi Microsoft Q.

## <a name="deploy-the-orderer-and-peer-organization"></a>Wdrażanie zamawiania i organizacji równorzędnej

Aby rozpocząć, potrzebujesz subskrypcji platformy Azure, która może obsługiwać wdrażanie kilku maszyn wirtualnych i standardowych kont magazynu. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

Aby rozpocząć wdrażanie składników Hyperledger Fabric, przejdź do Azure Portal [.](https://portal.azure.com)

1. Wybierz **pozycję Utwórz zasób Łańcuch**  >  **bloków,** a następnie wyszukaj Hyperledger Fabric na Azure Kubernetes Service **(wersja zapoznawcza).**

2. Wprowadź szczegóły projektu na **karcie Podstawowe** informacje.

    ![Zrzut ekranu przedstawiający kartę Podstawowe.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. Wprowadź następujące informacje:
    - **Subskrypcja:** wybierz nazwę subskrypcji, w której chcesz wdrożyć składniki Hyperledger Fabric sieci.
    - **Grupa zasobów:** utwórz nową grupę zasobów lub wybierz istniejącą pustą grupę zasobów. Grupa zasobów będzie zawierać wszystkie zasoby wdrożone w ramach szablonu.
    - **Region:** wybierz region platformy Azure, w którym chcesz wdrożyć klaster Azure Kubernetes Service dla Hyperledger Fabric klastra. Szablon jest dostępny we wszystkich regionach, w których jest dostępna usługi AKS. Wybierz region, w którym twoja subskrypcja nie ma limitu przydziału maszyny wirtualnej.
    - **Prefiks zasobu:** wprowadź prefiks nazewnictwa wdrożonych zasobów. Musi zawierać mniej niż sześć znaków, a kombinacja znaków musi zawierać zarówno cyfry, jak i litery.
4. Wybierz **kartę Ustawienia sieci szkieletowej,** aby zdefiniować Hyperledger Fabric sieciowe, które zostaną wdrożone.

    ![Zrzut ekranu przedstawiający kartę Ustawienia sieci szkieletowej.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. Wprowadź następujące informacje:
    - **Nazwa organizacji:** wprowadź nazwę organizacji Hyperledger Fabric, która jest wymagana dla różnych operacji płaszczyzny danych. Nazwa organizacji musi być unikatowa dla każdego wdrożenia.
    - **Składnik sieci szkieletowej:** wybierz pozycję **Usługa** zamawiania lub Węzły równorzędne **na** podstawie składnika sieci łańcucha bloków, który chcesz skonfigurować.
    - **Liczba węzłów:** dwa typy węzłów są następujące:
        - **Usługa zamawiania:** Węzły odpowiedzialne za kolejność transakcji w rejestrach. Wybierz liczbę węzłów, aby zapewnić odporność na uszkodzenia sieci. Obsługiwana liczba węzłów w kolejności to 3, 5 i 7.
        - **Węzły równorzędne:** węzły, które hostują rejestry i kontrakty inteligentne. W zależności od wymagań można wybrać od 1 do 10 węzłów.
    - **Równorzędna baza danych stanu świata węzła:** bazy danych stanu świata dla węzłów równorzędnych. LevelDB to domyślna baza danych stanu osadzona w węźle równorzędym. Przechowuje dane kodu łańcuchowego jako proste pary klucz/wartość i obsługuje tylko zapytania dotyczące klucza, zakresu kluczy i klucza złożonego. CouchDB to opcjonalna alternatywna baza danych stanu, która obsługuje rozbudowane zapytania, gdy wartości danych kodu łańcuchowego są modelowane jako dane JSON. To pole jest wyświetlane po wybraniu opcji Węzły **równorzędne** **z** listy rozwijanej Składnik sieci szkieletowej.
    - **Nazwa użytkownika urzędu certyfikacji** sieci szkieletowej: urząd certyfikacji sieci szkieletowej umożliwia zainicjowanie i uruchomienie procesu serwera, który hostuje urząd certyfikacji. Umożliwia zarządzanie tożsamościami i certyfikatami. Każdy klaster usługi AKS wdrożony w ramach szablonu będzie domyślnie miał zasobnik urzędu certyfikacji sieci szkieletowej. Wprowadź nazwę użytkownika używaną do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Hasło urzędu certyfikacji sieci szkieletowej:** wprowadź hasło do uwierzytelniania urzędu certyfikacji sieci szkieletowej.
    - **Potwierdź hasło:** Potwierdź hasło urzędu certyfikacji sieci szkieletowej.
    - **Certyfikaty:** jeśli chcesz użyć własnych certyfikatów głównych do zainicjowania urzędu certyfikacji sieci szkieletowej, wybierz opcję Przekaż certyfikat główny dla urzędu **certyfikacji sieci szkieletowej.** W przeciwnym razie urząd certyfikacji sieci szkieletowej domyślnie tworzy certyfikaty z podpisem własnym.
    - **Certyfikat główny:** przekaż certyfikat główny (klucz publiczny), za pomocą którego należy zainicjować urząd certyfikacji sieci szkieletowej. Certyfikaty w formacie PEM są obsługiwane. Certyfikaty powinny być prawidłowe i w strefie czasowej UTC.
    - **Klucz prywatny certyfikatu głównego:** przekaż klucz prywatny certyfikatu głównego. Jeśli masz certyfikat PEM z połączonym kluczem publicznym i prywatnym, przekaż go również tutaj.


6. Wybierz **kartę Ustawienia klastra usługi AKS,** aby zdefiniować Azure Kubernetes Service konfiguracji klastra. Klaster usługi AKS ma różne zasobniki skonfigurowane do uruchamiania Hyperledger Fabric sieci. Wdrożone zasoby platformy Azure to:

    - **Narzędzia sieci** szkieletowej: narzędzia, które są odpowiedzialne za konfigurowanie Hyperledger Fabric sieci szkieletowej.
    - **Zasobniki zamawiania/elementów równorzędnych:** węzły Hyperledger Fabric sieci.
    - **Serwer proxy:** zasobnik serwera proxy NGNIX, za pomocą którego aplikacje klienckie mogą komunikować się z klastrem usługi AKS.
    - **Urząd certyfikacji sieci** szkieletowej: zasobnik, który uruchamia urząd certyfikacji sieci szkieletowej.
    - **PostgreSQL:** wystąpienie bazy danych, które przechowuje tożsamości urzędu certyfikacji sieci szkieletowej.
    - **Magazyn kluczy:** wystąpienie usługi Azure Key Vault wdrożone w celu zapisania poświadczeń urzędu certyfikacji sieci szkieletowej i certyfikatów głównych dostarczonych przez klienta. Magazyn jest używany w przypadku ponowienia próby wdrożenia szablonu w celu obsługi mechaniki szablonu.
    - **Dysk zarządzany:** wystąpienie usługi Azure Dyski zarządzane, która zapewnia trwały magazyn dla rejestru i bazy danych stanu świata węzła równorzędnego.
    - **Publiczny adres IP:** punkt końcowy klastra usługi AKS wdrożony do komunikacji z klastrem.

    Wprowadź następujące informacje: 

    ![Zrzut ekranu przedstawiający kartę Ustawienia klastra A K S.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Nazwa klastra Kubernetes:** w razie potrzeby zmień nazwę klastra usługi AKS. To pole jest wstępnie zasypyowane na podstawie podanego prefiksu zasobu.
    - **Wersja rozwiązania Kubernetes:** wybierz wersję rozwiązania Kubernetes, która zostanie wdrożona w klastrze. W zależności od regionu wybranego na karcie **Podstawowe** dostępne obsługiwane wersje mogą ulec zmianie.
    - **Prefiks DNS:** wprowadź prefiks nazwy systemu nazw domen (DNS) dla klastra usługi AKS. Użyjesz usługi DNS do nawiązania połączenia z interfejsem API kubernetes podczas zarządzania kontenerami po utworzeniu klastra.
    - **Rozmiar węzła:** rozmiar węzła Kubernetes można wybrać z listy jednostek magazynowych maszyn wirtualnych (SKU) dostępnych na platformie Azure. Aby uzyskać optymalną wydajność, zalecamy usługę Ds3 w standardowych wersjach 2.
    - **Liczba węzłów:** wprowadź liczbę węzłów Kubernetes do wdrożenia w klastrze. Zalecamy, aby ta liczba węzłów była równa lub większa niż liczba węzłów Hyperledger Fabric określona na karcie **Ustawienia sieci szkieletowej.**
    - **Identyfikator klienta jednostki usługi:** wprowadź identyfikator klienta istniejącej jednostki usługi lub utwórz nową. Do uwierzytelniania usługi AKS jest wymagana jednostka usługi. Zobacz kroki [tworzenia jednostki usługi](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal).
    - **Klucz tajny klienta jednostki** usługi: wprowadź wpis tajny klienta jednostki usługi podany w identyfikatorze klienta dla jednostki usługi.
    - **Potwierdź klucz tajny** klienta: potwierdź klucz tajny klienta dla jednostki usługi.
    - **Włącz monitorowanie kontenerów:** włącz monitorowanie usługi AKS, co umożliwi wypychanie dzienników usługi AKS do określonego obszaru roboczego usługi Log Analytics.
    - **Obszar roboczy usługi Log Analytics:** obszar roboczy usługi Log Analytics zostanie wypełniony domyślnym obszarem roboczym utworzonym, jeśli jest włączone monitorowanie.

8. Wybierz **kartę Przeglądanie i** tworzenie. Ten krok wyzwala weryfikację podanych wartości.
9. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

    Wdrożenie trwa zwykle od 10 do 12 minut. Czas może się różnić w zależności od rozmiaru i liczby określonych węzłów usługi AKS.
10. Po pomyślnym wdrożeniu będziesz otrzymywać powiadomienia za pośrednictwem platformy Azure w prawym górnym rogu.
11. Wybierz **pozycję Przejdź do grupy zasobów,** aby sprawdzić wszystkie zasoby utworzone w ramach wdrożenia szablonu. Wszystkie nazwy zasobów będą rozpoczynać się prefiksem podanym na **karcie Podstawy.**

## <a name="build-the-consortium"></a>Tworzenie konsorcjum

Aby utworzyć konsorcjum łańcucha bloków po wdrożeniu usługi zamawiania i węzłów równorzędnych, należy wykonać następujące kroki po kolei. Skrypt usługi Azure Hyperledger Fabric (alf) ułatwia skonfigurowanie konsorcjum, utworzenie kanału i wykonanie operacji kodu łańcucha.

> [!NOTE]
> Skrypt azure Hyperledger Fabric (alf) został zaktualizowany w celu zapewnienia większej funkcjonalności. Jeśli chcesz odwołać się do starego skryptu, zobacz [readme w witrynie GitHub.](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md) Ten skrypt jest zgodny z Hyperledger Fabric na Azure Kubernetes Service szablonu w wersji 2.0.0 lub nowszej. Aby sprawdzić wersję wdrożenia, wykonaj kroki opisane w te tematze [Rozwiązywanie problemów.](#troubleshoot)

> [!NOTE]
> Skrypt jest dostarczany, aby ułatwić tylko scenariusze demonstracyjne, programowe i testowe. Kanał i konsorcjum, które tworzy ten skrypt, mają podstawowe Hyperledger Fabric, które upraszczają scenariusze pokazowe, programowe i testowe. W przypadku konfiguracji produkcyjnej zalecamy zaktualizowanie zasad kanału/konsorcjum Hyperledger Fabric zgodnie z potrzebami organizacji w zakresie zgodności przy użyciu natywnych interfejsów API Hyperledger Fabric.


Wszystkie polecenia uruchamiania skryptu usługi Azure Hyperledger Fabric można wykonać za pomocą interfejsu wiersza polecenia (CLI) powłoki Azure Bash. Możesz zalogować się do Azure Cloud Shell za pośrednictwemâ€ âthe Hyperledger Fabric w Azure Kubernetes Service szablonu w prawym górnym ![ ](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) rogu Azure Portal. W wierszu polecenia wpisz i wybierz klawisz Enter, aby przełączyć się do interfejsu wiersza polecenia powłoki Bash, lub wybierz pozycję Bash Cloud Shell `bash` pasku narzędzi. 

Zobacz [Azure Cloud Shell,](../../cloud-shell/overview.md) aby uzyskać więcej informacji.

![Zrzut ekranu przedstawiający polecenia w Azure Cloud Shell.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


Na poniższej ilustracji przedstawiono krok po kroku proces tworzenia konsorcjum między organizacją zamawiania a organizacją równorzędną. W poniższych sekcjach przedstawiono szczegółowe polecenia służące do wykonania tych kroków.

![Diagram przedstawiający proces tworzenia konsorcjum.](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

Po zakończeniu początkowej konfiguracji użyj aplikacji klienckiej, aby uzyskać następujące operacje: â€ za pomocą

- Zarządzanie kanałami
- Zarządzanie konsorcjum
- Zarządzanie kodem łańcucha

### <a name="download-client-application-files"></a>Pobieranie plików aplikacji klienckiej

Pierwszą konfiguracją jest pobranie plików aplikacji klienckiej. Uruchom następujące polecenia, aby pobrać wszystkie wymagane pliki i pakiety:

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

Te polecenia sklonuje kod aplikacji Hyperledger Fabric Azure z publicznego repozytorium GitHub, a następnie załaduje wszystkie zależne pakiety npm. Po pomyślnym wykonaniu polecenia w bieżącym katalogu node_modules folder. Wszystkie wymagane pakiety są ładowane do node_modules folderze.

### <a name="set-up-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Wszystkie zmienne środowiskowe są zgodne z konwencją nazewnictwa zasobów platformy Azure.

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>Ustawianie zmiennych środowiskowych dla klienta organizacji zamawiania

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>Ustawianie zmiennych środowiskowych dla klienta organizacji równorzędnej

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

Na podstawie liczby organizacji równorzędnych w konsorcjum może być konieczne powtórzenie poleceń równorzędnych i odpowiednie ustawienie zmiennej środowiskowej.

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>Ustawianie zmiennych środowiskowych dla konta usługi Azure Storage

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

Użyj poniższych poleceń, aby utworzyć konto usługi Azure Storage. Jeśli masz już konto usługi Azure Storage, pomiń ten krok.

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

Użyj poniższych poleceń, aby utworzyć udział plików na koncie usługi Azure Storage. Jeśli masz już udział plików, pomiń ten krok.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

Użyj następujących poleceń, aby wygenerować ciąg połączenia dla udziału plików platformy Azure.

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>Importowanie profilu połączenia organizacji, tożsamości użytkownika administratora i dostawcy usług mSP

Użyj następujących poleceń, aby pobrać profil połączenia organizacji, tożsamość użytkownika administratora i dostawcę usług zarządzanych (MSP) z klastra usługi Azure Kubernetes Service i zapisać te tożsamości w magazynie lokalnym aplikacji klienckiej. Przykładem magazynu lokalnego jest *katalog alfTool/stores.*

W przypadku organizacji zamawiania:

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

Z klienta organizacji zamawiania użyj następującego polecenia, aby utworzyć kanał, który zawiera tylko organizację zamawiania.  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>Dodawanie organizacji równorzędnej do zarządzania konsorcjum

>[!NOTE]
> Przed rozpoczęciem jakiejkolwiek operacji konsorcjum upewnij się, że wstępna konfiguracja aplikacji klienckiej została zakończona.  

Uruchom następujące polecenia w podanej kolejności, aby dodać organizację równorzędną w kanale i konsorcjum: 

1.  Z klienta organizacji równorzędnej przekaż do usługi Azure Storage usługę MSP organizacji równorzędnej.

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  Z klienta organizacji zamawiania pobierz usługę MSP organizacji równorzędnej z usługi Azure Storage. Następnie wydaj polecenie , aby dodać organizację równorzędną w kanale i konsorcjum.

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  Z klienta organizacji zamawiania przekaż profil połączenia z zamówieniem w usłudze Azure Storage, aby organizacja równorzędna była w stanie połączyć się z węzłami osoby zamawiania przy użyciu tego profilu połączenia.

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  Z klienta organizacji równorzędnej pobierz profil połączenia osoby zamawiania z usługi Azure Storage. Następnie uruchom polecenie , aby dodać węzły równorzędne w kanale.

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

Podobnie, aby dodać więcej organizacji równorzędnych w kanale, zaktualizuj zmienne środowiskowe elementów równorzędnych zgodnie z wymaganą organizacją równorzędną i ponownie wykonać kroki od 1 do 4.

### <a name="set-anchor-peers"></a>Ustawianie elementów równorzędnych zakotwiczenia

Z klienta organizacji równorzędnej uruchom polecenie , aby ustawić kotwicę równorzędną dla organizacji równorzędnej w określonym kanale.

>[!NOTE]
> Przed uruchomieniem tego polecenia upewnij się, że w kanale dodano organizację równorzędną przy użyciu poleceń zarządzania konsorcjum.

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` to rozdzielana spacjami lista węzłów równorzędnych, które mają być ustawione jako element równorzędny kotwicy. Na przykład:

  - Ustaw tak, jakby jako element równorzędny kotwicy był ustawiany `<anchorPeersList>` `"peer1"` tylko węzeł peer1.
  - Ustaw tak, jakby węzły peer1 i peer3 ustawiały `<anchorPeersList>` się jako `"peer1" "peer3"` kotwicze równorzędne.

## <a name="chaincode-management-commands"></a>Polecenia zarządzania kodem łańcuchowym

>[!NOTE]
> Przed rozpoczęciem jakiejkolwiek operacji kodu łańcucha upewnij się, że została wykonana początkowa konfiguracja aplikacji klienckiej.  

### <a name="set-the-chaincode-specific-environment-variables"></a>Ustawianie zmiennych środowiskowych specyficznych dla kodu łańcucha

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=â€œchaincode_example02â€
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=â€œ1â€ for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=â€œ/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/goâ€
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>Instalowanie kodu łańcucha  

Uruchom następujące polecenie, aby zainstalować kod łańcucha w organizacji równorzędnej.  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
Polecenie zainstaluje kod łańcucha na wszystkich węzłach równorzędnych organizacji równorzędnej ustawionych w zmiennej `ORGNAME` środowiskowej . Jeśli co najmniej dwie organizacje równorzędne znajdują się w kanale i chcesz zainstalować kod łańcucha na wszystkich z nich, uruchom to polecenie oddzielnie dla każdej organizacji równorzędnej.  

Wykonaj następujące kroki:  

1.  Ustaw `ORGNAME` i zgodnie z i uruchom `USER_IDENTITY` `peerOrg1` `./azhlf chaincode install` polecenie.  
2.  Ustaw `ORGNAME` i zgodnie z i uruchom `USER_IDENTITY` `peerOrg2` `./azhlf chaincode install` polecenie.  

### <a name="instantiate-chaincode"></a>Kod łańcucha wystąpienia  

Z aplikacji klienta równorzędnego uruchom następujące polecenie, aby utworzyć wystąpienia kodu łańcucha w kanale.  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

Przekaż nazwę funkcji wystąpienia i rozdzielaną spacjami listę argumentów odpowiednio w i `<instantiateFunc>` `<instantiateFuncArgs>` . Aby na przykład utworzyć wystąpienia chaincode_example02.go, ustaw `<instantiateFunc>` na `init` i na wartość `<instantiateFuncArgs>` `"a" "2000" "b" "1000"` .

Możesz również przekazać plik JSON konfiguracji kolekcji przy użyciu `--collections-config` flagi . Można też ustawić argumenty przejściowe przy użyciu flagi podczas wystąpienia `-t` kodu łańcucha używanego dla transakcji prywatnych.

Na przykład:

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

Część jest ścieżką do pliku JSON, który zawiera kolekcje zdefiniowane do wystąpienia kodu `<collectionConfigJSONFilePath>` prywatnego łańcucha danych. Plik konfiguracji przykładowej kolekcji JSON względem katalogu *alfTool* można znaleźć w następującej ścieżce: `./samples/chaincode/src/private_marbles/collections_config.json` .
Przekaż `<transientArgs>` jako prawidłowy kod JSON w formacie ciągu. Znak ucieczki dla wszystkich znaków specjalnych. Na przykład: `'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> Uruchom polecenie raz z dowolnej organizacji równorzędnej w kanale. Po pomyślnym przesłaniu transakcji do złóż zamówienie dystrybuuje tę transakcję do wszystkich organizacji równorzędnych w kanale. Kod łańcucha jest następnie owijany na wszystkich węzłach równorzędnych we wszystkich organizacjach równorzędnych w kanale.  

### <a name="invoke-chaincode"></a>Wywoływanie kodu łańcucha  

Z klienta organizacji równorzędnej uruchom następujące polecenie, aby wywołać funkcję chaincode:  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

Przekaż nazwę funkcji wywołania i rozdzielaną spacjami listę argumentów inâ€ `<invokeFunction>` â€ â€ âandâ€ `<invokeFuncArgs>` â â€ â€ ârespectively. Kontynuując przykład kodu łańcucha chaincode_example02.go, aby wykonać operację wywołania, setâ€ `<invokeFunction>` â â€ âtoâ€ â `invoke` â€ âandâ€ `<invokeFuncArgs>` â â€ â€ âto `"a" "b" "10"` .  

>[!NOTE]
> Uruchom polecenie raz z dowolnej organizacji równorzędnej w kanale. Po pomyślnym przesłaniu transakcji do zamawiania, zamówienie dystrybuuje tę transakcję do wszystkich organizacji równorzędnych w kanale. Stan świata jest następnie aktualizowany we wszystkich węzłach równorzędnych wszystkich organizacji równorzędnych w kanale.  


### <a name="query-chaincode"></a>Kod łańcucha zapytań  

Uruchom następujące polecenie, aby odpytywać kod łańcucha:  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
Endorsing peers to równorzędne, w których zainstalowano kod łańcucha i który jest wywoływany w celu wykonania transakcji. Należy ustawić, `<endorsingPeers>` aby zawierały nazwy węzłów równorzędnych z bieżącej organizacji równorzędnej. Wyliczysz endoring peers dla danej kombinacji kodu łańcucha i kanału rozdzielonych spacjami. Na przykład: `-p "peer1" "peer3"`.

Jeśli używasz narzędzia *alfTool* do instalowania kodu łańcucha, przekaż wszystkie nazwy węzłów równorzędnych jako wartość do argumentu elementu równorzędnego. Kod łańcucha jest instalowany w każdym węźle równorzędym dla tej organizacji. 

Przekaż nazwę funkcji zapytania i rozdzielaną spacjami listę argumentów inâ€ `<queryFunction>` â€ â€ âandâ€ `<queryFuncArgs>` â â€ â€ ârespectively. Ponownie takingâ€ â chaincode_example02.goââ€ âchaincode as a reference, to query the value of "a" in the world state, setâ€ `<queryFunction>` â â€ âââ€ â `query` andâ€ â€ â to `<queryArgs>` `"a"` .  

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="find-deployed-version"></a>Znajdowanie wdrożonej wersji

Uruchom następujące polecenia, aby znaleźć wersję wdrożenia szablonu. Ustaw zmienne środowiskowe zgodnie z grupą zasobów, w której wdrożono szablon.

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>Poprawianie poprzedniej wersji

Jeśli masz problemy z uruchamianiem kodu łańcucha we wszystkich wdrożeniach szablonu w wersji 3.0.0, wykonaj poniższe kroki, aby poprawić węzły równorzędne za pomocą poprawki.

Pobierz skrypt wdrażania elementów równorzędnych.

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

Uruchom skrypt przy użyciu następującego polecenia, zastępując parametry elementu równorzędnego.

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

Poczekaj na poprawki wszystkich węzłów równorzędnych. Zawsze możesz sprawdzić stan węzłów równorzędnych w innym wystąpieniu powłoki przy użyciu następującego polecenia.

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Aby być na bieżąco z ofertami usług łańcucha bloków i informacjami od zespołu inżynierów Azure Blockchain, odwiedź blog Azure Blockchain [łańcucha bloków.](https://azure.microsoft.com/blog/topics/blockchain/)

Aby przekazać opinię na temat produktu lub poprosić o nowe funkcje, opublikuj lub zagłosuj na pomysł za pośrednictwem forum opinii platformy [Azure na temat łańcucha bloków](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Pomoc techniczna w społeczności

Zaangażuj inżynierów firmy Microsoft i Azure Blockchain ekspertów społeczności:

- [Strona microsoft Q&A](/answers/topics/azure-blockchain-workbench.html) 
   
  Obsługa inżynieryjna szablonów łańcucha bloków jest ograniczona do problemów z wdrażaniem.
- [Społeczność techniczna firmy Microsoft](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
