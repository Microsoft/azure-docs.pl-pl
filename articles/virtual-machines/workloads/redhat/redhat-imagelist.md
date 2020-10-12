---
title: Obrazy Red Hat Enterprise Linux dostępne na platformie Azure
description: Dowiedz się więcej na temat Red Hat Enterprise Linux obrazów w Microsoft Azure
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
ms.openlocfilehash: f4ebf433237193c0db7859b029bb224c72c92eae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87291011"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Obrazy Red Hat Enterprise Linux (RHEL) dostępne na platformie Azure
Platforma Azure oferuje różne obrazy RHEL dla różnych przypadków użycia.

> [!NOTE]
> Wszystkie obrazy RHEL są dostępne w chmurach publicznych i Azure Government platformy Azure. Nie są one dostępne w chmurach platformy Azure w Chinach.

## <a name="list-of-rhel-images"></a>Lista obrazów RHEL
Jest to lista obrazów RHEL dostępnych na platformie Azure. O ile nie określono inaczej, wszystkie obrazy są LVM i dołączone do zwykłych repozytoriów RHEL (nie EUS, a nie E4S). Następujące obrazy są obecnie dostępne do użytku ogólnego:

> [!NOTE]
> Obrazy RAW nie są już tworzone na korzyść obrazów LVM. LVM zapewnia kilka korzyści w porównaniu ze starszym schematem partycjonowania RAW (innym niż LVM), w tym znacznie bardziej elastyczne opcje zmiany rozmiarów partycji.

Oferta| SKU | Partycjonowanie | Inicjowanie obsługi | Uwagi
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | SUROWCÓW    | Agent systemu Linux |
|             | 6.8      | SUROWCÓW    | Agent systemu Linux |
|             | 6,9      | SUROWCÓW    | Agent systemu Linux |
|             | 6,10     | SUROWCÓW    | Agent systemu Linux |
|             | 7 — NIEPRZETWORZONY    | SUROWCÓW    | Agent systemu Linux | Rodzina obrazów RHEL 7. x. <br> Domyślnie dołączone do zwykłych repozytoriów (nie EUS).
|             | 7 — LVM    | LVM    | Agent systemu Linux | Rodzina obrazów RHEL 7. x. <br> Domyślnie dołączone do zwykłych repozytoriów (nie EUS). Jeśli szukasz standardowego obrazu RHEL do wdrożenia, użyj tego zestawu obrazów i/lub jego odpowiednika generacji 2.
|             | 7lvm — Gen2| LVM    | Agent systemu Linux | Rodzina obrazów generacji 2, RHEL 7. x. <br> Domyślnie dołączone do zwykłych repozytoriów (nie EUS). Jeśli szukasz standardowego obrazu RHEL do wdrożenia, użyj tego zestawu obrazów i/lub jego odpowiednika generacji 1.
|             | 7-RAW-CI | NIEPRZETWORZONY ELEMENT CI | Cloud-init  | Rodzina obrazów RHEL 7. x. <br> Domyślnie dołączone do zwykłych repozytoriów (nie EUS).
|             | 7.2      | SUROWCÓW    | Agent systemu Linux |
|             | 7.3      | SUROWCÓW    | Agent systemu Linux |
|             | 7,4      | SUROWCÓW    | Agent systemu Linux | Dołączono do EUS repozytoriów domyślnie od kwietnia 2019.
|             | 74 – Gen2  | SUROWCÓW    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 7,5      | SUROWCÓW    | Agent systemu Linux | Dołączono do EUS repozytoriów domyślnie od czerwca 2019.
|             | 75 – Gen2  | SUROWCÓW    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 7,6      | SUROWCÓW    | Agent systemu Linux | Dołączono do EUS repozytoriów domyślnie od maja 2019.
|             | 76 – Gen2  | SUROWCÓW    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 7,7      | LVM    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 77 – Gen2  | LVM    | Agent systemu Linux | Domyślnie dołączone do repozytoriów EUS.
|             | 7,8      | LVM    | Agent systemu Linux | Dołączone do zwykłych repozytoriów (EUS niedostępne dla RHEL 7,8)
|             | 78 – Gen2  | LVM    | Agent systemu Linux | Dołączone do zwykłych repozytoriów (EUS niedostępne dla RHEL 7,8)
|             | 8 — LVM    | LVM    | Agent systemu Linux | Rodzina obrazów RHEL 8. x. Dołączone do zwykłych repozytoriów.
|             | 8-LVM-Gen2| LVM    | Agent systemu Linux | Funkcja Hyper-V Generation 2-RHEL 8. x rodzina obrazów. Dołączone do zwykłych repozytoriów.
|             | 8        | LVM    | Agent systemu Linux | Obrazy z RHEL 8,0.
|             | 8 — Gen2   | LVM    | Agent systemu Linux | Obrazy funkcji Hyper-V Generation 2-RHEL 8,0.
|             | 8.1      | LVM    | Agent systemu Linux | Obrazy z RHEL 8,2. Obecnie dołączone do zwykłych repozytoriów.
|             | 81gen2   | LVM    | Agent systemu Linux | Obrazy funkcji Hyper-V Generation 2-RHEL 8,1. Obecnie dołączone do zwykłych repozytoriów.
|             | 8,1-Ci   | LVM    | Agent systemu Linux | RHEL 8,1 obrazów przy użyciu funkcji Cloud-init jako agenta aprowizacji. Obecnie dołączone do zwykłych repozytoriów.
|             | 81-Ci-Gen2| LVM    | Agent systemu Linux | Funkcja Hyper-V Generation 2-RHEL 8,1 obrazów przy użyciu usługi Cloud-init jako agenta aprowizacji. Obecnie dołączone do zwykłych repozytoriów.
|             | 8.2      | LVM    | Agent systemu Linux | Obrazy z RHEL 8,2. Obecnie dołączone do zwykłych repozytoriów.
|             | 82gen2   | LVM    | Agent systemu Linux | Obrazy funkcji Hyper-V Generation 2-RHEL 8,1. Obecnie dołączone do zwykłych repozytoriów.
RHEL — SAP      | 7,4      | LVM    | Agent systemu Linux | RHEL SAP HANA 7,4 i aplikacje biznesowe. Dołączenie do repozytoriów E4S spowoduje naliczenie wersji Premium dla SAP i RHEL, a także Podstawowa opłata obliczeniowa.
|             | 74sap — Gen2| LVM    | Agent systemu Linux | RHEL SAP HANA 7,4 i aplikacje biznesowe. Obraz generacji 2. Dołączenie do repozytoriów E4S spowoduje naliczenie wersji Premium dla SAP i RHEL, a także Podstawowa opłata obliczeniowa.
|             | 7,5       | LVM    | Agent systemu Linux | RHEL SAP HANA 7,5 i aplikacje biznesowe. Dołączenie do repozytoriów E4S spowoduje naliczenie wersji Premium dla SAP i RHEL, a także Podstawowa opłata obliczeniowa.
|             | 75sap — Gen2| LVM    | Agent systemu Linux | RHEL SAP HANA 7,5 i aplikacje biznesowe. Obraz generacji 2. Dołączenie do repozytoriów E4S spowoduje naliczenie wersji Premium dla SAP i RHEL, a także Podstawowa opłata obliczeniowa.
|             | 7,6       | LVM    | Agent systemu Linux | RHEL SAP HANA 7,6 i aplikacje biznesowe. Dołączenie do repozytoriów E4S spowoduje naliczenie wersji Premium dla SAP i RHEL, a także Podstawowa opłata obliczeniowa.
|             | 76sap — Gen2| LVM    | Agent systemu Linux | RHEL SAP HANA 7,6 i aplikacje biznesowe. Obraz generacji 2. Dołączenie do repozytoriów E4S spowoduje naliczenie wersji Premium dla SAP i RHEL, a także Podstawowa opłata obliczeniowa.
|             | 7,7       | LVM    | Agent systemu Linux | RHEL SAP HANA 7,7 i aplikacje biznesowe. Dołączenie do repozytoriów E4S spowoduje naliczenie wersji Premium dla SAP i RHEL, a także Podstawowa opłata obliczeniowa.
RHEL — SAP-HANA | 6.7       | SUROWCÓW    | Agent systemu Linux | RHEL 6,7 dla SAP HANA. Nieaktualna korzyść dla obrazów RHEL-SAP.
|             | 7.2       | LVM    | Agent systemu Linux | RHEL 7,2 dla SAP HANA. Nieaktualna korzyść dla obrazów RHEL-SAP.
|             | 7.3       | LVM    | Agent systemu Linux | RHEL 7,3 dla SAP HANA. Nieaktualna korzyść dla obrazów RHEL-SAP.
RHEL — SAP-APPS | 6.8       | SUROWCÓW    | Agent systemu Linux | RHEL 6,8 dla oprogramowania SAP Business Applications. Nieaktualna korzyść dla obrazów RHEL-SAP.
|             | 7.3       | LVM    | Agent systemu Linux | RHEL 7,3 dla oprogramowania SAP Business Applications. Nieaktualna korzyść dla obrazów RHEL-SAP.
RHEL – HA       | 7,4       | LVM    | Agent systemu Linux | RHEL 7,4 z dodatkiem HA. Naliczanie wersji Premium za HA i RHEL na podstawie podstawowej opłaty obliczeniowej.
|             | 7,5       | LVM    | Agent systemu Linux | RHEL 7,5 z dodatkiem HA. Naliczanie wersji Premium za HA i RHEL na podstawie podstawowej opłaty obliczeniowej.
|             | 7,6       | LVM    | Agent systemu Linux | RHEL 7,6 z dodatkiem HA. Naliczanie wersji Premium za HA i RHEL na podstawie podstawowej opłaty obliczeniowej.
RHEL — SAP-HA   | 7,4          | LVM    | Agent systemu Linux | RHEL 7,4 dla oprogramowania SAP z usługami HA i Update Services. Dołączono do repozytoriów E4S. Naliczanie wersji Premium dla repozytoriów oprogramowania SAP i HA oraz RHEL w oparciu o podstawowe opłaty za obliczenia.
|             | 74sapha — Gen2 | LVM    | Agent systemu Linux | RHEL 7,4 dla oprogramowania SAP z usługami HA i Update Services. Obraz generacji 2. Dołączono do repozytoriów E4S. Naliczanie wersji Premium dla repozytoriów oprogramowania SAP i HA oraz RHEL w oparciu o podstawowe opłaty za obliczenia.
|             | 7,5          | LVM    | Agent systemu Linux | RHEL 7,5 dla oprogramowania SAP z usługami HA i Update Services. Dołączono do repozytoriów E4S. Naliczanie wersji Premium dla repozytoriów oprogramowania SAP i HA oraz RHEL w oparciu o podstawowe opłaty za obliczenia.
|             | 7,6          | LVM    | Agent systemu Linux | RHEL 7,6 dla oprogramowania SAP z usługami HA i Update Services. Dołączono do repozytoriów E4S. Naliczanie wersji Premium dla repozytoriów oprogramowania SAP i HA oraz RHEL w oparciu o podstawowe opłaty za obliczenia.
|             | 76sapha — Gen2 | LVM    | Agent systemu Linux | RHEL 7,6 dla oprogramowania SAP z usługami HA i Update Services. Obraz generacji 2. Dołączono do repozytoriów E4S. Naliczanie wersji Premium dla repozytoriów oprogramowania SAP i HA oraz RHEL w oparciu o podstawowe opłaty za obliczenia.
|             | 7,7          | LVM    | Agent systemu Linux | RHEL 7,7 dla oprogramowania SAP z usługami HA i Update Services. Dołączono do repozytoriów E4S. Naliczanie wersji Premium dla repozytoriów oprogramowania SAP i HA oraz RHEL w oparciu o podstawowe opłaty za obliczenia.
|             | 77sapha — Gen2 | LVM    | Agent systemu Linux | RHEL 7,7 dla oprogramowania SAP z usługami HA i Update Services. Obraz generacji 2. Dołączono do repozytoriów E4S. Naliczanie wersji Premium dla repozytoriów oprogramowania SAP i HA oraz RHEL w oparciu o podstawowe opłaty za obliczenia.
RHEL — BYOS     |RHEL — lvm74| LVM    | Agent systemu Linux | RHEL 7,4 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL — lvm75| LVM    | Agent systemu Linux | RHEL 7,5 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL — lvm76| LVM    | Agent systemu Linux | RHEL 7,6 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL-lvm76-Gen2| LVM    | Agent systemu Linux | RHEL 7,6 generacji 2 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL — lvm77| LVM    | Agent systemu Linux | RHEL 7,7 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL-lvm77-Gen2| LVM    | Agent systemu Linux | RHEL 7,7 generacji 2 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL — lvm78| LVM    | Agent systemu Linux | RHEL 7,8 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL-lvm78-Gen2| LVM    | Agent systemu Linux | RHEL 7,8 generacji 2 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL — lvm8 | LVM    | Agent systemu Linux | RHEL 8,0 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL-lvm8-Gen2 | LVM    | Agent systemu Linux | RHEL 8,0 generacji 2 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL — lvm81 | LVM    | Agent systemu Linux | RHEL 8,1 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL-lvm81-Gen2 | LVM    | Agent systemu Linux | RHEL 8,1 generacji 2 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL — lvm82 | LVM    | Agent systemu Linux | RHEL 8,2 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.
|             |RHEL-lvm82-Gen2 | LVM    | Agent systemu Linux | RHEL 8,2 generacji 2 BYOS obrazy, które nie są dołączone do żadnego źródła aktualizacji, nie będą naliczane opłaty za usługę RHEL Premium.

> [!NOTE]
> Oferta produktu RHEL-SAP-HANA jest uważana za koniec życia przez firmę Red Hat. Istniejące wdrożenia będą nadal działały normalnie, ale Red Hat zaleca klientom Migrowanie z obrazów RHEL-SAP-HANA do obrazów RHEL-SAP-HA, które obejmują SAP HANA repozytoria, a także dodatek o wysokiej dostępności. Więcej informacji o ofertach rozwiązań w chmurze firmy Red Hat można znaleźć [tutaj](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [obrazach Red Hat na platformie Azure](./redhat-images.md).
* Dowiedz się więcej o [infrastrukturze aktualizacji Red Hat](./redhat-rhui.md).
* Dowiedz się więcej o [ofercie BYOS RHEL](./byos.md).
* Informacje o zasadach obsługi systemu Red Hat dla wszystkich wersji programu RHEL można znaleźć na stronie [cykl życia Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
