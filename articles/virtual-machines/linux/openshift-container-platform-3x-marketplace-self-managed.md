---
title: Wdrażanie oferty platformy kontenerowej OpenShift 3.11 self-managed marketplace na platformie Azure
description: Wdrażanie oferty platformy kontenerowej OpenShift 3.11 self-managed marketplace na platformie Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1cf6c7417aa86d47e59e08786e7807e32c175a25
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759578"
---
# <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych

Przed użyciem oferty Marketplace do wdrożenia samodzielnie zarządzanego klastra Platformy kontenerów OpenShift 3.11 na platformie Azure należy skonfigurować kilka wymagań wstępnych.  Przeczytaj artykuł [wymagań wstępnych OpenShift,](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) aby uzyskać instrukcje dotyczące tworzenia klucza ssh (bez hasła), magazynu kluczy platformy Azure, klucza tajnego i jednostki usługi.

 
## <a name="deploy-using-the-marketplace-offer"></a>Wdrażanie przy użyciu oferty Marketplace

Najprostszym sposobem wdrożenia samodzielnie zarządzanego klastra OpenShift Container Platform 3.11 na platformie Azure jest użycie [oferty portalu Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)

Ta opcja jest najprostsza, ale ma również ograniczone możliwości dostosowywania. Oferta Marketplace wdraża platformę kontenerową OpenShift 3.11.82 i zawiera następujące opcje konfiguracji:

- **Węzły główne:** trzy (3) węzły główne z konfigurowalnym typem wystąpienia.
- **Węzły podczerwieni:** Trzy (3) węzły podczerwej z konfigurowalnym typem wystąpienia.
- **Węzły:** Liczba węzłów (od 1 do 9) i typ wystąpienia są konfigurowalne.
- **Typ dysku:** Używane są dyski zarządzane.
- **Sieć**: Obsługa nowych lub istniejących sieci i niestandardowego zakresu CIDR.
- **CNS**: CNS może być włączony.
- **Metryki:** Metryki Hawkular można włączyć.
- **Rejestrowanie:** Rejestrowanie EFK można włączyć.
- **Dostawca chmury Azure:** domyślnie włączone, można wyłączyć.

W lewym górnym rogu witryny Azure portal kliknij pozycję **Utwórz zasób**, wprowadź w polu wyszukiwania "platforma kontenera openshift" i naciśnij klawisz Enter.

   ![Wyszukiwanie nowych zasobów](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Strona Wyniki zostanie otwarta z **Red Hat OpenShift Container Platform 3.11 Self-Managed** na liście. 

   ![Nowy wynik wyszukiwania zasobów](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Kliknij ofertę, aby wyświetlić szczegóły oferty. Aby wdrożyć tę ofertę, kliknij przycisk **Utwórz**. Pojawi się interfejs użytkownika, aby wprowadzić niezbędne parametry. Pierwszy ekran to brzemię **Podstawy.**

   ![Strona tytułowa oferty](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Podstawy**

Aby uzyskać pomoc dotyczącą dowolnego z parametrów wejściowych, najedź kursorem na ***i*** obok nazwy parametru.

Wprowadź wartości parametrów wejściowych i kliknij przycisk **OK**.

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Nazwa użytkownika administratora maszyny Wirtualnej | Użytkownik administratora, który ma zostać utworzony we wszystkich wystąpieniach maszyn wirtualnych |
| Klucz publiczny SSH dla użytkownika administratora | Klucz publiczny SSH używany do logowania się do maszyny Wirtualnej — nie może mieć hasła |
| Subskrypcja | Subskrypcja platformy Azure w celu wdrożenia klastra |
| Grupa zasobów | Tworzenie nowej grupy zasobów lub wybieranie istniejącej pustej grupy zasobów dla zasobów klastra |
| Lokalizacja | Region platformy Azure do wdrożenia klastra w |

   ![Zaoferuj podstawowe ostrze](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Ustawienia infrastruktury**

Wprowadź wartości parametrów wejściowych i kliknij przycisk **OK**.

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Prefiks nazwy klastra OCP | Prefiks klastra używany do konfigurowania nazwy hostów dla wszystkich węzłów. Od 1 do 20 znaków |
| Rozmiar węzła głównego | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar,** aby wybrać inny rozmiar maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia roboczego |
| Rozmiar węzła infrastruktury | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar,** aby wybrać inny rozmiar maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia roboczego |
| Liczba węzłów aplikacji | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar,** aby wybrać inny rozmiar maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia roboczego |
| Rozmiar węzła aplikacji | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar,** aby wybrać inny rozmiar maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia roboczego |
| Rozmiar hosta bastionu | Zaakceptuj domyślny rozmiar maszyny Wirtualnej lub kliknij przycisk **Zmień rozmiar,** aby wybrać inny rozmiar maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej dla obciążenia roboczego |
| Nowa lub istniejąca sieć wirtualna | Tworzenie nowej sieci wirtualnej (domyślna) lub używanie istniejącej sieci vNet |
| Wybieranie domyślnych ustawień CIDR lub dostosowywanie zakresu ip (CIDR) | Zaakceptuj domyślne zakresy CIDR lub wybierz **niestandardowy zakres adresów IP** i wprowadź niestandardowe informacje CIDR.  Ustawienia domyślne utworzy sieć wirtualną z cidr 10.0.0.0/14, podsiecią główną z 10.1.0.0/16, podsiecią infra z 10.2.0.0/16 oraz podsiecią obliczeniową i OUN z 10.3.0.0/16 |
| Nazwa grupy zasobów magazynu kluczy | Nazwa grupy zasobów zawierającej magazyn kluczy |
| Nazwa magazynu kluczy | Nazwa magazynu kluczy zawierającego klucz tajny z kluczem prywatnym ssh.  Dozwolone są tylko znaki alfanumeryczne i kreski i mają od 3 do 24 znaków |
| Tajna nazwa | Nazwa klucza tajnego zawierającego klucz prywatny ssh.  Dozwolone są tylko znaki alfanumeryczne i kreski |

   ![Zaoferuj ostrze infrastruktury](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Zmień rozmiar**

Aby wybrać inny rozmiar maszyny Wirtualnej, kliknij pozycję ***Zmień rozmiar***.  Otworzy się okno wyboru maszyny Wirtualnej.  Wybierz odpowiedni rozmiar maszyny Wirtualnej i kliknij przycisk **Wybierz**.

   ![Wybieranie rozmiaru maszyny Wirtualnej](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Istniejąca sieć wirtualna**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Istniejąca nazwa sieci wirtualnej | Nazwa istniejącej sieci vNet |
| Nazwa podsieci dla węzłów głównych | Nazwa istniejącej podsieci dla węzłów głównych.  Musi zawierać co najmniej 16 adresów IP i postępować zgodnie z RFC 1918 |
| Nazwa podsieci dla węzłów podczerwych | Nazwa istniejącej podsieci dla węzłów podczerwień.  Musi zawierać co najmniej 32 adresy IP i postępować zgodnie z RFC 1918 |
| Nazwa podsieci dla węzłów obliczeniowych i cns | Nazwa istniejącej podsieci dla węzłów obliczeniowych i cns.  Musi zawierać co najmniej 32 adresy IP i postępować zgodnie z RFC 1918 |
| Grupa zasobów dla istniejącej sieci wirtualnej | Nazwa grupy zasobów zawierającej istniejącą w sieci vNet |

   ![Zaoferuj infrastrukturę istniejącego vnetu](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Niestandardowy zakres adresów IP**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Zakres adresów dla sieci wirtualnej | Niestandardowy cidr dla sieci vNet |
| Zakres adresów dla podsieci zawierającej węzły główne | Niestandardowy cidr dla podsieci głównej |
| Zakres adresów dla podsieci zawierającej węzły infrastruktury | Niestandardowy cidr dla podsieci infrastruktury |
| Zakres adresów dla podsieci zawierającej węzły obliczeniowe i węzły cns | Niestandardowy cidr dla węzłów obliczeniowych i cns |

   ![Zaoferuj infrastrukturę niestandardowy zakres adresów IP](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

Wprowadź wartości parametrów wejściowych i kliknij przycisk **OK**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Hasło użytkownika administratora OpenShift | Hasło dla początkowego użytkownika OpenShift.  Ten użytkownik będzie również administratorem klastra |
| Potwierdź hasło administratora OpenShift | Ponowne wpisywania hasła administratora OpenShift |
| Nazwa użytkownika Menedżera subskrypcji Red Hat | Nazwa użytkownika, aby uzyskać dostęp do subskrypcji Red Hat lub identyfikatora organizacji.  To poświadczenie jest używane do rejestrowania wystąpienia RHEL w subskrypcji i nie będzie przechowywane przez firmę Microsoft lub Red Hat |
| Hasło użytkownika menedżera subskrypcji Red Hat | Hasło dostępu do subskrypcji Red Hat lub klucza aktywacyjnego.  To poświadczenie jest używane do rejestrowania wystąpienia RHEL w subskrypcji i nie będzie przechowywane przez firmę Microsoft lub Red Hat |
| Red Hat Subscription Manager OpenShift Pool ID | Identyfikator puli zawierający uprawnienie Platformy kontenerowej OpenShift. Upewnij się, że masz wystarczające uprawnienia platformy kontenerowej OpenShift do instalacji klastra |
| Red Hat Subscription Manager OpenShift Pool ID dla brokera / węzłów głównych | Identyfikator puli zawierający uprawnienia platformy kontenerowej OpenShift dla węzłów brokera / głównego. Upewnij się, że masz wystarczające uprawnienia platformy kontenera OpenShift do instalacji klastra. Jeśli nie używasz identyfikatora brokera / puli głównej, wprowadź identyfikator puli dla węzłów aplikacji |
| Konfigurowanie dostawcy chmury platformy Azure | Skonfiguruj funkcję OpenShift do używania dostawcy chmury azure. Konieczne jest, jeśli przy użyciu dysku platformy Azure dołączyć dla woluminów trwałych.  Wartość domyślna to Tak |
| Identyfikator guid klienta usługi Azure AD | Identyfikator klienta jednostki usługi Azure AD — znany również jako identyfikator aplikacji. Jest to wymagane tylko wtedy, gdy skonfigurowanie dostawcy chmury Azure ustawionego na **Tak** |
| Klucz tajny identyfikatora klienta usługi Azure AD | Klucz tajny identyfikatora klienta usługi Azure AD. Jest to wymagane tylko wtedy, gdy skonfigurowanie dostawcy chmury Azure ustawionego na **Tak** |
 
   ![Zaoferuj ostrze OpenShift](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Dodatkowe ustawienia**

Blok Ustawienia dodatkowe umożliwia konfigurację OUN dla glusterfs storage, Logging, Metrics i Router Sub domain.  Domyślna nie zainstaluje żadnej z tych opcji i użyje nip.io jako domeny podrzędnej routera do celów testowych. Włączenie CNS zainstaluje trzy dodatkowe węzły obliczeniowe z trzema dodatkowymi dołączonymi dyskami, które będą hostować zasobników glusterfs.  

Wprowadź wartości parametrów wejściowych i kliknij przycisk **OK**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| Konfigurowanie magazynu macierzystego kontenera (CNS) | Instaluje układ OUN w klastrze OpenShift i włącza go jako magazyn. Wartość domyślna, jeśli dostawca platformy Azure jest wyłączony |
| Konfigurowanie rejestrowania klastrów | Instaluje funkcje rejestrowania EFK w klastrze.  Rozmiar węzłów podczerwień odpowiednio do obsługi zasobników EFK |
| Konfigurowanie metryk dla klastra | Instaluje metryki Hawkular w klastrze OpenShift.  Rozmiar węzłów podczerwień odpowiednio do obsługi hawkular metryki zasobników |
| Domyślna domena podrzędna routera | Wybierz nipio do testowania lub niestandardowe, aby wprowadzić własną domenę podrzędną do produkcji |
 
   ![Zaoferuj dodatkowe ostrze](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Dodatkowe ustawienia — dodatkowe parametry**

| Parametr wejściowy | Opis parametru |
|-----------------------|-----------------|
| (OUN) Rozmiar węzła | Zaakceptuj domyślny rozmiar węzła lub wybierz **zmień rozmiar,** aby wybrać nowy rozmiar maszyny Wirtualnej |
| Wprowadź niestandardową poddomenę | Niestandardowa domena routingu, która ma być używana do ujawniania aplikacji za pośrednictwem routera w klastrze OpenShift.  Pamiętaj, aby utworzyć odpowiedni wpis DNS z symbolami wieloznacznych] |
 
   ![Zaoferuj dodatkową instalację cns](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Podsumowanie**

Sprawdzanie poprawności odbywa się na tym etapie, aby sprawdzić przydział podstawowy jest wystarczająca do wdrożenia całkowitej liczby maszyn wirtualnych wybranych dla klastra.  Przejrzyj wszystkie wprowadzone parametry.  Jeśli dane wejściowe są dopuszczalne, kliknij przycisk **OK,** aby kontynuować.

   ![Ostrze podsumowania oferty](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Kup**

Potwierdź informacje kontaktowe na stronie Kup i kliknij przycisk **Kup,** aby zaakceptować warunki użytkowania i rozpocząć wdrażanie klastra Platformy kontenerowej OpenShift.

   ![Oferta zakupu ostrze](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Łączenie się z klastrem OpenShift

Po zakończeniu wdrażania pobierz połączenie z sekcji wyjściowej wdrożenia. Połącz się z konsolą OpenShift za pomocą przeglądarki, używając **adresu URL konsoli OpenShift**. można również SSH do hosta Bastion. Poniżej przedstawiono przykład, w którym nazwa użytkownika administratora jest clusteradmin i bastion publiczny IP DNS FQDN jest bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Polecenie [usuń grupę AZ](/cli/azure/group) służy do usuwania grupy zasobów, klastra OpenShift i wszystkich powiązanych zasobów, gdy nie są już potrzebne.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Następne kroki

- [Zadania po wdrożeniu](./openshift-container-platform-3x-post-deployment.md)
- [Rozwiązywanie problemów z wdrażaniem usługi OpenShift na platformie Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Wprowadzenie do platformy kontenerowej OpenShift](https://docs.openshift.com)
- 