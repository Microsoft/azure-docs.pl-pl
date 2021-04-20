---
title: Informacje na temat warunków SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Poznaj warunki użytkowania SAP HANA azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a243b348c01e6d1297a6a1fe016e3b6bc8d98d47
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719083"
---
# <a name="know-the-terms"></a>Poznawanie warunków

Kilka typowych definicji jest szeroko używanych w Przewodniku po architekturze i wdrażaniu technicznym. Zwróć uwagę na następujące terminy i ich znaczenie:

- **IaaS:** Infrastruktura jako usługa.
- **PaaS:** Platforma jako usługa.
- **SaaS:** oprogramowanie jako usługa.
- **Składnik SAP:** pojedyncza aplikacja SAP, taka jak ERP Central Component (ECC), Business Warehouse (BW), Solution Manager lub Enterprise Portal (EP). Składniki SAP mogą być oparte na tradycyjnych technologiach ABAP lub Java albo aplikacjach innych niż NetWeaver, takich jak obiekty biznesowe.
- **Środowisko SAP:** co najmniej jeden składnik SAP pogrupowany logicznie w celu wykonywania funkcji biznesowej, takiej jak opracowywanie, zapewnianie jakości, szkolenie, odzyskiwanie po awarii lub produkcja.
- **Krajobraz SAP:** odnosi się do wszystkich zasobów SAP w twoim krajobrazie IT. Środowisko SAP obejmuje wszystkie środowiska produkcyjne i nieprodukcyjne.
- **System SAP:** kombinacja warstwy DBMS i warstwy aplikacji, na przykład systemu dewelopera SAP ERP, systemu SAP BW testowego i systemu produkcyjnego SAP CRM. Wdrożenia platformy Azure nie obsługują dzielenia tych dwóch warstw między środowiskiem lokalnym i platformą Azure. System SAP jest wdrażany lokalnie lub na platformie Azure. Różne systemy środowiska SAP można wdrożyć na platformie Azure lub lokalnie. Na przykład można wdrożyć systemy dewelopera i testowania SAP CRM na platformie Azure podczas wdrażania lokalnego systemu produkcyjnego SAP CRM. W przypadku SAP HANA na platformie Azure (duże wystąpienia) zamierzony jest hostowanie warstwy aplikacji SAP systemów SAP na komputerach wirtualnych i powiązanego wystąpienia usługi SAP HANA w jednostce w sygnaturze SAP HANA na platformie Azure (duże wystąpienia).
- **Sygnatura dużych** wystąpień: stos infrastruktury sprzętowej, SAP HANA certyfikowany przez infrastrukturę TDI i przeznaczony do uruchamiania SAP HANA na platformie Azure.
- **SAP HANA na platformie Azure (duże wystąpienia):** Oficjalna nazwa oferty na platformie Azure uruchamiania wystąpień hana na platformie SAP HANA certyfikowanym sprzętem TDI, który jest wdrażany w sygnaturach dużych wystąpień w różnych regionach świadczenia usługi Azure. Termin duże wystąpienie *hana* to skrót *od SAP HANA Azure (duże wystąpienia)* i jest powszechnie używany w tym podręczniku wdrażania technicznego.
- **Wiele** lokacji: opisuje scenariusz, w którym maszyny wirtualne są wdrażane w subskrypcji platformy Azure, która ma łączność typu lokacja-lokacja, wiele lokacji lub Azure ExpressRoute między lokalnymi centrami danych i platformą Azure. W typowej dokumentacji platformy Azure tego rodzaju wdrożenia są również opisane jako scenariusze dla wielu środowisk. Przyczyną połączenia jest rozszerzenie domen lokalnych, lokalnych usług Azure Active Directory/OpenLDAP i lokalnej usługi DNS na platformę Azure. Obszar środowisk lokalnych jest rozszerzony na zasoby platformy Azure w subskrypcjach platformy Azure. Dzięki temu rozszerzeniu maszyny wirtualne mogą być częścią domeny lokalnej. 

   Użytkownicy domeny domeny lokalnej mogą uzyskać dostęp do serwerów i uruchamiać usługi na tych maszyn wirtualnych (takich jak usługi DBMS). Możliwa jest komunikacja i rozpoznawania nazw między maszynami wirtualnych wdrożonych lokalnie i maszynami wirtualnych wdrożonych na platformie Azure. Ten scenariusz jest typowy dla sposobu wdrażania większości zasobów SAP. Aby uzyskać więcej informacji, zobacz [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) and Create a virtual network with a site-to-site connection by using the Azure Portal (Tworzenie sieci wirtualnej za pomocą połączenia [lokacja-lokacja przy użyciu Azure Portal).](../../../vpn-gateway/tutorial-site-to-site-portal.md)
- **Dzierżawa:** klient wdrożony w sygnaturze dużego wystąpienia HANA jest izolowany w *dzierżawie.* Dzierżawa jest izolowana w warstwie sieci, magazynu i zasobów obliczeniowych od innych dzierżaw. Jednostki magazynowe i obliczeniowe przypisane do różnych dzierżaw nie widzą siebie nawzajem ani nie komunikują się ze sobą na poziomie sygnatury dużego wystąpienia HANA. Klient może zdecydować się na wdrożenie w różnych dzierżawach. Nawet wtedy nie ma komunikacji między dzierżawami na poziomie sygnatury dużego wystąpienia HANA.
- **Kategoria SKU:** w przypadku dużych wystąpień HANA oferowane są następujące dwie kategorie jednostki SKU:
    - **Klasa typu I:** S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 i S224 m
    - **Klasa typu II:** S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm i S960m
- **Sygnatura:** definiuje wewnętrzny rozmiar wdrożenia firmy Microsoft dużych wystąpień hana. Przed wdrożeniem jednostek dużego wystąpienia HANA należy wdrożyć sygnaturę dużego wystąpienia HANA składającą się z regałów obliczeniowych, sieciowych i magazynowych w lokalizacji centrum danych. Takie wdrożenie jest nazywane sygnaturą dużego wystąpienia HANA lub z poprawki 4 (patrz poniżej) w przypadku używania alternatywnego terminu dużego **wiersza wystąpienia**
- **Poprawka:** istnieją dwie różne wersje sygnatur dla sygnatur dużego wystąpienia HANA. Różnią się one architekturą i bliskości hostów maszyn wirtualnych platformy Azure.
    - "Poprawka 3" (Rev 3) to oryginalny projekt wdrożony w połowie 2016 r.
    - "Poprawka 4.2" (Rev 4.2) to nowy projekt, który zapewnia bliższą odległość od hostów maszyn wirtualnych platformy Azure. Wersja Rev 4.2 oferuje bardzo małe opóźnienie sieci między maszynami wirtualnych platformy Azure i jednostkami dużego wystąpienia HANA. Zasoby w Azure Portal są nazywane infrastrukturą baremetalową. Klienci mogą uzyskać dostęp do swoich zasobów jako wystąpień programu BareMetal z Azure Portal. 

Dostępnych jest wiele dodatkowych zasobów na temat wdrażania obciążenia SAP w chmurze. Jeśli planujesz wdrożenie usługi SAP HANA na platformie Azure, musisz mieć doświadczenie z zasadami IaaS platformy Azure i wdrażaniem obciążeń SAP w usłudze Azure IaaS. Przed kontynuowaniem zobacz [Używanie rozwiązań SAP na maszynach](get-started.md) wirtualnych platformy Azure, aby uzyskać więcej informacji. 

## <a name="next-steps"></a>Następne kroki
- Zapoznaj się z [tematem Certyfikacja HLI.](hana-certification.md)