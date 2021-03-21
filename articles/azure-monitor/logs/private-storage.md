---
title: Korzystanie z kont magazynu zarządzanych przez klienta w usłudze Azure Monitor Log Analytics
description: Korzystanie z własnego konta magazynu na potrzeby scenariuszy Log Analytics
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 69b5927c73dac14c76b94a4ee5bbb21449f8ec98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047457"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Korzystanie z kont magazynu zarządzanych przez klienta w usłudze Azure Monitor Log Analytics

Log Analytics opiera się na usłudze Azure Storage w różnych scenariuszach. To użycie jest zazwyczaj zarządzane automatycznie. Jednak niektóre przypadki wymagają podania konta magazynu i zarządzania nim, nazywanego również kontem magazynu zarządzanym przez klienta. W tym dokumencie opisano sposób korzystania z magazynu zarządzanego przez klienta dla dzienników funkcji wad/LAD, prywatnego linku i szyfrowania klucza zarządzanego przez klienta (CMK). 

> [!NOTE]
> Firma Microsoft zaleca, aby nie korzystać z zależności od zawartości Log Analytics przekazanej do magazynu zarządzanego przez klienta, biorąc pod względu na to, że formatowanie i zawartość mogą ulec zmianie.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Pozyskiwanie dzienników rozszerzeń Diagnostyka Azure (funkcji wad/LAD)
Agentów rozszerzeń Diagnostyka Azure (nazywanych również funkcji wad i LAD dla agentów systemu Windows i Linux) zbierają różne Dzienniki systemu operacyjnego i przechowują je na koncie magazynu zarządzanym przez klienta. Następnie można pozyskiwać te dzienniki w Log Analytics, aby je przejrzeć i przeanalizować.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Jak zbierać dzienniki rozszerzeń Diagnostyka Azure z konta magazynu
Połącz konto magazynu z obszarem roboczym Log Analytics jako źródło danych magazynu przy użyciu [Azure Portal](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage) lub przez wywołanie [interfejsu API usługi Storage Insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Obsługiwane typy danych:
* Dziennik systemu
* Zdarzenia systemu Windows
* Service Fabric
* Zdarzenia ETW
* Dzienniki usług IIS

## <a name="using-private-links"></a>Używanie linków prywatnych
Konta magazynu zarządzane przez klienta są używane do pozyskiwania dzienników niestandardowych lub dzienników usług IIS, gdy używane są linki prywatne do łączenia się z zasobami Azure Monitor. Proces pozyskiwania tych typów danych najpierw przekazuje dzienniki do pośredniego konta usługi Azure Storage, a następnie przekazuje je do obszaru roboczego. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Używanie konta magazynu zarządzanego przez klienta za pośrednictwem prywatnego linku
#### <a name="workspace-requirements"></a>Wymagania dotyczące obszaru roboczego
Podczas nawiązywania połączenia z Azure Monitor za pośrednictwem prywatnego linku Agenty Log Analytics mogą wysyłać dzienniki tylko do obszarów roboczych dostępnych za pośrednictwem prywatnego linku. To wymaganie oznacza:
* Skonfiguruj Azure Monitor obiekt zakresu linków prywatnych (AMPLS)
* Połącz je z obszarami roboczymi
* Połącz AMPLS z siecią za pośrednictwem prywatnego linku. 

Aby uzyskać więcej informacji na temat procedury konfiguracji AMPLS, zobacz [bezpieczne łączenie sieci do Azure monitor za pomocą prywatnego linku platformy Azure](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Wymagania konta magazynu
Aby konto magazynu zostało pomyślnie połączone z prywatnym łączem, musi:
* Znajdować się w sieci wirtualnej lub komunikacji równorzędnej i połączony z siecią wirtualną za pośrednictwem prywatnego linku.
* Znajdować się w tym samym regionie, w którym znajduje się obszar roboczy, z którym jest połączony.
* Zezwól Azure Monitor na dostęp do konta magazynu. Jeśli wybrano opcję zezwalania tylko wybranym sieci na dostęp do konta magazynu, należy wybrać wyjątek: "Zezwalaj na dostęp do tego konta magazynu zaufanym usługom firmy Microsoft".
![Obraz usług z zaufaniem do konta magazynu](./media/private-storage/storage-trust.png)
* Jeśli obszar roboczy obsługuje również ruch z innych sieci, należy skonfigurować konto magazynu tak, aby zezwalało na ruch przychodzący z odpowiednich sieci/Internetu.
* Koordynuj wersję protokołu TLS między agentami i kontem magazynu — zalecamy wysyłanie danych do Log Analytics przy użyciu protokołu TLS 1,2 lub nowszego. Przejrzyj [wskazówki dotyczące platformy](https://docs.microsoft.com/azure/azure-monitor/logs/data-security#sending-data-securely-using-tls-12)i w razie potrzeby [Skonfiguruj agentów do korzystania z protokołu TLS 1,2](https://docs.microsoft.com/azure/azure-monitor/agents/agent-windows#configure-agent-to-use-tls-12). Jeśli z jakiegoś powodu nie jest możliwe, należy skonfigurować konto magazynu w celu akceptowania protokołu TLS 1,0.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Używanie konta magazynu zarządzanego przez klienta do szyfrowania danych CMK
Usługa Azure Storage szyfruje wszystkie dane przechowywane na koncie magazynu. Domyślnie do szyfrowania danych są używane klucze zarządzane przez firmę Microsoft (MMK). Jednak usługa Azure Storage umożliwia również korzystanie z usługi CMK z magazynu kluczy Azure do szyfrowania danych magazynu. Możesz zaimportować własne klucze do Azure Key Vault lub użyć interfejsów API Azure Key Vault do wygenerowania kluczy.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Scenariusze CMK, które wymagają konta magazynu zarządzanego przez klienta
* Szyfrowanie zapytań alertów dziennika za pomocą CMK
* Szyfrowanie zapisanych zapytań za pomocą CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Jak zastosować CMK do kont magazynu zarządzanych przez klienta
##### <a name="storage-account-requirements"></a>Wymagania konta magazynu
Konto magazynu i Magazyn kluczy muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach. Aby uzyskać więcej informacji na temat szyfrowania i zarządzania kluczami usługi Azure Storage, zobacz [szyfrowanie usługi Azure Storage dla danych przechowywanych w spoczynku](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Zastosuj CMK do kont magazynu
Aby skonfigurować konto usługi Azure Storage do korzystania z usługi CMK z Azure Key Vault, użyj [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)lub [interfejsu wiersza polecenia](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Łączenie kont magazynu z obszarem roboczym Log Analytics
### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal
Na Azure Portal Otwórz menu obszar roboczy, a następnie wybierz pozycję *połączone konta magazynu*. Zostanie otwarty blok pokazujący połączone konta magazynu według przypadków użycia wymienionych powyżej (pozyskiwanie za pośrednictwem prywatnego linku, stosowanie CMK do zapisanych zapytań lub alertów).
![Obraz bloku połączone konta magazynu ](./media/private-storage/all-linked-storage-accounts.png) wybranie elementu w tabeli spowoduje otwarcie informacji o jego koncie magazynu, w którym można ustawić lub zaktualizować połączone konto magazynu dla tego typu. 
![Łączenie obrazu bloku konta magazynu możesz ](./media/private-storage/link-a-storage-account-blade.png) użyć tego samego konta dla różnych przypadków użycia, jeśli wolisz.

### <a name="using-the-azure-cli-or-rest-api"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure lub interfejs API REST
Możesz również połączyć konto magazynu z obszarem roboczym za pośrednictwem interfejsu [wiersza polecenia platformy Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) lub [API REST](/rest/api/loganalytics/linkedstorageaccounts).

Odpowiednie wartości DataSourceType to:
* CustomLogs — aby użyć konta magazynu dla dzienników niestandardowych i pozyskiwania dzienników usług IIS
* Zapytanie — aby użyć konta magazynu do przechowywania zapisanych zapytań (wymaganych do szyfrowania CMK)
* Alerty — do przechowywania alertów opartych na dziennikach (wymagane do szyfrowania CMK) przy użyciu konta magazynu


## <a name="managing-linked-storage-accounts"></a>Zarządzanie kontami połączonych magazynów

### <a name="create-or-modify-a-link"></a>Tworzenie lub modyfikowanie linku
Po połączeniu konta magazynu z obszarem roboczym Log Analytics rozpocznie korzystanie z niego zamiast konta magazynu należącego do usługi. Można 
* Rejestrowanie wielu kont magazynu w celu rozłożenia obciążenia dzienników między nimi
* Ponownie Użyj tego samego konta magazynu dla wielu obszarów roboczych

### <a name="unlink-a-storage-account"></a>Odłączanie konta magazynu
Aby zatrzymać korzystanie z konta magazynu, odłącz magazyn od obszaru roboczego. Odłączanie wszystkich kont magazynu z obszaru roboczego oznacza, że Log Analytics spróbuje polegać na kontach magazynu zarządzanych przez usługę. Jeśli sieć ma ograniczony dostęp do Internetu, te magazyny mogą być niedostępne, a żaden scenariusz, który opiera się na magazynie, zakończy się niepowodzeniem.

### <a name="replace-a-storage-account"></a>Zastępowanie konta magazynu
Aby zastąpić konto magazynu używane na potrzeby pozyskiwania,
1.  **Utwórz link do nowego konta magazynu.** Agenci rejestrowania otrzymają zaktualizowaną konfigurację i rozpoczną wysyłanie danych do nowego magazynu. Proces może potrwać kilka minut.
2.  **Następnie odłącz stare konto magazynu, aby agenci zatrzymali zapisywanie na usuniętym koncie.** Proces pozyskiwania przechowuje dane z tego konta do momentu jego pozyskania. Nie usuwaj konta magazynu, dopóki nie zostaną wyświetlone wszystkie dzienniki.

### <a name="maintaining-storage-accounts"></a>Obsługa kont magazynu
#### <a name="manage-log-retention"></a>Zarządzanie przechowywaniem dzienników
W przypadku korzystania z własnego konta magazynu przechowywanie jest możliwe. Log Analytics nie będą usuwać dzienników przechowywanych w magazynie prywatnym. Zamiast tego należy skonfigurować zasady obsługujące obciążenie zgodnie z preferencjami.

#### <a name="consider-load"></a>Rozważ obciążenie
Konta magazynu mogą obsługiwać pewne obciążenie żądań odczytu i zapisu przed rozpoczęciem ograniczania żądań (Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage](../../storage/common/scalability-targets-standard-account.md)). Ograniczanie przepustowości ma wpływ na czas pobierania dzienników. Jeśli konto magazynu jest przeciążone, zarejestruj dodatkowe konto magazynu, aby rozłożyć obciążenie między nimi. W celu monitorowania pojemności i wydajności konta magazynu Sprawdź [szczegółowe informacje w Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Powiązane opłaty
Konta magazynu są rozliczone przez ilość przechowywanych danych, typ magazynu i typ nadmiarowości. Aby uzyskać szczegółowe informacje, zobacz [Cennik usługi Block BLOB](https://azure.microsoft.com/pricing/details/storage/blobs) i [Cennik Table Storage](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [korzystaniu z prywatnego linku platformy Azure w celu bezpiecznego łączenia sieci z Azure monitor](private-link-security.md)
- Informacje o [Azure monitor kluczy zarządzanych przez klienta](../logs/customer-managed-keys.md)
