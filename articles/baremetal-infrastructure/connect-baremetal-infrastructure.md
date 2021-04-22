---
title: Łączenie wystąpień infrastruktury BareMetal na platformie Azure
description: Dowiedz się, jak identyfikować wystąpienia programu BareMetal i korzystać z Azure Portal interfejsu wiersza polecenia platformy Azure.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: e67ede85608f2a33dcc179005f0090ca2ce6a640
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871655"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Łączenie wystąpień infrastruktury BareMetal na platformie Azure

W tym artykule [pokazano, jak Azure Portal](https://portal.azure.com/) są wyświetlane wystąpienia aplikacji [BareMetal.](concepts-baremetal-infrastructure-overview.md) W tym artykule pokazano również, co można zrobić w Azure Portal z wdrożonych wystąpień infrastruktury BareMetal. 
 
## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów
Dostawca zasobów platformy Azure dla wystąpień bareMetal zapewnia widoczność wystąpień w Azure Portal. Domyślnie subskrypcja platformy Azure, która jest używana dla wdrożeń wystąpień programu BareMetal, rejestruje dostawcę zasobów *BareMetalInfrastructure.* Jeśli nie widzisz wdrożonych wystąpień systemu BareMetal, musisz zarejestrować dostawcę zasobów w swojej subskrypcji. 

Dostawcę zasobów wystąpienia programu BareMetal można zarejestrować przy użyciu interfejsu wiersza Azure Portal platformy Azure.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Musisz wyświetlić swoją subskrypcję w skrypcie usługi Azure Portal a następnie kliknąć dwukrotnie subskrypcję używaną do wdrażania wystąpień aplikacji BareMetal.
 
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu witryny Azure Portal wybierz pozycję **Wszystkie usługi**.

1. W polu **Wszystkie usługi** wprowadź wartość **subscription**, a następnie wybierz **pozycję Subskrypcje.**

1. Wybierz subskrypcję z listy subskrypcji.

1. Wybierz **pozycję Dostawcy zasobów** i w polu wyszukiwania wprowadź **BareMetalInfrastructure.** Dostawca zasobów powinien być zarejestrowany **,** jak pokazano na ilustracji.
 
>[!NOTE]
>Jeśli dostawca zasobów nie jest zarejestrowany, wybierz pozycję **Zarejestruj**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Zrzut ekranu przedstawiający zarejestrowane wystąpienia aplikacji BareMetal.":::

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby rozpocząć korzystanie z interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Zaloguj się do subskrypcji platformy Azure służącej do wdrażania wystąpienia programu BareMetal za pośrednictwem interfejsu wiersza polecenia platformy Azure. Zarejestruj dostawcę `BareMetalInfrastructure` zasobów za pomocą polecenia az provider [register:](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Aby wyświetlić wszystkich dostępnych dostawców, możesz użyć polecenia [az provider list.](/cli/azure/provider#az_provider_list)

---

Aby uzyskać więcej informacji na temat dostawców zasobów, zobacz [Azure resource providers and types (Dostawcy i typy zasobów platformy Azure).](../azure-resource-manager/management/resource-providers-and-types.md)  

## <a name="baremetal-instances-in-the-azure-portal"></a>Wystąpienia aplikacji BareMetal w Azure Portal
 
Podczas przesyłania żądania wdrożenia wystąpienia programu BareMetal należy określić subskrypcję platformy Azure, która jest nawiązywana z wystąpieniami programu BareMetal. Użyj tej samej subskrypcji, która umożliwia wdrożenie warstwy aplikacji, która działa w przypadku wystąpień programu BareMetal.
 
Podczas wdrażania wystąpień programu BareMetal nowa grupa zasobów platformy [Azure](../azure-resource-manager/management/manage-resources-portal.md) jest tworzona w subskrypcji platformy Azure użytej w żądaniu wdrożenia. Ta nowa grupa zasobów zawiera listę wszystkich wystąpień systemu BareMetal wdrożonych w tej subskrypcji.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W subskrypcji bareMetal w skrypcie Azure Portal grupy **zasobów.**
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Zrzut ekranu przedstawiający listę grup zasobów.":::

1. Na liście znajdź nową grupę zasobów.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Zrzut ekranu przedstawiający wystąpienie aplikacji BareMetal na odfiltrowanych listach grup zasobów." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Możesz filtrować według subskrypcji użytej do wdrożenia wystąpienia systemu BareMetal. Po odfiltrowyniu odpowiedniej subskrypcji możesz mieć długą listę grup zasobów. Poszukaj jednej z nich z poprawkami **-Txxx,** gdzie xxx ma trzy cyfry, takie **jak -T250.**

1. Wybierz nową grupę zasobów, aby wyświetlić jej szczegóły. Obraz przedstawia wdrożone jedno wystąpienie systemu BareMetal.
   
   >[!NOTE]
   >Jeśli wdrożono kilka dzierżaw wystąpień programu BareMetal w ramach tej samej subskrypcji platformy Azure, zobaczysz wiele grup zasobów platformy Azure.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić wszystkie wystąpienia aplikacji BareMetal, uruchom polecenie [az baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) dla grupy zasobów:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> Parametr `--output` jest parametrem globalnym dostępnym dla wszystkich poleceń. Wartość **tabeli** przedstawia dane wyjściowe w przyjaznym formacie. Aby uzyskać więcej informacji, zobacz [Formaty danych wyjściowych dla poleceń interfejsu wiersza polecenia platformy Azure.](/cli/azure/format-output-azure-cli)

---

## <a name="view-the-attributes-of-a-single-instance"></a>Wyświetlanie atrybutów pojedynczego wystąpienia

Możesz wyświetlić szczegóły pojedynczego wystąpienia.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Na liście wystąpień aplikacji BareMetal wybierz pojedyncze wystąpienie, które chcesz wyświetlić.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Zrzut ekranu przedstawiający atrybuty wystąpienia bareMetal pojedynczego wystąpienia." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Atrybuty na obrazie nie wyglądają znacznie inaczej niż atrybuty maszyny wirtualnej platformy Azure. Po lewej stronie zobaczysz grupę zasobów, region świadczenia usługi Azure oraz nazwę i identyfikator subskrypcji. Jeśli przypisano tagi, zobaczysz je również tutaj. Domyślnie wystąpienia programu BareMetal nie mają przypisanych tagów.
 
Po prawej stronie zobaczysz nazwę wystąpienia systemu operacyjnego BareMetal, adresu IP i SKU, która pokazuje liczbę wątków procesora CPU i pamięci. Zobaczysz również stan zasilania i wersję sprzętu (poprawka sygnatury wystąpienia programu BareMetal). Stan zasilania wskazuje, czy jednostka sprzętowa jest wł. czy wyłączona. Szczegóły systemu operacyjnego nie wskazują jednak, czy jest on uruchomiony.
 
Możliwe poprawki sprzętu to:

* Poprawka 3 (Rev 3)

* Poprawka 4 (Rev 4)
 
* Poprawka 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2 to najnowsza rebranded BareMetal Infrastructure korzystająca z istniejącej architektury Rev 4. Wersja 4 zapewnia bliższą odległość od hostów maszyn wirtualnych platformy Azure. Ma znaczne ulepszenia w zakresie opóźnienia sieci między maszynami wirtualnych platformy Azure i SAP HANA wystąpieniami. Możesz uzyskać dostęp do wystąpień programu BareMetal i zarządzać nimi za pośrednictwem Azure Portal. Aby uzyskać więcej informacji, zobacz [BareMetal Infrastructure on Azure (Infrastruktura systemu BareMetal na platformie Azure).](concepts-baremetal-infrastructure-overview.md)

 
Ponadto po prawej stronie znajduje się nazwa grupy umieszczania w pobliżu platformy [Azure,](../virtual-machines/co-location.md) która jest tworzona automatycznie dla każdego wdrożonego wystąpienia programu BareMetal. Odwołaj się do grupy umieszczania w pobliżu podczas wdrażania maszyn wirtualnych platformy Azure, które host są hostami warstwy aplikacji. Korzystając z grupy umieszczania w pobliżu skojarzonej z wystąpieniem programu BareMetal, należy upewnić się, że maszyny wirtualne platformy Azure są wdrażane w pobliżu wystąpienia programu BareMetal.
 
>[!TIP]
>Aby zlokalizować warstwę aplikacji w tym samym centrum danych platformy Azure co wersja Poprawka 4.x, zobacz Grupy umieszczania w pobliżu platformy Azure w [celu uzyskania optymalnego opóźnienia sieci.](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wyświetlić szczegóły wystąpienia programu BareMetal, uruchom [polecenie az baremetalinstance show:](/cli/azure/baremetalinstance#az_baremetalinstance_show)

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Jeśli nie masz pewności co do nazwy wystąpienia, uruchom `az baremetalinstance list` polecenie opisane powyżej.

---
 
## <a name="check-activities-of-a-single-instance"></a>Sprawdzanie działań pojedynczego wystąpienia
 
Możesz sprawdzić działania pojedynczego wystąpienia baremetalu. Jednym z głównych zarejestrowanych działań są ponowne uruchomienia wystąpienia. Dane na liście obejmują stan działania, sygnaturę czasową wyzwolonego działania, identyfikator subskrypcji i użytkownika platformy Azure, który wyzwolił działanie.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Zrzut ekranu przedstawiający działania wystąpienia aplikacji BareMetal." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Zmiany metadanych wystąpienia na platformie Azure również są rejestrowane w dzienniku aktywności. Oprócz zainicjowania ponownego uruchamiania można zobaczyć działanie write **bareMetallnstances**. To działanie nie wprowadza żadnych zmian w samym wystąpieniu programu BareMetal, ale dokumentuje zmiany metadanych jednostki na platformie Azure.
 
Innym działaniem, które jest rejestrowane, jest dodanie lub usunięcie [tagu](../azure-resource-manager/management/tag-resources.md) do wystąpienia.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Dodawanie i usuwanie tagu platformy Azure do wystąpienia

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Tagi platformy Azure można dodać do wystąpienia programu BareMetal lub usunąć. Tagi są przypisywane tak samo jak podczas przypisywania tagów do maszyn wirtualnych. Podobnie jak w przypadku maszyn wirtualnych, tagi istnieją w metadanych platformy Azure. Tagi mają takie same ograniczenia dla wystąpień baremetalu, jak w przypadku maszyn wirtualnych.
 
Usuwanie tagów działa również tak samo jak w przypadku maszyn wirtualnych. Stosowanie i usuwanie tagu jest wyświetlane w dzienniku aktywności wystąpienia aplikacji BareMetal.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Przypisywanie tagów do wystąpień bareMetal działa tak samo jak przypisywanie tagów dla maszyn wirtualnych. Podobnie jak w przypadku maszyn wirtualnych tagi istnieją w metadanych platformy Azure. Tagi mają takie same ograniczenia dla wystąpień baremetalu, jak w przypadku maszyn wirtualnych.

Aby dodać tagi do wystąpienia programu BareMetal, uruchom [polecenie az baremetalinstance update:](/cli/azure/baremetalinstance#az_baremetalinstance_update)

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Użyj tego samego polecenia, aby usunąć tag:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Sprawdzanie właściwości wystąpienia
 
Po pozyskaniu wystąpień możesz przejść do sekcji Właściwości, aby wyświetlić zebrane dane dotyczące wystąpień. Zebrane dane obejmują łączność z platformą Azure, zaplecza magazynu, identyfikator obwodu usługi ExpressRoute, unikatowy identyfikator zasobu i identyfikator subskrypcji. Użyjesz tych informacji w żądaniach pomocy technicznej lub podczas konfigurowania konfiguracji migawki magazynu.
 
Innym krytycznym elementem informacji, które zobaczysz, jest adres IP systemu plików NFS magazynu. Izoluje magazyn do **dzierżawy w** stosie wystąpienia baremetalu. Tego adresu IP użyjesz podczas edytowania pliku konfiguracji dla [kopii zapasowych migawek magazynu.](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Zrzut ekranu przedstawiający ustawienia właściwości wystąpienia programu BareMetal." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Ponowne uruchamianie wystąpienia programu BareMetal za pośrednictwem Azure Portal

Istnieją różne sytuacje, w których system operacyjny nie zakończy ponownego uruchomienia, co wymaga ponownego uruchomienia zasilania wystąpienia programu BareMetal.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ponowne uruchomienie zasilania wystąpienia można wykonać bezpośrednio z Azure Portal:
 
Wybierz **pozycję Uruchom** ponownie, a następnie pozycję **Tak,** aby potwierdzić ponowne uruchomienie.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Zrzut ekranu przedstawiający sposób ponownego uruchamiania wystąpienia programu BareMetal.":::
 
Po ponownym uruchomieniu wystąpienia programu BareMetal wystąpi opóźnienie. W trakcie tego opóźnienia stan zasilania zmienia się z **Starting** **(Uruchamianie)** na Started (Rozpoczęto), co oznacza, że system operacyjny został uruchomiony całkowicie. W związku z tym po ponownym uruchomieniu możesz zalogować się do jednostki tylko wtedy, gdy stan przełączy się na **Uruchomino.**

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ponownie uruchomić wystąpienie programu BareMetal, użyj [polecenia az baremetalinstance restart:](/cli/azure/baremetalinstance#az_baremetalinstance_restart)

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>W zależności od ilości pamięci w wystąpieniu programu BareMetal ponowne uruchomienie oraz ponowne uruchomienie sprzętu i systemu operacyjnego może potrwać do godziny.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Otwieranie wniosku o pomoc techniczną dla wystąpień baremetalu
 
Żądania pomocy technicznej można przesyłać specjalnie dla wystąpień aplikacji BareMetal.
1. W Azure Portal obszarze **Pomoc i obsługa techniczna** utwórz nowy wniosek o pomoc **[techniczną](https://rc.portal.azure.com/#create/Microsoft.Support)** i podaj następujące informacje dotyczące biletu:
 
   - **Typ problemu:** Wybierz typ problemu.
 
   - **Subskrypcja:** Wybierz swoją subskrypcję.
 
   - **Usługa:** BareMetal Infrastructure
 
   - **Zasób:** Podaj nazwę wystąpienia.
 
   - **Podsumowanie:** Podaj podsumowanie żądania.
 
   - **Typ problemu:** Wybierz typ problemu.
 
   - **Podtyp problemu:** Wybierz podtyp problemu.

1. Wybierz **kartę Rozwiązania,** aby znaleźć rozwiązanie problemu. Jeśli nie możesz znaleźć rozwiązania, przejdź do następnego kroku.

1. Wybierz **kartę Szczegóły** i wybierz, czy problem dotyczy maszyn wirtualnych, czy wystąpień aplikacji BareMetal. Te informacje pomagają w kierowaniu wniosku o pomoc techniczną do odpowiednich specjalistów.

1. Wskaż, kiedy problem zaczął się pojawiać, i wybierz region wystąpienia.

1. Podaj więcej szczegółów dotyczących żądania i w razie potrzeby przekaż plik.

1. Wybierz **pozycję Przeglądanie + tworzenie,** aby przesłać żądanie.
 
Potwierdzenie żądania przez przedstawiciela pomocy technicznej może potrwać do pięciu dni roboczych.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o obciążeniach:

- [Co to jest SAP HANA na platformie Azure (duże wystąpienia)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
