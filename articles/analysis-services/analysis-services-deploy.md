---
title: Wdrażanie modelu do Azure Analysis Services przy użyciu programu Visual Studio | Microsoft Docs
description: Dowiedz się, jak wdrożyć model tabelaryczny na serwerze Azure Analysis Services przy użyciu programu Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 45ea75b65e75f9bac4d6d4839ffe7fa095e3b25d
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018780"
---
# <a name="deploy-a-model-from-visual-studio"></a>Wdrażanie modelu z programu Visual Studio

Po utworzeniu serwera w ramach subskrypcji platformy Azure wszystko jest gotowe do wdrożenia bazy danych modelu tabelarycznego. Możesz użyć programu Visual Studio z projektami Analysis Services do kompilowania i wdrażania projektu modelu tabelarycznego, nad którym pracujesz. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* **Serwer usług Analysis Services** na platformie Azure. Aby dowiedzieć się więcej, zobacz artykuł [Create an Azure Analysis Services server](analysis-services-create-server.md) (Tworzenie serwera usług Azure Analysis Services).
* **Projekt modelu tabelarycznego** w programie Visual Studio lub istniejący model tabelaryczny na poziomie zgodności 1200 lub wyższym. Nie wiesz, jak go utworzyć? Spróbuj skorzystać z [samouczka sprzedaży modelowania tabelarycznego projektu Adventure Works Internet Sales](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Brama lokalna** — jeśli co najmniej jedno źródło danych znajduje się w sieci organizacji, należy zainstalować [bramę danych lokalnych](analysis-services-gateway.md). Brama jest niezbędna, aby umożliwić serwerowi w chmurze łączenie się z lokalnymi źródłami danych w celu przetwarzania i odświeżania danych w modelu.

> [!TIP]
> Przed jej wdrożeniem upewnij się, że można przetwarzać dane w tabelach. W programie Visual Studio kliknij pozycję **model**procesu  >  **Process**  >  **All (wszystkie**). Jeśli przetwarzanie zakończy się niepowodzeniem, wdrożenie nie jest możliwe.
> 
> 

## <a name="get-the-server-name"></a>Pobierz nazwę serwera

Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Aby wdrożyć z programu Visual Studio

1. W programie Visual Studio > **Eksplorator rozwiązań**, kliknij prawym przyciskiem myszy > **Właściwości**projektu. Następnie na **Deployment**  >  **serwerze** wdrażania wklej nazwę serwera.   
   
    ![Wklejanie nazwy serwera we właściwościach serwera wdrażania](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy **Właściwości**, a następnie kliknij przycisk **Wdróż**. Może zostać wyświetlony monit o zalogowanie się do platformy Azure.
   
    ![Wdrażanie na serwerze](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Stan wdrożenia pojawia się w oknie danych wyjściowych i oknie wdrażania.
   
    ![Stan wdrożenia](./media/analysis-services-deploy/aas-deploy-status.png)

To wszystko!


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli wdrażanie nie powiedzie się, prawdopodobnie program Visual Studio nie może nawiązać połączenia z serwerem. Upewnij się, że możesz nawiązać połączenie z serwerem przy użyciu programu SQL Server Management Studio (SSMS). Upewnij się, że właściwość serwera wdrażania dla projektu jest poprawna.

Jeśli wdrożenie zakończy się niepowodzeniem dla tabeli, prawdopodobnie serwer nie mógł nawiązać połączenia ze źródłem danych. Jeśli źródło danych znajduje się w lokalnej sieci organizacji, należy zainstalować [bramę danych lokalnych](analysis-services-gateway.md).

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu modelu tabelarycznego na serwerze możesz się z nim połączyć. Możesz połączyć się z [nim za pomocą SQL Server Management Studio (SSMS)](analysis-services-manage.md) , aby zarządzać nim. Możesz również [nawiązać połączenie za pomocą narzędzia klienta](analysis-services-connect.md), takiego jak usługi Power BI, Power BI Desktop lub program Excel, i rozpocząć tworzenie raportów.   

Aby dowiedzieć się więcej na temat zaawansowanych metod wdrażania, zobacz temat [wdrażanie rozwiązania modelu tabelarycznego](/analysis-services/deployment/tabular-model-solution-deployment?view=azure-analysis-services-current).