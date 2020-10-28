---
title: Samouczek — Wdrażanie chmury Azure wiosennej w sieci wirtualnej
description: Wdróż chmurę wiosenną platformy Azure w sieci wirtualnej (iniekcja v-NET).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: e0fc50647e926ea919f70b888f3efc303713fe1e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631193"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Samouczek: Wdrażanie chmury wiosennej platformy Azure w usłudze Azure Virtual Network (iniekcja sieci wirtualnej)

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

W tym samouczku wyjaśniono, jak wdrożyć wystąpienie usługi w chmurze Azure wiosny w sieci wirtualnej. Jest to czasami nazywane iniekcją sieci wirtualnej.  

Wdrożenie umożliwia:

* Izolacja aplikacji w chmurze i środowiska uruchomieniowego usługi Azure wiosny z Internetu w sieci firmowej
* Usługa Azure Wiosenna w chmurze współpracuje z systemami w lokalnych centrach danych i/lub usługach platformy Azure w innych sieciach wirtualnych
* Umożliwienie klientom kontrolowania przychodzącej i wychodzącej komunikacji sieciowej dla chmury wiosennej platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne
Należy zarejestrować dostawcę zasobów w chmurze usługi Azure wiosny `Microsoft.AppPlatform` zgodnie z instrukcjami [rejestrowania dostawcy zasobów na Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lub przez uruchomienie następującego polecenia AZ CLI:

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>Wymagania dotyczące sieci wirtualnej
Sieć wirtualna, w której jest wdrażane wystąpienie usługi w chmurze Azure wiosennej, musi spełniać następujące wymagania:

* **Lokalizacja** : Sieć wirtualna musi znajdować się w tej samej lokalizacji co wystąpienie usługi chmurowej Azure wiosennej.
* **Subskrypcja** : Sieć wirtualna musi znajdować się w tej samej subskrypcji co wystąpienie usługi chmurowej Azure wiosennej.
* **Podsieci** : Sieć wirtualna musi zawierać dwie podsieci dedykowane dla wystąpienia usługi w chmurze Azure wiosny: 
    * Jeden dla środowiska uruchomieniowego usługi
    * Jeden dla aplikacji mikrousług rozruchu sprężynowego. 
    * Istnieje relacja jeden do jednego między tymi podsieciami i wystąpieniem usługi w chmurze sieci Azure ze sprężyną. Dla każdego wdrażanego wystąpienia usługi należy użyć nowej podsieci, a Każda podsieć może zawierać tylko jedno wystąpienie usługi.
* **Przestrzeń adresowa** : jeden blok CIDR do/28 dla podsieci środowiska uruchomieniowego usługi i inny blok CIDR do/24 dla podsieci aplikacji mikrousług rozruchowych.
* **Tabela tras** : podsieci nie mogą mieć skojarzonej istniejącej tabeli tras.

Poniższe procedury opisują konfigurację sieci wirtualnej, aby zawierała wystąpienie chmury wiosennej platformy Azure.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Jeśli masz już sieć wirtualną do hostowania wystąpienia usługi w chmurze ze sprężyną Azure, pomiń krok 1, 2 i 3. Możesz rozpocząć od kroku 4, aby przygotować podsieci dla sieci wirtualnej.

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób** . W portalu Azure Marketplace wybierz pozycję **Sieć**  >  **Sieć wirtualna** .

1. W oknie dialogowym **Tworzenie sieci wirtualnej** wprowadź lub wybierz następujące informacje:

    |Ustawienie          |Wartość                                             |
    |-----------------|--------------------------------------------------|
    |Subskrypcja     |Wybierz subskrypcję.                         |
    |Grupa zasobów   |Wybierz grupę zasobów lub Utwórz nową.  |
    |Nazwa             |Wprowadzenie do *platformy Azure-sprężyny w chmurze*                   |
    |Lokalizacja         |Wybierz **Wschodnie stany USA**                                |

1. Kliknij przycisk **Dalej: adresy IP >** . 
 
1. W polu przestrzeń adresowa IPv4 wprowadź 10.1.0.0/16.

1. Wybierz pozycję **Dodaj podsieć** , a następnie wprowadź *podsieć Service-Runtime-Subnet* dla **nazwy podsieci** i 10.1.0.0/24 dla **zakresu adresów podsieci** . Następnie kliknij przycisk **Dodaj** .

1. Ponownie wybierz pozycję **Dodaj podsieć** , a następnie wprowadź **nazwę podsieci** i **zakres adresów podsieci** , na przykład *Apps-Subnet* i i 10.1.1.0/24.  Kliknij pozycję **Dodaj** .

1. Kliknij pozycję **Przejrzyj i utwórz** . Pozostaw resztę jako domyślne i kliknij przycisk **Utwórz** .

## <a name="grant-service-permission-to-the-virtual-network"></a>Udziel uprawnienia usługi do sieci wirtualnej

Wybierz wcześniej utworzoną sieć wirtualną *Azure-sprężynę i chmurę* .

1. Wybierz pozycję **Kontrola dostępu (IAM)** , a następnie wybierz pozycję **Dodaj > Dodaj przypisanie roli** .

    ![Kontrola dostępu do usługi v-NET](./media/spring-cloud-v-net-injection/access-control.png)

2. W oknie dialogowym **Dodawanie przypisania roli** wprowadź lub wybierz następujące informacje:

    |Ustawienie  |Wartość                                             |
    |---------|--------------------------------------------------|
    |Rola     |Wybierz **właściciela**                                  |
    |Wybierz pozycję   |Wprowadź *dostawcę zasobów chmury Azure wiosny*      |

    Następnie wybierz pozycję *dostawca zasobów w chmurze Azure wiosny* , a następnie kliknij przycisk **Zapisz** .

    ![Przyznaj dostawcy zasobów chmury Azure wiosny do usługi v-NET](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Można to również zrobić, uruchamiając następujące polecenie AZ CLI polecenia

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Wdróż wystąpienie usługi w chmurze Azure wiosny w sieci wirtualnej

1. Otwórz Azure Portal przy użyciu usługi at https://ms.portal.azure.com .

1. W górnym polu wyszukiwania Wyszukaj **chmurę Azure wiosnę** i wybierz z wyniku pozycję **chmura Wiosenna platformy Azure** .

1. Na stronie **chmura ze sprężyną Azure** wybierz pozycję **+ Dodaj** .

1. Wypełnij formularz na stronie **Tworzenie** chmury Azure wiosennej. 

1. Wybierz tę samą grupę zasobów i region, w której znajduje się sieć wirtualna.

1. W **obszarze** **Szczegóły usługi** wybierz pozycję *Azure-Sprężyna-chmura-Sieć wirtualna* .

1. Wybierz kartę **Sieć** i wybierz następujące opcje:

    |Ustawienie                                |Wartość                                             |
    |---------------------------------------|--------------------------------------------------|
    |Wdrażanie we własnej sieci wirtualnej     |Wybierz pozycję **Tak**                                    |
    |Sieć wirtualna                        |Wybierz pozycję *Azure-Sprężyna-chmura-Sieć wirtualna*                  |
    |Podsieć środowiska uruchomieniowego usługi                 |Wybieranie *usługi — środowisko uruchomieniowe — podsieć*                   |
    |Podsieć aplikacji mikrousług rozruchu sprężynowego   |Wybieranie *aplikacji — podsieć*                              |

    ![Karta Tworzenie sieci](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Kliknij pozycję **Przejrzyj i utwórz** .

1. Sprawdź specyfikacje i kliknij przycisk **Utwórz** .

Po wdrożeniu zostaną utworzone dwie dodatkowe grupy zasobów w ramach subskrypcji, co umożliwi hostowanie zasobów sieciowych dla wystąpienia usługi w chmurze wiosny Azure.  Przejdź do **strony głównej** , a następnie wybierz pozycję **grupy zasobów** z górnych elementów menu, aby znaleźć następujące nowe grupy zasobów.

Grupa zasobów o nazwie *AP-SVC-RT_ {wystąpienie usługi o nazwie} _ {region wystąpienia usługi}* zawiera zasoby sieciowe dla środowiska uruchomieniowego usługi wystąpienia usługi.

  ![Środowisko uruchomieniowe usługi](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Grupa zasobów o nazwie *"AP" App_ {instance Service Name} _ {region wystąpienia usługi}* zawiera zasoby sieciowe dla aplikacji mikrousług rozruchu sprężynowego wystąpienia usługi.

  ![Grupa zasobów aplikacji](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Te zasoby sieciowe są połączone z siecią wirtualną utworzoną powyżej.

  ![V-NET z podłączonym urządzeniem](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Grupy zasobów są w pełni zarządzane przez usługę w chmurze Azure wiosną. NIE usuwaj ręcznie ani nie Modyfikuj żadnych zasobów wewnątrz.

## <a name="next-steps"></a>Następne kroki

[Wdrażanie aplikacji w chmurze Azure wiosny w sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Zobacz także

- [Rozwiązywanie problemów z chmurą wiosenną platformy Azure w sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Obowiązki klientów do uruchamiania chmury Azure wiosny w sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)