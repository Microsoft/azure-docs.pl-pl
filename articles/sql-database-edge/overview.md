---
title: Co to jest Azure SQL Database Edge? | Microsoft Docs
description: Dowiedz się więcej o Azure SQL Database Edge
keywords: Wprowadzenie do usługi SQL Database Edge, co to jest baza danych SQL Database, Omówienie usługi SQL Database Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 03/24/2020
ms.openlocfilehash: d5c48b6036065f6182912c21c144cab80fc3cfbf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246708"
---
# <a name="what-is-azure-sql-database-edge-preview"></a>Co to jest Azure SQL Database Edge w wersji zapoznawczej?

Azure SQL Database Edge — wersja zapoznawcza to zoptymalizowany aparat relacyjnej bazy danych umożliwiający wdrażanie IoT i IoT Edge. Zapewnia możliwości tworzenia wysokiej wydajności magazynu danych i warstwy przetwarzania dla aplikacji i rozwiązań IoT. Azure SQL Database Edge oferuje możliwości przesyłania strumieniowego, przetwarzania i analizowania relacyjnych i nierelacyjnych, takich jak dane JSON, grafy i szeregi czasowe, co sprawia, że wybór jest właściwy dla różnych nowoczesnych aplikacji IoT.

Azure SQL Database Edge jest oparta na najnowszych wersjach [aparatu bazy danych Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), który zapewnia wiodącą w branży wydajność, funkcje zabezpieczeń i przetwarzania zapytań. Ponieważ Azure SQL Database Edge jest oparta na tym samym aparacie co SQL Server i Azure SQL Database, zapewnia ten sam obszar powierzchni programowania T-SQL, który umożliwia łatwiejsze i szybsze opracowywanie aplikacji i rozwiązań, a jednocześnie umożliwia przenoszenie aplikacji między urządzeniami IoT Edge, centrami danych i chmurą.

## <a name="deployment-models"></a>Modele wdrażania

Azure SQL Database Edge jest dostępna w portalu Azure Marketplace i można ją wdrożyć jako moduł dla [Azure IoT Edge](../iot-edge/about-iot-edge.md). Aby uzyskać więcej informacji, zobacz [wdrażanie Azure SQL Database Edge](deploy-portal.md).<br>

![Diagram omówienia SQL Database Edge](media/overview/overview.png)

## <a name="editions-of-sql-database-edge"></a>Wersje SQL Database Edge

SQL Database Edge jest dostępna w trzech różnych wersjach lub planach oprogramowania. Te wersje mają identyczne zestawy funkcji i różnią się w zależności od ich praw użytkowania i ilości procesora CPU/pamięci, które obsługują.

   |**Planowanie**  |**Opis**  |
   |---------|---------|
   |Deweloper Azure SQL Database Edge  |  Tylko programowanie jednostki SKU, każdy SQL Database kontenera brzegowego jest ograniczone do maksymalnie 4 rdzeni i 32 GB pamięci  |
   |Azure SQL Database Edge    |  Jednostka SKU produkcyjnej każdy SQL Database kontener brzegów jest ograniczony do maksymalnie 8 rdzeni i 64 GB pamięci. |

## <a name="pricing-and-availability"></a>Cennik i dostępność

Azure SQL Database jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji na temat cen i dostępności, zobacz [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> Aby zrozumieć różnice między funkcjami Azure SQL Database Edge i SQL Server, a także różnice między różnymi Azure SQL Database opcjami krawędzi, zobacz temat [funkcje bazy danych edge SQL Database](https://azure.microsoft.com/services/sql-database-edge/).

## <a name="streaming-capabilities"></a>Możliwości przesyłania strumieniowego  

Azure SQL Database Edge zapewnia wbudowaną funkcję przesyłania strumieniowego na potrzeby analiz w czasie rzeczywistym i skomplikowanego przetwarzania zdarzeń. Możliwość przesyłania strumieniowego jest tworzona przy użyciu tych samych konstrukcji co [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) i oferuje podobne możliwości, jak [Azure Stream Analytics na urządzeniach IoT Edge](../stream-analytics/stream-analytics-edge.md).

Aparat przesyłania strumieniowego dla Azure SQL Database Edge jest przeznaczony dla małych opóźnień, odporności, wydajnego wykorzystania przepustowości i zgodności.

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>Możliwości Machine Learning i sztucznej analizy

Azure SQL Database Edge zapewnia wbudowaną funkcję uczenia maszynowego i analizy dzięki integracji środowiska uruchomieniowego Open format ONNX (Open neuronowych Network Exchange), które umożliwia wymianę szczegółowych informacji i modeli sieci neuronowych między różnymi platformami. Aby uzyskać więcej informacji na temat ONNX, zobacz [tutaj](https://onnx.ai/). Środowisko uruchomieniowe ONNX zapewnia elastyczność tworzenia modeli w wybranym języku lub wybranych narzędziach, które można następnie przekonwertować na format ONNX w celu wykonania w SQL Database Edge. Aby uzyskać więcej informacji, zobacz [Machine Learning i sztuczną inteligencję z ONNX w SQL Database Edge](onnx-overview.md).

## <a name="working-with-azure-sql-database-edge"></a>Praca z Azure SQL Database Edge

Azure SQL Database Edge sprawia, że tworzenie i konserwowanie aplikacji jest łatwiejsze i bardziej produktywne. Użytkownicy mogą korzystać ze wszystkich znanych narzędzi i umiejętności, aby tworzyć wspaniałe aplikacje i rozwiązania na potrzeby ich IoT Edge. Użytkownik może opracowywać SQL Database Edge przy użyciu narzędzi, takich jak następujące:

- [Azure Portal](https://portal.azure.com/) — aplikacja oparta na sieci Web do zarządzania wszystkimi usługami platformy Azure.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) — bezpłatna, do pobrania aplikacja kliencka do zarządzania dowolną infrastrukturą SQL, od SQL Server do SQL Database.
- [SQL Server narzędzia danych w programie Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/) — bezpłatna, do pobrania aplikacja kliencka służąca do tworzenia SQL Server relacyjnych baz danych, baz danych SQL, pakietów usług Integration services, Analysis Services modeli danych i raportów usług Reporting Services.
- [Azure Data Studio](/sql/azure-data-studio/what-is/) — bezpłatne, dostępne do pobrania Narzędzie bazy danych międzyplatformowego dla profesjonalistów danych korzystające z rodziny Microsoft lokalnych i chmurowych platform danych w systemach Windows, MacOS i Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs) — bezpłatny, dostępny do pobrania Edytor kodu typu open source dla systemów Windows, MacOS i Linux. Obsługuje ona rozszerzenia, w tym [rozszerzenie MSSQL](https://aka.ms/mssql-marketplace) do wykonywania zapytań dotyczących Microsoft SQL Server, Azure SQL Database i Azure SQL Data Warehouse.


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje dotyczące cen i dostępności, zobacz [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).
- Żądanie włączenia Azure SQL Database Edge dla subskrypcji.
- Aby rozpocząć, zobacz następujące tematy:
  - [Wdróż SQL Database Edge przez Azure Portal](deploy-portal.md)
  - [Machine Learning i sztuczna inteligencja z SQL Database Edge](onnx-overview.md)
