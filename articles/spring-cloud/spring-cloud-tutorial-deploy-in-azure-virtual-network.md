---
title: Samouczek — Wdrażanie chmury Azure wiosennej w sieci wirtualnej
description: Wdróż chmurę wiosenną platformy Azure w sieci wirtualnej (wstrzykiwanie wirtualnej).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9d72d60bd3a1ef23b8122b2bc5ba4f0c5c701254
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587727"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-a-virtual-network"></a>Samouczek: Wdrażanie chmury wiosennej platformy Azure w sieci wirtualnej

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

W tym samouczku wyjaśniono, jak wdrożyć w sieci wirtualnej wystąpienie chmury wiosennej na platformie Azure. To wdrożenie jest czasami nazywane iniekcją sieci wirtualnej.

Wdrożenie umożliwia:

* Izolacja aplikacji w chmurze i środowiska uruchomieniowego usługi Azure wiosny z Internetu w sieci firmowej.
* Usługa Azure Wiosenna w chmurze współpracuje z systemami w lokalnych centrach danych lub usługach platformy Azure w innych sieciach wirtualnych.
* Umożliwienie klientom kontrolowania przychodzącej i wychodzącej komunikacji sieciowej dla chmury wiosennej platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Zarejestruj dostawcę zasobów w chmurze Azure wiosny **Microsoft. AppPlatform** i **Microsoft. ContainerService** zgodnie z instrukcjami w temacie [Rejestrowanie dostawcy zasobów na Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lub uruchamiając następujące polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Wymagania dotyczące sieci wirtualnej

Sieć wirtualna, w której jest wdrażane wystąpienie chmury Azure wiosennej, musi spełniać następujące wymagania:

* **Lokalizacja**: Sieć wirtualna musi znajdować się w tej samej lokalizacji co wystąpienie chmury wiosennej platformy Azure.
* **Subskrypcja**: Sieć wirtualna musi znajdować się w tej samej subskrypcji co wystąpienie chmury wiosennej platformy Azure.
* **Podsieci**: Sieć wirtualna musi zawierać dwie podsieci dedykowane dla wystąpienia chmury wiosennej platformy Azure:

    * Jeden dla środowiska uruchomieniowego usługi.
    * Jeden dla aplikacji mikrousług rozruchu sprężynowego.
    * Istnieje relacja jeden do jednego między tymi podsieciami i wystąpieniem chmury Azure wiosennej. Użyj nowej podsieci dla każdego wdrożonego wystąpienia usługi. Każda podsieć może zawierać tylko jedno wystąpienie usługi.
* **Przestrzeń adresowa**: bloki CIDR są blokowane do */28* dla podsieci środowiska uruchomieniowego usługi i podsieci aplikacji mikrousług rozruchowych.
* **Tabela tras**: podsieci nie mogą mieć skojarzonej istniejącej tabeli tras.

Poniższe procedury opisują konfigurację sieci wirtualnej, aby zawierała wystąpienie chmury wiosennej platformy Azure.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Jeśli masz już sieć wirtualną do hostowania wystąpienia chmury ze sprężyną Azure, Pomiń kroki 1, 2 i 3. Możesz rozpocząć od kroku 4, aby przygotować podsieci dla sieci wirtualnej.

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. W portalu Azure Marketplace wybierz pozycję **Sieć**  >  **Sieć wirtualna**.

1. W oknie dialogowym **Tworzenie sieci wirtualnej** wprowadź lub wybierz następujące informacje:

    |Ustawienie          |Wartość                                             |
    |-----------------|--------------------------------------------------|
    |Subskrypcja     |Wybierz subskrypcję.                         |
    |Grupa zasobów   |Wybierz grupę zasobów lub Utwórz nową.  |
    |Nazwa             |Wprowadź **Azure-Sprężyna-chmura-Sieć wirtualna**.                 |
    |Lokalizacja         |Wybierz pozycję **Wschodnie stany USA**.                               |

1. Wybierz pozycję **Dalej: adresy IP**.

1. W polu przestrzeń adresowa IPv4 wprowadź **10.1.0.0/16**.

1. Wybierz pozycję **Dodaj podsieć**. Następnie wprowadź **10.1.0.0/28** dla **nazwy podsieci** , a następnie wprowadź **wartości** dla **zakresu adresów podsieci**. Następnie wybierz pozycję **Dodaj**.

1. Ponownie wybierz pozycję **Dodaj podsieć** , a następnie wprowadź **nazwę podsieci** i **zakres adresów podsieci**. Na przykład wprowadź **aplikacje-Subnet** i **10.1.1.0/28**. Następnie wybierz pozycję **Dodaj**.

1. Wybierz pozycję **Przeglądanie + tworzenie**. Pozostaw pozostałe jako domyślne, a następnie wybierz pozycję **Utwórz**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Udziel uprawnienia usługi do sieci wirtualnej

Wybierz wcześniej utworzoną sieć wirtualną **Azure-sprężynę i chmurę** .

1. Wybierz pozycję **Kontrola dostępu (IAM)**, a następnie wybierz pozycję **Dodaj**  >  **Dodaj przypisanie roli**.

    ![Zrzut ekranu przedstawiający ekran kontroli dostępu.](./media/spring-cloud-v-net-injection/access-control.png)

1. W oknie dialogowym **Dodaj przypisanie roli** wprowadź lub wybierz następujące informacje:

    |Ustawienie  |Wartość                                             |
    |---------|--------------------------------------------------|
    |Rola     |Wybierz pozycję **właściciel**.                                 |
    |Wybierz pozycję   |Wprowadź **dostawcę zasobów chmury Azure wiosennej**.   |

    Następnie wybierz pozycję **dostawca źródła zasobów w chmurze Azure** i wybierz pozycję **Zapisz**.

    ![Zrzut ekranu pokazujący wybieranie dostawcy zasobów w chmurze platformy Azure.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Ten krok można także wykonać, uruchamiając następujące polecenie interfejsu wiersza polecenia platformy Azure:

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Wdróż wystąpienie chmury wiosennej na platformie Azure

Aby wdrożyć wystąpienie chmury wiosennej platformy Azure w sieci wirtualnej:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

1. W górnym polu wyszukiwania Wyszukaj **chmurę Azure wiosenną**. Wybierz z wyniku **chmurę z usługą Azure wiosną** .

1. Na stronie **chmura ze sprężyną Azure** wybierz pozycję **+ Dodaj**.

1. Wypełnij formularz na stronie **Tworzenie** chmury Azure wiosennej.

1. Wybierz tę samą grupę zasobów i region, w której znajduje się sieć wirtualna.

1. W **obszarze** **Szczegóły usługi** wybierz pozycję **Azure-Sprężyna-chmura-Sieć wirtualna**.

1. Wybierz kartę **Sieć** i wybierz następujące wartości:

    |Ustawienie                                |Wartość                                             |
    |---------------------------------------|--------------------------------------------------|
    |Wdrażanie we własnej sieci wirtualnej     |Wybierz pozycję **Tak**.                                   |
    |Sieć wirtualna                        |Wybierz pozycję **Azure-Sprężyna-chmura-Sieć wirtualna**.               |
    |Podsieć środowiska uruchomieniowego usługi                 |Wybierz pozycję **Usługa — środowisko uruchomieniowe — podsieć**.                |
    |Podsieć aplikacji mikrousług rozruchu sprężynowego   |Wybierz pozycję **aplikacje — podsieć**.                           |

    ![Zrzut ekranu przedstawiający kartę Sieć na stronie Tworzenie chmury Azure wiosennej.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Wybierz pozycję **Przejrzyj i Utwórz**.

1. Sprawdź specyfikacje i wybierz pozycję **Utwórz**.

    ![Zrzut ekranu pokazujący sprawdzanie specyfikacji.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Po wdrożeniu zostaną utworzone dwie dodatkowe grupy zasobów w ramach subskrypcji w celu hostowania zasobów sieciowych dla wystąpienia chmury wiosennej na platformie Azure. Przejdź do **strony głównej**, a następnie wybierz pozycję **grupy zasobów** z górnych elementów menu, aby znaleźć następujące nowe grupy zasobów.

Grupa zasobów o nazwie **AP-SVC-RT_ {wystąpienie usługi o nazwie} _ {region wystąpienia usługi}** zawiera zasoby sieciowe dla środowiska uruchomieniowego usługi wystąpienia usługi.

  ![Zrzut ekranu pokazujący środowisko uruchomieniowe usługi.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Grupa zasobów o nazwie **"AP" App_ {instance Service Name} _ {region wystąpienia usługi}** zawiera zasoby sieciowe dla aplikacji mikrousług rozruchu sprężynowego wystąpienia usługi.

  ![Zrzut ekranu pokazujący grupę zasobów aplikacji.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Te zasoby sieciowe są połączone z siecią wirtualną utworzoną na powyższym obrazie.

  ![Zrzut ekranu przedstawiający sieć wirtualną z połączonymi urządzeniami.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Grupy zasobów są w pełni zarządzane przez usługę w chmurze Azure wiosną. Nie usuwaj ręcznie ani *nie* Modyfikuj żadnego zasobu w ramach.

## <a name="limitations"></a>Ograniczenia

Mały zakres podsieci zapisuje adresy IP, ale ogranicza maksymalną liczbę wystąpień aplikacji, które mogą być przechowywane w chmurze Azure wiosennej.

| Routing CIDR podsieci aplikacji | Łączna liczba adresów IP | Dostępne adresy IP | Maksymalna liczba wystąpień aplikacji                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> Aplikacja z 1 rdzeniem: 96 <br/> Aplikacja z 2 rdzeniami: 48<br/>  Aplikacja z 3 rdzeniami: 32 <br/> Aplikacja z 4 rdzeniami: 24 </p> |
| /27             | 32        | 24            | <p> Aplikacja z 1 rdzeniem: 228<br/> Aplikacja z 2 rdzeniami: 144<br/>  Aplikacja z 3 rdzeniami: 96 <br/>  Aplikacja z 4 rdzeniami: 72</p> |
| /26             | 64        | 56            | <p> Aplikacja z 1 rdzeniem: 500<br/> Aplikacja z 2 rdzeniami: 336<br/>  Aplikacja z 3 rdzeniami: 224<br/>  Aplikacja z 4 rdzeniami: 168</p> |
| /25             | 128       | 120           | <p> Aplikacja z 1 rdzeniem: 500<br> Aplikacja z 2 rdzeniami: 500<br>  Aplikacja z 3 rdzeniami: 480<br>  Aplikacja z 4 rdzeniami: 360</p> |
| /24             | 256       | 248           | <p> Aplikacja z 1 rdzeniem: 500<br/> Aplikacja z 2 rdzeniami: 500<br/>  Aplikacja z 3 rdzeniami: 500<br/>  Aplikacja z 4 rdzeniami: 500</p> |

W przypadku podsieci pięć adresów IP jest zarezerwowanych przez platformę Azure, a co najmniej cztery adresy są wymagane przez chmurę z platformą Azure. Wymagane są co najmniej dziewięć adresów IP, więc/29 i/30 są nieoperacyjne.

Dla podsieci środowiska uruchomieniowego usługi minimalny rozmiar to/28. Ten rozmiar nie ma wpływu na liczbę wystąpień aplikacji.

## <a name="next-steps"></a>Następne kroki

[Wdrażanie aplikacji w chmurze Azure wiosny w sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Zobacz także

- [Rozwiązywanie problemów z chmurą wiosenną platformy Azure w sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Obowiązki klientów do uruchamiania chmury Azure wiosny w sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
