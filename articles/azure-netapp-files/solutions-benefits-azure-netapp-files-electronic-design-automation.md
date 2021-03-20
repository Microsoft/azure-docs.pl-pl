---
title: Zalety używania Azure NetApp Files do automatyzacji projektowania elektronicznego | Microsoft Docs
description: Wyjaśnia rozwiązanie, Azure NetApp Files zapewnia zaspokajanie potrzeb branży projektowania półprzewodników i mikroukładów. Prezentuje scenariusze testów korzystające ze standardowego testu porównawczego dla automatyzacji projektowania elektronicznego (EDA) przy użyciu Azure NetApp Files.
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
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82160157"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>Zalety korzystania z usługi Azure NetApp Files na potrzeby automatyzacji projektowania elektroniki

Czas wprowadzenia na rynek (TTM) to krytyczne zagadnienie dla branży projektowania półprzewodnikowego i mikroukładu. Branża ma wysoką przepustowość i małe opóźnienia związane z magazynem. W tym artykule wyjaśniono Azure NetApp Files rozwiązania, które zapewnia spełnienie wymagań branżowych. Prezentuje scenariusze testów korzystające ze standardowego testu porównawczego dla automatyzacji projektowania elektronicznego (EDA) przy użyciu Azure NetApp Files. 

## <a name="test-scenario-configurations"></a>Konfiguracje scenariusza testu

Testy obejmują trzy scenariusze z następującymi konfiguracjami. 

|    Scenariusz    |    Woluminy    |    Klienci<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    Jeden         |    1          |    1                           |
|    Dwa         |    6          |    24                          |
|    Trzy       |    12         |    24                          |

Pierwszy scenariusz dotyczy tego, jak daleko może być napędzany pojedynczy wolumin.  

Drugi i trzeci scenariusz szacują limity pojedynczego punktu końcowego Azure NetApp Files. Badają potencjalne korzyści wynikające z górnych limitów operacji we/wy i opóźnień.

## <a name="test-scenario-results"></a>Wyniki scenariusza testu

Poniższa tabela zawiera podsumowanie wyników scenariuszy testowych.

|    Scenariusz       |    Współczynnik operacji we/wy<br>  o 2 MS     |    Współczynnik operacji we/wy<br>  na brzegu     |    Przepływność<br>  o 2 MS     |    Przepływność<br>  na brzegu     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 wolumin       |    39 601                 |    49 502                      |    692 MiB/s                 |    866 MiB/s                      |
|    6 woluminów      |    255 613                |    317 000                     |    4 577 MiB/s               |    5 568 MiB/s                    |
|    12 woluminów     |    256 612                |    319 196                     |    4 577 MiB/s               |    5 709 MiB/s                    |

Scenariusz jednowoluminowy reprezentuje podstawową konfigurację aplikacji. Jest to scenariusz bazowy dla scenariuszy testowania kolejnych testów.  

Scenariusz sześciu woluminów przedstawia wzrost liniowy (600%) względem obciążenia pojedynczego woluminu.  Wszystkie woluminy w ramach jednej sieci wirtualnej są dostępne za pośrednictwem jednego adresu IP.  

Scenariusz 12-woluminowy przedstawia ogólny spadek opóźnienia w porównaniu z scenariuszem sześciu woluminów. Jednak nie ma odpowiedniego wzrostu w osiągalnej przepływności.   

Poniższy wykres ilustruje opóźnienia i szybkość operacji dla obciążenia EDA na Azure NetApp Files.  

![Opóźnienie i szybkość operacji dla obciążenia EDA na Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

Poniższy wykres ilustruje opóźnienia i przepływność obciążeń EDA na Azure NetApp Files.  

![Opóźnienie i przepływność dla obciążenia EDA na Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>Układ scenariuszy testowych 

Poniższa tabela zawiera podsumowanie układu scenariuszy testowych.

|    Scenariusz testu     |    Łączna liczba katalogów     |    Łączna liczba plików     |
|----------------------|------------------------------------|------------------------------|
|    1 wolumin          |    88 000                          |    880 000                   |
|    6 woluminów         |    568 000                         |    5 680 000                 |
|    12 woluminów        |    568 000                         |    5 680 000                 |

Pełne obciążenie jest kombinacją współbieżnie uruchomionych funkcjonalnych i fizycznych faz. Reprezentuje typowy przepływ z jednego zestawu narzędzi EDA do innego.   

Faza funkcjonalna składa się z wstępnych specyfikacji i projektu logicznego. Faza fizyczna jest wykonywana, gdy projekt logiczny jest konwertowany na układ fizyczny. Podczas fazy wylogowania i przeprowadzenia taśmy testy końcowe są wykonywane, a projekt jest dostarczany do odlewu dla produkcji.  

Faza funkcjonalna obejmuje kombinację sekwencyjnych i losowych operacji we/wy odczytu i zapisu. Faza funkcjonalna to intensywność metadanych, taka jak pliki i wywołania dostępu. Mimo że operacje metadanych są skutecznie bez rozmiaru, operacje odczytu i zapisu mieszczą się w przedziale od 1 K do 16 K. Większość odczytów należy do zakresu od 4 do 16 K.  Większość zapisów ma 4 K lub mniej. Faza fizyczna składa się z operacji sekwencyjnego odczytu i zapisu w całości, z mieszaniną 32 K i 64 K.  

Na wykresach powyżej większość przepływności pochodzi z kolejnej fazy fizycznej obciążenia. We/wy pochodzą z fazy działania z małą liczbą losową i intensywną. Obie fazy są wykonywane równolegle. 

Podsumowując, można sparować usługę Azure COMPUTE with Azure NetApp Files for EDA, aby uzyskać skalowalną przepustowość. 

## <a name="next-steps"></a>Następne kroki

- [Architektury rozwiązań używające usługi Azure NetApp Files](azure-netapp-files-solution-architectures.md)
