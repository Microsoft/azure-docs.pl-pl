---
title: Cloud Shell na platformie Azure Virtual Network
description: Wdrażanie Cloud Shell w sieci wirtualnej platformy Azure
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 722d935c242a51ddfc01377676f026b71a8951b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468542"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Wdrażanie Cloud Shell w sieci wirtualnej platformy Azure
> [!NOTE]
> Ta funkcja jest w publicznej wersji zapoznawczej.

Zwykła sesja Cloud Shell jest uruchamiana w kontenerze w sieci firmy Microsoft, niezależnie od zasobów. Oznacza to, że polecenia uruchamiane wewnątrz kontenera nie mogą uzyskać dostępu do zasobów, do których można uzyskać dostęp tylko z określonej sieci wirtualnej. Nie można na przykład użyć protokołu SSH do nawiązania połączenia z Cloud Shell z maszyną wirtualną, która ma tylko prywatny adres IP, lub używać polecenia kubectl do nawiązywania połączenia z klastrem Kubernetes, który zablokował dostęp. 

Ta opcjonalna funkcja rozwiązuje te ograniczenia i umożliwia wdrożenie Cloud Shell w kontrolowanej sieci wirtualnej platformy Azure. Z tego miejsca kontener może współdziałać z zasobami w wybranej sieci wirtualnej.  

Poniżej można zobaczyć architekturę zasobów, która zostanie wdrożona i użyta w tym scenariuszu.

![Ilustruje Cloud Shell architekturę izolowanej sieci wirtualnej.](media/private-vnet/data-diagram.png)

Aby móc używać Cloud Shell we własnym Virtual Network platformy Azure, musisz utworzyć kilka zasobów do obsługi tej funkcji. W tym artykule przedstawiono sposób konfigurowania wymaganych zasobów przy użyciu szablonu usługi ARM.

> [!NOTE]
> Te zasoby należy skonfigurować tylko raz dla sieci wirtualnej. Mogą one następnie być współużytkowane przez wszystkich administratorów mających dostęp do sieci wirtualnej.

## <a name="required-network-resources"></a>Wymagane zasoby sieciowe

### <a name="virtual-network"></a>Sieć wirtualna
Sieć wirtualna definiuje przestrzeń adresową, w której tworzone są co najmniej jedna podsieć.

Należy zidentyfikować żądaną sieć wirtualną, która ma być używana na potrzeby Cloud Shell. Zwykle jest to istniejąca sieć wirtualna zawierająca zasoby, które mają być zarządzane, lub sieć, która łączy się z sieciami, które zawierają Twoje zasoby.

### <a name="subnet"></a>Podsieć
W ramach wybranej sieci wirtualnej należy używać dedykowanej podsieci do Cloud Shell kontenerów. Ta podsieć jest delegowana do usługi Azure Container Instances (ACI).  Gdy użytkownik zażąda kontenera Cloud Shell w sieci wirtualnej, Cloud Shell używa ACI do utworzenia kontenera, który znajduje się w tej podsieci delegowanej.  W tej podsieci nie można tworzyć innych zasobów.

### <a name="network-profile"></a>Profil sieci
Profil sieci to szablon konfiguracji sieci dla zasobów platformy Azure, który określa pewne właściwości sieci dla zasobu.

### <a name="azure-relay"></a>Azure Relay
[Azure Relay](../azure-relay/relay-what-is-it.md) zezwala na dwa punkty końcowe, które nie są bezpośrednio dostępne do komunikacji. W takim przypadku służy do zezwalania przeglądarce administratora na komunikowanie się z kontenerem w sieci prywatnej.

Azure Relay wystąpienie używane dla Cloud Shell można skonfigurować do kontrolowania, które sieci mogą uzyskać dostęp do zasobów kontenera: 
- Dostępne z publicznej sieci Internet: w tej konfiguracji Cloud Shell zapewnia sposób dostępu do wewnętrznych zasobów z zewnątrz. 
- Dostępne w określonych sieciach: w tej konfiguracji administratorzy będą musieli uzyskać dostęp do Azure Portal z komputera z systemem w odpowiedniej sieci, aby móc korzystać z Cloud Shell.

## <a name="storage-requirements"></a>Wymagania dotyczące magazynu
Podobnie jak w przypadku standardowej Cloud Shell konto magazynu jest wymagane podczas korzystania z Cloud Shell w sieci wirtualnej. Każdy administrator wymaga udziału plików do przechowywania plików.  Konto magazynu musi być dostępne z sieci wirtualnej używanej przez Cloud Shell. 

## <a name="virtual-network-deployment-limitations"></a>Ograniczenia wdrożenia sieci wirtualnej
* Ze względu na dodatkowe zasoby sieci, uruchamianie Cloud Shell w sieci wirtualnej jest zwykle wolniejsze niż standardowa sesja Cloud Shella.

* W wersji zapoznawczej dla Cloud Shell w sieci wirtualnej obsługiwane są mniej regionów. Jest to obecnie ograniczone do: Zachodnie i WestCentralUS.

* [Azure Relay](../azure-relay/relay-what-is-it.md) nie jest bezpłatną usługą, zobacz [Cennik](https://azure.microsoft.com/pricing/details/service-bus/). W scenariuszu Cloud Shell jedno połączenie hybrydowe jest używane dla każdego administratora podczas korzystania z Cloud Shell. Połączenie zostanie automatycznie zamknięte po zakończeniu sesji Cloud Shell.

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

Dostawca zasobów Microsoft. ContainerInstances musi być zarejestrowany w subskrypcji zawierającej sieć wirtualną, której chcesz użyć. Wybierz odpowiednią subskrypcję w programie `Set-AzContext -Subscription {subscriptionName}` , a następnie uruchom polecenie:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Jeśli **RegistrationState** jest `Registered` , nie jest wymagana żadna akcja. Jeśli jest `NotRegistered` , uruchom polecenie `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` . 

## <a name="deploy-network-resources"></a>Wdrażanie zasobów sieciowych
 
### <a name="create-a-resource-group-and-virtual-network"></a>Tworzenie grupy zasobów i sieci wirtualnej
Jeśli masz już pożądaną sieć wirtualną, z którą chcesz nawiązać połączenie, Pomiń tę sekcję.

W Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure, Azure PowerShell itp. Utwórz grupę zasobów i sieć wirtualną w nowej grupie zasobów, **Grupa zasobów i Sieć wirtualna muszą znajdować się w tym samym regionie**.

> [!NOTE]
> W publicznej wersji zapoznawczej Grupa zasobów i Sieć wirtualna muszą znajdować się w WestCentralUS lub zachodnich.

### <a name="arm-templates"></a>Szablony usługi ARM
Użyj [szablonu szybkiego startu platformy Azure](https://aka.ms/cloudshell/docs/vnet/template) do tworzenia zasobów Cloud Shell w sieci wirtualnej i [szablonu szybkiego startu platformy Azure](https://aka.ms/cloudshell/docs/vnet/template/storage) w celu utworzenia niezbędnego magazynu. Zanotuj nazwy zasobów, a przede wszystkim nazwę udziału plików.

### <a name="open-relay-firewall"></a>Otwórz Zaporę przekaźnika
Przejdź do przekaźnika utworzonego przy użyciu powyższego szablonu, wybierz pozycję "Sieć" w obszarze Ustawienia, a następnie Zezwól na dostęp z sieci przeglądarki do przekaźnika. Domyślnie przekaźnik jest dostępny tylko z sieci wirtualnej, w której został utworzony. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Konfigurowanie Cloud Shell do korzystania z sieci wirtualnej.
> [!NOTE]
> Ten krok należy wykonać dla każdego administratora, który będzie używać Cloud Shell.

Po wdrożeniu wykonaj powyższe kroki przejdź do Cloud Shell w Azure Portal lub na https://shell.azure.com . Jedno z tych środowisk musi być używane za każdym razem, gdy chcesz nawiązać połączenie z izolowanym Cloud Shell środowiska.

> [!NOTE]
> Jeśli w przeszłości użyto Cloud Shell, należy odinstalować istniejące CloudDrive. Aby wykonać ten przebieg `clouddrive unmount` z aktywnej sesji Cloud Shell, Odśwież stronę.

Połącz się z Cloud Shell, zostanie wyświetlony monit z pierwszym uruchomieniem. Wybierz preferowane środowisko powłoki, wybierz pozycję "Pokaż ustawienia zaawansowane" i zaznacz pole wyboru "Pokaż ustawienia izolacji sieci wirtualnej". Wypełnij pola w oknie podręcznym.  Większość pól spowoduje automatyczne wypełnienie dostępnych zasobów, które mogą być skojarzone z Cloud Shell w sieci wirtualnej.  Nazwa udziału plików musi zostać wypełniona przez użytkownika.


![Ilustruje ustawienia Cloud Shell izolowanej sieci wirtualnej pierwszego środowiska.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o usłudze Azure Virtual Networks](../virtual-network/virtual-networks-overview.md)