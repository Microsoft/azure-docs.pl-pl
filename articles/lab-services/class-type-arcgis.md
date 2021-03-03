---
title: Konfigurowanie programu Lab for ArcMap\ArcGIS Desktop przy użyciu Azure Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium dla klas przy użyciu ArcGIS.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 530597a72b19afa1e80b5c7640b105d86479b1c1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740107"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Konfigurowanie programu Lab for ArcMap\ArcGIS Desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) to typ geograficznego systemu informacji (GIS).  ArcGIS jest używany do make\analyze map i pracy z danymi geograficznymi dostarczanymi przez [Instytut badania systemów środowiskowych](https://www.esri.com/en-us/home) (ESRI).  Mimo że ArcGIS Desktop zawiera kilka aplikacji, w tym artykule przedstawiono sposób konfigurowania laboratoriów do korzystania z ArcMap.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) służy do wprowadzania, edytowania i analizowania map 2D.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby rozpocząć Konfigurowanie laboratorium do korzystania z ArcMap, musisz mieć subskrypcję platformy Azure i konto laboratorium.  Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

Po uzyskaniu subskrypcji platformy Azure Możesz utworzyć nowe konto laboratorium w Azure Lab Services.  Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [Konfigurowanie konta laboratorium](tutorial-setup-lab-account.md).  Możesz również użyć istniejącego konta laboratorium.

### <a name="lab-account-settings"></a>Ustawienia konta laboratorium

Włącz ustawienia konta laboratorium zgodnie z opisem w poniższej tabeli.  Aby uzyskać więcej informacji o sposobie włączania obrazów w portalu Azure Marketplace, zobacz temat [Określanie obrazów portalu Azure Marketplace dostępnych dla twórców laboratorium](./specify-marketplace-images.md).

| Ustawienie konta laboratorium | Instrukcje |
| ------------------- | ------------ |
|Obraz z witryny Marketplace| Włącz obraz systemu Windows 10 Pro lub Windows 10 Pro N do użycia w ramach konta laboratorium.|

### <a name="licensing-server"></a>Serwer licencjonowania

Jednym z typów licencjonowania, które oferuje ArcGIS Desktop, jest [współbieżne używanie licencji](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  Wymaga to zainstalowania Menedżera licencji ArcGIS na serwerze licencji.  Menedżer licencji śledzi liczbę kopii oprogramowania, które mogą być uruchamiane w tym samym czasie.  Aby uzyskać więcej informacji na temat konfigurowania Menedżera licencji na serwerze, zobacz [Przewodnik po programie License Manager](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

Serwer licencji zazwyczaj znajduje się w sieci lokalnej lub jest hostowany na maszynie wirtualnej platformy Azure w ramach sieci wirtualnej platformy Azure.  Po skonfigurowaniu serwera licencji musisz nawiązać [połączenie równorzędne z siecią wirtualną](./how-to-connect-peer-virtual-network.md) przy użyciu [konta laboratorium](./tutorial-setup-lab-account.md).  Przed utworzeniem laboratorium należy wykonać komunikację równorzędną sieci, aby maszyny wirtualne laboratorium mogły uzyskiwać dostęp do serwera licencji i na odwrót.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie serwera licencji jako zasobu udostępnionego](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Ustawienia laboratorium

Rozmiar maszyny wirtualnej (VM), która jest zalecana w przypadku komputerów z systemem ArcGIS, zależy od aplikacji, rozszerzeń i konkretnych wersji, które będą używane przez uczniów.  Rozmiar maszyny wirtualnej zależy również od obciążeń, które mogą zostać wykonane przez uczniów.  Zapoznaj się z wymaganiami dotyczącymi [systemu ArcGIS Desktop](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) , aby ułatwić identyfikację rozmiaru maszyny wirtualnej.  Po zidentyfikowaniu potencjalnego rozmiaru maszyny wirtualnej zalecamy przetestowanie obciążeń uczniów w celu zapewnienia odpowiedniej wydajności.

W tym artykule zalecamy użycie [ **średniego** rozmiaru maszyny wirtualnej](administrator-guide.md#vm-sizing) w wersji [10.7.1 ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm), przy założeniu, że nie są używane żadne inne rozszerzenia pulpitu ArcGIS.  Jednakże, w zależności od potrzeb klasy, może być wymagane **duże** lub nawet **Small\Medium procesora GPU (wizualizacja)** .  Na przykład [rozszerzenie analityka przestrzennego](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) dołączonego do pulpitu ArcGIS obsługuje procesor GPU w celu zwiększenia wydajności, ale nie wymaga użycia procesora GPU.

| Ustawienie laboratorium | Wartość i opis |
| ------------ | ------------------ |
|Rozmiar maszyny wirtualnej| **Średni**.  Najlepiej dopasowane do relacyjnych baz danych, buforowania w pamięci i analizy.|  

### <a name="template-machine"></a>Komputer szablonu

Kroki opisane w tej sekcji pokazują, jak skonfigurować maszynę wirtualną szablonu:

1.  Uruchom szablon VM i Połącz się z maszyną przy użyciu protokołu RDP.

2.  Pobierz i Zainstaluj składniki pulpitu ArcGIS przy użyciu instrukcji z ESRI.  Te kroki obejmują przypisanie Menedżera licencji do licencjonowania współbieżnego użycia: 
    - [Wprowadzenie do instalowania i konfigurowania pulpitu ArcGIS](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Skonfiguruj zewnętrzny magazyn kopii zapasowych dla uczniów.  Studenci mogą zapisywać pliki bezpośrednio dla przypisanej do nich maszyn wirtualnych, ponieważ wszystkie wprowadzone zmiany są zapisywane między sesjami.  Zaleca się jednak, aby uczniowie tworzyli kopie zapasowe swoich prac do magazynu, który jest zewnętrzny od swojej maszyny wirtualnej z kilku powodów:
    - Aby umożliwić uczniom dostęp do swojej pracy po zakończeniu klasy i laboratorium.  
    - Na wypadek, gdy student uzyska niewłaściwy stan maszyny wirtualnej, a jego obraz wymaga [zresetowania](how-to-set-virtual-machine-passwords.md#reset-vms).

    W przypadku ArcGIS każdy student powinien utworzyć kopię zapasową następujących plików na końcu każdej sesji pracy:

    - plik MXD, który przechowuje informacje o układzie dla projektu.
    - Plik geobazy danych, w którym są przechowywane wszystkie dane tworzone przez ArcGIS.
    - Wszystkie inne dane, które mogą być używane przez studenta, takie jak pliki rastrowe, pliku kształtów, GeoTIFF itp.

    Zalecamy używanie usługi OneDrive do przechowywania kopii zapasowych.  Aby skonfigurować usługę OneDrive na maszynie wirtualnej szablonu, wykonaj kroki opisane w artykule [Instalowanie i Konfigurowanie usługi OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  Na koniec [Opublikuj](how-to-create-manage-template.md#publish-the-template-vm) maszynę wirtualną z szablonem, aby utworzyć maszynę wirtualną uczniów.

### <a name="auto-shutdown-and-disconnect-settings"></a>Ustawienia automatycznego zamykania i rozłączania

[Ustawienia automatycznego zamykania i rozłączania](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) laboratorium pomagają upewnić się, że maszyna wirtualna ucznia jest wyłączona, gdy nie jest używana.  Te ustawienia powinny być ustawiane na podstawie typów obciążeń, które będą wykonywane przez uczniów, tak aby ich maszyny wirtualne nie były zamykane w trakcie pracy.  Na przykład ustawienie " **Rozłącz użytkowników, gdy maszyny wirtualne są bezczynne** " rozłącza studenta od sesji RDP, gdy nie wykryto danych wejściowych myszy lub klawiatury przez określony czas.  To ustawienie musi zapewniać wystarczającą ilość czasu dla obciążeń, w których student nie korzysta aktywnie z myszy lub klawiatury, na przykład w celu uruchamiania długich zapytań lub poczekania na renderowanie.

W przypadku ArcGIS zalecamy następujące wartości dla tych ustawień:
- Rozłącz użytkowników, gdy maszyny wirtualne są w stanie bezczynności
    - 30 minut po wykryciu stanu bezczynności
- Zamknij maszyny wirtualne po rozłączeniu użytkowników
    - 15 minut po rozłączeniu użytkownika

## <a name="cost"></a>Koszt

Przyjrzyjmy się możliwemu szacunkowi kosztów dla tej klasy. To oszacowanie nie obejmuje kosztu uruchomienia serwera licencji. Będziemy używać klasy 25 studentów. Zaplanowana godzina klasy wynosi 20 godzin. Ponadto każdy student otrzymuje limit 10 godzin dla prac domowych lub przydziałów poza zaplanowanym czasem klasy. Wybrany rozmiar maszyny wirtualnej to **średnia**, czyli 42 jednostek laboratorium.

25 studentów \* (20 zaplanowanych godzin + 10 godzin przydziału) \* 42 jednostek laboratoryjnych * 0,01 USD za godzinę = 315,00 USD

>[!IMPORTANT]
> Oszacowanie kosztów odbywa się tylko na przykład.  Aby uzyskać aktualne informacje o cenach, zobacz [Cennik usługi Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)