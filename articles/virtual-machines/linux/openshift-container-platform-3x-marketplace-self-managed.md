---
title: Wdrażanie OpenShift kontenera platform 3,11 Self-Managed portalu Marketplace na platformie Azure
description: Wdróż OpenShift kontenerów platformy 3,11 Self-Managed portalu Marketplace na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f257ed1097f49074d70f45f59e9040265f6cedef
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670440"
---
# <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych

Przed rozpoczęciem korzystania z oferty portalu Marketplace w celu wdrożenia na platformie Azure klastra z OpenShift kontenerów 3,11 platformy Microsoft  Przeczytaj artykuł dotyczący [wymagań wstępnych OpenShift](./openshift-container-platform-3x-prerequisites.md) , aby uzyskać instrukcje dotyczące tworzenia klucza SSH (bez hasła), magazynu kluczy platformy Azure, wpisu tajnego magazynu kluczy i nazwy głównej usługi.

 
## <a name="deploy-using-the-marketplace-offer"></a>Wdrażanie przy użyciu oferty Marketplace

Najprostszym sposobem wdrożenia klastra OpenShift platformy Container platform 3,11 na platformie Azure jest korzystanie z [oferty portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

Ta opcja jest najprostsza, ale również ma ograniczoną możliwość dostosowywania. Oferta Marketplace służy do wdrażania OpenShift kontenera platform 3.11.82 i zawiera następujące opcje konfiguracji:

- **Węzły główne**: trzy (3) węzły główne z konfigurowalnym typem wystąpienia.
- **Węzły infrastruktury**: trzy (3) węzły infrastruktury z konfigurowalnym typem wystąpienia.
- **Węzły**: liczba węzłów (z zakresu od 1 do 9) i typ wystąpienia można skonfigurować.
- **Typ dysku**: Managed disks są używane.
- **Sieć**: obsługa nowej lub istniejącej sieci i NIESTANDARDOWEGO zakresu CIDR.
- **CNS**: CNS można włączyć.
- **Metryki**: można włączyć metryki Hawkular.
- **Rejestrowanie**: rejestrowanie EFK może być włączone.
- **Dostawca chmury platformy Azure**: domyślnie włączony, można go wyłączyć.

W lewym górnym rogu Azure Portal kliknij pozycję **Utwórz zasób**, w polu wyszukiwania wprowadź ciąg "OpenShift Container platform" i naciśnij klawisz ENTER.

   ![Nowe wyszukiwanie zasobów](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Zostanie otwarta strona wyniki z systemem **Red Hat OpenShift Container Platform 3,11** na liście. 

   ![Nowy wynik wyszukiwania zasobów](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Kliknij ofertę, aby wyświetlić szczegóły oferty. Aby wdrożyć tę ofertę, kliknij przycisk **Utwórz**. Zostanie wyświetlony interfejs użytkownika służący do wprowadzania wymaganych parametrów. Pierwszy ekran to blok **podstawy** .

   ![Strona tytułu oferty](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Podstawy**

Aby uzyskać pomoc dotyczącą dowolnego z parametrów wejściowych, umieść kursor nad ***i*** obok nazwy parametru.

Wprowadź wartości parametrów wejściowych, a następnie kliknij przycisk **OK**.

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Nazwa użytkownika administratora maszyny wirtualnej | Użytkownik administracyjny, który ma zostać utworzony na wszystkich wystąpieniach maszyn wirtualnych |
| Klucz publiczny SSH dla użytkownika administratora | Klucz publiczny SSH używany do logowania do maszyny wirtualnej — nie może mieć hasła |
| Subskrypcja | Subskrypcja platformy Azure do wdrożenia klastra |
| Grupa zasobów | Utwórz nową grupę zasobów lub wybierz istniejącą pustą grupę zasobów dla zasobów klastra |
| Lokalizacja | Region platformy Azure, w którym ma zostać wdrożony klaster |

   ![Blok podstawy oferty](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Ustawienia infrastruktury**

Wprowadź wartości parametrów wejściowych, a następnie kliknij przycisk **OK**.

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| OCP prefiks nazwy klastra | Prefiks klastra używany do konfigurowania nazw hostów dla wszystkich węzłów. Od 1 do 20 znaków |
| Rozmiar węzła głównego | Zaakceptuj domyślny rozmiar maszyny wirtualnej lub kliknij przycisk **Zmień rozmiar** , aby wybrać inny rozmiar maszyny wirtualnej.  Wybieranie odpowiedniego rozmiaru maszyny wirtualnej do obciążenia pracą |
| Rozmiar węzła infrastruktury | Zaakceptuj domyślny rozmiar maszyny wirtualnej lub kliknij przycisk **Zmień rozmiar** , aby wybrać inny rozmiar maszyny wirtualnej.  Wybieranie odpowiedniego rozmiaru maszyny wirtualnej do obciążenia pracą |
| Liczba węzłów aplikacji | Zaakceptuj domyślny rozmiar maszyny wirtualnej lub kliknij przycisk **Zmień rozmiar** , aby wybrać inny rozmiar maszyny wirtualnej.  Wybieranie odpowiedniego rozmiaru maszyny wirtualnej do obciążenia pracą |
| Rozmiar węzła aplikacji | Zaakceptuj domyślny rozmiar maszyny wirtualnej lub kliknij przycisk **Zmień rozmiar** , aby wybrać inny rozmiar maszyny wirtualnej.  Wybieranie odpowiedniego rozmiaru maszyny wirtualnej do obciążenia pracą |
| Rozmiar hosta bastionu | Zaakceptuj domyślny rozmiar maszyny wirtualnej lub kliknij przycisk **Zmień rozmiar** , aby wybrać inny rozmiar maszyny wirtualnej.  Wybieranie odpowiedniego rozmiaru maszyny wirtualnej do obciążenia pracą |
| Nowe lub istniejące Virtual Network | Utwórz nową sieć wirtualną (domyślną) lub Użyj istniejącej sieci wirtualnej |
| Wybierz domyślne ustawienia CIDR lub Dostosuj zakres adresów IP (CIDR) | Zaakceptuj domyślne zakresy CIDR lub wybierz **niestandardowy zakres adresów IP** , a następnie wprowadź informacje o niestandardowych CIDR.  Ustawienia domyślne spowodują utworzenie sieci wirtualnej z CIDR o wartości 10.0.0.0/14, podsieci głównej z 10.1.0.0ą/16, podsiecią infrastruktury z 10.2.0.0/16 i ilością podsieci obliczeniowych i CNS z 10.3.0.0/16 |
| Nazwa grupy zasobów Key Vault | Nazwa grupy zasobów, która zawiera Key Vault |
| Nazwa Key Vault | Nazwa Key Vault, która zawiera wpis tajny z kluczem prywatnym ssh.  Dozwolone są tylko znaki alfanumeryczne i łączniki oraz mają długość od 3 do 24 znaków |
| Nazwa wpisu tajnego | Nazwa wpisu tajnego, który zawiera klucz prywatny SSH.  Dozwolone są tylko znaki alfanumeryczne i łączniki |

   ![Blok infrastruktury oferty](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Zmień rozmiar**

Aby wybrać inny rozmiar maszyny wirtualnej, kliknij pozycję ***Zmień rozmiar** _.  Zostanie otwarte okno wyboru maszyny wirtualnej.  Wybierz żądany rozmiar maszyny wirtualnej, a następnie kliknij przycisk _ * wybierz * *.

   ![Wybierz rozmiar maszyny wirtualnej](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Istniejące Virtual Network**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Istniejąca nazwa Virtual Network | Nazwa istniejącej sieci wirtualnej |
| Nazwa podsieci dla węzłów głównych | Nazwa istniejącej podsieci dla węzłów głównych.  Musi zawierać co najmniej 16 adresów IP i być zgodne ze specyfikacją RFC 1918 |
| Nazwa podsieci dla węzłów infrastruktury | Nazwa istniejącej podsieci dla węzłów infrastruktury.  Musi zawierać co najmniej 32 adresów IP i być zgodne ze specyfikacją RFC 1918 |
| Nazwa podsieci dla węzłów obliczeniowych i CNS | Nazwa istniejącej podsieci dla węzłów obliczeniowych i CNS.  Musi zawierać co najmniej 32 adresów IP i być zgodne ze specyfikacją RFC 1918 |
| Grupa zasobów dla istniejących Virtual Network | Nazwa grupy zasobów zawierającej istniejącą sieć wirtualną |

   ![Infrastruktura oferty istniejącej sieci wirtualnej](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Niestandardowy zakres adresów IP**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Zakres adresów dla Virtual Network | Niestandardowy CIDR dla sieci wirtualnej |
| Zakres adresów dla podsieci zawierającej węzły główne | Niestandardowy CIDR dla podsieci głównej |
| Zakres adresów dla podsieci zawierającej węzły infrastruktury | Niestandardowy CIDR dla podsieci infrastruktury |
| Zakres adresów dla podsieci zawierającej węzły COMPUTE i CNS | Niestandardowy CIDR dla węzłów COMPUTE i CNS |

   ![Niestandardowy zakres adresów IP infrastruktury oferty](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Wprowadź wartości parametrów wejściowych, a następnie kliknij przycisk **OK** .

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| OpenShift hasło użytkownika administratora | Hasło dla początkowego użytkownika OpenShift.  Ten użytkownik będzie również administratorem klastra |
| Potwierdź hasło użytkownika administratora OpenShift | Wpisz ponownie hasło administratora OpenShift |
| Nazwa użytkownika programu Red Hat Subscription Manager | Nazwa użytkownika, aby uzyskać dostęp do subskrypcji Red Hat lub identyfikatora organizacji.  To poświadczenie służy do rejestrowania wystąpienia usługi RHEL w ramach subskrypcji i nie będzie przechowywane przez firmę Microsoft lub Red Hat |
| Hasło użytkownika programu Red Hat Subscription Manager | Hasło do uzyskiwania dostępu do subskrypcji Red Hat lub klucza aktywacji.  To poświadczenie służy do rejestrowania wystąpienia usługi RHEL w ramach subskrypcji i nie będzie przechowywane przez firmę Microsoft lub Red Hat |
| Identyfikator puli OpenShift w programie Red Hat Subscription Manager | Identyfikator puli zawierający uprawnienia platformy kontenera OpenShift. Upewnij się, że masz wystarczające uprawnienia platformy kontenera OpenShift na potrzeby instalacji klastra |
| Identyfikator puli OpenShift usługi Red Hat Subscription Manager dla brokera/węzłów głównych | Identyfikator puli, który zawiera uprawnienia platformy kontenera OpenShift dla brokera/węzłów głównych. Upewnij się, że masz wystarczające uprawnienia platformy kontenera OpenShift do instalacji klastra. Jeśli nie korzystasz z identyfikatora brokera/puli głównej, wprowadź identyfikator puli dla węzłów aplikacji |
| Konfigurowanie dostawcy chmury platformy Azure | Skonfiguruj OpenShift do korzystania z dostawcy chmury platformy Azure. Niezbędny w przypadku korzystania z dysku platformy Azure dla woluminów trwałych.  Wartość domyślna to Yes |
| Identyfikator GUID identyfikatora klienta jednostki usługi Azure AD | Identyfikator GUID identyfikatora klienta jednostki usługi Azure AD — znany również jako identyfikator AppID. Jest to konieczne tylko wtedy, gdy dla opcji Konfiguruj dostawcę chmury platformy Azure ustawiono **wartość tak** |
| Wpis tajny identyfikatora klienta jednostki usługi Azure AD | Identyfikator wpisu tajnego klienta usługi Azure AD. Jest to konieczne tylko wtedy, gdy dla opcji Konfiguruj dostawcę chmury platformy Azure ustawiono **wartość tak** |
 
   ![Blok OpenShift oferty](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Ustawienia dodatkowe**

Blok ustawienia dodatkowe umożliwia konfigurację wartości CN dla magazynu glusterfs, rejestrowania, metryk i poddomeny routera.  Wartość domyślna nie spowoduje zainstalowania żadnej z tych opcji i będzie używać nip.io jako poddomeny routera na potrzeby testowania. Włączenie CN spowoduje zainstalowanie trzech dodatkowych węzłów obliczeniowych z trzema dołączonymi dyskami, które będą hostować glusterfsy.  

Wprowadź wartości parametrów wejściowych, a następnie kliknij przycisk **OK** .

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Konfigurowanie magazynu natywnego kontenera (CNS) | Instaluje w klastrze OpenShift wartość CNS i włącza ją jako magazyn. Jeśli dostawca platformy Azure jest wyłączony |
| Konfigurowanie rejestrowania klastrów | Instaluje funkcję rejestrowania EFK w klastrze.  Rozmiar węzłów infrastruktury odpowiednio do hostowania EFK |
| Konfigurowanie metryk dla klastra | Instaluje metryki Hawkular w klastrze OpenShift.  Rozmiar węzłów infrastruktury odpowiednio do hostowania Hawkular metryk |
| Domyślna domena podrzędna routera | Wybierz nipio do testowania lub niestandardowego, aby wprowadzić własną poddomenę dla środowiska produkcyjnego |
 
   ![Dodatkowy blok oferty](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Dodatkowe ustawienia — dodatkowe parametry**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| CNS Rozmiar węzła | Zaakceptuj domyślny rozmiar węzła lub wybierz pozycję **Zmień rozmiar** , aby wybrać nowy rozmiar maszyny wirtualnej |
| Wprowadź niestandardową poddomenę | Niestandardowa domena routingu, która będzie używana do udostępniania aplikacji za pośrednictwem routera w klastrze OpenShift.  Upewnij się, że utworzono odpowiedni symbol wieloznaczny DNS. |
 
   ![Dodatkowe zainstalowanie aktualizacji CN](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Podsumowanie**

Walidacja odbywa się na tym etapie, aby sprawdzić, czy przydział podstawowy jest wystarczający do wdrożenia łącznej liczby maszyn wirtualnych wybranych dla klastra.  Przejrzyj wszystkie wprowadzone parametry.  Jeśli dane wejściowe są akceptowalne, kliknij przycisk **OK** , aby kontynuować.

   ![Blok podsumowania oferty](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Kup**

Potwierdź informacje kontaktowe na stronie Kupowanie, a następnie kliknij przycisk **Kup** , aby zaakceptować warunki użytkowania i rozpocząć wdrażanie klastra platformy kontenerów OpenShift.

   ![Blok kupowania oferty](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Nawiązywanie połączenia z klastrem OpenShift

Po zakończeniu wdrażania Pobierz połączenie z sekcji Wyjście wdrożenia. Nawiąż połączenie z konsolą OpenShift z przeglądarką przy użyciu **adresu URL konsoli OpenShift**. można również przeprowadzić protokół SSH do hosta bastionu. Poniżej znajduje się przykład, w którym nazwa użytkownika administratora to clusteradmin, a bastionu publiczny adres IP DNS FQDN to bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Użyj polecenia [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów, klaster OpenShift i wszystkie powiązane zasoby, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Następne kroki

- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem OpenShift na platformie Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Wprowadzenie do platformy kontenerów OpenShift](https://docs.openshift.com)
- 
