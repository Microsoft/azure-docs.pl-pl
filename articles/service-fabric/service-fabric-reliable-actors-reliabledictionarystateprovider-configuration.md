---
title: Zmień ustawienia ReliableDictionaryActorStateProvider
description: Dowiedz się więcej na temat konfigurowania Service Fabric stanowych aktorów typu ReliableDictionaryActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: fbd6f7cd3ade753c659464522408aa715cce48f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609744"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>Konfigurowanie Reliable Actors--ReliableDictionaryActorStateProvider
Domyślną konfigurację ReliableDictionaryActorStateProvider można zmodyfikować, zmieniając plik settings.xml wygenerowany w katalogu głównym pakietu programu Visual Studio w folderze config dla określonego aktora.

Środowisko uruchomieniowe Azure Service Fabric wyszukuje wstępnie zdefiniowane nazwy sekcji w pliku settings.xml i zużywa wartości konfiguracyjne podczas tworzenia podstawowych składników środowiska uruchomieniowego.

> [!NOTE]
> Nie usuwaj ani **nie** Modyfikuj nazw sekcji następujących konfiguracji w pliku settings.xml, który jest generowany w rozwiązaniu programu Visual Studio.
> 
> 

Istnieją również ustawienia globalne, które mają wpływ na konfigurację ReliableDictionaryActorStateProvider.

## <a name="global-configuration"></a>Konfiguracja globalna
Konfiguracja globalna została określona w manifeście klastra dla klastra w sekcji KtlLogger. Umożliwia ona konfigurację udostępnionej lokalizacji i rozmiaru dziennika oraz globalne limity pamięci używane przez rejestratora. Należy pamiętać, że zmiany w manifeście klastra mają wpływ na wszystkie usługi korzystające z ReliableDictionaryActorStateProvider i niezawodnych usług stanowych.

Manifest klastra to pojedynczy plik XML, który zawiera ustawienia i konfiguracje, które mają zastosowanie do wszystkich węzłów i usług w klastrze. Plik jest zwykle nazywany ClusterManifest.xml. Manifest klastra dla klastra można wyświetlić za pomocą polecenia Get-ServiceFabricClusterManifest PowerShell.

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtach |8388608 |Minimalna liczba KB do przydzielenia w trybie jądra dla puli pamięci buforu zapisu rejestratora. Ta Pula pamięci jest używana do buforowania informacji o stanie przed zapisaniem na dysku. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtach |Bez ograniczeń |Maksymalny rozmiar, do którego można zwiększyć pulę pamięci buforu zapisu rejestratora. |
| SharedLogId |GUID |"" |Określa unikatowy identyfikator GUID, który będzie używany do identyfikowania domyślnego udostępnionego pliku dziennika używanego przez wszystkie niezawodne usługi we wszystkich węzłach w klastrze, które nie określają SharedLogId w konfiguracji specyficznej dla usługi. Jeśli SharedLogId jest określony, należy również określić SharedLogPath. |
| SharedLogPath |W pełni kwalifikowana nazwa ścieżki |"" |Określa w pełni kwalifikowaną ścieżkę, w której udostępniony plik dziennika używany przez wszystkie niezawodne usługi we wszystkich węzłach w klastrze, który nie określa SharedLogPath w swojej konfiguracji specyficznej dla usługi. Jeśli jednak SharedLogPath jest określony, należy również określić SharedLogId. |
| SharedLogSizeInMB |Megabajtach |8192 |Określa liczbę MB miejsca na dysku do alokacji statycznej dla dziennika udostępnionego. Wartość musi być równa 2048 lub większa. |

### <a name="sample-cluster-manifest-section"></a>Przykładowa sekcja manifestu klastra
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Uwagi
Rejestrator ma globalną pulę pamięci przydzieloną z niestronicowanej pamięci jądra, która jest dostępna dla wszystkich niezawodnych usług w węźle do buforowania danych stanu przed zapisaniem w dedykowanym dzienniku skojarzonym z niezawodną repliką usługi. Rozmiar puli jest kontrolowany przez ustawienia WriteBufferMemoryPoolMinimumInKB i WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB określa początkowy rozmiar tej puli pamięci i najniższy rozmiar, do którego może zostać zmniejszony Pula pamięci. WriteBufferMemoryPoolMaximumInKB jest największym rozmiarem, do którego może rosnąć Pula pamięci. Każda otwarta niezawodna replika usługi może zwiększyć rozmiar puli pamięci przez system o określonej wielkości do WriteBufferMemoryPoolMaximumInKB. Jeśli ilość pamięci z puli pamięci jest większa niż dostępna, żądania dotyczące pamięci zostaną opóźnione do momentu udostępnienia pamięci. W związku z tym, jeśli Pula pamięci buforu zapisu jest za mała dla konkretnej konfiguracji, może to mieć wpływ na wydajność.

Ustawienia SharedLogId i SharedLogPath są zawsze używane razem do definiowania identyfikatora GUID i lokalizacji dla domyślnego dziennika udostępnionego dla wszystkich węzłów w klastrze. Domyślny dziennik udostępniony jest używany dla wszystkich niezawodnych usług, które nie określają ustawień w settings.xml dla określonej usługi. Aby zapewnić najlepszą wydajność, udostępnione pliki dzienników powinny być umieszczane na dyskach, które są używane wyłącznie dla udostępnionego pliku dziennika, aby zmniejszyć rywalizację.

SharedLogSizeInMB określa ilość miejsca na dysku do wstępnego przydzielenia dla domyślnego dziennika udostępnionego we wszystkich węzłach.  Nie trzeba określać SharedLogId i SharedLogPath, aby można było określić SharedLogSizeInMB.

## <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Konfiguracje zabezpieczeń replikatora służą do zabezpieczenia kanału komunikacyjnego, który jest używany podczas replikacji. Oznacza to, że usługi nie widzą żadnego ruchu związanego z replikacją, zapewniając również bezpieczeństwo danych, które są wykonywane o wysokiej dostępności.
Domyślnie pusta sekcja konfiguracji zabezpieczeń uniemożliwia zabezpieczenia replikacji.

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą mieć format PEM. Aby dowiedzieć się więcej na temat lokalizowania i konfigurowania certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nazwa sekcji
&lt;ServiceReplicatorSecurityConfig aktora &gt;

## <a name="replicator-configuration"></a>Konfiguracja replikatora
Konfiguracje replikatora służą do konfigurowania replikatora, który jest odpowiedzialny za zapewnianie niezawodnego stanu dostawcy stanu aktora przez replikowanie i utrwalanie stanu lokalnie.
Konfiguracja domyślna jest generowana przez szablon programu Visual Studio i powinna być wystarczająca. W tej sekcji omówiono dodatkowe konfiguracje, które są dostępne w celu dostrojenia replikatora.

### <a name="section-name"></a>Nazwa sekcji
&lt;ServiceReplicatorConfig aktora &gt;

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0,015 |Okres, przez który Replikator w dodatkowej chwili czeka po odebraniu operacji przed wysłaniem potwierdzenia do elementu podstawowego. Wszystkie inne potwierdzenia do wysłania dla operacji przetworzonych w ramach tego interwału są wysyłane jako jedna odpowiedź. |
| ReplicatorEndpoint |Nie dotyczy |Brak domyślnego parametru--Required |Adres IP i port, które będą używane przez Replikator podstawowy/pomocniczy do komunikowania się z innymi replikatorami w zestawie replik. Powinno to odwoływać się do punktu końcowego zasobu TCP w manifeście usługi. Zapoznaj się z informacjami o [zasobach manifestu usługi](service-fabric-service-manifest-resources.md) , aby dowiedzieć się więcej na temat definiowania zasobów punktu końcowego w manifeście usługi. |
| MaxReplicationMessageSize |Bajty |50 MB |Maksymalny rozmiar danych replikacji, które mogą być przesyłane w jednej wiadomości. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |8192 |Maksymalna liczba operacji w kolejce głównej. Operacja jest zwalniana, gdy podstawowy Replikator otrzyma potwierdzenie ze wszystkich dodatkowych replik. Ta wartość musi być większa niż 64 i potęgą 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |16384 |Maksymalna liczba operacji w kolejce pomocniczej. Operacja zostanie zwolniona po przeprowadzeniu wysokiej dostępności stanu przez trwałość. Ta wartość musi być większa niż 64 i potęgą 2. |
| CheckpointThresholdInMB |MB |200 |Ilość miejsca w pliku dziennika, po którym stan jest tworzony w punkcie kontrolnym. |
| MaxRecordSizeInKB |KB |1024 |Największy rozmiar rekordu, który Replikator może zapisać w dzienniku. Ta wartość musi być wielokrotnością 4 i większa niż 16. |
| OptimizeLogForLowerDiskUsage |Wartość logiczna |true |Jeśli wartość jest równa true, dziennik jest skonfigurowany tak, aby dedykowany plik dziennika repliki został utworzony przy użyciu pliku rozrzedzonego NTFS. Zmniejsza to rzeczywiste użycie miejsca na dysku dla pliku. W przypadku wartości false plik jest tworzony ze stałymi alokacjami, co zapewnia najlepszą wydajność zapisu. |
| SharedLogId |guid |"" |Określa unikatowy identyfikator GUID, który będzie używany do identyfikowania udostępnionego pliku dziennika używanego z tą repliką. Zazwyczaj usługi nie powinny używać tego ustawienia. Jeśli jednak SharedLogId jest określony, należy również określić SharedLogPath. |
| SharedLogPath |W pełni kwalifikowana nazwa ścieżki |"" |Określa w pełni kwalifikowaną ścieżkę, w której zostanie utworzony udostępniony plik dziennika dla tej repliki. Zazwyczaj usługi nie powinny używać tego ustawienia. Jeśli jednak SharedLogPath jest określony, należy również określić SharedLogId. |

## <a name="sample-configuration-file"></a>Przykładowy plik konfiguracji
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>Uwagi
Parametr BatchAcknowledgementInterval steruje opóźnieniem replikacji. Wartość "0" skutkuje najniższym możliwym opóźnieniem, kosztem przepływności (w przypadku, gdy więcej komunikatów potwierdzających musi być wysłanych i przetwarzanych, z których każda zawiera mniejszą liczbę potwierdzeń).
Im większa wartość BatchAcknowledgementInterval, tym wyższa ogólna przepływność replikacji, koszt opóźnienia operacji. Jest to bezpośrednio tłumaczone na opóźnienie zatwierdzeń transakcji.

Parametr CheckpointThresholdInMB Steruje ilością miejsca na dysku, która może być używana przez Replikator do przechowywania informacji o stanie w dedykowanym pliku dziennika repliki. Zwiększenie tej wartości niż domyślna może spowodować szybsze ponowne skonfigurowanie, gdy Nowa replika zostanie dodana do zestawu. Jest to spowodowane częściowym transferem stanu, który ma miejsce z powodu dostępności większej historii operacji w dzienniku. Może to spowodować zwiększenie czasu odzyskiwania repliki po awarii.

W przypadku ustawienia OptimizeForLowerDiskUsage na wartość true miejsce w pliku dziennika zostanie nadmiernie zainicjowane, aby aktywne repliki mogły przechowywać więcej informacji o stanie w plikach dziennika, podczas gdy nieaktywne repliki będą używać mniej miejsca na dysku. Dzięki temu można hostować więcej replik w węźle. W przypadku ustawienia OptimizeForLowerDiskUsage na wartość false informacje o stanie są szybciej zapisywane w plikach dziennika.

Ustawienie MaxRecordSizeInKB określa maksymalny rozmiar rekordu, który może być zapisany przez replikatora w pliku dziennika. W większości przypadków rozmiar domyślnego rekordu 1024 KB jest optymalny. Jeśli jednak usługa powoduje, że większe elementy danych będą częścią informacji o stanie, może być konieczne zwiększenie tej wartości. W przypadku MaxRecordSizeInKB mniejszym niż 1024 jest mało korzyści, ponieważ mniejsze rekordy używają tylko miejsca wymaganego dla mniejszego rekordu. Oczekujemy, że ta wartość powinna być zmieniana tylko w rzadkich przypadkach.

Ustawienia SharedLogId i SharedLogPath są zawsze używane razem, aby usługa korzystała z oddzielnego dziennika udostępnionego z domyślnego dziennika udostępnionego węzła. W celu uzyskania najlepszej wydajności, jak wiele usług, jak to możliwe, należy określić ten sam dziennik udostępniony. Udostępnione pliki dzienników powinny być umieszczane na dyskach, które są używane wyłącznie dla udostępnionego pliku dziennika, aby zmniejszyć rywalizację o ruchy. Oczekujemy, że te wartości byłyby zmieniane tylko w rzadkich przypadkach.

