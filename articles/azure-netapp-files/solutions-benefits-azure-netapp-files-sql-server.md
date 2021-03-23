---
title: Zalety korzystania z Azure NetApp Files wdrożenia SQL Server | Microsoft Docs
description: Przedstawia szczegółowe informacje na temat wydajności analizy kosztów na potrzeby wdrażania SQL Server przy użyciu Azure NetApp Files.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863908"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Zalety korzystania z Azure NetApp Files wdrożenia SQL Server

Azure NetApp Files redukuje SQL Server łączny koszt posiadania (TCO) w porównaniu do magazynu blokowego.  W przypadku magazynu blokowego maszyny wirtualne mają limity dla operacji we/wy i przepustowości na dysku, ale tylko limity przepustowości sieci są stosowane do Azure NetApp Files tylko w tym systemie.  Innymi słowy, w Azure NetApp Files nie są stosowane limity we/wy na poziomie maszyny wirtualnej. Bez tych limitów operacji we/wy SQL Server działające na mniejszych maszynach wirtualnych podłączonych do Azure NetApp Files mogą działać oraz SQL Server działające na znacznie większych maszynach wirtualnych. Ustalanie wielkości wystąpień w dół w taki sposób, aby zmniejszyć koszt obliczeń do 25% poprzedniego znacznika cen.  *Koszt obliczeń można zmniejszyć za pomocą Azure NetApp Files.*  

Koszty obliczeniowe są jednak małe w porównaniu z kosztami licencji SQL Server.  [Licencjonowanie](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) Microsoft SQL Server jest powiązane z fizyczną liczbą rdzeni. W związku z tym zmniejszenie rozmiaru wystąpienia wprowadza jeszcze większe oszczędności w zakresie licencjonowania oprogramowania. *Koszty licencji na oprogramowanie można zmniejszyć za pomocą Azure NetApp Files.*

Koszt samego magazynu jest zmienny w zależności od rzeczywistego rozmiaru bazy danych. Niezależnie od wybranego magazynu pojemność ma koszt, niezależnie od tego, czy jest to dysk zarządzany, czy udział plików.  W miarę zwiększania rozmiaru bazy danych i zwiększania kosztów magazynów przyczynia się do wzrostu całkowitego kosztu.  W związku z tym potwierdzenie jest korygowane w następujący sposób: *można zmniejszyć koszty wdrażania SQL Server z Azure NetApp Files.* 

W tym artykule przedstawiono szczegółową analizę kosztów i korzyści z używania Azure NetApp Files wdrażania SQL Server. Tylko mniejsze wystąpienia mają wystarczające użycie procesora, aby baza danych mogła działać tylko z blokiem o większych wystąpieniach, *a w wielu przypadkach mniejsze wystąpienia są jeszcze bardziej wydajne niż ich większe, oparte na dyskach, z powodu Azure NetApp Files.* 

## <a name="detailed-cost-analysis"></a>Szczegółowa analiza kosztów 

Dwa zestawy grafiki w tej sekcji przedstawiają przykład TCO.  Wybrano liczbę i typ dysków zarządzanych, poziom usług Azure NetApp Files i pojemność dla każdego scenariusza, aby osiągnąć najlepszą cenę wydajności.  Każda grafika składa się z zgrupowanych maszyn (D16 z Azure NetApp Files, w porównaniu do D64 z dyskiem zarządzanym na przykład), a ceny są podzielone na poszczególne typy maszyn.  

Pierwszy zestaw grafiki pokazuje całkowity koszt rozwiązania przy użyciu rozmiaru bazy danych 1 TiB, porównując D16s_v3 z D64, D8 do D32 i d4 z D16. Przewidywane operacje we/wy dla każdej konfiguracji są wskazywane przez zieloną lub żółtą linię i odpowiadają osi Y z prawej strony.

[![Ilustracja pokazująca ogólny koszt rozwiązania przy użyciu rozmiaru bazy danych 1 TIB. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


Drugi zestaw grafiki pokazuje całkowity koszt przy użyciu bazy danych 50-TiB. Porównania są w inny sposób takie same — D16 w porównaniu z Azure NetApp Files a D64 z blokiem przez przykład. 

[![Ilustracja pokazująca całkowity koszt przy użyciu rozmiaru bazy danych 50 TIB. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Wydajność i wiele z nich  

Aby zapewnić znaczącą redukcję kosztów, należy uzyskać dużą wydajność — największe wystąpienia w ogólnej obsłudze usługi Azure Inventory 80 000 operacji we/wy na sekundę. Pojedynczy wolumin Azure NetApp Files może osiągnąć 80 000 operacji we/wy bazy danych, a wystąpienia takie jak D16 mogą korzystać z tego samego. D16, zwykle do 25 600 operacji we/wy dysku, wynosi 25% rozmiaru D64.  D64s_v3 jest zdolny do 80 000 operacji we/wy na sekundę, a w związku z tym przedstawia doskonałego górnego punktu porównania.

D16s_v3 może zwiększyć ilość woluminu Azure NetApp Files do 80 000 operacji we/wy bazy danych. Zgodnie z opisem w narzędziu test porównawczy usługi SQL Storage (SSB) wystąpienie D16 osiągnęło obciążenie 125% większe niż dostępne dla dysku z wystąpienia D64.  Aby uzyskać szczegółowe informacje na temat tego narzędzia, zobacz sekcję [Narzędzie testowania SSB](#ssb-testing-tool) .

W przypadku korzystania z 1-TiBego rozmiaru zestawu roboczego i SQL Server odczytu o 80%, wydajności dla większości wystąpień w klasie wystąpienia D Większość, nie wszystkie, ponieważ same wystąpienia D2 i D64 zostały wykluczone z testów. Dawniej został pozostawiony, ponieważ nie obsługuje on przyspieszonej sieci, a tym, że jest to punkt porównania. Zapoznaj się z poniższym wykresem, aby zrozumieć odpowiednio limity D4s_v3, D8s_v3, D16s_v3 i D32s_v3.  Testy magazynu dysków zarządzanych nie są wyświetlane na wykresie. Wartości porównania są rysowane bezpośrednio z [tabeli limitów maszyn wirtualnych platformy Azure](../virtual-machines/dv3-dsv3-series.md) dla typu wystąpienia klasy D.

Dzięki Azure NetApp Files każde wystąpienie w klasie D może spełnić lub przekroczyć możliwości wydajności dysków dwa razy większe.  *Koszty licencji na oprogramowanie można znacznie zmniejszyć przy użyciu Azure NetApp Files.*  

* Użycie procesora D4 o 75% zostało dopasowane do możliwości dysku D16.  
    * D16 jest stawką ograniczoną na 25 600 operacji we/wy na sekundę.  
* D8 na 75% użycia procesora CPU dopasowane do możliwości dysku D32.  
    * D32 jest stawką ograniczoną na 51 200 operacji we/wy na sekundę.  
* D16 na 55% użycia procesora CPU dopasowane do możliwości dysku D64.  
    * D64 jest stawką ograniczoną na 80 000 operacji we/wy na sekundę.  
* Użycie procesora CPU przez D32 o 15% spełnia również możliwości dysków D64.  
    * D64, jak wspomniano powyżej, ma stawkę ograniczoną na 80 000 operacji we/wy na sekundę.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>Test limitów procesora S3B — wydajność i moc obliczeniowa

Na poniższym diagramie zestawiono test ograniczeń S3B procesora CPU:

![Diagram przedstawiający średnią wartość procentową procesora CPU dla jednego wystąpienia SQL Server przez Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

Skalowalność jest tylko częścią wątku. Druga część to opóźnienie.  W przypadku mniejszych maszyn wirtualnych można korzystać z znacznie większej liczby operacji we/wy, ale jest to kolejna czynność z niską częstotliwością jednocyfrową, jak pokazano poniżej.  

* Liczba operacji we/wy na sekundę sprawiły 26 000 w stosunku do Azure NetApp Files przy opóźnieniu 2,3-ms.  
* Liczba operacji we/wy D8 sprawiły 51 000 w stosunku do Azure NetApp Files przy opóźnieniu 2,0-MS.  
* Liczba operacji we/wy D16 sprawiły 88 000 w stosunku do Azure NetApp Files przy opóźnieniu 2,8-MS.
* Liczba operacji we/wy D32 sprawiły 80 000 w stosunku do Azure NetApp Files przy opóźnieniu 2,4-MS.  

### <a name="s3b-per-instance-type-latency-results"></a>Wyniki opóźnienia typu S3B na wystąpienie

Na poniższym diagramie przedstawiono opóźnienie dla SQL Server pojedynczego wystąpienia Azure NetApp Files:

![Diagram przedstawiający opóźnienie dla SQL Server jednego wystąpienia Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>Narzędzie testowania SSB 
 
Narzędzie Test porównawczy [TPC-E](http://www.tpc.org/tpce/) , poprzez projektowanie, naciska na *obliczenia* , a nie *Magazyn*. Wyniki testów przedstawione w tej sekcji są oparte na narzędziu do testowania obciążeniowego o nazwie SQL Storage test porównawczy (SSB).  Test porównawczy magazynu SQL Server może zapewnić dużą skalę wykonywania kodu SQL względem bazy danych SQL Server, aby symulować obciążenie OLTP, podobnie jak [Narzędzie Test porównawczy SLOB2 firmy Oracle](https://kevinclosson.net/slob/). 

Narzędzie SSB generuje obciążenia opartego na ZAZNACZeniu i aktualizacji, które wystawia te instrukcje bezpośrednio do bazy danych SQL Server działającej na maszynie wirtualnej platformy Azure.  W przypadku tego projektu obciążenia SSB są rozliczone od 1 do 100 SQL Server użytkowników, a 10 lub 12 punktów pośrednich na 15 minut na liczbę użytkowników.  Wszystkie metryki wydajności z tych przebiegów były z punktu widzenia monitora wydajności, aby powtarzalność SSB działała trzy razy na każdy scenariusz. 

Same testy zostały skonfigurowane jako 80% SELECT i 20% UPDATE, więc 90% Odczyt losowy.  Baza danych utworzona przez SSB ma rozmiar 1000 GB. Składa się z 15 tabel użytkowników i 9 000 000 wierszy na tabelę użytkownika i 8192 bajtów na wiersz. 

Test porównawczy SSB to narzędzie Open Source.  Jest on dostępny bezpłatnie na [stronie usługi SQL Storage](https://github.com/NetApp/SQL_Storage_Benchmark.git)w serwisie GitHub.  


## <a name="in-summary"></a>Podsumowanie  

Dzięki Azure NetApp Files można zwiększyć wydajność programu SQL Server, jednocześnie zmniejszając całkowity koszt posiadania. 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie woluminu SMB dla usługi Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Architektury rozwiązań używające Azure NetApp Files — SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

