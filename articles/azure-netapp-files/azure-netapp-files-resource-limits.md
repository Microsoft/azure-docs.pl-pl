---
title: Limity zasobów dla Azure NetApp Files | Microsoft Docs
description: Opisuje limity dotyczące zasobów Azure NetApp Files i sposób żądania zwiększenia limitu zasobów.
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
ms.date: 06/08/2020
ms.author: b-juche
ms.openlocfilehash: 8b417559a17dc05a07467a28d37fec9b9a7c12cb
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84553472"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure NetApp Files

Zrozumienie limitów zasobów Azure NetApp Files ułatwia zarządzanie woluminami.

## <a name="resource-limits"></a>Limity zasobów

W poniższej tabeli opisano limity zasobów dla Azure NetApp Files:

|  Zasób  |  Limit domyślny  |  Regulowane przez żądanie pomocy technicznej  |
|----------------|---------------------|--------------------------------------|
|  Liczba kont NetApp na region platformy Azure   |  10    |  Yes   |
|  Liczba pul pojemności na konto NetApp   |    25     |   Yes   |
|  Liczba woluminów na pulę pojemności     |    500   |    Yes     |
|  Liczba migawek na wolumin       |    255     |    Nie        |
|  Liczba podsieci delegowanych do Azure NetApp Files (Microsoft. NetApp/Volumes) na platformie Azure Virtual Network    |   1   |    Nie    |
|  Liczba używanych adresów IP w sieci wirtualnej (w tym natychmiastowo sieci wirtualnych komunikacji równorzędnej) z Azure NetApp Files   |    1000   |    Nie   |
|  Minimalny rozmiar puli o pojedynczej pojemności   |  4 TiB     |    Nie  |
|  Maksymalny rozmiar puli o pojedynczej pojemności    |  500 TiB   |   Nie   |
|  Minimalny rozmiar pojedynczego woluminu    |    100 GiB    |    Nie    |
|  Maksymalny rozmiar pojedynczego woluminu     |    100 TiB    |    Nie    |
|  Maksymalny rozmiar pojedynczego pliku     |    16 TiB    |    Nie    |    
|  Maksymalny rozmiar metadanych katalogu w jednym katalogu      |    320 MB    |    Nie    |    
|  Maksymalna liczba plików ([maxfiles](#maxfiles)) na wolumin     |    100 000 000    |    Yes    |    

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące zarządzania pojemnością](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Limity maxfiles<a name="maxfiles"></a> 

Woluminy Azure NetApp Files mają limit o nazwie *maxfiles*. Limit maxfiles to liczba plików, które może zawierać wolumin. Limit maxfiles dla woluminu Azure NetApp Files jest indeksowany na podstawie rozmiaru (przydziału) woluminu. Limit maxfiles dla woluminu rośnie lub zmniejsza się o szybkości 20 000 000 plików na TiB rozmiaru woluminu. 

Usługa dynamicznie dostosowuje limit maxfiles dla woluminu na podstawie jego rozmiaru aprowizacji. Na przykład wolumin skonfigurowany początkowo o rozmiarze 1 TiB będzie miał limit maxfiles równy 20 000 000. Kolejne zmiany rozmiaru woluminu spowodują automatyczne ponowne dostosowanie limitu maxfiles w zależności od następujących zasad: 

|    Rozmiar woluminu (przydział)     |  Automatyczne dostosowywanie limitu maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 000 000     |
|    >= 1 TiB, ale < 2 TiB    |    40 000 000     |
|    >= 2 TiB, ale < 3 TiB    |    60 000 000     |
|    >= 3 TiB, ale < 4 TiB    |    80 000 000     |
|    >= 4 TiB                |    100 000 000    |

W przypadku dowolnego rozmiaru woluminu można zainicjować [żądanie obsługi](#limit_increase) , aby zwiększyć limit maxfilesy przekraczający 100 000 000.

## <a name="request-limit-increase"></a>Zwiększenie limitu żądań<a name="limit_increase"></a> 

Możesz utworzyć żądanie pomocy technicznej platformy Azure, aby zwiększyć regulowane limity z powyższej tabeli. 

Ze płaszczyzny nawigacyjnej Azure Portal: 

1. Kliknij pozycję **Pomoc i obsługa techniczna**.
2. Kliknij pozycję **+ nowe żądanie obsługi**.
3. Na karcie podstawowe podaj następujące informacje: 
    1. Typ problemu: Wybierz **limity usługi i subskrypcji (przydziały)**.
    2. Subskrypcje: wybierz subskrypcję zasobu, dla którego chcesz zwiększyć przydział.
    3. Typ przydziału: wybierz pozycję **Magazyn: limity Azure NetApp Files**.
    4. Kliknij przycisk **Dalej: rozwiązania**.
4. Na karcie Szczegóły:
    1. W polu Opis podaj następujące informacje dotyczące odpowiedniego typu zasobu:

        |  Zasób  |    Zasoby nadrzędne      |    Żądane nowe limity     |    Przyczyna zwiększenia limitu przydziału       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Identyfikator subskrypcji*   |  *Żądany nowy maksymalny numer **konta***    |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Pula    |  *Identyfikator subskrypcji, identyfikator URI konta*  |  *Żądany nowy numer **puli***   |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Wolumin  |  *Identyfikator subskrypcji, identyfikator URI konta, identyfikator URI puli*   |  *Żądany nowy maksymalny numer **woluminu***     |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Maxfiles  |  *Identyfikator subskrypcji, identyfikator URI konta, identyfikator URI puli, identyfikator URI woluminu*   |  *Żądany nowy maksymalny numer **maxfiles***     |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |    

    2. Określ odpowiednią metodę obsługi i podaj informacje o kontrakcie.

    3. Kliknij przycisk **Dalej: przegląd + Utwórz** , aby utworzyć żądanie. 


## <a name="next-steps"></a>Następne kroki  

- [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model kosztów usługi Azure NetApp Files](azure-netapp-files-cost-model.md)
