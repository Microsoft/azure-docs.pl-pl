---
title: AzCopy logowanie | Microsoft Docs
description: Ten artykuł zawiera informacje referencyjne dotyczące polecenia logowania AzCopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878362"
---
# <a name="azcopy-login"></a>azcopy login

Loguje się do Azure Active Directory, aby uzyskać dostęp do zasobów usługi Azure Storage.

## <a name="synopsis"></a>Streszczenie

Zaloguj się do Azure Active Directory, aby uzyskać dostęp do zasobów usługi Azure Storage.

Aby można było autoryzować konto usługi Azure Storage, musisz przypisać rolę **współautor danych obiektów blob magazynu** do konta użytkownika w kontekście konta magazynu, nadrzędnej grupy zasobów lub subskrypcji nadrzędnej.

To polecenie spowoduje buforowanie zaszyfrowanych informacji logowania dla bieżącego użytkownika przy użyciu wbudowanych mechanizmów systemu operacyjnego.

> [!IMPORTANT]
> W przypadku ustawienia zmiennej środowiskowej przy użyciu wiersza polecenia ta zmienna zostanie odczytana w historii wiersza polecenia. Rozważ wyczyszczenie zmiennych, które zawierają poświadczenia z historii wiersza polecenia. Aby zachować zmienne w historii, można użyć skryptu w celu wyświetlenia monitu o podanie poświadczeń użytkownika i ustawienia zmiennej środowiskowej.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Pokrewne artykuły koncepcyjne

- [Wprowadzenie do narzędzia AzCopy](storage-use-azcopy-v10.md)
- [Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Transferowanie danych za pomocą narzędzia AzCopy i magazynu plików](storage-use-azcopy-files.md)
- [Konfigurowanie, optymalizowanie i rozwiązywanie problemów z AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Przykłady

Zaloguj się interaktywnie przy użyciu domyślnego identyfikatora dzierżawy usługi AAD ustawionego na wartość common:

```azcopy
azcopy login
```

Zaloguj się interaktywnie przy użyciu określonego identyfikatora dzierżawy:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Zaloguj się przy użyciu tożsamości przypisanej do systemu maszyny wirtualnej (VM):

```azcopy
azcopy login --identity
```

Zaloguj się przy użyciu tożsamości przypisanej do użytkownika maszyny wirtualnej i identyfikatora klienta tożsamości usługi:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Zaloguj się przy użyciu tożsamości przypisanej do użytkownika maszyny wirtualnej i identyfikatora obiektu tożsamości usługi:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Zaloguj się przy użyciu tożsamości przypisanej do użytkownika maszyny wirtualnej i identyfikatora zasobu tożsamości usługi:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Zaloguj się jako nazwa główna usługi przy użyciu klucza tajnego klienta: Ustaw zmienną środowiskową AZCOPY_SPA_CLIENT_SECRET na wpis tajny klienta dla uwierzytelniania podstawowego usługi opartego na wpisie tajnym.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Zaloguj się jako nazwa główna usługi przy użyciu certyfikatu i hasła:

Ustaw zmienną środowiskową AZCOPY_SPA_CERT_PASSWORD na hasło certyfikatu dla uwierzytelniania nazwy głównej usługi opartej na certyfikatach:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Traktuj `/path/to/my/cert` jako ścieżkę do pliku PEM lub PKCS12. Usługa AzCopy nie dociera do magazynu certyfikatów systemu w celu uzyskania certyfikatu.

`--certificate-path` jest wymagany w przypadku uwierzytelniania jednostki usługi opartej na certyfikatach.

## <a name="options"></a>Opcje

**--AAD-Endpoint** ciąg Azure Active Directory, który ma być używany przez punkt końcowy. Wartość domyślna ( https://login.microsoftonline.com) jest prawidłowa dla globalnej chmury platformy Azure. Ustaw ten parametr podczas uwierzytelniania w chmurze krajowej. Niewymagane w przypadku tożsamość usługi zarządzanej.

**--ciąg identyfikatora aplikacji** dla tożsamości przypisanej do użytkownika. Wymagane dla uwierzytelniania nazwy głównej usługi.

**--** ścieżka ciągu ścieżki certyfikatu do certyfikatu na potrzeby uwierzytelniania SPN. Wymagane dla uwierzytelniania nazwy głównej usługi opartej na certyfikatach.

**--Pomoc**   dla `azcopy login` polecenia.

**--tożsamość**   Zaloguj się przy użyciu tożsamości maszyny wirtualnej, znanej również jako tożsamość usługi zarządzanej (MSI).

**--Identity-Client-ID** ID klienta identyfikatora przypisanego przez użytkownika.

**--Identity-ID —** identyfikator obiektu ciągu tożsamości przypisanej do użytkownika.

**--** identyfikator zasobu ciągu identyfikatora zasobu tożsamości przypisanej do użytkownika.

**--Service-Principal**   Zaloguj się za pomocą głównej nazwy usługi (SPN) za pomocą certyfikatu lub klucza tajnego. Wpis tajny klienta lub hasło certyfikatu muszą być umieszczone w odpowiedniej zmiennej środowiskowej. Wpisz AzCopy ENV, aby zobaczyć nazwy i opisy zmiennych środowiskowych.

**--Identyfikator dzierżawy** identyfikator dzierżawy Azure Active Directory, który ma być używany dla logowania interaktywnego urządzenia OAuth.

## <a name="options-inherited-from-parent-commands"></a>Opcje dziedziczone z poleceń nadrzędnych

|Opcja|Opis|
|---|---|
|--Cap-MB/s|Szybkość transferu w megabitach na sekundę. Przepływność czasu na chwilę może się nieco różnić od końca. Jeśli ta opcja jest ustawiona na zero lub zostanie pominięta, przepływność nie zostanie ograniczona.|
|--ciąg typu wyjściowego|Format danych wyjściowych polecenia. Dostępne opcje to: text, JSON. Wartość domyślna to "text".|
|--Zaufane — ciąg sufiksów firmy Microsoft   |Określa dodatkowe sufiksy domeny, w których mogą być wysyłane Azure Active Directory tokeny logowania.  Wartość domyślna to "*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Wszystkie wymienione tutaj są dodawane do ustawień domyślnych. W celu zapewnienia bezpieczeństwa należy tu umieścić tylko domeny Microsoft Azure. Rozdziel wiele wpisów średnikami.|

## <a name="see-also"></a>Zobacz także

- [AzCopy](storage-ref-azcopy.md)
