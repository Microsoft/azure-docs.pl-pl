---
title: Co to jest Azure SQL Edge?
description: Dowiedz się więcej o usłudze Azure SQL Edge
keywords: Wprowadzenie do usługi SQL Edge, co to jest SQL Edge, Omówienie programu SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a74e12a5e9bdd2dfdbc6ac07b66798e517f6f426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395074"
---
# <a name="what-is-azure-sql-edge"></a>Co to jest Azure SQL Edge?

Usługa Azure SQL Edge jest zoptymalizowanym aparatem relacyjnej bazy danych, który umożliwia wdrażanie IoT i IoT Edge. Zapewnia możliwości tworzenia wysokiej wydajności magazynu danych i warstwy przetwarzania dla aplikacji i rozwiązań IoT. Usługa Azure SQL Edge oferuje możliwości przesyłania strumieniowego, przetwarzania i analizowania relacyjnych i nierelacyjnych danych, takich jak dane JSON, grafy i szeregi czasowe, dzięki czemu można wybrać różne nowoczesne aplikacje IoT.

Usługa Azure SQL Edge jest oparta na najnowszych wersjach [aparatu bazy danych SQL Server](/sql/sql-server/sql-server-technical-documentation), co zapewnia wiodącą w branży wydajność, funkcje zabezpieczeń i przetwarzania zapytań. Ponieważ usługa Azure SQL Edge jest oparta na tym samym aparacie co [SQL Server](/sql/sql-server/sql-server-technical-documentation) i [Azure SQL](../azure-sql/index.yml), zapewnia ten sam obszar powierzchni programowania Transact-SQL (T-SQL), który umożliwia łatwiejsze i szybsze tworzenie aplikacji i rozwiązań, a także przenoszenie aplikacji między urządzeniami IoT Edge, centrami danych i chmurą.

Co to jest Azure SQL Edge wideo w witrynie Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>Modele wdrażania

Usługa Azure SQL Edge obsługuje dwa tryby wdrażania.

- Połączone wdrożenie za pomocą Azure IoT Edge: usługa Azure SQL Edge jest dostępna w portalu Azure Marketplace i można ją wdrożyć jako moduł dla [Azure IoT Edge](../iot-edge/about-iot-edge.md). Aby uzyskać więcej informacji, zobacz [wdrażanie usługi Azure SQL Edge](deploy-portal.md).<br>

![Diagram omówienia programu SQL Edge](media/overview/overview.png)

- Rozłączono wdrożenie: obrazy kontenerów usługi Azure SQL Edge można ściągnąć z centrum Docker i wdrożyć je jako autonomiczny kontener platformy Docker lub w klastrze Kubernetes. Aby uzyskać więcej informacji, zobacz [wdrażanie usługi Azure SQL Edge przy użyciu platformy Docker](disconnected-deployment.md) i [wdrażanie kontenera usługi Azure SQL Edge w Kubernetes](deploy-kubernetes.md).

## <a name="editions-of-sql-edge"></a>Wersje programu SQL Edge

Program SQL Edge jest dostępny z dwiema różnymi wersjami lub planami oprogramowania. Te wersje mają identyczne zestawy funkcji i różnią się w zależności od ich praw użytkowania i ilości procesora CPU/pamięci, które obsługują.

   |**Planowanie**  |**Opis**  |
   |---------|---------|
   |Deweloper usługi Azure SQL Edge  |  Tylko programowanie SKU, każdy kontener programu SQL Edge jest ograniczony do maksymalnie 4 rdzeni i 32 GB pamięci  |
   |Usługa Azure SQL Edge    |  Jednostka SKU produkcji, każdy kontener programu SQL Edge jest ograniczony do maksymalnie 8 rdzeni i 64 GB pamięci. |

## <a name="pricing-and-availability"></a>Cennik i dostępność

Usługa Azure SQL Edge jest teraz ogólnie dostępna. Aby uzyskać więcej informacji na temat cen i dostępności w określonych regionach, zobacz [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/).

> [!IMPORTANT]
> Aby zrozumieć różnice między funkcjami usługi Azure SQL Edge i SQL Server, a także różnice między różnymi opcjami usługi Azure SQL Edge, zobacz temat [obsługiwane funkcje usługi Azure SQL Edge](features.md).

## <a name="streaming-capabilities"></a>Możliwości przesyłania strumieniowego  

Usługa Azure SQL Edge oferuje wbudowaną funkcję przesyłania strumieniowego na potrzeby analiz w czasie rzeczywistym i skomplikowanego przetwarzania zdarzeń. Możliwość przesyłania strumieniowego jest tworzona przy użyciu tych samych konstrukcji co [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i podobnych możliwości, co [Azure Stream Analytics na urządzeniach IoT Edge](../stream-analytics/stream-analytics-edge.md).

Aparat przesyłania strumieniowego usługi Azure SQL Edge został zaprojektowany z myślą o małym opóźnieniu, odporności, wydajnemu użyciu przepustowości i zgodności. 

Aby uzyskać więcej informacji na temat przesyłania strumieniowego danych w programie SQL Edge, odwołaj się do [przesyłania strumieniowego danych](stream-data.md)

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Możliwości Machine Learning i sztucznej analizy

Usługa Azure SQL Edge oferuje wbudowaną funkcję uczenia maszynowego i analitycznego, integrując środowisko uruchomieniowe Open format ONNX (Open neuronowych Network Exchange), które umożliwia wymianę usług uczenie głębokie i neuronowych sieci między różnymi platformami. Aby uzyskać więcej informacji na temat ONNX, zobacz [tutaj](https://onnx.ai/). Środowisko uruchomieniowe ONNX zapewnia elastyczność tworzenia modeli w wybranym języku lub wybranych narzędziach, które można następnie przekonwertować na format ONNX w celu wykonania w programie SQL Edge. Aby uzyskać więcej informacji, zobacz [Machine Learning i sztuczną inteligencję z ONNX w usłudze SQL Edge](onnx-overview.md).

## <a name="working-with-azure-sql-edge"></a>Praca z usługą Azure SQL Edge

Usługa Azure SQL Edge sprawia, że tworzenie i konserwowanie aplikacji jest łatwiejsze i wydajniejsze. Użytkownicy mogą korzystać ze wszystkich znanych narzędzi i umiejętności, aby tworzyć wspaniałe aplikacje i rozwiązania na potrzeby ich IoT Edge. Użytkownik może opracowywać w programie SQL Edge przy użyciu narzędzi, takich jak następujące:

- [Azure Portal](https://portal.azure.com/) — aplikacja oparta na sieci Web do zarządzania wszystkimi usługami platformy Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) — bezpłatna, do pobrania aplikacja kliencka do zarządzania dowolną infrastrukturą SQL, od SQL Server do SQL Database.
- [SQL Server narzędzia danych w programie Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) — bezpłatna, do pobrania aplikacja kliencka służąca do tworzenia SQL Server relacyjnych baz danych, baz danych SQL, pakietów usług Integration services, Analysis Services modeli danych i raportów usług Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) — bezpłatne, dostępne do pobrania Narzędzie bazy danych międzyplatformowego dla profesjonalistów danych korzystające z rodziny Microsoft lokalnych i chmurowych platform danych w systemach Windows, MacOS i Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) — bezpłatny, dostępny do pobrania Edytor kodu typu open source dla systemów Windows, MacOS i Linux. Obsługuje ona rozszerzenia, w tym [rozszerzenie MSSQL](https://aka.ms/mssql-marketplace) do wykonywania zapytań dotyczących Microsoft SQL Server, Azure SQL Database i usługi Azure Synapse Analytics.


## <a name="next-steps"></a>Następne kroki

- [Wdróż program SQL Edge za Azure Portal](deploy-portal.md)
- [Machine Learning i sztuczna inteligencja przy użyciu programu SQL Edge](onnx-overview.md)
- [Tworzenie kompleksowego rozwiązania IoT przy użyciu programu SQL Edge](tutorial-deploy-azure-resources.md)