---
title: Zarządzane łączniki dla Azure Logic Apps
description: Użyj wyzwalaczy i akcji zarządzanych przez firmę Microsoft, aby tworzyć zautomatyzowane przepływy pracy, które integrują inne aplikacje, dane, usługi i systemy przy użyciu Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: d50cf1009fb674f2d3d5914715dcbd9699565489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796924"
---
# <a name="managed-connectors-for-logic-apps"></a>Zarządzane łączniki dla Logic Apps

[Łączniki](apis-list.md) zarządzane zapewniają sposoby uzyskiwania dostępu do innych usług i systemów, w których wbudowane wyzwalacze i akcje [nie](built-in.md) są dostępne. Za pomocą tych wyzwalaczy i akcji można tworzyć przepływy pracy, które integrują dane, aplikacje, usługi w chmurze i systemy lokalne. W porównaniu z wbudowanymi wyzwalaczami i akcjami te łączniki są zwykle powiązane z określoną usługą lub systemem, takim jak Azure Blob Storage, Office 365, SQL, Salesforce lub SFTP. Zarządzane przez firmę Microsoft i hostowane na platformie Azure, zarządzane łączniki zwykle wymagają najpierw utworzenia połączenia z przepływu pracy i uwierzytelnienia tożsamości. Dostępne są zarówno wyzwalacze oparte na cyklu, jak i oparte na webhooku, więc jeśli używasz wyzwalacza opartego na cyklu, zapoznaj się z omówieniem [zachowania cyklicznego](apis-list.md#recurrence-behavior).

W przypadku kilku usług, systemów i protokołów, takich jak Azure Service Bus, Azure Functions, SQL, AS2 i tak dalej, Logic Apps także wersje wbudowane. Liczba i zakres różnią się w zależności od tego, czy tworzysz aplikację logiki z wieloma dzierżawami, czy aplikację logiki z jedną dzierżawą. W kilku przypadkach dostępna jest zarówno wersja wbudowana, jak i wersja łącznika zarządzanego. W większości przypadków wbudowana wersja zapewnia lepszą wydajność, możliwości, ceny i tak dalej. Aby na przykład wymieniać komunikaty B2B przy użyciu protokołu [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md)wybierz wbudowaną wersję, chyba że potrzebujesz funkcji śledzenia, które są dostępne tylko w (przestarzałej) wersji łącznika zarządzanego.

Niektóre łączniki zarządzane Logic Apps należą do wielu podkategori. Na przykład łącznik SAP jest zarówno łącznikiem [przedsiębiorstwa,](#enterprise-connectors) jak i [łącznikiem lokalnym](#on-premises-connectors).

* [Łączniki standardowe](#standard-connectors) zapewniają dostęp do usług, takich jak Azure Blob Storage, Office 365, SharePoint, Salesforce, Power BI, OneDrive i wiele innych.
* [Łączniki lokalne zapewniają](#on-premises-connectors) dostęp do systemów lokalnych, takich jak SQL Server, SharePoint Server, SAP, Oracle DB, udziały plików i inne.
* [](#integration-account-connectors) Łączniki konta integracji ułatwiają przekształcanie i weryfikowanie kodu XML, kodowanie i dekodowanie plików flat oraz przetwarzanie komunikatów między firmami (B2B) przy użyciu protokołów AS2, EDIFACT i X12. 

## <a name="standard-connectors"></a>Łączniki standardowe

Azure Logic Apps te popularne łączniki standardowe do tworzenia zautomatyzowanych przepływów pracy przy użyciu tych usług i systemów. Niektóre łączniki standardowe obsługują [również systemy lokalne lub](#on-premises-connectors) konta [integracji.](#integration-account-connectors)

Niektóre Logic Apps standardowa obsługują [systemy lokalne lub](#on-premises-connectors) konta [integracji.](#integration-account-connectors)

:::row:::
    :::column:::
        [![Azure Service Bus łącznika zarządzanego w Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus**][azure-service-bus-doc]
        \
        \
        Zarządzaj komunikatami asynchronicznymi, sesjami i subskrypcjami tematów przy użyciu najczęściej używanego łącznika w usłudze Logic Apps.
    :::column-end:::
    :::column:::
        [![SQL Server łącznika zarządzanego w Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        Połącz się ze swoim SQL Server lokalnym lub Azure SQL Database w chmurze, aby zarządzać rekordami, uruchamiać procedury składowane lub wykonywać zapytania.
    :::column-end:::
    :::column:::
        [![Ikona łącznika zarządzanego usługi Azure Blog Storage w Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blog Storage**][azure-blob-storage-doc]
        \
        \
        Połącz się z kontem usługi Azure Storage, aby można było tworzyć zawartość obiektów blob i zarządzać nimi.
    :::column-end:::
    :::column:::
        [![Ikona łącznika zarządzanego usługi Office 365 Outlook w usłudze Logic Apps][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        Połącz się z służbowym kontem e-mail, aby tworzyć wiadomości e-mail, zadania, wydarzenia i spotkania z kalendarza, kontakty, żądania i nie tylko oraz zarządzać nimi.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona łącznika zarządzanego STFP-SSH w Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        Łącz się z serwerami SFTP dostępnymi z Internetu za pomocą protokołu SSH, aby pracować ze swoimi plikami i folderami.
    :::column-end:::
    :::column:::
        [![Ikona łącznika zarządzanego usługi SharePoint Online w usłudze Logic Apps][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Nawiązywanie połączenia z usługą SharePoint Online w celu zarządzania plikami, załącznikami, folderami i nie tylko.
    :::column-end:::
    :::column:::
        [![Ikona łącznika zarządzanego usługi Azure Queues w usłudze Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Kolejki platformy Azure**][azure-queues-doc]
        \
        \
        Połącz się ze swoim kontem usługi Azure Storage, aby tworzyć kolejki i komunikaty oraz zarządzać nimi.
    :::column-end:::
    :::column:::
        [![Ikona łącznika zarządzanego FTP w Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**Ftp**][ftp-doc]
        \
        \
        Połącz się z serwerami FTP, do których masz dostęp z Internetu, aby pracować z plikami i folderami.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona łącznika zarządzanego systemu plików w Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**System plików**][file-system-doc]
        \
        \
        Połącz się z lokalnym udziałem plików, aby można było tworzyć pliki i zarządzać nimi.
    :::column-end:::
    :::column:::
        [![Azure Event Hubs łącznika zarządzanego w Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs**][azure-event-hubs-doc]
        \
        \
        Publikuj zdarzenia i korzystaj z nich za pośrednictwem centrum zdarzeń. Na przykład pobierz dane wyjściowe z aplikacji logiki przy użyciu usługi Event Hubs, a następnie wyślij je do dostawcy analiz w czasie rzeczywistym.
    :::column-end:::
    :::column:::
        [![Azure Event Grid łącznika zarządzanego w Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid**][azure-event-grid-doc]
        \
        \
        Monitoruj zdarzenia opublikowane przez Event Grid, na przykład gdy zmieniają się zasoby platformy Azure lub zasoby innych firm.
    :::column-end:::
    :::column:::
        [![Ikona łącznika zarządzanego usługi Salesforce w Logic Apps][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Połącz się z kontem usługi Salesforce, aby tworzyć elementy, takie jak rekordy, zadania, obiekty i nie tylko, oraz zarządzać nimi.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>Łączniki lokalne

Przed utworzeniem połączenia z systemem lokalnym należy najpierw pobrać, zainstalować i skonfigurować lokalną [bramę danych.][gateway-doc] Ta brama zapewnia bezpieczny kanał komunikacyjny bez konieczności skonfigurowania niezbędnej infrastruktury sieciowej. 

Poniższe łączniki to niektóre powszechnie używane łączniki [standardowe,](#standard-connectors) Logic Apps które zapewniają dostęp do danych i zasobów w systemach lokalnych. Aby uzyskać listę łączników lokalnych, zobacz [Obsługiwane źródła danych.](../logic-apps/logic-apps-gateway-connection.md#supported-connections)

:::row:::
    :::column:::
        [![Ikona lokalnego łącznika programu Biztalk Server w Logic Apps][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Ikona lokalnego łącznika systemu plików w Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**System plików**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Ikona lokalnego łącznika programu IBM Db2 w Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM Db2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Ikona lokalnego łącznika IBM Informix w Logic Apps][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona łącznika lokalnego programu MySQL w Logic Apps][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB łącznika lokalnego w Logic Apps][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Ikona lokalnego łącznika postgreSQL w Logic Apps][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**Postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Ikona lokalnego łącznika programu SharePoint Server w Logic Apps][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server łącznika lokalnego w Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Ikona lokalnego łącznika Teradata w Logic Apps][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>Łączniki konta integracji

Łączniki konta integracji w szczególności obsługują scenariusze komunikacji między [firmami (B2B)](../logic-apps/logic-apps-enterprise-integration-overview.md) w Azure Logic Apps. Po [utworzeniu](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) konta integracji i zdefiniowaniu artefaktów B2B, takich jak partnerzy handlowi, umowy, mapy i schematy, można użyć łączników konta integracji do kodowania i dekodowania komunikatów, przekształcania zawartości i nie tylko.

Jeśli na przykład używasz usługi Microsoft BizTalk Server, możesz utworzyć połączenie z przepływu pracy przy użyciu BizTalk Server [łącznika lokalnego](#on-premises-connectors). Następnie możesz rozszerzyć lub wykonać operacje podobne do bizTalk w przepływie pracy przy użyciu tych łączników konta integracji.

> [!NOTE]
> Aby można było używać łączników konta integracji, należy [połączyć aplikację logiki z kontem integracji.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)


:::row:::
    :::column:::
        [![Ikona akcji dekodowania AS2 w Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**Dekodowanie AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji kodowania AS2 w Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**Kodowanie AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji dekodowania EDIFACT w Logic Apps][edifact-icon]][edifact-decode-doc]
        \
        \
        [**Dekodowanie EDIFACT**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji kodowania EDIFACT w Logic Apps][edifact-icon]][edifact-encode-doc]
        \
        \
        [**Kodowanie EDIFACT**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona akcji dekodowania pliku Logic Apps][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Dekodowanie plików prostych**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji kodowania pliku płaskiego w Logic Apps][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Kodowanie pliku płaskiego**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji konto integracji w Logic Apps][integration-account-icon]][integration-account-doc]
        \
        \
        [**Konto integracji**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji przekształcania Liquid w Logic Apps][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Przekształcenia Liquid**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona akcji dekodowania X12 w Logic Apps][x12-icon]][x12-decode-doc]
        \
        \
        [**Dekodowanie X12**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji kodowania X12 w Logic Apps][x12-icon]][x12-encode-doc]
        \
        \
        [**Kodowanie X12**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji przekształcania KODU XML w Logic Apps][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**Przekształcenia XML**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Ikona akcji walidacji XML w Logic Apps][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**Walidacja kodu XML**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Łączniki dla przedsiębiorstw

Następujące łączniki zapewniają dostęp do systemów przedsiębiorstwa za dodatkową opłatą:

:::row:::
    :::column:::
        [![Ikona łącznika IBM 3270 Enterprise w Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**ŁĄCZNIK IBM 3270** Enterprise][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika IBM MQ Enterprise w Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**Łącznik IBM MQ** Enterprise][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika SAP Enterprise Connector w Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**Łącznik SAP** Enterprise Connector][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>Łączniki ISE

W środowisku usługi integracji (ISE) te łączniki zarządzane mają również wersje [środowiska ISE,](apis-list.md#ise-and-connectors)które mają inne możliwości niż ich wersje wielodostępne:

> [!NOTE]
> Aplikacje logiki, które działają w programie ISE i ich łącznikach, niezależnie od tego, gdzie te łączniki działają, są zgodne z ustalonym planem cen w porównaniu do planu cenowego opartego na zużyciu. Aby uzyskać więcej informacji, [zobacz Logic Apps cennika](../logic-apps/logic-apps-pricing.md) i [Logic Apps szczegóły cennika.](https://azure.microsoft.com/pricing/details/logic-apps/)

:::row:::
    :::column:::
        [![Ikona łącznika AS2 ISE w Logic Apps][as2-icon]][as2-doc]
        \
        \
        [**AS2** Ise][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation łącznika ISE w Logic Apps][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Azure Automation** Ise][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob Storage łącznika ISE w Logic Apps][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Azure Blob Storage** Ise][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB łącznika ISE w Logic Apps][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** Ise][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs łącznika ISE w Logic Apps][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Azure Event Hubs** Ise][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid łącznika ISE w Logic Apps][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Azure Event Grid** Ise][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika azure File Storage ISE w usłudze Logic Apps][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Azure File Storage** Ise][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault łącznika ise w Logic Apps][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** Ise][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor Logs ISE connector icon in Logic Apps][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Azure Monitor dzienników** Ise][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure Service Bus łącznika ISE w Logic Apps][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**Azure Service Bus** Ise][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics łącznika ISE w Logic Apps][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** Ise][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika azure Table Storage ISE w usłudze Logic Apps][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Azure Table Storage** Ise][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona łącznika usługi Azure Queues ISE w usłudze Logic Apps][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Kolejki platformy Azure** Ise][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika EDIFACT ISE w Logic Apps][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** Ise][edifact-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika systemu plików ISE w Logic Apps][file-system-icon]][file-system-doc]
        \
        \
        [**System plików** Ise][file-system-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika FTP ISE w Logic Apps][ftp-icon]][ftp-doc]
        \
        \
        [**PROTOKÓŁ FTP** Ise][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![Ikona łącznika IBM 3270 ISE w Logic Apps][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** Ise][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika IBM DB2 ISE w Logic Apps][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** Ise][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika IBM MQ ISE w Logic Apps][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** Ise][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika SAP ISE w Logic Apps][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Ise][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Ikona łącznika SFTP-SSH ISE w Logic Apps][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** Ise][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika SMTP ISE w Logic Apps][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** Ise][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server łącznika ISE w Logic Apps][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** Ise][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Ikona łącznika X12 ISE w Logic Apps][x12-icon]][x12-doc]
        \
        \
        [**X12** Ise][x12-doc]
    :::column-end:::
:::row-end:::

Więcej informacji można znaleźć w następujących tematach:

* [Dostęp do zasobów sieci wirtualnej platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Model cen aplikacji logiki](../logic-apps/logic-apps-pricing.md)
* [Nawiązywanie połączenia z sieciami wirtualnymi platformy Azure z Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie niestandardowych interfejsów API, które można wywołać z Logic Apps](/logic-apps/logic-apps-create-api-app)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png


<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Tworzenie zadań automatyzacji dla infrastruktury chmurowej i lokalnej oraz zarządzanie nimi"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Zarządzanie plikami w kontenerze obiektów blob za pomocą łącznika usługi Azure Blob Storage"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Połącz się z Azure Cosmos DB, aby uzyskać dostęp do dokumentów i procedur składowanych"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorowanie zdarzeń publikowanych przez Event Grid, na przykład w przypadku zmiany zasobów platformy Azure lub zasobów innych firm"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Połącz się z Azure Event Hubs, aby odbierać i wysyłać zdarzenia między aplikacjami logiki a Event Hubs"
[azure-file-storage-doc]: /connectors/azurefile/ "Połącz się ze swoim kontem usługi Azure Storage, aby tworzyć, aktualizować, pobrać i usunąć pliki"
[azure-key-vault-doc]: /connectors/keyvault/ "Połącz się ze swoim Azure Key Vault, aby zarządzać wpisami tajnymi i kluczami"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Uruchamianie zapytań względem dzienników Azure Monitor w obszarach roboczych usługi Log Analytics i Application Insights roboczych"
[azure-queues-doc]: /connectors/azurequeues/ "Nawiązywanie połączenia z kontem usługi Azure Storage w celu tworzenia kolejek i komunikatów oraz zarządzania nimi"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Wysyłanie komunikatów z Service Bus i tematów oraz odbieranie komunikatów z Service Bus i subskrypcji"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Połącz się Azure Synapse Analytics, aby wyświetlić dane"
[azure-table-storage-doc]: /connectors/azuretables/ "Połącz się z kontem usługi Azure Storage, aby można było tworzyć i aktualizować tabele oraz tworzyć zapytania dotyczące tabel i nie tylko"
[biztalk-server-doc]: /connectors/biztalk/ "Połącz się z BizTalk Server, aby można było uruchamiać aplikacje oparte na biztalkach obok siebie Azure Logic Apps"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Łączenie z lokalnym systemem plików"
[ftp-doc]: ./connectors-create-api-ftp.md "Łączenie z serwerem FTP/FTPS i wykonywanie zadań związanych z protokołem FTP, takich jak np. przekazywanie, pobieranie i usuwanie plików"
[github-doc]: ./connectors-create-api-github.md "Łączenie z witryną GitHub i śledzenie problemów"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Nawiązuje połączenie z kalendarzem Google i może zarządzać kalendarzem"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Łączenie się z arkuszami Google, aby można było modyfikować arkusze"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Nawiązuje połączenie z usługą Google Tasks, aby można było zarządzać zadaniami"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Nawiązywanie połączenia z aplikacjami 3270 na komputerach mainframe firmy IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Łączenie z bazą danych IBM DB2 w chmurze lub lokalnie. Aktualizowanie wiersza, uzyskiwanie tabeli i nie tylko"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Połącz się z programem Informix w chmurze lub lokalnie. Odczytywanie wiersza, lista tabel i inne informacje"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Nawiązywanie połączenia z programem IBM MQ lokalnie lub na platformie Azure w celu wysyłania i odbierania komunikatów"
[instagram-doc]: ./connectors-create-api-instagram.md "Połącz się z siecią OS. Wyzwalanie zdarzeń lub działanie na nie"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Łączenie z usługą Mandrill w celu komunikowania się"
[mysql-doc]: /connectors/mysql/ "Nawiązywanie połączenia z lokalną bazą danych MySQL w celu odczytu i zapisu danych"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Połącz się z kontem służbowym, aby wysyłać i odbierać wiadomości e-mail, zarządzać kalendarzem i kontaktami oraz nie tylko"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Połącz się z osobistą usługą Microsoft OneDrive, aby można było przekazywać, usuwać, wyświetlić listę plików i nie tylko"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Połącz się z firmową usługą Microsoft OneDrive, aby można było przekazywać, usuwać, wyświetlić listę plików i nie tylko"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Nawiązywanie połączenia z bazą danych Oracle w celu dodawania, wstawiania, usuwania wierszy i nie tylko"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Połącz się ze skrzynką pocztową programu Outlook, aby zarządzać pocztą e-mail, kalendarzami, kontaktami i nie tylko"
[postgre-sql-doc]: /connectors/postgresql/ "Nawiązywanie połączenia z bazą danych PostgreSQL w celu odczytywania danych z tabel"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Połącz się z kontem usługi Salesforce. Zarządzanie kontami, potencjalnymi klientami, szansami sprzedaży i nie tylko"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Łączenie z lokalnym systemem SAP"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Nawiąż połączenie z siecią SendGrid. Wysyłanie wiadomości e-mail i zarządzanie listami adresatów"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Połącz się z kontem SFTP przy użyciu protokołu SSH. Przekazywanie, uzyskiwanie, usuwanie plików i nie tylko"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Nawiązywanie połączenia z serwerem lokalnym programu SharePoint. Zarządzanie dokumentami, elementami listy i nie tylko"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Nawiązywanie połączenia z usługą SharePoint Online. Zarządzanie dokumentami, elementami listy i nie tylko"
[slack-doc]: ./connectors-create-api-slack.md "Nawiązywanie połączenia z slack i publikowanie wiadomości w kanałach Slack"
[smtp-doc]: ./connectors-create-api-smtp.md "Nawiązywanie połączenia z serwerem SMTP i wysyłanie wiadomości e-mail z załącznikami"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Łączenie z usługą SparkPost w celu komunikowania się"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Połącz się Azure SQL Database lub SQL Server. Tworzenie, aktualizowanie, uzyskiwanie i usuwanie wpisów w tabeli bazy danych SQL"
[teradata-doc]: /connectors/teradata/ "Nawiązywanie połączenia z bazą danych Teradata w celu odczytywania danych z tabel"
[twilio-doc]: ./connectors-create-api-twilio.md "Połącz się z Twilio. Wysyłanie i uzyskiwanie wiadomości, uzyskiwanie dostępnych numerów, zarządzanie przychodzącymi numerami telefonów i nie tylko"
[youtube-doc]: ./connectors-create-api-youtube.md "Połącz się z serwisem YouTube. Zarządzanie filmami wideo i kanałami"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Kodowanie i dekodowanie komunikatów, które korzystają z protokołu AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Kodowanie i dekodowanie komunikatów, które korzystają z protokołu EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Dekodowanie komunikatów, które korzystają z protokołu EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Kodowanie komunikatów, które korzystają z protokołu EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Dowiedz się więcej o pliku flat integracji dla przedsiębiorstw"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Dowiedz się więcej o pliku flat integracji dla przedsiębiorstw"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Zarządzanie metadanymi artefaktów konta integracji"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Przekształcanie danych JSON za pomocą szablonów Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Kodowanie i dekodowanie komunikatów, które korzystają z protokołu X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Dekodowanie komunikatów, które używają protokołu X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Kodowanie komunikatów, które używają protokołu X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Przekształcanie komunikatów XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Weryfikowanie komunikatów XML"


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Łączenie z lokalnymi źródłami danych z aplikacji logiki za pomocą bramy danych lokalnych"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Kodowanie i dekodowanie komunikatów, które korzystają z protokołu AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Kodowanie i dekodowanie komunikatów, które korzystają z protokołu EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Dekodowanie komunikatów, które korzystają z protokołu EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Kodowanie komunikatów, które korzystają z protokołu EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Dowiedz się więcej o pliku flat integracji dla przedsiębiorstw"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Dowiedz się więcej o pliku flat integracji dla przedsiębiorstw"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Zarządzanie metadanymi artefaktów konta integracji"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Przekształcanie danych JSON za pomocą szablonów Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Kodowanie i dekodowanie komunikatów, które korzystają z protokołu X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Dekodowanie komunikatów, które używają protokołu X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Kodowanie komunikatów, które używają protokołu X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Przekształcanie komunikatów XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Weryfikowanie komunikatów XML"
