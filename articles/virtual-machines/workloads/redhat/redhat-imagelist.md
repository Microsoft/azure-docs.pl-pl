---
title: Obrazy red hat enterprise z systemem Linux na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się więcej o obrazach systemu Red Hat Enterprise z systemem Linux na platformie Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 028c30fced14a60af9f5683e6c6e087b15591735
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605480"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Obrazy Red Hat Enterprise Linux (RHEL) dostępne na platformie Azure
Platforma Azure oferuje wiele obrazów RHEL dla różnych przypadków użycia.

> [!NOTE]
> Wszystkie obrazy RHEL są dostępne w chmurach publicznych platformy Azure i platformy Azure dla instytucji rządowych. Nie są one dostępne w chińskim chmurach platformy Azure.

## <a name="list-of-rhel-images"></a>Lista obrazów RHEL
Jest to lista obrazów RHEL dostępnych na platformie Azure. O ile nie określono inaczej, wszystkie obrazy są podzielone na partycje LVM i dołączone do zwykłych repozytoriów RHEL (nie EUS, nie E4S). Następujące obrazy są obecnie dostępne do użytku ogólnego:

Oferta| SKU | Partycjonowanie | Inicjowanie obsługi | Uwagi
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | Raw    | Agent systemu Linux |
|             | 6.8      | Raw    | Agent systemu Linux |
|             | 6.9      | Raw    | Agent systemu Linux |
|             | 6.10     | Raw    | Agent systemu Linux |
|             | 7-RAW    | Raw    | Agent systemu Linux | RHEL 7.x rodzina obrazów. <br> Domyślnie dołączane do zwykłych repozytoriów (nie EUS).
|             | 7-LVM    | Lvm    | Agent systemu Linux | RHEL 7.x rodzina obrazów. <br> Domyślnie dołączane do zwykłych repozytoriów (nie EUS). Jeśli szukasz standardowego obrazu RHEL do wdrożenia, użyj tego zestawu obrazów i/lub jego odpowiednika generacji 2.
|             | 7lvm-gen2| Lvm    | Agent systemu Linux | Generacja 2, rodzina obrazów RHEL 7.x. <br> Domyślnie dołączane do zwykłych repozytoriów (nie EUS). Jeśli szukasz standardowego obrazu RHEL do wdrożenia, użyj tego zestawu obrazów i/lub jego odpowiednika generacji 1.
|             | 7-RAW-CI | RAW-CI | chmura init  | RHEL 7.x rodzina obrazów. <br> Domyślnie dołączane do zwykłych repozytoriów (nie EUS).
|             | 7.2      | Raw    | Agent systemu Linux |
|             | 7.3      | Raw    | Agent systemu Linux |
|             | 7.4      | Raw    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS od kwietnia 2019 r.
|             | 74-gen2  | Raw    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 7,5      | Raw    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS od czerwca 2019 r.
|             | 75-gen2  | Raw    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 7.6      | Raw    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS od maja 2019 r.
|             | 76-gen2  | Raw    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 7.7      | Lvm    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 8-LVM    | Lvm    | Agent systemu Linux | RHEL 8.x rodzina obrazów. Dołączone do regularnych repozytoriów.
|             | 8-lvm-gen2| Lvm    | Agent systemu Linux | Hyper-V Generation 2 - RHEL 8.x rodzina obrazów. Dołączone do regularnych repozytoriów.
|             | 8        | Lvm    | Agent systemu Linux | Obrazy RHEL 8.0
|             | 8-gen2   | Lvm    | Agent systemu Linux | Obrazy Hyper-V Generacji 2 - RHEL 8.0.
|             | 8.1      | Lvm    | Agent systemu Linux | RHEL 8.1 zdjęć. Obecnie dołączone do regularnych repozytoriów.
|             | 81gen2   | Lvm    | Agent systemu Linux | Obrazy Hyper-V Generacji 2 - RHEL 8.1. Obecnie dołączone do regularnych repozytoriów.
RHEL-SAP      | 7.4      | Lvm    | Agent systemu Linux | RHEL 7.4 dla SAP HANA i aplikacji biznesowych. Dołączone do repozytoriów E4S, pobiera premię za SAP i RHEL, a także podstawową opłatę obliczeniową.
|             | 74sap-gen2| Lvm    | Agent systemu Linux | RHEL 7.4 dla SAP HANA i aplikacji biznesowych. Obraz generacji 2. Dołączone do repozytoriów E4S, pobiera premię za SAP i RHEL, a także podstawową opłatę obliczeniową.
|             | 7,5       | Lvm    | Agent systemu Linux | RHEL 7.5 dla SAP HANA i aplikacji biznesowych. Dołączone do repozytoriów E4S, pobiera premię za SAP i RHEL, a także podstawową opłatę obliczeniową.
|             | 75sap-gen2| Lvm    | Agent systemu Linux | RHEL 7.5 dla SAP HANA i aplikacji biznesowych. Obraz generacji 2. Dołączone do repozytoriów E4S, pobiera premię za SAP i RHEL, a także podstawową opłatę obliczeniową.
|             | 7.6       | Lvm    | Agent systemu Linux | RHEL 7.6 dla SAP HANA i aplikacji biznesowych. Dołączone do repozytoriów E4S, pobiera premię za SAP i RHEL, a także podstawową opłatę obliczeniową.
|             | 76sap-gen2| Lvm    | Agent systemu Linux | RHEL 7.6 dla SAP HANA i aplikacji biznesowych. Obraz generacji 2. Dołączone do repozytoriów E4S, pobiera premię za SAP i RHEL, a także podstawową opłatę obliczeniową.
|             | 7.7       | Lvm    | Agent systemu Linux | RHEL 7.7 dla SAP HANA i aplikacji biznesowych. Dołączone do repozytoriów E4S, pobiera premię za SAP i RHEL, a także podstawową opłatę obliczeniową.
RHEL-SAP-HANA | 6.7       | Raw    | Agent systemu Linux | RHEL 6.7 dla SAP HANA. Przestarzałe na rzecz obrazów RHEL-SAP.
|             | 7.2       | Lvm    | Agent systemu Linux | RHEL 7.2 dla SAP HANA. Przestarzałe na rzecz obrazów RHEL-SAP.
|             | 7.3       | Lvm    | Agent systemu Linux | RHEL 7.3 dla SAP HANA. Przestarzałe na rzecz obrazów RHEL-SAP.
APLIKACJE RHEL-SAP | 6.8       | Raw    | Agent systemu Linux | RHEL 6.8 dla aplikacji biznesowych SAP. Przestarzałe na rzecz obrazów RHEL-SAP.
|             | 7.3       | Lvm    | Agent systemu Linux | RHEL 7.3 dla aplikacji biznesowych SAP. Przestarzałe na rzecz obrazów RHEL-SAP.
RHEL-HA       | 7.4       | Lvm    | Agent systemu Linux | RHEL 7.4 z dodatkiem HA. Pobiera premię za HA i RHEL oprócz podstawowej opłaty obliczeniowej.
|             | 7,5       | Lvm    | Agent systemu Linux | RHEL 7.5 z dodatkiem HA. Pobiera premię za HA i RHEL oprócz podstawowej opłaty obliczeniowej.
|             | 7.6       | Lvm    | Agent systemu Linux | RHEL 7.6 z dodatkiem HA. Pobiera premię za HA i RHEL oprócz podstawowej opłaty obliczeniowej.
RHEL-SAP-HA   | 7.4          | Lvm    | Agent systemu Linux | RHEL 7.4 dla SAP z ha i usługi aktualizacji. Dołączone do repozytoriów E4S. Pobiera premię za repozytoria SAP i HA, a także RHEL, oprócz podstawowych opłat obliczeniowych.
|             | 74safa-gen2 | Lvm    | Agent systemu Linux | RHEL 7.4 dla SAP z ha i usługi aktualizacji. Obraz generacji 2. Dołączone do repozytoriów E4S. Pobiera premię za repozytoria SAP i HA, a także RHEL, oprócz podstawowych opłat obliczeniowych.
|             | 7,5          | Lvm    | Agent systemu Linux | RHEL 7.5 dla SAP z ha i usługi aktualizacji. Dołączone do repozytoriów E4S. Pobiera premię za repozytoria SAP i HA, a także RHEL, oprócz podstawowych opłat obliczeniowych.
|             | 7.6          | Lvm    | Agent systemu Linux | RHEL 7.6 dla SAP z ha i usługi aktualizacji. Dołączone do repozytoriów E4S. Pobiera premię za repozytoria SAP i HA, a także RHEL, oprócz podstawowych opłat obliczeniowych.
|             | 76safa-gen2 | Lvm    | Agent systemu Linux | RHEL 7.6 dla SAP z ha i usługi aktualizacji. Obraz generacji 2. Dołączone do repozytoriów E4S. Pobiera premię za repozytoria SAP i HA, a także RHEL, oprócz podstawowych opłat obliczeniowych.
|             | 7.7          | Lvm    | Agent systemu Linux | RHEL 7.7 dla SAP z ha i usługi aktualizacji. Dołączone do repozytoriów E4S. Pobiera premię za repozytoria SAP i HA, a także RHEL, oprócz podstawowych opłat obliczeniowych.
|             | 77safa-gen2 | Lvm    | Agent systemu Linux | RHEL 7.7 dla SAP z ha i usługi aktualizacji. Obraz generacji 2. Dołączone do repozytoriów E4S. Pobiera premię za repozytoria SAP i HA, a także RHEL, oprócz podstawowych opłat obliczeniowych.
rhel-byos     |rhel-lvm74| Lvm    | Agent systemu Linux | Obrazy RHEL 7.4 BYOS, nie dołączone do żadnego źródła aktualizacji, nie będą pobierać premii RHEL.
|             |rhel-lvm75| Lvm    | Agent systemu Linux | Obrazy RHEL 7.5 BYOS, nie dołączone do żadnego źródła aktualizacji, nie będą pobierać premii RHEL.
|             |rhel-lvm76| Lvm    | Agent systemu Linux | Obrazy RHEL 7.6 BYOS, nie dołączone do żadnego źródła aktualizacji, nie będą pobierać premii RHEL.
|             |rhel-lvm77| Lvm    | Agent systemu Linux | Obrazy RHEL 7.7 BYOS, nie dołączone do żadnego źródła aktualizacji, nie będą pobierać premii RHEL.
|             |rhel-lvm8 | Lvm    | Agent systemu Linux | Obrazy RHEL 8 BYOS (wersja pomocnicza RHEL jest wyświetlana w wartości wersji obrazu), nie jest dołączona do żadnego źródła aktualizacji, nie pobiera premii RHEL.

> [!NOTE]
> Oferta produktów RHEL-SAP-HANA jest uważana przez Red Hat za koniec życia. Istniejące wdrożenia będą nadal działać normalnie, ale Red Hat zaleca, aby klienci migrować z obrazów RHEL-SAP-HANA do obrazów RHEL-SAP-HA, który zawiera repozytoria SAP HANA, a także dodatek wysokiej klasy. Więcej informacji na temat oferty chmury SAP firmy Red Hat można znaleźć [tutaj.](https://access.redhat.com/articles/3751271)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [obrazach Red Hat](./redhat-images.md)na platformie Azure .
* Dowiedz się więcej o [infrastrukturze aktualizacji Red Hat](./redhat-rhui.md).
* Dowiedz się więcej o [ofercie RHEL BYOS.](./byos.md)
* Informacje na temat zasad obsługi Red Hat dla wszystkich wersji RHEL można znaleźć na stronie [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
