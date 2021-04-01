---
title: Rozwiązywanie problemów z połączeniami agentów serwerów z obsługą usługi Azure Arc
description: W tym artykule opisano sposób rozwiązywania problemów z agentem połączonej maszyny, które są dostępne w przypadku serwerów z włączonym rozwiązaniem Azure Arc podczas próby nawiązania połączenia z usługą.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 36feb6a65ec52d99dfd664ae54cb099ea6a7e239
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90900678"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Rozwiązywanie problemów z połączeniem z agentem podłączonych maszyn

Ten artykuł zawiera informacje o rozwiązywaniu problemów i rozwiązywaniu problemów, które mogą wystąpić podczas próby skonfigurowania serwera z włączoną obsługą usługi Azure ARC dla systemu Windows lub Linux. Dostępne są zarówno metody instalacji interaktywnej, jak i na skalę podczas konfigurowania połączenia z usługą. Aby uzyskać ogólne informacje, zobacz [Omówienie serwerów z obsługą Arc](./overview.md).

## <a name="agent-verbose-log"></a>Pełny dziennik agenta

Przed wykonaniem procedury rozwiązywania problemów opisanej w dalszej części tego artykułu wymagane są informacje o minimalnym zadaniu. Zawiera dane wyjściowe poleceń narzędzi **azcmagent** , gdy jest używany argument verbose (-v). Pliki dziennika są zapisywane w `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` systemach Windows i Linux do systemu `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Poniżej znajduje się przykład polecenia służącego do włączania pełnego rejestrowania przy użyciu agenta połączonej maszyny dla systemu Windows podczas przeprowadzania instalacji interaktywnej.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Poniżej znajduje się przykład polecenia umożliwiającego pełne rejestrowanie przy użyciu agenta połączonej maszyny dla systemu Windows podczas przeprowadzania instalacji w skali przy użyciu nazwy głównej usługi.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Poniżej znajduje się przykład polecenia służącego do włączenia pełnego rejestrowania przy użyciu agenta połączonej maszyny dla systemu Linux podczas przeprowadzania instalacji interaktywnej.

>[!NOTE]
>Aby uruchamiać **azcmagent**, musisz mieć uprawnienia dostępu *głównego* na maszynach z systemem Linux.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Poniżej znajduje się przykład polecenia służącego do włączania pełnego rejestrowania za pomocą agenta połączonej maszyny dla systemu Linux podczas przeprowadzania instalacji w skali przy użyciu nazwy głównej usługi.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problemy z połączeniem agenta z usługą

W poniższej tabeli wymieniono niektóre znane błędy i sugestie dotyczące sposobu rozwiązywania problemów i ich rozwiązywania.

|Komunikat |Błąd |Prawdopodobna przyczyna |Rozwiązanie |
|--------|------|---------------|---------|
|Nie można uzyskać przepływu urządzenia tokenu autoryzacji |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Nie można połączyć się z `login.windows.net` punktem końcowym | Sprawdź łączność z punktem końcowym. |
|Nie można uzyskać przepływu urządzenia tokenu autoryzacji |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Serwer proxy lub zapora blokuje dostęp do `login.windows.net` punktu końcowego. | Sprawdź łączność z punktem końcowym i nie jest blokowany przez zaporę lub serwer proxy. |
|Nie można uzyskać przepływu urządzenia tokenu autoryzacji  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | *Konfiguracja komputera zasady grupy obiektu \ Szablony administracyjne \ System \ profile użytkowników \ Usuń profile użytkowników starsze niż określona liczba dni po ponownym uruchomieniu systemu* jest włączona. | Upewnij się, że obiekt zasad grupy jest włączony i przeznaczony dla maszyny docelowej. Zobacz przypis <sup>[1](#footnote1)</sup> , aby uzyskać więcej szczegółów. |
|Nie można uzyskać tokenu autoryzacji z nazwy SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Serwer proxy lub zapora blokuje dostęp do `login.windows.net` punktu końcowego. |Sprawdź łączność z punktem końcowym i nie jest blokowany przez zaporę lub serwer proxy. |
|Nie można uzyskać tokenu autoryzacji z nazwy SPN |`Invalid client secret is provided` |Niewłaściwy lub nieprawidłowy klucz tajny jednostki usługi. |Sprawdź klucz tajny jednostki usługi. |
| Nie można uzyskać tokenu autoryzacji z nazwy SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Nieprawidłowa nazwa główna usługi i/lub identyfikator dzierżawy. |Sprawdź nazwę główną usługi i/lub identyfikator dzierżawy.|
|Pobierz odpowiedź zasobu ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Nieprawidłowe poświadczenia i/lub uprawnienia |Sprawdź, czy nazwa główna usługi jest członkiem roli **dołączania maszyny połączonej z platformą Azure** . |
|Nie można AzcmagentConnect zasobu ARM |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Dostawcy zasobów platformy Azure nie są zarejestrowani. |Zarejestruj [dostawców zasobów](./agent-overview.md#register-azure-resource-providers). |
|Nie można AzcmagentConnect zasobu ARM |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Serwer proxy lub zapora blokuje dostęp do `management.azure.com` punktu końcowego. |Sprawdź łączność z punktem końcowym i nie jest blokowany przez zaporę lub serwer proxy. |

<a name="footnote1"></a><sup>1</sup> Jeśli ten obiekt zasad grupy jest włączony i dotyczy maszyn z agentem podłączonym do maszyny połączonej, usuwa profil użytkownika skojarzony z wbudowanym kontem określonym dla usługi *himds* . W związku z tym usuwa również certyfikat uwierzytelniania używany do komunikacji z usługą buforowaną w lokalnym magazynie certyfikatów przez 30 dni. Przed upływem 30 dni zostanie podjęta próba odnowienia certyfikatu. Aby rozwiązać ten problem, wykonaj czynności, aby [wyrejestrować maszynę](manage-agent.md#unregister-machine) , a następnie ponownie ją zarejestrować z uruchomioną usługą `azcmagent connect` .

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj problemu lub nie możesz rozwiązać problemu, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [Microsoft Q&A](/answers/topics/azure-arc.html).

* Nawiąż połączenie z [@AzureSupport](https://twitter.com/azuresupport) kontem oficjalnego Microsoft Azure, aby zwiększyć komfort obsługi klienta. Pomoc techniczna systemu Azure łączy społeczność platformy Azure z odpowiedziami, wsparciem i ekspertami.

* Zaplikowanie zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję **Uzyskaj pomoc techniczną**.
