---
title: Utwórz obszar roboczy Log Analytics w Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć obszar roboczy Log Analytics, aby umożliwić zarządzanie rozwiązaniami i zbieraniem danych ze środowisk w chmurze i lokalnych w Azure Portal.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/18/2021
ms.openlocfilehash: 27eac9cefe645087cae43c34cb6503b562fb7c07
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656320"
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure Portal
Użyj menu **obszary robocze log Analytics** , aby utworzyć obszar roboczy log Analytics przy użyciu Azure Portal. Obszar roboczy Log Analytics jest unikatowym środowiskiem dla Azure Monitor danych dziennika. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym. Musisz mieć Log Analytics obszar roboczy, jeśli zamierzasz zbierać dane z następujących źródeł:

* Zasoby platformy Azure w ramach subskrypcji
* Komputery lokalne monitorowane przez System Center Operations Manager
* Kolekcje urządzeń z Configuration Manager 
* Diagnostyka lub dane dziennika z usługi Azure Storage

W przypadku innych źródeł, takich jak maszyny wirtualne platformy Azure i maszyny wirtualne z systemem Windows lub Linux w danym środowisku, zobacz następujące tematy:

*  [Zbieranie danych z usługi Azure Virtual Machines](../vm/quick-collect-azurevm.md) 
*  [Zbieranie danych z hybrydowego komputera z systemem Linux](../vm/quick-collect-linux-computer.md)
*  [Zbieranie danych z hybrydowego komputera z systemem Windows](../vm/quick-collect-windows-computer.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
W Azure Portal kliknij pozycję **wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.

![Azure Portal](media/quick-create-workspace/azure-portal-01.png)
  
Kliknij przycisk **Dodaj**, a następnie podaj wartości następujących opcji:

   * Wybierz **Subskrypcję** do połączenia poprzez wybór subskrypcji z listy rozwijanej, jeśli domyślnie wybrana subskrypcja jest niewłaściwa.
   * W obszarze **Grupa zasobów** wybierz już konfigurację istniejącej grupy zasobów lub Utwórz nową.  
   * Podaj nazwę nowego **obszaru roboczego log Analytics**, na przykład *DefaultLAWorkspace*. Ta nazwa musi być globalnie unikatowa w ramach wszystkich subskrypcji Azure Monitor.
   * Wybierz dostępny **region**.  Aby uzyskać więcej informacji, zobacz [regiony, w których log Analytics jest dostępna](https://azure.microsoft.com/regions/services/) , i Wyszukaj Azure monitor z pola **Wyszukaj produkt** .  


        ![Utwórz blok zasobów Log Analytics](media/quick-create-workspace/create-workspace.png)  


Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć ustawienia, a następnie **Utwórz** , aby utworzyć obszar roboczy. Spowoduje to wybranie domyślnej warstwy cenowej z opcją płatność zgodnie z rzeczywistym użyciem, która nie spowoduje ponoszenia żadnych zmian do momentu zebrania wystarczającej ilości danych. Aby uzyskać więcej informacji na temat innych warstw cenowych, zobacz [log Analytics szczegóły cennika](https://azure.microsoft.com/pricing/details/log-analytics/).



## <a name="troubleshooting"></a>Rozwiązywanie problemów
Podczas tworzenia obszaru roboczego, który został usunięty w ciągu ostatnich 14 dni i w [stanie usuwania nietrwałego](../logs/delete-workspace.md#soft-delete-behavior), operacja może mieć różny wynik w zależności od konfiguracji obszaru roboczego:
1. Jeśli podano tę samą nazwę obszaru roboczego, grupę zasobów, subskrypcję i region, jak w usuniętym obszarze roboczym, obszar roboczy zostanie odzyskany, w tym jego dane, konfiguracja i agenci połączone.
2. W przypadku użycia tej samej nazwy obszaru roboczego, ale innej grupy zasobów, subskrypcji lub regionu zostanie wyświetlony błąd *Ta nazwa obszaru roboczego jest już używana. Spróbuj użyć innego*. Aby zastąpić nietrwałe usuwanie i trwałe usuwanie obszaru roboczego i utworzyć nowy obszar roboczy o tej samej nazwie, wykonaj następujące kroki, aby najpierw odzyskać obszar roboczy i wykonać trwałe usuwanie:
   - [Odzyskiwanie](../logs/delete-workspace.md#recover-workspace) obszaru roboczego
   - [Trwałe usunięcie](../logs/delete-workspace.md#permanent-workspace-delete) obszaru roboczego
   - Utwórz nowy obszar roboczy przy użyciu tej samej nazwy obszaru roboczego

## <a name="next-steps"></a>Następne kroki
Teraz, gdy dostępny jest obszar roboczy, można skonfigurować zbieranie danych telemetrycznych monitorowania, uruchamiać wyszukiwania w dziennikach, aby analizować te dane, i dodać rozwiązanie do zarządzania, aby zapewnić dodatkowe dane i szczegółowe informacje analityczne. 

* Zobacz [monitorowanie kondycji obszaru roboczego log Analytics w Azure monitor](../logs/monitor-workspace.md) tworzenia reguł alertów w celu monitorowania kondycji obszaru roboczego. 
* Zobacz [zbieranie dzienników usługi platformy Azure i metryki do użycia w log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) , aby umożliwić zbieranie danych z zasobów platformy Azure za pomocą Diagnostyka Azure lub usługi Azure Storage.
