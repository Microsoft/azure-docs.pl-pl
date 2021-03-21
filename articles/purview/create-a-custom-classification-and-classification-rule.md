---
title: Tworzenie niestandardowej klasyfikacji i reguły klasyfikacji (wersja zapoznawcza)
description: Dowiedz się, jak utworzyć niestandardowe klasyfikacje w celu zdefiniowania typów danych w obszarze danych, które są unikatowe dla Twojej organizacji w usłudze Azure kontrolą.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/5/2021
ms.openlocfilehash: 2966618619aa40ed60c2f3d0fb2c8e080d34a016
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617050"
---
# <a name="custom-classifications-in-azure-purview"></a>Klasyfikacje niestandardowe w usłudze Azure kontrolą

W tym artykule opisano sposób tworzenia klasyfikacji niestandardowych w celu zdefiniowania typów danych w obszarze danych, które są unikatowe dla Twojej organizacji. Opisano w nim także tworzenie niestandardowych reguł klasyfikacji, które umożliwiają znalezienie określonych danych w całym obszarze danych.

## <a name="default-classifications"></a>Klasyfikacje domyślne

Usługa Azure kontrolą Data Catalog zapewnia duży zestaw klasyfikacji domyślnych, które reprezentują typowe typy danych osobistych, które mogą znajdować się w danej firmie.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="Wybierz klasyfikację" border="true":::

Istnieje również możliwość tworzenia klasyfikacji niestandardowych, jeśli którekolwiek z klasyfikacji domyślnych nie spełnią Twoich potrzeb.

## <a name="steps-to-create-a-custom-classification"></a>Procedura tworzenia klasyfikacji niestandardowej

Aby utworzyć klasyfikację niestandardową, wykonaj następujące czynności:

1. Z poziomu wykazu wybierz pozycję **centrum zarządzania** z menu po lewej stronie.

2. Wybierz pozycję **klasyfikacje** w obszarze **Zarządzanie metadanymi**.

3. Wybierz pozycję **+ Nowy**

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Nowa Klasyfikacja" border="true":::

Zostanie otwarte okienko **Dodaj nową klasyfikację** , w którym można nadać klasyfikacji nazwę i opis. Dobrym sposobem jest użycie konwencji odstępów nazw, takich jak `your company name.classification name` .

Klasyfikacje systemu Microsoft są pogrupowane pod zarezerwowaną `MICROSOFT.` przestrzenią nazw. Przykładem jest **firma Microsoft. Zarządowi. Prześlij. \_ \_ numer ubezpieczenia społecznego**.

Nazwa klasyfikacji musi rozpoczynać się od litery, po której następuje Sekwencja liter, cyfr i znaków kropki (.) lub podkreślenia. Spacje nie są dozwolone. Podczas wpisywania środowisko użytkownika automatycznie generuje przyjazną nazwę. Ta przyjazna nazwa jest widoczna dla użytkowników, gdy zostanie ona zastosowana do zasobu w wykazie.

Aby zachować krótką nazwę, system tworzy przyjazną nazwę na podstawie następującej logiki:

- Wszystkie z wyjątkiem ostatnich dwóch segmentów przestrzeni nazw są przycinane.

- Wielkość liter jest dostosowywana tak, aby pierwsza litera każdego wyrazu była Wielka literałem. Wszystkie pozostałe litery są konwertowane na małe litery.

- Wszystkie podkreślenia ( \_ ) są zamieniane na spacje.

Na przykład, jeśli nazwa klasyfikacji **contoso.hr. \_Identyfikator pracownika**, przyjazna nazwa jest przechowywana w systemie jako **HR. ID pracownika**.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

Wybierz pozycję **OK**, a Twoja nowa klasyfikacja zostanie dodana do listy klasyfikacji.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Klasyfikacja niestandardowa" border="true":::

Wybranie klasyfikacji na liście spowoduje otwarcie strony Szczegóły klasyfikacji. Tutaj znajdziesz wszystkie szczegóły klasyfikacji.

Te szczegóły obejmują liczbę wystąpień, a także nazwę formalną, skojarzone reguły klasyfikacji (jeśli istnieją) i nazwę właściciela.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Wybierz klasyfikację" border="true":::

## <a name="custom-classification-rules"></a>Niestandardowe reguły klasyfikacji

Usługa wykazu udostępnia zestaw domyślnych reguł klasyfikacji, które są używane przez skaner do automatycznego wykrywania określonych typów danych. Możesz również dodać własne niestandardowe reguły klasyfikacji, aby wykryć inne typy danych, które mogą być interesujące w zakresie danych. Ta funkcja może być bardzo wydajna, gdy \' spróbujesz znaleźć dane w obszarze danych.

Załóżmy na przykład, \' że firma o nazwie Contoso ma identyfikatory pracowników, które są ustandaryzowane w całej firmie przez pracownika programu Word, \" \" a po nim identyfikator GUID, aby utworzyć pracownika {GUID}. Na przykład jedno wystąpienie identyfikatora pracownika wygląda tak `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55` .

Firma Contoso może skonfigurować system do skanowania, aby znalazł wystąpienia tych identyfikatorów przez utworzenie niestandardowej reguły klasyfikacji. Mogą podawać wyrażenie regularne zgodne ze wzorcem danych, w tym przypadku `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . Opcjonalnie, jeśli dane znajdują się zwykle w kolumnie znającej nazwę, taką jak \_ identyfikator pracownika lub IDPracownika, można dodać wyrażenie regularne wzorca kolumny, aby skanowanie było jeszcze dokładniejsze. Przykładowe wyrażenie regularne to \_ identyfikator pracownika \| IDPracownika.

System skanujący może następnie użyć tej reguły do sprawdzenia rzeczywistych danych w kolumnie i nazwy kolumny, aby spróbować zidentyfikować każde wystąpienie, w którym znajduje się wzorzec identyfikatora pracownika.

## <a name="steps-to-create-a-custom-classification-rule"></a>Procedura tworzenia niestandardowej reguły klasyfikacji

Aby utworzyć niestandardową regułę klasyfikacji:

1. Utwórz klasyfikację niestandardową, postępując zgodnie z instrukcjami podanymi w poprzedniej sekcji. Tę klasyfikację niestandardową należy dodać do konfiguracji reguły klasyfikacji, aby system stosowała ją po znalezieniu dopasowania w kolumnie.

2. Wybierz ikonę **centrum zarządzania** .

3. Wybierz sekcję **reguły klasyfikacji** .

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Kafelek reguły klasyfikacji" border="true":::

4. Wybierz pozycję **Nowy**.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Dodaj nową regułę klasyfikacji" border="true":::

5. Zostanie otwarte okno dialogowe **Nowa reguła klasyfikacji** . Wypełnij pola i zdecyduj, czy chcesz utworzyć **regułę wyrażenia regularnego** , czy **regułę słownika**.

   |Pole     |Opis  |
   |---------|---------|
   |Nazwa   |    Wymagane. Maksymalna liczba znaków to 100.    |
   |Opis      |Opcjonalny. Maksymalna liczba znaków to 256.    |
   |Nazwa klasyfikacji    | Wymagane. Wybierz nazwę klasyfikacji z listy rozwijanej, aby poinformować skaner o jej zastosowaniu po znalezieniu dopasowania.        |
   |Stan   |  Wymagane. Opcje są włączone lub wyłączone. Domyślnie włączone.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Utwórz nową regułę klasyfikacji" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Tworzenie reguły wyrażenia regularnego

1. W przypadku tworzenia reguły wyrażenia regularnego zobaczysz następujący ekran. Opcjonalnie można przekazać plik, który będzie używany do **generowania sugerowanych wzorców wyrażeń regularnych** dla reguły.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Utwórz nową regułę wyrażenia regularnego" border="true":::

1. Jeśli zdecydujesz się na wygenerowanie sugerowanego wzorca wyrażenia regularnego, po przesłaniu pliku wybierz jeden z sugerowanych wzorców i kliknij przycisk **Dodaj do wzorców** , aby użyć sugerowanych wzorców danych i kolumn. Można dostosować sugerowane wzorce lub można również wpisać własne wzorce bez przekazywania pliku.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Generuj sugerowane wyrażenie regularne" border="true":::

   |Pole     |Opis  |
   |---------|---------|
   |Wzorzec danych    |Opcjonalny. Wyrażenie regularne reprezentujące dane przechowywane w polu dane. Limit jest bardzo duży. W poprzednim przykładzie wzorzec danych testuje dla identyfikatora pracownika, który jest dosłownie wyraz `Employee{GUID}` .  |
   |Wzorzec kolumny    |Opcjonalny. Wyrażenie regularne, które reprezentuje nazwy kolumn, które mają być zgodne. Limit jest bardzo duży. |

1. W obszarze **wzorzec danych** istnieją dwa progi, które można ustawić:

   - **Próg DISTINCT dopasowania**: całkowita liczba unikatowych wartości danych, które należy znaleźć w kolumnie przed uruchomieniem przez skaner wzorca danych. Sugerowana wartość to 8. Tę wartość można ręcznie dopasować do zakresu od 2 do 32. System wymaga tej wartości, aby upewnić się, że kolumna zawiera wystarczającą ilość danych dla skanera, aby precyzyjnie sklasyfikować go. Na przykład kolumna zawierająca wiele wierszy zawierających wartość 1 nie zostanie sklasyfikowana. Kolumny zawierające jeden wiersz z wartością i resztą wierszy mają wartości null, które również nie są klasyfikowane. Jeśli określisz wiele wzorców, ta wartość ma zastosowanie do każdego z nich.

   - **Minimalny próg dopasowania**: można użyć tego ustawienia, aby ustawić minimalną wartość procentową dopasowań wartości danych w kolumnie, która musi zostać znaleziona przez skaner dla klasyfikacji, która ma zostać zastosowana. Sugerowana wartość to 60%. Należy zachować ostrożność przy użyciu tego ustawienia. W przypadku obniżenia poziomu poniżej 60% możesz wprowadzić klasyfikacje fałszywie dodatnie w katalogu. Jeśli określisz wiele wzorców danych, to ustawienie jest wyłączone, a wartość zostanie ustalona na 60%.

1. Teraz możesz zweryfikować swoją regułę i **utworzyć** ją.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="Sprawdź regułę przed utworzeniem" border="true":::

### <a name="creating-a-dictionary-rule"></a>Tworzenie reguły słownika

1. W przypadku tworzenia reguły słownika zobaczysz następujący ekran. Przekaż plik, który zawiera wszystkie możliwe wartości dla tworzonej klasyfikacji w jednej kolumnie.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Utwórz regułę słownika" border="true":::

1. Po wygenerowaniu słownika można dostosować progi DISTINCT i minimalne dopasowanie oraz przesłać regułę.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Reguła słownika kontrolą — Dopasuj próg DISTINCT dopasowania i minimalny próg dopasowania" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Utwórz regułę słownika przy użyciu znacznika wyboru wygenerowanego przez słownik." border="true":::

## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu reguły klasyfikacji, jest ona gotowa do dodania do zestawu reguł skanowania, aby podczas skanowania była stosowana reguła podczas skanowania. Aby uzyskać więcej informacji, zobacz [Tworzenie zestawu reguł skanowania](create-a-scan-rule-set.md).
