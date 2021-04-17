---
title: Ustawienie konfiguracji narzędzia AzCopy w wersji 10 (Azure Storage) | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące ustawień konfiguracji programu AzCopy w wersji 10.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4fcbcf145dc417d2a7f78913e70429c3929cd902
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509119"
---
# <a name="azcopy-v10-configuration-settings-azure-storage"></a>Ustawienia konfiguracji narzędzia AzCopy w wersji 10 (Azure Storage)

AzCopy to narzędzie wiersza polecenia, przy użyciu którego można kopiować obiekty blob lub pliki do lub z konta magazynu. Ten artykuł zawiera listę zmiennych środowiskowych, których można użyć do skonfigurowania programu AzCopy w wersji 10.

> [!NOTE]
> Jeśli szukasz zawartości, która pomoże Ci rozpocząć pracę z platformą AzCopy, zobacz [Wprowadzenie do programu AzCopy.](storage-use-azcopy-v10.md)

## <a name="azcopy-v10-environment-variables"></a>Zmienne środowiskowe programu AzCopy w wersji 10

W poniższej tabeli opisano każdą zmienną środowiskową i popisano linki do zawartości, które mogą ułatwić korzystanie ze zmiennej.

| Zmienna środowiskowa | Opis |
|--|--|
| AWS_ACCESS_KEY_ID | Amazon Web Services dostępu. Udostępnia klucz do autoryzacji za pomocą Amazon Web Services. [Kopiowanie danych z usługi Amazon S3 do usługi Azure Storage przy użyciu narzędzia AzCopy](storage-use-azcopy-s3.md) |
| AWS_SECRET_ACCESS_KEY | Amazon Web Services tajny klucz dostępu Udostępnia klucz tajny do autoryzacji przy użyciu Amazon Web Services. [Kopiowanie danych z usługi Amazon S3 do usługi Azure Storage przy użyciu narzędzia AzCopy](storage-use-azcopy-s3.md) |
| AZCOPY_ACTIVE_DIRECTORY_ENDPOINT | Punkt Azure Active Directory do użycia. Ta zmienna jest używana tylko do automatycznego logowania. Zamiast tego użyj flagi wiersza polecenia podczas wywołania polecenia logowania. |
| AZCOPY_AUTO_LOGIN_TYPE | Ustaw tę zmienną na `DEVICE` wartość `MSI` , lub `SPN` . Ta zmienna umożliwia autoryzowanie bez użycia `azcopy login` polecenia . Ten mechanizm jest przydatny w przypadkach, gdy system operacyjny nie ma magazynu kluczy tajnych, takiego jak *keyring systemu* Linux. Zobacz [Autoryzowanie bez magazynu tajnych.](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_BUFFER_GB | Określ maksymalną ilość pamięci systemowej, której ma używać program AzCopy podczas pobierania i przekazywania plików. Wyrażaj tę wartość w gigabajtach (GB). Zobacz [Optymalizowanie użycia pamięci](storage-use-azcopy-optimize.md#optimize-memory-use) |
| AZCOPY_CACHE_PROXY_LOOKUP | Domyślnie program AzCopy w systemie Windows będzie buforować wyszukiwania serwera proxy na poziomie nazwy hosta (nie biorąc pod uwagę ścieżki URL). Ustaw wartość inną niż "true", aby wyłączyć pamięć podręczną. |
| AZCOPY_CONCURRENCY_VALUE | Określa liczbę równoczesnych żądań, które mogą wystąpić. Ta zmienna umożliwia zwiększenie przepływności. Jeśli komputer ma mniej niż 5 procesorów CPU, wartość tej zmiennej jest ustawiona na `32` . W przeciwnym razie wartość domyślna jest równa 16-krotności liczby procesorów CPU. Maksymalna wartość domyślna tej zmiennej to , ale można ręcznie ustawić `3000` tę wartość na wyższą lub niższą. Zobacz [Zwiększanie współbieżności](storage-use-azcopy-optimize.md#increase-concurrency) |
| AZCOPY_CONCURRENT_FILES | Zastępuje (przybliżoną) liczbę plików, które są w toku w dowolnym momencie, kontrolując liczbę plików, dla których współbieżnie inicjujemy transfery. |
| AZCOPY_CONCURRENT_SCAN | Steruje (maksymalnym) stopniem równoległości używanym podczas skanowania. Dotyczy tylko równoległych wyliczeń, które obejmują Azure Files/obiekty blob i lokalne systemy plików. |
| AZCOPY_DEFAULT_SERVICE_API_VERSION | Zastępuje wersję interfejsu API usługi, aby program AzCopy mógł obsłużyć środowiska niestandardowe, takie jak Azure Stack. |
| AZCOPY_DISABLE_HIERARCHICAL_SCAN | Ma zastosowanie tylko wtedy, gdy źródłem jest usługa Azure Blobs. Skanowanie współbieżne jest szybsze, ale wykorzystuje hierarchiczny interfejs API listy, co może spowodować większe koszty/operacje we/wy. Określ wartość "true", aby zachować wydajność, ale zaoszczędzić na kosztach. |
| AZCOPY_JOB_PLAN_LOCATION | Zastępuje miejsce przechowywania plików planu zadania (używanych do śledzenia postępu i wznawiania), aby uniknąć zapełniania dysku. |
| AZCOPY_LOG_LOCATION | Zastępuje miejsce przechowywania plików dziennika, aby uniknąć zapełniania dysku. |
| AZCOPY_MSI_CLIENT_ID | Identyfikator klienta tożsamości zarządzanej przypisanej przez użytkownika. Użyj , gdy `AZCOPY_AUTO_LOGIN_TYPE` wartość jest ustawiona na `MSI` . Zobacz [Autoryzowanie bez magazynu tajnych](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_OBJECT_ID | Identyfikator obiektu tożsamości zarządzanej przypisanej przez użytkownika. Użyj , gdy `AZCOPY_AUTO_LOGIN_TYPE` wartość jest ustawiona na `MSI` . Zobacz [Autoryzowanie bez magazynu tajnych](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_MSI_RESOURCE_STRING | Identyfikator zasobu tożsamości zarządzanej przypisanej przez użytkownika. Zobacz [Autoryzowanie bez magazynu tajnych](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_PACE_PAGE_BLOBS | Czy przepływność stronicowych obiektów blob powinna być automatycznie dostosowywana w celu dopasowania do limitów usługi? Wartość domyślna to „true”. Ustaw wartość "false", aby wyłączyć |
| AZCOPY_PARALLEL_STAT_FILES | Powoduje, że program AzCopy przeszuka właściwości pliku w równoległych "wątkach" podczas skanowania lokalnego systemu plików.  Wątki są rysowane z puli zdefiniowanej przez AZCOPY_CONCURRENT_SCAN.  Ustawienie wartości true może poprawić wydajność skanowania w systemie Linux.  Nie jest potrzebny lub zalecany w systemie Windows. |
| AZCOPY_SHOW_PERF_STATES | Jeśli zostanie ustawiona wartość ,dane wyjściowe na ekranie będą zawierać liczby fragmentów według stanu |
| AZCOPY_SPA_APPLICATION_ID | Identyfikator aplikacji rejestracji aplikacji jednostki usługi. Użyj , gdy `AZCOPY_AUTO_LOGIN_TYPE` jest ustawiona na `SPN` wartość . Zobacz [Autoryzowanie bez magazynu tajnych](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PASSWORD | Hasło certyfikatu. Użyj , gdy `AZCOPY_AUTO_LOGIN_TYPE` jest ustawiona na `SPN` wartość . Zobacz [Autoryzowanie bez magazynu tajnych](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CERT_PATH | Względna lub w pełni kwalifikowana ścieżka do pliku certyfikatu. Użyj , gdy `AZCOPY_AUTO_LOGIN_TYPE` wartość jest ustawiona na `SPN` . Zobacz [Autoryzowanie bez magazynu tajnych](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_SPA_CLIENT_SECRET | Klucz tajny klienta. Użyj , gdy `AZCOPY_AUTO_LOGIN_TYPE` wartość jest ustawiona na `SPN` . Zobacz [Autoryzowanie bez magazynu tajnych](storage-use-azcopy-authorize-azure-active-directory.md#authorize-without-a-secret-store) |
| AZCOPY_TENANT_ID | Identyfikator Azure Active Directory dzierżawy do użycia na użytek logowania interakcyjnego urządzenia OAuth. Ta zmienna jest używana tylko do automatycznego logowania. Zamiast tego użyj flagi wiersza polecenia podczas wywołania polecenia logowania. |
| AZCOPY_TUNE_TO_CPU | Ustaw wartość false, aby uniemożliwić programowi AzCopy uwzględnianie użycia procesora CPU podczas automatycznego dostrajania poziomu współbieżności (na przykład w poleceniu testu porównawczego). |
| AZCOPY_USER_AGENT_PREFIX | Dodaj prefiks do domyślnego agenta użytkownika programu AzCopy, który jest używany na potrzeby telemetrii. Spacja jest wstawiana automatycznie. |
| GOOGLE_APPLICATION_CREDENTIALS | Ścieżka bezwzględna do pliku klucza konta usługi Udostępnia klucz do autoryzacji w usłudze Google Cloud Storage. [Kopiowanie danych z usługi Google Cloud Storage do usługi Azure Storage przy użyciu narzędzia AzCopy (wersja zapoznawcza)](storage-ref-azcopy-configuration-settings.md) |
| HTTPS_PROXY | Konfiguruje ustawienia serwera proxy dla programu AzCopy. Ustaw tę zmienną na adres IP serwera proxy i numer portu serwera proxy. Na przykład `xx.xxx.xx.xxx:xx`. Jeśli uruchomisz narzędzie AzCopy w systemie Windows, narzędzie AzCopy automatycznie wykryje ustawienia serwera proxy, dlatego w systemie Windows nie trzeba używać tego ustawienia. Jeśli zdecydujesz się użyć tego ustawienia w systemie Windows, przesłoni ono automatyczne wykrywanie. Zobacz [Konfigurowanie ustawień serwera proxy](#configure-proxy-settings) |


## <a name="configure-proxy-settings"></a>Konfigurowanie ustawień serwera proxy

Aby skonfigurować ustawienia serwera proxy dla programu AzCopy, ustaw `HTTPS_PROXY` zmienną środowiskową . Jeśli uruchomisz narzędzie AzCopy w systemie Windows, narzędzie AzCopy automatycznie wykryje ustawienia serwera proxy, dlatego w systemie Windows nie trzeba używać tego ustawienia. Jeśli zdecydujesz się użyć tego ustawienia w systemie Windows, przesłoni ono automatyczne wykrywanie.

| System operacyjny | Polecenie  |
|--------|-----------|
| **Windows** | W wierszu polecenia użyj polecenia: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> W programie PowerShell użyj polecenia: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Obecnie program AzCopy nie obsługuje serwerów proxy, które wymagają uwierzytelniania przy użyciu protokołu NTLM lub Kerberos.

### <a name="bypassing-a-proxy"></a>Pomijanie serwera proxy

Jeśli używasz programu AzCopy w systemie Windows i chcesz poinformować go, aby w ogóle nie używać serwera _proxy_ (zamiast automatycznego wykrywania ustawień), użyj tych poleceń. W przypadku tych ustawień usługa AzCopy nie będzie szukać żadnego serwera proxy ani go używać.

| System operacyjny | Środowisko | Polecenia  |
|--------|-----------|----------|
| **Windows** | Wiersz polecenia (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

W innych systemach operacyjnych po prostu pozostaw zmienną HTTPS_PROXY nieustawienia, jeśli chcesz nie używać serwera proxy.

## <a name="see-also"></a>Zobacz też

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Optymalizowanie wydajności narzędzia AzCopy w wersji 10 przy użyciu usługi Azure Storage](storage-use-azcopy-optimize.md)
- [Rozwiązywanie problemów z programem AzCopy w wersji 10 w usłudze Azure Storage przy użyciu plików dziennika](storage-use-azcopy-configure.md)
