---
title: Integracja Azure Monitor dla maszyn wirtualnych map z Operations Manager | Microsoft Docs
description: Azure Monitor dla maszyn wirtualnych automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. W tym artykule omówiono używanie funkcji map do automatycznego tworzenia diagramów aplikacji rozproszonych w Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 288e3f0c048bddb3f3378226889dc519de27764b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927892"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integracja System Center Operations Manager z funkcją Azure Monitor dla maszyn wirtualnych map

W Azure Monitor dla maszyn wirtualnych można wyświetlić odnalezione składniki aplikacji na maszynach wirtualnych z systemem Windows i Linux, które działają na platformie Azure lub w środowisku. Dzięki integracji między funkcją mapy a System Center Operations Manager można automatycznie tworzyć diagramy aplikacji rozproszonych w Operations Manager, które są oparte na dynamicznych mapach zależności w Azure Monitor dla maszyn wirtualnych. W tym artykule opisano sposób konfigurowania System Center Operations Manager grupy zarządzania w celu obsługi tej funkcji.

>[!NOTE]
>Jeśli wdrożono już Service Map, można wyświetlić mapy w Azure Monitor dla maszyn wirtualnych, co obejmuje dodatkowe funkcje do monitorowania kondycji i wydajności maszyn wirtualnych. Funkcja map Azure Monitor dla maszyn wirtualnych ma zastąpić rozwiązanie autonomiczne Service Map. Aby dowiedzieć się więcej, zobacz [Azure monitor dla maszyn wirtualnych Omówienie](vminsights-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* System Center Operations Manager grupy zarządzania (2012 R2 lub nowsza).
* Obszar roboczy Log Analytics skonfigurowany do obsługi Azure Monitor dla maszyn wirtualnych.
* Co najmniej jedna maszyna wirtualna z systemem Windows i Linux albo komputery fizyczne monitorowane przez Operations Manager i wysyłające dane do obszaru roboczego Log Analytics. Serwery z systemem Linux, które są raportowane do grupy zarządzania Operations Manager, muszą być skonfigurowane do bezpośredniego łączenia się z Azure Monitor. Aby uzyskać więcej informacji, zapoznaj się z omówieniem w temacie [zbieranie danych dziennika za pomocą agenta log Analytics](../platform/log-analytics-agent.md).
* Jednostka usługi z dostępem do subskrypcji platformy Azure, która jest skojarzona z obszarem roboczym Log Analytics. Aby uzyskać więcej informacji, przejdź do [tworzenia nazwy głównej usługi](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Zainstaluj pakiet administracyjny Service Map

Integrację między Operations Manager i funkcją mapy można włączyć, importując pakiet pakietów administracyjnych Microsoft.SystemCenter. ServiceMap (Microsoft.SystemCenter. ServiceMap. mpb). Pakiet pakietów administracyjnych można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). Pakiet zawiera następujące pakiety administracyjne:

* Widoki aplikacji Service Map firmy Microsoft
* Wewnętrzny Service Map programu Microsoft System Center
* Zastępowanie Service Map programu Microsoft System Center
* Service Map programu Microsoft System Center

## <a name="configure-integration"></a>Konfigurowanie integracji

Po zainstalowaniu pakietu administracyjnego Service Map nowy węzeł **Service map**, zostanie wyświetlony w obszarze **Pakiet Operations Management Suite** w okienku **Administracja** w konsoli operacje Operations Manager.

>[!NOTE]
>[Pakiet Operations Management Suite to kolekcja usług](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) , która zawiera log Analytics, jest teraz częścią [Azure monitor](../overview.md).

Aby skonfigurować integrację z mapowaniem Azure Monitor dla maszyn wirtualnych, wykonaj następujące czynności:

1. Aby otworzyć Kreatora konfiguracji, w okienku **przegląd Service map** kliknij pozycję **Dodaj obszar roboczy**.  

    ![Okienko przeglądu Service Map](media/service-map-scom/scom-configuration.png)

2. W oknie **Konfiguracja połączenia** wprowadź nazwę lub identyfikator dzierżawy, identyfikator aplikacji (znany również jako nazwa użytkownika lub clientID) i hasło nazwy głównej usługi, a następnie kliknij przycisk **dalej**. Aby uzyskać więcej informacji, przejdź do tworzenia nazwy głównej usługi.

    ![Okno konfiguracji połączenia](media/service-map-scom/scom-config-spn.png)

3. W oknie **wybór subskrypcji** wybierz subskrypcję platformy Azure, grupę zasobów platformy Azure (tę, która zawiera obszar roboczy log Analytics), a log Analytics obszar roboczy, a następnie kliknij przycisk **dalej**.

    ![Obszar roboczy konfiguracji Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. W oknie **Wybór grupy maszyn** wybierz grupy maszyn, które mają zostać zsynchronizowane Service map Operations Manager. Kliknij przycisk **Dodaj/Usuń grupy maszyn**, wybierz grupy z listy **dostępnych grup maszyn**, a następnie kliknij przycisk **Dodaj**.  Po zakończeniu wybierania grup kliknij przycisk **OK** , aby zakończyć.

    ![Grupy maszyn konfiguracji Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. W oknie **Wybór serwera** Skonfiguruj grupę serwery Service map z serwerami, które mają być synchronizowane między Operations Manager i funkcją map. Kliknij przycisk **Dodaj/Usuń serwery**.

    Aby można było utworzyć diagram aplikacji rozproszonej dla serwera, na serwerze musi być:

   * Monitorowane przez Operations Manager
   * Skonfigurowano w celu raportowania do Log Analytics obszaru roboczego skonfigurowanego za pomocą Azure Monitor dla maszyn wirtualnych
   * Wymienione w grupie serwery Service Map

     ![Grupa konfiguracji Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcjonalne: wybierz pulę zasobów wszystkich serwerów zarządzania, z którą ma się komunikować Log Analytics, a następnie kliknij pozycję **Dodaj obszar roboczy**.

    ![Zrzut ekranu przedstawiający ekran Pula serwerów w obszarze Dodawanie Microsoft Operations Management Suite obszarze roboczym z wybraną pulą zasobów wszystkie serwery zarządzania.](media/service-map-scom/scom-config-pool.png)

    Skonfigurowanie i zarejestrowanie obszaru roboczego Log Analytics może potrwać minutę. Po jego skonfigurowaniu Operations Manager inicjuje pierwszą synchronizację mapy.

    ![Zrzut ekranu przedstawiający ekran ukończenie w obszarze Dodawanie Microsoft Operations Management Suite obszaru roboczego potwierdzający, że obszar roboczy został dodany.](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitorowanie integracji

Po nawiązaniu połączenia z obszarem roboczym Log Analytics w okienku **monitorowanie** w konsoli operacje Operations Manager zostanie wyświetlony nowy folder Service map.

![Okienko monitorowanie Operations Manager](media/service-map-scom/scom-monitoring.png)

Folder Service Map ma cztery węzły:

* **Aktywne alerty**: zawiera listę wszystkich aktywnych alertów dotyczących komunikacji między Operations Manager i Azure monitor.  

  >[!NOTE]
  >Te alerty nie są Log Analytics alerty zsynchronizowane z Operations Manager, są generowane w grupie zarządzania w oparciu o przepływy pracy zdefiniowane w pakiecie administracyjnym Service Map.

* **Serwery**: Wyświetla listę monitorowanych serwerów, które są skonfigurowane do synchronizacji z funkcji Azure monitor dla maszyn wirtualnych map.

    ![Okienko Operations Manager monitorowania serwerów](media/service-map-scom/scom-monitoring-servers.png)

* **Widoki zależności grupy maszyn**: zawiera listę wszystkich grup maszyn, które są synchronizowane z funkcji mapy. Możesz kliknąć dowolną grupę, aby wyświetlić jej diagram aplikacji rozproszonej.

    ![Zrzut ekranu z Service Map przedstawiający diagram z obrazami dla każdej grupy maszyn i linie wskazujące zależności między nimi.](media/service-map-scom/scom-group-dad.png)

* **Widoki zależności serwera**: wyświetla wszystkie serwery, które są synchronizowane z funkcji mapy. Możesz kliknąć dowolny serwer, aby wyświetlić jego diagram aplikacji rozproszonej.

    ![Zrzut ekranu z Service Map pokazujący diagram z obrazami dla każdego serwera i linie wskazujące zależności między nimi.](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Edytowanie lub usuwanie obszaru roboczego

Skonfigurowany obszar roboczy można edytować lub usunąć za pomocą okienka **Przegląd Service map** (okienko **Administracja** > **Pakiet Operations Management Suite**  >  **Service map**).

> [!NOTE]
> [Pakiet Operations Management Suite to zbiór usług](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) należących do log Analytics, które są teraz częścią [Azure monitor](../overview.md).

W tej bieżącej wersji można skonfigurować tylko jeden obszar roboczy Log Analytics.

![Okienko Operations Manager edytowanie obszaru roboczego](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurowanie reguł i zastąpień

Reguła, *Microsoft.SystemCenter. ServiceMapImport. Rule*, okresowo pobiera informacje z funkcji mapy Azure monitor dla maszyn wirtualnych. Aby zmodyfikować interwał synchronizacji, można zastąpić regułę i zmodyfikować wartość parametru **IntervalMinutes**.

![Okno właściwości zastąpień Operations Manager](media/service-map-scom/scom-overrides.png)

* **Włączone**: włącza lub wyłącza aktualizacje automatyczne.
* **IntervalMinutes**: określa czas między aktualizacjami. Domyślny interwał wynosi godzinę. Jeśli chcesz synchronizować mapy częściej, możesz zmienić wartość.
* **TimeoutSeconds**: określa czas, po jakim upłynął limit czasu żądania.
* **TimeWindowMinutes**: określa przedział czasu na potrzeby wykonywania zapytań dotyczących danych. Wartość domyślna to 60 minut, co jest maksymalnym dozwolonym interwałem.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Bieżący projekt przedstawia następujące problemy i ograniczenia:

* Można nawiązać połączenie tylko z jednym obszarem roboczym Log Analytics.
* Mimo że serwery programu można dodać do grupy Serwery Service Map ręcznie za pomocą okienka **Tworzenie** , mapy dla tych serwerów nie są synchronizowane natychmiast. Zostaną one zsynchronizowane z funkcji mapy Azure Monitor dla maszyn wirtualnych podczas następnego cyklu synchronizacji.
* Po wprowadzeniu zmian w diagramach aplikacji rozproszonych utworzonych przez pakiet administracyjny te zmiany prawdopodobnie zostaną zastąpione podczas następnej synchronizacji z Azure Monitor dla maszyn wirtualnych.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby uzyskać oficjalną dokumentację platformy Azure dotyczącą tworzenia nazwy głównej usługi, zobacz:

* [Tworzenie jednostki usługi przy użyciu programu PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Tworzenie jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli)
* [Tworzenie jednostki usługi przy użyciu Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>Sugestie

Czy masz jakieś opinie o integracji z funkcją mapy Azure Monitor dla maszyn wirtualnych lub z tą dokumentacją? Odwiedź naszą [stronę głosową użytkownika](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), na której można zasugerować funkcje lub zagłosować na istniejące sugestie.

