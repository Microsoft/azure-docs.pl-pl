---
title: Wpisy tajne usługi Azure Key Vault — usługa Azure Key Vault
description: Omówienie interfejsu REST usługi Azure Key Vault i szczegóły dewelopera dla wpisów tajnych.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2578f48ce218a0feaa5fb515ebc5d0e7154802ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424265"
---
# <a name="about-azure-key-vault-secrets"></a>Wpisy tajne usługi Azure Key Vault – informacje

Usługa Azure Key Vault umożliwia aplikacjom i użytkownikom platformy Microsoft Azure przechowywanie i używanie kilku typów tajnych danych:

- Wpisy tajne: zapewnia bezpieczne przechowywanie wpisów tajnych, takich jak hasła i parametry połączenia bazy danych.

- Usługa Azure Storage: może zarządzać kluczami konta usługi Azure Storage. Wewnętrznie Usługa Key Vault może wymieniać (synchronizować) klucze za pomocą konta usługi Azure Storage i okresowo ponownie wygenerować (obrócić) klucze. 

Aby uzyskać bardziej ogólne informacje na temat usługi Key Vault, zobacz [Co to jest usługa Azure Key Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault

Poniższe sekcje oferują ogólne informacje mające zastosowanie do implementacji usługi Usługi Usługi Key Vault. 

### <a name="objects-identifiers-and-versioning"></a>Obiekty, identyfikatory i przechowywanie wersji

Obiekty przechowywane w przechowalni kluczy są wersjonatowane przy każdym utworzeniu nowego wystąpienia obiektu. Każdej wersji jest przypisywany unikatowy identyfikator i adres URL. Po utworzeniu obiektu nadana jest unikatowy identyfikator wersji i oznaczona jako bieżąca wersja obiektu. Utworzenie nowego wystąpienia o tej samej nazwie obiektu nadaje nowemu obiektowi unikatowy identyfikator wersji, co powoduje, że staje się on bieżącą wersją.  

Obiekty w magazynie kluczy można rozwiązać przy użyciu bieżącego identyfikatora lub identyfikatora specyficznego dla wersji. Na przykład, biorąc pod `MasterKey`uwagę Klucz o nazwie , wykonywanie operacji z bieżącym identyfikatorem powoduje, że system do korzystania z najnowszej dostępnej wersji. Wykonywanie operacji z identyfikatorem specyficznym dla wersji powoduje, że system używa tej określonej wersji obiektu.  

Obiekty są jednoznacznie identyfikowane w magazynie kluczy przy użyciu adresu URL. Żadne dwa obiekty w systemie nie mają tego samego adresu URL, niezależnie od lokalizacji geograficznej. Pełny adres URL obiektu nosi nazwę identyfikatora obiektu. Adres URL składa się z prefiksu identyfikującego magazyn kluczy, typ obiektu, nazwę obiektu pod warunkiem użytkownika i wersję obiektu. Nazwa obiektu jest niewrażliwa na argumenty i niezmienna. Identyfikatory, które nie zawierają wersji obiektu są określane jako identyfikatory podstawowe.  

Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)

Identyfikator obiektu ma następujący ogólny format:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Gdzie:  

|||  
|-|-|  
|`keyvault-name`|Nazwa magazynu kluczy w usłudze Microsoft Azure Key Vault.<br /><br /> Nazwy magazynu kluczy są wybierane przez użytkownika i są unikatowe globalnie.<br /><br /> Nazwa magazynu kluczy musi być ciągiem znaków 3-24, zawierającym tylko 0-9, a-z, A-Z i -.|  
|`object-type`|Typ obiektu, "klucze" lub "wpisy tajne".|  
|`object-name`|Nazwa `object-name` użytkownika jest nazwą podana przez użytkownika i musi być unikatowa w magazynie kluczy. Nazwa musi być ciągiem znaków 1-127, zawierającym tylko 0-9, a-z, A-Z i -.|  
|`object-version`|An `object-version` jest generowany przez system, 32-znakowy identyfikator ciągu, który jest opcjonalnie używany do adresu unikatową wersję obiektu.|  

## <a name="key-vault-secrets"></a>Wpisy tajne magazynu kluczy 

### <a name="working-with-secrets"></a>Praca z sekretami

Z punktu widzenia dewelopera interfejsy API usługi Key Vault akceptują i zwracają tajne wartości jako ciągi. Wewnętrznie Usługa Key Vault przechowuje i zarządza wpisami tajnymi jako sekwencje oktetów (8-bitowych bajtów), o maksymalnym rozmiarze 25 k bajtów. Usługa Usługi Usługi Przechowalnia kluczy nie zapewnia semantyki wpisów tajnych. Po prostu akceptuje dane, szyfruje je, przechowuje i zwraca tajny identyfikator ("id"). Identyfikator może służyć do pobierania klucza tajnego w późniejszym czasie.  

W przypadku wysoce poufnych danych klienci powinni rozważyć zastosowanie dodatkowych warstw ochrony danych. Jednym z przykładów jest szyfrowanie danych przy użyciu oddzielnego klucza ochrony w celu przechowywania ich w usłudze Key Vault.  

Usługa Key Vault obsługuje również pole contentType dla wpisów tajnych. Klienci mogą określić typ zawartości klucza tajnego, aby pomóc w interpretacji tajnych danych podczas ich pobierania. Maksymalna długość tego pola wynosi 255 znaków. Nie ma wstępnie zdefiniowanych wartości. Sugerowane użycie jest wskazówką do interpretacji tajnych danych. Na przykład implementacja może przechowywać hasła i certyfikaty jako wpisy tajne, a następnie użyć tego pola do rozróżnienia. Nie ma wstępnie zdefiniowanych wartości.  

### <a name="secret-attributes"></a>Atrybuty tajne

Oprócz tajnych danych można określić następujące atrybuty:  

- *exp*: IntDate, opcjonalnie, domyślnie jest **zawsze**. Atrybut *exp* (czas wygaśnięcia) identyfikuje czas wygaśnięcia, po którym tajne dane nie powinny być pobierane, z wyjątkiem [szczególnych sytuacji](#date-time-controlled-operations). To pole służy do celów **informacyjnych** tylko wtedy, gdy informuje użytkowników usługi magazynu kluczy, że określony klucz tajny nie może być używany. Jego wartość MUSI być liczbą zawierającą wartość IntDate.   
- *nbf*: IntDate, opcjonalnie, domyślnie jest **teraz**. Atrybut *nbf* (nie wcześniej) określa czas, przed którym tajne dane NIE powinny być pobierane, z wyjątkiem [szczególnych sytuacji](#date-time-controlled-operations). To pole służy wyłącznie celom **informacyjnym.** Jego wartość MUSI być liczbą zawierającą wartość IntDate. 
- *włączone*: logiczne, opcjonalne, domyślnie jest **prawdziwe**. Ten atrybut określa, czy tajne dane mogą być pobierane. Włączony atrybut jest używany w połączeniu z *nbf* i *exp,* gdy operacja występuje między *nbf* i *exp*, będzie dozwolone tylko wtedy, gdy włączona jest ustawiona na **true**. Operacje poza oknem *nbf* i *exp* są automatycznie niedozwolone, z wyjątkiem [szczególnych sytuacji.](#date-time-controlled-operations)  

Istnieją dodatkowe atrybuty tylko do odczytu, które są zawarte w każdej odpowiedzi, która zawiera atrybuty tajne:  

- *utworzony*: IntDate, opcjonalnie. Utworzony atrybut wskazuje, kiedy ta wersja klucza tajnego została utworzona. Ta wartość jest null dla wpisów tajnych utworzonych przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.  
- *aktualizacja*: IntDate, opcjonalnie. Zaktualizowany atrybut wskazuje, kiedy ta wersja klucza tajnego została zaktualizowana. Ta wartość jest null dla wpisów tajnych, które zostały ostatnio zaktualizowane przed dodaniem tego atrybutu. Jego wartość musi być liczbą zawierającą wartość IntDate.

#### <a name="date-time-controlled-operations"></a>Operacje kontrolowane przez datę i godzinę

Operacja **get** secret będzie działać dla nie-jeszcze ważne i wygasłe wpisy tajne, poza *nbf* / *exp* okna. Wywołanie operacji **get** klucza tajnego, dla nieustałego jeszcze tajnego, może służyć do celów testowych. Pobieranie **(get**ting) wygasły klucz tajny, może służyć do odzyskiwania operacji.

### <a name="secret-access-control"></a>Kontrola dostępu do wpisów tajnych

Kontrola dostępu dla wpisów tajnych zarządzanych w magazynie kluczy jest dostarczana na poziomie magazynu kluczy, który zawiera te wpisy tajne. Zasady kontroli dostępu dla wpisów tajnych, różni się od zasad kontroli dostępu dla kluczy w tym samym Magazynie kluczy. Użytkownicy mogą utworzyć jeden lub więcej magazynów do przechowywania wpisów tajnych i są wymagane do utrzymania scenariusza odpowiedniej segmentacji i zarządzania wpisami tajnymi.   

Następujące uprawnienia mogą być używane, na zasadzie jednostkowej, we wpisie kontroli dostępu do wpisu nieudzielonych wpisów w przechowalni i ściśle dublować operacje dozwolone w obiekcie tajnym:  

- Uprawnienia do tajnych operacji zarządzania
  - *get*: Przeczytaj tajemnicę  
  - *lista*: Lista wpisów tajnych lub wersji klucza tajnego przechowywanego w magazynie kluczy  
  - *zestaw*: Tworzenie klucza tajnego  
  - *delete*: Usuwanie klucza tajnego  
  - *recover*: Odzyskiwanie usuniętego klucza tajnego
  - *kopia zapasowa*: Tworzenie kopii zapasowej klucza tajnego w magazynie kluczy
  - *przywracanie*: Przywracanie kopii zapasowej klucza tajnego do magazynu kluczy

- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyścić*: Przeczyścić (trwale usunąć) usunięty klucz tajny

Aby uzyskać więcej informacji na temat pracy z wpisami tajnymi, zobacz [Operacje tajne w odwołaniu do interfejsu API REST magazynu kluczy](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz [Vaults - Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [przechowalnia — Aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy). 

### <a name="secret-tags"></a>Tajne tagi  
Można określić dodatkowe metadane specyficzne dla aplikacji w postaci tagów. Usługa Key Vault obsługuje maksymalnie 15 znaczników, z których każdy może mieć 256 znaków i wartość 256 znaków.  

>[!Note]
>Tagi są czytelne przez rozmówcę, jeśli mają *listę* lub *uzyskać* pozwolenie.

## <a name="azure-storage-account-key-management"></a>Zarządzanie kluczami konta usługi Azure Storage

Usługa Key Vault może zarządzać kluczami kont magazynu platformy Azure:

- Wewnętrznie usługa Key Vault może wymieniać (synchronizować) klucze z kontem magazynu platformy Azure. 
- Magazyn kluczy okresowo regeneruje (obraca) klawisze.
- Wartości klucza nigdy nie są zwracane w odpowiedzi na wywołującego.
- Usługa Key Vault zarządza kluczami zarówno kont magazynu, jak i klasycznych kont magazynu.

Aby uzyskać więcej informacji, zobacz [Klucze kont usługi Azure Key Storage](../secrets/overview-storage-keys.md))

### <a name="storage-account-access-control"></a>Kontrola dostępu do konta magazynu

Podczas autoryzowania jednostki zabezpieczeń użytkownika lub podmiotu zabezpieczeń aplikacji można używać następujących uprawnień:  

- Uprawnienia dla zarządzanych kont magazynu i operacji definicji SaS
  - *get*: Pobiera informacje o koncie magazynu 
  - *lista*: Lista kont magazynu zarządzanych przez Magazyn kluczy
  - *aktualizacja*: Aktualizowanie konta magazynu
  - *delete*: Usuwanie konta magazynu  
  - *odzyskiwanie*: Odzyskiwanie usuniętego konta magazynu
  - *kopia zapasowa*: Tworzenie kopii zapasowej konta magazynu
  - *przywracanie*: Przywracanie konta magazynu kopii zapasowej do magazynu kluczy
  - *zestaw*: Tworzenie lub aktualizowanie konta magazynu
  - *regeneratekey*: Ponowne generowanie określonej wartości klucza dla konta magazynu
  - *getsas*: Uzyskaj informacje o definicji sygnatury dostępu Współdzielonego dla konta magazynu
  - *listsas*: Lista definicji sygnatury dostępu Współdzielonego magazynu dla konta magazynu
  - *deletesas*: Usuwanie definicji sygnatury dostępu Współdzielonego z konta magazynu
  - *setsas*: Tworzenie lub aktualizowanie nowej definicji/atrybutów sygnatury dostępu Współdzielonego dla konta magazynu

- Uprawnienia dla operacji uprzywilejowanych
  - *przeczyszczanie*: Przeczyszcz (trwale usuń) zarządzane konto magazynu

Aby uzyskać więcej informacji, zobacz [operacje konta magazynu w interfejsie API REST magazynu kluczy](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz [Vaults - Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [przechowalnia — Aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="see-also"></a>Zobacz też

- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
