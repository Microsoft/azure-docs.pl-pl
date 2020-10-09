---
title: Rozwiązanie VMware firmy Azure według CloudSimple — tworzenie CloudSimple prywatnej chmury
description: Opisuje, jak utworzyć chmurę prywatną CloudSimple do rozbudowania obciążeń programu VMware do chmury z elastycznością operacyjną i ciągłością
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77024793"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Utwórz chmurę prywatną CloudSimple

Chmura prywatna to izolowany stos oprogramowania VMware obsługujący hosty ESXi, vCenter, sieci vSAN i NSX. Chmurami prywatnymi zarządza się za pomocą portalu CloudSimple. Mają własny serwer vCenter we własnej domenie zarządzania. Stos działa na dedykowanych węzłach i izolowanych węzłach sprzętowych bez systemu operacyjnego.

Tworzenie chmury prywatnej pomaga sprostać różnym typowym potrzebom związanym z infrastrukturą sieci:

* **Wzrostu**. Jeśli do istniejącej infrastruktury został osiągnięty punkt odświeżenia sprzętowego, Chmura prywatna pozwala rozszerzyć system bez konieczności używania nowych inwestycji sprzętowych.

* **Szybkie rozszerzanie**. Jeśli wystąpią jakiekolwiek tymczasowe lub nieplanowane zapotrzebowanie na pojemność, Chmura prywatna pozwala na utworzenie dodatkowej pojemności bez opóźnień.

* **Zwiększona ochrona**. W przypadku chmury prywatnej co najmniej trzech węzłów otrzymujesz automatyczną nadmiarowość i ochronę o wysokiej dostępności.

* **Długoterminowe potrzeby dotyczące infrastruktury**. Jeśli centra danych mają pojemność lub chcesz zmienić strukturę w celu obniżenia kosztów, Chmura prywatna pozwala na wycofanie centrów danych i migrację do rozwiązania opartego na chmurze, a pozostałe zgodne z operacjami przedsiębiorstwa.

Podczas tworzenia chmury prywatnej otrzymujesz jeden klaster vSphere i wszystkie maszyny wirtualne zarządzania utworzone w tym klastrze.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby można było utworzyć chmurę prywatną, węzły muszą być obsługiwane. Aby uzyskać więcej informacji o węzłach aprowizacji, zobacz temat [udostępnianie węzłów dla rozwiązań VMware platformy Azure przez CloudSimple](create-nodes.md).

Przydziel zakres CIDR dla podsieci vSphere/sieci vSAN dla chmury prywatnej. Chmura prywatna jest tworzona jako izolowane środowisko stosu VMware (z hostami ESXi, vCenter, sieci vSAN i NSX) zarządzanych przez serwer vCenter. Składniki zarządzania są wdrażane w sieci wybranej dla podsieci vSphere/sieci vSAN CIDR. Zakres CIDR sieci jest podzielony na różne podsieci podczas wdrażania. Przestrzeń adresów podsieci vSphere/sieci vSAN musi być unikatowa. Nie może pokrywać się z żadną siecią, która komunikuje się ze środowiskiem CloudSimple. Sieci, które komunikują się z usługą CloudSimple, obejmują sieci lokalne i sieci wirtualne platformy Azure. Aby uzyskać więcej informacji na temat podsieci vSphere/sieci vSAN, zobacz sieci VLAN i podsieci — Omówienie.

* Minimalna prefiks zakresu CIDR vSphere/sieci vSAN:/24
* Maksymalna vSphere/sieci vSAN prefiks zakresu CIDR:/21


## <a name="access-the-cloudsimple-portal"></a>Uzyskiwanie dostępu do portalu CloudSimple

Dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Utwórz nową chmurę prywatną

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **usługi CloudSimple Services**.
3. Wybierz usługę CloudSimple, w której chcesz utworzyć chmurę prywatną.
4. W obszarze **Przegląd**kliknij pozycję **Utwórz chmurę prywatną** , aby otworzyć nową kartę przeglądarki dla portalu CloudSimple. Jeśli zostanie wyświetlony monit, zaloguj się przy użyciu poświadczeń logowania na platformie Azure.

    ![Utwórz chmurę prywatną na platformie Azure](media/create-private-cloud-from-azure.png)

5. W portalu CloudSimple Podaj nazwę chmury prywatnej.
6. Wybierz **lokalizację** chmury prywatnej.
7. Wybierz **Typ węzła**, spójny z zainicjowaną obsługą na platformie Azure.
8. Określ **liczbę węzłów**.  Aby można było utworzyć chmurę prywatną, wymagane są co najmniej trzy węzły.

    ![Tworzenie chmury prywatnej — informacje podstawowe](media/create-private-cloud-basic-info.png)

9. Kliknij przycisk **Dalej: Opcje zaawansowane**.
10. Wprowadź zakres CIDR dla podsieci vSphere/sieci vSAN. Upewnij się, że zakres CIDR nie nakłada się na żadną z lokalizacji lokalnych lub innych podsieci platformy Azure (sieci wirtualnych) lub z podsiecią bramy.

    **Opcje zakresu CIDR:** /24,/23,/22 lub/21. Zakres CIDR/24 obsługuje maksymalnie dziewięć węzłów, a/23 zakres CIDR obsługuje do 41 węzłów, a zakres CIDR/22 i/21 obsługuje maksymalnie 64 węzłów (maksymalną liczbę węzłów w chmurze prywatnej).

    > [!IMPORTANT]
    > Adresy IP w zakresie CIDR vSphere/sieci vSAN są zarezerwowane do użytku przez infrastrukturę chmury prywatnej.  Nie używaj adresu IP z tego zakresu na żadnej maszynie wirtualnej.

11. Kliknij przycisk **Dalej: Przejrzyj i Utwórz**.
12. Przejrzyj ustawienia. Jeśli musisz zmienić dowolne ustawienia, kliknij przycisk **Wstecz**.
13. Kliknij przycisk **Utwórz**.

Rozpocznie się proces aprowizacji w chmurze prywatnej. Zainicjowanie obsługi chmury prywatnej może potrwać do dwóch godzin.

Aby uzyskać instrukcje dotyczące rozwinięcia istniejącej chmury prywatnej, zobacz [Rozwiń chmurę prywatną](expand-private-cloud.md).
