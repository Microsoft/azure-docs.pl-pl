---
title: Informacje o kluczach tajnych Azure Key Vault — Azure Key Vault
description: Omówienie Azure Key Vault interfejsu REST i szczegółów dla deweloperów dla wpisów tajnych.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 7aa2feba5a2b2fa47bbb0c055a2f556b8997ab34
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "82930475"
---
# <a name="about-azure-key-vault-secrets"></a>Informacje o kluczach tajnych Azure Key Vault

Key Vault zapewnia bezpieczny magazyn wpisów tajnych, takich jak hasła i parametry połączenia bazy danych.

Z perspektywy deweloperów Key Vault interfejsy API akceptują i zwracają wartości tajne jako ciągi. Wewnętrznie program Key Vault przechowuje klucze tajne i zarządza nimi jako sekwencje oktetów (8-bitowych bajtów), a każdy z nich ma maksymalny rozmiar 25k bajtów. Usługa Key Vault nie zapewnia semantyki dla wpisów tajnych. Tylko akceptuje dane, szyfruje je, zapisuje i zwraca identyfikator tajny ("ID"). Identyfikatora można użyć do pobrania klucza tajnego w późniejszym czasie.  

W przypadku wysoce poufnych danych klienci powinni rozważyć zastosowanie dodatkowych warstw ochrony danych. Jednym z przykładów jest szyfrowanie danych przy użyciu oddzielnego klucza ochrony w celu przechowywania ich w usłudze Key Vault.  

Key Vault obsługuje również pole ContentType dla wpisów tajnych. Klienci mogą określić typ zawartości wpisu tajnego, aby pomóc w interpretacji danych tajnych podczas pobierania. Maksymalna długość tego pola to 255 znaków. Brak wstępnie zdefiniowanych wartości. Sugerowane użycie jest wskazówką dotyczącą interpretacji danych tajnych. Na przykład implementacja może przechowywać hasła i certyfikaty jako wpisy tajne, a następnie używać tego pola do rozróżniania. Brak wstępnie zdefiniowanych wartości.  

## <a name="encryption"></a>Szyfrowanie

Wszystkie wpisy tajne w Key Vault są przechowywane w postaci zaszyfrowanej. To szyfrowanie jest przezroczyste i nie wymaga żadnych działań od użytkownika. Usługa Azure Key Vault szyfruje wpisy tajne po ich dodaniu i odszyfrowuje je automatycznie podczas ich odczytywania. Klucz szyfrowania jest unikatowy dla każdego magazynu kluczy.

## <a name="secret-attributes"></a>Atrybuty wpisu tajnego

Oprócz danych tajnych można określić następujące atrybuty:  

- *EXP*: IntDate, opcjonalne, wartość domyślna to **nieskończoność**. Atrybut *EXP* (czas wygaśnięcia) określa czas wygaśnięcia lub po którym dane tajne nie powinny być pobierane, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations). To pole służy tylko do celów **informacyjnych** , ponieważ informuje użytkowników usługi magazynu kluczy, że nie można użyć określonego klucza tajnego. Wartość musi być liczbą zawierającą wartość IntDate.   
- *NBF*: IntDate, opcjonalnie, domyślnie jest **teraz**. Atrybut *NBF* (nie wcześniej) określa czas, po którym dane tajne nie powinny być pobierane, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations). To pole służy tylko do celów **informacyjnych** . Wartość musi być liczbą zawierającą wartość IntDate. 
- *włączone*: wartość logiczna, opcjonalna, **wartość**domyślna to true. Ten atrybut określa, czy można pobrać dane tajne. Atrybut Enabled jest używany w połączeniu z *NBF* i *EXP* , gdy operacja przejdzie między *NBF* i *EXP*, będzie dozwolona tylko wtedy, gdy ustawienie Enabled ma **wartość true**. Operacje poza oknem *NBF* i *EXP* są automatycznie niedozwolone, z wyjątkiem [określonych sytuacji](#date-time-controlled-operations).  

Istnieją dodatkowe atrybuty tylko do odczytu, które znajdują się w dowolnej odpowiedzi zawierającej atrybuty tajne:  

- *utworzono*: IntDate, opcjonalnie. Utworzony atrybut wskazuje, kiedy ta wersja wpisu tajnego została utworzona. Ta wartość jest zerowa dla wpisów tajnych utworzonych przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.  
- *Zaktualizowano*: IntDate, opcjonalnie. Zaktualizowany atrybut wskazuje, kiedy ta wersja wpisu tajnego została zaktualizowana. Ta wartość jest zerowa dla wpisów tajnych, które były ostatnio aktualizowane przed dodaniem tego atrybutu. Wartość musi być liczbą zawierającą wartość IntDate.

### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Operacja **pobrania** wpisu tajnego będzie działała w przypadku nieprawidłowych i wygasłych wpisów tajnych *nbf*poza  /  oknem*wygaśnięcia* protokołu NBF. Wywoływanie operacji **Get** wpisu tajnego dla nieprawidłowego hasła tajnego może być używane do celów testowych. Pobieranie (**pobieranie**) wygasłego klucza tajnego może być używane na potrzeby operacji odzyskiwania.

## <a name="secret-access-control"></a>Kontrola dostępu do wpisów tajnych

Access Control dla wpisów tajnych zarządzanych w programie Key Vault jest dostępny na poziomie Key Vault, który zawiera te klucze tajne. Zasady kontroli dostępu dla wpisów tajnych, różnią się od zasad kontroli dostępu dla kluczy w tym samym Key Vault. Użytkownicy mogą utworzyć jeden lub więcej magazynów w celu przechowywania wpisów tajnych i muszą utrzymywać odpowiednie segmentacje i zarządzanie wpisami tajnymi.   

Następujące uprawnienia mogą być używane dla każdego podmiotu zabezpieczeń w ramach wpisu kontroli dostępu tajnego w magazynie i ściśle duplikaty operacji dozwolonych na obiekcie tajnym:  

- Uprawnienia do operacji zarządzania kluczami tajnymi
  - *pobieranie*: odczytywanie wpisu tajnego  
  - *Lista*: wyświetlenie wpisów tajnych lub wersji wpisu Secret przechowywanych w Key Vault  
  - *Ustaw*: Utwórz klucz tajny  
  - *usuwanie*: Usuwanie wpisu tajnego  
  - *Odzyskaj*: Odzyskaj usunięty klucz tajny
  - *kopia zapasowa*: Tworzenie kopii zapasowej wpisu tajnego w magazynie kluczy
  - *przywracanie*: Przywracanie wpisu tajnego kopii zapasowej do magazynu kluczy

- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: przeczyszczanie (trwałe usuwanie) usuniętego klucza tajnego

Aby uzyskać więcej informacji na temat pracy z wpisami tajnymi, zobacz [Secret Operations w temacie Informacje o interfejsie API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="secret-tags"></a>Tagi tajne  
W postaci tagów można określić dodatkowe metadane specyficzne dla aplikacji. Key Vault obsługuje do 15 tagów, z których każdy może mieć nazwę znaku 256 i wartość znaku 256.  

>[!Note]
>Tagi są odczytywane przez obiekt wywołujący, jeśli mają uprawnienie *list* lub *Get* .

## <a name="azure-storage-account-key-management"></a>Zarządzanie kluczami konta usługi Azure Storage

Key Vault może zarządzać kluczami konta usługi Azure Storage:

- Wewnętrznie, Key Vault mogą wyświetlać (synchronizować) klucze za pomocą konta usługi Azure Storage. 
- Key Vault regeneruje ponownie klucze (obraca) okresowo.
- Wartości klucza nigdy nie są zwracane w odpowiedzi na obiekt wywołujący.
- Key Vault zarządza kluczami obu kont magazynu i klasycznych kont magazynu.

Aby uzyskać więcej informacji, zobacz [Azure Key Vault klucze kont magazynu](../secrets/overview-storage-keys.md)).

## <a name="storage-account-access-control"></a>Kontrola dostępu do konta magazynu

Podczas autoryzacji użytkownika lub podmiotu zabezpieczeń aplikacji można używać następujących uprawnień do wykonywania operacji na zarządzanym koncie magazynu:  

- Uprawnienia do zarządzanego konta magazynu i operacji w definicji sygnatury dostępu współdzielonego
  - *Get*: Pobiera informacje o koncie magazynu 
  - *Lista*: Wyświetl listę kont magazynu zarządzanych przez Key Vault
  - *Aktualizacja*: aktualizowanie konta magazynu
  - *usuwanie*: usuwanie konta magazynu  
  - *odzyskiwanie*: Odzyskiwanie usuniętego konta magazynu
  - *kopia zapasowa*: Tworzenie kopii zapasowej konta magazynu
  - *przywracanie*: Przywracanie kopii zapasowej konta magazynu do Key Vault
  - *zestaw*: Tworzenie lub aktualizowanie konta magazynu
  - *regeneratekey*: Wygeneruj ponownie określoną wartość klucza dla konta magazynu
  - *getsas*: Uzyskaj informacje na temat definicji sygnatury dostępu współdzielonego dla konta magazynu
  - *listsas*: Wyświetl definicje sygnatury dostępu współdzielonego magazynu dla konta magazynu
  - *deletesas*: usuwanie definicji sygnatury dostępu współdzielonego z konta magazynu
  - *setsas*: Utwórz lub zaktualizuj nowe definicje/atrybuty sygnatury dostępu współdzielonego dla konta magazynu

- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: przeczyszczanie (trwałe usuwanie) zarządzanego konta magazynu

Aby uzyskać więcej informacji, zobacz [operacje związane z kontem magazynu w temacie Informacje o interfejsie API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Następne kroki

- [Informacje o usłudze Key Vault](../general/overview.md)
- [Informacje o kluczach, wpisach tajnych i certyfikatach](../general/about-keys-secrets-certificates.md)
- [Informacje o kluczach](../keys/about-keys.md)
- [Informacje o certyfikatach](../certificates/about-certificates.md)
- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
