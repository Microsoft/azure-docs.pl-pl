---
title: Model operacji SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Model operacji SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e147e4a5f104ca4cd1a10a776c907e3f9f1d6128
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77616977"
---
# <a name="operations-model-and-responsibilities"></a>Model działania i obowiązki

Usługa udostępniona z SAP HANA na platformie Azure (duże wystąpienia) jest wyrównana z usługami Azure IaaS Services. Występuje wystąpienie dużego wystąpienia HANA z zainstalowanym systemem operacyjnym, który jest zoptymalizowany pod kątem SAP HANA. Podobnie jak w przypadku maszyn wirtualnych usługi Azure IaaS, większość zadań związanych z wzmacnianiem systemu operacyjnego, Instalowanie dodatkowego oprogramowania, instalowanie platformy HANA, eksploatacja systemów operacyjnych i HANA oraz aktualizowanie systemów operacyjnych i HANA jest odpowiedzialna. Firma Microsoft nie wymusza na użytkownikach aktualizacji systemu operacyjnego lub platformy HANA.

![Obowiązki SAP HANA na platformie Azure (duże wystąpienia)](./media/hana-overview-architecture/image2-responsibilities.png)

Jak pokazano na diagramie, SAP HANA na platformie Azure (duże wystąpienia) to wielodostępna oferta IaaS. W większości przypadków dział odpowiedzialności znajduje się na granicy infrastruktury systemu operacyjnego. Firma Microsoft jest odpowiedzialna za wszystkie aspekty usługi poniżej wiersza systemu operacyjnego. Użytkownik jest odpowiedzialny za wszystkie aspekty usługi powyżej wiersza. Jesteś odpowiedzialny za system operacyjny. Można nadal korzystać z większości bieżących metod lokalnych, które mogą być stosowane w celu zapewnienia zgodności, bezpieczeństwa, zarządzania aplikacjami, podstaw i zarządzania systemem operacyjnym. Systemy wyglądają tak, jakby znajdowały się w sieci.

Ta usługa jest zoptymalizowana pod kątem SAP HANA, więc istnieją obszary, w których należy współpracować z firmą Microsoft w celu korzystania z podstawowych funkcji infrastruktury w celu uzyskania najlepszych wyników.

Poniższa lista zawiera więcej szczegółów na temat poszczególnych warstw i obowiązków:

**Sieć**: wszystkie sieci wewnętrzne dla sygnatury dużego wystąpienia, na których działa SAP HANA. Odpowiedzialność obejmuje dostęp do magazynu, połączenia między wystąpieniami (w przypadku funkcji skalowania w poziomie i innych), łączności z poziomą i łącznością z platformą Azure, w której warstwa aplikacji SAP jest hostowana na maszynach wirtualnych. Obejmuje ona również łączność sieci WAN między centrami danych platformy Azure na potrzeby replikacji celów odzyskiwania po awarii. Wszystkie sieci są podzielone na partycje przez dzierżawcę i mają zastosowana jakość usług.

**Magazyn**: zwirtualizowany magazyn partycjonowany dla wszystkich woluminów wymaganych przez serwery SAP HANA, a także dla migawek. 

**Serwery**: dedykowane serwery fizyczne do uruchamiania SAP HANA baz danych przypisane do dzierżawców. Serwery typu I klasy SKU są abstrakcyjne sprzętowo. W przypadku tych typów serwerów konfiguracja serwera jest zbierana i utrzymywana w profilach, które można przenieść z jednego sprzętu fizycznego na inny sprzęt fizyczny. Takie (ręczne) Przenoszenie profilu według operacji można porównać z bitem do naprawy usługi platformy Azure. Serwery SKU klasy typu II nie oferują takiej możliwości.

**SDDC**: oprogramowanie do zarządzania służące do zarządzania centrami danych jako jednostkami zdefiniowanymi programowo. Pozwala ona firmie Microsoft na pule zasobów w celu skalowania, dostępności i wydajności.

**O/S**: wybrany system operacyjny (SUSE Linux lub Red Hat Linux), który jest uruchomiony na serwerach. Dostarczone przez siebie obrazy systemu operacyjnego zostały dostarczone przez dostawcę z systemem Linux do firmy Microsoft w celu uruchomienia SAP HANA. Musisz mieć subskrypcję z dostawcą systemu Linux dla określonego obrazu zoptymalizowanego pod kątem SAP HANA. Użytkownik jest odpowiedzialny za zarejestrowanie obrazów u dostawcy systemu operacyjnego. 

Od momentu przekazują przez firmę Microsoft użytkownik jest odpowiedzialny za wszelkie dalsze poprawki systemu operacyjnego Linux. Ta poprawka obejmuje dodatkowe pakiety, które mogą być niezbędne do pomyślnej instalacji SAP HANA i nie zostały uwzględnione przez określonego dostawcę systemu Linux w SAP HANA zoptymalizowanych obrazów systemów operacyjnych. (Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą instalacji oprogramowania SAP i uwagi dotyczące oprogramowania SAP). 

Użytkownik jest odpowiedzialny za stosowanie poprawek systemu operacyjnego z powodu nieprawidłowego działania lub optymalizacji systemu operacyjnego i jego sterowników względem określonego sprzętu serwera. Użytkownik jest odpowiedzialny za bezpieczeństwo lub stosowanie poprawek funkcjonalnych systemu operacyjnego. 

Odpowiedzialność obejmuje również monitorowanie i planowanie pojemności:

- Użycie zasobów procesora CPU.
- Użycie pamięci.
- Woluminy dyskowe związane z wolnym miejscem, operacjami IOPS i opóźnieniem.
- Ruch sieciowy na woluminie między dużym wystąpieniem HANA a warstwą aplikacji SAP.

Podstawowa infrastruktura usługi HANA duże wystąpienie zapewnia funkcję tworzenia kopii zapasowych i przywracania woluminu systemu operacyjnego. Korzystanie z tej funkcji jest również obowiązkiem użytkownika.

**Oprogramowanie pośredniczące**: wystąpienie SAP HANA, głównie. Odpowiedzialność za administrację, operacje i monitorowanie. Przy użyciu podanych funkcji można korzystać z migawek magazynu na potrzeby tworzenia kopii zapasowych i przywracania oraz odzyskiwania po awarii. Te możliwości są udostępniane przez infrastrukturę. Obowiązki obejmują również projektowanie wysokiej dostępności i odzyskiwania po awarii dzięki tym funkcjom, ich wykorzystanie i monitorowanie w celu ustalenia, czy migawki magazynu zostały wykonane pomyślnie.

**Dane**: dane zarządzane przez SAP HANA i inne dane, takie jak kopie zapasowe plików znajdujące się na woluminach lub udziałach plików. Obowiązki obejmują monitorowanie wolnego miejsca na dysku i zarządzanie zawartością na woluminach. Użytkownik jest odpowiedzialny za monitorowanie pomyślnego wykonania kopii zapasowych woluminów dyskowych i migawek magazynu. Pomyślne wykonanie replikacji danych do lokacji odzyskiwania po awarii jest odpowiedzialne za firmę Microsoft.

**Aplikacje:** Wystąpienia aplikacji SAP lub, w przypadku aplikacji innych niż SAP, warstwa aplikacji tych aplikacji. Twoje obowiązki obejmują wdrażanie, administrację, operacje i monitorowanie tych aplikacji. Użytkownik jest odpowiedzialny za planowanie pojemności procesora CPU, użycie pamięci, użycie usługi Azure Storage i wykorzystanie przepustowości sieci w sieciach wirtualnych. Użytkownik jest odpowiedzialny za planowanie pojemności dla wykorzystania zasobów z sieci wirtualnych do SAP HANA na platformie Azure (duże wystąpienia).

**WAN**: połączenia nawiązane z lokalnymi wdrożeniami platformy Azure dla obciążeń. Wszyscy klienci z dużym wystąpieniem HANA używają usługi Azure ExpressRoute do łączności. To połączenie nie jest częścią SAP HANA na platformie Azure (duże wystąpienia). Użytkownik jest odpowiedzialny za instalację tego połączenia.

**Archiwum**: można chcieć archiwizować kopie danych przy użyciu własnych metod na kontach magazynu. Archiwizowanie wymaga zarządzania, zgodności, kosztów i operacji. Użytkownik jest odpowiedzialny za generowanie kopii archiwalnych i kopii zapasowych na platformie Azure i przechowywanie ich w sposób zgodny.

Zobacz umowę [SLA dotyczącą SAP HANA na platformie Azure (duże wystąpienia)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Następne kroki**
- Rozpoznaj [architekturę SAP HANA (duże wystąpienia) na platformie Azure](hana-architecture.md)
