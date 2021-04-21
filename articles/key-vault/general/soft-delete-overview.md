---
title: Azure Key Vault usuwania nie | Microsoft Docs
description: Usuwanie nie soft -Azure Key Vault umożliwia odzyskanie usuniętych magazynów kluczy i obiektów magazynu kluczy, takich jak klucze, wpisy tajne i certyfikaty.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.openlocfilehash: 52cd7742f3c6961350f907cde8ffe19235cff9b8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753258"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault — omówienie usuwania nietrwałego

> [!IMPORTANT]
> Należy natychmiast włączyć usuwanie nie soft w magazynach kluczy. Możliwość rezygnacji z usuwania nie programowego zostanie wkrótce wycofana. Szczegółowe informacje można znaleźć [tutaj](soft-delete-change.md)

> [!IMPORTANT]
> Magazyn nieużytkowo usunięty wyzwala ustawienia usuwania zintegrowane z usługami Key Vault, tj. przypisania ról RBAC platformy Azure, Event Grid subskrypcje. Po odzyskaniu usuniętych nie Key Vault dla zintegrowanych usług należy ręcznie utworzyć ponownie. 

Key Vault usuwania nie programowego umożliwia odzyskiwanie usuniętych magazynów i usuniętych obiektów magazynu kluczy (na przykład kluczy, wpisów tajnych, certyfikatów), znanych jako usuwanie nie soft-delete. W szczególności osłaniamy następujące scenariusze: To zabezpieczenie zapewnia następujące zabezpieczenia:

- Usunięcie klucza tajnego, klucza, certyfikatu lub magazynu kluczy pozostanie możliwe do odzyskania przez konfigurowalny okres od 7 do 90 dni kalendarzowych. Jeśli nie określono konfiguracji, domyślny okres odzyskiwania zostanie ustawiony na 90 dni. Dzięki temu użytkownicy mają wystarczająco dużo czasu, aby zauważyć przypadkowe usunięcie tajnego pliku i zareagować na nie.
- Aby trwale usunąć klucz tajny, należy wykonać dwie operacje. Najpierw użytkownik musi usunąć obiekt , co powoduje jego stan nieużytkowo usunięte. Po drugie użytkownik musi przeczyścić obiekt w stanie usunięcia nieukreślone. Operacja przeczyszczania wymaga dodatkowych uprawnień zasad dostępu. Te dodatkowe zabezpieczenia zmniejszają ryzyko przypadkowego lub złośliwego usunięcia wpisów tajnych lub magazynu kluczy przez użytkownika.  
- Aby przeczyścić klucz tajny w stanie usunięcia nieukreślonego, należy przyznać jednostki usługi dodatkowe uprawnienie zasad dostępu "przeczyszczania". Uprawnienie zasad dostępu przeczyszczania nie jest przyznawane domyślnie żadnej jednostki usługi, w tym właścicielom magazynu kluczy i subskrypcji, i musi zostać celowo ustawione. Wymaganie podwyższonego poziomu uprawnień zasad dostępu w celu przeczyszczania nieumyślnego usunięcia tajnego zmniejsza prawdopodobieństwo przypadkowego usunięcia wpisów tajnych.

## <a name="supporting-interfaces"></a>Interfejsy obsługi

Funkcja usuwania nie soft-delete jest dostępna za pośrednictwem interfejsu [API REST,](/rest/api/keyvault/)interfejsu wiersza polecenia platformy [Azure,](./key-vault-recovery.md) [interfejsu Azure PowerShell](./key-vault-recovery.md), i [.NET/C#](/dotnet/api/microsoft.azure.keyvault) oraz szablonów [usługi ARM.](/azure/templates/microsoft.keyvault/2019-09-01/vaults)

## <a name="scenarios"></a>Scenariusze

Magazyny Azure Key Vault to śledzone zasoby zarządzane przez Azure Resource Manager. Azure Resource Manager określa również dobrze zdefiniowane zachowanie usuwania, które wymaga pomyślnej operacji DELETE, aby ten zasób był już niedostępny. Funkcja usuwania nieumyślnego usuwa odzyskiwanie usuniętego obiektu bez względu na to, czy usunięcie zostało przypadkowe, czy zamierzone.

1. W typowym scenariuszu użytkownik mógł przypadkowo usunąć magazyn kluczy lub obiekt magazynu kluczy; Jeśli ten magazyn kluczy lub obiekt magazynu kluczy będzie można odzyskać przez wstępnie określony okres, użytkownik może cofnąć usunięcie i odzyskać swoje dane.

2. W innym scenariuszu nieautoryzowany użytkownik może próbować usunąć magazyn kluczy lub obiekt magazynu kluczy, taki jak klucz wewnątrz magazynu, aby spowodować przerwy w działalności biznesowej. Oddzielenie usunięcia magazynu kluczy lub obiektu magazynu kluczy od rzeczywistego usunięcia danych bazowych może służyć jako środek bezpieczeństwa przez na przykład ograniczenie uprawnień do usuwania danych do innej, zaufanej roli. Takie podejście wymaga kworum dla operacji, która w przeciwnym razie może spowodować natychmiastową utratę danych.

### <a name="soft-delete-behavior"></a>Zachowanie usuwania nietrwałego

Po włączeniu usuwania nie softu zasoby oznaczone jako usunięte zasoby są zachowywane przez określony czas (domyślnie przez 90 dni). Ponadto usługa udostępnia mechanizm odzyskiwania usuniętego obiektu, co zasadniczo cofa usunięcie.

Podczas tworzenia nowego magazynu kluczy usuwanie nie soft-delete jest domyślnie włączone. Magazyn kluczy można utworzyć bez usuwania nie soft-delete za pomocą interfejsu wiersza [polecenia platformy Azure](./key-vault-recovery.md) lub [Azure PowerShell](./key-vault-recovery.md). Po włączeniu usuwania nie soft w magazynie kluczy nie można go wyłączyć

Domyślny okres przechowywania wynosi 90 dni, ale podczas tworzenia magazynu kluczy można ustawić interwał zasad przechowywania na wartość z przedziału od 7 do 90 dni do Azure Portal. Zasady przechowywania ochrony przed przeczyszczaniem wykorzystują ten sam interwał. Ustawionego interwału zasad przechowywania nie można zmienić.

Nie można ponownie użyć nazwy magazynu kluczy, który został usunięty nieukreślony, dopóki nie minął okres przechowywania.

### <a name="purge-protection"></a>Ochrona przed przeczyszczaniem

Ochrona przed przeczyszczaniem jest opcjonalnym Key Vault i **nie jest domyślnie włączona.** Ochronę przed przeczyszczaniem można włączyć tylko po włączeniu usuwania nie programowego.  Można ją włączona za pomocą interfejsu [wiersza polecenia](./key-vault-recovery.md?tabs=azure-cli) lub [programu PowerShell.](./key-vault-recovery.md?tabs=azure-powershell)

Gdy ochrona przed przeczyszczaniem jest wł., magazynu lub obiektu w stanie usunięcia nie można przeczyścić, dopóki nie minął okres przechowywania. Magazyny i obiekty, które nie zostały usunięte, nadal można odzyskać, zapewniając, że zostaną przestrzegane zasady przechowywania.

Domyślny okres przechowywania wynosi 90 dni, ale istnieje możliwość ustawienia interwału zasad przechowywania na wartość od 7 do 90 dni do Azure Portal. Po skonfigurowaniu i zapisaniu interwału zasad przechowywania nie można go zmienić dla tego magazynu.

### <a name="permitted-purge"></a>Dozwolone przeczyszczanie

Trwałe usuwanie, przeczyszczanie magazynu kluczy jest możliwe za pośrednictwem operacji POST na zasobie serwera proxy i wymaga specjalnych uprawnień. Ogólnie rzecz biorąc, tylko właściciel subskrypcji będzie mógł przeczyścić magazyn kluczy. Operacja POST wyzwala natychmiastowe i nieodzyskowalne usunięcie tego magazynu. 

Wyjątki są:
- Gdy subskrypcja platformy Azure zostanie oznaczona jako nie *do cofniania.* W takim przypadku tylko usługa może następnie wykonać rzeczywiste usunięcie i robi to jako zaplanowany proces. 
- Gdy `--enable-purge-protection flag` włączono funkcję w samym magazynie. W takim przypadku Key Vault 90 dni od czasu, gdy oryginalny obiekt tajny został oznaczony do usunięcia, aby trwale usunąć obiekt.

Aby uzyskać instrukcje, zobacz How [to use Key Vault soft-delete with CLI: Purging a key vault](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) (Jak używać usuwania nie soft-delete za pomocą programu PowerShell: przeczyszczanie magazynu kluczy) lub How to use Key Vault [soft-delete with PowerShell: Purging a key vault](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)(Jak używać usuwania nie soft-delete przy użyciu programu PowerShell: przeczyszczanie magazynu kluczy).

### <a name="key-vault-recovery"></a>Odzyskiwanie magazynu kluczy

Po usunięciu magazynu kluczy usługa tworzy zasób serwera proxy w ramach subskrypcji, dodając metadane wystarczające do odzyskania. Zasób serwera proxy jest przechowywanym obiektem dostępnym w tej samej lokalizacji co usunięty magazyn kluczy. 

### <a name="key-vault-object-recovery"></a>Odzyskiwanie obiektu magazynu kluczy

Po usunięciu obiektu magazynu kluczy, takiego jak klucz, usługa umieszcza obiekt w stanie usunięcia, co uniemożliwi jego dostęp do żadnych operacji pobierania. W tym stanie obiekt magazynu kluczy można wymieniać, odzyskiwać lub wymuszać/trwale usuwać. Aby wyświetlić obiekty, użyj polecenia interfejsu wiersza polecenia platformy Azure (zgodnie z opisem w te Key Vault How to use Key Vault soft-delete with CLI ) (Jak używać usuwania nie soft-delete za pomocą interfejsu wiersza polecenia) lub parametru Azure PowerShell (zgodnie z opisem w tesłudze `az keyvault key list-deleted` [](./key-vault-recovery.md) `-InRemovedState` Key Vault [soft-delete with PowerShell](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)).  

W tym samym czasie Key Vault usunięcie danych bazowych odpowiadających usuniętego magazynu kluczy lub obiektowi magazynu kluczy do wykonania po wstępnie określonym interwale przechowywania. Rekord DNS odpowiadający magazynowi jest również zachowywany przez czas trwania okresu przechowywania.

### <a name="soft-delete-retention-period"></a>Okres przechowywania usuwania nie soft-delete

Zasoby usunięte nieu programowo są przechowywane przez określony czas, czyli przez 90 dni. W okresie przechowywania usuwania nie softu obowiązują następujące zasady:

- Możesz wyświetlić listę wszystkich magazynów kluczy i obiektów magazynu kluczy w stanie usuwania nie soft-delete dla subskrypcji, a także uzyskać dostęp do informacji o ich usuwaniu i odzyskiwaniu.
  - Tylko użytkownicy ze specjalnymi uprawnieniami mogą wyświetlić listę usuniętych magazynów. Zalecamy, aby nasi użytkownicy utworzyli rolę niestandardową z tymi specjalnymi uprawnieniami do obsługi usuniętych magazynów.
- Nie można utworzyć magazynu kluczy o tej samej nazwie w tej samej lokalizacji; odpowiednio nie można utworzyć obiektu magazynu kluczy w danym magazynie, jeśli ten magazyn kluczy zawiera obiekt o tej samej nazwie i który jest w stanie usunięcia.
- Tylko specjalnie uprzywilejowany użytkownik może przywrócić magazyn kluczy lub obiekt magazynu kluczy, wydając polecenie odzyskiwania dla odpowiedniego zasobu serwera proxy.
  - Użytkownik, członek roli niestandardowej, który ma uprawnienia do tworzenia magazynu kluczy w grupie zasobów, może przywrócić magazyn.
- Tylko specjalnie uprzywilejowany użytkownik może wymucić usunięcie magazynu kluczy lub obiektu magazynu kluczy, wydając polecenie delete dla odpowiedniego zasobu serwera proxy.

Jeśli magazyn kluczy lub obiekt magazynu kluczy nie zostaną odzyskane, na koniec okresu przechowywania usługa wykonuje przeczyszczanie obiektu magazynu kluczy lub magazynu kluczy usuniętego nie programowo oraz jego zawartości. Usuwanie zasobów może nie być ponownieplanowane.

### <a name="billing-implications"></a>Implikacje dotyczące rozliczeń

Ogólnie rzecz biorąc, gdy obiekt (magazyn kluczy, klucz lub klucz tajny) jest w stanie usunięcia, możliwe są tylko dwie operacje: "przeczyszczanie" i "odzyskiwanie". Wszystkie pozostałe operacje nie powiodą się. W związku z tym, mimo że obiekt istnieje, nie można wykonywać żadnych operacji i dlatego nie wystąpi użycie, więc nie ma rachunku. Istnieją jednak następujące wyjątki:

- Akcje "przeczyszczania" i "odzyskiwania" będą wliczane do zwykłych operacji magazynu kluczy i będą naliczane.
- Jeśli obiekt jest kluczem HSM, opłata za "klucz chroniony przez moduł HSM" za wersję klucza miesięcznie będzie naliczana, jeśli wersja klucza była używana w ciągu ostatnich 30 dni. Następnie, ponieważ obiekt jest w stanie usunięcia, nie można wykonywać żadnych operacji na nim, więc nie będą naliczane żadne opłaty.

## <a name="next-steps"></a>Następne kroki

W poniższych dwóch przewodnikach przedstawiono podstawowe scenariusze użycia usuwania nie programowego.

- [Jak używać usuwania nie Key Vault z portalem](./key-vault-recovery.md?tabs=azure-portal)
- [Jak używać usuwania nietrwałego w usłudze Key Vault z programem PowerShell](./key-vault-recovery.md) 
- [Jak używać usuwania nietrwałego w usłudze Key Vault z interfejsem wiersza polecenia](./key-vault-recovery.md)
