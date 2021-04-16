---
title: azcopy login | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia azcopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4861749d66e466bc3302553af8b3ed4919a72e0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503239"
---
# <a name="azcopy-login"></a>azcopy login

Zaloguj się, aby Azure Active Directory uzyskać dostęp do zasobów usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

Zaloguj się do witryny Azure Active Directory, aby uzyskać dostęp do zasobów usługi Azure Storage.

Aby autoryzować konto usługi Azure Storage, musisz przypisać rolę Współautor danych obiektu blob usługi **Storage** do konta użytkownika w kontekście konta magazynu, nadrzędnej grupy zasobów lub subskrypcji nadrzędnej.

To polecenie spowoduje buforowanie zaszyfrowanych informacji logowania bieżącego użytkownika przy użyciu wbudowanych mechanizmów systemu operacyjnego.

> [!IMPORTANT]
> Jeśli ustawisz zmienną środowiskową przy użyciu wiersza polecenia, ta zmienna będzie można odczytać w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych zawierających poświadczenia z historii wiersza polecenia. Aby nie wyświetlać zmiennych w historii, możesz użyć skryptu, aby monitować użytkownika o poświadczenia i ustawić zmienną środowiskową.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Powiązane artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Samouczek: migrowanie danych lokalnych do magazynu w chmurze za pomocą programu AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Transferowanie danych za pomocą programu AzCopy i usługi Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)

## <a name="examples"></a>Przykłady

Zaloguj się interaktywnie przy użyciu domyślnego identyfikatora dzierżawy usługi AAD ustawionego na typowe:

```azcopy
azcopy login
```

Zaloguj się interaktywnie przy użyciu określonego identyfikatora dzierżawy:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Zaloguj się przy użyciu przypisanej przez system tożsamości maszyny wirtualnej:

```azcopy
azcopy login --identity
```

Zaloguj się przy użyciu tożsamości przypisanej przez użytkownika maszyny wirtualnej i identyfikatora klienta tożsamości usługi:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Zaloguj się przy użyciu tożsamości przypisanej przez użytkownika maszyny wirtualnej i identyfikatora obiektu tożsamości usługi:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Zaloguj się przy użyciu tożsamości przypisanej przez użytkownika maszyny wirtualnej i identyfikatora zasobu tożsamości usługi:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Zaloguj się jako jednostkę usługi przy użyciu wpisów tajnych klienta: ustaw zmienną środowiskową AZCOPY_SPA_CLIENT_SECRET klucz tajny klienta dla uwierzytelniania jednostki usługi opartej na wpisie tajnym.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Zaloguj się jako jednostkę usługi przy użyciu certyfikatu i hasła:

Ustaw zmienną środowiskową AZCOPY_SPA_CERT_PASSWORD na hasło certyfikatu dla uwierzytelniania jednostki usługi opartej na certyfikatach:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Traktuj `/path/to/my/cert` jako ścieżkę do pliku PEM lub PKCS12. Program AzCopy nie dociera do magazynu certyfikatów systemu w celu uzyskania certyfikatu.

`--certificate-path` jest obowiązkowy w przypadku uwierzytelniania jednostki usługi opartej na certyfikatach.

## <a name="options"></a>Opcje

**--aad-endpoint** string Azure Active Directory punkt końcowy do użycia. Wartość domyślna ( https://login.microsoftonline.com) jest poprawna dla globalnej chmury platformy Azure. Ustaw ten parametr podczas uwierzytelniania w chmurze krajowej. Nie jest wymagany dla tożsamości usługi zarządzanej.

**--application-id** ciąg Identyfikator aplikacji tożsamości przypisanej przez użytkownika. Wymagane dla uwierzytelniania jednostki usługi.

**--certificate-path** ciąg Ścieżka do certyfikatu do uwierzytelniania spN. Wymagane w przypadku uwierzytelniania jednostki usługi opartej na certyfikatach.

**--help**   pomoc dla `azcopy login` polecenia.

**--identity**   Zaloguj się przy użyciu tożsamości maszyny wirtualnej, znanej również jako tożsamość usługi zarządzanej (MSI).

**--identity-client-id** ciąg Identyfikator klienta tożsamości przypisanej przez użytkownika.

**--identity-object-id** ciąg identyfikator obiektu tożsamości przypisanej przez użytkownika.

**--identity-resource-id** ciąg identyfikator zasobu tożsamości przypisanej przez użytkownika.

**--service-principal**   Zaloguj się za pośrednictwem głównej nazwy usługi (SPN) przy użyciu certyfikatu lub tajnego. Klucz tajny klienta lub hasło certyfikatu muszą być umieszczone w odpowiedniej zmiennej środowiskowej. Wpisz AzCopy env, aby wyświetlić nazwy i opisy zmiennych środowiskowych.

**--tenant-id** string Identyfikator Azure Active Directory dzierżawy do użycia na użytek interakcyjnego logowania urządzenia OAuth.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--cap-mb/s float|Limituje szybkość transferu w megabitach na sekundę. Przepływność moment po chwili może się nieznacznie różnić od limitu. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie jest ograniczona.|
|--output-type string|Format danych wyjściowych polecenia. Dostępne opcje to: text, json. Wartość domyślna to "text".|
|--trusted-microsoft-suffixes string   |Określa dodatkowe sufiksy domeny, Azure Active Directory tokeny logowania mogą być wysyłane.  Wartość domyślna to '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net". Wszystkie wymienione w tym miejscu są dodawane do wartości domyślnej. Ze względów bezpieczeństwa należy w tym miejscu Microsoft Azure tylko domen. Oddziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz też

- [azcopy](storage-ref-azcopy.md)
