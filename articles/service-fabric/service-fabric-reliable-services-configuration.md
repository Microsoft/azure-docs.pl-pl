---
title: Konfigurowanie usługi Azure Service Fabric Reliable Services
description: Dowiedz się więcej o konfigurowaniu stanowych Reliable Services w aplikacji Service Fabric platformy Azure globalnie i dla jednej usługi.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.custom: devx-track-csharp
ms.openlocfilehash: cda0a9f988afae58a60bff051885a5eec8afe434
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023588"
---
# <a name="configure-stateful-reliable-services"></a>Konfigurowanie niezawodnych usług stanowych
Istnieją dwa zestawy ustawień konfiguracji dla niezawodnych usług. Jeden zestaw jest globalny dla wszystkich niezawodnych usług w klastrze, podczas gdy drugi zestaw jest specyficzny dla konkretnej niezawodnej usługi.

## <a name="global-configuration"></a>Konfiguracja globalna
Globalna konfiguracja niezawodnej usługi jest określona w manifeście klastra dla klastra w sekcji KtlLogger. Umożliwia ona konfigurację udostępnionej lokalizacji i rozmiaru dziennika oraz globalne limity pamięci używane przez rejestratora. Manifest klastra to pojedynczy plik XML, który zawiera ustawienia i konfiguracje, które mają zastosowanie do wszystkich węzłów i usług w klastrze. Plik jest zwykle nazywany ClusterManifest.xml. Manifest klastra dla klastra można wyświetlić za pomocą polecenia Get-ServiceFabricClusterManifest PowerShell.

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |Kilobajtach |8388608 |Minimalna liczba KB do przydzielenia w trybie jądra dla puli pamięci buforu zapisu rejestratora. Ta Pula pamięci jest używana do buforowania informacji o stanie przed zapisaniem na dysku. |
| WriteBufferMemoryPoolMaximumInKB |Kilobajtach |Bez ograniczeń |Maksymalny rozmiar, do którego można zwiększyć pulę pamięci buforu zapisu rejestratora. |
| SharedLogId |GUID |"" |Określa unikatowy identyfikator GUID, który będzie używany do identyfikowania domyślnego udostępnionego pliku dziennika używanego przez wszystkie niezawodne usługi we wszystkich węzłach w klastrze, które nie określają SharedLogId w konfiguracji specyficznej dla usługi. Jeśli SharedLogId jest określony, należy również określić SharedLogPath. |
| SharedLogPath |W pełni kwalifikowana nazwa ścieżki |"" |Określa w pełni kwalifikowaną ścieżkę, w której udostępniony plik dziennika używany przez wszystkie niezawodne usługi we wszystkich węzłach w klastrze, który nie określa SharedLogPath w swojej konfiguracji specyficznej dla usługi. Jeśli jednak SharedLogPath jest określony, należy również określić SharedLogId. |
| SharedLogSizeInMB |Megabajtach |8192 |Określa liczbę MB miejsca na dysku do alokacji statycznej dla dziennika udostępnionego. Wartość musi być równa 2048 lub większa. |

W poniższym przykładzie dla usługi Azure ARM lub lokalnego szablonu JSON pokazano, jak zmienić udostępniony dziennik transakcji, który zostanie utworzony w celu przywrócenia wszelkich niezawodnych kolekcji dla usług stanowych.

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="sample-local-developer-cluster-manifest-section"></a>Przykładowa lokalna sekcja manifestu lokalnego klastra
Jeśli chcesz zmienić to w lokalnym środowisku programistycznym, musisz edytować plik clustermanifest.xml lokalnego.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Uwagi
Rejestrator ma globalną pulę pamięci przydzieloną z niestronicowanej pamięci jądra, która jest dostępna dla wszystkich niezawodnych usług w węźle do buforowania danych stanu przed zapisaniem w dedykowanym dzienniku skojarzonym z niezawodną repliką usługi. Rozmiar puli jest kontrolowany przez ustawienia WriteBufferMemoryPoolMinimumInKB i WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB określa początkowy rozmiar tej puli pamięci i najniższy rozmiar, do którego może zostać zmniejszony Pula pamięci. WriteBufferMemoryPoolMaximumInKB jest największym rozmiarem, do którego może rosnąć Pula pamięci. Każda otwarta niezawodna replika usługi może zwiększyć rozmiar puli pamięci przez system o określonej wielkości do WriteBufferMemoryPoolMaximumInKB. Jeśli ilość pamięci z puli pamięci jest większa niż dostępna, żądania dotyczące pamięci zostaną opóźnione do momentu udostępnienia pamięci. W związku z tym, jeśli Pula pamięci buforu zapisu jest za mała dla konkretnej konfiguracji, może to mieć wpływ na wydajność.

Ustawienia SharedLogId i SharedLogPath są zawsze używane razem do definiowania identyfikatora GUID i lokalizacji dla domyślnego dziennika udostępnionego dla wszystkich węzłów w klastrze. Domyślny dziennik udostępniony jest używany dla wszystkich niezawodnych usług, które nie określają ustawień w settings.xml dla określonej usługi. Aby zapewnić najlepszą wydajność, udostępnione pliki dzienników powinny być umieszczane na dyskach, które są używane wyłącznie dla udostępnionego pliku dziennika, aby zmniejszyć rywalizację.

SharedLogSizeInMB określa ilość miejsca na dysku do wstępnego przydzielenia dla domyślnego dziennika udostępnionego we wszystkich węzłach.  Nie trzeba określać SharedLogId i SharedLogPath, aby można było określić SharedLogSizeInMB.

## <a name="service-specific-configuration"></a>Konfiguracja specyficzna dla usługi
Można modyfikować stanowe Reliable Services "konfiguracje domyślne przy użyciu pakietu konfiguracyjnego (konfiguracji) lub implementacji usługi (kod).

* Konfiguracja **konfiguracji za** pośrednictwem pakietu konfiguracyjnego jest realizowana przez zmianę pliku Settings.xml, który jest generowany w katalogu głównym pakietu Microsoft Visual Studio w folderze config dla każdej usługi w aplikacji.
* **Kod**   — konfiguracja za pośrednictwem kodu jest realizowana przez utworzenie ReliableStateManager przy użyciu obiektu ReliableStateManagerConfiguration z odpowiednimi zestawami opcji.

Domyślnie środowisko uruchomieniowe platformy Azure Service Fabric wyszukuje wstępnie zdefiniowane nazwy sekcji w pliku Settings.xml i zużywa wartości konfiguracyjne podczas tworzenia podstawowych składników środowiska uruchomieniowego.

> [!NOTE]
> **Nie** usuwaj nazw sekcji następujących konfiguracji w pliku Settings.xml, który jest generowany w rozwiązaniu programu Visual Studio, chyba że planujesz skonfigurować usługę za pomocą kodu.
> Zmiana nazwy pakietu lub sekcji konfiguracji wymaga zmiany kodu podczas konfigurowania ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Konfiguracje zabezpieczeń replikatora służą do zabezpieczenia kanału komunikacyjnego, który jest używany podczas replikacji. Oznacza to, że usługi nie będą mogły zobaczyć ruchu związanego z replikacją, zapewniając, że dane, które są wykonywane o wysokiej dostępności, również są bezpieczne. Domyślnie pusta sekcja konfiguracji zabezpieczeń uniemożliwia zabezpieczenia replikacji.

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą mieć format PEM. Aby dowiedzieć się więcej na temat lokalizowania i konfigurowania certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 
> 

### <a name="default-section-name"></a>Domyślna nazwa sekcji
ReplicatorSecurityConfig

> [!NOTE]
> Aby zmienić tę nazwę sekcji, Zastąp parametr replicatorSecuritySectionName konstruktorem ReliableStateManagerConfiguration podczas tworzenia ReliableStateManager dla tej usługi.
> 
> 

### <a name="replicator-configuration"></a>Konfiguracja replikatora
Konfiguracje replikatora konfiguruje Replikator, który jest odpowiedzialny za zapewnianie niezawodnego stanu niezawodności usługi przez replikację i utrwalanie stanu lokalnie.
Konfiguracja domyślna jest generowana przez szablon programu Visual Studio i powinna być wystarczająca. W tej sekcji omówiono dodatkowe konfiguracje, które są dostępne w celu dostrojenia replikatora.

### <a name="default-section-name"></a>Domyślna nazwa sekcji
ReplicatorConfig

> [!NOTE]
> Aby zmienić tę nazwę sekcji, Zastąp parametr replicatorSettingsSectionName konstruktorem ReliableStateManagerConfiguration podczas tworzenia ReliableStateManager dla tej usługi.
> 
> 

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0,015 |Okres, przez który Replikator w dodatkowej chwili czeka po odebraniu operacji przed wysłaniem potwierdzenia do elementu podstawowego. Wszystkie inne potwierdzenia do wysłania dla operacji przetworzonych w ramach tego interwału są wysyłane jako jedna odpowiedź. |
| ReplicatorEndpoint |Nie dotyczy |Brak domyślnego parametru--Required |Adres IP i port, które będą używane przez Replikator podstawowy/pomocniczy do komunikowania się z innymi replikatorami w zestawie replik. Powinno to odwoływać się do punktu końcowego zasobu TCP w manifeście usługi. Zapoznaj się z informacjami o [zasobach manifestu usługi](service-fabric-service-manifest-resources.md) , aby dowiedzieć się więcej na temat definiowania zasobów punktu końcowego w manifeście usługi. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |8192 |Maksymalna liczba operacji w kolejce głównej. Operacja jest zwalniana, gdy podstawowy Replikator otrzyma potwierdzenie ze wszystkich dodatkowych replik. Ta wartość musi być większa niż 64 i potęgą 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |16384 |Maksymalna liczba operacji w kolejce pomocniczej. Operacja zostanie zwolniona po przeprowadzeniu wysokiej dostępności stanu przez trwałość. Ta wartość musi być większa niż 64 i potęgą 2. |
| CheckpointThresholdInMB |MB |50 |Ilość miejsca w pliku dziennika, po którym stan jest tworzony w punkcie kontrolnym. |
| MaxRecordSizeInKB |KB |1024 |Największy rozmiar rekordu, który Replikator może zapisać w dzienniku. Ta wartość musi być wielokrotnością 4 i większa niż 16. |
| MinLogSizeInMB |MB |0 (określony przez system) |Minimalny rozmiar dziennika transakcyjnego. Nie będzie można obciąć dziennika do rozmiaru poniżej tego ustawienia. wartość 0 oznacza, że Replikator określi minimalny rozmiar dziennika. Zwiększenie tej wartości zwiększa możliwość wykonywania kopii częściowych i przyrostowych kopii zapasowych, ponieważ znaczenie odpowiednich rekordów dziennika jest mniejsze. |
| TruncationThresholdFactor |Czynnik |2 |Określa rozmiar dziennika, który zostanie wyzwolony. Próg obcinania jest określany przez MinLogSizeInMB pomnożony przez TruncationThresholdFactor. Wartość TruncationThresholdFactor musi być większa niż 1. MinLogSizeInMB * TruncationThresholdFactor musi być mniejsze niż MaxStreamSizeInMB. |
| ThrottlingThresholdFactor |Czynnik |4 |Określa, jaki rozmiar dziennika rozpocznie się dla repliki. Próg ograniczania przepustowości (w MB) jest określany przez funkcję Max ((MinLogSizeInMB * ThrottlingThresholdFactor), (CheckpointThresholdInMB * ThrottlingThresholdFactor)). Próg ograniczania (w MB) musi być większy niż próg obcinania (w MB). Próg obcinania (w MB) musi być mniejszy niż MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maksymalny rozmiar skumulowany (w MB) dzienników kopii zapasowej w danym łańcuchu dzienników kopii zapasowych. Przyrostowe żądania kopii zapasowej zakończą się niepowodzeniem, jeśli przyrostowa kopia zapasowa wygeneruje dziennik kopii zapasowej, który mógłby spowodować zebrane dzienniki kopii zapasowej, ponieważ odpowiednia pełna kopia zapasowa będzie większa niż ten rozmiar. W takich przypadkach użytkownik musi wykonać pełną kopię zapasową. |
| SharedLogId |GUID |"" |Określa unikatowy identyfikator GUID, który będzie używany do identyfikowania udostępnionego pliku dziennika używanego z tą repliką. Zazwyczaj usługi nie powinny używać tego ustawienia. Jeśli jednak SharedLogId jest określony, należy również określić SharedLogPath. |
| SharedLogPath |W pełni kwalifikowana nazwa ścieżki |"" |Określa w pełni kwalifikowaną ścieżkę, w której zostanie utworzony udostępniony plik dziennika dla tej repliki. Zazwyczaj usługi nie powinny używać tego ustawienia. Jeśli jednak SharedLogPath jest określony, należy również określić SharedLogId. |
| SlowApiMonitoringDuration |Sekundy |300 |Ustawia interwał monitorowania dla wywołań zarządzanych interfejsów API. Przykład: użytkownik podał funkcję wywołania zwrotnego kopii zapasowej. Po upływie interwału Raport kondycji ostrzeżeń zostanie wysłany do Menedżera kondycji. |
| LogTruncationIntervalSeconds |Sekundy |0 |Konfigurowalny interwał, w którym zostanie zainicjowane obcinanie dziennika dla każdej repliki. Jest on używany do zapewnienia, że dziennik jest również obcinany w oparciu o czas, a nie tylko rozmiar dziennika. To ustawienie wymusza także przeczyszczanie usuniętych wpisów w niezawodnym słowniku. W związku z tym można go użyć w celu zapewnienia, że usunięte elementy są czyszczone w odpowiednim czasie. |
| EnableStableReads |Wartość logiczna |Fałsz |Włączenie stałych odczytów ogranicza repliki pomocnicze do zwracania wartości, które były potwierdzone kworum. |

### <a name="sample-configuration-via-code"></a>Przykładowa konfiguracja za pośrednictwem kodu
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Przykładowy plik konfiguracji
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
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


### <a name="remarks"></a>Uwagi
BatchAcknowledgementInterval steruje opóźnieniem replikacji. Wartość "0" skutkuje najniższym możliwym opóźnieniem, kosztem przepływności (w przypadku, gdy więcej komunikatów potwierdzających musi być wysłanych i przetwarzanych, z których każda zawiera mniejszą liczbę potwierdzeń).
Im większa wartość BatchAcknowledgementInterval, tym wyższa ogólna przepływność replikacji, koszt opóźnienia operacji. Jest to bezpośrednio tłumaczone na opóźnienie zatwierdzeń transakcji.

Wartość CheckpointThresholdInMB Steruje ilością miejsca na dysku, która może być używana przez Replikator do przechowywania informacji o stanie w dedykowanym pliku dziennika repliki. Zwiększenie tej wartości niż domyślna może spowodować szybsze ponowne skonfigurowanie, gdy Nowa replika zostanie dodana do zestawu. Jest to spowodowane częściowym transferem stanu, który ma miejsce z powodu dostępności większej historii operacji w dzienniku. Może to spowodować zwiększenie czasu odzyskiwania repliki po awarii.

Ustawienie MaxRecordSizeInKB określa maksymalny rozmiar rekordu, który może być zapisany przez replikatora w pliku dziennika. W większości przypadków rozmiar domyślnego rekordu 1024 KB jest optymalny. Jeśli jednak usługa powoduje, że większe elementy danych będą częścią informacji o stanie, może być konieczne zwiększenie tej wartości. W przypadku MaxRecordSizeInKB mniejszym niż 1024 jest mało korzyści, ponieważ mniejsze rekordy używają tylko miejsca wymaganego dla mniejszego rekordu. Oczekujemy, że ta wartość powinna być zmieniana tylko w rzadkich przypadkach.

Ustawienia SharedLogId i SharedLogPath są zawsze używane razem, aby usługa korzystała z oddzielnego dziennika udostępnionego z domyślnego dziennika udostępnionego węzła. W celu uzyskania najlepszej wydajności, jak wiele usług, jak to możliwe, należy określić ten sam dziennik udostępniony. Udostępnione pliki dzienników powinny być umieszczane na dyskach, które są używane wyłącznie dla udostępnionego pliku dziennika, aby zmniejszyć rywalizację o ruch podrzędny. Oczekujemy, że ta wartość powinna być zmieniana tylko w rzadkich przypadkach.

## <a name="next-steps"></a>Następne kroki
* [Debugowanie aplikacji Service Fabric w programie Visual Studio](service-fabric-debugging-your-application.md)
* [Dokumentacja dla deweloperów Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
