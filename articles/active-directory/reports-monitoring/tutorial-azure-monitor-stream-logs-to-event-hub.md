---
title: Samouczek — przesyłanie strumieniowe dzienników do centrum zdarzeń platformy Azure | Microsoft Docs
description: Dowiedz się, jak skonfigurować Diagnostyka Azure do wypychania dzienników Azure Active Directory do centrum zdarzeń
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0443dcb2bf3bd58f2474c507c9f9594fb6d8a7f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469188"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>Samouczek: przesyłanie strumieniowe dzienników Azure Active Directory do centrum zdarzeń platformy Azure

W tym samouczku przedstawiono informacje na temat sposobu konfigurowania ustawień diagnostycznych usługi Azure Monitor w celu strumieniowego przesyłania dzienników usługi Azure Active Directory (Azure AD) do centrum zdarzeń platformy Azure. Ten mechanizm służy do integrowania dzienników z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) innych firm, takimi jak Splunk i QRadar.

## <a name="prerequisites"></a>Wymagania wstępne 

Do korzystania z tej funkcji są potrzebne następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Dzierżawa usługi Azure AD.
* Użytkownik będący *administratorem globalnym* lub *administratorem zabezpieczeń* dla tej dzierżawy usługi Azure AD.
* Przestrzeń nazw usługi Event Hubs i centrum zdarzeń w ramach subskrypcji platformy Azure. Dowiedz się, jak [utworzyć centrum zdarzeń](../../event-hubs/event-hubs-create.md).

## <a name="stream-logs-to-an-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. Wybierz kolejno pozycje **Azure Active Directory**  >  **monitorowanie**  >  **dzienników inspekcji**. 

3. Wybierz pozycję **Eksportuj ustawienia**.  
    
4. W okienku **Ustawienia diagnostyczne** wykonaj jedną z następujących czynności:
    * Aby zmienić istniejące ustawienia, wybierz polecenie **Edytuj ustawienie**.
    * Aby dodać nowe ustawienie, wybierz polecenie **Dodaj ustawienia diagnostyki**.  
      Możesz mieć maksymalnie trzy ustawienia.

      ![Eksportowanie ustawień](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Zaznacz pole wyboru **Przesyłaj strumieniowo do centrum zdarzeń** i wybierz pozycję **Centrum zdarzeń/Konfigurowanie**.

6. Wybierz subskrypcję platformy Azure i przestrzeń nazw usługi Event Hubs, do której chcesz kierować dzienniki.  
    Subskrypcja i przestrzeń nazw usługi Event Hubs muszą być skojarzone z dzierżawą usługi Azure AD, z której są przesyłane strumieniowo dzienniki. Możesz również określić centrum zdarzeń w przestrzeni nazw usługi Event Hubs, do której mają być wysyłane dzienniki. Jeśli nie określono żadnego centrum zdarzeń, centrum zdarzeń zostanie utworzone w przestrzeni nazw z domyślną nazwą **insights-logs-audit**.

7. Wybierz pozycję **OK**, aby zakończyć konfigurowanie centrum zdarzeń.

8. Wykonaj co najmniej jedną z następujących czynności:
    * Aby wysłać dzienniki inspekcji do centrum zdarzeń, zaznacz pole wyboru **AuditLogs** . 
    * Aby wysłać dzienniki logowania do centrum zdarzeń, zaznacz pole wyboru **SignInLogs** .

9. Wybierz pozycję **Zapisz**, aby zapisać ustawienie.

    ![Ustawienia diagnostyczne](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Po około 15 minutach sprawdź, czy zdarzenia są wyświetlane w centrum zdarzeń. Aby to zrobić, przejdź do centrum zdarzeń w portalu i sprawdź, czy liczba **komunikatów przychodzących** jest większa od zera. 

    ![Dzienniki inspekcji](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Uzyskiwanie dostępu do danych w centrum zdarzeń

Po wyświetleniu danych w centrum zdarzeń możesz uzyskać dostęp do danych i odczytać je na dwa sposoby:

* **Skonfiguruj obsługiwane narzędzie SIEM**. W celu odczytania danych z centrum zdarzeń większość narzędzi wymaga parametrów połączenia centrum zdarzeń i pewnych uprawnień do Twojej subskrypcji platformy Azure. Narzędzia innych firm oferujące integrację z usługą Azure Monitor to między innymi:
    
    * **ArcSight**: Aby uzyskać więcej informacji na temat integrowania dzienników usługi Azure AD z usługą Splunk, zobacz [integrowanie dzienników Azure Active Directory z ArcSight przy użyciu Azure monitor](howto-integrate-activity-logs-with-arcsight.md).
    
    * **Splunk**: aby uzyskać więcej informacji na temat sposobu integracji dzienników usługi Azure AD z narzędziem Splunk, zobacz [Integrate Azure AD logs with Splunk by using Azure Monitor (Integrowanie dzienników usługi Azure Active Directory z narzędziem Splunk za pomocą usługi Azure Monitor)](./howto-integrate-activity-logs-with-splunk.md).
    
    * **IBM QRadar**: składniki Microsoft Azure DSM i Microsoft Azure Event Hub Protocol są dostępne do pobrania w witrynie pomocy technicznej firmy [IBM](https://www.ibm.com/support). Aby uzyskać więcej informacji na temat integracji z platformą Azure, przejdź do witryny [IBM QRadar Security Intelligence Platform 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    * **Sumo Logic**: aby skonfigurować narzędzie Sumo Logic do pracy z danymi z centrum zdarzeń, zobacz [Install the Azure AD app and view the dashboards (Instalowanie aplikacji usługi Azure AD i wyświetlanie pulpitów nawigacyjnych)](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards). 

* **Skonfiguruj niestandardowe narzędzia**. Jeśli Twoje bieżące narzędzie SIEM nie jest jeszcze obsługiwane w diagnostyce usługi Azure Monitor, możesz skonfigurować narzędzia niestandardowe przy użyciu interfejsów API usługi Event Hubs. Aby dowiedzieć się więcej, zobacz [Wprowadzenie do odbierania komunikatów z centrum zdarzeń](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).


## <a name="next-steps"></a>Następne kroki

* [Integrowanie dzienników Azure Active Directory z usługą ArcSight przy użyciu Azure Monitor](howto-integrate-activity-logs-with-arcsight.md)
* [Integrate Azure AD logs with Splunk by using Azure Monitor (Integrowanie dzienników usługi Azure AD z narzędziem Splunk przy użyciu usługi Azure Monitor)](./howto-integrate-activity-logs-with-splunk.md)
* [Integrate Azure AD logs with SumoLogic by using Azure Monitor (Integrowanie dzienników usługi Azure AD z narzędziem SumoLogic przy użyciu usługi Azure Monitor)](howto-integrate-activity-logs-with-sumologic.md)
* [Interpretuj schemat dzienników inspekcji w Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpret sign-in logs schema in Azure Monitor (Interpretowanie schematu dzienników logowania w usłudze Azure Monitor)](reference-azure-monitor-sign-ins-log-schema.md)
