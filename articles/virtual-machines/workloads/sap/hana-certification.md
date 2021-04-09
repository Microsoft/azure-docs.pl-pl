---
title: Certyfikacja SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Certyfikacja SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8d4781ad9ca2a30e8523d0e2446dc3903a7cb8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101668862"
---
# <a name="certification"></a>Certyfikacja

Oprócz certyfikacji NetWeaver, SAP wymaga specjalnej certyfikacji SAP HANA do obsługi SAP HANA w niektórych infrastrukturach, takich jak Azure IaaS.

Podstawowa Uwaga dotycząca oprogramowania SAP w systemie NetWeaver oraz do stopnia certyfikacji SAP HANA, to [SAP uwagi #1928533 — aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533).

Rekordy certyfikacji dla SAP HANA na platformie Azure (duże wystąpienia) mogą znajdować się w witrynie [SAP HANA certyfikowanych platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . 

SAP HANA na platformie Azure (duże wystąpienia), o których mowa w witrynie SAP HANA certyfikowaną platformą IaaS, zapewnia klientom firmy Microsoft i SAP możliwość wdrażania dużych obciążeń SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA lub innych obSAP HANAych zadań na platformie Azure. Rozwiązanie jest oparte na dedykowanej sygnaturze sprzętu z certyfikatem SAP-HANA ([SAP HANA dostosowaną integrację centrów danych — TDI](https://scn.sap.com/docs/DOC-63140)). Jeśli zostanie uruchomione SAP HANA rozwiązanie skonfigurowane jako TDI, wszystkie aplikacje oparte na SAP HANA (takie jak SAP Business Suite na SAP HANA SAP BW na SAP HANA, S4/HANA i BW4/HANA) działają w infrastrukturze sprzętowej.

W porównaniu do uruchamiania SAP HANA na maszynach wirtualnych to rozwiązanie ma korzyść. Zapewnia znacznie większe ilości pamięci. Aby włączyć to rozwiązanie, należy zrozumieć następujące kluczowe aspekty:

- Warstwa aplikacji SAP i aplikacje inne niż SAP działają na maszynach wirtualnych, które są hostowane w zwykłych sygnaturach sprzętowych platformy Azure.
- Infrastruktura lokalna klienta, centra danych i wdrożenia aplikacji są połączone z platformą w chmurze za pośrednictwem ExpressRoute (zalecane) lub wirtualnej sieci prywatnej (VPN). Active Directory i DNS są również rozszerzane na platformę Azure.
- Wystąpienie bazy danych SAP HANA dla obciążenia HANA działa na SAP HANA na platformie Azure (duże wystąpienia). Sygnatura dużego wystąpienia jest połączona z siecią platformy Azure, więc oprogramowanie działające na maszynach wirtualnych może współdziałać z wystąpieniem HANA uruchomionym w dużym wystąpieniu platformy HANA.
- Sprzęt SAP HANA na platformie Azure (duże wystąpienia) jest dedykowanym sprzętem udostępnianym w IaaS z SUSE Linux Enterprise Server lub Red Hat Enterprise Linux preinstalowanym. Podobnie jak w przypadku maszyn wirtualnych, Twoje obowiązki mogą być dodatkowo aktualizowane i konserwowane w systemie operacyjnym.
- Twoja odpowiedzialność jest instalacją platformy HANA lub wszelkimi dodatkowymi składnikami niezbędnymi do uruchamiania SAP HANA na jednostkach dużego wystąpienia HANA. Wszystkie odpowiednie bieżące operacje i administrowanie SAP HANA na platformie Azure są również obowiązkiem użytkownika.
- Oprócz rozwiązań opisanych tutaj można zainstalować inne składniki w ramach subskrypcji platformy Azure, które łączą się z SAP HANA na platformie Azure (duże wystąpienia). Przykładami są składniki, które umożliwiają komunikację z SAP HANA bazą danych lub bezpośrednio z nią, takich jak serwery przeskoków, serwery RDP, SAP HANA Studio, SAP Data Services for SAP BI scenariusze lub rozwiązania do monitorowania sieci.
- Podobnie jak w przypadku platformy Azure, duże wystąpienie usługi HANA oferuje obsługę funkcji wysokiej dostępności i odzyskiwania po awarii.

**Następne kroki**
- Odwołuje się [do dostępnych jednostek SKU dla elementu HLI](hana-available-skus.md) 