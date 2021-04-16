---
title: Wdrażanie Azure Spring Cloud w sieci wirtualnej
description: Wdrażanie Azure Spring Cloud sieci wirtualnej (iniekcja sieci wirtualnej).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b0ea5728618c7b69403fcc4c0a3575b70fac6038
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482606"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Wdrażanie Azure Spring Cloud w sieci wirtualnej

**Ten artykuł ma zastosowanie do:** ââ" za â za java â ââ" za â â C #

W tym samouczku wyjaśniono, jak wdrożyć wystąpienie Azure Spring Cloud w sieci wirtualnej. To wdrożenie jest czasami nazywane iniekcją sieci wirtualnej.

Wdrożenie umożliwia:

* Izolacja Azure Spring Cloud aplikacji i środowiska uruchomieniowego usługi z internetâ €® w sieci firmowejâ €®.
* Azure Spring Cloud interakcje z systemami w lokalnych centrach danych â€®lub usługami platformy Azure w innych sieciach wirtualnych.
* Zwiększenie uprawnień klientów do kontrolowania przychodzącej i wychodzącej komunikacji sieciowej na Azure Spring Cloud.

> [!Note]
> Sieć wirtualną platformy Azure można wybrać tylko podczas tworzenia nowego Azure Spring Cloud usługi. Nie można zmienić używania innej sieci wirtualnej po Azure Spring Cloud została utworzona.

## <a name="prerequisites"></a>Wymagania wstępne

Zarejestruj dostawcę Azure Spring Cloud **Microsoft.AppPlatform** i **Microsoft.ContainerService** zgodnie z instrukcjami w te stronie Register resource provider on Azure Portal (Rejestrowanie dostawcy zasobów w usłudze [Azure Portal)](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lub uruchamiając następujące polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Wymagania dotyczące sieci wirtualnej

Sieć wirtualna, w której wdrażasz wystąpienie Azure Spring Cloud musi spełniać następujące wymagania:

* **Lokalizacja:** sieć wirtualna musi znajdować się w tej samej lokalizacji co Azure Spring Cloud wystąpienia.
* **Subskrypcja:** sieć wirtualna musi znajdować się w tej samej subskrypcji co Azure Spring Cloud wystąpienie.
* **Podsieci:** sieć wirtualna musi zawierać dwie podsieci dedykowane Azure Spring Cloud wystąpienia:

    * Jeden dla środowiska uruchomieniowego usługi.
    * Jeden dla aplikacji Spring Boot mikrousług.
    * Między tymi podsieciami a wystąpieniem usługi Azure Spring Cloud istnieje relacja jeden do jednego. Użyj nowej podsieci dla każdego wdrażanych wystąpień usługi. Każda podsieć może zawierać tylko jedno wystąpienie usługi.
* **Przestrzeń adresowa:** cidr blokuje do */28* dla podsieci środowiska uruchomieniowego usługi i podsieci Spring Boot mikrousług.
* **Tabela tras:** domyślnie podsieci nie muszą skojarzyć istniejących tabel tras. Możesz użyć [własnej tabeli tras](#bring-your-own-route-table).

Poniższe procedury opisują konfigurację sieci wirtualnej tak, aby zawierała wystąpienie Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Jeśli masz już sieć wirtualną do hostować wystąpienie Azure Spring Cloud, pomiń kroki 1, 2 i 3. Możesz rozpocząć od kroku 4, aby przygotować podsieci dla sieci wirtualnej.

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. Z Azure Marketplace sieci **wirtualnej** wybierz  >  **pozycję Sieć wirtualna.**

1. W **oknie dialogowym Tworzenie sieci** wirtualnej wprowadź lub wybierz następujące informacje:

    |Ustawienie          |Wartość                                             |
    |-----------------|--------------------------------------------------|
    |Subskrypcja     |Wybierz subskrypcję.                         |
    |Grupa zasobów   |Wybierz grupę zasobów lub utwórz nową.  |
    |Nazwa             |Wprowadź **wartość azure-spring-cloud-vnet.**                 |
    |Lokalizacja         |Wybierz pozycję **Wschodnie stany USA**.                               |

1. Wybierz **pozycję Dalej: Adresy IP.**

1. W przypadku przestrzeni adresowej IPv4 wprowadź **wartość 10.1.0.0/16.**

1. Wybierz **pozycję Dodaj podsieć.** Następnie wprowadź **wartość service-runtime-subnet w** polach **Nazwa** podsieci i wprowadź **wartość 10.1.0.0/28** w polach **Zakres adresów podsieci.** Następnie wybierz pozycję **Dodaj**.

1. Wybierz **ponownie pozycję Dodaj podsieć,** a następnie wprowadź nazwę podsieci **i** zakres **adresów podsieci.** Na przykład wprowadź **wartość apps-subnet** i **10.1.1.0/28.** Następnie wybierz pozycję **Dodaj**.

1. Wybierz pozycję **Przejrzyj i utwórz**. Pozostaw resztę jako wartości domyślne, a następnie wybierz **pozycję Utwórz**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Udzielanie usłudze uprawnień do sieci wirtualnej
Azure Spring Cloud wymaga **uprawnień właściciela** do sieci wirtualnej w celu udzielenia dedykowanej i dynamicznej jednostki usługi w sieci wirtualnej w celu dalszego wdrażania i konserwacji.

Wybierz sieć wirtualną **azure-spring-cloud-vnet,** która została wcześniej utworzona.

1. Wybierz **pozycję Kontrola dostępu (IAM),** a następnie wybierz pozycję Dodaj   >  **przypisanie roli.**

    ![Zrzut ekranu przedstawiający ekran Kontrola dostępu.](./media/spring-cloud-v-net-injection/access-control.png)

1. W **oknie dialogowym Dodawanie przypisania** roli wprowadź lub wybierz następujące informacje:

    |Ustawienie  |Wartość                                             |
    |---------|--------------------------------------------------|
    |Rola     |Wybierz **pozycję Właściciel**.                                 |
    |Wybierz pozycję   |Wprowadź **Azure Spring Cloud zasobów usługi**.   |

    Następnie wybierz **Azure Spring Cloud zasobów** i wybierz pozycję **Zapisz.**

    ![Zrzut ekranu przedstawiający wybieranie Azure Spring Cloud dostawcy zasobów.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Możesz również wykonać ten krok, uruchamiając następujące polecenie interfejsu wiersza polecenia platformy Azure:

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Wdrażanie Azure Spring Cloud danych

Aby wdrożyć Azure Spring Cloud w sieci wirtualnej:

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

1. W górnym polu wyszukiwania wyszukaj tekst **Azure Spring Cloud**. Wybierz **Azure Spring Cloud** z wyniku.

1. Na stronie **Azure Spring Cloud** wybierz pozycję **+ Dodaj**.

1. Wypełnij formularz na stronie Azure Spring Cloud **Tworzenie.**

1. Wybierz tę samą grupę zasobów i region co sieć wirtualna.

1. W **obszarze Nazwa** w obszarze Szczegóły **usługi** wybierz pozycję **azure-spring-cloud-vnet.**

1. Wybierz **kartę** Sieć i wybierz następujące wartości:

    |Ustawienie                                |Wartość                                             |
    |---------------------------------------|--------------------------------------------------|
    |Wdrażanie we własnej sieci wirtualnej     |Wybierz pozycję **Tak**.                                   |
    |Sieć wirtualna                        |Wybierz **pozycję azure-spring-cloud-vnet.**               |
    |Podsieć środowiska uruchomieniowego usługi                 |Wybierz **podsieć service-runtime-subnet.**                |
    |Spring Boot podsieci aplikacji mikrousług   |Wybierz **pozycję apps-subnet**.                           |

    ![Zrzut ekranu przedstawiający kartę Sieć na Azure Spring Cloud Tworzenie.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Wybierz **pozycję Przejrzyj i utwórz .**

1. Sprawdź specyfikacje, a następnie wybierz pozycję **Utwórz.**

    ![Zrzut ekranu przedstawiający weryfikowanie specyfikacji.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Po wdrożeniu w subskrypcji zostaną utworzone dwie dodatkowe grupy zasobów, które będą hostować zasoby sieciowe dla Azure Spring Cloud zasobów. Przejdź do **strony głównej**, a następnie wybierz pozycję **Grupy zasobów** z górnego menu, aby znaleźć następujące nowe grupy zasobów.

Grupa zasobów o nazwie **ap-svc-rt_{nazwa wystąpienia usługi}_{region** wystąpienia usługi} zawiera zasoby sieciowe dla środowiska uruchomieniowego usługi wystąpienia usługi.

  ![Zrzut ekranu przedstawiający środowisko uruchomieniowe usługi.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Grupa zasobów o nazwie **ap-app_{nazwa wystąpienia usługi}_{region** wystąpienia usługi} zawiera zasoby sieciowe dla Spring Boot aplikacji mikrousług wystąpienia usługi.

  ![Zrzut ekranu przedstawiający grupę zasobów aplikacji.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Te zasoby sieciowe są połączone z siecią wirtualną utworzoną na poprzedniej ilustracji.

  ![Zrzut ekranu przedstawiający sieć wirtualną z połączonymi urządzeniami.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Grupy zasobów są w pełni zarządzane przez usługę Azure Spring Cloud zasobów. Nie *należy* ręcznie usuwać ani modyfikować żadnych zasobów wewnątrz.

## <a name="using-smaller-subnet-ranges"></a>Korzystanie z mniejszych zakresów podsieci

W tej tabeli przedstawiono maksymalną liczbę wystąpień aplikacji, które Azure Spring Cloud przy użyciu mniejszych zakresów podsieci.

| CiDR podsieci aplikacji | Łączna liczba ip | Dostępne ip | Maksymalna liczba wystąpień aplikacji                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> Aplikacja z 1 rdzeniem: 96 <br/> Aplikacja z 2 rdzeniami: 48<br/>  Aplikacja z 3 rdzeniami: 32 <br/> Aplikacja z 4 rdzeniami: 24 </p> |
| /27             | 32        | 24            | <p> Aplikacja z 1 rdzeniem: 228<br/> Aplikacja z 2 rdzeniami: 144<br/>  Aplikacja z 3 rdzeniami: 96 <br/>  Aplikacja z 4 rdzeniami: 72</p> |
| /26             | 64        | 56            | <p> Aplikacja z 1 rdzeniem: 500<br/> Aplikacja z 2 rdzeniami: 336<br/>  Aplikacja z 3 rdzeniami: 224<br/>  Aplikacja z 4 rdzeniami: 168</p> |
| /25             | 128       | 120           | <p> Aplikacja z 1 rdzeniem: 500<br> Aplikacja z 2 rdzeniami: 500<br>  Aplikacja z 3 rdzeniami: 480<br>  Aplikacja z 4 rdzeniami: 360</p> |
| /24             | 256       | 248           | <p> Aplikacja z 1 rdzeniem: 500<br/> Aplikacja z 2 rdzeniami: 500<br/>  Aplikacja z 3 rdzeniami: 500<br/>  Aplikacja z 4 rdzeniami: 500</p> |

W przypadku podsieci platforma Azure rezerwuje pięć adresów IP, a co najmniej cztery adresy są wymagane przez Azure Spring Cloud. Wymaganych jest co najmniej dziewięć adresów IP, więc /29 i /30 są nieoperacyjne.

W przypadku podsieci środowiska uruchomieniowego usługi minimalny rozmiar to /28. Ten rozmiar nie ma wpływu na liczbę wystąpień aplikacji.

## <a name="bring-your-own-route-table"></a>Bring your own route table

Azure Spring Cloud obsługuje korzystanie z istniejących podsieci i tabel tras.

Jeśli podsieci niestandardowe nie zawierają tabel tras, program Azure Spring Cloud je dla każdej podsieci i dodaje do nich reguły w całym cyklu życia wystąpienia. Jeśli podsieci niestandardowe zawierają tabele tras, Azure Spring Cloud istniejące tabele tras podczas operacji wystąpienia i odpowiednio dodaje/aktualizuje i/lub reguły dla operacji.

> [!Warning] 
> Reguły niestandardowe można dodawać do niestandardowych tabel tras i aktualizować. Jednak reguły są dodawane przez Azure Spring Cloud i nie mogą być aktualizowane ani usuwane. Reguły, takie jak 0.0.0.0/0, muszą zawsze istnieć w danej tabeli tras i być mapowanie na element docelowy bramy internetowej, na przykład urządzenie WUS lub inną bramę ruchu wychodzącego. Należy zachować ostrożność podczas aktualizowania reguł, gdy modyfikowane są tylko reguły niestandardowe.


### <a name="route-table-requirements"></a>Wymagania dotyczące tabeli tras

Tabele tras, z którymi jest skojarzona niestandardowa sieć wirtualna, muszą spełniać następujące wymagania:

* Tabele tras platformy Azure można skojarzyć z siecią wirtualną tylko podczas tworzenia nowego Azure Spring Cloud usługi. Nie można zmienić tak, aby używać innej tabeli tras Azure Spring Cloud została utworzona.
* Zarówno podsieć aplikacji mikrousługi, jak i podsieć środowiska uruchomieniowego usługi muszą skojarzyć się z różnymi tabelami tras lub żadną z nich.
* Uprawnienia muszą zostać przypisane przed utworzeniem wystąpienia. Pamiętaj, aby przyznać usłudze Azure *Spring Cloud uprawnienia właściciela* do tabel tras.
* Skojarzonego zasobu tabeli tras nie można zaktualizować po utworzeniu klastra. Chociaż zasobu tabeli tras nie można zaktualizować, reguły niestandardowe można modyfikować w tabeli tras.
* Nie można ponownie użyć tabeli tras z wieloma wystąpieniami ze względu na potencjalne konflikty reguł routingu.

## <a name="next-steps"></a>Następne kroki

[Wdrażanie aplikacji w Azure Spring Cloud sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Zobacz też

- [Rozwiązywanie problemów Azure Spring Cloud sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Obowiązki klienta dotyczące uruchamiania Azure Spring Cloud sieci wirtualnej](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
