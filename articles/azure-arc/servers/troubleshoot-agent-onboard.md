---
title: Rozwiązywanie Azure Arc problemów z połączeniem agenta z włączonymi serwerami
description: W tym artykule opisano sposób rozwiązywania problemów z agentem połączonej maszyny, które występują Azure Arc serwerami z włączoną obsługą podczas próby nawiązania połączenia z usługą.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498003"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Rozwiązywanie Azure Arc problemów z połączeniem agenta z włączonymi serwerami

Ten artykuł zawiera informacje na temat rozwiązywania problemów, które mogą wystąpić podczas próby skonfigurowania agenta połączonej maszyny z włączoną obsługą usługi Azure Arc dla systemu Windows lub Linux. Podczas konfigurowania połączenia z usługą są uwzględniane zarówno metody instalacji interaktywnej, jak i w dużej skali. Aby uzyskać ogólne informacje, zobacz [Omówienie serwerów z obsługą usługi Arc.](./overview.md)

## <a name="agent-error-codes"></a>Kody błędów agenta

Jeśli podczas konfigurowania agenta serwerów z włączoną obsługą usługi Azure Arc wystąpi błąd, następująca tabela może pomóc w zidentyfikowaniu prawdopodobnej przyczyny i sugerowanych czynności w celu rozwiązania problemu. Aby kontynuować, należy wydrukować kod błędu ("0000" może być dowolną liczbą 4-cyfrową) wydrukowany w konsoli lub w `AZCM0000` danych wyjściowych skryptu.

| Kod błędu | Prawdopodobna przyczyna | Sugerowane korygowanie |
|------------|----------------|-----------------------|
| AZCM0000 | Akcja powiodła się | Nie dotyczy |
| AZCM0001 | Wystąpił nieznany błąd | Skontaktuj się z Pomoc techniczna Microsoft, aby uzyskać dalszą pomoc |
| AZCM0011 | Użytkownik anulował akcję (CTRL+C) | Ponów próbę wykonania poprzedniego polecenia |
| AZCM0012 | Podany token dostępu jest nieprawidłowy | Uzyskaj nowy token dostępu i spróbuj ponownie |
| AZCM0013 | Podane tagi są nieprawidłowe | Sprawdź, czy tagi są ujęte w podwójne cudzysłowy, oddzielone przecinkami, oraz czy wszystkie nazwy lub wartości ze spacjami są ujęte w cudzysłów pojedynczy: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | Chmura jest nieprawidłowa | Określ obsługiwaną chmurę: `AzureCloud` lub `AzureUSGovernment` |
| AZCM0015 | Określony identyfikator korelacji nie jest prawidłowym identyfikatorem GUID | Podaj prawidłowy identyfikator GUID dla `--correlation-id` |
| AZCM0016 | Brak obowiązkowego parametru | Przejrzyj dane wyjściowe, aby ustalić, których parametrów brakuje |
| AZCM0017 | Nazwa zasobu jest nieprawidłowa | Określ nazwę, która używa tylko znaków alfanumerycznych, łączników i/lub podkreśleń. Nazwa nie może kończyć się łącznikiem ani podkreśleniem. |
| AZCM0018 | Polecenie zostało wykonane bez uprawnień administracyjnych | Spróbuj ponownie wykonać polecenie z uprawnieniami administratora lub katalogu głównego w wierszu polecenia z podwyższonym poziomem uprawnień lub sesji konsoli. |
| AZCM0041 | Podane poświadczenia są nieprawidłowe | W przypadku identyfikatorów logowania urządzeń sprawdź, czy określone konto użytkownika ma dostęp do dzierżawy i subskrypcji, w której zostanie utworzony zasób serwera. W przypadku identyfikatorów logowania jednostki usługi sprawdź poprawność identyfikatora klienta i klucz tajny, datę wygaśnięcia tajnego i czy nazwa główna usługi pochodzi z tej samej dzierżawy, w której zostanie utworzony zasób serwera. |
| AZCM0042 | Tworzenie zasobu serwera z obsługą usługi Arc nie powiodło się | Sprawdź, czy określona nazwa główna użytkownika/usługi ma dostęp do tworzenia zasobów serwera z obsługą usługi Arc w określonej grupie zasobów. |
| AZCM0043 | Usuwanie zasobu serwera z obsługą usługi Arc nie powiodło się | Sprawdź, czy określona nazwa główna użytkownika/usługi ma dostęp do usuwania zasobów serwera z włączoną usługą Arc w określonej grupie zasobów. Jeśli zasób nie istnieje już na platformie Azure, użyj `--force-local-only` flagi , aby kontynuować. |
| AZCM0044 | Zasób o tej samej nazwie już istnieje | Określ inną nazwę parametru lub usuń istniejący serwer z usługą `--resource-name` Arc na platformie Azure i spróbuj ponownie. |
| AZCM0061 | Nie można uzyskać dostępu do usługi agenta | Sprawdź, czy polecenie jest uruchomione w kontekście użytkownika z podwyższonym poziomem uprawnień (administrator/katalog główny) i czy usługa HIMDS jest uruchomiona na serwerze. |
| AZCM0062 | Wystąpił błąd podczas nawiązywania połączenia z serwerem | Przejrzyj inne kody błędów w danych wyjściowych, aby uzyskać bardziej szczegółowe informacje. Jeśli błąd wystąpił po utworzeniu zasobu platformy Azure, przed ponowieniem próby musisz usunąć serwer Arc z grupy zasobów. |
| AZCM0063 | Wystąpił błąd podczas odłączania serwera | Przejrzyj inne kody błędów w danych wyjściowych, aby uzyskać bardziej szczegółowe informacje. Jeśli ten błąd nadal występuje, możesz usunąć zasób na platformie Azure, a następnie uruchomić na `azcmagent disconnect --force-local-only` serwerze, aby odłączyć agenta. |
| AZCM0064 | Usługa agenta nie odpowiada | Sprawdź stan `himds` usługi, aby upewnić się, że jest uruchomiona. Uruchom usługę, jeśli nie jest uruchomiona. Jeśli jest uruchomiony, zaczekaj chwilę, a następnie spróbuj ponownie. |
| AZCM0065 | Wystąpił wewnętrzny błąd komunikacji agenta | Skontaktuj się z Pomoc techniczna Microsoft, aby uzyskać pomoc |
| AZCM0066 | Usługa sieci Web agenta nie odpowiada lub jest niedostępna | Skontaktuj się z Pomoc techniczna Microsoft, aby uzyskać pomoc |
| AZCM0067 | Agent jest już połączony z platformą Azure | Postępuj zgodnie z instrukcjami [w pierwszej kolejności, aby odłączyć agenta,](manage-agent.md#unregister-machine) a następnie spróbuj ponownie. |
| AZCM0068 | Wystąpił błąd wewnętrzny podczas odłączania serwera od platformy Azure | Skontaktuj się z Pomoc techniczna Microsoft, aby uzyskać pomoc |
| AZCM0081 | Wystąpił błąd podczas pobierania certyfikatu Azure Active Directory tożsamości zarządzanej | Jeśli ten komunikat zostanie napotkany podczas próby połączenia serwera z platformą Azure, agent nie będzie mógł komunikować się z usługą Azure Arc azure. Usuń zasób na platformie Azure i spróbuj ponownie nałączyć połączenie. |
| AZCM0101 | Polecenie nie zostało pomyślnie analizowane | Uruchom `azcmagent <command> --help` polecenie , aby przejrzeć prawidłową składnię polecenia |
| AZCM0102 | Nie można pobrać nazwy hosta komputera | Uruchom `hostname` , aby sprawdzić komunikaty o błędach na poziomie systemu, a następnie skontaktuj się z Pomoc techniczna Microsoft. |
| AZCM0103 | Wystąpił błąd podczas generowania kluczy RSA | Skontaktuj się z Pomoc techniczna Microsoft, aby uzyskać pomoc |
| AZCM0104 | Nie można odczytać informacji o systemie | Sprawdź, czy tożsamość używana do uruchomienia ma `azcmagent` uprawnienia administratora/administratora w systemie i spróbuj ponownie. |

## <a name="agent-verbose-log"></a>Pełny dziennik agenta

Przed rozpoczęciem rozwiązywania problemów opisanych w dalszej części tego artykułu minimalnymi potrzebnymi informacjami jest pełny dziennik. Zawiera dane wyjściowe poleceń **narzędzia azcmagent,** gdy jest używany pełny argument (-v). Pliki dziennika są zapisywane w plikach `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` dla systemu Windows, a w systemie Linux w pliku `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Poniżej przedstawiono przykład polecenia umożliwiającego pełne rejestrowanie z agentem Connected Machine dla systemu Windows podczas przeprowadzania instalacji interakcyjnej.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Poniżej przedstawiono przykład polecenia umożliwiającego pełne rejestrowanie z agentem Connected Machine dla systemu Windows podczas przeprowadzania instalacji na dużą skalę przy użyciu jednostki usługi.

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

Poniżej przedstawiono przykład polecenia umożliwiającego pełne rejestrowanie za pomocą agenta Connected Machine dla systemu Linux podczas przeprowadzania instalacji interakcyjnej.

>[!NOTE]
>Aby uruchomić *program* **azcmagent,** musisz mieć uprawnienia dostępu administratora na maszynach z systemem Linux.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Poniżej przedstawiono przykład polecenia umożliwiającego pełne rejestrowanie z agentem Connected Machine dla systemu Linux podczas przeprowadzania instalacji na dużą skalę przy użyciu jednostki usługi.

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

W poniższej tabeli wymieniono niektóre znane błędy i sugestie dotyczące sposobu ich rozwiązywania.

|Komunikat |Błąd |Prawdopodobna przyczyna |Rozwiązanie |
|--------|------|---------------|---------|
|Nie można uzyskać przepływu urządzenia tokenu autoryzacji |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Nie można uzyskać dostępu do punktu `login.windows.net` końcowego | Sprawdź łączność z punktem końcowym. |
|Nie można uzyskać przepływu urządzenia tokenu autoryzacji |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Serwer proxy lub zapora blokuje dostęp do punktu `login.windows.net` końcowego. | Sprawdź łączność z punktem końcowym i nie jest ona blokowana przez zaporę lub serwer proxy. |
|Nie można uzyskać przepływu urządzenia tokenu autoryzacji  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | zasady grupy Konfiguracji komputera *obiektów\ Szablony administracyjne\ System\ Profile użytkowników\* Usuń profile użytkowników starsze niż określona liczba dni przy ponownym uruchomieniu systemu jest włączona. | Sprawdź, czy włączono cel zasad grupy i skierowano go do komputera, którego dotyczy problem. Aby uzyskać więcej informacji, zobacz przypis <sup>[1.](#footnote1)</sup> |
|Nie można uzyskać tokenu autoryzacji z usługi SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Serwer proxy lub zapora blokuje dostęp do punktu `login.windows.net` końcowego. |Sprawdź łączność z punktem końcowym i nie jest ona blokowana przez zaporę ani serwer proxy. |
|Nie można uzyskać tokenu autoryzacji z usługi SPN |`Invalid client secret is provided` |Nieprawidłowy lub nieprawidłowy klucz tajny jednostki usługi. |Sprawdź klucz tajny jednostki usługi. |
| Nie można uzyskać tokenu autoryzacji z usługi SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Nieprawidłowa jednostka usługi i/lub identyfikator dzierżawy. |Sprawdź jednostkę usługi i/lub identyfikator dzierżawy.|
|Uzyskiwanie odpowiedzi zasobu ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Nieprawidłowe poświadczenia i/lub uprawnienia |Sprawdź, czy Ty lub główna usługa jest **członkiem Azure Connected Machine dołączania.** |
|Nie można połączyć się z zasobem ARM AzcmagentConnect |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Dostawcy zasobów platformy Azure nie są zarejestrowani. |Zarejestruj [dostawców zasobów.](./agent-overview.md#register-azure-resource-providers) |
|Nie można połączyć się z zasobem ARM AzcmagentConnect |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Serwer proxy lub zapora blokuje dostęp do punktu `management.azure.com` końcowego. |Sprawdź łączność z punktem końcowym i nie jest ona blokowana przez zaporę ani serwer proxy. |

<a name="footnote1"></a><sup>1</sup> Jeśli ten obiekt zasad grupy jest włączony i ma zastosowanie do maszyn z agentem Connected Machine, usuwa profil użytkownika skojarzony z wbudowanym kontem określonym dla *usługi himds.* W związku z tym usuwa również certyfikat uwierzytelniania używany do komunikowania się z usługą, która jest buforowana w lokalnym magazynie certyfikatów przez 30 dni. Przed limitem 30 dni podejmowano próbę odnowienia certyfikatu. Aby rozwiązać ten problem, wykonaj kroki [wyrejestrowania](manage-agent.md#unregister-machine) maszyny, a następnie zarejestruj ją ponownie w usłudze z systemem `azcmagent connect` .

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz tutaj swojego problemu lub nie możesz go rozwiązać, wypróbuj jeden z następujących kanałów, aby uzyskać dodatkową pomoc techniczną:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za [pośrednictwem usługi Microsoft Q&A.](/answers/topics/azure-arc.html)

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z , oficjalnym kontem Microsoft Azure w celu poprawy jakości obsługi klienta. pomoc techniczna platformy Azure społeczności platformy Azure z odpowiedziami, pomocą techniczną i ekspertami.

* Zdaj zdarzenie pomocy technicznej platformy Azure. Przejdź do witryny [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną.**
