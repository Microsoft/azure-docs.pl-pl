---
title: Informacje Azure Key Vault tajne — Azure Key Vault
description: Omówienie Azure Key Vault tajnych.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 6d4f3f744a85c14c42ffef1c894b237081e871f8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752430"
---
# <a name="about-azure-key-vault-secrets"></a>Informacje o wpisach tajnych usługi Azure Key Vault

[Key Vault](../general/overview.md) bezpieczne przechowywanie ogólnych wpisów tajnych, takich jak hasła i parametry połączenia bazy danych.

Z perspektywy dewelopera interfejsy API Key Vault akceptować i zwracać wartości tajnych jako ciągi. Wewnętrznie Key Vault wpisy tajne i zarządza nimi jako sekwencjami oktetów (8-bitowych bajtów) o maksymalnym rozmiarze 25 tys. bajtów. Usługa Key Vault nie zapewnia semantyki wpisów tajnych. Jedynie akceptuje dane, szyfruje je, przechowuje i zwraca identyfikator klucza tajnego ("id"). Identyfikator może służyć do pobierania klucza tajnego w późniejszym czasie.  

W przypadku wysoce poufnych danych klienci powinni rozważyć zastosowanie dodatkowych warstw ochrony danych. Jednym z przykładów jest szyfrowanie danych przy użyciu oddzielnego klucza ochrony w celu przechowywania ich w usłudze Key Vault.  

Key Vault obsługuje również pole contentType dla wpisów tajnych. Klienci mogą określać typ zawartości wpisów tajnych, aby pomóc w interpretowaniu danych wpisów tajnych podczas ich pobierania. Maksymalna długość tego pola to 255 znaków. Nie ma wstępnie zdefiniowanych wartości. Sugerowane użycie jest wskazówką do interpretowania tajnych danych. Na przykład implementacja może przechowywać hasła i certyfikaty jako wpisy tajne, a następnie używać tego pola do rozróżnienia. Nie ma żadnych wstępnie zdefiniowanych wartości.  

## <a name="encryption"></a>Szyfrowanie

Wszystkie wpisy tajne w Key Vault są przechowywane w postaci zaszyfrowanej. To szyfrowanie jest przezroczyste i nie wymaga żadnej akcji ze strony użytkownika. Usługa Azure Key Vault szyfruje twoje wpisy tajne podczas dodawania ich i odszyfrowuje je automatycznie podczas ich odczytywania. Klucz szyfrowania jest unikatowy dla każdego magazynu kluczy.

## <a name="secret-attributes"></a>Atrybuty tajne

Oprócz danych tajnych można określić następujące atrybuty:  

- *exp*: IntDate, optional, default is **forever**. Atrybut *exp* (czas wygaśnięcia) identyfikuje czas wygaśnięcia w dniu lub po upływie którego NIE NALEŻY pobierać danych tajnych, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations). To pole służy **wyłącznie do celów** informacyjnych, ponieważ informuje użytkowników usługi Key Vault o tym, że nie można użyć określonego klucza tajnego. Jej wartość MUSI być liczbą zawierającą wartość IntDate.   
- *nbf:* IntDate, optional, default is **now**. Atrybut *nbf* (nie przed) identyfikuje czas, przed którym NIE NALEŻY pobierać danych tajnych, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations). To pole jest tylko **do celów** informacyjnych. Jej wartość MUSI być liczbą zawierającą wartość IntDate. 
- *enabled*: wartość logiczna, opcjonalna, wartość domyślna to **true.** Ten atrybut określa, czy można pobrać dane tajne. Atrybut enabled jest używany w połączeniu z *nbf* i *exp,* gdy operacja występuje między *nbf* i *exp*, będzie dozwolone tylko wtedy, gdy włączone jest ustawiona na **true**. Operacje poza *oknem nbf* i *exp* są automatycznie niedozwolone, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations).  

Istnieją dodatkowe atrybuty tylko do odczytu, które są zawarte w każdej odpowiedzi, która zawiera atrybuty tajne:  

- *created*: IntDate, optional (Opcjonalnie). Utworzony atrybut wskazuje, kiedy ta wersja tajnego została utworzona. Ta wartość ma wartość null dla wpisów tajnych utworzonych przed dodaniem tego atrybutu. Jej wartość musi być liczbą zawierającą wartość IntDate.  
- *updated*: IntDate, optional. Zaktualizowany atrybut wskazuje, kiedy ta wersja tajnego została zaktualizowana. Ta wartość ma wartość null dla wpisów tajnych, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jej wartość musi być liczbą zawierającą wartość IntDate.

Aby uzyskać informacje na temat typowych atrybutów dla każdego typu obiektu magazynu kluczy, zobacz [Azure Key Vault kluczy,](../general/about-keys-secrets-certificates.md) wpisów tajnych i certyfikatów — omówienie

### <a name="date-time-controlled-operations"></a>Operacje sterowane datą/godziną

Operacja get wpisów **tajnych** będzie działać dla nieuważnych i wygasłych wpisów tajnych poza *oknem exp nbf.*  /   Do celów testowych można użyć wywołania operacji **get** dla nieuzyskanego jeszcze tajnego. Pobieranie (pobieranie **ting)** wygasłego tajnego, może służyć do operacji odzyskiwania.

## <a name="secret-access-control"></a>Kontrola dostępu do wpisów tajnych

Access Control wpisów tajnych zarządzanych w Key Vault, jest dostarczany na poziomie Key Vault, który zawiera te wpisy tajne. Zasady kontroli dostępu dla wpisów tajnych różnią się od zasad kontroli dostępu dla kluczy w tym samym Key Vault. Użytkownicy mogą tworzyć co najmniej jeden magazyn do przechowywania wpisów tajnych i są zobowiązani do obsługi odpowiedniej segmentacji wpisów tajnych i zarządzania nimi w scenariuszu.   

Następujące uprawnienia mogą być używane dla każdego podmiotu zabezpieczeń we wpisie kontroli dostępu wpisów tajnych w magazynie i ściśle dublować operacje dozwolone w obiekcie wpisu tajnego:  

- Uprawnienia do operacji zarządzania kluczami tajnymi
  - *get*: Odczytywanie tajnego  
  - *list:* lista wpisów tajnych lub wersji wpisów tajnych przechowywanych w Key Vault  
  - *set*: Tworzenie tajnego  
  - *delete*: usuwanie tajnego  
  - *odzyskiwanie:* odzyskiwanie usuniętego tajnego
  - *kopia zapasowa:* tworzenie kopii zapasowej klucza tajnego w magazynie kluczy
  - *przywracanie:* przywracanie kopii zapasowej klucza tajnego do magazynu kluczy

- Uprawnienia do operacji uprzywilejowanych
  - *przeczyszczanie:* przeczyszczanie (trwałe usuwanie) usuniętego tajnego

Aby uzyskać więcej informacji na temat pracy z wpisami tajnymi, zobacz [Secret operations (Operacje](/rest/api/keyvault)na wpisach tajnych) w Key Vault API REST . Aby uzyskać informacje na temat ustanawiania uprawnień, zobacz [Magazyny — tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — aktualizowanie zasad dostępu.](/rest/api/keyvault/vaults/updateaccesspolicy) 

Przewodniki z instrukcje dotyczące kontrolowania dostępu w Key Vault:
- [Przypisywanie zasad dostępu Key Vault przy użyciu interfejsu wiersza polecenia](../general/assign-access-policy-cli.md)
- [Przypisywanie zasad Key Vault dostępu przy użyciu programu PowerShell](../general/assign-access-policy-powershell.md)
- [Przypisywanie Key Vault dostępu przy użyciu Azure Portal](../general/assign-access-policy-portal.md)
- [Zapewnianie dostępu do Key Vault, certyfikatów i wpisów tajnych przy użyciu kontroli dostępu opartej na rolach platformy Azure](../general/rbac-guide.md)

## <a name="secret-tags"></a>Tagi tajnych  
Dodatkowe metadane specyficzne dla aplikacji można określić w postaci tagów. Key Vault obsługuje do 15 tagów, z których każdy może mieć nazwę 256 znaków i wartość 256 znaków.  

>[!Note]
>Tagi są czytelne dla wywołującego, jeśli ma *listę* lub *uprawnienie do uzyskania.*

## <a name="azure-storage-account-key-management"></a>Zarządzanie kluczami konta usługi Azure Storage

Key Vault zarządzać [kluczami konta usługi Azure Storage:](../../storage/common/storage-account-overview.md)

- Wewnętrznie Key Vault listę kluczy (synchronizację) przy użyciu konta usługi Azure Storage. 
- Key Vault okresowo ponownie generuje (obraca) klucze.
- Wartości klucza nigdy nie są zwracane w odpowiedzi na wywołanie.
- Key Vault zarządza kluczami kont magazynu i klasycznych kont magazynu.

Aby uzyskać więcej informacji, zobacz:
- [Klucze dostępu konta magazynu](../../storage/common/storage-account-keys-manage.md)
- [Zarządzanie kluczami konta magazynu w Azure Key Vault](../secrets/overview-storage-keys.md))


## <a name="storage-account-access-control"></a>Kontrola dostępu do konta magazynu

Podczas autoryzowania użytkownika lub jednostki aplikacji do wykonywania operacji na zarządzanym koncie magazynu można użyć następujących uprawnień:  

- Uprawnienia dla zarządzanego konta magazynu i operacji saS-definition
  - *get*: pobiera informacje o koncie magazynu 
  - *list:* Lista kont magazynu zarządzanych przez Key Vault
  - *aktualizacja:* aktualizowanie konta magazynu
  - *delete*: usuwanie konta magazynu  
  - *odzyskiwanie:* odzyskiwanie usuniętego konta magazynu
  - *kopia* zapasowa: tworzenie kopii zapasowej konta magazynu
  - *przywracanie:* przywracanie kopii zapasowej konta magazynu do Key Vault
  - *set*: Tworzenie lub aktualizowanie konta magazynu
  - *Regeneratekey:* wygeneruj ponownie określoną wartość klucza dla konta magazynu
  - *getsas:* uzyskiwanie informacji o definicji sygnatury dostępu współdzielonego dla konta magazynu
  - *listsas:* Lista definicji sygnatury dostępu współdzielonego magazynu dla konta magazynu
  - *deletesas:* usuwanie definicji sygnatury dostępu współdzielonego z konta magazynu
  - *setsas:* tworzenie lub aktualizowanie nowej definicji/atrybutów sygnatury dostępu współdzielonego dla konta magazynu

- Uprawnienia do operacji uprzywilejowanych
  - *przeczyszczanie:* przeczyszczanie (trwałe usuwanie) zarządzanego konta magazynu

Aby uzyskać więcej informacji, zobacz [operacje konta magazynu w te Key Vault api REST](/rest/api/keyvault). Aby uzyskać informacje na temat ustanawiania uprawnień, zobacz [Magazyny — tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — aktualizowanie zasad dostępu.](/rest/api/keyvault/vaults/updateaccesspolicy)

Przewodniki z instrukcje dotyczące kontrolowania dostępu w Key Vault:
- [Przypisywanie zasad dostępu Key Vault przy użyciu interfejsu wiersza polecenia](../general/assign-access-policy-cli.md)
- [Przypisywanie zasad Key Vault dostępu przy użyciu programu PowerShell](../general/assign-access-policy-powershell.md)
- [Przypisywanie Key Vault dostępu przy użyciu Azure Portal](../general/assign-access-policy-portal.md)
- [Zapewnianie dostępu do Key Vault, certyfikatów i wpisów tajnych przy użyciu kontroli dostępu opartej na rolach platformy Azure](../general/rbac-guide.md)


## <a name="next-steps"></a>Następne kroki

- [Informacje o usłudze Key Vault](../general/overview.md)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](../general/about-keys-secrets-certificates.md)
- [Informacje o kluczach](../keys/about-keys.md)
- [Informacje o certyfikatach](../certificates/about-certificates.md)
- [Bezpieczny dostęp do magazynu kluczy](../general/security-overview.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)