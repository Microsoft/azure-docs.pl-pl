---
title: Kontrola dużych wystąpień platformy Azure HANA za pomocą Azure Portal | Microsoft Docs
description: W tym artykule opisano sposób identyfikowania dużych wystąpień platformy Azure HANA i korzystania z nich przy użyciu portalu
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/31/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5adb8edbeafa4d77e24bf865338e111c455cb80
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827516"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Sterowania oprogramowaniem Azure HANA — duże wystąpienia za pośrednictwem witryny Azure Portal

>[!NOTE]
>W przypadku protokołu rev 4,2 Postępuj zgodnie z instrukcjami w temacie [Zarządzanie wystąpieniami BareMetal za pomocą tematu Azure Portal](../../../baremetal-infrastructure/workloads/sap/baremetal-infrastructure-portal.md) .

W tym dokumencie opisano sposób, w jaki są prezentowane [duże wystąpienia usługi Hana](./hana-overview-architecture.md) w [Azure Portal](https://portal.azure.com) i jakie działania mogą być wykonywane za pomocą Azure Portal z jednostkami dużej liczby wystąpień usługi Hana wdrożonych dla Ciebie. Widoczność dużych wystąpień usługi HANA w Azure Portal jest udostępniana przez dostawcę zasobów platformy Azure dla dużych wystąpień HANA, które są obecnie dostępne w publicznej wersji zapoznawczej

## <a name="register-hana-large-instance-resource-provider"></a>Zarejestruj dostawcę zasobów dużego wystąpienia HANA
Zwykle Twoja subskrypcja platformy Azure używana na potrzeby wdrożeń dużych wystąpień usługi HANA jest zarejestrowana dla dostawcy zasobów dużej ilości wystąpienia HANA. Jeśli jednak nie widzisz wdrożonych jednostek dużego wystąpienia HANA, należy zarejestrować dostawcę zasobów w ramach subskrypcji platformy Azure. Istnieją dwa sposoby rejestrowania dostawcy zasobów dużej instancji HANA

### <a name="register-through-cli-interface"></a>Zarejestruj za poorednictwem interfejsu wiersza polecenia
Musisz zalogować się do subskrypcji platformy Azure, która jest używana na potrzeby wdrożenia dużego wystąpienia HANA za pośrednictwem interfejsu wiersza polecenia platformy Azure. Możesz (ponownie) zarejestrować dostawcę dużego wystąpienia HANA przy użyciu tego polecenia:
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

Aby uzyskać więcej informacji, zobacz artykuł [dostawcy zasobów platformy Azure i typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)


### <a name="register-through-azure-portal"></a>Zarejestruj się, Azure Portal
Można (ponownie) zarejestrować dostawcę zasobów dużego wystąpienia HANA za pomocą Azure Portal. Musisz wyświetlić swoją subskrypcję w Azure Portal i kliknąć dwukrotnie subskrypcję, która została użyta do wdrożenia jednostek dużych wystąpień usługi HANA. Na stronie Przegląd subskrypcji wybierz pozycję "dostawcy zasobów", jak pokazano poniżej, a następnie wpisz "HANA" w oknie wyszukiwania. 

![Rejestrowanie protokołu HLI RP za Azure Portal](./media/hana-li-portal/portal-register-hli-rp.png)

Na wyświetlonym zrzucie ekranu dostawca zasobów został już zarejestrowany. Na wypadek, gdyby dostawca zasobów nie został jeszcze zarejestrowany, naciśnij pozycję "Zarejestruj ponownie" lub "Zarejestruj".

Aby uzyskać więcej informacji, zobacz artykuł [dostawcy zasobów platformy Azure i typy](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Wyświetlanie jednostek dużych wystąpień platformy HANA w Azure Portal
W przypadku przesyłania żądania wdrożenia dużego wystąpienia platformy HANA zostanie wyświetlony monit o określenie subskrypcji platformy Azure, która jest również nawiązywana z dużymi wystąpieniami programu HANA. Zalecane jest używanie tej samej subskrypcji, która jest używana do wdrażania warstwy aplikacji SAP, która działa z jednostkami dużych wystąpień usługi HANA.
Po wdrożeniu pierwszego wystąpienia usługi HANA zostanie utworzona nowa [Grupa zasobów platformy Azure](../../../azure-resource-manager/management/manage-resources-portal.md) w ramach subskrypcji platformy Azure przesłanej w żądaniu wdrożenia dla dużych wystąpień usługi Hana.  Nowa grupa zasobów zawiera listę wszystkich jednostek dużych wystąpień usługi HANA wdrożonych w określonej subskrypcji.

Aby znaleźć nową grupę zasobów platformy Azure, należy wyświetlić listę zasobów w ramach subskrypcji, przechodząc do okienka nawigacji po lewej stronie Azure Portal

![Zrzut ekranu, który podświetla opcję grupy zasobów.](./media/hana-li-portal/portal-resource-group.png)

Na liście grup zasobów pojawia się na liście. może być konieczne odfiltrowanie subskrypcji użytej do wdrożenia dużych wystąpień platformy HANA

![Filtrowanie grup zasobów w Azure Portal](./media/hana-li-portal/portal-filtering-subscription.png)

Po przefiltrowaniu do odpowiedniej subskrypcji nadal może istnieć długa lista grup zasobów. Poszukaj jednego z użyciem po usunięciu wartości **-TXXX** , gdzie "XXX" to trzy cyfry, takie jak **T050**. 

Po znalezieniu grupy zasobów należy wyświetlić jej szczegóły. Otrzymana lista może wyglądać następująco:

![Lista HLI w Azure Portal](./media/hana-li-portal/portal-hli-units-list.png)

Wszystkie jednostki wymienione na liście reprezentują pojedyncze jednostki dużego wystąpienia HANA, które zostały wdrożone w ramach subskrypcji. W takim przypadku należy zapoznać się z ośmioma jednostkami dużych wystąpień usługi HANA, które zostały wdrożone w ramach subskrypcji.

Jeśli w ramach tej samej subskrypcji platformy Azure wdrożono kilka dzierżawców dużych wystąpień usługi HANA, znajdziesz wiele grup zasobów platformy Azure 


## <a name="look-at-attributes-of-single-hli-unit"></a>Sprawdź atrybuty pojedynczego elementu.
Na liście jednostek dużego wystąpienia HANA można kliknąć pojedynczą jednostkę i uzyskać szczegółowe informacje o pojedynczej jednostce dużego wystąpienia platformy HANA. 

Po kliknięciu przycisku "Pokaż więcej" na ekranie przeglądu otrzymasz prezentację jednostki, która wygląda następująco:

![Pokaż przegląd jednostki elementu HLI](./media/hana-li-portal/portal-show-overview.png)

Oglądając różne pokazane atrybuty, te atrybuty wyglądają nieco inaczej niż atrybuty maszyny wirtualnej platformy Azure. W nagłówku po lewej stronie zostanie wyświetlona Grupa zasobów, region platformy Azure, nazwa subskrypcji i identyfikator, a także niektóre dodane Tagi. Domyślnie jednostki dużego wystąpienia HANA nie mają przypisanego znacznika. Po prawej stronie nagłówka Nazwa jednostki jest wyświetlana jako przypisana, gdy wdrożenie zostało wykonane. Zostanie wyświetlony system operacyjny, a także adres IP. Podobnie jak w przypadku maszyn wirtualnych typ jednostki dużego wystąpienia HANA o liczbie wątków procesora CPU i pamięci jest również pokazywany. Więcej szczegółów na temat różnych jednostek dużych wystąpień platformy HANA przedstawiono tutaj:

- [Dostępne jednostki SKU dla oprogramowania SAP HANA na platformie Azure — duże wystąpienia](./hana-available-skus.md)
- [Architektura magazynu SAP HANA (duże wystąpienia)](./hana-storage-architecture.md) 

Dodatkowe dane w prawej dolnej części to poprawka sygnatury dużego wystąpienia platformy HANA. Możliwe wartości:

- Poprawka 3
- Wersja 4

Poprawka 4 to najnowsza architektura wydawana w dużych wystąpieniach platformy HANA z istotnymi ulepszeniami opóźnień sieci między maszynami wirtualnymi Azure i jednostkami dużych wystąpień usługi HANA wdrożonymi w ramach poprawek lub wierszy poprawki 4.
Inne bardzo ważne informacje znajdują się w prawym dolnym rogu omówienia z nazwą grupy rozmieszczenia usługi Azure zbliżeniowe, która jest tworzona automatycznie dla każdej wdrożonej jednostki w dużej instancji HANA. Ta grupa umieszczania sąsiedztwa musi być przywoływana podczas wdrażania maszyn wirtualnych platformy Azure, które obsługują warstwę aplikacji SAP. Za pomocą [grupy umieszczania usługi Azure zbliżeniowe](../../linux/co-location.md) skojarzonej z jednostką dużego wystąpienia Hana należy upewnić się, że maszyny wirtualne platformy Azure są wdrożone w pobliżu jednostki usługi Hana duże wystąpienie. Sposób, w jaki można używać grup umieszczania zbliżeniowe do lokalizowania warstwy aplikacji SAP w tym samym centrum danych platformy Azure, ponieważ wersja 4 hostowanych jednostek dużego wystąpienia HANA jest opisana w [grupach umieszczania bliskości platformy Azure w celu uzyskania optymalnego opóźnienia sieci przy użyciu aplikacji SAP](sap-proximity-placement-scenarios.md).

Dodatkowe pole w prawej kolumnie nagłówka informuje o stanie mocy jednostki dużego wystąpienia HANA.

> [!NOTE]
> Stan zasilania opisuje, czy jednostka sprzętowa jest włączona, czy wyłączona. Nie zawiera on informacji o tym, że system operacyjny jest uruchomiony. Po ponownym uruchomieniu jednostki dużego wystąpienia platformy HANA wystąpi niewielki czas, w którym stan jednostki zostanie zmieniony **, aby rozpocząć** pracę w stanie **uruchomienia**. Stan **rozpoczęty** oznacza, że system operacyjny jest uruchamiany lub że system operacyjny został całkowicie uruchomiony. W związku z tym po ponownym uruchomieniu jednostki nie można spodziewać się natychmiastowego zalogowania się do jednostki, gdy stan zostanie **uruchomiony**.
> 

W przypadku naciśnięcia przycisku "Zobacz więcej" są wyświetlane dodatkowe informacje. W ramach jednej z dodatkowych informacji jest wyświetlana poprawka sygnatury dużego wystąpienia HANA, a jednostka została wdrożona w. Zapoznaj się z artykułem [co to jest SAP HANA na platformie Azure (duże wystąpienia)](./hana-overview-architecture.md) , aby poznać różne wersje sygnatur dużych wystąpień usługi Hana

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>Sprawdź działania pojedynczej jednostki dużego wystąpienia HANA 
Poza zadaniem omówienia jednostek dużego wystąpienia HANA można sprawdzić działania określonej jednostki. Dziennik aktywności może wyglądać następująco:

![Okienko nawigacji w Azure Portal](./media/hana-li-portal/portal-activity-list.png)

Jednym z głównych działań zarejestrowanych jest ponowne uruchomienie jednostki. Wymienione dane obejmują stan działania, sygnaturę czasową wyzwalane przez działanie, Identyfikator subskrypcji, z którego wywołano działanie, oraz użytkownika platformy Azure, który wyzwolił działanie. 

Inne działanie, które jest rejestrowane, to zmiany w jednostce w metadanych platformy Azure. Oprócz zainicjowanego ponownego uruchomienia można zobaczyć działanie **HANAInstances zapisu**. Ten typ działania nie wprowadza żadnych zmian w jednostce dużej instancji HANA, ale dokumentuje zmiany metadanych jednostki na platformie Azure. W wymienionym przypadku dodaliśmy i usunięto tag (zobacz następną sekcję).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>Dodawanie i usuwanie znacznika platformy Azure do jednostki dużego wystąpienia HANA
Kolejną możliwością jest dodanie [znacznika](../../../azure-resource-manager/management/tag-resources.md) do jednostki dużego wystąpienia Hana. Sposób przypisywania tagów nie różni się od przypisywania tagów do maszyn wirtualnych. Podobnie jak w przypadku maszyn wirtualnych, Tagi istnieją w metadanych platformy Azure, a w przypadku dużych wystąpień HANA mają takie same ograniczenia jak Tagi maszyn wirtualnych.

Usuwanie tagów działa tak samo jak w przypadku maszyn wirtualnych. Obie działania, zastosowanie i usunięcie znacznika zostaną wyświetlone w dzienniku aktywności określonej jednostki dużego wystąpienia HANA.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>Sprawdź właściwości jednostki dużego wystąpienia HANA
Sekcja **Właściwości** zawiera ważne informacje, które można uzyskać, gdy wystąpienia są przekazywane do użytkownika. Jest to sekcja, w której można uzyskać wszystkie informacje, które mogą być wymagane w przypadku pomocy technicznej lub które są potrzebne podczas konfigurowania konfiguracji migawki magazynu. W związku z tym ta sekcja jest kolekcją danych wokół wystąpienia, łącznością wystąpienia z platformą Azure i zapleczem magazynu. Góra sekcji wygląda następująco:


![Górna część właściwości elementu HLI w Azure Portal](./media/hana-li-portal/portal-properties-top.png)

Pierwsze kilka elementów danych zostało już wykorzystanych na ekranie przeglądu. Jednak ważną częścią danych jest Identyfikator obwodu ExpressRoute, który uzyskano jako pierwsze wdrożone jednostki. W niektórych przypadkach pomocy technicznej może zostać wyświetlony monit o podanie tych danych. W dolnej części zrzutu ekranu jest wyświetlany ważny wpis danych. Wyświetlane dane to adres IP nagłówka magazynu NFS, który izoluje magazyn do **dzierżawy** w stosie dużych wystąpień platformy Hana. Ten adres IP jest również wymagany podczas edytowania [pliku konfiguracji kopii zapasowych migawek magazynu](./hana-backup-restore.md#set-up-storage-snapshots). 

Przewinięcie w dół w okienku właściwości umożliwia uzyskanie dodatkowych danych, takich jak unikatowy identyfikator zasobu dla jednostki dużego wystąpienia usługi HANA, lub identyfikatora subskrypcji, który został przypisany do wdrożenia.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Uruchom ponownie jednostkę dużego wystąpienia platformy HANA za pomocą Azure Portal
Po zainicjowaniu ponownego uruchomienia systemu operacyjnego Linux istniały różne sytuacje, w których system operacyjny nie może pomyślnie zakończyć ponownego uruchomienia. Aby wymusić ponowne uruchomienie, należy otworzyć żądanie obsługi, aby program Microsoft wykonał ponowne uruchomienie jednostki usługi HANA o dużym wystąpieniu. Funkcja ponownego uruchamiania w ramach jednostki dużego wystąpienia HANA jest teraz zintegrowana z Azure Portal. Gdy jesteś w części Omówienie jednostki dużego wystąpienia usługi HANA, zobaczysz przycisk ponownego uruchomienia na początku sekcji danych.

![Uruchom krok #1 w Azure Portal](./media/hana-li-portal/portal-restart-first-step.png)

Po naciśnięciu przycisku Uruchom ponownie zostanie wyświetlony monit z pytaniem, czy na pewno chcesz ponownie uruchomić jednostkę. Po potwierdzeniu naciśnięcia przycisku "tak" jednostka zostanie ponownie uruchomiona.

> [!NOTE]
> W procesie ponownego uruchamiania wystąpi niewielki czas, w którym stan jednostki zostanie zmieniony **, aby rozpocząć przejście do stanu** **uruchomienia**. Stan **rozpoczęty** oznacza, że system operacyjny jest uruchamiany lub że system operacyjny został całkowicie uruchomiony. W związku z tym po ponownym uruchomieniu jednostki nie można spodziewać się natychmiastowego zalogowania się do jednostki, gdy stan zostanie **uruchomiony**.

> [!IMPORTANT]
> Zależnie od ilości pamięci w jednostce duże wystąpienie wystąpienia HANA, ponowne uruchomienie i ponowne uruchomienie sprzętu i systemu operacyjnego może trwać do godziny.


## <a name="open-a-support-request-for-hana-large-instances"></a>Otwórz żądanie obsługi dla dużych wystąpień usługi HANA
Poza Azure Portal wyświetlania jednostek dużych wystąpień usługi HANA można także tworzyć żądania pomocy technicznej przeznaczone dla jednostki dużego wystąpienia platformy HANA. Po zakończeniu działania linku **nowe żądanie obsługi** 

![Zainicjuj #1 kroku żądania usługi w Azure Portal](./media/hana-li-portal/portal-initiate-support-request.png)

Aby można było uzyskać usługę SAP HANA — duże wystąpienia na liście na następnym ekranie, może być konieczne wybranie opcji "wszystkie usługi", jak pokazano poniżej

![Wybierz wszystkie usługi w Azure Portal](./media/hana-li-portal/portal-create-service-request.png)

Na liście usług można znaleźć usługę **SAP HANA dużym wystąpieniu**. Po wybraniu tej usługi możesz wybrać określone typy problemów, jak pokazano poniżej:


![Wybierz klasę problemu w Azure Portal](./media/hana-li-portal/portal-select-problem-class.png)

W ramach każdego z różnych typów problemów oferowany jest wybór podtypów problemu, które należy wybrać, aby zwiększyć jego przyczynę. Po wybraniu podtypu możesz teraz nazwać temat. Po zakończeniu procesu wyboru można przejść do następnego kroku tworzenia. W sekcji **Solutions** należy zapoznać się z dokumentacją obejmującą duże wystąpienia Hana, co może dać wskaźnik do rozwiązania problemu. Jeśli nie możesz znaleźć rozwiązania problemu w dokumentacji, możesz przejść do następnego kroku. W następnym kroku zostanie wyświetlony monit z pytaniem o to, czy występuje problem z maszynami wirtualnymi, czy z jednostkami dużych wystąpień platformy HANA. Te informacje pomagają skierować żądanie pomocy technicznej do właściwych specjalistów. 

![Szczegóły dotyczące przypadku pomocy technicznej w Azure Portal](./media/hana-li-portal/portal-support-request-details.png)

Po udzieleniu odpowiedzi na pytania i dostarczeniu dodatkowych szczegółów możesz przejść do następnego kroku, aby przejrzeć żądanie pomocy technicznej i przesłać je.

## <a name="next-steps"></a>Następne kroki

- [Jak monitorować SAP HANA (duże wystąpienia) na platformie Azure](./troubleshooting-monitoring.md)
- [Monitorowanie i rozwiązywanie problemów ze strony oprogramowania HANA](./hana-monitor-troubleshoot.md)
