---
title: Zmień ustawienia dostawcy KVSActorStateProvider
description: Dowiedz się więcej na temat konfigurowania Service Fabric stanowych aktorów typu dostawcy KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609778"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Konfigurowanie Reliable Actors--dostawcy KVSActorStateProvider
Domyślną konfigurację dostawcy KVSActorStateProvider można zmodyfikować, zmieniając plik settings.xml, który jest generowany w katalogu głównym pakietu Microsoft Visual Studio w folderze config dla określonego aktora.

Środowisko uruchomieniowe Azure Service Fabric wyszukuje wstępnie zdefiniowane nazwy sekcji w pliku settings.xml i zużywa wartości konfiguracyjne podczas tworzenia podstawowych składników środowiska uruchomieniowego.

> [!NOTE]
> Nie usuwaj ani **nie** Modyfikuj nazw sekcji następujących konfiguracji w pliku settings.xml, który jest generowany w rozwiązaniu programu Visual Studio.
> 
> 

## <a name="replicator-security-configuration"></a>Konfiguracja zabezpieczeń replikatora
Konfiguracje zabezpieczeń replikatora służą do zabezpieczenia kanału komunikacyjnego, który jest używany podczas replikacji. Oznacza to, że usługi nie widzą żadnego ruchu związanego z replikacją, zapewniając, że dane, które są wykonywane o wysokiej dostępności, również są bezpieczne.
Domyślnie pusta sekcja konfiguracji zabezpieczeń uniemożliwia zabezpieczenia replikacji.

> [!IMPORTANT]
> W węzłach systemu Linux certyfikaty muszą mieć format PEM. Aby dowiedzieć się więcej na temat lokalizowania i konfigurowania certyfikatów dla systemu Linux, zobacz [Konfigurowanie certyfikatów w systemie Linux](./service-fabric-configure-certificates-linux.md). 
> 

### <a name="section-name"></a>Nazwa sekcji
&lt;ServiceReplicatorSecurityConfig aktora &gt;

## <a name="replicator-configuration"></a>Konfiguracja replikatora
Konfiguracje replikatora konfiguruje Replikator, który jest odpowiedzialny za zapewnianie niezawodnego stanu dostawcy stanu aktora.
Konfiguracja domyślna jest generowana przez szablon programu Visual Studio i powinna być wystarczająca. W tej sekcji omówiono dodatkowe konfiguracje, które są dostępne w celu dostrojenia replikatora.

### <a name="section-name"></a>Nazwa sekcji
&lt;ServiceReplicatorConfig aktora &gt;

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Sekundy |0,015 |Okres, przez który Replikator w dodatkowej chwili czeka po odebraniu operacji przed wysłaniem potwierdzenia do elementu podstawowego. Wszystkie inne potwierdzenia do wysłania dla operacji przetworzonych w ramach tego interwału są wysyłane jako jedna odpowiedź. |
| ReplicatorEndpoint |Nie dotyczy |Brak domyślnego parametru--Required |Adres IP i port, które będą używane przez Replikator podstawowy/pomocniczy do komunikowania się z innymi replikatorami w zestawie replik. Powinno to odwoływać się do punktu końcowego zasobu TCP w manifeście usługi. Zapoznaj się z informacjami o [zasobach manifestu usługi](service-fabric-service-manifest-resources.md) , aby dowiedzieć się więcej na temat definiowania zasobów punktu końcowego w manifeście usługi. |
| RetryInterval |Sekundy |5 |Okres, po którym Replikator ponownie przesyła komunikat, jeśli nie otrzyma potwierdzenia dla operacji. |
| MaxReplicationMessageSize |Bajty |50 MB |Maksymalny rozmiar danych replikacji, które mogą być przesyłane w jednej wiadomości. |
| MaxPrimaryReplicationQueueSize |Liczba operacji |1024 |Maksymalna liczba operacji w kolejce głównej. Operacja jest zwalniana, gdy podstawowy Replikator otrzyma potwierdzenie ze wszystkich dodatkowych replik. Ta wartość musi być większa niż 64 i potęgą 2. |
| MaxSecondaryReplicationQueueSize |Liczba operacji |2048 |Maksymalna liczba operacji w kolejce pomocniczej. Operacja zostanie zwolniona po przeprowadzeniu wysokiej dostępności stanu przez trwałość. Ta wartość musi być większa niż 64 i potęgą 2. |

## <a name="store-configuration"></a>Konfiguracja magazynu
Konfiguracje magazynu są używane do konfigurowania lokalnego magazynu, który jest używany do utrwalania replikowanego stanu.
Konfiguracja domyślna jest generowana przez szablon programu Visual Studio i powinna być wystarczająca. W tej sekcji omówiono dodatkowe konfiguracje, które są dostępne do dostrajania magazynu lokalnego.

### <a name="section-name"></a>Nazwa sekcji
&lt;ServiceLocalStoreConfig aktora &gt;

### <a name="configuration-names"></a>Nazwy konfiguracji
| Nazwa | Jednostka | Wartość domyślna | Uwagi |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |) |200 |Ustawia maksymalny interwał wsadowy dla zatwierdzeń trwałego magazynu lokalnego. |
| MaxVerPages |Liczba stron |16384 |Maksymalna liczba stron wersji w lokalnej bazie danych magazynu. Określa maksymalną liczbę zaległych transakcji. |

## <a name="sample-configuration-file"></a>Przykładowy plik konfiguracji
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

