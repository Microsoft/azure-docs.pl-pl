---
title: Łączenie wystąpień infrastruktury BareMetal na platformie Azure
description: Dowiedz się, jak identyfikować wystąpienia BareMetal i korzystać z nich Azure Portal w interfejsie wiersza polecenia platformy Azure.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: a7fdc17aa4271915f7dc02aaa2d7a688016bf892
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579197"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Łączenie wystąpień infrastruktury BareMetal na platformie Azure

W tym artykule pokazano, jak [Azure Portal](https://portal.azure.com/) są wyświetlane [wystąpienia BareMetal](concepts-baremetal-infrastructure-overview.md). W tym artykule przedstawiono również informacje o tym, co można zrobić w Azure Portal przy użyciu wdrożonych wystąpień infrastruktury BareMetal. 
 
## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów
Dostawca zasobów platformy Azure dla wystąpień BareMetal zapewnia widoczność wystąpień w Azure Portal. Domyślnie subskrypcja platformy Azure używana dla wdrożeń wystąpień BareMetal rejestruje dostawcę zasobów *BareMetalInfrastructure* . Jeśli nie widzisz wdrożonych wystąpień BareMetal, musisz zarejestrować dostawcę zasobów w ramach subskrypcji. 

Dostawcę zasobów wystąpienia BareMetal można zarejestrować za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Musisz wyświetlić swoją subskrypcję w Azure Portal a następnie kliknąć dwukrotnie subskrypcję używaną do wdrożenia wystąpień usługi BareMetal.
 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.

1. W polu **wszystkie usługi** wprowadź **subskrypcję**, a następnie wybierz pozycję **subskrypcje**.

1. Wybierz subskrypcję z listy subskrypcji.

1. Wybierz pozycję **dostawcy zasobów** i wprowadź **BareMetalInfrastructure** w polu wyszukiwania. Dostawca zasobów powinien być **zarejestrowany**, ponieważ obraz pokazuje.
 
>[!NOTE]
>Jeśli dostawca zasobów nie jest zarejestrowany, wybierz pozycję **zarejestruj**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Zrzut ekranu przedstawiający zarejestrowane wystąpienia BareMetal.":::

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć korzystanie z interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Zaloguj się do subskrypcji platformy Azure, która jest używana na potrzeby wdrożenia wystąpienia BareMetal za pomocą interfejsu wiersza polecenia platformy Azure. Zarejestruj `BareMetalInfrastructure` dostawcę zasobów przy użyciu polecenia [AZ Provider Register](/cli/azure/provider#az_provider_register) :

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Możesz użyć polecenia [AZ Provider list](/cli/azure/provider#az_provider_list) , aby wyświetlić wszystkich dostępnych dostawców.

---

Aby uzyskać więcej informacji na temat dostawców zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>Wystąpienia BareMetal w Azure Portal
 
Po przesłaniu żądania wdrożenia wystąpienia BareMetal należy określić subskrypcję platformy Azure, która nawiązuje połączenie z wystąpieniami BareMetal. Użyj tej samej subskrypcji, która jest używana do wdrażania warstwy aplikacji, która działa w odniesieniu do wystąpień BareMetal.
 
Podczas wdrażania wystąpień usługi BareMetal zostanie utworzona nowa [Grupa zasobów platformy Azure](../azure-resource-manager/management/manage-resources-portal.md) w ramach subskrypcji platformy Azure użytej w żądaniu wdrożenia. Ta nowa grupa zasobów zawiera listę wszystkich wystąpień BareMetal wdrożonych w ramach tej subskrypcji.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W subskrypcji BareMetal w Azure Portal wybierz pozycję **grupy zasobów**.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Zrzut ekranu przedstawiający listę grup zasobów.":::

1. Zlokalizuj nową grupę zasobów na liście.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Zrzut ekranu przedstawiający wystąpienie BareMetal na liście filtrowane grupy zasobów." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Można filtrować według subskrypcji użytej do wdrożenia wystąpienia usługi BareMetal. Po przefiltrowaniu do odpowiedniej subskrypcji może istnieć długa lista grup zasobów. Poszukaj jednego z **TXXX** , gdzie xxx to trzy cyfry, takie jak **-T250**.

1. Wybierz nową grupę zasobów, aby wyświetlić jej szczegóły. Obraz pokazuje jedno wdrożone wystąpienie BareMetal.
   
   >[!NOTE]
   >Jeśli w ramach tej samej subskrypcji platformy Azure wdrożono kilka dzierżawców wystąpień BareMetal, zobaczysz wiele grup zasobów platformy Azure.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić wszystkie wystąpienia usługi BareMetal, uruchom polecenie [AZ baremetalinstance list](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) dla grupy zasobów:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> `--output`Parametr jest parametrem globalnym, który jest dostępny dla wszystkich poleceń. Wartość **tabeli** przedstawia dane wyjściowe w przyjaznym formacie. Aby uzyskać więcej informacji, zobacz [formaty danych wyjściowych dla poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Wyświetlanie atrybutów pojedynczego wystąpienia

Można wyświetlić szczegóły jednego wystąpienia.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Na liście wystąpień BareMetal wybierz pojedyncze wystąpienie, które chcesz wyświetlić.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Zrzut ekranu przedstawiający atrybuty wystąpienia BareMetal jednego wystąpienia." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Atrybuty w obrazie różnią się od atrybutów maszyny wirtualnej platformy Azure. Po lewej stronie zobaczysz grupę zasobów, region platformy Azure oraz nazwę i Identyfikator subskrypcji. Jeśli masz przypisane znaczniki, zobaczysz je tutaj. Domyślnie wystąpienia BareMetal nie mają przypisanych tagów.
 
Po prawej stronie zostanie wyświetlona nazwa wystąpienia BareMetal, systemu operacyjnego (OS), adresu IP i jednostki SKU, która pokazuje liczbę wątków procesora CPU i pamięci. Zobaczysz również stan zasilacza i wersja sprzętowa (poprawka sygnatury wystąpienia BareMetal). Stan zasilania wskazuje, czy jednostka sprzętowa jest włączona, czy wyłączona. Jednak szczegóły systemu operacyjnego nie wskazują, czy jest on uruchomiony.
 
Możliwe wersje sprzętu to:

* Poprawka 3 (rev 3)

* Poprawka 4 (rev 4)
 
* Poprawka 4,2 (rev 4,2)
 
>[!NOTE]
>Rev 4,2 to najnowsza BareMetal infrastruktura z wykorzystaniem istniejącej architektury rev 4. Rev 4 zapewnia bliższą bliskość hostom maszyn wirtualnych platformy Azure. Ma znaczące ulepszenia opóźnienia sieci między maszynami wirtualnymi platformy Azure i wystąpieniami SAP HANA. Możesz uzyskać dostęp do wystąpień BareMetal i zarządzać nimi za pomocą Azure Portal. Aby uzyskać więcej informacji, zobacz [infrastruktura BareMetal na platformie Azure](concepts-baremetal-infrastructure-overview.md).

 
Ponadto po prawej stronie znajduje się nazwa [grupy umieszczania usługi Azure zbliżeniowe](../virtual-machines/co-location.md) , która jest tworzona automatycznie dla każdego wdrożonego wystąpienia BareMetal. Odwołuje się do grupy położenia zbliżeniowe podczas wdrażania maszyn wirtualnych platformy Azure, które obsługują warstwę aplikacji. W przypadku korzystania z grupy umieszczania zbliżeniowe skojarzonej z wystąpieniem BareMetal upewnij się, że maszyny wirtualne platformy Azure zostaną wdrożone blisko wystąpienia BareMetal.
 
>[!TIP]
>Aby zlokalizować warstwę aplikacji w tym samym centrum danych platformy Azure w wersji 4. x, zobacz [grupy umieszczania zbliżeniowe platformy Azure w celu uzyskania optymalnego opóźnienia sieci](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić szczegóły wystąpienia BareMetal, uruchom polecenie [AZ baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) :

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Jeśli jesteś niepewną nazwą wystąpienia, uruchom `az baremetalinstance list` polecenie opisane powyżej.

---
 
## <a name="check-activities-of-a-single-instance"></a>Sprawdź działania pojedynczego wystąpienia
 
Możesz sprawdzić działania pojedynczego wystąpienia BareMetal. Jednym z zarejestrowanych działań głównych jest ponowne uruchomienie wystąpienia. Wymienione dane obejmują stan działania, sygnaturę czasową wyzwalane przez działanie, Identyfikator subskrypcji i użytkownika platformy Azure, który wyzwolił działanie.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Zrzut ekranu przedstawiający działania wystąpienia BareMetal." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Zmiany metadanych wystąpienia na platformie Azure są również rejestrowane w dzienniku aktywności. Oprócz zainicjowanego ponownego uruchomienia można zobaczyć działanie **BareMetallnstances zapisu**. To działanie nie wprowadza żadnych zmian w wystąpieniu BareMetal, ale dokumentuje zmiany w metadanych jednostki na platformie Azure.
 
Po dodaniu lub usunięciu [znacznika](../azure-resource-manager/management/tag-resources.md) do wystąpienia jest możliwe inne działanie, które jest rejestrowane.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Dodawanie i usuwanie tagu platformy Azure do wystąpienia

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Możesz dodać tagi platformy Azure do wystąpienia BareMetal lub usunąć je. Znaczniki są przypisywane tak samo, jak podczas przypisywania tagów do maszyn wirtualnych. Tak jak w przypadku maszyn wirtualnych, Tagi istnieją w metadanych platformy Azure. Tagi mają takie same ograniczenia dotyczące wystąpień BareMetal, jak w przypadku maszyn wirtualnych.
 
Usuwanie tagów działa również tak samo jak w przypadku maszyn wirtualnych. Stosowanie i usuwanie tagu znajduje się w dzienniku aktywności wystąpienia BareMetal.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Przypisanie tagów do wystąpień BareMetal działa tak samo jak przypisanie tagów dla maszyn wirtualnych. Tak jak w przypadku maszyn wirtualnych, Tagi istnieją w metadanych platformy Azure. Tagi mają takie same ograniczenia dotyczące wystąpień BareMetal, jak w przypadku maszyn wirtualnych.

Aby dodać tagi do wystąpienia BareMetal, uruchom polecenie [AZ baremetalinstance Update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) :

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Użyj tego samego polecenia, aby usunąć tag:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Sprawdź właściwości wystąpienia
 
Po uzyskaniu wystąpień możesz przejść do sekcji właściwości, aby wyświetlić zebrane dane o wystąpieniach. Zbierane dane obejmują łączność z platformą Azure, zaplecze magazynu, Identyfikator obwodu ExpressRoute, unikatowy identyfikator zasobu i Identyfikator subskrypcji. Te informacje będą używane w żądaniach obsługi lub podczas konfigurowania konfiguracji migawki magazynu.
 
Inną krytyczną informacją, która widzisz, jest adres IP magazynu NFS. Izoluje on magazyn do **dzierżawy** w stosie wystąpienia BareMetal. Ten adres IP będzie używany podczas edytowania [pliku konfiguracji kopii zapasowych migawek magazynu](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Zrzut ekranu przedstawiający ustawienia właściwości wystąpienia BareMetal." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Uruchom ponownie wystąpienie BareMetal za pomocą Azure Portal

Istnieją różne sytuacje, w których system operacyjny nie kończy ponownego uruchamiania, co wymaga ponownego uruchomienia wystąpienia BareMetal.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Możesz ponownie uruchomić wystąpienie bezpośrednio z Azure Portal:
 
Wybierz pozycję **Uruchom ponownie** , a następnie przycisk **tak** , aby potwierdzić ponowne uruchomienie.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Zrzut ekranu przedstawiający sposób ponownego uruchomienia wystąpienia programu BareMetal.":::
 
Po ponownym uruchomieniu wystąpienia BareMetal wystąpi opóźnienie. W tym opóźnieniu stan siły jest przenoszony od **początku** do **uruchomionego**, co oznacza, że system operacyjny został całkowicie uruchomiony. W związku z tym po ponownym uruchomieniu można zalogować się do jednostki tylko wtedy, gdy stan zostanie **uruchomiony**.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ponownie uruchomić wystąpienie BareMetal, użyj polecenia [AZ baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) :

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>W zależności od ilości pamięci w wystąpieniu BareMetal ponowne uruchomienie i ponowne uruchomienie sprzętu i systemu operacyjnego może trwać do godziny.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Otwórz żądanie obsługi dla wystąpień usługi BareMetal
 
Można przesłać żądania pomocy technicznej przeznaczone dla wystąpień BareMetal.
1. W Azure Portal, w obszarze **Pomoc i obsługa techniczna**, Utwórz **[nowe żądanie pomocy technicznej](https://rc.portal.azure.com/#create/Microsoft.Support)** i podaj następujące informacje dotyczące biletu:
 
   - **Typ problemu:** Wybierz typ problemu.
 
   - **Subskrypcja:** Wybierz swoją subskrypcję.
 
   - **Usługa:** Infrastruktura BareMetal
 
   - **Zasób:** Podaj nazwę wystąpienia.
 
   - **Podsumowanie:** Podaj podsumowanie Twojego żądania.
 
   - **Typ problemu:** Wybierz typ problemu.
 
   - **Podtyp problemu:** Wybierz podtyp dla problemu.

1. Wybierz kartę **rozwiązania** , aby znaleźć rozwiązanie problemu. Jeśli nie możesz znaleźć rozwiązania, przejdź do następnego kroku.

1. Wybierz kartę **szczegóły** i wybierz, czy problem dotyczy maszyn wirtualnych czy wystąpień BareMetal. Te informacje ułatwiają kierowanie prośby o pomoc techniczną do właściwych specjalistów.

1. Wskaż, kiedy problem pojawił się i wybierz region wystąpienia.

1. Podaj więcej szczegółowych informacji o żądaniu i Przekaż plik w razie konieczności.

1. Wybierz pozycję **Przegląd + Utwórz** , aby przesłać żądanie.
 
Potwierdzenie Twojego wniosku może potrwać do pięciu dni roboczych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat obciążeń:

- [Co to jest SAP HANA na platformie Azure (duże wystąpienia)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
