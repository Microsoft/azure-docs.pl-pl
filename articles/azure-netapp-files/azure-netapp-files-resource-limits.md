---
title: Limity zasobów dla Azure NetApp Files | Microsoft Docs
description: Opisuje limity Azure NetApp Files zasobów i sposób żądania zwiększenia limitu zasobów.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: f023bfa2b3941f7d667f4be34a8ee8dc1ed9a9c3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750198"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure NetApp Files

Zrozumienie limitów zasobów Azure NetApp Files ułatwia zarządzanie woluminami.

## <a name="resource-limits"></a>Limity zasobów

W poniższej tabeli opisano limity zasobów dla Azure NetApp Files:

|  Zasób  |  Limit domyślny  |  Dostosowywane za pośrednictwem wniosku o pomoc techniczną  |
|----------------|---------------------|--------------------------------------|
|  Liczba kont usługi NetApp na region platformy Azure na subskrypcję  |  10    |  Tak   |
|  Liczba pul pojemności na konto netapp   |    25     |   Tak   |
|  Liczba woluminów na subskrypcję   |    500     |   Tak   |
|  Liczba woluminów na pulę pojemności     |    500   |    Tak     |
|  Liczba migawek na wolumin       |    255     |    Nie        |
|  Liczba podsieci delegowanych do usługi Azure NetApp Files (Microsoft.NetApp/volumes) na platformę Azure Virtual Network    |   1   |    Nie    |
|  Liczba używanych ip w sieci wirtualnej (w tym natychmiastowa komunikacja równorzędna sieci wirtualnych) z Azure NetApp Files   |    1000   |    Nie   |
|  Minimalny rozmiar pojedynczej puli pojemności   |  4 TiB     |    Nie  |
|  Maksymalny rozmiar pojedynczej puli pojemności    |  500 TiB   |   Nie   |
|  Minimalny rozmiar pojedynczego woluminu    |    100 GiB    |    Nie    |
|  Maksymalny rozmiar pojedynczego woluminu     |    100 TiB    |    Nie    |
|  Maksymalny rozmiar pojedynczego pliku     |    16 TiB    |    Nie    |    
|  Maksymalny rozmiar metadanych katalogu w jednym katalogu      |    320 MB    |    Nie    |    
|  Maksymalna liczba plików[(maxfiles)](#maxfiles)na wolumin     |    100 mln    |    Tak    |    
|  Maksymalna liczba reguł zasad eksportu na wolumin     |    5  |    Nie    | 
|  Minimalna przypisana przepływność dla ręcznego woluminu QoS     |    1 MiB/s   |    Nie    |    
|  Maksymalna przypisana przepływność dla ręcznego woluminu QoS     |    4500 MiB/s    |    Nie    |    
|  Liczba woluminów ochrony danych replikacji między regionami (woluminy docelowe)     |    5    |    Tak    |     

Aby sprawdzić, czy katalog zbliża się do maksymalnego limitu rozmiaru metadanych katalogu (320 MB), zobacz [Jak mogę,](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size)czy katalog zbliża się do limitu rozmiaru .   

Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące zarządzania pojemnością.](azure-netapp-files-faqs.md#capacity-management-faqs)

## <a name="maxfiles-limits"></a>Limity maksymalnych plików <a name="maxfiles"></a> 

Azure NetApp Files woluminów mają limit o nazwie *maxfiles.* Limit maxfiles to liczba plików, które może zawierać wolumin. Systemy plików w systemie Linux odnoszą się do limitu jako *i-wi-wy*. Limit maxfiles dla woluminu Azure NetApp Files jest indeksowany na podstawie rozmiaru (limitu przydziału) woluminu. Maksymalny limit plików dla woluminu zwiększa się lub zmniejsza z szybkością 20 milionów plików na TiB aprowizowanych woluminów. 

Usługa dynamicznie dostosowuje limit maksymalnych plików dla woluminu na podstawie jego aprowizowanych rozmiarów. Na przykład wolumin skonfigurowany początkowo o rozmiarze 1 TiB będzie miał maksymalny limit plików 20 milionów. Kolejne zmiany rozmiaru woluminu spowoduje automatyczne ponowne dostosowanie limitu maxfiles na podstawie następujących reguł: 

|    Rozmiar woluminu (limit przydziału)     |  Automatyczne ponowne dostosowanie limitu maxfiles    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 mln     |
|    > 1 TiB, ale <= 2 TiB    |    40 mln     |
|    > 2 TiB, ale <= 3 TiB    |    60 mln     |
|    > 3 TiB, ale <= 4 TiB    |    80 mln     |
|    > 4 TiB                 |    100 mln    |

Jeśli przydzielono już co najmniej 4 TiB limitu przydziału dla [](#limit_increase) woluminu, możesz zainicjować wniosek o pomoc techniczną w celu zwiększenia maksymalnego limitu plików (i-węzłów) ponad 100 milionów. Dla każdego 100 milionów plików, które zwiększasz (lub ich część), musisz zwiększyć odpowiedni limit przydziału woluminu o 4 TiB.  Jeśli na przykład zwiększysz limit maksymalnych plików ze 100 milionów plików do 200 milionów plików (lub dowolną liczbę między nimi), musisz zwiększyć limit przydziału woluminu z 4 TiB do 8 TiB.

Limit maksymalnych plików można zwiększyć do 500 milionów, jeśli limit przydziału woluminu wynosi co najmniej 20 TiB. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>Zwiększenie limitu żądań <a name="limit_increase"></a> 

Możesz utworzyć wniosek o pomoc techniczną platformy Azure, aby zwiększyć dostosowywane limity z powyższej tabeli. 

Z Azure Portal nawigacyjnej: 

1. Kliknij **pozycję Pomoc i obsługa techniczna.**
2. Kliknij **pozycję + Nowy wniosek o pomoc techniczną.**
3. Na karcie Podstawowe podaj następujące informacje: 
    1. Typ problemu: wybierz **pozycję Limity usługi i subskrypcji (limity przydziału).**
    2. Subskrypcje: wybierz subskrypcję dla zasobu, dla których chcesz zwiększyć limit przydziału.
    3. Typ limitu przydziału: **wybierz pozycję Magazyn: Azure NetApp Files limity przydziału.**
    4. Kliknij **przycisk Dalej: Rozwiązania**.
4. Na karcie Szczegóły:
    1. W polu Opis podaj następujące informacje dotyczące odpowiedniego typu zasobu:

        |  Zasób  |    Zasoby nadrzędne      |    Żądane nowe limity     |    Przyczyna zwiększenia limitu przydziału       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Identyfikator subskrypcji*   |  *Żądano nowego **maksymalnego numeru** konta*    |  *Jaki scenariusz lub przypadek użycia monitował żądanie?*  |
        |  Pula    |  *Identyfikator subskrypcji, identyfikator URI konta usługi NetApp*  |  *Żądany nowy maksymalny **numer puli***   |  *Jaki scenariusz lub przypadek użycia monitował żądanie?*  |
        |  Wolumin  |  *Identyfikator subskrypcji, identyfikator URI konta usługi NetApp, identyfikator URI puli pojemności*   |  *Żądano nowego **maksymalnego numeru woluminu***     |  *Jaki scenariusz lub przypadek użycia monitował żądanie?*  |
        |  Maksymalna liczba plików  |  *Identyfikator subskrypcji, identyfikator URI konta usługi NetApp, identyfikator URI puli pojemności, identyfikator URI woluminu*   |  *Żądana nowa **maksymalna liczba maksymalnych plików***     |  *Jaki scenariusz lub przypadek użycia monitował żądanie?*  |    
        |  Woluminy ochrony danych replikacji między regionami  |  *Identyfikator subskrypcji, identyfikator URI docelowego konta usługi NetApp, identyfikator URI puli pojemności docelowej, identyfikator URI źródłowego konta netApp, identyfikator URI puli pojemności źródłowej, identyfikator URI woluminu źródłowego*   |  *Żądana nowa maksymalna liczba woluminów ochrony danych replikacji **między regionami (woluminy docelowe)** _     |  _What scenariusz lub przypadek użycia monitował żądanie?*  |    

    2. Określ odpowiednią metodę pomocy technicznej i podaj informacje o kontrakcie.

    3. Kliknij **przycisk Dalej: Przeglądanie + tworzenie,** aby utworzyć żądanie. 


## <a name="next-steps"></a>Następne kroki  

- [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model kosztów usługi Azure NetApp Files](azure-netapp-files-cost-model.md)
