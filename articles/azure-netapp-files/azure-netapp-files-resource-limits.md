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
ms.date: 01/29/2021
ms.author: b-juche
ms.openlocfilehash: c82e834c0af3737c1e5ef19c7aa789b94d87f6d8
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095395"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limity zasobów dla usługi Azure NetApp Files

Zrozumienie limitów zasobów Azure NetApp Files ułatwia zarządzanie woluminami.

## <a name="resource-limits"></a>Limity zasobów

W poniższej tabeli opisano limity zasobów dla Azure NetApp Files:

|  Zasób  |  Limit domyślny  |  Regulowane przez żądanie pomocy technicznej  |
|----------------|---------------------|--------------------------------------|
|  Liczba kont NetApp na region platformy Azure na subskrypcję  |  10    |  Tak   |
|  Liczba pul pojemności na konto NetApp   |    25     |   Tak   |
|  Liczba woluminów na subskrypcję   |    500     |   Tak   |
|  Liczba woluminów na pulę pojemności     |    500   |    Tak     |
|  Liczba migawek na wolumin       |    255     |    Nie        |
|  Liczba podsieci delegowanych do Azure NetApp Files (Microsoft. NetApp/Volumes) na platformie Azure Virtual Network    |   1   |    Nie    |
|  Liczba używanych adresów IP w sieci wirtualnej (w tym natychmiastowo sieci wirtualnych komunikacji równorzędnej) z Azure NetApp Files   |    1000   |    Nie   |
|  Minimalny rozmiar puli o pojedynczej pojemności   |  4 TiB     |    Nie  |
|  Maksymalny rozmiar puli o pojedynczej pojemności    |  500 TiB   |   Nie   |
|  Minimalny rozmiar pojedynczego woluminu    |    100 GiB    |    Nie    |
|  Maksymalny rozmiar pojedynczego woluminu     |    100 TiB    |    Nie    |
|  Maksymalny rozmiar pojedynczego pliku     |    16 TiB    |    Nie    |    
|  Maksymalny rozmiar metadanych katalogu w jednym katalogu      |    320 MB    |    Nie    |    
|  Maksymalna liczba plików ([maxfiles](#maxfiles)) na wolumin     |    100 000 000    |    Tak    |    
|  Minimalna przypisana przepływność dla ręcznego woluminu QoS     |    1 MiB/s   |    Nie    |    
|  Maksymalna przypisana przepływność dla ręcznego woluminu QoS     |    4 500 MiB/s    |    Nie    |    
|  Liczba woluminów ochrony danych replikacji między regionami (woluminy docelowe)     |    5    |    Tak    |     

Aby sprawdzić, czy katalog zbliża się do maksymalnego rozmiaru metadanych katalogu (320 MB), zobacz [Jak mogę określić, czy katalog zbliża się do rozmiaru limitu](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size).   

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące zarządzania pojemnością](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Limity maxfiles <a name="maxfiles"></a> 

Woluminy Azure NetApp Files mają limit o nazwie *maxfiles*. Limit maxfiles to liczba plików, które może zawierać wolumin. Limit maxfiles dla woluminu Azure NetApp Files jest indeksowany na podstawie rozmiaru (przydziału) woluminu. Limit maxfiles dla woluminu rośnie lub zmniejsza się o szybkości 20 000 000 plików na TiB rozmiaru woluminu. 

Usługa dynamicznie dostosowuje limit maxfiles dla woluminu na podstawie jego rozmiaru aprowizacji. Na przykład wolumin skonfigurowany początkowo o rozmiarze 1 TiB będzie miał limit maxfiles równy 20 000 000. Kolejne zmiany rozmiaru woluminu spowodują automatyczne ponowne dostosowanie limitu maxfiles w zależności od następujących zasad: 

|    Rozmiar woluminu (przydział)     |  Automatyczne dostosowywanie limitu maxfiles    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 000 000     |
|    > 1 TiB, ale <= 2 TiB    |    40 000 000     |
|    > 2 TiB, ale <= 3 TiB    |    60 000 000     |
|    > 3 TiB, ale <= 4 TiB    |    80 000 000     |
|    > 4 TiB                 |    100 000 000    |

Jeśli przydzielono już co najmniej 4 TiB przydziału dla woluminu, można zainicjować [żądanie obsługi](#limit_increase) , aby zwiększyć limit maxfiles przekraczający 100 000 000. W przypadku każdego 100 000 000 plików, które można zwiększyć (lub ich części), należy zwiększyć przydział odpowiedniego woluminu o 4 TiB.  Na przykład jeśli zwiększono limit maxfiles z plików 100 000 000 do 200 000 000 (lub dowolną liczbę z zakresu), należy zwiększyć przydział woluminu z 4 TiB do 8 TiB.

## <a name="request-limit-increase"></a>Zwiększenie limitu żądań <a name="limit_increase"></a> 

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
        |  Pula    |  *Identyfikator subskrypcji, identyfikator URI konta NetApp*  |  *Żądany nowy numer **puli***   |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Wolumin  |  *Identyfikator subskrypcji, identyfikator URI konta NetApp, identyfikator URI puli pojemności*   |  *Żądany nowy maksymalny numer **woluminu***     |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |
        |  Maxfiles  |  *Identyfikator subskrypcji, identyfikator URI konta NetApp, identyfikator URI puli pojemności, identyfikator URI woluminu*   |  *Żądany nowy maksymalny numer **maxfiles***     |  *Jakiego scenariusza lub przypadku użycia monituje o żądanie?*  |    
        |  Woluminy ochrony danych replikacji między regionami  |  *Identyfikator subskrypcji, identyfikator URI docelowego konta NetApp, identyfikator URI docelowej puli pojemności, identyfikator URI źródła NetApp konta, identyfikator URI puli pojemności źródła, identyfikator URI woluminu źródłowego*   |  * Żądana Nowa Maksymalna liczba **woluminów ochrony danych replikacji między regionami (woluminy docelowe)** _     |  Scenariusz _What lub przypadek użycia monituje o żądanie? *  |    

    2. Określ odpowiednią metodę obsługi i podaj informacje o kontrakcie.

    3. Kliknij przycisk **Dalej: przegląd + Utwórz** , aby utworzyć żądanie. 


## <a name="next-steps"></a>Następne kroki  

- [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Model kosztów usługi Azure NetApp Files](azure-netapp-files-cost-model.md)
