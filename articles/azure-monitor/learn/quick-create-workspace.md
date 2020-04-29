---
title: Tworzenie obszaru roboczego Log Analytics w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć obszar roboczy Log Analytics, aby umożliwić zarządzanie rozwiązaniami i zbieraniem danych ze środowisk w chmurze i lokalnych w Azure Portal.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 3c2e9d5634916c3713b7e3380c0496611d8f60a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77656283"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Utwórz obszar roboczy Log Analytics w Azure Portal
Użyj menu **obszary robocze log Analytics** , aby utworzyć obszar roboczy log Analytics przy użyciu Azure Portal. Obszar roboczy Log Analytics jest unikatowym środowiskiem dla Azure Monitor danych dziennika. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym. Musisz mieć Log Analytics obszar roboczy, jeśli zamierzasz zbierać dane z następujących źródeł:

* Zasoby platformy Azure w ramach subskrypcji
* Komputery lokalne monitorowane przez System Center Operations Manager
* Kolekcje urządzeń z Configuration Manager 
* Diagnostyka lub dane dziennika z usługi Azure Storage

W przypadku innych źródeł, takich jak maszyny wirtualne platformy Azure i maszyny wirtualne z systemem Windows lub Linux w danym środowisku, zobacz następujące tematy:

*  [Zbieranie danych z usługi Azure Virtual Machines](../learn/quick-collect-azurevm.md) 
*  [Zbieranie danych z hybrydowego komputera z systemem Linux](../learn/quick-collect-linux-computer.md)
*  [Zbieranie danych z hybrydowego komputera z systemem Windows](quick-collect-windows-computer.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
1. W Azure Portal kliknij pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.

    ![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
2. Kliknij przycisk **Dodaj**, a następnie wybierz opcje dla następujących elementów:

   * Podaj nazwę nowego **obszaru roboczego log Analytics**, na przykład *DefaultLAWorkspace*. Ta nazwa musi być globalnie unikatowa w ramach wszystkich subskrypcji Azure Monitor.
   * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
   * W obszarze **Grupa zasobów**wybierz już konfigurację istniejącej grupy zasobów lub Utwórz nową.  
   * Wybierz dostępną **lokalizację**.  Aby uzyskać więcej informacji, zobacz [regiony, w których log Analytics jest dostępna](https://azure.microsoft.com/regions/services/) , i Wyszukaj Azure monitor z pola **Wyszukaj produkt** .  
   * W przypadku tworzenia obszaru roboczego w nowej subskrypcji utworzonej po 2 kwietnia 2018 r. zostanie automatycznie użyty plan cenowy *Na GB*, a opcja wyboru warstwy cenowej będzie niedostępna.  W przypadku tworzenia obszaru roboczego dla istniejącej subskrypcji utworzonej przed 2 kwietnia lub dla subskrypcji powiązanej z istniejącą rejestracją Umowa Enterprise (EA) wybierz preferowaną warstwę cenową.  Aby uzyskać więcej informacji na temat określonych warstw, zobacz [log Analytics szczegóły cennika](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Utwórz blok zasobów Log Analytics](media/quick-create-workspace/create-loganalytics-workspace-02.png)  

3. Po podaniu wymaganych informacji w okienku **Obszar roboczy usługi Log Analytics** kliknij przycisk **OK**.  

Podczas weryfikowania informacji i tworzenia obszaru roboczego możesz śledzić postęp w sekcji **Powiadomienia** z poziomu menu. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy dostępny jest obszar roboczy, można skonfigurować zbieranie danych telemetrycznych monitorowania, uruchamiać wyszukiwania w dziennikach, aby analizować te dane, i dodać rozwiązanie do zarządzania, aby zapewnić dodatkowe dane i szczegółowe informacje analityczne. 

* Aby włączyć zbieranie danych z zasobów platformy Azure za pomocą usługi Diagnostyka Azure lub Azure Storage, zobacz [zbieranie dzienników usług platformy Azure i metryki do użycia w programie log Analytics](../platform/collect-azure-metrics-logs.md).  
* [Dodaj System Center Operations Manager jako źródło danych](../platform/om-agents.md) , aby zebrać dane z agentów zgłaszających Operations Manager grupy zarządzania i zapisać je w obszarze roboczym log Analytics. 
* Połącz [Configuration Manager](../platform/collect-sccm.md) , aby zaimportować komputery należące do kolekcji w hierarchii.  
* Przejrzyj dostępne [rozwiązania do monitorowania](../insights/solutions.md) oraz sposób dodawania lub usuwania rozwiązania z obszaru roboczego.
