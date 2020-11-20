---
title: Microsoft Azure certyfikaty dla SAP | Microsoft Docs
description: Zaktualizowana lista bieżących konfiguracji i certyfikatów SAP na platformie Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 184f01f27023ce5976e5204bed384df54bab82d4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950435"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Certyfikaty i konfiguracje SAP działające w systemie Microsoft Azure

W przypadku oprogramowania SAP i Microsoft istnieje obszerna historia współpracy w silnym partnerstwie, które ma wzajemne korzyści dla klientów. Firma Microsoft stale aktualizuje swoją platformę i przesyła nowe szczegóły certyfikacji do oprogramowania SAP w celu zapewnienia, że Microsoft Azure jest najlepszą platformą, na której będą uruchamiane obciążenia SAP. W poniższych tabelach Zanotuj konfiguracje obsługiwane przez platformę Azure oraz listę rosnących certyfikatów SAP. Ta lista zawiera listę przeglądów, która może odbiegać tutaj i z oficjalnych list SAP. Jak uzyskać szczegółowe dane, zapoznaj się z artykułem [jakie oprogramowanie SAP jest obsługiwane przez wdrożenia platformy Azure](./sap-supported-product-on-azure.md)

## <a name="sap-hana-certifications"></a>Certyfikaty SAP HANA
Wołują

- [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) na potrzeby SAP HANA obsługi natywnych maszyn wirtualnych platformy Azure i dużych wystąpień Hana.

| Produkt SAP | Obsługiwane systemy operacyjne | Oferty platformy Azure |
| --- | --- | --- |
| SAP HANA Developer Edition (w tym oprogramowanie klienckie HANA składające się z SQLODBC, ODBO-Windows Only, ODBC, JDBC Drivers, HANA Studio i HANA Database) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Rodzina maszyn wirtualnych z serii D |
| Firma 1 na platformie HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrolowana dostępność dla GS5. Pełna obsługa M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA na platformie Azure (duże wystąpienia) [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Pakiet na platformie HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA na platformie Azure (duże wystąpienia) [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise for BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA na platformie Azure (duże wystąpienia) [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA na platformie Azure (duże wystąpienia) <br /> [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Należy pamiętać, że SAP używa warunku "klastrowanie" w [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) jako synonim dla "skalowalności w poziomie", a nie dla "klastrowania" o wysokiej dostępności

## <a name="sap-netweaver-certifications"></a>Certyfikaty SAP NetWeaver
Microsoft Azure jest certyfikowany dla następujących produktów SAP z pełną pomocą techniczną firmy Microsoft i SAP.
Wołują

- [1928533 — aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533) dla wszystkich aplikacji opartych na oprogramowaniu SAP NetWeaver, w tym SAP Trex, SAP LIVECACHE i SAP Content Server. I wszystkie bazy danych, z wyłączeniem SAP HANA.


| Produkt SAP | System operacyjny gościa | RDBMS | Typy maszyn wirtualnych |
| --- | --- | --- | --- |
| Oprogramowanie SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko systemy Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,, M208s_v2, M208ms_v2, M416s_v2 |
| SAP Business — wszystko w jednym | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko systemy Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,, M208s_v2, M208ms_v2, M416s_v2 |
| SAP BusinessObjects BI | Windows |Nie dotyczy |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,, M208s_v2, M208ms_v2, M416s_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (tylko systemy Windows i Oracle Linux), DB2, SAP ASE |A5 do A11, D11 do D14, DS11 do DS14, DS11_v2 do DS15_v2, GS1 do GS5, D2s_v3 do D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts,, M208s_v2, M208ms_v2, M416s_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Inne obciążenie SAP obsługiwane na platformie Azure

| Produkt SAP | System operacyjny gościa | RDBMS | Typy maszyn wirtualnych |
| --- | --- | --- | --- |
| Oprogramowanie SAP Business One on on SQL Server | Windows  | SQL Server | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver<br /> [#928839 uwagi SAP](https://launchpad.support.sap.com/#/notes/928839) |
| PROTOKÓŁ SAP BPC 10,01 MS SP08 | System Windows i Linux | | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver<br /> #2451795 uwagi SAP |
| Oprogramowanie SAP Business Objects BI platform | System Windows i Linux | | #2145537 uwagi SAP |
| Data Services SAP 4,2 | | | #2288344 uwagi SAP |
| Platforma SAP Hybris Commerce platform  | Windows | SQL Server, Oracle | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver <br /> [Dokumentacja Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Platforma SAP Hybris Commerce platform  | SLES 12 lub nowsza | SAP HANA | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver <br /> [Dokumentacja Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| Platforma SAP Hybris Commerce platform  | RHEL 7 lub nowsze | SAP HANA | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver <br /> [Dokumentacja Hybris]https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/6.7.0.0/en-US/8c71300f866910149b40c88dfc0de431.html) |
| SAP (Hybris) Commerce platform 1811 i nowsze  | Windows, SLES lub RHEL | Baza danych SQL Azure | Wszystkie typy certyfikowanych maszyn wirtualnych NetWeaver <br /> [Dokumentacja Hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html) |
