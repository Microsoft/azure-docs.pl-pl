---
title: Informacje o kluczu konfiguracji aplikacji platformy Azure — magazyn wartości
description: Informacje na temat magazynu klucz-wartość w konfiguracji aplikacji platformy Azure, w którym są przechowywane dane konfiguracji jako wartości klucza. Kluczowe wartości to reprezentacja ustawień aplikacji.
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: ad9e96433a7ee72476ae2251c684d17ec7a6d1ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96930249"
---
# <a name="keys-and-values"></a>Klucze i wartości

Konfiguracja aplikacji platformy Azure przechowuje dane konfiguracji jako wartości klucza. Kluczowe wartości to prosta i elastyczna reprezentacja ustawień aplikacji używanych przez deweloperów.

## <a name="keys"></a>Klucze

Klucze służą jako identyfikatory wartości kluczy i są używane do przechowywania i pobierania odpowiednich wartości. Typowym sposobem organizowania kluczy w hierarchiczną przestrzeń nazw przy użyciu ogranicznika znaków, takiego jak `/` lub `:` . Użyj Konwencji najlepiej dopasowanej do Twojej aplikacji. Usługa App Configuration traktuje klucze jako jedną całość. Nie analizuje ona kluczy, aby ustalić, jak ich nazwy są strukturalne lub wymuszać każdą z nich regułę.

Oto przykład nazw kluczy, które są uporządkowane w hierarchii na podstawie usług składowych:

```aspx
    AppName:Service1:ApiEndpoint
    AppName:Service2:ApiEndpoint
```

Użycie danych konfiguracji w ramach struktur aplikacji może dyktować określone schematy nazewnictwa dla wartości kluczy. Na przykład struktura chmurowa w chmurze firmy Java definiuje `Environment` zasoby, które dostarczają ustawienia do aplikacji sprężynowej.  Są one sparametryzowane przez zmienne, które zawierają nazwę i *profil* *aplikacji* . Klucze służące do wiosennych danych konfiguracyjnych związanych z chmurą zwykle zaczynają się od tych dwóch elementów oddzielonych ogranicznikami.

Klucze przechowywane w usłudze App Configuration są opartymi na standardzie Unicode ciągami uwzględniającymi wielkość liter. Klucze *APP1* i *APP1* są odrębne w magazynie konfiguracji aplikacji. Należy pamiętać o tym, gdy używasz ustawień konfiguracji w ramach aplikacji, ponieważ niektóre struktury obsługują bez uwzględniania wielkości liter w kluczach konfiguracji. Nie zaleca się używania wielkości liter w odróżnieniu od kluczy.

Można użyć dowolnego znaku Unicode w nazwach kluczy, z wyjątkiem `%` . Nazwa klucza nie może być `.` lub `..` albo. Wartość klucza ma łączny limit rozmiaru równy 10 KB. Ten limit obejmuje wszystkie znaki w kluczu, jego wartość i wszystkie skojarzone opcjonalne atrybuty. W ramach tego limitu możesz mieć wiele poziomów hierarchii dla kluczy.

### <a name="design-key-namespaces"></a>Przestrzenie nazw kluczy projektu

Istnieją dwie ogólne metody nadawania nazw kluczom używanym na potrzeby danych konfiguracji: płaska i hierarchiczna. Te metody są podobne do punktu widzenia użycia aplikacji, ale hierarchiczne nazewnictwo oferuje szereg korzyści:

* Łatwość odczytywania. Ograniczniki w nazwie klucza hierarchicznego jako spacje w zdaniu. Zapewniają także naturalne przerwy między wyrazami.
* Łatwość zarządzania. Hierarchia nazw kluczy reprezentuje grupy logiczne danych konfiguracji.
* Łatwość użycia. Łatwiej jest napisać zapytanie, które dopasowuje klucze do wzorca w strukturze hierarchicznej i pobiera tylko część danych konfiguracji. Ponadto wiele nowszych platform programistycznych ma natywną obsługę hierarchicznych danych konfiguracyjnych, dzięki czemu aplikacja może korzystać z określonych zestawów konfiguracji.

Klucze w usłudze App Configuration można organizować hierarchicznie na wiele sposobów. Należy traktować takie klucze jako [identyfikatory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Każdy klucz hierarchiczny jest *ścieżką* zasobów składającą się z co najmniej jednego składnika, który jest połączony ze sobą ogranicznikami. Wybierz znak, który ma być używany jako ogranicznik w zależności od potrzeb aplikacji, języka programowania lub struktury. Użyj wielu ograniczników dla różnych kluczy w konfiguracji aplikacji.

### <a name="label-keys"></a>Klucze etykiet

Pary klucz-wartość w usłudze App Configuration mogą opcjonalnie mieć atrybut etykiety. Etykiety umożliwiają rozróżnienie par klucz-wartość z tym samym kluczem. Key *APP1* *z etykietami a* i *B* tworzy dwa osobne klucze w magazynie konfiguracji aplikacji. Domyślnie klucz-wartość nie ma etykiety. Aby jawnie odwoływać się do wartości klucza bez etykiety, użyj `\0` (adres URL zakodowany jako `%00` ).

Etykieta zapewnia wygodny sposób tworzenia wariantów klucza. Typowym zastosowaniem etykiet jest określenie wielu środowisk dla tego samego klucza:

```
    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production
```

### <a name="version-key-values"></a>Wartość klucza wersji

Użyj etykiet jako sposobu, aby utworzyć wiele wersji klucz-wartość. W etykietach można na przykład umieścić numer wersji aplikacji lub identyfikator zatwierdzenia usługi Git, aby identyfikować pary klucz-wartość skojarzone z określoną kompilacją oprogramowania.

> [!NOTE]
> Jeśli szukasz wersji zmiany, konfiguracja aplikacji zachowuje wszystkie zmiany w kluczu i wartości, które wystąpiły w przeszłości automatycznie. Aby uzyskać więcej informacji, zobacz [migawka do punktu w czasie](./concept-point-time-snapshot.md) .

### <a name="query-key-values"></a>Wartości klucza zapytania

Każda para klucz-wartość jest unikatowo identyfikowana za pomocą klucza oraz etykiety, która może mieć wartość `\0`. Należy wykonać zapytanie o magazyn konfiguracji aplikacji dla kluczowych wartości, określając wzorzec. Magazyn konfiguracji aplikacji zwraca wszystkie wartości klucza, które pasują do wzorca, łącznie z odpowiednimi wartościami i atrybutami. Użyj następujących wzorców kluczy w wywołaniach interfejsu API REST do konfiguracji aplikacji:

| Klucz | Opis |
|---|---|
| Element `key` jest pomijany lub `key=*` | Pasuje do wszystkich kluczy |
| `key=abc` | Dopasowuje dokładnie nazwę klucza **ABC** |
| `key=abc*` | Pasuje do nazw kluczy, które zaczynają się od ciągu **abc** |
| `key=abc,xyz` | Dopasowuje nazwy kluczy **ABC** lub **XYZ**. Ograniczone do pięciu CSV |

Można też uwzględnić następujące wzorce etykiet:

| Etykieta | Opis |
|---|---|
| Element `label` jest pomijany lub `label=*` | Dopasowuje dowolną etykietę, która obejmuje `\0` |
| `label=%00` | Dopasowuje `\0` etykietę |
| `label=1.0.0` | Dokładnie pasuje do etykiety **1.0.0** |
| `label=1.0.*` | Pasuje do etykiet zaczynających się od **1.0.** |
| `label=%00,1.0.0` | Dopasowuje etykiety `\0` lub **1.0.0**, ograniczone do pięciu CSV |

> [!NOTE]
> `*`, `,` , i `\` są zarezerwowane znaki w zapytaniach. Jeśli w nazwach kluczy lub etykietach użyto zastrzeżonego znaku, należy go wypróbować za pomocą `\{Reserved Character}` zapytania.

## <a name="values"></a>Wartości

Wartości przypisane do kluczy są również ciągami Unicode. W przypadku wartości można używać wszystkich znaków Unicode.

### <a name="use-content-type"></a>Użyj typu zawartości
Każda wartość klucza w konfiguracji aplikacji ma atrybut Content-Type. Opcjonalnie można użyć tego atrybutu do przechowywania informacji o typie wartości w klucz-wartość, która pomaga aplikacji prawidłowo przetworzyć ją. Dla typu zawartości można użyć dowolnego formatu. W obszarze Konfiguracja aplikacji są stosowane [typy nośników]( https://www.iana.org/assignments/media-types/media-types.xhtml) (znane także jako typy MIME) dla wbudowanych typów danych, takich jak flagi funkcji, odwołania Key Vault i wartości klucza JSON.

## <a name="next-steps"></a>Następne kroki

* [Migawka punktu w czasie](./concept-point-time-snapshot.md)
* [Zarządzanie funkcjami](./concept-feature-management.md)
* [Obsługa zdarzeń](./concept-app-configuration-event.md)
