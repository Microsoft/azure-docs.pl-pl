---
title: Informacje o kluczu konfiguracji aplikacji platformy Azure — magazyn wartości
description: Informacje o sposobie przechowywania danych konfiguracji w usłudze Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77523608"
---
# <a name="keys-and-values"></a>Klucze i wartości

Konfiguracja aplikacji platformy Azure przechowuje dane konfiguracji jako pary klucz-wartość. Pary klucz-wartość są prostą i elastyczną reprezentacją ustawień aplikacji używanych przez deweloperów.

## <a name="keys"></a>Klucze

Klucze służą jako identyfikatory par klucz-wartość i są używane do przechowywania i pobierania odpowiednich wartości. Typowym sposobem organizowania kluczy w hierarchiczną przestrzeń nazw przy użyciu ogranicznika znaków, takiego jak `/` lub. `:` Użyj Konwencji najlepiej dopasowanej do Twojej aplikacji. Usługa App Configuration traktuje klucze jako jedną całość. Nie analizuje ona kluczy, aby ustalić, jak ich nazwy są strukturalne lub wymuszać każdą z nich regułę.

Poniżej przedstawiono dwa przykłady nazw kluczy, które są uporządkowane w hierarchii:

* Na podstawie usług składowych

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Na podstawie regionów wdrożenia

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

Użycie danych konfiguracji w ramach struktur aplikacji może dyktować określone schematy nazewnictwa dla wartości kluczy. Na przykład struktura chmurowa w chmurze firmy Java `Environment` definiuje zasoby, które dostarczają ustawienia do aplikacji sprężynowej.  Są one sparametryzowane przez zmienne, które zawierają nazwę i *profil* *aplikacji* . Klucze służące do wiosennych danych konfiguracyjnych związanych z chmurą zwykle zaczynają się od tych dwóch elementów oddzielonych ogranicznikami.

Klucze przechowywane w usłudze App Configuration są opartymi na standardzie Unicode ciągami uwzględniającymi wielkość liter. Klucze *APP1* i *APP1* są odrębne w magazynie konfiguracji aplikacji. Należy pamiętać o tym, gdy używasz ustawień konfiguracji w ramach aplikacji, ponieważ niektóre struktury obsługują bez uwzględniania wielkości liter w kluczach konfiguracji. Nie zaleca się używania wielkości liter w odróżnieniu od kluczy.

Można użyć dowolnego znaku Unicode w nazwach kluczy `*`, z wyjątkiem `,`,, `\`i.  Jeśli musisz uwzględnić jeden z tych znaków zarezerwowanych, możesz użyć polecenia `\{Reserved Character}`. 

Dla pary klucz-wartość istnieje łączny limit rozmiaru równy 10 KB. Ten limit obejmuje wszystkie znaki w kluczu, jego wartość i wszystkie skojarzone opcjonalne atrybuty. W ramach tego limitu możesz mieć wiele poziomów hierarchii dla kluczy.

### <a name="design-key-namespaces"></a>Przestrzenie nazw kluczy projektu

Istnieją dwie ogólne metody nadawania nazw kluczom używanym na potrzeby danych konfiguracji: płaska i hierarchiczna. Te metody są podobne do punktu widzenia użycia aplikacji, ale hierarchiczne nazewnictwo oferuje szereg korzyści:

* Łatwość odczytywania. Ograniczniki w nazwie klucza hierarchicznego jako spacje w zdaniu. Zapewniają także naturalne przerwy między wyrazami.
* Łatwość zarządzania. Hierarchia nazw kluczy reprezentuje grupy logiczne danych konfiguracji.
* Łatwość użycia. Łatwiej jest napisać zapytanie, które dopasowuje klucze do wzorca w strukturze hierarchicznej i pobiera tylko część danych konfiguracji. Ponadto wiele nowszych platform programistycznych ma natywną obsługę hierarchicznych danych konfiguracyjnych, dzięki czemu aplikacja może korzystać z określonych zestawów konfiguracji.

Klucze w usłudze App Configuration można organizować hierarchicznie na wiele sposobów. Należy traktować takie klucze jako [identyfikatory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Każdy klucz hierarchiczny jest *ścieżką* zasobów składającą się z co najmniej jednego składnika, który jest połączony ze sobą ogranicznikami. Wybierz znak, który ma być używany jako ogranicznik w zależności od potrzeb aplikacji, języka programowania lub struktury. Użyj wielu ograniczników dla różnych kluczy w konfiguracji aplikacji.

### <a name="label-keys"></a>Klucze etykiet

Wartości kluczy w konfiguracji aplikacji mogą opcjonalnie mieć atrybut Label. Etykiety służą do odróżniania wartości klucza z tym samym kluczem. Key *APP1* *z etykietami a* i *B* tworzy dwa osobne klucze w magazynie konfiguracji aplikacji. Domyślnie wartość klucza nie ma etykiety. Aby jawnie odwoływać się do wartości klucza bez etykiety, `\0` Użyj (adres URL `%00`zakodowany jako).

Etykieta zapewnia wygodny sposób tworzenia wariantów klucza. Typowym zastosowaniem etykiet jest określenie wielu środowisk dla tego samego klucza:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Wartości klucza wersji

Konfiguracja aplikacji nie ma automatycznie wartości klucza wersji. Użyj etykiet jako sposobu tworzenia wielu wersji wartości klucza. Na przykład możesz wprowadzić numer wersji aplikacji lub identyfikator zatwierdzenia usługi Git w etykietach, aby zidentyfikować wartości kluczy skojarzone z konkretną kompilacją oprogramowania.

Możesz użyć dowolnego znaku Unicode w etykietach z wyjątkiem `*`dla `,`,, `\`i. Te znaki są zarezerwowane. Aby dołączyć znak zarezerwowany, należy go wypróbować za `\{Reserved Character}`pomocą polecenia.

### <a name="query-key-values"></a>Wartości klucza zapytania

Każda wartość klucza jest unikatowo identyfikowana przez jego klucz oraz etykieta, która `null`może być. Należy wykonać zapytanie dotyczące magazynu konfiguracji aplikacji, aby uzyskać kluczowe wartości przez określenie wzorca. Magazyn konfiguracji aplikacji zwraca wszystkie wartości klucza, które pasują do wzorca i odpowiadające im wartości i atrybuty. Użyj następujących wzorców kluczy w wywołaniach interfejsu API REST do konfiguracji aplikacji:

| Key | |
|---|---|
| Element `key` jest pomijany lub `key=*` | Pasuje do wszystkich kluczy |
| `key=abc` | Dopasowuje dokładnie nazwę klucza **ABC** |
| `key=abc*` | Pasuje do nazw kluczy, które zaczynają się od ciągu **abc** |
| `key=abc,xyz` | Dopasowuje nazwy kluczy **ABC** lub **XYZ**. Ograniczone do pięciu CSV |

Można też uwzględnić następujące wzorce etykiet:

| Etykieta | |
|---|---|
| Element `label` jest pomijany lub `label=*` | Dopasowuje dowolną etykietę, która obejmuje`null` |
| `label=%00` | Dopasowuje `null` etykietę |
| `label=1.0.0` | Dokładnie pasuje do etykiety **1.0.0** |
| `label=1.0.*` | Pasuje do etykiet zaczynających się od **1.0.** |
| `label=%00,1.0.0` | Dopasowuje `null` etykiety lub **1.0.0**, ograniczone do pięciu CSV |

## <a name="values"></a>Wartości

Wartości przypisane do kluczy są również ciągami Unicode. W przypadku wartości można używać wszystkich znaków Unicode. Istnieje opcjonalny, zdefiniowany przez użytkownika typ zawartości skojarzony z każdą wartością. Ten atrybut służy do przechowywania informacji o wartości, która ułatwia aplikacji prawidłowe przetwarzanie.

Dane konfiguracyjne przechowywane w magazynie konfiguracji aplikacji są szyfrowane w stanie spoczynku i w trakcie przesyłania. Klucze nie są szyfrowane w stanie spoczynku. Konfiguracja aplikacji nie jest rozwiązaniem zastępczym dla Azure Key Vault. Nie przechowuj w niej wpisów tajnych aplikacji.

## <a name="next-steps"></a>Następne kroki

* [Migawka punktu w czasie](./concept-point-time-snapshot.md)  
* [Zarządzanie funkcjami](./concept-feature-management.md)  
